---
layout:            post
title:             "17/12/29- machine learning  - R을 이용해 추천엔진 구축하기"
entitle:          "17/12/29- machine learning  - R을 이용해 추천엔진 구축하기"
category:          project
author:            myohyun
tags:              machine learning
---

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML'></script>

##1. 필요한 패키지 

-dplyr / data.table / reshape2

- 구축해볼 추천엔진은 협업 필터링 방식을 기반으로 한다. 협업 필터링 추천 시스템은 사용자의 주변을 고려한다. 
- A와 B 가 동일한 관심사를 가지고 있다면 미래에도 비슷한 취향을 갖는다 가정한다.  A가 구매하지 않은 책을 B가 구매했을 경우 A에게 그 책을 추천해준다. 
- 사용자 간의 유사도 점수를 계산 -> 유사도 점수를 기반으로 사용자에게 아이템을 추천하는 기능

##2. 기본 추천엔진 구축 순서 

영화추천

1. 데이터 로드 및 형식 변환
2. 사용자 사이의 유사도 계산하기(유사도 행렬 계산)
3. 사용자의 등급 예측하기
4. 사용자 유사도 점수를 기반으로 사용자에게 아이템 추천



###2-1. 데이터 로드 및 형식 변환

```R
ratings = csv.open("/home/ymmu/Downloads/Building_recommendation_engines_code/Chapter 2/ratings_3.csv")
dim(ratings) #결과: [1] 100004      3 3개의 header에 대한 100004개의 행
str(ratings) 
#'data.frame':	100004 obs. of  3 variables:
# $ userId : int  1 1 1 1 1 1 1 1 1 1 ...
# $ movieId: int  31 1029 1061 1129 1172 1263 1287 1293 1339 1343 ...
# $ rating : num  2.5 3 3 2 4 2 2 2 3.5 2 ...

#행렬을 만든다
movie_ratings = as.data.frame(reshape2::acast(ratings, userId~movieId, value.var = "rating"))
#userId(열쪽)~movieId(행쪽)?, 값으로는  rating 이 들어간다.

```

###2-2 사용자 사이의 유사도 계산

유사도 측정방법: 유클리드 거리, 코사인 거리, 피어슨 상관계수, 자카드 거리 등과 같이 다양하다.(4장-추천엔진을 위한 데이터마이닝 참조)

여기서는 상관관계를 이용. 

이유:상관관계가 두 아이템의 연관도 또는 두 아이템 벡터가 얼마나 밀접하게 공변하는지 또는 서로 연관됐는지를 보여주기 때문

###2-3. 사용자의 등급 예측하기

1. 타깃 유저가 아직 평가하지 않는 연화들을 골라냄
2. 다른 리뷰어들이 제공한 해당 영화의 모든 등급들만 따로 분리
3. sim_rating=해당 영화들의 등급 * (리뷰어와 타깃 유저 사이의) 유사도
4. 해당 영화의 예상 등급: $$ \frac{\sum sim_rating}{\sum similarity} $$ for `movie i`, `user j` 

### 2-4. 사용자 유사도 점수를 기반으로 사용자에게 아이템 추천

1. `user i` 의 평균 rating 등급 구함 -> 평균 등급보다 큰 아이템 추천 



첫번째 학습을 마치며:

1. R의 함수에 대해 이해도가 필요함
2. 등급을 예측할 때 저 수식이 어떻게 세워졌는지 이해가 필요함

배운 것들:

1. R파일에 함수를 작성한 후 불러쓰려면 `source("파일경로/파일이름.R")`를 이용하면 된다
2. install.packages("팩키지 이름")->인스톨, library("팩키지이름")-> 팩키지 안의 함수를 `팩키지이름::함수`처럼 안 부르고 함수명으로만 사용할 수 있다
3. 테이블의 컬럼지칭 : `테이블이름$컬럼이름`
4. `pdlyr`, `data.table` : 데이터 조작에 사용되는  R 패키지. 탐색적 분석과 데이터 조작에 매우 유용. 
5. `reshape2`: 일반적으로 데이터를 재구성하기 위해 사용하는 R 패키지.

   ```  R
%>% #여러 개의 코드를 그룹화하는 데 유용한 함수
acast()# reshape2/ 데이터프레임을 행렬 형식으로 만든다
setDT()# data.table/ 값 대신 참조를 통해 입력 데이터를 처리한다. 다시 말해, 데이터를 변환하는 동안에 물리적으로 데이터를 복사하지 않는다.
   ```

