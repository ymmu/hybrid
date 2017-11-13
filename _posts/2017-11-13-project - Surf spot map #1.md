---
layout:            post
title:             "17/11/13 - project - Surf spot map"
menutitle:         "17/11/13 - project - Surf spot map"
category:          project
author:            myohyun
tags:              js-node
---


# Today's work - 11/13 mon

## project- noname
1. 관심스팟을 데이터로 받아온 후 sql에 업데이트
2. server.js - map/ 은 오늘날짜의 관심스팟들의 데이터를 모두 불러온다
3. server.js - map/(hour) 는 오늘날짜-관심스팟들의 현재 시간대에 가장 가까운 데이터만 불러온다. ->구글맵 스팟을 클릭했을 때 infowindow에 뿌려주기 위해 만들었음. (근데 나라별로 시간대가 다르잖아..? 그걸 생각을 못했네;;)
4. 4.infowindow.js - 서버로부터 데이터를 받아온 후 map을 초기화하도록 만듦. getJSON 함수 콜백으로 initMap()내용- 마커와 infowindow 세팅 내용을 넣어줌.
5. sql문 및 기타 자잘한 syntax error 수정. 정말 자잘해서 생각도 잘 안 난다...(...)

<figure>
<img src="{{ "/media/img/17_11_13_0.png" | absolute_url }}" />
<figcaption>A window info is shown when spot is clicked </figcaption>
</figure>

## To do list (in order of priority)


1. 서버 및 기타 파일들 관계 문서화. -> 이거 좀 잘 정리하는 법 뭐 없나...;;

2. 자바스크립트 비동기/동기 처리 부분 정확하게. 자주 쓰는 함수 중 비동기 처리가 기본으로 되어있는지. getJSON()은 일단 async function 인 듯...

3. express 에서 app.get(...)을 쓰려했더니 not resolvec 라고...뭥미? 버전 확인을 했는데 4.16.였는데. router 뗌에 이제 지원을 안 하는 건가? 싶기도 하고. app.get(..) 시리즈와 router와의 차이, Router에 대해 좀 정리해놔야 할 듯하다.

4. #### 내일은 뭐할까? 
- 아이디어 수집..어떤 기능을 추가할까? 사이트의 목적을 명확하게.
- 이미 다녀왔던 스팟은 색깔을 다르게 표시하기.
- infowindow에 제목 등을 클릭하면 7일간 스팟 컨디션 다 볼 수 있게.
- 다녀온 스팟의 나라의 색깔을 바꾸기. 여러 스팟을 다녀온 곳일 수록 색깔을 진하게 한다던지.
- 다녀온 스팟 언제 다녀왔는지.
- 스팟 기본 정보 페이지 만들기. 스팟에 대해 자주 리뷰되는 단어 등을 크롤링 해 뿌려주기?
- 세션을 이용해서 가장 최근에 언제 접속했는지 표시
- favorite 스팟으로 설정하기.
- 경로 선으로 연결하기(for what?)
- 상급. 중급. 초보 등등도 표시할 수 있게.
- 로컬스팟인지도 표시할 수 있을 듯

## TIL
1. 미들웨어(app.use(...))는 적은 순서대로 실행된다. next()로 넘겨줘야 실행된다. 
2. google map 다루기. 마커와 infowindow 처리. 아직 더 재밌는 것들을 할 수 있을듯..

