---
layout: single
title: "Python 3.10 설치 (ubuntu 20.04)"
categories: Python
tag: [python, ubuntu]
toc: true
toc_sticky: true
toc_label: "목차"
---
ubuntu 20.04를 설치하면 python 3.8이 설치되어 있습니다. 3.8이면 서비스를 개발하기엔 충분 할 거라 생각했습니다. 인공지능 영상 관련 서비스도 3.8이면 가능했습니다. 하지만 자연어 처리를 위해서는 3.9 이상이 필요하게 되네요. ubuntu 20.04에 python 3.10을 설치한 내용을 간단히 정리했습니다. 참고로 저의 짧은 경험으로 **python3.8과 3.10은 가상환경을 사용하여 분리** 하는 것이 좋다고 판단됩니다.

## PPA 저장소 추가

Python 3.10은 Ubuntu 20.04의 기본 저장소에 포함되어 있지 않습니다. 하지만 PPA(Personal Package Archive)를 추가하여 설치할 수 있습니다. 다음 명령어를 통해 PPA를 추가합니다:

```bash
sudo add-apt-repository ppa:deadsnakes/ppa
```

## 시스템 업데이트

PA를 추가한 후, 시스템의 패키지 목록을 업데이트합니다:

```bash
sudo apt update
```

업데이트 된 패키지의 목록을 확인합니다:

```bash
apt list | grep python3.10
```

## Python 3.10 설치

Python 3.10을 설치합니다:

```bash
sudo apt install python3.10
```

설치 후 버전을 확인합니다:

```bash
python3.10 --version
```

```bash
Python 3.10.13
```

이전 버전(3.8)을 확인합니다.

```bash
python3 --version
```

```bash
Python 3.8.10
```

**python3은 3.8**을 사용하고 **python3.10은 3.10**을 사용합니다.

> 3.10 패키지 설치

```bash
sudo apt install python3.10-dev python3.10-venv python3.10-distutils python3.10-lib2to3 python3.10-gdbm python3.10-tk
```

## 여러 버전의 python 관리

Ubuntu 시스템에서 update-alternatives를 사용하여 여러 버전의 Python 인터프리터를 관리하고 선택할 수 있습니다.

python3.10을 우선순위 1로 설정합니다.

```bash
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.10 1
```

```bash
update-alternatives: using /usr/bin/python3.10 to provide /usr/bin/python (python) in auto mode
```

python3.8을 우선순위 2로 설정합니다.

```bash
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.8 2
```

```bash
update-alternatives: using /usr/bin/python3.8 to provide /usr/bin/python (python) in auto mode
```

Python 버전을 선택합니다.

```bash
sudo update-alternatives --config python
```

```bash
There are 2 choices for the alternative python (providing /usr/bin/python).

  Selection    Path                 Priority   Status
------------------------------------------------------------
* 0            /usr/bin/python3.8    2         auto mode
  1            /usr/bin/python3.10   1         manual mode
  2            /usr/bin/python3.8    2         manual mode

Press <enter> to keep the current choice[*], or type selection number: 
```

1번을 선택합니다.

```bash
There are 2 choices for the alternative python (providing /usr/bin/python).

  Selection    Path                 Priority   Status
------------------------------------------------------------
* 0            /usr/bin/python3.8    2         auto mode
  1            /usr/bin/python3.10   1         manual mode
  2            /usr/bin/python3.8    2         manual mode

Press <enter> to keep the current choice[*], or type selection number: 1
update-alternatives: using /usr/bin/python3.10 to provide /usr/bin/python (python) in manual mode
```

## pip3 업그레이드

pip3를 업그레이드 합니다.

```bash
pip3 install --upgrade pip
```

```bash
Collecting pip
  Using cached pip-23.3.2-py3-none-any.whl (2.1 MB)
Installing collected packages: pip
  Attempting uninstall: pip
    Found existing installation: pip 20.0.2
    Uninstalling pip-20.0.2:
      Successfully uninstalled pip-20.0.2
Successfully installed pip-23.3.2
```

## python3.10 가상환경 설정

python3.10을 사용하는 가상환경을 설정합니다.

가상환경을 구축 할 디렉토리는 `myvenv_app`로 하고 가상환경 이름은 `myvenv`으로 하겠습니다.

python3.10 명령어를 사용하여 가상환경을 생성합니다.

```bash
cd ~/myvenv_app
python3.10 -m venv myvenv
```

가상환경을 활성화 합니다.

```bash
source myvenv/bin/activate
```

---

해시태그: #python #python3.10 #ubuntu20.04
