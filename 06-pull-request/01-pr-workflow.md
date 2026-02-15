# PR 워크플로우

> 브랜치 → PR → 리뷰 → 머지, PR 작성법

## 개요

코드를 작성했으면 이제 팀에게 **"이 코드 좀 봐주세요"**라고 요청할 차례입니다. 이것이 바로 **Pull Request(PR)**예요. 이번 섹션에서는 브랜치에서 시작해 PR을 만들고, 리뷰를 거쳐 머지하는 전체 흐름을 배웁니다.

**선수 지식**: [Markdown 작성법](../05-github-start/04-markdown.md)에서 배운 마크다운 문법, [브랜치 병합](../03-branch/03-merge.md)의 기본 개념
**학습 목표**:
- Pull Request의 개념과 역할을 이해한다
- 브랜치 → PR → 리뷰 → 머지의 전체 워크플로우를 실행할 수 있다
- 효과적인 PR을 작성할 수 있다

## 왜 알아야 할까?

혼자 개발할 때는 `main` 브랜치에 바로 커밋해도 문제가 없죠. 하지만 팀으로 일하면 이야기가 달라집니다. 내가 작성한 코드가 기존 코드를 망가뜨리지는 않는지, 더 나은 방법은 없는지 — 누군가 확인해줘야 안심이 되거든요. PR은 이런 **코드 검증 과정을 체계적으로 만든 도구**입니다.

실제로 거의 모든 회사와 오픈소스 프로젝트에서 PR 없이는 `main` 브랜치에 코드를 넣을 수 없습니다. PR을 잘 다루는 건 **협업 개발자의 기본기**예요.

## 핵심 개념

### 개념 1: Pull Request란?

> 💡 **비유**: PR은 **검토 요청서**와 같습니다. 보고서를 작성한 후 팀장에게 "제 보고서 검토 부탁드립니다"라고 올리는 것처럼, 코드를 작성한 후 팀에게 "제 코드 확인해주세요"라고 요청하는 거예요. 팀장이 피드백을 주면 수정하고, 최종 승인을 받으면 공식 문서에 반영되는 것과 같은 흐름입니다.

**Pull Request**는 "내 브랜치의 변경 사항을 다른 브랜치(보통 `main`)에 합쳐달라"는 요청입니다. 이름이 "Pull" Request인 이유는, 원래 **"내 코드를 당겨가 주세요(pull)"**라는 의미에서 왔어요.

PR에는 다음 정보가 담깁니다:

| 구성 요소 | 설명 |
|-----------|------|
| **제목(Title)** | 변경 사항을 한 줄로 요약 |
| **설명(Description)** | 무엇을, 왜 변경했는지 상세 설명 |
| **변경된 파일(Files changed)** | 코드 diff — 어떤 줄이 추가/삭제/수정되었는지 |
| **커밋 목록(Commits)** | PR에 포함된 커밋들 |
| **리뷰어(Reviewers)** | 코드를 검토할 사람 |
| **라벨(Labels)** | `bug`, `enhancement`, `docs` 등 분류 태그 |
| **연결된 이슈(Linked issues)** | 이 PR이 해결하는 이슈 번호 |

### 개념 2: GitHub Flow — PR 중심 워크플로우

GitHub에서 권장하는 협업 흐름을 **GitHub Flow**라고 합니다. 놀랍도록 간단해요:

**1단계: 브랜치 생성** → **2단계: 작업 & 커밋** → **3단계: PR 생성** → **4단계: 코드 리뷰** → **5단계: 머지** → **6단계: 브랜치 삭제**

이 흐름을 명령어로 따라가 볼까요?

**1단계: 브랜치 생성**

```bash
# main에서 새 기능 브랜치 생성
git switch -c feature/add-login
```

**2단계: 작업 & 커밋**

```bash
# 코드 작성 후 커밋
git add src/login.js
git commit -m "Add login form component"

# 추가 작업 후 커밋
git add src/auth.js
git commit -m "Add authentication logic"

# 원격에 푸시
git push -u origin feature/add-login
```

**3단계: PR 생성**

```bash
# GitHub CLI로 PR 생성
gh pr create \
  --title "Add login feature" \
  --body "## 변경 사항
- 로그인 폼 컴포넌트 추가
- 인증 로직 구현

Closes #42" \
  --reviewer teammate1 \
  --label "enhancement"
```

**4단계: 코드 리뷰** — 리뷰어가 코드를 확인하고 피드백을 남깁니다 (다음 섹션에서 자세히 다룹니다).

**5단계: 머지**

```bash
# 리뷰 승인 후 머지
gh pr merge --squash --delete-branch
```

**6단계: 로컬 정리**

```bash
# main으로 돌아와서 최신 상태 동기화
git switch main
git pull
```

### 개념 3: 좋은 PR 작성법

PR은 코드만큼이나 **설명이 중요**합니다. 리뷰어가 맥락을 모르면 코드를 이해하기 어렵거든요.

**PR 제목 작성 규칙**:
- 명령형으로 작성: "Add login feature" (O) / "Added login feature" (X)
- 70자 이내로 간결하게
- 접두사를 활용하면 분류가 쉬움: `feat:`, `fix:`, `docs:`, `refactor:`

**PR 설명에 포함할 내용**:

> **무엇을(What)**: 어떤 변경이 있는지
> **왜(Why)**: 왜 이 변경이 필요한지
> **어떻게(How)**: 핵심 구현 방식 (복잡한 경우)
> **테스트**: 어떻게 테스트했는지
> **관련 이슈**: `Closes #42` 또는 `Fixes #42`

**이슈 자동 닫기 키워드** — PR 설명이나 커밋 메시지에 다음 키워드를 쓰면, PR이 머지될 때 이슈가 자동으로 닫힙니다:

| 키워드 | 예시 |
|--------|------|
| `Closes` | `Closes #42` |
| `Fixes` | `Fixes #42` |
| `Resolves` | `Resolves #42` |

> 💡 **알고 계셨나요?**: 이 키워드들은 대소문자를 구분하지 않습니다. `closes #42`, `CLOSES #42`, `Closes #42` 모두 동일하게 동작해요.

### 개념 4: PR 템플릿

매번 PR 설명을 처음부터 쓰는 건 번거롭죠. **PR 템플릿**을 만들면 모든 PR에 자동으로 양식이 채워집니다.

```bash
# 프로젝트 루트에 템플릿 파일 생성
mkdir -p .github
```

`.github/PULL_REQUEST_TEMPLATE.md` 파일에 다음과 같이 작성합니다:

```markdown
## 변경 사항
<!-- 무엇을 변경했는지 간단히 설명해주세요 -->

## 변경 이유
<!-- 왜 이 변경이 필요한지 설명해주세요 -->

## 테스트
- [ ] 로컬에서 테스트 완료
- [ ] 기존 테스트 통과 확인

## 관련 이슈
<!-- Closes #이슈번호 -->

## 스크린샷 (UI 변경 시)
<!-- 변경 전/후 스크린샷을 첨부해주세요 -->
```

이 파일을 저장소에 커밋해두면, 새 PR을 만들 때마다 이 양식이 자동으로 나타납니다.

### 개념 5: 라벨과 담당자

PR을 효율적으로 관리하려면 **라벨(Label)**과 **담당자(Assignee)**를 활용하세요.

```bash
# PR 생성 시 라벨과 담당자 지정
gh pr create \
  --title "Fix memory leak in cache" \
  --label "bug,priority:high" \
  --assignee "@me" \
  --reviewer "senior-dev,tech-lead"

# 이미 생성된 PR에 라벨 추가
gh pr edit 42 --add-label "needs-review"

# 라벨별 PR 목록 보기
gh pr list --label "bug"
```

자주 쓰는 라벨을 미리 만들어두면 PR 분류가 훨씬 편리해집니다:

| 라벨 | 용도 |
|------|------|
| `bug` | 버그 수정 |
| `enhancement` | 새 기능, 기능 개선 |
| `docs` | 문서 변경 |
| `refactor` | 리팩토링 (동작 변경 없음) |
| `breaking` | 하위 호환성 깨지는 변경 |
| `good first issue` | 신규 기여자용 쉬운 작업 |

## 실습: 첫 PR 만들어보기

```bash
# 1. 실습용 브랜치 생성
git switch -c feature/update-readme

# 2. README에 내용 추가
echo "" >> README.md
echo "## Contributing" >> README.md
echo "PR을 통해 기여해주세요!" >> README.md

# 3. 커밋 & 푸시
git add README.md
git commit -m "Add contributing section to README"
git push -u origin feature/update-readme

# 4. PR 생성
gh pr create \
  --title "Add contributing section to README" \
  --body "## 변경 사항
README에 Contributing 섹션을 추가했습니다.

## 변경 이유
기여 방법 안내가 필요합니다.

Closes #1" \
  --label "docs"
```

```output
Creating pull request for feature/update-readme into main in user/my-project

https://github.com/user/my-project/pull/2
```

```bash
# 5. PR 상태 확인
gh pr view 2
```

```output
Add contributing section to README #2
Open • user wants to merge 1 commit into main from feature/update-readme

  ## 변경 사항
  README에 Contributing 섹션을 추가했습니다.

  ## 변경 이유
  기여 방법 안내가 필요합니다.

  Closes #1

Labels: docs
```

```bash
# 6. PR 목록 확인
gh pr list
```

```output
Showing 1 of 1 pull request in user/my-project

#2  Add contributing section to README  feature/update-readme  about 1 minute ago
```

## 더 깊이 알아보기

### Pull Request의 역사

PR의 개념은 사실 GitHub보다 먼저 존재했습니다. **리눅스 커널 개발**에서 Linus Torvalds는 이메일로 "내 브랜치를 pull 해주세요"라고 요청하는 방식을 사용했어요. 이것이 문자 그대로 "Pull Request"의 원형입니다.

2008년 GitHub이 출시되면서 이 이메일 기반 워크플로우를 **웹 인터페이스로 체계화**했습니다. 코드 diff를 웹에서 바로 보고, 줄 단위로 댓글을 달 수 있게 된 거죠. 이 혁신이 오픈소스 기여의 문턱을 크게 낮췄습니다.

> 💡 **알고 계셨나요?**: GitHub에서는 "Pull Request"라고 부르지만, GitLab에서는 같은 기능을 **"Merge Request(MR)"**라고 부릅니다. 사실 "내 코드를 합쳐주세요"라는 의미에서는 Merge Request가 더 직관적인 이름이에요. 하지만 Git의 원래 워크플로우가 "pull" 기반이었기 때문에 GitHub은 이 이름을 유지하고 있습니다.

### CODEOWNERS — 자동 리뷰어 배정

대규모 프로젝트에서는 누가 어떤 코드를 리뷰해야 하는지 자동으로 정할 수 있습니다.

`.github/CODEOWNERS` 파일을 만들면, 특정 파일이 변경될 때 해당 담당자가 자동으로 리뷰어로 지정됩니다:

```bash
# .github/CODEOWNERS

# 모든 파일의 기본 리뷰어
* @team-lead

# 프론트엔드 코드는 프론트팀이 리뷰
/src/frontend/ @frontend-team

# 문서는 테크라이터가 리뷰
*.md @tech-writer

# 보안 관련 코드는 보안팀 필수 리뷰
/src/auth/ @security-team @team-lead
```

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "PR은 코드가 완성된 후에만 만드는 것이다" — 아닙니다! **Draft PR**을 활용하면 작업 중에도 PR을 만들 수 있어요. 초기에 PR을 열면 팀이 방향성을 미리 확인할 수 있어 나중에 큰 수정을 피할 수 있습니다. Draft PR은 [PR 관리](./03-pr-management.md)에서 자세히 다룹니다.

> 🔥 **실무 팁**: PR은 **작게 유지**하세요. 연구에 따르면 변경 400줄을 넘으면 리뷰 품질이 급격히 떨어집니다. 하나의 PR은 하나의 목적만 담는 것이 이상적이에요. 큰 기능은 여러 개의 작은 PR로 나눠서 보내세요.

> 🔥 **실무 팁**: PR 제목에 접두사를 붙이면 목록에서 한눈에 파악하기 좋습니다. `feat: 로그인 기능 추가`, `fix: 캐시 메모리 누수 수정`, `docs: API 문서 업데이트` — 이런 식으로요. 이 규칙은 [커밋 메시지 컨벤션](../11-team-tools/02-commit-convention.md)에서 더 자세히 다룹니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| Pull Request | 브랜치의 변경 사항을 다른 브랜치에 합쳐달라는 요청 |
| GitHub Flow | 브랜치 → PR → 리뷰 → 머지의 표준 협업 흐름 |
| PR 설명 | What(무엇을), Why(왜), How(어떻게)를 포함 |
| 이슈 자동 닫기 | `Closes #42` 등의 키워드로 머지 시 이슈 자동 종료 |
| PR 템플릿 | `.github/PULL_REQUEST_TEMPLATE.md`로 양식 자동화 |
| CODEOWNERS | 파일별 자동 리뷰어 배정 |
| `gh pr create` | CLI에서 PR 생성 |
| `gh pr merge` | CLI에서 PR 머지 |

## 다음 섹션 미리보기

PR을 만드는 법을 배웠으니, 이제 가장 중요한 단계인 **코드 리뷰**를 배울 차례입니다. [코드 리뷰](./02-code-review.md)에서는 리뷰를 요청하는 법, 라인 코멘트와 제안(Suggestion)을 다는 법, 그리고 Approve/Request Changes의 의미와 리뷰 에티켓을 알아봅니다.

## 참고 자료

- [GitHub Docs — Pull Request 소개](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests) - PR의 공식 개념 설명
- [GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow) - GitHub 권장 협업 워크플로우
- [GitHub Docs — PR 템플릿 만들기](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository) - PR 템플릿 공식 가이드
- [GitHub Docs — CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) - 자동 리뷰어 배정 가이드
