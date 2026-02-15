# GitHub 계정과 프로필

> 가입, 프로필 README, 기여 그래프, 핀 저장소

## 개요

지금까지 Git을 로컬에서 다루고, 원격 저장소와 연결하는 법까지 배웠습니다. 이번 섹션부터는 **GitHub 플랫폼 자체**를 본격적으로 활용하는 방법을 다룹니다. GitHub 계정을 만들고, 프로필을 개발자 포트폴리오처럼 꾸미는 방법을 알아보겠습니다.

**선수 지식**: [SSH와 인증](../04-remote/05-auth.md)에서 배운 GitHub 인증 설정
**학습 목표**:
- GitHub 계정을 생성하고 기본 설정을 완료할 수 있다
- 프로필 README를 만들어 자기소개 페이지를 꾸밀 수 있다
- 기여 그래프의 의미를 이해하고 활용할 수 있다
- 핀 저장소를 설정해 대표 프로젝트를 보여줄 수 있다

## 왜 알아야 할까?

GitHub 프로필은 개발자의 **온라인 이력서**와 같습니다. 채용 담당자, 동료 개발자, 오픈소스 커뮤니티 — 모두 여러분의 GitHub 프로필을 봅니다. 잘 정리된 프로필은 "이 사람은 어떤 개발자인가?"를 한눈에 보여주는 강력한 도구가 됩니다.

## 핵심 개념

### 개념 1: GitHub 가입과 기본 설정

> 💡 **비유**: GitHub 계정 만들기는 **개발자 마을에 집을 짓는 것**과 같습니다. 집(계정)을 짓고, 대문에 이름표(프로필)를 붙이고, 거실에 자랑거리(핀 저장소)를 진열하는 거죠.

GitHub에 가입하려면 [github.com](https://github.com)에 접속해서 이메일, 비밀번호, 사용자명을 입력하면 됩니다.

**사용자명(username) 선택 팁**:
- 기억하기 쉽고 전문적인 이름을 선택하세요
- 사용자명이 프로필 URL이 됩니다: `github.com/username`
- 한 번 정하면 바꿀 수 있지만, 기존 링크가 깨질 수 있어요

가입 후 기본 설정을 완료합니다:

```bash
# GitHub 가입 후 Git에 같은 정보를 설정
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

> ⚠️ **흔한 오해**: "GitHub 가입만 하면 Git을 쓸 수 있다" — Git과 GitHub는 별개입니다! Git은 로컬 도구이고, GitHub는 원격 호스팅 플랫폼이에요. Git은 따로 설치해야 하고, GitHub 가입 이메일과 `git config`의 이메일을 **일치시켜야** 기여 기록이 제대로 연결됩니다.

### 개념 2: 프로필 README — 개발자 자기소개 페이지

> 💡 **비유**: 프로필 README는 **집 앞에 세워둔 소개 간판**입니다. 지나가는 사람(방문자)이 문을 열기 전에 "이 집에 누가 살고, 무엇을 하는 사람인지" 한눈에 볼 수 있죠.

GitHub에는 **비밀 기능**이 하나 있습니다. 자신의 **사용자명과 같은 이름**의 저장소를 만들면, 그 저장소의 README.md가 프로필 페이지 상단에 표시됩니다!

```bash
# 프로필 README 저장소 만들기
# 사용자명이 'octocat'이라면:
gh repo create octocat --public --clone
cd octocat
```

```bash
# README.md 작성
cat > README.md << 'EOF'
# 안녕하세요! 👋

저는 **홍길동**입니다.
백엔드 개발에 관심이 많은 주니어 개발자예요.

## 🔧 기술 스택
- **언어**: Python, JavaScript, Go
- **프레임워크**: Django, React, Gin
- **도구**: Git, Docker, AWS

## 🌱 현재 배우는 중
- Kubernetes, GitHub Actions

## 📫 연락처
- 이메일: example@email.com
EOF
```

```bash
# 커밋하고 푸시
git add README.md
git commit -m "Add profile README"
git push origin main
```

프로필 README를 만들 때 지켜야 할 조건:
- 저장소 이름이 **사용자명과 정확히 동일**해야 합니다
- 저장소가 **공개(public)**이어야 합니다
- **README.md** 파일이 루트에 있어야 합니다

> 🔥 **실무 팁**: 프로필 README에 동적 콘텐츠를 추가할 수 있습니다. [github-readme-stats](https://github.com/anuraghazra/github-readme-stats)를 사용하면 자신의 GitHub 통계(커밋 수, PR 수, 사용 언어 비율 등)가 자동으로 업데이트되는 카드를 넣을 수 있어요.

### 개념 3: 기여 그래프 — 잔디 심기

> 💡 **비유**: 기여 그래프는 **출석 달력**과 같습니다. 매일 학교에 가면 도장이 찍히듯, GitHub에서 활동하면 초록색 칸이 채워지죠. 개발자들 사이에서 "잔디 심기"라고 부르는 이유입니다.

프로필 페이지 하단의 초록색 타일 그리드가 바로 **기여 그래프(contribution graph)**입니다. 지난 1년간의 활동을 시각적으로 보여줍니다.

**기여로 인정되는 활동**:
- 기본 브랜치(main/master)나 `gh-pages`에 대한 **커밋**
- **이슈(Issue)** 생성
- **Pull Request** 생성
- **Pull Request 리뷰** 제출
- **Discussion** 답변

**기여가 기록되지 않는 흔한 원인**:
- `git config`의 이메일이 GitHub 계정 이메일과 **다른** 경우
- fork한 저장소에서의 커밋 (원본에 머지되기 전까지)
- 기본 브랜치가 아닌 브랜치에서의 커밋

```bash
# 현재 설정된 이메일 확인
git config user.email

# GitHub 계정의 이메일과 일치하도록 설정
git config --global user.email "github-registered@email.com"
```

> ⚠️ **흔한 오해**: "잔디가 많을수록 좋은 개발자다" — 기여 그래프는 **활동 빈도**를 보여줄 뿐, 코드의 **질**을 보여주지 않습니다. 의미 없는 커밋으로 잔디를 채우는 것보다, 양질의 코드와 프로젝트를 꾸준히 만드는 게 훨씬 중요합니다.

### 개념 4: 핀 저장소 — 대표작 전시하기

> 💡 **비유**: 핀 저장소는 **포트폴리오의 첫 페이지**입니다. 작품이 100개 있어도, 가장 자신 있는 6개를 골라 첫 페이지에 놓는 것처럼요.

GitHub 프로필에서 최대 **6개의 저장소**를 핀(고정)할 수 있습니다. 방문자에게 가장 먼저 보여주고 싶은 프로젝트를 선택하세요.

**핀 저장소 설정 방법**:
1. 프로필 페이지 접속 (`github.com/username`)
2. **"Customize your pins"** 클릭
3. 최대 6개 저장소 또는 Gist 선택
4. **Save pins** 클릭

**효과적인 핀 저장소 전략**:
- 다양한 기술 스택을 보여주는 프로젝트 선택
- 각 저장소에 **설명(description)**과 **토픽(topics)** 추가
- README가 잘 정리된 저장소를 우선 선택
- 실제 동작하는 프로젝트나 데모 링크가 있으면 더 좋음

### 개념 5: 프로필 설정 마무리

프로필을 완성하려면 기본 정보도 채워야 합니다:

1. **프로필 사진**: 얼굴 사진이나 아바타 (기본 identicon보다 훨씬 전문적으로 보입니다)
2. **이름과 바이오**: 한 줄 자기소개 (예: "Backend Developer | Python & Go")
3. **위치, 회사, 웹사이트**: 선택 사항이지만, 채울수록 신뢰도가 올라갑니다
4. **소셜 링크**: 블로그, 트위터, LinkedIn 등

```bash
# GitHub CLI로 프로필 상태 확인
gh api user --jq '.login, .name, .bio, .public_repos'
```

```output
octocat
Octo Cat
Full-stack developer | Open source enthusiast
42
```

## 실습: 직접 해보기

```bash
# 1. GitHub 계정 이메일 확인
git config --global user.email

# 2. 프로필 README 저장소 만들기 (사용자명으로 교체)
gh repo create YOUR_USERNAME --public --clone
cd YOUR_USERNAME

# 3. README.md 작성
echo "# Hello! I'm YOUR_NAME 👋" > README.md
echo "" >> README.md
echo "Welcome to my GitHub profile!" >> README.md

# 4. 커밋 & 푸시
git add README.md
git commit -m "Create profile README"
git push origin main

# 5. 프로필 확인
gh browse
```

## 더 깊이 알아보기

### GitHub의 탄생과 "Social Coding"

GitHub는 2008년 Tom Preston-Werner, Chris Wanstrath, PJ Hyett, Scott Chacon이 공동 창립했습니다. 당시만 해도 코드를 공유하고 협업하는 과정이 번거로웠는데, GitHub는 Git 위에 **소셜 네트워크** 개념을 입혀서 "코드 공유와 협업을 SNS처럼 쉽게" 만들었죠.

"Social Coding"이라는 슬로건은 단순한 마케팅이 아니었습니다. 팔로우, 스타, 포크 같은 소셜 기능이 개발자 생태계를 완전히 바꿔놓았거든요. 2018년 Microsoft가 GitHub를 **75억 달러(약 10조 원)**에 인수했을 때, 이미 GitHub는 전 세계 개발자의 필수 인프라가 되어 있었습니다. 현재(2025년 기준) GitHub 사용자는 **1억 명**을 넘었습니다.

> 💡 **알고 계셨나요?**: GitHub의 마스코트 **Octocat**은 디자이너 Simon Oxley가 만든 캐릭터입니다. 원래는 iStock에서 구매한 이미지였는데, GitHub의 정체성이 되어버렸죠. 고양이(cat)와 문어(octopus)의 합성어인 Octocat은 이제 전 세계 개발자가 알아보는 아이콘이 되었습니다.

## 흔한 오해와 팁

> ⚠️ **흔한 오해**: "GitHub 계정은 하나만 있으면 된다" — 많은 회사에서 업무용 GitHub 계정을 별도로 사용합니다. 개인 프로젝트와 회사 프로젝트를 분리하고 싶다면 계정을 나눌 수 있어요. 다만 [SSH와 인증](../04-remote/05-auth.md)에서 배운 SSH config로 두 계정을 한 컴퓨터에서 관리하는 방법도 있습니다.

> 🔥 **실무 팁**: 기여 그래프에서 **private 저장소**의 활동도 표시할 수 있습니다. Settings → Profile → "Private contributions"를 체크하면, 비공개 활동도 익명화된 형태로 잔디에 반영됩니다. 다만 세부 내용은 본인만 볼 수 있어요.

> 🔥 **실무 팁**: GitHub 프로필 README의 최대 너비는 약 **115자**입니다. ASCII 아트나 테이블을 만들 때 이 너비를 넘기지 않도록 주의하세요. 모바일에서는 55자 정도가 적당합니다.

## 핵심 정리

| 개념 | 설명 |
|------|------|
| GitHub 계정 | `github.com`에서 가입, Git config 이메일과 일치 필수 |
| 프로필 README | 사용자명과 같은 이름의 공개 저장소에 README.md 작성 |
| 기여 그래프 | 1년간 활동을 초록색 타일로 시각화, "잔디 심기" |
| 핀 저장소 | 최대 6개 대표 프로젝트를 프로필 상단에 고정 |
| `git config user.email` | GitHub 이메일과 일치해야 기여 기록 연결 |
| `gh api user` | GitHub CLI로 프로필 정보 확인 |

## 다음 섹션 미리보기

프로필을 꾸몄으니, 이제 그 안에 채울 **저장소(repository)**를 만들어야겠죠? 다음 섹션 [저장소 만들기](./02-create-repo.md)에서는 GitHub 웹과 CLI로 저장소를 생성하고, README, LICENSE, .gitignore를 올바르게 설정하는 방법을 배웁니다.

## 참고 자료

- [GitHub Docs — 프로필 설정](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/about-your-profile) - 프로필 커스터마이징 공식 가이드
- [GitHub Docs — 프로필 README 관리](https://docs.github.com/en/account-and-profile/how-tos/profile-customization/managing-your-profile-readme) - 프로필 README 설정법
- [GitHub Docs — 기여 그래프](https://docs.github.com/en/account-and-profile/concepts/contributions-on-your-profile) - 기여로 인정되는 활동 기준
- [github-readme-stats](https://github.com/anuraghazra/github-readme-stats) - 프로필 README용 동적 통계 카드
