# AN IMAGE IS WORTH 16X16 WORDS: TRANSFORMERS FOR IMAGE RECOGNITION AT SCALE

## 1 배경

Self-attention기반의 구조들은 많이 알려졌듯이 자연어 처리분야에서 많이 사용되어 왔다. <br>
특히 큰 텍스트 코퍼스에서 사전 학습하고 작은 task-specific dataset에서 fine-tuning 하는 BERT와 같은 방식이 우세하였다.<br>
하지만, 비전분야에서는 Convolutional 구조가 아직까지 우세하게 사용되고 있다.<br>
NLP에서의 성공에 영감을 받아서 다양한 연구에서 CNN 같은 구조를 self-attention과 결합하려고 시도해왔는데, 가장 최근의 모델들은 이론상으로는 효과적이었지만, 특수한 어텐션 패턴 사용 때문에 현대 하드웨어 가속기에서 효과적으로 스케일링이 불가능했다.<br> 
NLP에서의 트랜스포머 스케일링이 성공한 것에 영감을 받아, 본 논문에서는 standard transformer를 최소한의 수정으로 직접 이미지에 적용하는 것에 대해 실험을 했다


## 2 목적

비전분야에 Self-attention을 적용하려는 많은 연구가 진행되었으나 아직 성공한 사례는 없는 실정이었다.<br>
이에 본 연구에서는 Self-attention을 적용하여 기존에 주류로 인식되었던 비전분야의 CNN 이미지 분류를 넘어설 수 있는 성능을 가진 모형을 제시하는것이 목적이다.


## 3 필요성

기존의 attention 기반의 모델들은 이론적으로 좋음에도 불구하고 특성화된 attention 패턴 때문에 효과적으로 다른 네트워크에 확장하기가 어려웠다.<br>
하지만 본 연구에서 적용한 모델은 transformer 구조를 거의 그대로 사용하기 때문에 확장성이 좋다. <br>
또한 Vision Transformer는 많은 이미지 분류 데이터 세트에서 최신 기술과 일치하거나 그 이상이지만 사전 훈련 비용은 비교적 저렴하다는것을 보여주는 모형이라할 수 있다.


## 4 실험설계

Vision Transformer를 학습할 때, 다음과 같은 방식을 사용하였다.<br> 
① large 데이터셋으로 사전학습 후 더 작은 데이터셋에 대해 fine-tune하는 방식 (이미지 resize 및 MLP 헤드 부분을 클래스 수에 맞게 교체.)<br>
② 학습을 위해 large 데이터셋인 ImageNet, ImageNet-21k, JFT 사용<br>
③ 전처리는 Resize, RandomCrop, RandomHorizontalFlip 사용<br>
④ 광범위하게 Dropout 적용 (qkv-prediction 부분 제외)<br>
⑤ ImageNet, CIFAR10/100, 9-task VTAB 등 데이터셋에 대해 transfer learning을 진행<br>

<p align="center">
<img src=https://production-media.paperswithcode.com/methods/Screen_Shot_2021-01-26_at_9.43.31_PM_uI4jjMq.png style="width: 800px; heigth: 500px;"/>
  </p>

사전 학습 조건은 다음과 같다.<br>
Optimizer : ADAM <br>
스케줄링 : linear learning rate decay <br>
weight decay : 0.1 (강한 regularization 사용)<br>
배치 사이즈 : 4,096<br>
Label smoothing 사용 (regularization 사용)<br>
validation accuracy 기준 early-stopping<br>


transfer learning 학습 조건은 다음과 같다.<br>
Optimizer : SGD 모멘텀<br>
스케줄링 : cosine learning rate decay<br>
weight decay : 미적용<br>
grad clipping 적용<br>
배치 사이즈 : 512<br>




## 5 검 증

테스트는 ViT-Base, Vit-Large, Vit-Huge 3가지 모델에 대하여 진행하였습니다. 아래 표의 /14, /16은 패치 크기를 나타낸다.<br>
두번째 표에서 성능을 살펴보면 CNN 모델에 비해 성능이 좋다는 것을 보여주고자 합니다. 특히, 학습 시간도 절약한 것이 인상적이다.<br> 

<p align="center"><img src=https://github.com/donggale72/webtest_html/blob/main/img/%ED%99%94%EB%A9%B4%20%EC%BA%A1%EC%B2%98%202022-09-07%20123452.jpg style="width: 800px; heigth:500px;"/>
  </p>

데이터의 특성에 따라 정확도가 달라질 수 있기 때문에, 여러 데이터의 유사한 종류의 데이터를 묶어서 성능을 비교하였다.<br>
이 때에도 ViT의 성능이 좋은 점을 보여주고자 하였다.<br>

<p align="center"><img src=https://github.com/donggale72/webtest_html/blob/main/img/%ED%99%94%EB%A9%B4%20%EC%BA%A1%EC%B2%98%202022-09-07%20123518.jpg style="width: 800px; heigth: 500px">
  </p>

위는 VTAB task에 대해 기존의 SOTA와 비교한 그래프인데 ViT-H/14가 모든 task에서 가장 뛰어난 정확도를 보여주는 것을 알 수 있다.<br>

## 6 결 론

트랜스포머 구조가 자연어 처리 task들에서 사실상 표준이 되는 동안, 비전에 이를 적용한 사례는 한정되어왔다. <br>
비전 분야에서 어텐션은 Convolutional network과 함께 적용되거나, Convolutional network의 특정 요소를 대체하기 위해 사용되었기 때문이다.<br>
본 논문에서는 이러한 CNN에 대한 의존이 필요하지 않고 순수 트랜스포머가 곧바로 이미지 패치들에 사용되고 이미지 분류에 잘 작동함을 보여주었다.<br> 
많은 양의 데이터에서 사전학습되고 여러 중간 사이즈나 작은 사이즈의 이미지 인식 데이터셋(ex. ImageNet, CIFAR-100)으로 전이 학습될 시에 Vision Transformer는 기존의 convolution기반의 SOTA 결과들에 비교해서 계산량은 적으면서도 훌륭한 결과를 보여주었다.
