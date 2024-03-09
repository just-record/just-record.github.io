---
layout: single
title: "Python - Google Colab"
categories: Python
tag: [Python, Google Colab]
toc: true
toc_sticky: true
toc_label: "목차"
---

## Google Colab(Google Colaboratory)이란?

Google Colaboratory, 일반적으로 Google Colab이라고 부릅니다. 이 플랫폼은 Google에서 클라우드 기반의 무료 Jupyter 노트북 환경을 제공합니다. Colab은 복잡한 소프트웨어 설정 없이 웹 브라우저를 통해 Python 코드를 작성하고 실행할 수 있게 해줍니다.

### Colab의 장점

- 웹 기반의 Python 개발 환경: 복잡한 소프트웨어 설정 없이 웹 브라우저를 통해 Python 코드를 작성하고 실행할 수 있습니다.
- 무료 GPU 사용: 무료 GPU를 제공합니다. 시간 제한이 있습니다. 비용을 지불하면 더 많은 시간과 리소스를 사용할 수 있습니다.
- 구글 드라이브와의 통합: Google 드라이브와의 통합을 제공하여, 노트북을 쉽게 저장하고 공유할 수 있습니다.
  - Google 드라이브에서 데이터를 노트북으로 직접 불러오거나, 노트북에서 Google 드라이브에 데이터를 저장할 수 있습니다.
- 공유 및 협업 기능: Google 문서도구와 같이 실시간으로 공유하여 협업할 수 있습니다.

## Colab 사용하기

Google Colab을 사용하기 위해서는 웹 브라우저와 Google 계정만 있으면 됩니다.

- 방법1: Google Colab 웹사이트(colab.research.google.com)에 접속하고 Google 계정으로 로그인합니다.
- 방법2: Google Driver에 접속하여 새로운 노트북을 생성합니다.

### Google Drive에서 Colab 노트북 생성 및 저장

- Google Drive에 접속합니다.
- 좌측 상단의 '새로만들기' -> '더보기' -> 'Google Colaboratory'
  - 최초 실행 시 에는 '더보기' -> '연결할 앱 더보기' -> 'Google Colaboratory'를 검색하여 설치
- 작업한 노트북은 Google 드라이브에 저장되므로 추후에도 언제든지 접근할 수 있습니다.

## Google 드라이브와 Colab 연결하기

Google Colab은 Google 드라이브에 저장된 데이터를 쉽게 불러오거나 저장할 수 있습니다.

노트북의 코드 셀에 아래의 코드 입력

```python
from google.colab import drive
drive.mount('/content/drive')
```

링크가 표시될 것입니다. 이 링크를 클릭하여 Google 계정을 선택하고, Colab이 Google 드라이브에 접근할 수 있는 권한을 부여합니다.
생성된 인증 코드를 복사하여 Colab에 붙여넣고 Enter 키를 누릅니다.

'/content/drive/' 경로를 통해 Google 드라이브의 파일에 접근할 수 있습니다.

---

해시태그: #Python #Google Colab #Google Drive
