# Electron Client User Journey Overview

Last updated: 2026-06-08

## Scope

This document summarizes the current major user journeys in the `apps/electron` Electron client. The summary is a factual journey inventory, not a proposal document. Each journey should be checked against current docs, screenshots, and product behavior before its HTML wireframe is treated as authoritative.

Current primary product surfaces:

- NoraHarness desktop app: the developer workstation UI.
- Bundled Gateway: OpenClaw Gateway process lifecycle, chat, models, configuration, cron jobs, and scheduled tasks.
- Runtime control plane: OpenClaw, Claude Code, and Codex session runtimes.
- Hardware Harness authoring workflows: workspace, agent authoring, mobile preview, firmware publish, device, and LVGL preview.

Out of scope for the current journey map:

- Voice: the business area is cancelled. The Voice tab in Settings is not treated as a current journey.
- Channels: the business area is temporarily paused. The Channels tab in Settings is not treated as a current journey.
- Advanced: the overall Advanced area is temporarily paused and is not treated as a normal settings journey. Cron Jobs / Scheduled Tasks, however, must be promoted as a first-class priority journey.

## Overall Information Architecture

```text
First launch
  -> Onboarding
     -> Welcome
     -> Provider
     -> Personality
     -> Notifications
     -> Completion
  -> Main app
     -> Chat mode
     -> Code mode
     -> Settings
     -> Update prompt / Gateway banner
```

The main app has two core modes by default:

- Chat mode: general conversation, session list, model selection, thinking level, attachments, and slash commands.
- Code mode: code/hardware workspace, session tabs, file/diff/agent authoring tabs, right-side tool panel, and runtime selection.

Settings is a supporting surface and should not compete with the main workflow hierarchy. The settings journeys that can remain are General, Workspace, Providers, Permissions, About, plus Scheduled Tasks as a separate promoted journey.

## Journey 1: First Launch Onboarding

Target user: a user installing and launching NoraHarness for the first time.

Trigger: `openclaw:onboarding-complete` is not `true`.

Current path:

1. The user launches the Electron app.
2. If onboarding is incomplete, the app renders the onboarding flow instead of the normal Chat/Code shell.
3. The user enters the Welcome step.
4. The user enters the Provider step, selects a provider, enters an API key or local base URL, and selects models.
5. The user verifies provider access or explicitly skips verification.
6. The app saves the provider, enabled models, and enabled state.
7. The user enters the Personality step and may edit or skip personality configuration.
8. The user enters the Notifications step and may request system notification permission or skip it.
9. The user enters the Completion step.
10. The user completes onboarding and the app writes `openclaw:onboarding-complete=true`.
11. If there is no active session, the app creates a chat draft session and enters the main app.

Key success states:

- The user has saved a provider/model choice or explicitly accepted the skipped-verification state.
- Notification permission is not a hard blocker; the user can skip it.
- After onboarding completes, the user lands in Chat mode rather than a blank state.

Known docs/E2E:

- `apps/electron/e2e/journeys/new-user-onboarding.md`
- `apps/electron/e2e/specs/onboarding.spec.ts`

## Journey 2: Main App Startup, Gateway Status, And Update Prompt

Target user: a normal user who has already completed onboarding.

Trigger: `openclaw:onboarding-complete=true`.

Current path:

1. The app starts and calls `useGateway` to observe Gateway status.
2. `GatewayConnectionBanner` shows connected, reconnecting, error, or running state.
3. The app enters Chat mode or Code mode based on `openclaw:global-agent-mode`; Chat mode is the default.
4. After Gateway reconnects, the app reloads sessions. If the current mode is Code mode, it ensures a code session exists.
5. If an update has already been downloaded, `UpdateReadyPrompt` shows a toast and the user can click Restart to install it.

Key success states:

- Gateway connection problems are visible, but they do not block local UI that can already be used.
- After reconnect, the session list and active session refresh automatically.
- The update prompt only appears once per version to avoid repeated interruptions.

## Journey 3: Chat Mode Conversation

Target user: a user who wants to talk to the agent directly, ask questions, or issue lightweight tasks.

Entry points:

- Default entry after onboarding completes.
- Selecting Chat in the sidebar AgentModeSelector.
- New Conversation from the tray menu.

Current path:

1. The user views chat sessions in the left sidebar.
2. The user can search, select, rename, and delete sessions.
3. The user clicks New conversation to create or reuse a chat draft session.
4. The main area shows `ChatArea`: header, message list, composer, error cards, tool/permission states.
5. The user enters text in the composer and can add attachments.
6. The user selects a model. OpenClaw chat sessions can also select a thinking level.
7. The user sends the message.
8. The UI optimistically shows the user message, then uses the runtime-neutral chat transport to stream assistant output, reasoning, tool activity, permission prompts, status, modified files, errors, and history refresh.
9. The user can stop the current run while it is streaming/submitted.

Key success states:

- Empty input cannot be sent.
- If no models are available, the user is prompted to select an enabled model first.
- After sending, the input clears and message history eventually reconciles with canonical backend history.
- OpenClaw, Claude, and Codex all render through the same `ChatMessages` protocol, avoiding a second chat UI.

Known docs/E2E:

- `apps/electron/e2e/journeys/first-message.md` is currently a stub.
- Runtime-neutral contract: `docs/hardware_harness/spec/noraharness/runtime-chat-ui-contract.md`.

## Journey 4: Create A Code Workspace And Start A Code Session

Target user: a user who needs an agent to write code, inspect files, run commands, or operate a hardware project in a local directory.

Entry point:

- Selecting Code in the sidebar AgentModeSelector.

Current path:

1. The user switches from Chat to Code.
2. The app enters `CodeWorkspaceLayout`.
3. If no workspace exists, the sidebar shows No workspaces yet and Create Workspace.
4. The user opens the create dialog.
5. The user selects a directory via the system directory picker. The name is derived from the directory name by default and can also be entered manually.
6. The user creates the workspace.
7. The app creates a code draft session, defaults the runtime to Claude, and opens a New Conversation tab.
8. The user enters a task in the code composer.
9. If the session has no working directory, the app first requires the user to choose one.
10. The user sends the message. After the session materializes, the code conversation begins.

Key success states:

- After workspace creation, the user must see a workspace row, draft session, active chat tab, and composer.
- Code mode sessions are grouped by workspace and support expand/collapse all, creating a new session inside a workspace, selecting, renaming, and deleting sessions.
- The user can select a runtime in the composer: OpenClaw, Claude Code, or Codex.
- Once a runtime is locked by native identity/resume id, it cannot be switched casually, preventing transcript ownership confusion.

Known docs/E2E:

- `apps/electron/e2e/journeys/code-new-project-chat.md`
- `apps/electron/e2e/specs/code-new-project-chat.spec.ts`

## Journey 5: Code Mode Runtime, Models, And Permissions

Target user: a user selecting different agent runtimes in a code session.

Current path:

1. The user opens the Code mode composer.
2. The user selects a runtime: OpenClaw, Claude Code, or Codex.
3. The composer loads the available model list for that runtime.
4. The user selects a model.
5. The user sends a task.
6. For Claude/Codex, the Electron main process uses the ACP stdio runtime to start, send, resume, cancel, and read session history.
7. Runtime events are converted into unified UI chunks and displayed in the same message list.
8. If the runtime is missing a binary, missing auth, fails handshake, receives a permission denial, has an invalid workspace, or hits a Gateway failure, the UI shows the corresponding state card.

Key success states:

- OpenClaw is a Gateway-backed runtime; Claude/Codex are Electron-managed ACP runtimes.
- Claude/Codex do not depend on Gateway to complete a turn.
- Runtime health and permission prompts should not be hidden in the terminal.

## Journey 6: Code Workspace Files, Diff, Terminal, And Right Panel

Target user: a developer viewing files, changes, and local commands inside a project.

Current path:

1. The user selects a workspace-backed session in Code mode.
2. The center area shows the tab bar and active tab.
3. The right panel can be opened or closed.
4. The upper right panel contains All Files, Changes, Device, and Preview.
5. The lower right panel contains Terminal and Device Log.
6. The user opens a file from All Files, creating a File tab.
7. The user opens a diff from Changes, creating a Diff tab.
8. The user uses the Terminal tab bound to `workspacePath`.
9. The user views device logs for the current session/workspace in Device Log.

Key success states:

- `TabContentArea` supports chat, file, diff, and agent-authoring tabs.
- When there is no active tab, the UI shows an empty placeholder rather than crashing.
- Right panel layout and collapsed state persist, and `workspacePath` resolves correctly after switching sessions.

## Journey 7: Agent Authoring For Agent Packages

Target user: a developer building a Hardware Harness agent/package.

Entry point:

- Agent authoring entry in the Code mode `WorkingDirBar`.

Current path:

1. The user opens the Agent Authoring tab.
2. The app resolves agent file paths from `workspacePath`.
3. The left rail shows Soul, Style, Tools, Heartbeat, Bootstrap, Skills, and Capabilities.
4. The user edits Soul/Style/Tools/Heartbeat/Bootstrap.
5. The user sees dirty state and can Save, Revert, or Save All.
6. When switching sections with unsaved changes, the app asks whether to discard them.
7. In Skills, the user views/edits skill files and probes.
8. In Capabilities, the user views the capability manifest, tool schemas, and diffs.

Key success states:

- Editing managed content must not destroy user-written prose.
- Capability preview must show the contract actually consumed by NoraCloud, Application Harness, and Mobile.
- Heartbeat is a periodic checklist for agent behavior. It is different from Gateway cron/scheduled tasks and should not be conflated with them.

Related specs:

- `docs/hardware_harness/spec/noraharness/package-authoring.md`
- `docs/hardware_harness/spec/noraharness/agent-authoring-presentation.md`

## Journey 8: Publish Agent, View Review, And Edit Catalog Info

Target user: a developer preparing to publish an agent package to the NoraCloud catalog.

Entry points:

- Publish, Review status chip, and Edit app info in the Agent Authoring header.

Current path:

1. The user clicks Publish.
2. The app builds a fresh package and starts catalog publish.
3. The Publish dialog shows validation, upload, submit, error, or success state.
4. After submission, the header Publish button becomes a review status chip.
5. The user clicks the chip to open the Review Status modal.
6. The user can refresh review status or enter Publish new version from the modal.
7. If the package has already been published, the user can click Edit app info to edit catalog-facing information.

Key success states:

- Publish failure must not discard local draft/export state.
- Failure must point to the specific package surface.
- Last published state is visible in the bottom status bar and can open the catalog URL.

Related spec:

- `docs/hardware_harness/spec/noraharness/publish-preview-release.md`

## Journey 9: Mobile Preview

Target user: a developer who wants to preview the current agent/package on mobile.

Entry point:

- Preview on Mobile in the Agent Authoring header.

Current path:

1. The user clicks Preview on Mobile.
2. The app opens `MobilePreviewDialog`.
3. The app generates a short-lived preview handoff for the current workspace/package.
4. The UI shows QR/connection information, expiry countdown, and status.
5. The user scans or connects from the Mobile app.
6. After expiry, the user can regenerate the preview.

Key success states:

- Preview payload must not contain provider keys, device tokens, push tokens, or local absolute paths.
- Preview status and expiry time must be visible.
- Preview failure must be presented separately from Publish failure.

## Journey 10: Firmware Publish

Target user: a developer who has published a catalog agent and needs to upload firmware OTA versions.

Entry point:

- Publish Firmware in the Agent Authoring header. It is available only when `lastPublished` exists.

Current path:

1. The user clicks Publish Firmware.
2. The app opens `PublishFirmwareDialog` and passes the catalog agent id.
3. The user selects or confirms `firmware.bin`.
4. The user enters a semver firmware version and selects a channel: stable, beta, or dev.
5. The user enters optional device model, hardware revision, and release notes.
6. The main process calculates SHA-256 and size.
7. The main process requests a NoraCloud presign URL, PUT uploads bytes, and finalizes metadata.
8. The UI shows the successful firmware version, channel, SHA-256, size, created time, and refreshes latest firmware.

Key success states:

- Agent package versions and firmware versions are sibling version families under the same app id and do not overwrite each other.
- The renderer never touches better-auth cookies or S3 credentials.
- Duplicate version, auth, oversize, and storage failure cases need stable error codes and retry/edit paths.

Related spec:

- `docs/hardware_harness/spec/noraharness/firmware-upload.md`

## Journey 11: Cloud Deploy (Docs And Code Must Be Removed)

Status: this feature must be removed from both documentation and code. It is no longer an Electron client user journey.

## Journey 12: Device Status, PlatformIO, And Device Logs

Target user: a developer debugging real hardware devices.

Entry points:

- Device tab in the Code mode right panel and Device Log in the lower panel.

Current path:

1. The user selects a workspace.
2. The Device tab checks PlatformIO status.
3. The app subscribes to the project gateway device stream.
4. The app fetches device status and merges historical state, avoiding transient `unknown` overwriting a recently known valid connection.
5. The UI shows device connection, port, memory, latency, firmware, last seen, and agent state.
6. If the project gateway stream is unavailable, the UI shows gateway/stream diagnostics.
7. The user can start PlatformIO installation or refresh status.
8. The user views device event logs in the Device Log tab.

Key success states:

- Hardware Harness server unavailable, timeout, and stream reconnect states should be explicit.
- USB fallback notice appears only when a device exists and the gateway stream is disconnected/unavailable.
- Device status must not be cleared because of one failed fetch after the user has just seen valid information.

## Journey 13: LVGL Preview

Target user: a developer who needs a local preview of LVGL UI.

Entry point:

- Preview tab in the Code mode right panel.

Current path:

1. The user selects a workspace.
2. The Preview tab loads preview availability.
3. If available, the user clicks Start.
4. The main process resolves a preview manifest, legacy script, or compatibility scaffold.
5. The UI shows Resolve, Scaffold, Configure, Build, and Capture progress.
6. After preview completes, the UI shows screenshot, manifest summary, and created time.
7. The user can Rebuild.
8. On failure, the UI shows filtered diagnostic logs.

Key success states:

- Missing manifest, unsupported, building, running, stale, and error states must be clear.
- First build can be slow; progress copy should explain the current stage.
- Preview is a build-and-screenshot workflow, not a live device mirror.

Related specs:

- `docs/hardware_harness/spec/noraharness/lvgl-preview-runtime.md`
- `docs/hardware_harness/spec/noraharness/lvgl-preview-panel.md`

## Journey 14: Settings - General

Target user: a user managing app-level basic settings.

Current path:

1. The user opens Settings from the sidebar footer.
2. The app enters General by default.
3. The user views Gateway Status and last check.
4. The user can Restart Gateway.
5. The user configures HTTP Proxy. After save, the app can probe the port and indicate reachability.
6. The user toggles menu bar animations, launch at login, dock icon, canvas, and debug tools.
7. The user sets the default thinking level, and the app writes it to Gateway config.
8. When debug tools are enabled, the user can Reset Onboarding.

Key success states:

- Gateway health updates after restart.
- Proxy save failure rolls the UI back.
- Thinking level config write failure rolls back to the previous value.

## Journey 15: Settings - Workspace Files

Target user: a user directly editing main agent workspace markdown files.

Current path:

1. The user opens Settings -> Workspace.
2. The app lists the main agent's SOUL, IDENTITY, USER, AGENTS, TOOLS, HEARTBEAT, and MEMORY files.
3. The user selects a file.
4. The app loads file content into the Markdown editor.
5. The user edits content, and it auto-saves after 2 seconds.
6. When switching files with dirty changes, the app saves the current file first.
7. The user can manually Refresh.

Key success states:

- Missing files show missing state rather than writing an empty editor back over the file.
- Auto-save failure shows a toast.
- This Settings workspace edits main agent files. It is not the same layer as a Code mode project workspace.

## Journey 16: Settings - Providers And Default Model

Target user: a user configuring model providers, enabled models, and default model.

Current path:

1. The user opens Providers.
2. The app loads configured providers and the current default model.
3. The user can add a provider: Anthropic, OpenAI, Google, OpenRouter, Mistral, DeepSeek, xAI, Groq, Perplexity, Moonshot, MiniMax, Together, Hugging Face, or Local Models.
4. The user enters an API key or local base URL.
5. For dynamic catalog providers, the app loads/validates available models and caches the result for 24 hours.
6. The user selects enabled models.
7. The user saves the provider.
8. The user selects a default model from enabled models.
9. The app patches config and waits for Gateway/model store refresh.

Key success states:

- At least one model must be selected before saving.
- OpenRouter can fetch a model catalog without a key; local providers validate base URL.
- If the default model is no longer enabled, it should be shown as no longer enabled instead of silently failing.

## Journey 17: Settings - Extensions / ACP Runtime Management (Docs And Code Must Be Removed)

Status: this feature must be removed from both documentation and code. It is no longer an Electron client Settings journey.

## Journey 18: Settings - Permissions

Target user: a user controlling whether the agent can execute commands and use system permissions.

Current path:

1. The user opens Permissions.
2. The app loads exec approvals.
3. The user selects a command execution policy: Never run commands, Ask before running, or Auto-run all commands.
4. The user manages allowlist path patterns.
5. The user views Notifications, Microphone, Accessibility, and other macOS permissions.
6. The user can open system settings or request microphone permission.
7. The app polls permission changes and updates the UI.

Key success states:

- Auto-run all commands needs a clear danger label.
- Allowlist accepts only path patterns; basenames such as `echo` should not pass.
- System permission is a real macOS permission entry point and cannot fake `granted`.

## Journey 19: Settings - About And Updates

Target user: a user viewing versions, checking updates, and installing updates.

Current path:

1. The user opens About.
2. The app shows app name and display version.
3. The user can enable/disable auto-update.
4. The user clicks Check for updates.
5. If an update exists, the user can Download.
6. During download, progress, speed, and remaining size are shown.
7. After download completes, the user can Install and relaunch.

Key success states:

- Update disabled, idle, checking, available, downloading, downloaded, installing, and error states need clear copy.
- Auto-update save failure rolls back.
- When download speed is missing, the local estimate smooths it instead of showing an empty state.

## Journey 20: Scheduled Tasks / Cron Jobs Priority Journey

This is the Electron client journey that currently needs the strongest product focus. It should not be buried inside Advanced, and it must not disappear when Advanced is temporarily cancelled.

### 20.1 Current Code Facts

The current Settings -> Advanced -> Cron Jobs surface has implemented:

1. Open Advanced tab.
2. The app calls `client.cronList()`.
3. The UI shows cron job name and schedule.
4. The user can enable/disable a job with a switch.
5. The app calls `client.cronUpdate(job.id, { enabled })`.
6. Success toast: job enabled/disabled.
7. Failure toast: Failed to update.

The current Gateway client already has methods:

- `cronList()`
- `cronCreate(job)`
- `cronUpdate(id, patch)`
- `cronDelete(id)`

However, the current UI exposes only list and enabled toggle. It does not expose the complete journeys for create, edit, delete, run history, failure notification, delivery target, one-shot reminder, or isolated recurring job.

### 20.2 Product Positioning

Scheduled Tasks is the user entry point for the Gateway scheduler. It lets the agent run future or recurring tasks. It is not normal Settings, and it is not agent Heartbeat file editing.

It should be a standalone first-class journey:

- Suggested location: sidebar footer or a top-level main-mode entry named `Tasks` / `Scheduled Tasks`.
- Settings can retain a lightweight shortcut, but it should not be the only entry point.
- When Advanced is temporarily cancelled, Scheduled Tasks must remain reachable.

### 20.3 Core User Journey: Create A One-Shot Reminder

Target user: a user who wants the agent to remind them or run a lightweight system event at a specific time.

Target path:

1. The user opens Scheduled Tasks.
2. The user clicks New task.
3. The user selects One-shot.
4. The user enters a name.
5. The user selects a time: an exact date/time or a relative time such as 20 minutes later.
6. The user selects execution mode: Main session.
7. The user enters a system event, such as a reminder to check something.
8. The user selects whether to wake now / next heartbeat.
9. The user saves.
10. The UI shows next run time, enabled state, and delete policy.
11. When due, Gateway creates a background task and delivers the result or reminder to the target session.

Success states:

- Time, timezone, and next run are clearly shown.
- The user can distinguish "schedule saved" from "task already executed".
- A successful one-shot task can auto-delete by default, and the UI must show that policy.

### 20.4 Core User Journey: Create A Recurring Isolated Job

Target user: a user who wants the agent to regularly generate reports, inspect state, sync data, or process background work.

Target path:

1. The user opens Scheduled Tasks.
2. The user clicks New task.
3. The user selects Recurring.
4. The user selects schedule type: Every interval or Cron expression.
5. The user sets timezone.
6. The user selects execution style: Isolated.
7. The user enters prompt/message.
8. The user can select model, thinking, light context, and allowed tools.
9. The user selects delivery: announce, webhook, or none.
10. If announce is selected, the user selects channel/target. While channels are paused, at least session delivery should be supported or channel delivery should be clearly marked unavailable.
11. The user saves.
12. The UI shows enabled state, schedule, next run, last run, and last status.
13. Each execution creates a background task record.

Success states:

- Isolated job session semantics are clear: each run is a fresh session and does not inherit ambient chat context.
- Failed, skipped, timed out, and cancelled states are distinct from success.
- The user can see the latest run result, failure reason, and next run time.

### 20.5 Core User Journey: Manage Existing Scheduled Tasks

Target user: a user who needs to pause, resume, edit, or delete scheduled tasks.

Target path:

1. The user opens the Scheduled Tasks list.
2. The user filters by enabled/running/failed/skipped.
3. The user opens task detail.
4. The user can toggle enabled.
5. The user can edit schedule, prompt, model, tools, delivery, and failure destination.
6. The user can delete the task.
7. The user can view run history.
8. The user can view background tasks created by cron execution.
9. For a running task, the user can cancel it.

Success states:

- Toggle enabled is a quick action. Edit/delete/cancel are explicit confirmation actions.
- After a schedule edit, `nextRunAt` should refresh. A pure formatting change should not accidentally clear a pending slot.
- Run history and task ledger are not confused: a cron job is a scheduler definition; a background task is an execution record.

### 20.6 Core User Journey: Create A Scheduled Task From Conversation

Target user: a user naturally saying "remind me tomorrow morning" or "summarize this every day" inside chat.

Target path:

1. The user requests a scheduled task in a Chat/Code session.
2. The agent generates a cron job draft.
3. The UI shows a confirmation card with name, schedule, timezone, execution style, message, and delivery.
4. The user confirms or edits it.
5. The app creates the cron job.
6. The UI shows a creation success card in the current session and can jump to Scheduled Tasks detail.

Success states:

- The agent cannot silently create high-risk scheduled tasks.
- Delivery target must preserve the current live context, but pairing-store approval must not be used as a fallback automation recipient.
- Scheduled tasks requiring tool permissions must inherit or explicitly set tool policy.

### 20.7 Fields Scheduled Tasks Must Show

The list must show:

- Name
- Enabled
- Schedule summary
- Timezone
- Execution style: main / isolated / current / custom session
- Next run
- Last run
- Last status: ok / error / skipped / running / paused
- Delivery mode

Detail must show:

- Job id
- Created/updated time
- Full schedule definition
- Prompt/system event
- Model/thinking/tool restrictions
- Delivery target
- Failure notification destination
- Delete-after-run policy
- Run history
- Linked background tasks

### 20.8 Difference From Heartbeat / Background Tasks

UI copy must make these three concepts explicit:

- Scheduled Tasks / Cron Jobs: decide when something runs and persist the job definition.
- Background Tasks: record detached work execution status and result. This is a ledger, not a scheduler.
- Heartbeat: the main session's periodic wake/check mechanism. It is not the same as an independent cron job.

Related OpenClaw docs:

- `docs/automation/cron-jobs.md`
- `docs/automation/tasks.md`

## Explicitly Excluded Or Paused Settings Journeys

### Voice

Status: business area cancelled.

Recommendation:

- Do not include it in current user journeys.
- The UI should later remove or hide the Voice tab to avoid implying the voice business can be configured.

### Channels

Status: business area temporarily paused.

Recommendation:

- Do not include it in current user journeys.
- If Scheduled Tasks need a delivery target while channels are paused, provide an alternative path: session delivery, webhook, or clear unavailable state.
- The existing Channels tab in Settings is historical/temporary only and is not a current product entry point.

### Advanced

Status: the overall area is temporarily paused.

Recommendation:

- Do not treat Advanced as a user journey.
- Config editor, Skills, Debug, and similar areas remain internal/developer capabilities and should not enter the current product mainline.
- Split Cron Jobs out of Advanced and promote them into a standalone Scheduled Tasks journey.

## Current Journey Gap Priority

P0:

- Standalone Scheduled Tasks entry and complete create/edit/delete/detail/run-history journeys.
- Turn First Chat Message E2E from stub into active coverage.
- Real end-to-end validation for Code mode runtime readiness/permission error cards.

P1:

- Workspace persistence across restart.
- E2E journey docs and specs for Agent Authoring publish, mobile preview, and firmware publish.
- Failure-state journeys for device stream and LVGL preview.

P2:

- Cancel Voice in Settings, pause Channels, and hide or split Advanced.
- E2E coverage for provider dynamic model cache and default model settings.
- E2E coverage for update download/install states.
