# Reset 심화

> soft, mixed, hard 차이, reset vs revert vs checkout

## 개요

[되돌리기 기초](../02-file-history/04-undo-basics.md)에서 reset과 revert를 간단히 다뤘습니다. 하지만 `git reset`에는 **세 가지 모드**가 있고, 각각 영향을 미치는 범위가 완전히 다릅니다. 이번 섹션에서는 reset의 내부 동작을 깊이 이해하고, 비슷해 보이는 revert, checkout, restore와의 차이를 명확히 정리합니다.

**선수 지식**: [되돌리기 기초](../02-file-history/04-undo-basics.md), [Reflog와 복구](./02-reflog.md)
**학습 목표**:
- `--soft`, `--mixed`, `--hard`의 차이를 정확히 구분한다
- reset이 영향을 미치는 세 영역(HEAD, 스테이징, 작업 디렉토리)을 이해한다
- reset, revert, checkout, restore, switch의 적합한 사용 상황을 판별한다
- 위험한 reset 실수를 방지하고 복구할 수 있다

## 왜 알아야 할까?

`git reset`은 Git에서 가장 **강력하면서 동시에 가장 혼동되는** 명령어입니다. "soft랑 mixed 차이가 뭐지?", "reset을 쓸지 revert를 쓸지 모르겠다", "checkout으로 파일 되돌리는 거랑 reset이랑 뭐가 다른 거야?" — 이런 질문을 해본 적 있다면, 이 섹션이 모든 혼란을 해소해줄 겁니다.

## 핵심 개념

### 개념 1: Git의 세 가지 영역 복습

reset을 이해하려면 Git의 **세 가지 영역**을 확실히 알아야 합니다:

> 💡 **비유**: Git의 세 영역을 **요리 과정**에 비유하면:
> - **작업 디렉토리(Working Directory)** = 도마 위: 재료를 손질하는 곳
> - **스테이징 영역(Staging Area/Index)** = 접시에 담기: 어떤 음식을 서빙할지 고르는 곳
> - **HEAD (Repository)** = 손님 테이블: 최종 서빙 완료 (커밋됨)

`git reset`은 이 세 영역 중 **어디까지 되돌릴지**를 모드로 결정합니다.

### 개념 2: reset의 세 가지 모드

```bash
# 현재 상태: 커밋 3개가 있고, HEAD가 C를 가리킴
# A → B → C (HEAD)
```

**`--soft`**: HEAD만 이동 (스테이징과 작업 디렉토리는 그대로)

```bash
git reset --soft HEAD~1
```

| 영역 | 변경 전 | 변경 후 |
|------|---------|---------|
| HEAD | C를 가리킴 | **B를 가리킴** |
| 스테이징 영역 | C의 내용 | C의 내용 (그대로) |
| 작업 디렉토리 | C의 내용 | C의 내용 (그대로) |

> 결과: C의 변경 사항이 **스테이징된 상태**로 남아 있습니다. 바로 `git commit`하면 C를 다시 만들 수 있어요.

**`--mixed`** (기본값): HEAD + 스테이징 영역 이동 (작업 디렉토리는 그대로)

```bash
git reset --mixed HEAD~1
# 또는 간단히:
git reset HEAD~1
```

| 영역 | 변경 전 | 변경 후 |
|------|---------|---------|
| HEAD | C를 가리킴 | **B를 가리킴** |
| 스테이징 영역 | C의 내용 | **B의 내용** |
| 작업 디렉토리 | C의 내용 | C의 내용 (그대로) |

> 결과: C의 변경 사항이 **unstaged 상태**로 남아 있습니다. `git add`를 다시 해야 커밋할 수 있어요.

**`--hard`**: HEAD + 스테이징 + 작업 디렉토리 모두 이동

```bash
# ⚠️ 주의: 작업 디렉토리의 변경 사항이 사라집니다!
git reset --hard HEAD~1
```

| 영역 | 변경 전 | 변경 후 |
|------|---------|---------|
| HEAD | C를 가리킴 | **B를 가리킴** |
| 스테이징 영역 | C의 내용 | **B의 내용** |
| 작업 디렉토리 | C의 내용 | **B의 내용** |

> 결과: C의 변경 사항이 **완전히 사라집니다**. 작업 디렉토리까지 깨끗이 되돌립니다.

### 한눈에 비교

| 모드 | HEAD | 스테이징 | 작업 디렉토리 | 위험도 |
|------|------|----------|---------------|--------|
| `--soft` | 이동 | 유지 | 유지 | 안전 |
| `--mixed` (기본) | 이동 | **초기화** | 유지 | 안전 |
| `--hard` | 이동 | **초기화** | **초기화** | **위험!** |

### 개념 3: 각 모드의 실전 활용

**`--soft` 활용**: 커밋 메시지를 고치거나 여러 커밋을 하나로 합치기

```bash
# 최근 3개 커밋을 하나로 합치기 (squash)
git reset --soft HEAD~3
git commit -m "세 커밋을 하나로 합침"
```

**`--mixed` 활용**: 스테이징을 취소하고 다시 선별적으로 add하기

```bash
# "아, 이 파일은 이 커밋에 포함하지 말걸..."
git reset HEAD~1
# 이제 원하는 파일만 다시 선택
git add src/feature.js
git commit -m "기능만 커밋"
git add src/test.js
git commit -m "테스트 추가"
```

**`--hard` 활용**: 모든 것을 깨끗이 되돌리기 (최후 수단)

```bash
# ⚠️ 작업 중인 내용이 전부 사라집니다!
# 로컬 변경을 완전히 버리고 원격 상태로 맞추기
git reset --hard origin/main
```

### 개념 4: reset vs revert

| 항목 | `reset` | `revert` |
|------|---------|----------|
| 방식 | 커밋을 **지우고** HEAD를 이동 | 변경을 **되돌리는 새 커밋** 생성 |
| 히스토리 | 변경됨 (커밋이 사라짐) | 보존됨 (새 커밋 추가) |
| push된 커밋 | **사용 금지** (force push 필요) | **안전하게 사용 가능** |
| 되돌리기 | reflog로 복구 | revert의 revert로 복구 |

```bash
# reset — 커밋 C를 히스토리에서 제거
# A → B → C (HEAD) → A → B (HEAD) — C는 히스토리에서 사라짐
git reset --hard HEAD~1

# revert — C를 되돌리는 새 커밋 D 생성
# A → B → C (HEAD) → A → B → C → D (HEAD) — D가 C의 변경을 취소
git revert HEAD
```

> 🔥 **실무 팁**: **황금 규칙** — 이미 `push`한 커밋은 `reset` 대신 `revert`를 사용하세요. reset 후 force push하면 팀원의 로컬 저장소와 충돌이 생깁니다.

### 개념 5: reset vs checkout vs restore vs switch

Git의 역사를 보면, `checkout`이 너무 많은 일을 했기 때문에 Git 2.23(2019년)부터 **역할이 분리**되었습니다:

| 용도 | 옛날 방식 | 현재 방식 (Git 2.23+) |
|------|-----------|---------------------|
| 브랜치 전환 | `git checkout <branch>` | `git switch <branch>` |
| 파일 되돌리기 | `git checkout -- <file>` | `git restore <file>` |
| 커밋 되돌리기 | `git reset` | `git reset` (변경 없음) |

```bash
# 파일 단위 되돌리기 — restore 사용 (권장)
git restore src/app.js                    # 작업 디렉토리의 변경 취소
git restore --staged src/app.js           # 스테이징 취소 (add 되돌리기)
git restore --source=HEAD~2 src/app.js    # 2커밋 전 상태로 파일 복원

# 브랜치 전환 — switch 사용 (권장)
git switch main
git switch -c new-branch
```

> ⚠️ **흔한 오해**: "checkout은 더 이상 쓰면 안 된다" — `checkout`은 여전히 동작하고, 없어지지 않습니다. 다만 `switch`와 `restore`가 **의도가 더 명확**해서 실수를 줄일 수 있어요. 새로운 프로젝트에서는 새 명령어를 사용하는 것이 좋습니다.

### 개념 6: 파일 단위 reset

`git reset`은 커밋뿐 아니라 **특정 파일의 스테이징을 취소**하는 데도 사용합니다:

```bash
# 특정 파일의 스테이징 취소 (작업 디렉토리는 그대로)
git reset HEAD src/app.js

# Git 2.23+ 에서는 restore 사용 권장
git restore --staged src/app.js
```

> **참고**: 파일 단위 reset은 `--soft`나 `--hard`를 사용할 수 없고, `--mixed` 동작만 합니다.

## 실습: 직접 해보기

```bash
# 1. 실습 준비
mkdir reset-practice && cd reset-practice
git init

# 2. 커밋 3개 만들기
echo "v1" > file.txt && git add . && git commit -m "버전 1"
echo "v2" > file.txt && git add . && git commit -m "버전 2"
echo "v3" > file.txt && git add . && git commit -m "버전 3"

# 3. --soft 테스트
git reset --soft HEAD~1
git status
```

```output
Changes to be committed:
  modified:   file.txt
```

```bash
# file.txt 내용은 "v3" 그대로, 스테이징된 상태
cat file.txt
```

```output
v3
```

```bash
# 다시 커밋으로 복구
git commit -m "버전 3 (재커밋)"

# 4. --mixed 테스트
git reset HEAD~1
git status
```

```output
Changes not staged for commit:
  modified:   file.txt
```

```bash
# file.txt 내용은 "v3" 그대로, 하지만 unstaged
# 다시 add하고 커밋
git add . && git commit -m "버전 3 (재커밋)"

# 5. --hard 테스트 (주의!)
git reset --hard HEAD~1
cat file.txt
```

```output
v2
```

```bash
# file.txt가 "v2"로 돌아갔습니다! "v3"은 사라짐
# 하지만 reflog로 복구 가능!
git reflog
git reset --hard HEAD@{1}
cat file.txt
```

```output
v3
```

## 더 깊이 알아보기

### checkout의 정체성 위기

`git checkout`이 `switch`와 `restore`로 분리된 데는 이유가 있습니다. 원래 `checkout`은 무려 **5가지 이상의 역할**을 했거든요:

1. 브랜치 전환: `git checkout main`
2. 브랜치 생성 + 전환: `git checkout -b feature`
3. 파일 복원: `git checkout -- file.txt`
4. 특정 커밋의 파일 복원: `git checkout abc123 -- file.txt`
5. Detached HEAD: `git checkout abc123`

이렇게 하나의 명령어가 너무 많은 일을 하다 보니, 실수하기 쉬웠습니다. 예를 들어 `git checkout feature`가 "feature 브랜치로 전환"인지 "feature라는 파일을 복원"인지 헷갈릴 수 있었죠. 그래서 Git 2.23에서 Elijah Newren 등의 주도로 명확한 역할 분리가 이루어졌습니다.

> 💡 **알고 계셨나요?**: `git switch`와 `git restore`를 도입한 Git 2.23의 릴리스 노트에는 "checkout 명령어가 스위스 군용 칼처럼 너무 많은 일을 한다"는 표현이 있었습니다. 새 명령어의 목표는 **"하나의 명령어는 하나의 일만"**이었죠.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`git reset --hard`가 유일한 위험한 reset이다" — `--mixed`(기본값)도 주의가 필요합니다! 스테이징 영역이 초기화되면서 정성스럽게 `add`한 파일 선택이 사라지거든요. 물론 작업 디렉토리의 파일 자체는 남아있어서 `--hard`보다는 안전합니다.

> ⚠️ **흔한 오해**: "reset과 revert는 같은 일을 한다" — 완전히 다릅니다! reset은 히스토리를 **변경**하고, revert는 히스토리를 **추가**합니다. 혼자 작업할 때는 reset이 편하지만, 팀 작업에서는 revert가 안전합니다.

> 🔥 **실무 팁**: `git reset --hard`를 사용하기 전에 습관적으로 **`git stash`를 먼저 실행**하세요. 만약의 경우 stash에서 복구할 수 있습니다. 혹은 `git reflog`의 현재 위치를 메모해두세요.

> 🔥 **실무 팁**: 스테이징 취소에는 `git restore --staged <file>`을 사용하세요. `git reset HEAD <file>`보다 의도가 명확하고, 실수로 커밋을 날릴 위험이 없습니다.

## 핵심 정리

| 명령어 | HEAD | 스테이징 | 작업 디렉토리 | 용도 |
|--------|------|----------|---------------|------|
| `reset --soft` | 이동 | 유지 | 유지 | 커밋 합치기, 메시지 수정 |
| `reset --mixed` | 이동 | 초기화 | 유지 | 스테이징 재선택 |
| `reset --hard` | 이동 | 초기화 | 초기화 | 완전 되돌리기 (위험!) |
| `revert` | — | — | — | 안전한 되돌리기 (새 커밋) |
| `restore` | — | — | 복원 | 파일 단위 되돌리기 |
| `restore --staged` | — | 복원 | — | 스테이징 취소 |

## 다음 섹션 미리보기

reset으로 개별 커밋을 되돌리는 방법을 배웠습니다. 그런데 만약 Git 히스토리 전체에서 **실수로 커밋한 비밀번호를 완전히 제거**해야 한다면? 다음 섹션 [히스토리 재작성](./04-history-rewrite.md)에서는 `git-filter-repo`와 BFG Cleaner를 사용해 히스토리를 대규모로 재작성하는 방법을 배웁니다.

## 참고 자료

- [Pro Git Book — Reset Demystified](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified) - reset의 내부 동작을 가장 잘 설명한 글
- [Git 공식 문서 — git-reset](https://git-scm.com/docs/git-reset) - 명령어 레퍼런스
- [Git 공식 문서 — git-restore](https://git-scm.com/docs/git-restore) - 파일 복원 명령어
- [GitHub Blog — Highlights from Git 2.23](https://github.blog/open-source/git/highlights-from-git-2-23/) - switch/restore 도입 배경
