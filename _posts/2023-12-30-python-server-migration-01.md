---
layout: single
title: "Python 서버 이전 - 01 (ubuntu 20.04)"
categories: Python
tag: [Python]
toc: true
toc_sticky: true
toc_label: "목차"
---
개발 서버를 이전해야 할 경우가 있습니다. '개발 서버를 이전하는 경우가 많이 생기겠어' 라고 생각을 했습니다. 근데 생각외로 많았습니다. 자주 하던 작업이 아니어서 체감상 많이 생겼다고 느끼는 것일 수도 있습니다. **확실 한건 갑작스럽게 서버를 이전해야 하는 경우에는 매우 당황**스럽니다. 너무 당황하지 말자는 뜻에서 간단히 정리해 봤습니다.

환경은 아래로 가정하겠습니다.

- cloud
- ubuntu 20.04
- python 3.8
- git, github
- visual studio code

## 대략의 절차

1. 새로운 서버를 생성합니다.
2. 파이썬을 설치합니다.
3. 소스코드를 이전합니다.
4. 개발 tool을 설치 하고 설정합니다.
5. 테스트 합니다.

## 새로운 서버 생성

서버는 클라우드, 온프레미스 등 다양한 방법이 있습니다. 사전에 이전 계획이 있었으면 서버를 이미 준비 할 수 있지만 **갑작스러운 경우에는 클라우드 서비스**가 큰 도움이 됩니다.

네이버 클라우드의 서버 생성은 [Cloud - 01 (naver - server)](https://just-record.github.io/cloud/cloud-01/)를 참조 하시면 됩니다.

## 파이썬 설치

먼저 root가 아닌 사용자가 필요합니다. python 어플리케이션을 실행할 사용자를 생성합니다. 그리고 python을 설치합니다. ubuntu 20.04에는 python 3.8이 이미 설치되어 있습니다. pip는 설치를 해야 합니다. pip는 설치 하고 python 가상 환경을 설정합니다.

- 사용자 계정 추가
- python/pip 설치
- python 가상 환경 설정

python은 **가상 환경 사용을 추천** 드립니다. 여러 프로젝트를 개발하다 보면 각각의 프로젝트마다 패키지가 다르고 패키지의 버전도 다를 수 있습니다. 이런 경우 가상 환경을 사용하면 각각의 프로젝트마다 독립적인 환경을 구성할 수 있습니다. 처음부터 가상환경을 구성하여 사용하는 습관을 들이는 것이 좋다고 생각합니다.

[Python 개발 환경 설정 - 01 (ubuntu 20.04)](https://just-record.github.io/python/python-dev-env-01/)를 참조 하시면 ubuntu 20.04에 파이썬 개발 환경을 설정하는 방법을 확인 하실 수 있습니다.

## 소스코드 이전

먼저 **소스코드는 어떠한 형태로든 반드시 백업**을 해 두어야 합니다. 사전에 계획하여 서버를 이전 하면 좋겠지만 갑작스러운 경우도 발생합니다. 소스코드는 무조건 백업을 해 두어야 합니다. 여기서는 github를 사용하여 소스코드를 이전하는 방법을 확인 하겠습니다.

[Github ssh - 원격 서버에서 Github 접속하기](https://just-record.github.io/github/github-ssh-01/)과 [Github ssh - 소스 코드 서버 이전](https://just-record.github.io/github/github-ssh-02/)를 참조 하시면 Github에 소스 코드를 이전하는 방법을 확인 하실 수 있습니다.

> Github에서 소스 코드를 이전 할 때 주의 사항

.gitignore 파일에 의해 제외된 파일은 이전 되지 않습니다. 예를 들어 openai의 API key 정보를 담고 있는 .env 파일은 **.gitignore에 의해 제외**되어 있습니다. 이런 파일은 따로 백업을 해 두어야 합니다.

## 개발 tool 설치 및 설정

개발 tool은 개발 환경에 따라 다르겠지만 여기서는 Visual Studio Code를 사용하여 원격 서버에 접속하여 개발 환경을 설정하는 방법을 확인 하겠습니다.

visual studio code

- 원격 서버의 소스 코드를 직접 편집
- 터미널도 지원(여러 개의 터미널을 동시에 사용)
- 가상 환경의 python interpreter를 지정하여 사용
- git 지원

[Visual Studio Code - 01](https://just-record.github.io/visual-studio-code/vscode-01/) 를 참조 하시면 됩니다.

추가로 jupyter notebook을 의 설치 및 설정은 [Python 개발 환경 설정 - 01 (ubuntu 20.04)](https://just-record.github.io/python/python-dev-env-01/)를 참조 하시면 됩니다.

## 테스트

- visual studio code를 통해 원격 서버에 접속합니다.
- 터미널을 실행 시키고 가상 환경을 활성화 합니다.
- python 프로그램을 실행 시킵니다.(또는 jupyter notebook을 실행 시킵니다.)
- 결과를 확인합니다.
- github test를 위해 소스 코드를 수정합니다.
- github에 push 합니다.
- github에서 소스 코드를 확인합니다.

---

작성 하다 보니까 그저 이전의 포스팅을 모아 놓은것 같습니다. 그래도 갑작스러운 일로 당황스러울 때 도움이 되었으면 좋겠습니다.

---

해시태그: #python #ubuntu20.04 #개발환경 #jupyter #notebook #가상환경
