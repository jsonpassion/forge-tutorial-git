# 2026: Git 완전 정복

<p align="center">
  <strong>입문부터 실무까지, Git & GitHub A to Z</strong><br>
  <em>Forge: Git 앱 튜토리얼 콘텐츠 저장소</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Git-2.40+-F05032?logo=git&logoColor=white" alt="Git 2.40+">
  <img src="https://img.shields.io/badge/License-GPL--3.0-blue" alt="License">
  <img src="https://img.shields.io/badge/Language-한국어-brightgreen" alt="Korean">
  <img src="https://img.shields.io/badge/Sections-24%2F56-orange" alt="Progress">
</p>

---

## 소개

**2026: Git 완전 정복**은 Git과 GitHub을 처음 배우는 개발자부터 실무에서 활용하려는 시니어까지, 체계적으로 학습할 수 있도록 구성된 한국어 튜토리얼입니다.

- **12개 챕터**, **56개 섹션**의 단계별 커리큘럼
- 모든 개념에 **일상적 비유**와 **실행 가능한 예제** 포함
- Git의 **역사적 에피소드**와 **실무 팁**으로 깊이 있는 학습
- **Forge: Git** 앱에서 GitHub Raw URL로 실시간 로드

---

## 진행 현황

```
Part 1  ██████████████████████████████████████████  100%  Ch1-2 완료
Part 2  ██████████████████████████████████████████  100%  Ch3-4 완료
Part 3  ██████████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░   33%  Ch5-7
Part 4  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░    0%  Ch8-9
Part 5  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░    0%  Ch10-12
```

**전체**: 24 / 56 섹션 완료 (43%)

---

## 목차

### Part 1: Git 기초 (입문)

<details>
<summary><strong>Ch1. Git 시작하기</strong> — 5/5 ✅</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | [Git이란?](01-git-start/01-what-is-git.md) | 버전 관리의 개념, Git의 탄생과 철학, 분산 버전 관리 |
| 02 | [설치와 초기 설정](01-git-start/02-install-setup.md) | Git 설치(macOS/Windows/Linux), git config, 터미널 기본 |
| 03 | [첫 번째 저장소](01-git-start/03-first-repo.md) | git init, 작업 디렉토리·스테이징·저장소의 3단계 구조 |
| 04 | [커밋의 기본](01-git-start/04-commit-basics.md) | add, commit, status, log — 변경 사항을 기록하는 법 |
| 05 | [변경 추적과 커밋 메시지](01-git-start/05-diff-messages.md) | diff, show, 좋은 커밋 메시지 작성법 |

</details>

<details>
<summary><strong>Ch2. 파일과 히스토리 관리</strong> — 5/5 ✅</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | [.gitignore](02-file-history/01-gitignore.md) | 추적 제외 패턴, 글로벌 설정, 이미 추적 중인 파일 제거 |
| 02 | [파일 상태 관리](02-file-history/02-file-operations.md) | restore, checkout --, rm, mv — 파일 되돌리기와 이동 |
| 03 | [히스토리 탐색](02-file-history/03-log-exploration.md) | log 옵션, blame, show, shortlog — 커밋 기록 살펴보기 |
| 04 | [되돌리기 기초](02-file-history/04-undo-basics.md) | reset, revert, amend — 실수를 바로잡는 세 가지 방법 |
| 05 | [태그](02-file-history/05-tags.md) | tag 생성과 관리, 시맨틱 버저닝, 릴리스 태깅 |

</details>

### Part 2: 브랜치와 원격 저장소 (초급)

<details>
<summary><strong>Ch3. 브랜치</strong> — 5/5 ✅</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | [브랜치란?](03-branch/01-branch-concept.md) | 브랜치의 개념, HEAD 포인터, 커밋 그래프로 이해하기 |
| 02 | [브랜치 생성과 전환](03-branch/02-create-switch.md) | branch, switch, checkout — 브랜치 만들고 이동하기 |
| 03 | [브랜치 병합](03-branch/03-merge.md) | merge, fast-forward, 3-way merge의 차이와 원리 |
| 04 | [충돌 해결](03-branch/04-conflict.md) | 충돌 발생 원인, 마커 읽기, 단계별 해결 전략 |
| 05 | [브랜치 관리](03-branch/05-branch-management.md) | 삭제, 이름 변경, 목록 관리, 브랜치 정리 전략 |

</details>

<details>
<summary><strong>Ch4. 원격 저장소</strong> — 5/5 ✅</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | [원격 저장소 개념](04-remote/01-remote-concept.md) | remote, origin, upstream — 원격 저장소의 구조 |
| 02 | [clone과 fork](04-remote/02-clone-fork.md) | 저장소 복제, fork와 clone의 차이, 오픈소스 기여 준비 |
| 03 | [push와 pull](04-remote/03-push-pull.md) | 원격 동기화, tracking branch, pull 충돌 해결 |
| 04 | [fetch와 remote 관리](04-remote/04-fetch-remote.md) | fetch vs pull, 다중 remote, prune, 원격 브랜치 추적 |
| 05 | [SSH와 인증](04-remote/05-auth.md) | SSH 키 생성과 등록, HTTPS 토큰, Credential Manager |

</details>

### Part 3: GitHub 협업 (중급)

<details>
<summary><strong>Ch5. GitHub 시작하기</strong> — 4/4 ✅</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | [GitHub 계정과 프로필](05-github-start/01-github-account.md) | 가입, 프로필 README, 기여 그래프, 핀 저장소 |
| 02 | [저장소 만들기](05-github-start/02-create-repo.md) | 레포 생성, README, LICENSE 선택, .gitignore 템플릿 |
| 03 | [GitHub CLI (gh)](05-github-start/03-github-cli.md) | gh 설치, 인증, repo/issue/pr 기본 명령어 |
| 04 | [Markdown 작성법](05-github-start/04-markdown.md) | GitHub Flavored Markdown, README 작성, 문서화 전략 |

</details>

<details>
<summary><strong>Ch6. Pull Request와 코드 리뷰</strong> — 0/4</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | PR 워크플로우 | 브랜치 → PR → 리뷰 → 머지, PR 작성법 |
| 02 | 코드 리뷰 | 리뷰 요청, 라인 코멘트, Approve/Request Changes |
| 03 | PR 관리 | 충돌 해결, Draft PR, Merge 전략(Squash/Rebase/Merge Commit) |
| 04 | 오픈소스 기여 | fork → upstream sync → 첫 PR, 기여 가이드라인 |

</details>

<details>
<summary><strong>Ch7. Issues와 프로젝트 관리</strong> — 0/4</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | Issues 활용 | 이슈 작성, 라벨, 마일스톤, Assignee, 이슈 참조 |
| 02 | Projects 보드 | 칸반 보드, 자동화, 타임라인 뷰, 커스텀 필드 |
| 03 | Discussions와 Wiki | 커뮤니티 소통, Q&A, 문서 관리 |
| 04 | 템플릿과 자동화 | Issue/PR 템플릿, CONTRIBUTING.md, CODEOWNERS |

</details>

### Part 4: 고급 Git (중상급)

<details>
<summary><strong>Ch8. Rebase와 고급 브랜치 전략</strong> — 0/4</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | Rebase 기초 | rebase vs merge, 리베이스 원리, 언제 사용해야 하는가 |
| 02 | Interactive Rebase | squash, fixup, reword, edit, drop — 히스토리 다듬기 |
| 03 | Cherry-pick | 특정 커밋만 가져오기, 충돌 처리, 활용 시나리오 |
| 04 | 워크플로우 전략 | Git Flow, GitHub Flow, Trunk-Based Development 비교 |

</details>

<details>
<summary><strong>Ch9. 히스토리 관리와 Git 내부</strong> — 0/5</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | Stash | 작업 임시 저장, pop, apply, drop, 브랜치로 변환 |
| 02 | Reflog와 복구 | HEAD 이력 추적, 잃어버린 커밋 복구, 실수 되돌리기 |
| 03 | Reset 심화 | soft, mixed, hard 차이, reset vs revert vs checkout |
| 04 | 히스토리 재작성 | filter-repo, BFG Cleaner, 민감 정보 제거, 대용량 파일 정리 |
| 05 | Git 내부 구조 | 객체(blob/tree/commit), refs, HEAD, DAG, packfile |

</details>

### Part 5: 자동화와 실무 (실무~전문가)

<details>
<summary><strong>Ch10. GitHub Actions와 자동화</strong> — 0/5</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | Actions 시작하기 | workflow, job, step 개념, 마켓플레이스 |
| 02 | 워크플로우 작성 | YAML 문법, 트리거(on), 환경변수, 시크릿 |
| 03 | 빌드와 테스트 자동화 | CI 파이프라인 구축, 매트릭스 빌드, 캐싱 |
| 04 | 배포 자동화 | CD 파이프라인, 환경 분리, 자동 릴리스, 아티팩트 |
| 05 | GitHub Pages | 정적 사이트 배포, Jekyll, 커스텀 도메인, Actions 연동 |

</details>

<details>
<summary><strong>Ch11. 팀 협업과 도구</strong> — 0/5</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | 브랜치 네이밍 컨벤션 | 팀 브랜치 규칙, prefix 전략, 자동 검증 |
| 02 | 커밋 메시지 컨벤션 | Conventional Commits, commitlint, 자동 Changelog |
| 03 | 코드 리뷰 문화 | 효과적인 리뷰, CODEOWNERS, 리뷰 자동화, 팀 가이드 |
| 04 | 모노레포와 서브모듈 | monorepo vs multi-repo, submodule, subtree, 대규모 프로젝트 |
| 05 | GUI 도구 활용 | VS Code, GitKraken, Fork 비교, CLI vs GUI 선택 가이드 |

</details>

<details>
<summary><strong>Ch12. 트러블슈팅과 생태계</strong> — 0/5</summary>

| # | 섹션 | 설명 |
|:-:|------|------|
| 01 | 흔한 실수와 해결 | 잘못된 커밋, 브랜치 실수, push 취소, 되돌리기 시나리오 |
| 02 | 대규모 저장소 관리 | Git LFS, sparse-checkout, shallow clone, partial clone |
| 03 | Bisect와 디버깅 | git bisect로 버그 원점 찾기, 자동화 스크립트 |
| 04 | 보안 | signed commits(GPG/SSH), secret scanning, Dependabot |
| 05 | Git 생태계와 다음 단계 | GitLab/Bitbucket 비교, Git 최신 동향, DevOps 확장 |

</details>

---

## 기술 기준

| 항목 | 기준 |
|------|------|
| **Git** | 2.40+ (최신 기능 포함) |
| **플랫폼** | CLI(터미널) + GitHub + GUI 도구 |
| **GitHub CLI** | gh 2.x+ |
| **OS** | macOS / Windows / Linux 크로스 플랫폼 |
| **GUI 도구** | VS Code, GitKraken, Fork, Sourcetree |

## 콘텐츠 특징

- **비유 우선 설명**: 모든 개념을 일상적 비유로 먼저 소개
- **실행 가능한 예제**: 터미널에서 바로 복사-붙여넣기로 실습 가능
- **역사적 에피소드**: Git과 GitHub의 탄생 비화, 기능의 유래
- **실무 팁**: 현업 개발자가 알아야 할 노하우와 함정
- **크로스 플랫폼**: macOS, Windows, Linux 차이가 있는 경우 모두 표기

## 프로젝트 구조

```
forge-tutorial-git/
├── manifest.json              # 전체 튜토리얼 구조 정의 (진실의 원천)
├── contents_status.md         # 진행 현황 추적
├── 01-git-start/              # Part 1: Git 기초 (입문)
├── 02-file-history/
├── 03-branch/                 # Part 2: 브랜치와 원격 저장소 (초급)
├── 04-remote/
├── 05-github-start/           # Part 3: GitHub 협업 (중급)
├── 06-pull-request/
├── 07-issues-projects/
├── 08-advanced-branch/        # Part 4: 고급 Git (중상급)
├── 09-history-internals/
├── 10-github-actions/         # Part 5: 자동화와 실무 (실무~전문가)
├── 11-team-tools/
├── 12-troubleshooting/
├── resources/                 # 참고 자료 모음
│   ├── essential-papers.md
│   ├── datasets.md
│   └── tools.md
└── assets/
    └── images/
```

## 앱 연동

**Forge: Git** 앱은 GitHub Raw URL을 통해 이 저장소의 마크다운 콘텐츠를 실시간으로 로드합니다. `manifest.json`이 전체 구조의 진실의 원천(single source of truth) 역할을 합니다.

## 참고 자료

- [Pro Git Book](https://git-scm.com/book/en/v2) — Git 공식 무료 도서
- [Git 공식 문서](https://git-scm.com/docs) — 명령어 레퍼런스
- [GitHub Docs](https://docs.github.com) — GitHub 기능 공식 문서
- [GitHub Skills](https://skills.github.com) — GitHub 인터랙티브 학습

## 라이선스

[GPL-3.0 License](LICENSE)
