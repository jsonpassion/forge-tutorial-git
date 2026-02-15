# 충돌 해결

> 충돌 발생 원인, 마커 읽기, 단계별 해결 전략

## 개요

앞서 머지를 배울 때, Git이 서로 다른 파일의 변경 사항을 자동으로 합쳐준다는 것을 봤습니다. 하지만 **같은 파일의 같은 부분**을 두 브랜치에서 동시에 고쳤다면 어떻게 될까요? Git도 "이건 어떤 버전을 써야 할지 모르겠어요"라고 손을 듭니다. 이것이 바로 **충돌(conflict)**입니다.

**선수 지식**: [브랜치 병합](./03-merge.md)에서 배운 merge의 기본 동작
**학습 목표**:
- 충돌이 왜 발생하는지 원리를 이해한다
- 충돌 마커(`<<<<<<<`, `=======`, `>>>>>>>`)를 읽고 해석할 수 있다
- CLI와 에디터에서 충돌을 단계별로 해결할 수 있다

## 왜 알아야 할까?

충돌은 피할 수 없습니다. 팀으로 일하면 같은 파일을 여러 사람이 동시에 수정하는 일이 반드시 생기거든요. 혼자 개발할 때도, 여러 브랜치에서 동일한 설정 파일이나 공통 모듈을 건드리면 충돌이 발생합니다. 충돌을 두려워하면 브랜치 사용을 꺼리게 되고, 결국 Git의 장점을 제대로 활용하지 못하게 됩니다. 충돌 해결은 **배우면 익숙해지는 기술**이에요.

## 핵심 개념

### 충돌은 왜 생길까?

> 💡 **비유**: 충돌은 **같은 문장을 두 사람이 동시에 다르게 고쳤을 때** 생깁니다. 논문을 공동 집필하는데, A가 "오늘 날씨가 좋다"를 "오늘 날씨가 맑다"로, B가 "오늘 날씨가 따뜻하다"로 각각 고쳤다면 — 어떤 버전을 쓸지 Git도 결정할 수 없죠.

Git이 자동으로 합칠 수 있는 경우와 없는 경우를 정리하면:

| 상황 | 결과 |
|------|------|
| A가 `file1.txt`를, B가 `file2.txt`를 수정 | 자동 머지 (다른 파일) |
| A가 `file.txt`의 1~5줄을, B가 20~25줄을 수정 | 자동 머지 (같은 파일, 다른 부분) |
| A가 `file.txt`의 3줄을, B도 3줄을 수정 | **충돌!** (같은 파일, 같은 부분) |
| A가 파일 삭제, B가 같은 파일 수정 | **충돌!** (수정 vs 삭제) |

### 충돌 직접 만들어보기

충돌을 이해하는 가장 좋은 방법은 직접 만들어보는 것입니다.

```bash
# 1. 실습 저장소 준비
mkdir conflict-practice && cd conflict-practice
git init

# 2. 공통 파일 만들기
cat > greeting.txt << 'EOF'
안녕하세요!
저는 개발자입니다.
Git을 공부하고 있습니다.
EOF
git add . && git commit -m "초기 커밋: 인사말 파일"

# 3. feature 브랜치에서 2번째 줄 수정
git switch -c feature
sed -i '' 's/저는 개발자입니다./저는 프론트엔드 개발자입니다./' greeting.txt
git add . && git commit -m "직무를 구체적으로 변경"

# 4. main으로 돌아와서 같은 2번째 줄을 다르게 수정
git switch main
sed -i '' 's/저는 개발자입니다./저는 백엔드 개발자입니다./' greeting.txt
git add . && git commit -m "직무를 백엔드로 변경"

# 5. 머지 시도 → 충돌 발생!
git merge feature
```

```error
Auto-merging greeting.txt
CONFLICT (content): Merge conflict in greeting.txt
Automatic merge failed; fix conflicts and then commit the result.
```

### 충돌 마커 읽기

충돌이 발생하면 Git은 파일에 **충돌 마커**를 삽입합니다. 이 마커를 읽을 줄 알면 반은 해결한 겁니다.

```bash
# 충돌 파일 내용 확인
cat greeting.txt
```

```output
안녕하세요!
<<<<<<< HEAD
저는 백엔드 개발자입니다.
=======
저는 프론트엔드 개발자입니다.
>>>>>>> feature
Git을 공부하고 있습니다.
```

마커의 의미를 하나씩 살펴볼게요:

| 마커 | 의미 |
|------|------|
| `<<<<<<< HEAD` | 현재 브랜치(main)의 내용 시작 |
| `=======` | 구분선 — 위가 "ours", 아래가 "theirs" |
| `>>>>>>> feature` | 병합하려는 브랜치(feature)의 내용 끝 |

> ⚠️ **흔한 오해**: "HEAD는 항상 내 코드, feature는 항상 상대 코드" — 대부분 맞지만, **rebase** 중에는 의미가 뒤바뀝니다. rebase에서는 HEAD가 상대 브랜치를, "theirs"가 내 변경 사항을 가리킵니다. 이 부분은 [Rebase 기초](../08-advanced-branch/01-rebase.md)에서 자세히 다룹니다.

### diff3 스타일 — 더 많은 정보 보기

기본 충돌 마커에는 "원래 내용(공통 조상)"이 빠져 있어서, 양쪽이 각각 뭘 바꾼 건지 파악하기 어려울 수 있습니다. `diff3` 스타일을 사용하면 **원본도 함께** 볼 수 있습니다.

```bash
# diff3 스타일 전역 설정 (권장!)
git config --global merge.conflictstyle diff3
```

설정 후 충돌 마커가 이렇게 바뀝니다:

```output
안녕하세요!
<<<<<<< HEAD
저는 백엔드 개발자입니다.
||||||| parent of abc1234
저는 개발자입니다.
=======
저는 프론트엔드 개발자입니다.
>>>>>>> feature
Git을 공부하고 있습니다.
```

가운데 `|||||||` 블록이 **원본 내용**입니다. 이제 "원래는 '개발자'였는데, main은 '백엔드 개발자'로, feature는 '프론트엔드 개발자'로 바꿨구나"를 한눈에 알 수 있죠!

> 🔥 **실무 팁**: `merge.conflictstyle diff3`는 거의 모든 상황에서 유용합니다. 원본이 보이면 양쪽의 의도를 파악하기 훨씬 쉬워지거든요. Git 설정에 꼭 추가하세요.

### 충돌 해결: 단계별 가이드

충돌을 해결하는 과정은 명확합니다:

**1단계: 충돌 파일 파악**

```bash
git status
```

```output
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   greeting.txt

no changes added to commit (use "git add" to track)
```

**2단계: 충돌 파일 편집**

파일을 열어서 마커를 제거하고, **최종적으로 원하는 내용**만 남깁니다.

```bash
# 방법 A: "ours" 선택 (현재 브랜치 내용만)
cat > greeting.txt << 'EOF'
안녕하세요!
저는 백엔드 개발자입니다.
Git을 공부하고 있습니다.
EOF
```

```bash
# 방법 B: "theirs" 선택 (병합 브랜치 내용만)
cat > greeting.txt << 'EOF'
안녕하세요!
저는 프론트엔드 개발자입니다.
Git을 공부하고 있습니다.
EOF
```

```bash
# 방법 C: 양쪽 합치기 (가장 흔한 경우)
cat > greeting.txt << 'EOF'
안녕하세요!
저는 풀스택 개발자입니다.
Git을 공부하고 있습니다.
EOF
```

**3단계: 해결했다고 Git에 알리기**

```bash
# 해결된 파일을 스테이징
git add greeting.txt
```

**4단계: 머지 커밋 완료**

```bash
# 머지 커밋 생성
git commit
```

에디터가 열리면서 기본 머지 메시지가 표시됩니다. 그대로 저장해도 되고, 충돌 해결 내용을 추가로 적어도 됩니다.

```output
[main abc1234] Merge branch 'feature'
```

### 빠른 해결: 한쪽 전체 선택

충돌 파일이 많거나, 한쪽 버전을 통째로 선택하고 싶을 때:

```bash
# 현재 브랜치(ours) 내용으로 전체 파일 해결
git checkout --ours greeting.txt
git add greeting.txt

# 병합 브랜치(theirs) 내용으로 전체 파일 해결
git checkout --theirs greeting.txt
git add greeting.txt
```

### VS Code에서 충돌 해결하기

VS Code는 충돌 마커를 자동으로 감지해서 편리한 UI를 제공합니다:

1. 충돌 파일을 열면 마커 위에 **Accept Current Change**, **Accept Incoming Change**, **Accept Both Changes** 버튼이 표시됨
2. 클릭 한 번으로 선택 가능
3. 더 복잡한 충돌은 **3-way 머지 에디터**에서 Incoming / Current / Result를 나란히 보면서 해결

```bash
# VS Code를 머지 도구로 설정
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# 머지 도구 실행
git mergetool
```

## 실습: 직접 해보기

```bash
# 1. 실습 저장소 준비
mkdir conflict-lab && cd conflict-lab
git init

# 2. 기본 파일 생성
cat > app.js << 'EOF'
function greet(name) {
    return "Hello, " + name;
}

function add(a, b) {
    return a + b;
}
EOF
git add . && git commit -m "초기 코드"

# 3. feature-template 브랜치: 템플릿 리터럴로 변경
git switch -c feature-template
sed -i '' 's/return "Hello, " + name;/return `Hello, ${name}!`;/' app.js
git add . && git commit -m "템플릿 리터럴 적용"

# 4. main: 같은 줄을 다른 방식으로 변경
git switch main
sed -i '' 's/return "Hello, " + name;/return "Hi, " + name + "!";/' app.js
git add . && git commit -m "인사말 변경"

# 5. 머지 시도 → 충돌!
git merge feature-template

# 6. 충돌 상태 확인
git status

# 7. 충돌 파일 확인 후 수동으로 해결
cat app.js
# 마커를 제거하고 원하는 내용으로 수정

# 8. 해결 후 완료
git add app.js
git commit -m "인사말 함수 충돌 해결: 템플릿 리터럴 + 새 인사말 적용"

# 9. 히스토리 확인
git log --oneline --graph --all
```

## 더 깊이 알아보기

### 충돌 해결의 역사

초기 버전 관리 시스템인 CVS(Concurrent Versions System, 1990년)에서는 충돌이 발생하면 전체 파일을 잠그는(lock) 방식을 사용했습니다. 한 사람이 파일을 편집하는 동안 다른 사람은 아예 수정할 수 없었죠. 이 방식은 안전하지만, 팀 생산성을 심각하게 떨어뜨렸습니다.

SVN(2000년)은 "낙관적 병합(optimistic merging)"을 도입해서 동시 편집을 허용했지만, 충돌 해결 경험이 그리 좋지 않았습니다. Git(2005년)은 여기서 한 발 더 나아가, 뛰어난 3-way 머지 알고리즘으로 대부분의 충돌을 자동 해결하고, 정말 사람이 판단해야 할 부분만 남겨둡니다.

> 💡 **알고 계셨나요?**: Git은 충돌 해결 시 "rerere(reuse recorded resolution)" 기능을 제공합니다. 한 번 해결한 충돌 패턴을 기억해두고, 같은 충돌이 다시 발생하면 자동으로 적용해줍니다. `git config --global rerere.enabled true`로 활성화할 수 있어요. 장기 브랜치를 주기적으로 머지하는 워크플로우에서 특히 유용합니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "충돌이 생기면 뭔가 잘못한 거다" — 충돌은 **정상적인 상황**입니다. 여러 사람이 같은 코드를 활발하게 개발하고 있다는 건강한 신호이기도 해요. 두려워하지 말고, 해결 방법을 익히는 게 중요합니다.

> ⚠️ **흔한 오해**: "충돌 마커를 그대로 커밋해도 된다" — 절대 안 됩니다! `<<<<<<<`, `=======`, `>>>>>>>`가 포함된 파일을 커밋하면 코드가 깨집니다. 해결 후에 마커가 전부 제거됐는지 반드시 확인하세요.

> 🔥 **실무 팁**: 충돌을 줄이는 가장 좋은 방법은 **자주 머지하는 것**입니다. main에서 오래 벗어나 있을수록 충돌이 커지고 복잡해집니다. 매일 또는 최소 2~3일에 한 번은 main의 변경 사항을 내 브랜치에 합쳐두세요. `git merge main`이나 `git rebase main`을 습관화하면 대형 충돌을 예방할 수 있습니다.

> 🔥 **실무 팁**: 충돌 해결에 자신이 없으면 `git merge --abort`로 안전하게 되돌릴 수 있습니다. 압박받지 말고, 천천히 상황을 파악한 후 다시 시도하세요.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| 충돌 원인 | 같은 파일의 같은 부분을 두 브랜치에서 동시에 수정 |
| `<<<<<<< HEAD` | 현재 브랜치(ours)의 변경 내용 시작 |
| `=======` | ours와 theirs의 구분선 |
| `>>>>>>> branch` | 병합 브랜치(theirs)의 변경 내용 끝 |
| 해결 순서 | 마커 제거 → 원하는 내용 작성 → `git add` → `git commit` |
| `git merge --abort` | 머지 자체를 취소하고 이전 상태로 복원 |
| `--ours` / `--theirs` | 한쪽 전체를 선택하는 빠른 해결법 |
| `diff3` 스타일 | 공통 조상까지 표시하여 충돌 이해도 향상 |

## 다음 섹션 미리보기

충돌을 해결할 수 있게 됐으니, 이제 브랜치를 **정리하고 관리하는 방법**을 배울 차례입니다. 다음 섹션 [브랜치 관리](./05-branch-management.md)에서는 불필요한 브랜치 삭제, 이름 변경, 브랜치 전략 등 실무에서 꼭 필요한 관리 기법을 다룹니다.

## 참고 자료

- [Pro Git Book — 충돌의 기초](https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B8%8C%EB%9E%9C%EC%B9%98%EC%99%80-Merge-%EC%9D%98-%EA%B8%B0%EC%B4%88) - 공식 충돌 해결 가이드
- [Git 공식 문서 — Advanced Merging](https://git-scm.com/book/en/v2/Git-Tools-Advanced-Merging) - rerere, 복잡한 충돌 해결 기법
- [GitHub Docs — 충돌 해결](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-using-the-command-line) - GitHub에서의 충돌 해결
- [VS Code — Merge Conflicts](https://code.visualstudio.com/docs/sourcecontrol/merge-conflicts) - VS Code의 충돌 해결 UI 사용법
