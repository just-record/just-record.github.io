---
layout: single
title: "Visual studio code - Jupyter notebook 사용하기"
categories: visual-studio-code
tag: [Visual studio code, Jupyter notebook]
toc: true
toc_sticky: true
toc_label: "목차"
---

Jupyter notebook으로 코딩 할 때 Copilot을 사용하고 싶어 졌습니다. Visual studio code에서 Jupyter notebook을 사용하면 Copilot을 사용할 수 있네요.

## Jupyter 확장 설치

Visual studio code를 실행합니다.
좌측 아이콘 중 `확장(Extensions)`을 클릭합니다.
검색 란에 `Jupyter`를 입력합니다.
'Jupyter' 확장을 설치합니다.

## Jupyter notebook 파일 열기 또는 생성

### 기존 Notebook 파일 열기

VS Code에서 .ipynb 파일을 직접 열 수 있습니다.  
파일 > 열기를 선택하거나, 탐색기에서 해당 파일을 더블클릭

### 새로운 Notebook 파일 생성

- 새 파일을 생성하고 파일 확장자를 .ipynb로 지정
- 커맨드 팔레트(Ctrl+Shift+P 또는 Cmd+Shift+P on macOS)를 열고 "Jupyter: Create New Blank Notebook" 명령을 실행

## Jupyter notebook 사용 하기

'.ipynb' 파일을 열면 Jupyter notebook이 실행됩니다.

`select kernel`를 클릭하여 사용할 커널을 선택합니다.

코드 셀에 Python 코드를 입력하고 실행할 수 있습니다. 셀을 실행하기 위해 셀 내부에서 Shift+Enter를 사용하거나, 셀 좌측의 실행 버튼을 클릭합니다. 결과는 바로 아래에 표시됩니다.

> Jupyter 서버를 실행 시키지 않아도 Kernel만 선택 하면 바로 사용할 수 있습니다. 아마도 'Jupyter' 확장이 내부적으로 서버를 실행시키는 것 같습니다.

---

해시태그: #visual-studio-code #jupyter-notebook #python #copilot #vscode
