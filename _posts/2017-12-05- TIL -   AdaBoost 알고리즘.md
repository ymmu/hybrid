---
layout:            post
title:             "17/12/05 - TIL - AdaBoost 알고리즘"
menutitle:         "17/12/05 - TIL - AdaBoost 알고리즘"
category:          Machine learning
author:            myohyun
tags:              python
---
<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML'></script>

# AdaBoost 알고리즘

책에는 에이다부스트라 해석해놓았지만, 동영상에서는 "에다부스트"가 맞다고 생각하는데 Ada가 adaptive에서 나왔기 때문이라 한다. 그리고 앙상블..ensemble..언셈블..

책으로 먼저 봤지만 뭔가 이 책이 뒷부분으로 오면서 번역이 점점 별로가 되어가는 듯한 느낌이...^^; 구글링을 하다 udacity의 동영상이 길이도 짧고/알아듣기도 좋고/설명도 잘 되어있어서 주소를 첨부한다.

- [Ensemble learner](https://youtu.be/Un9zObFjBH0)
- [Bootstrap aggregating bagging](https://youtu.be/2Mg8QD0F1dQ): 각각의 learner를 **다른 데이터셋** 으로 훈련시킨다.
- [Boosting](https://youtu.be/GM3CDQfQ4sw): Bagging에서 단순하게 변화를 준 것인데, 시스템이 잘 예측하지 못하는 부분에 초점을 맞춤으로써 learner를 향상시키기 위해 노력함. 단순한 bagging 결과보다 overfitting하는 경향이 있다.

책의 내용을 처음 읽었을 땐 이게 뭔 내용인고? 싶었는데 여러 번 읽고, 코드의 문법도 찾아보고, 특히 위의 동영상을 보고 다시 코드를 읽어보니 무슨 내용인지 이해가 간다.

ensemble method는 여러가지 learner를 조합해서 만들 수 있는데, 책에선 단순히 의사결정트리의 하나의 가지(stump)만으로 데이터를 -1과 1로 분류한다.  앞 페이지에서 의사결정트리를 건너뛰고 해서 용어가 좀 생소했지만 stump라 하는 것은 

예를 들어, 다음과 같은 의사결정식을 보았을 떄

```
           x -> [ larger than 1 ? ]   -(yes)-> 1
                                      -(no) -> -1
```

에서 분류항목을 말한다고 이해하면 될 것 같다.  책에서는 단일 스텀프라 했으므로 저런 예처럼 검사항목이 하나만 있는 것으로 보면 될 것 같다.  

책에서 분류를 위해 쓴 단일스텀프는 데이터셋의 column 마다  min, max를 이용해 column 안의 요소들의 분포범위를 구한 후 `umSteps` 로 나눠서 구한 수를  `stepSize`라 정의한다.

 $$stepSize = \frac{max-min}{numSteps} $$

이 `stepSize`를  `min`값에 하나하나 더해가며 요소 분포범위 안에 위치하는 임계점-`threshVal`을 만들고, 각 `threshVal`에 의해 데이터셋의 column 요소를 1과 -1로 나눠서 예측값을 내놓는다.

데이터셋:

|  x   |  y   | 분류 label |
| :--: | :--: | :------: |
|  1   | 2.1  |    1     |
|  2   | 1.1  |    1     |
| 1.3  |  1   |    -1    |
|  1   |  1   |    -1    |
|  2   |  1   |    1     |

 x column에서

`max`=2, `min`=1, `numSteps`=10으로 잡으면 `stepSize` = 0.1

임계값:  1, 1,1 1,2 1,3 ... 1.9, 2,0가 구해진다. (책에서는 `min` 보다 작은 임계점도 있었음)이 모든 임계값에 대해 의사결정stump를 돌려 예측값을 얻어보자. 

1. 만약 임계값보다 작거나 같으면 -1, 크면 1를 준다 가정을 "lt"

2. 만약 임계값보다 크면 -1, 작거나같으면 1를 준다 가정을 "gt"

    x축 조사

| 임계값(threshVal) |    예측한 값(lt)     |   예측한 값(gt)    |
| :------------: | :--------------: | :------------: |
|       1        |  [-1,1,1,-1,1]   | [1,-1,-1,1,-1] |
|      1.2       |  [-1,1,1,-1,1]   | [1,-1,-1,1,-1] |
|      1.3       |  [-1,1,-1,-1,1]  | [1,-1,1,1,-1]  |
|      ...       |       ...        |      ...       |
|      2.0       | [-1,-1,-1,-1,-1] |  [1,1,1,1,1]   |

   y축에 대해서도 같은 작업으 해준다.

   x,y 축에서 조사된 예측값 중 데이터셋의 분류라벨과 비교하여 오류율이 가장 작은 것을 `bestClassEst` 라고 둔다.

```python
예측값: [-1,1,-1,-1,1]
데이터셋분류값:[1,1,-1,-1,1]
오류율(error): 0.2
    ...
가장 작은 오류율을 가진 예측값: bestClassEst에 저장
```

`bestClassEst`의 오류율`error`을 이용해 가중치를  `D`를 업데이트 한다. 가중치`D`는 예측값의 요소가  훈련셋의 라벨의 요소와 다르면 업데이트 되면서 가중치가 올라가고, 같으면 가중치가 줄어들게 설계되어있다.

`D` **구하는 법:**

if(예측값의 요소 == 훈련셋의 라벨 요소)

$$ D_{t+1}=\frac{D_{t}*e^{+\alpha}}{sum{D}}$$

else

$$ D_{t+1}=\frac{D_{t}*e^{-\alpha}}{sum{D}}$$



**알파 구하는 법:**

$$ \alpha = 0.5 *ln(\frac{1-\epsilon}{\epsilon}) (\epsilon = 오류율)$$



이렇게 업데이트 된 `D`는 사실 계산에는 사용이 안 된다. (...)

예측값을 업데이트하는 건 $$\alpha$$ 값이다. $$\alpha$$와  `bestClassEst`를 이용해 현재 분류기의 결과가 추가된 예측값 `gregateClassEst` 를 구해준다. 

```python
aggregateClassEst  += alpha * bestClassEst 
## aggregateClassEst는 반복할 수록 계속 누적되면서 업데이트 되는 것임
```

`aggregateClassEst`  는 정수가 아니므로 `sign`함수를 이용해 1,-1로 분류해준다. 

(`sign`함수는 0보다 크면 1, 작으면 -1, 0이면 0을 리턴한다) 

`aggregateClassEst`으로부터 정제된 예측값을  데이터셋의 라벨값과 비교하여  `error`를 살펴본다. `error`= 0 이라면 멈추고 아니면 설정한 횟수만큼 반복잡업해준다. 책에서는 다른 여러  learner들을 사용한게 아니라 한 종류의 의사결정stump만 썼기 때문에 같은 learner로 여러 번 작업해준거임.

동영상에서는 가중치 $$\alpha$$얘기는 없다. 핵심개념만 설명해서 그런듯. 단지 첫번째 분류기가 잘못 구분한 데이터들을 다음 분류기의 데이터셋에 포함해서 분류하게 만든다고 설명한다.

 책에서는 $$\alpha$$가 adaBoost 에서 사용된 분류기중 오류율이 높은 분류기에는 가중치 `D` 가 높게 나오도록 설정한다.

