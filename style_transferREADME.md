## 1.배경

이미지의 특성을 다른 스타일로의 전이는 어려운 작업이다.
이전의 연구에서는 시맨틱 정보나 이미지 콘텐츠에서 스타일을 분리하여 texutre 합성을 진행하며 low level feature만을 사용하는 한계가 있었다.
이에 본연구에서는 자연스러운 이미지특성과 스타일을 분리하여 style transfer 를 수행할 수 있는 새로운 알고리즘에 대해 소개하고자 하였다.


## 2.목적

대부분의 이전 texture transfer algorithms 은 texture 합성을 위해 비모수 방법에 의존하면서 대상 이미지의 구조를 보존하는 다양한 방법을 사용하였으나 구조적 한계인 낮은 수준의 이미지만 사용하여 texture transfer를 하였다.
그러나 이상적인 style transfer algorithms 은 대상 이미지에서 의미있는 특성을 추출할 수 있어야 한다.
이를 위해 본 연구에서는 일반 특징 표현이 고성능 컨볼루션 신경망에 의해 어떻게 학습되는지 보여준다.
이 네트워크는 자연 이미지의 내용과 스타일이 독립적으로 처리되고 사용되어지며, 새로운 알고리즘인 예술 스타일의 신경 알고리즘을 도입하여 이미지에 자연적 질감을 합성하는 것이 본연구의 목적이다.


## 3.필요성

이 알고리즘은 이미지의 콘텐츠를 특정 스타일에 최적화 시키는 방식으로 작동되며, 기존의 알고리즘에 비해 최대 100배이상 빨리 스타일 이미지의 화풍으로 채색한것 같은 새로운 이미지를 생성하는 최적화 기술이다.
이 기술로 기존 기술보다 빠르며 예술적 스타일에 자연적 질감이 가미된 이미지 생성에 있어 기준을 제시해 준다고할 수 있다.  



## 4.실험설계

본 연구에서는 16개의 CNN과 5개의 pooling layer로 구성된 VGG19에서 normalized 된 feature map를 활용한다.
fully conneted layer는 사용하지 않으며 이미지 합성을 위해 max pooling 대신 avg pooling을 사용했다.
@@ -31,7 +31,7 @@ size의 크기는 항상 같게 하기 위해 style image와 content image를 re

![](https://velog.velcdn.com/images%2Fgoe87088%2Fpost%2F1789ccb3-8855-46bd-a971-725d32c3939b%2Fimage.png)


## 5.검증

photograph A를 content로 하고 여러 painting에 대해 style transfer를 진행한 결과이다.

@@ -40,7 +40,7 @@ photograph A를 content로 하고 여러 painting에 대해 style transfer를 
CNN을 이용해 content와 style의 representation을 잘 분리 가능하였으며, network의 high layer에서 style representation을 얻는 것이 더좋다.
본 논문에서는 다섯개(최대) layer에서 style feature을 얻었으며, network의 high level에서 style과 content가 적절하게 merge가 된다.


## 6.결론

본 연구는 자연스러운 이미지특성과 스타일을 분리하여 style transfer 를 수행할 수 있는 새로운 알고리즘을 제시 하였다.
물론 본 알고리즘은 높은 지각된 질적 결과를 보여주지만 기술적 결함도 존재한다.
