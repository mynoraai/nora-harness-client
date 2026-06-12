# Journey 2: Main App Startup / Recovery State

This record describes the existing startup and recovery journey after onboarding has already been completed. It is the text source for a future one-to-one HTML wireframe update.

Implementation maturity: implemented.

Record status: ready for review before wireframe changes.

## Journey Scope

User entry: the user launches NoraHarness after first-run onboarding is complete, returns to the app after a restart, or refreshes the renderer while the desktop app is already running.

User goal: reach an operable Chat or Code surface, with the previous mode, relevant sessions, workspace context, Gateway status, active run state, and update prompts restored or recoverable.

End state: the user can continue a previous session, create a new one, recover Gateway/update issues, or respond to a restored runtime permission request.

## State Inventory

Main startup path:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main step | Journey 2 | App launch after completed onboarding | Opens or returns to the desktop app | Main app shell begins loading; Gateway banner may appear before status settles | The app confirms onboarding is complete and enters the main app path instead of first-run onboarding | `2` |
| `2` | Main step | Journey 2 | Persisted shell restore | Waits for startup state to resolve | The app restores either Chat or Code mode; sidebar/search/header surfaces appear | The app reads saved mode, settings, sessions, workspaces, layout preferences, and available session-backed tab state | `2.1`, `2.2`, or `2.3` |
| `3` | Main step | Journey 2 | Gateway connection state settles | Reviews the banner or continues if no banner is shown | Connected state hides the Gateway banner; reconnecting or unavailable states show a top banner with reason text | The app resolves local Gateway status and updates runtime availability | `4`, `3.1`, or `3.2` |
| `4` | Main step | Journey 2 | Active run recovery | Waits while the selected runtime is checked for in-flight work | If an active run is found, the chat surface can restore running indicators, streamed content, or a permission request card | The app asks the active runtime for recoverable runs and reattaches to replayable events when possible | `5`, `4.1`, or `4.2` |
| `5` | Main step | Journey 2 | Operable main surface | Continues work, starts a new task, switches mode, or opens Settings | Composer or creation entry is available; sidebar and mode switcher are usable; update notices may appear | The app is stable in the selected mode with the latest known gateway, session, workspace, and update state | End or `5.1` |

Mode restore branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Branch | `2` | Chat mode restored | Waits, searches, or selects a conversation | Chat mode is active; conversation sidebar, grouped sessions, main chat surface, and composer are available when a session is selected | Chat sessions are loaded and sorted; a valid active chat session is selected or a draft/empty state is prepared | `3` |
| `2.2` | Branch | `2` | Code mode restored | Waits, expands a workspace, or selects a code session | Code mode is active; workspace sidebar, working directory bar, tabs, right panel, and bottom panel areas appear | Code sessions and workspaces are loaded; workspace context is restored from saved workspace metadata or from session workspace path when needed | `3` |
| `2.3` | Branch | `2` | No restorable session for the active mode | Starts a new conversation or creates/selects a workspace/session | The active mode shows an empty or creation-ready state instead of a previous conversation | No valid active session can be selected for the restored mode, so the app offers the relevant creation entry | `5` |

Gateway recovery branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Branch | `3` | Gateway reconnecting | Waits while the app retries | Banner title reads `Reconnecting...`; attempt count and reason can be shown | The app schedules reconnect attempts and keeps the main UI visible with degraded runtime readiness | `3` or `3.2` |
| `3.2` | Branch | `3` | Gateway unavailable or errored | Opens Settings, retries later, or uses whatever UI remains available | Banner title reads `Gateway unavailable`; reason text explains that the local gateway could not be reached after retries | The app records a disconnected/error state; runtime-backed actions may remain unavailable until recovery | `3.2.1` or `5` |
| `3.2.1` | Child branch | `3.2` | Gateway restart from Settings | Opens Settings and clicks restart from the General gateway area | Settings shows Gateway status, last check information, agent count when known, and a restart control | The app requests a gateway restart and then refreshes health/status | `3` |

Runtime and update recovery branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.1` | Branch | `4` | Permission request recovered | Approves or denies the restored request | A permission request appears in the conversation as an approval-style message/card | The user's choice is submitted and the recovered run either continues or stops according to that choice | `5` |
| `4.2` | Branch | `4` | Active run reconnect failed | Retries, resends, or starts a new request | The chat surface shows a recovery/error state instead of a live restored stream | The app cannot reattach the old run; the user can retry recovery or continue manually | `5` |
| `5.1` | Branch | `5` | Update prompt or update settings state appears | Defers, checks, downloads, or installs/restarts | A toast or Settings update area shows available, checking, downloading, downloaded, installing, or error state | The app updates desktop update state while keeping the main surface available | `5.1.1`, `5.1.2`, or `5` |
| `5.1.1` | Child branch | `5.1` | Update downloaded and ready to install | Clicks restart/install or defers | A ready-to-install prompt offers a restart/install action | The app begins install/restart only after the user chooses the action | `5` |
| `5.1.2` | Child branch | `5.1` | Update check/download/install error | Dismisses, retries later, or opens Settings | Update UI shows an error state while the app remains usable | The update state remains recoverable and does not block Chat or Code startup | `5` |

## Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Chat startup | `1 -> 2 -> 2.1 -> 3 -> 4 -> 5` | The user lands in Chat mode with restored or newly prepared conversation state. |
| Code startup | `1 -> 2 -> 2.2 -> 3 -> 4 -> 5` | The user lands in Code mode with restored workspace/session context and usable code panels. |
| No restorable active session | `1 -> 2 -> 2.3 -> 5` | The app remains usable and offers a creation entry instead of showing a broken restored session. |
| Gateway reconnect succeeds | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3 -> 4 -> 5` | The user sees the reconnect banner temporarily, then continues once the Gateway returns to connected state. |
| Gateway unavailable with restart recovery | `1 -> 2 -> 2.2 -> 3 -> 3.2 -> 3.2.1 -> 3 -> 4 -> 5` | The user recovers the Gateway from Settings and returns to the restored Code surface. |
| Gateway unavailable without immediate recovery | `1 -> 2 -> 2.1 -> 3 -> 3.2 -> 5` | The main UI remains visible, but runtime-backed actions stay degraded until the Gateway is recovered. |
| Active permission request recovered | `1 -> 2 -> 2.1 -> 3 -> 4 -> 4.1 -> 5` | The user handles the restored approval request and returns to an operable conversation. |
| Active run reconnect failed | `1 -> 2 -> 2.1 -> 3 -> 4 -> 4.2 -> 5` | The user sees recovery failure and can retry, resend, or continue with a new request. |
| Update downloaded after startup | `1 -> 2 -> 2.1 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 5` | The user can restart/install the downloaded update or defer and keep working. |
| Update error after startup | `1 -> 2 -> 2.2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.2 -> 5` | The update problem is visible and recoverable without blocking the restored app surface. |

## State Details

### 1 Launch After Onboarding

User input: the user opens NoraHarness after onboarding has already been completed, or returns after the app restarts.

Visible interface state: the app does not show the first-run onboarding flow. It begins loading the normal desktop shell, and a Gateway banner may be visible while connection status is being resolved.

Client state change: the app confirms that onboarding is complete and proceeds to the main app initialization path.

Exit: startup state restore begins.

### 2 Persisted Shell Restore

User input: the user waits for the app to restore its previous working context.

Visible interface state: the app restores the previously selected Chat or Code mode. Search, sidebar, mode switcher, and the main content region appear as the restored state resolves.

Client state change: saved preferences and app data are read. This includes selected mode, sessions, workspaces, layout/settings preferences, and any session-backed tab state that can be reconstructed.

Exit: the app enters the restored mode branch, or a creation-ready state if no session can be restored.

### 2.1 Chat Mode Restored

User input: the user can search, select a conversation, or continue in the active conversation.

Visible interface state: Chat mode is selected. The sidebar shows grouped conversations when available, and the main chat surface shows the active conversation, an empty state, or a draft-ready composer.

Client state change: chat sessions are loaded and sorted. If the previously active chat session is still valid, it remains active; otherwise the app chooses a valid chat session or prepares a draft/empty state.

Exit: Gateway connection status settles.

### 2.2 Code Mode Restored

User input: the user can expand a workspace, select a code session, open tabs, or continue in the restored code session.

Visible interface state: Code mode is selected. The workspace sidebar, working directory bar, tab strip, file/diff/preview area, right panel, and bottom terminal/log area are available according to the restored workspace state.

Client state change: code sessions and workspaces are loaded. When a session points to a workspace path, the app restores or reconstructs the workspace context needed to display it.

Exit: Gateway connection status settles.

### 2.3 No Restorable Session

User input: the user starts a new conversation, creates a workspace, or selects another available item.

Visible interface state: the selected mode shows an empty or creation-ready state instead of a stale conversation.

Client state change: no valid active session is selected for the current mode. The app keeps the mode active and exposes the relevant creation entry.

Exit: the app is usable for creating new work.

### 3 Gateway Connection State Settles

User input: the user reviews the banner if one appears, or ignores this state when no banner is shown.

Visible interface state: if connected, the Gateway banner is hidden. If reconnecting or unavailable, the banner appears above the main shell.

Client state change: Gateway status is checked and reflected in the UI. Runtime availability follows that status.

Exit: active run recovery begins when Gateway state allows it, or the user remains in a degraded/recoverable Gateway state.

### 3.1 Gateway Reconnecting

User input: the user waits while reconnect attempts run.

Visible interface state: the top banner reads `Reconnecting...`, includes retry context when available, and keeps the rest of the app visible.

Client state change: reconnect attempts continue until the Gateway connects or the retry path is exhausted.

Exit: returns to Gateway connected status, or moves to Gateway unavailable.

### 3.2 Gateway Unavailable Or Errored

User input: the user can open Settings, wait, or continue with non-runtime UI areas.

Visible interface state: the top banner reads `Gateway unavailable` and includes a reason when known. Runtime-backed actions may appear unavailable or fail until Gateway recovery succeeds.

Client state change: Gateway status is marked disconnected or errored. The app preserves the restored shell while preventing the user from losing context.

Exit: the user may restart Gateway from Settings or continue in a degraded app state.

### 3.2.1 Gateway Restart From Settings

User input: the user opens Settings and uses the Gateway restart control.

Visible interface state: Settings shows the Gateway status area with current health information and a restart control.

Client state change: a restart request is sent and the app refreshes Gateway health after the request.

Exit: Gateway status settles again.

### 4 Active Run Recovery

User input: the user waits while the app checks whether a previous run is still active.

Visible interface state: if no active run exists, the user simply lands in the normal session surface. If a run exists, the conversation can restore running indicators, streamed content, or a pending permission request.

Client state change: the app checks the active runtime for recoverable work and reconnects to replayable events when possible.

Exit: the app reaches the normal surface, a restored permission request, or a reconnect failure recovery state.

### 4.1 Permission Request Recovered

User input: the user approves or denies the restored request.

Visible interface state: the approval request appears in the conversation as part of the chat flow.

Client state change: the app submits the user's choice and resumes or stops the recovered run according to that choice.

Exit: the user returns to the operable conversation surface.

### 4.2 Active Run Reconnect Failed

User input: the user retries recovery, resends the request, starts a new task, or continues manually.

Visible interface state: the chat surface shows a recovery or error state instead of a live restored stream.

Client state change: the old run cannot be reattached. The app keeps the session visible and leaves recovery/manual continuation available.

Exit: the user returns to an operable main surface.

### 5 Operable Main Surface

User input: the user continues work, starts a new task, switches mode, opens Settings, or responds to any update prompt.

Visible interface state: the restored mode is usable. Chat shows conversation/composer surfaces; Code shows workspace/session surfaces. Update notices can appear without blocking work.

Client state change: the app is stable with the latest known mode, session, workspace, Gateway, active run, and update state.

Exit: the journey is complete unless the user enters the update branch.

### 5.1 Update Prompt Or Update Settings State

User input: the user checks, defers, downloads, installs, restarts, or dismisses update UI.

Visible interface state: update state is visible through a toast or Settings update area. The app can show checking, available, downloading, downloaded, installing, or error states.

Client state change: desktop update state changes independently from Chat or Code startup state.

Exit: the user returns to the operable main surface.

### 5.1.1 Update Downloaded And Ready To Install

User input: the user clicks restart/install, or defers.

Visible interface state: a ready-to-install prompt offers a restart/install action.

Client state change: install/restart begins only after the user chooses it.

Exit: the app continues normally if deferred, or restarts as part of installation.

### 5.1.2 Update Error

User input: the user dismisses, retries later, or opens Settings.

Visible interface state: update UI shows an error state while the rest of the app remains usable.

Client state change: the update state remains recoverable and does not block restored Chat or Code use.

Exit: the user returns to the operable main surface.
