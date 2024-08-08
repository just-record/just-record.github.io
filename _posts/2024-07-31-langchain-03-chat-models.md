---
layout: single
title: "LangChain - 03. Chat models" 
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

## Chat models

<https://python.langchain.com/v0.2/docs/concepts/#chat-models>

- 메시지 시퀀스를 입력으로 사용하고 **채팅** 메시지를 출력으로 반환하는 언어 모델
- 대화 메시지에 고유한 역할을 할당하는 것을 지원 - AI, 사용자, 시스템 메시지
- LangChain 래퍼는 문자열이 입력으로 전달되면 이를 'HumanMessage'로 변환되어 기본 모델에 전달
- LangChain은 채팅 모델을 호스팅하지 않으며, 대신 제3자 통합에 의존

### Chat modles 의 일부 표준화된 매개 변수

- model: 모델의 이름
- temperature: 샘플링 온도
- timeout: 요청 시간 초과
- max_tokens: 생성할 최대 토큰 수
- stop: 기본 정지 시퀀스
- max_retries: 요청을 재시도할 최대 횟수
- api_key: 모델 제공자의 API 키
- base_url: 요청을 보낼 엔드포인트

표준 매개변수는 의도된 기능을 가진 매개변수를 노출하는 모델 제공자에게만 적용됩니다. 예를 들어, 일부 제공자는 최대 출력 토큰에 대한 구성을 노출하지 않으므로 이러한 경우 max_tokens를 지원할 수 없습니다. 표준 매개변수는 현재 자체 통합 패키지(예: langchain-openai, langchain-anthropic 등)가 있는 통합에만 적용되며(해당 통합에 특정한 다른 매개변수도 받아들임), langchain-community의 모델에는 적용되지 않습니다.

### Chat models 외 LLMs 의 차이

|  | Chat models | LLMs |
| --- | --- | --- |
|입출력 형식|메시지 시퀀스를 입력으로 받고 채팅 메시지를 출력|문자열을 입력으로 받아 문자열을 출력|
|모델의 연대|일반적으로 더 새로운 모델|전통적으로 더 오래된 모델|
|역할 구분|대화 메시지에 고유한 역할(AI, 사용자, 시스템 메시지 등)을 할당|기본적으로 역할 구분 없이 텍스트만 처리|
|유연성|LangChain 래퍼를 통해 문자열 입력도 지원|LangChain 래퍼를 통해 메시지 입력도 지원|
|사용 사례|대화형 AI, 챗봇, 복잡한 상호작용에 더 적합|단순한 텍스트 생성, 번역, 요약 등의 작업|
|최신 트렌드|최근에는 이미지, 오디오, 비디오 등을 처리할 수 있는 다중 모달 기능|주로 텍스트 처리에 초점|
|권장 사용|최신 모델들은 대부분 Chat models 형태로, 채팅이 아닌 용도로도 이를 사용하는 것이 권장||

## Chat models 예시

### 통합 패키지 - langchain-openai

```python
from dotenv import load_dotenv
load_dotenv()

from langchain_openai import ChatOpenAI

chat_model = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0.7,
    max_tokens=150,
)    

messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "What is the capital of Korea?"}
]

result = chat_model.invoke(messages)
print(result.content)
# Korea is divided into two countries: South Korea and North Korea. The capital of South Korea is Seoul, while the capital of North Korea is Pyongyang.
```

### 통합 패키지 - langchain-anthropic

```python
from dotenv import load_dotenv
load_dotenv()

from langchain_openai import ChatOpenAI

chat_model = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0.7,
    max_tokens=150,
)    

messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "What is the capital of Korea?"}
]

result = chat_model.invoke(messages)
print(result.content)
# There are actually two countries commonly referred to as Korea:
# 1. South Korea (officially the Republic of Korea): The capital is Seoul.
# 2. North Korea (officially the Democratic People's Republic of Korea): The capital is Pyongyang.
# When people ask about "Korea" without specifying North or South, they are often referring to South Korea. In this case, the capital would be Seoul, which is also the largest city in South Korea and one of the largest metropolitan areas in the world.
```

### langchain-anthropic

TODO - 최신 버전으로 업데이트 필요

---

해시태그: #LangChain #PromptTemplates #ChatPromptTemplate #from_messages #input_variables #partial_variables #MessagesPlaceholder #SystemMessage #HumanMessage #AIMessage #FewShotPromptTemplate #SemanticSimilarityExampleSelector #FewShotChatMessagePromptTemplate #PipelinePromptTemplate #LangSmith_hub
