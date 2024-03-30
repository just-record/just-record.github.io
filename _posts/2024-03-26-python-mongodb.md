---
layout: single
title: "MongoDB - Python에서 MongoDB 사용하기"
categories: MongoDB
tag: [MongoDB, Python]
toc: true
toc_sticky: true
toc_label: "목차"
---

## MongoDB 란?

MongoDB는 JSON 문서 기반 NoSQL 데이터베이스입니다.

### NoSQL 이란?

NoSQL은 관계형 데이터베이스 대신 키-값, 문서, 열 지향, 그래프 등의 데이터 모델을 사용하는 비관계형 데이터베이스입니다.

## MongoDB 설치

### 설치 파일 다운로드    

[https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)

MongoDB Community Server Download -> select package -> (Version, Platform, Package) 선택 -> Download

2024년 3월 30일 기준: Version - 7.07, Platform - Windows, Package - msi, mongodb-windows-x86_64-7.0.7-signed.msi

### 설치(서버 및 클라이언트)

다운로드 받은 파일을 실행하여 MongoDB를 설치합니다.

Next -> I accept the terms in the License Agreement -> Next -> Complete -> Run service as Network Service user ->  Next -> Next -> Install -> Finish

### Compass

MongoDB Compass(서버 접속 프로그램)이 자동으로 실행 됩니다. Compass를 사용하여 MongoDB에 접속할 수 있습니다.

'Start' 버튼 클릭

New Connection -> 'Connect' 버튼 클릭

- URI: mongodb://localhost:27017

<!-- ## MongoDB 조회, 입력, 수정, 삭제

- MongoDB 조회: 참조자료 - <https://minimilab.tistory.com/43>
- MongoDB 입력: 참조자료 - <https://cionman.tistory.com/46>
- MongoDB 수정: 참조자료 - <https://velopert.com/545>
- MongoDB 삭제: 참조자료 - <https://www.zerocho.com/category/MongoDB/post/579ecb1fc097d015000404dd> -->

## 'pymongo' 모듈

Python에서 MongoDB를 사용하기 위해 'pymongo' 모듈을 사용합니다.

참조자료: <https://pymongo.readthedocs.io/en/stable/tutorial.html>

pymongo 설치

```bash
pip install pymongo
```

## MongoDB 연결

### MongoClient를 사용하여 MongoDB에 연결

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
# client = MongoClient('mongodb://localhost:27017/')
```

### 데이터베이스 및 컬렉션 생성

데이터베이스: 관련된 데이터를 그룹화하는 컨테이너 역할

컬렉션: 유사한 JSON 문서들의 집합. 관계형 데이터베이스의 테이블과 유사

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
# client = MongoClient('mongodb://localhost:27017/')

db = client.test_database
# db = client['test-database']

collection = db.test_collection
# collection = db['test-collection']
```

## 데이터 입력

### insert_one - 한건의 row를 입력

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

data_dic = {'name': '홍길동', 'age': 17}
re_data_dic = collection.insert_one(data_dic)

print(re_data_dic)
print(re_data_dic.inserted_id)

data_dic2 = {'name': '김길동', 'age': 28}
re_data_dic2 = collection.insert_one(data_dic2)
print(re_data_dic2)
```

### insert_many - 여러건의 row를 입력

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

data_list = [
    {'name': '이길동', 'age': 25},
    {'name': '박길동', 'age': 31},
    {'name': '정길동', 'age': 35}    
]

re_data_dic2 = collection.insert_many(data_list)
print(re_data_dic2)
print(re_data_dic2.inserted_ids)
```

## 데이터 조회

### find_one - 한건의 row를 조회

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

search_result = collection.find_one()
print(search_result)
print(type(search_result))
```

### find - 여러건의 row를 조회

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

search_results = collection.find()
print(search_results)

for search_result in search_results:
    print(search_result)
```

### 조건 검색

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

# where name = '홍길동'
search_results = collection.find({'name':'홍길동'})
for search_result in search_results:
    print(search_result)

# where name = '홍길동' and age = 17
search_results = collection.find({'name':'홍길동', 'age':17})
for search_result in search_results:
    print(search_result)

# where age > 17
search_results = collection.find({'age':{'$gt':17}})
for search_result in search_results:
    print(search_result)    

# where age >= 17
search_results = collection.find({'age':{'$gte':17}})
for search_result in search_results:
    print(search_result)    

# where age < 28
search_results = collection.find({'age':{'$lt':28}})
for search_result in search_results:
    print(search_result)    

# where age <= 28
search_results = collection.find({'age':{'$lte':28}})
for search_result in search_results:
    print(search_result)    

# where name != '홍길동'
search_results = collection.find({'name':{'$ne':'홍길동'}})
for search_result in search_results:
    print(search_result)    

# select name from ... where age > 17
search_results = collection.find({'age':{'$gt':17}}, {'name':1})
for search_result in search_results:
    print(search_result)    

# select name from ... where age > 17
search_results = collection.find({'age':{'$gt':17}}, {'_id':0, 'name':1, 'age':1})
for search_result in search_results:
    print(search_result)
```

### pymongo 정규식

```plaintext
like '홍%' => find({'name':{'$regex':'^홍'}})
like '%길동' => find({'name':{'$regex':'길동$'}})
like '%길%' => find({'name':{'$regex':'길'}})


db.users.find({name: /a/})  //like '%a%'
out: paulo, patric
 
db.users.find({name: /^pa/}) //like 'pa%' 
out: paulo, patric
 
db.users.find({name: /ro$/}) //like '%ro'
out: pedro
```

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

# like
search_results = collection.find({'name':{'$regex':'김'}})
for search_result in search_results:
    print(search_result)
```

## 데이터 수정

### update_one - 한건의 row를 수정

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

collection.update_one ({'name':'홍길동'}, {'$set':{'age':18}})

search_results = collection.find({'name':'홍길동'})
for search_result in search_results:
    print(search_result)
```

### update_many - 여러건의 row를 수정

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

collection.update_many ({'age':{'$gt':30}}, {'$set':{'age':31}})  

search_results = collection.find()
for search_result in search_results:
    print(search_result)
```

## 데이터 삭제

### delete_one - 한건의 row를 삭제

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

collection.delete_one ({'name':'홍길동'})

search_results = collection.find()
for search_result in search_results:
    print(search_result)
```

### delete_many - 여러건의 row를 삭제

```python
from pymongo import MongoClient

client = MongoClient('localhost', 27017)
db = client.test_database
collection = db.test_collection

collection.delete_many ({'age':{'$gte':30}})

search_results = collection.find()
for search_result in search_results:
    print(search_result)
```

---

해시태그: #MongoDB #Python #pymongo #NoSQL #insert_one #insert_many #find_one #find #update_one #update_many #delete_one #delete_many
