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

### 방법 1 - from_template
prompt = PromptTemplate.from_template(template)
partial_prompt = prompt.partial(adjective="funny")
print(partial_prompt.invoke({"topic": "cats"}))

### 방법 2 - partial_variables
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

### 5. partial_variables - 함수와 함께 사용 하기

```python
from langchain_core.prompts import PromptTemplate
from datetime import datetime


def _get_datetime():
    now = datetime.now()
    return now.strftime("%m/%d/%Y, %H:%M:%S")


### 방법 1 - from_template
prompt = PromptTemplate(
    template="Tell me a {adjective} joke about the day {date}",
    input_variables=["adjective", "date"],
)
partial_prompt = prompt.partial(date=_get_datetime)
print(partial_prompt.format(adjective="funny"))
# Tell me a funny joke about the day 07/03/2024, 19:30:23

### 방법 2 - partial_variables
print("-" * 30)
prompt = PromptTemplate(
    template="Tell me a {adjective} joke about the day {date}",
    input_variables=["adjective"],
    partial_variables={"date": _get_datetime},
)
print(prompt.format(adjective="funny"))
# Tell me a funny joke about the day 07/03/2024, 19:30:23
```

```python
# 결과
Tell me a funny joke about the day 07/03/2024, 19:30:23
------------------------------
Tell me a funny joke about the day 07/03/2024, 19:30:23
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

## Few Shot과 Example Selector

### FewShotPromptTemplate

'FewShotPromptTemplate'은 Few-shot 학습을 위한 프롬프트 템플릿을 생성한다.

```python
from langchain_core.prompts import PromptTemplate

example_prompt = PromptTemplate.from_template("Question: {question}\n{answer}")

### example_prompt 출력
print(example_prompt)
# input_variables=['answer', 'question'] template='Question: {question}\n{answer}'

### few-shot example set
examples = [
    {
        "question": "Who lived longer, Muhammad Ali or Alan Turing?",
        "answer": """
Are follow up questions needed here: Yes.
Follow up: How old was Muhammad Ali when he died?
Intermediate answer: Muhammad Ali was 74 years old when he died.
Follow up: How old was Alan Turing when he died?
Intermediate answer: Alan Turing was 41 years old when he died.
So the final answer is: Muhammad Ali
""",
    },
    {
        "question": "When was the founder of craigslist born?",
        "answer": """
Are follow up questions needed here: Yes.
Follow up: Who was the founder of craigslist?
Intermediate answer: Craigslist was founded by Craig Newmark.
Follow up: When was Craig Newmark born?
Intermediate answer: Craig Newmark was born on December 6, 1952.
So the final answer is: December 6, 1952
""",
    },
    {
        "question": "Who was the maternal grandfather of George Washington?",
        "answer": """
Are follow up questions needed here: Yes.
Follow up: Who was the mother of George Washington?
Intermediate answer: The mother of George Washington was Mary Ball Washington.
Follow up: Who was the father of Mary Ball Washington?
Intermediate answer: The father of Mary Ball Washington was Joseph Ball.
So the final answer is: Joseph Ball
""",
    },
    {
        "question": "Are both the directors of Jaws and Casino Royale from the same country?",
        "answer": """
Are follow up questions needed here: Yes.
Follow up: Who is the director of Jaws?
Intermediate Answer: The director of Jaws is Steven Spielberg.
Follow up: Where is Steven Spielberg from?
Intermediate Answer: The United States.
Follow up: Who is the director of Casino Royale?
Intermediate Answer: The director of Casino Royale is Martin Campbell.
Follow up: Where is Martin Campbell from?
Intermediate Answer: New Zealand.
So the final answer is: No
""",
    },
]

### example set중 하나를 example_prompt에 적용
print('-'*30)
results = example_prompt.invoke(examples[0])
print(results)
# text='Question: Who lived longer, Muhammad Ali or Alan Turing?\n\nAre follow up questions needed here: Yes.\nFollow up: How old was Muhammad Ali when he died?\nIntermediate answer: Muhammad Ali was 74 years old when he died.\nFollow up: How old was Alan Turing when he died?\nIntermediate answer: Alan Turing was 41 years old when he died.\nSo the final answer is: Muhammad Ali\n'


### FewShotPromptTemplate 사용
print('-'*30)

from langchain_core.prompts import FewShotPromptTemplate

prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_prompt,
    suffix="Question: {input}",
    input_variables=["input"],
)

print(
    prompt.invoke({"input": "Who was the father of Mary Ball Washington?"}).to_string()
)
# Question: Who lived longer, Muhammad Ali or Alan Turing?

# Are follow up questions needed here: Yes.
# Follow up: How old was Muhammad Ali when he died?
# Intermediate answer: Muhammad Ali was 74 years old when he died.
# Follow up: How old was Alan Turing when he died?
# Intermediate answer: Alan Turing was 41 years old when he died.
# So the final answer is: Muhammad Ali


# Question: When was the founder of craigslist born?

# Are follow up questions needed here: Yes.
# Follow up: Who was the founder of craigslist?
# Intermediate answer: Craigslist was founded by Craig Newmark.
# Follow up: When was Craig Newmark born?
# Intermediate answer: Craig Newmark was born on December 6, 1952.
# So the final answer is: December 6, 1952


# Question: Who was the maternal grandfather of George Washington?

# Are follow up questions needed here: Yes.
# Follow up: Who was the mother of George Washington?
# Intermediate answer: The mother of George Washington was Mary Ball Washington.
# Follow up: Who was the father of Mary Ball Washington?
# Intermediate answer: The father of Mary Ball Washington was Joseph Ball.
# So the final answer is: Joseph Ball


# Question: Are both the directors of Jaws and Casino Royale from the same country?

# Are follow up questions needed here: Yes.
# Follow up: Who is the director of Jaws?
# Intermediate Answer: The director of Jaws is Steven Spielberg.
# Follow up: Where is Steven Spielberg from?
# Intermediate Answer: The United States.
# Follow up: Who is the director of Casino Royale?
# Intermediate Answer: The director of Casino Royale is Martin Campbell.
# Follow up: Where is Martin Campbell from?
# Intermediate Answer: New Zealand.
# So the final answer is: No


# Question: Who was the father of Mary Ball Washington?
```

### SemanticSimilarityExampleSelector

'SemanticSimilarityExampleSelector'는 주어진 입력과 가장 유사한 예제(Few shot을 위한 example set 중에서)를 선택한다. 가장 유사한 예제를 선택하기 위해 임베딩 벡터를 사용한다. 임베딩 벡터는 openai의 'OpenAIEmbeddings'를 사용하므로 openai의 API 키가 필요하다. 또한, 임베딩 벡터를 저장하고 유사성을 측정하기 위해 'Chroma'를 사용한다.

```python
# ... 위의 코드 examples 정의 까지는 코드가 동일함 ...
from dotenv import load_dotenv
load_dotenv()

from langchain_core.example_selectors import SemanticSimilarityExampleSelector

example_selector = SemanticSimilarityExampleSelector.from_examples(
    # This is the list of examples available to select from.
    examples,
    # This is the embedding class used to produce embeddings which are used to measure semantic similarity.
    OpenAIEmbeddings(),
    # This is the VectorStore class that is used to store the embeddings and do a similarity search over.
    Chroma,
    # This is the number of examples to produce.
    k=1,
)

# Select the most similar example to the input.
question = "Who was the father of Mary Ball Washington?"
selected_examples = example_selector.select_examples({"question": question})
print(f"Examples most similar to the input: {question}")


### 1. 가장 유사한 example 출력
for example in selected_examples:
    print("\n")
    for k, v in example.items():
        print(f"{k}: {v}")


### 2. FewShotPromptTemplate에 적용
print('-'*30)

from langchain_core.prompts import FewShotPromptTemplate

prompt = FewShotPromptTemplate(
    example_selector=example_selector,
    example_prompt=example_prompt,
    suffix="Question: {input}",
    input_variables=["input"],
)

print(
    prompt.invoke({"input": "Who was the father of Mary Ball Washington?"}).to_string()
)
```

```python
# 결과
Examples most similar to the input: Who was the father of Mary Ball Washington?


answer: 
Are follow up questions needed here: Yes.
Follow up: Who was the mother of George Washington?
Intermediate answer: The mother of George Washington was Mary Ball Washington.
Follow up: Who was the father of Mary Ball Washington?
Intermediate answer: The father of Mary Ball Washington was Joseph Ball.
So the final answer is: Joseph Ball

question: Who was the maternal grandfather of George Washington?
------------------------------
Question: Who was the maternal grandfather of George Washington?

Are follow up questions needed here: Yes.
Follow up: Who was the mother of George Washington?
Intermediate answer: The mother of George Washington was Mary Ball Washington.
Follow up: Who was the father of Mary Ball Washington?
Intermediate answer: The father of Mary Ball Washington was Joseph Ball.
So the final answer is: Joseph Ball


Question: Who was the father of Mary Ball Washington?
```

### FewShotChatMessagePromptTemplate

'FewShotChatMessagePromptTemplate'은 대화형 AI 모델을 위한 Few-shot 프롬프트 템플릿을 생성한다.

```python
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model="gpt-3.5-turbo-0125", temperature=0.0)

### 1. 정의 되지 않은 '🦜'로 연산하기
print(model.invoke("What is 2 🦜 9?").content)
# he expression "2 🦜 9" is not a standard mathematical operation or equation. It appears to be a combination of the number 2 and the parrot emoji 🦜 followed by the number 9. It does not have a specific mathematical meaning.

### 2. '🦜'연산을 Chat Few Shot Promt 만들기
print('-'*30)
from langchain_core.prompts import ChatPromptTemplate, FewShotChatMessagePromptTemplate

examples = [
    {"input": "2 🦜 2", "output": "4"},
    {"input": "2 🦜 3", "output": "5"},
]

# This is a prompt template used to format each individual example.
example_prompt = ChatPromptTemplate.from_messages(
    [
        ("human", "{input}"),
        ("ai", "{output}"),
    ]
)
few_shot_prompt = FewShotChatMessagePromptTemplate(
    example_prompt=example_prompt,
    examples=examples,
    input_variables=[],
)

print(few_shot_prompt.invoke({}).to_messages())
# [HumanMessage(content='2 🦜 2'), AIMessage(content='4'), HumanMessage(content='2 🦜 3'), AIMessage(content='5')]

### 3. Chat Few Shot Prompt를 사용하여 ChatOpenAI에 적용하기
print('-'*30)
final_prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "You are a wondrous wizard of math."),
        few_shot_prompt,
        ("human", "{input}"),
    ]
)

from langchain_openai import ChatOpenAI

chain = final_prompt | model

print(chain.invoke({"input": "What is 2 🦜 9?"}).content)
# 11
```

```python
# 결과
he expression "2 🦜 9" is not a standard mathematical operation or equation. It appears to be a combination of the number 2 and the parrot emoji 🦜 followed by the number 9. It does not have a specific mathematical meaning.
------------------------------
[HumanMessage(content='2 🦜 2'), AIMessage(content='4'), HumanMessage(content='2 🦜 3'), AIMessage(content='5')]
------------------------------
11
```

### SemanticSimilarityExampleSelector과 FewShotChatMessagePromptTemplate

```python
from dotenv import load_dotenv
load_dotenv()

from langchain_chroma import Chroma
from langchain_core.example_selectors import SemanticSimilarityExampleSelector
from langchain_openai import OpenAIEmbeddings

examples = [
    {"input": "2 🦜 2", "output": "4"},
    {"input": "2 🦜 3", "output": "5"},
    {"input": "2 🦜 4", "output": "6"},
    {"input": "What did the cow say to the moon?", "output": "nothing at all"},
    {
        "input": "Write me a poem about the moon",
        "output": "One for the moon, and one for me, who are we to talk about the moon?",
    },
]

to_vectorize = [" ".join(example.values()) for example in examples]
print(to_vectorize)
# ['2 🦜 2 4', '2 🦜 3 5', '2 🦜 4 6', 'What did the cow say to the moon? nothing at all', 'Write me a poem about the moon One for the moon, and one for me, who are we to talk about the moon?']
embeddings = OpenAIEmbeddings()
# 벡터 DB(Chroma) 에 저장
vectorstore = Chroma.from_texts(to_vectorize, embeddings, metadatas=examples)


### 1. example selector 로 가장 유사한 예제 선택하기
print('-'*30)

example_selector = SemanticSimilarityExampleSelector(
    vectorstore=vectorstore,
    k=2,
)
# The prompt template will load examples by passing the input do the `select_examples` method
print(example_selector.select_examples({"input": "horse"}))
# [{'input': 'What did the cow say to the moon?', 'output': 'nothing at all'}, {'input': '2 🦜 4', 'output': '6'}]


### 2. example selector가 포함된 Chat Prompt Template 만들기
print('-'*30)

from langchain_core.prompts import ChatPromptTemplate, FewShotChatMessagePromptTemplate

few_shot_prompt = FewShotChatMessagePromptTemplate(
    # The input variables select the values to pass to the example_selector
    input_variables=["input"],
    example_selector=example_selector,
    # Define how each example will be formatted.
    # In this case, each example will become 2 messages:
    # 1 human, and 1 AI
    example_prompt=ChatPromptTemplate.from_messages(
        [("human", "{input}"), ("ai", "{output}")]
    ),
)

print(few_shot_prompt.invoke(input="What's 3 🦜 3?").to_messages())
# What's 3 🦜 3?과 가장 유사한 예제 2개를 반환
# [HumanMessage(content='2 🦜 3'), AIMessage(content='5'), HumanMessage(content='2 🦜 4'), AIMessage(content='6')]

print('-'*30)
final_prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "You are a wondrous wizard of math."),
        few_shot_prompt,
        ("human", "{input}"),
    ]
)

print(few_shot_prompt.invoke(input="What's 3 🦜 3?"))
# messages=[HumanMessage(content='2 🦜 3'), AIMessage(content='5'), HumanMessage(content='2 🦜 4'), AIMessage(content='6')]

### Use with an chat model
print('-'*30)
from langchain_openai import ChatOpenAI

chain = final_prompt | ChatOpenAI(model="gpt-3.5-turbo", temperature=0.0)
print(chain.invoke({"input": "What's 3 🦜 3?"}).content)
# 6
```

## PipelinePromptTemplate

'PipelinePromptTemplate'은 여러 프롬프트 템플릿을 연결하여 하나의 프롬프트 템플릿을 생성한다.

```python
from langchain_core.prompts import PromptTemplate

### 1. 단순 문자열 연결
prompt = (
    PromptTemplate.from_template("Tell me a joke about {topic}")
    + ", make it funny"
    + "\n\nand in {language}"
)

print(prompt)
# input_variables=['language', 'topic'] template='Tell me a joke about {topic}, make it funny\n\nand in {language}'

print('-'*30)
print(prompt.format(topic="sports", language="spanish"))
# Tell me a joke about sports, make it funny

# and in spanish


### 2. Chat prompt 연결 - 리스트의 '+' 연산
print('-'*30)
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage

prompt = SystemMessage(content="You are a nice pirate")
new_prompt = (
    prompt + HumanMessage(content="hi") + AIMessage(content="what?") + "{input}"
)
print(new_prompt.format_messages(input="i said hi"))
# [SystemMessage(content='You are a nice pirate'), HumanMessage(content='hi'), AIMessage(content='what?'), HumanMessage(content='i said hi')]


### 3. PipelinePrompt 사용
print('-'*30)
from langchain_core.prompts import PipelinePromptTemplate, PromptTemplate

full_template = """{introduction}

{example}

{start}"""
full_prompt = PromptTemplate.from_template(full_template)

introduction_template = """You are impersonating {person}."""
introduction_prompt = PromptTemplate.from_template(introduction_template)

example_template = """Here's an example of an interaction:

Q: {example_q}
A: {example_a}"""
example_prompt = PromptTemplate.from_template(example_template)

start_template = """Now, do this for real!

Q: {input}
A:"""
start_prompt = PromptTemplate.from_template(start_template)

input_prompts = [
    ("introduction", introduction_prompt),
    ("example", example_prompt),
    ("start", start_prompt),
]
pipeline_prompt = PipelinePromptTemplate(
    final_prompt=full_prompt, pipeline_prompts=input_prompts
)

print(pipeline_prompt.input_variables)
# ['example_q', 'person', 'example_a', 'input']

print('-'*30)
print(
    pipeline_prompt.format(
        person="Elon Musk",
        example_q="What's your favorite car?",
        example_a="Tesla",
        input="What's your favorite social media site?",
    )
)
# You are impersonating Elon Musk.

# Here's an example of an interaction:

# Q: What's your favorite car?
# A: Tesla

# Now, do this for real!

# Q: What's your favorite social media site?
# A:
```

- 여러 개의 하위 프롬프트 템플릿을 정의 - (introduction, example, start)
- 하위 프롬프트 템플릿을 하나의 최종 템플릿(full_template)에 조합
- 'PipelinePromptTemplate'을 사용하여 위의 과정을 자동화

## 파일과 LangSmith hub에서 PromptTemplates 읽기

`prompt_basic.yaml` 파일

```yaml
_type: "prompt"
template: "Tell me a joke about {topic}"
input_variables: ["topic"]
```

```python
### 01. 파일에서 PromptTemplates 읽기
from langchain.prompts import load_prompt


file_path_prompt = './prompt_basic.yaml'

prompt_template = load_prompt(file_path_prompt)
print(prompt_template.invoke({"topic": "cats"}).to_messages())

### 2. LangSmith hub에서 PromptTemplates 읽기
print('='*30)
from langchain import hub

prompt_template = hub.pull("jbsh/langchain_practice_basic", api_url="https://api.hub.langchain.com")
print(prompt_template)
print('-'*30)
print(prompt_template.invoke({"topic": "cats"}).to_messages())
```

```python
# 결과
[HumanMessage(content='Tell me a joke about cats')]
==============================
input_variables=['topic'] metadata={'lc_hub_owner': 'jbsh', 'lc_hub_repo': 'langchain_practice_basic', 'lc_hub_commit_hash': '240f00963407c13026b1e00d32990a5e3117e63d47a911f02ec3fe3a77d7e543'} messages=[SystemMessagePromptTemplate(prompt=PromptTemplate(input_variables=[], template='You are a chatbot.')), HumanMessagePromptTemplate(prompt=PromptTemplate(input_variables=['topic'], template='Tell me a joke about {topic}'))]
------------------------------
[SystemMessage(content='You are a chatbot.'), HumanMessage(content='Tell me a joke about cats')]
```

---

해시태그: #LangChain #PromptTemplates #ChatPromptTemplate #from_messages #input_variables #partial_variables #MessagesPlaceholder #SystemMessage #HumanMessage #AIMessage #FewShotPromptTemplate #SemanticSimilarityExampleSelector #FewShotChatMessagePromptTemplate #PipelinePromptTemplate #LangSmith_hub
