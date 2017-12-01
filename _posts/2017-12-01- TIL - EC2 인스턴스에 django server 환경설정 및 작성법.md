layout:            post
title:                "17/12/01- TIL - EC2 인스턴스에 django server 환경설정 및 작성법"
enutitle:          "17/12/01- TIL - EC2 인스턴스에 django server 환경설정 및 작성법"
category:          Machine learning
author:            myohyun
tags:              Machine learning


<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML'></script>


## DJango 사용하기

[참조사이트](https://tutorial.djangogirls.org/ko/django_orm/)


가상환경을 실행
```python
$ source myvenv/bin/activate
```

`myvenv`를 여러분이 선택한 `virtualenv` 이름으로 바꾸는 것을 잊지 마세요!

> **Note** 가끔씩 `source`가 사용할 수 없을 수도 있습니다. 이 경우에는 아래와 같이 입력하세요. :
```python
$ . myvenv/bin/activate
```


## MTV 개발방식

**모델: ** 테이블 정의(model.py)(데이터베이스 테이블!)

**템플릿:** 사용자가 보게 될 화면의 모습을 정의(templates 폴더 및 *.html 파일에)

**뷰:** 어플리케이션의 제어 및 처리 로직을 정의(views.py파일)

앱 개발에 필요한 것은 장고가 알아서 만들어줌.



###앱 만드는 순서(책 기준): 

- 프로젝트 뼈대만들기: 프로젝트 및 앱 개발에 필요한 디렉터리와 파일 생성
- 모델코딩: 테이블 관련 사항을 개발(models.py, admin.py) 
- URL conf 코딩: URL 및 뷰 매핑 관계를 정의 (urls.py파일)
- 뷰코딩: 앱 로직 개발(views.py 파일)
- 템플릿 코딩하기: 화면 UI 개발 (templates/디렉터리 하위의 *.html파일들) 

### settings.py 주요사항

프로젝트 설정파일임.

- 데이터베이스 설정: 디폴트로 sqlite3 디비엔진 사용
- 팀플릿 항목 설정: TEMPLATES 항목으로 지정
- 정적 파일 항목 설정: STATIC_URL등 관련 항목을 지정
- 어플리케이션 등록: 개발하는 앱, 즉 프로젝트에 포함되는 앱들은 모두 설정 파일에 등록
- 타임존 지정: 최초에는 세계표준시(UTC)로 설정되어 있음. 한국시간으로 변경해야

### models.py 주요사항

### URLconf주요사항

###views.py주요사항

### ADmin사이트

개발용 웹 서버  - runserver

장고가 테스트용 웹 서버제공 - but 처리 능력도 떨어지고 보안에도 취약함

**실제 상용화에는 Apache, Nginx등의 상용 웹 서버를 사용해야 함**

 

## 작업코딩순서

| 작업순서      | 관련 명령/파일        | 필요한 작업 내용          |       |
| --------- | --------------- | ------------------ | ----- |
| 뼈대 만들기    | startproject    | 프로젝트 생성            | - [x] |
|           | settings.py     | 프로젝트 설정 항목 변경      | - [x] |
|           | migrate         | User/Group 테이블생성   | - [x] |
|           | createsuperuser | 프로젝트 관리자인 슈퍼유저를 만듦 | - [x] |
|           | startapp        | 북마크 앱 생성           | - [ ] |
|           | settings.py     | 북마크 앱 등록           | - [ ] |
| 모델 코딩하기   | models.py       | 모델(테이블) 정의         | - [ ] |
|           | admin.py        | Admin 사이트에 모델 등록   | - [ ] |
|           | makemigrations  | 모델을 디비에 반영         | - [ ] |
|           | migrate         |                    | - [ ] |
| URLconf코딩 | urls.py         | URL정의              | - [ ] |
| 뷰 코딩      | views.py        | 뷰로직 작성             | - [ ] |
| 템플릿 코딩    | templates 폴더    | 템플릿 파일 작성          | - [ ] |
| 그 외       | -               |                    |       |



## 삽질

모든 건 책과 똑같이 작업하지 않으면서 일어난다..

1. ​ 일단 프로젝트에 새 앱을 작성했으면 프로젝트의 settings.py에 앱을 등록해줘야 한다.
```python
       INSTALLED_APPS = [
         'django.contrib.admin',
         'django.contrib.auth',
         'django.contrib.contenttypes',
         'django.contrib.sessions',
         'django.contrib.messages',
         'django.contrib.staticfiles',
         'mlproject.apps.MlprojectConfig', #내가 만든 앱 추가함,
         'jsonpractice.apps.JsonpracticeConfig', #내가 만든 앱 추가함,
       ]
```

2. 여기서 끝이 아니고,  프로젝트에 알려줘야 한다.

```python
   python manage.py makemigrations jsonpractice ##  내가 등록한 앱 등록해줘야
```

3. urls.py에 views.py의 모듈을 등록하는데도 빵빵 터짐. 프로젝트 urls.py에는 각 앱 고유의 urls.py를  include 해준다

```python
##프로젝트 urls.py  
from django.conf.urls import url, include ##include 빼먹지마라
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'', include('jsonpractice.urls',namespace ='jsonpractice')), 
  ##그냥 프로젝트 urls.py에 각 앱의 urls.py include 할 때 경로를 설정하지 마시오. 그냥 
  ## 각 앱의 urls.py에 경로를 다 넣어버리라구
]
```
​	3.1. jsonpractice 앱의 urls.py 설정

```python
from django.conf.urls import url
from .views import showAll, showOne #아 이거 진짜 개짜증이었음. from(파일) import (함수 or class)

urlpatterns = {
    url(r'^postman/$', showAll, name='showAll'), #함수 인자까지는 안 써도 되는 듯하다.잘 모르겠음
}
```

4. views.py  모듈이나 클래스 설정할 때

```python
from django.shortcuts import render
from django.http import JsonResponse
from django.core import serializers
from django.http import HttpResponse
import json
from django.shortcuts import render
from .ml_api.test import cal, calVec ## conda package 테스트
# Create your views here.

# request를 줘야 url로 접근하면 요청으로 간주하고 뿌려준다. 그니까 url로 요청이 와서 작동하는 함수는 
# 다 param 으로 request를 줘야 하는 듯 
def showAll(request):
    result = cal(1,1) ## conda package 테스트
    array = calVec() ## conda package 테스트. array json데이터 변환에 유의. tolist() 사용
    data={'baz': 'goo', 'foo': 'bar', 'result': result, 'array':array}
    #package = serializers.serialize("json",data)
    return HttpResponse(json.dumps(data), content_type='application/json')

```

4.1 views.py모듈 잘못해서 난 에러모음

```python
showAll(request) rerquest를 안 넣어줘서 그런 것 아닌가 싶은데....
```

관련 에러

```python
TypeError: __init__() missing 1 required positional argument: 'app_module'
```





```python
**설정: from .views import showAll, showOne
**url(r'^postman/$', showAll, name='showAll') 
```

위의 설정과 관련된 에러 추정1

```python
  File "/media/ymmu/Dr.Mu/python_study/jangopractice/mlproject/jsonpractice/urls.py", line 5, in <module>
    url(r'^$', showAll(), name='showAll'),
  File "/home/ymmu/anaconda/envs/jangopractice/lib/python3.6/site-packages/django/conf/urls/__init__.py", line 85, in url
    raise TypeError('view must be a callable or a list/tuple in the case of include().')
TypeError: view must be a callable or a list/tuple in the case of include().

```

위의 설정과 관련된 에러 추정2

```python
  File "/media/ymmu/Dr.Mu/python_study/jangopractice/mlproject/jsonpractice/urls.py", line 6, in <module>
    url(r'^test$', showOne(), name='showOne'),
  File "/media/ymmu/Dr.Mu/python_study/jangopractice/mlproject/jsonpractice/views.py", line 16, in showOne
    package = serializers.serialize("json",data)
  File "/home/ymmu/anaconda/envs/jangopractice/lib/python3.6/site-packages/django/core/serializers/__init__.py", line 129, in serialize
    s.serialize(queryset, **options)
  File "/home/ymmu/anaconda/envs/jangopractice/lib/python3.6/site-packages/django/core/serializers/base.py", line 84, in serialize
    concrete_model = obj._meta.concrete_model
AttributeError: 'str' object has no attribute '_meta'

```

## conda package 사용하기

```python
## 이거 jsonpractice.ml_api.test 파일임
import numpy as np

# 외부 커스텀 py 파일 사용 테스트 
def cal(a,b):
    return a+b

#conda package 모듈들 사용 테스트
def calVec():
    return np.random.randn(2, 3).tolist() 
##  Array로 그냥 보내면 json 변환이 안 된다. tolist()꼭 붙여야


```
<figure>
   <img src="{{ "/media/img/Screenshot from 2017-12-01 18-38-53.png" | absolute_url }}" />
   <figcaption>장고서버 접속 후 /postman/ 경로로 데이터 뿌려주기 결과</figcaption>
</figure>