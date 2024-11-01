---
layout: single
title: "LLM - NVIDIA GPU 종류"
categories: GPU
tag: [OpenAI, NVIDIA, GPU]
toc: true
toc_sticky: true
toc_label: "목차"
---

✔️ 인공지능 학습과 추론에 NVIDIA GPU를 많이 사용 하는 이유

- NVIDIA가 병렬 컴퓨팅 플랫폼인 CUDA를 개발 - AI 가속에 대한 조기 투자
- 대부분의 AI/ML 프레임워크(TensorFlow, PyTorch)가 CUDA에 최적화됨 - 소프트웨어 생태계
- 독적에 가까운 시장 점유율

## NVIDIA의 대표적인 GPU

- RTX 3090
- RTX 4090
- RTX A5000
- RTX A6000
- A100
- H100

## 특징 비교

하드웨어 사양 보다 AI 학습에 관련된 특징을 중심으로 비교

| 특징 | RTX 3090 | RTX 4090 | RTX A5000 | RTX A6000 | A100 | H100 |
|------|----------|----------|------------|------------|------|------|
| **메모리** | 24GB GDDR6X | 24GB GDDR6X | 24GB GDDR6 | 48GB GDDR6 | 40GB HBM2 / 80GB HBM2e | 80GB HBM3 |
| **연산(FP32)** | 35.6 TFLOPS | 82.6 TFLOPS | 27.8 TFLOPS | 38.7 TFLOPS | 19.5 TFLOPS | 51 TFLOPS |
| **데스크탑** | 설치 가능 | 설치 가능 | 설치 가능 | 설치 가능 | 설치 불가 | 설치 불가 |
| **멀티 GPU** | NVLink | 지원안함 | NVLink | NVLink | NVLink | NVLink |
| **주요 용도** | 게이밍/렌더링 | 게이밍/렌더링 | 전문가용 워크스테이션 | 전문가용 워크스테이션 | AI 학습/추론 | AI 학습/추론 |
| **가격(2024.11.01-쿠팡)** | 2,362,910원 | 3,199,000원 | 3,230,000원 | 6,290,000원 | 13,600,000원 ~ 29,750,000원 | 42,850,000원 |
| **단종 여부** | 단종 | - | - | - | 단종 | - |

✔️ 추가 참고사항:

1. **데스크탑 설치 관련**
   - RTX 시리즈: 일반 PCIe 슬롯 사용 가능
   - A5000/A6000: 워크스테이션용이나 PCIe 설치 가능
   - A100/H100: 특수 서버 폼팩터(SXM) 필요

2. **멀티 GPU 연결**
   - 4090은 단일 GPU 사용 권장 (멀티 GPU 미지원)
   - A5000/A6000은 전문 작업용 멀티 GPU 구성 가능
   - A100/H100은 대규모 AI 클러스터 구성 가능

## NVLink

GPU를 연결하는 하드웨어 - 병렬 처리를 위한 고속 연결

- <https://blogs.nvidia.co.kr/blog/what-is-nvidia-nvlink/>
- <https://m.blog.naver.com/infracube/223463625493>

✔️ 설치

- <https://pubs.lenovo.com/st650-v3/ko/install_the_nvlink>

## 연산 속도

✔️ TFLOPS(테라 플롭스)란?

- T = Terra(1조)
- FLOPS = 초당 계산 횟수
- 1 TFLOPS = 1초에 1조 번의 계산을 할 수 있다는 뜻
  - 1명의 사람이 1초에 1개의 계산을 한다고 가정
  - 1 TFLOPS는 1조 명의 사람이 동시에 계산하는 것
  - RTX 3090: 35.6 TFLOPS - 1초에 35조 6천억 번 계산 가능

✔️ FP32(Floating Point 32-bit)란?

- FP = Floating Point (부동소수점)
- 소수점이 있는 숫자를 다루는 방식
- FP32는 32비트로 소수점을 다루는 방식

✔️ FP32와 FP16 비교

| 구분 | FP32 | FP16 |
|------|-------|-------|
| 표현 가능한 숫자 범위 | 매우 크고 정밀함 | 상대적으로 작고 덜 정밀함 |
| 메모리 사용 | 2배 더 많음 | 절반만 사용 |
| 계산 속도 | 더 느림 | 더 빠름 |
| 비유(자로 길이를 잴 때) | 밀리미터(mm)까지 정확하게 재는 것 | 센티미터(cm)까지만 재는 것 |

각각의 장단점

- FP32:
  - 장점: 매우 정확한 계산 가능
  - 단점: 더 많은 메모리와 시간 필요
  - 사용: 정확한 계산이 필요할 때

- FP16:
  - 장점: 빠른 계산과 적은 메모리 사용
  - 단점: 정확도가 조금 떨어짐
  - 사용: 약간의 오차가 허용되는 AI 작업

- RTX 4090의 경우
  - FP32: 82.6 TFLOPS (1초에 82조 6천억 번 계산)
  - FP16: 165.2 TFLOPS (1초에 165조 2천억 번 계산)

---

해시태그: #LLM #NVIDIA #GPU #RTX 3090 #RTX 4090 #RTX A5000 #RTX A6000 #A100 #H100 #NVLink #TFLOPS #FP32 #FP16
