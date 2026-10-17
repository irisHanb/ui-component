# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

> 이 저장소의 작업 지침과 UI 문구는 한국어로 씁니다. 코드 식별자와 기술 용어는 원문 그대로 둡니다.

## 프로젝트 개요

CareerFoundry [32 UI Elements 글로서리](https://careerfoundry.com/en/blog/ui-design/ui-element-glossary/)를 기반으로 한 프런트엔드 UI 컴포넌트 레퍼런스 / 플레이그라운드입니다. 사용자는 각 컴포넌트의 코드를 복사해 자기 사이트에 붙여 넣습니다.

- **최종 산출물**: 저장소 루트의 `ui-kit-playground.html`. 빌드 없이 브라우저에서 바로 열리는 단일 self-contained HTML입니다(아직 없음).
- **좌측 사이드바**: 32개 UI 요소(카테고리별 그룹) + 공통 레이어 컴포넌트 그룹(모달·확인·경고·알림·토스트·스낵바·바텀 시트 등)
- **우측 메인**: 클릭한 항목의 이름 + 정의·설명 → Preview / HTML / CSS / JS 탭
- **코드 탭**: 구문 하이라이트, 탭마다 복사 버튼. JS가 없는 컴포넌트는 "필요 없음"을 표시합니다.

`docs/resource/`는 빌드 결과가 아니라 참고 자료입니다. 새 코드는 이 폴더 밖에 작성합니다.

## 실행 / 미리보기

빌드 도구, 패키지 매니저, 린터, 테스트가 없습니다. 순수 HTML/CSS/vanilla JS입니다.

```bash
# Clipboard API는 보안 컨텍스트가 필요하므로 file:// 대신 localhost에서 확인
python3 -m http.server 8000
# → http://localhost:8000/ui-kit-playground.html

# 참고 파일을 바로 열기
open docs/resource/component-portfolio.html

# 이 폴더 세션에서 사용자가 입력한 프롬프트만 추출 (python3 필요)
./extract-my-prompts.sh                     # 최신 세션 1개 → my-prompts.md (덮어쓰기)
./extract-my-prompts.sh --append -o Prompt.md   # 전 세션, 새 프롬프트만 이어붙임
./extract-my-prompts.sh --help
```

## 아키텍처 — 데이터 모델

시작점은 `docs/resource/component-portfolio.html`입니다. 요구 사양(카테고리 그룹 사이드바 + 우측 메인 + 4개 탭 + 복사)과 가장 가깝습니다. 이 파일의 구조를 가져오고, 데이터 필드는 `ui-components-32-catalog.html`의 `usecase`·`feature`를 더해 확장합니다.

```js
const COMPONENTS = [
  { n, name, ko, cat,   // cat: 'input' | 'nav' | 'info' | 'container' | 'layer'
    desc,               // 정의·설명 (우측 메인 상단)
    usecase,            // 사용 사례
    feature,            // 핵심 기능 요약
    html, css, js },    // 미리보기와 코드 탭이 같은 문자열을 씀
];
```

- **데이터와 렌더러를 분리합니다.** `select(n)`이 상세 화면·탭·복사 버튼을 다시 그립니다(`component-portfolio.html:1001`). 미리보기와 코드 탭이 같은 문자열을 쓰므로 복사한 코드와 보이는 동작이 일치합니다.
- **미리보기는 iframe으로 격리합니다.** `buildSrcdoc()`이 `css`·`html`·`js`를 한 문서로 합쳐 `iframe.srcdoc` 프로퍼티에 할당합니다(`component-portfolio.html:978-1030`). 레이어 컴포넌트의 `position: fixed`도 iframe 안에서만 덮습니다.
- **복사한 코드는 단독으로 동작해야 합니다.** CSS는 컴포넌트마다 고유 접두어를 붙인 시맨틱 클래스로 씁니다(`.acc__`, `.car__` 등). JS는 외부 라이브러리 없이 vanilla로 씁니다. Tailwind 유틸리티나 셸의 CSS 변수에 의존하지 않습니다.
- **복사 버튼**: `navigator.clipboard.writeText()`로 복사하고 "복사됨 ✓"을 1.2초 표시합니다. 실패하면 코드 영역을 `Range`로 선택하고 "선택됨 · Ctrl+C"를 표시합니다(`ui-elements-demo.html:3860-3868`).
- **하이라이트**: 산출물은 self-contained이므로 Prism 같은 외부 JS를 쓰지 않고 HTML/CSS/JS용 정규식 하이라이터를 인라인으로 둡니다. 토큰 분류는 `ui-elements-demo.html`의 Prism 토큰 색(`:340`)을 참고합니다.
- **JS 문자열 작성 시 주의**
  - 템플릿 리터럴 안의 `</script>`는 `<\/script>`로 씁니다.
  - 컴포넌트 코드 안의 백틱과 `${`는 escape합니다.
  - 코드 탭에 넣기 전에 `&`, `<`, `>`를 escape한 뒤 하이라이트합니다.

## 컴포넌트 범위

- **32개 요소**: `ui-components-32-catalog.html`과 `component-portfolio.html`에 카테고리와 함께 정의돼 있습니다. 카테고리는 Input Controls / Navigational / Informational / Containers 4종입니다.
- **공통 레이어(`cat: 'layer'`)**: 요구 항목은 Alert, Confirm, Notification, Toast, Snackbar, Bottom Sheet입니다. 동작 패턴은 `alert-ui-showcase.html`의 `demos` 객체(`render`/`fire`/`code`)를 참고합니다. 해당 파일에는 Top Banner, Inline Alert, Slide-over, Popover, Full-screen Takeover, Command Bar, Notification List 패턴도 있습니다.

## 리소스 파일별 역할 (`docs/resource/`)

| 파일 | 가져올 것 |
|---|---|
| `component-portfolio.html` | **시작점.** 레이아웃, `ELEMENTS` 데이터, `select()`·`buildSrcdoc()`·탭·복사 로직. Noto Sans KR(본문) / JetBrains Mono(코드), 무채색 + `#2563EB` 액센트 1색입니다. 디자인 규칙 위반 3건은 아래 디자인 절에 정리했습니다. |
| `ui-components-32-catalog.html` | 32종 정의의 `usecase`·`feature` 문구, 날짜 피커·폼 검증 같은 인터랙티브 데모. `linear-gradient` 16곳이 디자인 규칙을 위반하므로 비주얼은 다시 작성합니다. |
| `ui-elements-demo.html` | 클립보드 fallback, `location.hash` 라우팅, 375px 좁은 화면 미리보기, 접근성 설명(`a11y` 필드) |
| `alert-ui-showcase.html` | 레이어/알림 동작 패턴. 그라데이션·글로우·`backdrop-filter`를 쓰므로 비주얼은 가져오지 않습니다. |
| `ui-component.html` | Tailwind CDN + Lucide + `script: () => {}` 방식이고, 미리보기를 본문에 `innerHTML`로 넣어 ID·CSS가 충돌합니다. 따라 하지 않을 방식의 예입니다. |
| `label-chip-system.html` | 라벨/칩 17색 팔레트, 크기·상태, 우선순위 배지, 토큰 정의 방식 |
| `colorpicker.html`, `color-personal-card.html` | 색상 선택 UI 보조 데모 |

## 디자인 규칙 — anti-ai-slop (강제)

[.claude/rules/anti-ai-slop.md](.claude/rules/anti-ai-slop.md)가 모든 HTML·SVG 생성에 적용됩니다. 금지·강제 항목과 출력 전 자가 점검 체크리스트는 그 파일에 있습니다. 이 프로젝트에서 규칙을 적용할 때 걸리는 지점은 다음과 같습니다.

- **카탈로그 셸과 복사용 컴포넌트 코드 모두**에 적용합니다. 사용자가 복사해 가는 `css` 문자열도 규칙을 통과해야 합니다.
- **`transition`은 색·투명도 같은 기능적 상태에만, 150ms 이하로 씁니다.** 레이어 컴포넌트(토스트·스낵바·바텀 시트·모달)의 표시·숨김도 슬라이드나 scale 없이 opacity 150ms 이하로 처리합니다.
- **시작점 파일도 위반이 있습니다.** `component-portfolio.html`에서 옮길 때 다음을 고칩니다.

| 위치 | 위반 | 처리 |
|---|---|---|
| `:589-590` `.ldr__line` | `pulse` 키프레임 | 정적 회색 블록으로 바꿈 |
| `:288` `.car__track`, `:734` `.prog__fill` | transition 200ms | 150ms 이하로 줄임 |
| `:925` `.tgl__sw` | `border-radius: 13px` | 8px 이하로 줄임 |

## 프롬프트 기록 훅

`.claude/settings.local.json`의 **Stop 훅**이 응답이 끝날 때마다 `extract-my-prompts.sh`를 실행합니다. 이 스크립트는 `~/.claude/projects/` 아래 세션 jsonl에서 `cwd`가 일치하는 세션을 찾아, 사용자가 입력한 텍스트만 Markdown으로 저장합니다. 도구 출력과 시스템 주입 메시지는 제외합니다.

- `settings.local.json`은 머신 전용이라 `.gitignore`에 들어 있습니다. 훅 command가 절대경로이므로 다른 머신에서는 경로를 고쳐야 합니다.
- 현재 훅은 스크립트를 인자 없이 호출합니다. 그래서 `statusMessage`("Prompt.md 기록 갱신 중")와 달리 `$PWD/my-prompts.md`에 최신 세션만 덮어씁니다.
- `Prompt.md` 이어붙이기가 목적이면 command에 `--append -o Prompt.md`를 붙여야 합니다. `--append`는 `Prompt.md.state` 파일에 마지막 기록 시각을 저장하고, 그 이후 프롬프트만 추가합니다.
- `my-prompts.md`, `Prompt.md`, `Prompt.md.state`는 스크립트가 생성하는 파일입니다. 직접 편집하지 않습니다.

## 접근성

토글형 버튼은 `aria-expanded`, 스위치는 `role="switch"` + `aria-checked`, 모달·확인 창은 `role="dialog"` + `aria-modal="true"`, 토스트·스낵바는 `role="status"`(오류는 `role="alert"`)를 씁니다.
