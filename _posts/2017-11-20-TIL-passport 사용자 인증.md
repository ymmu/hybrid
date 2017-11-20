---
layout:            post
title:             "17/11/20 - TIL - passport 사용자 인증"
menutitle:         "17/11/20 - TIL - passport 사용자 인증"
category:          js/node
author:            myohyun
tags:              js-node
---

# passport

```c

//===== Passport 사용 =====//
var passport = require('passport');
var flash = require('connect-flash');

// 세션 설정. passpart에서 세션 저장하려면 express session 설정이 먼저 있어야 함.
app.use(expressSession({
    secret:'my key',
    resave:true,
    saveUninitialized:true 
}));


// Passport의 세션을 사용할 때는 그 전에 Express의 세션을 사용하는 코드가 있어야 함. 위치가 잘못 놨다가 계속 req.user 값이 안 넘어가서 인증 안 됬다고 뜸...
app.use(passport.initialize()); //패스포트 초기화
app.use(passport.session()); //로그인세션 유지
app.use(flash());


//===== Passport 사용 설정 =====//
// Passport의 세션을 사용할 때는 그 전에 Express의 세션을 사용하는 코드가 있어야 함
app.use(passport.initialize());
app.use(passport.session());
app.use(flash());
```

```c

var LocalStrategy = require('passport-local').Strategy;
//패스포트 로그인 설정
passport.use('local-login',new LocalStrategy({
    usernameField: 'id', //usernameField 랑 passwordField 이름 바꾸면 안 되는구나;;;;
    passwordField: 'pwd',
    passReqToCallback: true // 이 옵션을 설정하면 아래 콜백 함수의 첫번째 파라미터로 req 객체 전달됨
}, function (req, id, pwd, done) {

    console.log('passport의 local-login 호출됨:');

    var mysql = app.get('mysql');
    //유저이름 있는지 확인
    var query = mysql.client.query('select * from user where id = ?', id, function (e, user) {

        console.log(user[0].pwd);
        if (e) {return done(e)};
        if(!user){
            console.log('일치하는 계정이 없음');
            return done(null, false, req.flash('loginMessage', '등록된 계정이 없습니다'));
        }

        if(user[0].pwd != pwd){
            console.log('비번이 안 맞슈');
            return done(null, false, req.flash('loginMessage', '비밀번호가 일치하지 않습니다'));
        }

        //pwd까지 정상이면
        console.log('계정과 비번 모두 일치');
        return done(null, user);

    });

}));

```


```c
//사용자 인증에 성공했을 때 호출
passport.serializeUser(function (user, done) {
    console.log('serializeUser() 호출됨');
    console.dir(user);

    done(null, user);
});

//사용자 인증 이후 사용자 요청이 있을 때마다 호출
passport.deserializeUser(function (user, done) {
    console.log('deserializeUser() 호출됨');
    console.dir(user);
    done(null, user);
});
```

