# Journey 16: Settings - About / Updates

This Markdown file documents the current implemented journey for the Settings About tab and the main UI update-ready notice. Each section ID matches a state that a wireframe card can represent.

User entry: the user opens Settings and selects `About`, or the user sees an update-ready notice while using the main app.

User goal: the user confirms the NoraHarness version, checks update status, downloads an update when available, and chooses whether to install now or later.

End state: the user knows the app is current, has an update ready for later installation, starts install and relaunch, or remains on a retryable update error.

Implementation maturity: Implemented.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches, detail states, state-language states, and recovery states use decimal IDs scoped to the step where the state starts. Sequential follow-up states use an additional decimal level.

About tab main path states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 16 | User enters Settings About. | Opens Settings, selects `About`, or arrives from an update notice. | The Settings shell is visible with `Back to app`, left navigation, and `About` selected. | The client mounts the About surface and starts loading app identity, app settings, and update state. | `2` |
| `2` | Main path | Journey 16 | About tab content is available. | Reads the app identity, version, auto-update setting, update status, and links. | `NoraHarness`, `Version`, `Your AI assistant, running locally.`, Auto-update, update status, update actions, links, and license text are visible. | The client holds the latest known build info, auto-update setting, and update state for this tab. | `2.1`, `3`, or `6` |
| `3` | Main path | Journey 16 | Manual update check begins. | Clicks `Check for Updates`. | The check button changes to `Checking...`, shows a spinning refresh icon, and is disabled while checking. | Update state changes from idle or previous result to checking. | `3.1`, `3.2`, or `3.3` |
| `4` | Main path | Journey 16 | User starts downloading an available update. | Clicks `Download <version>`. | The download action is available only for an available update and then enters downloading state. | Update state changes to downloading and starts reporting progress for the selected version. | `4.1` or `4.2` |
| `5` | Main path | Journey 16 | Downloaded update waits for user choice. | Reviews install choices after the package downloads. | The status says `Version <version> downloaded.` and `Install and Relaunch` is visible. | Update state is downloaded and remains stable until the user installs or leaves it for later. | `5.1`, `5.2`, or `6` |
| `6` | End state | Journey 16 | Stable end state for this journey. | Leaves Settings, keeps working, retries later, or returns to the update controls. | The user sees up-to-date, disabled, available, downloaded, installed/relaunching, or retryable error state. | The client rests with the latest visible app identity and update state. | End or another Journey 16 route |

Branch journey states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1.1` | Branch journey | `1 Settings About entry` | Main UI reports a downloaded update while Chat or Code remains usable. | Notices the update-ready prompt while using the main app. | A persistent notice says `Version <version> is ready to install.` with `Restart NoraHarness to finish the update.` and a `Restart` action. | The client surfaces downloaded update state outside Settings without blocking Chat or Code. | `1.1.1`, `5.1`, or `5.2` |
| `1.1.1` | Branch follow-up | `1.1 Main UI update-ready notice` | User opens Settings from the notice context or app chrome. | Opens Settings and selects `About`. | Settings About shows the same downloaded update and the `Install and Relaunch` action. | The client keeps the main notice state and Settings update state aligned. | `5` |
| `3.1` | Branch journey | `3 Manual update check` | Manual check finds no update. | Waits for the check result. | A success toast says `You are on the latest version.` and the status returns to a ready or last-checked state. | Update state returns to idle with the last checked time. | `6` |
| `3.2` | Branch journey | `3 Manual update check` | Manual check finds an available update. | Reviews the available version. | The status says `Version <version> is available.` and `Download <version>` appears. | Update state records the available version and enables the download action. | `4` or `6` |
| `4.1` | Branch journey | `4 Download update` | Download is in progress. | Waits while the package downloads. | The status shows downloaded bytes or percent, speed, and remaining size when available; conflicting update actions are disabled. | Update state reports downloading progress for the available version. | `4.1.1` or `4.2` |
| `4.1.1` | Branch follow-up | `4.1 Download in progress` | Download completes. | Waits for completion. | A success toast says `Update package downloaded.` and the status changes to `Version <version> downloaded.` | Update state changes from downloading to downloaded. | `5` |
| `5.1` | Branch journey | `5 Downloaded update choice` | User installs later. | Leaves Settings, dismisses the prompt if available, or keeps working. | The downloaded state remains visible in Settings, and the main app can remain usable. | The client keeps the update ready without starting installation. | `6` |
| `5.2` | Branch journey | `5 Downloaded update choice` | User chooses install. | Clicks `Install and Relaunch` in Settings or `Restart` from the main notice. | The install action is visible only after download is complete; after click, the status changes to installing and relaunching. | Update state changes to installing and the app prepares to relaunch. | `5.2.1` |
| `5.2.1` | Branch follow-up | `5.2 Install chosen` | App is installing and relaunching. | Waits for the app to quit and relaunch. | The current app surface is about to close; copy says the update is installing and relaunching. | The client starts the install/relaunch path and leaves the current running session. | App restart |

Detail states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Detail state | `2 About tab content` | App identity and version are visible before and after build info resolves. | Reads the app name and version. | A fallback `NoraHarness` name and version are visible immediately; when build info resolves, the displayed app name and version update if needed. | The client replaces fallback identity text with resolved build identity when available. | `2` |
| `2.2` | Detail state | `2 About tab content` | Auto-update setting is changed successfully. | Toggles Auto-update. | The switch changes state and a success toast confirms auto-update enabled or auto-update disabled. | The client saves the new setting and starts or stops automatic update checking according to the setting. | `2` |
| `2.5` | Detail state | `2 About tab content` | Project links remain available from About. | Clicks `GitHub`, `Website`, or `Documentation`. | Links are grouped separately from update controls; Settings stays open while the destination opens externally. | The client requests external navigation without closing the About tab. | `2` |

State language states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `6.2` | State language | `6 Stable end state` | Shared update status vocabulary. | Reads update state or chooses the matching action. | Labels include unavailable, disabled, ready to check, checking, available, downloading, downloaded, installing, up to date, error, retry, later, and restart. | The client keeps buttons, toasts, and status copy aligned with the current update state. | Any Journey 16 update state |

Recovery and error states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.3` | Recovery state | `2 About tab content` | Auto-update setting fails to save. | Toggles Auto-update and the save fails. | The switch returns to its previous value and an error toast says the setting failed to update. | The client rolls back the visible switch and keeps the prior auto-update setting. | `2` |
| `2.4` | Recovery state | `2 About tab content` | Update status is unavailable or disabled. | Reads the update status. | The status area says `Update status unavailable.` or shows a disabled reason such as unsupported updates. | The client does not expose download or install actions for an unavailable or disabled update path. | `2` or `6` |
| `3.3` | Recovery state | `3 Manual update check` | Manual check fails. | Waits for the failed result. | An error toast shows the failure reason and the About status shows the error. | Update state changes to error and preserves retryability. | `3.3.1` or `6` |
| `3.3.1` | Recovery follow-up | `3.3 Manual check failure` | User retries update check after failure. | Clicks `Check for Updates` again. | The status returns to checking and the button is disabled while the retry runs. | Update state leaves error and starts a new explicit check. | `3.1`, `3.2`, or `3.3` |
| `4.2` | Recovery state | `4 Download update` | Download fails. | Waits for the failed download result. | An error toast shows the failure reason; the status area shows an error and retryable update state. | Update state changes to error and does not present the package as downloaded. | `4.2.1` or `6` |
| `4.2.1` | Recovery follow-up | `4.2 Download failure` | User retries download after failure. | Returns to the available update state and clicks `Download <version>` again when available. | The status returns to downloading if retry starts; progress appears again. | Update state leaves error and starts another download attempt for the available version. | `4.1`, `4.1.1`, or `4.2` |
| `6.1` | Recovery summary | `6 Stable end state` | Update controls recover without blocking the app. | Retries, installs later, opens Settings, or continues working. | Errors, disabled state, and downloaded state stay visible, while normal Chat or Code work remains available unless the user starts relaunch. | Update state is isolated from the main work surface until install/relaunch is chosen. | `2`, `3`, `4`, `5`, or app restart |

## Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| About identity review | `1 -> 2 -> 2.1 -> 2.5 -> 6` | The user confirms app name, version, links, and license without starting an update action. |
| Auto-update changed successfully | `1 -> 2 -> 2.2 -> 6` | The user changes the auto-update setting and returns to stable About state. |
| Auto-update save failure | `1 -> 2 -> 2.3 -> 2 -> 6` | The switch rolls back, an error toast appears, and the prior setting remains active. |
| Update unavailable or disabled | `1 -> 2 -> 2.4 -> 6` | The user sees that update status or update actions are not available from this app state. |
| Manual check finds no update | `1 -> 2 -> 3 -> 3.1 -> 6` | The user sees latest-version feedback and remains on the About tab. |
| Manual check finds update but user waits | `1 -> 2 -> 3 -> 3.2 -> 6` | The user sees an available version and can return later to download it. |
| Download update and install later | `1 -> 2 -> 3 -> 3.2 -> 4 -> 4.1 -> 4.1.1 -> 5 -> 5.1 -> 6` | The update package is downloaded and remains ready while the user keeps working. |
| Download update and install now | `1 -> 2 -> 3 -> 3.2 -> 4 -> 4.1 -> 4.1.1 -> 5 -> 5.2 -> 5.2.1` | The user starts install and relaunch from Settings. |
| Check failure then retry succeeds with no update | `1 -> 2 -> 3 -> 3.3 -> 3.3.1 -> 3.1 -> 6` | The failed check is retried and ends in up-to-date state. |
| Check failure then retry finds update | `1 -> 2 -> 3 -> 3.3 -> 3.3.1 -> 3.2 -> 6` | The failed check is retried and returns an available update that the user can download later. |
| Download failure then retry completes | `1 -> 2 -> 3 -> 3.2 -> 4 -> 4.2 -> 4.2.1 -> 4.1 -> 4.1.1 -> 5 -> 5.1 -> 6` | A failed download returns to progress on retry and ends with a ready update. |
| Main UI notice installs now | `1.1 -> 5.2 -> 5.2.1` | The user restarts from the main update-ready notice and leaves the current app session. |
| Main UI notice hands off to Settings | `1.1 -> 1.1.1 -> 5 -> 5.1 -> 6` | The user opens Settings About, sees the same downloaded update, and installs later. |
| Recovery remains non-blocking | `1 -> 2 -> 3 -> 3.3 -> 6.1 -> 6` | The user can keep working or return to Settings later after an update error. |

## Main Path

### 1 Settings About Entry

User entry: the user opens Settings from the app chrome and selects `About`, or arrives because update state in the main app led them to the About tab.

User action: the user opens the About surface.

Visible UI state:

- The Settings shell fills the app window.
- `Back to app` is available.
- Left navigation includes `General`, `Workspace`, `Providers`, `Permissions`, `Voice`, `Channels`, `Advanced`, and `About`.
- `About` is the selected tab.

Client state change: the client mounts the About surface and starts loading app identity, saved auto-update setting, and the current update state.

Exit / next state: continue to `2 About Tab Content`.

### 2 About Tab Content

User entry: the About tab is mounted inside Settings.

User action: the user reads app identity, version, update status, auto-update setting, and project links.

Visible UI state:

- The app icon placeholder shows `NH`.
- The identity block shows `NoraHarness`, `Version <version>`, and `Your AI assistant, running locally.`
- Auto-update appears with the copy `Check NoraHarness Version for signed macOS arm64 update packages.`
- The status area shows the current update message.
- `Check for Updates` is visible when no update operation is already blocking it.
- `Download <version>` appears only when an update is available.
- `Install and Relaunch` appears only when a downloaded update is ready.
- `GitHub`, `Website`, `Documentation`, and `MIT License` remain visible.

Client state change: the client keeps app identity, update status, and auto-update setting in sync with the desktop app state.

Exit / next state: the user can read version details in `2.1`, change auto-update through `2.2` or `2.3`, open links through `2.5`, check updates through `3 Manual Update Check`, or leave through `6 Stable End State`.

### 3 Manual Update Check

User entry: the About tab is visible and no check, download, or install operation is already running.

User action: the user clicks `Check for Updates`.

Visible UI state:

- The button label changes to `Checking...`.
- The refresh icon spins.
- The check action is disabled while checking.
- Download and install actions are not newly exposed until the check resolves.

Client state change: update state changes to checking and records that the user explicitly requested the check.

Exit / next state: the result becomes `3.1 No Update Available`, `3.2 Update Available`, or `3.3 Manual Check Failure`.

### 4 Download Update

User entry: the About tab says `Version <version> is available.` and shows `Download <version>`.

User action: the user clicks `Download <version>`.

Visible UI state:

- The available-version status remains tied to the version being downloaded.
- The download action enters a busy state and conflicting update actions are unavailable.
- Progress appears after the download starts.

Client state change: update state changes to downloading and begins reporting downloaded bytes, total size when known, speed, and remaining size when available.

Exit / next state: progress continues through `4.1 Download In Progress`; success moves to `4.1.1 Download Complete`; failure moves to `4.2 Download Failure`.

### 5 Downloaded Update Choice

User entry: the update package has downloaded successfully.

User action: the user chooses whether to install now or later.

Visible UI state:

- Status says `Version <version> downloaded.`
- `Install and Relaunch` is visible as the primary install action.
- The user can also leave Settings or keep working without installing immediately.

Client state change: update state is downloaded and stable until the user explicitly starts install.

Exit / next state: install later moves to `5.1 Install Later`; installing now moves to `5.2 Install Chosen`.

### 6 Stable End State

User entry: the user has reached a resting update state or leaves the About tab.

User action: the user returns to the app, retries later, keeps working, or waits for relaunch after install is chosen.

Visible UI state:

- Up-to-date state shows latest-version feedback and last-check style status.
- Available state keeps the download action visible.
- Downloaded state keeps install available.
- Disabled or unavailable state explains that update actions are not currently available.
- Error state shows a retryable failure.

Client state change: the client rests with the latest known update and settings state.

Exit / next state: the journey ends unless the user starts another Journey 16 route.

## Branch Journeys

### 1.1 Main UI Update-Ready Notice

Trigger: an update is already downloaded while the user is on the main Chat or Code surface.

Visible UI state:

- A persistent notice says `Version <version> is ready to install.`
- The description says `Restart NoraHarness to finish the update.`
- A `Restart` action is available.
- The normal Chat or Code surface remains usable behind the notice.

Allowed user actions: click `Restart`, dismiss or ignore the notice if available, keep working, or open Settings.

Client state change: downloaded update state is surfaced outside Settings without blocking the current main app surface.

Recovery / next state: `Restart` moves to `5.2 Install Chosen`; opening Settings moves to `1.1.1 Main UI Notice To Settings`; deferring moves to `5.1 Install Later`.

Blocks progress: no, until the user explicitly chooses restart/install.

### 1.1.1 Main UI Notice To Settings

Trigger: the user responds to the main update-ready state by opening Settings About.

Visible UI state:

- Settings opens with `About` selected.
- The update status still says the downloaded version is ready.
- `Install and Relaunch` is visible.

Allowed user actions: install and relaunch, leave Settings, or keep the update ready for later.

Client state change: the main update-ready notice and Settings About state remain aligned to the same downloaded update.

Recovery / next state: continue to `5 Downloaded Update Choice`.

Blocks progress: no.

### 3.1 No Update Available

Trigger: the manual update check completes and the app is already current.

Visible UI state:

- A success toast says `You are on the latest version.`
- The status returns to ready/last-checked language.
- `Check for Updates` is enabled again.
- No download action is shown.

Allowed user actions: check again, leave Settings, toggle auto-update, or open links.

Client state change: update state returns to idle with a last checked time.

Recovery / next state: move to `6 Stable End State` or return to `3 Manual Update Check`.

Blocks progress: no.

### 3.2 Update Available

Trigger: the manual update check finds an available version.

Visible UI state:

- The status says `Version <version> is available.`
- `Download <version>` appears.
- `Check for Updates` is enabled when no operation is running.

Allowed user actions: download, check again when allowed, leave Settings, or keep working without downloading.

Client state change: update state records the available version and enables the download action.

Recovery / next state: clicking download moves to `4 Download Update`; waiting or leaving moves to `6 Stable End State`.

Blocks progress: no.

### 4.1 Download In Progress

Trigger: the user starts downloading an available update.

Visible UI state:

- The update status shows progress for the selected version.
- If total size is known, the message includes percent and remaining size.
- If speed is not known yet, the status can show calculating speed.
- Conflicting update actions are disabled while downloading.

Allowed user actions: wait, leave Settings if the app permits it, or continue using non-blocked app surfaces.

Client state change: update state sends progress updates and smooths visible speed as more data arrives.

Recovery / next state: successful completion moves to `4.1.1 Download Complete`; failure moves to `4.2 Download Failure`.

Blocks progress: temporarily for update actions that conflict with the download.

### 4.1.1 Download Complete

Trigger: the update package downloads and verifies successfully.

Visible UI state:

- A success toast says `Update package downloaded.`
- The status says `Version <version> downloaded.`
- `Install and Relaunch` appears.

Allowed user actions: install and relaunch, leave Settings, keep working, or respond to the main UI update-ready notice.

Client state change: update state changes to downloaded and records that the package is ready to install.

Recovery / next state: move to `5 Downloaded Update Choice`.

Blocks progress: no.

### 5.1 Install Later

Trigger: the update is downloaded and the user does not choose install/relaunch now.

Visible UI state:

- Settings About keeps showing the downloaded status and install action.
- The main app can continue to show an update-ready notice.
- Chat and Code remain usable.

Allowed user actions: return later to install, click restart from the main notice, leave Settings, or keep working.

Client state change: update state stays downloaded.

Recovery / next state: return to `6 Stable End State`, or later move to `5.2 Install Chosen`.

Blocks progress: no.

### 5.2 Install Chosen

Trigger: the user clicks `Install and Relaunch` in Settings or `Restart` from the main update-ready notice.

Visible UI state:

- The status changes to installing and relaunching.
- Install controls become busy or unavailable.
- The user expects the current app session to close.

Allowed user actions: wait for the app to quit and relaunch.

Client state change: update state changes to installing and the desktop app starts the relaunch path.

Recovery / next state: continue to `5.2.1 Installing And Relaunching`.

Blocks progress: yes, because the current app session is intentionally leaving.

### 5.2.1 Installing And Relaunching

Trigger: the install path has started.

Visible UI state:

- The app is closing or has closed.
- Relaunch is expected after installation completes.

Allowed user actions: wait for relaunch.

Client state change: the current running app exits so installation can finish and the updated app can reopen.

Recovery / next state: app restart.

Blocks progress: yes, by explicit user choice.

## Detail States

### 2.1 App Identity And Version

Trigger: the About tab opens and app identity is displayed.

Visible UI state:

- The fallback app name and version are visible immediately.
- When build info resolves, the display updates to the resolved app name and display version.
- License text remains visible at the bottom of About.

Allowed user actions: read the version, open links, toggle auto-update, check for updates, or leave Settings.

Client state change: resolved build identity replaces fallback identity when available.

Recovery / next state: return to `2 About Tab Content`.

Blocks progress: no.

### 2.2 Auto-Update Changed

Trigger: the user toggles Auto-update and the setting saves successfully.

Visible UI state:

- The switch changes to the selected state.
- A success toast confirms whether auto-update is enabled or disabled.
- The About tab remains open.

Allowed user actions: toggle again, check for updates manually, open links, or leave Settings.

Client state change: the saved setting is updated. Automatic checks are started or stopped according to the saved value.

Recovery / next state: return to `2 About Tab Content`.

Blocks progress: no.

### 2.5 External Project Links

Trigger: the user clicks `GitHub`, `Website`, or `Documentation`.

Visible UI state:

- The selected link opens externally.
- The Settings About tab remains open.
- Update status and version information remain unchanged.

Allowed user actions: return from the external destination, click another link, check for updates, or leave Settings.

Client state change: the client requests external navigation and preserves About state.

Recovery / next state: return to `2 About Tab Content`.

Blocks progress: no.

## State Language

### 6.2 Update Status Labels

Applies to: `2 About Tab Content`, `3 Manual Update Check`, `4 Download Update`, `5 Downloaded Update Choice`, `6 Stable End State`, and the main UI update-ready notice.

Visible status language:

- Unavailable: update state could not be loaded yet.
- Disabled: updates are not supported or not active in this app state.
- Ready to check: the user can start a manual check.
- Checking: the app is fetching update availability.
- Available: a specific version can be downloaded.
- Downloading: progress, speed, and remaining size may be visible.
- Downloaded: the package is ready to install.
- Installing/relaunching: the app is leaving the current session to apply the update.
- Up to date: the latest version is already installed.
- Error: check, download, install, or settings update failed and can be retried when appropriate.
- Later/restart: the user can defer installation or explicitly restart to finish.

Client state change: visible status copy, button availability, toasts, and main UI notice stay aligned with the same update state.

Exit / next state: return to the active Journey 16 state.

## Errors And Recovery

### 2.3 Auto-Update Save Failure

Trigger: the user toggles Auto-update and the setting fails to save.

Visible UI state:

- The switch returns to the previous state.
- An error toast says the setting failed to update.
- Other About controls remain available.

Allowed user actions: retry the toggle, check for updates manually, open links, or leave Settings.

Client state change: the client restores the previous auto-update setting and does not apply the failed change.

Recovery / next state: return to `2 About Tab Content`.

Blocks progress: no.

### 2.4 Update Status Unavailable Or Disabled

Trigger: the update state cannot be loaded, or this build/platform does not support updates.

Visible UI state:

- The status area says `Update status unavailable.` or shows the disabled reason.
- Download and install actions are not shown.
- Manual check may be unavailable or may return an explicit unsupported-state error.

Allowed user actions: leave Settings, continue using the app, or retry if a manual check remains available.

Client state change: the client keeps update actions hidden unless a supported update state becomes available.

Recovery / next state: return to `2 About Tab Content` if update state becomes available, or rest at `6 Stable End State`.

Blocks progress: no for normal app use; yes for update actions that are unsupported or unavailable.

### 3.3 Manual Check Failure

Trigger: the manual update check fails.

Visible UI state:

- An error toast shows the failure reason.
- The About status area shows an error message.
- `Check for Updates` can be used again when no operation is running.

Allowed user actions: retry, leave Settings, keep working, or open links.

Client state change: update state changes to error and keeps the failure explicit and retryable.

Recovery / next state: retry moves to `3.3.1 Retry Manual Check`; leaving moves to `6 Stable End State`.

Blocks progress: yes for completing the update check; no for normal app use.

### 3.3.1 Retry Manual Check

Trigger: the user clicks `Check for Updates` after a previous check error.

Visible UI state:

- The status returns to checking.
- The button says `Checking...`.
- Error copy is replaced by the active checking state while the retry runs.

Allowed user actions: wait for the retry to resolve or leave Settings if they do not need the result.

Client state change: update state leaves error and starts another explicit check.

Recovery / next state: retry resolves as `3.1 No Update Available`, `3.2 Update Available`, or `3.3 Manual Check Failure`.

Blocks progress: temporarily for update controls while checking.

### 4.2 Download Failure

Trigger: the update package fails to download or verify.

Visible UI state:

- An error toast shows the failure reason.
- The status area shows an error.
- The package is not presented as ready to install.

Allowed user actions: retry when the available update can be downloaded again, check again, leave Settings, or keep working.

Client state change: update state changes to error and preserves retryability.

Recovery / next state: retry moves to `4.2.1 Retry Download`; leaving moves to `6 Stable End State`.

Blocks progress: yes for update completion; no for normal app use.

### 4.2.1 Retry Download

Trigger: the user retries after a download failure.

Visible UI state:

- The available update can be downloaded again when the UI returns to an available update state.
- Progress appears again after the retry starts.
- Prior error copy is replaced by active download progress.

Allowed user actions: wait for download completion, leave Settings, or retry again after another failure.

Client state change: update state leaves error and starts another download attempt for the available version.

Recovery / next state: progress moves to `4.1 Download In Progress`; success moves through `4.1.1 Download Complete`; another failure returns to `4.2 Download Failure`.

Blocks progress: temporarily for update actions while downloading.

### 6.1 Non-Blocking Update Recovery

Recovery states: `2.3 Auto-Update Save Failure`, `2.4 Update Status Unavailable Or Disabled`, `3.3 Manual Check Failure`, `4.2 Download Failure`, and `5.1 Install Later`.

User-visible recovery:

- Failed setting changes roll back and keep the prior setting active.
- Failed checks and downloads show error feedback and allow retry when no operation is running.
- Disabled or unavailable update state hides actions that cannot be completed.
- Downloaded updates can wait; install/relaunch starts only after explicit user action.
- Main Chat and Code work remains usable unless the user chooses install/restart.

Client state change: update errors, disabled state, downloaded state, and main UI update-ready notice remain separate from the user's active Chat or Code work until install is chosen.

Exit / next state: retry returns to `3 Manual Update Check` or `4 Download Update`; deferral returns to `6 Stable End State`; install leaves through `5.2.1 Installing And Relaunching`.

## Wireframe

The HTML wireframe must use this record as its route source. It keeps six first-level bands in this order: `Main happy path`, `Route map`, `Branch journeys`, `Detail states`, `State language`, and `Errors and recovery`.

The `Main happy path` band shows the Journey 16 main steps `1 -> 2 -> 3 -> 4 -> 5 -> 6` as the primary product chain. Every route in the Route Table appears separately in the `Route map` band as a full-size product wireframe route row. Route rows do not replace the separate Branch, Detail, State Language, or Errors and Recovery bands.

The `Detail states`, `State language`, and `Errors and recovery` bands use structured summary, status, and copy cards with the matching record IDs. They should not be drawn as route rows or collapsed into one support area.
