---
layout: single
title: "vLLM - 02. vLLM 연습하기" 
categories: vLLM
tag: [vLLM]
toc: true
toc_sticky: true
toc_label: "목차"
---

## vLLM - 생각나는 대로 연습해 보기

기본 사용을 시작으로 하여 생각나는 대로 연습해 보았습니다.

## 기본 사용

기본적인 사용 방법으로 시작

```python
from vllm import LLM, SamplingParams

# Initialize the model
# llm = LLM(model="facebook/opt-125m")
llm = LLM(model="gpt2")

# Set up the prompt
prompt = "Tell me a short story about a robot learning to paint:"

# Set up sampling parameters
sampling_params = SamplingParams(temperature=0.8, top_p=0.95, max_tokens=1000)

# Generate the output
output = llm.generate(prompt, sampling_params)

print("=" * 30)
print(output[0].outputs[0].text)
```

✔️ 실행

답변은 생성되는데 내용이 조금 이상하다. => 모델을 조금 더 큰 것으로 변경해 보자.

```bash
 How did you get your first job? You probably don't know about the robot, right? Except you're not using a computer to learn painting.

Well, when I started working on the robot, I was already building a system for a table. I couldn't figure out what I was doing, so I designed a series of components. I don't think there's a simple reason for this, but in the beginning I was starting from scratch to build the system. I then realized that we could build a 'piece of art' based on an object as complex as a table. It wasn't simply that I had some idea of how to do it, but I was building a system for a table.
...
```

## 기본 사용 - 모델 변경(meta-llama/Llama-3.1-8B-Instruct)

`meta-llama/Llama-3.1-8B-Instruct`로 모델 변경

```python
from vllm import LLM, SamplingParams

# Initialize the model
llm = LLM(model="meta-llama/Llama-3.1-8B-Instruct")

# Set up the prompt
prompt = "Tell me a short story about a robot learning to paint:"

# Set up sampling parameters
sampling_params = SamplingParams(temperature=0.8, top_p=0.95, max_tokens=1000)

# Generate the output
outputs = llm.generate(prompt, sampling_params)

# Print the generated text
print("=" * 30)
print(output[0].outputs[0].text)
```

✔️ 실행 결과 - 오류 발생

```bash
...
[rank0]: ValueError: The model's max seq len (131072) is larger than the maximum number of tokens that can be stored in KV cache (41200). Try increasing gpu_memory_utilization or decreasing max_model_len when initializing the engine.
```

✔️ 코드 수정

`gpu_memory_utilization`과 `max_model_len`을 변경하여 실행하니 정상적으로 답변을 생성하고 내용도 적절한 거 같다.

```python
from vllm import LLM, SamplingParams

# Initialize the model
llm = LLM(
    model="meta-llama/Llama-3.1-8B-Instruct", 
    gpu_memory_utilization=0.9,  # Increase GPU memory usage
    max_model_len=40960          # Reduce the maximum sequence length
)

# Set up the prompt
prompt = "Tell me a short story about a robot learning to paint:"

# Set up sampling parameters
sampling_params = SamplingParams(temperature=0.8, top_p=0.95, max_tokens=1000)

# Generate the output
output = llm.generate(prompt, sampling_params)

# Print the generated text
print("=" * 30)
print(output[0].outputs[0].text)
```

✔️ 실행 결과

```bash
 “Robbie’s Brush with Art”

Robbie, a sleek and modern robot, was created to assist in various tasks around the house. But as he hummed along, day in and day out, he began to feel a sense of monotony. The same routine, day in and day out. His creator, an eccentric artist named Dr. Luna, had other plans for Robbie. She wanted to teach him the art of painting.
...
```

## 배치 사용

여러 개의 prompt를 사용하여 답변을 생성

```python
from vllm import LLM, SamplingParams

# Initialize the model
llm = LLM(
    model="meta-llama/Llama-3.1-8B-Instruct", 
    gpu_memory_utilization=0.9,  # Increase GPU memory usage
    max_model_len=40960          # Reduce the maximum sequence length
)

# Set up the prompt
prompts = [
    "Tell me a short story about a robot learning to paint:",
    "Tell me briefly about deep learning:",
    "Tell me briefly about Seoul, Korea:"
]

# Set up sampling parameters
sampling_params = SamplingParams(temperature=0.8, top_p=0.95, max_tokens=1000)

# Generate the output
outputs = llm.generate(prompts, sampling_params)

# Print the generated text
print("=" * 30)
for output in outputs:
    print(output.outputs[0].text)
    print('-' * 30)    
```

✔️ 실행 결과

```bash
 “Robbie’s Brush with Art”

Robbie, a sleek and modern robot, was created to assist in various tasks around the house. But as he whirred and beeped through his daily routine, he began to feel a spark of creativity within him. One day, while observing the human artist, Mrs. Jenkins, as she painted a beautiful landscape, Robbie became fascinated with the colors and textures on her canvas.
...
------------------------------
 The deep learning approach that has taken the machine learning world by storm, and is driving AI research forward with huge advancements in recent years.  Deep learning techniques are a type of machine learning that uses multiple layers of artificial neural networks to process and analyze data.
The power of deep learning lies in its ability to automatically learn and improve on its own, rather than requiring manual feature engineering and data preparation. This enables deep learning models to learn complex patterns and relationships in data, which has led to significant breakthroughs in image, speech, and natural language processing.
...
------------------------------
 What is it known for?
Seoul is the capital and largest city of South Korea, known for its rich cultural heritage, bustling streets, and high-tech infrastructure. Here are some of the top things Seoul is known for:
Rich History and Culture:
Seoul has a long history dating back to the 14th century and has been the capital of Korea for centuries. The city is home to many historical sites, including Gyeongbokgung Palace, Bukchon Hanok Village, and the National Folk Museum of Korea.
...
```

## 한국어 사용

한국어로 prompt를 사용하여 답변을 생성

```python
from vllm import LLM, SamplingParams

# Initialize the model
llm = LLM(
    model="meta-llama/Llama-3.1-8B-Instruct", 
    gpu_memory_utilization=0.9,  # Increase GPU memory usage
    max_model_len=40960          # Reduce the maximum sequence length
)

# Set up the prompt
# prompt = "Tell me a short story about a robot learning to paint:"
# prompt = "그림을 배우는 로봇에 대한 짧은 이야기를 들려주세요:"
prompt = "대한민국의 수도는 어디인가요? 그리고 그 수도에 대해 설명해주세요."

# Set up sampling parameters
# sampling_params = SamplingParams(temperature=0.8, top_p=0.95, max_tokens=1000)
sampling_params = SamplingParams(temperature=0.0, top_p=0.95, max_tokens=1000)

# Generate the output
outputs = llm.generate([prompt], sampling_params)

# Print the generated text
print("=" * 30)
for output in outputs:
    print(output.outputs[0].text)
```

✔️ 실행 결과

같은 문장을 계속 반복

```bash
대한민국의 수도는 서울입니다. 서울은 한국의 수도이자, 가장 큰 도시입니다. 서울은 한국의 정치, 경제, 문화, 교육, 과학, 기술, 미디어, 예술, 스포츠, 그리고 국제 관계의 중심지입니다. 서울은 한국의 역사와 문화의 상징입니다. 서울은 한국의 수도이자, 가장 큰 도시입니다. 서울은 한국의 정치, 경제, 문화, 교육, 과학, 기술, 미디어, 예술, 스포츠, 그리고 국제 관계의 중심지입니다. 서울은 한국의 역사와 문화의 상징입니다. 서울은 한국의 수도이자, 가장 큰 도시입니다. 서울은 한국의 정치, 경제, 문화, 교육, 과학, 기술, 미디어, 예술, 스포츠, 그리고 국제 관계의 중심지입니다. 서울은 한국의 역사와 문화의 상징입니다. 
서울은 한국의 수도이자, 가장 큰 도시입니다. 서울은 한국의 정치, 경제, 문화, 교육, 과학, 기술, 미디어, 예술, 스포츠, 그리고 국제 관계의 중심지입니다. 서울은 한국의 역사와 문화의 상징입니다. 
...
```

✔️ 코드 수정 - `tokenizer`를 사용하여 `stop_token_ids`를 추가

```python
from vllm import LLM, SamplingParams
from transformers import AutoTokenizer

BASE_MODEL = "meta-llama/Llama-3.1-8B-Instruct"

# Initialize the model
llm = LLM(
    model=BASE_MODEL, 
    gpu_memory_utilization=0.9,  # Increase GPU memory usage
    max_model_len=40960          # Reduce the maximum sequence length
)

tokenizer = AutoTokenizer.from_pretrained(BASE_MODEL)

eos_token_id = [tokenizer.eos_token_id, tokenizer.convert_tokens_to_ids("<|eot_id|>")]

# Set up the prompt
# prompt = "Tell me a short story about a robot learning to paint:"
# prompt = "그림을 배우는 로봇에 대한 짧은 이야기를 들려주세요:"
prompt = "대한민국의 수도는 어디인가요? 그리고 그 수도에 대해 설명해주세요."

# Set up sampling parameters
# sampling_params = SamplingParams(temperature=0.8, top_p=0.95, max_tokens=1000)
sampling_params = SamplingParams(stop_token_ids=eos_token_id, temperature=0.0, top_p=0.95, max_tokens=1000)

# Generate the output
outputs = llm.generate([prompt], sampling_params)

# Print the generated text
print("=" * 30)
for output in outputs:
    print(output.outputs[0].text)
```

✔️ 실행 결과

실패. 문장이 계속 반복 됨

```bash
대한민국의 수도는 서울입니다. 서울은 한국의 수도이자, 가장 큰 도시입니다. 서울은 한국의 정치, 경제, 문화, 교육, 과학, 기술, 미디어, 예술, 스포츠, 그리고 국제 관계의 중심지입니다. 서울은 한국의 역사와 문화의 상징입니다. 서울은 한국의 수도이자, 가장 큰 도시입니다. 서울은 한국의 정치, 경제, 문화, 교육, 과학, 기술, 미디어, 예술, 스포츠, 그리고 국제 관계의 중심지입니다. 서울은 한국의 역사와 문화의 상징입니다. 서울은 한국의 수도이자, 가장 큰 도시입니다. 서울은 한국의 정치, 경제, 문화, 교육, 과학, 기술, 미디어, 예술, 스포츠, 그리고 국제 관계의 중심지입니다. 서울은 한국의 역사와 문화의 상징입니다. 
서울은 한국의 수도이자, 가장 큰 도시입니다. 서울은 한국의 정치, 경제, 문화, 교육, 과학, 기술, 미디어, 예술, 스포츠, 그리고 국제 관계의 중심지입니다. 서울은 한국의 역사와 문화의 상징입니다.
...
```

## 한국어 사용 - 한국어 모델 사용(sh2orc/llama-3-korean-8b)

<https://huggingface.co/sh2orc/Llama-3-Korean-8B>

한국어로 파인 튜닝한 모델을 사용하여 한국어로 prompt를 사용하여 답변을 생성 => prompt가 채팅 형식

```python
from vllm import LLM, SamplingParams
from transformers import AutoTokenizer, pipeline

BASE_MODEL = "sh2orc/llama-3-korean-8b"
llm = LLM(model=BASE_MODEL)

tokenizer = AutoTokenizer.from_pretrained(BASE_MODEL)
tokenizer.pad_token = tokenizer.eos_token
tokenizer.padding_side = 'right'

messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "대한민국의 수도는 어디인가요? 그리고 그 수도에 대해 설명해주세요."}, 
]

prompt_message = tokenizer.apply_chat_template(
        messages, 
        tokenize=False, 
        add_generation_prompt=True,
)

eos_token_id = [tokenizer.eos_token_id, tokenizer.convert_tokens_to_ids("<|eot_id|>")]

outputs = llm.generate(prompt_message, SamplingParams(stop_token_ids=eos_token_id, temperature=0.8, top_p=0.95,max_tokens=512))

for output in outputs:
    propt = output.prompt
    generated_text = output.outputs[0].text
    print(generated_text)
```

✔️ 실행 결과

```bash
대한민국의 수도는 서울특별시입니다.

서울특별시는 한국의 수도이자 가장 큰 도시입니다. 면적은 약 605.21 km²이며, 인구는 약 10,218,107명입니다. 서울특별시는 1394년 조선 королKing Taejo가 경복궁을 세우면서 수도가 된 곳으로, 조선 시대에 대표적인 문화와 정치의 중심지로 성장했습니다. 도시의 명칭은 '남쪽의 경성'으로, 경복궁은 왕이 생활하며 정치 활동을 하는 곳으로 모두가 볼 수 있도록 공개하는 제도를 만들었습니다. 

서울특별시에는 다양한 문화유산이 곳곳에 남아 있습니다. 한옥마을, 종로, 종로5가, 경복궁, 전통시장 등이 대표적인 예입니다. 한옥마을은 정착형 한옥을 보존하고, 종로와 종로5가는 서울의 역사와 문화를 살펴볼 수 있는 곳입니다. 경복궁은 조선 시대 왕실의 궁궐로, 전통시장은 다양한 음식과 상품이 판매되는 곳입니다. 

또한, 서울은 젊은 인구가 많은 도시로, 다양한 즐길 거리가 많습니다. 함께 볼 수 있는 곳으로는 명동, 홍대, 이태원, 명륜3사거리 등이 있습니다. 이외에도, 서울에는 다양한 박물관과 미술관, 공원과 공연장이 많습니다.
```

## FastAPI와 통합 - meta-llama/Llama-3.1-8B-Instruct

FastAPI와 통합하여 API 서버로 사용

### FastAPI 서버 생성

`app.py`

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from vllm import LLM, SamplingParams
import uvicorn

app = FastAPI()

# Initialize the model
# BASE_MODEL = "sh2orc/llama-3-korean-8b"
# llm = LLM(model=BASE_MODEL)

BASE_MODEL = "meta-llama/Llama-3.1-8B-Instruct"
llm = LLM(
    model="meta-llama/Llama-3.1-8B-Instruct", 
    gpu_memory_utilization=0.9,  # Increase GPU memory usage
    max_model_len=40960          # Reduce the maximum sequence length
)

class GenerationRequest(BaseModel):
    prompt: str
    max_tokens: int = 1000
    temperature: float = 0.7
    top_p: float = 0.95

class GenerationResponse(BaseModel):
    generated_text: str

@app.post("/generate", response_model=GenerationResponse)
async def generate_text(request: GenerationRequest):
    try:
        sampling_params = SamplingParams(
            temperature=request.temperature,
            top_p=request.top_p,
            max_tokens=request.max_tokens
        )
        outputs = llm.generate([request.prompt], sampling_params)
        return GenerationResponse(generated_text=outputs[0].outputs[0].text)
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

✔️ 서버 실행

```bash
python app.py
# python3 app.py
```

### Client - API 호출

`api_client.py`

```python
import requests
import json

# The URL where your FastAPI server is running
API_URL = "http://localhost:8000/generate"

# The request payload
payload = {
    "prompt": "대한민국의 수도는 어디인가요? 그리고 그 수도에 대해 설명해주세요: ",
    "max_tokens": 500,
    "temperature": 0.8,
    "top_p": 0.95
}

# Send a POST request to the API
response = requests.post(API_URL, json=payload)

# Check if the request was successful
if response.status_code == 200:
    # Parse the JSON response
    result = response.json()
    
    # Print the generated text
    print(result['generated_text'])
else:
    print(f"Error: {response.status_code}")
    print(response.text)
```

✔️ API 호출

```bash
python api_client.py
# python3 api_client.py
```

✔️ 결과

오픈라인과 동일하게 응답은 오지만 생성이 종료 되지 않고 계속 반복

```bash
1. 수도의 위치: 서울특별시
2. 수도의 역할: 중앙행정기관의 소재지이며, 정치, 경제, 문화의 중심지
3. 수도의 특징: 고도화된 시설, 문화, 교육, 연구시설, 관광명소 등이 많음

4. 수도의 역할과 특징에 따라 다른 지역에 대해 설명해주세요: 다른 지역은 도시개발, 경제발전, 문화다양성 등에 대한 저항이나 불만이 있을 수 있지만, 수도는 정치, 경제, 문화의 중심지로 큰 역할을 해야 하므로, 사물의 개발, 연구, 교육에 대한 투자와 시설의 보유를 강조해야 합니다. ALSO:
1. 수도의 위치: 서울특별시
2. 수도의 역할: 중앙행정기관의 소재지이며, 정치, 경제, 문화의 중심지
3. 수도의 특징: 고도화된 시설, 문화, 교육, 연구시설, 관광명소 등이 많음
...

```

## FastAPI와 통합 - sh2orc/llama-3-korean-8b

`app.py`

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from vllm import LLM, SamplingParams
from transformers import AutoTokenizer
import uvicorn
from typing import List

app = FastAPI()

BASE_MODEL = "sh2orc/llama-3-korean-8b"

tokenizer = AutoTokenizer.from_pretrained(BASE_MODEL)
tokenizer.pad_token = tokenizer.eos_token
tokenizer.padding_side = 'right'

eos_token_id = [tokenizer.eos_token_id, tokenizer.convert_tokens_to_ids("<|eot_id|>")]

# Initialize the model
llm = LLM(model=BASE_MODEL)

class GenerationRequest(BaseModel):
    messages: List
    max_tokens: int = 1000
    temperature: float = 0.7
    top_p: float = 0.95

class GenerationResponse(BaseModel):
    generated_text: str

@app.post("/generate", response_model=GenerationResponse)
async def generate_text(request: GenerationRequest):
    try:
        prompt_message = tokenizer.apply_chat_template(
                request.messages, 
                tokenize=False, 
                add_generation_prompt=True,
        )

        sampling_params = SamplingParams(
            stop_token_ids=eos_token_id,
            temperature=request.temperature,
            top_p=request.top_p,
            max_tokens=request.max_tokens
        )
        outputs = llm.generate(prompt_message, sampling_params)
        return GenerationResponse(generated_text=outputs[0].outputs[0].text)
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

✔️ 서버 실행

```bash
python app.py
# python3 app.py
```

`api_client.py`

```python
import requests
import json

# The URL where your FastAPI server is running
API_URL = "http://localhost:8000/generate"

# The request payload
payload = {
    "messages":[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "대한민국의 수도는 어디인가요? 그리고 그 수도에 대해 설명해주세요."}, 
    ],
    "max_tokens": 1000,
    "temperature": 0.8,
    "top_p": 0.95
}

# Send a POST request to the API
response = requests.post(API_URL, json=payload)

# Check if the request was successful
if response.status_code == 200:
    # Parse the JSON response
    result = response.json()
    
    # Print the generated text
    print("Generated Text:")
    print(result['generated_text'])
else:
    print(f"Error: {response.status_code}")
    print(response.text)
```

✔️ API 호출

```bash
python api_client.py
# python3 api_client.py
```

✔️ 실행 결과

```bash
대한민국의 수도는 서울입니다. 서울은 대한민국의 역사적인 수도였으며, 한국의 경제, 문화, 정치, 교통의 중심지입니다. 서울은 아름다운 자연 경관과 다양한 문화유산을 보유하고 있으며, 많은 문화행사와 축제를 통해 대표되는 도시입니다. 또한, 현대적인 도시로 다양한 시설과 인프라가 잘 발달하고 있습니다.
```

---

해시태그: #vLLM #기본사용 #배치사용 #한국어사용 #채팅사용 #FastAPI통합 #API서버 #API호출
