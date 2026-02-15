# 커밋의 기본

> add, commit, status, log — 변경 사항을 기록하는 법

## 개요

이전 섹션에서 첫 커밋을 만들어 봤는데요, 이번에는 Git의 핵심 명령어 네 가지를 본격적으로 익혀봅시다. `add`, `commit`, `status`, `log` — 이 네 가지만 능숙해져도 Git의 80%는 쓸 수 있습니다.

**선수 지식**: [첫 번째 저장소](./03-first-repo.md)에서 배운 3단계 구조(작업 디렉토리 → 스테이징 → 저장소)
**학습 목표**:
- `git add`의 다양한 사용법을 익힌다
- `git commit`으로 의미 있는 커밋을 만들 수 있다
- `git status`와 `git log`로 현재 상태와 이력을 확인할 수 있다

## 왜 알아야 할까?

커밋은 Git에서 **가장 기본이 되는 단위**입니다. 프로젝트의 모든 이력은 커밋의 연속으로 이루어지죠. 잘 만든 커밋은 나중에 버그를 추적하거나, 특정 시점으로 되돌리거나, 팀원에게 변경 의도를 전달할 때 빛을 발합니다. 반대로 엉성한 커밋은 나중에 큰 고통이 됩니다.

## 핵심 개념

### git add — 장바구니에 담기

> 💡 **비유**: `git add`는 **이사할 때 박스에 짐을 담는 과정**입니다. 방 안의 모든 물건을 한 박스에 넣을 수도 있고, 종류별로 나눠 담을 수도 있죠. 어떻게 담느냐에 따라 나중에 찾기가 편해집니다.

```bash
# 특정 파일 하나 스테이징
git add README.md

# 여러 파일 한 번에 스테이징
git add file1.txt file2.txt

# 현재 디렉토리의 모든 변경 사항 스테이징
git add .

# 특정 확장자 파일 모두 스테이징
git add *.js

# 변경된 파일만 스테이징 (새 파일 제외)
git add -u
```

> ⚠️ **흔한 오해**: "`git add .`과 `git add -A`는 같다" — 거의 같지만 미묘한 차이가 있었습니다. Git 2.x부터는 둘 다 모든 변경(추가/수정/삭제)을 스테이징합니다. 하지만 **의도하지 않은 파일이 포함될 수 있으니** 커밋 전 반드시 `git status`로 확인하세요.

#### 부분 스테이징 — 파일의 일부분만 골라 담기

하나의 파일에서 여러 곳을 수정했는데, 일부 변경만 커밋하고 싶을 때가 있습니다:

```bash
# 파일 내 변경 사항을 하나씩 확인하며 선택적으로 스테이징
git add -p README.md
```

```output
diff --git a/README.md b/README.md
--- a/README.md
+++ b/README.md
@@ -1,3 +1,5 @@
 # My Project
+
+새로운 기능을 추가했습니다.
Stage this hunk [y,n,q,a,d,s,e,?]?
```

`y`(yes), `n`(no), `s`(split) 등으로 변경 블록(hunk)을 하나씩 선택할 수 있습니다.

### git commit — 스냅샷 찍기

> 💡 **비유**: `git commit`은 **사진 찍기**와 같습니다. 스테이징 영역에 올려둔 파일들의 현재 모습을 "찰칵!" 하고 영구 보존하는 거예요. 사진에 날짜와 설명(메시지)이 함께 기록됩니다.

```bash
# 기본 커밋 (메시지를 인라인으로)
git commit -m "로그인 페이지 레이아웃 추가"

# 에디터에서 상세한 메시지 작성
git commit

# add + commit 한 번에 (추적 중인 파일만, 새 파일은 제외)
git commit -am "오타 수정"
```

> 🔥 **실무 팁**: `git commit -am`은 편리하지만 **새로 만든 파일(Untracked)**은 포함하지 않습니다. 새 파일이 있다면 반드시 `git add`를 먼저 해주세요.

#### 커밋에 포함되는 정보

하나의 커밋에는 다음 정보가 기록됩니다:

| 항목 | 설명 | 예시 |
|------|------|------|
| 해시(SHA) | 커밋의 고유 식별자 (40자) | `a1b2c3d4e5f6...` |
| 작성자 | 이름과 이메일 | `홍길동 <gildong@example.com>` |
| 날짜 | 커밋 시점 | `2026-02-15 10:30:00` |
| 메시지 | 변경 내용 설명 | `로그인 페이지 레이아웃 추가` |
| 부모 커밋 | 이전 커밋의 해시 | `f7e8d9c0...` |
| 스냅샷 | 프로젝트 전체 상태 | (트리 객체로 저장) |

### git status — 현재 상태 파악하기

`git status`는 **가장 자주 쓰게 될 명령어**입니다. 현재 어떤 파일이 어떤 상태에 있는지 한눈에 보여줍니다.

```bash
# 기본 상태 확인
git status
```

```output
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   index.html

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   style.css

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	app.js
```

```bash
# 짧은 형식으로 보기 (실무에서 자주 사용)
git status -s
```

```output
M  index.html
 M style.css
?? app.js
```

짧은 형식의 기호 의미:

| 기호 | 의미 |
|------|------|
| `??` | 추적 안 됨 (Untracked) |
| `A` | 새로 추가됨 (Staged) |
| `M` (왼쪽) | 수정 후 스테이징됨 |
| `M` (오른쪽) | 수정됨 (스테이징 안 됨) |
| `D` | 삭제됨 |

### git log — 이력 들여다보기

`git log`는 커밋 이력을 시간 역순(최신 → 과거)으로 보여줍니다.

```bash
# 기본 로그 (상세)
git log
```

```output
commit a1b2c3d (HEAD -> main)
Author: 홍길동 <gildong@example.com>
Date:   Sat Feb 15 10:30:00 2026 +0900

    로그인 페이지 레이아웃 추가

commit f7e8d9c
Author: 홍길동 <gildong@example.com>
Date:   Sat Feb 15 10:00:00 2026 +0900

    프로젝트 초기화
```

```bash
# 한 줄로 보기 (가장 많이 사용하는 옵션)
git log --oneline
```

```output
a1b2c3d (HEAD -> main) 로그인 페이지 레이아웃 추가
f7e8d9c 프로젝트 초기화
```

```bash
# 최근 N개만 보기
git log --oneline -5

# 그래프로 브랜치 구조 시각화
git log --oneline --graph --all

# 특정 파일의 변경 이력만 보기
git log --oneline -- README.md

# 통계 정보 포함 (몇 줄 추가/삭제)
git log --stat -3
```

## 실습: 직접 해보기

여러 번의 커밋을 만들고 이력을 확인하는 실습입니다:

```bash
# 1. 실습용 저장소 만들기
mkdir commit-practice && cd commit-practice
git init

# 2. 첫 번째 파일 만들고 커밋
echo "# 커밋 연습" > README.md
git add README.md
git commit -m "프로젝트 시작: README 추가"

# 3. 두 번째 파일 추가
echo "console.log('Hello!');" > app.js
git add app.js
git commit -m "기본 앱 파일 추가"

# 4. 파일 수정 후 세 번째 커밋
echo "console.log('World!');" >> app.js
echo "body { margin: 0; }" > style.css
git add .
git commit -m "앱 기능 확장 및 스타일 추가"

# 5. 이력 확인
git log --oneline
```

```output
c3d4e5f (HEAD -> main) 앱 기능 확장 및 스타일 추가
b2c3d4e 기본 앱 파일 추가
a1b2c3d 프로젝트 시작: README 추가
```

```bash
# 6. 통계 포함 로그
git log --stat
```

## 더 깊이 알아보기

### 커밋 해시의 비밀

모든 커밋에는 `a1b2c3d...`와 같은 **SHA-1 해시**가 부여됩니다. 이 40자리 문자열은 커밋의 내용(파일 스냅샷, 메시지, 시간, 부모 커밋 등)을 기반으로 계산되기 때문에, 내용이 조금이라도 다르면 완전히 다른 해시가 생성됩니다. 덕분에 Git은 **데이터 변조를 자동으로 감지**할 수 있죠.

> 💡 **알고 계셨나요?**: SHA-1은 160비트(40자리 16진수) 해시를 생성하는데, 가능한 해시의 수는 약 $2^{160}$개입니다. 이론적으로 같은 해시가 나올 확률(충돌)은 사실상 0에 가깝습니다. 그럼에도 Git은 보안을 위해 SHA-256으로의 전환을 준비하고 있습니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "커밋은 변경된 파일만 저장한다" — Git의 커밋은 **프로젝트 전체의 스냅샷**입니다. 다만 변경되지 않은 파일은 이전 스냅샷의 참조만 저장하므로, 실제 저장 공간은 효율적으로 관리됩니다.

> 🔥 **실무 팁**: 커밋은 **작고 자주** 하는 것이 좋습니다. "로그인 기능 구현 + 버그 수정 + 스타일 변경"을 하나의 커밋에 담지 마세요. 각각을 별도의 커밋으로 분리하면, 나중에 특정 변경만 되돌리거나 찾기가 훨씬 쉬워집니다.

> 🔥 **실무 팁**: `git log`에 별칭(alias)을 설정하면 편리합니다:
> ```bash
> git config --global alias.lg "log --oneline --graph --all --decorate"
> ```
> 이제 `git lg`만 입력하면 예쁜 그래프 로그를 볼 수 있습니다!

## 핵심 정리

| 명령어 | 설명 |
|--------|------|
| `git add <file>` | 파일을 스테이징 영역에 추가 |
| `git add .` | 모든 변경 사항을 스테이징 |
| `git add -p` | 변경 사항을 부분적으로 선택하여 스테이징 |
| `git commit -m "메시지"` | 스테이징된 내용을 커밋 |
| `git commit -am "메시지"` | 추적 중인 수정 파일을 add+commit 동시 수행 |
| `git status` | 현재 파일 상태 확인 |
| `git status -s` | 짧은 형식으로 상태 확인 |
| `git log --oneline` | 커밋 이력을 한 줄씩 표시 |

## 다음 섹션 미리보기

커밋을 만드는 법을 배웠으니, 이제 **무엇이 바뀌었는지 자세히 살펴보는 법**을 알아볼 차례입니다. 다음 섹션 [변경 추적과 커밋 메시지](./05-diff-messages.md)에서는 `git diff`로 변경 내용을 비교하고, 좋은 커밋 메시지를 작성하는 규칙을 배웁니다.

## 참고 자료

- [Pro Git Book — Git 기초: 수정하고 저장소에 저장하기](https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-%EC%88%98%EC%A0%95%ED%95%98%EA%B3%A0-%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0) - add와 commit의 공식 가이드
- [Git 공식 문서 — git-log](https://git-scm.com/docs/git-log) - git log의 모든 옵션
- [Atlassian Git Tutorial — Saving Changes](https://www.atlassian.com/git/tutorials/saving-changes/git-commit) - 커밋 개념을 시각적으로 설명
