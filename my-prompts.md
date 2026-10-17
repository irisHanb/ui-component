# Claude Code 프롬프트 모음

- 프로젝트: `/Users/hanb/dev-ai/lectures/solving-claude-code/ui-component`
- 범위: 이번 세션
- 추출 시각: 2026-10-17 18:49:21
- 세션 수: 1 / 프롬프트 수: 8

---

### 1. 2026-10-17

<browser_instruction># Claude in Chrome browser automation

You have access to browser automation tools (mcp__claude-in-chrome__*) for interacting with web pages in Chrome. Follow these guidelines for effective browser automation.

## Loading deferred tools

If the mcp__claude-in-chrome__* tools are deferred (must be loaded via ToolSearch before use), load every tool you expect to need in ONE ToolSearch call — the select query accepts a comma-separated list — never one call per tool. Start with the core set:

ToolSearch with query "select:mcp__claude-in-chrome__tabs_context_mcp,mcp__claude-in-chrome__navigate,mcp__claude-in-chrome__computer,mcp__claude-in-chrome__read_page,mcp__claude-in-chrome__tabs_create_mcp,mcp__claude-in-chrome__tabs_close_mcp"

Add task-specific tools to the same call when the task obviously needs them: read_console_messages / read_network_requests for debugging, form_input for forms, gif_creator for recordings, javascript_tool for page scripting.

## GIF recording

When performing multi-step browser interactions that the user may want to review or share, use mcp__claude-in-chrome__gif_creator to record them.

You must ALWAYS:
* Capture extra frames before and after taking actions to ensure smooth playback
* Name the file meaningfully to help the user identify it later (e.g., "login_process.gif")

## Console log debugging

You can use mcp__claude-in-chrome__read_console_messages to read console output. Console output may be verbose. If you are looking for specific log entries, use the 'pattern' parameter with a regex-compatible pattern. This filters results efficiently and avoids overwhelming output. For example, use pattern: "[MyApp]" to filter for application-specific logs rather than reading all console output.

## Alerts and dialogs

IMPORTANT: Do not trigger JavaScript alerts, confirms, prompts, or browser modal dialogs through your actions. These browser dialogs block all further browser events and will prevent the extension from receiving any subsequent commands. Instead, when possible, use console.log for debugging and then use the mcp__claude-in-chrome__read_console_messages tool to read those log messages. If a page has dialog-triggering elements:
1. Avoid clicking buttons or links that may trigger alerts (e.g., "Delete" buttons with confirmation dialogs)
2. If you must interact with such elements, warn the user first that this may interrupt the session
3. Use mcp__claude-in-chrome__javascript_tool to check for and dismiss any existing dialogs before proceeding

If you accidentally trigger a dialog and lose responsiveness, inform the user they need to manually dismiss it in the browser.

## Avoid rabbit holes and loops

When using browser automation tools, stay focused on the specific task. If you encounter any of the following, stop and ask the user for guidance:
- Unexpected complexity or tangential browser exploration
- Browser tool calls failing or returning errors after 2-3 attempts
- No response from the browser extension
- Page elements not responding to clicks or input
- Pages not loading or timing out
- Unable to complete the browser task despite multiple approaches

Explain what you attempted, what went wrong, and ask how the user would like to proceed. Do not keep retrying the same failing browser action or explore unrelated pages without checking in first.

## Tab context and session startup

IMPORTANT: At the start of each browser automation session, call mcp__claude-in-chrome__tabs_context_mcp first to get information about the user's current browser tabs. Use this context to understand what the user might want to work with before creating new tabs.

Never reuse tab IDs from a previous/other session. Follow these guidelines:
1. Only reuse an existing tab if the user explicitly asks to work with it
2. Otherwise, create a new tab with mcp__claude-in-chrome__tabs_create_mcp
3. If a tool returns an error indicating the tab doesn't exist or is invalid, call tabs_context_mcp to get fresh tab IDs
4. When a tab is closed by the user or a navigation error occurs, call tabs_context_mcp to see what tabs are available</browser_instruction>


<pasted_content id="f6d6">
https://careerfoundry.com/en/blog/ui-design/ui-element-glossary/ 를 기반으로 32가지 UI 컴포넌트와 컴포넌트의 미리보기, HTML, CSS, Javascript(있을경우) 코드 영역에 해당 코드들이 로드되어야 하고, 코드 하일라이트 기본적인 기능들이 적용되어야 해. 각 소스마다 복사하기 버튼을 통해 해당 요소를 복사해서 내 사이트에 붙여넣기 하는 컴포넌트 페이지를 만드는거야.

촤측에는 UI 요소 리스트와 UX에 필요한 기본적인 공통 모달(경고, 확인, 알림, 토스트, 스낵바, 바텀 시트 등 사이트에 꼭 필요한 알림등 레이어 처리 관련 컴포넌트)들을 포함해서 해당 리스트를 누르면 우측 메인 컨텐츠 영역에 클릭한 컴포넌트 명 - 정의와 설명이 나오고 하단에는 미리보기 html, css, js 탭들이 나와서 각각의 기능을 수항하면 돼

참고해야 할 리소스는 @docs/resource/component-portfolio.html 구조를 비롯해서 @docs/resource/ui-component.html @docs/resource/ui-components-32-catalog.html 이고 @docs/resource/label-chip-system.html @docs/resource/alert-ui-showcase.html 이고 @docs/resource/ 에 참고할 자료들들을 업로드 해놨어.

이를 바탕으로 /init 을 실행해서 기본적인 CLAUDE.md를 비롯한 초기화 작업 진행하고 CLAUDE.md는 한글로 작성해
</pasted_content id="f6d6">

### 2. 2026-10-17

@CLAUDE_ori.md 와 @CLAUDE.md 를 서로 비교, 참고해서 @CLAUDE.md 를 업데이트 혹은 수정해줘

### 3. 2026-10-17

육안을 비교하면 두 파일내용이 완전히 달라, 재시도 후 수정해줘

### 4. 2026-10-17

내용이 다르니 분석해서 업데이트 해

### 5. 2026-10-17

@.claude/rules/anti-ai-slop.md 추가했어. @CLAUDE.md 파일 업데이트 해줘

### 6. 2026-10-17

누락된 파일들 넣어뒀어. @CLAUDE.md 파일 갱신해줘

### 7. 2026-10-17

handoff 스킬을 만들거야.

세션을 넘길 때 아래를 한 파일(.handoff.md 또는 STATE)에 적는다.

✅ 무엇이 됐나 (검증된 증거와 함께)
❌ 무엇을 시도했고 실패했나 (반복 방지)
⏳ 다음 할 일 + 아직 안 한 것

체크리스트 형태로 만들어줘

### 8. 2026-10-17

handoff 검증해봐

