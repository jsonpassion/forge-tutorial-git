# GitHub CLI (gh)

> gh 설치, 인증, repo/issue/pr 기본 명령어

## 개요

지금까지 GitHub에서 저장소를 만들거나 설정을 바꿀 때 브라우저를 열었죠? **GitHub CLI(gh)**를 사용하면 터미널에서 바로 GitHub의 거의 모든 기능을 사용할 수 있습니다. 저장소 관리, 이슈 생성, PR 작성까지 — 터미널을 떠나지 않고도 가능합니다.

**선수 지식**: [저장소 만들기](./02-create-repo.md), [SSH와 인증](../04-remote/05-auth.md)
**학습 목표**:
- GitHub CLI를 설치하고 인증할 수 있다
- `gh repo` 명령어로 저장소를 관리할 수 있다
- `gh issue`로 이슈를 생성하고 조회할 수 있다
- `gh pr`로 Pull Request를 생성하고 관리할 수 있다

## 왜 알아야 할까?

개발 중에 "이슈 하나 만들어야지" 하면서 브라우저를 열고, GitHub에 로그인하고, 저장소를 찾고... 이 과정이 번거롭다고 느낀 적 있나요? GitHub CLI를 쓰면 **터미널에서 3초 만에** 이슈를 만들고, PR을 생성하고, 코드 리뷰를 확인할 수 있습니다. 특히 키보드에서 손을 떼지 않고 작업 흐름을 유지할 수 있어서, 생산성이 크게 올라갑니다.

## 핵심 개념

### 개념 1: 설치와 인증

> 💡 **비유**: GitHub CLI는 **GitHub의 리모컨**입니다. TV(GitHub 웹사이트) 앞까지 걸어가서 버튼을 누르는 대신, 소파(터미널)에서 리모컨 하나로 모든 걸 조작할 수 있죠.

#### 설치

```bash
# macOS (Homebrew)
brew install gh

# Windows (winget)
winget install --id GitHub.cli

# Windows (Chocolatey)
choco install gh

# Linux (Debian/Ubuntu)
sudo apt install gh

# Linux (Fedora)
sudo dnf install gh
```

```bash
# 설치 확인
gh --version
```

```output
gh version 2.65.0 (2025-01-15)
https://github.com/cli/cli/releases/tag/v2.65.0
```

#### 인증

설치 후 가장 먼저 할 일은 **GitHub 계정과 연결(인증)**하는 것입니다:

```bash
# 대화형 인증 시작
gh auth login
```

```console
? What account do you want to log into?  GitHub.com
? What is your preferred protocol for Git operations?  SSH
? Upload your SSH public key to your GitHub account?  Yes
? Title for your SSH key:  My Laptop
? How would you like to authenticate GitHub CLI?  Login with a web browser

! First copy your one-time code: XXXX-XXXX
Press Enter to open github.com in your browser...
✓ Authentication complete.
- gh config set -h github.com git_protocol ssh
✓ Configured git protocol
✓ Uploaded the SSH key to your GitHub account
✓ Logged in as octocat
```

```bash
# 인증 상태 확인
gh auth status
```

```output
github.com
  ✓ Logged in to github.com account octocat (keyring)
  - Active account: true
  - Git operations protocol: ssh
  - Token: gho_************************************
  - Token scopes: 'gist', 'read:org', 'repo', 'workflow'
```

> 🔥 **실무 팁**: `gh auth login`은 SSH 키 업로드도 함께 처리할 수 있습니다. [SSH와 인증](../04-remote/05-auth.md)에서 수동으로 설정하는 방법을 배웠지만, gh auth login이 이를 한 번에 해결해주니 훨씬 편리하죠.

### 개념 2: gh repo — 저장소 관리

```bash
# 저장소 만들기 (대화형)
gh repo create

# 공개 저장소 만들기 + 클론
gh repo create my-app --public --clone

# 현재 디렉토리를 GitHub에 올리기
gh repo create --source=. --public --push

# 저장소 클론
gh repo clone owner/repo-name

# 저장소 정보 보기
gh repo view owner/repo-name

# 현재 저장소를 브라우저에서 열기
gh browse

# 저장소 포크
gh repo fork owner/repo-name --clone
```

```bash
# 저장소 목록 보기
gh repo list
```

```output
octocat/my-app           My awesome application        public   5m
octocat/dotfiles         Personal configuration files   public   2d
octocat/private-project  Internal tool                  private  1w
```

```bash
# 저장소 설정 변경
gh repo edit --description "New description" --add-topic react
```

```bash
# ⚠️ 주의: 저장소 삭제는 되돌릴 수 없습니다!
# 모든 코드, 이슈, PR, Wiki가 영구 삭제됩니다
gh repo delete my-old-repo --yes
```

> ⚠️ **흔한 오해**: "`gh repo delete`로 삭제한 저장소를 복구할 수 있다" — **불가능합니다.** GitHub은 삭제된 저장소를 복구해주지 않습니다. 삭제 전에 반드시 로컬 백업이 있는지 확인하세요.

> ⚠️ **흔한 오해**: "`gh repo clone`과 `git clone`은 같다" — 기능은 비슷하지만, `gh repo clone`은 **단축 형식**(owner/repo)을 지원하고, fork 감지 시 upstream도 자동 설정해줍니다. `git clone`은 전체 URL이 필요하죠.

### 개념 3: gh issue — 이슈 관리

> 💡 **비유**: 이슈는 **프로젝트의 할 일 목록(TODO)**입니다. 버그 신고, 기능 제안, 질문 등을 체계적으로 관리하는 도구인데, gh로 터미널에서 바로 만들 수 있어요.

```bash
# 이슈 목록 보기
gh issue list
```

```output
Showing 3 of 3 open issues in octocat/my-app

ID    TITLE                    LABELS     UPDATED
#12   Fix login redirect bug   bug        about 2 hours ago
#11   Add dark mode support    feature    about 1 day ago
#10   Update documentation     docs       about 3 days ago
```

```bash
# 이슈 만들기 (대화형)
gh issue create

# 한 줄로 이슈 만들기
gh issue create --title "Fix login bug" --body "로그인 후 리다이렉트가 작동하지 않습니다"

# 라벨과 담당자 지정
gh issue create \
  --title "Add search feature" \
  --body "검색 기능을 추가해야 합니다" \
  --label "feature,priority:high" \
  --assignee "@me"

# 특정 이슈 상세 보기
gh issue view 12

# 이슈 상태 변경
gh issue close 12
gh issue reopen 12

# 이슈에 코멘트 추가
gh issue comment 12 --body "수정 완료했습니다. PR #15를 확인해주세요."

# 브라우저에서 이슈 열기
gh issue view 12 --web
```

### 개념 4: gh pr — Pull Request 관리

> 💡 **비유**: PR(Pull Request)은 **동료에게 보내는 검토 요청서**입니다. "제가 이렇게 고쳤는데, 확인해주시겠어요?" — 이 과정을 터미널에서 바로 할 수 있습니다.

```bash
# PR 만들기 (대화형)
gh pr create

# 한 줄로 PR 만들기
gh pr create --title "Fix login bug" --body "로그인 리다이렉트 수정"

# 리뷰어 지정
gh pr create \
  --title "Add search feature" \
  --body "검색 기능 구현" \
  --reviewer teammate1,teammate2 \
  --base main

# Draft PR 만들기 (아직 리뷰 준비 전)
gh pr create --draft --title "WIP: New feature"
```

```bash
# PR 목록 보기
gh pr list
```

```output
Showing 2 of 2 open pull requests in octocat/my-app

ID    TITLE               BRANCH         UPDATED
#15   Fix login bug       fix/login      about 1 hour ago
#14   Add dark mode       feature/dark   about 2 days ago
```

```bash
# PR 상세 보기
gh pr view 15

# PR의 변경 사항 확인 (diff)
gh pr diff 15

# PR 체크아웃 (로컬에서 테스트)
gh pr checkout 15

# PR 머지
gh pr merge 15

# 머지 방법 선택
gh pr merge 15 --squash    # Squash and merge
gh pr merge 15 --rebase    # Rebase and merge
gh pr merge 15 --merge     # Create a merge commit

# PR 상태 확인 (CI 결과 포함)
gh pr checks 15
```

```output
All checks were successful
0 cancelled, 0 failing, 0 pending, 3 successful

✓  Build and Test   2m30s  https://github.com/...
✓  Lint             45s    https://github.com/...
✓  Type Check       1m12s  https://github.com/...
```

> 🔥 **실무 팁**: `gh pr checkout`은 정말 강력합니다. 동료의 PR을 리뷰할 때, 브라우저에서 코드를 읽는 것보다 **로컬에서 직접 실행**해보는 게 훨씬 정확하거든요. `gh pr checkout 15`로 해당 브랜치를 즉시 로컬에 가져올 수 있습니다.

### 개념 5: 유용한 추가 명령어

```bash
# GitHub 상태 페이지 확인 (서비스 장애 여부)
gh status

# 자신에게 할당된 알림 확인
gh api notifications --jq '.[].subject.title' | head -5

# API 직접 호출 (고급)
gh api repos/octocat/my-app --jq '.stargazers_count'

# gist 만들기
gh gist create file.txt --public --desc "My code snippet"

# 확장 기능 설치
gh extension install dlvhdr/gh-dash
```

```bash
# 자주 쓰는 별칭(alias) 설정
gh alias set prc 'pr create --fill'
gh alias set prv 'pr view --web'
gh alias set il 'issue list'

# 별칭 사용
gh prc    # = gh pr create --fill
gh prv    # = gh pr view --web
gh il     # = gh issue list
```

> 🔥 **실무 팁**: `gh pr create --fill`은 커밋 메시지에서 PR 제목과 본문을 자동으로 채워줍니다. 커밋 메시지를 잘 작성했다면, PR 생성이 **한 줄 명령어**로 끝나죠.

## 실습: 직접 해보기

```bash
# 1. gh 인증 상태 확인
gh auth status

# 2. 실습 저장소 만들기
gh repo create gh-practice --public --add-readme --clone
cd gh-practice

# 3. 이슈 만들기
gh issue create --title "첫 번째 이슈" --body "GitHub CLI로 만든 이슈입니다"

# 4. 이슈 목록 확인
gh issue list

# 5. 브랜치 만들고 변경 사항 커밋
git switch -c feature/hello
echo "Hello from GitHub CLI!" > hello.txt
git add hello.txt
git commit -m "Add hello.txt"
git push -u origin feature/hello

# 6. PR 만들기
gh pr create --title "Add hello.txt" --body "GitHub CLI 실습용 PR입니다"

# 7. PR 목록 확인
gh pr list

# 8. PR을 브라우저에서 확인
gh pr view --web
```

## 더 깊이 알아보기

### GitHub CLI의 탄생

GitHub CLI는 2020년 2월에 베타로 출시되었습니다. 사실 그 전에도 **hub**라는 비공식 CLI 도구가 있었는데, GitHub 공동 창립자인 Chris Wanstrath가 직접 만든 것이었어요. hub가 인기를 끌자, GitHub는 아예 **공식 CLI**를 새로 개발하기로 결정했습니다.

gh의 설계 철학은 명확합니다 — **"GitHub을 커맨드 라인으로 가져오자"**. Git 명령어를 대체하는 게 아니라, GitHub 플랫폼 고유 기능(이슈, PR, Actions, 릴리스 등)을 터미널에서 사용할 수 있게 하는 것이죠. 그래서 `git push`는 그대로 `git push`이지만, PR을 만들 때는 `gh pr create`를 쓰는 겁니다.

> 💡 **알고 계셨나요?**: GitHub CLI는 Go 언어로 작성되었습니다. GitHub의 여러 내부 도구가 Go로 만들어져 있는데, 크로스 플랫폼 바이너리를 쉽게 빌드할 수 있다는 장점 때문이에요. gh의 소스 코드도 [cli/cli](https://github.com/cli/cli)에서 오픈소스로 공개되어 있습니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "GitHub CLI가 있으면 Git이 필요 없다" — GitHub CLI는 **GitHub 플랫폼 기능**(이슈, PR, 릴리스, Actions)을 다루는 도구입니다. `add`, `commit`, `push`, `merge` 같은 **Git 핵심 명령어는 여전히 Git을 사용**합니다. 둘은 보완 관계예요.

> 🔥 **실무 팁**: `gh`는 **GitHub Actions의 워크플로우 실행**도 관리할 수 있습니다. `gh run list`, `gh run view`, `gh run watch`로 CI/CD 결과를 터미널에서 실시간으로 확인할 수 있어요. 이 내용은 [Ch10. GitHub Actions](../10-github-actions/01-actions-intro.md)에서 더 자세히 다룹니다.

> 🔥 **실무 팁**: 여러 GitHub 계정(개인 + 회사)을 사용한다면, `gh auth switch` 명령어로 계정을 전환할 수 있습니다. 먼저 `gh auth login`으로 각 계정을 등록해두면, `gh auth switch`로 빠르게 전환 가능합니다.

## 핵심 정리

| 명령어 | 설명 |
|--------|------|
| `gh auth login` | GitHub 계정 인증 |
| `gh auth status` | 인증 상태 확인 |
| `gh repo create` | 저장소 생성 |
| `gh repo clone` | 저장소 클론 (단축 형식 지원) |
| `gh browse` | 현재 저장소를 브라우저에서 열기 |
| `gh issue create` | 이슈 생성 |
| `gh issue list` | 이슈 목록 조회 |
| `gh pr create` | Pull Request 생성 |
| `gh pr checkout` | PR 브랜치를 로컬에 체크아웃 |
| `gh pr merge` | PR 머지 (squash/rebase/merge 선택) |
| `gh alias set` | 별칭(alias) 등록 |

## 다음 섹션 미리보기

GitHub CLI로 이슈와 PR을 다루다 보면, **마크다운(Markdown)**으로 내용을 작성해야 하는 상황이 많아집니다. 다음 섹션 [Markdown 작성법](./04-markdown.md)에서는 GitHub Flavored Markdown의 문법과, README부터 이슈/PR까지 효과적으로 문서를 작성하는 전략을 배웁니다.

## 참고 자료

- [GitHub CLI 공식 매뉴얼](https://cli.github.com/manual/) - 모든 명령어와 옵션 레퍼런스
- [GitHub CLI 퀵스타트](https://docs.github.com/en/github-cli/github-cli/quickstart) - 공식 시작 가이드
- [cli/cli GitHub 저장소](https://github.com/cli/cli) - gh 소스 코드와 릴리스 노트
- [GitHub CLI 예제 모음](https://cli.github.com/manual/examples) - 실전 활용 예시
