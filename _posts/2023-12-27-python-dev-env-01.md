---
layout: single
title: "Python 개발 환경 설정 - 01 (ubuntu 20.04)"
categories: Python
tag: [Python]
toc: true
toc_sticky: true
toc_label: "목차"
---
ubuntu 20.04에 Python 개발 환경을 설정하겠습니다.

클라우드에서 ubuntu 설치는 [Cloud - 01 (naver - server)]({{site.url}}/cloud-01/)를 참고 하시기 바랍니다.

## 사용자 계정

ubuntu server에 root로 접속하여 사용자 계정을 생성합니다.

```bash
sudo adduser dev01
```

```bash
sudo adduser dev01 sudo
```

새로운 사용자를 sudo 그룹에 추가합니다.

```bash
exit
```

터미널을 종료합니다.

---

dev01 사용자로  다시 접속합니다.

## python 설치

ubuntu 20.04에는 python 3.8이 설치되어 있습니다. 확인해 보겠습니다.

```bash
python3 --version
```

```bash
Python 3.8.10
```

### pip 설치

> 패키지 목록 업데이트

```bash
sudo apt update
```

root password 입력

> 업데이트 가능한 패키지 목록 확인

```bash
sudo apt list --upgradable
```

> 패키지 업그레이드

```bash
sudo apt upgrade
```

'Y' 입력, default값을 모두 선택합니다.

> pip 설치: Python 3용 pip

```bash
sudo apt install python3-pip
```

'Y' 입력

> 설치 확인

```bash
pip3 --version
```

```bash
pip 20.0.2 from /usr/lib/python3/dist-packages/pip (python 3.8)
```

## 파이썬 가상환경 설정

파이썬의 `venv` 모듈로 가상환경을 설정합니다.

> 가상환경(venv) 모듈 설치

```bash
sudo apt install python3.8-venv
```

> 디렉토리 구성

디렉토리를 생성합니다. 저는 아래와 같이 생성하였습니다. 여러분이 원하시는 구조로 생성하시면 됩니다.

```bash
mkdir app           # 어플리케이션 디렉토리
cd app
mkdir devcomn_prj   # devcomn 가상환경의 상위 디렉토리
cd devcomn_prj
mkdir src           # devcomn 가상환경에서 개발할 소스 디렉토리
```

> 가상환경 생성

아래의 명령은 `dev01@server_name:~/app/devcomn_prj$`에서 실행합니다.

```bash
python3 -m venv devcomn
```

`devcomn` 디렉토리가 생성되었으면 성공입니다.

> 최종 디렉토리 구성

```text
.
└── app
    └── devcomn_prj
        ├── devcomn
        └── src
```

- app: 어플리케이션 디렉토리 - 예를 들어 새로운 어플리케이션은 app 디렉토리 하위에 devapp1_prj 를 생성해서 개발합니다.
- devcomn: devcomn 가상환경 디렉토리
- src: devcomn의 가상환경에서 개발할 소스 디렉토리
- devcomn_prj: devcomn 가상환경과 소스의 상위 디렉토리

제가 임의로 구성한 디렉토리 구조입니다. **여러분이 원하시는 구조**로 디렉토리를 구성하시면 됩니다.  만약 devcomn 가상환경이 더 이상 필요 없게 되면 devcomn_prj 디렉토리를 삭제하시면 됩니다.

> 가상환경 활성화

아래의 명령은 `dev01@server_name:~/app/devcomn_prj$`에서 실행합니다. 바로 하위에 `devcomn` 디렉토리가 있어야 합니다.

```bash
source devcomn/bin/activate
```

`(devcomn) dev01@server_name:~/app/devcomn_prj$`와 같이 `(devcomn)`이 표시되면 성공입니다.

> pip 업그레이드

```bash
pip3 install --upgrade pip
```

> 필요한 패키지 설치

가상환경을 활성화 한 후 필요한 패키지를 설치합니다. 예를 들어 numpy 패키지를 설치하려면 아래와 같이 실행합니다.

```bash
pip3 install numpy
# ...
```

지금 설치하는 패키지는 가상 공간에서만 사용할 수 있습니다.

> 패키지 설치 디렉토리 확인

가상환경의 패키지 설치 디렉토리를 확인해 보겠습니다.

```bash
ls -al devcomn/lib/python3.8/site-packages/
``````

```bash
drwxrwxr-x 21 dev01 dev01 4096 Dec 27 21:55 numpy
drwxrwxr-x  2 dev01 dev01 4096 Dec 27 21:55 numpy-1.24.4.dist-info
drwxrwxr-x  2 dev01 dev01 4096 Dec 27 21:55 numpy.libs
```

조금 전 설치한 numpy 패키지가 있는 것을 확인할 수 있습니다.

> 가상환경 비활성화

가상환경을 비활성화 하려면 아래의 명령을 실행합니다.

```bash
deactivate
```

## jupyter notebook 설치

가상환경에서 jupyter notebook을 설치하겠습니다.

> jupyter notebook 설치

```bash
# jupyter notebook 설치
pip3 install jupyter notebook   
# Jupyter 노트북과 같은 인터랙티브 환경에서 Python 커널을 제공합니다.
pip3 install ipykernel
# Jupyter 노트북에서 'devcomn'이라는 커널을 선택하여 사용할 수 있습니다.
python3 -m ipykernel install --user --name devcomn
```

### jupyter notebook 환경 설정

현재 디렉토리는 `dev01@server_name:~/app/devcomn_prj$`입니다.

> `jupyter_notebook_config.py` 파일을 생성합니다.

```bash
jupyter notebook --generate-config
```

```bash
Writing default config to: /home/dev01/.jupyter/jupyter_notebook_config.py
```

생성된 파일을 현재 디렉토리로 복사합니다.

```bash
cp ~/.jupyter/jupyter_notebook_config.py ./
```

```bash
vi jupyter_notebook_config.py
```

```text
c = get_config()
c.ServerApp.port = 8880
c.ServerApp.ip = '0.0.0.0'
c.ServerApp.allow_root = True
c.ServerApp.notebook_dir = './src'
c.ServerApp.open_browser = False
```

위의 내용들을 주석을 풀고 원하는 값으로 변경합니다.

- c.ServerApp.port: 서비스 포트 # 원하는 포트로 변경, 보안을 위해 8888 이외의 포트로 변경
- c.ServerApp.ip: 특정 IP로 들어오는 요청만 허용 # '0.0.0.0'은 모든 IP 허용
- c.ServerApp.allow_root: root 계정으로 실해 허용 여부
- c.ServerApp.notebook_dir: jupyter notebook 실행 디렉토리
- c.ServerApp.open_browser: 브라우저 자동 실행 여부

<!--
c.NotebookApp.allow_origin = '*'
c.NotebookApp.allow_root = True
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.notebook_dir = './src'
c.NotebookApp.open_browser = False
c.NotebookApp.password = 'argon2:$argon2id$v=19$m=10240,t=10,p=8$USOgSyrC6hnEgJhUd7nyYw$ObzG4NqqiwdeO/jhtKK4mUzHBRwdjQdlkjUdTPqZ6TQ'
c.NotebookApp.password_required = True
c.NotebookApp.port = 8182
c.NotebookApp.token = ''
-->

접속 패스워드는 조금 후 설정하겠습니다.

저장하고 vi를 종료합니다.

> 접속 비밀번호 생성

```bash
jupyter notebook password
```

```bash
Enter password:     # 비밀번호 입력 (예: mypassword)
Verify password:    # 비밀번호 확인 (예: mypassword)
[JupyterPasswordApp] Wrote hashed password to /home/dev01/.jupyter/jupyter_server_config.json
```

비밀번호를 확인합니다.

```bash
cat ~/.jupyter/jupyter_server_config.json
```

```json
{
  "IdentityProvider": {
    "hashed_password": "argon2:$argon2id$v=19$m=10240,t=10,p=8$FsGB7mSHSkaKpX5Hd1neiw$6cRBJCI2hH27oUMPP2HLzqa+YpNBIgvRDRseNr25MhY"
  }
}
```

비밀번호 "argon2:$argon2id$v=19$m=10240,t=10,p=8$FsGB7mSHSkaKpX5Hd1neiw$6cRBJCI2hH27oUMPP2HLzqa+YpNBIgvRDRseNr25MhY"를 복사합니다.

다시 `jupyter_notebook_config.py`을 편집합니다.

```bash
vi jupyter_notebook_config.py
```

```text
c.ServerApp.password = 'argon2:$argon2id$v=19$m=10240,t=10,p=8$FsGB7mSHSkaKpX5Hd1neiw$6cRBJCI2hH27oUMPP2HLzqa+YpNBIgvRDRseNr25MhY'
c.ServerApp.password_required = True
```

위의 내용들을 주석을 풀고 위와 동일하게 변경합니다.

저장하고 vi를 종료합니다.

> `run_jupyter.sh` 파일을 생성합니다.

jupyter notebook을 실행하는 스크립트 파일을 생성합니다.

```bash
vi run_jupyter.sh
```

```text
jupyter notebook --config=./jupyter_notebook_config.py
```

위의 내용을 입력하고 저장하고 vi를 종료합니다.

현재 디렉토리의 `jupyter_notebook_config.py`을 사용하여 jupyter notebook을 실행합니다. config 파일에 의해 하위의 `./src` 가 root 디렉토리가 됩니다.

실행 권한을 부여합니다.

```bash
chmod 750 ./run_jupyter.sh
```

### 방화벽 설정

> 방화벽 설정

```bash
sudo ufw allow 8880
```

```bash
Rule added
Rule added (v6)
```

> 방화벽 상태 확인

```bash
sudo ufw status
```

```bash
Status: active

To                         Action      From
--                         ------      ----
8880                       ALLOW       Anywhere
8880 (v6)                  ALLOW       Anywhere (v6)
```

> 방화벽 비활성화

```bash
sudo ufw disable
```

```bash
Firewall stopped and disabled on system startup
```

### jupyter notebook 실행

> jupyter notebook 실행

```bash
./run_jupyter.sh
```

http://server_ip:8880 에 접속합니다.

Password(mypassword)를 입력하고 `Log in` 버튼을 클릭합니다.

jupyter notebook이 실행되면서 `./src` 디렉토리가 root 디렉토리가 됩니다.

우측 상단의 `New` 버튼을 클릭하고 `Notebook`을 선택하여 새로운 노트북 파일일 생성합니다. 새창이 열리면서 Kernel을 선택 할 수 있습니다. 조금 전 설치 한 `devcomn`을 선택 할 수 있습니다.

![jupyter notebook 화면]({{site.url}}/images/python-dev-env/python-dev-env-01_01.png)

---

해시태그: #cloud #navercloud #server #ubuntu #서버생성
