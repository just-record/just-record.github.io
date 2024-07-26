---
layout: single
title: "LangChain - 02. PromptTemplates" 
categories: LangChain
tag: [Python, LLM, LangChain]
toc: true
toc_sticky: true
toc_label: "목차"
---

## LangChain 이란?

LangChain은 대규모 언어 모델(LLM)을 활용한 애플리케이션 개발을 위한 프레임워크입니다.

[공식 문서](https://python.langchain.com/v0.2/docs/introduction/)

[위의 공식 문서를 보고 연습한 곳](https://github.com/just-record/langchain_practice)

## PromptTemplates 이해 하기

### 1. 'Tell me a joke about cats'를 llm에 요청 하기

```python
# 앞으로 아래 2줄은 생략하겠습니다.
from dotenv import load_dotenv
load_dotenv()

from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")

result = llm.invoke("Tell me a joke about cats")
print(result.content)
```

```python
# 결과
Why was the cat sitting on the computer?

Because it wanted to keep an eye on the mouse!
```

### 2. cats를 변수화 하기

'Tell me a joke about {topic}'

```python
# ... 위와 동일 ...

query = "Tell me a joke about {topic}".format(topic="cats")
result = llm.invoke(query)
print(result.content)
```

### 3. PromptTemplates 사용하기

```python
from langchain_core.prompts import PromptTemplate

llm = ChatOpenAI(model="gpt-4o-mini")

query = "Tell me a joke about {topic}"
# topic을 변수화한 query를 PromptTemplate으로 변환
prompt_template = PromptTemplate.from_template(query) 

### 1. prompt 확인하기
print(prompt_template.format(topic="cats")) # topic을 cats로 할당
print(prompt_template.invoke({"topic": "cats"}))

### 2. PromptTemplate을 chain에 사용
print('-'*50)
chain = prompt_template | llm
print(chain.invoke({"topic": "cats"}).content)
```

```python
# 결과
Tell me a joke about cats
text='Tell me a joke about cats'
--------------------------------------------------
Why was the cat sitting on the computer?

Because it wanted to keep an eye on the mouse!
```

## PromptTemplates 사용 예시

### 1. from_template 사용

```python
from langchain_core.prompts import PromptTemplate

prompt_template = PromptTemplate.from_template("Tell me a joke about {topic}")

print(prompt_template.invoke({"topic": "cats"}))
# text='Tell me a joke about cats'
```

### 2. input_variables 사용

변수를 명시적으로 선언

```python
from langchain_core.prompts import PromptTemplate

prompt_template = PromptTemplate(
    template="Tell me a joke about {topic}",
    input_variables=["topic"]
)

print(prompt_template.invoke({"topic": "cats"}))
# text='Tell me a joke about cats'
```

### 3. 2개 이상의 변수 사용

```python
from langchain_core.prompts import PromptTemplate

template = "Tell me a {adjective} joke about {topic}"

### 방법 1. 
prompt_template = PromptTemplate.from_template(template)

print(prompt_template.invoke({"adjective": "funny", "topic": "joke"}))


### 방법 2.
print('-'*50)
prompt_template = PromptTemplate(
    template="Tell me a {adjective} joke about {topic}",
    input_variables=["adjective", "topic"],
    validate_template=True # template에 모든 변수가 포함되어 있는지,  사용되는지 확인
)

print(prompt_template.invoke({"adjective": "funny", "topic": "joke"}))
```

```python
# 결과
text='Tell me a funny joke about joke'
--------------------------------------------------
text='Tell me a funny joke about joke'
```

### 4. partial_variables - 변수를 미리 할당

template의 변수 일부분을 미리 할당

```python
from langchain_core.prompts import PromptTemplate

template = "Tell me a {adjective} joke about {topic}"

### 방법 1
prompt = PromptTemplate.from_template(template)
partial_prompt = prompt.partial(adjective="funny")
print(partial_prompt.invoke({"topic": "cats"}))

### 부분을 포매팅 하기(문자열) - 방법 2
print('-'*30)
prompt = PromptTemplate(
    template=template, 
    input_variables=["topic"], 
    partial_variables={"adjective": "funny"}
)
print(partial_prompt.invoke({"topic": "cats"}))
```

```python
# 결과
text='Tell me a funny joke about cats'
------------------------------
text='Tell me a funny joke about cats'
```

## ChatPromptTemplate 란?

대화형 AI 모델을 위한 구조화된 다중 메시지 프롬프트 생성기

### PromptTemplate과 ChatPromptTemplate의 차이점

|구분|PromptTemplate|ChatPromptTemplate|
|---|---|---|
|사용 목적|일반적인 텍스트 기반 프롬프트에 사용되면 단일 문자열 형태의 프롬프트를 생성|대화형 AI 모델에 특화되어 있으면 여러 메시지로 구성된 대화 형식의 프롬프트를 생성|
|구조|단일 템플릿 문자열로 구성되며 변수를 중괄호 {} 안에 넣어 표현|여러 개의 메시지 템플릿으로 구성되며 각 메시지는 역할(시스템, 사용자, AI)과 내용을 가짐|
|메시지 형식|단일 문자열을 출력|리스트 형태의 메시지를 출력하며 각 메시지는 역할과 내용을 포함하는 딕셔너리 형태|
|호환성|대부분의 텍스트 생성 모델과 호환|주로 대화형 모델과 함께 사용|

```python
from langchain_core.prompts import ChatPromptTemplate

prompt_template = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant"),
    ("user", "Tell me a joke about {topic}")
])

### 1. prompt_template invoke하여 결과 확인
print(prompt_template.invoke({"topic": "cats"}))


### 2. llm까지 연결하여 결과 확인
print('-'*30)
from dotenv import load_dotenv
load_dotenv()

from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")

chain = prompt_template | llm
print(chain.invoke({"topic": "cats"}))
```

```python
# 결과
messages=[SystemMessage(content='You are a helpful assistant'), HumanMessage(content='Tell me a joke about cats')]
------------------------------
Why was the cat sitting on the computer?

Because it wanted to keep an eye on the mouse!
```

## ChatPromptTemplate 사용 예시

### 1. from_messages

```python
from langchain_core.prompts import ChatPromptTemplate

template = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful AI bot. Your name is {name}."),
    ("human", "Hello, how are you doing?"),
    ("ai", "I'm doing well, thanks!"),    # ai의 답변을 추가
    ("human", "{user_input}"),
])

prompt_value = template.invoke(
    {
        "name": "Bob",
        "user_input": "What is your name?"
    }
)
print(prompt_value)
```

```python
# 결과 - {name}과 {user_input}을 실제 값으로 대체 함
messages=[
    SystemMessage(content='You are a helpful AI bot. Your name is Bob.'), 
    HumanMessage(content='Hello, how are you doing?'), 
    AIMessage(content="I'm doing well, thanks!"), 
    HumanMessage(content='What is your name?')
    ]
```

### 2. input_variables

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.messages import SystemMessage, HumanMessage, AIMessage

template = ChatPromptTemplate(
    input_variables=["name", "user_input"],
    messages=[
        SystemMessage(content="You are a helpful AI bot. Your name is {name}."),
        HumanMessage(content="Hello, how are you doing?"),
        AIMessage(content="I'm doing well, thanks!"),
        HumanMessage(content="{user_input}"),
    ]
)

prompt_value = template.invoke(
    {
        "name": "Bob",
        "user_input": "What is your name?"
    }
)
print(prompt_value)
```

```python
# 결과 - {name}과 {user_input}을 실제로 대체 하진 않음
messages=[
    SystemMessage(content='You are a helpful AI bot. Your name is {name}.'), 
    HumanMessage(content='Hello, how are you doing?'), 
    AIMessage(content="I'm doing well, thanks!"), 
    HumanMessage(content='{user_input}')
    ]
```

## MessagesPlaceholder

특정한 위치에 메시지 목록을 삽입하기

```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.messages import HumanMessage, AIMessage


### 01. MessagesPlaceholder
prompt_template = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant"),
    MessagesPlaceholder("msgs")
])

results = prompt_template.invoke({"msgs": [HumanMessage(content="hi!")]})
print(results)


### 02. "placeholder"
print('-'*30)
prompt_template = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant"),
    ("placeholder", "{msgs}")
])

results = prompt_template.invoke({"msgs": [HumanMessage(content="hi!")]})
print(results)


### 3. 2개 이상의 placeholder
print('-'*30)
prompt_template2 = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant"),
    MessagesPlaceholder("human_msgs"),
    MessagesPlaceholder("ai_msgs")
])

results = prompt_template2.invoke({"human_msgs": [HumanMessage(content="hi!")], "ai_msgs": [AIMessage(content="Hello! How can I assist you today?!")]})
print(results)

```

```python
# 결과
messages=[SystemMessage(content='You are a helpful assistant'), HumanMessage(content='hi!')]
messages=[SystemMessage(content='You are a helpful assistant'), HumanMessage(content='hi!')]
messages=[SystemMessage(content='You are a helpful assistant'), HumanMessage(content='hi!'), AIMessage(content='Hello! How can I assist you today?!')]false
```

---

해시태그: #LangChain #PromptTemplates #ChatPromptTemplate #from_messages #input_variables #partial_variables #MessagesPlaceholder #SystemMessage #HumanMessage #AIMessage
