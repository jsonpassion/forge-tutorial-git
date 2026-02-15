# 되돌리기 기초

> reset, revert, amend — 실수를 바로잡는 세 가지 방법

## 개요

프로그래밍에서 실수는 피할 수 없습니다. 커밋 메시지에 오타가 있거나, 잘못된 파일을 커밋했거나, 아예 커밋 자체를 취소하고 싶을 때가 있죠. Git은 이런 상황을 위한 도구를 여러 가지 제공합니다. 각 도구는 **되돌리는 방식과 안전성**이 다르기 때문에 상황에 맞는 도구를 고르는 것이 중요합니다.

**선수 지식**: [커밋의 기본](../01-git-start/04-commit-basics.md)의 커밋 개념, [파일 상태 관리](./02-file-operations.md)의 `git restore`
**학습 목표**:
- `git commit --amend`로 마지막 커밋을 수정할 수 있다
- `git reset`의 세 가지 모드(soft, mixed, hard)를 이해하고 사용할 수 있다
- `git revert`로 안전하게 커밋을 취소할 수 있다
- 상황에 따라 어떤 방법을 선택해야 하는지 판단할 수 있다

## 왜 알아야 할까?

실수를 되돌리는 방법을 모르면, 잘못된 커밋 위에 "수정", "재수정", "진짜 최종" 같은 커밋을 쌓게 됩니다. 이력이 지저분해지는 것은 물론, 나중에 문제를 추적하기도 어려워지죠. **깔끔하게 되돌리는 능력**이야말로 Git을 제대로 활용하는 핵심 스킬입니다.

## 핵심 개념

### git commit --amend — 마지막 커밋 고치기

> 💡 **비유**: `--amend`는 **방금 보낸 문자 메시지를 수정하는 것**과 같습니다. 보내자마자 오타를 발견했을 때, 메시지를 삭제하고 고쳐서 다시 보내는 거죠. 단, 상대방이 이미 읽은 뒤(push한 뒤)라면 곤란해질 수 있습니다!

`--amend`는 **가장 최근 커밋**을 수정합니다. 두 가지 용도로 자주 쓰이죠:

**1. 커밋 메시지만 수정**

```bash
# 잘못된 메시지로 커밋한 경우
git commit -m "로긴 기능 추가"    # 오타!

# 메시지 수정
git commit --amend -m "로그인 기능 추가"
```

**2. 빠뜨린 파일 추가**

```bash
# 파일을 빠뜨리고 커밋한 경우
git commit -m "로그인 기능 추가"

# 빠뜨린 파일을 스테이징하고 amend
git add forgotten-file.js
git commit --amend --no-edit    # --no-edit: 메시지는 그대로 유지
```

> ⚠️ **흔한 오해**: "`--amend`는 커밋을 수정한다" — 사실 기존 커밋을 **수정**하는 게 아니라, 기존 커밋을 **새 커밋으로 교체**하는 것입니다. 커밋 해시가 바뀌거든요. 그래서 이미 `push`한 커밋을 `amend`하면, 원격 저장소와 로컬의 이력이 달라져서 문제가 됩니다. **아직 push하지 않은 커밋에만** 사용하세요.

### git reset — 커밋 되감기

> 💡 **비유**: `git reset`은 **동영상의 되감기 버튼**입니다. 특정 시점으로 되돌아갈 수 있는데, "되감은 구간의 내용을 어떻게 처리할 것인가"에 따라 세 가지 모드가 있습니다.

`git reset`은 HEAD(현재 브랜치의 포인터)를 이전 커밋으로 이동시킵니다. 세 가지 모드에 따라 **스테이징 영역**과 **작업 디렉토리**에 미치는 영향이 다릅니다:

```bash
# --soft: HEAD만 이동 (스테이징 & 작업 디렉토리 유지)
git reset --soft HEAD~1

# --mixed: HEAD + 스테이징 영역 초기화 (작업 디렉토리 유지) [기본값]
git reset HEAD~1
git reset --mixed HEAD~1    # 위와 동일

# ⚠️ --hard: HEAD + 스테이징 + 작업 디렉토리 모두 초기화
git reset --hard HEAD~1
```

세 모드의 차이를 표로 정리하면:

| 모드 | HEAD | 스테이징 영역 | 작업 디렉토리 | 용도 |
|------|------|-------------|-------------|------|
| `--soft` | 이동 | **유지** | **유지** | 커밋만 취소하고 다시 커밋하고 싶을 때 |
| `--mixed` | 이동 | **초기화** | **유지** | 커밋과 스테이징을 취소하고 파일은 보존할 때 |
| `--hard` | 이동 | **초기화** | **초기화** | 모든 것을 완전히 되돌리고 싶을 때 |

각 모드를 실제 시나리오로 이해해 볼까요?

#### --soft: "커밋만 취소, 나머지는 그대로"

```bash
# 상황: 두 개의 커밋을 하나로 합치고 싶다
git log --oneline
# c3d4e5f (HEAD) 버튼 스타일 수정
# b2c3d4e 로그인 폼 추가
# a1b2c3d 초기 커밋

# 마지막 2개 커밋을 취소 (변경 내용은 스테이징에 남음)
git reset --soft HEAD~2

# 하나의 커밋으로 다시 만들기
git commit -m "로그인 폼 및 스타일 추가"
```

#### --mixed: "커밋과 스테이징 취소, 파일은 보존"

```bash
# 상황: 의도하지 않은 파일이 커밋에 포함되었다
git reset HEAD~1

# 이제 파일들은 작업 디렉토리에 수정된 상태로 남아 있음
# 원하는 파일만 골라서 다시 커밋
git add correct-file.js
git commit -m "올바른 파일만 포함"
```

#### --hard: "모든 것을 되돌리기" (위험!)

```bash
# ⚠️ 주의: 커밋되지 않은 변경 사항이 영구적으로 삭제됩니다!
git reset --hard HEAD~1
```

> ⚠️ **흔한 오해**: "`git reset --hard`로 삭제된 커밋은 영원히 사라진다" — 커밋 자체는 즉시 삭제되지 않고, Git의 **reflog**에 일정 기간(기본 90일) 남아 있습니다. 정말 급하다면 [Reflog와 복구](../09-history-internals/02-reflog.md)에서 배울 방법으로 복구할 수 있어요. 하지만 **커밋되지 않은 변경 사항**은 reflog로도 복구할 수 없으니, `--hard`는 정말 신중하게 사용해야 합니다!

### git revert — 안전한 되돌리기

> 💡 **비유**: `git revert`는 **취소선을 긋는 것**과 같습니다. 이전에 쓴 내용을 지우개로 지우는 게 아니라(reset), ~~이렇게~~ 취소선을 그어서 "이 부분은 없던 일로 합니다"라는 **새로운 기록**을 남기는 거죠.

`git revert`는 특정 커밋의 변경을 **반대로 적용하는 새 커밋**을 만듭니다. 기존 이력을 수정하지 않으므로, **이미 push한 커밋을 되돌릴 때** 가장 안전한 방법입니다.

```bash
# 마지막 커밋 되돌리기
git revert HEAD

# 특정 커밋 되돌리기
git revert a1b2c3d

# 커밋하지 않고 되돌리기만 (나중에 수동 커밋)
git revert --no-commit a1b2c3d
```

revert를 실행하면 에디터가 열리면서 기본 메시지가 제안됩니다:

```console
Revert "로그인 기능 추가"

This reverts commit a1b2c3d4e5f6...
```

### reset vs revert — 언제 무엇을 쓸까?

이것이 가장 핵심적인 판단 기준입니다:

| 상황 | 추천 방법 | 이유 |
|------|----------|------|
| 아직 push하지 않은 커밋 | `git reset` | 이력을 깔끔하게 정리 가능 |
| 이미 push한 커밋 | `git revert` | 다른 사람의 이력을 망가뜨리지 않음 |
| 커밋 메시지만 수정 | `git commit --amend` | 가장 간단한 방법 |
| 마지막 커밋에 파일 추가 | `git commit --amend` | 불필요한 "파일 추가" 커밋 방지 |

> **핵심 원칙**: `reset`은 **로컬(개인) 브랜치**에서, `revert`는 **공유 브랜치**에서 사용하세요. 이미 다른 사람이 pull한 커밋을 `reset`으로 없애버리면, 팀원의 저장소와 이력이 꼬이게 됩니다.

## 실습: 직접 해보기

```bash
# 1. 실습용 저장소 준비
mkdir undo-practice && cd undo-practice
git init

# 2. 여러 커밋 만들기
echo "# 되돌리기 연습" > README.md
git add . && git commit -m "초기 커밋"

echo "첫 번째 기능" > feature.txt
git add . && git commit -m "첫 번째 기능 추가"

echo "두 번째 기능" >> feature.txt
git add . && git commit -m "두 번째 기능 추가"

echo "세 번째 기능" >> feature.txt
git add . && git commit -m "세 번째 기능 추가"

# 3. 현재 이력 확인
git log --oneline

# 4. amend 연습 — 메시지 수정
git commit --amend -m "세 번째 기능 추가 (개선 버전)"
git log --oneline

# 5. reset --soft 연습 — 커밋만 취소
git reset --soft HEAD~1
git status    # feature.txt가 staged 상태
git commit -m "두 번째 + 세 번째 기능 통합"

# 6. revert 연습 — 안전하게 되돌리기
git revert HEAD --no-edit
git log --oneline    # "Revert" 커밋이 추가됨
cat feature.txt      # 두 번째+세 번째 기능이 사라짐
```

## 더 깊이 알아보기

### 되돌리기 도구의 진화

Git 초기에는 되돌리기 명령어가 체계적이지 않았습니다. `git checkout`이 브랜치 전환도 하고, 파일 복원도 하고, 커밋 시점으로 이동도 하는 만능 도구였거든요. 시간이 지나면서 역할이 분리되었습니다:

- **파일 수준 되돌리기**: `git checkout -- <파일>` → `git restore <파일>` (Git 2.23+)
- **스테이징 취소**: `git reset HEAD <파일>` → `git restore --staged <파일>` (Git 2.23+)
- **커밋 수준 되돌리기**: `git reset`, `git revert`는 그대로 유지

이 정리 덕분에 이제는 "파일을 되돌리려면 `restore`, 커밋을 되돌리려면 `reset`/`revert`"라는 직관적인 구분이 가능해졌습니다. 더 깊은 내용은 [Reset 심화](../09-history-internals/03-reset-deep.md)에서 다룹니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`git reset --hard`가 가장 확실한 해결책이다" — `--hard`는 확실하긴 하지만 **위험합니다**. 커밋되지 않은 변경 사항은 영구적으로 삭제되며, reflog로도 복구할 수 없어요. 대부분의 상황에서는 `--soft`나 `--mixed`로 충분합니다.

> 🔥 **실무 팁**: `git revert`로 **머지 커밋**을 되돌릴 때는 `-m 1` 옵션이 필요합니다. 머지 커밋에는 두 개의 부모가 있어서, "어느 쪽을 기준으로 되돌릴 것인가"를 지정해야 하기 때문이에요. 보통 `-m 1`은 머지를 받은 브랜치(main)를 기준으로 되돌리는 것을 의미합니다.

## 핵심 정리

| 명령어 | 설명 |
|--------|------|
| `git commit --amend` | 마지막 커밋의 메시지나 내용 수정 (push 전에만!) |
| `git reset --soft HEAD~1` | 커밋만 취소 (변경은 스테이징에 유지) |
| `git reset HEAD~1` | 커밋 + 스테이징 취소 (변경은 작업 디렉토리에 유지) |
| `git reset --hard HEAD~1` | 커밋 + 스테이징 + 작업 디렉토리 모두 되돌림 (위험!) |
| `git revert <커밋>` | 해당 커밋을 취소하는 새 커밋 생성 (push된 커밋에 안전) |

## 다음 섹션 미리보기

실수를 바로잡는 방법을 배웠으니, Ch2의 마지막 주제인 **태그**를 알아봅시다. 다음 섹션 [태그](./05-tags.md)에서는 `git tag`로 중요한 시점을 표시하고, 시맨틱 버저닝으로 릴리스를 관리하는 방법을 배웁니다.

## 참고 자료

- [Pro Git Book — 되돌리기](https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-%EB%90%98%EB%8F%8C%EB%A6%AC%EA%B8%B0) - 되돌리기 공식 가이드
- [Git 공식 문서 — git-reset](https://git-scm.com/docs/git-reset) - reset의 모든 옵션 레퍼런스
- [Git 공식 문서 — git-revert](https://git-scm.com/docs/git-revert) - revert의 모든 옵션 레퍼런스
- [Atlassian Git Tutorial — Undoing Changes](https://www.atlassian.com/git/tutorials/undoing-changes) - reset, revert, checkout 비교 가이드
