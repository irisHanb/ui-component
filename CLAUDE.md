# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

> 이 저장소의 작업 지침은 한글로 작성한다. 코드 식별자·기술 용어는 원문 그대로
> 둔다.

## 프로젝트 개요

CareerFoundry의
[32 UI Elements 글로서리](https://careerfoundry.com/en/blog/ui-design/ui-element-glossary/)를
기반으로 한 **프런트엔드 UI 컴포넌트 레퍼런스 / 플레이그라운드**다. 최종
산출물은 의존성 없이 브라우저에서 바로 열리는 **단일 self-contained HTML
페이지**다.

목표 UX(요구 사항):

- **좌측**: UI 요소 리스트 + UX 공통 오버레이
  컴포넌트(모달·확인·경고·알림·토스트·스낵바·바텀 시트 등 레이어 처리 요소)
- **우측 메인**: 좌측에서 클릭한 항목의 **이름 + 정의/설명**, 그 아래 **Preview
  / HTML / CSS / JS** 탭
- 각 코드 영역에 **구문 하이라이트**와 **복사 버튼**(붙여넣어 바로 동작하는
  코드)

정본 빌드 타깃 파일명은 `ui-kit-playground.html`이다(저장소 루트에 생성).
`docs/resource/`의 파일들은 빌드 결과가 아니라 **참고용 레퍼런스**다.

## 실행 / 미리보기

빌드·번들·패키지 매니저가 **없다**(`package.json`·`node_modules`·테스트·린트
없음). 순수 HTML/CSS/vanilla JS다.

```bash
# 정적 서버로 미리보기 (clipboard API 등은 file:// 보다 http:// 에서 안정적)
python3 -m http.server 8000
# → http://localhost:8000/ui-kit-playground.html

# 또는 단일 파일을 바로 브라우저로 열기
open docs/resource/ui-components-32-catalog.html
```

## 아키텍처 — 핵심 데이터 모델

정본 참고 구조는
[docs/resource/ui-components-32-catalog.html](docs/resource/ui-components-32-catalog.html)다.
모든 컴포넌트를 **하나의 데이터 배열**로 정의하고 JS가 카드/패널로 렌더링하는
data-driven 방식이며, 새 빌드도 이 모델을 따른다:

```js
const components = [
  {
    id,
    num,
    name,
    nameKo,
    cat, // cat: 'input' | 'nav' | 'info' | 'container'
    desc, // 정의·설명 (우측 메인 상단)
    usecase, // 사용 사례
    feature, // 핵심 기능 요약
    html,
    css,
    js,
  }, // 탭에 그대로 들어가는 소스 문자열
];
```

설계 원칙:

- **CSS는 self-contained 시맨틱 클래스**(`.accordion`, `.btn`, `.modal-box` …)로
  작성한다. 학습자가 탭의 코드를 복사해 다른 페이지에 붙이면 그대로 동작하는
  이식성이 목적이다. Tailwind 유틸리티 클래스 의존(아래 `ui-component.html`
  방식)은 **지양**한다 — 복사·이식성이 떨어진다.
- 탭 전환은 패널의 `display` 토글, 복사 버튼은 해당 컴포넌트의 소스 문자열을
  `navigator.clipboard.writeText()`로 복사하고 "복사됨" 상태를 잠깐 표시한다.
- 카테고리 분류 4종: **Input Controls / Navigational / Informational /
  Containers**.
- **미리보기에 iframe을 쓰지 않는다**(아래 `component-portfolio.html`의 iframe
  `srcdoc` 방식은 따르지 않음). 페이지 안 `.stage`에 직접 그리고,
  `runPreview()`가 컴포넌트 JS를 `new Function`으로 실행하면서
  `document`·`setTimeout`·`setInterval`을 감싸 조회 범위를 `.stage`로 좁히고
  리스너·타이머를 컴포넌트 전환 시 해제한다. `.stage`의 `contain: layout`이
  `position: fixed` 요소를 영역 안에 가둔다. `<dialog>`의 `showModal()`은
  미리보기에서만 `openInStage()`로 바꿔 끼워 `.stage` 안에 띄운다(배경·바깥
  클릭·Esc·`inert`를 흉내 냄). 복사용 코드는 그대로 `showModal()`을 쓴다.
- **디자인 토큰은 `label-chip-system.html` 기준**이다(아래 portfolio의 `#2563EB`가
  아님). 액센트 1색은 sage — 글자·채움 `#527D6F`(흰 글자 대비 4.64), hover·연한
  배경 위 글자 `#456A5E`, 테두리·선택 막대 `#629584`, 연한 배경 `#E8F5F0`. 텍스트
  `#1E2D3D`/`#4A5C6E`/`#5F6E7E`, 테두리 `#E2E6EC`/`#CDD3DC`, 페이지 `#F0F2F5`.
  제목 Plus Jakarta Sans. 칩은 sm 20 / md 24 / lg 28px, 라벨 색은 COLOR.json 17색.
  단 흰 글자가 AA 미달인 5종은 배경을 유지하고 같은 색상 계열의 어두운 글자로 바꿨다:
  frontend `#001029`(5.05), backend `#002910`(7.15), design `#170029`(4.77),
  bug `#280103`(5.03), magenta `#290020`(5.84). 17종 모두 4.5:1 이상.
- **상태·메시지에 따라 내용 길이가 바뀌는 컴포넌트는 루트에 너비를 지정한다**
  (`width` + `max-width`). 지정하지 않으면 flex 컨테이너(`.stage`, 사용자 페이지의
  flex·grid) 안에서 내용 길이만큼 줄어 펼침·메시지 변경 때 폭이 흔들린다. 예:
  Accordion `width:100%; max-width:360px`, Toast `300px`, Snackbar `320px`. 메시지
  칸은 `flex:1`로 남는 폭을 채워 닫기·실행 취소 버튼을 오른쪽 끝에 고정한다.
- **목록형 컴포넌트 공통 규칙**(Accordion·Dropdown·Kebab·Meatballs·Hamburger·
  Döner·Bento·Sidebar·Tab Bar·Notification Center·Feed·Comment·Card·Search):
  패널 흰 배경 + `1px #E2E6EC` + radius 8px(떠 있는 패널만 중성 그림자 1단계),
  항목 13px `#1E2D3D`(메뉴 항목 radius 6px), hover `#F6F7F9`, 선택·현재·펼침은
  배경 `#E8F5F0` + 글자 `#456A5E` + 600, 메타(시간 등) 12px `#5F6E7E`, 섹션
  라벨 11px 600 `#5F6E7E`, 위험 항목 `#DC2626`, 포커스
  `outline: 2px solid #629584`. 카테고리 칩은 chip-sm(20px·10px·600·radius 4px)에
  COLOR.json의 어두운 글자 프리셋(periwinkle·mint·rose·sky·lime 등, 대비
  6.8~9.3)만 쓴다. `#8A96A4`는 대비 3.01이라 글자에 쓰지 않고 아이콘·구분선에만
  쓴다.
- **화면 패턴(`cat: 'pattern'`)**: 기존 컴포넌트를 조합한 화면 단위 예제. #45 소셜
  로그인, #46 회원가입 폼. 입력값은 서버로 보내지 않고 `setTimeout` 1초로 요청을
  흉내 낸다. 소셜 로그인은 화면만 제공하며 실제 OAuth 인가 URL은 서버에서 만든다
  (코드 주석 참고). 폼 필드에 `name="name"`을 쓰지 않는다 — `form.name`이 폼 자체의
  name 속성을 돌려줘 입력 요소를 가린다.
- **브랜드 색 예외**: 소셜 로그인 버튼만 각 사 가이드라인 색·로고를 쓴다(Kakao
  `#FEE500` + 85% 검정 글자, Naver `#03C75A` + 검정 글자·로고, Google 흰 배경 +
  `#747775` 테두리 + 4색 G, Apple 검정). 그 밖의 요소는 sage 액센트 1색 규칙을 따른다.
  가이드라인 색이 AA(4.5:1)에 못 미치면 접근성을 우선한다 — Naver 흰 글자(2.25)를
  검정(9.32)으로 바꾼 것이 그 예다.
  로고 출처: simple-icons 16.35.0(Kakao 말풍선·Naver·Apple), devicon 2.17.0
  `google-original`.

레이아웃 패러다임은 두 가지 레퍼런스가 다른데, **요구되는 형태는 사이드바 + 단일
메인 영역**(좌측 리스트 클릭 → 우측 메인 재렌더)이다. 즉 **카탈로그의 깔끔한
데이터 모델 + 사이드바 레이아웃**을 결합한다.

## 32개 컴포넌트 + 공통 오버레이 범위

`docs/resource/ui-components-32-catalog.html`에 32종이 카테고리와 함께 모두
정의돼 있다(Accordion·Button·Card·Modal·Dropdown·Toggle … cat:
input/nav/info/container). 좌측 리스트에는 이 32종에 더해
`alert-ui-showcase.html`의 **오버레이/알림 패턴**(Toast/Snackbar·Top
Banner·Inline Alert·Slide-over·Popover·Full-screen Takeover·Command
Bar·Notification List 등)을 공통 컴포넌트로 추가한다.

## 리소스 파일별 역할 (`docs/resource/`)

| 파일                                                                    | 무엇을 취할지                                                                                                                                                                        |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `ui-components-32-catalog.html`                                         | **정본 데이터 모델·코드·분류.** 32종 정의와 self-contained CSS의 출처.                                                                                                               |
| `ui-component-demo.html`                                                | 사이드바+메인 레이아웃 아이디어. 단 **Tailwind CDN + Lucide 아이콘 + `script:()=>{}` 패턴**이라 코드 이식성은 떨어짐 — 구조만 참고.                                                  |
| `alert-ui-showcase.html`                                                | 오버레이/알림 **동작 패턴**의 출처(`demos` 객체: render/fire/code). ⚠️ 시각 스타일은 그라데이션·글로우·backdrop-blur를 써서 아래 디자인 규칙을 **위반**하므로 비주얼은 베끼지 말 것. |
| `label-chip-system.html`                                                | 라벨/칩 시스템 — 17색 팔레트, 크기·상태, 우선순위 배지/Select, COLOR.json 토큰.                                                                                                      |
| `colorpicker.html`, `color-personal-card.html`, `ui-elements-demo.html` | 보조 데모.                                                                                                                                                                           |
| `component-portfolio.html`                                              | **요구 사양에 가장 근접한 정본 prototype.** 32종 데이터 모델(`ELEMENTS =                                                                                                             |

[{n,name,ko,cat,desc,html,css,js},…]`)을 카테고리 그룹 사이드바 + 우측 메인(이름·한글명·뱃지·설명 +   Preview/HTML/CSS/JS 탭)으로 렌더한다. 미리보기는 iframe `srcdoc`으로 컴포넌트 CSS를 페이지와 격리하고, 복사   버튼은 `navigator.clipboard.writeText()`+ "복사됨 ✓" 토스트로 동작한다. 폰트는 Noto Sans KR(본문) /   JetBrains Mono(코드), 무채색 +`#2563EB`
단일 액센트로 anti-ai-slop 규칙을 준수한다.

**`ui-kit-playground.html` 빌드는 이 파일의 구조·코드를 시작점으로 삼고, 좌측
리스트에 오버레이/알림 패턴만 추가한다.** |

## 강제 디자인 규칙 — anti-ai-slop

[.claude/rules/anti-ai-slop.md](.claude/rules/anti-ai-slop.md)는 이 저장소의
모든 이미지·HTML·SVG·슬라이드 생성에 적용되는 **강제 규칙**이며 기본 동작을
override한다. 요지:

- **금지**: 그라데이션 일체, 색 들어간 그림자·글로우·`blur≥20px`,
  backdrop-filter 글래스모피즘, hover/load 시 transform·fade·키프레임 장식 모션,
  배경 워터마크/그리드/광선, 카드 상단 컬러 액센트 바, 이모지 불릿, 마케팅
  상투어.
- **강제**: 무채색 베이스 + 액센트 1색(색은 의미에만), 그림자는 중성 회색
  1단계(`0 1px 2px rgba(0,0,0,.06)`)만, 구획은 `1px solid border`+여백으로,
  `border-radius` 0~8px, 위계는 크기·굵기·여백·정렬로, 폰트는
  기본값(Inter/Roboto/Arial/system) 수렴 금지하고 목적에 맞게 의도적으로 고르고
  이유를 한 줄 밝힘.

**주의**: `alert-ui-showcase.html`과 카탈로그 일부(헤더 숫자·카드 이미지의
`linear-gradient`)는 이 규칙 이전 자료라 위반 요소가 있다. 동작·구조는 참고하되
**비주얼은 규칙에 맞게 다시 작성**한다. 출력 전 `anti-ai-slop.md`의 자가 점검
체크리스트(그라데이션/컬러 그림자/장식 모션/배경 장식 등)를 통과시킨다.

## 프롬프트 기록 훅

`.claude/settings.local.json`의 **Stop 훅**이 응답이 끝날 때마다
`extract-my-prompts.sh`를 인자 없이 실행한다. 스크립트는
`~/.claude/projects/` 아래 세션 jsonl 중 이 폴더(`cwd`)의 최신 세션에서 사용자가
입력한 프롬프트만 뽑아 실행 위치의 [my-prompts.md](my-prompts.md)를 **덮어쓴다**.
`settings.local.json`은 개인/머신 전용이라 `.gitignore` 처리돼 있다(커밋되지
않음). 훅 command가 절대 경로라 다른 머신에서는 경로를 고쳐야 한다.

기록에서 빼는 것(`extract-my-prompts.sh`의 추출 단계):

| 대상 | 처리 |
| --- | --- |
| 도구 출력·메타 메시지, `Caveat:` 래퍼 | 메시지 전체 제외 |
| `<system-reminder`·`<local-command`·`<command-`·`<bash-input`·`<bash-stdout`로 시작하는 주입 | 메시지 전체 제외 |
| `<ide_…>…</ide_…>`, `<browser_instruction>…</browser_instruction>` 블록 | 블록만 잘라 내고 나머지 입력은 유지 |
| git 동작만 지시한 짧은 입력("commit", "commit, push 해", "make pr to main", "새 브랜치 만들어서 commit" 등) | `is_git_only()`: 모든 단어가 git 동작어·보조어이고 동작어가 1개 이상, 12단어 이하일 때 제외. 다른 요청이 섞이면 유지 |

`Prompt.md`에 전 세션을 이어붙이려면 훅 command를
`extract-my-prompts.sh --append -o Prompt.md`로 바꾼다. `--append`는
`Prompt.md.state`에 마지막 기록 시각을 저장하고 그 이후 프롬프트만 추가한다.
`my-prompts.md`·`Prompt.md`·`Prompt.md.state`는 스크립트가 만드는 파일이라 직접
편집하지 않는다. 저장소가 public이라 커밋하면 프롬프트 기록이 공개된다.
