# 브랜치 생성과 전환

> branch, switch, checkout — 브랜치 만들고 이동하기

## 개요

앞서 브랜치가 "커밋을 가리키는 가벼운 포인터"라는 것을 배웠습니다. 이제 실제로 브랜치를 만들고, 브랜치 사이를 자유롭게 오가는 방법을 익힐 차례입니다. Git에는 브랜치를 다루는 명령어가 여러 개 있는데요, 어떤 걸 써야 할지 헷갈리는 분들이 많습니다. 이 섹션에서 깔끔하게 정리해드리겠습니다.

**선수 지식**: [브랜치란?](./01-branch-concept.md)에서 배운 브랜치 포인터와 HEAD 개념
**학습 목표**:
- `git branch`로 브랜치를 생성하고 목록을 확인할 수 있다
- `git switch`와 `git checkout`의 차이를 이해하고 상황에 맞게 사용할 수 있다
- 브랜치를 만들면서 동시에 전환하는 방법을 익힌다

## 왜 알아야 할까?

개발할 때 "새 기능 브랜치 만들어서 작업하고, 끝나면 다시 main으로 돌아오기" — 이 흐름은 하루에도 수십 번 반복됩니다. 브랜치를 빠르고 정확하게 만들고 전환하는 건 Git 사용의 기본 중의 기본이죠. 여기서 실수하면 엉뚱한 브랜치에 커밋하거나, 작업 중인 변경 사항을 날리는 일이 벌어질 수 있습니다.

## 핵심 개념

### 브랜치 만들기: `git branch`

> 💡 **비유**: `git branch`는 **갈림길에 새 이정표를 세우는 것**입니다. 이정표를 세웠다고 해서 그쪽으로 발걸음을 옮긴 건 아니에요 — 여전히 원래 길 위에 서 있습니다.

`git branch` 명령어는 브랜치를 **만들기만** 합니다. 전환은 하지 않아요. 이 점을 꼭 기억하세요.

```bash
# 새 브랜치 생성 (현재 브랜치에서 전환하지 않음)
git branch feature-login
```

```bash
# 브랜치 목록 확인 (* 표시가 현재 브랜치)
git branch
```

```output
  feature-login
* main
```

별표(`*`)가 `main`에 붙어 있죠? 브랜치를 만들었지만 아직 `main`에 서 있다는 뜻입니다.

```bash
# 특정 커밋에서 브랜치 만들기
git branch hotfix-bug a1b2c3d

# 원격 브랜치를 기반으로 로컬 브랜치 만들기
git branch feature-api origin/feature-api
```

### 브랜치 전환: `git switch` vs `git checkout`

> 💡 **비유**: 브랜치 전환은 **TV 채널 바꾸기**와 같습니다. 리모컨 버튼 하나로 완전히 다른 화면(작업 공간)으로 전환되지만, 이전 채널의 내용은 그대로 보존되어 있죠. Git에서 `switch`가 바로 이 리모컨 역할을 합니다.

Git에는 브랜치를 전환하는 명령어가 두 개 있습니다. 왜 그럴까요?

원래 `git checkout`이 모든 걸 담당했습니다 — 브랜치 전환도, 파일 복원도, 커밋 탐색도. 문제는 이 명령어가 **너무 많은 일**을 했다는 거예요. 같은 명령어가 "브랜치 이동"도 하고 "파일 되돌리기"도 하니, 초보자들이 혼란스러워했습니다.

그래서 Git 2.23(2019년)부터 역할이 분리됐습니다:

| 명령어 | 역할 | 도입 시기 |
|--------|------|-----------|
| `git switch` | 브랜치 전환 **전용** | Git 2.23 (2019) |
| `git restore` | 파일 복원 **전용** | Git 2.23 (2019) |
| `git checkout` | 둘 다 가능 (레거시) | Git 초기부터 |

```bash
# ✅ 권장: git switch로 브랜치 전환
git switch feature-login
```

```output
Switched to branch 'feature-login'
```

```bash
# ⚠️ 레거시: git checkout으로도 가능하지만 switch 권장
git checkout feature-login
```

```output
Switched to branch 'feature-login'
```

> 🔥 **실무 팁**: Git 2.23 이상이라면 **`git switch`를 사용하세요**. 의도가 명확하고, 실수로 파일을 덮어쓸 위험이 없습니다. `git checkout`은 브랜치 전환과 파일 복원을 같은 명령어로 처리하기 때문에, 파일명과 브랜치명이 같을 때 예상치 못한 결과가 발생할 수 있거든요.

### 만들면서 동시에 전환하기

실무에서 가장 많이 쓰는 패턴은 "새 브랜치를 만들고 바로 그 브랜치로 이동"하는 것입니다.

```bash
# ✅ 권장: switch -c (create의 c)
git switch -c feature-signup
```

```output
Switched to a new branch 'feature-signup'
```

```bash
# 레거시: checkout -b (branch의 b)
git checkout -b feature-signup
```

```output
Switched to a new branch 'feature-signup'
```

두 명령어의 플래그 비교:

| 작업 | `git switch` | `git checkout` |
|------|-------------|----------------|
| 브랜치 전환 | `git switch 브랜치명` | `git checkout 브랜치명` |
| 만들면서 전환 | `git switch -c 브랜치명` | `git checkout -b 브랜치명` |
| 특정 커밋 기반 생성 | `git switch -c 브랜치명 커밋해시` | `git checkout -b 브랜치명 커밋해시` |
| 이전 브랜치로 돌아가기 | `git switch -` | `git checkout -` |

```bash
# 이전 브랜치로 빠르게 돌아가기 (cd - 와 비슷!)
git switch -
```

```output
Switched to branch 'main'
```

```bash
# 다시 한 번 실행하면 직전 브랜치로
git switch -
```

```output
Switched to branch 'feature-signup'
```

> 💡 **알고 계셨나요?**: `git switch -`의 `-`는 셸에서 `cd -`(이전 디렉토리로 이동)와 같은 관례를 따른 것입니다. Git은 이런 작은 편의 기능이 곳곳에 숨어 있어요. 두 브랜치를 왔다 갔다 하며 작업할 때 정말 유용합니다.

### 전환할 때 무슨 일이 벌어질까?

브랜치를 전환하면 Git은 세 가지 작업을 수행합니다:

1. **HEAD 업데이트**: `.git/HEAD`가 새 브랜치를 가리키도록 변경
2. **인덱스(스테이징 영역) 업데이트**: 새 브랜치의 최신 커밋 내용으로 갱신
3. **작업 디렉토리 업데이트**: 파일들이 새 브랜치의 상태로 변경

```bash
# main 브랜치에서 작업
echo "main 기능" > feature.txt
git add . && git commit -m "main에서 기능 추가"

# feature 브랜치 만들고 전환
git switch -c feature-new

# 이 브랜치에서 다른 작업
echo "새 기능" > new-feature.txt
git add . && git commit -m "새 기능 파일 추가"

# main으로 돌아가면?
git switch main

# new-feature.txt가 사라짐! (main에는 없으니까)
ls new-feature.txt
```

```error
ls: new-feature.txt: No such file or directory
```

걱정 마세요 — 파일이 삭제된 게 아닙니다! `feature-new` 브랜치에 안전하게 있어요. `git switch feature-new`로 돌아가면 다시 나타납니다.

### 전환 시 주의할 점: 커밋하지 않은 변경 사항

> ⚠️ **흔한 오해**: "브랜치를 전환하면 작업 중인 내용이 날아간다" — 아닙니다! Git은 기본적으로 **커밋하지 않은 변경 사항이 충돌하면 전환을 거부**합니다. 변경 사항을 보호해주는 거죠.

```bash
# feature 브랜치에서 파일 수정 중 (아직 커밋 안 함)
echo "작업 중..." >> feature.txt

# 다른 브랜치로 전환 시도
git switch main
```

충돌이 없다면 수정 사항이 **그대로 따라옵니다**(carry over). 충돌이 있다면:

```error
error: Your local changes to the following files would be overwritten by checkout:
        feature.txt
Please commit your changes or stash them before you switch branches.
Aborting
```

이럴 때 선택지는 세 가지입니다:

```bash
# 방법 1: 커밋하기
git add . && git commit -m "작업 중간 저장"

# 방법 2: stash로 임시 저장 (나중에 Ch9에서 자세히 배웁니다)
git stash

# 방법 3: 변경 사항 버리기 (⚠️ 주의: 되돌릴 수 없음!)
git restore .
```

## 실습: 직접 해보기

```bash
# 1. 실습용 저장소 준비
mkdir switch-practice && cd switch-practice
git init

# 2. 첫 커밋 만들기
echo "# 브랜치 전환 실습" > README.md
git add . && git commit -m "초기 커밋"

# 3. 브랜치 목록 확인 (main만 존재)
git branch

# 4. 새 브랜치 만들기 (전환하지 않음!)
git branch feature-a

# 5. 브랜치 목록 확인 (main에 * 표시)
git branch

# 6. feature-a로 전환
git switch feature-a

# 7. 이 브랜치에서 작업
echo "Feature A 기능" > feature-a.txt
git add . && git commit -m "Feature A 추가"

# 8. 새 브랜치를 만들면서 동시에 전환
git switch -c feature-b

# 9. 이 브랜치에서 작업
echo "Feature B 기능" > feature-b.txt
git add . && git commit -m "Feature B 추가"

# 10. main으로 돌아가기
git switch main

# 11. 각 브랜치의 파일 확인
ls                    # README.md만 존재
git switch feature-a
ls                    # README.md + feature-a.txt
git switch feature-b
ls                    # README.md + feature-a.txt + feature-b.txt

# 12. 커밋 그래프로 전체 그림 확인
git log --oneline --graph --all
```

## 더 깊이 알아보기

### `git switch`는 왜 만들어졌을까?

`git checkout`의 역사는 Git 초창기로 거슬러 올라갑니다. Linus Torvalds가 2005년에 Git을 만들 때, 명령어 설계보다는 **내부 성능과 데이터 무결성**에 집중했습니다. 그래서 `checkout`이라는 하나의 명령어에 여러 기능이 뒤섞이게 된 거죠.

10년 넘게 "checkout이 너무 많은 일을 한다"는 커뮤니티의 피드백이 쌓이다가, 2019년 Git 2.23에서 드디어 `switch`와 `restore`로 분리됐습니다. Git 개발자 Junio C Hamano가 이끈 이 변화는 "하나의 명령어는 하나의 일만"이라는 유닉스 철학을 뒤늦게 따른 것이기도 합니다.

참고로 `git switch`는 아직 공식적으로 "experimental" 태그가 붙어 있지만, 실제로는 완전히 안정적이며 모든 주요 Git 배포판에서 지원합니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`git checkout`은 더 이상 쓰면 안 된다" — `checkout`은 여전히 유효한 명령어이고, 수많은 튜토리얼과 문서에서 사용합니다. 다만 **브랜치 전환**에는 `switch`가 더 명확하다는 것이죠. 기존 스크립트나 습관에서 `checkout`을 쓰고 있다면, 급하게 바꿀 필요는 없습니다.

> 🔥 **실무 팁**: 브랜치명과 파일명이 같으면 `git checkout`은 혼란에 빠집니다. 예를 들어 `main.py`라는 파일과 `main.py`라는 브랜치가 있을 때, `git checkout main.py`가 파일 복원인지 브랜치 전환인지 모호해지죠. `git switch`는 브랜치 전용이라 이런 문제가 원천적으로 없습니다.

> 💡 **알고 계셨나요?**: `git switch -c`의 `-c`는 "create"의 약자이고, `git checkout -b`의 `-b`는 "branch"의 약자입니다. 두 명령어 모두 같은 결과를 내지만, `-c`(create)가 의미적으로 더 직관적이라 기억하기 쉽습니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| `git branch 이름` | 브랜치 생성 (전환 안 함) |
| `git switch 이름` | 브랜치 전환 (권장) |
| `git switch -c 이름` | 생성 + 전환 동시에 (권장) |
| `git checkout 이름` | 브랜치 전환 (레거시) |
| `git checkout -b 이름` | 생성 + 전환 (레거시) |
| `git switch -` | 이전 브랜치로 돌아가기 |
| 미커밋 변경 사항 | 충돌 없으면 carry over, 충돌 시 전환 거부 |

## 다음 섹션 미리보기

브랜치를 만들고 전환할 수 있게 됐으니, 이제 **갈라진 브랜치를 다시 하나로 합치는 방법**을 배울 차례입니다. 다음 섹션 [브랜치 병합](./03-merge.md)에서는 fast-forward 머지와 3-way 머지의 차이, 그리고 `git merge` 명령어를 자세히 다룹니다.

## 참고 자료

- [Pro Git Book — 브랜치와 Merge의 기초](https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B8%8C%EB%9E%9C%EC%B9%98%EC%99%80-Merge-%EC%9D%98-%EA%B8%B0%EC%B4%88) - 브랜치 생성과 전환의 공식 가이드
- [Git 공식 문서 — git-switch](https://git-scm.com/docs/git-switch) - switch 명령어 레퍼런스
- [Git 공식 문서 — git-branch](https://git-scm.com/docs/git-branch) - branch 명령어 레퍼런스
- [Git Switch vs. Checkout: What's the Difference?](https://kodekloud.com/blog/git-switch-vs-checkout/) - switch와 checkout의 차이를 상세히 비교
