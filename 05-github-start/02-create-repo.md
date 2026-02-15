# 저장소 만들기

> 레포 생성, README, LICENSE 선택, .gitignore 템플릿

## 개요

GitHub에서 프로젝트를 시작하려면 **저장소(repository)**를 만들어야 합니다. 그런데 저장소를 만들 때 README, LICENSE, .gitignore 같은 옵션이 나오는데, 뭘 선택해야 할지 막막하셨죠? 이번 섹션에서는 저장소를 올바르게 생성하고, 프로젝트의 **첫인상을 결정하는 파일들**을 설정하는 방법을 배웁니다.

**선수 지식**: [GitHub 계정과 프로필](./01-github-account.md), [첫 번째 저장소](../01-git-start/03-first-repo.md)
**학습 목표**:
- GitHub 웹과 CLI 두 가지 방법으로 저장소를 생성할 수 있다
- README.md를 효과적으로 작성할 수 있다
- 프로젝트에 맞는 LICENSE를 선택할 수 있다
- .gitignore 템플릿을 활용해 불필요한 파일을 제외할 수 있다

## 왜 알아야 할까?

저장소의 첫 세팅이 프로젝트의 **전문성**을 결정합니다. LICENSE 없는 오픈소스는 법적으로 "사용 불가"이고, .gitignore 없이 시작하면 `node_modules/`나 `.env` 같은 파일이 저장소에 올라가서 나중에 지우기가 정말 번거롭습니다. 처음부터 제대로 세팅하면 이런 문제를 사전에 방지할 수 있어요.

## 핵심 개념

### 개념 1: 저장소 생성 — 웹 vs CLI

> 💡 **비유**: 저장소 만들기는 **새 노트 한 권을 펼치는 것**과 같습니다. 노트를 펼치면서 표지에 이름(README)을 쓰고, 사용 규칙(LICENSE)을 정하고, "이 페이지는 안 쓸 거야(.gitignore)"라고 표시해두는 거죠.

#### 방법 1: GitHub 웹에서 생성

1. GitHub 우측 상단 **"+"** → **"New repository"** 클릭
2. 저장소 이름, 설명(Description) 입력
3. 공개(Public) / 비공개(Private) 선택
4. 초기화 옵션 선택:
   - **Add a README file**: 체크하면 README.md 자동 생성
   - **Add .gitignore**: 언어/프레임워크별 템플릿 선택
   - **Choose a license**: 라이선스 파일 자동 생성
5. **"Create repository"** 클릭

#### 방법 2: GitHub CLI로 생성

```bash
# 대화형으로 저장소 만들기
gh repo create

# 한 줄로 공개 저장소 만들기 + 로컬에 클론
gh repo create my-project --public --clone

# 비공개 저장소 + README 포함
gh repo create my-private-app --private --add-readme --clone

# 기존 로컬 저장소를 GitHub에 올리기
cd existing-project
gh repo create --source=. --public --push
```

#### 방법 3: 로컬에서 먼저 만들고 연결

```bash
# 로컬에서 저장소 초기화
mkdir my-project && cd my-project
git init

# 파일 만들고 첫 커밋
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"

# GitHub에 원격 저장소 만들고 연결
gh repo create my-project --public --source=. --push
```

> 🔥 **실무 팁**: `gh repo create --source=. --push`는 현재 디렉토리의 Git 저장소를 GitHub에 올리는 가장 빠른 방법입니다. origin 설정부터 push까지 한 번에 처리되거든요.

### 개념 2: README.md — 프로젝트의 얼굴

> 💡 **비유**: README는 **상품의 포장지**입니다. 아무리 좋은 내용물(코드)이 들어 있어도, 포장(README)이 없으면 사람들은 뭐가 들었는지 모르고 지나칩니다.

README.md는 저장소에 방문한 사람이 **가장 먼저 보는 파일**입니다. GitHub는 저장소 메인 페이지에 README.md 내용을 자동으로 렌더링합니다.

**좋은 README의 5가지 필수 요소**:

1. **프로젝트 이름과 한 줄 설명**: 이게 뭔지 즉시 이해할 수 있어야 함
2. **설치/실행 방법**: 복사-붙여넣기로 바로 실행 가능한 명령어
3. **사용 예시**: 스크린샷이나 코드 예제
4. **기여 방법**: 오픈소스라면 어떻게 기여할 수 있는지
5. **라이선스**: 어떤 조건으로 사용할 수 있는지

좋은 README 템플릿 예시:

> **# 프로젝트 이름**
>
> 프로젝트에 대한 간단한 설명을 한두 줄로 작성합니다.
>
> **## 설치** → `npm install my-project`
>
> **## 사용법** → `npx my-project --help`
>
> **## 기여하기** → PR은 언제나 환영합니다!
>
> **## 라이선스** → MIT License

> ⚠️ **흔한 오해**: "README는 나중에 쓰면 된다" — README 없는 저장소는 설명서 없는 조립 가구와 같습니다. 프로젝트를 시작할 때 최소한의 README를 작성하고, 프로젝트가 발전하면서 함께 업데이트하는 게 좋은 습관이에요.

### 개념 3: LICENSE — 내 코드의 사용 규칙

> 💡 **비유**: LICENSE는 **집의 방문 규칙**입니다. "자유롭게 구경하세요(MIT)", "구경은 가능하지만 변경은 안 돼요(소스 공개 조건부)", "나의 허락 없이는 출입 금지(All Rights Reserved)" 같은 규칙이죠.

GitHub에 코드를 올렸다고 해서 다른 사람이 자유롭게 사용할 수 있는 건 아닙니다. **LICENSE 파일이 없으면 기본적으로 저작권법에 의해 보호**되어, 다른 사람은 법적으로 사용할 수 없어요.

**주요 오픈소스 라이선스 비교**:

| 라이선스 | 자유도 | 핵심 조건 | 대표 프로젝트 |
|----------|--------|-----------|---------------|
| **MIT** | 매우 높음 | 저작권 표시만 하면 자유 사용 | React, Node.js, jQuery |
| **Apache 2.0** | 높음 | 저작권 표시 + 변경 사항 명시 | Android, Kubernetes, Swift |
| **GPL 3.0** | 조건부 | 파생 코드도 GPL로 공개해야 함 | Linux Kernel, WordPress |
| **BSD 2-Clause** | 매우 높음 | 저작권 표시만 하면 자유 사용 | FreeBSD, Nginx |

```bash
# GitHub CLI로 라이선스 목록 확인
gh api licenses --jq '.[].key'
```

```output
agpl-3.0
apache-2.0
bsd-2-clause
bsd-3-clause
...
mit
...
```

```bash
# 저장소 생성 시 라이선스 지정
gh repo create my-oss-project --public --license mit --clone
```

**라이선스 선택 가이드**:
- **"최대한 자유롭게 쓰게 하고 싶다"** → MIT 또는 BSD
- **"상업적 사용도 OK, 특허 보호도 필요"** → Apache 2.0
- **"파생 코드도 오픈소스로 유지되길 원한다"** → GPL 3.0
- **"모르겠다, 일단 자유롭게"** → MIT (가장 많이 사용)

> 💡 **알고 계셨나요?**: [choosealicense.com](https://choosealicense.com)은 GitHub에서 직접 만든 사이트로, 자신에게 맞는 라이선스를 쉽게 고를 수 있도록 도와줍니다. GitHub 저장소 생성 시 라이선스 선택 화면에서도 이 사이트를 안내하고 있어요.

### 개념 4: .gitignore — 추적하지 않을 파일 지정

> 💡 **비유**: .gitignore는 **이삿짐 센터에 "이건 안 가져가셔도 돼요"라고 적어둔 목록**입니다. 임시 파일, 빌드 산출물, 비밀 설정 파일 등을 Git이 무시하도록 해주죠.

[.gitignore 섹션](../02-file-history/01-gitignore.md)에서 기본 사용법을 배웠죠? GitHub에서 저장소를 만들 때 **프로그래밍 언어별 .gitignore 템플릿**을 자동으로 추가할 수 있습니다.

```bash
# GitHub CLI로 .gitignore 템플릿 목록 확인
gh api gitignore/templates --jq '.[]' | head -20
```

```output
Actionscript
Ada
Agda
Android
...
Node
...
Python
...
```

**자주 쓰는 .gitignore 템플릿**:

| 언어/프레임워크 | 주요 제외 항목 |
|----------------|---------------|
| **Node** | `node_modules/`, `dist/`, `.env` |
| **Python** | `__pycache__/`, `*.pyc`, `venv/`, `.env` |
| **Swift** | `.build/`, `DerivedData/`, `*.xcuserstate` |
| **Java** | `*.class`, `target/`, `.gradle/` |

```bash
# gitignore.io API로 맞춤 .gitignore 생성
# Node.js + macOS + VS Code 조합
curl -sL https://www.toptal.com/developers/gitignore/api/node,macos,visualstudiocode > .gitignore
```

> ⚠️ **흔한 오해**: ".gitignore는 이미 추적 중인 파일도 무시한다" — .gitignore는 **아직 추적되지 않은 파일**에만 적용됩니다. 이미 커밋된 파일을 무시하려면 먼저 `git rm --cached` 로 추적을 해제해야 해요.

### 개념 5: 저장소 설정 다듬기

저장소를 만든 후 추가로 설정하면 좋은 것들이 있습니다:

**Description과 Topics**:
- **Description**: 한 줄 설명 (검색 결과에 표시됨)
- **Topics**: 태그처럼 최대 20개 키워드 추가 (예: `react`, `typescript`, `tutorial`)
- GitHub 검색과 Explore에서 토픽으로 저장소를 찾을 수 있어요

```bash
# 저장소 설명 추가
gh repo edit --description "A modern web app built with React and TypeScript"

# 토픽 추가
gh repo edit --add-topic react --add-topic typescript --add-topic webapp
```

**Social Preview 이미지**:
- 저장소 링크를 슬랙이나 트위터에 공유할 때 표시되는 미리보기 이미지
- Settings → Social preview에서 업로드 (권장 크기: 1280x640px)

**기본 브랜치 이름**:
- 새 저장소의 기본 브랜치 이름은 `main`이 표준
- Settings → General → Default branch에서 변경 가능

## 실습: 직접 해보기

```bash
# 1. GitHub CLI로 저장소 만들기
gh repo create my-first-repo --public --add-readme --license mit --gitignore Node --clone

# 2. 저장소로 이동
cd my-first-repo

# 3. 생성된 파일 확인
ls -la
```

```output
.git/
.gitignore
LICENSE
README.md
```

```bash
# 4. README 수정 (에디터로 아래 내용을 작성)
# ── README.md 내용 ──
# # My First Repo
# GitHub에서 만든 첫 번째 저장소입니다.
# ## 기술 스택
# - Node.js / Express
# ## 시작하기
# npm install && npm start
# ## 라이선스
# MIT License
```

원하는 에디터(VS Code, nano 등)로 README.md를 수정하세요:

```bash
# VS Code로 열기
code README.md

# 또는 nano로 열기
nano README.md
```

```bash
# 5. 커밋 & 푸시
git add README.md
git commit -m "Update README with project info"
git push

# 6. 저장소 설정 추가
gh repo edit --description "My first GitHub repository" --add-topic nodejs --add-topic tutorial

# 7. 브라우저에서 확인
gh browse
```

## 더 깊이 알아보기

### 저장소 구조의 진화

초기 오픈소스 프로젝트들은 README라는 이름조차 통일되지 않았습니다. `READ.ME`, `readme.txt`, `00-READ-THIS` 등 다양한 이름이 혼재했죠. 1990년대부터 `README`라는 이름이 관례로 굳어졌는데, 대문자로 쓰는 이유가 재미있습니다 — 당시 파일 목록이 알파벳순으로 정렬되었는데, **대문자가 소문자보다 먼저 정렬**되어서 `README`가 항상 목록 상단에 위치했기 때문입니다.

GitHub가 2008년에 등장하면서 README + LICENSE + .gitignore라는 **"저장소 3종 세트"**가 사실상 표준이 되었고, 이후 CONTRIBUTING.md, CODE_OF_CONDUCT.md, SECURITY.md 같은 파일도 커뮤니티 관례로 자리잡았습니다.

> 💡 **알고 계셨나요?**: GitHub의 .gitignore 템플릿은 [github/gitignore](https://github.com/github/gitignore) 저장소에서 관리됩니다. 이 저장소는 무려 **16만 개 이상의 Star**를 받은 GitHub에서 가장 인기 있는 저장소 중 하나예요. 커뮤니티가 직접 템플릿을 기여하고 관리하고 있습니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "Private 저장소는 완전히 안전하다" — Private 저장소는 초대된 사람만 볼 수 있지만, `.env`나 API 키 같은 민감한 정보는 절대 커밋하면 안 됩니다. 나중에 Public으로 전환하거나, 협업자가 fork할 수 있기 때문이에요.

> 🔥 **실무 팁**: 저장소를 만들 때 `.gitignore`와 `LICENSE`를 함께 생성하면, GitHub이 자동으로 첫 커밋을 만들어줍니다. 이렇게 하면 빈 저장소에 `git push`할 때 발생하는 "empty repository" 관련 혼란을 피할 수 있어요.

> 🔥 **실무 팁**: 팀 프로젝트에서는 저장소 생성 시 **Repository template** 기능을 활용하세요. `.github/` 폴더, CI 설정, 공통 .gitignore 등이 포함된 템플릿 저장소를 만들어두면, 새 프로젝트를 시작할 때마다 동일한 구조로 빠르게 세팅할 수 있습니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| `gh repo create` | GitHub CLI로 저장소 생성 (--public, --private 옵션) |
| README.md | 프로젝트 소개 문서, 저장소의 얼굴 |
| LICENSE | 코드 사용 조건 명시 (MIT, Apache, GPL 등) |
| .gitignore | Git이 추적하지 않을 파일 패턴 지정 |
| Description | 저장소 한 줄 설명, 검색 결과에 표시 |
| Topics | 태그 키워드, 최대 20개, 검색/탐색에 활용 |
| `gh repo edit` | 저장소 설명, 토픽, 설정 변경 |
| Social Preview | 링크 공유 시 표시되는 미리보기 이미지 |

## 다음 섹션 미리보기

저장소를 만들었으니 이제 더 효율적으로 GitHub을 사용해볼까요? 다음 섹션 [GitHub CLI (gh)](./03-github-cli.md)에서는 브라우저를 열지 않고도 터미널에서 GitHub의 거의 모든 기능을 사용하는 방법을 배웁니다. 저장소 관리, 이슈 생성, PR 작업까지 CLI 하나로!

## 참고 자료

- [GitHub Docs — 저장소 만들기](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository) - 저장소 생성 공식 가이드
- [choosealicense.com](https://choosealicense.com) - GitHub이 만든 라이선스 선택 도우미
- [github/gitignore](https://github.com/github/gitignore) - 공식 .gitignore 템플릿 모음
- [Pro Git Book — GitHub 시작하기](https://git-scm.com/book/en/v2/GitHub-Account-Setup-and-Configuration) - Git 공식 가이드의 GitHub 섹션
