# 파일 상태 관리

> restore, checkout --, rm, mv — 파일 되돌리기와 이동

## 개요

코딩을 하다가 "아, 이 수정 다 취소하고 원래대로 돌리고 싶다!" 하는 순간, 한 번쯤 겪어보셨죠? 또는 파일 이름을 바꾸거나, 더 이상 필요 없는 파일을 삭제할 때도 Git에게 알려줘야 합니다. 이 섹션에서는 파일 수준의 되돌리기와 관리 명령어를 배웁니다.

**선수 지식**: [.gitignore](./01-gitignore.md)에서 배운 추적/비추적 파일 개념, [첫 번째 저장소](../01-git-start/03-first-repo.md)의 3단계 구조
**학습 목표**:
- `git restore`로 작업 디렉토리와 스테이징 영역의 변경을 되돌릴 수 있다
- `git rm`과 `git mv`로 파일을 삭제하고 이동할 수 있다
- `git restore`와 `git checkout --`의 차이를 이해한다

## 왜 알아야 할까?

실수는 누구나 합니다. 파일을 잘못 수정했거나, 실험적인 코드를 작성했다가 마음에 안 들거나, `git add`를 너무 일찍 해버렸거나. 이런 상황에서 **당황하지 않고 깔끔하게 되돌리는 능력**이 바로 Git을 제대로 쓰는 개발자와 그렇지 않은 개발자의 차이입니다.

## 핵심 개념

### git restore — 되돌리기의 새로운 표준

> 💡 **비유**: `git restore`는 **Ctrl+Z(실행 취소)의 Git 버전**입니다. 문서 편집기에서 실행 취소를 누르면 이전 상태로 돌아가듯, `git restore`는 파일을 이전 시점의 상태로 복원합니다.

`git restore`는 파일의 변경 사항을 되돌리는 명령어입니다. 두 가지 주요 용도가 있죠:

**1. 작업 디렉토리의 변경 되돌리기** (아직 `add` 안 한 변경)

```bash
# 특정 파일의 수정 내용 되돌리기 (마지막 커밋 상태로)
git restore README.md

# 여러 파일 한 번에 되돌리기
git restore app.js style.css

# 현재 디렉토리의 모든 변경 되돌리기
# ⚠️ 주의: 되돌리면 수정 내용이 사라집니다!
git restore .
```

**2. 스테이징 취소하기** (`add`한 것을 다시 내리기)

```bash
# 스테이징된 파일을 다시 unstage (변경 내용은 유지됨)
git restore --staged README.md

# 모든 스테이징 취소
git restore --staged .
```

이 두 가지를 정리하면:

| 명령어 | 효과 | 변경 내용 |
|--------|------|----------|
| `git restore <파일>` | 작업 디렉토리 변경 취소 | **사라짐** (복구 불가!) |
| `git restore --staged <파일>` | 스테이징만 취소 | 작업 디렉토리에 **유지됨** |

> ⚠️ **흔한 오해**: "`git restore --staged`는 변경 내용을 삭제한다" — 아닙니다! `--staged`는 파일을 스테이징 영역에서 내릴 뿐, 작업 디렉토리의 수정 내용은 그대로 보존됩니다. 안심하고 쓰세요.

두 옵션을 합치면 **스테이징과 작업 디렉토리 모두** 되돌릴 수도 있습니다:

```bash
# 스테이징도 취소하고, 작업 디렉토리 변경도 취소
# ⚠️ 주의: 수정 내용이 완전히 사라집니다!
git restore --staged --worktree README.md
```

#### 특정 커밋 시점으로 복원하기

`--source` 옵션을 사용하면 마지막 커밋이 아닌, **특정 시점의 파일**을 가져올 수 있습니다:

```bash
# 2커밋 전의 README.md로 복원
git restore --source HEAD~2 README.md

# 특정 커밋 해시의 파일로 복원
git restore --source a1b2c3d README.md

# 특정 브랜치의 파일로 복원
git restore --source main README.md
```

### git checkout -- (레거시 방식)

`git restore`가 등장하기 전에는 `git checkout --`가 같은 역할을 했습니다:

```bash
# 레거시: 작업 디렉토리 변경 되돌리기
git checkout -- README.md

# 레거시: 스테이징 취소
git reset HEAD README.md
```

`git checkout`은 **브랜치를 전환**할 때도, **파일을 되돌릴** 때도 사용하는 만능 명령어였는데, 이게 혼란의 원인이었죠. 그래서 Git 2.23(2019년)에서 역할이 분리되었습니다:

| 과거 (checkout 만능) | 현재 (역할 분리) |
|---------------------|-----------------|
| `git checkout <브랜치>` | `git switch <브랜치>` |
| `git checkout -- <파일>` | `git restore <파일>` |
| `git checkout HEAD -- <파일>` | `git restore --source HEAD <파일>` |

> 💡 **알고 계셨나요?**: `git switch`와 `git restore`는 베트남 출신 개발자 **Nguyễn Thái Ngọc Duy**가 설계했습니다. 그는 Git v2.16부터 v2.23까지 724개의 커밋을 기여한 최다 기여자였는데, "`git checkout`이 너무 많은 일을 하는 것이 사용자 혼란의 원인"이라는 문제를 해결하기 위해 이 두 명령어를 만들었습니다. 2024년에는 공식적으로 `git checkout`이 **폐기(deprecated)되지 않을 것**이라는 결정이 내려져, 두 방식 모두 계속 사용 가능합니다.

### git rm — 파일 삭제

Git이 추적 중인 파일을 삭제할 때는 단순히 파일을 지우는 것만으로는 부족합니다. Git에게도 "이 파일을 더 이상 추적하지 마"라고 알려줘야 하죠.

```bash
# 파일 삭제 + Git 추적에서 제거 (실제 파일도 삭제됨)
git rm old-file.txt

# Git 추적에서만 제거 (파일은 디스크에 남김)
# .gitignore 섹션에서 배운 그 명령어!
git rm --cached secret.env

# 디렉토리 전체 제거 (-r 필요)
git rm -r old-directory/
```

`git rm`과 그냥 `rm`의 차이:

```bash
# 방법 1: git rm (한 번에 처리)
git rm old-file.txt
git commit -m "불필요한 파일 삭제"

# 방법 2: rm + git add (두 단계)
rm old-file.txt
git add old-file.txt    # 삭제도 "변경"이므로 스테이징 필요
git commit -m "불필요한 파일 삭제"
```

두 방법의 결과는 동일하지만, `git rm`이 한 단계로 깔끔합니다.

### git mv — 파일 이동 및 이름 변경

> 💡 **비유**: `git mv`는 **이사 신고서**와 같습니다. 파일의 주소(경로)가 바뀌면 Git에게 "이 파일이 여기로 이사했어요"라고 정식으로 알려주는 거죠.

```bash
# 파일 이름 변경
git mv old-name.js new-name.js

# 파일을 다른 디렉토리로 이동
git mv app.js src/app.js

# 이동 + 이름 변경 동시에
git mv utils.js lib/helpers.js
```

사실 `git mv`는 내부적으로 세 가지 명령어의 조합입니다:

```bash
# git mv old.js new.js 는 사실 이것과 동일:
mv old.js new.js
git rm old.js
git add new.js
```

> 💡 **알고 계셨나요?**: Git은 파일 이동이나 이름 변경을 **명시적으로 기록하지 않습니다**. 대신 두 커밋 사이의 스냅샷을 비교해서, 내용이 비슷한 파일이 한 곳에서 사라지고 다른 곳에 나타나면 "이름이 변경되었구나!"라고 **추론**합니다. 기본 유사도 기준은 **50%** — 파일 내용의 절반 이상이 같으면 이름 변경으로 인식하죠. `git log --follow`를 쓰면 이름이 바뀐 파일의 이력도 추적할 수 있습니다.

## 실습: 직접 해보기

```bash
# 1. 실습용 저장소 준비
mkdir file-ops-practice && cd file-ops-practice
git init

# 2. 파일 만들고 커밋
echo "# 파일 관리 연습" > README.md
echo "console.log('hello');" > app.js
echo "body { margin: 0; }" > style.css
git add .
git commit -m "초기 파일 추가"

# 3. 파일 수정 후 restore로 되돌리기
echo "수정된 내용" >> README.md
git diff README.md          # 변경 확인
git restore README.md       # 되돌리기!
git diff README.md          # 변경 없음 확인

# 4. 스테이징 후 취소해보기
echo "새 내용" >> app.js
git add app.js
git status                  # staged 상태 확인
git restore --staged app.js # 스테이징 취소
git status                  # unstaged로 변경됨 (내용은 유지!)

# 5. 파일 이름 변경
git mv app.js main.js
git status                  # renamed 표시 확인

# 6. 커밋
git add .
git commit -m "app.js를 main.js로 이름 변경"

# 7. 이력 추적
git log --oneline --follow -- main.js
```

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`git restore`는 실험적(Experimental) 명령어라서 불안정하다" — 처음 도입된 Git 2.23(2019년)에서는 실험적 태그가 붙어 있었지만, 현재(Git 2.51 기준) 공식 문서에서 실험적 경고가 **제거**되었습니다. 안심하고 사용하세요.

> 🔥 **실무 팁**: `git restore .`은 **커밋되지 않은 모든 변경을 날려버리므로** 정말 조심해야 합니다. 실행 전에 반드시 `git diff`로 무엇이 사라질지 확인하세요. 변경을 임시로 보관하고 싶다면 [Stash](../09-history-internals/01-stash.md)를 사용하는 것이 더 안전합니다.

## 핵심 정리

| 명령어 | 설명 |
|--------|------|
| `git restore <파일>` | 작업 디렉토리 변경 되돌리기 (수정 내용 삭제) |
| `git restore --staged <파일>` | 스테이징 취소 (변경 내용은 유지) |
| `git restore --source <커밋> <파일>` | 특정 시점의 파일로 복원 |
| `git rm <파일>` | 파일 삭제 + 추적 해제 |
| `git rm --cached <파일>` | 추적만 해제 (파일은 유지) |
| `git mv <원본> <대상>` | 파일 이동/이름 변경 |

## 다음 섹션 미리보기

파일을 되돌리고 관리하는 법을 배웠으니, 이제 **커밋 히스토리를 깊이 있게 탐색하는 법**을 알아봅시다. 다음 섹션 [히스토리 탐색](./03-log-exploration.md)에서는 `git log`의 다양한 옵션, `git blame`으로 코드 작성자 추적, `git shortlog`로 기여자 통계를 확인하는 방법을 배웁니다.

## 참고 자료

- [Git 공식 문서 — git-restore](https://git-scm.com/docs/git-restore) - restore의 모든 옵션 레퍼런스
- [Pro Git Book — 되돌리기](https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-%EB%90%98%EB%8F%8C%EB%A6%AC%EA%B8%B0) - 파일 되돌리기 공식 가이드
- [Git 2.23 릴리스 노트 — switch와 restore](https://github.blog/open-source/git/highlights-from-git-2-23/) - 새 명령어 도입 배경
- [Git 공식 문서 — git-rm](https://git-scm.com/docs/git-rm) - rm 명령어의 모든 옵션
