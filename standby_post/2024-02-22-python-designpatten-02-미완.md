---
layout: single
title: "Python - 디자인 패턴 - 생성 패턴"
categories: Python
tag: [Python, design pattern]
toc: true
toc_sticky: true
toc_label: "목차"
---
객체 생성 방식을 정의합니다.

## 싱글턴 패턴 (Singleton Pattern)

클래스의 인스턴스가 오직 하나만 생성되도록 보장합니다. 프로그램 실행 중에 클래스의 객체가 한 번만 생성되고, 모든 접근은 이 유일한 객체에 대해 이루어집니다.

### __new__ 이용

Python의 __new__ 메소드는 __init__ 메소드보다 먼저 호출되어 인스턴스를 생성합니다. 이 특성을 이용하여 클래스의 인스턴스가 이미 생성되었는지 확인하고, 생성되지 않았다면 인스턴스를 생성하고, 이미 생성되었다면 기존의 인스턴스를 반환하는 방식으로 Singleton 패턴을 구현할 수 있습니다.

```python
class Singleton:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            # 다른 클래스를 상속하지 않았으므로 object 클래스(파이썬에서 모든 클래스의 최상위 부모 클래스)의 __new__ 메소드를 호출
            cls._instance = super(Singleton, cls).__new__(cls, *args, **kwargs)
        return cls._instance

# 사용 예
singleton1 = Singleton()
singleton2 = Singleton()

print(singleton1 is singleton2)  # True를 출력, 두 변수가 동일한 인스턴스를 가리킴
```

### decorator 이용

decorator를 이용 하는 방법인데 closure와 비교하여 정확히 이해가 되지 않습니다.

```python
def singleton(cls):
    instances = {}
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance

@singleton
class Singleton:
    pass

# 사용 예
singleton1 = Singleton()
singleton2 = Singleton()

print(singleton1 is singleton2)  # True를 출력, 두 변수가 동일한 인스턴스를 가리킴
```

## 팩토리 메서드 패턴 (Factory Method Pattern)

객체 생성을 서브클래스에게 위임하여 객체 생성을 캡슐화합니다. 생성할 객체의 정확한 클래스를 지정하지 않고도 객체를 생성할 수 있는 인터페이스를 제공합니다. 이 패턴은 객체 생성을 서브클래스에게 맡김으로써 객체 생성을 캡슐화합니다. 이 패턴은 주로 서브클래스가 어떤 클래스의 인스턴스를 생성할지 결정하게 하려고 할 때 사용됩니다.

```python
from abc import ABC, abstractmethod

class Logger(ABC):
    @abstractmethod
    def log(self, message: str):
        pass

class FileLogger(Logger):
    def log(self, message: str):
        print(f"Logging '{message}' to a file.")

class ConsoleLogger(Logger):
    def log(self, message: str):
        print(f"Logging '{message}' to the console.")

class LoggerFactory(ABC):
    @abstractmethod
    def create_logger(self) -> Logger:
        pass

class FileLoggerFactory(LoggerFactory):
    def create_logger(self) -> Logger:
        return FileLogger()

class ConsoleLoggerFactory(LoggerFactory):
    def create_logger(self) -> Logger:
        return ConsoleLogger()

# 사용 예
factory = FileLoggerFactory()
logger = factory.create_logger()
logger.log("This is a test.")  # 파일에 로깅

factory = ConsoleLoggerFactory()
logger = factory.create_logger()
logger.log("This is a test.")  # 콘솔에 로깅
```

## 추상 팩토리 패턴 (Abstract Factory Pattern)

관련된 객체의 그룹을 생성하기 위한 인터페이스를 제공하지만, 그 객체들이 생성될 구체적인 클래스를 지정하지 않습니다. 이 패턴은 여러 팩토리 메소드를 그룹화하여 작동하며, 클라이언트가 구체적인 클래스를 명시하지 않고도 서로 관련있거나 의존적인 여러 객체를 생성할 수 있게 합니다.

```python
from abc import ABC, abstractmethod

class Button(ABC):
    @abstractmethod
    def paint(self):
        pass

class LinuxButton(Button):
    def paint(self):
        print("Render a button in a Linux style.")

class WindowsButton(Button):
    def paint(self):
        print("Render a button in a Windows style.")

class GUIFactory(ABC):
    @abstractmethod
    def create_button(self) -> Button:
        pass

class LinuxFactory(GUIFactory):
    def create_button(self) -> Button:
        return LinuxButton()

class WindowsFactory(GUIFactory):
    def create_button(self) -> Button:
        return WindowsButton()

# 사용 예
factory = LinuxFactory()
button = factory.create_button()
button.paint()  # Linux 스타일 버튼 렌더링

factory = WindowsFactory()
button = factory.create_button()
button.paint()  # Windows 스타일 버튼 렌더링
```

## 빌더 패턴 (Builder Pattern)

복잡한 객체의 생성 과정을 단순화합니다.

## 프로토타입 패턴 (Prototype Pattern)

기존 객체를 복제하여 객체를 생성합니다.

---

해시태그: #Python #design pattern #Creational Patterns #Structural Patterns #Behavioral Patterns #생성 패턴 #구조 패턴 #행위 패턴
