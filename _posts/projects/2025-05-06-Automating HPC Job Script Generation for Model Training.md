---
title: Automating HPC Job Script Generation for Model Training and Evaluation
excerpt: The ability to make one's own tools is one of a programmer's biggest strengths.
date: 2025-05-06
categories:
  - projects
tags:
  - Blog
---
## Introduction: Running my code on an HPC cluster

![job_scheduling_system](/assets/job_scheduling_system.png)

The figure shows a process of submitting a job to an HPC cluster and executing code.

I often run experiments involving training and testing computer vision models. Recently, I’ve been working with large models like Depth Anything, and in such cases, my local GPU’s VRAM is often insufficient. As a result, I’ve increasingly been using not only lab computers but also TSUBAME—the supercomputer owned by my university. However, an HPC (High Performance Computing) cluster like TSUBAME is a shared resource among multiple users, so I can’t just run my program whenever I want. Instead, I need to submit a script specifying what resources I need and which program I want to run. The system uses this request to allocate appropriate resources and execute the program. This kind of system is called a job scheduling system (or more broadly, a batch system), and the script that includes the code and required resources is called a job.

## Problem: Writing a job script is too tedious

Writing that script manually is a tedious task. When running similar experiments repeatedly, the required resources usually don’t change much, but I still had to write the same script over and over again. This manual process also led to frequent mistakes, like inputting the wrong hyperparameters and ending up with useless training runs.

## Solution: Write a script that manages job submission

![job_scheduling_system](/assets/HPC_job_script_generator.png)

The figure above shows the workflow of a program that automatically generates job scripts. For example, I can call a shell function named `train_manager` (defined in `.bashrc`) with an argument like `--experiment train_with_batch_4`. This shell function then invokes a Python script and passes along the argument. The Python script looks up the hyperparameters for `train_with_batch_4` from a file that stores settings for multiple experiment versions. It combines these hyperparameters with a boilerplate job script template to generate the final `train_script`. The shell function then receives this script and immediately submits it to the scheduler.
In a similar manner, I made a pipeline for generating `evaluation_script`. 

### How I approached to make the program

First, I separated the components required for the job script. These components fall into two categories: (1) the necessary resources, such as the number of CPUs or GPUs, and (2) the hyperparameters or metadata about the experiment—like which dataset to use. I used Python’s `dataclass` to define this information. This is a key part of the system, as Python’s `dataclass` allows dot notation for accessing member variables, similar to `argparse.Namespace`. By replacing the `args` object that used to hold a `Namespace` with a `dataclass`, I could use the same code without any issues. After completing the Python script, I defined a shell function in my `.bashrc` that both runs the generated Python script and takes care of submitting the job instead of me. 

## Result: How effective was it?

It was extremely effective. It didn’t just save time—it also prevented errors that often occurred when writing job scripts manually. Validation time was drastically reduced as well. For example, suppose I had to evaluate models from epoch 0 to 200 and there was only one day left before a research meeting. I could instantly generate evaluation scripts for checkpoints from epoch 0–10, 10–20, 20–30, and so on, reducing validation time to one-tenth. Of course, this costs money for using TSUBAME, but the lab covers the usage fees, not me lol.

## Reflection: What was the hardest part? What is a takeaway?

Initially, I was hesitant to deviate from the established workflow. There are regular research meetings, and I didn’t want to be scolded by wasting time for automating job script generation—a task that doesn’t even need to be discussed in meetings. Plus, since research directions often change unpredictably, I had to design the program carefully so that it wouldn’t become useless for future experiments.


