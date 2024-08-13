---
layout: single
title: "Ollama - 01. 설치 및 기본 사용" 
categories: Ollama
tag: [Ollama]
toc: true
toc_sticky: true
toc_label: "목차"
---

## Ollama란?

<https://ollama.com/>

ollama는 대규모 언어 모델(Large Language Models, LLMs)을 로컬 환경에서 쉽게 실행할 수 있게 해주는 오픈소스 프레임워크입니다. 이 도구는 복잡한 AI 모델을 개인용 컴퓨터나 서버에서 간편하게 구동할 수 있도록 설계되었습니다.

✔️ 주요 목적

- LLMs의 접근성 향상
- 로컬 환경에서의 AI 모델 실행 간소화
- 다양한 AI 모델의 쉬운 관리 및 사용

✔️ ollama의 특징과 장점

- 로컬 실행
  - 인터넷 연결 없이 모델 사용 가능
  - 데이터 프라이버시 및 보안 강화
  - 지연 시간 감소로 빠른 응답 속도

- 사용 편의성
  - 간단한 명령어로 모델 다운로드 및 실행
  - 복잡한 설정 없이 바로 사용 가능
  - 사용자 친화적인 CLI 인터페이스

- 다양한 모델 지원:
  - Llama 3.1, Phi 3, Mistral, Gemma 2 등 다양한 오픈소스 모델 지원
  - 커스텀 모델 추가 기능

- 리소스 효율성:
  - 최적화된 실행으로 하드웨어 자원 효율적 사용
  - 다양한 하드웨어 사양에 맞춤 실행 가능

- API 지원:
  - RESTful API를 통한 프로그래밍 언어 연동
  - 다양한 애플리케이션 개발 가능

- 커뮤니티 및 오픈소스:
  - 활발한 개발자 커뮤니티
  - 지속적인 업데이트 및 개선

- 경량화 및 양자화:
  - 모델 크기 축소 및 성능 최적화 기능
  - 제한된 리소스에서도 고성능 모델 실행 가능

- 멀티 모델 관리:
  - 여러 모델을 쉽게 전환하며 사용 가능
  - 다양한 작업에 적합한 모델 선택 용이

## 설치

<https://ollama.com/download>

✔️ Linux (Ubuntu 22.04 LTS)

```bash
# Update package list and upgrade existing packages
sudo apt update && sudo apt upgrade -y

# Install required dependencies
sudo apt install curl -y

# Download Ollama install script
curl -fsSL https://ollama.com/install.sh | sh

# Verify installation
ollama --version
# ollama version is 0.3.5
```

## 서비스 시작 및 모델 다운로드, 실행

✔️ 서비스 시작

```bash
ollama serve
```

위의 명령어로 설치만 했는데 이미 서비스가 실행 중에 있었다. 음~ 이전에 내가 설치를 했었나?

```bash
ollama serve
# Error: listen tcp 127.0.0.1:11434: bind: address already in use

sudo ss -lptn 'sport = :11434'
# ... 127.0.0.1:11434 ...  users:(("ollama",pid= ...

# reboot 을 해도 자동으로 실행되는 것 같다.
```

✔️ 모델 다운로드

```bash
ollama pull llama3.1
```

모델 목록 조회

- <https://ollama.com/library>에서 원하는 모델을 검색 후 선택
- 좌측 'select box'에서 원하는 모델 사이즈 선택 -> 우측에 실행 스크립트 확인

예시: llama3.1 모델 다운로드

- <https://ollama.com/library/llama3.1>
  - '8b' -> 'ollama run llama3.1'
  - '70b' -> 'ollama run llama3.1:70b'
  - '405b' -> 'ollama run llama3.1:405b'

모델을 다운로드 하지 않고 바로 실행 하면 다운로드 후에 실행 된다.

✔️ 모델 실행

```bash
ollama run llama3.1
# ollama run gemma2
```

모델을 실행 하면 `>>>` 프롬프트가 나오고 입력을 받을 수 있다. 참고로 **대화형 모드**입니다.

```plaintext
>>> hi
How's it going? Is there something I can help you with or would you like to chat?

>>> 내 이름은 홍길동이야.
Nice to meet you, 홍길동님!

홍길동은 한국의 전설적인 청춘인 홍길동이라는 자이브에서 유래한 이름입니다. 그럼, 홍길동님이 궁금 한 것은 무엇인가요?

>>> 내 이름이 뭐야?
홍길동님이 자기 이름을 물으셨다구요?

 내 이름은 나로군요! (I'm just a computer program, I don't have a real name)
```

✔️ 모델 실행 종료
  
```bash
>>> /bye
# 또는
# Ctrl + d
```

## 명령어

### 기본 명령어

```bash
# 도움말
ollama --help
# 버전 확인
ollama --version
# 사용 가능한 모델 목록 조회
ollama list
# 모델 정보 확인
ollama show [모델명] # llama3.1
# 모델 삭제
ollama rm [모델명] # llama3.1
# 모델 다운로드
ollama pull [모델명] # llama3.1
# 모델 실행
ollama run [모델명] # llama3.1
# 단일 쿼리 실행
ollama run [모델명] [쿼리]
### ollama run llama3.1 "Tell me about Ollama"
```

### 모델 실행 중 명령어

```bash
# 도움말
/help
# 종료
/bye
# 대화 내용 초기화
/clear
# 모델 로드
/load [모델명] # llama3.1
# 현재 세션 저장
/save [모델명]
# 모델 상세 정보 확인
/show info
# 모델 라이선스 확인
/show license
# 모델의 Modelfile 확인
/show modelfile
# 모델의 Parameters 확인
/show parameters
# 모델의 system 메시지 확인
/show system
# 모델의 Prompt Template 확인
/show template
```

- `/load` 예시
  - llama3.1 모델을 사용 하다가
  - `/load gemma2`를 실행 하면 gemma2 모델로 변경된다.
  - `/show info`를 실행 하면 gemma2 모델 정보가 나온다.
- `/save` 예시
  - gemma2 모델을 사용 하여 대화를 하다가
  - `/save gemma2_chathistory`를 실행 하면 지금 까지의 대화 내용을 포함하여 모델이 저장된다.
  - `ollama run gemma2_chathistory`를 실행 하면 이전 대화 내용을 포함하여 다시 실행 된다.
  - 아래는 다시 실행 했을 때의 예시이다.

```plaintext
ollama run gemma2_chathistory
>>> Hi
Hello! 👋 How can I help you today? 😊

>>> My name is KimHun
Hi KimHun, it's nice to meet you!

What can I do for you? 😄  Do you have any questions I can answer or tasks I can help with?

>>> What is my name?
Your name is KimHun. 😊  I remembered that from when you introduced yourself!  Is there anything else I can help you with?  </p>
```

---

해시태그: #Ollama #설치 #기본사용 #모델다운로드 #모델실행 #명령어 #대화형모드
