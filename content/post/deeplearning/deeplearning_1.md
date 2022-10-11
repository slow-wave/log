---
title: "[deeplearing] CNN"
date: 2022-10-11T16:04:21+09:00
draft: false
tags: ["deeplearing"]
categories: ["deeplearing"]
showToc: true
UseHugoToc: true
comments: true
---

## Fully Connected Layer

- fully connected layer만으로 구성된 인공신경망의 입력데이터는 1차원(배열) 형태로 한정됨.
- 한 장의 컬러 사진은 3차원 데이터임. (R,G,B)
- 따라서 사진 데이터로 Fully Connected 신경망을 학습시켜야할 경우, 3차원을 1차원으로 평면화시켜야함.
    - 평면화를 시키면 공간 정보 손실 발생 → 신경망이 특징을 추출 및 학습할 때 정확도 높이는데 한계
    - 이미지의 공간 정보를 유지한 상태로 학습이 가능한 모델이 CNN임.

## CNN(Convolution neural networks)이란?

- 합성곱(convolution) 이라는 연산을 사용하는 신경망
- 이미지 분류 작업에서 좋은 성능을 보여줌.
- 시각 피질에 대한 실험에서 얻은 데이터에서 영감을 얻은 특별한 구조를 사용함.
    - 사람의 시력은 여러 피질 단계로 이뤄져 있고, 각 피질 단계는 점점 더 구조화된 정보를 인식함.
    - 단일 픽셀을 본 후 거기서부터 단순한 기하 형태를 인식하고 물체, 얼굴, 인체, 동물 등과 같이 복잡한 요소를 인식
- CNN에서는 완전 연결신경망과 달리 뉴런을 kernel(filter)이라고 함.
    - kernel은 입력 데이터와 합성곱 연산을 수행하게되는 행렬임.
- convolution 계산을 통해 얻은 출력을 feature map이라고 함.
- Convolution Layer는 Filter의 크기, Stride, padding 적용 여부, Max Pooling 크기에 따라서 출력 데이터의 Shape이 변경됨.

## CNN의 구조

- 이미지의 특징을 추출하는 부분과 클래스를 분류하는 부분으로 나눌 수 있음.
- Convolution Layer와 Pooling Layer를 여러 겹 쌓는 형태로 구성됨.
    - Convolution Layer - 입력데이터에 필터를 적용 후 활성화 함수를 반영하는 필수 요소임.
    - Pooling Layer - 선택 요소임.
- Fully Connected Layer
- Flatten layer - 이미지의 특징을 추출하는 부분과 이미지를 분류하는 부분 사이에 이미지 형태의 데이터를 배열 형태로 만드는 레이어

## Convolution

- 합성곱 연산은 두 함수 f,g 가운데 하나의 함수를 반전(reverse), 전이(shift) 시킨 다음, 다른 하나의 함수와 곱한 결과를 적분하는 것을 의미함.

## Channel

- 이미지 픽셀 하나하나는 실수임. 컬러 사진은 천연색을 표현하기 위해, 각 픽셀을 RGB 3개의 실수로 표현한 3차원 데이터임.
- Convolution Layer에 유입되는 입력데이터에는 한 개 이상의 필터가 적용됨.
- 1개 필터는 Feature Map의 채널이 됨.
    - convolution layer에 N개의 필터가 적용된다면 출력데이터는 N개의 채널을 갖게됨.

## Filter & Stride

### Filter

- 이미지의 특징을 찾아내기 위한 공용 파라미터임.
- 필터는 일반적으로 (4,4)나 (3,3)과 같은 정사각 행렬로 정의됨.
- CNN에서 학습의 대상은 필터 파라미터임.
- 하나의 Convolution Layer에 크기가 같은 여러 개의 필터를 적용할 수 있음. → 입력 데이터에 적용한 필터의 개수는 출력 데이터인 Feature Map의 채널이 됨.
- 입력 데이터를 지정된 간격으로 순회하며 채널별로 합성곱을 하고 모든 채널의 합성곱의 합을 Feature map(Activation Map)으로 만듦.
- 입력 데이터에 적용한 필터의 개수는 출력 데이터인 Feature map의 채널이 됨.

### Stride

- 지정된 간격으로 필터를 순회하는 간격을 stride라고 함.

## padding

- Convolution layer에서 Filter와 Stride의 작용으로 Feature Map의 크기는 입력데이터보다 작음.
- 신경망에 Kernel을 적용하면 층이 깊어지면서 데이터의 차원이 줄어듦. → Convolution Layer의 출력 데이터가 줄어드는 것을 방지하는 방법이 padding임.
- padding은 입력 데이터 주변을 특정값으로 채우는 것을 의미함.
    - 보통 0으로 채워 넣음.
- 외각을 0 값으로 둘러싸는 특징으로부터 인공 신경망이 이미지의 외각을 인식하는 학습 효과도 있음.

### padding의 종류

- same padding
    - 입력과 feature map의 크기를 동일하게 만들기 위해 입력 주위에 0으로 패딩하는 것임.
- valid padding
    - padding 없이 순수한 입력 배열에서만 합성곱을 하여 특성맵을 만드는 경우임. → feature map의 크기가 줄어듦.

## Pooling Layer

- 데이터의 차원을 줄이거나 특정 데이터를 강조하는 용도로 사용됨.
- 합성곱에서 stride를 크게 하여 특성맵을 줄이는 것보다 풀링 층에서 크기를 줄이는 것이 경험적으로 더 나은 성능을 내기 때문임.
- pooling layer를 통과하면 행렬의 크기 감소
- pooling layer를 통해서 채널 수 변경 없음.
- 학습 대상 파라미터 없음.

### Pooling의 종류

- Max pooling - CNN에서 주로 사용하는 방법
- Mean pooling
- Average Pooling

## CNN의 구성

- Filter, Stride, Padding을 조절하여 특징 추출(feature extraction) 부분의 입출력 크기를 계산하고 맞추는 방법이 중요함.

{{< figure src="/images/deeplearning/deeplearning_1/1.png">}}

**그림 8**: 전형적인 CNN, [출처](https://www.researchgate.net/figure/Architecture-of-our-unsupervised-CNN-Network-contains-three-stages-each-of-which_28343325)

### 참고 자료

- **[TAEWAN.KIM 블로그](http://taewan.kim/post/cnn/)(CNN, Convolutional Neural Network 요약)**