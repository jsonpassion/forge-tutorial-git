# .gitignore

> 추적 제외 패턴, 글로벌 설정, 이미 추적 중인 파일 제거

## 개요

프로젝트를 하다 보면 Git이 추적하면 **안 되는** 파일이 반드시 생깁니다. `node_modules/`, `.env`, `.DS_Store` 같은 것들이죠. 이런 파일이 커밋에 섞여 들어가면 저장소가 비대해지거나, 심각한 경우 비밀 키가 유출될 수 있습니다. `.gitignore` 파일은 이런 걱정을 덜어주는 Git의 필수 도구입니다.

**선수 지식**: [커밋의 기본](../01-git-start/04-commit-basics.md)에서 배운 `git add`, `git status`
**학습 목표**:
- `.gitignore` 파일의 역할과 패턴 문법을 이해한다
- 글로벌 `.gitignore`와 로컬 설정의 차이를 안다
- 이미 추적 중인 파일을 Git에서 제거하는 방법을 익힌다

## 왜 알아야 할까?

[커밋의 기본](../01-git-start/04-commit-basics.md)에서 `git add .`으로 모든 파일을 한 번에 스테이징하는 법을 배웠죠? 편리하지만, 이 명령은 **모든 파일**을 가리지 않고 담아버립니다. API 키가 담긴 `.env` 파일이나, 수백 MB짜리 `node_modules/` 폴더가 커밋에 들어가면 큰일이죠. `.gitignore`는 `git add .`을 안심하고 쓸 수 있게 해주는 **안전망**입니다.

## 핵심 개념

### .gitignore란?

> 💡 **비유**: `.gitignore`는 **택배 기사에게 붙여두는 메모**와 같습니다. "이 방은 무시해주세요", "이 상자는 포장하지 마세요"라고 적어두면, 택배 기사(Git)는 해당 물건을 건드리지 않고 넘어가죠.

`.gitignore`는 프로젝트 루트(또는 하위 디렉토리)에 놓는 **평문 텍스트 파일**입니다. 이 파일에 적힌 패턴과 일치하는 파일은 Git이 무시합니다 — `git status`에도 나타나지 않고, `git add .`에도 포함되지 않습니다.

```bash
# 프로젝트 루트에 .gitignore 파일 만들기
echo "node_modules/" > .gitignore
echo ".env" >> .gitignore
echo "*.log" >> .gitignore

# 확인
cat .gitignore
```

```output
node_modules/
.env
*.log
```

### 패턴 문법 — 무엇을 무시할지 정하기

> 💡 **비유**: `.gitignore`의 패턴은 **검색 필터**와 비슷합니다. "*.jpg"라고 쓰면 모든 JPG 파일을 한 번에 걸러내듯, `.gitignore`도 특정 규칙에 맞는 파일을 자동으로 걸러냅니다.

`.gitignore`는 유닉스의 **glob 패턴**을 사용합니다. 주요 문법을 정리하면:

| 패턴 | 의미 | 예시 |
|------|------|------|
| `*` | 슬래시를 제외한 모든 문자열 | `*.log` → 모든 .log 파일 |
| `?` | 슬래시를 제외한 문자 1개 | `?.txt` → a.txt, b.txt |
| `[abc]` | 대괄호 안의 문자 중 하나 | `[Mm]akefile` |
| `**` | 모든 하위 디렉토리 | `**/test` → 어디서든 test 폴더 |
| `/` (앞) | 루트 기준 경로 고정 | `/build` → 루트의 build만 |
| `/` (뒤) | 디렉토리만 매칭 | `logs/` → logs 디렉토리 |
| `!` | 패턴 부정 (예외 처리) | `!important.log` |
| `#` | 주석 | `# 이 줄은 주석` |

실제 프로젝트에서 자주 쓰는 패턴을 살펴볼까요?

```bash
# ===== 기본 패턴 =====
# 특정 파일
.env
.DS_Store
Thumbs.db

# 특정 확장자
*.log
*.tmp
*.swp

# 특정 디렉토리
node_modules/
dist/
build/
__pycache__/

# ===== 고급 패턴 =====
# 모든 하위 디렉토리의 .class 파일
**/*.class

# 루트의 TODO만 무시 (하위 디렉토리의 TODO는 추적)
/TODO

# logs 디렉토리는 무시하되, logs/important.log는 추적
logs/
!logs/important.log

# 특정 디렉토리 내 모든 파일 무시하되, 디렉토리 구조는 유지
temp/*
!temp/.gitkeep
```

#### 패턴 부정(!)의 주의점

예외 처리(`!`)를 쓸 때 한 가지 함정이 있습니다. **부모 디렉토리가 통째로 무시되면, 자식 파일의 예외가 작동하지 않습니다**:

```bash
# ❌ 이렇게 하면 예외가 작동하지 않음
myDir
!myDir/keep-this.txt

# ✅ 이렇게 해야 예외가 작동함
myDir/*
!myDir/keep-this.txt
```

`myDir`을 통째로 무시하면 Git이 디렉토리 안을 아예 들여다보지 않기 때문이죠. `myDir/*`로 디렉토리 **내부의 파일들**을 무시하면, 특정 파일을 예외로 살릴 수 있습니다.

### 무시 설정의 세 가지 레벨

Git에서 파일을 무시하는 방법은 하나만 있는 게 아닙니다. 상황에 따라 세 가지 레벨을 선택할 수 있어요:

| 레벨 | 파일 위치 | 공유 여부 | 용도 |
|------|----------|----------|------|
| 프로젝트 | `.gitignore` | 팀원과 공유됨 | 프로젝트 공통 무시 파일 (node_modules, build 등) |
| 로컬 전용 | `.git/info/exclude` | 나만 사용 | 이 저장소에서 나만 쓰는 무시 설정 |
| 글로벌 | `~/.gitignore_global` | 나만 사용 | 모든 저장소에 적용할 무시 설정 |

#### 글로벌 .gitignore 설정하기

OS별 자동 생성 파일(`.DS_Store`, `Thumbs.db` 등)은 모든 저장소에서 무시해야 하니까, 글로벌 설정이 딱입니다:

```bash
# 글로벌 gitignore 파일 만들기
touch ~/.gitignore_global

# Git에 등록
git config --global core.excludesFile ~/.gitignore_global
```

```bash
# 글로벌 gitignore에 OS/에디터 파일 추가
cat > ~/.gitignore_global << 'EOF'
# macOS
.DS_Store
.AppleDouble
.LSOverride

# Windows
Thumbs.db
ehthumbs.db
Desktop.ini

# Linux
*~
.directory

# 에디터
*.swp
*.swo
.idea/
.vscode/
*.sublime-workspace
EOF
```

> 🔥 **실무 팁**: 글로벌 `.gitignore`에 OS/에디터 관련 파일을 설정하면, 프로젝트의 `.gitignore`에서 이런 항목을 빼고 **프로젝트 고유 무시 규칙**에만 집중할 수 있습니다. 팀원 각자가 글로벌 설정을 하면 프로젝트 `.gitignore`가 깔끔해지죠.

### GitHub의 .gitignore 템플릿

매번 `.gitignore`를 처음부터 작성할 필요는 없습니다. GitHub은 **언어별, 프레임워크별 템플릿**을 제공합니다:

```bash
# GitHub에서 저장소를 만들 때 .gitignore 템플릿을 선택할 수 있음
# 또는 github/gitignore 저장소에서 직접 가져오기
# https://github.com/github/gitignore
```

자주 쓰이는 템플릿 예시:

| 언어/프레임워크 | 무시하는 대표 항목 |
|----------------|-------------------|
| Node.js | `node_modules/`, `dist/`, `.env` |
| Python | `__pycache__/`, `*.pyc`, `venv/`, `.egg-info/` |
| Java | `*.class`, `*.jar`, `target/`, `build/` |
| Swift/Xcode | `build/`, `DerivedData/`, `*.xcuserstate` |
| Unity | `Library/`, `Temp/`, `*.csproj` |

> 💡 **알고 계셨나요?**: GitHub의 `github/gitignore` 저장소에는 150개 이상의 템플릿이 있으며, 오픈소스 커뮤니티가 지속적으로 관리합니다. 새 프로젝트를 시작할 때 여기서 자신의 기술 스택에 맞는 템플릿을 가져오면 시간을 크게 아낄 수 있어요.

### 이미 추적 중인 파일 제거하기

여기가 가장 **혼동되는 부분**입니다. `.gitignore`에 파일을 추가해도, **이미 Git이 추적 중인 파일은 계속 추적됩니다**. `.gitignore`는 아직 추적되지 않는(Untracked) 파일에만 적용되거든요.

이미 커밋된 파일을 무시하려면 두 단계가 필요합니다:

```bash
# 1단계: Git의 추적에서 제거 (파일 자체는 삭제하지 않음!)
git rm --cached .env

# 2단계: .gitignore에 추가
echo ".env" >> .gitignore

# 3단계: 변경 사항 커밋
git add .gitignore
git commit -m "환경 변수 파일을 버전 관리에서 제거"
```

> ⚠️ **흔한 오해**: "`git rm --cached`는 파일을 삭제한다" — 아닙니다! `--cached` 옵션을 붙이면 **Git의 인덱스(추적 목록)에서만 제거**되고, 실제 파일은 디스크에 그대로 남아 있습니다. `--cached` 없이 `git rm`만 쓰면 파일이 정말 삭제되니 주의하세요.

디렉토리 전체를 제거할 때는 `-r` 옵션을 추가합니다:

```bash
# 디렉토리 전체를 추적에서 제거
git rm -r --cached node_modules/
```

### 디버깅 — 왜 이 파일이 무시되는 거지?

`.gitignore` 규칙이 복잡해지면, 어떤 규칙 때문에 특정 파일이 무시되는지 헷갈릴 수 있습니다. `git check-ignore`가 이 문제를 해결해 줍니다:

```bash
# 특정 파일이 무시되는 이유 확인
git check-ignore -v debug.log
```

```output
.gitignore:3:*.log    debug.log
```

이 출력은 `.gitignore` 파일의 **3번째 줄**에 있는 `*.log` 패턴 때문에 `debug.log`가 무시되고 있다고 알려줍니다.

## 실습: 직접 해보기

```bash
# 1. 실습용 저장소 만들기
mkdir ignore-practice && cd ignore-practice
git init

# 2. 여러 파일 만들기
echo "console.log('app');" > app.js
echo "SECRET_KEY=abc123" > .env
echo "에러 로그..." > error.log
mkdir node_modules
echo "{}" > node_modules/package.json

# 3. git status로 모든 파일 확인
git status

# 4. .gitignore 만들기
cat > .gitignore << 'EOF'
.env
*.log
node_modules/
EOF

# 5. 다시 상태 확인 — .env, error.log, node_modules가 사라짐!
git status

# 6. .gitignore도 함께 커밋
git add .
git commit -m "프로젝트 초기화: .gitignore 설정"

# 7. check-ignore 테스트
git check-ignore -v .env error.log app.js
```

```output
.gitignore:1:.env     .env
.gitignore:2:*.log    error.log
```

`app.js`는 무시 대상이 아니므로 출력되지 않습니다.

## 더 깊이 알아보기

### 무시 파일의 역사 — CVS에서 Git까지

파일을 무시하는 개념은 Git이 처음 만든 게 아닙니다. 버전 관리의 역사를 따라가 보면 흥미로운 진화를 볼 수 있어요:

- **CVS** (1990년대): `.cvsignore` 파일로 무시 패턴을 관리했습니다
- **Subversion** (2000년대): 파일이 아닌 `svn:ignore` **속성**으로 무시를 설정했는데, 이 방식은 디렉토리마다 따로 설정해야 해서 불편했습니다
- **Git** (2005년~): `.gitignore` 파일로 돌아왔지만, 여기에 **glob 패턴과 `**` 재귀 매칭**이라는 강력한 기능을 추가했죠

참고로 glob이라는 용어 자체가 1971년 Bell Labs의 유닉스에서 비롯되었습니다. Thompson과 Ritchie의 Unix Programmer's Manual에 이미 `*`과 `?` 와일드카드가 등장하거든요. 50년 넘는 역사를 가진 셈이죠!

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "`.gitignore`에 추가하면 Git 히스토리에서도 사라진다" — 아닙니다! `.gitignore`는 **앞으로의** 추적만 막습니다. 이미 커밋된 파일은 히스토리에 영원히 남아 있어요. 민감한 정보가 커밋된 경우, [히스토리 재작성](../09-history-internals/04-history-rewrite.md)으로 완전히 제거해야 합니다.

> 🔥 **실무 팁**: `.gitignore`만으로 보안에 의존하지 마세요. 실수로 `.env`를 커밋했다면 이미 히스토리에 남아 있습니다. `git-secrets`나 `gitleaks` 같은 도구로 커밋 전에 **민감 정보를 자동 스캔**하는 것이 안전합니다. 자세한 내용은 [보안](../12-troubleshooting/04-security.md)에서 다룹니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| `.gitignore` | 프로젝트별 무시 규칙 (팀과 공유됨) |
| `.git/info/exclude` | 로컬 전용 무시 규칙 (나만 사용) |
| `core.excludesFile` | 글로벌 무시 규칙 (모든 저장소에 적용) |
| `git rm --cached` | 추적 중인 파일을 인덱스에서만 제거 (파일은 유지) |
| `git check-ignore -v` | 파일이 어떤 규칙에 의해 무시되는지 확인 |
| `!패턴` | 무시 규칙의 예외 (특정 파일만 추적) |

## 다음 섹션 미리보기

`.gitignore`로 불필요한 파일을 걸러내는 법을 배웠습니다. 다음 섹션 [파일 상태 관리](./02-file-operations.md)에서는 `git restore`로 수정을 되돌리고, `git rm`과 `git mv`로 파일을 삭제하거나 이동하는 방법을 배웁니다.

## 참고 자료

- [Pro Git Book — .gitignore](https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-%EC%88%98%EC%A0%95%ED%95%98%EA%B3%A0-%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0#_ignoring) - .gitignore 공식 가이드
- [Git 공식 문서 — gitignore](https://git-scm.com/docs/gitignore) - 패턴 문법의 모든 규칙
- [github/gitignore](https://github.com/github/gitignore) - 150개 이상의 언어/프레임워크별 .gitignore 템플릿
- [Atlassian Git Tutorial — .gitignore](https://www.atlassian.com/git/tutorials/saving-changes/gitignore) - 실용적인 예제와 시각적 설명
