# attention is all you need

## 1 배경

기존 RNN 계열의 LSTM, GRU 기반 모델을 활용한 Sequence Transduction 에서는 Long-Term Dependency Problem 등이 있다.<br>
아무리 Cell의 이전 상태, 이전 문맥과의 연관성 등을 고려한다고 해도 Sequence의 길이가 길어질수록 후속 Cell로 넘어가면서 이전 Cell의 정보를 잘 보존하지 못하는 단점이 있다.<br>
또한  해당 시점의 단어와 이전 혹은 다음에 나올 단어가 얼만큼 의미적인 연관이 되어있는지를 반영하는 문제도 있었다.<br>
이런 부분이 Encoder-Decoder Model로 대표되는 seq2seq Model에서 Attention이라는 기법을 통해 어느 정도 보완이 되었다. <br>
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

<p align="center"><img src=http://jalammar.github.io/images/t/transformer_resideual_layer_norm_3.png style="width: 1000px; height: 500px;"/>
</p>
<br>

1. self-attention
2. position-wise feed forward network 이다.<br>
encoder는 구조가 6개로 쌓여져 있으며, 2개의 sub-layer가 존재하며, 각 layer 이후에는 정규화를 해 주었다.<br>
decoder는 구조가 6개로 크게 3개의 sub-layer가 존재한다.
여기서 encoder와 틀린점은 masked을 사용하였는데 이는 self-attention시 자신의 처리 이후 단어는 볼수 없도록 하여 self-attention되는 것을 막기 위함이다.


## 5 검증





## 6 결론

기존의 RNN 모델의 한계를 탈피하고자 병렬처리가 가능한 Transformer를 제안하였다.
Transformer는 병렬처리가 가능하여 빠르며, 정보손실을 어느정도 보완이 가능하여  정확하게 data를 처리할 수 있었다.
