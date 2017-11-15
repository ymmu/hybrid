---
layout:            post
title:             "17/11/15 - TIL - MongoDB 사용/ app.set()"
menutitle:         "17/11/15 - TIL - MongoDB 사용/ app.set()"
category:          js/node
author:            myohyun
tags:              js-node
---

# MogoDB란?

MongoDB는 비관계형 데이터베이스.
mysql같은 관계형 데이터베이스를 쓰다 MongoDB를 쓰니 이건 또 뭔 이상한 놈인가 싶은 생각이 먼저 들었는데 알면 알 수록 기묘한 매력이 있다?

-관계형 데이터베이스는 시스템의 신뢰도를 높이는 데 필요한 장치를 많이 가지고 있음. 그러나 SQL문을 읽어들이고 실행하는 데 많은 리소스를 사용하며 이 때문에 성능이 떨어지는 경우가 있음.

-NoSQL 데이터베이스는 성능을 최우선으로 생각하기 때문에 실시간으로 처리해야 하는 경우나 대용량 트래픽을 감당할 수 있는 메시징 시스템 등에 활용. 특히 클라우드 서비스로 서버를 구성하는 경우가 많아지면서 많은 사용자를 수용하거나 시스템 자원을 적게 소모하는 NoSQL 데이터베이스에 점점 더 관심을 갖게 됨.

-MongoDB는 NoSQL이기 때문에 관계형 데이터베이스의 테이블 개념이 없음. 그 대신 몽고디비는 여러 데이터가 모인 하나의 단위를 컬렉션(Collection)이라고 부른다. 컬렉션 안에 내가 집어넣고 싶은 문서를 자유롭게 넣을 수 있다. 그냥 사용자 지정 묶음을 컬렉션이고 그

# MongoDB 사용 

처음부터 모듈로 분리해서 작업했음...

1. 'mongoose'로 Schema 설정할 수 있다.
2. 'mongoose'의 model()함수를 이용해서 사용자가 만든 schema를 적용한 객체(자바론 class 같은 것)를 만들 수 있다.

```c
var mongoose = require('mongoose');
var UserSchema = mongoose.Schema({
	id:{type:String}
});

UserSchema.static('함수이름', function(){ /*함수정의*/}); //static()으로 정의한 함수는 객체(클래스)로만 불러올 수 있는 함수이다
UserSchema.method('함수이름', function(){ /*함수정의*/}); //method()으로 정의한 함수는 인스턴스객체로만 불러올 수 있는 함수이다.

//인스턴스객체는 var user = new UserModel({ /*즉석 스키마 */});로 만들어진 user를 말한다.

var UserModel = mongoose.model("name", UserSchema); 
//UserModel은 클래스라 보면 된다. 이 클래스의 함수와 변수 등등은 UserSchema에 정의해 놓는다.
//mongoose.model()로 만들어진 객체는 기본적으로 find, save, update, remove 함수를 쓸 수 있다

```

이쯤 되면 자바 스크립트가 이상한 건지 mongoDB가 이상한 건지..혼란스럽다-_-;;
MongoDB를 다루기 위해 mongoose가 나왔음. 
-> 이DB의 정형화되지 않은 데이터를 다루기 위해 schema를 적용한 객체(클래스)를 만듦...
정리해 놓으면 이해가 가는데.
(결론은 MongoDB가 이상한 놈이네. 그렇다고 자바스크립트도 안 이상한 건 아니지만;)

보통 sql에서는 이런 작업을 안 해도 된다. mysql만 봐도...
DB의 자유도는 높아보이나, 한편으로 상대적으로 복잡해보인다. 확실히 정제되지 않은 방대한 데이터를 다룰 때는 이런 DB를 사용하는게 상대적으로 편하긴 할 것 같다.


