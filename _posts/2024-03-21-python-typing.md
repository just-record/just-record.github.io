---
layout: single
title: "Python - typing"
categories: Python
tag: [Python, typing]
toc: true
toc_sticky: true
toc_label: "목차"
---

Python에서 타입 힌트를 구성하고 사용하면 코드의 가독성과 유지 관리성이 크게 향상될 수 있습니다. 또한 자동 완성 및 정적 분석과 같은 작업에 대한 더 나은 도구 지원을 용이하게 할 수 있습니다. 타입 힌트는 변수, 함수의 매개변수, 반환 값 등에 대한 타입 정보를 명시적으로 선언하는 것입니다.

타입 힌트은 Python 인터프리터에 의해 강제되지 않기 때문에 **실행 시점에는 무시**됩니다. 타입 힌팅은 주로 코드 가독성, IDE 지원, 정적 분석을 위한 용도입니다.

## 기본 타입

정수, 문자열, 부동 소수점 및 부울과 같은 기본 유형의 경우 변수 및 함수 반환 유형에 타입 정보를 명시적으로 선언

```python
# 변수에 타입 힌트 추가
name: str = "Python"
age: int = 30
is_student: bool = True

# 함수의 매개변수와 반환 값에 타입 힌트 추가
def greet(name: str) -> str:
    return f"Hello, {name}"

print(greet("Python"))
print(greet(1234))  # 타입 힌트는 강제성이 아니으로 에러가 발생하지 않음
```

## 컨테이너 타입

List, Dict, Tuple, Set 등에 대한 타입 정보를 명시적으로 선언

```python
# typing 모듈을 사용하여 타입 힌트 추가
from typing import List, Dict, Tuple, Set

nums: List[int] = [1, 2, 3]
pairs: Dict[str, int] = {"a": 1, "b": 2}
point: Tuple[int, int] = (3, 4)
unique: Set[str] = {"apple", "banana", "orange"}

nums = ["1", "2", "3"]  # 타입 힌트는 강제성이 아니으로 에러가 발생하지 않음
```

## Union과 '|'

여러 타입 중 하나를 가질 수 있는 변수를 선언

### Python 3.10 미만

Union을 사용

```python
from typing import Union

value: Union[int, str] = 42
value = "Hello"  # 이것도 가능

value = 3.14  # 타입 힌트는 강제성이 아니으로 에러가 발생하지 않음
```

### Python 3.10 이상

'|' 연산자를 사용

```python
value: int | str = 42
value = "Hello"  # 이것도 가능
```

## Optional

Optional을 사용하여 None 값도 허용하는 변수를 선언

```python
from typing import Optional

def get_value() -> Optional[int]:
    return None

def process(x: Optional[str]) -> None:
    if x is None:
        print("Value is None")
    else:
        print(f"Value: {x}")

value: Optional[int] = get_value()
process(value)  # Value is None

process("Hello")  # Value: Hello
```

## Any

모든 타입을 가질 수 있는 변수를 선언

```python
from typing import Any

def accept_anything(value: Any) -> None:
    print(f"Value: {value}")

accept_anything(42)
accept_anything("Hello")
accept_anything([1, 2, 3])
```

## Callable

함수를 가리키는 변수를 선언

```python
from typing import Callable

def apply(func: Callable[[int, int], int], x: int, y: int) -> int:
    return func(x, y)

def add(x: int, y: int) -> int:
    return x + y

print(apply(add, 3, 4))  # 7
```

## TypeVar와 Generic(제네릭)

TypeVar는 제네릭 타입(generic type)을 정의할 때 사용되는 타입 변수(type variable)를 생성합니다. 타입 변수는 실제 타입을 나타내는 것이 아니라, 타입을 매개변수화하여 나중에 구체적인 타입으로 대체될 수 있는 역할을 합니다.

TypeVar를 사용하여 Generic(제네릭) 타입을 선언합니다.

```python
from typing import TypeVar, List

T = TypeVar('T') # 타입 변수는 일반적으로 대문자 하나로 선언

def repeat(x: T, n: int) -> List[T]:
    result = []
    for _ in range(n):
        result.append(x)
    return result

print(repeat(42, 3))  # [42, 42, 42]
print(repeat("Hello", 2))  # ['Hello', 'Hello']
```

`repeat`는 제네릭 함수 입니다. 함수의 반환 타입은 `List[T]`로 정의되어 있습니다. 여기서 `T`는 타입 변수입니다. 함수를 호출할 때 `T`에 실제 타입이 대입되어 `List[int]`나 `List[str]`와 같은 구체적인 타입이 됩니다.

### Generic class (제네릭 클래스)

제네릭 클래스를 정의할 때도 TypeVar를 사용합니다.

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Box(Generic[T]):
    def __init__(self, value: T) -> None:
        self.value = value
```

`Box`는 제네릭 클래스 입니다. 클래스의 타입 변수는 `Generic[T]`로 정의되어 있습니다. 여기서 `T`는 타입 변수입니다. 클래스를 사용할 때 `T`에 실제 타입이 대입되어 `Box[int]`나 `Box[str]`와 같은 구체적인 타입이 됩니다.

## Literal 타입

Literal은 변수, 함수 매개변수 및 반환 유형에 사용할 수 있습니다. 이는 상수를 다루거나 함수가 특정 값 집합만 허용할 때 특히 유용합니다.

```python
from typing import Literal

def process_status(status: Literal["open", "close"]) -> None:
    print(f"Status: {status}")


process_status("open")  # Status: open
process_status("close")  # Status: close
process_status("stop")  # 타입 힌트는 강제성이 아니으로 에러가 발생하지 않음
```

## Annotated

'Annotated'는 Python 3.8 버전부터 추가된 기능으로 타입 힌트에 메타데이터를 추가할 수 있습니다.

```python
from typing import Annotated

Value = Annotated[int, "Must be >0"]
x: Value = 5 
y: Value = -1  # 에러는 발생 하지 않음

print(x, y)

def add(a: Annotated[int, "First arg"], b: Annotated[int, "Second arg"]) -> int:
    return a + b

print(add(3, 4))
print(add("3", "4"))  # 에러는 발생 하지 않음
```

---

해시태그: #Python #typing #Union #Optional #Any #Callable #TypeVar #Generic #Literal #Annotated
