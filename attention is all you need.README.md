# attention is all you need

## 1 배경

기존 RNN 계열의 LSTM, GRU 기반 모델을 활용한 Sequence Transduction 에서는 Long-Term Dependency Problem 등이 있다.<br>
아무리 Cell의 이전 상태, 이전 문맥과의 연관성 등을 고려한다고 해도 Sequence의 길이가 길어질수록 후속 Cell로 넘어가면서 이전 Cell의 정보를 잘 보존하지 못하는 단점이 있다.<br>
또한  해당 시점의 단어와 이전 혹은 다음에 나올 단어가 연관이 되어있는지를 반영하는 문제도 있었다.<br>
이런 부분이 Encoder-Decoder Model로 대표되는 seq2seq Model에서 Attention이라는 모델을 통해 어느 정도 보완이 되는것을 확인하였다. <br>
Attention에 대해 간략히 요약하자면 Serial하게 나열된 각각의 Decoder Cell의 특정 시점에서 Encoder의 입력 시퀀스 정보를 참조한다는 것이다.  <br>
이 때 입력 시퀀스(이를테면 문장)를 이루는 각각의 단어를 모두 동일한 비율로 참조하는 것이 아닌 해당 시점의 디코더와 연관이 높은 비율로 차등 적용하여 참조한다. <br>
Attention이란 말 그대로 Decoder Cell 입장에서 가장 연관성이 높은 Encoder의 입력을 주의(Attention)하여 Input Sequence(문장)을 이루는 각 단어들간의 의미 연관 정보를 반영하는 기법이다.


## 2 목적
  
Encoder/Decoder를 이루는 각 Cell을 LSTM이나 GRU 혹은 바닐라 RNN 등의 Recurrent Unit로 구성하면 Recurrent한 특성 때문에 이전 시점의 연산이 완료되야만 다음 시점으로 넘어갈 수 있다.<br>
즉 모델의 구조상 Serial하게 연산이 되는데 이는 전체 학습시간이 많이 걸린다는 단점이 있다.<br>
이러한 문제들을 보완하기 위해 Attention Model로만 Encoder/Decoder를 구성하여 모형을 제시하고 이러한 문제를 해결하는 것이 본 연구의 목적이다.

## 3 필요성

self-Attention 모델이전 대부분의 자연어 처리는 encoder-decoder 구조를 가지는 Rnn, Cnn 등의 모델로 구현되었으나 병렬처리의 불가능과 속도가 느리다는 단점이 있었으며 이와 함께 정보손실이 발생하기도 했다.<br>
위와 같은 한계점을 해결하기 위해 본연구에서 self-Attention 모형을 제시하였다.<br>
실험 결과 병렬처리가 가능하여 기존 모델 보다 빠르며, 정보손실을 어느정도 줄일 수 있었다.<br> 


## 4 실험설계

모델구조는 아래의 2개 구조를 합한것과 같다.<br>

<p align="center"><img src=http://jalammar.github.io/images/t/transformer_resideual_layer_norm_3.png style="width: 1000px; heigth: 500px;"/>
</p>
<br>

1. self-attention
2. position-wise feed forward network 이다.<br>
encoder는 구조가 6개로 쌓여져 있으며, 2개의 sub-layer가 존재하며, 각 layer 이후에는 정규화를 해 주었다.<br>
decoder는 구조가 6개로 크게 3개의 sub-layer가 존재한다.<br>
여기서 encoder와 틀린점은 masked을 사용하였는데 이는 self-attention시 자신의 처리 이후 단어는 볼수 없도록 하여 self-attention되는 것을 막기 위함이다.

### Encoder

인코더는 6개의 동일한 층으로 구성된다. <br>
각 층은 2개의 sub-layer이 있으며 1번째는 multi-head self-attention mechanism이고 2번째는 pointwise fully-connected 순전파 네트워크이다.<br>
각 단어의 위치 정보를 인코딩해서 임베딩 출력 값과 합체 → inputMulti-Head attention: self-attention을 병렬적으로 사용<br>
Encoder의 전체적 flow : Multi-Head self-Attention 도착해서 Attention 수행 → Residual Connection & Layer → Normalization → Feed-Forward 도착 → Residual Connection & Layer Normalization → output 이 과정을 6번 반복한다.<br>
각 sublayer의 결과 값 = LayerNorm(x+Sublayer(x))<br>
Sublayer(x) : sublayer에서 입출력 dimension은 d_{model} = 512 <br>
(임베딩 벡터의 차원과 Sublayer의 출력 벡터 차원이 같아야 하므로 본 논문에서는 512 차원으로 통일)<br>

### Decoder

Encoder도 Decoder와 같이 6개의 동일한 층으로 구성된다.<br>
Encoder와 다르게 총 3개의 sub-layer을 갖고 있음 → 2개는 기존의 Encoder의 sub-layer와 동일 + 1개는 Encoder의 ouput에 대해 Multi-head Attention을 계산하는 sub-layer이다.<br>
따라서, Decoder은 Encoder 구조 뒤에 오는 단어를 미리 알지 못하게(앞에 있는, 아는 단어로만 예측했다고 확실히 하기 위해서) 마스킹한 Attention layer을 갖고 있다.<br>
Encoder과 비슷하게 Residual Connection & Layer Normalization이 각 sub-layer의 정규화 layer 뒤에 있다<br>

### Attention

Attention에는 Query, Key, Value 벡터가 있다.<br>
출력은 이 3가지 벡터의 가중치 합으로 나타난다. <br>
Query = 값을 구하고자 하는 단어 <br>
Key = 해당 문장에서 추출된 단어<br>
Value = 각 단어의 값 <br>
으로 구조화 하였다.


## 5 검증

모델의 성능은 2개의 Task에 대해 각각 아래와 같이 달성했다고 한다.<br> 
논문이 나온 시점에서 최고 수준의 성능을 보여주었다.<br>

- WMT 2014 English-to-German Translation task ->  28.4 BLEU<br>

- WMT 2014 English-to-French Translation task -> 41.0 BLEU<br>


<p align="center"><img src=https://github.com/donggale72/webtest_html/blob/main/img/%ED%99%94%EB%A9%B4%20%EC%BA%A1%EC%B2%98%202022-09-07%20142746.jpg     style="width: 800px; heigth: 500px"/>
  </p>
  
WMT 2014 English-to-German translation 태스크와 English-to-French translation 태스크에서 이전 SOTA 모델을 능가하는 성능을 기록했다.<br> 
Transformer을 이용함으로써 학습 효율은 높고 성능은 더욱 개선되었음을 알 수 있다.
기계번역 태스크 이외에도 구문 분석과 같은 다른 NLP 태스크에 적용해본 결과, 별도의 미세 조정 없이도 SOTA 결과를 보임을 알 수 있었다.<br> 




## 6 결론

Transformer는 recurrence와 convolution을 모두 제거한, 오직 Attention에만 의존하는 새로운 종류의 모델이다.<br> 
기계번역 태스크 이외에 다른 NLP 태스크에서도 기존 RNN이나 CNN을 사용하는 모델보다 훨씬 학습 효율이 높을 뿐더러 성능이 높다.<br>
따라서 본 논문에서는 기존의 RNN 모델의 한계를 탈피하고자 병렬처리가 가능한 Transformer를 제안하였다.<br>
Transformer는 병렬처리가 가능하여 빠르며, 정보손실을 어느정도 보완이 가능하여  정확하게 data를 처리할 수 있으며 성능 또한 높음을 알수 있다.
