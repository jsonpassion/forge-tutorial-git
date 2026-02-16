# 커밋 메시지 컨벤션

> Conventional Commits, commitlint, 자동 Changelog

## 개요

[변경 추적과 커밋 메시지](../01-git-start/05-diff-messages.md)에서 좋은 커밋 메시지의 원칙과 Conventional Commits의 기본을 배웠죠. 이번 섹션에서는 그 개념을 **팀 전체에 적용하고 자동화**하는 방법을 다룹니다. commitlint로 규칙을 강제하고, Changelog를 자동 생성하며, 시맨틱 버저닝까지 자동화하는 흐름을 완성합니다.

**선수 지식**: [변경 추적과 커밋 메시지](../01-git-start/05-diff-messages.md)에서 배운 커밋 메시지 작성법, [브랜치 네이밍 컨벤션](./01-branch-naming.md)
**학습 목표**:
- Conventional Commits 규약을 심화 이해한다
- commitlint + Husky로 메시지를 자동 검증할 수 있다
- semantic-release 또는 release-please로 버전과 Changelog를 자동 관리할 수 있다

## 왜 알아야 할까?

"feat: 로그인" vs "fix: 로그인" — 이 한 단어의 차이가 **릴리스 버전을 결정**합니다. `feat`는 마이너 버전(1.1.0 → 1.2.0), `fix`는 패치 버전(1.1.0 → 1.1.1)으로 올라가거든요. 커밋 메시지가 규칙적이면 버전 관리, Changelog 생성, 릴리스 자동화까지 모두 기계가 대신할 수 있습니다. 반대로 규칙이 없으면? "update", "fix stuff", "WIP" 같은 메시지가 쌓이고, 누가 뭘 왜 바꿨는지 알 수 없는 히스토리가 됩니다.

## 핵심 개념

### 개념 1: Conventional Commits 심화

> 💡 **비유**: Conventional Commits는 **택배 표준 송장**과 같습니다. 모든 택배 회사가 같은 형식의 송장을 사용하면, 어떤 회사든 자동 분류기로 택배를 처리할 수 있죠. 커밋 메시지도 형식이 통일되면, 어떤 도구든 자동으로 분석하고 처리할 수 있습니다.

[이전 섹션](../01-git-start/05-diff-messages.md)에서 기본 형식을 배웠습니다. 여기서는 **전체 사양**을 정리합니다:

```console
<타입>[선택 범위]: <설명>

[선택 본문]

[선택 꼬리말]
```

**타입(type) 전체 목록**:

| 타입 | 의미 | SemVer 영향 |
|------|------|------------|
| `feat` | 새 기능 추가 | **MINOR** (1.0.0 → 1.1.0) |
| `fix` | 버그 수정 | **PATCH** (1.0.0 → 1.0.1) |
| `docs` | 문서 변경만 | 없음 |
| `style` | 포맷팅, 세미콜론 등 (코드 동작 변경 X) | 없음 |
| `refactor` | 리팩토링 (기능/버그 변경 X) | 없음 |
| `perf` | 성능 개선 | PATCH |
| `test` | 테스트 추가/수정 | 없음 |
| `build` | 빌드 시스템, 외부 의존성 변경 | 없음 |
| `ci` | CI 설정 변경 | 없음 |
| `chore` | 기타 변경 (소스/테스트 미변경) | 없음 |
| `revert` | 이전 커밋 되돌리기 | 상황에 따라 다름 |

**범위(scope)** — 변경 영역을 명시:

```bash
feat(auth): 소셜 로그인 추가
fix(cart): 수량 0 입력 시 에러 수정
docs(readme): 설치 가이드 추가
refactor(api): 인증 미들웨어 구조 개선
```

**BREAKING CHANGE** — 하위 호환성이 깨지는 변경:

```bash
# 방법 1: 꼬리말에 명시
feat(api): 사용자 API 응답 형식 변경

BREAKING CHANGE: 응답의 `user` 필드가 `data.user`로 이동했습니다.
마이그레이션 가이드: https://example.com/migration

# 방법 2: 타입 뒤에 ! 추가
feat(api)!: 사용자 API 응답 형식 변경
```

BREAKING CHANGE는 **MAJOR** 버전을 올립니다 (1.0.0 → 2.0.0).

> ⚠️ **흔한 오해**: "`chore`와 `build`의 차이가 뭐예요?" — `build`는 빌드 시스템이나 외부 의존성에 관한 변경(webpack 설정, package.json 수정 등)이고, `chore`는 그 외 나머지(`.gitignore` 수정, 스크립트 정리 등)입니다. 사실 `chore`는 Conventional Commits 공식 스펙에는 없지만, Angular 컨벤션에서 유래한 것으로 사실상 표준이 되었습니다.

### 개념 2: commitlint — 메시지 자동 검증

규칙을 정했으면 **강제**해야 합니다. `commitlint`는 커밋 메시지가 Conventional Commits 규약을 따르는지 자동으로 확인하는 도구입니다.

```bash
# commitlint 설치
npm install --save-dev @commitlint/cli @commitlint/config-conventional
```

```bash
# commitlint.config.js 생성
echo "export default { extends: ['@commitlint/config-conventional'] };" > commitlint.config.js
```

```bash
# Husky 설정 (이전 섹션에서 이미 설치했다면 건너뛰기)
npm install --save-dev husky
npx husky init

# commit-msg 훅 추가
echo "npx --no -- commitlint --edit \$1" > .husky/commit-msg
```

이제 규칙에 맞지 않는 커밋은 자동으로 **차단**됩니다:

```bash
# ❌ 규칙 위반 — 차단됨
git commit -m "로그인 수정"
```

```error
⧗   input: 로그인 수정
✖   subject may not be empty [subject-empty]
✖   type may not be empty [type-empty]

✖   Found 2 problems, 0 warnings
```

```bash
# ✅ 규칙 준수 — 통과
git commit -m "fix(auth): 로그인 시 빈 이메일 허용 방지"
```

```output
[feature/42-login-fix abc1234] fix(auth): 로그인 시 빈 이메일 허용 방지
 1 file changed, 5 insertions(+), 2 deletions(-)
```

**커스텀 규칙 추가**:

```javascript
// commitlint.config.js
export default {
  extends: ['@commitlint/config-conventional'],
  rules: {
    // 타입 범위 제한
    'scope-enum': [2, 'always', [
      'auth', 'cart', 'ui', 'api', 'docs', 'config'
    ]],
    // 설명 최대 길이
    'header-max-length': [2, 'always', 100],
    // 본문은 빈 줄로 시작
    'body-leading-blank': [2, 'always'],
  }
};
```

### 개념 3: commitizen — 대화형 커밋 도우미

> 💡 **비유**: commitizen은 **세금 신고 프로그램**과 같습니다. 복잡한 양식을 직접 채울 필요 없이 질문에 하나씩 답하면 완성된 양식이 만들어지죠. 커밋 메시지도 마찬가지 — 타입은 뭔지, 범위는 뭔지, 설명은 뭔지 하나씩 물어보고 완벽한 형식의 메시지를 만들어줍니다.

```bash
# commitizen 설치
npm install --save-dev commitizen cz-conventional-changelog

# package.json에 설정 추가
npx commitizen init cz-conventional-changelog --save-dev --save-exact
```

```bash
# git commit 대신 사용
npx cz
```

```console
? Select the type of change that you're committing:
❯ feat:     A new feature
  fix:      A bug fix
  docs:     Documentation only changes
  style:    Changes that do not affect the meaning of the code
  refactor: A code change that neither fixes a bug nor adds a feature
  perf:     A code change that improves performance
  test:     Adding missing tests or correcting existing tests

? What is the scope of this change (e.g. component or file name)?
auth

? Write a short, imperative tense description of the change:
소셜 로그인 추가

? Provide a longer description of the change: (press enter to skip)
Google, Kakao, Naver OAuth 2.0 연동

? Are there any breaking changes? No
? Does this change affect any open issues? Yes
? Add issue references (e.g. "fix #123", "re #123".):
closes #42

───────────────────
feat(auth): 소셜 로그인 추가

Google, Kakao, Naver OAuth 2.0 연동

closes #42
───────────────────
```

### 개념 4: 자동 Changelog 생성

커밋 메시지가 규칙적이면, **Changelog를 자동으로 생성**할 수 있습니다. 수동으로 "이번 릴리스에 뭐가 바뀌었지?" 하고 커밋을 하나씩 뒤지는 건 과거의 일입니다.

**release-please** (Google의 도구, GitHub Actions 연동):

```yaml
# .github/workflows/release.yml
name: Release Please

on:
  push:
    branches:
      - main

permissions:
  contents: write
  pull-requests: write

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        with:
          release-type: node
```

이 워크플로우가 하는 일:

1. `main`에 `feat:` 커밋이 푸시되면 → **Release PR** 자동 생성
2. Release PR에 **CHANGELOG.md** 자동 업데이트
3. PR을 머지하면 → **GitHub Release** + **Git 태그** 자동 생성

```output
## [2.3.0](https://github.com/example/repo/compare/v2.2.0...v2.3.0) (2026-02-16)

### Features
* **auth**: 소셜 로그인 추가 (#42) (abc1234)
* **ui**: 다크 모드 지원 (#56) (def5678)

### Bug Fixes
* **cart**: 수량 0 입력 시 에러 수정 (#48) (ghi9012)
```

**semantic-release** (완전 자동화):

```bash
# semantic-release 설치
npm install --save-dev semantic-release
```

```json
// package.json 또는 .releaserc.json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/npm",
    "@semantic-release/github",
    "@semantic-release/git"
  ]
}
```

| 도구 | 방식 | 특징 |
|------|------|------|
| **release-please** | PR 기반 (사람이 확인 후 머지) | 안전, Google 개발, 20+ 언어 지원 |
| **semantic-release** | 완전 자동 (커밋 → 릴리스) | 빠르지만 실수 시 자동 배포 |
| **changesets** | 수동 changeset 파일 추가 방식 | 모노레포에 적합 |
| **git-cliff** | 템플릿 기반 Changelog 생성 (Rust) | 언어 무관, 고도의 커스터마이징 |

> 🔥 **실무 팁**: 처음에는 **release-please**를 추천합니다. PR 기반이라 릴리스 전에 사람이 확인할 수 있어 안전하거든요. 익숙해지면 semantic-release로 완전 자동화를 시도해볼 수 있습니다.

### 개념 5: GitHub Actions로 커밋 메시지 검증

로컬 commitlint는 우회할 수 있으므로(Husky를 설치 안 하거나, `--no-verify` 플래그 사용), **서버 측에서도 검증**하는 것이 안전합니다:

```yaml
# .github/workflows/commitlint.yml
name: Commitlint

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  commitlint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      # wagoid/commitlint-github-action — 가장 널리 사용되는 Action
      - uses: wagoid/commitlint-github-action@v6
        with:
          configFile: commitlint.config.mjs
          failOnErrors: true
          helpURL: https://www.conventionalcommits.org/
```

> 💡 **알고 계셨나요?**: **Squash Merge**를 사용하는 팀이라면, 개별 커밋보다 **PR 제목**이 더 중요합니다. `amannn/action-semantic-pull-request@v5` Action을 사용하면 PR 제목이 Conventional Commits 형식인지 자동으로 검증할 수 있습니다.

## 실습: commitlint + Husky 세팅

```bash
# 1. 실습 프로젝트 생성
mkdir commit-convention-demo && cd commit-convention-demo
git init

# 2. npm 프로젝트 초기화
npm init -y

# 3. commitlint + Husky 설치
npm install --save-dev @commitlint/cli @commitlint/config-conventional husky

# 4. commitlint 설정 파일 생성
echo "export default { extends: ['@commitlint/config-conventional'] };" > commitlint.config.js

# 5. Husky 초기화
npx husky init

# 6. commit-msg 훅 설정
echo "npx --no -- commitlint --edit \$1" > .husky/commit-msg

# 7. 파일 추가
echo "# Commit Convention Demo" > README.md
git add .
git commit -m "chore: 프로젝트 초기화"
```

```output
[main (root-commit) a1b2c3d] chore: 프로젝트 초기화
 5 files changed, 1234 insertions(+)
```

```bash
# 8. 잘못된 커밋 시도 — 차단!
echo "hello" > test.txt
git add test.txt
git commit -m "update"
```

```error
⧗   input: update
✖   subject may not be empty [subject-empty]
✖   type may not be empty [type-empty]

✖   Found 2 problems, 0 warnings
```

```bash
# 9. 올바른 커밋 — 성공!
git commit -m "test: 테스트 파일 추가"
```

```output
[main def5678] test: 테스트 파일 추가
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt
```

## 더 깊이 알아보기

### Conventional Commits의 탄생 배경

Conventional Commits는 **Angular 프로젝트의 커밋 컨벤션**에서 출발했습니다. Google의 Angular 팀은 2013년부터 엄격한 커밋 메시지 규칙을 사용했는데, 이 규칙 덕분에 Changelog 자동 생성과 시맨틱 버저닝이 가능해졌죠. 이 아이디어가 너무 좋았기 때문에 2017년 Ben Coe 등이 Angular에 종속되지 않는 범용 규약으로 **Conventional Commits 1.0.0**을 정식 발표했습니다.

현재 Vue.js, Electron, Babel, Angular 등 수많은 오픈소스 프로젝트가 이 규약을 채택하고 있으며, [배포 자동화](../10-github-actions/04-cd.md)에서 배운 자동 릴리스와 결합하면 진정한 **"코드 → 배포" 완전 자동화**가 실현됩니다.

> 💡 **알고 계셨나요?**: Conventional Commits 규약을 따르면 `git log` 만으로도 다음 릴리스의 버전 번호를 예측할 수 있습니다. `feat:` 커밋이 있으면 마이너 업, `fix:`만 있으면 패치 업, `BREAKING CHANGE`가 있으면 메이저 업 — 사람이 판단할 필요가 없어요.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "모든 커밋이 `feat`이나 `fix`여야 하나요?" — 아닙니다! `docs`, `chore`, `ci`, `style` 등은 릴리스에 영향을 주지 않는 타입입니다. 문서를 수정했을 때 `feat`를 쓰면 불필요한 버전 업이 일어납니다. **변경의 성격에 맞는 타입**을 정확히 선택하세요.

> 🔥 **실무 팁**: commitizen이 번거롭다면, VS Code 확장 **"Conventional Commits"**을 설치해보세요. 커밋 시 드롭다운 메뉴에서 타입을 선택할 수 있어서 훨씬 편합니다. [GUI 도구 활용](./05-gui-tools.md)에서 더 다양한 확장을 소개합니다.

> 🔥 **실무 팁**: PR에서 **Squash Merge**를 사용하는 팀이라면, 개별 커밋보다 **PR 제목**이 더 중요합니다. PR 제목을 Conventional Commits 형식으로 작성하도록 규칙을 정하면, 스쿼시된 커밋 메시지가 자동으로 규약을 따르게 됩니다. [PR 관리](../06-pull-request/03-pr-management.md)에서 배운 Squash Merge와 자연스럽게 연결되죠.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| **Conventional Commits** | `타입(범위): 설명` 형식의 커밋 메시지 규약 |
| **타입** | feat, fix, docs, style, refactor, perf, test, build, ci, chore |
| **BREAKING CHANGE** | 하위 호환성이 깨지는 변경 → MAJOR 버전 업 |
| **commitlint** | 커밋 메시지 규칙 검증 도구 |
| **Husky** | Git Hook을 팀과 공유 가능하게 만드는 도구 |
| **commitizen (cz)** | 대화형으로 커밋 메시지를 생성하는 도구 |
| **release-please** | PR 기반 자동 릴리스 (Google) |
| **semantic-release** | 완전 자동 릴리스 (커밋 → 버전 → Changelog → 배포) |

## 다음 섹션 미리보기

커밋 메시지 규칙으로 **코드 변경의 기록**을 체계화했습니다. 하지만 코드의 품질을 지키는 건 결국 **사람의 눈**이 필요합니다. 다음 섹션 [코드 리뷰 문화](./03-review-culture.md)에서는 효과적인 리뷰 가이드라인, CODEOWNERS 활용, AI 리뷰 도구까지 — 팀의 **코드 리뷰 문화**를 만드는 방법을 배웁니다.

## 참고 자료

- [Conventional Commits 1.0.0](https://www.conventionalcommits.org/ko/v1.0.0/) - 공식 규약 (한국어)
- [commitlint Docs](https://commitlint.js.org/) - commitlint 공식 문서
- [Husky Docs](https://typicode.github.io/husky/) - Husky 공식 문서
- [release-please](https://github.com/googleapis/release-please) - Google의 자동 릴리스 도구
- [semantic-release Docs](https://semantic-release.gitbook.io/) - semantic-release 공식 문서
- [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/main/CONTRIBUTING.md#-commit-message-format) - Conventional Commits의 원조
