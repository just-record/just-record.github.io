---
layout: single
title: "LLM - NVIDIA GPU 종류"
categories: GPU
tag: [OpenAI, NVIDIA, GPU]
toc: true
toc_sticky: true
toc_label: "목차"
---

✔️인공지능 학습과 추론에 NVIDIA GPU를 많이 사용 하는 이유

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
| **FP32 성능** | 35.6 TFLOPS | 82.6 TFLOPS | 27.8 TFLOPS | 38.7 TFLOPS | 19.5 TFLOPS | 51 TFLOPS |
| **데스크탑 설치** | 가능 | 가능 | 가능 | 가능 | 불가능(서버용) | 불가능(서버용) |
| **멀티 GPU** | NVLink 지원 | 지원안함 | NVLink 지원 | NVLink 지원 | NVLink 지원 | NVLink 지원 |
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

<https://pubs.lenovo.com/st650-v3/ko/install_the_nvlink>

---

해시태그: #LLM #NVIDIA #GPU #RTX3090 #RTX4090 #RTXA5000 #RTXA6000 #A100 #H100
