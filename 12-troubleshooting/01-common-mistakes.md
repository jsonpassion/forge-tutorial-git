# 흔한 실수와 해결

> 잘못된 커밋, 브랜치 실수, push 취소, 되돌리기 시나리오

## 개요

Git을 사용하다 보면 누구나 실수를 합니다. 잘못된 브랜치에 커밋하거나, 민감한 파일을 push하거나, 실수로 브랜치를 삭제하는 일은 생각보다 흔하죠. 중요한 건 실수를 하지 않는 것이 아니라, **실수를 빠르게 복구하는 방법**을 아는 것입니다.

**선수 지식**: [Reflog와 복구](../09-history-internals/02-reflog.md), [Reset 심화](../09-history-internals/03-reset-deep.md)에서 배운 복구 도구
**학습 목표**:
- Git에서 가장 흔한 실수 유형을 파악한다
- 각 상황별 최적의 복구 전략을 선택할 수 있다
- push된 커밋도 안전하게 되돌릴 수 있다

## 왜 알아야 할까?

"Oh Shit, Git!?!" — 이 이름의 웹사이트가 있을 정도로 Git 실수는 보편적입니다. 실제로 이 사이트(ohshitgit.com)는 개발자 사이에서 가장 많이 공유되는 Git 자료 중 하나예요. 실수를 두려워하면 Git을 소극적으로 사용하게 되고, 그러면 오히려 더 큰 실수로 이어집니다. 이번 섹션에서 다루는 시나리오들을 익혀두면 어떤 상황에서도 침착하게 대응할 수 있습니다.

## 핵심 개념

### 시나리오 1: 커밋 메시지를 잘못 작성했을 때

> 💡 **비유**: 편지를 봉투에 넣고 봉한 직후, 오타를 발견한 상황입니다. 아직 우체통에 넣지 않았다면 봉투를 뜯고 고치면 되죠.

**아직 push하지 않은 경우** — `--amend`로 수정:

```bash
# 마지막 커밋 메시지를 수정 (에디터가 열림)
git commit --amend

# 메시지를 직접 지정
git commit --amend -m "올바른 커밋 메시지"
```

> ⚠️ **흔한 오해**: `--amend`는 커밋을 "수정"하는 것이 아니라 **새 커밋으로 교체**하는 것입니다. 커밋 해시가 변경되므로, 이미 push한 커밋에는 사용하지 마세요.

### 시나리오 2: 커밋에 파일을 빠뜨렸을 때

```bash
# 빠뜨린 파일을 스테이징
git add forgotten-file.js

# 이전 커밋에 합치기 (메시지는 유지)
git commit --amend --no-edit
```

### 시나리오 3: 잘못된 브랜치에 커밋했을 때

> 💡 **비유**: 택배를 잘못된 주소로 보냈을 때, 다행히 아직 배송 전이라면 택배를 회수해서 올바른 주소로 다시 보낼 수 있죠.

**main에 커밋했지만 feature 브랜치에 해야 했던 경우:**

```bash
# 1. 현재 커밋을 가진 새 브랜치 생성
git branch feature/correct-branch

# 2. main에서 잘못된 커밋 제거
git reset --hard HEAD~1
# ⚠️ 주의: --hard는 작업 디렉토리 변경사항도 삭제합니다

# 3. 올바른 브랜치로 이동
git switch feature/correct-branch
```

**이미 push한 경우 — revert 사용:**

```bash
# main에서 잘못된 커밋을 되돌리는 새 커밋 생성
git revert <잘못된-커밋-해시>
git push origin main

# 올바른 브랜치에서 cherry-pick
git switch feature/correct-branch
git cherry-pick <원래-커밋-해시>
```

### 시나리오 4: 실수로 브랜치를 삭제했을 때

```bash
# reflog에서 삭제된 브랜치의 마지막 커밋 찾기
git reflog

# 해당 커밋에서 브랜치 복구
git checkout -b recovered-branch <커밋-해시>
```

> 💡 **알고 계셨나요?**: Git의 reflog는 기본적으로 **90일** 동안 기록을 보관합니다. 그 안에만 복구하면 대부분의 실수는 되돌릴 수 있어요.

### 시나리오 5: push를 취소하고 싶을 때

**개인 브랜치인 경우:**

```bash
# 이전 상태로 되돌리기
git reset --hard HEAD~1

# 안전한 강제 push (다른 사람이 push했는지 확인)
git push --force-with-lease origin my-branch
```

**공유 브랜치(main)인 경우:**

```bash
# ⚠️ 절대 force push 하지 마세요!
# revert로 안전하게 되돌리기
git revert <잘못된-커밋-해시>
git push origin main
```

> 🔥 **실무 팁**: `--force` 대신 항상 `--force-with-lease`를 사용하세요. 이 옵션은 다른 사람이 push한 커밋이 있는지 확인하고, 있다면 push를 거부합니다. 팀의 작업을 실수로 덮어쓰는 것을 방지해줍니다.

### 시나리오 6: 잘못된 파일을 커밋했을 때

```bash
# .env 파일을 실수로 커밋한 경우
# Git 추적에서만 제거 (로컬 파일은 유지)
git rm --cached .env
echo ".env" >> .gitignore
git commit -m "Remove .env from tracking"
```

> ⚠️ **흔한 오해**: `git rm --cached`로 파일을 제거해도 **Git 히스토리에는 남아있습니다**. 민감한 정보가 포함된 파일이라면 [히스토리 재작성](../09-history-internals/04-history-rewrite.md)이 필요합니다.

## 실습: 직접 해보기

```bash
# 실습 저장소 만들기
mkdir git-mistakes-lab && cd git-mistakes-lab
git init

# 파일 생성과 첫 커밋
echo "Hello" > hello.txt
git add hello.txt
git commit -m "Initial commit"

# 의도적으로 실수하기 — 잘못된 메시지로 커밋
echo "World" >> hello.txt
git add hello.txt
git commit -m "오타가 있는 메세지"

# 커밋 메시지 수정
git commit --amend -m "hello.txt에 World 추가"

# 잘못된 브랜치에 커밋하기 실습
echo "Feature code" > feature.txt
git add feature.txt
git commit -m "Add feature"

# 새 브랜치로 옮기기
git branch feature/new-feature
git reset --hard HEAD~1
git switch feature/new-feature

# feature.txt가 여기에 있는지 확인
ls feature.txt
```

## 더 깊이 알아보기

Git의 reflog 시스템은 Linus Torvalds가 "개발자의 안전망"으로 설계한 기능입니다. 흥미롭게도 Git의 가비지 컬렉터(`git gc`)조차 reflog에 기록된 커밋은 기본 90일 동안 절대 삭제하지 않습니다. 이 설계 철학 덕분에 Git에서는 **진짜로 데이터를 잃기가 매우 어렵습니다** — 물론 `git gc --prune=now`를 직접 실행하지 않는다면요.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`git reset --hard`로 모든 것이 사라진다" — 아닙니다. 커밋된 내용은 reflog에 남아 있어 복구 가능합니다. 단, 커밋하지 않은 변경사항은 정말로 사라지니 주의하세요.

> 🔥 **실무 팁**: `git add -p` (패치 모드)를 습관화하세요. 파일 전체가 아닌 변경 단위(hunk)별로 스테이징할 수 있어, 실수로 불필요한 코드를 커밋하는 것을 방지합니다.

> 💡 **알고 계셨나요?**: GitHub과 GitLab에서는 main 브랜치에 **보호 규칙(branch protection)**을 설정할 수 있습니다. force push를 차단하고, PR 리뷰를 필수로 요구하면 많은 실수를 미연에 방지할 수 있어요.

## 핵심 정리

| 상황 | 해결 방법 | 안전도 |
|------|----------|--------|
| 커밋 메시지 수정 | `git commit --amend` | push 전에만 안전 |
| 파일 빠뜨림 | `git add` + `--amend --no-edit` | push 전에만 안전 |
| 잘못된 브랜치에 커밋 | `branch` + `reset --hard` | push 전에만 안전 |
| push된 커밋 되돌리기 | `git revert` | 항상 안전 |
| 브랜치 삭제 복구 | `reflog` + `checkout -b` | 90일 이내 안전 |
| 안전한 강제 push | `--force-with-lease` | 개인 브랜치만 |

## 다음 섹션 미리보기

실수를 복구하는 방법을 배웠으니, 다음 섹션에서는 저장소 자체가 커졌을 때 발생하는 문제와 해결책 — [대규모 저장소 관리](./02-large-repos.md)(Git LFS, sparse-checkout, shallow clone)를 다룹니다.

## 참고 자료

- [Oh Shit, Git!?!](https://ohshitgit.com/) - Git 실수 복구의 바이블, 간결하고 실용적인 가이드
- [Pro Git Book - 되돌리기](https://git-scm.com/book/ko/v2/Git의-기초-되돌리기) - 공식 되돌리기 문서
- [GitHub Docs - Resolving merge conflicts](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts) - PR 충돌 해결 가이드
- [GitKraken Blog - 7 Common Git Mistakes](https://www.gitkraken.com/blog/git-common-mistakes) - 흔한 실수와 시각적 해결 방법
