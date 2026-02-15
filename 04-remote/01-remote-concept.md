# 원격 저장소 개념

> remote, origin, upstream — 원격 저장소의 구조

## 개요

지금까지는 내 컴퓨터 안에서만 Git을 사용했습니다. 하지만 Git의 진짜 힘은 **여러 사람이 같은 프로젝트를 함께 작업**할 수 있다는 데 있는데요. 이를 가능하게 하는 것이 바로 **원격 저장소(remote repository)**입니다.

**선수 지식**: [브랜치 관리](../03-branch/05-branch-management.md)까지의 로컬 Git 기본 개념
**학습 목표**:
- 원격 저장소가 무엇인지 이해한다
- origin과 upstream의 차이를 설명할 수 있다
- `git remote` 명령어로 원격 저장소를 관리할 수 있다

## 왜 알아야 할까?

혼자 코딩할 때는 로컬 저장소만으로도 충분합니다. 하지만 현실의 개발은 대부분 **팀 작업**이죠. 코드를 팀원과 공유하고, 백업하고, 어디서든 접근하려면 원격 저장소가 필수입니다. GitHub, GitLab, Bitbucket 같은 플랫폼이 바로 이 원격 저장소를 호스팅하는 서비스예요. 원격 저장소의 구조를 제대로 이해해야 `push`, `pull`, `fetch` 같은 명령어가 **왜 그렇게 동작하는지** 감이 잡힙니다.

## 핵심 개념

### 개념 1: 원격 저장소란?

> 💡 **비유**: 원격 저장소는 **클라우드 드라이브**와 비슷합니다. 내 컴퓨터(로컬)에 있는 파일을 Google Drive에 올려두면, 다른 기기에서도 접근할 수 있고 다른 사람과 공유할 수도 있죠. 원격 저장소도 마찬가지입니다 — 내 로컬 저장소의 코드를 인터넷 어딘가에 올려두고 동기화하는 것이에요.

원격 저장소는 **네트워크 어딘가에 있는 Git 저장소의 복사본**입니다. 로컬 저장소와 구조는 완전히 동일하지만, 여러 사람이 접근할 수 있다는 점이 다릅니다.

**분산 버전 관리의 핵심**: Git에서는 모든 개발자가 **전체 히스토리의 완전한 사본**을 가지고 있습니다. 원격 저장소는 "중앙 서버"가 아니라, 팀이 합의한 **공유 지점(shared hub)**일 뿐이에요. 기술적으로는 어떤 저장소든 원격이 될 수 있습니다 — 심지어 같은 컴퓨터의 다른 폴더도요!

```bash
# 현재 저장소에 연결된 원격 저장소 확인
git remote
```

```output
origin
```

```bash
# URL까지 상세하게 확인
git remote -v
```

```output
origin  https://github.com/username/my-project.git (fetch)
origin  https://github.com/username/my-project.git (push)
```

여기서 `(fetch)`와 `(push)`가 따로 표시되는 게 보이시나요? Git에서는 가져오기(fetch)와 보내기(push)에 **서로 다른 URL**을 설정할 수도 있습니다. 대부분은 같은 URL이지만, 특수한 환경에서는 다를 수 있어요.

### 개념 2: origin — "내가 복제해온 곳"

> 💡 **비유**: `origin`은 **내 본적지** 같은 거예요. "어디서 왔어?" 물으면 답하는 곳이죠. Git에서 `origin`은 "이 저장소가 어디서 복제(clone)되었는가"를 가리킵니다.

`origin`은 Git이 자동으로 붙이는 **원격 저장소의 기본 별명(alias)**입니다. `git clone`을 하면 Git이 원본 URL에 자동으로 `origin`이라는 이름을 붙여줍니다.

```bash
# 저장소를 복제하면 자동으로 origin이 설정됨
git clone https://github.com/username/my-project.git
cd my-project

# origin 확인
git remote -v
```

```output
origin  https://github.com/username/my-project.git (fetch)
origin  https://github.com/username/my-project.git (push)
```

> ⚠️ **흔한 오해**: "`origin`은 Git의 특별한 키워드다" — 아닙니다! `origin`은 그냥 **관습적인 이름**일 뿐이에요. `pizza`라고 이름 붙여도 완벽하게 동작합니다. 다만, 모든 개발자가 `origin`이라는 이름을 기대하기 때문에, 바꾸면 혼란만 생깁니다.

`git init`으로 로컬에서 새 저장소를 만든 경우에는 원격이 자동 설정되지 않습니다. 직접 추가해야 하죠:

```bash
# 새 저장소에 원격 추가
git remote add origin https://github.com/username/my-project.git
```

### 개념 3: upstream — "원본 프로젝트"

> 💡 **비유**: 음식으로 비유하면, `origin`은 **내가 레시피를 따라 만든 요리**, `upstream`은 **원본 레시피를 쓴 셰프의 원본**입니다. 원본 셰프가 레시피를 업데이트하면, 나도 반영하고 싶을 때가 있죠.

`upstream`은 주로 **오픈소스 프로젝트에서 fork한 경우** 사용하는 이름입니다. 원본 저장소를 fork하면, 내 GitHub 계정에 복사본이 생기고 이것이 `origin`이 됩니다. 원본 저장소는 `upstream`이라는 이름으로 추가해요.

| 이름 | 가리키는 곳 | 주요 용도 |
|------|-------------|-----------|
| **origin** | 내 GitHub 저장소 (fork한 사본) | push/pull 주 대상 |
| **upstream** | 원본 프로젝트 저장소 | 원본의 최신 변경사항 가져오기 |

```bash
# 원본 저장소를 upstream으로 추가
git remote add upstream https://github.com/original-author/project.git

# 전체 원격 확인
git remote -v
```

```output
origin    https://github.com/my-account/project.git (fetch)
origin    https://github.com/my-account/project.git (push)
upstream  https://github.com/original-author/project.git (fetch)
upstream  https://github.com/original-author/project.git (push)
```

### 개념 4: 원격 저장소 관리 명령어

```bash
# 원격 저장소 추가
git remote add <이름> <URL>

# 원격 저장소 이름 변경
git remote rename <기존이름> <새이름>

# 원격 저장소 제거
git remote remove <이름>

# 원격 저장소 URL 변경
git remote set-url <이름> <새URL>
```

실무에서 자주 쓰이는 시나리오를 볼게요:

```bash
# HTTPS에서 SSH로 전환할 때
git remote set-url origin git@github.com:username/my-project.git

# URL이 바뀌었는지 확인
git remote -v
```

```output
origin  git@github.com:username/my-project.git (fetch)
origin  git@github.com:username/my-project.git (push)
```

```bash
# 원격 저장소의 상세 정보 보기
git remote show origin
```

```output
* remote origin
  Fetch URL: git@github.com:username/my-project.git
  Push  URL: git@github.com:username/my-project.git
  HEAD branch: main
  Remote branches:
    main    tracked
    develop tracked
  Local branch configured for 'git pull':
    main merges with remote main
  Local ref configured for 'git push':
    main pushes to main (up to date)
```

`git remote show`는 원격의 브랜치 상태, 트래킹 설정, push/pull 관계까지 한눈에 보여주는 매우 유용한 명령어입니다.

### 개념 5: 원격 추적 브랜치 (Remote-Tracking Branch)

> 💡 **비유**: 원격 추적 브랜치는 **뉴스 속보의 스냅샷**과 비슷합니다. "지금 이 순간 원격에 무엇이 있는가"를 찍어둔 사진이에요. 실시간이 아니라, 마지막으로 통신(`fetch`)했을 때의 상태입니다.

원격 추적 브랜치는 `origin/main`, `origin/develop` 같은 형태로 표시됩니다. 이것은 **원격 저장소의 브랜치 상태를 로컬에 기록한 읽기 전용 포인터**예요.

```bash
# 원격 추적 브랜치 확인
git branch -r
```

```output
  origin/main
  origin/develop
  origin/feature-login
```

```bash
# 로컬 + 원격 모든 브랜치 보기
git branch -a
```

```output
* main
  develop
  remotes/origin/main
  remotes/origin/develop
  remotes/origin/feature-login
```

중요한 점은, 원격 추적 브랜치는 **자동으로 업데이트되지 않는다**는 겁니다. `git fetch`를 실행해야 최신 상태가 반영됩니다. 이 부분은 [fetch와 remote 관리](./04-fetch-remote.md)에서 자세히 다룹니다.

## 실습: 직접 해보기

```bash
# 1. 실습용 "원격 저장소" 만들기 (로컬에서 시뮬레이션)
mkdir remote-practice && cd remote-practice

# bare 저장소 만들기 (원격 저장소 역할)
mkdir my-remote.git
cd my-remote.git
git init --bare
cd ..

# 2. 로컬 저장소 만들기
mkdir my-local
cd my-local
git init

# 3. 원격 저장소 연결
git remote add origin ../my-remote.git

# 4. 확인
git remote -v

# 5. 첫 커밋 후 push
echo "# Remote Practice" > README.md
git add README.md
git commit -m "첫 커밋"
git push -u origin main

# 6. 두 번째 원격 추가 (upstream 시뮬레이션)
mkdir ../upstream-remote.git
cd ../upstream-remote.git
git init --bare
cd ../my-local
git remote add upstream ../upstream-remote.git

# 7. 전체 원격 확인
git remote -v

# 8. 원격 상세 정보 확인
git remote show origin
```

> 💡 **알고 계셨나요?**: `git init --bare`로 만든 저장소는 작업 디렉토리가 없는 **순수한 Git 데이터베이스**입니다. GitHub 같은 서비스가 서버에서 사용하는 것이 바로 이 bare 저장소예요. 파일을 직접 편집하는 것이 아니라, 다른 저장소에서 push/pull하는 용도로만 사용됩니다.

## 더 깊이 알아보기

### Git의 분산 모델은 어떻게 탄생했을까?

Git 이전의 버전 관리 시스템(CVS, SVN)은 **중앙 집중식(centralized)**이었습니다. 모든 코드가 하나의 서버에 있고, 개발자는 서버에 연결해야만 커밋할 수 있었죠. 서버가 다운되면? 아무도 일할 수 없었습니다.

2005년, Linus Torvalds가 Git을 만들 때 핵심 설계 원칙 중 하나가 바로 **"모든 복사본이 완전한 저장소"**였습니다. 네트워크가 끊겨도 커밋, 브랜치, 히스토리 탐색이 모두 가능해야 한다는 것이었죠. 비행기 안에서, 지하철에서, 인터넷 없이도 개발을 계속할 수 있어야 했거든요.

이 설계가 바로 **분산 버전 관리(DVCS, Distributed Version Control System)**의 핵심이고, Git이 SVN을 대체하게 된 가장 큰 이유 중 하나입니다.

### "origin"이라는 이름의 유래

`origin`이라는 이름은 "이 저장소가 **기원(origin)**한 곳"이라는 의미에서 붙여졌습니다. `git clone`은 원본을 복제하는 것이니, 원본의 출처를 가리키는 이름으로 `origin`이 자연스럽게 선택된 거죠. 특별한 키워드가 아닌 순수한 관습(convention)이지만, 전 세계 개발자가 동일하게 사용하면서 사실상의 표준이 되었습니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "원격 저장소는 항상 GitHub에 있다" — 아닙니다! 원격 저장소는 같은 컴퓨터의 다른 폴더, 사내 서버, USB 드라이브, 심지어 이메일을 통해서도 연결할 수 있습니다. GitHub는 가장 인기 있는 호스팅 서비스일 뿐이에요.

> 🔥 **실무 팁**: `git remote show origin`을 주기적으로 실행해보세요. 원격의 브랜치 상태, 트래킹 설정, push/pull 관계를 한눈에 파악할 수 있어서 "왜 push가 안 되지?" 같은 문제를 빠르게 진단할 수 있습니다.

> 🔥 **실무 팁**: 회사에서 HTTPS와 SSH 중 하나로 통일하세요. 팀원마다 다르면 인증 문제 디버깅이 복잡해집니다. 자세한 설정은 [SSH와 인증](./05-auth.md)에서 다룹니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| 원격 저장소 | 네트워크에 있는 Git 저장소의 복사본. 공유와 협업의 허브 |
| `origin` | clone 시 자동 설정되는 원격 저장소의 기본 이름 |
| `upstream` | fork한 경우, 원본 프로젝트를 가리키는 관습적 이름 |
| `git remote -v` | 연결된 원격 저장소와 URL 확인 |
| `git remote add` | 새 원격 저장소 연결 |
| `git remote show` | 원격 저장소 상세 정보 확인 |
| 원격 추적 브랜치 | `origin/main` 같은 형태로 원격 상태를 로컬에 기록한 포인터 |
| bare 저장소 | 작업 디렉토리 없이 Git 데이터만 가진 서버용 저장소 |

## 다음 섹션 미리보기

원격 저장소가 무엇인지 이해했으니, 다음 섹션 [clone과 fork](./02-clone-fork.md)에서는 **원격 저장소를 내 컴퓨터로 가져오는 두 가지 방법**을 비교합니다. 건물 전체를 복사하는 clone과, 레시피를 가져와서 내 식대로 바꾸는 fork — 언제 어떤 것을 써야 할까요?

## 참고 자료

- [Pro Git Book — Working with Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes) - 원격 저장소의 공식 가이드
- [Git 공식 문서 — git-remote](https://git-scm.com/docs/git-remote) - remote 명령어 전체 옵션
- [Pro Git Book — Remote Branches](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches) - 원격 추적 브랜치의 동작 원리
- [GitHub Docs — Configuring a remote for a fork](https://docs.github.com/articles/configuring-a-remote-for-a-fork) - fork 시 upstream 설정 가이드
