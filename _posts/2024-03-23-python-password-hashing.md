---
layout: single
title: "Python - 비밀번호 해싱"
categories: Python
tag: [Python, Password-Hashing]
toc: true
toc_sticky: true
toc_label: "목차"
---

## 비밀번호 해싱 (Password Hashing)

비밀번호를 데이터베이스(또는 다른 저장매체)에 저장할 때 평문으로 저장하면 보안에 취약하므로 해싱(또는 암호화)하는 것이 일반적입니다.
비밀번호 해싱은 일반 텍스트 비밀번호를 가져와 해시 함수를 적용하고 결과 해시 값을 저장합니다.

### 해시 함수(Hash Function)

해시 함수는 임의의 길이의 입력 데이터를 고정된 길이의 유일한 값으로 변환하는 단방향 함수입니다. 참고로 변환된 고정 길이의 유일한 값은 해시 값입니다.

| 속성 | 설명 |
| :--- | :--- |
| 결정적 | 동일한 입력은 항상 동일한 결과를 낳습니다. |
| 고정 크기 | 입력 크기에 관계없이 출력 길이가 고정됩니다. |
| 빠른 계산 | 이 함수는 해시 값을 빠르게 생성해야 합니다. |
| 사전 이미지 저항 | 해시 함수를 역전시키는 것은 계산상 불가능해야 합니다. |
| 충돌 저항 | 동일한 출력을 생성하는 두 개의 서로 다른 입력을 찾는 것은 어렵습니다. |

### 솔팅 (Salting)

솔팅은 비밀번호 해싱 시 추가적인 랜덤 데이터를 더하는 것을 말합니다.

목적

- 동일한 비밀번호에 대해 매번 다른 해시 값이 생성되게 함으로써 무차별 대입 공격을 어렵게 만듭니다.
- 해시 테이블 공격을 방지합니다. 해시 테이블은 미리 계산된 해시 값을 모아둔 것인데, 솔팅이 없으면 이 테이블을 이용해 쉽게 비밀번호를 찾을 수 있습니다.

방식

1. 각 사용자 계정마다 랜덤으로 생성된 고유한 솔팅 값을 비밀번호에 붙입니다.
2. 비밀번호와 솔팅 값을 합쳐서 해시 함수에 넣어 해시 값을 만듭니다.
3. 데이터베이스에는 해시 값과 소금 값을 함께 저장합니다.

### 해싱 프로세스

회원 가입

- 사용자 A의 비밀번호 "mypassword"를 입력(생성) 합니다.
- 랜덤 솔팅 값을 "ab12cd"로 생성합니다.
- "mypassword" + "ab12cd"를 해싱하여 해시값 "xyz789..."를 얻습니다.
- 데이터베이스에 "xyz789..., ab12cd"를 저장합니다.

로그인

- 사용자가 입력한 비밀번호에 저장된 소금 값을 더한 후 해싱합니다.
- 생성된 해시 값과 DB에 저장된 해시 값을 비교하여 인증합니다.

### 암호화와 해싱의 차이

암호화는 암호문을 원래의 평문으로 복호화할 수 있는 가역적인 프로세스로 키를 이용하여 암호화하고, 동일한 키로 암호문을 평문으로 복호화할 수 있습니다.
해싱은 단방향 함수로 평문을 해시값으로 변환하는 것으로, 해시값을 다시 평문으로 되돌릴 수 없는 비가역적인 프로세스입니다.

## Passlib

Passlib는 파이썬에서 안전한 비밀번호 해싱을 쉽게 구현할 수 있게 해주는 라이브러리입니다.

- 다양한 해시 알고리즘 지원: Passlib는 bcrypt, scrypt, argon2 등 다양한 해시 알고리즘을 지원합니다.
- 자동 솔팅(Salting): 대부분의 해시 알고리즘에서 자동으로 랜덤 솔트를 사용하여 해싱을 수행하므로 무차별 대입 공격에 강합니다.
- 다중 해시 지원: 단일 문자열에 여러 다양한 해시 알고리즘을 적용하여 저장할 수 있습니다.
- 간편한 사용법: CryptContext를 이용하면 해시 알고리즘과 설정을 한 번에 관리할 수 있어 매우 편리합니다.

### 설치

```bash
pip install passlib
```

<!-- ```bash
pip install passlib[bcrypt]
```

만약 'bcrypt'가 아닌 다른 알고리즘을 사용하려면 해당 알고리즘을 설치해야 합니다.

```bash
pip install passlib argon2-cffi
``` -->

### 예제

```python
from passlib.context import CryptContext

# CryptContext: 해시 알고리즘과 관련 옵션을 관리하는 주요 클래스
# schemes: 사용할 해시 알고리즘을 지정
# schemes=['bcrypt', 'argon2'] # 여러개 지정 가능
# deprecated: 이전 해시 스키마(알고리즘)를 자동으로 감지하고 처리하는 옵션
# auto: 이전 스키마를 자동으로 인식하고 새 스키마로 업그레이드할지 여부를 결정
bcrypt_context = CryptContext(schemes=['bcrypt'], deprecated="auto")


def get_password_hash(password):
    return bcrypt_context.hash(password)


def verify_password(plain_password, hashed_password):
    return bcrypt_context.verify(plain_password, hashed_password)


# Hash a new password
hashed_password = get_password_hash("my_secret_password")
print(hashed_password)

# Verify the password
if verify_password("my_secret_password", hashed_password):
    print("The password is correct!")
else:
    print("The password is incorrect.")

# Verify the password
if verify_password("your_secret_password", hashed_password):
    print("Your_secret_password is correct!")
else:
    print("Your_secret_password is incorrect.")    
```

```python
# 출력
$2b$12... # 해시 값
The password is correct!
Your_secret_password is incorrect.
```

bcrypt의 해시 함수 내부적으로 OpenBSD에서 개발한 bcrypt 해시 함수를 사용합니다. 이 함수는 Blowfish 암호화 알고리즘을 기반으로 하며, 키 스트레칭과 솔팅을 내장하고 있어 무작위 대입 공격에 강합니다.

솔팅은 자동으로 생성됩니다. 이 솔팅 값은 해시와 함께 단일 문자열로 저장되어 별도 관리가 필요없으며 비밀번호를 확인할 때 저장된 해시 문자열에서 솔팅 값을 추출하 사용됩니다.

---

해시태그: #Python #Hashing #Password Hashing #Passlib #CryptContext
