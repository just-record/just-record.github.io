---
layout: single
title: "Python - class"
categories: Python
tag: [Python, class]
toc: true
toc_sticky: true
toc_label: "목차"
---
Python의 class에 대해 알아보겠습니다.

## 자동차

우리가 좋아하는 자동차를 Python의 class로 표현해 보겠습니다. 자동차는 이름이 있고 달리 수 있습니다.

```python
class Car:
    def __init__(self, name):
        self.name = name

    def run(self):
        return f"{self.name} runs!"
```

자동차의 설계도 입니다.

- 자동차를 구입하면 이름을 지어 줍니다: `__init__` 메소드
  - `self`는 객체 자신을 의미합니다.

- 자동차는 달릴 수 있습니다: `run` 메소드

```python
my_car = Car("Benz")
print(my_car.run())
```

```text
Benz runs!
```

- 'Benz'라는 이름을 가진 자동차를 만듭니다.
- 자동차가 달립니다.

## 자동차들

Benz 외에도 여러 자동차가 있을 수 있습니다.

```python
my_car = Car("Benz")
your_car = Car("BMW")
his_car = Car("Audi")
her_car = Car("Lamborghini")

print(my_car.run())
print(your_car.run())
print(his_car.run())
print(her_car.run())
```

```text
Benz runs!
BMW runs!
Audi runs!
Lamborghini runs!
```

이처럼 class를 사용하여 이름이 다른 여러 자동차를 만들 수 있습니다.

## 후진도 할 수 있는 자동차

이번 자동차는 색깔도 있고 후진도 할 수 있습니다.

```python
class Car:
    def __init__(self, name, color):
        self.name = name
        self.color = color

    def run(self):
        return f"{self.name} runs!"

    def reverse(self):
        return f"{self.name} reverses!"
```

```python
my_car = Car("Benz", "black")
your_car = Car("BMW", "white")

print(my_car.run())
print(my_car.reverse())
print(your_car.run())
print(your_car.reverse())
```

```text
Benz runs!
Benz reverses!
BMW runs!
BMW reverses!
```

---

해시태그: #python #decorator #closure
