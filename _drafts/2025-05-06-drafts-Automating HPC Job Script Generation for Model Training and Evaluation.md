---
title: drafts-Automating HPC Job Script Generation for Model Training and Evaluation
excerpt: The ability to make one's own tools is one of a programmer's biggest strengths.
date: 2025-05-06
categories:
  - projects
tags:
  - Blog
---
# Automating HPC Job Script Generation for Model Training and Evaluation

## Introduction: Running my code on an HPC cluster
![job_scheduling_system](/assets/job_scheduling_system.png)
(HPC cluster에 job을 제출하고 코드가 실행되는 과정)

나는 컴퓨터비전 모델들을 테스트, 학습을 하는 실험을 많이 함. 최근에는 Depth Anything 등의 대형모델도 자주 돌리는데, 그럴 땐 로컬의 GPU로는 VRAM이 딸리는 경우가 많았음. 그래서 이번에 실험실 내의 컴퓨터 뿐만 아니라 우리 연구실이 속한 동과대가 소유한 슈퍼컴퓨터인 TSUBAME를 점점 자주 사용하게 됨. 그런데 이런 TSUBAME같은 HPC(High Performance Computing) cluster는 복수의 이용자가 resource를 공유하기 때문에, 내가 프로그램을 돌리고 싶으면 바로 돌릴 수 있는 게 아님. 어떤 리소스를 어떤 프로그램을 돌리는 데 사용하고 싶다는 내용을 적은 스크립트를 제출해야 시스템에서 내 요청을 확인하고 적절히 리소스를 분배해서 프로그램을 실행시켜줌. 이런 시스템을 job scheduling system (넓게는 batch system)이라고 하고, 돌릴 코드와 필요한 리소스 정보를 담은 스크립트를 job 이라고 함.

## Problem: Writing a job script for HPC cluster is too tedious
저 script 를 짜는 게 너무 귀찮음. 비슷한 실험이 반복될 때, 결국 사용하는 리소스는 크게 안 바뀌는데 매번 똑같은 내용을 작성해야했음. 그리고 매번 직접 작성하다보니 job script에 학습에 필요한 hyper params을 잘못 입력해서 쓸모없는 학습을 돌리게 되는 경우도 많았음.

## Solution: Write a script that manages job submission
![job_scheduling_system](/assets/HPC_job_script_generator.png)

위 figure는 자동으로 스크립트를 작성해주는 프로그램의 workflow임. 예를 들어, train_manager 라는 shell function (.bashrc에 정의되어있음)을 --experiment train_with_batch_4 라는 인수로 호출함. 그러면 shell function은 python script 를 호출해서 --experiment train_with_batch_4 인수를 그대로 넘겨줌. 그럼 python script는 따로 정의된 여러 버전의 학습에 필요한 설정들을 담는 파일에서 train_with_batch_4라는 학습에 필요한 hyper params를 가져옴. 그리고 그 hyper params와 boilerplate script를 조합해서 train_script를 만듦. 그리고 그걸 다시 shell function에 넘겨줌. shell function은 그 script 를 바로 submit 해서 scheduler 에게 넘겨줌.

### Plan for this solution
먼저, job script에 필요한 부분을 따로 분리함. script에 필요한 부분은 2가지인데, 첫번째는 바로 필요한 resource, 즉, CPU와 GPU가 몇 개씩 필요한지 등의 정보. 두번째는 학습이나 추론에 필요한 hyper params. 말은 hyper params 라고 했지만 사용할 dataset의 이름 같은 것도 포함하는 실험 전반에 대한 메타정보임. 이 정보는 python 의 dataclass를 사용해서 정의함. 이게 하나의 포인트인데, python이 제공하는 dataclass 는 argparse의 Namespace 객체와 비슷하게 점 표기로 멤버 변수에 접근할 수 있음. 기존 코드에서 args 에 argparse.Namespace 값을 넣던 것을 dataclass로 바로 바꿔주면 아무 문제없이 작동함. 

## Result: How effective it was?
굉장히 효과적이었음. 단순히 시간을 좀 절약하는 수준이 아니라, job 스크립트 작성에 일어나는 실수를 방지함. 그리고 validation에 걸리는 시간이 획기적으로 줄음. 0~200 epoch 까지의 모델을 평가해야하는데 연구 미팅이 하루밖에 안 남았다? 0~10 epoch checkpoint 를 evaluation 하는 script 를 만들고, 10~20 epoch checkpoint evaluation script, 20~30 epoch checkpoint script, ... 를 순식간에 만들어서 validation에 걸리는 시간을 1/10 로 단축함. 물론 돈은 더 들지만 TSUBAME사용비를 대는 건 내가 아니라 교수님임.

어느정도 정해진 워크플로우를 깨고 새로운 방식을 도입한다는 게 꽤 저항감이 있었음. 정기적으로 연구미팅이 있고, 미팅에서 말할 필요도 없는 학습 스크립트 생성 자동화를 위해서 시간을 쓰고 있다고 말하고 싶지 않았기 때문. 그리고 연구라는 게 어떤 방식으로 흘러갈지 몰라서 내가 만든 스크립트가 새로운 실험에 쓸모없게 된다면 정말 시간을 땅에 버린거나 마찬가지니까, 프로그램을 설계할 때 생각을 많이 해야했음.
