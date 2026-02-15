# 설치와 초기 설정

> Git 설치(macOS/Windows/Linux), git config, 터미널 기본

## 개요

Git을 사용하려면 먼저 설치하고, "나는 누구인지" 알려줘야 합니다. 이 섹션에서는 운영체제별 Git 설치 방법과 첫 사용 전 필수 설정을 다룹니다.

**선수 지식**: [Git이란?](./01-what-is-git.md)에서 배운 Git의 기본 개념
**학습 목표**:
- 자신의 운영체제에 Git을 설치할 수 있다
- `git config`로 사용자 정보를 설정할 수 있다
- 설정의 3단계 우선순위(system/global/local)를 이해한다

## 왜 알아야 할까?

Git을 설치하는 것은 간단하지만, **초기 설정을 제대로 하지 않으면** 첫 커밋부터 막히게 됩니다. "Author identity unknown"이라는 에러 메시지를 보고 당황하는 경우가 많은데요, 이 섹션을 따라하면 그런 일은 없을 겁니다. 또한 한 번 설정해두면 거의 다시 건드릴 필요가 없으니, 처음에 제대로 해두는 것이 좋습니다.

## 핵심 개념

### Git 설치하기

> 💡 **비유**: Git 설치는 **새 도구 상자를 사는 것**과 같습니다. 도구 상자를 사서 작업대에 올려놓아야 비로소 도구를 꺼내 쓸 수 있죠.

#### macOS

macOS에는 Xcode Command Line Tools에 Git이 포함되어 있습니다. 터미널을 열고 `git`을 입력하면 자동으로 설치를 안내하기도 합니다.

```bash
# 방법 1: Xcode Command Line Tools (가장 간편)
xcode-select --install

# 방법 2: Homebrew (최신 버전 관리가 편리)
brew install git

# 설치 확인
git --version
```

```output
git version 2.48.0
```

#### Windows

```bash
# 방법 1: winget (Windows 10/11 내장 패키지 관리자, 권장)
winget install --id Git.Git -e --source winget

# 방법 2: Chocolatey
choco install git

# 방법 3: 공식 사이트에서 설치 파일 다운로드
# https://git-scm.com/download/win
```

> 🔥 **실무 팁**: Windows에서는 **Git Bash**가 함께 설치됩니다. Git Bash는 Linux/macOS와 동일한 bash 명령어를 사용할 수 있어서, 크로스 플랫폼 개발 환경에서 매우 유용합니다.

#### Linux

```bash
# Debian / Ubuntu
sudo apt update && sudo apt install git

# Fedora
sudo dnf install git

# Arch Linux
sudo pacman -S git

# 설치 확인
git --version
```

### git config — 나를 소개하기

> 💡 **비유**: `git config`는 **명함을 만드는 과정**입니다. 모든 커밋에 "누가 이 변경을 했는지"가 기록되는데, 명함 없이는 커밋을 남길 수 없습니다.

Git을 설치한 후 가장 먼저 해야 할 일은 **이름과 이메일을 설정**하는 것입니다. 이 정보는 모든 커밋에 포함됩니다.

```bash
# 필수 설정: 이름과 이메일
git config --global user.name "홍길동"
git config --global user.email "gildong@example.com"

# 기본 브랜치 이름을 main으로 설정 (Git 2.28+)
git config --global init.defaultBranch main

# 기본 에디터 설정 (VS Code 사용자)
git config --global core.editor "code --wait"
```

설정이 잘 되었는지 확인해 볼까요?

```bash
# 현재 설정 전체 보기
git config --list

# 특정 설정 값 확인
git config user.name
```

```output
홍길동
```

### 설정의 3단계 우선순위

Git 설정에는 세 가지 범위가 있으며, 좁은 범위가 넓은 범위를 덮어씁니다:

| 범위 | 플래그 | 적용 대상 | 설정 파일 위치 |
|------|--------|----------|---------------|
| System | `--system` | 컴퓨터의 모든 사용자 | `/etc/gitconfig` |
| Global | `--global` | 현재 사용자의 모든 저장소 | `~/.gitconfig` |
| Local | `--local` | 현재 저장소만 | `.git/config` |

> ⚠️ **흔한 오해**: "회사 이메일과 개인 이메일을 하나만 쓸 수 있다" — 사실 **local** 설정으로 저장소마다 다른 이메일을 쓸 수 있습니다. 회사 프로젝트에는 회사 이메일, 개인 프로젝트에는 개인 이메일을 설정하세요.

```bash
# 회사 프로젝트에서만 회사 이메일 사용
cd ~/work/company-project
git config --local user.email "gildong@company.com"
```

### 추천 설정

Git 코어 개발자들이 추천하는 설정 몇 가지를 소개합니다:

```bash
# 컬러 출력 활성화
git config --global color.ui auto

# push 시 현재 브랜치만 push (안전)
git config --global push.default simple

# push 시 자동으로 upstream 설정
git config --global push.autoSetupRemote true

# pull 시 불필요한 머지 커밋 방지
git config --global pull.rebase true

# 이미 해결한 충돌을 기억 (reuse recorded resolution)
git config --global rerere.enabled true

# fetch 시 삭제된 원격 브랜치 자동 정리
git config --global fetch.prune true

# diff 알고리즘을 더 정확한 histogram으로 변경
git config --global diff.algorithm histogram
```

## 실습: 직접 해보기

```bash
# 1. Git 버전 확인
git --version

# 2. 이름과 이메일 설정
git config --global user.name "내이름"
git config --global user.email "내이메일@example.com"

# 3. 기본 브랜치 이름 설정
git config --global init.defaultBranch main

# 4. 설정 확인
git config --global --list

# 5. 설정 파일 직접 열어보기
cat ~/.gitconfig
```

```output
[user]
    name = 내이름
    email = 내이메일@example.com
[init]
    defaultBranch = main
```

## 더 깊이 알아보기

### init.defaultBranch — "master"에서 "main"으로

2020년, 소프트웨어 업계에서는 포용적 언어(inclusive language) 운동의 일환으로 기본 브랜치 이름을 `master`에서 `main`으로 변경하는 움직임이 일어났습니다. GitHub, GitLab, Bitbucket 등 주요 플랫폼이 이를 채택했고, Git 2.28(2020년 7월)부터 `init.defaultBranch` 설정이 추가되었습니다. 오늘날 대부분의 새 프로젝트는 `main`을 기본 브랜치로 사용합니다.

> 💡 **알고 계셨나요?**: Git의 설정 파일은 사실 평범한 텍스트 파일입니다. `git config` 명령어 없이 `~/.gitconfig` 파일을 직접 편집해도 동일한 효과를 얻을 수 있어요.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "Git 설치만 하면 바로 쓸 수 있다" — `user.name`과 `user.email`을 설정하지 않으면 첫 커밋에서 에러가 발생합니다. 설치 후 반드시 이 두 가지를 설정하세요.

```error
Author identity unknown

*** Please tell me who you are.

Run
  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"
```

> 🔥 **실무 팁**: `git config --global credential.helper`를 설정하면 GitHub 비밀번호를 매번 입력하지 않아도 됩니다. macOS는 `osxkeychain`, Windows는 `manager`가 기본 제공됩니다. 자세한 내용은 [SSH와 인증](../04-remote/05-auth.md)에서 다룹니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| `git --version` | 설치된 Git 버전 확인 |
| `git config --global` | 현재 사용자의 모든 저장소에 적용되는 설정 |
| `user.name` / `user.email` | 커밋에 기록되는 작성자 정보 (필수) |
| `init.defaultBranch` | 새 저장소의 기본 브랜치 이름 설정 |
| system → global → local | 설정 우선순위 (local이 가장 높음) |

## 다음 섹션 미리보기

설치와 설정이 끝났으니, 이제 진짜 Git을 사용해볼 시간입니다! 다음 섹션 [첫 번째 저장소](./03-first-repo.md)에서는 `git init`으로 저장소를 만들고, Git의 핵심인 **작업 디렉토리 → 스테이징 → 저장소** 3단계 구조를 배웁니다.

## 참고 자료

- [Pro Git Book — 처음 시작: Git 설정](https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EC%B5%9C%EC%B4%88-%EC%84%A4%EC%A0%95) - 공식 설정 가이드, 한국어 번역 제공
- [Git 공식 문서 — git-config](https://git-scm.com/docs/git-config) - 모든 설정 옵션의 레퍼런스
- [How Core Git Developers Configure Git](https://blog.gitbutler.com/how-git-core-devs-configure-git/) - Git 코어 개발자들의 실제 설정 공유
- [Git 공식 다운로드 페이지](https://git-scm.com/downloads) - 운영체제별 최신 설치 파일
