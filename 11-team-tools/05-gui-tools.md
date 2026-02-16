# GUI 도구 활용

> VS Code, GitKraken, Fork 비교, CLI vs GUI 선택 가이드

## 개요

지금까지 이 튜토리얼에서는 주로 **CLI(터미널)**로 Git을 배웠습니다. CLI는 Git의 모든 기능에 접근할 수 있고, 자동화에 유리하죠. 하지만 커밋 그래프를 시각화하거나, 복잡한 merge 충돌을 해결하거나, 변경 사항을 빠르게 훑어볼 때는 **GUI 도구**가 훨씬 편리합니다. 이번 섹션에서는 대표적인 Git GUI 도구를 비교하고, CLI와 GUI를 **상황에 맞게 조합**하는 방법을 배웁니다.

**선수 지식**: 이 튜토리얼에서 배운 Git 명령어 전반
**학습 목표**:
- 주요 Git GUI 도구의 특징과 장단점을 이해한다
- VS Code의 Git 통합 기능을 활용할 수 있다
- CLI와 GUI를 상황에 맞게 조합하여 사용할 수 있다

## 왜 알아야 할까?

"진짜 개발자는 CLI만 쓴다" — 이런 말을 들어본 적 있나요? 사실 이건 **편견**입니다. 숙련된 개발자들도 상황에 따라 GUI를 적극 활용합니다. Linus Torvalds 조차 Git을 만들었지만 복잡한 merge에는 `meld` 같은 시각적 도구를 사용했어요.

중요한 건 CLI **또는** GUI가 아니라, CLI **와** GUI를 적재적소에 사용하는 것입니다.

## 핵심 개념

### 개념 1: CLI vs GUI — 언제 무엇을?

> 💡 **비유**: CLI와 GUI의 관계는 **요리에서 칼과 푸드 프로세서**의 관계와 같습니다. 칼(CLI)은 정밀하고 모든 상황에 사용할 수 있지만 숙련이 필요하고, 푸드 프로세서(GUI)는 특정 작업을 빠르고 쉽게 처리하지만 세밀한 조작은 어렵죠. 최고의 요리사는 둘 다 사용합니다.

**CLI가 더 좋은 상황**:

| 작업 | CLI 장점 |
|------|---------|
| 자동화 (스크립트, CI/CD) | 프로그래밍 가능 |
| 복잡한 명령 (rebase, filter-repo) | 전체 옵션 접근 |
| SSH 원격 서버 작업 | GUI 설치 불필요 |
| 반복 작업 (alias 활용) | 빠른 실행 |
| 배치 작업 (`git submodule foreach`) | 일괄 처리 |

**GUI가 더 좋은 상황**:

| 작업 | GUI 장점 |
|------|---------|
| 커밋 히스토리 탐색 | 그래프 시각화 |
| 복잡한 merge 충돌 해결 | 3-way diff 뷰 |
| 부분 스테이징 (hunk 단위) | 클릭으로 선택 |
| 브랜치 관계 파악 | 시각적 다이어그램 |
| diff 확인 | 색상 하이라이팅, 인라인 비교 |
| Git 초보자 학습 | 직관적 인터페이스 |

### 개념 2: VS Code — 개발 환경과 Git의 통합

VS Code는 **별도 설치 없이** Git 기능이 내장되어 있어 가장 많이 사용되는 Git GUI입니다. 에디터에서 코드를 수정하면서 바로 커밋, 푸시, 브랜치 전환이 가능하죠.

**내장 Git 기능**:

| 기능 | 사용법 |
|------|--------|
| **Source Control 패널** | `Ctrl+Shift+G` (macOS: `⌘+Shift+G`) |
| **변경 파일 보기** | Source Control → 변경된 파일 클릭 |
| **부분 스테이징** | diff 뷰에서 줄 선택 → "Stage Selected Ranges" |
| **인라인 diff** | 에디터에서 변경 줄 옆 색상 표시 클릭 |
| **브랜치 전환** | 하단 상태 바의 브랜치 이름 클릭 |
| **충돌 해결** | 충돌 마커에서 "Accept Current/Incoming/Both" 클릭 |
| **통합 터미널** | `` Ctrl+` `` 으로 CLI와 즉시 전환 |

**핵심 확장(Extensions)**:

| 확장 | 기능 | 설치 수 |
|------|------|---------|
| **GitLens** | blame, 파일 히스토리, 커밋 그래프, AI 커밋 메시지 | 3,000만+ |
| **Git Graph** | 커밋 그래프 시각화, 브랜치 관계 | 1,200만+ |
| **Conventional Commits** | 커밋 메시지 타입 선택 드롭다운 | 100만+ |
| **Git History** | 파일/줄 단위 히스토리 탐색 | 800만+ |

```bash
# VS Code에서 추천 확장 설치
code --install-extension eamodio.gitlens
code --install-extension mhutchie.git-graph
```

**GitLens의 핵심 기능**:

- **Inline Blame**: 각 줄 옆에 마지막 수정자와 커밋 메시지 표시
- **File History**: 파일의 전체 변경 이력을 시각적으로 탐색
- **Commit Graph**: GitKraken 수준의 커밋 그래프 (Pro 기능)
- **Compare**: 브랜치, 태그, 커밋 간 비교

> 🔥 **실무 팁**: VS Code의 **"Source Control → ··· → Views → Source Control Repositories"**를 활성화하면, 여러 저장소를 하나의 창에서 관리할 수 있습니다. [모노레포](./04-monorepo-submodule.md)나 서브모듈 프로젝트에서 특히 유용합니다.

### 개념 3: GitKraken — 시각적 Git 전문 도구

GitKraken은 Git **전용** GUI 도구로, 특히 **커밋 그래프의 시각화**가 뛰어납니다.

**주요 특징**:

| 기능 | 설명 |
|------|------|
| **커밋 그래프** | 컬러풀한 브랜치 시각화, 드래그&드롭 머지 |
| **내장 머지 도구** | 3-way diff로 충돌 해결 |
| **Gitflow 지원** | 버튼 클릭으로 Git Flow 실행 |
| **GitHub/GitLab 연동** | PR 생성/리뷰를 앱 내에서 수행 |
| **AI 기능** | AI 머지 충돌 해결, AI 커밋 메시지 생성 |
| **팀 기능** | 공유 워크스페이스, 팀 활동 피드, DORA 인사이트 |
| **프로파일** | 여러 Git 계정 간 전환 |

**최신 버전**: v11.9.0 (2026년 2월) — AI 모델 선택, ARM 리눅스/Windows 지원 추가
**플랫폼**: macOS, Windows, Linux
**가격**: 무료 (공개 저장소), Pro $4.95/월 (비공개 저장소, AI 기능)

**GitKraken이 특히 유용한 상황**:

- 복잡한 브랜치 관계를 시각적으로 파악할 때
- [Interactive Rebase](../08-advanced-branch/02-interactive-rebase.md)를 시각적으로 수행할 때
- Git Flow를 GUI로 관리할 때 ([워크플로우 전략](../08-advanced-branch/04-workflow-strategies.md) 참고)

### 개념 4: Fork — 가볍고 빠른 Git 클라이언트

Fork는 **macOS와 Windows** 전용 Git 클라이언트로, 가벼우면서도 강력한 기능을 제공합니다.

**주요 특징**:

| 기능 | 설명 |
|------|------|
| **빠른 성능** | 네이티브 앱으로 반응 속도 빠름 |
| **Interactive Rebase** | 드래그&드롭으로 커밋 재정렬 |
| **이미지 diff** | 이미지 파일 변경을 시각적으로 비교 |
| **Git LFS 지원** | 대용량 파일 관리 |
| **다중 탭** | 여러 저장소를 탭으로 전환 |
| **내장 머지 도구** | 충돌 해결용 3-way 병합 |

**최신 업데이트** (2026년 1월): Claude Code Review 연동, AI 커밋 메시지 생성, 다중 소스코드 디렉토리 지원
**플랫폼**: macOS, Windows (Linux 미지원)
**가격**: 무료 평가 후 $49.99 (일회성 구매, 1년 업데이트 포함)

### 개념 5: 기타 도구 비교

| 도구 | 플랫폼 | 가격 | 특징 |
|------|--------|------|------|
| **Sourcetree** | macOS, Windows | 무료 | Atlassian 제작, Jira 연동 |
| **GitHub Desktop** | macOS, Windows | 무료 | GitHub 최적화, 초보자 친화적 |
| **Tower** | macOS, Windows | $69/년 | 전문가용, 빠른 성능 |
| **Sublime Merge** | macOS, Windows, Linux | $99 | 초고속 성능, Sublime Text 연동 |
| **lazygit** | 모든 OS (터미널) | 무료 | 터미널 UI, CLI 사용자에게 인기 |

**lazygit — CLI 사용자를 위한 터미널 GUI**:

```bash
# lazygit 설치
# macOS
brew install lazygit

# Windows
winget install lazygit

# Linux
sudo apt install lazygit
```

```bash
# 실행
lazygit
```

lazygit은 터미널 안에서 동작하는 **TUI(Terminal User Interface)**입니다. CLI의 장점(터미널 환경, SSH 사용 가능, 키보드 중심)과 GUI의 장점(시각적 상태 확인, 빠른 탐색)을 모두 갖추고 있어서, CLI에 익숙하지만 시각적 보조가 필요한 개발자에게 인기가 많습니다.

### 도구 선택 가이드

어떤 도구를 선택할지 고민된다면, 이 흐름을 따라가보세요:

**이미 VS Code를 사용 중인가?** → GitLens + Git Graph 확장 설치 (추가 도구 불필요)

**시각적 커밋 그래프가 중요한가?** → GitKraken (가장 아름다운 그래프)

**가볍고 빠른 전용 앱이 필요한가?** → Fork (macOS/Windows)

**터미널을 벗어나고 싶지 않은가?** → lazygit (TUI)

**GitHub에 최적화된 간단한 앱이 필요한가?** → GitHub Desktop

**Jira를 쓰는 팀인가?** → Sourcetree (Atlassian 생태계)

> ⚠️ **흔한 오해**: "GUI를 쓰면 Git을 제대로 배울 수 없다" — 오히려 반대일 수 있습니다! GUI의 커밋 그래프를 보면서 `merge`, `rebase`, `cherry-pick`이 실제로 어떤 변화를 만드는지 **시각적으로 확인**할 수 있거든요. 다만, GUI**만** 사용하면 자동화 스크립트를 작성하거나 서버 환경에서 작업할 수 없으니, **CLI 기본기는 반드시** 익혀두세요.

## 실습: VS Code에서 Git 활용하기

```bash
# 1. 실습 저장소 열기 (또는 기존 프로젝트)
mkdir vscode-git-demo && cd vscode-git-demo
git init
echo "# VS Code Git Demo" > README.md
git add . && git commit -m "chore: 프로젝트 초기화"

# 2. VS Code에서 열기
code .
```

**VS Code 안에서 따라하기**:

1. **Source Control 패널 열기**: `Ctrl+Shift+G` (macOS: `⌘+Shift+G`)
2. **파일 수정**: README.md에 내용 추가
3. **diff 확인**: Source Control에서 변경된 파일 클릭
4. **부분 스테이징**: diff 뷰에서 특정 줄만 선택 → 우클릭 → "Stage Selected Ranges"
5. **커밋**: 메시지 입력 → `Ctrl+Enter` (macOS: `⌘+Enter`)
6. **브랜치 전환**: 하단 상태 바에서 브랜치 이름 클릭 → 새 브랜치 생성
7. **통합 터미널**: `` Ctrl+` `` 으로 CLI 명령도 즉시 실행

```bash
# VS Code 터미널에서 CLI와 혼용
git log --oneline --graph -10
```

## 더 깊이 알아보기

### Git GUI의 역사

Git 초기에는 CLI만 존재했습니다. Linus Torvalds가 만든 Git은 **리눅스 커널 개발자**를 위한 도구였으니 당연히 CLI 중심이었죠. 하지만 Git이 대중화되면서 2006년에 **gitk**(Tcl/Tk 기반)와 **git-gui**가 Git에 번들로 포함되었습니다.

```bash
# Git에 기본 포함된 GUI 도구들 (아직도 존재!)
gitk           # 커밋 히스토리 뷰어
git gui        # 기본 GUI (스테이징, 커밋)
```

이후 2010년대에 Sourcetree(2011), GitKraken(2016), Fork(2017) 같은 전문 GUI 도구가 등장하면서 Git GUI 생태계가 풍성해졌습니다. 그리고 VS Code(2015)가 에디터 내장 Git 기능으로 시장을 평정하면서, 현재는 **"에디터 통합 + 전문 GUI + CLI"**를 필요에 따라 조합하는 것이 표준이 되었습니다.

> 💡 **알고 계셨나요?**: VS Code의 GitLens 확장은 원래 개인 프로젝트였는데, 너무 인기가 높아져 GitKraken을 만든 Axosoft가 2021년에 인수했습니다. 그래서 현재 GitLens의 Pro 기능과 GitKraken의 일부 기능이 통합되고 있어요. 하나의 라이선스로 두 도구를 모두 사용할 수 있습니다.

## 흔한 오해와 팁

> 🔥 **실무 팁**: Git 설정에서 **기본 에디터와 diff 도구**를 GUI로 설정해두면 편리합니다:

```bash
# VS Code를 기본 에디터로 설정
git config --global core.editor "code --wait"

# VS Code를 diff 도구로 설정
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'

# VS Code를 merge 도구로 설정
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

> 🔥 **실무 팁**: **여러 도구를 조합하세요!** 많은 개발자의 워크플로우를 보면: 일상적인 add/commit/push는 **VS Code**에서, 복잡한 히스토리 탐색이나 rebase는 **GitKraken** 또는 **Fork**에서, 자동화 스크립트나 CI/CD는 **CLI**에서 수행합니다. 하나의 도구에 얽매일 필요가 없어요.

## 핵심 정리

| 도구 | 최적의 사용 상황 | 가격 |
|------|-----------------|------|
| **VS Code + GitLens** | 개발 중 즉시 Git 사용, 가장 범용적 | 무료 (Pro 옵션) |
| **GitKraken** | 시각적 그래프, Git Flow, 팀 협업 | 무료/Pro $4.95/월 |
| **Fork** | 가볍고 빠른 전용 클라이언트 | $49.99 |
| **lazygit** | 터미널 환경, SSH 서버, CLI 선호자 | 무료 |
| **GitHub Desktop** | GitHub 중심, 초보자 | 무료 |
| **Sourcetree** | Jira/Bitbucket 연동, 무료 | 무료 |

| 상황 | 추천 |
|------|------|
| 일상 커밋, 푸시, 브랜치 전환 | VS Code Source Control |
| 커밋 그래프 탐색 | GitKraken, Git Graph 확장 |
| 복잡한 충돌 해결 | VS Code 내장 머지, Fork |
| Interactive Rebase | GitKraken, Fork |
| 자동화 스크립트 | CLI |
| 원격 서버 작업 | CLI, lazygit |

## 다음 섹션 미리보기

Ch11에서 팀으로 Git을 효과적으로 사용하기 위한 모든 것 — 네이밍 규칙, 커밋 컨벤션, 리뷰 문화, 저장소 전략, GUI 도구 — 을 배웠습니다! 다음 챕터 [Ch12. 트러블슈팅과 생태계](../12-troubleshooting/01-common-mistakes.md)에서는 Git을 사용하면서 겪는 **흔한 실수와 해결법**, 대규모 저장소 관리, 보안, 그리고 Git 생태계 전체를 조망합니다. 실무에서 만날 수 있는 모든 문제 상황에 대비해봅시다!

## 참고 자료

- [VS Code — Version Control](https://code.visualstudio.com/docs/sourcecontrol/overview) - VS Code 공식 Git 기능 문서
- [GitLens Extension](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) - GitLens 마켓플레이스
- [GitKraken](https://www.gitkraken.com/) - GitKraken 공식 사이트
- [Fork](https://git-fork.com/) - Fork 공식 사이트
- [lazygit](https://github.com/jesseduffield/lazygit) - lazygit GitHub 저장소
- [GitHub Desktop](https://desktop.github.com/) - GitHub Desktop 공식 사이트
