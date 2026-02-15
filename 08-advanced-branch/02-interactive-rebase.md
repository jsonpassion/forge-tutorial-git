# Interactive Rebase

> squash, fixup, reword, edit, drop — 히스토리 다듬기

## 개요

일반 rebase가 "브랜치를 옮기는 것"이었다면, **Interactive Rebase**는 "커밋 히스토리를 편집하는 것"입니다. 커밋 메시지를 고치고, 여러 커밋을 하나로 합치고, 순서를 바꾸고, 심지어 커밋을 쪼갤 수도 있어요. Git에서 가장 강력한 히스토리 편집 도구입니다.

**선수 지식**: [Rebase 기초](./01-rebase.md)에서 배운 rebase의 원리와 황금 규칙
**학습 목표**:
- Interactive Rebase의 6가지 명령(pick, reword, edit, squash, fixup, drop)을 이해한다
- 커밋을 합치고, 메시지를 수정하고, 순서를 변경할 수 있다
- autosquash로 자동화된 커밋 정리를 할 수 있다
- `--update-refs`로 스택 브랜치를 효율적으로 관리한다

## 왜 알아야 할까?

코드를 작성하다 보면 커밋이 지저분해집니다. "WIP", "fix typo", "오타 수정", "리뷰 반영" — 이런 커밋이 쌓이면 나중에 히스토리를 읽기 어려워지죠. PR을 올리기 전에 **Interactive Rebase로 커밋을 정리**하면, 리뷰어가 이해하기 쉬운 깔끔한 히스토리를 만들 수 있습니다.

## 핵심 개념

### 개념 1: Interactive Rebase 시작하기

> 💡 **비유**: Interactive Rebase는 **영화 편집**과 같습니다. 촬영한 장면(커밋)들을 편집실에서 순서를 바꾸고, 여러 장면을 하나로 합치고, 불필요한 장면을 삭제하고, 자막(커밋 메시지)을 수정하는 거예요. 편집이 끝나면 깔끔한 최종본이 완성됩니다.

```bash
# 최근 N개의 커밋을 편집
git rebase -i HEAD~3

# 특정 브랜치 기준으로 그 이후 커밋 편집
git rebase -i main
```

실행하면 에디터가 열리고 **할 일 목록(todo list)**이 나타납니다:

```bash
pick a1b2c3d Add user model
pick e4f5g6h Add login form
pick i7j8k9l Fix typo in login
```

> ⚠️ **주의**: 목록의 순서는 `git log`와 **반대**입니다. 가장 **오래된 커밋이 위에**, 최신이 아래에 있어요.

### 개념 2: 6가지 명령어

`pick` 대신 다른 명령어로 바꾸면 해당 커밋에 대한 동작이 변경됩니다:

| 명령어 | 축약 | 동작 |
|--------|------|------|
| **pick** | p | 커밋을 그대로 사용 (기본값) |
| **reword** | r | 커밋은 유지, **메시지만 수정** |
| **edit** | e | 커밋에서 **일시 정지** — 내용 수정, 커밋 분할 가능 |
| **squash** | s | 이전 커밋에 **합치기** — 메시지 편집 기회 제공 |
| **fixup** | f | 이전 커밋에 **합치기** — 메시지 버림 (조용히 합침) |
| **drop** | d | 커밋 **삭제** (줄 자체를 지워도 동일) |

추가로 `exec` (x) 명령으로 셸 명령을 실행할 수도 있습니다.

### 개념 3: reword — 커밋 메시지 수정

```bash
git rebase -i HEAD~3
```

에디터에서:
```bash
reword a1b2c3d Add usr model    # "usr" → "user"로 오타 수정
pick e4f5g6h Add login form
pick i7j8k9l Fix typo in login
```

저장하면 Git이 해당 커밋의 메시지 편집 화면을 열어줍니다. 메시지를 "Add user model"로 고치고 저장하면 끝!

### 개념 4: squash & fixup — 커밋 합치기

PR 전에 가장 많이 사용하는 기능입니다. 여러 작은 커밋을 하나의 의미 있는 커밋으로 합칩니다.

```bash
git rebase -i HEAD~4
```

에디터에서:
```bash
pick a1b2c3d Add login feature
squash e4f5g6h Fix login validation
squash i7j8k9l Add error message
fixup m0n1o2p Fix typo
```

- `squash`: 위의 커밋에 합치면서, 두 커밋의 메시지를 **함께 편집**
- `fixup`: 위의 커밋에 합치면서, 이 커밋의 메시지를 **버림** (더 깔끔)

결과: 4개 커밋이 1개로 합쳐집니다.

> 🔥 **실무 팁**: "이 커밋의 메시지는 의미 없고, 앞 커밋의 보완일 뿐" → `fixup` 사용. "두 커밋의 내용을 합치면서 새 메시지를 작성하고 싶다" → `squash` 사용.

### 개념 5: edit — 커밋 수정 & 분할

`edit`은 해당 커밋에서 rebase를 **일시 정지**합니다. 이 상태에서 커밋 내용을 수정하거나, 하나의 커밋을 여러 개로 **쪼갤** 수 있어요.

**커밋 분할(split) 예시**:

```bash
git rebase -i HEAD~3
```

에디터에서:
```bash
edit a1b2c3d Add model and controller    # 이 커밋을 두 개로 분할
pick e4f5g6h Add views
```

저장하면 Git이 해당 커밋에서 멈춥니다:

```bash
# 1. 현재 커밋을 되돌리되 변경 사항은 유지
git reset HEAD^

# 2. 첫 번째 부분만 커밋
git add src/model.js
git commit -m "Add user model"

# 3. 두 번째 부분 커밋
git add src/controller.js
git commit -m "Add user controller"

# 4. rebase 계속
git rebase --continue
```

### 개념 6: 커밋 순서 변경 & 삭제

에디터에서 **줄의 순서를 바꾸면** 커밋 순서가 변경됩니다. **줄을 삭제하면** 커밋이 제거됩니다 (`drop`과 동일).

```bash
# 원래 순서:
pick a1b2c3d Feature A
pick e4f5g6h Feature B
pick i7j8k9l Feature C

# 순서 변경 + Feature B 삭제:
pick i7j8k9l Feature C
pick a1b2c3d Feature A
# e4f5g6h 줄 삭제 = Feature B 삭제
```

> ⚠️ **주의**: 순서를 바꾸면 **충돌이 발생할 수 있습니다**. 특히 뒤의 커밋이 앞의 커밋에 의존하는 경우에요. 충돌이 나면 `--abort`로 취소할 수 있습니다.

### 개념 7: Autosquash — 자동 정리

`fixup!`이나 `squash!` 접두사로 커밋하면, Interactive Rebase에서 **자동으로 올바른 위치에 배치**됩니다.

```bash
# 1. 원래 커밋
git commit -m "Add login feature"

# 2. 나중에 이 커밋의 수정 사항을 만들 때
git commit --fixup=<원래-커밋-해시>
```

```output
[feature a1b2c3d] fixup! Add login feature
```

```bash
# 3. interactive rebase에서 자동으로 정렬
git rebase -i --autosquash main
```

에디터가 열리면 fixup 커밋이 자동으로 올바른 위치에 배치되어 있습니다:

```bash
pick a1b2c3d Add login feature
fixup b2c3d4e fixup! Add login feature    # 자동으로 여기에 배치!
pick c3d4e5f Other changes
```

```bash
# autosquash를 기본값으로 설정 (강력 추천)
git config --global rebase.autoSquash true
```

### 개념 8: --update-refs (Git 2.38+)

여러 브랜치가 연결된 **스택 브랜치**를 작업할 때, rebase하면 중간 브랜치 포인터가 뒤처지는 문제가 있었습니다. `--update-refs`를 사용하면 **모든 중간 브랜치 포인터가 자동으로 업데이트**됩니다.

```bash
# 스택 브랜치 리베이스 시 자동으로 중간 브랜치 업데이트
git rebase -i --update-refs main

# 기본값으로 설정
git config --global rebase.updateRefs true
```

## 실습: Interactive Rebase로 히스토리 정리하기

```bash
# 1. 지저분한 커밋 히스토리 만들기
git switch -c feature/cleanup
echo "model" > model.js && git add . && git commit -m "Add user model"
echo "controller" > ctrl.js && git add . && git commit -m "Add controller"
echo "fix" >> model.js && git add . && git commit -m "Fix typo in model"
echo "view" > view.js && git add . && git commit -m "Add views"
echo "test" > test.js && git add . && git commit -m "WIP test"

# 2. interactive rebase 시작
git rebase -i HEAD~5
```

에디터에서 다음과 같이 수정:
```bash
pick a1b2c3d Add user model
fixup c3d4e5f Fix typo in model       # model 커밋에 합침
pick b2c3d4e Add controller
pick d4e5f6g Add views
drop e5f6g7h WIP test                 # 불필요한 WIP 커밋 삭제
```

```bash
# 3. 정리된 히스토리 확인
git log --oneline
```

```output
d4e5f6g (HEAD -> feature/cleanup) Add views
c3d4e5f Add controller
b2c3d4e Add user model
```

5개 커밋이 깔끔한 3개로 정리되었습니다!

## 더 깊이 알아보기

### exec — 커밋별 테스트 실행

`exec` 명령으로 각 커밋이 독립적으로 빌드/테스트가 되는지 확인할 수 있습니다:

```bash
# 모든 커밋마다 테스트 실행
git rebase -i HEAD~5 --exec "npm test"
```

테스트가 실패하면 rebase가 멈추고, 해당 커밋을 수정할 수 있습니다. 이 기능은 **"모든 커밋이 빌드 가능한 상태여야 한다"**는 원칙을 지키는 데 매우 유용해요.

### 히스토리 재작성에 대한 Git의 철학

Interactive Rebase는 Git 초기부터 있었지만, 당시에는 매우 급진적인 기능이었습니다. Subversion이나 CVS에서는 커밋 히스토리가 **불변**이었거든요. "히스토리는 있는 그대로 보존해야 한다"는 전통적 관점과, "히스토리는 의미 있고 깔끔해야 한다"는 Git의 관점이 대립했고, 이 논쟁은 여전히 진행 중입니다.

> 💡 **알고 계셨나요?**: Git 2.38(2022년)에 추가된 `--update-refs` 옵션은 **스택 PR 워크플로우**를 위해 만들어졌습니다. Graphite, Stacked PRs 같은 도구들이 이 기능 위에 구축되어, 하나의 큰 PR 대신 여러 작은 PR을 체인으로 만드는 워크플로우를 지원합니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "Interactive Rebase는 위험하다" — rebase 중 언제든 `git rebase --abort`로 **완전히 취소**할 수 있고, 잘못된 rebase도 `git reflog`로 복구할 수 있습니다. 안전장치가 있으니 두려워하지 마세요.

> 🔥 **실무 팁**: **PR 올리기 직전**에 Interactive Rebase를 하세요. "WIP", "fix", "oops" 같은 커밋을 정리하고, 각 커밋이 하나의 논리적 단위가 되도록 만들면 리뷰어가 훨씬 편합니다.

> 🔥 **실무 팁**: `git commit --fixup=<hash>`와 `--autosquash`를 습관화하세요. 작업 중에 이전 커밋을 수정해야 할 때 fixup 커밋을 만들어두고, PR 전에 한 번 `git rebase -i --autosquash`로 정리하면 매우 효율적입니다.

## 핵심 정리

| 명령어 | 동작 |
|--------|------|
| `pick` (p) | 커밋 그대로 사용 |
| `reword` (r) | 커밋 메시지만 수정 |
| `edit` (e) | 커밋에서 정지 — 수정/분할 가능 |
| `squash` (s) | 이전 커밋에 합침 — 메시지 합쳐서 편집 |
| `fixup` (f) | 이전 커밋에 합침 — 메시지 버림 |
| `drop` (d) | 커밋 삭제 |
| `exec` (x) | 셸 명령어 실행 |
| `--autosquash` | fixup!/squash! 접두사 커밋 자동 배치 |
| `--update-refs` | 스택 브랜치 포인터 자동 업데이트 |

## 다음 섹션 미리보기

Interactive Rebase로 히스토리를 자유자재로 편집하는 법을 배웠습니다. 하지만 때로는 브랜치 전체가 아니라 **특정 커밋 하나만** 가져오고 싶을 때가 있어요. [Cherry-pick](./03-cherry-pick.md)에서는 원하는 커밋만 골라서 적용하는 방법과, 핫픽스/백포팅 등의 활용 시나리오를 배웁니다.

## 참고 자료

- [Pro Git Book — 히스토리 재작성](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History) - Interactive Rebase 상세 가이드
- [Git 공식 문서 — git-rebase](https://git-scm.com/docs/git-rebase) - rebase 명령어 전체 옵션
- [thoughtbot — Interactive Rebase](https://thoughtbot.com/blog/git-interactive-rebase-squash-amend-rewriting-history) - 실전 Interactive Rebase 가이드
- [Andrew Lock — Autosquash로 부드러운 Rebase](https://andrewlock.net/smoother-rebases-with-auto-squashing-git-commits/) - autosquash 활용법
