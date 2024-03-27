---
layout: single
title: "Python - 공공데이터 포털 Open API 사용하기"
categories: Python
tag: [Python, API]
toc: true
toc_sticky: true
toc_label: "목차"
---

## openAPI 신청

```plaintext
공공데이터 포털

- https://www.data.go.kr
- 회원가입 : https://www.data.go.kr/uim/mss/socialMberInfoStep1FormView.do
- 검색어 : 대기오염
    - openAPI -> 한국환경공단_에어코리아_대기오염정보 -> 상세보기
    - 참고문서 download
    - 활용신청
         - 활용목적 : 웹사이트 개발 -> 테스트 웹사이트 개발
         - 동의 -> 활용신청
    - 마이페이지
        - 한국환경공단_에어코리아_대기오염정보
        - End Point : http://apis.data.go.kr/B552584/ArpltnInforInqireSvc
        - 일반 인증키(Encoding) : %2Fr5O.....
        - 일반 인증키(Decoding) : 
    - 에어코리아_대기오염정보 조회 서비스_기술문서_v1.0.docx
        - 분석
```

## 시도별 실시간 측정정보 조회

### 웹브라우저 test

요청주소

<http://apis.data.go.kr/B552584/ArpltnInforInqireSvc/getCtprvnRltmMesureDnsty>
결과: `SERVICE_KEY_IS_NOT_REGISTERED_ERROR`

요청변수(Request Parameter)를 포함하여 요청하기

<http://apis.data.go.kr/B552584/ArpltnInforInqireSvc/getCtprvnRltmMesureDnsty?sidoName=대구&pageNo=1&numOfRows=10&returnType=json&serviceKey=서비스키&ver=1.0>

- numOfRows(데이터 건수) : 10
- returnType(반환값 타입) : json
- sidoName(시도명) : 대구
- 서비스키(인증키) : 자신의 서비스키(일반 인증키 Decoding)

## 'requests' 모듈

`requests`는 HTTP 요청을 보내고 응답을 받기 위한 인기있는 라이브러리입니다.

설치: `pip install requests`

```python
import requests

url = 'http://apis.data.go.kr/B552584/ArpltnInforInqireSvc/getCtprvnRltmMesureDnsty'

param_data = {'sidoName':'서울',
              'pageNo':'1',
              'numOfRows' : '10',
              'returnType' : 'json',
              'serviceKey' : '자신의 서비스키 입력',
              'ver' : '1.0'}

# get 방식
# params를 url에 붙여서 요청
res = requests.get(url, params=param_data) 

print(type(res))
print(res)
print(type(res.text))
print(res.text)
```

결과 값의 `res.text`가 문자열 형식이며 문자열을 JSON 포맷을 취하고 있습니다.

## 결과 값을 JSON 형식으로 보기

### Web JSON Viewer

웹에서 제공해 주는 JSON Viewer를 이용하여 결과 값을 보기

<http://jsonviewer.stack.hu/>

`res.text`의 값을 복사하여 붙여넣기

### 'json' 모듈

`json` 모듈을 이용하여 JSON 형식으로 변환

```python
import requests
import json

url = 'http://apis.data.go.kr/B552584/ArpltnInforInqireSvc/getCtprvnRltmMesureDnsty'

param_data = {'sidoName':'서울',
              'pageNo':'1',
              'numOfRows' : '10',
              'returnType' : 'json',
              'serviceKey' : '자신의 서비스키 입력',
              'ver' : '1.0'}

res = requests.get(url, params=param_data)
data = res.json()
print(type(data))
print(data)
```

JSON으로 변환된 결과 값은 딕셔너리 형식으로 저장되어 있습니다. 내부의 key와 value를 확인하여 원하는 값을 추출할 수 있습니다. `data`까지의 소스 코드는 생략합니다.

```python
print(data.keys()) # response

res_data = data['response'] # ['body', 'header']

res_body = res_data['body']
res_header = res_data['header']
print(res_body.keys()) # ['totalCount', 'items', 'pageNo', 'numOfRows']
print(res_header.keys()) # ['resultMsg', 'resultCode']

### item을 제외한 나머지 값 가져오기 -> item은 list 형태
print('totalCount : {}'.format(res_body['totalCount']))
# print('items : {}'.format(res_body['items']))
print('pageNo : {}'.format(res_body['pageNo']))
print('numOfRows : {}'.format(res_body['numOfRows']))

print('resultMsg : {}'.format(res_header['resultMsg']))
print('resultCode : {}'.format(res_header['resultCode']))

### item 값 가져오기
res_items = res_body['items']
print(type(res_items))
print(len(res_items))

# loop
for item in res_items:
    # print(item)
    # print(type(item))
    # print(item.keys())
    for key in item.keys():
        print('{} : {}'.format(key, item[key]))
    print('----------')
```

## 원하는 형태로 data 가공

`res_items`까지의 소스 코드는 생략합니다.

### [{a:1}, {b:2}, {c:3},...]

```python
list_dic = []
for item in res_items:
    list_dic.append(item)

print(list_dic)    
print(len(list_dic))
print(list_dic[0])
print(list_dic[0]['sidoName'])
```

### [[1, 2, 3],[4, 5, 6],...]

```python
list_all = []

for item in res_items:
    list_row = []
    for key in item.keys():
        list_row.append(item[key])
    list_all.append(list_row)

print(list_all)
print(len(list_all))
print(list_all[0])
print(len(list_all[0]))
```

필요 시 MongoDB에 PostgreSQL등 에 저장할 수 있습니다.

---

해시태그: #OpenAPI #공공데이터포털 #requests #json #MongoDB #PostgreSQL
