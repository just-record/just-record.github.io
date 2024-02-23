---
layout: single
title: "Python - 디자인 패턴 - 개요"
categories: Python
tag: [Python, design pattern]
toc: true
toc_sticky: true
toc_label: "목차"
---

디자인 패턴(Design Pattern)은 코드를 더욱 유연하고 유지 관리하기 쉽게 만드는 데 도움이 되는 프로그래밍 방식의 템플릿입니다. 이는 **반복적인 프로그래밍 문제를 해결하기 위해 이미 검증된 설계 솔루션을 제공**합니다. 디자인 패턴은 대체로 세 가지 주요 카테고리로 분류됩니다: 생성 패턴(Creational Patterns), 구조 패턴(Structural Patterns), 그리고 행위 패턴(Behavioral Patterns)이 있습니다.

## 생성 패턴 (Creational Patterns)

**객체 생성 방식을 정의**합니다. 객체 생성 메커니즘을 효율화하고, 객체의 생성과 클래스의 인스턴스화를 분리함으로써 코드의 유연성을 높입니다.

- 싱글턴 패턴 (Singleton Pattern): 클래스의 인스턴스가 오직 하나만 생성되도록 보장합니다.
- 팩토리 메서드 패턴 (Factory Method Pattern): 객체 생성을 서브클래스에게 위임하여 객체 생성을 캡슐화합니다.
- 추상 팩토리 패턴 (Abstract Factory Pattern): 관련된 객체의 그룹을 생성하기 위한 인터페이스를 제공합니다.
- 빌더 패턴 (Builder Pattern): 복잡한 객체의 생성 과정을 단순화합니다.
- 프로토타입 패턴 (Prototype Pattern): 기존 객체를 복제하여 객체를 생성합니다.

## 구조 패턴 (Structural Patterns)

**객체 간의 관계를 정의**합니다. 클래스나 객체를 조합해 더 큰 구조를 만듭니다. 이는 다양한 객체들 간의 관계를 정의하여, 복잡한 시스템을 더 쉽게 개발하고 유지할 수 있게 돕습니다.

- 어댑터 패턴 (Adapter Pattern): 인터페이스 호환성 문제 없이 함께 작동하지 않는 클래스들을 결합할 수 있습니다.
- 컴포지트 패턴 (Composite Pattern): 객체들을 트리 구조로 구성하여, 개별 객체와 객체의 조합을 클라이언트가 동일하게 처리할 수 있도록 합니다.
- 프록시 패턴 (Proxy Pattern): 객체에 대한 접근을 제어하는 대리자 또는 자리표시자를 제공합니다.
- 파사드 패턴 (Facade Pattern): 복잡한 시스템에 대한 간단한 인터페이스를 제공합니다.
- 데코레이터 패턴 (Decorator Pattern): 객체에 동적으로 책임을 추가할 수 있습니다.

## 행위 패턴 (Behavioral Patterns)

**객체 간의 상호 작용 방식을 정의**합니다. 객체들 사이의 통신 방식을 개선하여, 시스템 내의 객체들이 상호작용하는 방식을 정의합니다. 이러한 패턴은 객체 간의 책임 분배와 알고리즘의 캡슐화를 도와줍니다.

- 옵서버 패턴 (Observer Pattern): 객체의 상태 변화를 관찰하고, 이러한 변화에 응답하는 객체들 간의 의존성을 정의합니다.
- 스테이트 패턴 (State Pattern): 객체의 내부 상태가 변경될 때마다 객체의 행동을 변경합니다.
- 전략 패턴 (Strategy Pattern): 실행 시 알고리즘을 객체의 행동으로 정의하여, 알고리즘을 사용하는 클라이언트와 독립적으로 알고리즘을 변경할 수 있게 합니다.
- 커맨드 패턴 (Command Pattern): 요청을 객체의 형태로 캡슐화하여, 사용자가 요청을 다양한 요청, 큐, 로그 요청과 함께 파라미터화할 수 있게 합니다.
- 메멘토 패턴 (Memento Pattern): 객체의 상태를 이전 상태로 복원할 수 있는 방법을 제공합니다(되돌리기 기능).
- 중재자 패턴 (Mediator Pattern): 객체 간의 복잡한 통신을 캡슐화하고, 객체들이 직접 참조하지 않도록 중재자 객체를 통해 이들 간의 상호작용을 조정합니다.
- 방문자 패턴 (Visitor Pattern): 객체 구조에 속한 요소에 대해 수행할 연산을 별도의 객체로 정의합니다. 연산을 수행할 때 객체 구조를 변경하지 않고도 새로운 연산을 추가할 수 있습니다.
- 체인 오브 리스폰서빌리티 패턴 (Chain of Responsibility Pattern): 요청을 여러 객체에 걸쳐 전달하는 방법을 제공하여, 요청을 처리할 객체를 런타임에 결정할 수 있게 합니다.
- 이터레이터 패턴 (Iterator Pattern): 컬렉션의 요소를 순회하는 방법을 제공하며, 컬렉션 구현과 순회 방법을 분리합니다.
- 템플릿 메소드 패턴 (Template Method Pattern): 연산의 골격을 정의하고, 일부 단계를 서브클래스에 연기함으로써 서브클래스가 특정 단계를 재정의할 수 있게 합니다.

---

해시태그: #Python #design pattern #Creational Patterns #Structural Patterns #Behavioral Patterns #생성 패턴 #구조 패턴 #행위 패턴
