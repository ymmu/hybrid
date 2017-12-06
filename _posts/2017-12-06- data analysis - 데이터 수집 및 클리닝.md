---
layout:            post
title:             "17/12/06 - data analysis  - 데이터 수집 및 클리닝"
menutitle:         "17/12/06 - data analysis -데이터 수집 및 클리닝 "
category:          data analysis
author:            myohyun
tags:               python
---

## python을 사용한 csv파일 다루기
오늘 한 작업은 csv 파일로부터 필요한 항을 다른 파일로부터 가져오고, 기존 column으로부터 필요한 column을 새로 만들어내는 작업을 했다. 또한 비어있는 항에 대해 정확한 정보를 채워넣거나, 정말로 데이터를 얻을 수 없는 부분은 `NONE`으로 채워넣는 작업을 해두었다.


```python
import csv

#print(fieldnames)
'''
한국공항공사_항공사_코드정보 (copy).csv 를 읽어서 '항공사명'에 (**)형식으로 붙어있는 항공사코드를 떼어내 
새로운 column '항공사코드'로 만들어낸다.
그런 후 기존column들 +'항공사코드'를 합쳐서 새로운 'flight_code.csv'파일을 만들어낸다.
'''
#두자리 항공기 코드 추출하기
flightCode= open('한국공항공사_항공사_코드정보 (copy).csv', 'rt', encoding='euc-kr')
fcReader = csv.DictReader(flightCode)

fc_list = []
for row in fcReader:
    slt = row['항공사명'].split('(')
    if(len(slt)==2):
        slt = {'항공사코드':slt[1].split(')')[0]}
    else:
        slt={'항공사코드':'none'}
    #print(slt)
    row.update(slt)
    #print(row)
    fc_list.append(row)


fc_fieldName=[]
for key in fc_list[0].keys():
    fc_fieldName.append(key)
    

## flight_code.csv 파일을 열거나, 없으면 만든다. 
## fieldnames = column명들 넘겨줌.  리스트형식으로 넘겨주는 것 가능
## DictWriter를 이용해 row들을 기록한다.
## 기록할 때 with안에 writer가 포함되어있어야 함
with open('flight_code.csv', 'w', newline='') as csvfile:
    fieldnames = fieldnames
    writer = csv.DictWriter(csvfile, fieldnames=fc_fieldName)
    writer.writeheader()
    writer.writerows(fc_list)

```
##fc_list결과
```python
fc_list

'''
out_put:

[{'국가': '호주', '항공사 코드': 'AAA', '항공사명': '안셋항공(AN)', '항공사코드': 'AN'},
 {'국가': '미국', '항공사 코드': 'AAL', '항공사명': '아메리칸항공(AA)', '항공사코드': 'AA'},
 {'국가': '한국', '항공사 코드': 'AAR', '항공사명': '아시아나항공(OZ)', '항공사코드': 'OZ'},
 {'국가': '아이슬랜드', '항공사 코드': 'ABD', '항공사명': '에어아틀란타(CC)', '항공사코드': 'CC'},
 {'국가': '한국', '항공사 코드': 'ABL', '항공사명': '에어부산(BX)', '항공사코드': 'BX'},
 ...
 
'''
```

## 두 csv파일 합치기
`세계공항코드_2016년도_기준 (copy).csv` 의 공항코드(MWX 등으로 표기)가 보기 힘들어 `한국공항공사_국제선항공기스케줄_20160327_20161030 (copy).csv` 로부터 공항명을 찾아 달아준다. 또한 정확한 의미를 전달하기 위해 column 명 중 [항공]->[출발항공],  [상대항공]->[도착항공]으로 바꾸어준다. 그 이외에 비어있는 란중에서 인터넷을 통해 값을 구할 수 있는 부분은 채워주고, 원래 없거나 구할 수 없는 란은 ` none`으로 채워준다.
```python
airportCode= open('세계공항코드_2016년도_기준 (copy).csv', 'rt', encoding='euc-kr')
flightScj= open('한국공항공사_국제선항공기스케줄_20160327_20161030 (copy).csv', 'rt', encoding='euc-kr')
flightInfo= open('flight_code.csv', 'rt', encoding='utf8')

acReader = csv.DictReader(airportCode)
fsReader = csv.DictReader(flightScj)
fiReader = csv.DictReader(flightInfo)

print(acReader.fieldnames)
print(fsReader.fieldnames)
print(fiReader.fieldnames)

#공항이 코드명으로 기록되어있기 때문에 공항명을 넣어주기 위해 행마다 공항코드명에 맞는 공항한글명을 검색하여 list에 기록한다.
# 항공기스케줄 행의 타입이 dict인데 update() 함수로 공항명 키와 값을 추가한다.

list=[]

for row2 in fsReader:
    airportCode= open('세계공항코드_2016년도_기준 (copy).csv', 'rt', encoding='euc-kr') 
    flightInfo= open('flight_code.csv', 'rt', encoding='utf8')
    ## 한번 open 해서 변수로 부른 후 이터레이딩하면 다시 맨 앞으로 안 오고 읽은 것들은 다 날리는듯
    acReader = csv.DictReader(airportCode)
    fiReader = csv.DictReader(flightInfo)
    for row1 in acReader:
        if(row2['공항']==row1['IATA코드']):
            #print(row1['한글명'])
            row2.update({'출발공항':row1['한글명']})
            #list.append(row2)
        elif(row2['상대공항']==row1['IATA코드']):
            row2.update({'도착공항':row1['한글명']})
    
    row2.update({'항공사이름':''})
    for row in fiReader:
        if(row2['항공사']==row['항공사코드']):
            row2.update({'항공사이름':row['항공사명']})
            break
            
    list.append(row2)       
        #print('row2 다음')
        
```

## flight_code.csv파일 합치기

```python
#print(list[1]) 
# 새로 만들 merge.csv파일을 위한 column명 기록한다.
fieldnames=[]
for key in list[0].keys():
    fieldnames.append(key)


## merge.csv 파일을 열거나, 없으면 만든다. 
## fieldnames = column명들 넘겨줌.  리스트형식으로 넘겨주는 것 가능
## DictWriter를 이용해 row들을 기록한다.
## 기록할 때 with안에 writer가 포함되어있어야 함
with open('merge.csv', 'w', newline='') as csvfile:
    fieldnames = fieldnames
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
    writer.writeheader()
    writer.writerows(list)

```