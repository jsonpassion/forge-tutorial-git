# 2026: Git 완전 정복

> 입문부터 실무까지, Git & GitHub A to Z

**12챕터 56섹션**으로 구성된 A-to-Z Git & GitHub 튜토리얼입니다.

[![Git 2.40+](https://img.shields.io/badge/Git-2.40+-F05032?logo=git&logoColor=white)](https://git-scm.com)
[![License](https://img.shields.io/badge/License-GPL--3.0-blue)](LICENSE)
[![한국어](https://img.shields.io/badge/Language-한국어-brightgreen)](#)
[![56/56 Sections](https://img.shields.io/badge/Sections-56%2F56_Complete-success)](#목차)

## 튜토리얼 현황

| 항목 | 수량 | 상태 |
|------|------|------|
| 총 챕터 | 12개 | ✅ 완료 |
| 총 섹션 | 56개 | ✅ 완료 |
| Part 1: Git 기초 | 10개 섹션 | ✅ 완료 |
| Part 2: 브랜치와 원격 | 10개 섹션 | ✅ 완료 |
| Part 3: GitHub 협업 | 12개 섹션 | ✅ 완료 |
| Part 4: 고급 Git | 9개 섹션 | ✅ 완료 |
| Part 5: 자동화와 실무 | 15개 섹션 | ✅ 완료 |

---

## 이 튜토리얼의 특징

- **비유 우선 설명** — 모든 개념을 일상적 비유로 먼저 소개한 뒤, 기술 설명과 실습으로 이어집니다
- **실행 가능한 예제** — 터미널에서 바로 복사-붙여넣기로 실습할 수 있는 완전한 명령어
- **역사적 에피소드** — Git과 GitHub의 탄생 비화, 기능의 유래, 개발자 이야기
- **실무 팁과 흔한 오해** — 현업 개발자가 알아야 할 노하우와 초보자가 자주 하는 실수
- **크로스 플랫폼** — macOS, Windows, Linux 차이가 있는 경우 모두 표기
- **특수 코드 블록** — `output`, `error`, `console` 태그로 명령어 결과를 시각적으로 구분

---

## 목차

### Part 1: Git 기초 (입문)

#### Ch1. Git 시작하기
- [01. Git이란?](01-git-start/01-what-is-git.md) — 버전 관리의 개념, Git의 탄생과 철학, 분산 버전 관리
- [02. 설치와 초기 설정](01-git-start/02-install-setup.md) — Git 설치(macOS/Windows/Linux), git config, 터미널 기본
- [03. 첫 번째 저장소](01-git-start/03-first-repo.md) — git init, 작업 디렉토리 · 스테이징 · 저장소의 3단계 구조
- [04. 커밋의 기본](01-git-start/04-commit-basics.md) — add, commit, status, log — 변경 사항을 기록하는 법
- [05. 변경 추적과 커밋 메시지](01-git-start/05-diff-messages.md) — diff, show, 좋은 커밋 메시지 작성법

#### Ch2. 파일과 히스토리 관리
- [01. .gitignore](02-file-history/01-gitignore.md) — 추적 제외 패턴, 글로벌 설정, 이미 추적 중인 파일 제거
- [02. 파일 상태 관리](02-file-history/02-file-operations.md) — restore, checkout --, rm, mv — 파일 되돌리기와 이동
- [03. 히스토리 탐색](02-file-history/03-log-exploration.md) — log 옵션, blame, show, shortlog — 커밋 기록 살펴보기
- [04. 되돌리기 기초](02-file-history/04-undo-basics.md) — reset, revert, amend — 실수를 바로잡는 세 가지 방법
- [05. 태그](02-file-history/05-tags.md) — tag 생성과 관리, 시맨틱 버저닝, 릴리스 태깅

### Part 2: 브랜치와 원격 저장소 (초급)

#### Ch3. 브랜치
- [01. 브랜치란?](03-branch/01-branch-concept.md) — 브랜치의 개념, HEAD 포인터, 커밋 그래프로 이해하기
- [02. 브랜치 생성과 전환](03-branch/02-create-switch.md) — branch, switch, checkout — 브랜치 만들고 이동하기
- [03. 브랜치 병합](03-branch/03-merge.md) — merge, fast-forward, 3-way merge의 차이와 원리
- [04. 충돌 해결](03-branch/04-conflict.md) — 충돌 발생 원인, 마커 읽기, 단계별 해결 전략
- [05. 브랜치 관리](03-branch/05-branch-management.md) — 삭제, 이름 변경, 목록 관리, 브랜치 정리 전략

#### Ch4. 원격 저장소
- [01. 원격 저장소 개념](04-remote/01-remote-concept.md) — remote, origin, upstream — 원격 저장소의 구조
- [02. clone과 fork](04-remote/02-clone-fork.md) — 저장소 복제, fork와 clone의 차이, 오픈소스 기여 준비
- [03. push와 pull](04-remote/03-push-pull.md) — 원격 동기화, tracking branch, pull 충돌 해결
- [04. fetch와 remote 관리](04-remote/04-fetch-remote.md) — fetch vs pull, 다중 remote, prune, 원격 브랜치 추적
- [05. SSH와 인증](04-remote/05-auth.md) — SSH 키 생성과 등록, HTTPS 토큰, Credential Manager

### Part 3: GitHub 협업 (중급)

#### Ch5. GitHub 시작하기
- [01. GitHub 계정과 프로필](05-github-start/01-github-account.md) — 가입, 프로필 README, 기여 그래프, 핀 저장소
- [02. 저장소 만들기](05-github-start/02-create-repo.md) — 레포 생성, README, LICENSE 선택, .gitignore 템플릿
- [03. GitHub CLI (gh)](05-github-start/03-github-cli.md) — gh 설치, 인증, repo/issue/pr 기본 명령어
- [04. Markdown 작성법](05-github-start/04-markdown.md) — GitHub Flavored Markdown, README 작성, 문서화 전략

#### Ch6. Pull Request와 코드 리뷰
- [01. PR 워크플로우](06-pull-request/01-pr-workflow.md) — 브랜치 → PR → 리뷰 → 머지, PR 작성법
- [02. 코드 리뷰](06-pull-request/02-code-review.md) — 리뷰 요청, 라인 코멘트, Approve/Request Changes
- [03. PR 관리](06-pull-request/03-pr-management.md) — 충돌 해결, Draft PR, Merge 전략(Squash/Rebase/Merge Commit)
- [04. 오픈소스 기여](06-pull-request/04-open-source.md) — fork → upstream sync → 첫 PR, 기여 가이드라인

#### Ch7. Issues와 프로젝트 관리
- [01. Issues 활용](07-issues-projects/01-issues.md) — 이슈 작성, 라벨, 마일스톤, Assignee, 이슈 참조
- [02. Projects 보드](07-issues-projects/02-projects.md) — 칸반 보드, 자동화, 타임라인 뷰, 커스텀 필드
- [03. Discussions와 Wiki](07-issues-projects/03-discussions-wiki.md) — 커뮤니티 소통, Q&A, 문서 관리
- [04. 템플릿과 자동화](07-issues-projects/04-templates.md) — Issue/PR 템플릿, CONTRIBUTING.md, CODEOWNERS

### Part 4: 고급 Git (중상급)

#### Ch8. Rebase와 고급 브랜치 전략
- [01. Rebase 기초](08-advanced-branch/01-rebase.md) — rebase vs merge, 리베이스 원리, 언제 사용해야 하는가
- [02. Interactive Rebase](08-advanced-branch/02-interactive-rebase.md) — squash, fixup, reword, edit, drop — 히스토리 다듬기
- [03. Cherry-pick](08-advanced-branch/03-cherry-pick.md) — 특정 커밋만 가져오기, 충돌 처리, 활용 시나리오
- [04. 워크플로우 전략](08-advanced-branch/04-workflow-strategies.md) — Git Flow, GitHub Flow, Trunk-Based Development 비교

#### Ch9. 히스토리 관리와 Git 내부
- [01. Stash](09-history-internals/01-stash.md) — 작업 임시 저장, pop, apply, drop, 브랜치로 변환
- [02. Reflog와 복구](09-history-internals/02-reflog.md) — HEAD 이력 추적, 잃어버린 커밋 복구, 실수 되돌리기
- [03. Reset 심화](09-history-internals/03-reset-deep.md) — soft, mixed, hard 차이, reset vs revert vs checkout
- [04. 히스토리 재작성](09-history-internals/04-history-rewrite.md) — filter-repo, BFG Cleaner, 민감 정보 제거, 대용량 파일 정리
- [05. Git 내부 구조](09-history-internals/05-git-internals.md) — 객체(blob/tree/commit), refs, HEAD, DAG, packfile

### Part 5: 자동화와 실무 (실무~전문가)

#### Ch10. GitHub Actions와 자동화
- [01. Actions 시작하기](10-github-actions/01-actions-intro.md) — workflow, job, step 개념, 마켓플레이스
- [02. 워크플로우 작성](10-github-actions/02-workflow-yaml.md) — YAML 문법, 트리거(on), 환경변수, 시크릿
- [03. 빌드와 테스트 자동화](10-github-actions/03-ci.md) — CI 파이프라인 구축, 매트릭스 빌드, 캐싱
- [04. 배포 자동화](10-github-actions/04-cd.md) — CD 파이프라인, 환경 분리, 자동 릴리스, 아티팩트
- [05. GitHub Pages](10-github-actions/05-pages.md) — 정적 사이트 배포, Jekyll, 커스텀 도메인, Actions 연동

#### Ch11. 팀 협업과 도구
- [01. 브랜치 네이밍 컨벤션](11-team-tools/01-branch-naming.md) — 팀 브랜치 규칙, prefix 전략, 자동 검증
- [02. 커밋 메시지 컨벤션](11-team-tools/02-commit-convention.md) — Conventional Commits, commitlint, 자동 Changelog
- [03. 코드 리뷰 문화](11-team-tools/03-review-culture.md) — 효과적인 리뷰, CODEOWNERS, 리뷰 자동화, 팀 가이드
- [04. 모노레포와 서브모듈](11-team-tools/04-monorepo-submodule.md) — monorepo vs multi-repo, submodule, subtree, 대규모 프로젝트
- [05. GUI 도구 활용](11-team-tools/05-gui-tools.md) — VS Code, GitKraken, Fork 비교, CLI vs GUI 선택 가이드

#### Ch12. 트러블슈팅과 생태계
- [01. 흔한 실수와 해결](12-troubleshooting/01-common-mistakes.md) — 잘못된 커밋, 브랜치 실수, push 취소, 되돌리기 시나리오
- [02. 대규모 저장소 관리](12-troubleshooting/02-large-repos.md) — Git LFS, sparse-checkout, shallow clone, partial clone
- [03. Bisect와 디버깅](12-troubleshooting/03-bisect-debug.md) — git bisect로 버그 원점 찾기, 자동화 스크립트
- [04. 보안](12-troubleshooting/04-security.md) — signed commits(GPG/SSH), secret scanning, Dependabot
- [05. Git 생태계와 다음 단계](12-troubleshooting/05-ecosystem.md) — GitLab/Bitbucket 비교, Git 최신 동향, DevOps 확장

---

## 학습 로드맵

> 자신의 수준에 맞는 Part부터 시작하세요.

```
Git 처음이라면          혼자 써봤다면           팀에서 쓰고 싶다면
    │                       │                       │
    ▼                       ▼                       ▼
 Part 1 ──────────►  Part 2 ──────────►  Part 3
(Git 기초)          (브랜치/원격)         (GitHub 협업)
 Ch1-2                Ch3-4                Ch5-7
    │                   │                    │
    └───────────────────┴────────────────────┘
                        │
                        ▼
                     Part 4
                   (고급 Git)
                     Ch8-9
                        │
                        ▼
                     Part 5
                  (자동화/실무)
                    Ch10-12
```

| Part | 대상 | 챕터 | 핵심 키워드 |
|:----:|------|:----:|------------|
| **1** | Git이 처음인 분 | Ch1-2 | init, commit, diff, log, reset, tag |
| **2** | 혼자 Git은 쓰는 분 | Ch3-4 | branch, merge, remote, push, pull, SSH |
| **3** | 팀 협업을 시작하는 분 | Ch5-7 | GitHub, PR, 코드 리뷰, Issues, Projects |
| **4** | 히스토리를 다듬고 싶은 분 | Ch8-9 | rebase, cherry-pick, stash, reflog, Git 내부 |
| **5** | 자동화와 실무 운영이 필요한 분 | Ch10-12 | Actions, CI/CD, 컨벤션, 트러블슈팅, 보안 |

---

## 부록

### Resources
- [Git 공식 문서와 핵심 레퍼런스](resources/essential-papers.md) — 꼭 읽어야 할 공식 문서 모음
- [챕터별 실습 프로젝트](resources/datasets.md) — 실습용 저장소와 프로젝트
- [Git 도구와 유용한 확장](resources/tools.md) — CLI 도구, GUI, IDE 확장 가이드

---

## 기술 기준

| 항목 | 기준 |
|------|------|
| **Git** | 2.40+ (최신 기능 포함) |
| **플랫폼** | CLI(터미널) + GitHub + GUI 도구 |
| **GitHub CLI** | gh 2.x+ |
| **OS** | macOS / Windows / Linux |
| **GUI 도구** | VS Code, GitKraken, Fork, Sourcetree |

## 앱 연동

**Forge: Git** 앱은 이 저장소의 마크다운을 GitHub Raw URL로 실시간 로드합니다.
`manifest.json`이 전체 구조의 진실의 원천(single source of truth)입니다.

## 참고 자료

- [Pro Git Book](https://git-scm.com/book/en/v2) — Git 공식 무료 도서
- [Git 공식 문서](https://git-scm.com/docs) — 명령어 레퍼런스
- [GitHub Docs](https://docs.github.com) — GitHub 기능 공식 문서
- [GitHub Skills](https://skills.github.com) — 인터랙티브 학습 코스

## 기여하기

오타, 오류, 개선 사항은 [Issue](https://github.com/jsonpassion/forge-tutorial-git/issues)나 PR로 알려주세요.

## 라이선스

[GPL-3.0 License](LICENSE)
