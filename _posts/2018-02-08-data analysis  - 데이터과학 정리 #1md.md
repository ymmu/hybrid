---
layout:            post
title:             "18/02/08- data analysis  - 데이터과학 정리 #1"
entitle:          "18/02/08- data analysis  - 데이터과학 정리 #1"
category:          project
author:            myohyun
tags:             data analysis
---

그동안 데이터정제 밑 분석으로 파이썬을 써왔는데, 빼먹거나 보충할 문법들을 정리

1. 모듈의 기능들을 통째로 불러와서 기존의 변수들을 덮어쓰지 말것


```python

match = 10
from re import * # re에도 match라는 함수가 존재
print match # "<function re.match>"라고 떠버림
```

2.  list에 추가할 때 append는 원소 추가 / extend 는 두 리스트를 한 리스트로 붙일 때 or  '+'로도 붙일 수 있음
3.  tuple 은 변경할 수 없는 list. 수정 빼로 리스트의 모든 기능 적용 가능.
4.  tuple과 list는 다중 할당을 지원한다. 

```python
x,y=1,2
x,y=y,x  #pythonic한 변수 교환!
```

5. 원소 in (리스트 ,  딕셔너리) -> in은 리스트나 딕셔너리에 원소가 존재하는지 체크해서 있으면 True, 없으면 False
6. dict는 key가 존재하지 않는데 부르면 에러가 난다. 따라서 예외처리를 해주던가, dafualtdict를 사용하면 키가 없을 때 생성해서 값을 집어넣어줌

```python
from collections import defaultdict
```

7. Counter : 카운터는 연속된 값을 defaultdict(int) 와 유사한 객체로 변환해 주며, key와 value의 빈도를 연결시켜 준다. 앞으로 히스토그램을 그릴 때 자주 사용할 것이다.

```python
from collections import Counter
c= Counter([0,1,2,0]) # c= {0:2. 1:1, 2:1} (각 원소가 몇 번 나왔는지 딕셔너리 형태로)
```
8. set: 데이터 구조 중 유일한 항목의 집합을 나타내는 구조이다. 

   두 가지 장점:

   - in은 set에서 굉장히 빠르게 작동한다. 수많은 항복 중에서 특정 항목의 존재 여부를 확인해 보기 위해서는 list를 사용하는 것보다 set을 사용하는 것이 훨씬 효율적이다
   - 중복된 원소를 제거해준다

9. and는 첫 번째 값이 참이면 두번째 값을 반환해주고, 첫 번째 값이 거짓이면 첫 번째 값을 반환해준다

```python
s= some_string
first = s and s[0]

같은 문법:
if s:
    first = s[0]
else:
    first = ""

```

10. list의 모든 항목이 참이라면 True를 반환해주는 all 함수와 적어도 하나의 항목이 참이라면 True를 반환해주는 any함수가 있다
11. 정렬: 파이썬의 모든 list에는 자동으로 정렬해 주는 sort 메서드가 있다. 만약 이미 만든 list를 망치고 싶지 않으면 sorted 함수를 사용해서 새롭게 정렬된 list를 생성할 수 있다

```python
x=[4,1,2,3]
y=sorted(x) #x를 정렬할 값을 y에 따로 저장할 수 있다
x.sort() #x 안에서 그냥 정렬

#key에다 정렬기준을 줄 수 있다. 지정한 함수의 결과값을 기준으로 리스트 정렬
#reverser= True로 해놓으면 반대로 정렬가능

x = sorted(x, key= lambda x:x, reverse=True) #람다식 써도 되고
x = sorted(x, key= abs, reverse=False) # 있는 함수 줘도 된다

```
12. list comprehension : pythonic하게 
```python
even_num = [x for x in range(5) if x%2 == 0 ] # [0,2,4]

#보통 리스트에서 불필요한 값은 밑줄로 표기한다
zeroes = [0 for _ in even_num] # [0,0,0]

#오 요건 보는데 좀 이상했어 읽는 법 캐치완료
pairs = [ (friend, user_id)
    		for friend in user['friends'] #다중 for문은 위부터 읽어서 결과값은 맨 첫줄에 쓴다 생각
         	for user_id in friend['id']    
]
```

13. 생성자(generator)는 주로 (for문을 통해서) 반복할 수 있으며 제네레이터의 각 항목은 필요한 순간에 그떄그떄 생성된다. 제네레이터를 만드는 한 가지 방법은 함수와 yield를 활용하는 것(여전히 모호함)
14. 난수생성: 

```python
import random
random.random()
#동일한 난수 계속 생성하고 싶으면 seed함수 사용

random.seed(10) #seed를 10으로 설정
random.random() #0.23456
random.seed(10) #seed를 다시 10으로 설정해도 
random.random() #0.23456  값은 같다

random_list = range(10)
random.shuffle( random_list) #리스트를 섞어줌

friends = random.choice(['Alice', 'Bob', 'Charlie']) #아무거나 선택해줌

lottery_nums  = range(60)
sample = random.sample(lottery_nums, 6) #6개 무작위 중복비허용 샘플 뽑아줌. 우왕!

#중복허용 샘플 뽑기
four_with_replacement = [random.choice(range(10)) for _ in rnage(4)]
#[9,4,4,2]


```

15.  클래스만들기

```python

class Set:
    
    def __init__(self, values = None) #이것이 바로 생성자, self를 첫번째 인자로 (관습)
    	s1 = Set() #비어있는 
        s2 = Set([1,2,2,3]) #초기값 있는
        
        ...
        
	def __ref__(self):
        #자바의 toString()과 같은 함수
        ...
        
```

16. 함수형 도구: 함수를 이용해 다른 함수를 만들 떄. map, filter, reduce도 알아둘 것

```python
#가끔식 여러 함수를 호출할 때 한 함수의 특정 부분을 사용해서 새로운 함수를 만들고 싶을 때가 있는데, 이를 partial function application 또는 currying 이라고 한다.

#변수 두 개에 대한 지수함수가 주어졌는데
def exp(base, power):
    return base**power

#base=2로 두는 함수를 만들고 싶다.

from functools import partial
two_to_the = partial(exp, 2) # 아마 exp함수의 첫번째 인자를 자동으로 선택하나보다
two_t0_the(3) # 8이 나옴

#두 번쨰 인자를 고정하고 싶을 때는 exp 함수의 변수 이름을 명시해준다
square_of = partial(exp, power=2)   #power는 exp 함수의 두번쨰 인자였음
print square_of(3) #9

#함수의 중간에 있는 인자를 partial에 쓰면 복잡해질 수 있으니 지양한다



```

17. enumerate : list를 반복하면서 list의 항목과 인덱스가 모두 필요한 경우가 종종 있다

```python
#내가 자주 쓰던 파이썬스럽지 않은 방법
i=0
for document in documents:
    do_something(i, document)
    i+=1
    
#pythonic 방법
for i, document in enumerate(documents):
    do_something(i, document)
    
#index만 필요할 떄 내가 쓰던 pythonic하지 않은 방법
for i in range(len(documents)): 
    do_something(i)

#index만 필요할 떄 pythonic 방법
for i,_ in enumerate(documents): do_something(i)

```

18. zip과 argument unpacking

- 가끔씩 두 개 이상의 list를 서로 묶어 주고 싶은 때가 있다. zip은 여러 개의 리스트를 서로 상응하는 항모그이 tuple로 구성된 list로 변환해 준다

```python
list1 = ['a', 'b', 'c']
list2 = [1,2,3]
zip(list1, list2)
#결과 : [('a', 1), ('b', 2), ('c', 3)]

#주어진 리스트이 길이가 서로 다른 경우 zip은 첫 번째 리스트가 끝나면 멈춘다
#묶인 리스트는 다음과 같은 트릭을 사용해서 다시 풀어줄 수 있다
pairs = [('a', 1), ('b', 2), ('c', 3)]
letters, nums = zip(*pairs)

add(*[1,2]) #-> add(1,2)랑 같아짐. 와우! 유용한 기법!

```

19. args 와 kwargs

특정 함수 f를 입력하면 f의 결과를 두 배로 만드는 함수를 반환해 주는 고차 함수를 만들고 싶다고 해보자

```python
def doublef(f):
    def g(x): #이런짓도 가능하군. 여기서부터 일단 이상하다
        return 2*f(x)
    return g

def f1(x):
    return x+1

g=doublef(f1)
print(g(3))  #(3+1)*2=8
print(g(-1)) #(-1+1)*2 == 0

#두 개 이상의 인자를 받는 함수의 경우에는 문제가 발생한다
def f2(x,y):
    return x+y
g=doublef(f2)
print(g(1,2)) # 타입에러가 남. 인자가 하나여야 하는데 2개라고.

#문제를 해결하기 위해 임의의 수의 인자를 받는 함수를 만들어 줘야 한다, 앞서 설명한 argument unpacking 을 사용하면 마법같이 임의의 수의 인자를 받는 함수를 만들 수 있다

def magic(*args, **kwargs): #하나는 그냥 어규먼트이고, 하나는 키워드 어규먼트
	print('unnamed args:', args)  #이름 업는 인자들로 구성된 튜플
    print('keyword args:', args)  #이름이 주어진 인자로 구성된 dict

magic(1,2,key="word", key2="word2")
#출력값
# unnamed args : (1,2)
# keyword args : {key:'word', key2:'word2'}

#정해진 수의 인자가 있는 함수를 호출할 때도 list나 dict로 인자를 전달할 수 있다
def other_way_magic(x,y,z):
    return x+y+z

x_y_list = [1,2]
z_dict = {'a':3}
print(other_way_magic(*x_y_list, **z_dict))

#args와 kwargs 를 사용하면 온갖 희한한 것을 할 수 있다.


#아까 에러나던 함수 수정
def doublef_correct(f):
    #f의 인자에 상관없이 작동
    def g(*args, **kwargs): #이런짓도 가능하군. 여기서부터 일단 이상하다
        #g의 인자가 무엇이든 간에 f로 보내줌
        return 2*f(*args, **kwargs)
    return g

def f2(x,y):
    return x+y

g=doublef_correct(f2)
print(g(1,2)) # 6
```

