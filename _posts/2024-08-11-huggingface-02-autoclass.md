---
layout: single
title: "Hugging Face - 02. Transformers - AutoClass" 
categories: Hugging-Face
tag: [Hugging-Face, Transformers, AutoClass]
toc: true
toc_sticky: true
toc_label: "목차"
---

## Transformer

공식문서: <https://huggingface.co/docs/transformers/index>

위의 공식 문서를 연습 한 곳: <https://github.com/just-record/huggingface_practice>

🤗 Transformers는 최신의 사전 훈련된 모델을 쉽게 다운로드하고 훈련할 수 있는 API와 도구를 제공합니다.

### 설치

```bash
pip install transformers, datasets, evaluate, accelerate
pip install torch       # PyTorch
pip install tensorflow  # TensorFlow
```

## AutoClass

<https://huggingface.co/docs/transformers/autoclass_tutorial>

AutoClass는 주어진 체크포인트에서 올바른 아키텍처를 자동으로 추론하고 로드합니다. from_pretrained() 메소드를 사용하면 어떤 아키텍처의 사전 훈련된 모델도 빠르게 로드할 수 있어, 모델을 처음부터 훈련하는 데 시간과 리소스를 들일 필요가 없습니다. 이러한 체크포인트에 구애받지 않는 코드를 작성하면, 한 체크포인트에서 작동하는 코드는 아키텍처가 다르더라도 유사한 작업을 위해 훈련된 다른 체크포인트에서도 작동할 것입니다.

- Architecture (아키텍처): 아키텍처는 모델의 구조나 "뼈대"를 의미
  - 예를 들어, BERT, GPT, ResNet 등이 각각 다른 아키텍처입니다.
- Checkpoint (체크포인트): 특정 아키텍처에 대해 학습된 가중치(weights)의 집합
  - 예를 들어, 'google-bert/bert-base-uncased'는 BERT 아키텍처의 한 체크포인트
- Model (모델): 아키텍처나 체크포인트를 모두 지칭할 수 있어 문맥에 따라 모델이 아키텍처를 의미할 수도 있고, 특정 체크포인트를 의미할 수도 있음
  - 예를 들어, "BERT 모델"이라고 할 때는 BERT 아키텍처를 의미할 수 있지만, "사전 훈련된 BERT 모델을 로드했다"라고 하면 특정 체크포인트를 의미할 가능성이 높습니다.

## AutoTokenizer

거의 모든 NLP 작업은 토크나이저로 시작합니다. 토크나이저는 입력을 모델이 처리할 수 있는 형식으로 변환합니다.

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("google-bert/bert-base-uncased")
# AutoTokenizer에 의해 선택된 토크나이저 조회
print(tokenizer)

print('-'*30)
sequence = "In a hole in the ground there lived a hobbit."
# sequence를 자동으로 선택 된 tokenizer에 의해 토큰화
print(tokenizer(sequence))
```

```python
# 결과
BertTokenizerFast(name_or_path='google-bert/bert-base-uncased', vocab_size=30522, model_max_length=512, is_fast=True, padding_side='right', truncation_side='right', special_tokens={'unk_token': '[UNK]', 'sep_token': '[SEP]', 'pad_token': '[PAD]', 'cls_token': '[CLS]', 'mask_token': '[MASK]'}, clean_up_tokenization_spaces=True),  added_tokens_decoder={
        0: AddedToken("[PAD]", rstrip=False, lstrip=False, single_word=False, normalized=False, special=True),
        100: AddedToken("[UNK]", rstrip=False, lstrip=False, single_word=False, normalized=False, special=True),
        101: AddedToken("[CLS]", rstrip=False, lstrip=False, single_word=False, normalized=False, special=True),
        102: AddedToken("[SEP]", rstrip=False, lstrip=False, single_word=False, normalized=False, special=True),
        103: AddedToken("[MASK]", rstrip=False, lstrip=False, single_word=False, normalized=False, special=True),
}
------------------------------
{
'input_ids': [101, 1999, 1037, 4920, 1999, 1996, 2598, 2045, 2973, 1037, 7570, 10322, 4183, 1012, 102], 
'token_type_ids': [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], 
'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
}
```

## AutoImageProcessor

비전 작업의 경우, 이미지 프로세서는 이미지를 올바른 입력 형식으로 처리합니다.

```python
from transformers import AutoImageProcessor

image_processor = AutoImageProcessor.from_pretrained("google/vit-base-patch16-224")

print(image_processor)

print('-'*30)
import requests
from PIL import Image
import io

image_url="https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/pipeline-cat-chonk.jpeg"
response = requests.get(image_url)
image = Image.open(io.BytesIO(response.content))

# 이미지 전처리
inputs = image_processor(images=image, return_tensors="pt")

print("전처리된 이미지 텐서 shape:", inputs.pixel_values.shape)
print("입력 이미지의 타입:", type(inputs))

# 전처리된 이미지의 주요 속성 출력
for key, value in inputs.items():
    print(f"{key}: {type(value)} - shape: {value.shape}")
```

```python
# 결과
Fast image processor class <class 'transformers.models.vit.image_processing_vit_fast.ViTImageProcessorFast'> is available for this model. Using slow image processor class. To use the fast image processor class set `use_fast=True`.
ViTImageProcessor {
  "do_normalize": true,
  "do_rescale": true,
  "do_resize": true,
  "image_mean": [
    0.5,
    0.5,
    0.5
  ],
  "image_processor_type": "ViTImageProcessor",
  "image_std": [
    0.5,
    0.5,
    0.5
  ],
  "resample": 2,
  "rescale_factor": 0.00392156862745098,
  "size": {
    "height": 224,
    "width": 224
  }
}
------------------------------
전처리된 이미지 텐서 shape: torch.Size([1, 3, 224, 224])
입력 이미지의 타입: <class 'transformers.image_processing_base.BatchFeature'>
pixel_values: <class 'torch.Tensor'> - shape: torch.Size([1, 3, 224, 224])
```

## AutoBackbone

AutoBackbone은 사전 훈련된 모델을 백본으로 사용하여 백본의 다양한 단계에서 특징 맵을 얻을 수 있게 해줍니다.

```python
# pip install Pillow
from transformers import AutoImageProcessor, AutoBackbone
import torch
from PIL import Image
import requests


url = "http://images.cocodataset.org/val2017/000000039769.jpg"
image = Image.open(requests.get(url, stream=True).raw)
processor = AutoImageProcessor.from_pretrained("microsoft/swin-tiny-patch4-window7-224")
model = AutoBackbone.from_pretrained("microsoft/swin-tiny-patch4-window7-224", out_indices=(1,))

inputs = processor(image, return_tensors="pt")
outputs = model(**inputs)
feature_maps = outputs.feature_maps

print(feature_maps[0].shape)
```

```python
# 결과
torch.Size([1, 96, 56, 56])
# 첫 번째 차원 (1): 배치 크기
# 두 번째 차원 (96): 채널 수 또는 특성 맵(feature map)의 수
# 세 번째 차원 (56): 높이
# 네 번째 차원 (56): 너비
```

- 'out_indices': 특징 맵을 얻고자 하는 레이어의 인덱스
- 'out_features': 특징 맵을 얻고자 하는 레이어의 이름

## AutoFeatureExtractor

오디오 작업의 경우, 특성 추출기가 오디오 신호를 올바른 입력 형식으로 처리합니다.

```python
from transformers import AutoFeatureExtractor
import librosa
import requests

feature_extractor = AutoFeatureExtractor.from_pretrained("ehcalabres/wav2vec2-lg-xlsr-en-speech-emotion-recognition")

# 오디오 파일 URL
audio_url = "https://huggingface.co/datasets/Narsil/asr_dummy/resolve/main/mlk.flac"

# 오디오 파일 다운로드 및 로드
audio_file = requests.get(audio_url).content
with open("temp_audio.flac", "wb") as f:
    f.write(audio_file)

# librosa를 사용하여 오디오 로드
audio, sample_rate = librosa.load("temp_audio.flac", sr=16000)

# 올바른 입력 형식으로 변환
inputs = feature_extractor(audio, sampling_rate=sample_rate, return_tensors="pt", padding=True)
print(f'inputs: {inputs}')
```

```python
# 결과
inputs: {'input_values': tensor([[ 0.0717,  0.0463, -0.0508,  ..., -0.0046,  0.0326, -0.1615]]), 'attention_mask': tensor([[1, 1, 1,  ..., 1, 1, 1]], dtype=torch.int32)}
```

## AutoModel

AutoModelFor 클래스를 사용하면 주어진 작업에 대해 사전 훈련된 모델을 로드할 수 있습니다

`Pytorch` 예시

```python
from transformers import AutoModelForSequenceClassification

# 시퀀스 분류를 위한 모델 로드
model = AutoModelForSequenceClassification.from_pretrained("distilbert/distilbert-base-uncased")

##########################################
### 예제에 이어서 코드 계속 작성
##########################################
from transformers import AutoTokenizer

# 토크나이저 로드
tokenizer = AutoTokenizer.from_pretrained("distilbert/distilbert-base-uncased")

# 입력 텍스트
text = "This restaurant is awesome"

# 텍스트를 토큰화
inputs = tokenizer(text, return_tensors="pt")
print(f'inputs: {inputs}')

# 모델에 입력
outputs = model(**inputs)
print(f'outputs: {outputs}')
print(f'outputs logits: {outputs.logits[0]}')
```

```python
# 결과
inputs: {'input_ids': tensor([[  101,  2023,  4825,  2003, 12476,   102]]), 'attention_mask': tensor([[1, 1, 1, 1, 1, 1]])}
# - input_ids: 토큰의 수치적 표현입니다.
# - attention_mask: 어떤 토큰에 주의를 기울여야 하는지를 나타냅니다.
outputs: SequenceClassifierOutput(loss=None, logits=tensor([[-0.0002, -0.0701]], grad_fn=<AddmmBackward0>), hidden_states=None, attentions=None)
outputs logits: tensor([-0.0002, -0.0701], grad_fn=<SelectBackward0>)
```

- distilbert/distilbert-base-uncased
  - DistilBERT는 BERT(Bidirectional Encoder Representations from Transformers)의 경량화 버전입니다. 원래 BERT 모델의 성능을 거의 유지하면서도 크기와 속도 면에서 개선된 모델입니다.
  - "base": 기본 크기의 모델을 의미합니다 (대형 모델도 있습니다).
  - "uncased": 텍스트를 소문자로 변환하여 대소문자를 구분하지 않는 모델임을 나타냅니다.

## 추가 예시 - Quick Tour

> AutoTokenizer

```python
from transformers import AutoTokenizer

model_name = "nlptown/bert-base-multilingual-uncased-sentiment"
tokenizer = AutoTokenizer.from_pretrained(model_name)

encoding = tokenizer("We are very happy to show you the 🤗 Transformers library.")
print(f'enconding: {encoding}')

##########################################
### 입력 리스트 가능, padding, truncation로 균일한 길이의 배치를 반환
##########################################
pt_batch = tokenizer(
    ["We are very happy to show you the 🤗 Transformers library.", "We hope you don't hate it."],
    padding=True,
    truncation=True,
    max_length=512,
    return_tensors="pt",
)

print(f'pt_batch: {pt_batch}')
```

```python
# 결과
enconding: {
'input_ids': [101, 11312, 10320, 12495, 19308, 10114, 11391, 10855, 10103, 100, 58263, 13299, 119, 102], 
'token_type_ids': [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], 
'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
}
pt_batch: {
'input_ids': tensor([
                      [  101, 11312, 10320, 12495, 19308, 10114, 11391, 10855, 10103,   100, 58263, 13299,   119,   102],
                      [  101, 11312, 18763, 10855, 11530,   112,   162, 39487, 10197,   119,    102,     0,     0,     0]
                    ]), 
'token_type_ids': tensor([
                      [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
                      [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
                    ]), 
'attention_mask': tensor([
                      [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
                      [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0]
                     ])
}
```

- nlptown/bert-base-multilingual-uncased-sentiment
  - nlptown: 이 모델을 개발하고 공개한 조직 또는 사용자의 이름
  - bert: 이 모델의 기본 구조가 BERT 아키텍처를 사용
  - base: BERT의 기본 크기 버전 -'small'보다 크고 'large'보다 작은 모델
  - multilingual: 다국어 지원
  - uncased: 대소문자 구분 없음
  - sentiment: 감성 분석 - 긍정/부정

> AutoModel

```python
from transformers import AutoModelForSequenceClassification, AutoTokenizer

model_name = "nlptown/bert-base-multilingual-uncased-sentiment"

pt_model = AutoModelForSequenceClassification.from_pretrained(model_name)
pt_tokenizer = AutoTokenizer.from_pretrained(model_name)

pt_batch = pt_tokenizer(
    ["We are very happy to show you the 🤗 Transformers library.", "We hope you don't hate it."],
    padding=True,
    truncation=True,
    max_length=512,
    return_tensors="pt",
)

pt_outputs = pt_model(**pt_batch)

from torch import nn

pt_predictions = nn.functional.softmax(pt_outputs.logits, dim=-1)
print(f'pt_predictions: {pt_predictions}')
```

```python
# 결과
pt_predictions: 
tensor([[0.0021, 0.0018, 0.0115, 0.2121, 0.7725],
        [0.2084, 0.1826, 0.1969, 0.1755, 0.2365]], grad_fn=<SoftmaxBackward0>)
# 1점(매우 부정), 2점(부정), 3점(중립), 4점(긍정), 5점(매우 긍정)으로 레이블링된 감성 점수
```

---

해시태그: #Hugging-Face #Transformers #AutoClass #AutoTokenizer #AutoImageProcessor #AutoBackbone #AutoFeatureExtractor #AutoModel
