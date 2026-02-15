# 브랜치 관리

> 삭제, 이름 변경, 목록 관리, 브랜치 정리 전략

## 개요

브랜치를 만들고, 머지하고, 충돌도 해결했습니다. 하지만 프로젝트가 커지면 브랜치도 쌓이기 시작하는데요 — 다 쓴 브랜치, 오래된 브랜치, 이름이 잘못된 브랜치가 뒤섞이면 혼란스러워집니다. 이번 섹션에서는 브랜치를 깔끔하게 **정리하고 관리하는 방법**을 배웁니다.

**선수 지식**: [브랜치 생성과 전환](./02-create-switch.md), [브랜치 병합](./03-merge.md)
**학습 목표**:
- 머지 완료된 브랜치를 안전하게 삭제할 수 있다
- 브랜치 이름을 변경할 수 있다
- 브랜치 목록을 다양한 방식으로 확인하고 필터링할 수 있다
- 정기적인 브랜치 정리 습관을 세울 수 있다

## 왜 알아야 할까?

실무 프로젝트에서 `git branch`를 실행했을 때 브랜치가 수십 개 나열되는 건 흔한 일입니다. 이 중 절반은 이미 머지되어 필요 없는 것들이죠. 브랜치를 정리하지 않으면 "이 브랜치가 아직 작업 중인 건지, 다 끝난 건지" 파악하기 어려워지고, 팀원 간 혼란이 생깁니다. **깔끔한 브랜치 관리는 좋은 개발 습관**의 핵심입니다.

## 핵심 개념

### 브랜치 목록 확인

> 💡 **비유**: 브랜치 목록 보기는 **책상 서랍 정리 전에 서랍 안을 확인하는 것**과 같습니다. 뭐가 있는지 알아야 정리를 시작할 수 있죠.

```bash
# 로컬 브랜치 목록
git branch
```

```output
  feature-login
  feature-signup
  hotfix-bug
* main
```

```bash
# 각 브랜치의 마지막 커밋도 함께 표시
git branch -v
```

```output
  feature-login  a1b2c3d 로그인 기능 구현
  feature-signup b2c3d4e 회원가입 폼 추가
  hotfix-bug     c3d4e5f 긴급 버그 수정
* main           d4e5f6g Merge branch 'hotfix-bug'
```

```bash
# 원격 브랜치 포함 전체 목록
git branch -a
```

```output
  feature-login
  feature-signup
  hotfix-bug
* main
  remotes/origin/main
  remotes/origin/feature-login
  remotes/origin/develop
```

```bash
# 원격 브랜치만 보기
git branch -r
```

#### 머지 상태로 필터링하기

정리할 때 가장 유용한 옵션은 머지 상태 필터입니다:

```bash
# main에 이미 머지된 브랜치 (삭제해도 안전!)
git branch --merged main
```

```output
  feature-login
  hotfix-bug
* main
```

```bash
# 아직 머지되지 않은 브랜치 (삭제 주의!)
git branch --no-merged main
```

```output
  feature-signup
```

> 🔥 **실무 팁**: `git branch --merged main`에 나오는 브랜치는 이미 main에 합쳐진 것이므로 **안심하고 삭제**할 수 있습니다. 정기적으로 이 목록을 확인하고 정리하는 습관을 들이세요.

### 브랜치 삭제

> 💡 **비유**: 브랜치 삭제는 **포스트잇 화살표를 떼어내는 것**입니다. 화살표(포인터)만 없어지는 거지, 책의 내용(커밋)은 그대로 남아 있어요. 이미 main에 머지된 내용이라면, 브랜치를 지워도 아무것도 잃지 않습니다.

```bash
# 안전한 삭제: 머지 완료된 브랜치만 삭제 가능
git branch -d feature-login
```

```output
Deleted branch feature-login (was a1b2c3d).
```

```bash
# 머지되지 않은 브랜치를 삭제하려고 하면?
git branch -d feature-signup
```

```error
error: The branch 'feature-signup' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-signup'.
```

Git이 안전장치를 걸어준 거예요! 아직 머지되지 않은 브랜치는 `-d`로 삭제할 수 없습니다.

```bash
# ⚠️ 강제 삭제: 머지 여부와 상관없이 삭제
git branch -D feature-signup
```

> ⚠️ **흔한 오해**: "브랜치를 삭제하면 커밋도 사라진다" — 브랜치가 가리키던 커밋은 **바로 삭제되지 않습니다**. 다른 브랜치나 태그에서 접근할 수 있다면 그대로 남아 있고, 접근할 수 없는 커밋도 약 30일간 [reflog](../09-history-internals/02-reflog.md)에 보존됩니다. 단, 이 기간이 지나면 `git gc`에 의해 정리될 수 있으니, 중요한 작업이 있는 브랜치는 머지하거나 태그를 달아두세요.

### 현재 브랜치는 삭제할 수 없다

```bash
# main에 있는 상태에서 main 삭제 시도
git branch -d main
```

```error
error: Cannot delete branch 'main' checked out at '/Users/you/project'
```

삭제하려는 브랜치에서 먼저 다른 브랜치로 이동해야 합니다.

### 브랜치 이름 변경

```bash
# 현재 브랜치의 이름 변경
git branch -m new-feature-name
```

```bash
# 특정 브랜치의 이름 변경 (해당 브랜치에 있지 않아도 됨)
git branch -m old-name new-name
```

```bash
# 이름이 이미 존재하는 경우 강제 변경
# ⚠️ 주의: 기존 브랜치를 덮어씁니다!
git branch -M old-name new-name
```

#### 원격 브랜치 이름 변경하기

원격 브랜치는 직접 이름을 바꿀 수 없어서, "삭제 후 새 이름으로 푸시"하는 방식을 사용합니다:

```bash
# 1. 로컬에서 이름 변경
git branch -m old-feature new-feature

# 2. 새 이름으로 원격에 푸시
git push origin new-feature

# 3. 원격의 이전 이름 브랜치 삭제
git push origin --delete old-feature
```

> 🔥 **실무 팁**: 공유 브랜치의 이름을 변경할 때는 반드시 **팀원에게 먼저 알리세요**. 다른 사람이 이전 이름의 브랜치를 추적하고 있다면, 그들의 로컬 환경에서도 조치가 필요합니다.

### 브랜치 정리 전략

#### 원격에서 삭제된 브랜치 동기화: prune

팀원이 원격에서 브랜치를 삭제해도, 내 로컬의 원격 추적 브랜치(remote-tracking branch)는 남아 있습니다. `prune`으로 정리할 수 있습니다.

```bash
# 원격에서 삭제된 브랜치의 로컬 참조 정리
git fetch --prune
```

```bash
# 자동 prune 설정 (fetch할 때마다 자동 실행)
git config --global fetch.prune true
```

> 💡 **알고 계셨나요?**: `git fetch --prune`은 원격 추적 브랜치(`remotes/origin/...`)만 정리합니다. 로컬 브랜치는 절대 건드리지 않으니 안심하세요. 로컬 브랜치 정리는 직접 `git branch -d`로 해야 합니다.

#### 머지된 로컬 브랜치 일괄 삭제

```bash
# main에 머지된 브랜치를 확인 후 삭제 (main, develop 제외)
git branch --merged main | grep -v "main\|develop" | xargs git branch -d
```

이 명령어를 분해하면:
1. `git branch --merged main` — main에 머지된 브랜치 목록
2. `grep -v "main\|develop"` — main과 develop은 제외
3. `xargs git branch -d` — 나머지를 하나씩 삭제

#### 오래된 브랜치 찾기

```bash
# 각 브랜치의 마지막 커밋 날짜 확인
git branch -v --sort=-committerdate
```

```output
* main           d4e5f6g 2026-02-15 최신 머지
  feature-new    c3d4e5f 2026-02-14 기능 개발 중
  old-experiment a1b2c3d 2025-11-03 실험적 시도
```

```bash
# 마지막 커밋이 오래된 순으로 정렬
git branch -v --sort=committerdate
```

```bash
# 특정 날짜 이전에 마지막 커밋된 브랜치 찾기 (format으로 날짜 확인)
git for-each-ref --sort=committerdate refs/heads/ --format='%(committerdate:short) %(refname:short)'
```

```output
2025-11-03 old-experiment
2026-02-14 feature-new
2026-02-15 main
```

## 실습: 직접 해보기

```bash
# 1. 실습 저장소 준비
mkdir branch-mgmt && cd branch-mgmt
git init

# 2. 초기 커밋
echo "# 브랜치 관리 실습" > README.md
git add . && git commit -m "초기 커밋"

# 3. 여러 브랜치 만들기
git branch feature-a
git branch feature-b
git branch hotfix-1
git branch experiment

# 4. 브랜치 목록 확인
git branch

# 5. feature-a에서 작업 후 머지
git switch feature-a
echo "기능 A" > a.txt
git add . && git commit -m "기능 A 추가"
git switch main
git merge feature-a

# 6. hotfix-1에서 작업 후 머지
git switch hotfix-1
echo "긴급 수정" > fix.txt
git add . && git commit -m "긴급 버그 수정"
git switch main
git merge hotfix-1

# 7. 머지된 브랜치 확인
git branch --merged main

# 8. 머지된 브랜치 삭제 (안전!)
git branch -d feature-a
git branch -d hotfix-1

# 9. 머지되지 않은 브랜치 확인
git branch --no-merged main

# 10. 브랜치 이름 변경
git branch -m feature-b feature-beta

# 11. 최종 상태 확인
git branch -v
git log --oneline --graph --all
```

## 더 깊이 알아보기

### 기본 브랜치 이름의 변천사

Git의 기본 브랜치 이름은 오랫동안 `master`였습니다. 하지만 2020년, 기술 업계 전반에서 포용적인 언어 사용에 대한 논의가 활발해지면서, GitHub는 새 저장소의 기본 브랜치를 `main`으로 변경했습니다. Git 자체도 2.28 버전(2020년 7월)부터 `init.defaultBranch` 설정을 추가하여, 사용자가 기본 브랜치 이름을 자유롭게 지정할 수 있게 했습니다.

```bash
# 기본 브랜치 이름을 main으로 설정
git config --global init.defaultBranch main
```

기존 저장소의 `master`를 `main`으로 변경하려면:

```bash
# 로컬에서 이름 변경
git branch -m master main

# 원격에 반영 (원격 저장소 권한 필요)
git push -u origin main

# 원격의 master 삭제
git push origin --delete master
```

> 💡 **알고 계셨나요?**: `master`라는 이름은 원래 BitKeeper(Git 이전에 리눅스 커널이 사용하던 VCS)의 "master/slave" 용어에서 유래했습니다. Git으로 전환할 때 이 이름이 관성적으로 이어진 것이죠. 현재 대부분의 오픈소스 프로젝트와 기업은 `main`을 기본 브랜치로 사용합니다.

### 유용한 alias 설정

브랜치 관리를 자주 하다 보면 반복 명령어가 많아집니다. alias로 간소화하세요:

```bash
# 머지된 브랜치 목록
git config --global alias.bm "branch --merged"

# 머지되지 않은 브랜치 목록
git config --global alias.bnm "branch --no-merged"

# 브랜치를 최신 순으로 보기
git config --global alias.recent "branch -v --sort=-committerdate"

# 커밋 그래프 보기
git config --global alias.lg "log --oneline --graph --all --decorate"
```

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`-d`와 `-D`는 같은 거다" — 아닙니다! `-d`(소문자)는 **안전한 삭제**로, 머지되지 않은 브랜치를 삭제하려 하면 거부합니다. `-D`(대문자)는 **강제 삭제**로, 머지 여부와 상관없이 즉시 삭제합니다. 평소에는 항상 `-d`를 사용하고, 정말 필요한 경우에만 `-D`를 쓰세요.

> 🔥 **실무 팁**: `git fetch --prune`을 자동화하세요. `git config --global fetch.prune true`를 설정하면, `git fetch`할 때마다 원격에서 삭제된 브랜치의 로컬 참조가 자동으로 정리됩니다. 수동으로 할 필요가 없어져요.

> 🔥 **실무 팁**: 팀에서 "머지된 브랜치는 즉시 삭제" 규칙을 세우세요. GitHub에서 PR을 머지하면 자동으로 브랜치를 삭제하는 옵션이 있습니다. Settings → General → "Automatically delete head branches"를 켜면 됩니다.

## 핵심 정리

| 명령어 | 설명 |
|--------|------|
| `git branch` | 로컬 브랜치 목록 |
| `git branch -v` | 마지막 커밋과 함께 목록 표시 |
| `git branch -a` | 원격 브랜치 포함 전체 목록 |
| `git branch --merged` | 머지 완료된 브랜치 필터 |
| `git branch --no-merged` | 머지되지 않은 브랜치 필터 |
| `git branch -d 이름` | 안전한 삭제 (머지 확인) |
| `git branch -D 이름` | 강제 삭제 (주의!) |
| `git branch -m 새이름` | 현재 브랜치 이름 변경 |
| `git fetch --prune` | 원격에서 삭제된 브랜치 참조 정리 |

## 다음 섹션 미리보기

Ch3 브랜치의 모든 기초를 마쳤습니다! 브랜치를 만들고, 전환하고, 합치고, 충돌을 해결하고, 정리하는 방법까지 배웠죠. 다음 챕터 [Ch4. 원격 저장소](../04-remote/01-remote-concept.md)에서는 이 브랜치들을 **로컬을 넘어 원격(GitHub 등)과 연결하는 방법**을 배웁니다. 혼자만의 작업에서 세상과 연결되는 순간이에요!

## 참고 자료

- [Pro Git Book — 브랜치 관리](https://git-scm.com/book/en/v2/Git-Branching-Branch-Management) - 공식 브랜치 관리 가이드
- [Git 공식 문서 — git-branch](https://git-scm.com/docs/git-branch) - branch 명령어 전체 옵션
- [GitHub Docs — About branches](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-branches) - GitHub의 브랜치 관리 가이드
- [Git Tower — Clean Up Remote Branches with Prune](https://www.git-tower.com/learn/git/faq/cleanup-remote-branches-with-git-prune/) - prune으로 브랜치 정리하기
