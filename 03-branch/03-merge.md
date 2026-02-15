# 브랜치 병합

> merge, fast-forward, 3-way merge의 차이와 원리

## 개요

브랜치를 만들어서 작업을 마쳤다면, 이제 그 결과물을 원래 브랜치에 합쳐야 합니다. 이 "합치기" 과정이 바로 **머지(merge)**입니다. Git은 상황에 따라 서로 다른 방식으로 병합하는데요, 이 차이를 이해하면 커밋 히스토리를 깔끔하게 관리할 수 있습니다.

**선수 지식**: [브랜치 생성과 전환](./02-create-switch.md)에서 배운 브랜치 만들기와 전환
**학습 목표**:
- fast-forward 머지와 3-way 머지의 차이를 설명할 수 있다
- `git merge` 명령어로 브랜치를 병합할 수 있다
- `--no-ff` 옵션의 용도를 이해한다

## 왜 알아야 할까?

브랜치를 만드는 건 시작일 뿐이고, **합치는 것이 진짜 완성**입니다. 혼자 개발하든 팀으로 개발하든, "이 기능 다 만들었으니 main에 합칠게요"라는 순간은 반드시 옵니다. 머지의 원리를 모르면 예상치 못한 머지 커밋이 생기거나, 히스토리가 의도와 다르게 꼬일 수 있습니다. 원리를 알면 상황에 맞는 전략을 선택할 수 있죠.

## 핵심 개념

### 머지의 기본 사용법

> 💡 **비유**: 머지는 **두 갈래 길이 다시 하나로 합쳐지는 합류 지점**입니다. 한 길에서는 다리를 건설하고, 다른 길에서는 도로를 포장했다면 — 합류 지점에서 두 작업이 하나의 완성된 도로가 되는 거죠.

머지의 기본 흐름은 간단합니다:

1. **병합받을 브랜치**로 먼저 이동
2. `git merge`로 **가져올 브랜치**를 지정

```bash
# 1. main 브랜치로 이동 (병합받을 곳)
git switch main

# 2. feature 브랜치를 main에 병합 (가져올 곳)
git merge feature-login
```

"main에 서서, feature-login을 가져온다" — 이 방향을 꼭 기억하세요. 현재 있는 브랜치가 병합의 **대상(target)**이 됩니다.

### Fast-Forward 머지 — 직진!

> 💡 **비유**: fast-forward 머지는 **에스컬레이터를 타는 것**과 같습니다. main이 멈춰 있는 동안 feature가 앞서 나갔다면, main은 그냥 에스컬레이터를 타고 feature 위치까지 "쭉 올라가면" 됩니다. 별도의 합류 지점이 필요 없죠.

fast-forward 머지는 가장 단순한 형태입니다. **main에 새 커밋이 없고**, feature 브랜치만 앞으로 진행했을 때 발생합니다.

이 상황을 정리하면:

- **main**: A → B (멈춰 있음)
- **feature**: A → B → C → D (앞으로 진행)

main 입장에서는 feature까지 "빨리 감기"만 하면 됩니다. 머지 커밋이 따로 생기지 않아요.

```bash
# 실습: fast-forward 머지
mkdir ff-practice && cd ff-practice
git init

# main에서 첫 커밋
echo "초기 파일" > file.txt
git add . && git commit -m "A: 초기 커밋"

# feature 브랜치 만들고 전환
git switch -c feature

# feature에서만 커밋 추가 (main은 그대로)
echo "기능 1" >> file.txt
git add . && git commit -m "B: 기능 1 추가"

echo "기능 2" >> file.txt
git add . && git commit -m "C: 기능 2 추가"

# main으로 돌아가서 머지
git switch main
git merge feature
```

```output
Updating a1b2c3d..e4f5g6h
Fast-forward
 file.txt | 2 ++
 1 file changed, 2 insertions(+)
```

출력에 `Fast-forward`라고 나오죠? 머지 커밋 없이 main 포인터가 feature 위치로 이동한 겁니다.

```bash
# 히스토리 확인 — 일직선!
git log --oneline --graph
```

```output
* e4f5g6h (HEAD -> main, feature) C: 기능 2 추가
* d3c4b5a B: 기능 1 추가
* a1b2c3d A: 초기 커밋
```

### 3-Way 머지 — 갈라진 길의 합류

> 💡 **비유**: 3-way 머지는 **Y자 합류 지점**과 같습니다. 두 갈래 길이 각자 다른 방향으로 진행한 후, 다시 만나는 지점에 "합류 표지판(머지 커밋)"을 세워야 합니다.

main과 feature **양쪽 모두** 새 커밋이 있으면, Git은 fast-forward를 할 수 없습니다. 대신 **3-way 머지**를 수행하고, 그 결과로 **머지 커밋**을 만듭니다.

"3-way"라는 이름은 Git이 참조하는 세 지점에서 유래합니다:

| 지점 | 역할 |
|------|------|
| **공통 조상(common ancestor)** | 두 브랜치가 갈라지기 전의 마지막 공통 커밋 |
| **현재 브랜치 끝(ours)** | main의 최신 커밋 |
| **병합할 브랜치 끝(theirs)** | feature의 최신 커밋 |

Git은 이 세 지점을 비교해서, 각 파일의 변경 사항을 자동으로 합칩니다.

```bash
# 실습: 3-way 머지
mkdir 3way-practice && cd 3way-practice
git init

# 공통 조상 커밋
echo "공통 내용" > shared.txt
git add . && git commit -m "A: 초기 커밋"

# feature 브랜치에서 작업
git switch -c feature
echo "기능 코드" > feature.txt
git add . && git commit -m "B: 기능 추가"

# main으로 돌아와서 다른 작업
git switch main
echo "버그 수정" > bugfix.txt
git add . && git commit -m "C: 버그 수정"

# 이제 main과 feature 모두 새 커밋이 있음 → 3-way 머지
git merge feature
```

머지 커밋 메시지를 편집하는 에디터가 열립니다. 기본 메시지를 그대로 저장해도 됩니다.

```output
Merge made by the 'ort' strategy.
 feature.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 feature.txt
```

```bash
# 히스토리 확인 — Y자 합류 모양!
git log --oneline --graph --all
```

```output
*   f7g8h9i (HEAD -> main) Merge branch 'feature'
|\
| * b2c3d4e (feature) B: 기능 추가
* | c3d4e5f C: 버그 수정
|/
* a1b2c3d A: 초기 커밋
```

> 💡 **알고 계셨나요?**: Git의 기본 머지 전략 이름이 'ort'인데요, 이전에는 'recursive'였습니다. Git 2.33(2021년)부터 기본값이 변경됐습니다. 'ort'는 "Ostensibly Recursive's Twin(표면적으로는 recursive의 쌍둥이)"의 약자로, 같은 결과를 내면서도 성능이 크게 개선됐습니다. 이름이 재미있죠?

### `--no-ff` — 항상 머지 커밋 남기기

fast-forward가 가능한 상황에서도, **일부러** 머지 커밋을 남기고 싶을 때가 있습니다. "이 기능은 별도 브랜치에서 개발했다"는 기록을 명시적으로 남기기 위해서죠.

```bash
# fast-forward 가능하지만 머지 커밋을 강제로 생성
git merge --no-ff feature-login
```

```output
Merge made by the 'ort' strategy.
 login.txt | 1 +
 1 file changed, 1 insertion(+)
```

```bash
# 히스토리에 머지 커밋이 남음
git log --oneline --graph
```

```output
*   d4e5f6g (HEAD -> main) Merge branch 'feature-login'
|\
| * c3d4e5f (feature-login) 로그인 기능 구현
|/
* a1b2c3d 초기 커밋
```

| 옵션 | 동작 | 언제 사용? |
|------|------|-----------|
| (기본값) | 가능하면 fast-forward, 아니면 3-way | 일반적인 로컬 작업 |
| `--no-ff` | 항상 머지 커밋 생성 | 기능 브랜치 히스토리를 명시적으로 보존 |
| `--ff-only` | fast-forward만 허용, 아니면 실패 | CI/CD에서 깨끗한 히스토리 강제 |

> 🔥 **실무 팁**: 많은 팀에서 `--no-ff`를 기본 전략으로 채택합니다. GitHub의 "Merge pull request" 버튼이 바로 `--no-ff`로 동작하거든요. 이렇게 하면 "어떤 기능이 언제 합쳐졌는지"를 히스토리에서 한눈에 파악할 수 있습니다. 반대로 혼자 작업하는 개인 프로젝트에서는 fast-forward가 더 깔끔할 수 있어요.

### 머지 취소하기

머지 도중에 "이건 아닌데?"라는 생각이 들 수 있습니다. 아직 머지가 완료되지 않았다면:

```bash
# 머지 진행 중 취소 (충돌 해결 중일 때)
git merge --abort
```

이미 머지 커밋이 만들어진 후라면:

```bash
# ⚠️ 주의: 머지 커밋을 되돌리기
git revert -m 1 HEAD
```

## 실습: 직접 해보기

```bash
# 1. 실습 저장소 준비
mkdir merge-practice && cd merge-practice
git init

# 2. 초기 커밋
echo "# Merge 실습 프로젝트" > README.md
git add . && git commit -m "초기 커밋"

# === Fast-Forward 머지 체험 ===

# 3. feature-a 브랜치 만들고 작업
git switch -c feature-a
echo "로그인 기능" > login.txt
git add . && git commit -m "로그인 기능 추가"

# 4. main으로 돌아와서 fast-forward 머지
git switch main
git merge feature-a
git log --oneline --graph

# === 3-Way 머지 체험 ===

# 5. feature-b 브랜치 만들고 작업
git switch -c feature-b
echo "회원가입 기능" > signup.txt
git add . && git commit -m "회원가입 기능 추가"

# 6. main에서도 별도 작업 추가
git switch main
echo "홈페이지 수정" >> README.md
git add . && git commit -m "README 업데이트"

# 7. 3-way 머지 (양쪽 모두 새 커밋이 있으므로)
git merge feature-b
git log --oneline --graph --all

# === --no-ff 머지 체험 ===

# 8. feature-c 브랜치 만들고 작업
git switch -c feature-c
echo "프로필 기능" > profile.txt
git add . && git commit -m "프로필 기능 추가"

# 9. main에서 --no-ff 머지 (fast-forward 가능하지만 머지 커밋 강제)
git switch main
git merge --no-ff feature-c
git log --oneline --graph --all
```

## 더 깊이 알아보기

### 머지 알고리즘의 진화

Git의 머지 알고리즘은 꾸준히 발전해왔습니다. 초기에는 단순한 3-way 머지만 지원했지만, 리눅스 커널처럼 복잡한 프로젝트에서 **여러 공통 조상**이 존재하는 경우가 많아지면서, "recursive" 전략이 기본값이 됐습니다.

2021년 Git 2.33에서는 'ort' 전략이 등장했습니다. Elijah Newren이 수년에 걸쳐 개발한 이 전략은 recursive와 같은 결과를 내면서도, 대규모 저장소에서 머지 속도를 크게 개선했습니다. 이름의 유래인 "Ostensibly Recursive's Twin"은 "겉보기에는 recursive의 쌍둥이"라는 뜻으로, 호환성을 유지하면서도 내부 구현을 완전히 새로 작성했다는 의미를 담고 있습니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "머지하면 커밋이 항상 생긴다" — fast-forward 머지에서는 머지 커밋이 생기지 않습니다. 머지 커밋이 생기는 건 양쪽 브랜치가 모두 진행했을 때(3-way)이거나, `--no-ff` 옵션을 사용했을 때입니다.

> ⚠️ **흔한 오해**: "머지는 위험하다" — 머지 자체는 안전한 작업입니다. Git은 자동으로 변경 사항을 합치고, 충돌이 있을 때만 사용자에게 결정을 요청합니다. `git merge --abort`로 언제든 취소할 수 있어요. 두려워하지 마세요!

> 🔥 **실무 팁**: 머지하기 전에 `git log --oneline main..feature`로 "feature에만 있는 커밋"을 미리 확인하는 습관을 들이세요. 어떤 변경 사항이 합쳐질지 예측할 수 있습니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| fast-forward 머지 | main에 새 커밋이 없을 때, 포인터만 앞으로 이동. 머지 커밋 없음 |
| 3-way 머지 | 양쪽 모두 새 커밋이 있을 때, 공통 조상을 기준으로 병합. 머지 커밋 생성 |
| `--no-ff` | fast-forward 가능해도 머지 커밋을 강제 생성 |
| `--ff-only` | fast-forward만 허용, 아니면 실패 |
| `git merge --abort` | 진행 중인 머지 취소 |
| ort 전략 | Git 2.33+의 기본 머지 전략 (recursive의 후속) |

## 다음 섹션 미리보기

머지가 항상 순조로운 건 아닙니다. **같은 파일의 같은 부분**을 두 브랜치에서 동시에 수정했다면? Git도 자동으로 합칠 수 없는 상황이 발생합니다. 다음 섹션 [충돌 해결](./04-conflict.md)에서 충돌이 왜 생기고, 어떻게 읽고 해결하는지 배웁니다.

## 참고 자료

- [Pro Git Book — 브랜치와 Merge의 기초](https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B8%8C%EB%9E%9C%EC%B9%98%EC%99%80-Merge-%EC%9D%98-%EA%B8%B0%EC%B4%88) - 머지의 공식 가이드
- [Git 공식 문서 — git-merge](https://git-scm.com/docs/git-merge) - merge 명령어 레퍼런스
- [Atlassian Git Tutorial — Git Merge](https://www.atlassian.com/git/tutorials/using-branches/git-merge) - 그림과 함께 설명하는 머지 가이드
- [GitHub Docs — About merge methods](https://docs.github.com/articles/about-merge-methods-on-github) - GitHub의 머지 옵션 설명
