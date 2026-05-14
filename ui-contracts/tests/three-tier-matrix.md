# Agent UI Three-Tier Test Matrix

Source rows: `ONB-*`, `MAIN-*`, `CHAT-*`, `CODE-*`, `AUTH-*`, `SET-*`, `BND-*`

Status: Draft, source-anchored

## Tier Definitions

| Tier           | Boundary                                                                                  | Tooling                                               | Failure means                         |
| -------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------- | ------------------------------------- |
| L1 unit        | One module, pure helper, reducer, store slice, or validator. No DOM/IPC/network.          | Vitest direct calls and mocks.                        | Helper or reducer contract is broken. |
| L2 integration | Multi-module inside one process. May use jsdom and mocked gateway/client boundary.        | Vitest + React Testing Library or main-process mocks. | UI and boundary code disagree.        |
| L3 e2e         | Full Electron app with renderer, main, spawned gateway, and optionally stubbed providers. | Future Playwright/Electron or computer-use harness.   | User-visible journey breaks.          |

## Running

```bash
cd apps/electron && pnpm test
cd apps/electron && pnpm test:main
pnpm test -- src/gateway/<filter>
pnpm test -- src/agents/<filter>
```

L3 is not wired yet.

## L1 Coverage

| Contract area                                       | Status  | Existing tests or gap                                                                                                                              |
| --------------------------------------------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GatewayProcessManager` health/spawn/restart        | Partial | Indirect main-process coverage in `apps/electron/src/main/update-manager.test.ts` and `apps/electron/src/main/mobile-preview-gateway-sync.test.ts` |
| `GatewayEventRouter` buffer/activeRuns/TTL          | No test | Logic starts at `apps/electron/src/main/gateway-event-router.ts:69`                                                                                |
| Renderer error boundary                             | No test | UI fallback at `apps/electron/src/renderer/src/main.tsx:8`                                                                                         |
| `useAppStore.setGlobalAgentMode`                    | Covered | `apps/electron/src/renderer/test/app-store.test.ts`                                                                                                |
| `useSessionStore` create/activate/draft/materialize | Covered | `apps/electron/src/renderer/test/session-store.test.ts`                                                                                            |
| `useTabStore` add/remove/active/agent authoring tab | Covered | `apps/electron/src/renderer/test/tab-store.test.ts`                                                                                                |
| `useModelStore` model matching/helpers              | Covered | `apps/electron/src/renderer/test/model-store.test.ts`                                                                                              |
| `usePanelStore`                                     | No test | Right-panel state still needs focused coverage                                                                                                     |
| `useWorkspaceStore`                                 | Partial | Indirect via `apps/electron/src/renderer/test/code-session-selection.test.ts`                                                                      |
| Onboarding app-store gate                           | Partial | `setOnboardingComplete` covered in app-store tests; full flow not covered                                                                          |
| History normalization                               | Covered | `apps/electron/src/renderer/test/format-converters.test.ts`                                                                                        |
| DualStreamHandler                                   | Covered | `apps/electron/src/renderer/test/dual-stream-handler.test.ts`                                                                                      |
| `useChat` reducer                                   | Covered | `apps/electron/src/renderer/test/use-chat.test.tsx`                                                                                                |
| Slash command parser/navigation                     | No test | Component logic in `apps/electron/src/renderer/src/components/chat/SlashCommandMenu.tsx`                                                           |
| Agent file root resolution                          | Covered | `apps/electron/src/renderer/test/agent-files-resolve.test.ts`                                                                                      |
| DM policy validation                                | Covered | `apps/electron/src/renderer/test/dm-policy-validation.test.ts`                                                                                     |
| Timezone helpers                                    | Covered | `apps/electron/src/renderer/test/timezones.test.ts`                                                                                                |
| SettingsDialog tab resolution                       | No test | `apps/electron/src/renderer/src/components/settings/SettingsDialog.tsx`                                                                            |
| Provider validation helpers                         | No test | `apps/electron/src/main/ipc-gateway.ts:102`                                                                                                        |
| Gateway chat param validators                       | Partial | Exercised through gateway chat tests; not isolated                                                                                                 |
| Agent event emission                                | Covered | `src/agents/pi-embedded-subscribe.handlers.tools.test.ts`; `src/agents/pi-embedded-subscribe.handlers.messages.test.ts`; lifecycle tests           |
| Per-LLM repo-specific logic                         | Partial | OpenAI reasoning covered; provider validator and chefSlug mapping gaps remain                                                                      |
| Agent Authoring store                               | Covered | `apps/electron/src/renderer/src/stores/agent-authoring-store.test.ts`                                                                              |
| Agent Authoring skill helpers                       | Covered | `apps/electron/src/renderer/test/skill-model.test.ts`; `apps/electron/src/renderer/test/skill-template.test.ts`                                    |
| Capability diff panel                               | Covered | `apps/electron/src/renderer/test/capability-diff-panel.test.tsx`                                                                                   |

## L2 Coverage

| Source rows                                                                                        | Contract                         | Status            | Existing tests or gap                                                                                                                              |
| -------------------------------------------------------------------------------------------------- | -------------------------------- | ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `MAIN-01`                                                                                          | Returning-user main window frame | Partial           | Store coverage is indirect; no focused renderer integration test for Chat/Code branch, gateway banner, update toast                                |
| `MAIN-02`                                                                                          | Mode tab switch                  | No focused test   | `useAppStore.setGlobalAgentMode` has unit coverage; no layout-switch integration test                                                              |
| `MAIN-03`                                                                                          | Sidebar shared controls          | Covered/partial   | Session sidebar tests cover adjacent behavior; keyboard collapse, mode-specific Chat/Code list split, and Settings open path need focused coverage |
| `MAIN-04`                                                                                          | Account footer                   | No focused test   | Login, signed-in identity, role badge, and sign-out footer states need controlled auth stubs                                                       |
| `MAIN-05`                                                                                          | Tray system menu                 | No test           | Needs Electron tray harness for Show/Hide, New Conversation, Gateway status, Restart Gateway, and Quit                                             |
| `MAIN-06`                                                                                          | Pairing notification route       | No test           | Needs OS-notification or Electron-main harness from notification click to Settings Channels target                                                 |
| `ONB-01`, `ONB-02`, `ONB-03`, `ONB-04`, `ONB-05`                                                   | First-run onboarding flow        | Partial/no test   | Store gate is partial; provider, personality, notification, and completion step transitions need focused renderer tests                            |
| `CHAT-01`                                                                                          | Chat sidebar session management  | Covered/partial   | `apps/electron/src/renderer/test/session-sidebar.test.tsx`; `apps/electron/src/renderer/test/session-item.test.tsx`                                |
| `CHAT-02`                                                                                          | Chat header collapsed controls   | No focused test   | Needs renderer test for open-sidebar, new conversation, title, model badge, and unavailable model state                                            |
| `CHAT-07`, `CHAT-08`, `BND-03`                                                                     | Send, stream, abort, reconnect   | Covered/partial   | `apps/electron/src/renderer/test/protocol-bridge.test.ts`; `apps/electron/src/renderer/test/reconnect-guard.test.ts`                               |
| `CHAT-03`                                                                                          | ChatMessages rendering           | Partial           | Adjacent renderer tests; full visual matrix not complete                                                                                           |
| `CHAT-01`, `CODE-03`                                                                               | SessionSidebar branching         | Partial           | `apps/electron/src/renderer/test/code-session-selection.test.ts`                                                                                   |
| `CHAT-04`                                                                                          | Composer input/attachments/slash | Partial           | Hook/transport coverage; component interactions incomplete                                                                                         |
| `CHAT-05`                                                                                          | Model picker                     | No focused test   | Needs grouped picker test                                                                                                                          |
| `CHAT-06`                                                                                          | Thinking picker                  | No focused test   | Needs native/select behavior test                                                                                                                  |
| `CODE-01`                                                                                          | Code IDE frame layout            | Partial           | Session bootstrap and right-panel store sync are indirect; no focused renderer test for sidebar/bar/tab/right/bottom layout                        |
| `CODE-02`                                                                                          | Workspace creation dialog        | No focused test   | Needs Browse/Create/Cancel/Escape renderer coverage with mocked native directory picker                                                            |
| `CODE-03`                                                                                          | Code sessions and workspaces     | Covered/partial   | `apps/electron/src/renderer/test/code-session-selection.test.ts`; visible expand/collapse controls still need coverage                             |
| `CODE-04`                                                                                          | Tab system                       | Covered/partial   | `apps/electron/src/renderer/test/tab-store.test.ts`                                                                                                |
| `CODE-05`                                                                                          | WorkingDirBar Edit Agent gate    | Covered/partial   | `apps/electron/src/renderer/test/agent-files-resolve.test.ts`; tab store tests                                                                     |
| `CODE-06`, `CODE-07`, `CODE-08`, `CODE-09`                                                         | Right panel and bottom panel     | Partial           | Workspace/device IPC tests needed                                                                                                                  |
| `CODE-10`                                                                                          | FileTab                          | No focused test   | Needs loading, error, binary/too-large, path header, line highlight, and rendered code assertions                                                  |
| `CODE-11`                                                                                          | DiffTab                          | No focused test   | Needs loading, error, no-output, counts, hunk row, and old/new line-number assertions                                                              |
| `CODE-12`                                                                                          | Empty tab placeholder            | No focused test   | Needs fallback dispatch assertion for missing/incomplete active tab                                                                                |
| `CODE-13`                                                                                          | Cloud deploy latent branch       | No test           | Needs explicit absence assertion while `TOP_TABS` omits `cloud`                                                                                    |
| `AUTH-01`                                                                                          | Agent file editing               | Partial           | Store coverage exists; full authoring tab file read/write/save/revert flow missing                                                                 |
| `AUTH-02`                                                                                          | Capabilities section             | Partial           | Diff panel covered; device stream/status section flow missing                                                                                      |
| `AUTH-03`                                                                                          | Skills section                   | Partial           | Component coverage exists for skills/new skill/probe; integrated path still incomplete                                                             |
| `AUTH-04`                                                                                          | Mobile preview                   | Partial           | Dialog and main store coverage exist; Code -> authoring -> preview L3 missing                                                                      |
| `AUTH-05`                                                                                          | Publish/export                   | Partial           | Export dialog covered; publish dialog missing                                                                                                      |
| `SET-*`                                                                                            | Settings tabs                    | Partial/no test   | Config-editor tests exist; Settings frame/providers/voice/channels need focused integration tests                                                  |
| `SET-01`, `SET-02`, `SET-03`, `SET-04`, `SET-05`, `SET-06`, `SET-07`, `SET-08`, `SET-09`, `SET-10` | Settings per-tab journeys        | Partial/no test   | `settings-tests.md` owns row-by-row gaps; most tabs need mocked Electron API wrapper tests                                                         |
| `BND-01`                                                                                           | Provider validation IPC          | No focused test   | Needs mocked `net.fetch`                                                                                                                           |
| `BND-02`                                                                                           | Model selection boundary         | Partial           | Model helpers covered; picker/default model/no-model interaction tests missing                                                                     |
| `BND-03`                                                                                           | Gateway chat methods             | Partial           | Gateway chat tests cover related behavior                                                                                                          |
| `BND-04`                                                                                           | ACP visible boundary             | Root ACP contract | Visible boundary rows are tracked here; ACP semantics are covered by `docs/hardware_harness/ui-contracts/agent-ui-contracts-via-acp.md`            |

## L3 Scenarios

| Scenario ID                 | Source rows                                                                                        | User journey                                          | Status     |
| --------------------------- | -------------------------------------------------------------------------------------------------- | ----------------------------------------------------- | ---------- |
| `ONB-first-run`             | `ONB-01`, `ONB-02`, `ONB-03`, `ONB-04`, `ONB-05`                                                   | Launch, complete onboarding, land in Chat.            | No harness |
| `CHAT-sidebar-manage`       | `CHAT-01`                                                                                          | Create, search, rename, delete chat sessions.         | No harness |
| `CHAT-header-collapsed`     | `CHAT-02`                                                                                          | Use collapsed-sidebar Chat header controls.           | No harness |
| `CHAT-message-list-inspect` | `CHAT-03`                                                                                          | Inspect empty, markdown, tool, and command states.    | No harness |
| `CHAT-send-abort`           | `CHAT-04`, `CHAT-07`, `BND-03`                                                                     | Type prompt, send, observe streaming, stop.           | No harness |
| `CHAT-model-picker`         | `CHAT-05`, `BND-02`                                                                                | Open picker, search model, select grouped entry.      | No harness |
| `CHAT-thinking-picker`      | `CHAT-06`                                                                                          | Change thinking level and verify visible pill.        | No harness |
| `CHAT-attachment`           | `CHAT-04`                                                                                          | Add file/image attachment and send.                   | No harness |
| `MAIN-returning-user`       | `MAIN-01`                                                                                          | Launch into saved Chat or Code mode.                  | No harness |
| `MODE-chat-code`            | `MAIN-02`, `CODE-01`                                                                               | Switch Chat to Code.                                  | No harness |
| `MAIN-sidebar`              | `MAIN-03`                                                                                          | Use shared sidebar collapse, new, search, Settings.   | No harness |
| `MAIN-account`              | `MAIN-04`                                                                                          | Sign in, verify identity/role, sign out.              | No harness |
| `MAIN-tray`                 | `MAIN-05`                                                                                          | Operate the app from the tray menu.                   | No harness |
| `MAIN-pairing-route`        | `MAIN-06`, `SET-05`                                                                                | Click pairing notification into Channels settings.    | No harness |
| `CODE-frame-layout`         | `CODE-01`                                                                                          | Enter Code mode and verify Code IDE frame layout.     | No harness |
| `CODE-create-workspace`     | `CODE-02`, `CODE-03`                                                                               | Create workspace and first code session.              | No harness |
| `CODE-file-diff`            | `CODE-06`, `CODE-07`, `CODE-10`, `CODE-11`                                                         | Open file and changed-file diff from right panel.     | No harness |
| `CODE-edit-agent`           | `CODE-05`, `AUTH-01`                                                                               | Click Edit Agent and verify Agent Authoring tab.      | No harness |
| `AUTH-edit-files`           | `AUTH-01`                                                                                          | Edit and save an authoring markdown file.             | No harness |
| `AUTH-capabilities`         | `AUTH-02`                                                                                          | Inspect device manifest and capability diff states.   | No harness |
| `AUTH-skills`               | `AUTH-03`                                                                                          | Select, edit, preview, create, and probe skills.      | No harness |
| `AUTH-mobile-preview`       | `AUTH-04`                                                                                          | Open mobile preview and inspect QR/error states.      | No harness |
| `AUTH-publish`              | `AUTH-05`                                                                                          | Publish/export package paths with controlled stubs.   | No harness |
| `CODE-device-terminal`      | `CODE-08`, `CODE-09`                                                                               | Use Device tab, Terminal tab, Device Log tab.         | No harness |
| `CODE-empty-tab`            | `CODE-12`                                                                                          | Verify no-active-tab placeholder.                     | No harness |
| `CODE-cloud-not-exposed`    | `CODE-13`                                                                                          | Verify Cloud is absent from visible Code tabs.        | No harness |
| `SET-provider`              | `SET-04`, `BND-01`                                                                                 | Add provider, validate models, save, choose default.  | No harness |
| `SET-all-tabs`              | `SET-01`, `SET-02`, `SET-03`, `SET-04`, `SET-05`, `SET-06`, `SET-07`, `SET-08`, `SET-09`, `SET-10` | Visit every Settings tab and verify primary controls. | No harness |
| `BND-model-selection`       | `BND-02`, `CHAT-05`, `SET-04`                                                                      | Select chat/default model and verify boundary calls.  | No harness |
| `BND-acp-visible`           | `BND-04`, `CHAT-03`, `CODE-05`                                                                     | Inspect ACP visible blocks and Code status pill.      | No harness |
| `RECOVER-refresh`           | `CHAT-08`, `BND-03`                                                                                | Refresh mid-stream and reconnect.                     | No harness |

## Stable Selector Requirements

L3 scenarios should use role, accessible name, `aria-label`, or `data-testid`. If a target lacks stable selectors, mark the scenario as blocked by `No stable selector`.

High-risk targets for selectors:

- `CHAT-05` model picker rows.
- `CHAT-06` thinking picker values.
- `CODE-01` Code IDE frame landmarks.
- `CODE-02` workspace creation dialog controls.
- `CODE-07` Changes empty states and Retry.
- `CODE-10` FileTab states.
- `CODE-11` DiffTab states.
- `CODE-12` EmptyTabPlaceholder.
- `CODE-09` Terminal and Device Log tab state.
- `AUTH-04` mobile preview state variants.
- `AUTH-01`, `AUTH-02`, `AUTH-03`, and `AUTH-05` authoring tab selectors.
- `SET-05` channel cards and setup modals.
- `SET-01` through `SET-10` per-tab roots and primary control selectors.
- `BND-04` ACP block wrappers and modified-files/permission/status selectors.

## Live Tests

Provider live tests are not normal L1/L2/L3 coverage. Keep them under provider/gateway coverage and mark them live-only.

Examples:

- OpenAI realtime live tests under `src/agents/openai-ws-stream.e2e.test.ts`.
- Anthropic setup token live tests under `src/agents/anthropic.setup-token.live.test.ts`.
- Google Gemini switch live tests under `src/agents/google-gemini-switch.live.test.ts`.
