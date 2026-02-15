# Markdown 작성법

> GitHub Flavored Markdown, README 작성, 문서화 전략

## 개요

GitHub에서 README, 이슈, PR, 위키, 댓글 — 거의 모든 텍스트를 **마크다운(Markdown)**으로 작성합니다. 마크다운을 잘 쓰면 문서가 깔끔하고 전문적으로 보이지만, 문법을 모르면 서식이 엉망이 되기 쉽죠. 이번 섹션에서는 GitHub에서 사용되는 **GitHub Flavored Markdown(GFM)**의 핵심 문법과, 효과적인 문서 작성 전략을 배웁니다.

**선수 지식**: [GitHub CLI (gh)](./03-github-cli.md)에서 이슈와 PR 생성 경험
**학습 목표**:
- GitHub Flavored Markdown의 핵심 문법을 이해하고 사용할 수 있다
- README를 체계적으로 작성할 수 있다
- 이슈와 PR에서 마크다운을 효과적으로 활용할 수 있다
- GitHub 전용 마크다운 기능(Alert, Mermaid 등)을 활용할 수 있다

## 왜 알아야 할까?

개발자의 코드는 컴퓨터가 읽지만, **문서는 사람이 읽습니다**. 아무리 뛰어난 코드라도 README가 없거나 이슈 설명이 "안 됨"뿐이면, 동료가 이해하기 어렵습니다. 마크다운은 배우는 데 10분이 걸리지만, 한 번 익히면 **개발자 인생 전체에서** 사용하는 핵심 기술이에요.

## 핵심 개념

### 개념 1: 마크다운 기초 문법

> 💡 **비유**: 마크다운은 **간단한 신호 체계**와 같습니다. 교통 신호가 빨간불(정지), 초록불(진행)처럼 몇 가지 기호로 의미를 전달하듯, 마크다운도 `#`(제목), `**`(굵게), `-`(목록) 같은 몇 가지 기호로 문서의 구조를 표현합니다.

**제목(Heading)**:

```markdown
# 제목 1 (가장 큰 제목)
## 제목 2
### 제목 3
```

**텍스트 강조**:

```markdown
**굵게(Bold)**
*기울임(Italic)*
~~취소선(Strikethrough)~~
`인라인 코드(Inline Code)`
```

**목록(List)**:

```markdown
- 순서 없는 목록 항목 1
- 순서 없는 목록 항목 2
  - 들여쓰기 항목

1. 순서 있는 목록 항목 1
2. 순서 있는 목록 항목 2
```

**링크와 이미지**:

```markdown
[링크 텍스트](https://example.com)
![이미지 설명](image.png)
```

**인용(Blockquote)**:

```markdown
> 인용 블록입니다.
> 여러 줄도 가능합니다.
```

> 🔥 **실무 팁**: 마크다운에서 **줄바꿈**은 직관과 다릅니다. 그냥 엔터 한 번은 같은 문단으로 이어지고, **빈 줄 하나**를 넣어야 새 문단이 됩니다. 또는 줄 끝에 **공백 2개**를 넣으면 같은 문단 내에서 줄바꿈이 됩니다.

### 개념 2: 코드 블록 — 개발자의 핵심 도구

> 💡 **비유**: 코드 블록은 **게시판의 코드 전용 칸**입니다. 일반 텍스트와 시각적으로 분리되어, 코드가 코드답게 보이도록 해줍니다.

**인라인 코드**: 문장 중간에 코드를 넣을 때 백틱 하나로 감쌉니다.

```markdown
`git status` 명령어로 현재 상태를 확인하세요.
```

**코드 블록**: 백틱 세 개로 감싸고, 언어를 지정하면 **구문 하이라이팅**이 적용됩니다.

````markdown
```python
def hello():
    print("Hello, World!")
```
````

GitHub에서 지원하는 주요 언어 태그:

| 태그 | 언어 |
|------|------|
| `bash`, `shell` | Bash/Shell |
| `python`, `py` | Python |
| `javascript`, `js` | JavaScript |
| `typescript`, `ts` | TypeScript |
| `json` | JSON |
| `yaml`, `yml` | YAML |
| `markdown`, `md` | Markdown |
| `swift` | Swift |
| `go` | Go |
| `java` | Java |
| `diff` | Diff (변경 사항) |

**diff 블록**은 코드 변경 사항을 시각적으로 보여줄 때 유용합니다:

````markdown
```diff
- const name = "old";
+ const name = "new";
```
````

### 개념 3: 테이블과 체크리스트

**테이블**: 파이프(`|`)와 하이픈(`-`)으로 만듭니다.

```markdown
| 명령어 | 설명 | 위험도 |
|--------|------|--------|
| `git pull` | 원격 변경 가져오기 | 낮음 |
| `git reset --hard` | 모든 변경 삭제 | 높음 |
```

**열 정렬**:

```markdown
| 왼쪽 정렬 | 가운데 정렬 | 오른쪽 정렬 |
|:----------|:----------:|----------:|
| 텍스트 | 텍스트 | 텍스트 |
```

**체크리스트(Task List)**: 이슈와 PR에서 진행 상황을 추적할 때 아주 유용합니다.

```markdown
- [x] 로그인 기능 구현
- [x] 테스트 코드 작성
- [ ] 코드 리뷰 반영
- [ ] 배포
```

> 💡 **알고 계셨나요?**: GitHub 이슈에서 체크리스트를 사용하면, 이슈 목록에 **진행률 바**가 자동으로 표시됩니다. "2/4 tasks" 같은 형태로요. 프로젝트 진행 상황을 한눈에 파악할 수 있어서 팀 협업에서 정말 유용합니다.

### 개념 4: GitHub 전용 마크다운 기능

> 💡 **비유**: GitHub Flavored Markdown(GFM)은 **일반 마크다운에 GitHub 특제 소스를 얹은 것**입니다. 기본 마크다운 문법에 GitHub에서만 동작하는 특별한 기능들이 추가되어 있어요.

#### Alert (알림 블록)

GitHub은 2023년부터 **Alert 문법**을 지원합니다. 중요한 정보를 시각적으로 강조할 때 사용합니다:

```markdown
> [!NOTE]
> 참고할 만한 일반 정보입니다.

> [!TIP]
> 더 나은 방법을 제안할 때 사용합니다.

> [!IMPORTANT]
> 반드시 알아야 할 핵심 정보입니다.

> [!WARNING]
> 주의가 필요한 잠재적 문제입니다.

> [!CAUTION]
> 위험한 작업에 대한 경고입니다.
```

5가지 Alert 타입:

| 타입 | 용도 | 색상 |
|------|------|------|
| `[!NOTE]` | 일반 참고 정보 | 파란색 |
| `[!TIP]` | 유용한 팁 | 초록색 |
| `[!IMPORTANT]` | 핵심 정보 | 보라색 |
| `[!WARNING]` | 주의 사항 | 노란색 |
| `[!CAUTION]` | 위험 경고 | 빨간색 |

#### 자동 링크

GitHub은 특정 패턴을 **자동으로 링크**로 변환합니다:

| 패턴 | 예시 | 변환 결과 |
|------|------|-----------|
| 이슈/PR 번호 | `#123` | 해당 이슈/PR 링크 |
| 사용자 멘션 | `@octocat` | 해당 사용자 프로필 링크 |
| 커밋 SHA | `a1b2c3d` | 해당 커밋 링크 |
| URL | `https://...` | 클릭 가능한 링크 |
| 다른 저장소 이슈 | `owner/repo#123` | 해당 저장소의 이슈 링크 |

```markdown
이 버그는 #42에서 보고되었고,
@octocat님이 a1b2c3d 커밋으로 수정했습니다.
관련 이슈: cli/cli#1234
```

#### 접기(Details/Summary)

긴 내용을 접어서 숨길 수 있습니다:

```markdown
<details>
<summary>자세한 에러 로그 (클릭해서 펼치기)</summary>

에러 로그나 긴 코드를 여기에 넣습니다.
이슈에 긴 로그를 붙일 때 아주 유용합니다.

</details>
```

> 🔥 **실무 팁**: 이슈에 에러 로그를 붙일 때 `<details>` 태그를 활용하세요. 수백 줄의 로그가 이슈 본문을 가득 채우면 읽기 어렵습니다. 접어두면 필요할 때만 펼쳐볼 수 있어서 훨씬 깔끔합니다.

#### Mermaid 다이어그램

GitHub은 Mermaid 문법으로 **다이어그램을 코드로 그릴 수 있습니다**:

````markdown
```mermaid
graph LR
    A[개발] --> B[PR 생성]
    B --> C[코드 리뷰]
    C --> D[머지]
    D --> E[배포]
```
````

### 개념 5: 효과적인 README 작성 전략

좋은 README는 **5초 안에** 방문자의 질문에 답해야 합니다: "이게 뭐지? 왜 쓰지? 어떻게 시작하지?"

**README 작성 템플릿**:

> **1단계 — 프로젝트 소개** (5초 이내 이해 가능)
> - 프로젝트 이름 + 한 줄 설명
> - 배지(빌드 상태, 버전, 라이선스 등)
>
> **2단계 — 빠른 시작** (1분 이내 실행 가능)
> - 설치 명령어 (복사-붙여넣기 가능)
> - 가장 기본적인 사용 예시
>
> **3단계 — 상세 정보** (필요할 때 참고)
> - 설정 옵션, API 문서, 아키텍처 설명
> - 기여 가이드 (CONTRIBUTING.md 링크)
>
> **4단계 — 부가 정보**
> - 라이선스, 크레딧, 연락처

**배지(Badge) 추가하기**:

배지는 [shields.io](https://shields.io)에서 생성할 수 있습니다:

```markdown
![Build Status](https://img.shields.io/github/actions/workflow/status/owner/repo/ci.yml)
![License](https://img.shields.io/github/license/owner/repo)
![Version](https://img.shields.io/github/v/release/owner/repo)
```

**이슈 작성 가이드**:

좋은 이슈는 **재현 가능한 정보**를 담고 있습니다:

> **제목**: 간결하지만 구체적으로 (X "안 됨", O "로그인 시 404 에러 발생")
>
> **본문 구성**:
> 1. **환경**: OS, 브라우저, 버전
> 2. **재현 단계**: 1, 2, 3 순서로
> 3. **기대 결과**: 원래 어떻게 되어야 하는지
> 4. **실제 결과**: 실제로 무슨 일이 일어났는지
> 5. **스크린샷/로그**: 가능하면 첨부

## 실습: 직접 해보기

```bash
# 1. 마크다운 연습용 저장소 만들기
gh repo create markdown-practice --public --add-readme --clone
cd markdown-practice
```

README.md를 에디터로 열어 아래 내용을 연습해보세요:

```markdown
# 나의 마크다운 연습

## 텍스트 서식
**굵게**, *기울임*, ~~취소선~~, `인라인 코드`

## 목록
- 항목 1
- 항목 2
  - 하위 항목

1. 첫 번째
2. 두 번째

## 체크리스트
- [x] 마크다운 기초 배우기
- [ ] 코드 블록 연습하기
- [ ] 테이블 만들어보기

## 테이블
| 기능 | 문법 |
|------|------|
| 굵게 | `**텍스트**` |
| 링크 | `[텍스트](URL)` |

## Alert
> [!TIP]
> 마크다운은 연습할수록 빨라집니다!
```

```bash
# 2. 커밋 & 푸시
git add README.md
git commit -m "Practice markdown syntax"
git push

# 3. GitHub에서 렌더링 확인
gh browse

# 4. 이슈도 마크다운으로 만들어보기
gh issue create \
  --title "마크다운 연습 이슈" \
  --body "## 할 일
- [ ] 테이블 연습
- [ ] Alert 문법 연습
- [ ] Mermaid 다이어그램 그려보기

> [!NOTE]
> 이 이슈는 마크다운 연습용입니다."
```

## 더 깊이 알아보기

### 마크다운의 탄생

마크다운은 2004년 John Gruber와 Aaron Swartz가 함께 만들었습니다. 이름의 유래가 재미있는데, **HTML(HyperText Markup Language)**의 "Markup"에서 영감을 받아 "Markdown"(아래로 내리다)이라고 지은 거예요. 즉, "마크업을 더 간단하게 내리자"는 뜻이었습니다.

원래 목표는 **이메일처럼 자연스럽게 읽히는 텍스트**가 그대로 HTML로 변환되는 것이었습니다. 실제로 잘 작성된 마크다운 파일은 원본 텍스트 자체로도 구조가 한눈에 보이죠.

그런데 마크다운에는 공식 표준이 없어서 각 플랫폼마다 조금씩 다른 방언이 생겼습니다. GitHub가 2009년에 만든 **GitHub Flavored Markdown(GFM)**은 테이블, 체크리스트, 자동 링크 같은 기능을 추가한 것으로, 이후 2017년에 공식 스펙([github.github.com/gfm](https://github.github.com/gfm/))으로 정립되었습니다.

> 💡 **알고 계셨나요?**: 마크다운 공동 창시자 Aaron Swartz는 RSS, Creative Commons, Reddit 등에도 기여한 인터넷 활동가였습니다. 26세의 나이로 세상을 떠났지만, 그가 만든 마크다운은 전 세계 개발자의 일상 도구가 되었습니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "마크다운은 어디서든 똑같이 보인다" — 아닙니다! GitHub, VS Code, Notion, Slack 등 각 플랫폼마다 지원하는 마크다운 문법이 다릅니다. 예를 들어 `> [!NOTE]` Alert 문법은 GitHub에서만 동작하고, Mermaid 다이어그램도 모든 곳에서 지원되지는 않아요. 작성하는 플랫폼의 지원 문법을 확인하세요.

> 🔥 **실무 팁**: GitHub에서 마크다운을 미리보기할 때 **Preview 탭**을 활용하세요. 이슈, PR, README 편집 화면에서 "Preview" 탭을 클릭하면 렌더링된 결과를 바로 확인할 수 있습니다. 커밋하기 전에 확인하는 습관을 들이세요.

> 🔥 **실무 팁**: PR이나 이슈 댓글에서 코드를 인용할 때, GitHub의 **코드 라인 참조** 기능을 활용하세요. 파일의 특정 줄을 선택하면 URL이 생성되는데, 이를 댓글에 붙여넣으면 해당 코드가 자동으로 인라인 표시됩니다.

## 핵심 정리

| 문법 | 용도 | 예시 |
|------|------|------|
| `#`, `##`, `###` | 제목 | `# 큰 제목` |
| `**텍스트**` | 굵게 | **굵게** |
| `*텍스트*` | 기울임 | *기울임* |
| `` `코드` `` | 인라인 코드 | `git status` |
| ` ``` ` | 코드 블록 | 구문 하이라이팅 지원 |
| `- [ ]` / `- [x]` | 체크리스트 | 이슈에서 진행률 표시 |
| `\| \| \|` | 테이블 | 비교표, 정리표 |
| `> [!NOTE]` | Alert | 5가지 타입 (NOTE/TIP/IMPORTANT/WARNING/CAUTION) |
| `#123` | 이슈 참조 | 자동 링크 변환 |
| `@user` | 사용자 멘션 | 알림 발송 |
| `<details>` | 접기 | 긴 내용 숨기기 |

## 다음 섹션 미리보기

Ch5에서 GitHub의 기본기를 마쳤습니다! 프로필 꾸미기, 저장소 생성, CLI 활용, 그리고 마크다운까지 익혔죠. 다음 챕터 [Ch6. Pull Request와 코드 리뷰](../06-pull-request/01-pr-workflow.md)에서는 GitHub 협업의 **핵심인 PR 워크플로우** — 브랜치에서 PR 생성, 코드 리뷰, 머지까지의 전체 과정을 배웁니다.

## 참고 자료

- [GitHub Docs — 기본 마크다운 문법](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) - 공식 마크다운 가이드
- [GitHub Flavored Markdown Spec](https://github.github.com/gfm/) - GFM 공식 사양
- [GitHub Docs — Alert 문법](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#alerts) - Alert 블록 공식 문서
- [shields.io](https://shields.io) - README 배지 생성 도구
