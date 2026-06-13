# NoraHarness Electron User Journey Inventory (Version 2)

This version records the implemented NoraHarness Electron journeys in user-behavior terms: where the user enters, what they do, what visible client state changes, and where the journey ends.

This version expands only the currently confirmed primary journeys. Mobile Preview has been removed from the primary journey set, and Firmware Publish is merged into Journey 09's unified publish center.

## Evidence Sources

- `apps/electron/src/renderer/src/App.tsx`
- `apps/electron/src/renderer/src/components/**`
- `apps/electron/src/renderer/src/stores/**`
- `apps/electron/e2e/journeys/*.md`
- `docs/hardware_harness/spec/noraharness/*.md`

## Confirmed Primary Journeys

### 01. First Launch / Onboarding

**User entry**: The user installs and opens NoraHarness for the first time. The client detects that onboarding has not been completed locally.

**User behavior**: The user starts from Welcome, selects or enters an AI provider, verifies the key, and continues through Provider, Personality, Notifications, and Completion.

**Client state changes**:

- The first screen moves from Welcome into the step-by-step onboarding shell.
- In the Provider step, verification and continue actions stay unavailable until the user enters a valid-looking key or endpoint.
- After the user starts verification, the UI enters a verifying state; success shows verified feedback and enables continue, while failure keeps the user on the same step with an inline error.
- Each Continue marks the current step complete and swaps the main content to the next step; Back returns to the previous step while preserving or resetting that step state as appropriate.
- After the final step, the client writes onboarding complete locally and switches from the onboarding shell to the main app shell.

**End state**: The user lands in the main app with the sidebar and Chat/Code surface visible. Later launches skip Welcome/onboarding unless local data is reset.

### 02. Main App Startup / Recovery State

**User entry**: The user launches the app after onboarding is complete, or the renderer refreshes/restarts while the app is running.

**User behavior**: The user waits for the main surface to load and reviews the Gateway banner, current mode, recent conversations, update notice, and available input area.

**Client state changes**:

- The client first enters a startup check and reads persisted onboarding, global mode, sessions, workspaces, open tabs, and settings.
- The Gateway area moves from checking/starting to connected, disconnected, or error; each state changes banner copy, retry/restart actions, and some runtime capabilities.
- The app restores the previous Chat or Code mode; if no session can be restored, it shows an empty state and creation entry.
- If an active runtime run exists, the client tries to reconnect; success restores running indicators, the message stream, or permission request card, while failure shows retry/resend recovery.
- If an update is available, the UI shows an update notice; the user can defer it or enter download/install.

**End state**: The client rests on an operable main surface. The user can continue the previous session, create a new one, or recover from Gateway/update state.

### 03. Login / Account Authentication

**User entry**: The user clicks the account login entry in the sidebar, or a Catalog publishing action blocks on account authentication.

**User behavior**: The user starts login, completes browser or embedded authentication, then can inspect account state, sign out, or retry the action that was blocked before login.

**Client state changes**:

- When signed out, the sidebar shows a login entry; account-required features show a login requirement instead of continuing submission.
- After the user clicks login, the client enters signing in / waiting state and prevents duplicate clicks on related buttons.
- After login succeeds, the account area switches to signed-in state and shows identity or avatar; account-dependent actions move from blocked to available.
- If login fails or is cancelled, the UI returns to signed-out state and shows failure or cancellation feedback at the original entry.
- After sign out, local account state is cleared and Catalog publishing or other account-required entries return to login-gated state.

**End state**: The user is clearly signed in or signed out. Every account-required entry can show continue, block, or re-login based on that state.

### 04. Chat Mode

**User entry**: The user enters the default Chat mode in the main app, or switches back to Chat from the sidebar mode switcher.

**User behavior**: The user views the conversation list, searches or creates a conversation, types in the composer, attaches content or commands, selects runtime/model/thinking, sends a message, and handles stop, permission requests, tool execution, and recovery.

**Client state changes**:

- The sidebar shows the Chat conversation list, search box, and new conversation entry; when no conversation exists, the main area shows an empty state and composer.
- After the user selects a conversation, the main area loads its messages; older messages can continue loading in pages.
- After the user types content, the send button becomes available; after send, the user message is appended immediately and the composer enters running constraints.
- While running, the UI shows assistant pending/streaming state, stop button, tool blocks, permission request cards, and possible file/command results.
- If the runtime requires user approval, the message stream stops at approval state; after approval or denial, the client continues running or shows the denied end state.
- If the user stops the run, the current assistant turn ends as stopped/cancelled; if the run fails, the stream shows an error card with retry or regenerate.
- On success, the assistant message moves from streaming to finalized, the composer becomes editable again, and the title, timestamp, and sidebar summary refresh.

**End state**: The conversation rests after a completed, stopped, or failed turn. The user can continue sending, retry, switch conversations, or leave Chat mode.

### 05. Code Mode / Workspace

**User entry**: The user clicks Code in the sidebar AgentModeSelector, or enters from an existing Code workspace/session.

**User behavior**: The user creates or selects a workspace, picks a local directory, creates a Code session, and sends a code-related request inside that workspace.

**Client state changes**:

- After mode switches from Chat to Code, the layout becomes the Code workspace shell; the sidebar shows workspaces instead of ordinary Chat conversations.
- When no workspace exists, the workspace panel shows an empty state and Create Workspace button.
- After the user opens the create workspace dialog, the UI waits for folder selection; the create button stays unavailable until a directory is selected.
- After the user selects a directory, the path field is filled, the workspace name is inferred or editable, and the create button becomes available.
- After creation succeeds, the dialog closes, the sidebar adds and expands the workspace, and the client creates a draft code session with a new TabBar tab.
- After the user sends from the Code composer, the draft session materializes, the working directory bar shows the current workspace, and run state moves from idle to starting/running.

**End state**: The user rests in an open Code session with workspace, active tab, working directory, and composer ready for continued work.

### 06. Runtime / Model / Permission

**User entry**: The user opens runtime/model selectors in the Chat or Code composer, or triggers runtime readiness, provider checks, or permission requests while sending.

**User behavior**: The user selects runtime, model, and thinking level; after sending, the user responds to provider missing, runtime startup failure, or tool permission prompts.

**Client state changes**:

- The Runtime selector shows the current runtime; after the user switches it, composer and conversation state record the new runtime.
- The Model selector shows the current provider/model; after selection, later sends use the new model configuration.
- Before sending, if provider is not ready, the UI shows missing configuration, missing authentication, or unavailable model and blocks the run.
- After send, runtime state moves from idle to starting; success enters running, while failure becomes failed and shows the cause in the message stream or status bar.
- When a tool or command needs permission, the client inserts a permission request card; approval continues the run, denial ends that tool step and shows the result in the stream.
- After completion, runtime returns to idle or ready; if a recoverable run exists, refresh enters reconnecting and then restores running or failed.

**End state**: Runtime, model, and permission choices are visible in the UI. The current turn is complete or rests in a clear recoverable, retryable, or approval-required state.

### 07. Code Workbench Panels

**User entry**: The user inspects the right workbench and bottom panels inside a Code session.

**User behavior**: The user switches among Files, Changes, Diff/File tabs, Terminal, and Device Log to inspect files, changes, command output, and device logs.

**Client state changes**:

- After Code loads, the right panel shows a default tab; clicking another tab changes panel content without changing the active Code session.
- The Files panel shows the workspace file tree; selecting a file opens a file tab or preview.
- The Changes panel shows the change list; selecting a change opens a diff view and keeps selection and diff content in sync.
- The Terminal panel shows the command area for the current session; while running it streams output and active state, and after completion it preserves results.
- The Device Log panel shows log stream, filters, pause/resume, and autoscroll state; changing filters narrows or restores visible log rows.
- Collapsing, expanding, or resizing panels changes layout size and visible area without losing the active session or message stream.

**End state**: The user rests in a selected workbench view. File, diff, terminal, or log state remains aligned with the current Code session.

### 08. Agent Authoring

**User entry**: The user clicks Edit Agent in a Code workspace, or enters Agent Authoring from an agent-configuration entry.

**User behavior**: The user edits Soul, Style, Tools, Heartbeat, Bootstrap, Skills, and Capabilities, reviews capability differences, adds or tests skills, and saves changes.

**Client state changes**:

- Entering Authoring switches the UI from a code session to the agent configuration editor and loads the current workspace agent files and config state.
- Switching among Soul, Style, Tools, Heartbeat, Bootstrap, and other areas updates the main editor and marks the matching nav item active.
- After the user edits text or config, the page enters dirty state; save button or status changes from clean to unsaved/saving.
- After save succeeds, dirty state clears and saved feedback appears; save failure preserves user input and shows an error.
- The Skills area lists existing skills; the user can create, edit, preview, or run probes, with pending during execution and success/failure afterward.
- The Capabilities area shows agent and device/manifest capability state; refreshing updates lists, difference hints, and availability.

**End state**: Agent configuration is clearly saved, failed-to-save, or dirty. The user can return to the Code session or continue to publishing.

### 09. Unified Publish Center / Catalog Review / Firmware Publish

**User entry**: The user clicks Publish in Agent Authoring, or enters the unified publish center from a publishing status row.

**User behavior**: The user confirms login state, then reviews App record, Icon & screenshots, Agent package, and Firmware in one publish center; the user can edit app info/media, submit app/agent package review, review the result, and continue to production package or firmware publish from the same center.

**Client state changes**:

- When signed out, publish does not submit directly and instead shows a login gate; after login, the user returns to publishing.
- After entering the publish center, the UI shows App record, Icon & screenshots, Agent package, and Firmware rows; each row shows draft, ready, missing, pending, approved, rejected, revoked, optional, or similar status based on completeness and backend state.
- When editing app info/media, the dialog shows display name, description, icon, and screenshots; changes enter unsaved state, saving disables the button, and success refreshes the row.
- After submitting app or agent package review, the submit button becomes pending; success changes state to submitted/in review and shows later editing constraints.
- When review returns approved/rejected/revoked, the publish center reflects the state through badges, copy, and available actions; approved rows can proceed to publish to production.
- The Firmware row reads version, channel, and device metadata from build/manifest; incomplete form or build output disables publish, publishing shows uploading/publishing, success refreshes available/latest state, and failure remains on the row with retry.

**End state**: App record, agent package, and firmware all sit in one publish center with clear states: unsubmitted, in review, approved, rejected, revoked, ready for production, published, or failed and retryable.

### 10. Device / PlatformIO / Logs

**User entry**: The user opens the Device tab or Device Log panel in the Code workbench.

**User behavior**: The user checks PlatformIO state, refreshes devices, inspects connected devices, device metrics, USB fallback, and logs.

**Client state changes**:

- When the Device tab opens, the client loads PlatformIO availability, installation state, and device connection state.
- When PlatformIO is unavailable, the UI shows install/repair guidance; when available, it shows ready state and available device actions.
- While refreshing devices, the UI enters loading; completion shows connected/disconnected device cards, firmware version, memory, latency, and similar information.
- Opening logs starts receiving device or USB monitor output; pausing stops visual autoscroll, and resuming continues it.
- Changing filters updates the visible log list immediately by level, source, or search term.
- If the device disconnects or probing fails, device cards and log area switch to disconnected/error state while preserving refresh or retry.

**End state**: The user can tell whether the development device, PlatformIO, and log path are ready, missing, disconnected, or error.

### 11. LVGL Preview

**User entry**: The user opens the Preview tab or LVGL Preview panel in the Code workbench.

**User behavior**: The user starts preview, triggers build/capture/rebuild, and inspects screenshot result, errors, and logs.

**Client state changes**:

- When the Preview tab opens, the client checks LVGL preview capability, dependencies, and current workspace state.
- If preview is unavailable, the UI shows missing dependency or non-runnable reason and disables the start action.
- After the user starts or rebuilds, the UI enters building/capturing state and shows progress steps or a loading overlay.
- On success, the screenshot area changes from empty state to the latest preview image and shows metadata such as time, size, or source.
- If build or capture fails, the UI shows error state, log summary, and retry; any old screenshot is preserved or replaced with an error placeholder according to UI rules.

**End state**: The user sees the latest LVGL screenshot, or sees a clear failure reason and retry action.

### 12. Settings - General

**User entry**: The user opens Settings from the app settings entry and lands on the General tab.

**User behavior**: The user checks Gateway status, restarts Gateway, and configures proxy, login item, dock, debug tools, and other general settings.

**Client state changes**:

- After Settings opens, the General tab loads current local settings and Gateway state.
- Gateway state updates from loading to running, stopped, restarting, or error; the restart button enables or disables based on state.
- After the user toggles login item, dock, debug tools, or similar switches, the switch immediately reflects the target state; saving or failure shows matching feedback.
- After the user edits proxy or similar fields, the form enters changed state; save success returns it to clean, while failure preserves input and shows an error.
- After Settings closes, the main app updates dock, debug UI, or connection behavior based on the new settings.

**End state**: General settings are saved or left in a retryable error state. Current Gateway state remains visible in Settings.

### 13. Settings - Workspace Files

**User entry**: The user opens the Workspace Files tab in Settings.

**User behavior**: The user views and edits SOUL, IDENTITY, USER, AGENTS, TOOLS, HEARTBEAT, MEMORY, and other workspace files.

**Client state changes**:

- After the tab opens, the client loads the current workspace file list and selected file content.
- Selecting a different file swaps editor content and updates current filename and description state.
- After editing content, the editor enters dirty state and save/reset actions become available.
- During save, the UI enters saving; success clears the dirty marker, while failure preserves edited content and shows an error.
- If a file is missing, the UI shows missing/empty state and offers create or restore when supported.

**End state**: The user finishes viewing, saving, resetting, or rests with unsaved edits for a workspace file.

### 14. Settings - Providers

**User entry**: The user opens the Providers tab in Settings, or jumps to Provider configuration from a provider-missing prompt.

**User behavior**: The user adds, edits, or removes providers, fills API key, base URL, enabled models, and selects a default model.

**Client state changes**:

- After Providers opens, the client loads existing providers and default model state.
- When adding a provider, the form starts empty; save/verify stays unavailable until required fields are present.
- After API key or base URL input, verification becomes available; pending is shown during verification, success shows verified, and failure shows inline error.
- After changing enabled models or default model, list and default badge update; save success returns configuration to clean state.
- When deleting a provider, the UI asks for confirmation; after deletion succeeds, the provider is removed and related default model may fall back or require reselection.

**End state**: Provider configuration is ready for runs, or clearly indicates missing authentication, verification failure, or no default model waiting for user action.

### 15. Settings - Permissions

**User entry**: The user opens the Permissions tab in Settings, or arrives from a runtime permission/system permission prompt.

**User behavior**: The user configures exec approval and allowlist, and reviews or handles notifications, microphone, accessibility, and other system permissions.

**Client state changes**:

- After Permissions opens, the client loads current execution policy, allowlist, and system permission state.
- After changing exec approval policy, the UI shows the new policy choice and after saving it affects whether later tool execution requires confirmation.
- Adding or removing allowlist entries updates the list immediately; save failure restores or flags unsaved state.
- When system permission is missing, the UI shows missing/needs action and offers open system settings or recheck.
- After the user grants permission and returns to the app, recheck changes state from missing to granted; if still denied, it remains blocked with explanation.

**End state**: Permissions shows final execution policy and system permission state. Later Chat/Code runs follow these permissions as auto-run, request approval, or blocked.

### 16. Settings - About / Updates

**User entry**: The user opens the About / Updates tab in Settings, or clicks an update notice in the main UI.

**User behavior**: The user checks version information, checks for updates, downloads an update, and chooses install or later.

**Client state changes**:

- After About/Updates opens, the UI shows current app version, build information, and updater state.
- After the user checks for updates, state moves from idle to checking; if none exists, it shows up to date.
- When an update exists, the UI shows new version information and a download entry; after clicking download it enters downloading and shows progress.
- After download completes, state changes to ready to install and the install/restart button becomes available.
- If checking or download fails, state becomes error and the UI shows failure reason and retry.

**End state**: The user confirms the app is up to date, finishes download and waits to install, or rests on a retryable update error.

## Boundary Notes Preserved From The Original Inventory

The following items were treated as partially implemented, paused, historical, or outside the current primary journey set, so this version does not expand them:

- Scheduled Tasks / Cron Jobs
- Channels
- Voice
- Advanced Config / Skills / Debug
- Cloud Deploy
- Settings Extensions / ACP Runtime Management
- Mobile Preview

## Split Guidance

When turning a journey into an HTML interaction wireframe, keep these fields explicit:

1. Entry: the visible control or state where the user starts.
2. Preconditions: signed out, provider missing, Gateway disconnected, no workspace, and similar states.
3. User actions: click, type, select, upload, approve, deny, retry.
4. Client state changes: loading, disabled/enabled, dirty/saving/saved, pending/success/error, running/stopped/reconnecting.
5. End state: whether the user can continue, retry, close, or remains blocked.
