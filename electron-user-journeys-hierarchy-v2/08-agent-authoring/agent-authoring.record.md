# Journey 08: Agent Authoring

This Markdown file is the text counterpart to `agent-authoring.wireframe.html`. It documents the current implemented Agent Authoring journey in product terms. Each section ID matches a card in the wireframe.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branch, detail, state-language, and recovery states use decimal IDs scoped to the step where the state starts. Sequential follow-up states use nested decimal IDs.

Main path states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `1` | Main path | Journey 08 | Code workspace entry with an available authoring action. |
| `2` | Main path | Journey 08 | Agent Authoring opens or focuses. |
| `3` | Main path | Journey 08 | Main authoring editor area. |
| `4` | Main path | Journey 08 | Core file edit and save loop. |
| `5` | Main path | Journey 08 | Skills management and probe path. |
| `6` | Main path | Journey 08 | Capabilities review path. |
| `7` | Main path | Journey 08 | Leave, return, or publish entry. |
| `8` | Main path | Journey 08 | Stable authoring state. |

Branch journey states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `2.1` | Branch journey | `2 Agent Authoring Surface` | Authoring files are still resolving. |
| `3.1` | Branch journey | `3 Main Editor Area` | `Soul` section is active. |
| `3.2` | Branch journey | `3 Main Editor Area` | `Style` section is active. |
| `3.3` | Branch journey | `3 Main Editor Area` | `Tools` section is active. |
| `3.4` | Branch journey | `3 Main Editor Area` | `Heartbeat` section is active. |
| `3.5` | Branch journey | `3 Main Editor Area` | `Bootstrap` section is active. |
| `5.2` | Branch journey | `5 Skills Management` | No skills are present. |
| `5.3` | Branch journey | `5 Skills Management` | A readable skill is selected for editing. |
| `5.4` | Branch journey | `5 Skills Management` | Selected skill preview is visible. |
| `5.5` | Branch journey | `5 Skills Management` | `New Skill` dialog is open. |
| `5.6` | Branch journey | `5 Skills Management` | Probe tab is open for the selected skill. |
| `6.3` | Branch journey | `6 Capabilities Review` | Device manifest view is visible. |
| `6.4` | Branch journey | `6 Capabilities Review` | Difference view is loading. |
| `6.5` | Branch journey | `6 Capabilities Review` | User refreshes capability state. |
| `7.1` | Branch journey | `7 Leave, Return, Or Publish Entry` | User returns to Code workspace work. |
| `7.2` | Branch journey | `7 Leave, Return, Or Publish Entry` | User opens publish entry from Agent Authoring. |
| `7.3` | Branch journey | `7 Leave, Return, Or Publish Entry` | User opens review status details. |
| `7.4` | Branch journey | `7 Leave, Return, Or Publish Entry` | User follows the last published catalog link. |
| `7.5` | Branch journey | `7 Leave, Return, Or Publish Entry` | User closes a publish or review child surface. |

Detail states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `4.1` | Detail state | `4 Core File Edit And Save Loop` | Active editable target is dirty. |
| `4.2` | Detail state | `4 Core File Edit And Save Loop` | Selected file save is in progress. |
| `4.2.1` | Detail state | `4.2 Selected File Saving` | Selected file save succeeds. |
| `4.3` | Detail state | `4 Core File Edit And Save Loop` | Multiple core sections are dirty. |
| `4.3.1` | Detail state | `4.3 Save All` | Save All completes for every dirty section. |
| `4.4` | Detail state | `4 Core File Edit And Save Loop` | User reverts the active dirty section. |
| `5.1` | Detail state | `5 Skills Management` | Skills list is loading. |
| `5.5.1` | Detail state | `5.5 New Skill Dialog` | New skill is created successfully. |
| `5.6.2` | Detail state | `5.6 Probe Tab` | Probe is running. |
| `5.6.2.1` | Detail state | `5.6.2 Probe Running` | Probe succeeds. |
| `6.1` | Detail state | `6 Capabilities Review` | Capability device state is loading. |
| `6.4.1` | Detail state | `6.4 Difference View Loading` | Difference view is ready. |
| `6.5.1` | Detail state | `6.5 Capabilities Refresh` | Refresh succeeds. |

State language states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Shared authoring status and control labels. |

Errors and recovery states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `2.2` | Error / recovery | `2 Agent Authoring Surface` | Agent Authoring entry is unavailable for the current workspace. |
| `3.6` | Error / recovery | `3 Main Editor Area` | User tries to switch sections while dirty. |
| `3.6.1` | Error / recovery | `3.6 Unsaved Section Switch` | Discard and section switch are confirmed. |
| `3.6.2` | Error / recovery | `3.6 Unsaved Section Switch` | Section switch is canceled. |
| `4.2.2` | Error / recovery | `4.2 Selected File Saving` | Selected file save fails. |
| `4.3.2` | Error / recovery | `4.3 Save All` | Save All leaves one or more failures. |
| `5.5.2` | Error / recovery | `5.5 New Skill Dialog` | New skill slug is invalid or duplicate. |
| `5.5.3` | Error / recovery | `5.5 New Skill Dialog` | New skill creation fails. |
| `5.6.1` | Error / recovery | `5.6 Probe Tab` | Probe is unavailable. |
| `5.6.2.2` | Error / recovery | `5.6.2 Probe Running` | Probe fails. |
| `6.2` | Error / recovery | `6 Capabilities Review` | No focused device is connected. |
| `6.4.2` | Error / recovery | `6.4 Difference View Loading` | Difference cannot be computed. |
| `6.5.2` | Error / recovery | `6.5 Capabilities Refresh` | Refresh fails. |
| `8.1` | Error / recovery | `2 Agent Authoring Surface` | Editable content cannot be read cleanly at first load. |
| `8.2` | Error / recovery | `3 Main Editor Area` | Section switching protects unsaved content. |
| `8.3` | Error / recovery | `4 Core File Edit And Save Loop` | Save failure recovery. |
| `8.4` | Error / recovery | `5 Skills Management` | Skills list or skill file cannot be loaded. |
| `8.5` | Error / recovery | `5 Skills Management` | Probe cannot run or returns failure. |
| `8.6` | Error / recovery | `6 Capabilities Review` | Capability refresh or comparison fails. |

| Route | Composition | Result / next state |
| --- | --- | --- |
| Core editor happy path | `1 → 2 → 2.1 → 3 → 3.1 → 4 → 4.1 → 4.2 → 4.2.1 → 8` | User opens Agent Authoring, edits Soul, saves successfully, and rests in saved state. |
| Section navigation without edits | `1 → 2 → 2.1 → 3 → 3.1 → 3.2 → 3.3 → 3.4 → 3.5 → 8` | User reviews core authoring sections and stops without creating dirty state. |
| Dirty section switch confirmed | `1 → 2 → 2.1 → 3 → 3.1 → 4 → 4.1 → 3.6 → 3.6.1 → 3.2 → 8` | User discards unsaved Soul edits and switches to Style. |
| Dirty section switch canceled | `1 → 2 → 2.1 → 3 → 3.1 → 4 → 4.1 → 3.6 → 3.6.2 → 4.1 → 8` | User cancels the discard prompt and stays on the dirty section. |
| Selected save failure then retry | `1 → 2 → 2.1 → 3 → 3.3 → 4 → 4.1 → 4.2 → 4.2.2 → 8.3 → 4.2 → 4.2.1 → 8` | Save failure preserves edits; retry succeeds and clears the error. |
| Save All succeeds | `1 → 2 → 2.1 → 3 → 3.1 → 4 → 4.1 → 3.6 → 3.6.1 → 3.2 → 4.1 → 4.3 → 4.3.1 → 8` | User creates multiple dirty core sections and saves them together. |
| Save All partial failure | `1 → 2 → 2.1 → 3 → 3.1 → 4 → 4.1 → 3.6 → 3.6.1 → 3.2 → 4.1 → 4.3 → 4.3.2 → 8.3 → 8` | User saves multiple sections; at least one failed section remains recoverable. |
| Revert active edit | `1 → 2 → 2.1 → 3 → 3.4 → 4 → 4.1 → 4.4 → 8` | User reverts a dirty Heartbeat edit and returns to clean state. |
| Skills edit and save | `1 → 2 → 2.1 → 3 → 5 → 5.1 → 5.3 → 4.1 → 4.2 → 4.2.1 → 8` | User edits a selected skill file through the shared save controls. |
| Skills empty then create | `1 → 2 → 2.1 → 3 → 5 → 5.1 → 5.2 → 5.5 → 5.5.1 → 5.3 → 8` | Empty Skills state leads to a successful new skill and selected skill editor. |
| Invalid new skill slug | `1 → 2 → 2.1 → 3 → 5 → 5.1 → 5.2 → 5.5 → 5.5.2 → 5.5 → 8` | Dialog blocks creation until the user fixes the skill name. |
| New skill creation failure | `1 → 2 → 2.1 → 3 → 5 → 5.1 → 5.2 → 5.5 → 5.5.3 → 5.5 → 8` | Dialog keeps user input and shows the create failure. |
| Skill preview | `1 → 2 → 2.1 → 3 → 5 → 5.1 → 5.3 → 5.4 → 5.3 → 8` | User previews a selected skill without changing its source. |
| Skill probe succeeds | `1 → 2 → 2.1 → 3 → 5 → 5.1 → 5.3 → 5.6 → 5.6.2 → 5.6.2.1 → 8` | User runs a probe and sees a response or tool-call result. |
| Skill probe unavailable | `1 → 2 → 2.1 → 3 → 5 → 5.1 → 5.3 → 5.6 → 5.6.1 → 8.5 → 5.6` | Probe is blocked until deployment, ownership, or loaded-skill state is fixed. |
| Skill probe fails then retryable | `1 → 2 → 2.1 → 3 → 5 → 5.1 → 5.3 → 5.6 → 5.6.2 → 5.6.2.2 → 8.5 → 5.6` | Probe failure stays inside the panel and can be retried. |
| Capabilities no-device path | `1 → 2 → 2.1 → 3 → 6 → 6.1 → 6.2 → 8` | User opens Capabilities without a connected device and sees a non-blocking empty state. |
| Capabilities manifest path | `1 → 2 → 2.1 → 3 → 6 → 6.1 → 6.3 → 8` | User reviews the focused device manifest. |
| Capabilities diff succeeds | `1 → 2 → 2.1 → 3 → 6 → 6.1 → 6.3 → 6.4 → 6.4.1 → 8` | User opens the difference view and reviews capability alignment. |
| Capabilities diff unavailable | `1 → 2 → 2.1 → 3 → 6 → 6.1 → 6.3 → 6.4 → 6.4.2 → 8.6 → 8` | Difference view cannot be computed and remains recoverable. |
| Capabilities refresh succeeds | `1 → 2 → 2.1 → 3 → 6 → 6.1 → 6.2 → 6.5 → 6.5.1 → 6.3 → 8` | User refreshes after a device becomes available and reaches manifest view. |
| Capabilities refresh fails | `1 → 2 → 2.1 → 3 → 6 → 6.1 → 6.3 → 6.5 → 6.5.2 → 8.6 → 8` | Refresh error stays scoped to Capabilities. |
| Publish entry handoff | `1 → 2 → 2.1 → 3 → 7 → 7.2` | User opens the publish/status entry and leaves Journey 08 for the publish journey if they continue. |
| Review detail return | `1 → 2 → 2.1 → 3 → 7 → 7.2 → 7.3 → 7.5 → 8` | User inspects review details and returns to Agent Authoring. |
| Last published link | `1 → 2 → 2.1 → 3 → 7 → 7.4 → 8` | User opens the catalog destination while the authoring surface remains stable. |
| Return to Code workspace | `1 → 2 → 2.1 → 3 → 7 → 7.1` | User leaves Agent Authoring and returns to Code work. |
| Agent workspace entry unavailable | `1 → 2.2` | User cannot enter Agent Authoring until a valid agent workspace is active. |

## Main Path

### 1 Code Workspace Entry

User entry: the user is in Code mode with an active workspace that exposes agent authoring.

User action: the user clicks `Edit Agent`.

Visible UI state:

- Code mode remains visible and `Edit Agent` is available for an agent workspace

Client state change: The current workspace becomes the authoring target.

Exit / next state: clicking `Edit Agent` advances to `2 Agent Authoring Surface`.

### 2 Agent Authoring Surface

User entry: the user has clicked `Edit Agent` or returned to an already open Agent Authoring surface.

User action: the user waits for authoring files to resolve.

Visible UI state:

- Header shows `Agent · <workspace>`, authoring rail, and initial resolving state when needed
- Header includes `Publish` or a review/status chip.
- The rail lists `Soul`, `Style`, `Tools`, `Heartbeat`, `Bootstrap`, `Skills`, and `Capabilities`.

Client state change: Editable sections, publish status, and save state are prepared.

Exit / next state: file resolution continues through `2.1 Authoring Files Resolving`, then the user reaches `3 Main Editor Area`.

### 3 Main Editor Area

User entry: the authoring surface is open and at least one section is available.

User action: the user selects a rail section, reads content, edits content, or enters Skills/Capabilities.

Visible UI state:

- The active rail item is highlighted and the matching editor or management view is visible
- Dirty core sections show a small dirty marker in the rail.
- Skills and Capabilities replace the core editor with management views.

Client state change: The active section and editable target are tracked.

Exit / next state: core authoring proceeds to `4 Core File Edit And Save Loop`; Skills proceeds to `5 Skills Management`; Capabilities proceeds to `6 Capabilities Review`.

### 4 Core File Edit And Save Loop

User entry: the user is in `Soul`, `Style`, `Tools`, `Heartbeat`, `Bootstrap`, or a selected skill editor.

User action: the user edits, saves, saves all, reverts, or attempts to switch away with unsaved content.

Visible UI state:

- File actions and footer show clean, unsaved, saving, saved, or failed state
- `Save` and `Revert` are disabled while content is clean.
- Saving shows `Saving...`; failure shows `Save error` while content remains editable.

Client state change: Clean, dirty, saving, saved, and error state are tracked per editable target.

Exit / next state: successful save, revert, or stable dirty/error state can rest at `8 Stable Authoring State`.

### 5 Skills Management

User entry: the user clicks `Skills`.

User action: the user refreshes the skill list, selects a skill, edits or previews it, creates a new skill, or opens Probe.

Visible UI state:

- Skills shows list count, `New Skill`, `Refresh`, `Edit`, `Preview`, and `Probe`
- Actions include `New Skill` and `Refresh`.
- Detail tabs include `Edit`, `Preview`, and `Probe`.

Client state change: The selected skill and probe readiness are tracked.

Exit / next state: editing a skill can route through `4 Core File Edit And Save Loop`; probing routes through `5.6 Probe Tab`.

### 6 Capabilities Review

User entry: the user clicks `Capabilities`.

User action: the user reads focused device status, reviews `View`, opens `View Diff`, or clicks `Refresh`.

Visible UI state:

- Capabilities shows device state, manifest, difference view, loading, empty, or error state
- Manifest view shows device-reported tools when available.
- No-device, loading, unable-to-compute, and refresh-error states remain inside this section.

Client state change: Device capability state and comparison state are refreshed when requested.

Exit / next state: the user rests in `8 Stable Authoring State` or retries through a capability recovery branch.

### 7 Leave, Return, Or Publish Entry

User entry: the user is on any stable Agent Authoring state.

User action: the user returns to Code work, opens `Publish`, opens review details, or follows the last published catalog link.

Visible UI state:

- Publish or status child surfaces open above Agent Authoring and can return
- Publish and review child surfaces appear above authoring and can return to authoring when closed.

Client state change: Authoring state is preserved while the user moves to adjacent surfaces.

Exit / next state: return to Code workspace, hand off to Journey 09, or return to `8 Stable Authoring State`.

### 8 Stable Authoring State

User entry: any main path, branch, save state, Skills state, Capabilities state, or publish return can settle here.

User action: the user pauses, continues editing, retries, saves, reverts, changes section, returns to Code, or publishes.

Visible UI state:

- Current section remains visible with clear saved, dirty, saving, or error language
- Save status is one of clean, unsaved, saving, saved, or failed.
- Skills and Capabilities retain their selected view when the user stays in those sections.

Client state change: Active section, editable target, selected skill, capability view, and status hints remain aligned.

Exit / next state: end of Journey 08 unless the user resumes a branch or hands off to Journey 09.

## Branch Journeys

### 2.1 Authoring Files Resolving

Trigger: Authoring files are still resolving.

Visible UI state: Main content shows `Resolving files...`; file actions are unavailable.

Allowed user actions: Waits.

Client state change: Editable files finish resolving.

Recovery / next state: `3`.

Blocks progress: temporarily.

### 3.1 Soul Section Active

Trigger: `Soul` section is active.

Visible UI state: Soul editor shows selected file label, `Revert`, `Save`, content, and footer status.

Allowed user actions: Clicks `Soul` or arrives at the default section.

Client state change: Soul becomes the active editable target.

Recovery / next state: `4` or `8`.

Blocks progress: no.

### 3.2 Style Section Active

Trigger: `Style` section is active.

Visible UI state: Style editor replaces the previous content and the rail marks `Style` active.

Allowed user actions: Clicks `Style`.

Client state change: Style becomes the active editable target.

Recovery / next state: `4` or `8`.

Blocks progress: no.

### 3.3 Tools Section Active

Trigger: `Tools` section is active.

Visible UI state: Tools editor replaces the previous content and the rail marks `Tools` active.

Allowed user actions: Clicks `Tools`.

Client state change: Tools becomes the active editable target.

Recovery / next state: `4` or `8`.

Blocks progress: no.

### 3.4 Heartbeat Section Active

Trigger: `Heartbeat` section is active.

Visible UI state: Heartbeat editor replaces the previous content and the rail marks `Heartbeat` active.

Allowed user actions: Clicks `Heartbeat`.

Client state change: Heartbeat becomes the active editable target.

Recovery / next state: `4` or `8`.

Blocks progress: no.

### 3.5 Bootstrap Section Active

Trigger: `Bootstrap` section is active.

Visible UI state: Bootstrap editor replaces the previous content and the rail marks `Bootstrap` active.

Allowed user actions: Clicks `Bootstrap`.

Client state change: Bootstrap becomes the active editable target.

Recovery / next state: `4` or `8`.

Blocks progress: no.

### 5.2 No Skills

Trigger: No skills are present.

Visible UI state: Empty state says `No skills yet. Click New Skill to create one.`

Allowed user actions: Reads the empty state or clicks `New Skill`.

Client state change: An empty skill list is treated as usable state.

Recovery / next state: `5.5` or `8`.

Blocks progress: no.

### 5.3 Selected Skill Edit

Trigger: A readable skill is selected for editing.

Visible UI state: List highlights the skill; detail tabs show `Edit`, `Preview`, and `Probe`.

Allowed user actions: Selects a skill and uses `Edit`.

Client state change: The selected skill becomes the active editable target for save/revert/status.

Recovery / next state: `4.1`, `5.4`, or `5.6`.

Blocks progress: no.

### 5.4 Skill Preview

Trigger: Selected skill preview is visible.

Visible UI state: Rendered Markdown preview replaces plaintext editing.

Allowed user actions: Clicks `Preview`.

Client state change: The same selected skill remains active without mutating source content.

Recovery / next state: `5.3` or `5.6`.

Blocks progress: no.

### 5.5 New Skill Dialog

Trigger: `New Skill` dialog is open.

Visible UI state: Dialog shows `Name`, slug preview, optional `Description`, target preview, `Cancel`, and `Create`.

Allowed user actions: Clicks `New Skill`, enters name and optional description.

Client state change: The target slug is normalized and validated.

Recovery / next state: `5.5.1`, `5.5.2`, or `5.5.3`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 5.6 Probe Tab

Trigger: Probe tab is open for the selected skill.

Visible UI state: Probe panel shows runtime status, loaded-skill state, prompt input, `Refresh`, and `Run Probe`.

Allowed user actions: Clicks `Probe`.

Client state change: Probe readiness is checked for the selected skill.

Recovery / next state: `5.6.1`, `5.6.2`, or `8.5`.

Blocks progress: no.

### 6.3 Manifest View

Trigger: Device manifest view is visible.

Visible UI state: `View` is active and grouped manifest details are visible when available.

Allowed user actions: Reviews the focused device manifest.

Client state change: Latest known device capability data is displayed.

Recovery / next state: `6.4` or `6.5`.

Blocks progress: no.

### 6.4 Difference View Loading

Trigger: Difference view is loading.

Visible UI state: `View Diff` becomes active and the body shows `Loading agent capabilities...`.

Allowed user actions: Clicks `View Diff`.

Client state change: Agent-declared and device-reported capabilities are compared.

Recovery / next state: `6.4.1` or `6.4.2`.

Blocks progress: temporarily.

### 6.5 Capabilities Refresh

Trigger: User refreshes capability state.

Visible UI state: `Refresh` is disabled or spinning while the section reloads.

Allowed user actions: Clicks `Refresh`.

Client state change: Fresh device state is requested.

Recovery / next state: `6.5.1` or `6.5.2`.

Blocks progress: temporarily.

### 7.1 Return To Code Workspace

Trigger: User returns to Code workspace work.

Visible UI state: Code workspace content regains focus.

Allowed user actions: Switches back to Code work.

Client state change: Saved state remains available and dirty buffers follow the current leave path.

Recovery / next state: Code workspace or `1`.

Blocks progress: no.

### 7.2 Publish Entry

Trigger: User opens publish entry from Agent Authoring.

Visible UI state: A status-first publish surface opens above Agent Authoring.

Allowed user actions: Clicks `Publish` or a review/status chip.

Client state change: Editor state remains mounted while publish status is reviewed.

Recovery / next state: Journey 09 or `7.5`.

Blocks progress: no.

### 7.3 Review Detail Entry

Trigger: User opens review status details.

Visible UI state: Review details open above the editor and can return to publish status.

Allowed user actions: Uses a review/status entry for a submitted package.

Client state change: Visible review state refreshes without clearing editor content.

Recovery / next state: `7.5`.

Blocks progress: no.

### 7.4 Last Published Link

Trigger: User follows the last published catalog link.

Visible UI state: Footer shows `Last published <time> · <version>` with an external-link affordance.

Allowed user actions: Clicks the footer last published link.

Client state change: The catalog destination opens outside the authoring surface.

Recovery / next state: `8`.

Blocks progress: no.

### 7.5 Publish Child Return

Trigger: User closes a publish or review child surface.

Visible UI state: User returns to Agent Authoring or the status-first publish surface.

Allowed user actions: Closes the child surface or completes a child action.

Client state change: The prior authoring or publish context becomes active again.

Recovery / next state: `7`, `8`, or Journey 09.

Blocks progress: no.

## Detail States

### 4.1 Dirty State

Trigger: Active editable target is dirty.

Visible UI state: Footer shows `Unsaved · <selected file>` and `Save`/`Revert` are enabled.

Allowed user actions: Changes editor content.

Client state change: Current content differs from the saved baseline.

Recovery / next state: `4.2`, `4.3`, `4.4`, `3.6`, or `7`.

Blocks progress: no.

### 4.2 Selected File Saving

Trigger: Selected file save is in progress.

Visible UI state: `Save` changes to `Saving...`; duplicate save is disabled while content remains visible.

Allowed user actions: Clicks `Save`.

Client state change: The active editable target starts saving.

Recovery / next state: `4.2.1` or `4.2.2`.

Blocks progress: temporarily.

### 4.2.1 Selected File Saved

Trigger: Selected file save succeeds.

Visible UI state: Footer shows `Saved <time> · <selected file>` and file actions return to clean state.

Allowed user actions: Waits for save to finish.

Client state change: Saved content becomes the new clean baseline.

Recovery / next state: `8`.

Blocks progress: no.

### 4.3 Save All

Trigger: Multiple core sections are dirty.

Visible UI state: Header shows `Save All (N)` and dirty markers remain until each save resolves.

Allowed user actions: Clicks `Save All (N)`.

Client state change: Each dirty core section starts its own save.

Recovery / next state: `4.3.1` or `4.3.2`.

Blocks progress: temporarily.

### 4.3.1 Save All Complete

Trigger: Save All completes for every dirty section.

Visible UI state: Dirty markers clear and the active footer returns to saved or clean state.

Allowed user actions: Waits for saves to settle.

Client state change: All successful sections receive new clean baselines.

Recovery / next state: `8`.

Blocks progress: no.

### 4.4 Revert Active Section

Trigger: User reverts the active dirty section.

Visible UI state: Editor content returns to the last saved version and unsaved/error language clears.

Allowed user actions: Clicks `Revert`.

Client state change: The active editable target returns to clean state.

Recovery / next state: `8`.

Blocks progress: no.

### 5.1 Skills List Loading

Trigger: Skills list is loading.

Visible UI state: Skill count area and actions remain visible; body can show `Loading skills...`.

Allowed user actions: Opens `Skills` or clicks `Refresh`.

Client state change: The visible skill list refreshes.

Recovery / next state: `5.2`, `5.3`, or `8.4`.

Blocks progress: temporarily.

### 5.5.1 New Skill Created

Trigger: New skill is created successfully.

Visible UI state: Dialog closes, list refreshes, and the new skill becomes selected.

Allowed user actions: Clicks `Create` with valid input.

Client state change: The new skill is added and selected.

Recovery / next state: `5.3`.

Blocks progress: no.

### 5.6.2 Probe Running

Trigger: Probe is running.

Visible UI state: `Run Probe` shows loading and the panel waits for a result.

Allowed user actions: Enters a prompt and clicks `Run Probe`.

Client state change: The probe prompt is submitted and running state is recorded.

Recovery / next state: `5.6.2.1` or `5.6.2.2`.

Blocks progress: temporarily.

### 5.6.2.1 Probe Success

Trigger: Probe succeeds.

Visible UI state: Panel shows completion time, response text, and any tool calls.

Allowed user actions: Reviews the result.

Client state change: The visible probe result is recorded for inspection.

Recovery / next state: `8`.

Blocks progress: no.

### 6.1 Capability Device State Loading

Trigger: Capability device state is loading.

Visible UI state: Header actions remain visible and `Refresh` can spin while loading.

Allowed user actions: Opens `Capabilities` or clicks `Refresh`.

Client state change: Current device capability state is requested.

Recovery / next state: `6.2`, `6.3`, or `8.6`.

Blocks progress: temporarily.

### 6.4.1 Difference View Ready

Trigger: Difference view is ready.

Visible UI state: Difference panel shows agent-declared and device-reported capability groups with status badges.

Allowed user actions: Reviews matched, missing, and unused groups.

Client state change: Comparison result is visible for the focused device.

Recovery / next state: `8`.

Blocks progress: no.

### 6.5.1 Capabilities Refresh Succeeded

Trigger: Refresh succeeds.

Visible UI state: Device status, manifest, empty state, or difference entry updates.

Allowed user actions: Waits for refresh to complete.

Client state change: Latest capability state replaces stale state.

Recovery / next state: `6.2`, `6.3`, or `6.4`.

Blocks progress: no.

## State Language

### 0.1 Shared Authoring Language

| Surface | Copy / control language | Meaning |
| --- | --- | --- |
| Code workspace entry | `Edit Agent` | Opens or focuses Agent Authoring for the active agent workspace. |
| Authoring header | `Agent · <workspace>` | Names the workspace being authored. |
| Authoring header | `Save All (N)` | Saves multiple dirty core sections. |
| Core rail | `Soul`, `Style`, `Tools`, `Heartbeat`, `Bootstrap` | Single editable agent sections. |
| Management rail | `Skills`, `Capabilities` | Multi-state authoring and validation sections. |
| File subheader | `Revert`, `Save`, `Saving...` | Active editable target actions and in-progress save state. |
| Footer | `Unsaved`, `Saved`, `Save error` | Current editable target status. |
| Skills | `No skills`, `New Skill`, `Refresh`, `Edit`, `Preview`, `Probe`, `Run Probe` | Skill list, authoring, preview, and probe actions. |
| Capabilities | `No device connected`, `View`, `View Diff`, `Refresh`, `Loading agent capabilities...` | Device capability and comparison states. |
| Publish entry | `Publish`, review/status chip, `Last published` | Handoff to publishing or previously published catalog destination. |

## Errors And Recovery

### 2.2 Agent Workspace Entry Unavailable

Trigger: Agent Authoring entry is unavailable for the current workspace.

Visible UI state: `Edit Agent` is absent when the active workspace is not authorable as an agent.

Allowed user actions: Looks for the entry or changes workspace.

Client state change: Agent Authoring does not open for that workspace.

Recovery / next state: Code workspace.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 3.6 Unsaved Section Switch

Trigger: User tries to switch sections while dirty.

Visible UI state: A discard confirmation asks whether to lose unsaved changes and switch.

Allowed user actions: Clicks another rail item before saving or reverting.

Client state change: Section switching is paused until the user chooses.

Recovery / next state: `3.6.1` or `3.6.2`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 3.6.1 Discard And Switch Confirmed

Trigger: Discard and section switch are confirmed.

Visible UI state: The requested section becomes active and the previous dirty marker clears.

Allowed user actions: Confirms the discard prompt.

Client state change: Previous content returns to the last saved baseline.

Recovery / next state: `3`.

Blocks progress: no.

### 3.6.2 Section Switch Canceled

Trigger: Section switch is canceled.

Visible UI state: The original section remains active with unsaved content visible.

Allowed user actions: Cancels the discard prompt.

Client state change: No section or content state changes.

Recovery / next state: `4.1`.

Blocks progress: no.

### 4.2.2 Selected File Save Failed

Trigger: Selected file save fails.

Visible UI state: `Save error` or error text is visible and edited content remains in place.

Allowed user actions: Reads the error or retries.

Client state change: The active target enters error state without discarding content.

Recovery / next state: `8.3`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 4.3.2 Save All Partial Failure

Trigger: Save All leaves one or more failures.

Visible UI state: Successful sections clear; failed section feedback and editable content remain.

Allowed user actions: Reads failure feedback and selects failed section if needed.

Client state change: Successful saves become clean and failed saves preserve edits.

Recovery / next state: `8.3`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 5.5.2 Invalid Or Duplicate Skill Slug

Trigger: New skill slug is invalid or duplicate.

Visible UI state: Inline validation appears and `Create` is disabled.

Allowed user actions: Types a name that normalizes to an invalid or duplicate slug.

Client state change: No skill is created.

Recovery / next state: `5.5`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 5.5.3 New Skill Create Failed

Trigger: New skill creation fails.

Visible UI state: Dialog stays open with the user input and inline error.

Allowed user actions: Clicks `Create` and the write fails.

Client state change: Draft dialog state is preserved and no skill is added.

Recovery / next state: `5.5`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 5.6.1 Probe Unavailable

Trigger: Probe is unavailable.

Visible UI state: Panel shows actionable unavailable language and disables `Run Probe` when needed.

Allowed user actions: Reads the deployment, workspace, or loaded-skill hint.

Client state change: Probe submission is blocked until prerequisites recover.

Recovery / next state: `8.5`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 5.6.2.2 Probe Failed

Trigger: Probe fails.

Visible UI state: Panel shows failure time and error details without leaving Probe.

Allowed user actions: Reviews the error and can retry.

Client state change: Running state clears and the prompt is preserved.

Recovery / next state: `8.5`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 6.2 No Device Connected

Trigger: No focused device is connected.

Visible UI state: Header says `No device connected`; body explains that capabilities populate after firmware reports a manifest.

Allowed user actions: Reads the empty state or connects a device.

Client state change: Capabilities remains usable without a focused device.

Recovery / next state: `6.5` or `8`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 6.4.2 Difference Unavailable

Trigger: Difference cannot be computed.

Visible UI state: Body says the comparison cannot be computed and points back to capability sources.

Allowed user actions: Reads the unable-to-compute state.

Client state change: No comparison result is available.

Recovery / next state: `8.6`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 6.5.2 Capabilities Refresh Failed

Trigger: Refresh fails.

Visible UI state: A red inline error appears while Capabilities remains open.

Allowed user actions: Reads the error and can retry.

Client state change: Previous visible context is preserved when possible.

Recovery / next state: `8.6`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 8.1 Initial Content Recovery

Trigger: Editable content cannot be read cleanly at first load.

Visible UI state: The editor can present fallback editable content or a local error instead of closing the surface.

Allowed user actions: Waits, then edits or saves if possible.

Client state change: The surface remains open and recoverable.

Recovery / next state: `4.1` or `8`.

Blocks progress: no.

### 8.2 Unsaved Switch Recovery

Trigger: Section switching protects unsaved content.

Visible UI state: The prompt prevents accidental loss when moving away from a dirty section.

Allowed user actions: Confirms or cancels the discard prompt.

Client state change: The client either reverts and switches or keeps the dirty section active.

Recovery / next state: `3.6.1` or `3.6.2`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 8.3 Save Failure Recovery

Trigger: Save failure recovery.

Visible UI state: Error remains visible while edited content is preserved.

Allowed user actions: Retries save, edits, reverts, or saves all again.

Client state change: The failed target stays editable and retryable.

Recovery / next state: `4.2`, `4.4`, or `8`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 8.4 Skills Recovery

Trigger: Skills list or skill file cannot be loaded.

Visible UI state: Skills shows loading, empty, missing-file, or list-error state without breaking the editor.

Allowed user actions: Refreshes, creates a skill, selects another skill, or keeps editing elsewhere.

Client state change: The problem remains scoped to Skills.

Recovery / next state: `5.1`, `5.2`, `5.3`, or `8`.

Blocks progress: no.

### 8.5 Probe Recovery

Trigger: Probe cannot run or returns failure.

Visible UI state: Probe hints and errors stay inside the Probe panel.

Allowed user actions: Restarts runtime, refreshes status, edits prompt, or retries.

Client state change: Selected skill is preserved and running state clears after failure.

Recovery / next state: `5.6` or `5.6.2`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

### 8.6 Capabilities Recovery

Trigger: Capability refresh or comparison fails.

Visible UI state: Error or unable-to-compute language stays inside Capabilities.

Allowed user actions: Refreshes again, connects device, fixes capability declarations, or changes section.

Client state change: Previous context is preserved where possible.

Recovery / next state: `6.5`, `6.4`, or `8`.

Blocks progress: yes for the affected action, no for unrelated authoring work.

## Wireframe

The wireframe must use this record as its source of truth. Its left jump list and canvas must keep these six first-level bands in order: `Main happy path`, `Route map`, `Branch journeys`, `Detail states`, `State language`, and `Errors and recovery`.

The `Main happy path` band should express Journey 08's own primary path as a concise chain: Code workspace entry, Agent Authoring surface, main editor area, dirty edit, selected save, saved state, and stable authoring state. It should not expand into every branch just because the Route Table has many rows.

The `Route map` band must include one full-size route row for every row in the Route Table, with `Core editor happy path` first. Each route row must use full product wireframe cards, including repeated states when that makes the route readable.

The `Branch journeys` band must keep full product wireframe cards for branch paths. The `Detail states`, `State language`, and `Errors and recovery` bands must be independent structured summary/status/copy cards, not route rows and not a combined support area.
