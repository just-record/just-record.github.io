---
layout: single
title: "OpenAI API - 03. Endpoint (Embedding, Images)" 
categories: OpenAI-API
tag: [OpenAI-API, Python]
toc: true
toc_sticky: true
toc_label: "목차"
---

수정 일자: 2024-04-10

출처: <https://platform.openai.com/docs/api-reference/audio>

OpenAI의 공식 문서를 정리(그냥 제가 이해 할 수 있는 것만)한 것입니다. Python 기준으로 간단하게 작성하였습니다.

OpenAI의 API를 사용하면 OpenAI에서 제공하는 [웹 사이트](https://chat.openai.com)가 아닌 다른 곳에서 OpenAI의 인공지능 기능을 사용할 수 있습니다. 개발하고자 하는 서비스에 OpenAI의 인공지능 기능을 추가 할 수 있습니다.

Endpoint는 OpenAI API의 URL을 의미합니다. 각 Endpoint는 OpenAI의 서비스 중 하나를 나타냅니다. 예를 들어, `https://api.openai.com/v1/chat/completions`는 채팅을 완성하는 데 사용되는 Endpoint입니다.

## Embedding

텍스트를 숫자로 변환하는 API입니다. 임베딩은 부동 소숫점 숫자로 이루어진 벡터입니다. 임베딩은 텍스트(단어, 문장)의 의미 있는 표현을 밀집 벡터 공간에 매핑하는 것을 의미합니다.

의미적으로 유사한 단어들은 벡터 공간에서 가까이 위치하게 됩니다. 예를 들어, "king"과 "queen"은 비슷한 의미를 가지고 있기 때문에 벡터 공간에서 가까이 위치하게 됩니다.

텍스트의 관련성을 측정하는 임베딩은 다음 용도로 사용 됩니다.

- 검색: 사용자가 입력한 검색어와 가장 관련성이 높은 문서를 찾습니다.
- 클러스터링: 유사성을 기준으로 그룹을 만듭니다.
- 추천: 사용자에게 관련성이 높은 항목을 추천합니다.
- 이상 탐지: 관련성이 거의 없는 항목을 찾습니다.
- 다양성 측정: 유사성 분포를 분석합니다.
- 분류: 텍스트를 범주로 분류합니다.

가이드: <https://platform.openai.com/docs/guides/embeddings/embeddings>

### 임베딩 생성 (Create Embedding)

입력 텍스트를 임베딩 벡터로 변환합니다.

API: <https://platform.openai.com/docs/api-reference/embeddings/create>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

response = client.embeddings.create(
  model="text-embedding-ada-002",
  input="The food was delicious and the waiter...",
  encoding_format="float"
)

print(len(response.data))
print(len(response.data[0].embedding))
print(response.data[0].embedding[:10]))
```

- `model`: 임베딩 모델(`text-embedding-ada-002`)의 이름입니다.
- `input`: 임베딩을 생성할 텍스트입니다.
- `encoding_format`: 임베딩 벡터의 형식입니다.

결과:

```python
1
1536
[0.0022786001, -0.009292118, 0.015702665, -0.007711697, -0.00470953, 0.014915627, -0.009857008, -0.038209394, -0.0069310064, -0.028587228]
```

사용 할 수 있는 임베딩 모델: <https://platform.openai.com/docs/guides/embeddings/embedding-models>

### 임베딩 객체 (The embedding Object)

임베딩 엔드포인트에서 반환된 임베딩 객체

상세: <https://platform.openai.com/docs/api-reference/embeddings/object>

```python
{
  "object": "embedding",
  "embedding": [
    0.0023064255,
    -0.009327292,
    .... (1536 floats total for ada-002)
    -0.0028842222,
  ],
  "index": 0
}
```

- `object`: 객체의 유형입니다. 항상 "embedding"입니다.
- `embedding`: 임베딩 벡터입니다. 임베딩 모델에 따라 길이가 다를 수 있습니다.
- `index`: 입력 텍스트의 인덱스입니다.

## Fine-tunning, Batch, Files

어느 정도의 규모와 비용이 있어야 한다는 판단 등으로 정리를 생략하겠습니다.

## Images

프롬프트 및/또는 입력 이미지가 주어지면 새로운 이미지를 생성합니다.

가이드: <https://platform.openai.com/docs/guides/images/image-generation>

### 이미지 생성 (Create Image)

가이드: <https://platform.openai.com/docs/guides/images/generations>  
API: <https://platform.openai.com/docs/api-reference/images/create>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

response = client.images.generate(
  model="dall-e-3",
  prompt="A cute baby sea otter",
  n=1,
  size="1024x1024"
)

print(response.data[0].revised_prompt)
print('-------------')
print(response.data[0].url)
```

- `model`: 이미지 생성 모델(`dall-e-3`)의 이름입니다.
- `prompt`: 이미지를 생성할 때 사용되는 텍스트입니다.
- `n`: 생성할 이미지의 수입니다.
- `size`: 생성할 이미지의 크기입니다.

결과:

```plaintext
A sweet image of a baby sea otter, playing in the ocean waters. ...
-------------
https://oaidalleapiprodscus.blob.core.windows.net/private/org-GdevoA5kUDOY7DiM...
```

### 이미지 편집 생성 (Create image edit)

원본 이미지와 프롬프트가 주어지면 편집되거나 확장된 이미지를 생성합니다.

가이드: <https://platform.openai.com/docs/guides/images/edits-dall-e-2-only>  
API: <https://platform.openai.com/docs/api-reference/images/createEdit>

2개의 이미지가 필요 합니다.

- otter.png: 4MB 미만의 유효한 정사각형의 PNG 파일 - 조금 전 생성한 수달 이미지를 사용 했습니다.
- mask.png: otter.png와 동일한 사이즈이면서 투명한 영역이 있는 4MB 미만의 유효한 PNG 파일 - 조금 전 생성한 수달 이미지에서 머리 부분을 투명하게 만들었습니다.

`otter.png`:
![otter.png 다운로드]({{site.url}}/images/openai-api/openai-api-03_01.png)
`mask.png`:
![mask.png 다운로드]({{site.url}}/images/openai-api/openai-api-03_02.png)

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

response = client.images.edit(
  image=open("otter.png", "rb"),
  mask=open("mask.png", "rb"),
  prompt="A cute baby sea otter wearing a beret",
  n=2,
  size="1024x1024"
)

print(response.data[0].url)
print(response.data[1].url)
```

결과:

![편집되어서 생성된 이미지]({{site.url}}/images/openai-api/openai-api-03_03.png)

### 이미지 변형 생성 (Create image variation)

원본 이미지가 주어지면 변형된 이미지를 생성합니다. 프롬프트는 없습니다.

가이드: <https://platform.openai.com/docs/guides/images/variations-dall-e-2-only>  
API: <https://platform.openai.com/docs/api-reference/images/createVariation>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

response = client.images.create_variation(
  image=open("otter.png", "rb"),
  n=2,
  size="1024x1024",
  prompt="A cute baby sea otter wearing a beret",
)


print(response.data[0].url)
print(response.data[1].url)
```

결과:

변형된 이미지를 생성합니다. 프롬프트를 주어 원하는 방향으로 변형 하지는 못합니다.

---

해시태그: #OpenAI #API #Python #endpoint #embedding #text-embedding-ada-002 #images #Dalle-3 #Dalle-2 #create-image #create-image-edit
