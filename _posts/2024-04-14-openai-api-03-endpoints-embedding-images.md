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

출처: <https://platform.openai.com/docs/api-reference>

OpenAI의 공식 문서를 거의 그대로 옮긴 것입니다. Python 기준으로 간단하게 작성하였습니다.

OpenAI의 API를 사용하면 OpenAI에서 제공하는 [웹 사이트](https://chat.openai.com)가 아닌 다른 곳에서 OpenAI의 인공지능 기능을 사용할 수 있습니다. 개발하고자 하는 서비스에 OpenAI의 인공지능 기능을 추가 할 수 있습니다.

Endpoint는 OpenAI API의 URL을 의미합니다. 각 Endpoint는 OpenAI의 서비스 중 하나를 나타냅니다. 예를 들어, `https://api.openai.com/v1/chat/completions`는 채팅을 완성하는 데 사용되는 Endpoint입니다.

OpenAI API 연습 코드: <https://github.com/just-record/openai_api>

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

embedding = client.embeddings.create(
  model="text-embedding-ada-002", # Embedding 모델 이름
  input="나는 학교에 다니는 학생이다.",
  encoding_format="float"
)

embedding_vector = embedding.data[0].embedding

print(f'Embedding vector의 길이: {len(embedding_vector)}')
print(f'Embedding vector - 앞에서 10개 값: {embedding_vector[:10]}')
```

- `model`: 임베딩 모델(`text-embedding-ada-002`)의 이름입니다.
- `input`: 임베딩을 생성할 텍스트입니다.
- `encoding_format`: 임베딩 벡터의 형식입니다.

결과:

```text
Embedding vector의 길이: 1536
Embedding vector - 앞에서 10개 값: [-0.014749587, -0.019521875, -0.0037461228, -0.020424407, -0.022044016, 0.015862295, -0.025419237, 0.008067146, -0.008091873, 0.004073754]
```

사용 할 수 있는 임베딩 모델: <https://platform.openai.com/docs/guides/embeddings/embedding-models>

## 임베딩 벡터의 차원 확인

동일한 임베딩 모델은 동일한 벡터 차원을 생성 하는지 확인합니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI


model = "text-embedding-ada-002"
sentences = [
    '나는 학교에 다니는 학생이다.',
    '나는 학생을 가르치는 선생님다.',
    '오늘 날씨가 좋아서 나들이를 가고 싶다.'
]


client = OpenAI()


for i, sentence in enumerate(sentences):
    embedding = client.embeddings.create(
    model=model,
    input=sentence,
    encoding_format="float"
    )

    embedding_vector = embedding.data[0].embedding

    print(f'{i}번 문장의 Embedding vector의 길이: {len(embedding_vector)}')
    print(f'{i}번 문장의 Embedding vector - 앞에서 10개 값: {embedding_vector[:10]}')
```

결과: 3개의 다른 문장의 차원은 1,536개로 동일하였고 벡테 값은 모두 달랐습니다.

```text
0번 문장의 Embedding vector의 길이: 1536
0번 문장의 Embedding vector - 앞에서 10개 값: [-0.014749587, -0.019521875, -0.0037461228, -0.020424407, -0.022044016, 0.015862295, -0.025419237, 0.008067146, -0.008091873, 0.004073754]
1번 문장의 Embedding vector의 길이: 1536
1번 문장의 Embedding vector - 앞에서 10개 값: [-0.0046398193, -0.0037137263, 0.009298347, -0.02644198, 0.0038696341, 0.01648882, 0.0042562857, 0.009909506, -0.013807204, 0.011512239]
2번 문장의 Embedding vector의 길이: 1536
2번 문장의 Embedding vector - 앞에서 10개 값: [-0.009437798, -0.005597814, 0.011033466, -0.03842578, -0.010060498, 0.015411826, -0.0073621306, -0.0006109433, 0.0018324245, 0.0031621486]
```

### 문장(임베딩 벡터)의 유사도 확인

'코사인 유사도(Cosine Similarity)'를 사용하여 두 문장의 유사도를 계산합니다.

- 첫번째 문장: 나는 학교에 다니는 학생이다.
- 두번째 문장: 나는 학생을 가르치는 선생님다.
- 세번째 문장: 오늘 날씨가 좋아서 나들이를 가고 싶다.

첫번째 문장 : 두번째 문장 | 첫번째 문장 : 세번째 문장

```python
# pip install numpy
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI
import numpy as np # type: ignore
from numpy.linalg import norm # type: ignore


def cosine_similarity(a,b):
    return np.dot(a,b) / (norm(a)*norm(b))


model = "text-embedding-ada-002"
sentences = [
    '나는 학교에 다니는 학생이다.',
    '나는 학생을 가르치는 선생님다.',
    '오늘 날씨가 좋아서 나들이를 가고 싶다.'
]
vectors = []


client = OpenAI()


for i, sentence in enumerate(sentences):
    embedding = client.embeddings.create(
    model=model,
    input=sentence,
    encoding_format="float"
    )

    vectors.append(embedding.data[0].embedding)

### 첫 번째 문장을 두번째, 세번째 문장과 각각 코사인 유사도 계산 - 값이 작을 수록 가까운 거리
print(f'첫번째 문장과 두번째 문장의 거리: {1 - cosine_similarity(vectors[0], vectors[1])}')
print(f'첫번째 문장과 세번째 문장의 거리: {1 - cosine_similarity(vectors[0], vectors[2])}')
# '나는 학교에 다니는 학생이다.'와 '나는 학생을 가르치는 선생님다.'가 '오늘 날씨가 좋아서 나들이를 가고 싶다.'보다 더 가까운 거리에 있음을 확인할 수 있습니다.
```

결과:

```text
첫번째 문장과 두번째 문장의 거리: 0.07935289192352712
첫번째 문장과 세번째 문장의 거리: 0.1924438784161303
```

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

## Fine-tunning, Batch

어느 정도의 규모와 비용이 있어야 한다는 판단 등으로 정리를 생략하겠습니다.

## Files

### 파일 업로드 (Upload File)

파일(문서)를 업로드 합니다. (파인 튜닝 또는 Assistant, Message에서 사용)

API: <https://platform.openai.com/docs/api-reference/files/create>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

my_file = client.files.create(
  file=open("train.csv", "rb"),
  purpose="assistants" # 'fine-tune', 'assistants', 'batch', 'user_data', 'responses'
)

print(my_file.model_dump_json())
```

### 파일 목록 (List Files)

파일 목록을 조회합니다.

API: <https://platform.openai.com/docs/api-reference/files/list>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

files = client.files.list()
for file in files.data:
  print(file.model_dump_json())
```

### 파일 조회 (Retrieve File)

파일을 조회합니다. 파일 ID를 사용합니다.

API: <https://platform.openai.com/docs/api-reference/files/retrieve>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI
client = OpenAI()

file = client.files.retrieve("file-123abc")

print(file.model_dump_json())
```

### 파일 삭제 (Delete File)

파일을 삭제합니다.

API: <https://platform.openai.com/docs/api-reference/files/delete>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

file = client.files.delete("file-123abc")

print(file.model_dump_json())
```

### 파일 콘텐츠 검색 (Retrieve file content)

파일의 콘텐츠를 검색합니다.

API: <https://platform.openai.com/docs/api-reference/files/retrieve-contents>

`assistants` 파일은 콘텐츠를 검색할 수 없습니다. `fine-tune`용의 파일은 검색 되는걸 확인 했습니다.

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

content = client.files.content("file-123abc")
print(content.content.decode('utf-8')) # bytes to string
```

### 파일 객체 (The file Object)

API: <https://platform.openai.com/docs/api-reference/files/object>

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
import urllib.request

client = OpenAI()

images = client.images.generate(
  model="dall-e-3",
  prompt="A cute baby sea otter",
  n=1,
  size="1024x1024"
)

urllib.request.urlretrieve(images.data[0].url, "./baby_sea_otter.png")
print(images.data[0].url)
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
import urllib.request

client = OpenAI()

images = client.images.edit(
  image=open("otter.png", "rb"),
  mask=open("mask.png", "rb"),
  prompt="A cute baby sea otter wearing a beret",
  n=1,
  size="1024x1024"
)

urllib.request.urlretrieve(images.data[0].url, "./otter_edited.png")
print(images.data[0].url)
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
import urllib.request

client = OpenAI()

images = client.images.create_variation(
  image=open("otter.png", "rb"),
  n=2, # 생성할 이미지의 개수
  size="1024x1024"
)

for i in range(2):
    urllib.request.urlretrieve(images.data[i].url, f"./baby_sea_otter_{i}.png")
    print(images.data[i].url)
```

결과:

2개의 변형된 이미지를 생성합니다. 프롬프트를 주어 원하는 방향으로 변형 하지는 못합니다.

## Models

API를 사용하여 모델을 조회하고 삭제합니다.

### 모델 목록 (List Models)

API: <https://platform.openai.com/docs/api-reference/models/list>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

models = client.models.list().data

print(f'모델의 개수: {len(models)}')
for model in models:
    print(model.model_dump_json())
```

결과: 모델 목록을 출력합니다. (사용자가 파인 튜닝 하여 생성한 모델도 포함 되어 있습니다.)

```text
모델의 개수: 32
{"id":"gpt-3.5-turbo-16k-0613","created":1685474247,"object":"model","owned_by":"openai"}
{"id":"whisper-1","created":1677532384,"object":"model","owned_by":"openai-internal"}
...
```

### 모델 검색 (Retrieve Model)

모델 ID를 이용하여 모델을 검색합니다.

API: <https://platform.openai.com/docs/api-reference/models/retrieve>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

model = client.models.retrieve("gpt-3.5-turbo-16k-0613")

print(model.model_dump_json())
```

결과:

```text
{"id":"gpt-3.5-turbo-16k-0613","created":1685474247,"object":"model","owned_by":"openai"}
```

### 파인 튜닝 된 모델 삭제 (Delete a fine-tuned model)

파인 튜닝 된 모델을 삭제합니다.

API: <https://platform.openai.com/docs/api-reference/models/delete>

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

model = client.models.delete("davinci:ft-personal-2023-xx-xx-xx-xx-xx")

print(model.model_dump_json())
```

결과:

```text
{"id":"davinci:ft-personal-2023-xx-xx-xx-xx-xx","object":"model","deleted":true}
```

### 모델 객체

<https://platform.openai.com/docs/api-reference/models/object>

## Moderation

Moderations를 생성 하여 텍스트가 잠재적으로 유해한지 확인합니다.

가이드: <https://platform.openai.com/docs/guides/moderation/moderation>  
API: <https://platform.openai.com/docs/api-reference/moderations>

### 모더레이션 생성 (Create Moderation)

```python
from dotenv import load_dotenv # type: ignore
load_dotenv()
from openai import OpenAI

client = OpenAI()

moderation = client.moderations.create(input="I want to kill them.")
result = moderation.results[0]

print(result.flagged)
print('-----------')
for key, value in result.categories:
    print(f'{key}:  {value}')
print('-----------')
for key, value in result.category_scores:
    print(f'{key}:  {value}')
```

결과:

```plaintext
True
-----------
harassment:  True
harassment_threatening:  True
hate:  False
hate_threatening:  False
self_harm:  False
self_harm_instructions:  False
self_harm_intent:  False
sexual:  False
sexual_minors:  False
violence:  True
violence_graphic:  False
self-harm:  False
sexual/minors:  False
hate/threatening:  False
violence/graphic:  False
self-harm/intent:  False
self-harm/instructions:  False
harassment/threatening:  True
-----------
harassment:  0.5278584957122803
harassment_threatening:  0.5712487697601318
hate:  0.2324090600013733
hate_threatening:  0.024183575063943863
self_harm:  2.3696395601291442e-06
self_harm_instructions:  1.132860139030356e-09
self_harm_intent:  1.7161115692942985e-06
sexual:  1.205232911161147e-05
sexual_minors:  7.506431387582779e-08
violence:  0.997192919254303
violence_graphic:  3.399916022317484e-05
self-harm:  2.3696395601291442e-06
sexual/minors:  7.506431387582779e-08
hate/threatening:  0.024183575063943863
violence/graphic:  3.399916022317484e-05
self-harm/intent:  1.7161115692942985e-06
self-harm/instructions:  1.132860139030356e-09
harassment/threatening:  0.5712487697601318
```

### Moderation 객체 (The moderation Object)

```json
{
    "id": "modr-XXXXX",
    "model": "text-moderation-007",
    "results": [
        {
            "flagged": true,
            "categories": {
                "sexual": false,
                "hate": false,
                "harassment": false,
                "self-harm": false,
                "sexual/minors": false,
                "hate/threatening": false,
                "violence/graphic": false,
                "self-harm/intent": false,
                "self-harm/instructions": false,
                "harassment/threatening": true,
                "violence": true
            },
            "category_scores": {
                "sexual": 1.2282071e-6,
                "hate": 0.010696256,
                "harassment": 0.29842457,
                "self-harm": 1.5236925e-8,
                "sexual/minors": 5.7246268e-8,
                "hate/threatening": 0.0060676364,
                "violence/graphic": 4.435014e-6,
                "self-harm/intent": 8.098441e-10,
                "self-harm/instructions": 2.8498655e-11,
                "harassment/threatening": 0.63055265,
                "violence": 0.99011886
            }
        }
    ]
}
```

---

해시태그: #OpenAI #API #Python #endpoint #embedding #text-embedding-ada-002 #images #Dalle #Models #Moderations
