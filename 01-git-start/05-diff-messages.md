# 변경 추적과 커밋 메시지

> diff, show, 좋은 커밋 메시지 작성법

## 개요

커밋을 만드는 법은 배웠지만, "정확히 뭐가 바뀐 거지?"라는 질문에 답하려면 `git diff`가 필요합니다. 그리고 커밋 메시지를 어떻게 쓰느냐에 따라 미래의 나(또는 팀원)가 고마워하거나 원망하게 됩니다. 이 섹션에서는 변경 내용을 비교하는 방법과, 좋은 커밋 메시지를 작성하는 규칙을 배웁니다.

**선수 지식**: [커밋의 기본](./04-commit-basics.md)에서 배운 add, commit, status, log
**학습 목표**:
- `git diff`로 다양한 단계의 변경 사항을 비교할 수 있다
- `git show`로 특정 커밋의 내용을 확인할 수 있다
- 좋은 커밋 메시지의 작성 규칙을 이해하고 적용할 수 있다

## 왜 알아야 할까?

코드를 수정한 뒤 커밋하기 전에 **"내가 정확히 무엇을 바꿨는지"** 확인하는 것은 프로 개발자의 기본 습관입니다. 실수로 디버깅용 `console.log`를 남겨두거나, 의도하지 않은 파일을 수정한 채 커밋하는 일을 방지할 수 있거든요. 그리고 커밋 메시지는 **미래의 나에게 보내는 편지**입니다. 6개월 뒤에 "이 커밋은 왜 했지?"라고 궁금해질 때, 좋은 메시지가 답을 줍니다.

## 핵심 개념

### git diff — 무엇이 바뀌었는가?

> 💡 **비유**: `git diff`는 **두 장의 사진을 겹쳐서 틀린 그림 찾기**를 하는 것과 같습니다. 어떤 줄이 추가되었고, 어떤 줄이 삭제되었는지를 빨간색(삭제)과 초록색(추가)으로 명확히 보여줍니다.

`git diff`는 비교하는 대상에 따라 여러 가지로 사용됩니다:

```bash
# 1. 작업 디렉토리 vs 스테이징 영역 (아직 add 안 한 변경)
git diff

# 2. 스테이징 영역 vs 최근 커밋 (add 했지만 commit 안 한 변경)
git diff --staged

# 3. 작업 디렉토리 vs 최근 커밋 (모든 미커밋 변경)
git diff HEAD
```

> 이 세 가지 사용법을 정리하면:

| 명령어 | 비교 대상 | 용도 |
|--------|----------|------|
| `git diff` | 작업 디렉토리 ↔ 스테이징 | "아직 스테이징 안 한 변경은?" |
| `git diff --staged` | 스테이징 ↔ 최근 커밋 | "이번에 커밋될 내용은?" |
| `git diff HEAD` | 작업 디렉토리 ↔ 최근 커밋 | "마지막 커밋 이후 모든 변경은?" |

실제 출력을 살펴볼까요?

```bash
# 예시: README.md를 수정한 뒤
git diff
```

```output
diff --git a/README.md b/README.md
index 1234567..abcdefg 100644
--- a/README.md
+++ b/README.md
@@ -1,3 +1,5 @@
 # My Project

-간단한 프로젝트입니다.
+Git을 배우기 위한 프로젝트입니다.
+
+## 시작하기
```

출력의 각 부분이 의미하는 것:

| 요소 | 의미 |
|------|------|
| `--- a/README.md` | 변경 전 파일 (a) |
| `+++ b/README.md` | 변경 후 파일 (b) |
| `@@ -1,3 +1,5 @@` | 변경 위치 (1번째 줄부터, 전 3줄 → 후 5줄) |
| `-간단한 프로젝트입니다.` | 삭제된 줄 (빨간색) |
| `+Git을 배우기 위한...` | 추가된 줄 (초록색) |

#### 더 많은 diff 활용법

```bash
# 특정 파일만 비교
git diff README.md

# 두 커밋 사이의 차이
git diff abc1234 def5678

# 단어 단위로 차이 보기 (줄 단위보다 정밀)
git diff --word-diff

# 변경된 파일 이름만 보기
git diff --name-only

# 변경 통계만 보기 (몇 줄 추가/삭제)
git diff --stat
```

### git show — 특정 커밋 자세히 보기

`git show`는 특정 커밋의 **메타 정보와 변경 내용**을 함께 보여줍니다.

```bash
# 최근 커밋의 상세 정보
git show

# 특정 커밋의 상세 정보
git show a1b2c3d

# 특정 커밋에서 특정 파일의 내용만 보기
git show a1b2c3d:README.md
```

```output
commit a1b2c3d (HEAD -> main)
Author: 홍길동 <gildong@example.com>
Date:   Sat Feb 15 11:00:00 2026 +0900

    로그인 유효성 검사 추가

diff --git a/login.js b/login.js
index 1234567..abcdefg 100644
--- a/login.js
+++ b/login.js
@@ -10,6 +10,12 @@
 function handleLogin(email, password) {
+  // 이메일 형식 검증
+  if (!isValidEmail(email)) {
+    return { error: '올바른 이메일을 입력하세요' };
+  }
+
   return authenticate(email, password);
 }
```

### 좋은 커밋 메시지 작성법

> 💡 **비유**: 커밋 메시지는 **일기장**과 같습니다. "오늘 뭔가 했다"라고 쓰면 나중에 아무것도 기억나지 않지만, "오후에 로그인 버그를 고쳤다. 이메일 검증 로직이 빠져있었다"라고 쓰면 훨씬 유용하죠.

#### 나쁜 커밋 메시지 vs 좋은 커밋 메시지

| 나쁜 예 | 좋은 예 |
|---------|---------|
| `수정` | `로그인 시 이메일 유효성 검사 추가` |
| `버그 고침` | `회원가입 시 중복 이메일 체크 누락 수정` |
| `ㅇㅇ` | `README에 설치 가이드 추가` |
| `asdf` | `불필요한 console.log 제거` |
| `여러 가지 수정` | `결제 모듈 리팩토링 및 에러 처리 개선` |

#### 커밋 메시지 7가지 규칙

1. **제목과 본문을 빈 줄로 분리**한다
2. **제목은 50자 이내**로 작성한다
3. **제목 첫 글자는 대문자**로 (영문의 경우)
4. **제목 끝에 마침표를 넣지 않는다**
5. **제목은 명령문**으로 작성한다 ("Add feature" O, "Added feature" X)
6. **본문은 72자마다 줄바꿈**한다
7. 본문에 **"무엇을"과 "왜"**를 설명한다 ("어떻게"는 코드가 말해준다)

```bash
# 제목만 있는 간단한 커밋
git commit -m "회원가입 이메일 유효성 검사 추가"

# 제목 + 본문이 있는 상세한 커밋 (에디터가 열림)
git commit
```

에디터에서 작성하는 상세한 커밋 메시지 예시:

```console
회원가입 이메일 유효성 검사 추가

기존에는 이메일 형식 검증 없이 서버로 전송되어,
잘못된 이메일로 가입이 완료되는 문제가 있었습니다.

정규식 기반 클라이언트 측 검증을 추가하여
서버 요청 전에 형식을 확인합니다.

Resolves: #42
```

#### Conventional Commits (팀 프로젝트용)

팀에서는 커밋 메시지 형식을 통일하면 자동화에 큰 도움이 됩니다. **Conventional Commits**은 가장 널리 쓰이는 규약입니다:

```console
<타입>(<범위>): <설명>

<본문>

<꼬리말>
```

주요 타입:

| 타입 | 의미 | 예시 |
|------|------|------|
| `feat` | 새 기능 | `feat: 소셜 로그인 추가` |
| `fix` | 버그 수정 | `fix: 로그인 시 빈 이메일 허용 방지` |
| `docs` | 문서 변경 | `docs: API 사용법 README에 추가` |
| `style` | 포맷팅, 세미콜론 등 (기능 변경 X) | `style: 들여쓰기 통일` |
| `refactor` | 리팩토링 (기능 변경 X) | `refactor: 인증 모듈 구조 개선` |
| `test` | 테스트 추가/수정 | `test: 로그인 유닛 테스트 추가` |
| `chore` | 빌드, 도구 설정 등 | `chore: ESLint 설정 업데이트` |

> 🔥 **실무 팁**: Conventional Commits를 사용하면 `semantic-release` 같은 도구로 **버전 자동 관리**와 **Changelog 자동 생성**이 가능해집니다. 자세한 내용은 [커밋 메시지 컨벤션](../11-team-tools/02-commit-convention.md)에서 다룹니다.

## 실습: 직접 해보기

```bash
# 1. 기존 실습 저장소에서 계속 (또는 새로 만들기)
cd commit-practice

# 2. 파일 수정
echo "## 설치 방법" >> README.md
echo "npm install" >> README.md

# 3. diff로 변경 내용 확인
git diff

# 4. 스테이징
git add README.md

# 5. 스테이징된 변경 확인
git diff --staged

# 6. 좋은 메시지로 커밋
git commit -m "README에 설치 가이드 섹션 추가"

# 7. 방금 커밋한 내용 확인
git show

# 8. 전체 이력에서 변경 통계 보기
git log --stat --oneline
```

## 더 깊이 알아보기

### 커밋 메시지 문화의 변천사

초기 오픈소스 프로젝트에서는 커밋 메시지에 특별한 규칙이 없었습니다. 하지만 리눅스 커널처럼 수천 명이 참여하는 프로젝트에서는 일관된 메시지가 필수가 되었고, Linus Torvalds는 메일링 리스트에서 **"커밋 메시지의 첫 줄은 72자 이내의 요약이어야 한다"**는 원칙을 강조했습니다. 이것이 오늘날 대부분의 커밋 메시지 가이드라인의 기초가 되었죠.

2019년에 등장한 **Conventional Commits** 1.0.0은 이런 관행을 공식 규약으로 정리한 것입니다. Angular 프로젝트의 커밋 컨벤션에서 영감을 받아 만들어졌으며, 현재 수많은 오픈소스 프로젝트와 기업에서 채택하고 있습니다.

> 💡 **알고 계셨나요?**: GitHub에서 커밋 메시지의 제목이 72자를 넘으면 `...`으로 잘려서 표시됩니다. 50자 이내를 권장하는 이유가 여기에 있어요. `git log --oneline`에서도 깔끔하게 보이고, 다양한 도구와의 호환성도 좋아집니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`git diff`를 실행했는데 아무것도 안 나온다" — `git add`로 이미 스테이징했기 때문입니다. 스테이징된 변경을 보려면 `git diff --staged`(또는 `--cached`)를 사용하세요.

> 🔥 **실무 팁**: 커밋하기 직전에 `git diff --staged`로 최종 검토하는 습관을 들이세요. 의도하지 않은 디버깅 코드나 TODO 주석이 섞여 들어가는 것을 방지할 수 있습니다.

## 핵심 정리

| 명령어/개념 | 설명 |
|-------------|------|
| `git diff` | 스테이징 안 된 변경 사항 비교 |
| `git diff --staged` | 스테이징된 변경 사항 비교 (커밋 전 최종 확인) |
| `git diff HEAD` | 마지막 커밋 이후 모든 변경 비교 |
| `git show` | 특정 커밋의 상세 내용 확인 |
| 커밋 메시지 | 제목 50자 이내, 명령문, "무엇을/왜" 중심 |
| Conventional Commits | `feat`, `fix`, `docs` 등 타입 접두사 규약 |

## 다음 섹션 미리보기

Ch1의 모든 기초를 마스터했습니다! 다음 챕터 [Ch2. 파일과 히스토리 관리](../02-file-history/01-gitignore.md)에서는 `.gitignore`로 불필요한 파일을 제외하고, `restore`와 `reset`으로 실수를 되돌리고, `tag`로 중요한 시점을 표시하는 방법을 배웁니다.

## 참고 자료

- [Pro Git Book — 변경사항 확인하기](https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-%EC%88%98%EC%A0%95%ED%95%98%EA%B3%A0-%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0#_%EB%B3%80%EA%B2%BD_%EC%82%AC%ED%95%AD_%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0) - diff 사용법 공식 가이드
- [Conventional Commits 1.0.0](https://www.conventionalcommits.org/ko/v1.0.0/) - Conventional Commits 공식 규약 (한국어)
- [How to Write a Git Commit Message](https://cbea.ms/git-commit/) - Chris Beams의 커밋 메시지 7가지 규칙 (가장 유명한 가이드)
- [Git 공식 문서 — git-diff](https://git-scm.com/docs/git-diff) - diff의 모든 옵션 레퍼런스
