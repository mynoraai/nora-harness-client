# Journey 07: Code Workbench Panels

This Markdown file is the text counterpart to `code-workbench-panels.wireframe.html`. It documents the current implemented journey for inspecting Code-mode workbench panels. Each section ID matches a state that can be represented in the wireframe.

## Journey Scope

User entry: the user is already inside a Code session with a workspace selected and inspects the right workbench panel, center file/diff tabs, and bottom panels.

User goal: the user switches among `All Files`, `Changes`, `Device`, `Terminal`, and `Device Log` to inspect files, changed files, command output, device readiness, and device logs without leaving the active Code session.

End state: the user rests in a selected workbench view. File, diff, terminal, device, or log state remains aligned with the current Code session.

Implementation maturity: implemented.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches, detail states, state-language notes, and recovery states use decimal IDs scoped to the step where the state starts. Sequential follow-up states use another decimal level.

Main inspection path:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 07 | Code workbench entry. | Opens or returns to a Code session. | The Code frame shows the workspace sidebar, working directory bar, center tabs, right panel, and bottom panel. | The active Code session and workspace remain selected while workbench panel state becomes available. | `2` |
| `2` | Main path | Journey 07 | Stable Code session context. | Confirms the current session and workspace before inspecting panels. | The center tab remains a Code conversation, file, diff, or placeholder while panel controls stay visible around it. | The client keeps the active session, active center tab, and selected workspace in sync. | `3`, `4`, `5`, or `6` |
| `3` | Main path | Journey 07 | Right panel inspection. | Uses top tabs and row clicks in the right panel. | The top tabs are `All Files`, `Changes`, and `Device`; selecting a top tab changes only the right panel body. | The selected right-panel tab changes while the active Code session remains unchanged. | `7` or a right-panel branch |
| `4` | Main path | Journey 07 | Center file or diff inspection. | Opens a file or changed-file diff from the right panel, then switches or closes center tabs. | A read-only file tab, diff tab, Code conversation tab, or `No active tab` placeholder is shown in the center. | The active center tab changes without changing the selected workspace. | `7` or a center-tab branch |
| `5` | Main path | Journey 07 | Bottom panel inspection. | Switches between `Terminal` and `Device Log`. | The bottom strip shows `Terminal`, `Device Log`, and a collapse or expand control. | The selected bottom tab and expanded/collapsed body stay with the workbench layout. | `7` or a bottom-panel branch |
| `6` | Main path | Journey 07 | Layout adjustment. | Collapses, expands, or resizes workbench panels. | Sidebar, right panel, and bottom panel change available space while the center session remains visible. | Layout state changes without losing file, diff, terminal, or log context. | `7` or a layout branch |
| `7` | Main path | Journey 07 | Stable selected workbench view. | Continues inspecting, switches another panel, returns to conversation, or leaves Code mode. | The last selected right-panel, center-tab, bottom-panel, and layout states remain visible. | The workbench rests with session context and panel selections preserved. | End |

Right panel tabs, files, changes, diff, preview, and device:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Branch journey | `3 Right panel inspection` | `All Files` selected. | Clicks `All Files`, expands folders, refreshes, retries, or selects a file. | The workspace tree shows loading, folder rows, file rows, empty state, no-workspace state, or retryable error state. | Workspace tree data loads for the active workspace and folder expansion state updates as the user navigates. | `3.1.1`, `3.1.2`, or `3.1.3` |
| `3.1.1` | Branch follow-up | `3.1 All Files selected` | Folder expansion and lazy loading. | Expands or collapses a folder row. | The folder row opens to show child folders and files, or shows loading while children are fetched. | The tree adds child rows under the selected folder and preserves the current right-panel tab. | `3.1` |
| `3.1.2` | Branch follow-up | `3.1 All Files selected` | File row opens a center file tab. | Clicks a file row. | A center tab opens or activates for the selected file while the right panel keeps the tree visible. | The center active tab becomes a read-only file preview for the selected file. | `4.1` |
| `3.1.3` | Branch follow-up | `3.1 All Files selected` | File tree empty or unavailable. | Reads the empty, no-workspace, or error state; uses `Retry` or selects a workspace when available. | The tree area explains why files are not listed and shows recovery when retry is possible. | The panel avoids showing stale file rows and can reload once workspace or file access is available. | `3.1` or `7` |
| `3.2` | Branch journey | `3 Right panel inspection` | `Changes` selected. | Clicks `Changes`, refreshes, retries, or selects a changed file. | The changes list shows loading, changed-file rows with status letters, no changes, no-workspace, no-git, or retryable error state. | Change data loads for the active workspace and the selected changed file can open a diff tab. | `3.2.1`, `3.2.2`, or `3.2.3` |
| `3.2.1` | Branch follow-up | `3.2 Changes selected` | Changed file opens a center diff tab. | Clicks a changed-file row. | A center diff tab opens or activates while the changes list remains visible in the right panel. | The center active tab becomes a read-only diff for the selected changed file. | `4.2` |
| `3.2.2` | Branch follow-up | `3.2 Changes selected` | No changes or no git repository. | Reads the no-changes or no-git state. | The right panel states that there are no changes to show, or that the workspace is not a git repository. | The changes view does not open a diff and remains ready to refresh. | `3.2` or `7` |
| `3.2.3` | Branch follow-up | `3.2 Changes selected` | Changes list error. | Clicks `Retry` or `Refresh`. | The right panel shows a retryable error instead of a change list. | The changes view reloads and then returns to loading, list, no-git, no-changes, or error state. | `3.2` |
| `3.3` | Branch journey | `3 Right panel inspection` | `Device` selected. | Clicks `Device`, installs PlatformIO if offered, refreshes, or retries detection. | The right panel shows PlatformIO readiness, installation or polling state, device status, diagnostics, and stream status. | Device readiness, setup, and live status are refreshed for the active workspace. | `3.3.1`, `3.3.2`, `3.3.3`, or `3.3.4` |
| `3.3.1` | Branch follow-up | `3.3 Device selected` | PlatformIO missing. | Clicks `Install PlatformIO` when the install action is shown. | The Device tab explains that PlatformIO is missing and presents install or setup guidance. | Installation starts for the selected workspace and duplicate install actions are blocked while progress is active. | `3.3.2` |
| `3.3.2` | Branch follow-up | `3.3.1 PlatformIO missing` | PlatformIO installing or polling. | Waits while setup continues. | The Device tab shows setup progress or polling feedback. | Setup state is refreshed until PlatformIO becomes available or fails. | `3.3.3` or `3.3.4` |
| `3.3.3` | Branch follow-up | `3.3 Device selected` | Device ready or streaming. | Reviews status, diagnostics, and stream badge. | Device status, diagnostics, and live stream indicators are visible and current. | Device status stream updates are merged into the visible Device tab. | `7` |
| `3.3.4` | Branch follow-up | `3.3 Device selected` | Device unavailable, paused, or errored. | Clicks `Retry`, refreshes, or switches away. | The Device tab keeps the last known state where possible and shows retry for setup or detection failure. | Device probing restarts on retry, or streaming stops cleanly when the user leaves the tab. | `3.3` or `7` |
| `3.4` | Boundary state | `3 Right panel inspection` | Preview is not part of this right-panel tab set. | Looks for a preview tab in this workbench panel. | No `Preview` top tab appears beside `All Files`, `Changes`, and `Device` in this journey. | The workbench does not change state; preview work belongs to a separate journey when available. | `3` or `7` |

Center files, changes, diff, and empty-tab states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.1` | Branch journey | `4 Center file or diff inspection` | File tab loading or loaded. | Waits for file content, scrolls, or switches tabs. | The center tab shows loading, a path header, optional `Line N`, read-only code, and line numbers when content loads. | File content resolves into a read-only preview tied to the current workspace. | `4.1.1`, `4.1.2`, or `7` |
| `4.1.1` | Branch follow-up | `4.1 File tab loading or loaded` | File cannot be previewed as text. | Reads the binary or too-large explanation. | The file tab shows a non-editable binary or too-large card instead of a code preview. | The file tab remains open but does not render text content. | `4` or `7` |
| `4.1.2` | Branch follow-up | `4.1 File tab loading or loaded` | File read error. | Retries by reopening or switches to another tab. | The file tab shows an unable-to-open state. | The failed file preview remains isolated to that tab; other workbench panels stay usable. | `3.1` or `7` |
| `4.2` | Branch journey | `4 Center file or diff inspection` | Diff tab loading or loaded. | Waits for diff content, scrolls hunks, or switches tabs. | The center tab shows loading, a file path header, added and removed counts, hunk rows, and old/new line numbers when content loads. | Diff content resolves into a read-only comparison tied to the selected changed file. | `4.2.1`, `4.2.2`, or `7` |
| `4.2.1` | Branch follow-up | `4.2 Diff tab loading or loaded` | No diff output for selected file. | Reads the empty diff state. | The diff tab explains that there is no diff output to show. | The tab remains open and the changes list can still be refreshed or another file selected. | `3.2` or `7` |
| `4.2.2` | Branch follow-up | `4.2 Diff tab loading or loaded` | Diff read error. | Retries by reopening the changed file or switches to another tab. | The diff tab shows an unable-to-open state. | The failed diff preview remains isolated to that tab; other workbench panels stay usable. | `3.2` or `7` |
| `4.3` | Branch journey | `4 Center file or diff inspection` | No active center tab. | Closes tabs or starts from an incomplete active tab state. | The center area shows `No active tab` and `Create a new tab to get started.` | The tab area has no valid active file, diff, or Code conversation to render. | `3.1`, `3.2`, or `7` |

Bottom terminal and device log:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `5.1` | Branch journey | `5 Bottom panel inspection` | `Terminal` selected. | Clicks `Terminal`, waits for history, types commands when available, or reviews output. | The bottom panel shows the workspace shell surface with history and live terminal output. | The terminal is opened or reused for the active workspace and maintains live data, exit, and resize state. | `5.1.1`, `6.2`, or `7` |
| `5.1.1` | Branch follow-up | `5.1 Terminal selected` | Terminal output streaming or preserved. | Watches running output, scrolls history, or leaves the panel. | Terminal output streams while active and remains visible after completion or exit. | Terminal history and current output remain associated with the active workspace. | `5.1` or `7` |
| `5.2` | Branch journey | `5 Bottom panel inspection` | `Device Log` selected. | Clicks `Device Log`, reviews rows, refreshes, pauses, resumes, or changes filters. | The bottom panel shows device log rows, severity filters, USB monitor controls, pause/resume, refresh, and autoscroll behavior. | Recent logs load for the active workspace and visible rows update based on filters and pause state. | `5.2.1`, `5.2.2`, `5.2.3`, or `7` |
| `5.2.1` | Branch follow-up | `5.2 Device Log selected` | Log monitor enabled or disabled. | Toggles the USB/device log monitor when the control is available. | The monitor control reflects whether live device log collection is enabled. | The log monitor starts or stops while existing log rows remain available for inspection. | `5.2` |
| `5.2.2` | Branch follow-up | `5.2 Device Log selected` | Log load error or empty log list. | Clicks refresh, adjusts filters, or waits for new logs. | The log area shows an empty state or retryable load state instead of log rows. | The panel avoids mixing stale rows with failed or empty log state and can reload. | `5.2` or `7` |
| `5.2.3` | Branch follow-up | `5.2 Device Log selected` | Log row file reference. | Opens a referenced file when a log row points to workspace content. | The log row remains visible and the center area can open the referenced file. | The active center tab becomes a file preview while the Device Log panel remains selected. | `4.1` |

Panel inspection and layout details:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.6` | Detail state | `3 Right panel inspection` | Right-panel tab switching and row action details. | Switches top tabs, expands rows, opens files or diffs, refreshes, retries, or leaves a tab selected. | The right panel preserves the selected tab while its body changes between tree, changes, device, empty, loading, and recovery states. | Right-panel tab and row selection update without replacing the active Code session. | `3.1`, `3.2`, `3.3`, `4`, or `7` |
| `6.5` | Detail state | `6 Layout adjustment` | Resize and collapse control details. | Collapses, expands, or resizes right and bottom regions while content is visible. | Right panel, bottom panel, and center area change available space while selected tabs and loaded content remain visible or restorable. | Layout detail state updates without clearing file, diff, terminal, log, or device context. | `3`, `5`, `6`, or `7` |

Filters, pause, and autoscroll:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `5.3` | Detail state | `5.2 Device Log selected` | Severity and text filtering. | Turns severity filters on or off, or uses search/filter controls when visible. | The log list narrows or restores visible rows according to the current filters. | Log data remains loaded while visible rows are narrowed locally for inspection. | `5.2` |
| `5.4` | Detail state | `5.2 Device Log selected` | Pause and resume. | Clicks pause or resume while logs are streaming. | Paused state prevents the visible list from jumping; resume returns the panel to live updates. | Incoming logs are held or displayed according to the pause state without changing the selected Code session. | `5.2` |
| `5.5` | Detail state | `5.2 Device Log selected` | Autoscroll state. | Leaves autoscroll on, scrolls away from the bottom, or returns to the latest logs. | The log viewport either follows new entries or preserves the user's manual scroll position. | The panel tracks whether new rows should move the viewport or stay offscreen until the user returns. | `5.2` |

State language:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Workbench tab labels and panel names. | Reads top tabs and bottom tabs while choosing a workbench panel. | The journey uses `All Files`, `Changes`, `Device`, `Terminal`, and `Device Log` as stable panel labels. | Label language clarifies available panel targets without changing selected tabs or workspace context. | Any Journey 07 state |
| `0.2` | State language | Whole journey | Empty, loading, and unavailable copy. | Reads placeholder, loading, empty, no-workspace, no-git, binary, too-large, or unable-to-open copy. | Empty and unavailable states describe what is missing and where the user can recover. | Status copy explains current availability without clearing selected panel state. | Any Journey 07 state |
| `0.3` | State language | Whole journey | Device Log controls and live-update copy. | Reads log action labels while filtering, pausing, resuming, or following latest logs. | Device Log controls use `Pause`, `Resume`, `Autoscroll`, severity labels, monitor state, and filtered-empty copy. | Log control copy explains whether rows are live, paused, filtered, or following latest output. | `5.2`, `5.3`, `5.4`, or `5.5` |
| `0.4` | State language | Whole journey | Recovery action language. | Reads retry, refresh, setup, and restore actions while recovering a panel. | Recovery controls use `Retry`, `Refresh`, `Install PlatformIO`, expand, collapse, and restore-oriented labels. | Recovery language keeps the affected panel local to the current Code session. | Any recovery state |

Resize and collapse:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `6.1` | Branch journey | `6 Layout adjustment` | Right panel collapsed or expanded. | Clicks the right-panel toggle. | The right panel hides or returns with the previous selected top tab and bottom tab. | Right-panel visibility changes while the active center tab and Code session remain selected. | `3`, `4`, or `7` |
| `6.2` | Branch journey | `6 Layout adjustment` | Bottom panel collapsed or expanded. | Clicks the bottom panel collapse or expand control. | The bottom body hides or returns; the bottom tab labels remain visible when collapsed. | Bottom-panel visibility changes while the selected bottom tab is preserved. | `5` or `7` |
| `6.3` | Branch journey | `6 Layout adjustment` | Panel resized. | Drags a panel boundary where resizing is available. | The center work area, right panel, and bottom panel adjust their visible space. | Layout sizing changes without resetting selected tabs, file preview, diff preview, terminal history, or logs. | `7` |

Recovery and error:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.5` | Recovery summary | `3 Right panel inspection` | Right-panel load and workspace recovery. | Selects a workspace, clicks `Retry`, clicks `Refresh`, or switches tabs. | File, changes, and device panels show loading, empty, no-workspace, or retryable error states without replacing the Code session. | A failing panel is isolated to that panel and can reload without clearing the active center tab. | `3.1`, `3.2`, `3.3`, or `7` |
| `4.4` | Recovery summary | `4 Center file or diff inspection` | File, diff, and empty-tab recovery. | Reopens the item, chooses another file/change, creates a new tab, or returns to conversation. | File and diff errors stay inside their tabs; the placeholder gives the user a way back to tab creation or panel selection. | Center-tab failure does not reset right-panel or bottom-panel selection. | `3.1`, `3.2`, `4`, or `7` |
| `5.6` | Recovery summary | `5 Bottom panel inspection` | Terminal and log recovery. | Switches bottom tabs, refreshes logs, resumes logs, collapses the panel, or continues in the center. | Terminal and Device Log problems stay in the bottom panel while the Code session remains usable. | Bottom-panel state can recover independently from center file/diff inspection. | `5.1`, `5.2`, `6.2`, or `7` |
| `6.4` | Recovery summary | `6 Layout adjustment` | Layout recovery. | Reopens the right panel, expands the bottom panel, or returns to the active center tab. | Collapsed or resized panels can return without losing the selected workbench state. | The saved layout returns or updates while session context remains stable. | `3`, `5`, or `7` |

Route table:

| Route | Composition | Result / next state |
| --- | --- | --- |
| Main inspection path | `1 -> 2 -> 3 -> 7` | The user enters Code mode workbench inspection, checks the right panel, and rests on the selected workbench view. |
| Open file from All Files | `1 -> 2 -> 3 -> 3.1 -> 3.1.1 -> 3.1.2 -> 4 -> 4.1 -> 7` | The user expands the file tree, opens a file, reviews the read-only file tab, and keeps the Code session active. |
| All Files unavailable and recovered | `1 -> 2 -> 3 -> 3.1 -> 3.1.3 -> 3.1 -> 7` | The file tree shows no-workspace, empty, or error state; the user recovers or leaves the panel stable. |
| Open diff from Changes | `1 -> 2 -> 3 -> 3.2 -> 3.2.1 -> 4 -> 4.2 -> 7` | The user opens a changed file diff and reviews hunk rows in the center. |
| Changes no-git or no-changes | `1 -> 2 -> 3 -> 3.2 -> 3.2.2 -> 7` | The user sees that no diff can be opened from the current changes state and remains in a stable workbench view. |
| Changes retry | `1 -> 2 -> 3 -> 3.2 -> 3.2.3 -> 3.2 -> 7` | The user retries or refreshes the change list and returns to the changes panel. |
| File preview cannot render | `1 -> 2 -> 3 -> 3.1 -> 3.1.2 -> 4 -> 4.1 -> 4.1.1 -> 7` | The file tab stays open with binary or too-large explanation instead of editable content. |
| File read error | `1 -> 2 -> 3 -> 3.1 -> 3.1.2 -> 4 -> 4.1 -> 4.1.2 -> 3.1 -> 7` | The user returns to the file tree after a file preview fails. |
| Diff no output | `1 -> 2 -> 3 -> 3.2 -> 3.2.1 -> 4 -> 4.2 -> 4.2.1 -> 3.2 -> 7` | The diff tab shows no output and the user returns to the changes list or remains stable. |
| Diff read error | `1 -> 2 -> 3 -> 3.2 -> 3.2.1 -> 4 -> 4.2 -> 4.2.2 -> 3.2 -> 7` | The user returns to the changes list after a diff preview fails. |
| Device ready | `1 -> 2 -> 3 -> 3.3 -> 3.3.3 -> 7` | The user opens Device, confirms device readiness or stream state, and rests in the selected view. |
| PlatformIO install recovery | `1 -> 2 -> 3 -> 3.3 -> 3.3.1 -> 3.3.2 -> 3.3.3 -> 7` | The user installs PlatformIO and reaches device-ready or streaming state. |
| Device retry after unavailable state | `1 -> 2 -> 3 -> 3.3 -> 3.3.4 -> 3.3 -> 7` | The user retries device detection after paused or error state. |
| Preview boundary | `1 -> 2 -> 3 -> 3.4 -> 7` | The user does not find a Preview top tab in this workbench panel set and remains in the Code workbench. |
| Terminal inspection | `1 -> 2 -> 5 -> 5.1 -> 5.1.1 -> 7` | The user opens Terminal, reviews streamed or preserved output, and remains in the active Code workspace. |
| Device Log inspection | `1 -> 2 -> 5 -> 5.2 -> 7` | The user opens Device Log, reviews log rows, and leaves the panel selected. |
| Device Log filters and pause | `1 -> 2 -> 5 -> 5.2 -> 5.3 -> 5.4 -> 5.5 -> 5.2 -> 7` | The user filters logs, pauses or resumes updates, controls autoscroll behavior, and returns to the log list. |
| Device Log monitor toggle | `1 -> 2 -> 5 -> 5.2 -> 5.2.1 -> 5.2 -> 7` | The user toggles live device log collection and returns to the log panel. |
| Device Log file reference | `1 -> 2 -> 5 -> 5.2 -> 5.2.3 -> 4 -> 4.1 -> 7` | The user opens a referenced file from a log row while keeping the Device Log panel available. |
| Bottom panel error or empty state | `1 -> 2 -> 5 -> 5.2 -> 5.2.2 -> 5.2 -> 7` | The user refreshes or adjusts filters after Device Log is empty or fails to load. |
| Right panel collapse and restore | `1 -> 2 -> 6 -> 6.1 -> 3 -> 7` | The user hides and restores the right panel without losing the selected top tab. |
| Bottom panel collapse and restore | `1 -> 2 -> 5 -> 6.2 -> 5 -> 7` | The user hides and restores the bottom panel without losing Terminal or Device Log selection. |
| Layout resize | `1 -> 2 -> 6 -> 6.3 -> 7` | The user resizes the workbench and keeps active file, diff, terminal, or log context. |
| Empty center tab | `1 -> 2 -> 4 -> 4.3 -> 3.1 -> 7` | The center area shows the `No active tab` placeholder until the user opens a file or other Code tab. |
| Panel recovery summary | `1 -> 2 -> 3 -> 3.5 -> 7` | Right-panel failures stay isolated and the active Code session remains usable. |
| Center recovery summary | `1 -> 2 -> 4 -> 4.4 -> 7` | File, diff, and empty-tab issues recover without resetting panel selection. |
| Bottom recovery summary | `1 -> 2 -> 5 -> 5.6 -> 7` | Terminal and Device Log issues recover inside the bottom panel. |
| Layout recovery summary | `1 -> 2 -> 6 -> 6.4 -> 7` | Collapsed or resized panels can be restored while preserving workbench context. |

## Main Path

### 1 Code Workbench Entry

User entry: the user enters this journey from an already open Code session.

User action: the user starts inspecting panels around the active Code work area.

Visible UI state:

- The Code frame is visible with workspace sidebar, working directory bar, center tabs, right panel, and bottom panel.
- The right-panel top tabs and bottom-panel tab strip are available when the right panel is open.
- The active center area can be a Code conversation, file, diff, or placeholder.

Client state change: the active Code session, active workspace, active center tab, selected right-panel tab, selected bottom tab, and layout state are available for inspection.

Exit / next state: continue to `2 Stable Code Session Context`.

### 2 Stable Code Session Context

User entry: the workbench frame is visible and the user is ready to inspect panel content.

User action: the user confirms the current session/workspace, then chooses a right-panel tab, center tab, bottom tab, or layout control.

Visible UI state:

- The working directory bar keeps the current workspace visible.
- The center tab strip preserves the active Code conversation, file, diff, or empty-tab state.
- Panel changes do not replace the active Code session.

Client state change: session context stays stable while panel selections and center tab selections can change independently.

Exit / next state: the user can move to `3 Right Panel Inspection`, `4 Center File Or Diff Inspection`, `5 Bottom Panel Inspection`, or `6 Layout Adjustment`.

### 3 Right Panel Inspection

User entry: the right panel is visible.

User action: the user switches among `All Files`, `Changes`, and `Device`, then uses rows and actions inside the selected tab.

Visible UI state:

- `All Files`, `Changes`, and `Device` are visible as top tabs.
- The selected top tab changes the right-panel body.
- The center Code session remains visible beside the right panel.

Client state change: right-panel selection updates and the selected tab loads the relevant workspace information.

Exit / next state: the user can open files or diffs into `4 Center File Or Diff Inspection`, switch to `5 Bottom Panel Inspection`, adjust layout in `6 Layout Adjustment`, or rest in `7 Stable Selected Workbench View`.

### 4 Center File Or Diff Inspection

User entry: a file or changed file is opened from the right panel, or the center area has no active tab.

User action: the user reads a file, reviews a diff, scrolls, switches tabs, closes tabs, or returns to the right panel.

Visible UI state:

- File tabs show read-only file content when readable.
- Diff tabs show read-only diff content when available.
- Missing or invalid active tab state shows `No active tab` and `Create a new tab to get started.`

Client state change: the active center tab changes independently of right-panel and bottom-panel selections.

Exit / next state: the user can return to `3 Right Panel Inspection`, use `5 Bottom Panel Inspection`, adjust layout in `6 Layout Adjustment`, or rest in `7 Stable Selected Workbench View`.

### 5 Bottom Panel Inspection

User entry: the bottom panel is visible or can be expanded.

User action: the user switches between `Terminal` and `Device Log`, reviews output or logs, and uses available controls.

Visible UI state:

- `Terminal` and `Device Log` are visible as bottom tabs.
- The bottom panel body can show terminal output, device logs, or a collapsed body.
- The right panel and center tab remain part of the same Code workbench.

Client state change: bottom-tab selection and expanded/collapsed state update while the active session remains selected.

Exit / next state: the user can switch right-panel tabs, inspect center tabs, adjust layout, or rest in `7 Stable Selected Workbench View`.

### 6 Layout Adjustment

User entry: the user wants more room for the center work area, right panel, or bottom panel.

User action: the user collapses, expands, or resizes available panel regions.

Visible UI state:

- The right panel can hide or return.
- The bottom panel body can collapse or expand.
- Resizing changes visible space without replacing the center content.

Client state change: layout state updates while selected tabs and session context remain preserved.

Exit / next state: the user returns to panel inspection or rests in `7 Stable Selected Workbench View`.

### 7 Stable Selected Workbench View

User entry: the user has selected a workbench view and is no longer changing panel state.

User action: the user continues inspecting, starts another panel action, returns to the Code conversation, switches mode, or leaves the journey.

Visible UI state:

- The selected right-panel, center-tab, bottom-panel, and layout states remain visible.
- The active Code session remains aligned with the selected workspace.
- Any file, diff, terminal, device, or log state stays scoped to the current Code workbench.

Client state change: no new transition is required; the workbench rests in its latest stable state.

Exit / next state: end of Journey 07, or the user enters another Code, Device, Preview, or Authoring journey.

## Branch Journeys

### 3.1 All Files Selected

Trigger: the user clicks `All Files` in the right-panel top tabs.

Visible UI state:

- The file tree area loads the active workspace.
- Folder rows can expand and collapse.
- File rows can be selected to open a center file tab.
- No-workspace, empty, and retryable error states replace the tree when needed.

Allowed user actions: expand folders, collapse folders, select a file, refresh or retry when available, switch to `Changes` or `Device`, collapse the right panel, or leave the panel selected.

Client state change: workspace tree state loads and updates for the active workspace.

Recovery / next state: folder expansion goes to `3.1.1 Folder Expansion And Lazy Loading`; file selection goes to `3.1.2 File Row Opens A Center File Tab`; unavailable tree state goes to `3.1.3 File Tree Empty Or Unavailable`.

Blocks progress: no, unless the user specifically needs a file that cannot be listed.

### 3.1.1 Folder Expansion And Lazy Loading

Trigger: the user expands a folder in `All Files`.

Visible UI state:

- The selected folder opens under its parent row.
- Child rows appear after loading.
- Collapsing the folder hides child rows without leaving `All Files`.

Allowed user actions: expand another folder, collapse the current folder, select a file, refresh/retry, or switch tabs.

Client state change: folder children are added under the selected folder while right-panel selection remains `All Files`.

Recovery / next state: return to `3.1 All Files Selected`.

Blocks progress: temporarily, only while child rows are loading.

### 3.1.2 File Row Opens A Center File Tab

Trigger: the user clicks a file row in `All Files`.

Visible UI state:

- The clicked file opens or activates a center tab.
- The right panel remains on `All Files`.
- The center tab starts in file loading state or shows the existing file preview if already loaded.

Allowed user actions: read the file, switch files, close the center tab, open another folder, or switch right-panel tabs.

Client state change: the selected file becomes the active center tab for read-only preview.

Recovery / next state: continue to `4.1 File Tab Loading Or Loaded`.

Blocks progress: no.

### 3.1.3 File Tree Empty Or Unavailable

Trigger: `All Files` cannot show normal file rows because there is no workspace, the tree is empty, or loading fails.

Visible UI state:

- The right panel shows no-workspace, empty, or retryable error copy.
- `Retry` or refresh is available when the failure can be retried.
- No stale tree rows are treated as current.

Allowed user actions: choose or create a workspace when that path is available, retry, refresh, switch tabs, or leave the panel.

Client state change: file-tree loading is blocked or failed without changing the active Code session.

Recovery / next state: retry or workspace selection returns to `3.1 All Files Selected`; leaving the panel can settle in `7 Stable Selected Workbench View`.

Blocks progress: yes for opening a file from `All Files`; no for other workbench inspection.

### 3.2 Changes Selected

Trigger: the user clicks `Changes` in the right-panel top tabs.

Visible UI state:

- The changes area loads the active workspace's changed files.
- Changed-file rows can show status letters.
- `Retry`, `Refresh`, no-changes, no-workspace, no-git, and error states can appear.

Allowed user actions: refresh, retry, select a changed file, switch right-panel tabs, collapse the right panel, or leave the panel selected.

Client state change: changes state loads for the active workspace and can open center diff tabs.

Recovery / next state: changed-file selection goes to `3.2.1 Changed File Opens A Center Diff Tab`; no-change/no-git state goes to `3.2.2 No Changes Or No Git Repository`; load failure goes to `3.2.3 Changes List Error`.

Blocks progress: no, unless the user specifically needs a diff that is unavailable.

### 3.2.1 Changed File Opens A Center Diff Tab

Trigger: the user clicks a changed-file row in `Changes`.

Visible UI state:

- The clicked changed file opens or activates a center diff tab.
- The right panel remains on `Changes`.
- The center tab starts in diff loading state or shows the existing diff if already loaded.

Allowed user actions: read the diff, choose another changed file, refresh changes, close the center tab, or switch tabs.

Client state change: the selected changed file becomes the active center tab for read-only diff inspection.

Recovery / next state: continue to `4.2 Diff Tab Loading Or Loaded`.

Blocks progress: no.

### 3.2.2 No Changes Or No Git Repository

Trigger: the `Changes` tab loads but there is no change list to show.

Visible UI state:

- No-changes state tells the user there are no changed files.
- No-git state tells the user the workspace cannot provide a git change list.
- Diff-opening controls are not shown for absent rows.

Allowed user actions: refresh, switch right-panel tabs, inspect files, or leave the panel selected.

Client state change: the changes view remains loaded but has no selectable diff row.

Recovery / next state: refresh returns to `3.2 Changes Selected`; otherwise the user can rest in `7 Stable Selected Workbench View`.

Blocks progress: yes for opening a diff from `Changes`; no for other workbench inspection.

### 3.2.3 Changes List Error

Trigger: the `Changes` tab fails to load changed-file data.

Visible UI state:

- The changes area shows a retryable error instead of a change list.
- `Retry` or `Refresh` is available.

Allowed user actions: retry, refresh, switch tabs, or leave the panel selected.

Client state change: failed changes loading stays scoped to the `Changes` tab.

Recovery / next state: retry returns to `3.2 Changes Selected`.

Blocks progress: yes for opening a diff until the list reloads.

### 3.3 Device Selected

Trigger: the user clicks `Device` in the right-panel top tabs.

Visible UI state:

- The Device tab shows PlatformIO readiness, device status, diagnostics, and stream status when available.
- Setup, installing, loading, paused, and error states appear inside the Device tab.
- Retry is available for recoverable setup or detection failures.

Allowed user actions: install PlatformIO when offered, retry detection, refresh device status, switch tabs, or leave the Device tab selected.

Client state change: device readiness and live status are refreshed for the active workspace.

Recovery / next state: missing PlatformIO goes to `3.3.1 PlatformIO Missing`; setup progress goes to `3.3.2 PlatformIO Installing Or Polling`; ready/streaming goes to `3.3.3 Device Ready Or Streaming`; unavailable or errored status goes to `3.3.4 Device Unavailable, Paused, Or Errored`.

Blocks progress: no for file/diff/terminal inspection; yes for device-dependent inspection until setup or detection recovers.

### 3.3.1 PlatformIO Missing

Trigger: the Device tab determines PlatformIO is unavailable for the active workspace.

Visible UI state:

- The Device tab explains the missing PlatformIO requirement.
- `Install PlatformIO` is available when setup can start from the app.

Allowed user actions: click `Install PlatformIO`, switch tabs, or leave the Device tab selected.

Client state change: no device probing is treated as ready until PlatformIO setup starts or the requirement is otherwise satisfied.

Recovery / next state: clicking install moves to `3.3.2 PlatformIO Installing Or Polling`.

Blocks progress: yes for device readiness and stream inspection.

### 3.3.2 PlatformIO Installing Or Polling

Trigger: the user starts PlatformIO installation, or setup is being checked.

Visible UI state:

- The Device tab shows setup progress or polling feedback.
- Duplicate setup actions are blocked while setup is in progress.

Allowed user actions: wait, switch tabs, or retry later if setup fails.

Client state change: setup state refreshes until it resolves as available or failed.

Recovery / next state: success continues to `3.3.3 Device Ready Or Streaming`; failure continues to `3.3.4 Device Unavailable, Paused, Or Errored`.

Blocks progress: temporarily for device readiness.

### 3.3.3 Device Ready Or Streaming

Trigger: Device status resolves as available or a live stream is connected.

Visible UI state:

- Device status and diagnostics are visible.
- Stream state is current.
- The user can keep the Device tab selected while inspecting other workbench areas.

Allowed user actions: refresh, switch tabs, inspect logs in the bottom panel, or leave Device selected.

Client state change: live device updates keep the visible Device tab current for the active workspace.

Recovery / next state: the user can rest in `7 Stable Selected Workbench View`, or stream interruption can move to `3.3.4 Device Unavailable, Paused, Or Errored`.

Blocks progress: no.

### 3.3.4 Device Unavailable, Paused, Or Errored

Trigger: device detection fails, stream stops, setup fails, or the Device tab cannot maintain live status.

Visible UI state:

- The Device tab shows paused, unavailable, or error state.
- Last known status can remain visible when useful.
- Retry is available for setup or detection recovery.

Allowed user actions: retry, refresh, switch tabs, inspect logs, or leave Device selected.

Client state change: device status is marked unavailable or not live without clearing other workbench state.

Recovery / next state: retry returns to `3.3 Device Selected`; leaving the panel can settle in `7 Stable Selected Workbench View`.

Blocks progress: yes for live device inspection; no for other Code workbench inspection.

### 3.4 Preview Boundary

Trigger: the user looks for a preview surface within the Journey 07 right-panel tab set.

Visible UI state:

- The visible right-panel top tabs remain `All Files`, `Changes`, and `Device`.
- There is no `Preview` top tab in this journey's current workbench panel set.

Allowed user actions: continue with files, changes, device, terminal, or logs; enter the separate preview journey when the product exposes that route.

Client state change: no workbench state changes because Preview is outside this panel set.

Recovery / next state: return to `3 Right Panel Inspection` or rest in `7 Stable Selected Workbench View`.

Blocks progress: no for Journey 07; preview inspection belongs to a separate journey.

Center tab branch states:

### 4.1 File Tab Loading Or Loaded

Trigger: the user opens a file from `All Files`.

Visible UI state:

- The file tab shows loading until content resolves.
- Loaded text files show a path header, optional `Line N`, line numbers, and read-only code.
- The tab does not present file editing controls.

Allowed user actions: scroll, switch tabs, close the tab, open another file, or return to panel inspection.

Client state change: file content is read for preview and tied to the selected workspace.

Recovery / next state: binary or too-large content goes to `4.1.1 File Cannot Be Previewed As Text`; load failure goes to `4.1.2 File Read Error`; normal loaded state can settle in `7 Stable Selected Workbench View`.

Blocks progress: no.

### 4.1.1 File Cannot Be Previewed As Text

Trigger: the selected file is binary or too large for text preview.

Visible UI state:

- The file tab explains why read-only code is not shown.
- The tab remains part of the center tab strip.

Allowed user actions: close the tab, switch tabs, open another file, or return to `All Files`.

Client state change: the file preview stops at an explanatory state instead of rendering text.

Recovery / next state: return to `4 Center File Or Diff Inspection` or rest in `7 Stable Selected Workbench View`.

Blocks progress: yes for reading that file as text; no for other workbench tasks.

### 4.1.2 File Read Error

Trigger: file content fails to load.

Visible UI state:

- The file tab shows an unable-to-open state.
- Other tabs and panels remain visible.

Allowed user actions: reopen the file, choose another file, close the tab, or switch panels.

Client state change: file-read failure stays isolated to that center tab.

Recovery / next state: return to `3.1 All Files Selected` or rest in `7 Stable Selected Workbench View`.

Blocks progress: yes for that file preview; no for the rest of the Code session.

### 4.2 Diff Tab Loading Or Loaded

Trigger: the user opens a changed file from `Changes`.

Visible UI state:

- The diff tab shows loading until diff content resolves.
- Loaded diffs show a file path header, added and removed counts, hunk rows, and old/new line numbers.
- The diff is read-only.

Allowed user actions: scroll, switch tabs, close the tab, open another changed file, or return to the changes list.

Client state change: diff content is read for the selected changed file and tied to the active workspace.

Recovery / next state: empty diff output goes to `4.2.1 No Diff Output For Selected File`; load failure goes to `4.2.2 Diff Read Error`; normal loaded state can settle in `7 Stable Selected Workbench View`.

Blocks progress: no.

### 4.2.1 No Diff Output For Selected File

Trigger: the selected changed file opens a diff tab but has no renderable diff output.

Visible UI state:

- The diff tab shows a no-diff empty state.
- The changes list can still be refreshed or used to choose another file.

Allowed user actions: return to `Changes`, refresh changes, close the diff tab, or inspect another file.

Client state change: the diff tab remains open but has no hunk rows to render.

Recovery / next state: return to `3.2 Changes Selected` or rest in `7 Stable Selected Workbench View`.

Blocks progress: yes for reviewing that diff; no for other workbench tasks.

### 4.2.2 Diff Read Error

Trigger: diff content fails to load.

Visible UI state:

- The diff tab shows an unable-to-open state.
- Other tabs and panels remain visible.

Allowed user actions: reopen the changed file, choose another changed file, close the tab, or switch panels.

Client state change: diff-read failure stays isolated to that center tab.

Recovery / next state: return to `3.2 Changes Selected` or rest in `7 Stable Selected Workbench View`.

Blocks progress: yes for that diff preview; no for the rest of the Code session.

### 4.3 No Active Center Tab

Trigger: all center tabs are closed, or the active tab lacks enough information to render.

Visible UI state:

- The center area shows `No active tab`.
- The hint says `Create a new tab to get started.`
- Right and bottom panels can remain visible.

Allowed user actions: create or select a Code tab, open a file from `All Files`, open a diff from `Changes`, or leave the workbench stable.

Client state change: the center tab area has no valid active tab but the workspace and panel state remain available.

Recovery / next state: opening a file returns through `3.1 All Files Selected`; opening a diff returns through `3.2 Changes Selected`; otherwise the workbench can rest in `7 Stable Selected Workbench View`.

Blocks progress: yes for center content; no for right-panel or bottom-panel inspection.

Bottom panel branch states:

### 5.1 Terminal Selected

Trigger: the user clicks `Terminal` in the bottom panel.

Visible UI state:

- The bottom panel shows the workspace shell surface.
- Previous terminal history can appear when the terminal is reused.
- Live command output appears as it is produced.

Allowed user actions: type when the terminal is ready, scroll history, switch to `Device Log`, collapse the bottom panel, or continue reading output.

Client state change: the terminal opens or reuses a workspace shell and preserves history, live data, exit, and resize state.

Recovery / next state: output review continues in `5.1.1 Terminal Output Streaming Or Preserved`, or the user can collapse through `6.2 Bottom Panel Collapsed Or Expanded`.

Blocks progress: no.

### 5.1.1 Terminal Output Streaming Or Preserved

Trigger: the terminal has live output, history, or completed command output to show.

Visible UI state:

- Running output streams into the terminal surface.
- Completed or exited output remains available for inspection.
- The bottom panel remains scoped to the active workspace.

Allowed user actions: scroll, type another command when available, switch tabs, collapse the panel, or leave the output visible.

Client state change: terminal history and live output stay associated with the current workspace.

Recovery / next state: return to `5.1 Terminal Selected` or rest in `7 Stable Selected Workbench View`.

Blocks progress: no.

### 5.2 Device Log Selected

Trigger: the user clicks `Device Log` in the bottom panel.

Visible UI state:

- The bottom panel shows device log rows when available.
- Severity filters, USB monitor controls, pause/resume, refresh, and autoscroll behavior are available according to current state.
- Empty and retryable states stay inside the log panel.

Allowed user actions: filter logs, pause or resume updates, refresh, toggle monitor, open file references, switch to `Terminal`, collapse the panel, or leave logs visible.

Client state change: recent logs load for the active workspace and visible rows follow filter and pause state.

Recovery / next state: monitor changes go to `5.2.1 Log Monitor Enabled Or Disabled`; empty/error state goes to `5.2.2 Log Load Error Or Empty Log List`; file reference selection goes to `5.2.3 Log Row File Reference`; filters and scroll controls go through `5.3`, `5.4`, and `5.5`.

Blocks progress: no, unless the user specifically needs logs that are unavailable.

### 5.2.1 Log Monitor Enabled Or Disabled

Trigger: the user toggles the USB/device log monitor.

Visible UI state:

- The monitor control changes label or state to reflect whether live collection is enabled.
- Existing log rows remain available while monitor state changes.

Allowed user actions: toggle again, refresh, filter, pause, resume, or switch panels.

Client state change: live device log collection starts or stops for the active workspace.

Recovery / next state: return to `5.2 Device Log Selected`.

Blocks progress: no.

### 5.2.2 Log Load Error Or Empty Log List

Trigger: Device Log has no rows to show or fails to load.

Visible UI state:

- The log area shows an empty or retryable load state.
- Refresh remains the primary recovery action when retry is available.
- Filters may be adjusted if they are hiding matching rows.

Allowed user actions: refresh, adjust filters, toggle monitor, wait for new logs, or switch panels.

Client state change: the log panel does not treat stale rows as current when loading fails or no rows match.

Recovery / next state: return to `5.2 Device Log Selected` after refresh, filter adjustment, or new log arrival.

Blocks progress: yes for log review until rows appear; no for other workbench inspection.

### 5.2.3 Log Row File Reference

Trigger: a log row includes a file reference the user can open.

Visible UI state:

- The selected log row remains visible in the bottom panel.
- The center area opens or activates a file preview for the referenced file.

Allowed user actions: read the file, return to the logs, switch tabs, or continue filtering logs.

Client state change: the active center tab changes to a file preview while the Device Log panel remains selected.

Recovery / next state: continue to `4.1 File Tab Loading Or Loaded`.

Blocks progress: no.

Resize and collapse branch states:

### 6.1 Right Panel Collapsed Or Expanded

Trigger: the user clicks the right-panel toggle.

Visible UI state:

- Collapsed state gives more room to the center work area.
- Expanded state returns the right panel with its previous selected top tab.
- Center tab content remains visible.

Allowed user actions: reopen or close the panel, continue in center tabs, switch bottom tabs, or leave the layout stable.

Client state change: right-panel visibility updates while preserving selected right-panel tab and active center tab.

Recovery / next state: return to `3 Right Panel Inspection`, `4 Center File Or Diff Inspection`, or `7 Stable Selected Workbench View`.

Blocks progress: no.

### 6.2 Bottom Panel Collapsed Or Expanded

Trigger: the user clicks the bottom panel collapse or expand control.

Visible UI state:

- Collapsed state hides the bottom panel body while keeping the bottom strip available.
- Expanded state returns the previously selected bottom tab body.

Allowed user actions: expand, collapse, switch bottom tabs, continue center work, or inspect the right panel.

Client state change: bottom-panel visibility updates while preserving selected bottom tab.

Recovery / next state: return to `5 Bottom Panel Inspection` or `7 Stable Selected Workbench View`.

Blocks progress: no.

### 6.3 Panel Resized

Trigger: the user changes panel sizing where resizing is available.

Visible UI state:

- Center, right, and bottom regions adjust available space.
- File, diff, terminal, and log content remain in their current views.

Allowed user actions: continue resizing, switch panels, collapse a panel, or rest with the new layout.

Client state change: layout dimensions update without resetting selected tabs or loaded panel content.

Recovery / next state: rest in `7 Stable Selected Workbench View`.

Blocks progress: no.

## Detail States

### 3.6 Right-Panel Tab And Row Details

Trigger: the user uses the right panel as a tabbed inspection area rather than a navigation replacement.

Visible UI state:

- `All Files`, `Changes`, and `Device` remain top tabs in the same right-panel region.
- Row actions can expand folders, open files, open diffs, refresh lists, retry failed loads, or leave a tab selected.
- Right-panel loading, empty, no-workspace, no-git, unavailable, and retryable states remain inside the selected tab.

Allowed user actions: switch tabs, expand or collapse rows, open a file or diff into the center, retry or refresh, collapse the right panel, or leave the selected tab stable.

Client state change: right-panel tab and row state update while the active Code session, center tab, bottom tab, and workspace context remain available.

Recovery / next state: row actions can move to `3.1`, `3.2`, `3.3`, `4 Center File Or Diff Inspection`, or `7 Stable Selected Workbench View`.

Blocks progress: no for the whole workbench; only the specific unavailable panel task is blocked.

### 5.3 Severity And Text Filtering

Trigger: the user changes Device Log filters.

Visible UI state:

- Visible log rows narrow or expand according to severity and filter controls.
- The panel can show an empty filtered state if no row matches.

Allowed user actions: adjust filters again, clear filters, refresh, pause or resume logs, or switch panels.

Client state change: loaded log rows remain available while the visible list changes according to filter state.

Recovery / next state: return to `5.2 Device Log Selected`.

Blocks progress: no.

### 5.4 Pause And Resume

Trigger: the user pauses or resumes Device Log updates.

Visible UI state:

- Paused state keeps the visible list from jumping while the user reads.
- Resume returns the log panel to live visual updates.

Allowed user actions: resume, pause again, scroll, filter, refresh, or switch panels.

Client state change: incoming log display follows pause state without changing the selected Code session.

Recovery / next state: return to `5.2 Device Log Selected`.

Blocks progress: no.

### 5.5 Autoscroll State

Trigger: the log viewport is following the latest entry, or the user scrolls away from the bottom.

Visible UI state:

- With autoscroll active, new rows keep the latest logs in view.
- When the user scrolls away, the viewport preserves the reading position until the user returns to latest logs.

Allowed user actions: scroll to latest, keep reading older logs, pause, resume, filter, or switch panels.

Client state change: viewport-following state changes independently from loaded log data.

Recovery / next state: return to `5.2 Device Log Selected`.

Blocks progress: no.

### 6.5 Resize And Collapse Control Details

Trigger: the user adjusts the right panel or bottom panel to make room for the center work area or to restore hidden panel content.

Visible UI state:

- Collapsed right panel still leaves the center work area and active bottom tab available.
- Collapsed bottom panel keeps bottom tab labels available while hiding the bottom body.
- Resize keeps file, diff, terminal, log, and device context attached to the same workbench state.

Allowed user actions: collapse, expand, resize, switch panels, continue center work, or rest with the adjusted layout.

Client state change: layout details update without clearing selected tabs, loaded file content, diff content, terminal history, device state, or logs.

Recovery / next state: return to `3 Right Panel Inspection`, `5 Bottom Panel Inspection`, `6 Layout Adjustment`, or `7 Stable Selected Workbench View`.

Blocks progress: no.

## State Language

### 0.1 Workbench Tab Labels And Panel Names

Trigger: the user reads panel names while choosing where to inspect files, changes, device state, terminal output, or device logs.

Visible UI state:

- Right-panel top tabs use `All Files`, `Changes`, and `Device`.
- Bottom-panel tabs use `Terminal` and `Device Log`.

Allowed user actions: switch to any visible panel tab, leave the selected tab stable, or continue inspecting the current panel.

Client state change: tab language identifies available panel targets without changing selected tabs, workspace context, loaded files, diffs, terminal output, or logs.

Recovery / next state: any Journey 07 state can display this language while preserving its own route.

Blocks progress: no.

### 0.2 Empty, Loading, And Unavailable Copy

Trigger: the user lands on a workbench panel or center tab that has no rows, is loading, cannot render, or cannot access the requested content.

Visible UI state:

- Center empty state uses `No active tab` and `Create a new tab to get started.`
- File and diff unavailable states explain binary, too-large, no-output, or unable-to-open conditions.
- Right-panel empty states explain no workspace, empty tree, no changes, no git repository, unavailable device, or retryable load failure.

Allowed user actions: wait, select another item, create or select a tab, retry, refresh, switch panels, or leave the workbench stable.

Client state change: status copy replaces stale content without clearing other panel state.

Recovery / next state: return to the relevant panel branch, recovery summary, or `7 Stable Selected Workbench View`.

Blocks progress: only for the affected content.

### 0.3 Device Log Controls And Live-Update Copy

Trigger: the user inspects Device Log controls while logs are live, paused, filtered, empty, or following the latest entry.

Visible UI state:

- Log controls use `Pause`, `Resume`, `Autoscroll`, severity labels, monitor state, and refresh language.
- Filtered-empty copy tells the user to adjust filters or refresh.
- Paused and autoscroll state explains whether new rows move the viewport.

Allowed user actions: filter logs, pause, resume, refresh, toggle monitor, return to latest logs, or switch panels.

Client state change: log-control language tracks visible row behavior without changing the selected Code session.

Recovery / next state: return to `5.2 Device Log Selected`, `5.3 Severity And Text Filtering`, `5.4 Pause And Resume`, or `5.5 Autoscroll State`.

Blocks progress: no.

### 0.4 Recovery Action Language

Trigger: the user sees a recoverable panel, file, diff, device, terminal, log, or layout condition.

Visible UI state:

- Recoverable panels use direct actions such as `Retry` and `Refresh`.
- Device setup can show `Install PlatformIO`.
- Layout recovery keeps expand, collapse, and restore actions near the affected panel.

Allowed user actions: retry, refresh, install setup support when offered, restore a hidden panel, choose another item, or continue working elsewhere.

Client state change: recovery language keeps the failed or hidden state scoped to the affected panel.

Recovery / next state: any recovery state can display this language while preserving its own route.

Blocks progress: only for the affected task.

## Errors And Recovery

### 3.5 Right-Panel Load And Workspace Recovery

Trigger: files, changes, or device information cannot load normally.

Visible UI state:

- The affected right-panel tab shows loading, empty, no-workspace, unavailable, or retryable error state.
- Other Code workbench regions remain visible.

Allowed user actions: choose or create a workspace when relevant, click `Retry`, click `Refresh`, switch tabs, or keep working elsewhere.

Client state change: the failed right-panel state stays scoped to that tab.

Recovery / next state: retry or refresh returns to `3.1 All Files Selected`, `3.2 Changes Selected`, or `3.3 Device Selected`; otherwise the user can rest in `7 Stable Selected Workbench View`.

Blocks progress: only for the affected right-panel task.

### 4.4 File, Diff, And Empty-Tab Recovery

Trigger: the center file/diff tab fails, cannot render content, has no output, or has no valid active tab.

Visible UI state:

- File or diff issues are shown inside the affected center tab.
- Empty center state shows `No active tab` and `Create a new tab to get started.`
- Right and bottom panels remain available.

Allowed user actions: open another file, open another diff, create/select a Code tab, close the failed tab, or continue panel inspection.

Client state change: center-tab failure does not clear right-panel or bottom-panel state.

Recovery / next state: return to `3.1 All Files Selected`, `3.2 Changes Selected`, `4 Center File Or Diff Inspection`, or `7 Stable Selected Workbench View`.

Blocks progress: only for the affected center tab.

### 5.6 Terminal And Log Recovery

Trigger: terminal output exits, Device Log is empty, log load fails, filters hide all rows, or live log updates are paused.

Visible UI state:

- Terminal output remains inspectable after completion or exit.
- Device Log shows empty, filtered, paused, or retryable state without replacing the Code session.

Allowed user actions: switch bottom tabs, refresh logs, clear filters, resume logs, collapse the panel, or continue center work.

Client state change: bottom-panel recovery is independent from center file/diff inspection.

Recovery / next state: return to `5.1 Terminal Selected`, `5.2 Device Log Selected`, `6.2 Bottom Panel Collapsed Or Expanded`, or `7 Stable Selected Workbench View`.

Blocks progress: only for the affected terminal or log task.

### 6.4 Layout Recovery

Trigger: a panel is collapsed, resized too small for the user's current task, or hidden while the user needs it again.

Visible UI state:

- The user can reopen the right panel, expand the bottom panel, or adjust space.
- Previously selected tabs and loaded content return when the panel is visible again.

Allowed user actions: reopen, expand, resize, switch tabs, or keep the current layout.

Client state change: layout recovery returns or updates panel visibility without resetting workbench context.

Recovery / next state: return to `3 Right Panel Inspection`, `5 Bottom Panel Inspection`, or `7 Stable Selected Workbench View`.

Blocks progress: no.

## Wireframe

The matching `code-workbench-panels.wireframe.html` must use this record as its source of truth.

- The canvas and jump list must keep first-level bands in this order: `Main happy path`, `Route map`, `Branch journeys`, `Detail states`, `State language`, and `Errors and recovery`.
- The route rows must be generated from the Route Table above, with one full-size product wireframe row for every listed route.
- Branch, detail, state-language, and recovery bands must remain separate; route rows do not replace those bands.
- Route rows and Branch journeys should show the Code workbench shell as full product wireframe cards.
- Detail states should use Journey 1 style summary/status cards for filters, pause/resume, autoscroll, panel inspection details, and resize/collapse details.
- State language should use Journey 1 style copy/status cards for tabs, empty/loading/error copy, Device Log controls, and recovery action language.
- Errors and recovery should use Journey 1 style recovery summary index cards that reference the full branch journeys and recovery routes above.
