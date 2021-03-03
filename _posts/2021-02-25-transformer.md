---
layout: post
title:  "Attention Is All You Need 공부"
date:   2021-02-25 15:00:00 +0530
tag:
- transformer
- Attention Is All You Need
---

# Ing..


# Intro

Recurrent neural networks는 t번째의 output을 얻기위해 t번째 input과 t-1번쨰 hidden state가 필요하다. 이는 모델의 병렬화를 막고 Long term dependency문제가 있다.  이것을 해결하기 위해 제안된 모델은 LSTM(Long Short Term Memory)이다. LSTM은 입력값과 이전의 출력값을 얼마나 사용하거나 잃어바릴지를 고려하는 gate를 추가하여 멀리있는 단어에도 영향을 전달 할 수 있도록한다. 하지만 이 방법도 거리의 한계는 있고 병렬처리의 어려움이 있다.   이를 해결하기 위해 ***Attention***을 사용하였다.


sample link [blog]

[blog]: https://supernova817.github.io
