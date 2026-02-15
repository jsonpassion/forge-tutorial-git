# clone과 fork

> 저장소 복제, fork와 clone의 차이, 오픈소스 기여 준비

## 개요

원격 저장소의 개념을 배웠으니, 이제 실제로 **원격에 있는 저장소를 내 컴퓨터로 가져오는 방법**을 알아볼 차례입니다. Git에서는 크게 **clone**과 **fork** 두 가지 방식이 있는데, 비슷해 보이지만 목적과 동작 방식이 꽤 다릅니다.

**선수 지식**: [원격 저장소 개념](./01-remote-concept.md)에서 배운 origin, upstream
**학습 목표**:
- `git clone`의 동작 원리와 옵션을 이해한다
- fork의 개념과 clone과의 차이를 명확히 구분할 수 있다
- 오픈소스 프로젝트에 기여하기 위한 fork → clone → upstream 워크플로우를 수행할 수 있다

## 왜 알아야 할까?

개발을 시작하는 거의 모든 상황은 **기존 코드를 가져오는 것**에서 출발합니다. 회사에 입사하면 팀 프로젝트를 clone하고, 오픈소스에 기여하려면 fork 후 clone 하죠. 이 두 가지를 혼동하면 "왜 push가 안 되지?", "내 변경이 원본에 반영이 안 되네?" 같은 상황에 빠지기 쉽습니다.

## 핵심 개념

### 개념 1: git clone — "건물 전체를 복사하기"

> 💡 **비유**: clone은 **건물 전체를 복사해서 내 땅에 똑같이 짓는 것**과 같습니다. 외관뿐 아니라 내부 구조, 설비 도면, 리모델링 기록까지 전부 복제됩니다. Git clone도 마찬가지로 코드만이 아니라 **모든 커밋 히스토리, 브랜치, 태그**를 통째로 가져옵니다.

```bash
# 기본 clone
git clone https://github.com/username/project.git
```

이 한 줄이 내부적으로 하는 일을 풀어보면:

1. `project`라는 폴더를 생성
2. 그 안에 `.git` 디렉토리를 초기화
3. 원격 저장소의 **모든 데이터**를 다운로드
4. 원격 URL을 `origin`이라는 이름으로 자동 등록
5. 기본 브랜치(main)를 체크아웃

```bash
# 다른 폴더 이름으로 clone
git clone https://github.com/username/project.git my-folder

# 특정 브랜치만 clone
git clone -b develop https://github.com/username/project.git

# 최신 커밋만 가져오기 (히스토리 생략 — 빠르지만 제한적)
git clone --depth 1 https://github.com/username/project.git
```

```bash
# SSH로 clone (인증 설정 필요 — Ch4.5에서 설명)
git clone git@github.com:username/project.git
```

> 🔥 **실무 팁**: 대규모 저장소(리눅스 커널, Chromium 등)를 clone할 때는 `--depth 1`로 **shallow clone**을 하면 시간과 디스크를 크게 절약할 수 있습니다. 단, 전체 히스토리가 필요한 작업(`git log`, `git blame` 등)에는 제한이 있어요.

### 개념 2: fork — "레시피를 가져와서 내 식대로"

> 💡 **비유**: fork는 **원본 레시피를 가져와서 내 레시피북에 복사하는 것**입니다. 원본 셰프의 레시피는 그대로이고, 나는 내 사본에서 자유롭게 재료를 바꾸거나 조리법을 수정할 수 있어요. 나중에 "이 변형이 더 맛있어요!"라고 원본 셰프에게 제안(Pull Request)할 수도 있습니다.

**fork는 Git의 명령어가 아닙니다.** GitHub, GitLab 같은 플랫폼이 제공하는 기능이에요. fork를 하면:

1. 원본 저장소의 완전한 복사본이 **내 GitHub 계정**에 생성됨
2. 원본과 연결 관계가 유지됨 (GitHub이 추적)
3. 내 복사본에 대한 **완전한 권한**을 가짐

fork는 GitHub 웹사이트에서 "Fork" 버튼을 클릭하거나, GitHub CLI로 할 수 있습니다:

```bash
# GitHub CLI로 fork
gh repo fork original-author/project
```

```output
✓ Created fork username/project
? Would you like to clone the fork? Yes
✓ Cloned fork
```

### 개념 3: clone vs fork — 핵심 차이

| 비교 항목 | clone | fork |
|-----------|-------|------|
| **동작 위치** | 로컬 컴퓨터에 복사 | GitHub 계정에 복사 |
| **명령어** | `git clone` (Git 명령어) | GitHub 웹/CLI (플랫폼 기능) |
| **원본과의 관계** | origin으로 연결 | GitHub이 fork 관계 추적 |
| **push 권한** | 원본에 권한이 있어야 push 가능 | 내 fork에는 자유롭게 push |
| **주 사용 상황** | 팀 프로젝트, 내가 권한이 있는 저장소 | 오픈소스 기여, 남의 프로젝트를 내 것으로 |
| **코드 동기화** | `git pull`로 직접 | upstream 설정 후 fetch |

**한 줄 요약**: 권한이 있으면 **clone**, 없으면(또는 독립적으로 발전시키고 싶으면) **fork → clone**.

### 개념 4: fork → clone → upstream 워크플로우

오픈소스 프로젝트에 기여하는 표준 워크플로우입니다:

**1단계: fork** — GitHub에서 원본 프로젝트를 내 계정으로 fork

**2단계: clone** — fork한 저장소를 로컬로 clone

```bash
# 내 fork를 clone
git clone https://github.com/my-account/project.git
cd project
```

**3단계: upstream 추가** — 원본 저장소를 upstream으로 등록

```bash
# 원본 저장소를 upstream으로 추가
git remote add upstream https://github.com/original-author/project.git

# 확인
git remote -v
```

```output
origin    https://github.com/my-account/project.git (fetch)
origin    https://github.com/my-account/project.git (push)
upstream  https://github.com/original-author/project.git (fetch)
upstream  https://github.com/original-author/project.git (push)
```

**4단계: 원본과 동기화** — upstream의 최신 변경사항을 내 로컬에 반영

```bash
# upstream의 최신 변경사항 가져오기
git fetch upstream

# 내 main 브랜치에 반영
git switch main
git merge upstream/main

# 내 fork(origin)에도 반영
git push origin main
```

**5단계: 작업 후 PR** — 새 브랜치에서 작업 후 Pull Request

```bash
# 작업 브랜치 생성
git switch -c fix-typo

# 코드 수정 후 커밋
git add .
git commit -m "Fix typo in README"

# 내 fork에 push
git push origin fix-typo

# GitHub에서 원본 프로젝트로 Pull Request 생성
```

> ⚠️ **흔한 오해**: "fork하면 원본과 자동으로 동기화된다" — 아닙니다! fork는 복사하는 그 시점의 스냅샷일 뿐이에요. 원본이 업데이트되어도 내 fork는 자동으로 따라가지 않습니다. upstream을 직접 설정하고 주기적으로 fetch + merge 해야 합니다.

### 개념 5: clone의 유용한 옵션들

```bash
# 모든 서브모듈까지 함께 clone
git clone --recurse-submodules https://github.com/username/project.git

# 특정 브랜치만 clone (나머지 브랜치는 가져오지 않음)
git clone --single-branch -b main https://github.com/username/project.git

# 큰 파일 없이 clone (Git LFS 파일은 나중에 별도 다운로드)
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/username/large-project.git
```

```bash
# shallow clone 후 나중에 전체 히스토리 가져오기
git clone --depth 1 https://github.com/username/project.git
cd project

# 필요할 때 전체 히스토리 복원
git fetch --unshallow
```

## 실습: 직접 해보기

```bash
# 1. 실습용 bare 저장소 만들기 (원본 프로젝트 역할)
mkdir -p /tmp/clone-practice
cd /tmp/clone-practice

mkdir original-project.git
cd original-project.git
git init --bare
cd ..

# 2. 원본에 초기 콘텐츠 넣기
git clone original-project.git author-workspace
cd author-workspace
echo "# Awesome Project" > README.md
echo "print('hello')" > app.py
git add . && git commit -m "Initial commit"
git push origin main
cd ..

# 3. clone 해보기 (팀원 역할)
git clone original-project.git teammate-workspace
cd teammate-workspace
git remote -v
git log --oneline
cd ..

# 4. fork 시뮬레이션 (원본을 복제한 별도의 저장소)
git clone --bare original-project.git forked-project.git

# fork한 저장소를 clone
git clone forked-project.git contributor-workspace
cd contributor-workspace

# upstream 설정
git remote add upstream ../original-project.git
git remote -v

# 5. 원본에 변경이 생긴 상황 시뮬레이션
cd ../author-workspace
echo "v2.0" >> README.md
git add . && git commit -m "Update to v2.0"
git push origin main
cd ..

# 6. contributor가 upstream과 동기화
cd contributor-workspace
git fetch upstream
git merge upstream/main
git log --oneline
```

## 더 깊이 알아보기

### fork의 탄생 — GitHub이 만든 문화

"fork"라는 개념은 원래 오픈소스 세계에서 **프로젝트가 방향성 차이로 갈라지는 것**을 의미했습니다. 예를 들어, LibreOffice는 OpenOffice의 "fork"이고, MariaDB는 MySQL의 "fork"죠. 이런 맥락에서 fork는 약간 부정적인 뉘앙스도 있었습니다.

하지만 2008년 GitHub이 등장하면서 fork에 **완전히 새로운 의미**를 부여했습니다. "누구나 쉽게 복사하고, 수정하고, 제안할 수 있는 협업 도구"로 만든 것이죠. GitHub의 fork + Pull Request 모델은 오픈소스 기여의 문턱을 획기적으로 낮추어, "Social Coding"이라는 문화를 만들어냈습니다. 2018년 Microsoft가 GitHub을 75억 달러에 인수한 것도, 이 협업 모델의 가치를 인정한 것이었습니다.

> 💡 **알고 계셨나요?**: GitHub 이전에 오픈소스에 기여하려면 메일링 리스트에 패치 파일을 보내야 했습니다. 리눅스 커널은 아직도 이 방식을 사용하지만, 대부분의 프로젝트는 GitHub의 fork + PR 모델로 전환했죠. 이 모델 덕분에 오늘날 오픈소스 기여자 수가 폭발적으로 증가할 수 있었습니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "clone하면 원격에서도 뭔가 바뀐다" — 아닙니다! `git clone`은 순수하게 **읽기 전용 복사**입니다. 원격 저장소에는 아무런 변화도 일어나지 않아요. push하기 전까지는 내 로컬에서 일어나는 일이 원격에 영향을 주지 않습니다.

> 🔥 **실무 팁**: GitHub CLI(`gh`)를 사용하면 fork + clone + upstream 설정을 한 번에 할 수 있습니다. `gh repo fork --clone original-author/project`만 실행하면 세 단계가 자동으로 처리됩니다.

> 🔥 **실무 팁**: 대규모 모노레포를 clone할 때는 **sparse-checkout**을 고려하세요. 필요한 디렉토리만 체크아웃하여 디스크 사용량과 빌드 시간을 줄일 수 있습니다. 자세한 내용은 [대규모 저장소 관리](../12-troubleshooting/02-large-repos.md)에서 다룹니다.

## 핵심 정리

| 명령어/개념 | 설명 |
|-------------|------|
| `git clone <URL>` | 원격 저장소를 로컬에 완전 복제 |
| `git clone --depth 1` | 최신 커밋만 가져오는 shallow clone |
| `git clone -b <브랜치>` | 특정 브랜치를 체크아웃하며 clone |
| `git clone --recurse-submodules` | 서브모듈까지 함께 clone |
| fork | GitHub 계정에 원격 저장소의 사본을 생성 (플랫폼 기능) |
| `gh repo fork` | GitHub CLI로 fork 수행 |
| fork → clone → upstream | 오픈소스 기여의 표준 워크플로우 |
| `git fetch --unshallow` | shallow clone을 전체 히스토리로 복원 |

## 다음 섹션 미리보기

저장소를 가져오는 방법을 배웠으니, 이제 **변경사항을 주고받는 방법**을 알아볼 차례입니다. 다음 섹션 [push와 pull](./03-push-pull.md)에서는 내 커밋을 원격에 올리고(`push`), 원격의 변경사항을 내려받는(`pull`) 핵심 명령어를 다룹니다.

## 참고 자료

- [Pro Git Book — Working with Remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes) - clone과 원격 저장소 작업 가이드
- [Git 공식 문서 — git-clone](https://git-scm.com/docs/git-clone) - clone 명령어 전체 옵션
- [GitHub Docs — Fork a repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo) - GitHub fork 공식 가이드
- [Atlassian — Git Forks and Upstreams](https://www.atlassian.com/git/tutorials/git-forks-and-upstreams) - fork와 upstream 전략 심화
