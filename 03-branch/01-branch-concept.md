# 브랜치란?

> 브랜치의 개념, HEAD 포인터, 커밋 그래프로 이해하기

## 개요

지금까지 우리는 하나의 일직선 위에서 커밋을 쌓아왔습니다. 하지만 실제 개발에서는 "새 기능을 만들면서 동시에 버그도 고치고 싶다"는 상황이 매일 벌어지죠. 브랜치(branch)는 이런 **평행 작업**을 가능하게 해주는 Git의 핵심 기능입니다.

**선수 지식**: [태그](../02-file-history/05-tags.md)에서 배운 "커밋을 가리키는 포인터" 개념, [커밋의 기본](../01-git-start/04-commit-basics.md)의 커밋 해시
**학습 목표**:
- 브랜치가 내부적으로 어떻게 작동하는지 이해한다
- HEAD 포인터의 역할을 설명할 수 있다
- 커밋 그래프(DAG)를 읽고 브랜치 관계를 파악할 수 있다

## 왜 알아야 할까?

혼자 개발할 때도 "실험적인 코드를 시도해보고 싶은데, 지금 잘 되고 있는 코드를 망치고 싶지 않다"는 상황이 생깁니다. 팀으로 일하면 더 심해지죠 — 다섯 명이 동시에 같은 코드베이스에서 서로 다른 기능을 만들어야 합니다. 브랜치 없이는 불가능한 일입니다. Git의 브랜치는 **거의 비용이 들지 않으면서도 완전히 독립된 작업 공간**을 제공하기 때문에, 현대 소프트웨어 개발에서 빠질 수 없는 핵심 도구입니다.

## 핵심 개념

### 브랜치는 포인터다

> 💡 **비유**: 브랜치는 **포스트잇 화살표**와 같습니다. 책의 특정 페이지에 붙여둔 화살표인데요, 새 페이지를 넘길 때마다(새 커밋을 할 때마다) 화살표가 **자동으로 따라 이동**합니다. 반면 앞서 배운 [태그](../02-file-history/05-tags.md)는 특정 페이지에 **고정된** 이름표였죠.

많은 초보자가 "브랜치를 만들면 파일이 전부 복사되는 거 아닌가?"라고 생각합니다. 하지만 사실 Git의 브랜치는 놀라울 정도로 가볍습니다. 브랜치의 정체는 **특정 커밋을 가리키는 40자(SHA-1 해시) 포인터**가 담긴 작은 파일에 불과합니다.

```bash
# 브랜치가 실제로 어떻게 저장되는지 확인
cat .git/refs/heads/main
```

```output
a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0
```

이게 전부입니다! 브랜치를 새로 만든다는 건, 이 작은 파일 하나를 추가하는 것이죠. SVN에서 브랜치를 만들면 전체 디렉토리를 복사해야 했던 것과는 차원이 다릅니다.

| 특성 | 브랜치 | 태그 |
|------|--------|------|
| 저장 위치 | `.git/refs/heads/` | `.git/refs/tags/` |
| 커밋 시 이동 | 새 커밋으로 **자동 이동** | **고정** (절대 이동하지 않음) |
| 용도 | 진행 중인 작업 흐름 | 릴리스 등 중요 시점 기록 |

> ⚠️ **흔한 오해**: "브랜치를 만들면 파일이 복사된다" — 아닙니다! Git 브랜치는 커밋을 가리키는 **40바이트짜리 포인터 파일**일 뿐입니다. 파일을 복사하지 않기 때문에 1,000개의 브랜치를 만들어도 저장소 크기가 거의 변하지 않아요. SVN에서 브랜치 만들기를 두려워했다면, Git에서는 그 걱정을 완전히 내려놓아도 됩니다.

### HEAD — 지금 내가 서 있는 곳

> 💡 **비유**: HEAD는 **"현재 위치"를 가리키는 GPS 핀**입니다. 지도(커밋 그래프) 위에서 "당신은 여기에 있습니다"라는 파란 점이죠. 새 커밋을 만들면, 이 핀도 함께 앞으로 이동합니다.

HEAD는 "지금 어떤 브랜치에서 작업 중인가"를 알려주는 특별한 포인터입니다. `.git/HEAD` 파일에 저장되어 있죠.

```bash
# HEAD가 어디를 가리키는지 확인
cat .git/HEAD
```

```output
ref: refs/heads/main
```

HEAD는 보통 브랜치를 **간접적으로** 가리킵니다. 이 연결 고리를 따라가면:

1. `.git/HEAD` → `ref: refs/heads/main` (main 브랜치를 가리킴)
2. `.git/refs/heads/main` → `a1b2c3d` (실제 커밋 해시)

이렇게 HEAD → 브랜치 → 커밋의 **체인 구조**로 이루어져 있습니다. 새 커밋을 만들면, 현재 브랜치 포인터가 새 커밋으로 이동하고, HEAD는 여전히 그 브랜치를 가리키고 있으니 자연스럽게 새 커밋 위치로 따라오는 거죠.

```bash
# HEAD가 가리키는 브랜치 확인 (프로그래밍 방식)
git symbolic-ref HEAD
```

```output
refs/heads/main
```

#### Detached HEAD — GPS 핀이 길에서 벗어났을 때

특정 커밋을 직접 체크아웃하면, HEAD가 브랜치가 아닌 **커밋을 직접 가리키는** 상태가 됩니다. 이걸 **detached HEAD** 상태라고 합니다.

```bash
# 특정 커밋으로 직접 이동 (detached HEAD 발생)
git checkout a1b2c3d
```

```output
Note: switching to 'a1b2c3d'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.
```

이 상태에서 커밋을 만들면, 어떤 브랜치에도 속하지 않는 "고아 커밋"이 됩니다. 나중에 가비지 컬렉션에 의해 삭제될 수 있으니, detached HEAD에서 중요한 작업을 했다면 반드시 브랜치를 만들어두세요.

```bash
# detached HEAD에서 빠져나오기
git switch main

# 또는 현재 위치에서 새 브랜치 만들기
git switch -c new-branch-name
```

### 커밋 그래프 — 브랜치의 큰 그림

> 💡 **비유**: 커밋 그래프는 **지하철 노선도**와 같습니다. 각 역(커밋)은 이전 역(부모 커밋)과 연결되어 있고, 노선(브랜치)이 갈라졌다가 다시 합쳐질 수 있죠.

Git의 커밋들은 **방향이 있는 비순환 그래프(DAG, Directed Acyclic Graph)**를 형성합니다. 각 커밋은 자신의 **부모 커밋**을 가리키고, 이 연결이 모여서 프로젝트의 전체 이력이 됩니다.

```bash
# 커밋 그래프 시각화
git log --oneline --graph --all
```

```output
* d4e5f6g (HEAD -> feature) 소셜 로그인 추가
* c3d4e5f 로그인 폼 구현
| * b2c3d4e (main) 버그 수정
|/
* a1b2c3d 초기 커밋
```

이 그래프를 읽는 방법:

| 기호 | 의미 |
|------|------|
| `*` | 커밋 하나 |
| `\|` | 브랜치 흐름 (세로선) |
| `/` 또는 `\` | 브랜치가 갈라지거나 합쳐지는 지점 |
| `(HEAD -> feature)` | HEAD가 feature 브랜치를 가리키고 있음 |

## 실습: 직접 해보기

```bash
# 1. 실습용 저장소 준비
mkdir branch-practice && cd branch-practice
git init

# 2. 첫 커밋 만들기
echo "# 브랜치 실습" > README.md
git add . && git commit -m "초기 커밋"

# 3. 내부 구조 들여다보기
cat .git/HEAD                    # HEAD가 main을 가리킴
cat .git/refs/heads/main         # main이 커밋 해시를 가리킴

# 4. 현재 브랜치 이름과 커밋 확인
git branch                       # * main
git log --oneline                # 커밋 해시 확인

# 5. 새 브랜치 만들기 (아직 전환하지 않음)
git branch feature

# 6. 두 브랜치가 같은 커밋을 가리키는지 확인
cat .git/refs/heads/main
cat .git/refs/heads/feature      # 같은 해시!

# 7. 커밋 그래프 확인
git log --oneline --graph --all --decorate
```

## 더 깊이 알아보기

### Git은 왜 이렇게 가벼운 브랜치를 만들 수 있을까?

이건 Git의 **탄생 설계**와 관련이 있습니다. 2005년 Linus Torvalds가 Git을 만들 때 세운 핵심 설계 목표 중 하나가 바로 **"수천 개의 병렬 브랜치를 효율적으로 지원할 것"**이었습니다. 리눅스 커널 개발에서는 수백 명의 개발자가 동시에 각자의 브랜치에서 작업하기 때문이죠.

이전 세대의 VCS인 SVN에서 브랜치를 만들면 전체 소스 코드 디렉토리가 복사됐습니다. 대규모 프로젝트에서는 이 작업에 수 분이 걸리기도 했죠. 그래서 SVN에서는 브랜치를 "큰 기능을 개발할 때만" 사용하는 게 관례였습니다. 반면 Git은 브랜치가 40바이트짜리 파일 하나라서, 아무리 작은 기능이라도 브랜치를 만드는 게 자연스럽습니다.

> 💡 **알고 계셨나요?**: Git의 설계 목표 5가지는 "속도, 단순한 설계, 비선형 개발의 강력한 지원(수천 개의 병렬 브랜치), 완전한 분산, 대규모 프로젝트의 효율적 처리"입니다. 이 목표들이 모두 브랜치를 저렴하게 만드는 방향으로 수렴하죠. Linus가 Git을 만든 지 20년이 지났지만, 이 설계 철학은 여전히 빛을 발합니다.

### packed-refs — 브랜치가 수천 개라면?

브랜치마다 파일이 하나씩 생기니, 저장소에 수백 수천 개의 브랜치가 있으면 파일 시스템에 부담이 될 수 있습니다. Git은 이를 위해 `git pack-refs` 명령으로 여러 ref 파일을 `.git/packed-refs` 하나로 합칠 수 있습니다. `git gc`가 자동으로 처리해주기도 하죠. 더 자세한 내부 구조는 [Git 내부 구조](../09-history-internals/05-git-internals.md)에서 다룹니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "detached HEAD 상태는 위험하다" — 위험하지는 않지만, **주의가 필요한** 상태입니다. 이 상태에서 만든 커밋은 브랜치에 속하지 않아서, 다른 브랜치로 전환하면 접근하기 어려워집니다. 만약 detached HEAD에서 작업했다면, `git switch -c 새이름`으로 브랜치를 만들어두세요.

> 🔥 **실무 팁**: `git log --oneline --graph --all`은 너무 자주 쓰이기 때문에, alias로 등록해두면 편합니다. `git config --global alias.lg "log --oneline --graph --all --decorate"` 한 번 설정하면, 이후 `git lg`만 입력하면 됩니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| 브랜치 | 특정 커밋을 가리키는 이동하는 포인터 (`.git/refs/heads/`) |
| HEAD | 현재 작업 중인 브랜치를 가리키는 특별한 포인터 (`.git/HEAD`) |
| detached HEAD | HEAD가 브랜치가 아닌 커밋을 직접 가리키는 상태 |
| 커밋 그래프(DAG) | 커밋의 부모-자식 관계가 만드는 방향성 비순환 그래프 |
| 브랜치 vs 태그 | 브랜치는 커밋과 함께 이동, 태그는 고정 |

## 다음 섹션 미리보기

브랜치가 무엇인지 이해했으니, 이제 직접 만들고 전환해볼 차례입니다. 다음 섹션 [브랜치 생성과 전환](./02-create-switch.md)에서는 `git branch`, `git switch`, `git checkout`을 사용하여 브랜치를 생성하고 오가는 방법을 배웁니다.

## 참고 자료

- [Pro Git Book — 브랜치란 무엇인가](https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B8%8C%EB%9E%9C%EC%B9%98%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80) - 브랜치의 공식 가이드
- [Git Internals — Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References) - refs, HEAD의 내부 구조
- [A Look Under the Hood: How Branches Work in Git](https://stackoverflow.blog/2021/04/05/a-look-under-the-hood-how-branches-work-in-git/) - Stack Overflow 블로그의 브랜치 심화 설명
- [Git 공식 문서 — git-branch](https://git-scm.com/docs/git-branch) - branch 명령어 레퍼런스
