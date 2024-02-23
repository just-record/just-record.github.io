---
layout: single
title: "Python - 객체지향 프로그래밍(캡슐화, 상속, 다형성, 추상화)"
categories: Python
tag: [Python, oop]
toc: true
toc_sticky: true
toc_label: "목차"
---
## 캡슐화(Encapsulation)

객체의 일부 구성 요소를 외부에서 **직접 접근할 수 없도록 숨기는 것**을 의미합니다. 데이터와 그 데이터를 처리하는 메서드를 하나의 단위로 묶어 외부에서 직접 접근하지 못하도록 제한합니다.

Python은 직접적인 접근 제한자(public, protected, private)를 제공하지 않지만, 속성과 메서드 이름 앞에 **언더스코어(_)** 또는 **더블 언더스코어(__)**를 붙여서 이를 간접적으로 구현합니다.

- public: 언더스코어(_) 없이 정의된 속성과 메서드
- protected: 언더스코어(_) 하나로 시작하는 속성과 메서드
- private: 언더스코어(_) 두개로 시작하는 속성과 메서드

```python
class Account:
    def __init__(self, balance):
        self.__balance = balance  # private 변수

    def get_balance(self):
        return self.__balance

    def withdraw(self, amount):
        if amount > self.__balance:
            raise ValueError("잔액이 부족합니다.")
        self.__balance -= amount

    def deposit(self, amount):
        self.__balance += amount

account = Account(100000)
print(account.get_balance())  # 잔액 확인 # 100000

account.withdraw(50000) # 출금
print(account.get_balance())  # 잔액 확인 # 50000

# 예외 발생
try:
    account.withdraw(100000)
except ValueError as e:
    print(e)  # 잔액이 부족합니다.

# 직접 접근 불가
# print(account.__balance)  # AttributeError: 'Account' object has no attribute '__balance'
```

## 상속(Inheritance)

한 클래스가 다른 클래스의 **속성과 메서드를 물려받는** 메커니즘입니다. 상속은 기반 클래스(base class) 또는 슈퍼 클래스(super class)의 기능을 파생 클래스(derived class) 또는 서브 클래스(sub class)가 물려받는 형태로 구현됩니다. 이를 통해 코드 중복을 줄이고 유지 관리를 용이하게 하며, 코드의 재사용성을 높일 수 있습니다.

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        raise NotImplementedError

class Dog(Animal):
    def speak(self):
        return "멍멍!"

class Cat(Animal):
    def speak(self):
        return "야옹!"

# 객체 생성
dog = Dog("코코")
cat = Cat("나비")

# 출력
print(dog.name)  # 코코
print(dog.speak())  # 멍멍!
print(cat.name)  # 나비
print(cat.speak())  # 야옹!
```

## 다형성(Polymorphism)

**같은 이름의 메서드 또는 연산자를 다른 클래스에서 다르게 구현하는 것**을 의미합니다. 즉, 서로 다른 클래스에서 같은 이름의 메서드를 호출하더라도, 그 결과가 서로 다르게 반환될 수 있도록 하는 것입니다.

```python
class Shape:
    def area(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius * self.radius

def compute_area(shape):
    print(f"The area is {shape.area()}")

compute_area(Rectangle(10, 20)) # 다형성을 활용한 면적 계산
compute_area(Circle(5)) # 다형성을 활용한 면적 계산
```

## 추상화(Abstraction)

**복잡한 내부 구현을 숨기고 사용자에게 필요한 기능만을 제공**하는 방법입니다. 추상 클래스(abstract class)와 추상 메서드(abstract method)를 사용하여 구현할 수 있으며, 이는 abc 모듈(Abstract Base Classes)을 통해 제공됩니다. **추상 클래스는 직접 인스턴스화될 수 없으며**, 하나 이상의 추상 메서드를 포함할 수 있습니다. 파생 클래스에서는 기반 추상 클래스의 **모든 추상 메서드를 구현**해야 합니다.

```python
from abc import ABC, abstractmethod

class DatabaseConnector(ABC):
    @abstractmethod
    def connect(self):
        pass

    @abstractmethod
    def disconnect(self):
        pass

class MySQLConnector(DatabaseConnector):
    def connect(self):
        print("Connecting to MySQL database...")

    def disconnect(self):
        print("Disconnecting from MySQL database...")

class SQLiteConnector(DatabaseConnector):
    def connect(self):
        print("Connecting to SQLite database...")

    def disconnect(self):
        print("Disconnecting from SQLite database...")

# 추상 클래스는 인스턴스화할 수 없습니다.
# db = DatabaseConnector()  # TypeError: Can't instantiate abstract class

# 파생 클래스의 인스턴스 생성
mysql = MySQLConnector()
sqlite = SQLiteConnector()

# 연결 메서드 호출
mysql.connect()
mysql.disconnect()

sqlite.connect()
sqlite.disconnect()
```

### 모든 추상 메서드를 구현 하지 않으면 오류가 발생합니다

```python
from abc import ABC, abstractmethod

class DatabaseConnector(ABC):
    @abstractmethod
    def connect(self):
        pass

    @abstractmethod
    def disconnect(self):
        pass

class MySQLConnector(DatabaseConnector):
    def connect(self):
        print("Connecting to MySQL database...")

    # def disconnect(self):
    #     print("Disconnecting from MySQL database...")

class SQLiteConnector(DatabaseConnector):
    def connect(self):
        print("Connecting to SQLite database...")

    def disconnect(self):
        print("Disconnecting from SQLite database...")

# 추상 클래스는 인스턴스화할 수 없습니다.
# db = DatabaseConnector()  # TypeError: Can't instantiate abstract class

# 파생 클래스의 인스턴스 생성
mysql = MySQLConnector()
sqlite = SQLiteConnector()

# 연결 메서드 호출
mysql.connect()
mysql.disconnect()

sqlite.connect()
sqlite.disconnect()
```

MySQLConnector 클래스에서 disconnect 메서드를 구현하지 않으면 오류가 발생합니다.

```python
TypeError: Can't instantiate abstract class MySQLConnector with abstract methods disconnect
```

---

해시태그: #Python #oop #encaosulation #inheritance #polymorphism #abstraction #캡슐화 #상속 #다형성 #추상화
