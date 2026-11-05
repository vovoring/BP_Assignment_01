# LEO's Homepage — 개인 소개·포트폴리오 웹사이트

전북대학교 컴퓨터인공지능학부 **LEO**의 자기소개와 게임 개발 활동(Unity 프로젝트 *Lost Paradise* 괴물 AI 개발)을 정리한 웹사이트입니다.
Hugo 테마 **Hermit-V2**로 만들었고, `main` 브랜치에 push하면 GitHub Actions가 GitHub Pages에 자동으로 배포합니다.

| 항목 | 주소 |
| --- | --- |
| 공개 사이트 | https://vovoring.github.io/BP_Assignment_01/ |
| 저장소 | https://github.com/vovoring/BP_Assignment_01 |
| 배포 기록(Actions) | https://github.com/vovoring/BP_Assignment_01/actions |

---

## 1. 저장소 구조

```text
BP_Assignment_01/
├─ .github/
│  ├─ workflows/hugo-pages.yml   # 배포 워크플로 (main push 시 자동 실행)
│  └─ pages_build.py             # 의존성 설치·빌드·주소 보정 스크립트 (수업 제공)
├─ .github-pages.json            # 빌드 설정(Hugo 버전, 설정 파일명, 테마명, baseURL)
├─ prepare_github_pages.py       # 최초 배포 준비 스크립트 (수업 제공, pages_build.py와 동일 파일)
├─ LICENSE                       # Hermit-V2 MIT 라이선스 원문
└─ site/                         # ← 실제 Hugo 사이트 (수정은 모두 여기서)
   ├─ pages.json                 # Hugo 설정 파일 (hugo.toml 역할)
   ├─ content/                   # 페이지 본문(Markdown)
   ├─ static/images/             # 이미지·영상
   └─ themes/template/           # Hermit-V2 테마 (일부 수정)
```

> Hugo 사이트 파일은 루트가 아니라 **`site/` 폴더**에 있습니다. 설정 파일도 `hugo.toml`이 아니라 **`site/pages.json`** 입니다.

---

## 2. 실행 환경과 빌드·실행 방법

### 2-1. 필요한 도구와 버전

배포 워크플로(`.github/workflows/hugo-pages.yml`)에 적힌 버전과 같습니다.

| 도구 | 버전 | 용도 |
| --- | --- | --- |
| Git | 최신 | 저장소 복제, commit·push |
| Python | 3.12 (Actions 기준, 3.13에서도 빌드 확인) | `.github/pages_build.py` 실행 |
| Node.js / npm | 24 (Actions 기준, 20에서도 빌드 확인) | `--dependencies` 단계에서 postcss·tailwindcss·pagefind를 `.pages-tools/`에 설치 |
| **Hugo Extended** | **0.167.0** | 사이트 생성 (일반판이 아닌 **Extended**판 필요) |
| Dart Sass | 1.105.0 | 테마 SCSS 컴파일 |

### 2-2. 설치

**Hugo Extended 0.167.0**: https://github.com/gohugoio/hugo/releases/tag/v0.167.0 에서 OS에 맞는 `hugo_extended_0.167.0_*` 파일을 받아 압축을 풀고 PATH에 추가합니다.
**Dart Sass 1.105.0**: https://github.com/sass/dart-sass/releases/tag/1.105.0 에서 받은 뒤 압축을 풀고 `dart-sass` 폴더를 PATH에 추가합니다.

Linux 예시 (Actions와 같은 방식):

```bash
mkdir -p ~/tools && cd ~/tools
curl -fL https://github.com/gohugoio/hugo/releases/download/v0.167.0/hugo_extended_0.167.0_linux-amd64.tar.gz | tar -xz
curl -fL https://github.com/sass/dart-sass/releases/download/1.105.0/dart-sass-1.105.0-linux-x64.tar.gz | tar -xz
export PATH="$HOME/tools:$HOME/tools/dart-sass:$PATH"
```

설치 확인:

```bash
hugo version      # hugo v0.167.0-...+extended 가 나와야 함
sass --version    # 1.105.0
python --version
node --version
```

### 2-3. 복제와 빌드 (배포와 같은 결과물 만들기)

명령은 반드시 **저장소 최상위 폴더**에서 실행합니다. 스크립트가 현재 폴더 기준으로 `.github-pages.json`과 `site/`를 찾습니다.

```bash
git clone https://github.com/vovoring/BP_Assignment_01.git
cd BP_Assignment_01

python .github/pages_build.py --dependencies   # 빌드 보조 도구 설치
python .github/pages_build.py --build          # public/ 폴더에 사이트 생성
```

성공하면 마지막 줄에 `빌드 완료. 프로젝트 주소 경로 보정: N 개 파일`이 출력되고 `public/index.html`이 생깁니다.
favicon 누락이나 `languageCode deprecated` 같은 `WARN` 메시지가 나와도 빌드 결과에는 영향이 없습니다.
`public/`과 `.pages-tools/`는 `.gitignore`에 포함되어 있으므로 커밋하지 않습니다.

### 2-4. 브라우저에서 확인

**방법 A: 빠른 미리보기 (글 수정 시 자동 새로고침)**

```bash
hugo server --source site --config pages.json
```

http://localhost:1313/BP_Assignment_01/ 에 접속합니다.
※ About의 포트폴리오 이미지와 「산출물과 결과 설명」 글의 영상은 `../static/...` 상대경로로 연결되어 있습니다. 이 경로는 `--build` 단계에서만 보정되기 때문에 방법 A에서는 깨져 보일 수 있습니다.

**방법 B: 실제 배포본과 같은 화면 확인**

```bash
# macOS / Linux
SITE_BASE_URL=http://localhost:8000/ python .github/pages_build.py --build
cd public && python -m http.server 8000
```

```powershell
# Windows PowerShell
$env:SITE_BASE_URL="http://localhost:8000/"; python .github/pages_build.py --build
cd public; python -m http.server 8000
```

http://localhost:8000/ 에 접속합니다. 확인이 끝나면 `Ctrl+C`로 서버를 종료합니다.

---

## 3. 수정 위치와 재배포 방법

### 3-1. 수정할 파일 위치

| 바꾸고 싶은 것 | 파일 |
| --- | --- |
| 사이트 제목, 부제, 상단 메뉴(Posts·About·Game), 소셜 링크, 색상 | `site/pages.json` |
| 홈 문구 | `site/content/_index.md` |
| 자기소개·동아리 포트폴리오 | `site/content/about.md` |
| 게시글 9개 (관심 분야, 학습 목표, 역량, 대표 활동 등) | `site/content/posts/*.md` |
| 공룡 점프 미니게임 (Game 메뉴) | `site/content/game.md` |
| 이미지·영상 | `site/static/images/portfolio.png`, `site/static/images/Project.mp4` |
| 다크/라이트 전환, `?` 조작법 안내, `<- esc` 버튼, 키보드 탐색, 포커스 표시, Sticky Footer | `site/themes/template/layouts/_partials/extra-foot.html` |
| 게시글 좌우 방향키(←/→) 이동 | `site/themes/template/assets/js/main.js` |
| 게시글 메타 정보·이전/다음 화살표 스타일 | `site/themes/template/assets/scss/style.scss` |
| 게시글 상단 메타 영역 / 이전·다음 글 버튼 | `site/themes/template/layouts/_partials/posts_single_info.html`, `site/themes/template/layouts/posts/single.html` |
| 아이콘(전북대 학사모 `school` 아이콘 포함) | `site/themes/template/layouts/_partials/svg.html` |

새 글을 추가할 때는 `site/content/posts/`에 `.md` 파일을 만들고, 기존 글처럼 머리말(front matter)을 넣습니다.

```markdown
---
title: 글 제목
date: 2026-11-05
draft: false        # true면 공개 사이트에 나오지 않음
description: "목록에 표시될 설명"
tags:
 - "Game develop"
categories:
 - "Project"
---
본문...
```

`.github/pages_build.py`, `prepare_github_pages.py`, `.github/workflows/hugo-pages.yml`은 배포용 수업 제공 파일이므로 수정하지 않습니다.

### 3-2. 커밋·push → Actions → 공개 화면 확인

1. 파일을 수정한 뒤 `python .github/pages_build.py --build`로 오류가 없는지 확인합니다.
2. `main` 브랜치에 커밋하고 push합니다.
   ```bash
   git add .
   git commit -m "수정 내용 요약"
   git push origin main
   ```
3. push하면 **Deploy Hugo to GitHub Pages** 워크플로가 자동으로 실행됩니다.
4. 저장소의 **Actions** 탭(https://github.com/vovoring/BP_Assignment_01/actions)에서 최신 실행을 열고, `build`와 `deploy` 작업이 모두 초록색 체크인지 확인합니다.
   - 빨간 X가 표시되면 해당 단계 로그를 열어 오류를 확인하고, 고친 뒤 다시 push합니다.
   - 코드를 바꾸지 않고 다시 배포하려면 Actions → *Deploy Hugo to GitHub Pages* → **Run workflow**를 누릅니다.
5. 로그인하지 않은 브라우저(시크릿 창)에서 https://vovoring.github.io/BP_Assignment_01/ 을 열어 강력 새로고침(`Ctrl+F5`)한 뒤 수정 내용이 반영되었는지 확인합니다.

> 최초 1회 설정: Settings → Pages → Build and deployment → Source를 **GitHub Actions**로 선택해야 합니다.

---

## 4. 사용 자료 출처와 직접 제작·AI 생성 구분

| 구분 | 위치 | 출처 / 내용 | 이용 조건 |
| --- | --- | --- | --- |
| 외부 · 테마 원본 | `site/themes/template/` | Hugo 테마 **Hermit-V2** — https://github.com/1bl4z3r/hermit-V2 (원작 Track3/hermit 기반) | MIT License, 원문은 루트 `LICENSE`에 보존 |
| 외부 · 테마 포함 라이브러리 | `site/themes/template/assets/scss/_normalize.scss`, `_animate.scss`, `assets/js/code-copy.js`, `layouts/_partials/svg.html`의 아이콘 | normalize.css, animate.css, Feather Icons 등 Hermit-V2에 포함된 자료 | 각 원본 라이선스, 테마 안의 출처 표기 유지 |
| 외부 · 생성 도구 | Hugo, Dart Sass (빌드할 때만 사용) | https://gohugo.io/ , https://sass-lang.com/dart-sass/ | 각 도구 라이선스 |
| 수업 제공 | `prepare_github_pages.py`, `.github/pages_build.py`, `.github/workflows/hugo-pages.yml`, `.github-pages.json` | 초급프로젝트 수업 제공 GitHub Pages 배포 준비 스크립트 v3.1 | 과제 수행용, 수정하지 않음 |
| 직접 제작 | `site/content/_index.md`, `about.md`, `posts/*.md`(9개) | 자기소개·관심 분야·학습 목표·대표 활동 글 전체를 직접 작성 | 저작권: 본인 |
| 직접 제작 | `site/static/images/portfolio.png`, YouTube 영상 https://youtu.be/SlFyMldFBJs | 동아리(GPU) 입부용으로 직접 만든 Unreal Engine 점프맵 캡처와 플레이 영상 | 저작권: 본인 |
| 직접 제작 | `site/static/images/Project.mp4` | 대표 활동(*Lost Paradise* 괴물 AI) 중간 결과 영상 | 저작권: 본인 |
| 직접 설정 | `site/pages.json` | 사이트 제목·메뉴·소셜 링크·색상 설정 | — |
| AI 생성·보조 후 본인 검증 | `site/content/game.md` | 캔버스 공룡 점프 미니게임, 모바일 터치 버튼, 60FPS 고정, 최고 점수 `localStorage` 저장 | 본인 동작 검증 후 적용 |
| AI 생성·보조 후 본인 검증 | `site/themes/template/layouts/_partials/extra-foot.html` (새 파일) | 테마 전환, 조작법 모달, esc 버튼, 키보드 탐색, 포커스 표시, 겹침 방지 여백, Sticky Footer | 본인 동작 검증 후 적용 |
| AI 보조로 테마 수정 | `assets/js/main.js`, `assets/scss/style.scss`, `layouts/_partials/posts_single_info.html`, `layouts/posts/single.html`, `layouts/_partials/svg.html`(`school` 아이콘), `layouts/_partials/header.html`·`social-icons.html`(`aria-label` 추가) | 방향키 이동, 메타·화살표 스타일, 아이콘, 접근성 이름 | 원본은 MIT, 수정 부분은 본인 검증 |

## 5. 라이선스 고지

- 기반 테마 Hermit-V2는 **MIT License**(Copyright (c) 2018-present, The Hermit-V2 Authors)로 배포됩니다. 원문은 루트 [`LICENSE`](./LICENSE)에 그대로 보존합니다.
- 공개 사이트 하단의 `Made with Hugo & Hermit-V2` 출처 표기(`footer.html`)를 유지합니다.
- 본인이 직접 쓴 글과 직접 만든 이미지·영상의 저작권은 본인에게 있으며, MIT 라이선스 적용 대상이 아닙니다.
