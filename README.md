# UI Kit Playground

프런트엔드 개발에 필요한 UI 컴포넌트 46종을 한 페이지에서 보고, 코드를 복사해 쓸 수 있는 레퍼런스 사이트입니다.
CareerFoundry의 [32 UI Elements 글로서리](https://careerfoundry.com/en/blog/ui-design/ui-element-glossary/)에 나오는 32종에, 사이트에서 자주 쓰는 알림·레이어 컴포넌트 12종과 이를 조합한 화면 패턴 2종(소셜 로그인, 회원가입)을 더했습니다.

**바로 보기: [https://irishanb.github.io/ui-component/](https://irishanb.github.io/ui-component/)** (GitHub Pages)

## 기능

| 영역 | 내용 |
|---|---|
| 좌측 목록 | 이름 검색(영문·한글·번호), 분류 필터 칩, 번호순 목록 |
| 우측 상세 | 컴포넌트 이름·정의, 사용 사례, 핵심 기능 |
| 미리보기 탭 | 실제로 클릭해 볼 수 있는 동작 예제 |
| HTML / CSS / JS 탭 | 구문 하이라이트된 소스와 복사 버튼 |
| 주소 | `#id`로 특정 컴포넌트를 바로 엽니다. 예: `#accordion`, `#toast` |

복사한 코드는 외부 라이브러리 없이 동작합니다. HTML·CSS·JS 세 탭의 코드를 각각 자기 페이지에 붙여 넣으면 미리보기와 같은 동작을 합니다.

## 컴포넌트 목록

| 분류 | 개수 | 컴포넌트 |
|---|---|---|
| 입력 컨트롤 | 11 | Button, Checkbox, Dropdown, Form, Input Field, Picker, Radio Buttons, Search Field, Slider Controls, Stepper, Toggle |
| 내비게이션 | 9 | Bento Menu, Breadcrumb, Döner Menu, Hamburger Menu, Kebab Menu, Meatballs Menu, Pagination, Sidebar, Tab Bar |
| 정보 표시 | 8 | Comment, Feed, Icon, Loader, Notification, Progress Bar, Tag, Tooltip |
| 컨테이너 | 4 | Accordion, Card, Carousel, Modal |
| 공통 레이어 | 12 | Alert, Confirm, Notification Center, Toast, Snackbar, Bottom Sheet, Top Banner, Inline Alert, Slide-over, Popover, Full-screen Takeover, Command Bar |
| 화면 패턴 | 2 | Social Login(카카오·네이버·Google·Apple + 이메일 로그인), Sign-up Form(비밀번호 강도·약관 동의·중복 이메일 오류) |

화면 패턴은 화면만 제공합니다. 입력값은 서버로 보내지 않으며, 실제 소셜 로그인(OAuth)은 서버에서 인가 URL을 만들어 연결해야 합니다.

## 실행 방법

빌드 도구와 패키지 설치가 필요 없습니다. `ui-kit-playground.html` 파일 하나로 동작합니다.

### 로컬 서버로 열기 (권장)

```bash
git clone https://github.com/irisHanb/ui-component.git
cd ui-component
python3 -m http.server 8000
```

브라우저에서 http://localhost:8000/ui-kit-playground.html 을 엽니다. 서버는 터미널에서 `Ctrl+C`로 종료합니다.

### 파일로 바로 열기

```bash
open ui-kit-playground.html        # macOS
start ui-kit-playground.html       # Windows
```

`file://` 주소에서는 브라우저에 따라 복사 버튼의 Clipboard API가 막힐 수 있습니다. 막히면 코드 영역이 선택되고 "선택됨 · Ctrl+C"가 표시되므로, 그 상태에서 복사 단축키를 누르면 됩니다.

## 배포

`main` 브랜치에 push하면 GitHub Actions가 GitHub Pages에 자동으로 배포합니다.

| 주소 | 내용 |
|---|---|
| [https://irishanb.github.io/ui-component/](https://irishanb.github.io/ui-component/) | 사이트 첫 화면(Accordion) |
| [https://irishanb.github.io/ui-component/ui-kit-playground.html](https://irishanb.github.io/ui-component/ui-kit-playground.html) | 같은 페이지의 원래 파일명 주소 |
| [https://irishanb.github.io/ui-component/#toast](https://irishanb.github.io/ui-component/#toast) | `#id`를 붙이면 해당 컴포넌트가 바로 열립니다 |

- 워크플로: [.github/workflows/deploy-pages.yml](.github/workflows/deploy-pages.yml)
- 사이트에는 `ui-kit-playground.html` 하나만 올라갑니다(`index.html`로도 제공). 저장소의 다른 파일은 사이트에 포함되지 않습니다.
- 배포 결과는 저장소의 Actions 탭에서 확인합니다.

## 저장소 구성

| 경로 | 내용 |
|---|---|
| `ui-kit-playground.html` | 사이트 전체. 컴포넌트 데이터, 렌더링, 미리보기, 하이라이터가 모두 이 파일에 있습니다. |
| `docs/resource/` | 제작에 참고한 HTML 자료. 사이트 빌드에는 쓰이지 않습니다. |
| `.github/workflows/` | GitHub Pages 자동 배포 워크플로 |
| `CLAUDE.md` | 코드 구조, 설계 원칙, 디자인 토큰 등 작업 지침 |
| `.claude/rules/anti-ai-slop.md` | 디자인 규칙(그라데이션·장식 모션 금지, 액센트 1색 등) |

## 디자인

- 색: Polarize 10색 팔레트. 액센트는 파랑 `#25539B` 1색, 페이지 바탕은 무채색 `#F0F2F5`, 빨강·주황·호박·민트는 위험·경고·주의·성공 표시에만 씁니다. 모든 글자는 배경 대비 4.5:1 이상입니다.
- 폰트: 제목 Plus Jakarta Sans, 본문 Noto Sans KR, 코드 JetBrains Mono
- 아이콘: [Lucide](https://lucide.dev/) 인라인 SVG(`lucide-static@0.460.0`)
- 라벨 칩: 팔레트 10색, 크기 sm 20px / md 24px / lg 28px

자세한 값과 규칙은 [CLAUDE.md](CLAUDE.md)에 있습니다.
