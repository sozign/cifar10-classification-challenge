# cifar10-classification-challenge

- CIFAR-10 Classification을 수행하는 딥러닝 기반 네트워크의 성능을 향상시키는 프로젝트를 진행했습니다.
- `VGG(Visual Geometry Group)` 아키텍처 `VGG11`, `VGG16`, `VGG19`를 각각 epoch 20회로 훈련시켰을 때 나타나는 과적합 현상을 해소하는 방안을 중점적으로 탐구했습니다. (weight_decay, Augmentation) 세 모델의 과적합을 모두 해소한 이후에는, 그 중 가장 성능이 높았던 `VGG16` 아키텍처를 사용하여 90% 정확도의 분류 성능까지 이끌어냈습니다. 아키텍처를 VGG16으로 선정한 이후에는 팀원들이 역할을 분담하여 optimizer 3종 (Adam, RMS prop, SGD with momentum)를 사용했을 때의 정확도를 비교하였고, 셋 중 가장 성능이 좋았던 `Adam`을 Optimizer로 채택하게 되었습니다.
  <br/>
- 4종의 Scheduler를 바꿔가며 성능을 비교했을 땐 `CosineAnnealingLR Scheduler`가 가장 우수한 성능을 보여 이를 채택하였고
- Batch size 또한 16부터 128까지 바꾸어 성능을 비교하여 가장 정확도가 높은 모델을 제작하였던 64로 채택하였습니다.
- 이렇게 VGG16이라는 아키텍처를 기반으로 딥러닝 모델을 구성하는 다양한 파라미터들을 튜닝하는 방식으로 프로젝트를 진행한 결과, 92.28%의 분류성능을 갖는 VGG16 기반 CIFAR-10 Classification 모델을 제작할 수 있었습니다.

    <br/>

# 모델 요약
| 아키텍처 | 정확도 | epoch | Augmentation Type | Optimizer | Weight_decay | lr | Scheduler |
| ----|---|----|----|----|----|----|-----|
| VGG16 | 92.28% | 50 | Random Rotation <br/> Random Affine <br/> Random | Adam | 0.00005 | 0.001 | CosineAnnealing  <br/> w/ T_max = 50 |  

<br/>

# VGG16
![image](https://github.com/sozign/cifar10-classification-challenge/assets/148179726/da252c7f-2443-4494-a9e4-b00bcde731c7)

- VGG16은 크기가 3x3으로 고정된 Convolution 필터를 사용한 것이 특징인 VGG 아키텍처의 일종입니다.
- 크기 2x2, stride 2 의 Maxpooling layer를 거치며 이미지 크기가 절반으로 줄어들고, 이후에 CBR (Conv+BatchNorm+ReLU) Block을 2~3회 거칩니다.
- 총 다섯번의 동일한 Max Pooling layer를 거치며 (HxWxC) = 1x1x512 크기의 텐서로 변환되는 것이 특징이며. (input_size : 32->16->8->4->2->1), 512개의 채널을 10개의 채널과 매칭하기위해 nn.Linear가 사용되었습니다.

<br/>


# VGG 모델의 Overfitting 발견
- Validation Loss와 Training Loss를 비교할 수 있는 그래프를 제작하였습니다
- 훈련을 거듭하며 Training Loss는 줄어들고 있지만 실제 성능인 Validation Loss값은 오히려 점점 커지는 양상, 즉 OverFitting 현상을 확인할 수 있었습니다.
- 세 아키텍처 모두의 경우 Overfitting이 일어나고 있음을 확인했고, Validation Loss 개선을 위해 과적합 해소 방안을 탐구했습니다.
  <img width="810" alt="image" src="https://github.com/sozign/cifar10-classification-challenge/assets/148179726/9ba6df0f-5084-423c-88b8-4b0d8f34f3e1">

<br/>

# Overfitting의 해소
과적합 해소를 위해 다음의 방법을 시도해보았습니다.
- Augmentation
- L2 Regularization (weight_decay)
- DropOut
이 중 Augmentation과 L2가 탁월한 효과를 보였고, 최종 모델에 채택하였습니다.
<img width="809" alt="image" src="https://github.com/sozign/cifar10-classification-challenge/assets/148179726/0ca217d6-90a0-47c9-a597-c8bcdfe3bb99">


<br/>


# Augmentation
<img width="775" alt="image" src="https://github.com/sozign/cifar10-classification-challenge/assets/148179726/63f0d1ab-d224-46b4-a7eb-b44d23bb32ef">

<br/>


# 기타 요소 선정
### 1. Optimizer: Adam
<img width="804" alt="image" src="https://github.com/sozign/cifar10-classification-challenge/assets/148179726/30ebc14d-83aa-47ee-962a-ca31306f0c2f">

### 2. Batch Size: 64
<img width="806" alt="image" src="https://github.com/sozign/cifar10-classification-challenge/assets/148179726/4172589e-6409-4850-bb84-bc0dc6c5854f">

### 3. Batch Scheduler: CosineAnnealingLR
<img width="810" alt="image" src="https://github.com/sozign/cifar10-classification-challenge/assets/148179726/e8bffeef-408f-4563-a40f-2b95efd44adb">
