---
layout:            post
title:             "17/12/03 - TIL - javascript 인스턴스객체 모듈화 할 떄 주의점"
menutitle:         "17/12/03 - TIL - javascript 인스턴스객체 모듈화 할 떄 주의점"
category:          js/node
author:            myohyun
tags:              js-node
---

## 어마어마한 삽질

정말..기본이 안 되어있어서 시간 낭비한 최고의 케이스인듯.;;;

모듈화할 때 인스턴스객체 함수를 정의 하면서 그 안에다 바로  database 객체의 변수 client를 정의해주었다. 

그리고 대 참사가..;;;

```javascript

//database 객체에 init, connect 함수 정의해줌
var database = {};

database.init = function (app, config) {
    console.log("mysql init  실행");
    connect(app, config);
}

function connect(app, config) {
  
  //database.client <- 함수 안에서 database.client를 즉석에서 정의해주었다.
  //자바를 먼저 배운 나로서는 짜증나는 -_-;;
    database.client = mysql.createConnection({

        host: 'localhost',
        user: config.mysql.user,
        password: config.mysql.password,
        database: config.mysql.database

    });

...

}
```



그리고 다른 함수에서 client.query 를 날리는데 계속 client가 undefiend라고 에러를 던지는 것이다...아 정말 로그도 찍어보고  혹시 운영체제 이전하면서 프로젝트 파일에 망가진 모듈이 있나 모듈도 다시 깔아보고, mysql 모듈만 따로 뗴어내서 실험도 해보다 결국 찾아냈다.

```javascript
database.insert= function (marineWeather, input){

    //데이터베이스에 넣어준다
    var sql = 'insert into weather set ? ';

	//---생략 ---//
  
  //database를 뺴고 client만 적어줬더니 client를 계속 지역변수로 읽고는 undefined라 뱉어냈던..-_-;;;
  //아니 근데 더 불가사의한건 예전에는 돌아갔다고...도대체 뭐냐고...
  database.client.query(sql, value, function (e, result) {
    if(e) throw e;
    console.log(result);

  });
```
