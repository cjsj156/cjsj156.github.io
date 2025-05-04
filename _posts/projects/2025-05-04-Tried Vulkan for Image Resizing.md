---
title: Tried Vulkan for Image Resizing
excerpt: Tried Vulkan on Android
date: 2025-05-04
categories:
  - projects
tags:
  - Blog
---

### Can we make image processing faster with smartphone GPU with Vulkan?

#### Background
Nefrock, the company where I worked on this project, wanted to examine the possibility of optimizing image preprocessing using the GPU of a smartphone. They're providing a service for real-time OCR on smartphones, and while the deep learning model itself can perform computations on the smartphone GPU, the image input to the model was not preprocessed on the GPU. So they wanted to explore whether it's possible to preprocess images (resizing, in this case) directly on the GPU and achieve better speed.

#### Technology Stacks for Implementation
We used C++ with Vulkan.

#### What and How to Implement?

First, my adviser asked me to implement very simple process: **get the camera image, then resize it**.
I needed to implement this process in two versions.:

- **CPU Resizing**: using only CPU and OpenCV
- **Vulkan Resizing**: using smartphone GPU via Vulkan

After implementation, we compared the time taken for resizing images captured from the camera.

We referred to [this GitHub project](https://github.com/ktzevani/native-camera-vulkan), which uses Vulkan for image processing on Android, and built our application based on it.

#### Two Types of Camera Image Resize Application Logic

##### CPU Resizing

![](/assets/CPU_Resizing.png)

This version is straightforward. It captures an image from the camera, copies it to unified memory accessible by the CPU, and resizes it using OpenCV. We measured the time from when the image data arrived at the Hardware Buffer to the completion of resizing. 

If you're unfamiliar with **Hardware Buffer** and **Unified Memory**:

- **Hardware Buffer**: memory accessible by hardware like camera and GPU
- **Unified Memory**: shared memory accessible from both CPU and GPU Most modern smartphones have this architecture.

##### Vulkan Resizing
![](/assets/Vulkan_Resizing%201.png)
This version uses the GPU for resizing. Light green arrows represent GPU processes. After acquiring the camera image into Hardware Buffer, the GPU copies it into unified memory, performs resizing (rendering at a lower resolution using Vulkan API), and copies the image onto a **Staging Buffer**.

GPU does perform the resizing as intended, but the process is more complex. New concepts like **Staging Buffer** and **Image** objects come into play.

Before I proceed to what those are, I'd like to introduce the typical behaviour of Vulkan in an ordinary PC.

Vulkan Work Flow in Ordinary PC
![Vulkan_Operation_in_Ordinary_PC](/assets/Vulkan_Operation_in_Ordinary_PC_2.png)

This is the work flow of Vulkan in oridnary PC step by step.

1. Create a Staging Buffer from image data
2. Copy the data to GPU VRAM as an Image object
3. Perform rendering operations on the Image
4. Convert the Image back to Staging Buffer and copy it to CPU-accessible RAM

Typically, ordinary PCs have dedicated VRAM for GPU and it has no access to RAM, unlike the case of unified memory where both CPU and GPU can access the same memory space. To let the GPU access RAM, we need to arrange the data according to certain requirements. That can be done by converting the image data into **Staging Buffer** object following Vulkan API requirements. Then the data is prepared for GPU copying. After that, the GPU copies the image to VRAM and simultaneously transforms it into an **Image** object. This Image object ensures the image data meets specific GPU-side constraints just simliar to Staging Buffer object. Then, the GPU performs graphical operations on the Image. Once finished, the GPU gives the Image back to the CPU by copying it into another Staging Buffer on RAM. This is the typical process of Vulkan, and the [Vulkan Tutorial](https://vulkan-tutorial.com/Introduction) explains this approach in detail.

In our smartphone case, since memory is unified, we assumed **Staging Buffer** might not be necessary. However, we still followed the full pipeline due to the complexity of deviating from the standard process.

##### Vulkan Rendering Process and Prgramming
Compared to OpenCV, Vulkan required:

1. A full **rendering pipeline**
2. Programming at a much **lower abstraction level**

Vulkan image rendering pipeline is as below.
```
image + vertex (input)
   -> Vertex Shader
   -> Rasterization
   -> Fragment Shader
   -> Rendered Image (output)
```
This is fundamentally different from OpenCV, which treats images as matrices and applies operations pixel-wise. Vulkan is focused on rendering and uses 3D concepts like MVP (Model-View-Projection) transformation. In the [codebase we used](https://github.com/ktzevani/native-camera-vulkan), the original rendering logic was for a rotating cube. We modified it to render a static rectangle, resized through vertex positioning, and aligned the camera view perpendicularly.

About lower abstraction level, I had to carefully set every detailed settings everytime I make an object like below. 
```
VkImageCreateInfo imageInfo{};
imageInfo.sType = VK_STRUCTURE_TYPE_IMAGE_CREATE_INFO;
imageInfo.imageType = VK_IMAGE_TYPE_2D;
imageInfo.extent.width = static_cast<uint32_t>(texWidth);
imageInfo.extent.height = static_cast<uint32_t>(texHeight);
imageInfo.extent.depth = 1;
imageInfo.mipLevels = 1;
imageInfo.arrayLayers = 1;
...
```

These are settings just for making an Image object. These settings continue even more. And these settings should be set exactly according to your usage otherwise it wouldn't work. Although this low-level design makes Vulkan blazingly fast, it makes also programming very stressful.

#### Result
We compared performance:

- **CPU Resizing**: 6–8ms/image
- **Vulkan Resizing**: 50–70ms/image

Despite expecting speed-up from GPU, the Vulkan version was **significantly slower**. Even accounting for sequential processing of images (limiting parallelism), the difference was too large. We suspect that the overhead from using **Staging Buffers** in Vulkan was a major cause.

We couldn't test a Staging Buffer-free version due to time constraints.
#### Conclusion
Using **Vulkan’s graphics pipeline** for preprocessing images on smartphones is **not practical** for Nefrock’s real-time OCR use case. Following Vulkan's typical process adds overhead that negates GPU benefits. Also, Vulkan’s low-level programming drastically reduces productivity, which doesn’t align well with deep learning development where rapid prototyping is important.

#### Note
However, this does not mean GPU-based preprocessing is hopeless:

- We did **not test Vulkan’s compute shaders**, which allow general-purpose GPU (GPGPU) programming and may offer simpler and more efficient processing.
- Vulkan’s **hardware compatibility** is excellent, making it a viable option for large-scale deployment across diverse edge devices.

#### Reflection
Using C++ and Vulkan to implement low-level GPU processing on smartphones was frustrating but also rewarding. Learning about 3D graphics related contents such as MVP transformations was fun. But I regret not accomplishing more during the internship. I often jumped straight into coding without grasping the whole architecture, and it worked for many cases but this time I overlooked the fact that Vulkan wasn't that simple. I need to go back to review concepts in Vulkan after making useless codes. I learned that balancing hands-on attempts with structured understanding is essential.