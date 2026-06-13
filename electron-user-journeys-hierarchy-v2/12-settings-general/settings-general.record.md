# Journey 12: Settings - General

This record describes the implemented NoraHarness Electron journey. It is the text source for the matching HTML wireframe and keeps the journey IDs aligned with visible wireframe cards.

## Journey Scope

User entry: the user opens Settings from the app settings entry and lands on the General tab.

User goal: the user checks Gateway status, restarts Gateway if needed, configures HTTP Proxy, and adjusts general app behavior such as Launch at Login, Dock Icon, Enable debug tools, and Default Thinking Level.

End state: General settings are saved, applied to the app surface where relevant, or left in a visible retryable error state. Current Gateway state remains visible in Settings.

Implementation maturity: Implemented.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches and recovery cases use decimal IDs scoped to the step where the state starts.

State inventory:

Settings entry and main tab load:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 12 | Settings entry from the main app. | Opens Settings from the app settings entry. | The Settings frame replaces the app work surface, with `Back to app` and the left navigation visible. | The app records that Settings is open while preserving the previous work context behind it. | `2` |
| `2` | Main path | Journey 12 | General tab begins loading. | Waits for the General tab to settle or scans the Settings frame. | `General` is highlighted in the left navigation; the content area shows the General heading and starts loading saved settings and Gateway state. | The client starts reading saved General settings and the latest Gateway health. | `2.2` |
| `2.1` | Branch journey | `2 General Tab Load` | Settings was opened with an invalid or unavailable requested tab. | Opens Settings through a route that cannot resolve to a valid tab. | Settings still opens safely with `General` highlighted. | The client falls back to the General tab instead of leaving Settings blank. | `2` |
| `2.2` | Branch follow-up | `2 General Tab Load` | General settings have loaded enough to inspect. | Reviews the loaded General surface. | The page shows `Gateway Status`, `Network`, `Features`, and `Default Thinking Level`; saved values populate their controls. | Saved General settings and Gateway health are reflected in the visible controls. | `3` or `4` |

Gateway status and restart states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3` | Main path | Journey 12 | Gateway status review. | Reads the Gateway Status card. | The card shows `Gateway Status`, last check metadata when available, an agent count when available, a status badge, and `Restart`. | Gateway health is represented as a user visible status that can enable or disable restart. | `3.1`, `3.2`, or `4` |
| `3.1` | Branch journey | `3 Gateway Status Review` | Gateway is running. | Continues reviewing settings or chooses another action. | The Gateway badge reads `Running`; `Restart` is available. | Runtime backed work can remain available outside Settings. | `4` or `5` |
| `3.2` | Branch journey | `3 Gateway Status Review` | Gateway is stopped, unavailable, or in an error state. | Reviews the status and decides whether to restart. | The Gateway badge shows a nonrunning or error state; `Restart` remains the primary recovery action when available. | Runtime backed work is treated as degraded until Gateway health recovers. | `3.3` or `5` |
| `3.3` | Branch journey | `3 Gateway Status Review` | Gateway restart is requested. | Clicks `Restart`. | The restart action enters a pending state, and the General tab stays usable instead of showing a full screen blocker. | The client asks Gateway to restart and then refreshes Gateway health. | `3.3.1` or `3.3.2` |
| `3.3.1` | Branch follow-up | `3.3 Gateway Restart Requested` | Gateway restart succeeds. | Waits for the restart to finish. | A success message can appear; the Gateway Status card refreshes and returns to `Running` when health is available. | Gateway health is refreshed and runtime backed work can recover. | `5` |
| `3.3.2` | Branch follow-up | `3.3 Gateway Restart Requested` | Gateway restart fails. | Reads the failure message. | An error message appears while the Gateway Status card remains visible with retry available. | Gateway remains unavailable or unknown, and the failed restart is left retryable. | `6.1` |

Proxy edit and save states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4` | Main path | Journey 12 | General settings change surface. | Chooses a General setting to change. | The user can edit `HTTP Proxy`, toggle feature switches, or change `Default Thinking Level` without leaving the General tab. | The client keeps the current form state separate from saved state until each control finishes saving. | `4.1`, `4.2`, `4.3`, `4.4`, `4.5`, or `5` |
| `4.1` | Branch journey | `4 General Settings Change Surface` | HTTP Proxy value is edited. | Types or clears a proxy URL, then blurs the field or presses Enter. | The `HTTP Proxy` field shows the typed value; the auto detected proxy hint remains below the input when available. | The edited proxy value is trimmed and prepared for save. | `4.1.1`, `4.1.2`, or `4.1.3` |
| `4.1.1` | Branch follow-up | `4.1 HTTP Proxy Edited` | HTTP Proxy save succeeds. | Waits for save feedback or continues. | The field keeps the saved value and returns to a clean state; a success message can appear. | The saved proxy value becomes the app setting for future Gateway requests after the required restart behavior. | `5` |
| `4.1.2` | Branch follow-up | `4.1 HTTP Proxy Edited` | HTTP Proxy saves with a reachability warning. | Reads the warning and decides whether to keep the value. | The field remains saved, but a warning explains that the proxy may not be reachable. | The app keeps the proxy value while exposing the warning as nonblocking feedback. | `5` or `4.1` |
| `4.1.3` | Branch follow-up | `4.1 HTTP Proxy Edited` | HTTP Proxy save fails. | Reads the error. | The typed proxy value remains visible with an error message or failed save feedback. | The previous saved proxy setting remains effective until the user retries successfully. | `6.2` |

Login item, dock, debug, and related controls:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.2` | Branch journey | `4 General Settings Change Surface` | Launch at Login is toggled. | Turns `Launch at Login` on or off. | The switch immediately reflects the target state and may be temporarily disabled while saving. | The app attempts to save whether NoraHarness starts after sign in. | `4.2.1` or `4.2.2` |
| `4.2.1` | Branch follow-up | `4.2 Launch At Login Toggle` | Launch at Login save succeeds. | Continues after the switch settles. | The switch remains in the chosen position. | The new launch behavior is saved. | `5` |
| `4.2.2` | Branch follow-up | `4.2 Launch At Login Toggle` | Launch at Login save fails. | Reads the failed save feedback. | The switch rolls back or shows a failed state so the user can retry. | The previous launch behavior remains effective. | `6.3` |
| `4.3` | Branch journey | `4 General Settings Change Surface` | Dock Icon is toggled. | Turns `Dock Icon` on or off. | The switch immediately reflects the target state and may be temporarily disabled while saving. | The app attempts to save whether NoraHarness remains visible in the Dock. | `4.3.1` or `4.3.2` |
| `4.3.1` | Branch follow-up | `4.3 Dock Icon Toggle` | Dock Icon save succeeds. | Continues after the switch settles. | The switch remains in the chosen position. | The new Dock preference is saved and will affect the app presentation after Settings closes or the app refreshes that surface. | `5` |
| `4.3.2` | Branch follow-up | `4.3 Dock Icon Toggle` | Dock Icon save fails. | Reads the failed save feedback. | The switch rolls back or shows a failed state so the user can retry. | The previous Dock preference remains effective. | `6.3` |
| `4.4` | Branch journey | `4 General Settings Change Surface` | Enable debug tools is toggled. | Turns `Enable debug tools` on or off. | The switch immediately reflects the target state and may be temporarily disabled while saving. | The app attempts to save whether developer oriented controls are visible. | `4.4.1` or `4.4.3` |
| `4.4.1` | Branch follow-up | `4.4 Enable Debug Tools Toggle` | Debug tools are enabled and the Developer section appears. | Reviews the newly revealed Developer section. | A `Developer` area appears with `Reset Onboarding`. | Debug controls become visible inside General. | `4.4.2` or `5` |
| `4.4.2` | Branch follow-up | `4.4.1 Debug Tools Enabled` | Reset Onboarding is used. | Clicks `Reset Onboarding`. | The Developer section remains visible and reset feedback can appear. | Onboarding completion is cleared for a later first launch path. | `5` |
| `4.4.3` | Branch follow-up | `4.4 Enable Debug Tools Toggle` | Enable debug tools save fails. | Reads the failed save feedback. | The switch rolls back or shows a failed state; the Developer section follows the effective saved state. | The previous debug tools preference remains effective. | `6.3` |
| `4.5` | Branch journey | `4 General Settings Change Surface` | Default Thinking Level is changed. | Opens the selector and chooses a level such as `off`, `minimal`, `low`, `medium`, `high`, `xhigh`, or `adaptive`. | The selector shows the chosen level while saving is attempted. | The app attempts to save the default thinking level for new conversations. | `4.5.1` or `4.5.2` |
| `4.5.1` | Branch follow-up | `4.5 Default Thinking Level Change` | Default Thinking Level save succeeds. | Continues after the selector settles. | The selector remains on the chosen level. | New conversations use the saved default thinking level. | `5` |
| `4.5.2` | Branch follow-up | `4.5 Default Thinking Level Change` | Default Thinking Level save fails. | Reads the failed save feedback. | The selector returns to the previous effective value or shows retryable failure feedback. | The previous default thinking level remains effective. | `6.4` |

Close and apply behavior:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `5` | Main path | Journey 12 | User leaves General Settings. | Clicks `Back to app`, closes Settings, or stops making changes. | Settings closes or remains visible if the user chooses to keep working. | Any successfully saved setting is ready to affect the app where that setting is visible. | `5.1`, `5.2`, or `5.3` |
| `5.1` | Branch journey | `5 Close And Apply` | Settings closes after clean saves or no changes. | Returns to the app. | The previous Chat or Code context is visible again. | Saved General settings remain effective; no unresolved error state follows the user. | `6` |
| `5.2` | Branch journey | `5 Close And Apply` | Settings closes or pauses with retryable errors still present. | Leaves the failed state as is or returns to the app after reading it. | The last visible General state includes an error, warning, or retry action before leaving. | Failed settings do not replace their previous effective values. | `6` |
| `5.3` | Branch journey | `5 Close And Apply` | App presentation updates after close. | Returns to the app after changing Dock, debug, or related visible app behavior. | The main app reflects saved presentation preferences where they are visible. | Saved app behavior is applied outside Settings. | `6` |

Detail states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.3` | Detail state | `2 General Tab Load` | Settings frame, left navigation, and General selected state. | Scans the Settings frame or chooses another tab. | `Back to app` remains visible; `General` is highlighted among the Settings tabs. | The active Settings tab remains General unless the user selects another tab. | `3` or another Settings journey |
| `3.4` | Detail state | `3 Gateway Status Review` | Gateway Status card layout and restart affordance. | Reads last check, agent count, badge, and restart control. | The Gateway Status card shows status metadata, a badge, and `Restart` in one row. | Gateway health controls status language and restart availability. | `3.1`, `3.2`, or `3.3` |
| `4.6` | Detail state | `4 General Settings Change Surface` | Network proxy field behavior. | Edits, clears, saves, or retries `HTTP Proxy`. | The proxy input, auto-detected hint, save feedback, warning, or error appear in the Network section. | The effective proxy changes only after a successful save. | `4.1`, `4.1.1`, `4.1.2`, or `4.1.3` |
| `4.7` | Detail state | `4 General Settings Change Surface` | Feature switch behavior. | Toggles menu bar animation, Launch at Login, Dock Icon, Allow Canvas, or Enable debug tools. | Each switch shows target, saving, saved, rollback, or failed state inside the Features panel. | Each preference saves independently and failed saves restore the previous effective value. | `4.2`, `4.3`, `4.4`, or `6.3` |
| `4.8` | Detail state | `4.4.1 Debug Tools Enabled` | Developer section behavior. | Reviews or uses `Reset Onboarding`. | The Developer section appears only when debug tools are enabled and contains `Reset Onboarding`. | Developer controls become available only while the debug preference is effective. | `4.4.2` or `5` |
| `5.4` | Detail state | `5 Close And Apply` | Return-to-app and applied presentation behavior. | Clicks `Back to app`, closes Settings, or leaves a retryable error visible. | The previous app context returns; saved presentation settings can become visible outside Settings. | Saved settings apply outside Settings while failed settings remain retryable. | `5.1`, `5.2`, `5.3`, or `6` |

State-language states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Shared Settings labels and actions. | Reads Settings navigation and action labels. | Labels include `Back to app`, `General`, `Gateway Status`, `Network`, `Features`, `Developer`, `Restart`, and `Reset Onboarding`. | The Settings surface keeps navigation and action language stable across states. | Any state |
| `3.5` | State language | `3 Gateway Status Review` | Gateway health labels. | Reads the Gateway badge or restart feedback. | Gateway language includes `Loading`, `Running`, `Stopped`, `Restarting`, `Error`, `Restart`, and retry feedback. | Gateway status language follows current health and restart state. | `3.1`, `3.2`, `3.3`, `3.3.1`, or `3.3.2` |
| `4.9` | State language | `4 General Settings Change Surface` | Save feedback labels. | Reads save feedback near the changed control. | Save language includes `Saving`, `Saved`, `Warning`, `Failed`, `Retry`, and previous-value rollback. | The visible feedback follows the save state for the individual control. | `4.1`, `4.2`, `4.3`, `4.4`, or `4.5` |
| `4.10` | State language | `4 General Settings Change Surface` | Preference control labels. | Reads or changes General preference controls. | Labels include `HTTP Proxy`, `Launch at Login`, `Dock Icon`, `Allow Canvas`, `Enable debug tools`, and `Default Thinking Level`. | Preference labels stay stable while values, switches, and selectors change. | Any General settings change state |
| `5.5` | State language | `5 Close And Apply` | Close and apply language. | Reads close or return feedback. | Close/apply language includes `Back to app`, clean close, retryable error, and applied presentation feedback. | Return-to-app language reflects whether changes saved cleanly or remain retryable. | `5.1`, `5.2`, `5.3`, or `6` |

Recovery and error states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `6` | End state | Journey 12 | Stable end state for General Settings. | Continues work, retries later, or reopens Settings. | General settings are saved, applied, or left in a clear retryable state; Gateway status remains visible when Settings is open. | The app rests with the latest successful settings and any failed setting still recoverable. | End |
| `6.1` | Recovery summary | `3.3.2 Gateway Restart Fails` | Gateway restart error recovery. | Clicks `Restart` again, waits, or returns to the app with degraded runtime behavior. | Error feedback stays near the Gateway Status card; the restart action remains discoverable when available. | Gateway remains unavailable or unknown until a later successful health refresh. | `3.3` or `5` |
| `6.2` | Recovery summary | `4.1.3 HTTP Proxy Save Fails` | HTTP Proxy save error recovery. | Corrects the proxy value, clears it, or retries save. | The proxy field preserves the user's latest input and shows failed save feedback. | The previous saved proxy stays effective until a later successful save. | `4.1` |
| `6.3` | Recovery summary | Toggle save failure | Launch at Login, Dock Icon, or Enable debug tools failed to save. | Retries the switch or leaves it at the restored value. | The failed switch returns to its previous effective state or shows retryable feedback. | The previous saved preference remains effective. | `4.2`, `4.3`, or `4.4` |
| `6.4` | Recovery summary | `4.5.2 Default Thinking Level Save Fails` | Default Thinking Level save error recovery. | Selects the level again or keeps the restored value. | The selector shows the effective previous value or retryable failure feedback. | The previous default thinking level remains effective for new conversations. | `4.5` |

Route table:

| Route | Composition | Result / next state |
| --- | --- | --- |
| Review General with Gateway running | `1 -> 2 -> 2.2 -> 3 -> 3.1 -> 5 -> 5.1 -> 6` | The user reviews General settings and returns to the app without changing anything. |
| Invalid tab falls back to General | `1 -> 2.1 -> 2 -> 2.2 -> 3 -> 3.1 -> 5 -> 5.1 -> 6` | Settings opens safely on General even when the requested tab cannot be used. |
| Gateway restart succeeds | `1 -> 2 -> 2.2 -> 3 -> 3.2 -> 3.3 -> 3.3.1 -> 5 -> 5.1 -> 6` | The user restarts Gateway, sees health refresh, and leaves Settings cleanly. |
| Gateway restart fails and remains retryable | `1 -> 2 -> 2.2 -> 3 -> 3.2 -> 3.3 -> 3.3.2 -> 6.1 -> 5 -> 5.2 -> 6` | The Gateway problem remains visible and retryable, while the user can still return to the app. |
| HTTP Proxy save succeeds | `1 -> 2 -> 2.2 -> 4 -> 4.1 -> 4.1.1 -> 5 -> 5.1 -> 6` | The proxy value is saved and the General tab returns to a clean state. |
| HTTP Proxy saves with warning | `1 -> 2 -> 2.2 -> 4 -> 4.1 -> 4.1.2 -> 5 -> 5.1 -> 6` | The proxy value is kept, while the user has seen nonblocking reachability feedback. |
| HTTP Proxy save fails then succeeds after correction | `1 -> 2 -> 2.2 -> 4 -> 4.1 -> 4.1.3 -> 6.2 -> 4.1 -> 4.1.1 -> 5 -> 5.1 -> 6` | The failed input stays editable until the user corrects it and saves successfully. |
| Launch at Login toggle succeeds | `1 -> 2 -> 2.2 -> 4 -> 4.2 -> 4.2.1 -> 5 -> 5.1 -> 6` | The app records whether NoraHarness should start after sign in. |
| Launch at Login toggle fails then retries | `1 -> 2 -> 2.2 -> 4 -> 4.2 -> 4.2.2 -> 6.3 -> 4.2 -> 4.2.1 -> 5 -> 5.1 -> 6` | The switch recovers from failure by returning to the previous value, then saving on retry. |
| Dock Icon toggle applies after close | `1 -> 2 -> 2.2 -> 4 -> 4.3 -> 4.3.1 -> 5 -> 5.3 -> 6` | The Dock preference is saved and affects app presentation after Settings closes or refreshes that surface. |
| Dock Icon toggle fails then retries | `1 -> 2 -> 2.2 -> 4 -> 4.3 -> 4.3.2 -> 6.3 -> 4.3 -> 4.3.1 -> 5 -> 5.3 -> 6` | The Dock preference remains recoverable when saving fails. |
| Enable debug tools reveals Developer section | `1 -> 2 -> 2.2 -> 4 -> 4.4 -> 4.4.1 -> 5 -> 5.1 -> 6` | The Developer section becomes visible after debug tools are enabled. |
| Reset Onboarding from Developer section | `1 -> 2 -> 2.2 -> 4 -> 4.4 -> 4.4.1 -> 4.4.2 -> 5 -> 5.1 -> 6` | Debug mode exposes reset, and the next relevant launch can return to onboarding. |
| Enable debug tools save fails then retries | `1 -> 2 -> 2.2 -> 4 -> 4.4 -> 4.4.3 -> 6.3 -> 4.4 -> 4.4.1 -> 5 -> 5.1 -> 6` | Debug tools remain at their previous value until retry succeeds. |
| Default Thinking Level save succeeds | `1 -> 2 -> 2.2 -> 4 -> 4.5 -> 4.5.1 -> 5 -> 5.1 -> 6` | The selected thinking level becomes the default for new conversations. |
| Default Thinking Level save fails then retries | `1 -> 2 -> 2.2 -> 4 -> 4.5 -> 4.5.2 -> 6.4 -> 4.5 -> 4.5.1 -> 5 -> 5.1 -> 6` | The selector preserves the previous effective default until the save succeeds. |

## Main Path

### 1 Settings Entry

User entry: the user is in the main app and opens Settings from the app settings entry.

User action: the user chooses Settings.

Visible UI state:

- The app switches to the full Settings frame.
- `Back to app` appears in the left rail.
- Settings navigation is visible, with `General`, `Workspace`, `Providers`, `Extensions`, `Permissions`, `Voice`, `Channels`, `Advanced`, and `About`.

Client state change: the app opens Settings while retaining the previous Chat or Code context for return.

Exit / next state: Settings opens the General tab in `2 General Tab Load`.

### 2 General Tab Load

User entry: the Settings frame is open and General is the selected tab or fallback tab.

User action: the user waits for the General page to populate.

Visible UI state:

- `General` is highlighted in the left navigation.
- The content heading reads `General`.
- Supporting copy reads `App-level settings and gateway status.`
- The General content area begins resolving Gateway status and saved settings.

Client state change: saved app settings and Gateway health begin loading into the General controls.

Exit / next state: loaded General content moves to `2.2 General Settings Loaded`, then the user can review Gateway in `3 Gateway Status Review` or change settings in `4 General Settings Change Surface`.

### 3 Gateway Status Review

User entry: the General tab is visible and the Gateway Status card has enough data to show a state.

User action: the user reads the card, checks the status badge, and optionally uses `Restart`.

Visible UI state:

- The `Gateway Status` card is near the top of General.
- The card shows last check metadata when available.
- A status badge such as `Running` appears.
- `Restart` is available when the card can request a restart.

Client state change: Gateway health is reflected in the status badge and in whether restart is available.

Exit / next state: running Gateway moves through `3.1 Gateway Running`; stopped or errored Gateway moves through `3.2 Gateway Stopped Or Error`; the user can also proceed to settings changes in `4 General Settings Change Surface`.

### 4 General Settings Change Surface

User entry: the user is on General and chooses to change a setting.

User action: the user edits `HTTP Proxy`, changes a feature switch, changes `Default Thinking Level`, or reveals developer controls.

Visible UI state:

- `Network` contains `HTTP Proxy` and an auto detected proxy hint when available.
- `Features` contains switches for `Play menu bar icon animations`, `Launch at Login`, `Dock Icon`, `Allow Canvas`, and `Enable debug tools`.
- `Default Thinking Level` appears as a selector with the current level.
- The Developer section appears only when debug tools are enabled.

Client state change: each control saves independently and either keeps the new value or returns to its previous effective value on failure.

Exit / next state: each changed control routes through its matching branch, then returns to `5 Close And Apply` or remains in a recovery state.

### 5 Close And Apply

User entry: the user has finished reviewing or changing General settings.

User action: the user clicks `Back to app`, closes Settings, or pauses on the General tab.

Visible UI state:

- Clean settings can close immediately back to the previous app context.
- Retryable errors remain visible in General until the user leaves or retries.
- Saved presentation settings can affect the app after the user returns.

Client state change: successful settings remain saved; failed settings do not replace their previous effective values.

Exit / next state: clean close goes to `5.1 Settings Close Clean`; retryable close or pause goes to `5.2 Close With Retryable Error`; app presentation changes go to `5.3 Presentation Settings Applied`.

### 6 Stable End State

User entry: the user has returned to the app, paused in General, or left a retryable General setting visible.

User action: the user continues work, retries later, or reopens Settings.

Visible UI state:

- Saved settings remain visible the next time General opens.
- Retryable errors remain understandable and correctable.
- Gateway status remains available inside General.

Client state change: the app rests with the latest successful General settings and any failed setting still recoverable.

Exit / next state: end.

## Branch Journeys

### 2.1 Invalid Requested Tab Fallback

Trigger: Settings is opened with a requested tab that cannot be matched to a visible Settings tab.

Visible UI state: the Settings frame opens with `General` highlighted instead of leaving the content blank.

Allowed user actions: use General, choose another tab from the left navigation, or click `Back to app`.

Client state change: the app chooses General as the safe visible tab.

Recovery / next state: continue through `2 General Tab Load`.

Blocks progress: no.

### 2.2 General Settings Loaded

Trigger: General has finished loading enough saved settings and Gateway state to show the controls.

Visible UI state:

- Gateway Status, Network, Features, and Default Thinking Level are visible.
- Saved switch values and proxy value appear in their controls.
- Auto detected proxy text appears when available.

Allowed user actions: review Gateway, restart Gateway, edit proxy, toggle switches, change thinking level, switch tabs, or return to the app.

Client state change: saved settings and Gateway health are reflected in the General controls.

Recovery / next state: continue to `3 Gateway Status Review`, `4 General Settings Change Surface`, or `5 Close And Apply`.

Blocks progress: no.

### 3.1 Gateway Running

Trigger: Gateway health resolves as running.

Visible UI state:

- The Gateway Status badge reads `Running`.
- Last check metadata and agent count can appear.
- `Restart` remains available.

Allowed user actions: continue reviewing settings, restart Gateway anyway, edit another General setting, switch tabs, or return to the app.

Client state change: runtime backed app behavior can remain available outside Settings.

Recovery / next state: continue to `4 General Settings Change Surface` or `5 Close And Apply`.

Blocks progress: no.

### 3.2 Gateway Stopped Or Error

Trigger: Gateway health resolves as stopped, unavailable, unknown, or errored.

Visible UI state:

- The Gateway Status card remains visible.
- The status badge communicates the nonrunning state.
- Restart or retry behavior remains near the status.

Allowed user actions: click `Restart`, wait, switch tabs, change settings that do not depend on Gateway, or return to the app.

Client state change: runtime backed work is treated as degraded until Gateway health recovers.

Recovery / next state: restart moves to `3.3 Gateway Restart Requested`; leaving without restart moves to `5 Close And Apply`.

Blocks progress: yes for runtime backed work, no for the General settings frame itself.

### 3.3 Gateway Restart Requested

Trigger: the user clicks `Restart` from the Gateway Status card.

Visible UI state:

- The restart action shows a pending or disabled state while the request runs.
- The Settings frame and left navigation remain available.
- Gateway status is refreshed after the request completes.

Allowed user actions: wait, read status, or use nonblocked Settings navigation.

Client state change: the app requests a Gateway restart and then checks Gateway health again.

Recovery / next state: success moves to `3.3.1 Gateway Restart Succeeds`; failure moves to `3.3.2 Gateway Restart Fails`.

Blocks progress: temporarily for Gateway dependent work.

### 3.3.1 Gateway Restart Succeeds

Trigger: Gateway restart completes successfully and health refreshes.

Visible UI state: the Gateway Status card updates toward `Running`, and a success message can appear.

Allowed user actions: continue changing settings, restart again, switch tabs, or return to the app.

Client state change: Gateway health is refreshed and runtime backed behavior can recover.

Recovery / next state: continue to `5 Close And Apply` or `4 General Settings Change Surface`.

Blocks progress: no.

### 3.3.2 Gateway Restart Fails

Trigger: the restart request fails or health does not recover.

Visible UI state: an error message appears while the Gateway Status card and restart action remain discoverable.

Allowed user actions: retry restart, wait and refresh later, change non Gateway settings, switch tabs, or return to the app.

Client state change: Gateway remains unavailable or unknown, and the failed restart is left retryable.

Recovery / next state: retry through `6.1 Gateway Restart Error Recovery` or return through `5 Close And Apply`.

Blocks progress: yes for Gateway dependent work.

### 4.1 HTTP Proxy Edited

Trigger: the user edits the `HTTP Proxy` input and blurs the field or presses Enter.

Visible UI state:

- The typed proxy value remains visible in the field.
- The auto detected proxy hint remains visible when available.
- Save feedback can appear near the Network section.

Allowed user actions: wait for save, edit again, clear the field, switch tabs, or return to the app after save resolves.

Client state change: the app prepares the trimmed proxy value for saving.

Recovery / next state: success moves to `4.1.1 HTTP Proxy Save Succeeds`; warning moves to `4.1.2 HTTP Proxy Saves With Warning`; failure moves to `4.1.3 HTTP Proxy Save Fails`.

Blocks progress: temporarily for the proxy field while saving.

### 4.1.1 HTTP Proxy Save Succeeds

Trigger: the edited proxy value saves successfully.

Visible UI state: the input keeps the saved value and no longer appears dirty.

Allowed user actions: keep editing General, restart Gateway if needed, switch tabs, or return to the app.

Client state change: the saved proxy becomes the effective proxy setting for future Gateway requests after the required restart behavior.

Recovery / next state: continue to `5 Close And Apply`.

Blocks progress: no.

### 4.1.2 HTTP Proxy Saves With Warning

Trigger: the proxy value saves, but reachability feedback reports a warning.

Visible UI state: the saved value remains in the input, and nonblocking warning feedback is visible.

Allowed user actions: keep the saved value, edit it again, clear it, restart Gateway if needed, or return to the app.

Client state change: the app keeps the saved value while preserving the warning for the user's next action.

Recovery / next state: continue to `5 Close And Apply` or return to `4.1 HTTP Proxy Edited`.

Blocks progress: no.

### 4.1.3 HTTP Proxy Save Fails

Trigger: the proxy value cannot be saved.

Visible UI state: the user's latest input remains visible with failed save feedback.

Allowed user actions: correct the value, clear it, retry save, switch tabs, or return to the app.

Client state change: the previous saved proxy remains effective.

Recovery / next state: continue to `6.2 HTTP Proxy Save Error Recovery`.

Blocks progress: yes for saving the new proxy value.

### 4.2 Launch At Login Toggle

Trigger: the user toggles `Launch at Login`.

Visible UI state: the switch moves to the target state and may be disabled while saving.

Allowed user actions: wait for save, toggle again after save settles, change another setting, or return to the app.

Client state change: the app attempts to save whether NoraHarness starts after sign in.

Recovery / next state: success moves to `4.2.1 Launch At Login Save Succeeds`; failure moves to `4.2.2 Launch At Login Save Fails`.

Blocks progress: temporarily for this switch while saving.

### 4.2.1 Launch At Login Save Succeeds

Trigger: Launch at Login saves successfully.

Visible UI state: the switch remains in the selected state.

Allowed user actions: continue changing settings or return to the app.

Client state change: the selected launch behavior is saved.

Recovery / next state: continue to `5 Close And Apply`.

Blocks progress: no.

### 4.2.2 Launch At Login Save Fails

Trigger: Launch at Login cannot be saved.

Visible UI state: the switch rolls back or shows failed save feedback.

Allowed user actions: retry the switch, leave it restored, change another setting, or return to the app.

Client state change: the previous launch behavior remains effective.

Recovery / next state: continue to `6.3 Toggle Save Error Recovery`.

Blocks progress: yes for changing this preference.

### 4.3 Dock Icon Toggle

Trigger: the user toggles `Dock Icon`.

Visible UI state: the switch moves to the target state and may be disabled while saving.

Allowed user actions: wait for save, toggle again after save settles, change another setting, or return to the app.

Client state change: the app attempts to save whether NoraHarness stays visible in the Dock.

Recovery / next state: success moves to `4.3.1 Dock Icon Save Succeeds`; failure moves to `4.3.2 Dock Icon Save Fails`.

Blocks progress: temporarily for this switch while saving.

### 4.3.1 Dock Icon Save Succeeds

Trigger: Dock Icon saves successfully.

Visible UI state: the switch remains in the selected state.

Allowed user actions: continue changing settings or return to the app.

Client state change: the Dock preference is saved and can affect app presentation outside Settings.

Recovery / next state: continue to `5 Close And Apply`.

Blocks progress: no.

### 4.3.2 Dock Icon Save Fails

Trigger: Dock Icon cannot be saved.

Visible UI state: the switch rolls back or shows failed save feedback.

Allowed user actions: retry the switch, leave it restored, change another setting, or return to the app.

Client state change: the previous Dock preference remains effective.

Recovery / next state: continue to `6.3 Toggle Save Error Recovery`.

Blocks progress: yes for changing this preference.

### 4.4 Enable Debug Tools Toggle

Trigger: the user toggles `Enable debug tools`.

Visible UI state: the switch moves to the target state and may be disabled while saving.

Allowed user actions: wait for save, toggle again after save settles, change another setting, or return to the app.

Client state change: the app attempts to save whether developer oriented controls are visible.

Recovery / next state: success moves to `4.4.1 Debug Tools Enabled`; failure moves to `4.4.3 Enable Debug Tools Save Fails`.

Blocks progress: temporarily for this switch while saving.

### 4.4.1 Debug Tools Enabled

Trigger: Enable debug tools is saved on.

Visible UI state: a `Developer` section appears, including `Reset Onboarding`.

Allowed user actions: use `Reset Onboarding`, keep changing settings, turn debug tools off, or return to the app.

Client state change: developer oriented controls become visible in General.

Recovery / next state: reset moves to `4.4.2 Reset Onboarding Used`; otherwise continue to `5 Close And Apply`.

Blocks progress: no.

### 4.4.2 Reset Onboarding Used

Trigger: the user clicks `Reset Onboarding` in the Developer section.

Visible UI state: reset feedback can appear while the Developer section remains visible.

Allowed user actions: return to the app, continue editing settings, or leave Settings.

Client state change: onboarding completion is cleared for a later first launch path.

Recovery / next state: continue to `5 Close And Apply`.

Blocks progress: no for Settings; it changes a later launch path.

### 4.4.3 Enable Debug Tools Save Fails

Trigger: Enable debug tools cannot be saved.

Visible UI state: the switch rolls back or shows failed save feedback, and the Developer section follows the effective saved state.

Allowed user actions: retry the switch, leave it restored, change another setting, or return to the app.

Client state change: the previous debug tools preference remains effective.

Recovery / next state: continue to `6.3 Toggle Save Error Recovery`.

Blocks progress: yes for changing this preference.

### 4.5 Default Thinking Level Change

Trigger: the user chooses a different `Default Thinking Level`.

Visible UI state: the selector shows the target level while save feedback resolves.

Allowed user actions: wait for save, choose another level after save settles, edit another setting, or return to the app.

Client state change: the app attempts to save the selected level for new conversations.

Recovery / next state: success moves to `4.5.1 Default Thinking Level Save Succeeds`; failure moves to `4.5.2 Default Thinking Level Save Fails`.

Blocks progress: temporarily for this selector while saving.

### 4.5.1 Default Thinking Level Save Succeeds

Trigger: the selected thinking level saves successfully.

Visible UI state: the selector remains on the selected level.

Allowed user actions: continue changing settings or return to the app.

Client state change: new conversations use the saved default thinking level.

Recovery / next state: continue to `5 Close And Apply`.

Blocks progress: no.

### 4.5.2 Default Thinking Level Save Fails

Trigger: the selected thinking level cannot be saved.

Visible UI state: the selector returns to the previous effective value or shows retryable failed save feedback.

Allowed user actions: choose again, keep the restored value, change another setting, or return to the app.

Client state change: the previous default thinking level remains effective.

Recovery / next state: continue to `6.4 Default Thinking Level Save Error Recovery`.

Blocks progress: yes for changing this default.

### 5.1 Settings Close Clean

Trigger: the user leaves Settings after clean saves or without changes.

Visible UI state: the previous app context returns.

Allowed user actions: continue Chat or Code work, reopen Settings, or use other app navigation.

Client state change: saved settings remain effective and no unresolved General error follows the user.

Recovery / next state: continue to `6 Stable End State`.

Blocks progress: no.

### 5.2 Close With Retryable Error

Trigger: the user leaves or pauses after a failed restart or failed save remains visible.

Visible UI state: before leaving, General shows the error or warning near the relevant control.

Allowed user actions: retry before leaving, return to the app, or reopen Settings later.

Client state change: failed changes do not replace previous effective settings.

Recovery / next state: continue to `6 Stable End State`.

Blocks progress: no for leaving Settings; yes for the failed setting until retry succeeds.

### 5.3 Presentation Settings Applied

Trigger: the user closes Settings after a saved setting changes app presentation, such as Dock visibility or debug oriented UI.

Visible UI state: the main app reflects saved presentation preferences where they are visible.

Allowed user actions: continue work or reopen Settings to adjust the preference again.

Client state change: saved presentation settings apply outside the General tab.

Recovery / next state: continue to `6 Stable End State`.

Blocks progress: no.

## Detail States

### 2.3 Settings Frame And General Navigation

Trigger: the General tab is loaded or selected inside the Settings frame.

Visible UI state:

- `Back to app` is visible in the left rail.
- `General` is highlighted in the Settings navigation.
- Other Settings tabs remain visible so the user can leave this journey for another Settings area.

Allowed user actions: return to the app, stay on General, or choose another Settings tab.

Client state change: the active Settings tab remains General unless the user selects another tab.

Recovery / next state: continue to `3 Gateway Status Review` or leave for another Settings journey.

Blocks progress: no.

### 3.4 Gateway Status Card Detail

Trigger: the user inspects Gateway status from General.

Visible UI state:

- The `Gateway Status` card shows last check metadata when available.
- Agent count appears when health data includes it.
- The badge communicates current Gateway state.
- `Restart` stays in the same card as the status.

Allowed user actions: read status, restart Gateway, wait for refresh, change another General setting, switch tabs, or return to the app.

Client state change: Gateway health controls badge language and restart availability.

Recovery / next state: continue to `3.1 Gateway Running`, `3.2 Gateway Stopped Or Error`, or `3.3 Gateway Restart Requested`.

Blocks progress: no for Settings; Gateway-dependent work can be blocked when status is nonrunning.

### 4.6 Network Proxy Detail

Trigger: the user edits or reviews `HTTP Proxy`.

Visible UI state:

- The Network section shows the proxy input and auto-detected proxy hint.
- Edited values remain visible while save feedback resolves.
- Success, warning, and failed save feedback appear near the proxy control.

Allowed user actions: edit, clear, save, retry, leave the saved value, or return to the app.

Client state change: the effective proxy changes only after a successful save.

Recovery / next state: continue to `4.1 HTTP Proxy Edited`, `4.1.1 HTTP Proxy Save Succeeds`, `4.1.2 HTTP Proxy Saves With Warning`, or `4.1.3 HTTP Proxy Save Fails`.

Blocks progress: temporarily for the proxy field while saving; yes for the new proxy value after save failure.

### 4.7 Feature Switch Detail

Trigger: the user toggles a switch in the Features panel.

Visible UI state:

- Switches show current and target state inline.
- Saving can temporarily disable or visually mark the affected switch.
- Failed save feedback rolls the switch back or marks it retryable.

Allowed user actions: wait for save, retry a failed switch, change another setting, switch tabs, or return to the app.

Client state change: each preference saves independently and failed saves preserve the previous effective value.

Recovery / next state: continue to `4.2 Launch At Login Toggle`, `4.3 Dock Icon Toggle`, `4.4 Enable Debug Tools Toggle`, or `6.3 Toggle Save Error Recovery`.

Blocks progress: temporarily for the affected switch while saving.

### 4.8 Developer Section Detail

Trigger: `Enable debug tools` is saved on.

Visible UI state:

- A `Developer` section appears below the Features panel.
- `Reset Onboarding` is available only while debug tools are enabled.
- Reset feedback can appear after the user uses the control.

Allowed user actions: reset onboarding, continue changing settings, disable debug tools, switch tabs, or return to the app.

Client state change: developer controls become available only while the debug preference is effective.

Recovery / next state: reset moves to `4.4.2 Reset Onboarding Used`; otherwise continue to `5 Close And Apply`.

Blocks progress: no.

### 5.4 Return And Apply Detail

Trigger: the user leaves General or Settings closes.

Visible UI state:

- The previous app context returns after `Back to app`.
- Clean saves leave no unresolved General error in the app.
- Saved presentation settings can become visible outside Settings.
- Retryable errors remain recoverable when Settings is reopened.

Allowed user actions: continue work, reopen Settings, or retry later.

Client state change: saved settings apply outside Settings while failed settings remain previous-value effective.

Recovery / next state: continue to `5.1 Settings Close Clean`, `5.2 Close With Retryable Error`, `5.3 Presentation Settings Applied`, or `6 Stable End State`.

Blocks progress: no for leaving Settings.

## State Language

### 0.1 Shared Settings Labels

Trigger: any Journey 12 state is visible.

Visible UI state: shared labels include `Back to app`, `General`, `Gateway Status`, `Network`, `Features`, `Developer`, `Restart`, and `Reset Onboarding`.

Allowed user actions: read labels, use visible controls, or navigate away from General.

Client state change: the app keeps Settings navigation and action language stable while state-specific content changes.

Recovery / next state: any Journey 12 state.

Blocks progress: no.

### 3.5 Gateway Status Language

Trigger: Gateway health or restart state changes.

Visible UI state: Gateway language includes `Loading`, `Running`, `Stopped`, `Restarting`, `Error`, `Restart`, and retry feedback.

Allowed user actions: wait, restart, retry, switch tabs, or return to the app.

Client state change: the visible Gateway copy follows health and restart state.

Recovery / next state: `3.1 Gateway Running`, `3.2 Gateway Stopped Or Error`, `3.3 Gateway Restart Requested`, `3.3.1 Gateway Restart Succeeds`, or `3.3.2 Gateway Restart Fails`.

Blocks progress: only when Gateway-dependent work is unavailable.

### 4.9 Save Feedback Language

Trigger: a proxy, switch, or thinking-level save starts, succeeds, warns, or fails.

Visible UI state: save language includes `Saving`, `Saved`, `Warning`, `Failed`, `Retry`, and previous-value rollback.

Allowed user actions: wait, retry, correct input, leave a warning as-is, or return to the app.

Client state change: each control's feedback follows its own save result.

Recovery / next state: the user returns to the affected control branch or matching recovery summary.

Blocks progress: only for the affected setting while unresolved.

### 4.10 Preference Control Language

Trigger: the user reviews or edits General preferences.

Visible UI state: preference labels include `HTTP Proxy`, `Launch at Login`, `Dock Icon`, `Allow Canvas`, `Enable debug tools`, and `Default Thinking Level`.

Allowed user actions: edit proxy, toggle switches, choose a thinking level, reveal Developer controls, switch tabs, or return to the app.

Client state change: labels remain stable while values, switches, and selectors change.

Recovery / next state: any General setting branch under `4 General Settings Change Surface`.

Blocks progress: no.

### 5.5 Close And Apply Language

Trigger: the user leaves General or Settings closes after a clean, applied, or retryable state.

Visible UI state: close/apply language includes `Back to app`, clean close, retryable error, and applied presentation feedback.

Allowed user actions: return to the app, continue work, reopen Settings, or retry later.

Client state change: return-to-app language reflects whether changes saved cleanly or remain retryable.

Recovery / next state: `5.1 Settings Close Clean`, `5.2 Close With Retryable Error`, `5.3 Presentation Settings Applied`, or `6 Stable End State`.

Blocks progress: no.

## Errors And Recovery

Failed or retryable branch states that remain represented in `## Branch Journeys`: `3.3.2 Gateway Restart Fails`, `4.1.3 HTTP Proxy Save Fails`, `4.2.2 Launch At Login Save Fails`, `4.3.2 Dock Icon Save Fails`, `4.4.3 Enable Debug Tools Save Fails`, `4.5.2 Default Thinking Level Save Fails`, and `5.2 Close With Retryable Error`.

### 6.1 Gateway Restart Error Recovery

Trigger: Gateway restart fails or Gateway health does not recover after restart.

Visible UI state: the Gateway Status card remains visible with error feedback and retry available when supported.

Allowed user actions: retry `Restart`, wait, switch tabs, change non Gateway settings, or return to the app.

Recovery / next state: retry returns to `3.3 Gateway Restart Requested`; returning to the app moves through `5 Close And Apply`.

Blocks progress: yes for Gateway dependent work.

### 6.2 HTTP Proxy Save Error Recovery

Trigger: the edited proxy value cannot be saved.

Visible UI state: the `HTTP Proxy` field preserves the user's input and shows failed save feedback.

Allowed user actions: correct the value, clear it, retry save, switch tabs, or return to the app.

Recovery / next state: correcting or retrying returns to `4.1 HTTP Proxy Edited`.

Blocks progress: yes for the new proxy value.

### 6.3 Toggle Save Error Recovery

Trigger: Launch at Login, Dock Icon, or Enable debug tools cannot be saved.

Visible UI state: the affected switch rolls back or shows failed save feedback.

Allowed user actions: retry the affected switch, leave it restored, change another setting, switch tabs, or return to the app.

Recovery / next state: retry returns to `4.2 Launch At Login Toggle`, `4.3 Dock Icon Toggle`, or `4.4 Enable Debug Tools Toggle`.

Blocks progress: yes for the affected preference.

### 6.4 Default Thinking Level Save Error Recovery

Trigger: the selected Default Thinking Level cannot be saved.

Visible UI state: the selector returns to the previous effective level or shows retryable failed save feedback.

Allowed user actions: select the level again, keep the restored value, change another setting, switch tabs, or return to the app.

Recovery / next state: retry returns to `4.5 Default Thinking Level Change`.

Blocks progress: yes for changing the default thinking level.

## Wireframe

The matching HTML wireframe must use this record as its route source and keep the following first-level canvas bands in order:

1. `Main happy path`
2. `Route map`
3. `Branch journeys`
4. `Detail states`
5. `State language`
6. `Errors and recovery`

The main happy path band uses the concise Journey 12 main path `1 -> 2 -> 3 -> 4 -> 5 -> 6`, so the first row stays readable as the product's primary Settings flow. The route map band includes one full-size product route row for every route in the route table. The branch, detail, state-language, and error bands do not replace the route rows; they provide separate inspection areas for the same record-defined states.
