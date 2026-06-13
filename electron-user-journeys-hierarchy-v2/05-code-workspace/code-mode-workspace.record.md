# Journey 05: Code Mode / Workspace

This Markdown file is the text counterpart to `code-mode-workspace.wireframe.html`. It documents the current Code mode workspace journey in user-visible terms. Each section ID is intended to match a card or detail frame in the wireframe.

User entry: the user clicks `Code` in the sidebar mode selector, or returns to an existing Code workspace/session.

User goal: the user creates or selects a local workspace, opens a Code session for that workspace, and sends the first code-related request from the Code composer.

End state: the user rests in an open Code session with a selected workspace, active tab, working directory, right panel, bottom panel, and composer ready for continued work.

Implementation maturity: implemented.

## Numbering And Route Tables

Main journey steps use plain numbers. Branches, detail states, language states, and recovery summaries use decimal IDs scoped to the step where that state starts. Sequential follow-up states use an additional decimal level. The same ID should be used in the state inventory, route table, section heading, and matching wireframe card.

Main path states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 05 | Entry into Code mode. | Clicks `Code`, or reopens the app while Code was the active mode. | The mode selector highlights Code and the ordinary Chat conversation list is replaced by workspace-focused navigation. | The app switches the active work surface from Chat to Code and prepares workspace/session state. | `2` |
| `2` | Main path | Journey 05 | Code workspace shell appears. | Reviews the Code layout or waits for saved state to settle. | The shell shows the workspace sidebar, center tab area, working directory bar area, right panel, bottom panel, and Code composer. | The app loads available workspaces, sessions, tabs, and panel preferences for Code mode. | `3` or `2.1` |
| `3` | Main path | Journey 05 | No active workspace is ready. | Looks for a workspace entry or creation action. | The workspace area shows no selected workspace or an empty workspace state, with `Create Workspace` available. | The app has Code mode active but does not yet have a usable local directory for code work. | `4` |
| `4` | Main path | Journey 05 | Create Workspace dialog opens. | Clicks `Create Workspace`. | A dialog titled `Create Workspace` asks for a workspace name and folder; `Create` is disabled until a folder is selected. | Workspace creation is pending user folder selection and does not yet change the active workspace. | `5`, `4.1`, or `4.2` |
| `5` | Main path | Journey 05 | Local directory is selected. | Uses `Browse` or the folder picker to choose a local directory. | The folder field is filled, the workspace name is inferred or editable, and `Create` becomes available. | The selected folder and workspace name are held in the dialog as pending workspace details. | `6`, `5.1`, or `5.2` |
| `6` | Main path | Journey 05 | Workspace is created and selected. | Clicks `Create`. | The dialog closes; the new workspace appears in the sidebar, is selected or expanded, and the Code frame shows its path. | The workspace list gains the new workspace and Code mode has an active local directory. | `7` or `6.1` |
| `7` | Main path | Journey 05 | Draft Code session is ready. | Reviews the new center tab or selects the draft session row. | A Code session tab is active; the composer shows `Assign a task or ask any question`; model and thinking controls remain visible. | A workspace-scoped draft Code session is active but has not yet become a persisted conversation turn. | `8` |
| `8` | Main path | Journey 05 | First Code request is sent. | Types a code-related prompt and clicks the send control. | The user prompt appears in the session, the composer enters running constraints, and the session shows starting or running feedback. | The draft session materializes into an active Code run tied to the selected workspace. | `8.3` then `9`, or first-run branch |
| `9` | Main path | Journey 05 | Open Code workspace is ready for continued work. | Continues prompting, switches tabs, opens files, checks panels, or pauses. | The selected workspace, active Code session, working directory, right panel, bottom panel, and composer remain visible. | Code mode is stable with the current workspace/session context preserved. | End |

Workspace create/select states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Branch journey | `2 Code Workspace Shell` | Existing workspace is available and selected. | Selects a workspace row or continues with the restored workspace. | The sidebar highlights the workspace; its sessions are visible; the working directory bar shows its folder path. | Code mode adopts that workspace as the active local directory. | `7` |
| `2.2` | Branch journey | `2 Code Workspace Shell` | Workspace list is present but collapsed or not focused. | Expands a workspace group, collapses all, expands all, or searches. | Workspace groups show counts and session rows; expanding a group reveals its Code sessions. | The visible workspace list changes without leaving Code mode or changing the active session unless a row is selected. | `2.1`, `3`, or `7` |
| `3.1` | Branch journey | `3 No Active Workspace` | Empty workspace state prompts creation. | Clicks `Create Workspace`. | The empty state explains that a workspace is needed for local files and commands, with `Create Workspace` as the primary action. | The app stays in Code mode and prepares to open the creation dialog. | `4` |
| `3.2` | Branch journey | `3 No Active Workspace` | User chooses an existing workspace instead of creating one. | Selects an available workspace row. | The selected workspace becomes highlighted and its sessions or draft entry become visible. | Code mode switches to the selected workspace context. | `7` |

Folder picker cancel, invalid, and already-exists states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.1` | Branch journey | `4 Create Workspace Dialog` | User cancels the dialog before choosing a folder. | Clicks `Cancel`, closes the dialog, or presses Escape. | The dialog closes and the user returns to the previous Code workspace or empty state. | No workspace is created and any unsaved dialog input is discarded. | `3` or `2` |
| `4.2` | Branch journey | `4 Create Workspace Dialog` | Folder picker is opened. | Clicks `Browse` or the folder field action. | The product waits for the native folder picker result; the Create Workspace dialog remains the return point. | Workspace creation remains pending while folder selection is unresolved. | `4.2.1` or `5` |
| `4.2.1` | Branch follow-up | `4.2 Folder Picker Opened` | Folder picker is canceled. | Cancels the native folder picker. | The Create Workspace dialog returns with no folder selected and `Create` still disabled. | No folder is stored for the pending workspace. | `4` |
| `5.1` | Branch journey | `5 Local Directory Selected` | Selected folder is invalid or unavailable. | Attempts to create with a folder that cannot be used. | The dialog stays open and shows a folder-related error; the user-entered name and path remain available for correction. | Workspace creation does not complete and the previous workspace context is preserved. | `5` or `4` |
| `5.2` | Branch journey | `5 Local Directory Selected` | Selected folder already belongs to an existing workspace. | Attempts to create with a duplicate folder. | The dialog or workspace area indicates that the workspace already exists or can be selected instead. | The app avoids creating a duplicate workspace for the same folder. | `5.2.1` or `5` |
| `5.2.1` | Branch follow-up | `5.2 Workspace Already Exists` | User selects the existing workspace. | Chooses the existing workspace from the sidebar or recovery action. | The existing workspace is highlighted and its sessions are shown. | Code mode switches to the already-created workspace context. | `7` |
| `6.1` | Branch journey | `6 Workspace Created And Selected` | Workspace creation fails after the user clicks Create. | Reviews the error, edits the fields, retries, or cancels. | The dialog remains open with a failure message and keeps the selected folder visible. | No new workspace is added; the prior Code state remains intact. | `5` or `4.1` |

Session and tab creation states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `7.1` | Branch journey | `7 Draft Code Session Ready` | User starts a new Code session from a workspace control. | Clicks the workspace plus control or a new-session action. | A new Code session tab opens or becomes active for the selected workspace. | A workspace-scoped draft Code session is created or reused. | `7` |
| `7.2` | Branch journey | `7 Draft Code Session Ready` | User opens an existing Code session. | Clicks a session row under the selected workspace. | The matching center tab opens or becomes active and the session history appears. | The selected Code session becomes active while staying tied to its workspace. | `9` or `8` |
| `7.3` | Branch journey | `7 Draft Code Session Ready` | Tab bar plus is used while a workspace is active. | Clicks the plus control in the center tab bar. | A new Code chat tab appears in the tab strip and becomes active. | A new draft session is associated with the active workspace. | `7` |
| `7.4` | Branch journey | `7 Draft Code Session Ready` | Tab creation is requested without an active workspace. | Clicks the tab plus while no workspace is selected. | The workspace creation dialog or empty workspace state takes focus instead of opening an unscoped Code session. | The app blocks unscoped Code session creation until a workspace is selected or created. | `4` or `3` |
| `7.5` | Branch journey | `7 Draft Code Session Ready` | No active tab is available. | Closes tabs or enters Code mode with no valid active tab. | The center area shows a no-active-tab placeholder or a prompt to select/create a Code session. | Code mode remains open but does not attach the composer to a missing session. | `7.1`, `7.2`, or `7.3` |

First code run states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `8.1` | Branch journey | `8 First Code Request Sent` | Composer is empty or not sendable. | Leaves the composer empty or removes all prompt text. | The send control is unavailable while model and thinking controls remain visible. | No Code run starts and the draft session remains draft-ready. | `7` or `8` |
| `8.2` | Branch journey | `8 First Code Request Sent` | Runtime, provider, or model readiness blocks the send. | Attempts to send while a prerequisite is missing. | The session shows a readiness prompt or recovery message instead of starting normal Code work. | The first Code run is blocked until the readiness issue is resolved. | `8` |
| `8.3` | Branch journey | `8 First Code Request Sent` | First run starts successfully. | Sends a valid prompt in the selected workspace. | The prompt appears in the session; assistant running, thinking, command, tool, or status content begins to appear. | The run becomes active and is attached to the selected workspace/session. | `9` |
| `8.4` | Branch journey | `8 First Code Request Sent` | First run fails before useful output appears. | Reviews the failure, retries, edits the prompt, or starts a new session. | The session shows an inline error or failed state while the workspace and composer remain available. | The failed turn is recorded or kept visible without removing the workspace context. | `8`, `7.1`, or `9` |
| `8.5` | Branch journey | `8 First Code Request Sent` | User stops the first running Code task. | Clicks stop while the run is active. | The session shows the stopped turn and the composer becomes usable again. | The active run ends in a stopped state while the workspace/session remains selected. | `9` |

Detail states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.3` | Detail state | `2 Code Workspace Shell` | Code layout regions. | Reads or uses the workspace sidebar, center tabs, right panel, bottom panel, and composer. | Code mode presents a workspace tree/list on the left, tabbed work in the center, `All Files`, `Changes`, and `Device` on the right, and `Terminal` / `Device Log` below. | The selected workspace/session and panel state remain aligned. | Any Code workspace state |
| `4.3` | Detail state | `4 Create Workspace Dialog` | Dialog controls and keyboard behavior. | Uses folder selection, edits the name, clicks Create/Cancel, presses Enter, or presses Escape. | `Create Workspace`, folder field, workspace name, `Browse`, `Cancel`, and `Create` controls remain within a focused dialog. | The dialog either submits valid workspace details or returns to the prior Code state. | `5`, `6`, or `4.1` |
| `7.6` | Detail state | `7 Draft Code Session Ready` | Tab strip behavior. | Selects, closes, or creates tabs. | Chat, file, diff, and authoring tabs can appear in the same center strip; the active tab is visually marked. | Tab state changes without losing the selected workspace. | `7`, `7.2`, `7.5`, or `9` |
| `8.6` | Detail state | `8 First Code Request Sent` | Composer controls. | Types a prompt, adds context, changes model/thinking, sends, or stops. | The composer shows placeholder text, attachment/add control, model pill, thinking control, and send/stop affordance. | Composer readiness follows prompt text and run state. | `8`, `8.1`, `8.3`, or `8.5` |

State language states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Shared Code workspace labels. | Reads navigation, controls, tabs, and status labels. | Labels include `Chat`, `Code`, `Workspaces`, `Create Workspace`, `Search conversations...`, `All Files`, `Changes`, `Device`, `Terminal`, `Device Log`, and `Assign a task or ask any question`. | The app uses the same labels across entry, creation, session, and panel states. | Any state |
| `4.4` | State language | `4 Create Workspace Dialog` | Workspace creation labels. | Reads the dialog and chooses an action. | Labels include `Create Workspace`, `Browse`, `Cancel`, `Create`, folder/path, workspace name, and disabled Create state. | Creation copy reflects whether a folder has been selected. | `4`, `5`, or recovery branch |
| `8.7` | State language | `8 First Code Request Sent` | First-run status labels. | Reads run state and chooses retry, stop, or continue. | Labels include draft, ready, starting, running, completed, stopped, failed, retry, and send. | Session and composer state use the same status vocabulary. | `8`, `8.3`, `8.4`, `8.5`, or `9` |

Errors and recovery states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.5` | Recovery summary | `4 Create Workspace Dialog` | Dialog and folder picker recovery. | Cancels, reopens, browses again, or selects a valid folder. | The user always returns either to the dialog or to the prior Code state; no partial workspace appears. | Pending folder/name state is either kept in the dialog or discarded on cancel. | `3`, `4`, or `5` |
| `5.3` | Recovery summary | `5 Local Directory Selected` | Invalid, unavailable, duplicate, and failed-create recovery. | Corrects the folder, selects the existing workspace, retries Create, or cancels. | The dialog preserves useful input and gives the user a visible way out. | Workspace creation remains atomic from the user's perspective. | `5`, `5.2.1`, `6`, or `4.1` |
| `7.7` | Recovery summary | `7 Draft Code Session Ready` | Session and tab recovery. | Opens another session, creates a new tab, or returns from no-active-tab state. | Code mode stays visible and offers a session/tab action instead of showing a blank center. | Workspace context is preserved while session/tab state is corrected. | `7`, `7.1`, `7.2`, or `7.3` |
| `8.8` | Recovery summary | `8 First Code Request Sent` | First-run send and runtime recovery. | Adds prompt text, resolves readiness, retries, stops, or continues after failure. | The session shows the blocked, running, stopped, failed, or ready state inline. | The workspace/session remains selected while the run state changes. | `8`, Journey 06, or `9` |

| Route | Composition | Result / next state |
| --- | --- | --- |
| Main create-workspace route | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8 -> 9` | The user creates a workspace, opens a draft Code session, sends the first Code request, and lands in a ready Code workspace. |
| Restored existing workspace | `1 -> 2 -> 2.1 -> 7 -> 8 -> 8.3 -> 9` | The user enters Code mode with an existing workspace and starts from the restored or selected workspace. |
| Select existing workspace instead of creating | `1 -> 2 -> 3 -> 3.2 -> 7 -> 8 -> 8.3 -> 9` | The user bypasses creation by selecting a workspace that is already available. |
| Workspace list expand then select | `1 -> 2 -> 2.2 -> 2.1 -> 7 -> 8 -> 8.3 -> 9` | The user expands workspace navigation, selects a workspace, and continues into a Code session. |
| Create dialog canceled | `1 -> 2 -> 3 -> 3.1 -> 4 -> 4.1 -> 3` | The user exits workspace creation without creating a workspace and returns to the no-workspace state. |
| Folder picker canceled | `1 -> 2 -> 3 -> 3.1 -> 4 -> 4.2 -> 4.2.1 -> 4` | The user cancels the native folder picker and returns to the dialog with Create still unavailable. |
| Invalid or unavailable folder | `1 -> 2 -> 3 -> 3.1 -> 4 -> 4.2 -> 5 -> 5.1 -> 5` | The user sees the folder problem and remains in the dialog to correct the selected directory. |
| Existing workspace selected after duplicate folder | `1 -> 2 -> 3 -> 3.1 -> 4 -> 4.2 -> 5 -> 5.2 -> 5.2.1 -> 7 -> 8 -> 8.3 -> 9` | The app avoids duplicate workspace creation and the user continues with the existing workspace. |
| Create fails then retry succeeds | `1 -> 2 -> 3 -> 3.1 -> 4 -> 4.2 -> 5 -> 6.1 -> 5 -> 6 -> 7 -> 8 -> 8.3 -> 9` | The user recovers from a creation failure and completes workspace creation. |
| New session from workspace control | `1 -> 2 -> 2.1 -> 7 -> 7.1 -> 7 -> 8 -> 8.3 -> 9` | The user creates a new draft Code session inside the active workspace before sending. |
| Existing session opened | `1 -> 2 -> 2.1 -> 7 -> 7.2 -> 9` | The user opens an existing Code session and lands directly in the stable Code workspace. |
| Tab plus without workspace | `1 -> 2 -> 3 -> 7.4 -> 4 -> 4.2 -> 5 -> 6 -> 7 -> 8 -> 8.3 -> 9` | The user tries to create a Code tab before workspace selection, then creates a workspace and continues. |
| No active tab recovery | `1 -> 2 -> 2.1 -> 7 -> 7.5 -> 7.1 -> 7 -> 8 -> 8.3 -> 9` | The user recovers from an empty center area by creating or selecting a Code session. |
| Empty composer | `1 -> 2 -> 2.1 -> 7 -> 8 -> 8.1 -> 8 -> 8.3 -> 9` | The send stays blocked until the user enters prompt text, then the first run starts. |
| Readiness gate before first run | `1 -> 2 -> 2.1 -> 7 -> 8 -> 8.2 -> 8 -> 8.3 -> 9` | Runtime, provider, or model readiness is resolved before the first Code run starts. |
| First run failure then retry | `1 -> 2 -> 2.1 -> 7 -> 8 -> 8.4 -> 8 -> 8.3 -> 9` | The user retries after an early first-run failure and returns to a ready Code workspace. |
| First run stopped | `1 -> 2 -> 2.1 -> 7 -> 8 -> 8.3 -> 8.5 -> 9` | The user stops the first Code task and remains in the selected workspace/session. |

## Main Path

### 1 Enter Code Mode

User entry: the user is in the normal app shell and wants to work on local code, or the app restores Code mode from a previous launch.

User action: the user clicks `Code`, or continues from the restored Code mode.

Visible UI state:

- The sidebar mode selector shows `Code` as active.
- Workspace navigation replaces the ordinary Chat conversation list.
- Settings and account controls remain in the shared app chrome.

Client state change: the app switches to Code mode and begins loading workspace, session, tab, and panel state.

Exit / next state: continue to `2 Code Workspace Shell`.

### 2 Code Workspace Shell

User entry: Code mode is active.

User action: the user reviews the workspace sidebar, waits for restored state, expands workspace groups, or prepares to create/select a workspace.

Visible UI state:

- The left side is organized around `Workspaces`.
- The center can show Code session tabs or a placeholder.
- The right panel shows `All Files`, `Changes`, and `Device`.
- The bottom panel exposes `Terminal` and `Device Log`.
- The composer remains positioned for Code-session prompts.

Client state change: saved Code workspace, selected workspace, open tabs, and panel preferences are loaded into the Code shell.

Exit / next state: if an existing workspace can be selected, continue through `2.1 Existing Workspace Selected`; otherwise continue to `3 No Active Workspace`.

### 3 No Active Workspace

User entry: Code mode is active but no workspace is selected or available.

User action: the user reads the empty state and chooses whether to create or select a workspace.

Visible UI state:

- The workspace area makes `Create Workspace` available.
- The center area may show a no-workspace or create/select prompt.
- File, change, device, terminal, and log areas do not show active workspace content yet.

Client state change: Code mode remains active but local file, command, device, and workspace-backed Code-session actions are not ready.

Exit / next state: clicking `Create Workspace` moves to `4 Create Workspace Dialog`; selecting an available workspace moves through `3.2 Select Existing Workspace Instead`.

### 4 Create Workspace Dialog

User entry: the user chooses `Create Workspace` from the Code workspace area.

User action: the user reviews the dialog, edits the workspace name if needed, and chooses a folder.

Visible UI state:

- A focused `Create Workspace` dialog appears.
- Workspace name and folder fields are visible.
- `Browse`, `Cancel`, and `Create` are visible.
- `Create` is disabled until a folder is selected.

Client state change: workspace creation is staged in the dialog; the active workspace does not change yet.

Exit / next state: folder selection proceeds through `4.2 Folder Picker Opened` and then `5 Local Directory Selected`; cancel proceeds through `4.1 Create Dialog Canceled`.

### 5 Local Directory Selected

User entry: the user has returned from folder selection with a local folder.

User action: the user confirms or edits the workspace name, then clicks `Create`.

Visible UI state:

- The folder field shows the selected path.
- The workspace name is inferred from the folder or remains editable.
- `Create` is available.

Client state change: the pending workspace has a selected directory and can be submitted.

Exit / next state: successful creation moves to `6 Workspace Created And Selected`; folder validation, duplicate, or creation failure routes through `5.1`, `5.2`, or `6.1`.

### 6 Workspace Created And Selected

User entry: the user submits the Create Workspace dialog successfully.

User action: the user watches the dialog close and confirms the new workspace is selected.

Visible UI state:

- The dialog closes.
- The workspace appears in the sidebar and is selected or expanded.
- The working directory area shows the selected folder path.
- The right and bottom panels become workspace-scoped.

Client state change: the new workspace becomes available as the active Code workspace.

Exit / next state: continue to `7 Draft Code Session Ready`.

### 7 Draft Code Session Ready

User entry: a workspace is selected and Code mode has a session/tab target.

User action: the user reviews the active Code session or creates/selects a different Code session.

Visible UI state:

- A Code session tab is active in the center tab strip.
- The composer shows `Assign a task or ask any question`.
- Model and thinking controls are visible near the composer.
- The workspace sidebar still shows the selected workspace and session rows.

Client state change: a workspace-scoped draft or existing Code session is active.

Exit / next state: typing and sending a prompt moves to `8 First Code Request Sent`.

### 8 First Code Request Sent

User entry: the user is in a selected workspace with an active Code session.

User action: the user types a code-related prompt and sends it.

Visible UI state:

- The prompt appears in the Code session.
- The composer moves from editable/send-ready into running constraints.
- The session shows starting, running, thinking, command, tool, or similar progress content.

Client state change: the draft session becomes an active Code run tied to the selected workspace.

Exit / next state: successful start proceeds through `8.3 First Run Starts` and then `9 Open Code Workspace Ready`; blocked, failed, empty, or stopped states route through the matching first-run branch.

### 9 Open Code Workspace Ready

User entry: the first Code request has started, completed, stopped, failed in a recoverable way, or the user has opened an existing Code session.

User action: the user continues prompting, opens files, reviews changes, uses terminal/log panels, switches sessions, or pauses.

Visible UI state:

- Code mode remains active.
- The selected workspace and session are clear.
- The working directory, right panel, bottom panel, and composer remain available.

Client state change: Code mode rests in a stable selected-workspace/session state.

Exit / next state: end of Journey 05. Runtime/model/permission details continue in Journey 06; file, diff, terminal, and device panel inspection continues in Journey 07.

## Branch Journeys

### 2.1 Existing Workspace Selected

Trigger: Code mode loads with an existing workspace available, or the user selects one from the workspace list.

Visible UI state:

- The selected workspace row is highlighted.
- Session rows for that workspace are visible when the group is expanded.
- The working directory area shows the selected folder path.

Allowed user actions: open an existing session, create a new Code session, expand/collapse workspace groups, open panels, or type in the composer when a session is active.

Client state change: Code mode adopts the selected workspace as the active local directory.

Recovery / next state: continue to `7 Draft Code Session Ready`.

Blocks progress: no.

### 2.2 Workspace List Expanded Or Filtered

Trigger: the user interacts with workspace navigation before selecting a workspace.

Visible UI state:

- Workspace groups can expand or collapse.
- Session counts and session rows remain tied to their workspace group.
- Search or navigation changes what the user can quickly select.

Allowed user actions: expand, collapse, search, select a workspace, select a session, or create a workspace.

Client state change: the workspace list presentation changes; active workspace changes only after a workspace/session is selected.

Recovery / next state: selecting a workspace moves to `2.1 Existing Workspace Selected`; if nothing usable exists, the user continues to `3 No Active Workspace`.

Blocks progress: no.

### 3.1 Empty Workspace State

Trigger: Code mode has no selected workspace and the user needs to create one.

Visible UI state:

- The Code surface explains that a workspace is needed before local code work can start.
- `Create Workspace` is the primary visible action.
- The composer may remain visible but cannot provide full local workspace context.

Allowed user actions: create a workspace, select an existing workspace if one is visible, or leave Code mode.

Client state change: no workspace context is selected yet.

Recovery / next state: clicking `Create Workspace` moves to `4 Create Workspace Dialog`.

Blocks progress: yes for workspace-backed code work.

### 3.2 Select Existing Workspace Instead

Trigger: the user chooses an already available workspace instead of creating a new one.

Visible UI state:

- The selected workspace row becomes active.
- Its sessions and path become the visible Code context.

Allowed user actions: open an existing session, create a new Code session, or send a prompt in the active draft session.

Client state change: Code mode switches from no active workspace to the selected existing workspace.

Recovery / next state: continue to `7 Draft Code Session Ready`.

Blocks progress: no.

### 4.1 Create Dialog Canceled

Trigger: the user cancels or closes the Create Workspace dialog before submitting.

Visible UI state:

- The dialog closes.
- The previous Code shell is visible again.
- If no workspace existed before, the no-workspace state remains.

Allowed user actions: reopen `Create Workspace`, select an existing workspace, or leave Code mode.

Client state change: no workspace is created and any pending dialog values are discarded.

Recovery / next state: return to `3 No Active Workspace` or `2 Code Workspace Shell`.

Blocks progress: yes if no workspace remains selected.

### 4.2 Folder Picker Opened

Trigger: the user clicks `Browse` or the folder field action in the Create Workspace dialog.

Visible UI state:

- The product waits for the folder picker.
- The Create Workspace dialog remains the expected return point.

Allowed user actions: choose a folder or cancel the picker.

Client state change: no workspace is created while the picker is open.

Recovery / next state: choosing a folder moves to `5 Local Directory Selected`; canceling moves to `4.2.1 Folder Picker Canceled`.

Blocks progress: temporarily.

### 4.2.1 Folder Picker Canceled

Trigger: the user cancels the native folder picker.

Visible UI state:

- The Create Workspace dialog is visible again.
- No folder is filled in.
- `Create` remains disabled.

Allowed user actions: browse again, manually correct dialog input where available, or cancel the dialog.

Client state change: no folder path is stored for the pending workspace.

Recovery / next state: return to `4 Create Workspace Dialog`.

Blocks progress: yes until a folder is selected.

### 5.1 Invalid Or Unavailable Directory

Trigger: the selected folder cannot be used for workspace creation.

Visible UI state:

- The Create Workspace dialog remains open.
- The selected folder and workspace name stay visible.
- A folder-related error tells the user the selection needs correction.

Allowed user actions: choose another folder, edit the workspace name if relevant, retry Create, or cancel.

Client state change: workspace creation is rejected and no new workspace is added.

Recovery / next state: choose a valid folder and return to `5 Local Directory Selected`, or cancel through `4.1 Create Dialog Canceled`.

Blocks progress: yes for this workspace creation attempt.

### 5.2 Workspace Already Exists

Trigger: the selected folder already maps to a workspace in the app.

Visible UI state:

- The user sees duplicate-workspace feedback or can identify the existing workspace in the sidebar.
- The app does not present a second identical workspace as successfully created.

Allowed user actions: select the existing workspace, choose another folder, or cancel.

Client state change: duplicate workspace creation is avoided.

Recovery / next state: selecting the existing workspace moves to `5.2.1 Existing Workspace Selected After Duplicate`; choosing a different folder returns to `5 Local Directory Selected`.

Blocks progress: yes until the user selects existing workspace or changes folder.

### 5.2.1 Existing Workspace Selected After Duplicate

Trigger: the user recovers from duplicate-folder feedback by selecting the existing workspace.

Visible UI state:

- The existing workspace is highlighted.
- Its sessions and path become visible in Code mode.

Allowed user actions: open or create a Code session and continue work.

Client state change: Code mode switches to the existing workspace instead of creating a duplicate.

Recovery / next state: continue to `7 Draft Code Session Ready`.

Blocks progress: no.

### 6.1 Workspace Creation Failed

Trigger: the user clicks `Create`, but the workspace cannot be created.

Visible UI state:

- The dialog remains open.
- The chosen folder and workspace name remain visible.
- A failure message lets the user retry, correct, or cancel.

Allowed user actions: retry, choose a different folder, edit the name, or cancel.

Client state change: no new workspace is added and the previous Code state is preserved.

Recovery / next state: retry returns to `5 Local Directory Selected`; cancel returns through `4.1 Create Dialog Canceled`.

Blocks progress: yes for the attempted new workspace.

### 7.1 New Session From Workspace Control

Trigger: the user clicks a new-session control inside the active workspace group.

Visible UI state:

- A new Code session tab appears or becomes active.
- The workspace remains selected in the sidebar.
- The composer is ready for a first prompt.

Allowed user actions: type a prompt, open another session, close tabs, or inspect panels.

Client state change: a new draft Code session is associated with the selected workspace.

Recovery / next state: return to `7 Draft Code Session Ready`.

Blocks progress: no.

### 7.2 Existing Session Opened

Trigger: the user clicks an existing Code session row under a workspace.

Visible UI state:

- The selected session opens in the center area.
- Previous messages or run output appear when available.
- The session tab becomes active.

Allowed user actions: continue the session, send a new prompt, stop/retry if a run state allows it, or switch tabs.

Client state change: the existing session becomes the active Code session.

Recovery / next state: if the session is usable, continue to `9 Open Code Workspace Ready`; sending a new prompt enters `8 First Code Request Sent`.

Blocks progress: no.

### 7.3 Tab Plus Creates Code Session

Trigger: the user clicks the plus control in the center tab strip while a workspace is active.

Visible UI state:

- A new Code chat/session tab appears in the tab strip.
- The new tab is active and ready for prompt input.

Allowed user actions: type the first prompt, switch tabs, close the tab, or create another session.

Client state change: a workspace-scoped draft Code session is created or reused and activated.

Recovery / next state: return to `7 Draft Code Session Ready`.

Blocks progress: no.

### 7.4 Tab Plus Without Workspace

Trigger: the user asks for a new Code tab while no workspace is selected.

Visible UI state:

- Code mode does not open an unscoped coding session.
- The workspace creation dialog or no-workspace prompt becomes the next visible step.

Allowed user actions: create a workspace, select an existing workspace, or cancel.

Client state change: session creation waits for workspace context.

Recovery / next state: create/select workspace through `4 Create Workspace Dialog` or `3 No Active Workspace`.

Blocks progress: yes for new Code session creation.

### 7.5 No Active Tab Placeholder

Trigger: the active tab is closed or no valid tab can be restored.

Visible UI state:

- The center area shows a no-active-tab or create/select prompt instead of blank content.
- Sidebar and workspace context remain visible.

Allowed user actions: create a new Code session, select an existing session, or choose a different workspace.

Client state change: active workspace can remain selected even though no center tab is active.

Recovery / next state: create a session through `7.1` or `7.3`, or open an existing session through `7.2`.

Blocks progress: yes for composer-backed work until a session is active.

### 8.1 Empty Composer

Trigger: the user has an active Code session but no prompt text is entered.

Visible UI state:

- The composer placeholder remains visible.
- The send control is unavailable or inert.
- Model and thinking controls remain visible.

Allowed user actions: type a prompt, change model/thinking, add context, or switch sessions.

Client state change: no run starts.

Recovery / next state: typing prompt text returns to `8 First Code Request Sent`.

Blocks progress: yes for sending.

### 8.2 Runtime Or Provider Gate

Trigger: the user attempts the first send while runtime, provider, model, or permission readiness blocks the run.

Visible UI state:

- The session shows readiness or recovery language instead of normal running output.
- The user can follow the model/provider/runtime recovery path.

Allowed user actions: resolve the readiness gate, retry, change model/runtime, open Settings when offered, or return to the session.

Client state change: no successful first Code run begins until readiness is resolved.

Recovery / next state: continue through Journey 06, then return to `8 First Code Request Sent`.

Blocks progress: yes for the first run.

### 8.3 First Run Starts

Trigger: the user sends a valid prompt in a selected workspace and readiness gates pass.

Visible UI state:

- The prompt appears in the active Code session.
- The assistant side shows starting, running, thinking, tool, command, or progress output.
- The composer reflects active-run constraints.

Allowed user actions: wait, stop if available, inspect panels, or continue after completion.

Client state change: the session becomes an active Code run tied to the selected workspace.

Recovery / next state: normal progress leads to `9 Open Code Workspace Ready`; stop moves through `8.5 First Run Stopped`; early failure moves through `8.4 First Run Failed`.

Blocks progress: temporarily while the run is active.

### 8.4 First Run Failed

Trigger: the first Code run fails before useful output appears or cannot proceed.

Visible UI state:

- The session shows an inline error or failed state.
- The workspace, session, panels, and composer remain visible.
- Retry or manual recovery is available when the UI offers it.

Allowed user actions: retry, edit the prompt, choose another model/runtime, create a new session, or continue inspecting the workspace.

Client state change: the failed turn remains visible without clearing the workspace context.

Recovery / next state: retry returns to `8 First Code Request Sent`; creating a new session moves to `7.1 New Session From Workspace Control`; manual continuation can rest at `9 Open Code Workspace Ready`.

Blocks progress: no after the failure settles, because the user can recover or continue manually.

### 8.5 First Run Stopped

Trigger: the user stops the active first Code task.

Visible UI state:

- The current turn is marked stopped or no longer running.
- The composer becomes usable again.
- The selected workspace/session remains visible.

Allowed user actions: send a follow-up, retry with a different prompt, inspect panels, or leave the session.

Client state change: the active run is ended in a stopped state.

Recovery / next state: continue to `9 Open Code Workspace Ready`.

Blocks progress: no after stop completes.

## Detail States

### 2.3 Code Layout Regions

Trigger: the Code workspace shell is visible.

Visible UI state:

- Left navigation: `Workspaces`, search, workspace groups, session rows, settings/account controls.
- Center: working directory area, tab strip, Code session or placeholder, and composer.
- Right panel: `All Files`, `Changes`, and `Device`.
- Bottom panel: `Terminal` and `Device Log`.

Allowed user actions: select workspace/session, create workspace/session, switch tabs, open panels, type into composer, or leave Code mode.

Client state change: selected workspace, active tab, right panel, bottom panel, and composer state remain synchronized around the active Code context.

Recovery / next state: return to the active Code journey state.

Blocks progress: no.

### 4.3 Dialog Controls And Keyboard

Trigger: `Create Workspace` is open.

Visible UI state:

- Workspace name and folder/path controls are visible.
- `Browse`, `Cancel`, and `Create` are visible.
- `Create` is disabled until a folder is selected.
- Enter can submit only when the dialog is valid; Escape or close returns to the previous Code state.

Allowed user actions: browse for a folder, edit the name, create, cancel, or close.

Client state change: valid details can submit workspace creation; cancellation discards pending details.

Recovery / next state: submit moves to `6 Workspace Created And Selected` or `6.1 Workspace Creation Failed`; cancel moves to `4.1 Create Dialog Canceled`.

Blocks progress: temporarily.

### 7.6 Tab Strip Behavior

Trigger: the user has one or more tabs in Code mode.

Visible UI state:

- Session, file, diff, and authoring tabs can share the center tab strip.
- The active tab is visually marked.
- Closing a tab selects another available tab or shows the no-active-tab placeholder.

Allowed user actions: select, close, or create tabs.

Client state change: active tab changes without changing the active workspace unless the selected tab belongs to another workspace/session context.

Recovery / next state: tab actions return to `7 Draft Code Session Ready`, `7.2 Existing Session Opened`, `7.5 No Active Tab Placeholder`, or `9 Open Code Workspace Ready`.

Blocks progress: no unless no active session tab remains.

### 8.6 Composer Controls

Trigger: a Code session is active.

Visible UI state:

- The composer shows prompt placeholder or typed text.
- The add/context control, model pill, thinking control, and send/stop affordance remain visible.
- During a run, the composer reflects that the session is busy.

Allowed user actions: type, add context, change model/thinking, send, stop, or wait.

Client state change: composer readiness follows prompt text and active-run state.

Recovery / next state: empty text moves to `8.1 Empty Composer`; successful send moves through `8.3 First Run Starts`; stop moves through `8.5 First Run Stopped`.

Blocks progress: only when empty or running.

## State Language

### 0.1 Shared Code Workspace Labels

Surface: Code workspace shell.

Copy / control language:

- `Chat`
- `Code`
- `Workspaces`
- `Create Workspace`
- `Search conversations...`
- `All Files`
- `Changes`
- `Device`
- `Terminal`
- `Device Log`
- `Assign a task or ask any question`

Meaning: these labels orient the user around Code mode, workspace selection, panel inspection, and the composer.

Client state change: label state follows the active Code mode, workspace, panel, and session state.

Exit / next state: any Code workspace state.

### 4.4 Workspace Creation Labels

Surface: Create Workspace dialog and folder selection return state.

Copy / control language:

- `Create Workspace`
- `Browse`
- `Cancel`
- `Create`
- Workspace name
- Folder or path field
- Disabled Create state until a folder is selected

Meaning: these labels describe whether the user is still choosing a folder or ready to submit workspace creation.

Client state change: Create becomes available only after a folder is selected and remains unavailable after picker cancellation.

Exit / next state: `4 Create Workspace Dialog`, `5 Local Directory Selected`, or a folder/create recovery branch.

### 8.7 First Run Status Labels

Surface: active Code session and composer.

Copy / control language:

- Draft
- Ready
- Starting
- Running
- Completed
- Stopped
- Failed
- Retry
- Send

Meaning: these labels describe whether the first Code request can be sent, is running, has ended, or needs recovery.

Client state change: the session and composer reflect the same run state.

Exit / next state: `8 First Code Request Sent`, first-run branch, or `9 Open Code Workspace Ready`.

## Errors And Recovery

### 4.5 Dialog And Folder Picker Recovery

Trigger: the user closes the dialog, cancels the picker, or returns without a selected folder.

Visible UI state:

- Canceling the dialog returns to the prior Code shell.
- Canceling the picker returns to the dialog.
- `Create` remains disabled when no folder is selected.

Allowed user actions: reopen create, browse again, select an existing workspace, or leave Code mode.

Client state change: no partial workspace is created.

Recovery / next state: return to `3 No Active Workspace`, `4 Create Workspace Dialog`, or `5 Local Directory Selected`.

Blocks progress: yes until a workspace is selected or created.

### 5.3 Directory And Create Recovery

Trigger: the chosen directory is invalid, unavailable, duplicate, or creation fails.

Visible UI state:

- The dialog keeps the useful selected information visible.
- The user gets a clear way to choose another folder, select the existing workspace, retry, or cancel.

Allowed user actions: correct folder, select existing workspace, retry Create, or cancel.

Client state change: the workspace list changes only after successful creation or existing workspace selection.

Recovery / next state: `5 Local Directory Selected`, `5.2.1 Existing Workspace Selected After Duplicate`, `6 Workspace Created And Selected`, or `4.1 Create Dialog Canceled`.

Blocks progress: yes for the failed create attempt.

### 7.7 Session And Tab Recovery

Trigger: session creation is requested without a workspace, a tab is closed, or no active tab can be restored.

Visible UI state:

- Code mode stays visible.
- The user sees a workspace creation/select prompt or no-active-tab placeholder rather than a blank center.

Allowed user actions: create/select workspace, create a new session, select an existing session, or switch tabs.

Client state change: workspace context is preserved while session/tab state is corrected.

Recovery / next state: `7 Draft Code Session Ready`, `7.1 New Session From Workspace Control`, `7.2 Existing Session Opened`, or `7.3 Tab Plus Creates Code Session`.

Blocks progress: yes only until an active workspace/session exists.

### 8.8 First Run Recovery

Trigger: the first send is empty, readiness-gated, failed, stopped, or otherwise not completed normally.

Visible UI state:

- The session shows the blocked, running, stopped, failed, or ready state inline.
- The workspace/sidebar/panels stay visible.
- The composer eventually returns to a usable state unless a readiness gate must be resolved first.

Allowed user actions: type a prompt, resolve readiness through Journey 06, retry, stop, change model/thinking, create a new session, or continue manually.

Client state change: run state changes without losing the selected workspace/session.

Recovery / next state: `8 First Code Request Sent`, Journey 06, or `9 Open Code Workspace Ready`.

Blocks progress: only while readiness is unresolved or a run is actively executing.

## Wireframe

The matching wireframe should use this record as the route source of truth. The main route should show Code mode entry, workspace creation, active session setup, first send, and the stable Code workspace. Additional route rows should cover restored workspace selection, create/cancel, folder picker cancellation, invalid folder, duplicate workspace recovery, session/tab creation, empty composer, readiness gate, first-run failure, and stopped-run recovery. Each wireframe card should use the IDs and headings from this record.
