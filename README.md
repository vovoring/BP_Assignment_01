# LEO's Homepage — 개인 소개 및 포트폴리오 웹사이트

전북대학교 컴퓨터인공지능학부 재학생 **LEO**의 개인 소개 및 게임 개발 활동을 정리한 포트폴리오 웹사이트 저장소입니다.  
Hugo의 **Hermit-V2** 테마를 기반으로 구축되었으며, GitHub Actions를 통해 GitHub Pages로 자동 배포됩니다.

- **공개 사이트 주소**: [https://vovoring.github.io/BP\_Assignment\_01/](https://www.google.com/search?q=https://vovoring.github.io/BP_Assignment_01/)
- **GitHub 저장소 주소**: [https://github.com/vovoring/BP\_Assignment\_01](https://www.google.com/search?q=https://github.com/vovoring/BP_Assignment_01)
- **GitHub Actions 배포 현황**: [https://github.com/vovoring/BP\_Assignment\_01/actions](https://www.google.com/search?q=https://github.com/vovoring/BP_Assignment_01/actions)

## 1. 사이트 주요 구성 및 핵심 기능

| 메뉴 / 기능 | 경로 / 위치 | 주요 내용 및 동작 설명 |
| :--- | :--- | :--- |
| **홈 화면** | `/` (`site/content/_index.md`) | 사이트 제목(`LEO's homepage`), 타이프라이터 부제, 소셜 아이콘(GitHub·이메일·전북대 홈페이지) 및 주요 메뉴 연결 |
| **Posts** | `/posts/` (`site/content/posts/*.md`) | 관심 분야, 학습 목표, 역량, 대표 활동(`Lost Paradise` 괴물 AI 개발) 과정과 결과를 정리한 9개 게시글 목록 |
| **About** | `/about/` (`site/content/about.md`) | 자기소개 및 게임 개발 동아리(GPU) 입부 당시 제작한 언리얼 엔진 점프맵 포트폴리오 이미지·시연 영상 링크 |
| **Game** | `/game/` (`site/content/game.md`) | 키보드(`Space`, `↑`, `↓`) 및 모바일 터치 버튼(`▲ Jump`, `▼ Down`)으로 플레이 가능한 캔버스 기반 공룡 점프 미니게임 |
| **전역 편의·접근성 UI** | 전 페이지 공통 (`extra-foot.html`) | 다크/라이트 테마 전환 버튼, `? 조작법` 단축키 안내 모달, `<- esc` 상위 페이지 복귀 버튼, 방향키(`↑`/`↓`/`←`/`→`) 메뉴·글 탐색 |

## 2. 실행 환경 및 로컬 빌드·실행 방법 (P56)

이 저장소는 수업 제공 준비 스크립트(`prepare_github_pages.py`)를 통해 실제 Hugo 사이트 파일들이 최상위 루트가 아닌 **`site/` 폴더 내부**에 구성되어 있습니다. 로컬 PC에서 사이트를 동일하게 빌드하거나 실행하려면 아래 도구 버전과 명령어를 사용합니다.

### 필요한 도구 및 권장 버전

| 도구명 | 버전 | 용도 및 비고 |
| :--- | :--- | :--- |
| **Python** | `3.12` (최소 `3.9` 이상) | 배포 준비 및 경로 보정 스크립트(`.github/pages_build.py`) 실행 |
| **Hugo Extended** | `0.167.0` (`v0.146.0` 이상) | SCSS 컴파일을 위해 일반 버전이 아닌 **Extended** 버전 필수 |
| **Dart Sass** | `1.105.0` | 테마의 SCSS 스타일시트 빌드 |
| **Git** | 최신 버전 | 저장소 클론 및 버전 관리 |

### 설치 및 재현 명령어

저장소 최상위 폴더(`BP_Assignment_01`)에서 터미널을 열고 아래 순서대로 실행합니다.

**1단계: 저장소 복제 및 폴더 이동**

```bash
git clone https://github.com/vovoring/BP_Assignment_01.git
cd BP_Assignment_01
```

**2단계: GitHub Pages 배포와 동일한 정적 빌드 실행 (`public/` 폴더 생성)**

```bash
python .github/pages_build.py --dependencies
python .github/pages_build.py --build
```

위 명령을 실행하면 `site/pages.json` 설정을 읽어 `public/` 폴더에 최종 배포용 HTML·CSS·JS 파일이 생성되고 하위 경로(`/BP_Assignment_01/`)가 자동 보정됩니다.

**3단계: 로컬 개발 서버로 화면 바로 확인하기**

```bash
hugo server --source site --config pages.json --themesDir themes --theme template -D
```

명령 실행 후 웹 브라우저에서 `http://localhost:1313/BP_Assignment_01/` 주소로 접속하면 로컬에서 사이트를 바로 확인할 수 있습니다.

## 3. 파일 수정 위치 및 재배포·확인 방법 (P57)

사이트의 설정이나 본문 글, 추가 기능을 수정할 때는 반드시 **`site/` 폴더 아래의 파일**을 수정해야 배포 화면에 반영됩니다.

### 수정할 파일 위치 안내

| 수정 대상 | 파일 상대 경로 | 역할 및 주요 수정 내용 |
| :--- | :--- | :--- |
| **사이트 기본 설정·메뉴** | `site/pages.json` | 사이트 제목, `baseURL`, 한국어(`ko`/`ko-kr`), 상단 메뉴(`Posts`, `About`, `Game`), 소셜 링크 설정 |
| **홈·자기소개 본문** | `site/content/_index.md`<br>`site/content/about.md` | 홈 기본 문구, 학과·학년 소개, 동아리 입부 포트폴리오 사진 및 유튜브 영상 링크 |
| **개인 소개·활동 게시글** | `site/content/posts/*.md` | 관심 분야·학습 목표·역량·대표 활동(`Lost Paradise`) 관련 마크다운 게시글 9개 |
| **본문 첨부 이미지** | `site/static/images/portfolio.png` | `About` 페이지에 표시되는 점프맵 포트폴리오 캡처 이미지 |
| **공룡 점프 미니게임** | `site/content/game.md` | 캔버스(`#dino-canvas`) 기반 미니게임 스타일, 모바일 터치 버튼, 60FPS 고정 게임 루프 스크립트 |
| **전역 UI·테마·키보드 탐색** | `site/themes/template/layouts/_partials/extra-foot.html` | 다크/라이트 모드 전환, `? 조작법` 모달, `<- esc` 상위 복귀 버튼, `↑`/`↓`/`Enter`/`ESC` 탐색, 포커스·반응형 여백(P33·P38·P43), Sticky Footer |
| **게시글 좌우 방향키 이동** | `site/assets/js/main.js`<br>`site/themes/template/assets/js/main.js` | 게시글 상세 화면에서 좌우 방향키(`←`/`→`)로 이전·다음 게시글로 이동하는 이벤트 리스너 |
| **게시글 메타·화살표 스타일** | `site/assets/scss/style.scss`<br>`site/themes/template/assets/scss/style.scss` | 게시글 메타 정보(`.post-meta-desc`, `.post-meta-item`) 여백 정돈 및 이전·다음 화살표(`.post-nav-label svg`) 크기 확대 |
| **게시글 상세 레이아웃·정보** | `site/themes/template/layouts/posts/single.html`<br>`site/themes/template/layouts/_partials/posts_single_info.html` | 이전·다음 글 버튼에 화살표 아이콘 적용 및 상단 메타 영역(설명·태그·카테고리·단어 수·날짜) 구조 개편 |
| **커스텀 SVG 아이콘** | `site/themes/template/layouts/_partials/svg.html` | 전북대학교 홈페이지 링크용 학사모(`school`) SVG 아이콘 추가 |

### 수정 후 자동 재배포 및 확인 절차

1. 위 경로의 파일을 수정하고 저장한 뒤, 로컬에서 `python .github/pages_build.py --build`를 실행해 빌드 오류가 없는지 먼저 점검합니다.
2. 아래 명령어로 변경 사항을 `main` 브랜치에 커밋하고 원격 저장소에 푸시(`push`)합니다.

```bash
git add .
git commit -m "수정 내용 요약 메시지"
git push origin main
```

3. `main` 브랜치에 `push`가 완료되면 `.github/workflows/hugo-pages.yml` 워크플로가 자동으로 실행됩니다.
4. GitHub 저장소의 **[Actions 탭](https://www.google.com/search?q=https://github.com/vovoring/BP_Assignment_01/actions)** 에서 가장 최근 실행된 `Deploy Hugo to GitHub Pages` 워크플로의 `build`와 `deploy` 작업이 모두 초록색 체크(성공)로 끝났는지 확인합니다.
5. 배포 완료 후 로그인하지 않은 브라우저(시크릿 창 등)에서 **[공개 사이트 주소](https://www.google.com/search?q=https://vovoring.github.io/BP_Assignment_01/)** 를 새로고침하여 수정한 내용이 정상 반영되었는지 확인합니다.

## 4. 사용 자료 출처·라이선스 및 직접 제작·AI 생성 구분 (P58)

이 프로젝트에 사용된 오픈소스 테마, 수업 제공 스크립트, 본인 직접 제작 콘텐츠, AI 생성·보조 코드의 파일별 구분과 출처·이용 조건은 다음과 같습니다.

| 구분 | 사용 위치 (파일 상대 경로) | 상세 내용 및 출처 / 사용 도구 | 라이선스 및 이용 조건 |
| :--- | :--- | :--- | :--- |
| **원본 테마 (외부)** | `site/themes/template/`<br>`site/assets/` | Hugo 테마 **Hermit-V2** ([1bl4z3r/hermit-V2](https://github.com/1bl4z3r/hermit-V2), 원작자 [Track3/hermit](https://github.com/Track3/hermit) 포크) | **MIT License** (`LICENSE` 및 `site/themes/template/LICENSE` 보존, 하단 푸터 `Made with Hugo & Hermit-V2` 출처 고지 유지) |
| **테마 내장 오픈소스 (외부)** | `site/assets/scss/_normalize.scss`<br>`site/assets/scss/_animate.scss`<br>`site/themes/template/layouts/_partials/svg.html`<br>`site/assets/js/code-copy.js` | [normalize.css](https://necolas.github.io/normalize.css/), [animate.css](https://daneden.github.io/animate.css/), [Feather Icons](https://feathericons.com/), [Tom Spencer code-copy.js](https://www.fiznool.com/blog/2018/09/14/adding-click-to-copy-buttons-to-a-hugo-powered-blog/) | **MIT License** (원본 테마에 포함된 오픈소스 라이선스 및 출처 유지) |
| **배포 준비 도구 (외부)** | `prepare_github_pages.py`<br>`.github/pages_build.py`<br>`.github/workflows/hugo-pages.yml` | 2026학년도 초급프로젝트 수업 제공 GitHub Pages 자동 배포 준비 스크립트 (`v3.1`) | 수업 과제 수행용 제공 스크립트 |
| **직접 제작 (본인)** | `site/content/about.md`<br>`site/content/posts/*.md` (총 9개 파일) | 본인 소개글, 관심 분야, 학습 목표, 역량, 대표 활동(`Lost Paradise` 괴물 AI 개발) 본문 텍스트 전체 직접 작성 | 본인 직접 작성 |
| **직접 제작 (본인)** | `site/static/images/portfolio.png`<br>[유튜브 시연 영상](https://youtu.be/SlFyMldFBJs) | 게임 개발 동아리(GPU) 입부 포트폴리오로 직접 제작한 언리얼 엔진 점프맵 플레이 영상 및 캡처 이미지 | 본인 직접 제작 및 촬영 |
| **AI 생성·보조 (AI)** | `site/themes/template/layouts/_partials/extra-foot.html` | 전 페이지 공통 추가 기능 구현: 다크/라이트 테마 전환 버튼 및 스타일(`html.light-theme`), 메인 화면 `? 조작법` 단축키 모달, 하위 페이지 `<- esc` 상위 복귀 버튼, 키보드(`↑`/`↓`/`Enter`/`ESC`) 메뉴·게시글 탐색, `:focus-visible` 초점 표시(P43), 반응형·스크롤 가림 방지 여백(P33·P38), 하단 Sticky Footer | AI 코드 생성·보조 후 본인 동작 검증·적용 |
| **AI 생성·보조 (AI)** | `site/content/game.md` | HTML5 `<canvas>` 기반 공룡 점프 미니게임 구현, 모바일/터치 전용 조작 버튼(`▲ Jump`, `▼ Down`), 고주사율 모니터 대응 60FPS 프레임 고정(`dt` 보정), 최고 점수 `localStorage` 저장 | AI 코드 생성·보조 후 본인 동작 검증·적용 |
| **AI 생성·보조 (AI)** | `site/assets/js/main.js`<br>`site/themes/template/assets/js/main.js` | 게시글 상세 페이지에서 키보드 좌우 방향키(`←`/`→`) 입력 시 이전·다음 게시글로 이동하는 단축키 기능 추가 | 원본 테마(MIT) 위 AI 보조 및 본인 검증 |
| **AI 생성·보조 (AI)** | `site/assets/scss/style.scss`<br>`site/themes/template/assets/scss/style.scss` | 게시글 상단 메타 정보(`.post-meta-desc`, `.post-meta-item`, `.post-meta-separator`) 레이아웃 스타일 추가 및 하단 이전·다음 글 화살표(`.post-nav-label svg`) 크기 확대 | 원본 테마(MIT) 위 AI 보조 및 본인 검증 |
| **AI 생성·보조 (AI)** | `site/themes/template/layouts/_partials/posts_single_info.html`<br>`site/themes/template/layouts/posts/single.html` | 게시글 상단 메타 영역(설명·작성자 숨김 처리·태그·카테고리·단어 수·날짜) 구조 정리 및 하단 이전·다음 글 네비게이션 화살표 아이콘 적용 | 원본 테마(MIT) 위 AI 보조 및 본인 검증 |
| **AI 생성·보조 (AI)** | `site/themes/template/layouts/_partials/svg.html`<br>`site/pages.json` | 전북대학교 홈페이지 링크용 학사모(`school`) SVG 아이콘 추가 및 `site/pages.json` 사이트 설정·메뉴·소셜 링크 구성 보조 | 원본 테마(MIT) 위 AI 보조 및 본인 설정·검증 |

## 5. 라이선스 고지 (License)

- 이 저장소의 기반 테마인 **Hermit-V2**는 **MIT License** 하에 배포되며, 원본 저작권 고지와 허가 조건은 루트의 `LICENSE` 및 `site/themes/template/LICENSE` 파일에 그대로 보존되어 있습니다.
- 웹사이트 공개 화면 하단 푸터에도 테마 제작자 및 도구 출처(`Made with Hugo & Hermit-V2`) 고지를 삭제하지 않고 유지합니다.