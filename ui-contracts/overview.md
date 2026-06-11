# Agent UI Contracts Overview

Status: Draft contract index

Entry point: `npm run dev:electron`.

Journey entry: `docs/hardware_harness/ui-contracts/agent-ui-user-journey.md`.

Use this directory when you need to understand how the Agent UI behaves today. Start with the journey doc, then open the focused contract for the screen you are changing. Each contract must answer five plain questions: what can the user see, what can they do, what changes on screen, which API or local state is touched, and which repo lines prove it.

## Scope

The contracts cover the Electron IDE experience from first launch through normal use:

| Area                                             | Detailed contract group                                                               | Source rows |
| ------------------------------------------------ | ------------------------------------------------------------------------------------- | ----------- |
| First-run setup                                  | Onboarding stepper and first usable chat                                              | `ONB-*`     |
| Main window, sidebar, account, and system routes | Returning-user frame, mode switch, sidebar, login footer, tray, update/status, routes | `MAIN-*`    |
| Chat tab                                         | Sessions, message list, composer, model picker, thinking picker, send/stop/recovery   | `CHAT-*`    |
| Code tab                                         | Workspaces, code sessions, tabs, file/diff/device panels, terminal, empty states      | `CODE-*`    |
| Agent authoring                                  | Agent files, skills, capabilities, removed mobile preview boundary, publish/export    | `AUTH-*`    |
| Settings                                         | Settings frame and every visible settings tab                                         | `SET-*`     |
| Provider and gateway boundaries                  | Provider validation, model selection, chat RPC, visible ACP boundary markers          | `BND-*`     |

ACP-specific routing, permission prompts, runtime events, and renderer bridge behavior are covered by `docs/hardware_harness/ui-contracts/agent-ui-contracts-via-acp.md`. Rows in this directory may still mark visible ACP UI only; do not infer ACP behavior from public SDK knowledge.

## Journey Inventory Alignment

The current end-to-end journey source is `electron-user-journeys-hierarchy-v2/user-journey-inventory.md`. Use it as the behavior-language source when contract rows need to describe what the user does, what visible state changes, and where the flow ends.

| Inventory journey | Primary UI contract rows | Contract files to update together |
| ----------------- | ------------------------ | --------------------------------- |
| `01 First Launch / Onboarding` | `ONB-01` through `ONB-05` | `onboarding/onboarding-flow.md`, `tests/onboarding-tests.md` |
| `02 Main App Startup / Recovery State` | `MAIN-01`, `MAIN-03`, `MAIN-05` | `main-window/main-window-runtime.md`, `tests/main-window-tests.md` |
| `03 Login / Account Authentication` | `MAIN-04` | `main-window/main-window-runtime.md`, `tests/main-window-tests.md` |
| `04 Chat Mode` | `CHAT-01` through `CHAT-08`, `BND-03`, `BND-04` | `chat/chat-runtime.md`, `chat/client-state-machine.md`, `provider-gateway-boundaries/*` |
| `05 Code Mode / Workspace` | `CODE-01` through `CODE-05` | `code-ide/code-mode-runtime.md`, `tests/code-ide-tests.md` |
| `06 Runtime / Model / Permission` | `CHAT-05`, `CHAT-07`, `CHAT-08`, `SET-07`, `BND-04` | `chat/chat-runtime.md`, `settings/permissions.md`, `provider-gateway-boundaries/acp-visible-boundary.md` |
| `07 Code Workbench Panels` | `CODE-06` through `CODE-12` | `code-ide/code-mode-runtime.md`, `tests/code-ide-tests.md` |
| `08 Agent Authoring` | `AUTH-01` through `AUTH-03` | `agent-authoring/file-edit.md`, `agent-authoring/capabilities.md`, `agent-authoring/skills.md` |
| `09 Catalog Review / Firmware Publish` | `AUTH-05` | `agent-authoring/publish-export.md`, `tests/agent-authoring-tests.md` |
| `10 Device / PlatformIO / Logs` | `CODE-08`, `CODE-10`, `CODE-11` | `code-ide/code-mode-runtime.md`, `tests/code-ide-tests.md` |
| `11 LVGL Preview` | `CODE-14` | `code-ide/code-mode-runtime.md`, `tests/code-ide-tests.md` |
| `12 Settings - General` | `SET-02` | `settings/general.md`, `tests/settings-tests.md` |
| `13 Settings - Workspace Files` | `SET-03` | `settings/workspace.md`, `tests/settings-tests.md` |
| `14 Settings - Providers` | `SET-04`, `BND-01`, `BND-02` | `settings/providers.md`, `provider-gateway-boundaries/provider-validation.md` |
| `15 Settings - Permissions` | `SET-07`, runtime permission parts of `CHAT-08` | `settings/permissions.md`, `chat/chat-runtime.md` |
| `16 Settings - About / Updates` | `SET-10`, frame update prompt parts of `MAIN-01` | `settings/about.md`, `main-window/main-window-runtime.md` |

Journey 09 does not rewrite Agent Authoring into an auth journey or a four-row release contract. `AUTH-05` remains the Publish to Catalog / package export boundary; Mobile Preview is marked as a deleted entry.

## Directory Layout

Use this structure for follow-up documents:

```text
docs/hardware_harness/ui-contracts/
  overview.md
  agent-ui-user-journey.md
  agent-ui-contracts-via-acp.md
  assets/screenshots/
  onboarding/README.md
  main-window/README.md
  chat/README.md
  code-ide/README.md
  agent-authoring/README.md
  settings/README.md
  provider-gateway-boundaries/README.md
  provider-gateway-boundaries/runtime-flows.md
  tests/README.md
```

Each subdirectory starts with a `README.md` that lists the rows it owns, then uses focused Markdown files for the actual contracts. A detailed contract must be understandable on its own.

## Contract File Template

Each detailed contract should use this shape:

```markdown
# <Contract Name>

Source rows: `<ROW-ID>`, `<ROW-ID>`
Entry path: <visible navigation path from app boot>
Status: Draft / Stable / TODO

## User Journey

Describe the user-visible flow in order. Do not include hidden implementation steps here.

## UI Surface

List visible controls, menus, tabs, buttons, empty states, loading states, error states, disabled states, and modals.

## Interaction Contract

For each action, document:

| User action | UI precondition | UI result | Backend/API path | Evidence | Test coverage |
| ----------- | --------------- | --------- | ---------------- | -------- | ------------- |

## Data And Events

List request parameters, response fields, stream events, IPC/RPC names, or local state used by this UI surface.

## Gaps

Use `TODO`, `No test`, `Not exposed`, or `Unverified` for anything not backed by repo evidence.
```

## Evidence Rules

Every positive behavior must have a repo-local `file:line` anchor. Prefer anchors closest to the behavior:

1. UI component line for visible controls and states.
2. Renderer store/client line for local state or IPC/RPC calls.
3. Main-process IPC line for Electron bridge behavior.
4. Gateway method or provider implementation line for backend behavior.
5. Existing test line only when documenting current coverage.

Do not describe expected behavior as implemented behavior. If the code path cannot be traced, mark it as `TODO` or `Unverified`.

## Test Mapping

Tests should map back to contract rows instead of inventing a separate taxonomy:

| Test layer  | Purpose                                                            | Contract mapping                                      |
| ----------- | ------------------------------------------------------------------ | ----------------------------------------------------- |
| Unit        | Validate pure render/state helpers and request shaping.            | One or more interaction rows inside a contract file.  |
| Integration | Validate renderer-to-IPC/RPC behavior and state transitions.       | One user action with real or mocked gateway boundary. |
| E2E         | Validate visible UI journeys for human and computer-use execution. | One complete source row or short row sequence.        |

When writing test docs, keep the contract row ID in every scenario title. Example: `CHAT-05 selects a model from the grouped model picker`.

## Naming Rules

Use lowercase kebab-case for folders and files:

```text
chat/chat-runtime.md
chat/client-state-machine.md
code-ide/code-mode-runtime.md
settings/settings-runtime.md
agent-authoring/authoring-runtime.md
```

Keep filenames aligned to user-visible surfaces, not internal class names. Prefer grouped contract names such as `chat-runtime.md`, `code-mode-runtime.md`, or `main-window-runtime.md` over component names such as `ModelSelectorContent.md` or `ChangesTree.md`.

Use names that describe the user-visible area. `main-window` covers the Electron frame that wraps Chat, Code, the shared sidebar, account footer, tray routes, and notification routes.

## Canonical Files

This directory is the canonical location for detailed Agent UI contracts. Row IDs originate in `docs/hardware_harness/ui-contracts/agent-ui-user-journey.md`, and each surface README plus detailed file below expands those rows.

| Directory                      | Detailed files                                                                                                                                                                                                                                              |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `./`                           | `overview.md`, `agent-ui-user-journey.md`, `agent-ui-contracts-via-acp.md`                                                                                                                                                                                  |
| `onboarding/`                  | `README.md`, `onboarding-flow.md`                                                                                                                                                                                                                           |
| `main-window/`                 | `README.md`, `main-window-runtime.md`                                                                                                                                                                                                                       |
| `chat/`                        | `README.md`, `chat-runtime.md`, `client-state-machine.md`                                                                                                                                                                                                   |
| `code-ide/`                    | `README.md`, `code-mode-runtime.md`                                                                                                                                                                                                                         |
| `agent-authoring/`             | `README.md`, `authoring-runtime.md`, `file-edit.md`, `capabilities.md`, `skills.md`, `mobile-preview.md`, `publish-export.md`                                                                                                                               |
| `settings/`                    | `README.md`, `settings-runtime.md`, `settings-frame.md`, `general.md`, `workspace.md`, `providers.md`, `channels.md`, `extensions.md`, `permissions.md`, `voice.md`, `advanced.md`, `about.md`                                                              |
| `provider-gateway-boundaries/` | `README.md`, `process-architecture.md`, `transport-and-events.md`, `provider-validation.md`, `model-selection.md`, `gateway-chat.md`, `acp-visible-boundary.md`, `runtime-flows.md`, `per-llm-repo-specifics.md`, `reference-surfaces.md`                   |
| `tests/`                       | `README.md`, `three-tier-matrix.md`, `coverage-index.md`, `l3-computer-use-scenarios.md`, `onboarding-tests.md`, `main-window-tests.md`, `chat-tests.md`, `code-ide-tests.md`, `agent-authoring-tests.md`, `settings-tests.md`, `provider-gateway-tests.md` |

Detailed contract groups currently split out:

- `onboarding/README.md` maps `ONB-01` through `ONB-05`, with `onboarding-flow.md` covering first run, provider verification, personality setup, skip behavior, and handoff into chat.
- `main-window/README.md` maps `MAIN-01` through `MAIN-06`, with `main-window-runtime.md` covering boot, the main frame, sidebar mode switching, account/login entry, update/status screens, and route handoffs.
- `chat/README.md` maps `CHAT-01` through `CHAT-08`, with `chat-runtime.md` covering session creation and selection, message list rendering, composer state, model/thinking selectors, send/stop/retry behavior, and visible error states.
- `code-ide/README.md` maps `CODE-01` through `CODE-14`, with `code-mode-runtime.md` covering workspace selection, Code sessions, file/diff panels, device/preview/terminal panels, and empty/error states.
- `agent-authoring/README.md` maps `AUTH-01` through `AUTH-05`, with focused contracts for file editing, capabilities, skills, removed mobile preview, and publish/export.
- `provider-gateway-boundaries/README.md` maps `BND-01` through `BND-04`, with focused contracts for provider validation, model selection, gateway chat, and visible ACP markers. `provider-gateway-boundaries/runtime-flows.md` is the end-to-end reference for ACP, chat, gateway messaging, and session routing. ACP behavior is covered by `docs/hardware_harness/ui-contracts/agent-ui-contracts-via-acp.md`.
