# Journey 13: Settings - Workspace Files

This Markdown file is the text counterpart to `settings-workspace-files.wireframe.html`. It documents the current implemented journey for editing fixed workspace Markdown files from Settings. Each section ID matches a visible state that can be drawn in the wireframe.

## Journey Scope

User entry: the user opens Settings and selects the `Workspace` tab.

User goal: the user views and edits `SOUL`, `IDENTITY`, `USER`, `AGENTS`, `TOOLS`, `HEARTBEAT`, `MEMORY`, and other fixed workspace files while keeping save state understandable.

End state: the user finishes viewing, saving, resetting through editor history, refreshing, retrying, or rests with unsaved edits for a selected workspace file.

Implementation maturity: Implemented.

## Numbering And Route Tables

These tables define the journey IDs used by this record and by the HTML wireframe. Main journey steps use plain numbers. Branches and sequential follow-up states use decimal IDs scoped to the parent step.

Main path states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 13 | Settings opens to the workspace file area. | Opens Settings and selects `Workspace`. | The Settings frame stays open with the left navigation active on `Workspace`. | The client mounts the workspace file surface and prepares to load the file list. | `2` |
| `2` | Main path | Journey 13 | Workspace file list begins loading. | Waits for file status. | A centered spinner and `Loading workspace files...` appear when no file list is available yet. | The client requests the current workspace file status list for the default agent. | `2.1` |
| `3` | Main path | Journey 13 | Workspace editor surface is available. | Reviews the selected file. | The `Workspace` header, description, `Refresh`, file pills, file description row, `Save`, and Markdown editor are visible. | The client has a file list and starts or finishes loading selected file content. | `3.1` or `3.2` |
| `4` | Main path | Journey 13 | User edits the selected file. | Types in the Markdown editor or uses editor toolbar controls. | The selected file remains visible, editor content changes, and the row can show `Unsaved`. | The client compares editor content with the last loaded or saved content. | `4.1` |
| `5` | Main path | Journey 13 | Journey rests in a stable workspace file state. | Continues editing, switches file, refreshes, saves, closes Settings, or leaves the content dirty. | The selected file is either clean, dirty, loading, retryable, or saved with toast feedback. | The client keeps the selected file, editor content, original content, save status, and file status list aligned. | End |

Branch journey states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Branch journey | `2 Workspace File List Loading` | Initial list request is in progress. | Waits. | The loading spinner remains visible until the list resolves. | The client keeps the whole tab in loading state while no prior list is available. | `3` or `2.2` |
| `2.3` | Branch journey | `3 Workspace Editor Surface` | User refreshes the file status list after the tab is already usable. | Clicks `Refresh`. | The `Refresh` button disables and shows a spinner; existing editor content remains visible. | The client reloads the file status list without clearing the current editor content. | `3` or `2.3.1` |
| `2.3.1` | Branch follow-up | `2.3 Refresh Files` | Refresh succeeds. | Continues from the refreshed list. | A success toast may appear and the file pills update their check or `Empty` status. | The client replaces the visible file status list and keeps the current selected file. | `3.2` |
| `3.1` | Branch journey | `3 Workspace Editor Surface` | Selected file content is loading. | Waits. | The editor area shows a centered spinner and `Loading file...` while header and file pills remain visible. | The client loads the selected file content and temporarily withholds the editable editor body. | `3.2` or `3.3` |
| `3.2` | Branch journey | `3 Workspace Editor Surface` | A selected file is loaded and clean. | Reads, edits, saves, refreshes, switches file, or closes Settings. | The active file pill is highlighted, the file name and description are shown, `Save` is disabled if there are no changes, and the editor is editable. | The client records the selected file content as the clean comparison point. | `4`, `2.3`, `3.1`, or `5` |
| `3.3` | Branch journey | `3 Workspace Editor Surface` | Selected fixed file is missing or empty. | Selects a pill marked `Empty` or lands on an empty file. | The file pill shows `Empty`; the description still explains the file purpose; the editor opens with empty content or placeholder copy. | The client treats the missing file as editable empty content for the selected file name. | `3.3.1`, `3.4`, `4`, or `5` |
| `3.3.1` | Branch follow-up | `3.3 Missing File Empty State` | User creates a missing file by saving content. | Types content and saves, or waits for autosave after editing. | `Unsaved` appears after typing; save feedback behaves like any other file. | The client writes content for the missing fixed file name. | `3.3.2` or `4.4` |
| `3.3.2` | Branch follow-up | `3.3.1 Create Missing File By Saving` | Missing file is now present. | Continues editing or selects another file. | The matching pill changes from `Empty` to the check status after the status list refreshes. | The client refreshes file status and stores the saved content as clean. | `5` |
| `3.4` | Branch journey | `3.3 Missing File Empty State` | Restore is not offered as a separate visible action in the current tab. | Refreshes, edits and saves, switches files, or closes Settings. | No dedicated restore button is shown; the user can create content by saving or refresh the file status. | The client does not restore a template from this surface; it keeps the selected missing file editable. | `2.3`, `3.3.1`, `3.1`, or `5` |
| `3.5` | Branch journey | `4.1 Dirty Editing` | User selects a different file while current content is unsaved. | Clicks another file pill. | The current editor remains visible briefly while the selected pill changes or prepares to change. | The client cancels the delayed autosave timer and starts saving the current file as the selected file changes. | `3.5.1` |
| `3.5.1` | Branch follow-up | `3.5 Select Another File With Unsaved Changes` | Current dirty file is saved as part of switching. | Waits during the switch. | Save feedback may appear for the previous file; the next selected file begins loading after the switch action. | The client writes the previous file content and records the new selected file for loading. | `3.5.2` or `4.4` |
| `3.5.2` | Branch follow-up | `3.5.1 Save During Switch` | Next file loads while the switch completes. | Waits for the newly selected file. | The next file pill is active and the editor shows `Loading file...` until the next content appears. | The client loads the next selected file and replaces the editor content and description. | `3.2` or `3.3` |
| `4.1` | Branch journey | `4 Edit Workspace File` | Editor content differs from the last loaded or saved version. | Types Markdown, formats content, or uses undo and redo. | `Unsaved` appears near the file description and `Save` becomes available. | The client marks the selected file dirty and schedules a delayed save. | `4.1.1`, `4.2`, `4.3`, `3.5`, or `5` |
| `4.1.1` | Branch follow-up | `4.1 Dirty Editing` | Delayed autosave starts after the user pauses typing. | Waits or keeps typing. | The same file remains selected; if the save is active, the save control shows saving state. | The client writes the current editor content unless a newer edit cancels and reschedules the delay. | `4.1.2` or `4.4` |
| `4.1.2` | Branch follow-up | `4.1.1 Delayed Autosave Saving` | Delayed autosave succeeds. | Continues editing or leaves the file. | A saved toast may appear, `Unsaved` clears, and `Save` disables. | The client updates the clean comparison point and refreshes file status. | `5` |
| `4.2` | Branch journey | `4 Edit Workspace File` | User explicitly saves dirty content. | Clicks `Save` or uses the editor save shortcut. | `Save` disables and shows a spinner while the selected file remains visible. | The client writes the current editor content for the selected file. | `4.2.1` or `4.4` |
| `4.2.1` | Branch follow-up | `4.2 Explicit Save` | Explicit save succeeds. | Continues after save feedback. | A saved toast names the file, `Unsaved` clears, and `Save` is disabled. | The client updates the clean comparison point and refreshes file status. | `5` |
| `4.3` | Branch journey | `4 Edit Workspace File` | User returns the editor to its last loaded or saved content through editor history. | Uses undo or redo until the content matches the clean version. | `Unsaved` clears once the editor content matches the last loaded or saved content. | The client treats the file as clean because editor content and saved comparison content match. | `5` |

Detail states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.6` | Detail state | `3 Workspace Editor Surface` | Workspace editor anatomy. | Scans the tab layout. | Header, description, Refresh, fixed file pills, selected-file description, Save, and editor are arranged in one Settings panel. | The client keeps selected file metadata and editor content aligned. | Active workspace state |
| `3.7` | Detail state | `3.3 Missing File Empty State` | Missing-file creation details. | Reads the empty state and decides whether to create content. | `Empty` marks the file pill while the editor still permits typing. | Saving typed content creates or replaces content for the selected fixed file. | `3.3.1` or `5` |
| `4.5` | Detail state | `4 Edit Workspace File` | Markdown editor toolbar and reset behavior. | Uses formatting, undo, redo, or save shortcut. | Toolbar controls remain above the editable document; undo or redo can return content to the clean version. | The client updates dirty state based on whether editor content matches the clean comparison point. | `4.1`, `4.3`, or `5` |

State language:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Shared Workspace labels. | Reads the tab and action labels. | Labels include `Workspace`, `Back to app`, `Refresh`, `Save`, and fixed file names. | The client uses the same visible vocabulary across clean, dirty, loading, and error states. | Any state |
| `2.4` | State language | `2 Workspace File List Loading` | Loading and refresh language. | Reads progress copy. | `Loading workspace files...`, `Loading file...`, `Refreshing...`, `Retry`, and `Files refreshed` distinguish loading scope. | Loading states stay tied to list, content, or refresh actions. | `2.1`, `2.3`, or `3.1` |
| `3.8` | State language | `3 Workspace Editor Surface` | File status language. | Reads file pill and dirty markers. | `Empty`, check status, and `Unsaved` describe whether a file exists and whether the current editor differs from saved content. | File status and dirty state are shown separately. | `3.2`, `3.3`, or `4.1` |
| `4.6` | State language | `4 Edit Workspace File` | Save feedback language. | Reads save and toast feedback. | `<file> saved`, `Failed to save <file>`, saving spinner, and disabled Save communicate write progress or failure. | Save success updates clean content; save failure preserves dirty content. | `4.2.1`, `4.4`, or `5` |

Errors and recovery states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.2` | Recovery state | `2 Workspace File List Loading` | Initial file list fails before any list is available. | Reads the error. | An error row shows `Failed to load files` and a `Retry` button. | The client stores a load error and keeps the file editor hidden until retry succeeds. | `2.2.1` |
| `2.2.1` | Recovery follow-up | `2.2 Initial List Failure` | User retries after the initial list failure. | Clicks `Retry`. | The tab returns to file-list loading and then either shows the workspace editor or the same error again. | The client clears the previous load error and requests the file list again. | `2.1`, then `3` or `2.2` |
| `4.4` | Recovery state | `4 Edit Workspace File` | Save fails during explicit save, delayed autosave, missing-file creation, or switch-save. | Reads failure feedback. | A failure toast names the file; the edited content remains in the editor. | The client leaves the clean comparison point unchanged and preserves the edited content. | `4.4.1` or `4.4.2` |
| `4.4.1` | Recovery follow-up | `4.4 Save Failure` | User retries saving the preserved content. | Clicks `Save` again or waits for another autosave after editing. | The same dirty content remains visible; saving state appears again during retry. | The client attempts to write the same selected file content again. | `4.2.1`, `4.1.2`, `3.3.2`, or `4.4` |
| `4.4.2` | Recovery follow-up | `4.4 Save Failure` | User continues editing after failure. | Changes the content, refreshes status, switches after another save attempt, or closes Settings with dirty content. | `Unsaved` remains visible because the editor still differs from the last saved version. | The client preserves the edited content and keeps the file dirty until a later save succeeds or content returns to clean. | `4.1`, `2.3`, `3.5`, or `5` |

Route table:

| Route | Composition | Result / next state |
| --- | --- | --- |
| Main view and explicit save | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 4 -> 4.1 -> 4.2 -> 4.2.1 -> 5` | The user opens Workspace, edits a loaded file, explicitly saves, and rests with clean content. |
| Initial list failure and retry | `1 -> 2 -> 2.1 -> 2.2 -> 2.2.1 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 5` | The first file list request fails, the user retries, and the workspace editor becomes usable. |
| Refresh while usable | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 2.3 -> 2.3.1 -> 3 -> 3.2 -> 5` | The user refreshes file status without losing the selected editor content. |
| Clean file switch | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 3.1 -> 3.2 -> 5` | The user selects another clean file and the editor swaps to the next file content. |
| Delayed autosave | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 4 -> 4.1 -> 4.1.1 -> 4.1.2 -> 5` | The user edits, pauses, autosave succeeds, and dirty state clears. |
| Reset through editor history | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 4 -> 4.1 -> 4.3 -> 5` | The user undoes or redoes edits until the content matches the last loaded or saved version. |
| Missing file created by save | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.3 -> 3.3.1 -> 3.3.2 -> 5` | A file marked `Empty` is created when the user saves content for that fixed file. |
| Missing file with no restore action | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.3 -> 3.4 -> 5` | The user sees that the file is empty and can refresh, create by saving, or leave it empty. |
| Switch with unsaved changes | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 4 -> 4.1 -> 3.5 -> 3.5.1 -> 3.5.2 -> 3.2 -> 5` | A save for the current dirty file starts as the next selected file loads. |
| Save failure then retry | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 4 -> 4.1 -> 4.2 -> 4.4 -> 4.4.1 -> 4.2.1 -> 5` | A failed explicit save preserves the edit; retry succeeds and clears dirty state. |
| Autosave failure then continue editing | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 4 -> 4.1 -> 4.1.1 -> 4.4 -> 4.4.2 -> 4.1` | Autosave fails, the editor keeps the changed content, and the user continues editing. |

## Main Path

### 1 Open Settings Workspace

User entry: the user is in the normal app shell and opens Settings.

User action: the user selects the `Workspace` tab from the Settings left navigation.

Visible UI state:

- Settings stays in its full-window frame with `Back to app` available.
- The `Workspace` tab is active in the left navigation.
- The workspace file surface begins loading.

Client state change: the client mounts the workspace file surface and prepares to load the fixed workspace file list.

Exit / next state: continue to `2 Workspace File List Loading`.

### 2 Workspace File List Loading

User entry: the `Workspace` tab has mounted.

User action: the user waits for the file status list.

Visible UI state:

- If no file list has loaded yet, the tab shows `Loading workspace files...`.
- No editor content is shown until the initial list is available.

Client state change: the client requests file status for the fixed workspace files.

Exit / next state: continue through `2.1 Initial List Loading`.

### 3 Workspace Editor Surface

User entry: the file status list has loaded.

User action: the user reviews file pills, selected file description, editor content, and available actions.

Visible UI state:

- Header title is `Workspace`.
- Supporting copy says `Edit your agent's personality and configuration files.`
- `Refresh` is available.
- Fixed file pills include `SOUL`, `IDENTITY`, `USER`, `AGENTS`, `TOOLS`, `HEARTBEAT`, and `MEMORY`.
- Present files show a check status; missing files show `Empty`.
- The selected file row shows the file label, description, possible `Unsaved` marker, and `Save`.

Client state change: the client keeps file status, selected file, selected description, editor content, clean comparison content, loading state, and save state in sync.

Exit / next state: selected content loads through `3.1 File Content Loading` and settles in `3.2 Selected File Clean` or `3.3 Missing File Empty State`.

### 4 Edit Workspace File

User entry: a selected file is loaded in the editor.

User action: the user types Markdown or uses toolbar controls such as headings, emphasis, lists, quote, code, horizontal rule, undo, and redo.

Visible UI state:

- The editor remains in the selected file context.
- Content changes are visible immediately in the editor.
- When content differs from the saved comparison point, `Unsaved` appears and `Save` becomes available.

Client state change: the client updates local editor content, compares it to the last clean content, and schedules a delayed save after edits.

Exit / next state: dirty content enters `4.1 Dirty Editing`.

### 5 Stable Workspace File State

User entry: the workspace file surface has reached a usable resting point.

User action: the user may continue editing, save, refresh, select another file, close Settings, or leave dirty content visible.

Visible UI state:

- A selected file remains visible with accurate clean, dirty, saving, loading, empty, or error feedback.
- The user can return to the app through the Settings frame.

Client state change: the client keeps the latest selected file and editor state available while Settings remains open.

Exit / next state: this journey ends until the user takes another Workspace action.

## Branch Journeys

### 2.1 Initial List Loading

Trigger: the `Workspace` tab starts with no loaded file status list.

Visible UI state: the tab shows a spinner with `Loading workspace files...`; file pills and editor content are not visible yet.

Allowed user actions: wait, close Settings, or return to the app.

Client state change: initial file status loading is active.

Recovery / next state: success proceeds to `3 Workspace Editor Surface`; failure proceeds to `2.2 Initial List Failure`.

Blocks progress: yes, because there is no file list or editor to use yet.

### 2.3 Refresh Files

Trigger: the user clicks `Refresh` while the workspace editor is already usable.

Visible UI state: existing file pills and editor content remain visible while `Refresh` disables and shows spinner feedback.

Allowed user actions: wait for refresh, continue reading current content, or close Settings.

Client state change: the client requests a fresh file status list without clearing the selected editor content.

Recovery / next state: success proceeds to `2.3.1 Refresh Files Success`; failure leaves the current surface usable and may show failure feedback.

Blocks progress: no.

### 2.3.1 Refresh Files Success

Trigger: the refresh request succeeds.

Visible UI state: a `Files refreshed` toast may appear, file pills update their check or `Empty` status, and the selected file remains selected.

Allowed user actions: continue editing, select a file, refresh again, or close Settings.

Client state change: file status list is replaced with the latest status.

Recovery / next state: return to `3.2 Selected File Clean` when selected content remains clean, or the active editor state if edits are still in progress.

Blocks progress: no.

### 3.1 File Content Loading

Trigger: the user lands on the default selected file or selects another file.

Visible UI state: header, file pills, and file description area remain visible; the editor area shows `Loading file...`.

Allowed user actions: wait, choose another file, refresh, close Settings, or return to the app.

Client state change: the client requests content for the selected file and prepares to replace editor content.

Recovery / next state: loaded content proceeds to `3.2 Selected File Clean`; missing or empty content proceeds to `3.3 Missing File Empty State`.

Blocks progress: temporarily for editing the selected file.

### 3.2 Selected File Clean

Trigger: selected file content has loaded and matches the current clean comparison point.

Visible UI state: the selected file pill is highlighted, file name and description are visible, `Unsaved` is absent, `Save` is disabled, and the editor is editable.

Allowed user actions: edit, select another file, refresh, close Settings, or return to the app.

Client state change: editor content and clean comparison content match.

Recovery / next state: editing proceeds to `4 Edit Workspace File`; clean file selection proceeds through `3.1 File Content Loading`; refresh proceeds to `2.3 Refresh Files`.

Blocks progress: no.

### 3.3 Missing File Empty State

Trigger: the selected fixed file is missing or has no content available.

Visible UI state: the file pill shows `Empty`; the selected file description remains visible; the editor opens empty and may show placeholder copy; `Save` is disabled until typing.

Allowed user actions: type content, save after editing, refresh status, select another file, close Settings, or return to the app.

Client state change: the selected missing file is represented as empty editable content.

Recovery / next state: typing and saving proceeds to `3.3.1 Create Missing File By Saving`; no action can rest in `5 Stable Workspace File State`.

Blocks progress: no for editing, yes for having meaningful saved content until the user creates it.

### 3.3.1 Create Missing File By Saving

Trigger: the user edits an empty missing file and saves it explicitly or through delayed autosave.

Visible UI state: `Unsaved` appears after typing, save feedback behaves like any other file, and the file remains selected.

Allowed user actions: keep editing, wait for autosave, click `Save`, or close Settings.

Client state change: the client writes content under the selected fixed file name.

Recovery / next state: save success proceeds to `3.3.2 Missing File Created`; save failure proceeds to `4.4 Save Failure`.

Blocks progress: temporarily while saving.

### 3.3.2 Missing File Created

Trigger: saving content for a missing file succeeds.

Visible UI state: a saved toast may appear, `Unsaved` clears, and after status refresh the file pill changes from `Empty` to check status.

Allowed user actions: continue editing, select another file, refresh, close Settings, or return to the app.

Client state change: saved content becomes the clean comparison point and file status refreshes.

Recovery / next state: rest in `5 Stable Workspace File State`.

Blocks progress: no.

### 3.4 Restore Not Offered In Current Tab

Trigger: the user is on a missing or empty file and looks for a restore action.

Visible UI state: the current tab does not show a dedicated restore button; the user can use `Refresh`, select another file, or create content by editing and saving.

Allowed user actions: refresh, type and save content, select another file, close Settings, or return to the app.

Client state change: no template restore is performed from this tab.

Recovery / next state: refresh proceeds to `2.3 Refresh Files`; create proceeds to `3.3.1 Create Missing File By Saving`; leaving the file empty can rest in `5 Stable Workspace File State`.

Blocks progress: no.

### 3.5 Select Another File With Unsaved Changes

Trigger: the user clicks a different file pill while the current selected file is dirty.

Visible UI state: the current file content remains visible briefly while the selected pill changes or prepares to change; save feedback may appear before the next file fully loads.

Allowed user actions: wait for the switch save and next file load.

Client state change: the client cancels any pending delayed autosave and starts saving the current dirty file as the selected file changes.

Recovery / next state: proceed to `3.5.1 Save During Switch`.

Blocks progress: temporarily.

### 3.5.1 Save During Switch

Trigger: dirty content must be handled as the selected file changes.

Visible UI state: the workspace file surface remains visible, save feedback may name the previous file, and the next selection is pending or beginning.

Allowed user actions: wait, then respond to success or failure.

Client state change: the previous file content is written while the next selected file is recorded for loading.

Recovery / next state: success proceeds to `3.5.2 Load Next File After Switch`; failure proceeds to `4.4 Save Failure`.

Blocks progress: temporarily.

### 3.5.2 Load Next File After Switch

Trigger: the previous dirty file has been saved during file switching.

Visible UI state: the newly selected file pill becomes active and the editor shows `Loading file...` until the next content appears.

Allowed user actions: wait, refresh, close Settings, or return to the app.

Client state change: selected file changes and next file content loading begins.

Recovery / next state: loaded content proceeds to `3.2 Selected File Clean`; missing or empty content proceeds to `3.3 Missing File Empty State`.

Blocks progress: temporarily for editing the next file.

### 4.1 Dirty Editing

Trigger: the user changes editor content so it differs from the last loaded or saved content.

Visible UI state: `Unsaved` appears near the selected file description, `Save` becomes available unless a save is already running, and the editor remains editable.

Allowed user actions: keep typing, click `Save`, use editor history, select another file, wait for delayed autosave, close Settings, or return to the app.

Client state change: dirty state is true and delayed autosave is scheduled.

Recovery / next state: delayed save proceeds to `4.1.1 Delayed Autosave Saving`; explicit save proceeds to `4.2 Explicit Save`; editor reset proceeds to `4.3 Reset Through Editor History`; file switch proceeds to `3.5 Select Another File With Unsaved Changes`.

Blocks progress: no, but unsaved content needs save or reset before it becomes clean.

### 4.1.1 Delayed Autosave Saving

Trigger: the user pauses after editing long enough for delayed autosave to start.

Visible UI state: the selected file stays in place and saving feedback may appear on the save control while the write is active.

Allowed user actions: wait, continue editing, or later retry if failure appears.

Client state change: the client writes the latest scheduled editor content unless a newer edit cancels and reschedules the save.

Recovery / next state: success proceeds to `4.1.2 Delayed Autosave Success`; failure proceeds to `4.4 Save Failure`.

Blocks progress: temporarily for clean save state.

### 4.1.2 Delayed Autosave Success

Trigger: delayed autosave succeeds.

Visible UI state: a saved toast may appear, `Unsaved` clears, and `Save` becomes disabled.

Allowed user actions: continue editing, select another file, refresh, close Settings, or return to the app.

Client state change: current editor content becomes the clean comparison point and file status refreshes.

Recovery / next state: rest in `5 Stable Workspace File State`.

Blocks progress: no.

### 4.2 Explicit Save

Trigger: the user clicks `Save` or uses the editor save shortcut while the selected file is dirty.

Visible UI state: `Save` disables and shows spinner feedback while the selected file and edited content remain visible.

Allowed user actions: wait for save result.

Client state change: the client writes the current editor content for the selected file.

Recovery / next state: success proceeds to `4.2.1 Explicit Save Success`; failure proceeds to `4.4 Save Failure`.

Blocks progress: temporarily for clean save state.

### 4.2.1 Explicit Save Success

Trigger: explicit save succeeds.

Visible UI state: a saved toast names the file, `Unsaved` clears, and `Save` becomes disabled.

Allowed user actions: continue editing, select another file, refresh, close Settings, or return to the app.

Client state change: current editor content becomes the clean comparison point and file status refreshes.

Recovery / next state: rest in `5 Stable Workspace File State`.

Blocks progress: no.

### 4.3 Reset Through Editor History

Trigger: the user uses undo or redo until the editor content again matches the last loaded or saved version.

Visible UI state: toolbar history controls update, `Unsaved` clears when the content matches the clean comparison point, and `Save` disables once there is no dirty content.

Allowed user actions: continue editing, save later changes, select another file, close Settings, or return to the app.

Client state change: dirty state returns to false because editor content and clean comparison content match.

Recovery / next state: rest in `5 Stable Workspace File State`.

Blocks progress: no.

## Detail States

### 3.6 Workspace Editor Anatomy

Trigger: the workspace editor surface is visible.

Visible UI state: the content area contains the `Workspace` heading, short description, `Refresh`, fixed file pills, selected-file description row, `Save`, and the Markdown editor.

Allowed user actions: refresh, select a file pill, edit, save, use editor toolbar controls, close Settings, or return to the app.

Client state change: selected file metadata, editor content, clean comparison content, file status, and save state remain aligned.

Recovery / next state: returns to the active workspace state.

Blocks progress: no.

### 3.7 Missing File Creation Details

Trigger: a fixed file pill is marked `Empty` and the user opens it.

Visible UI state: the file is not hidden; its description remains visible and the empty editor accepts content.

Allowed user actions: type content, save, refresh, select another file, close Settings, or return to the app.

Client state change: typed content becomes dirty local editor content and can create the missing fixed file when saved.

Recovery / next state: create through `3.3.1 Create Missing File By Saving`, refresh through `2.3 Refresh Files`, or rest in `5 Stable Workspace File State`.

Blocks progress: no for editing.

### 4.5 Markdown Editor Toolbar And Reset Behavior

Trigger: the editor is visible for the selected file.

Visible UI state: formatting controls, undo, redo, editable document body, and save shortcut behavior remain available inside the editor.

Allowed user actions: format content, undo, redo, type, use save shortcut, or continue to the Settings-level `Save`.

Client state change: the dirty marker is based on editor content compared with the last loaded or saved version; returning to the clean version clears `Unsaved`.

Recovery / next state: dirty content proceeds to `4.1 Dirty Editing`; reset through history proceeds to `4.3 Reset Through Editor History`.

Blocks progress: no.

## State Language

### 0.1 Shared Workspace Labels

Meaning: the flow uses stable product labels for navigation and file actions.

Visible UI language: `Workspace`, `Back to app`, `Refresh`, `Save`, `SOUL`, `IDENTITY`, `USER`, `AGENTS`, `TOOLS`, `HEARTBEAT`, and `MEMORY`.

State alignment: the same labels appear across loading, clean, dirty, missing, saving, and failure states.

### 2.4 Loading And Refresh Language

Meaning: loading copy distinguishes initial file-list loading, selected-file loading, and refresh.

Visible UI language: `Loading workspace files...`, `Loading file...`, `Refreshing...`, `Retry`, and `Files refreshed`.

State alignment: list loading blocks the editor until it succeeds; file loading keeps the tab chrome visible; refresh preserves current content.

### 3.8 File Status And Dirty Language

Meaning: the file list separates file existence from unsaved editor content.

Visible UI language: check status, `Empty`, and `Unsaved`.

State alignment: `Empty` belongs to the file pill; `Unsaved` belongs to the selected editor content.

### 4.6 Save Feedback Language

Meaning: save copy explains whether writing is active, successful, or failed.

Visible UI language: `Save`, saving spinner, `<file> saved`, and `Failed to save <file>`.

State alignment: save success clears dirty state; save failure preserves editor content and keeps recovery available.

## Errors And Recovery

### 2.2 Initial List Failure

Trigger: the initial file status list cannot be loaded.

Visible UI state: the workspace content area shows `Failed to load files` and a `Retry` button; file pills and editor content are hidden.

Allowed user actions: click `Retry`, close Settings, or return to the app.

Client state change: the load error is stored while the initial loading state ends.

Recovery / next state: `Retry` proceeds to `2.2.1 Retry After List Failure`.

Blocks progress: yes, until retry succeeds.

### 2.2.1 Retry After List Failure

Trigger: the user clicks `Retry` after the initial list failed.

Visible UI state: the tab attempts loading again; success shows the normal workspace editor surface, while failure returns to the same retry state.

Allowed user actions: wait, retry again after failure, close Settings, or return to the app.

Client state change: the previous error is cleared and a new file status request begins.

Recovery / next state: proceed to `2.1 Initial List Loading`, then `3 Workspace Editor Surface` or back to `2.2 Initial List Failure`.

Blocks progress: temporarily.

### 4.4 Save Failure

Trigger: explicit save, delayed autosave, missing-file creation, or save during switch fails.

Visible UI state: a failure toast names the file that could not save, edited content remains visible, and `Unsaved` remains visible when content still differs from the clean comparison point.

Allowed user actions: retry save, keep editing, select another file after another save attempt, refresh status, close Settings, or return to the app.

Client state change: the clean comparison point is not updated, and edited content is preserved.

Recovery / next state: retry proceeds to `4.4.1 Retry Save`; continuing edits proceeds to `4.4.2 Continue Editing After Save Failure`.

Blocks progress: no for editing, yes for reaching clean saved state.

### 4.4.1 Retry Save

Trigger: the user retries after a save failure.

Visible UI state: the same edited content remains available and saving feedback appears again during the retry.

Allowed user actions: wait for retry result or continue editing after another failure.

Client state change: the client attempts to save the preserved content again.

Recovery / next state: success returns to the relevant success state; failure returns to `4.4 Save Failure`.

Blocks progress: temporarily.

### 4.4.2 Continue Editing After Save Failure

Trigger: after save failure, the user keeps working instead of retrying immediately.

Visible UI state: the editor remains editable, `Unsaved` remains visible for dirty content, and the user can still use toolbar controls, `Save`, `Refresh`, or file selection.

Allowed user actions: edit, retry save, refresh file status, select another file, close Settings, or return to the app.

Client state change: edited content remains local and dirty until a later save succeeds or the user returns content to the clean version.

Recovery / next state: continue in `4.1 Dirty Editing`, refresh through `2.3 Refresh Files`, switch through `3.5 Select Another File With Unsaved Changes`, or rest in `5 Stable Workspace File State` with dirty content.

Blocks progress: no for editing, yes for clean saved state.

## Wireframe

The HTML wireframe must preserve five first-level bands in this order:

1. `Main happy path`
2. `Route path`
3. `Detail states`
4. `State language`
5. `Errors and recovery`

The Main happy path band must show only the main path states `1 -> 2 -> 3 -> 4 -> 5`, so it remains a readable summary of the user's primary path. Route rows must be drawn separately from the route table above, one full row per route. Every Route path row uses full-size product wireframe cards and copies repeated states when that makes the path readable.

The `Branch Journeys` section remains in this Markdown record as the textual source of branch behavior, but it is not a separate HTML band. Branch states that appear in route compositions are shown inside the corresponding Route path rows.

The product frame should show the Settings shell with `Workspace` active, fixed file pills, selected file description, Markdown editor, dirty/save/reset states, missing-file create behavior, switch-save behavior for unsaved file selection, and save failure/retry recovery.
