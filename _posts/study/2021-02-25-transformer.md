---
layout: post
title:  "Attention Is All You Need 공부"
date:   2021-02-25 15:00:00 +0530
tag:
- Transformer
- Attention Is All You Need


---




# Intro

2017 NIPS에서 구글이 소개한 Transformer은 NLP에서 큰 주목을 받았다. 기존의 CNN과 RNN이 주를 이뤘던 연구에서 새로운 모델을 제안했기 때문이다.

**Recurrent neural networks (RNN) ** 는 t번째의 output을 얻기위해 t번째 input과 t-1번쨰 hidden state가 필요하다. 이는 모델의 병렬화를 막고 *Long term dependency*문제가 있다.  

이것을 해결하기 위해 제안된 모델은 **Long Short Term Memory (LSTM)**이다. **LSTM**은 입력값과 이전의 출력값을 얼마나 사용하거나 잃어바릴지를 고려하는 gate를 추가하여 멀리있는 단어에도 영향을 전달 할 수 있도록한다. 하지만 이 방법도 거리의 한계는 있고 병렬처리의 어려움이 있다.   이를 해결하기 위해 ***Attention***을 사용하였다.

<br>

# Model

![Model](https://user-images.githubusercontent.com/58262251/111722408-23690a80-88a5-11eb-9dbe-c465a51da9d6.PNG)

모델의 전체 구조는 **Encoder**과 **Decoder**로 구성 되어있다.  

<br>

# Encoder

![Encoder](https://user-images.githubusercontent.com/58262251/111722399-2106b080-88a5-11eb-8318-41b24086fe0f.PNG)

**Encoder**를 나눠보면 2개의 sub layer(Self-Attention layer, Feed Forward layer)로 구분된다. 

###   Self-Attention Layer

인코더에 들어온 Input은 Self-Attention layer를 먼저 지난다.   *Self-Attention*이란 각 문장에서 각 단어끼리 얼마나 관계가 있는지를 계산해서 반영하는 방법이다.  각 단어와 다른 단어들 사이의 관계값을 계산한다. 이 관계값을 Attention Score라 한다. 

<br>

다음과 같은 단어 벡터가 입력으로 들어왔다고 하자.

![Encoder_1](https://user-images.githubusercontent.com/58262251/111722403-2237dd80-88a5-11eb-9915-4559e0faaf4e.PNG)

<br>

텍스트의 정보를 벡터로 표현하여 유사도를 계산한다. 유사도를 계산하는 방법에는 내적, 맨해튼 dist등이 있지만 Transformer 모델에선 내적을 사용한다.

![Encoder_2](https://user-images.githubusercontent.com/58262251/111722405-22d07400-88a5-11eb-9be6-e0da2135b235.PNG)

<br>

- 각 단어들의 Attention Score를 구한 후 Softmax를 사용하여 Attention Score를 확률값으로 표현한다.
- 확률값으로 표현한 Attention Score를 각 단어벡터와 곱 한후 더해서 컨벡스트 벡터를 구한다.

![Encoder_3](https://user-images.githubusercontent.com/58262251/111722406-22d07400-88a5-11eb-87e0-b5d28d3c75ef.PNG)

![Encoder_4](https://user-images.githubusercontent.com/58262251/111722407-23690a80-88a5-11eb-9a9b-b3c73b060ccc.PNG)

<br>

###      Scaled Dot-Production Attention

기본적인 Dot-Production Attention의 input은 3가지(**query, key, value**)로 구성된다. (Positional Encoding 쪽 설명)

![Scaled_Dot_Product_attention](https://user-images.githubusercontent.com/58262251/111722413-2401a100-88a5-11eb-859b-b9e6a0a7001e.PNG)

Scaled Dot-Production Attention 구조

<br>

위에서 설명한 Dot-Production Attention에서의 softmax 전단계의 값을 구할 때 더 안정적인 gradient를 갖기 위하여 Key vector의 차원값의 제곱근 만큼 나누어준다.  

*위 설명에선 "난 오늘 아침을 먹었다"의 점수를  Key vector의 차원값의 제곱근 만큼 나누어준 값이 "25, 24, 24.3, 24.1"이다.*  

![Scaled_Dot_Product_attention_function](https://user-images.githubusercontent.com/58262251/111740506-37723380-88c8-11eb-8986-31cbaa54a29a.PNG)

<br>



### Positional Encoding

모델의 전체 구조중 Encoder의 input은 Input embedding과 positional encoding이 더해져서 구해지는걸 볼 수있다.  Transformer는 RNN과 달리 단어의 위치정보를 알 수 없다. 그래서 단어 임베딩에 위치 정보를 더해준다. 이를 positional encoding (PE)이라 한다. 

Positional Encoding의 함수는 다음과 같다.

![Positional_Encoding_function](https://user-images.githubusercontent.com/58262251/111741969-b49ea800-88ca-11eb-858f-8f41e8e53b6c.PNG)

<br>

위의 식에서 pos 는 전체 시퀀스에서 몇번째 임베딩 벡터인지를 의미하고, i는 임베딩 벡터 내의 인덱스, d_model은 임베딩 벡터의 차원 수를 나타낸다.

Positional Encoding를 거친 후의 벡터는 Self-Attention Layer설명의 아래와같은 벡터가 된다.

![Encoder_1](https://user-images.githubusercontent.com/58262251/111722403-2237dd80-88a5-11eb-9915-4559e0faaf4e.PNG)

<br>

위와 같은 벡터를 구하면 학습 가능한 weight 행렬인 WQ, WK, WV로 곱하여 query, key, value를 구한다. 이후 진행은 위의 Scaled Dot-Production Attention 이다.

<br>

### Multi head Attention

![Multi_Head_Attention](https://user-images.githubusercontent.com/58262251/111722411-2401a100-88a5-11eb-8562-51d11c1f609f.PNG)

앞서 self dot-production attention에서 본 query, key, value에 대한 특징값을 헤드 수만큼 나누어 Linear layer를 거쳐 scaled dot-production을 구해 다시 합치는 과정을 거친다.



### Residual Connection

각 encoder 내의 sub-layer들은 residual connection으로 연결되어 있으며 그 후엔 layer normalize 과정을 거친다. 

![Residual_connection](https://user-images.githubusercontent.com/58262251/111747189-5e356780-88d2-11eb-8e98-e9591808ffa1.PNG)

그림과 같이 입력 정보 x가 있고 layer를 거쳐나온 F(x)가 있을 때 이 두정보를 더해 앞에있던 정보 x를 보존하게 된다.

<br><br>

# Decoder








sample link [blog]

[blog]: https://supernova817.github.io
