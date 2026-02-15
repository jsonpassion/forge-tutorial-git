# 히스토리 탐색

> log 옵션, blame, show, shortlog — 커밋 기록 살펴보기

## 개요

[커밋의 기본](../01-git-start/04-commit-basics.md)에서 `git log --oneline`으로 이력을 간단히 조회하는 법을 배웠고, [변경 추적과 커밋 메시지](../01-git-start/05-diff-messages.md)에서 `git show`로 특정 커밋을 살펴보는 법도 익혔는데요. 이번에는 **진짜 실무에서 쓰는** 히스토리 탐색 기법을 본격적으로 배워봅시다. 수천 개의 커밋 속에서 원하는 정보를 정확히 찾아내는 능력, 프로 개발자의 필수 스킬입니다.

**선수 지식**: [커밋의 기본](../01-git-start/04-commit-basics.md)의 `git log` 기초, [변경 추적과 커밋 메시지](../01-git-start/05-diff-messages.md)의 `git show`
**학습 목표**:
- `git log`의 고급 필터링과 포맷 옵션을 활용할 수 있다
- `git blame`으로 코드의 작성자와 변경 이력을 추적할 수 있다
- `git shortlog`로 기여자 통계를 확인할 수 있다

## 왜 알아야 할까?

"이 버그가 언제부터 있었지?", "이 코드를 누가 작성했지?", "지난달에 어떤 변경이 있었지?" — 개발을 하다 보면 이런 질문이 수시로 생깁니다. Git의 히스토리 탐색 도구는 이 모든 질문에 답을 줄 수 있는데요, 도구를 얼마나 잘 쓰느냐에 따라 **몇 시간이 걸릴 디버깅이 몇 분 만에** 끝나기도 합니다.

## 핵심 개념

### git log 고급 옵션

> 💡 **비유**: `git log`의 기본 옵션이 **도서관 전체 서가를 둘러보는 것**이라면, 고급 옵션은 **사서에게 "2024년에 출판된 한국어 소설 중 200페이지 이하인 것"이라고 구체적으로 요청**하는 것과 같습니다.

#### 출력 형식 다듬기

```bash
# 한 줄 요약 (가장 많이 사용)
git log --oneline

# 변경 통계 포함
git log --stat

# 변경 내용(diff) 포함
git log -p

# 그래프 시각화 (브랜치 구조 파악에 유용)
git log --oneline --graph --all --decorate
```

#### 커스텀 포맷

`--pretty=format`을 사용하면 원하는 정보만 골라서 표시할 수 있습니다:

```bash
# 해시, 작성자, 날짜, 제목을 깔끔하게
git log --pretty=format:"%h %an (%ar) - %s"
```

```output
a1b2c3d 홍길동 (2 hours ago) - 로그인 버그 수정
f7e8d9c 김철수 (3 days ago) - 회원가입 기능 추가
b3c4d5e 홍길동 (1 week ago) - 프로젝트 초기화
```

자주 쓰는 포맷 플레이스홀더:

| 플레이스홀더 | 의미 | 예시 |
|-------------|------|------|
| `%H` / `%h` | 커밋 해시 (전체/축약) | `a1b2c3d4` / `a1b2c3d` |
| `%an` | 작성자 이름 | `홍길동` |
| `%ae` | 작성자 이메일 | `gildong@example.com` |
| `%ar` | 상대 날짜 | `2 hours ago` |
| `%ad` | 날짜 (--date 옵션에 따름) | `2026-02-15` |
| `%s` | 커밋 메시지 제목 | `로그인 버그 수정` |
| `%d` | 브랜치/태그 참조 | `(HEAD -> main, tag: v1.0)` |

#### 날짜 형식 옵션

```bash
# 상대 시간
git log --date=relative --format="%h %ad %s"

# YYYY-MM-DD만 표시
git log --date=short --format="%h %ad %s"

# 사람 친화적 (불필요한 정보 생략)
git log --date=human --format="%h %ad %s"

# ISO 8601 형식
git log --date=iso --format="%h %ad %s"
```

#### 커밋 필터링 — 원하는 것만 찾기

이 부분이 실무에서 정말 많이 쓰이는데요:

```bash
# 최근 N개 커밋만
git log -5

# 날짜 범위로 필터
git log --after="2026-01-01" --before="2026-02-01"
git log --after="1 week ago"
git log --since="yesterday"

# 작성자로 필터
git log --author="홍길동"
git log --author="홍길동\|김철수"    # 여러 명 (정규식 지원)

# 커밋 메시지 검색
git log --grep="버그"
git log --grep="fix" -i            # 대소문자 무시

# 특정 파일의 이력만
git log -- src/login.js
git log --oneline -- "*.css"       # 모든 CSS 파일
```

#### Pickaxe — 코드 변경을 추적하는 비밀 무기

`-S`와 `-G` 옵션은 **코드에서 특정 문자열이 추가되거나 삭제된 커밋**을 찾아줍니다. 이름이 "pickaxe(곡괭이)"인 이유는 커밋 역사를 파헤치듯 검색하기 때문이에요:

```bash
# "validateEmail"이 추가되거나 제거된 커밋 찾기
git log -S "validateEmail"

# 정규식으로 검색 (패턴 매칭)
git log -G "function\s+validate"

# diff도 함께 보기
git log -S "validateEmail" -p
```

> 🔥 **실무 팁**: "이 함수가 언제 추가됐지?" 또는 "이 코드가 언제 삭제됐지?"를 찾을 때 `-S` 옵션이 정말 강력합니다. `git blame`이 현재 존재하는 코드만 추적한다면, `-S`는 **사라진 코드**까지 찾아냅니다.

### git blame — 누가 이 코드를 작성했나?

> 💡 **비유**: `git blame`은 **출석부 옆에 적힌 메모**와 같습니다. 코드의 각 줄 옆에 "이 줄은 누가, 언제, 어떤 커밋에서 작성했는지"가 적혀 있어서, 궁금한 줄을 바로 추적할 수 있죠.

```bash
# 파일의 각 줄에 작성자와 커밋 정보 표시
git blame README.md
```

```output
a1b2c3d4 (홍길동 2026-02-15 10:00:00 +0900  1) # My Project
a1b2c3d4 (홍길동 2026-02-15 10:00:00 +0900  2)
f7e8d9c0 (김철수 2026-02-15 11:30:00 +0900  3) ## 설치 방법
f7e8d9c0 (김철수 2026-02-15 11:30:00 +0900  4) npm install
b3c4d5e1 (홍길동 2026-02-15 14:00:00 +0900  5)
b3c4d5e1 (홍길동 2026-02-15 14:00:00 +0900  6) ## 사용법
```

#### blame 활용 옵션

```bash
# 특정 줄 범위만 확인 (10~20번째 줄)
git blame -L 10,20 src/app.js

# 특정 함수만 추적 (함수 이름으로)
git blame -L :handleLogin src/auth.js

# 공백 변경 무시 (포맷팅 변경 걸러내기)
git blame -w src/app.js

# 코드 이동 감지 (같은 파일 내)
git blame -M src/app.js

# 코드 복사 감지 (다른 파일에서 복사해 온 경우)
git blame -C src/app.js
```

> 💡 **알고 계셨나요?**: "blame(비난)"이라는 이름이 너무 공격적이라고 느끼는 개발자들도 있습니다. 그래서 Subversion에서는 같은 기능에 `blame`, `praise(칭찬)`, `annotate(주석 달기)`라는 **세 가지 별명**을 모두 지원했죠! Git에도 `git annotate`라는 호환 명령어가 있지만, 출력 형식만 살짝 다를 뿐 기능은 동일합니다. 참고로 "blame" 명령어의 역사는 1997년 넷스케이프에서 만들어진 `cvsblame`이라는 Perl 스크립트까지 거슬러 올라갑니다.

### git shortlog — 기여자 통계

`git shortlog`는 커밋을 **작성자별로 그룹화**하여 보여줍니다. 프로젝트의 기여도를 한눈에 파악할 때 유용하죠:

```bash
# 작성자별 커밋 그룹화
git shortlog

# 커밋 수 기준 정렬 (요약)
git shortlog -sn
```

```output
    15  홍길동
     8  김철수
     3  이영희
```

```bash
# 이메일 포함
git shortlog -sne

# 특정 기간의 기여도
git shortlog -sn --after="2026-01-01"
```

### git show 심화

[변경 추적과 커밋 메시지](../01-git-start/05-diff-messages.md)에서 `git show`의 기본 사용법을 배웠는데, 여기서 몇 가지 유용한 옵션을 더 알아봅시다:

```bash
# 커밋 메시지만 보기 (diff 없이)
git show -s --format="%h %an: %s" a1b2c3d

# 특정 커밋의 특정 파일 내용 보기
git show a1b2c3d:src/app.js

# 태그의 상세 정보 보기
git show v1.0.0

# 변경 통계만 보기
git show --stat a1b2c3d
```

## 실습: 직접 해보기

```bash
# 1. 기존 실습 저장소에서 여러 커밋 만들기
mkdir log-practice && cd log-practice
git init

# 2. 여러 파일과 커밋 만들기
echo "# Log Practice" > README.md
git add . && git commit -m "프로젝트 초기화"

echo "function login() {}" > auth.js
git add . && git commit -m "feat: 로그인 함수 추가"

echo "function signup() {}" >> auth.js
git add . && git commit -m "feat: 회원가입 함수 추가"

echo "body { margin: 0; }" > style.css
git add . && git commit -m "style: 기본 스타일 추가"

echo "## 설치법" >> README.md
git add . && git commit -m "docs: README에 설치법 추가"

# 3. 다양한 log 옵션 테스트
git log --oneline
git log --pretty=format:"%h (%ar) %s"
git log --oneline --graph

# 4. 필터링
git log --grep="feat"
git log -- auth.js

# 5. blame
git blame auth.js

# 6. shortlog
git shortlog -sn

# 7. pickaxe로 특정 코드 찾기
git log -S "signup" --oneline
```

## 더 깊이 알아보기

### 유용한 log 별칭 모음

히스토리 탐색을 더 편리하게 만들어 주는 Git 별칭을 설정해 봅시다:

```bash
# 예쁜 그래프 로그
git config --global alias.lg "log --oneline --graph --all --decorate"

# 오늘 한 작업 보기
git config --global alias.today "log --oneline --since='midnight'"

# 내 커밋만 보기 (user.name 기준)
git config --global alias.mine "log --oneline --author='$(git config user.name)'"
```

이제 `git lg`, `git today`, `git mine`으로 빠르게 이력을 확인할 수 있습니다!

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`git blame`은 범인을 찾기 위한 도구다" — blame이라는 이름 때문에 오해가 생기지만, 실제로는 **코드의 맥락을 이해**하기 위한 도구입니다. "이 줄은 왜 이렇게 작성됐지?"를 알기 위해 해당 커밋의 메시지를 찾아보는 것이 올바른 사용법이에요. 누군가를 비난하기 위한 도구가 아닙니다!

> 🔥 **실무 팁**: `git log`에 유용한 별칭을 설정해두면 생산성이 크게 올라갑니다. 특히 `git lg`(그래프 로그)는 거의 모든 시니어 개발자가 쓰는 별칭이에요. [설치와 초기 설정](../01-git-start/02-install-setup.md)에서 배운 `git config --global alias`를 활용하세요.

## 핵심 정리

| 명령어 | 설명 |
|--------|------|
| `git log --oneline --graph --all` | 그래프 형태의 전체 이력 시각화 |
| `git log --author="이름"` | 특정 작성자의 커밋만 필터 |
| `git log --grep="키워드"` | 커밋 메시지에서 키워드 검색 |
| `git log -S "코드"` | 특정 코드가 변경된 커밋 찾기 (Pickaxe) |
| `git log -- <파일>` | 특정 파일의 변경 이력만 조회 |
| `git blame <파일>` | 파일의 각 줄의 작성자와 커밋 정보 표시 |
| `git shortlog -sn` | 작성자별 커밋 수 통계 |

## 다음 섹션 미리보기

히스토리를 자유롭게 탐색할 수 있게 되었으니, 이제 **히스토리를 되돌리는 법**을 배울 차례입니다. 다음 섹션 [되돌리기 기초](./04-undo-basics.md)에서는 `git reset`, `git revert`, `git commit --amend`로 실수를 바로잡는 세 가지 방법을 배웁니다.

## 참고 자료

- [Pro Git Book — 커밋 히스토리 조회하기](https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-%EC%BB%A4%EB%B0%8B-%ED%9E%88%EC%8A%A4%ED%86%A0%EB%A6%AC-%EC%A1%B0%ED%9A%8C%ED%95%98%EA%B8%B0) - git log 공식 가이드
- [Git 공식 문서 — git-blame](https://git-scm.com/docs/git-blame) - blame의 모든 옵션 레퍼런스
- [Atlassian Git Tutorial — Git Log](https://www.atlassian.com/git/tutorials/git-log) - 고급 로그 옵션 시각적 설명
- [Git 공식 문서 — git-shortlog](https://git-scm.com/docs/git-shortlog) - shortlog 옵션 레퍼런스
