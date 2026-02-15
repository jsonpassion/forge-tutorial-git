# 오픈소스 기여

> fork → upstream sync → 첫 PR, 기여 가이드라인

## 개요

오픈소스에 기여한다는 건 **전 세계 개발자들과 함께 일하는 것**입니다. 처음에는 막막할 수 있지만, 절차를 알면 생각보다 간단해요. 이번 섹션에서는 오픈소스 프로젝트에 fork해서 코드를 수정하고, 첫 PR을 보내기까지의 전체 과정을 배웁니다.

**선수 지식**: [PR 관리](./03-pr-management.md)에서 배운 머지 전략, [clone과 fork](../04-remote/02-clone-fork.md)의 기본 개념
**학습 목표**:
- fork → clone → 작업 → PR의 오픈소스 기여 워크플로우를 실행할 수 있다
- upstream과 origin의 관계를 이해하고 동기화할 수 있다
- CONTRIBUTING.md, CLA 등 기여 가이드라인을 이해한다
- 좋은 기여자의 에티켓을 안다

## 왜 알아야 할까?

오픈소스 기여는 개발자에게 여러 가치를 줍니다. 첫째, **실력이 늘어요** — 실제 프로덕션 코드를 읽고 수정하면서 배울 점이 많습니다. 둘째, **포트폴리오가 됩니다** — GitHub 기여 기록은 가장 객관적인 이력서예요. 셋째, **커뮤니티와 연결**됩니다 — 유명 프로젝트의 메인테이너에게 코드 리뷰를 받는 경험은 어디서도 할 수 없죠.

전 세계 소프트웨어의 80% 이상이 오픈소스 컴포넌트를 포함하고 있습니다. 내가 쓰는 도구에 직접 기여할 수 있다는 건 개발자의 특권이에요.

## 핵심 개념

### 개념 1: 오픈소스 기여 워크플로우

> 💡 **비유**: 오픈소스 기여는 **공공 도서관의 오타 수정**과 비슷합니다. 도서관 책에서 오타를 발견하면, (1) 책을 복사하고, (2) 복사본에서 오타를 수정한 뒤, (3) 도서관에 "이 수정 사항을 반영해주세요"라고 제출합니다. 도서관 사서(메인테이너)가 확인 후 원본에 반영하는 거죠.

전체 흐름을 단계별로 알아볼게요.

**1단계: 프로젝트 Fork**

```bash
# GitHub CLI로 fork + 로컬 clone 한 번에
gh repo fork owner/awesome-project --clone
```

```output
✓ Created fork your-name/awesome-project
✓ Cloned fork
! Repository owner/awesome-project set as the default repository.
  To learn more about the default repository, run: gh repo set-default --help
```

이 한 줄로 세 가지가 자동으로 설정됩니다:
- `origin` → 내 fork (`your-name/awesome-project`)
- `upstream` → 원본 저장소 (`owner/awesome-project`)
- 로컬에 코드가 클론됨

```bash
# remote 확인
git remote -v
```

```output
origin    https://github.com/your-name/awesome-project.git (fetch)
origin    https://github.com/your-name/awesome-project.git (push)
upstream  https://github.com/owner/awesome-project.git (fetch)
upstream  https://github.com/owner/awesome-project.git (push)
```

**2단계: 최신 상태 동기화**

작업 전에 항상 원본 저장소의 최신 변경 사항을 동기화하세요:

```bash
# 방법 1: gh CLI (가장 간단)
gh repo sync --branch main

# 방법 2: git 명령어
git fetch upstream
git switch main
git merge upstream/main
git push origin main
```

**3단계: 작업 브랜치 생성 & 코드 수정**

```bash
# 브랜치 이름은 작업 내용을 명확히 설명
git switch -c fix/typo-in-readme

# 코드 수정
# ... 에디터에서 수정 ...

# 커밋
git add README.md
git commit -m "Fix typo in installation section"
```

**4단계: Push & PR 생성**

```bash
# 내 fork에 푸시
git push -u origin fix/typo-in-readme

# 원본 저장소에 PR 생성
gh pr create \
  --title "Fix typo in installation section" \
  --body "## 변경 사항
README.md 설치 가이드의 오타를 수정했습니다.

- 'teh' → 'the'
- 'instal' → 'install'

## 관련 이슈
Fixes #156"
```

```output
Creating pull request for your-name:fix/typo-in-readme into owner:main

https://github.com/owner/awesome-project/pull/789
```

**5단계: 리뷰 피드백 대응 & 머지 대기**

메인테이너가 리뷰 후 피드백을 남기면 수정해서 추가 커밋을 푸시합니다. 승인되면 메인테이너가 머지합니다.

**6단계: 정리**

```bash
# 머지 후 동기화 & 브랜치 삭제
gh repo sync --branch main
git switch main
git pull
git branch -d fix/typo-in-readme
```

### 개념 2: upstream 동기화의 중요성

fork는 특정 시점의 **스냅샷**입니다. 원본 저장소는 계속 업데이트되지만, 내 fork는 자동으로 동기화되지 않아요. 정기적으로 동기화하지 않으면 내 코드가 뒤처져서 충돌이 발생합니다.

```bash
# 동기화 상태 확인
git fetch upstream
git log --oneline main..upstream/main
```

```output
a1b2c3d Update API documentation
e4f5g6h Fix security vulnerability
h7i8j9k Add new feature
```

위 출력이 나오면 원본이 3개 커밋만큼 앞서 있다는 뜻입니다. 동기화하세요:

```bash
# 동기화
git switch main
git merge upstream/main
git push origin main
```

> 🔥 **실무 팁**: **작업 시작 전에 항상 동기화**하세요. `gh repo sync --branch main` 한 줄이면 됩니다. 이 습관만으로 충돌의 대부분을 예방할 수 있어요.

### 개념 3: CONTRIBUTING.md — 기여 가이드라인

대부분의 오픈소스 프로젝트에는 **CONTRIBUTING.md** 파일이 있습니다. PR을 보내기 전에 **반드시** 이 파일을 읽으세요. 프로젝트마다 규칙이 다르거든요.

CONTRIBUTING.md에 보통 포함되는 내용:

| 항목 | 설명 |
|------|------|
| **개발 환경 설정** | 빌드 방법, 의존성 설치 |
| **코딩 스타일** | 린트 규칙, 네이밍 컨벤션 |
| **커밋 메시지 규칙** | Conventional Commits 등 |
| **PR 체크리스트** | 테스트 추가, 문서 업데이트 등 |
| **이슈 작성법** | 버그 리포트, 기능 제안 양식 |
| **행동 강령** | 커뮤니티 규범 (Code of Conduct) |
| **CLA/DCO** | 법적 동의 필요 여부 |

### 개념 4: CLA와 DCO

일부 프로젝트에서는 기여 전에 법적 동의가 필요합니다.

**CLA (Contributor License Agreement)**:
- 기여 코드의 **지적 재산권**을 프로젝트에 부여하는 계약
- 보통 웹에서 한 번 서명하면 됨
- Google, Microsoft, Apache Foundation 등 대기업 프로젝트에서 사용

**DCO (Developer Certificate of Origin)**:
- CLA보다 가벼운 방식 — "이 코드를 기여할 권리가 있다"는 선언
- 커밋에 `Signed-off-by:` 줄을 추가

```bash
# DCO 서명이 필요한 프로젝트에서 커밋
git commit -s -m "Fix memory leak in cache module"
```

```output
[fix/memory-leak a1b2c3d] Fix memory leak in cache module
 1 file changed, 3 insertions(+), 1 deletion(-)
```

```bash
# 커밋 메시지에 자동으로 다음이 추가됨:
git log -1
```

```output
Fix memory leak in cache module

Signed-off-by: Your Name <your.email@example.com>
```

| 항목 | CLA | DCO |
|------|-----|-----|
| 무게감 | 정식 법적 계약 | 가벼운 자기 선언 |
| 방식 | 웹에서 한 번 서명 | 매 커밋에 `-s` 옵션 |
| 사용처 | Google, Microsoft, Apache | Linux Kernel, GitLab, CNCF |

### 개념 5: 첫 기여를 위한 프로젝트 찾기

처음부터 큰 프로젝트에 기여하기 어려울 수 있어요. **"Good First Issue"** 라벨이 붙은 이슈부터 시작하는 게 좋습니다.

```bash
# GitHub CLI로 good first issue 검색
gh search issues --label "good first issue" --state open --language javascript --sort created

# 특정 프로젝트에서 검색
gh issue list --repo facebook/react --label "good first issue" --state open
```

기여 프로젝트를 찾을 수 있는 곳:

- **goodfirstissue.dev** — 인기 오픈소스의 쉬운 이슈를 모아둔 사이트
- **GitHub Explore** — 트렌딩 저장소와 토픽 탐색
- **firsttimersonly.com** — 첫 기여자를 위한 리소스 모음
- **내가 사용하는 도구** — 매일 쓰는 라이브러리가 가장 좋은 시작점!

> 💡 **알고 계셨나요?**: GitHub에서 "Good First Issue" 라벨은 특별한 의미가 있습니다. 이 라벨이 붙은 이슈는 저장소의 **Contribute 페이지**(`github.com/owner/repo/contribute`)에 자동으로 노출되어요. 메인테이너가 신규 기여자를 위해 의도적으로 준비한 이슈입니다.

## 실습: 오픈소스 기여 연습

실제 오픈소스에 바로 기여하기 전에, 자신의 저장소로 연습해봅시다.

```bash
# 1. 자신의 저장소를 fork처럼 활용 (연습용)
# 또는 github.com/firstcontributions/first-contributions를 fork

# first-contributions로 연습하기
gh repo fork firstcontributions/first-contributions --clone
cd first-contributions

# 2. 브랜치 생성
git switch -c add-your-name

# 3. Contributors.md에 자신의 이름 추가 (에디터에서 수정)
# Contributors.md 파일을 열고 알파벳 순서에 맞게 이름 추가

# 4. 커밋 & 푸시
git add Contributors.md
git commit -m "Add my-name to Contributors list"
git push -u origin add-your-name

# 5. PR 생성
gh pr create \
  --title "Add my-name to Contributors list" \
  --body "첫 오픈소스 기여 연습입니다!"
```

> 💡 **알고 계셨나요?**: [first-contributions](https://github.com/firstcontributions/first-contributions)는 오픈소스 기여를 연습하기 위한 프로젝트로, 49,000개 이상의 fork와 수만 명의 기여자가 있습니다. 실제로 기여 절차를 경험해볼 수 있는 가장 좋은 출발점이에요.

## 더 깊이 알아보기

### 오픈소스의 역사

"오픈소스(Open Source)"라는 용어는 1998년 Christine Peterson이 만들었습니다. 같은 해 **Open Source Initiative(OSI)**가 설립되면서 오픈소스 운동이 본격화되었죠.

하지만 코드를 공유하는 문화는 그 전부터 있었습니다. 리처드 스톨먼의 **GNU 프로젝트(1983)**와 **자유 소프트웨어 재단(1985)**이 그 시작이에요. 리눅스 토르발즈가 1991년에 공개한 **리눅스 커널**은 오픈소스의 가장 상징적인 프로젝트가 되었습니다.

GitHub이 2008년에 등장하면서 오픈소스 기여의 문턱이 크게 낮아졌어요. 이전에는 메일링 리스트로 패치를 보내야 했는데, GitHub의 fork-and-PR 워크플로우가 이 과정을 누구나 할 수 있도록 단순화한 거죠. 2018년 Microsoft가 GitHub을 75억 달러에 인수했을 때 많은 개발자가 우려했지만, 오히려 GitHub은 더 많은 기능을 무료로 제공하기 시작했습니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "오픈소스 기여는 코드를 작성해야만 한다" — 전혀 아닙니다! 문서 수정, 번역, 버그 리포트, 테스트 작성, 디자인 개선 — 이 모든 것이 기여입니다. 특히 문서 기여는 메인테이너가 가장 환영하는 기여 중 하나예요. 코드 한 줄 없이도 오픈소스에 기여할 수 있습니다.

> 🔥 **실무 팁**: PR을 보내기 전에 **이슈에 먼저 댓글**을 달아보세요. "이 이슈를 해결해보고 싶은데, 제가 작업해도 될까요?"라고 물어보면 메인테이너가 방향을 안내해줄 수 있어요. 갑자기 큰 PR을 보내면 방향이 맞지 않아 거절될 수 있습니다.

> 🔥 **실무 팁**: 기여가 거절되어도 **개인적으로 받아들이지 마세요**. 메인테이너는 코드 품질, 프로젝트 방향, 유지보수 부담 등 다양한 이유로 PR을 닫을 수 있습니다. 정중하게 이유를 물어보고 다음에 참고하면 됩니다.

> ⚠️ **흔한 오해**: "main 브랜치에서 바로 작업해도 된다" — fork 에서도 반드시 **별도 브랜치**를 만들어서 작업하세요. main에서 직접 작업하면 upstream 동기화가 복잡해지고, 여러 PR을 동시에 진행하기 어렵습니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| Fork | 원본 저장소를 내 계정으로 복사 |
| upstream | 원본 저장소 (fork의 원본) |
| origin | 내 fork (푸시 대상) |
| `gh repo fork --clone` | fork + clone + remote 설정 한 번에 |
| `gh repo sync` | fork를 upstream과 동기화 |
| CONTRIBUTING.md | 프로젝트별 기여 규칙과 절차 안내 |
| CLA | 정식 기여 라이선스 계약 |
| DCO | `git commit -s`로 하는 가벼운 자기 선언 |
| Good First Issue | 신규 기여자를 위한 쉬운 이슈 라벨 |

## 다음 섹션 미리보기

Ch6에서 PR의 모든 것을 배웠습니다 — 워크플로우, 코드 리뷰, 관리 기법, 그리고 오픈소스 기여까지! 다음 챕터 [Ch7. Issues와 프로젝트 관리](../07-issues-projects/01-issues.md)에서는 GitHub의 **이슈 트래킹**과 **프로젝트 보드**를 활용해 팀의 작업을 체계적으로 관리하는 방법을 배웁니다.

## 참고 자료

- [GitHub Docs — 프로젝트에 기여하기](https://docs.github.com/en/get-started/exploring-projects-on-github/contributing-to-a-project) - 오픈소스 기여 공식 가이드
- [Open Source Guide — 기여 방법](https://opensource.guide/how-to-contribute/) - GitHub이 운영하는 오픈소스 가이드
- [GitHub Docs — Fork 동기화](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork) - fork 동기화 공식 문서
- [first-contributions](https://github.com/firstcontributions/first-contributions) - 첫 기여 연습 프로젝트
- [goodfirstissue.dev](https://goodfirstissue.dev/) - 초보자 친화적 이슈 모음
