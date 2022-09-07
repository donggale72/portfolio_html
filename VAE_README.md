# VAE


## 1. 배경

본 연구에서는 연속 잠재 변수에 대한 효율적인 근사 추론을 위해 가변 하한의 새로운 추정기인 확률적 기울기 VB(SGVB)를 활용 하였다.<br>
제안된 추정기는 표준 확률적 기울기 방법을 사용하여 간단하게 미분하고 최적화할 수 있음을 보여주었다.<br> 
또한 iid 데이터 세트 및 데이터 포인트당 연속 잠재 변수의 경우 효율적인 추론 및 학습을 위한 효율적인 알고리즘인 AEVB(Auto-Encoding VB)를 제시하였다.<br>
이 알고리즘은 SGVB 추정기를 사용하여 근사 추론 모델을 학습할 수 있음을 보여주었다.


## 2. 목적

data와 같은 분포를 가지는 sample 분포에서 sample을 뽑고 어떤 새로운 것을 생성해내는 것이 목표다. 즉,
주어진 training data가 p_data(x)(확률밀도함수)가 어떤 분포를 가지고 있다면, sample 모델 p_model(x) 역시 같은 분포를 가지면서,
그 모델을 통해 나온 inference 값이 새롭게 생성된 x를 만드는 것이다.


## 3. 필요성

VAE는 데이터가 생성되는 과정, 즉 데이터의 확률분포를 학습하기 위한 두 개의 뉴럴네트워크로 구성되어 있다.<br>
VAE는 잠재변수(latent variable) z를 가정하고 있는데, 우선 encoder라 불리는 뉴럴네트워크는 관측된 데이터 x를 받아서 잠재변수 z를 만들어 낸다.<br> 
decoder라 불리는 뉴럴네트워크는 encoder가 만든 z를 활용해 x를 복원해내는 역할을 하게된다
.


## 4. 실험설계

데이터는 MNIST와Frey Face을 사용하였으며, MNIST의 경우 500개의 은닉 유닛을 갖고 Frey Face 데이터 세트의 경우 200개의 은닉 유닛을 갖는 생성 모델(디코더) 및 해당 인코더(일명 인식 모델)를 훈련했다.<br>
VAE는 데이터가 생성되는 과정, 즉 데이터의 확률분포를 학습하기 위한 두 개의 뉴럴네트워크로 구성되어 있습니다. VAE는 잠재변수(latent variable) z를 가정하고 있는데, 우선 encoder라 불리는 뉴럴네트워크는 관측된 데이터 x를 받아서 잠재변수 z를 만들어 내며, decoder라 불리는 뉴럴네트워크는 encoder가 만든 z를 활용해 x를 복원해내는 역할을 한다<br>
Encoder는 input을 latent space로 변환하는 역할을 한다. 이 때, encoder는 input 데이터의 posterior, 달리 말하면 input x가 주어졌을 때 latent vector z의 분포, 즉  q(z|x)를 approximate하는 것이다. 여기서 이 q(z|x)를 가장 잘 나타내는 분포로 정규분포(예시)를 선택한다면, q(z|x)를 approximate할 때는 이 정규분포를 나타내는 평균(mu)과 표준편차(sigma)의 parameter를 찾는 것이 될 것이다.<br>
Decoder는 encoder와 반대로 latent space를 input으로 변환하는 역할을 한다. 이 때, decoder는 input의 true distribution, 달리 말하면 latent vector z가 주어졌을 때 x의 분포,  p(z|x)를 approximate하는 것이다. 어떤 z라는 vector가 주어짐에 따라 다시 데이터 x를 generate하는 역할을 하기 때문에, decoder가 generative model의 역할을 하게 된다.<br>
Latent space는 말 그대로 어떤 숨겨진 vector들을 말한다. 이 latent space가 주어져야, decoder는 이를 활용해 data를 generate할 수 있다.<br> 
만약 여기서, VAE가 autoencoder처럼 input과 output을 똑같이 만드는 것을 목적으로 한다면 이 목적으로 만들어진 latent space는 항상 input과 같은 모양의 데이터를 만들어낼 수밖에 없을 것이다.<br>
<p align="center">
  <img src=https://github.com/donggale72/webtest_html/blob/main/img/%ED%99%94%EB%A9%B4%20%EC%BA%A1%EC%B2%98%202022-08-29%20171801.jpg style="width:1000px; height:500px;"/>

이를 방지하기 위해 noise를 sampling하여 이로부터 latent space를 만든다.<br> 
예를 들어, 어떤 표준 정규분포 (평균이 0이고 표준편차가 1인 정규분포)로부터 하나의 noise epsilon을 샘플링하여 얻고, 이에 encoder로부터 얻은 분산을 곱하고 평균을 더해서 latent vector z를 얻는 것이다. 이를 reparametrization trick이라고 부른다.<br>
그렇다면 위와 같은 구조를 가지는 VAE를 학습할 때는 어떤 loss를 최소화하기위해 maximum likelihood 접근법을 택한다.<br>


## 5. 검증

본연구에서 MNIST의 경우 500개의 은닉 유닛을 갖고 Frey Face 데이터 세트의 경우 200개의 은닉 유닛을 갖는 생
성 모델(디코더) 및 해당 인코더(일명 인식 모델)를 훈련했다(과다적합을 방지하기 위해 상당히 작은 데이터 세트 ). 
선택된 은닉 유닛의 수는 자동 인코더에 대한 이전 문헌을 기반으로 하며 다른 알고리즘의 상대적 성능은 이러한 선택에 그다지 민감하지 않았다.<br>

<p align="center">
  <img src=https://github.com/donggale72/webtest_html/blob/main/img/%ED%99%94%EB%A9%B4%20%EC%BA%A1%EC%B2%98%202022-08-29%20171801.jpg style="width:1000px; height:500px;"/>
  </p>

그림은 하한을 비교할 때의 결과를 보여준다. 흥미롭게도, 불필요한 잠복 변수는 과적합을 일으키지 않
았으며, 이는 변동 경계의 정규화 특성으로 설명된다.


## 6. 결 론

Generative Model을 통해 적은 data를 가지고 원래 data가 가지는 분포를 꽤 가깝게 근사하고 이를 통해 새로운 data를 생성해낼 수 있다는 점과
Latent variable의 분포를 가정해 sampling 할 값들의 분포를 control 할 수 있게 되고, manifold도 잘 학습이 되었다.<br>
이는 data의 특징들도 잘 알 수 있고, 그 특징들의 분포들은 크게 벗어나지 않게 control 하면서 그 속에서 새로운 값을 만들 수 있음을 보여준다.
