# 코드 리뷰 문화

> 효과적인 리뷰, CODEOWNERS, 리뷰 자동화, 팀 가이드

## 개요

[코드 리뷰](../06-pull-request/02-code-review.md)에서 GitHub의 리뷰 기능 — 라인 코멘트, Suggestion, Approve/Request Changes 사용법을 배웠죠. 이번 섹션에서는 한 걸음 더 나아가, 팀에서 **건강한 리뷰 문화를 만드는 방법**을 다룹니다. CODEOWNERS로 리뷰어를 자동 배정하고, AI 도구로 1차 검토를 자동화하며, 팀 전체가 따를 리뷰 가이드라인을 설계합니다.

**선수 지식**: [코드 리뷰](../06-pull-request/02-code-review.md)에서 배운 리뷰 기본, [커밋 메시지 컨벤션](./02-commit-convention.md)
**학습 목표**:
- 효과적인 코드 리뷰 가이드라인을 설계할 수 있다
- CODEOWNERS 파일을 작성하고 활용할 수 있다
- 리뷰 자동화 도구를 프로젝트에 적용할 수 있다

## 왜 알아야 할까?

코드 리뷰의 **도구**는 알지만, **문화**가 없는 팀이 많습니다. 리뷰가 병목이 되어 PR이 일주일째 방치되거나, "LGTM(Looks Good To Me)" 한 줄로 대충 넘기거나, 사소한 스타일 지적으로 리뷰가 10라운드까지 가는 상황 — 이 모든 것이 리뷰 문화의 부재에서 비롯됩니다.

구글은 리뷰 가이드라인을 **공개 문서**로 유지합니다. Netflix, Meta, Microsoft 같은 기업들도 자체 리뷰 규칙을 가지고 있죠. 이번 섹션에서 이런 기업들의 노하우를 배워봅시다.

## 핵심 개념

### 개념 1: 좋은 리뷰의 원칙

> 💡 **비유**: 코드 리뷰는 **요리 교실의 피드백**과 같습니다. 좋은 선생은 "이건 맛없어"라고 하지 않고, "소금을 조금 줄이면 재료 본연의 맛이 더 살아날 거예요"라고 합니다. 코드 리뷰도 마찬가지 — **문제를 지적하는 것**이 아니라 **더 나은 방향을 제안하는 것**이어야 합니다.

**구글의 코드 리뷰 원칙** (Google Engineering Practices에서 발췌):

| 원칙 | 설명 |
|------|------|
| **리뷰어는 24시간 내 응답** | 리뷰 요청이 오면 하루 안에 최소 첫 응답 |
| **완벽보다 개선** | 코드가 전보다 나아졌으면 승인 |
| **작은 PR 선호** | 200줄 이하의 PR이 리뷰 품질이 높음 |
| **자동화 가능한 건 자동화** | 스타일, 포맷은 린터에 맡기기 |
| **교육적 코멘트 구분** | "Nit:" (사소한 제안)으로 필수/선택 구분 |

**리뷰 코멘트 에티켓**:

| ❌ 나쁜 리뷰 | ✅ 좋은 리뷰 |
|-------------|-------------|
| "이거 왜 이렇게 했어요?" | "이 부분은 X 방식도 고려해볼 수 있을 것 같은데, 어떻게 생각하시나요?" |
| "이건 아닌 것 같은데" | "이 접근 대신 Y 패턴을 사용하면 Z 이점이 있습니다" |
| "LGTM" | "로직 확인했고, 에지 케이스 처리도 잘 되어 있네요. LGTM!" |
| "변수명 별로예요" | "Nit: `data`보다 `userProfile`이면 의도가 더 명확할 것 같습니다" |

**코멘트 접두사 컨벤션**:

| 접두사 | 의미 | 반영 의무 |
|--------|------|----------|
| (없음) | 일반 피드백 | 리뷰어와 논의 후 결정 |
| `Nit:` | 사소한 제안 (nitpick) | 선택 (다음 PR에서 해도 됨) |
| `Question:` | 이해를 위한 질문 | 답변 필수, 코드 변경은 선택 |
| `Suggestion:` | 구체적 대안 제시 | 수용 권장 |
| `Blocking:` | 머지 전 반드시 수정 | 필수 |

### 개념 2: CODEOWNERS 심화

[PR 워크플로우](../06-pull-request/01-pr-workflow.md)에서 CODEOWNERS의 기본을 배웠습니다. 여기서는 **실전 패턴**을 다룹니다.

```bash
# .github/CODEOWNERS

# 전역 기본 — 모든 파일의 기본 리뷰어
* @team-lead

# 프론트엔드 — 디렉토리별 전문가 배정
/src/components/     @frontend-team
/src/styles/         @frontend-team @design-team
/src/pages/          @frontend-team

# 백엔드 — API와 DB 전문가
/src/api/            @backend-team
/src/database/       @backend-team @dba-team
/src/middleware/     @backend-team

# 인프라 & CI/CD — DevOps 팀
/.github/workflows/  @devops-team
/docker/             @devops-team
/terraform/          @devops-team

# 보안 민감 파일 — 보안 팀 필수 리뷰
/src/auth/           @security-team @backend-team
*.env.example        @security-team

# 문서 — 기술 작가 또는 문서 담당자
/docs/               @docs-team
*.md                 @docs-team

# 의존성 변경 — 리드 개발자
package.json         @tech-lead
package-lock.json    @tech-lead
go.mod               @tech-lead
```

**CODEOWNERS 매칭 규칙**:

| 패턴 | 매칭 대상 |
|------|----------|
| `*` | 모든 파일 |
| `*.js` | 모든 .js 파일 |
| `/docs/` | 루트의 docs 디렉토리 |
| `docs/` | 모든 위치의 docs 디렉토리 |
| `/src/components/**` | components 하위 모든 파일 (재귀적) |

> ⚠️ **흔한 오해**: "CODEOWNERS에 등록된 사람이 Approve하지 않으면 머지할 수 없다" — 기본적으로는 **그렇지 않습니다**. CODEOWNERS를 강제하려면 Branch Protection Rule에서 **"Require review from Code Owners"** 옵션을 켜야 합니다. 이 설정 없이는 CODEOWNERS는 자동 리뷰 요청만 할 뿐, 머지를 막지는 않아요.

### 개념 3: 리뷰 자동화 도구

사람의 리뷰 전에 **기계가 먼저** 확인할 수 있는 것들이 많습니다. [빌드와 테스트 자동화](../10-github-actions/03-ci.md)에서 배운 CI와 결합하면 효과가 배가됩니다.

**1) GitHub Copilot 코드 리뷰**

2025년에 정식 출시(GA)된 GitHub Copilot의 코드 리뷰 기능은 PR의 리뷰어로 **Copilot을 지정**하면 AI가 코드를 분석합니다:

- 잠재적 버그와 로직 오류 탐지 (CodeQL, ESLint 등 린터와 LLM 결합)
- 보안 취약점 경고
- Suggestion 형태로 클릭 한 번에 적용 가능한 수정안 제시
- `@copilot` 멘션 시 코딩 에이전트가 **자동으로 수정 PR**을 생성

| 특징 | 설명 |
|------|------|
| 리뷰 타입 | **Comment**만 남김 (Approve/Request Changes 불가) |
| 필수 승인 | 카운트에 **포함되지 않음** (머지 차단 불가) |
| 비용 | 프리미엄 요청 **1회** 소모 |
| 요금제 | Copilot Pro, Pro+, Business, Enterprise 필요 |

**2) danger-js — PR 규칙 자동 검증**

```javascript
// dangerfile.js
import { danger, warn, fail } from 'danger';

// PR 크기 경고
const bigPRThreshold = 500;
if (danger.github.pr.additions + danger.github.pr.deletions > bigPRThreshold) {
  warn('이 PR은 변경량이 많습니다. 더 작은 단위로 나눌 수 있을까요?');
}

// 테스트 파일 누락 경고
const hasTestChanges = danger.git.modified_files.some(f => f.includes('.test.'));
const hasSrcChanges = danger.git.modified_files.some(f => f.includes('src/'));
if (hasSrcChanges && !hasTestChanges) {
  warn('소스 코드가 변경되었지만 테스트 파일은 변경되지 않았습니다.');
}

// PR 설명 누락 차단
if (danger.github.pr.body.length < 10) {
  fail('PR 설명을 작성해주세요. 무엇을, 왜 변경했는지 알려주세요.');
}
```

```yaml
# .github/workflows/danger.yml
name: Danger JS
on: [pull_request]
jobs:
  danger:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - run: npm ci
      - run: npx danger ci
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**3) 자동 리뷰어 배정 (round-robin)**

```yaml
# .github/workflows/auto-assign.yml
name: Auto Assign Reviewers
on:
  pull_request:
    types: [opened, ready_for_review]
jobs:
  assign:
    runs-on: ubuntu-latest
    steps:
      - uses: hkusu/review-assign-action@v1
        with:
          assignees: kim,lee,park
          max-num-of-reviewers: 2
```

### 개념 4: 팀 리뷰 가이드라인 템플릿

실제 팀에서 사용할 수 있는 리뷰 가이드라인 템플릿입니다. CONTRIBUTING.md에 포함하거나 별도 문서로 관리합니다:

**리뷰어 체크리스트**:

- **기능**: 요구사항대로 동작하는가?
- **버그**: 에지 케이스와 에러 처리가 적절한가?
- **보안**: SQL Injection, XSS 등 취약점은 없는가?
- **성능**: 불필요한 DB 쿼리, N+1 문제는 없는가?
- **가독성**: 코드를 처음 보는 사람이 이해할 수 있는가?
- **테스트**: 새 기능/수정에 대한 테스트가 있는가?
- **문서**: 변경에 따른 문서 업데이트가 되어 있는가?

**PR 작성자 체크리스트**:

- PR 설명에 **무엇을, 왜** 변경했는지 작성했는가?
- 관련 이슈를 연결했는가?
- 셀프 리뷰를 먼저 했는가?
- PR 크기가 적절한가? (300줄 이하 권장)
- 스크린샷이 필요한 UI 변경이라면 첨부했는가?

### 개념 5: Branch Protection으로 리뷰 강제

리뷰 문화를 **규칙으로 강제**하려면 Branch Protection이 필수입니다:

```bash
# GitHub CLI로 브랜치 보호 규칙 설정
gh api repos/{owner}/{repo}/branches/main/protection -X PUT \
  --input - <<EOF
{
  "required_pull_request_reviews": {
    "required_approving_review_count": 2,
    "dismiss_stale_reviews": true,
    "require_code_owner_reviews": true,
    "require_last_push_approval": true
  },
  "required_status_checks": {
    "strict": true,
    "contexts": ["ci", "commitlint"]
  },
  "enforce_admins": true
}
EOF
```

| 옵션 | 설명 |
|------|------|
| `required_approving_review_count` | 필수 승인 수 (1~6명) |
| `dismiss_stale_reviews` | 새 커밋 푸시 시 기존 승인 무효화 |
| `require_code_owner_reviews` | CODEOWNERS 승인 필수 |
| `require_last_push_approval` | 마지막 푸시한 사람 이외의 승인 필요 |
| `enforce_admins` | 관리자도 규칙 적용 |

## 실습: CODEOWNERS + Branch Protection 설정

```bash
# 1. 실습 저장소에서 CODEOWNERS 파일 생성
mkdir -p .github
cat > .github/CODEOWNERS << 'EOF'
# 전체 기본 리뷰어
* @your-username

# 프론트엔드
/src/frontend/  @frontend-reviewer

# 문서
*.md  @docs-reviewer
EOF

# 2. 커밋
git add .github/CODEOWNERS
git commit -m "chore: CODEOWNERS 파일 추가"

# 3. 푸시
git push origin main
```

```bash
# 4. Branch Protection 설정 (GitHub CLI)
gh api repos/{owner}/{repo}/branches/main/protection -X PUT \
  --field "required_pull_request_reviews[required_approving_review_count]=1" \
  --field "required_pull_request_reviews[require_code_owner_reviews]=true"
```

## 더 깊이 알아보기

### 코드 리뷰의 진화

[코드 리뷰](../06-pull-request/02-code-review.md)에서 배운 것처럼 코드 리뷰의 역사는 1976년 IBM의 Fagan Inspection에서 시작합니다. 인쇄된 코드를 회의실에서 검토하던 시대에서, 이메일로 패치를 보내던 시대(Linux 커널 방식)를 거쳐, GitHub의 PR 리뷰 시스템이 현재의 표준이 되었죠.

2025년에는 AI가 리뷰 프로세스에 본격적으로 참여하기 시작했습니다. GitHub Copilot 코드 리뷰, Amazon CodeGuru, CodeRabbit 같은 도구들이 1차 리뷰를 자동으로 수행합니다. 하지만 "비즈니스 로직이 맞는가?", "설계 방향이 적절한가?" 같은 판단은 여전히 사람의 영역입니다. AI와 사람의 리뷰가 **보완적으로 공존하는 시대**가 된 것이죠.

> 💡 **알고 계셨나요?**: 구글에서는 모든 코드 변경에 리뷰가 필수이며, 리뷰어가 **24시간 이내에 응답하지 않으면** 다른 리뷰어로 자동 재배정됩니다. 이 규칙 덕분에 구글의 평균 리뷰 소요 시간은 약 4시간이라고 합니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "리뷰 승인 수가 많을수록 좋다" — 꼭 그렇지 않습니다! 필수 리뷰어 수를 너무 높게 설정하면 PR이 오래 대기하게 되고, "대충 LGTM"을 남기는 습관이 생깁니다. 소규모 팀(2~5명)에서는 **1~2명**, 대규모 팀(10명 이상)에서도 **2~3명**이면 충분합니다.

> 🔥 **실무 팁**: "**셀프 리뷰** 습관을 들이세요!" PR을 생성하기 전에 자신의 코드를 **GitHub의 Files Changed 탭**에서 한 번 더 확인하세요. 놀랍게도 많은 실수가 셀프 리뷰에서 발견됩니다 — diff 화면으로 보면 에디터에서 놓쳤던 것들이 눈에 들어오거든요.

> 🔥 **실무 팁**: **Draft PR**을 활용하세요! [PR 관리](../06-pull-request/03-pr-management.md)에서 배운 것처럼, 작업 중인 PR을 Draft로 올리면 "아직 리뷰 시작하지 마세요"라는 신호가 됩니다. 코드가 어느 정도 완성되면 Draft를 해제하고 리뷰를 요청하면 됩니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| **리뷰 응답 시간** | 24시간 이내 첫 응답 권장 (구글 기준) |
| **PR 크기** | 200~300줄 이하가 리뷰 품질 높음 |
| **CODEOWNERS** | 파일/디렉토리별 자동 리뷰어 배정 |
| **코멘트 접두사** | Nit:, Question:, Suggestion:, Blocking: |
| **Copilot 리뷰** | `@copilot`을 리뷰어로 지정하여 AI 1차 검토 |
| **danger-js** | PR 규칙(크기, 설명, 테스트 유무) 자동 검증 |
| **Branch Protection** | 필수 리뷰 수, CODEOWNERS 강제, 상태 검사 |
| **셀프 리뷰** | PR 생성 전 자신의 diff를 먼저 검토 |

## 다음 섹션 미리보기

지금까지 브랜치 이름, 커밋 메시지, 코드 리뷰 — 팀 협업의 **프로세스**를 다뤘습니다. 다음 섹션 [모노레포와 서브모듈](./04-monorepo-submodule.md)에서는 프로젝트의 **구조** 자체를 다룹니다. 여러 프로젝트를 하나의 저장소에 담는 모노레포와, 다른 저장소를 참조하는 서브모듈/서브트리 — 대규모 프로젝트를 관리하는 기술을 배웁니다.

## 참고 자료

- [Google Engineering Practices — Code Review](https://google.github.io/eng-practices/review/) - 구글의 코드 리뷰 가이드라인
- [GitHub Docs — CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) - CODEOWNERS 공식 문서
- [GitHub Docs — Branch Protection Rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-a-branch-protection-rule) - 브랜치 보호 규칙
- [danger-js](https://danger.systems/js/) - PR 자동화 도구
- [GitHub Blog — Copilot Code Review](https://github.blog/changelog/2024-10-29-copilot-code-review-in-github-com-public-preview/) - Copilot 코드 리뷰 기능
