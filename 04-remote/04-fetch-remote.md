# fetch와 remote 관리

> fetch vs pull, 다중 remote, prune, 원격 브랜치 추적

## 개요

앞서 `git pull`이 `fetch + merge`라는 것을 배웠는데요. 그러면 **fetch만 따로 하면 뭐가 좋을까요?** 이번 섹션에서는 `fetch`의 진짜 가치, 여러 원격 저장소를 동시에 관리하는 방법, 그리고 원격 브랜치를 깔끔하게 추적하고 정리하는 전략을 알아봅니다.

**선수 지식**: [push와 pull](./03-push-pull.md)에서 배운 기본 동기화 개념
**학습 목표**:
- `fetch`와 `pull`의 차이를 명확히 이해하고 상황에 맞게 선택할 수 있다
- 여러 개의 remote를 등록하고 관리할 수 있다
- 원격에서 삭제된 브랜치를 prune으로 정리할 수 있다
- 원격 브랜치 추적 상태를 파악하고 관리할 수 있다

## 왜 알아야 할까?

실무에서 "일단 pull 해"라고 하는 사람이 많지만, 숙련된 개발자는 **fetch 먼저, 확인 후 merge**하는 습관을 가지고 있습니다. 중요한 릴리스 직전이나 복잡한 머지 상황에서 pull의 자동 merge가 문제를 일으킬 수 있거든요. 또한 오픈소스 기여나 사내 미러링 같은 상황에서는 여러 remote를 다루는 기술이 필수입니다.

## 핵심 개념

### 개념 1: fetch — "먼저 보고, 나중에 합치기"

> 💡 **비유**: `git fetch`는 **우편함 확인**과 같습니다. "편지가 왔는지 확인만" 하는 거예요. 우편함(원격)에서 편지(변경사항)를 꺼내 책상 위에 올려두지만, **아직 읽지는 않은** 상태입니다. 반면 `git pull`은 편지를 꺼내서 바로 읽고(merge) 처리까지 해버리는 것이죠.

```bash
# 원격의 변경사항을 다운로드 (작업 디렉토리에 영향 없음!)
git fetch origin
```

```output
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
From https://github.com/username/project
   a1b2c3d..e4f5g6h  main       -> origin/main
 * [new branch]      feature-x  -> origin/feature-x
```

fetch 후에는 **원격 추적 브랜치**(`origin/main`, `origin/feature-x`)만 업데이트됩니다. 내 로컬 브랜치와 작업 디렉토리는 그대로예요.

```bash
# fetch 후 변경사항 확인
git log HEAD..origin/main --oneline
```

```output
e4f5g6h Update API endpoint
d3c2b1a Fix typo in README
```

```bash
# 변경 내용 자세히 보기
git diff main origin/main

# 확인 후 병합 결정
git merge origin/main
```

### 개념 2: fetch vs pull — 언제 무엇을 쓸까?

| 상황 | 추천 | 이유 |
|------|------|------|
| 평소 작업 시작할 때 | `git pull` | 빠르게 최신화하면 됨 |
| 릴리스/배포 직전 | `git fetch` + 확인 + `merge` | 변경사항을 먼저 검토해야 안전 |
| 복잡한 충돌이 예상될 때 | `git fetch` + `diff` + `merge` | 어떤 충돌이 올지 미리 파악 |
| 원격에 뭐가 바뀌었는지 확인만 | `git fetch` + `log` | 병합 없이 확인만 |
| CI/CD 파이프라인에서 | `git fetch` | 자동 merge는 위험할 수 있음 |

```bash
# 실전: fetch → 확인 → merge 패턴
git fetch origin
git log --oneline HEAD..origin/main  # 새 커밋 확인
git diff --stat HEAD origin/main     # 변경된 파일 확인
git merge origin/main                # 확인 후 병합
```

> ⚠️ **흔한 오해**: "`fetch`는 `pull`의 약한 버전이다" — 아닙니다! fetch는 **더 신중한 버전**이에요. pull이 "가져오기 + 합치기"를 한 번에 하는 반면, fetch는 "가져오기"만 하고 합치기는 개발자의 판단에 맡깁니다. 경험이 쌓일수록 fetch를 더 자주 쓰게 됩니다.

### 개념 3: 다중 remote 관리

하나의 로컬 저장소에 **여러 개의 원격 저장소**를 연결할 수 있습니다. 실무에서 흔한 시나리오를 볼게요:

#### 시나리오 1: 오픈소스 기여 (origin + upstream)

```bash
git remote -v
```

```output
origin    https://github.com/my-account/project.git (fetch)
origin    https://github.com/my-account/project.git (push)
upstream  https://github.com/original/project.git (fetch)
upstream  https://github.com/original/project.git (push)
```

#### 시나리오 2: 미러링 (회사 서버 + GitHub)

```bash
# 회사 내부 GitLab
git remote add company https://gitlab.company.com/team/project.git

# GitHub (오픈소스 미러)
git remote add github https://github.com/company/project.git

git remote -v
```

```output
company  https://gitlab.company.com/team/project.git (fetch)
company  https://gitlab.company.com/team/project.git (push)
github   https://github.com/company/project.git (fetch)
github   https://github.com/company/project.git (push)
origin   https://github.com/username/project.git (fetch)
origin   https://github.com/username/project.git (push)
```

```bash
# 특정 remote에서만 fetch
git fetch company
git fetch github

# 모든 remote에서 한 번에 fetch
git fetch --all
```

#### 시나리오 3: 동료의 브랜치 직접 확인

코드 리뷰를 위해 동료의 저장소를 직접 remote로 추가할 수도 있습니다:

```bash
# 동료의 fork를 remote로 추가
git remote add colleague https://github.com/colleague/project.git
git fetch colleague

# 동료의 브랜치 확인
git log colleague/feature-auth --oneline

# 동료의 브랜치를 로컬에서 테스트
git switch -c test-colleague-feature colleague/feature-auth
```

### 개념 4: prune — 원격에서 삭제된 브랜치 정리

팀원이 원격에서 브랜치를 삭제해도, 내 로컬의 **원격 추적 브랜치**(`origin/feature-old`)는 남아 있습니다. 시간이 지나면 이런 "유령 브랜치"가 쌓이게 되죠.

```bash
# 원격에서 이미 삭제된 브랜치의 로컬 참조 확인
git remote prune origin --dry-run
```

```output
Pruning origin
URL: https://github.com/username/project.git
 * [would prune] origin/feature-completed
 * [would prune] origin/hotfix-old
```

```bash
# 실제 정리 실행
git remote prune origin

# 또는 fetch와 동시에 prune
git fetch --prune
```

```bash
# 자동 prune 설정 (fetch할 때마다 자동 실행)
git config --global fetch.prune true
```

> 🔥 **실무 팁**: `fetch.prune true`는 한 번 설정하면 영구 적용됩니다. 원격에서 삭제된 브랜치가 로컬에 쌓이는 것을 자동으로 방지해주니, **설정하지 않을 이유가 없습니다**.

### 개념 5: 원격 브랜치 추적 상태 파악

```bash
# 모든 브랜치의 tracking 상태 (ahead/behind)
git branch -vv
```

```output
* main           a1b2c3d [origin/main] Latest commit
  feature-done   e4f5g6h [origin/feature-done: gone] Completed feature
  feature-wip    i7j8k9l [origin/feature-wip: ahead 3, behind 1] Work in progress
  local-only     m0n1o2p No tracking
```

각 상태의 의미:

| 표시 | 의미 |
|------|------|
| `[origin/main]` | 동기화됨 |
| `[origin/branch: ahead 3]` | 로컬이 3개 커밋 앞서 있음 → push 필요 |
| `[origin/branch: behind 1]` | 원격이 1개 커밋 앞서 있음 → pull 필요 |
| `[origin/branch: ahead 3, behind 1]` | 서로 다른 커밋 있음 → pull 후 push |
| `[origin/branch: gone]` | 원격에서 삭제됨 → prune 또는 로컬 삭제 고려 |
| tracking 없음 | 원격과 연결 안 됨 |

```bash
# fetch 후 ahead/behind 상태를 한눈에 보기
git fetch --all
git for-each-ref --format='%(refname:short) %(upstream:short) %(upstream:track)' refs/heads
```

```output
main origin/main [up to date]
feature-wip origin/feature-wip [ahead 3, behind 1]
feature-done origin/feature-done [gone]
local-only
```

## 실습: 직접 해보기

```bash
# 1. 실습 환경 구성
mkdir -p /tmp/fetch-practice
cd /tmp/fetch-practice

# 원격 저장소
mkdir remote.git
cd remote.git && git init --bare && cd ..

# 로컬 저장소
git clone remote.git workspace
cd workspace
echo "# Fetch 실습" > README.md
git add . && git commit -m "초기 커밋"
git push -u origin main

# 2. 원격에 변경 만들기 (다른 개발자 시뮬레이션)
cd ..
git clone remote.git other-dev
cd other-dev
echo "새 기능" > feature.txt
git add . && git commit -m "새 기능 추가"
git push origin main
cd ..

# 3. fetch로 변경사항 확인 (merge 없이!)
cd workspace
git fetch origin

# 4. 무엇이 바뀌었는지 확인
git log HEAD..origin/main --oneline
git diff main origin/main

# 5. 확인 후 merge
git merge origin/main
cat feature.txt  # "새 기능" 확인!

# 6. 두 번째 remote 추가
mkdir ../mirror.git
cd ../mirror.git && git init --bare && cd ../workspace
git remote add mirror ../mirror.git
git push mirror main

# 7. 전체 remote 확인
git remote -v

# 8. 모든 remote에서 fetch
git fetch --all
```

## 더 깊이 알아보기

### refspec — fetch의 내부 동작

`git fetch`가 실제로 무엇을 가져올지는 **refspec**이라는 규칙으로 결정됩니다. `.git/config` 파일에서 확인할 수 있어요:

```bash
git config --get remote.origin.fetch
```

```output
+refs/heads/*:refs/remotes/origin/*
```

이것을 해석하면:
- `refs/heads/*` — 원격의 모든 브랜치를
- `refs/remotes/origin/*` — 로컬의 `origin/` 아래에 저장
- `+` — fast-forward가 아니어도 강제 업데이트

특정 브랜치만 fetch하고 싶다면:

```bash
# main 브랜치만 fetch
git fetch origin main

# 특정 브랜치를 다른 이름으로 fetch
git fetch origin main:refs/remotes/origin/production
```

> 💡 **알고 계셨나요?**: refspec은 Git의 초기 설계부터 존재한 강력한 기능입니다. Linus Torvalds는 Git을 "콘텐츠 주소 지정 파일시스템(content-addressable filesystem)"으로 설계했는데, refspec은 이 파일시스템에서 어떤 참조(refs)를 가져올지 정의하는 매핑 규칙이에요. 대부분의 개발자는 기본 설정으로 충분하지만, CI/CD나 특수한 워크플로우에서는 refspec을 직접 조작해야 할 때가 있습니다.

### fetch.writeCommitGraph — 성능 최적화

Git 2.18부터 도입된 commit graph는 fetch 시 성능을 크게 향상시킵니다:

```bash
# fetch 시 commit graph 자동 업데이트 (대규모 저장소에서 효과적)
git config --global fetch.writeCommitGraph true
```

이 설정은 `git log`, `git merge-base` 같은 히스토리 탐색 명령어의 속도를 큰 저장소에서 최대 수십 배까지 높여줍니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`git fetch --all`은 모든 브랜치의 코드를 다운로드한다" — 부분적으로 맞지만, **작업 디렉토리에는 아무 변화가 없습니다.** 모든 remote의 원격 추적 브랜치만 업데이트될 뿐, 로컬 브랜치를 merge하거나 코드를 변경하지 않아요.

> 🔥 **실무 팁**: `git fetch` 후 `git log --oneline HEAD..origin/main`을 습관화하세요. "원격에 내가 아직 받지 않은 커밋이 몇 개 있는지" 한눈에 파악할 수 있습니다. `..` 두 개가 핵심입니다 — "HEAD에는 없지만 origin/main에는 있는 커밋"을 보여줍니다.

> 🔥 **실무 팁**: `git remote update`는 `git fetch --all`과 거의 같은 동작을 합니다. 하지만 `fetch --all`이 더 명시적이고 널리 사용되므로, `fetch --all`을 추천합니다.

## 핵심 정리

| 명령어 | 설명 |
|--------|------|
| `git fetch` | 원격 변경사항을 다운로드 (병합 안 함) |
| `git fetch --all` | 모든 remote에서 fetch |
| `git fetch --prune` | fetch + 삭제된 원격 브랜치 참조 정리 |
| `git remote prune origin` | 원격에서 삭제된 브랜치 참조만 정리 |
| `git log HEAD..origin/main` | fetch 후 새 커밋 확인 |
| `git diff main origin/main` | fetch 후 변경 내용 비교 |
| `git remote add <이름> <URL>` | 새 remote 추가 |
| `git fetch --all && git branch -vv` | 전체 동기화 상태 확인 |

## 다음 섹션 미리보기

원격 저장소와의 통신을 마스터했습니다! 그런데 한 가지 — `push`나 `fetch`를 할 때마다 비밀번호를 입력하고 계신가요? 다음 섹션 [SSH와 인증](./05-auth.md)에서는 **SSH 키와 인증 설정**을 배워서, 한 번 설정하면 비밀번호 없이도 안전하게 원격과 통신하는 방법을 알아봅니다.

## 참고 자료

- [Pro Git Book — Remote Branches](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches) - 원격 브랜치와 fetch의 동작 원리
- [Git 공식 문서 — git-fetch](https://git-scm.com/docs/git-fetch) - fetch 명령어 전체 옵션
- [Git 공식 문서 — git-remote](https://git-scm.com/docs/git-remote) - remote 관리 전체 옵션
- [GitLab Blog — Git pull vs. git fetch](https://about.gitlab.com/blog/git-pull-vs-git-fetch-whats-the-difference/) - fetch와 pull 비교 가이드
