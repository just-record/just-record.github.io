---
layout: single
title: "Github - Large files Error"
categories: Github
tag: [Github, Large files]
toc: true
toc_sticky: true
toc_label: "목차"
---
git으로 소스코드를 관리하다 보면 실수로 **대용량 파일이 생성** 된 걸 놓치고 **commit** 하는 경우가 있습니다. .gitignore에 미처 작성 하지 못한 거죠. 제 경우는 인공지능 모델 파일이었습니다. 물론 .gitignore에 인공지능 모델 파일 확장자를 등록을 했지만 백업 파일을 생성하면서 문제가 발생하였습니다.  

## 오류 메시지

git puhs를 하면 아래와 같은 에러가 발생합니다.

```bash
remote: error: Trace: ...
remote: error: See https://gh.io/lfs for more information.
remote: error: File ...(대용량 파일의 경로와 파일명) 137.24 MB; this exceeds GitHub's file size limit of 100.00 MB
remote: error: GH001: Large files detected. You may want to try Git Large File Storage - https://git-lfs.github.com.
To github.com:...(저장소 주소).git
 ! [remote rejected] main -> main (pre-receive hook declined)
error: failed to push some refs to '(저장소 주소).git'
```

- .gitignore에 대용량 파일 확장자를 등록하고 commit 하고 push를 해도 같은 에러가 발생합니다.
- 실제 대용량 파일을 삭제하고 commit 하고 push를 해도 같은 에러가 발생합니다.

## 대용량 파일 제거

아래의 명령을 통해서 대용량 파일을 리포지토리의 모든 커밋 기록에서 제거합니다.  
대용량 파일의 경로와 파일명을 `service/models/best.pt`로 가정하겠습니다.

```bash
git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch service/models/best.pt' --prune-empty -- --all
```

- git filter-branch -f: 리포지토리의 커밋, 태그 등을 재작성하기 위해 사용되며, -f 또는 --force 는 필터링 작업이 이미 실행된 이력이 있더라도 강제로 실행
- --index-filter: 각 커밋의 인덱스를 필터링할 때 사용되는 스크립트를 지정
- 'git rm --cached --ignore-unmatch service/models/best.pt': 실제로 파일을 제거하는 명령어
  - git rm --cached: 실제 디렉토리의 파일은 그대로 두고 인덱스에서만 제거
  - --ignore-unmatch: 지정된 파일이 없어도 에러를 발생시키지 않고 명령어를 계속 실행
  - service/models/best.pt: 제거하려는 파일의 경로
- --prune-empty: 필터링 후에 변경 사항이 없는 빈 커밋을 제거
- -- --all: 리포지토리의 모든 브랜치와 태그에 대해 이 작업을 적용

모두 다 이해 하실 필요는 없을 것 같습니다. **대용량 파일의 경로와 파일명(`service/models/best.pt')만 변경** 해서 실행 하시면 됩니다.

## 변경 사항 강제 push

아래의 명령을 통해서 변경 사항을 강제로 push 합니다.

```bash
git push origin main --force
```

- --force: 변경 사항을 강제로 push

push가 성공적으로 완료 되었습니다.  

수정 한 소스코드가 많은 경우 무척 당황스럽습니다. 수정한 소스코드를 백업하고 다시 clone 받기도 무척 번거롭니다. 이 포스팅이 조그이나마 도움이 되었으면 좋겠습니다.

---

해시태그: #git #github #large files #git filter-branch #git push --force
