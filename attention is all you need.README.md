## attention is all you need

기존 RNN 계열의 LSTM, GRU 기반 모델을 활용한 Sequence Transduction 에서는 Long-Term Dependency Problem이 있다.<br>
아무리 Cell의 이전 상태, 이전 문맥과의 연관성 등을 고려한다고 해도 Sequence의 길이가 길어질수록 후속 Cell로 넘어가면서 이전 Cell의 정보를 잘 보존하지 못하는 단점이 있다.<br>
또한  해당 시점의 단어와 이전 혹은 다음에 나올 단어가 얼만큼 의미적인 연관이 되어있는지를 반영하는 문제도 있었다.
