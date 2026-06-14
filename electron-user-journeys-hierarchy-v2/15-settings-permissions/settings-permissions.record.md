# Journey 15: Settings - Permissions

This record describes the implemented NoraHarness Electron journey. It is the text source for the matching HTML wireframe and keeps the journey IDs aligned with visible wireframe cards.

## Journey Scope

User entry: The user opens the Permissions tab in Settings, or arrives from a runtime permission or system permission prompt.

User goal: The user configures command execution approval and the command allowlist, then reviews or handles notifications, microphone, accessibility, and other system permissions.

End state: Permissions shows the final command execution policy, command allowlist, and system permission state. Later Chat and Code runs follow these settings as auto-run, approval-required, or blocked.

Implementation maturity: Implemented.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches and recovery cases use decimal IDs scoped to the step where the state starts. State-language frames use `0.x` IDs because they apply across the whole journey.

Permissions tab main path:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main step | `Journey 15` | Settings entry into Permissions. | Opens Settings and selects `Permissions`, or follows a permission-related prompt into Settings. | Settings fills the window, `Back to app` remains visible, and the left navigation highlights `Permissions`. | The client enters the Permissions settings surface without changing the user's prior Chat or Code context. | `2` |
| `2` | Main step | `Journey 15` | Permissions data loads. | Waits for the tab to populate. | The Permissions page shows command execution, command allowlist, notifications, and system permission sections, with loading or resolved values. | Current command policy, allowlist entries, and system permission states are read into the tab. | `3`, `4`, or `5` |
| `3` | Main step | `Journey 15` | Command execution policy section. | Reviews or changes the selected policy. | The radio group shows `Never run commands`, `Ask before running`, and `Auto-run all commands`; auto-run carries a `Danger` marker. | The selected policy is prepared for persistence and later runtime command behavior. | `3.1`, `3.2`, `3.3`, or `4` |
| `4` | Main step | `Journey 15` | Command allowlist section. | Reviews existing entries, types a path pattern, adds a pattern, or removes a stale pattern. | The allowlist shows existing path patterns, remove controls, an input, a plus action, and validation hints. | The allowlist is updated optimistically and then persisted or rolled back when persistence fails. | `4.1`, `4.2`, or `5` |
| `5` | Main step | `Journey 15` | Notifications and system permission review. | Opens notification settings, grants microphone permission, opens accessibility settings, or rechecks permission state. | The page shows a notifications card with `Open Settings` and system permission rows with status badges and actions. | Permission state is requested, rechecked, or left as the latest known status. | `5.1`, `5.2`, `5.3`, `5.4`, or `6` |
| `6` | End state | `Journey 15` | Permissions settings are stable. | Leaves Settings, stays on the tab, or later starts a Chat or Code run. | Final policy, allowlist, and permission badges remain visible. | Later runtime work follows the saved permission policy and the current system permission state. | End or `6.1`, `6.2`, `6.3` |

Exec approval policy changes:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Branch journey | `3 Command Execution Policy` | The user selects `Never run commands`. | Chooses the strictest command policy. | `Never run commands` becomes selected while the other radio choices remain available. | Later command execution is set to block instead of asking or running automatically. | `3.4` |
| `3.2` | Branch journey | `3 Command Execution Policy` | The user selects `Ask before running`. | Chooses the approval-required policy. | `Ask before running` becomes selected. | Later command execution is set to pause for user approval when needed. | `3.4` |
| `3.3` | Branch journey | `3 Command Execution Policy` | The user selects `Auto-run all commands`. | Chooses the least restrictive policy after seeing the danger marker. | `Auto-run all commands` becomes selected and remains visually marked as dangerous. | Later command execution is set to run without a per-command prompt when other gates allow it. | `3.4` |
| `3.4` | Branch follow-up | `3.1`, `3.2`, or `3.3` | The changed policy is saving. | Waits after selecting a policy. | The selected radio remains visible; controls may be temporarily disabled or show saving feedback. | The new policy is being persisted. | `3.4.1` or `3.4.2` |
| `3.4.1` | Branch follow-up | `3.4 Policy Saving` | Policy save succeeds. | Continues reviewing Settings. | The selected radio remains active and any saving feedback clears. | The saved policy becomes the source of truth for later runtime prompts. | `4` or `6` |
| `3.4.2` | Recovery state | `3.4 Policy Saving` | Policy save fails. | Reads the failure and retries or chooses another policy. | The tab shows a retryable failure while preserving a clear selected policy state. | The previous saved policy is restored or the unsaved state is flagged. | `3` |

Allowlist add, remove, save, and failure:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.1` | Branch journey | `4 Command Allowlist` | The user adds a path pattern. | Types a path-pattern entry and clicks the plus action. | The entry appears in the allowlist if it passes validation. | The list is updated optimistically and persistence begins. | `4.1.1`, `4.1.2`, or `4.1.3` |
| `4.1.1` | Branch follow-up | `4.1 Add Allowlist Pattern` | Added allowlist entry saves. | Continues reviewing the list. | The new pattern remains visible and validation feedback clears. | The saved allowlist includes the new entry. | `4` or `5` |
| `4.1.2` | Recovery state | `4.1 Add Allowlist Pattern` | Added pattern is invalid. | Reads the validation hint and edits the input. | Inline validation explains that the allowlist expects a path pattern rather than a blank or basename-only value. | The invalid entry is not added or persisted. | `4` |
| `4.1.3` | Recovery state | `4.1 Add Allowlist Pattern` | Added pattern is a duplicate. | Reads the validation hint and edits or clears the input. | Inline validation identifies the duplicate while the existing saved entry remains visible. | The duplicate is not added or persisted. | `4` |
| `4.2` | Branch journey | `4 Command Allowlist` | The user removes an allowlist entry. | Clicks the remove control on an existing pattern. | The pattern disappears from the visible list while the remaining entries stay visible. | The list is updated optimistically and persistence begins. | `4.2.1` or `4.2.2` |
| `4.2.1` | Branch follow-up | `4.2 Remove Allowlist Pattern` | Removal saves. | Continues reviewing the list. | The removed pattern stays absent. | The saved allowlist no longer includes the removed entry. | `4` or `5` |
| `4.2.2` | Recovery state | `4.1 Add Allowlist Pattern` or `4.2 Remove Allowlist Pattern` | Allowlist save fails. | Reads the failure, retries, or edits the list again. | A retryable failure appears and the list is restored or marked unsaved. | The saved allowlist remains unchanged or returns to the last known saved state. | `4` |

System permissions missing, open settings, recheck, granted, denied, and unknown:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `5.1` | Branch journey | `5 System Permission Review` | A system permission is missing or needs action. | Reviews the affected permission row. | The row shows a missing, not granted, denied, or needs-action badge with an available action. | The permission remains unresolved until the user requests, opens settings, or rechecks. | `5.1.1`, `5.3`, `5.4`, or `6` |
| `5.1.1` | Branch follow-up | `5.1 System Permission Missing` | The user opens system settings. | Clicks `Open Settings` for notifications, accessibility, or another settings-backed permission. | The button shows an opening or in-progress state, then the user leaves the app for the operating-system settings surface. | The app records that the user has been handed off and waits for return or recheck. | `5.1.2` |
| `5.1.2` | Branch follow-up | `5.1.1 Open System Settings` | The user rechecks permission state after returning. | Returns to NoraHarness and clicks recheck, or waits for the tab to poll. | The row shows a checking state, then refreshes its badge. | The latest permission state replaces the previous visible state. | `5.1.2.1`, `5.1.2.2`, or `5.1.2.3` |
| `5.1.2.1` | Branch follow-up | `5.1.2 Recheck System Permission` | Permission is granted. | Continues reviewing Settings. | The permission row shows `Granted` and no longer presents an urgent recovery action. | The permission is treated as available for later features that need it. | `6` |
| `5.1.2.2` | Recovery state | `5.1.2 Recheck System Permission` | Permission is still denied or not granted. | Opens settings again, retries, or leaves it unresolved. | The permission row stays blocked or not granted and keeps explanatory recovery copy. | Features that need this permission remain unavailable or degraded. | `5` or `6` |
| `5.1.2.3` | Recovery state | `5.1.2 Recheck System Permission` | Permission state is unknown. | Retries the check or continues with the unknown status visible. | The row shows `Unknown` and keeps the recovery action available when possible. | The client cannot confirm the permission state and avoids treating it as granted. | `5` or `6` |
| `5.2` | Branch journey | `5 System Permission Review` | Notification settings handoff. | Clicks `Open Settings` in the notifications card. | The notifications card shows an opening state and then returns to the Permissions tab after the external settings handoff. | Notification settings are not edited inside NoraHarness; the tab relies on the user returning and rechecking as needed. | `5.1.1` |
| `5.3` | Branch journey | `5 System Permission Review` | Microphone permission request. | Clicks the microphone grant action. | The microphone row shows requesting or checking state, then resolves to granted, denied, or unknown. | The microphone permission request is attempted and the permission list is refreshed. | `5.1.2` |
| `5.4` | Branch journey | `5 System Permission Review` | Accessibility settings handoff. | Clicks `Open Settings` for Accessibility. | The accessibility row keeps the app-visible action while the user grants access in system settings. | Accessibility cannot be granted entirely inside the app, so the journey moves through external settings and recheck. | `5.1.1` |

Runtime prompt handoff:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `6.1` | Branch journey | `6 Stable Permissions State` | Later runtime command auto-runs. | Starts or continues a Chat or Code task after selecting auto-run. | The later runtime surface proceeds without a per-command approval prompt when the command is otherwise allowed. | The saved auto-run policy is applied outside Settings. | End |
| `6.2` | Branch journey | `6 Stable Permissions State` | Later runtime command asks for approval. | Starts or continues a Chat or Code task after selecting ask-before-running. | The later runtime surface can stop at an approval request and wait for an allow or deny response. | The saved ask policy is applied outside Settings. | End |
| `6.3` | Recovery state | `6 Stable Permissions State` | Later runtime command is blocked. | Starts or continues a Chat or Code task after selecting never-run or leaving required permission unresolved. | The later runtime surface shows a blocked, denied, or permission-required result instead of silently running the command. | The saved block policy or unresolved permission state prevents the action. | End |

Detail states:

No separate detail-only states exist for this journey. The settings page details are part of the main, branch, state-language, and recovery states above.

State language:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Command execution language. | Reads command policy labels and the warning marker. | `Never run commands`, `Ask before running`, `Auto-run all commands`, and `Danger` distinguish the policy choices. | Later runtime behavior uses the chosen policy language. | Active state |
| `0.2` | State language | Whole journey | Command allowlist language. | Reads the allowlist row labels, add/remove actions, and validation. | `Command Allowlist`, plus action, remove action, path-pattern hint, and inline validation explain allowlist editing. | Allowlist edits keep the same vocabulary while saving, failing, or restoring. | Active state |
| `0.3` | State language | Whole journey | Notification settings language. | Reads the notification card action. | `Open Settings` communicates that notification recovery happens outside NoraHarness. | The app can hand the user off and then refresh visible state. | Active state |
| `0.4` | State language | Whole journey | System permission language. | Reads permission badges and actions. | `Granted`, `Not Granted`, `Denied`, `Unknown`, `Grant`, `Open Settings`, and `Recheck` distinguish permission states. | Denied and unknown states remain visible until a later check confirms access. | Active state |
| `0.5` | State language | Whole journey | Runtime handoff language. | Reads later Chat or Code runtime permission language. | Approval prompt, auto-run, blocked, denied, and permission required language reflects the Settings choice. | Later runtime surfaces reuse the saved permission state. | Active state |

Recovery and error:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Recovery state | `2 Load Permissions State` | Command permission settings are still loading. | Waits. | Command policy and allowlist controls show loading or disabled state. | The tab waits for saved command permission state before enabling changes. | `3` |
| `2.2` | Recovery state | `2 Load Permissions State` | Command permission settings fail to load. | Retries, leaves Settings, or continues with unavailable controls. | A retryable failure appears and command policy controls are unavailable or stale. | The tab avoids pretending unsaved or unknown command policy is current. | `2` or `6` |
| `5.5` | Recovery state | `5 System Permission Review` | System permission bridge is unavailable. | Reads the message and retries in the desktop app context when available. | Permission rows show unknown or unavailable state with recovery copy instead of grant controls that cannot work. | The client cannot refresh or open the system permission action from the current context. | `5` |
| `5.6` | Recovery state | `5.1.1 Open System Settings` | Opening system settings fails. | Retries the open action or manually opens system settings. | The row or toast shows that the settings handoff did not complete. | The permission state remains unchanged. | `5` |
| `5.7` | Recovery state | `5.1.2 Recheck System Permission` | Permission recheck times out or does not resolve. | Retries recheck or leaves the current badge visible. | The row returns from checking to the last known badge with retry available. | No new permission state replaces the last known state. | `5` |

Route table:

| Route | Composition | Result / next state |
| --- | --- | --- |
| Permissions review only | `1 -> 2 -> 3 -> 4 -> 5 -> 6` | The user confirms the current command policy, allowlist, and system permission badges without changing them. |
| Exec policy changed to never run | `1 -> 2 -> 3 -> 3.1 -> 3.4 -> 3.4.1 -> 4 -> 5 -> 6 -> 6.3` | Later runtime command execution is blocked unless the user changes policy again. |
| Exec policy changed to ask before running | `1 -> 2 -> 3 -> 3.2 -> 3.4 -> 3.4.1 -> 4 -> 5 -> 6 -> 6.2` | Later runtime command execution can hand off to an approval prompt. |
| Exec policy changed to auto-run | `1 -> 2 -> 3 -> 3.3 -> 3.4 -> 3.4.1 -> 4 -> 5 -> 6 -> 6.1` | Later runtime command execution can proceed without a per-command approval prompt when other gates allow it. |
| Exec policy save failure | `1 -> 2 -> 3 -> 3.2 -> 3.4 -> 3.4.2 -> 3` | The user remains in the policy section with the previous saved policy restored or the unsaved state flagged. |
| Allowlist add saved | `1 -> 2 -> 3 -> 4 -> 4.1 -> 4.1.1 -> 5 -> 6` | The new path pattern remains visible and becomes part of later command permission behavior. |
| Allowlist invalid entry | `1 -> 2 -> 3 -> 4 -> 4.1 -> 4.1.2 -> 4` | The invalid value is not saved; the user corrects the input in the allowlist section. |
| Allowlist duplicate entry | `1 -> 2 -> 3 -> 4 -> 4.1 -> 4.1.3 -> 4` | The duplicate value is not saved; the existing saved pattern remains visible. |
| Allowlist removal saved | `1 -> 2 -> 3 -> 4 -> 4.2 -> 4.2.1 -> 5 -> 6` | The removed pattern stays absent and later command behavior uses the reduced allowlist. |
| Allowlist save failure | `1 -> 2 -> 3 -> 4 -> 4.2 -> 4.2.2 -> 4` | The user remains in the allowlist section and can retry or edit after the failed save. |
| Notification settings handoff | `1 -> 2 -> 3 -> 4 -> 5 -> 5.2 -> 5.1.1 -> 5.1.2 -> 5.1.2.1 -> 6` | The user opens operating-system notification settings, returns, rechecks, and sees a granted or refreshed state. |
| Microphone granted after request | `1 -> 2 -> 3 -> 4 -> 5 -> 5.3 -> 5.1.2 -> 5.1.2.1 -> 6` | The microphone row resolves to granted and the Permissions tab reaches a stable state. |
| Microphone denied after request | `1 -> 2 -> 3 -> 4 -> 5 -> 5.3 -> 5.1.2 -> 5.1.2.2 -> 6 -> 6.3` | The microphone row remains denied or not granted, and later microphone-dependent behavior remains blocked. |
| Accessibility granted after settings | `1 -> 2 -> 3 -> 4 -> 5 -> 5.4 -> 5.1.1 -> 5.1.2 -> 5.1.2.1 -> 6` | The user grants accessibility outside the app and the recheck confirms it. |
| Accessibility still denied | `1 -> 2 -> 3 -> 4 -> 5 -> 5.4 -> 5.1.1 -> 5.1.2 -> 5.1.2.2 -> 6 -> 6.3` | Accessibility remains blocked and later accessibility-dependent behavior stays unavailable. |
| Permission unknown after recheck | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 5.1.2 -> 5.1.2.3 -> 6` | The user reaches a stable but unresolved Permissions state with the unknown badge still visible. |
| Command permissions load failure | `1 -> 2 -> 2.2 -> 2` | The user retries loading before changing command policy or allowlist. |
| System permission bridge unavailable | `1 -> 2 -> 3 -> 4 -> 5 -> 5.5 -> 5` | The user stays in the system permission section with unknown or unavailable state visible. |
| Open system settings failure | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 5.6 -> 5` | The user retries or manually opens system settings because the handoff failed. |
| Permission recheck timeout | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 5.1.2 -> 5.7 -> 5` | The last known badge remains visible and the user can retry the check. |

## Main Path

### 1 Open Settings Permissions

User entry: the user opens Settings from the app and selects `Permissions`, or follows a runtime or system permission prompt that takes them to the Permissions tab.

User action: the user enters the Permissions settings surface.

Visible UI state:

- Settings occupies the main window.
- `Back to app` remains available.
- The left navigation highlights `Permissions`.
- The prior Chat or Code context is not shown as the active surface while Settings is open.

Client state change: the client switches to the Permissions settings surface and preserves the user's prior app context for return.

Exit / next state: the tab begins loading permission data in `2 Load Permissions State`.

### 2 Load Permissions State

User entry: the Permissions tab has opened.

User action: the user waits for values to populate.

Visible UI state:

- The page title and section layout are visible.
- Command execution controls, command allowlist, notification settings, and system permission rows either show loading state or resolved values.
- Controls that depend on loaded values are not treated as confirmed until loading resolves.

Client state change: the current command execution policy, allowlist entries, and system permission statuses are read into the visible tab state.

Exit / next state: resolved values lead to `3 Command Execution Policy`, `4 Command Allowlist`, or `5 System Permission Review`; loading and failure states route through `2.1` or `2.2`.

### 3 Command Execution Policy

User entry: command permission settings have loaded.

User action: the user reviews the current radio selection or chooses a different policy.

Visible UI state:

- The command execution radio group shows `Never run commands`, `Ask before running`, and `Auto-run all commands`.
- `Auto-run all commands` has a visible `Danger` marker.
- The current saved or selected policy is clear before the user leaves the section.

Client state change: selecting a policy updates the visible choice and starts the save path for that policy.

Exit / next state: unchanged policy can continue to `4 Command Allowlist`; changed policy routes to `3.1`, `3.2`, or `3.3`.

### 4 Command Allowlist

User entry: the user reaches the command allowlist section after reviewing or changing command policy.

User action: the user adds a new path pattern, removes a stale pattern, or only reviews the list.

Visible UI state:

- Existing allowlist entries appear as removable rows.
- The add input names the expected path-pattern shape.
- The plus action is available only when the input can be submitted.
- Validation feedback appears inline when the user enters an invalid or duplicate value.

Client state change: valid add and remove actions update the list immediately, then either persist or roll back if saving fails.

Exit / next state: add routes through `4.1`; remove routes through `4.2`; simple review continues to `5 System Permission Review`.

### 5 System Permission Review

User entry: the user reaches notifications and system permissions after command policy and allowlist review.

User action: the user opens notification settings, requests microphone access, opens accessibility settings, rechecks status, or leaves a permission unresolved.

Visible UI state:

- A notifications card offers `Open Settings`.
- System permission rows show status badges such as granted, not granted, denied, missing, or unknown.
- Microphone can offer a direct grant action.
- Accessibility uses an `Open Settings` handoff.
- Recheck or checking state appears after the user returns from a system permission action.

Client state change: the tab requests or refreshes system permission state, records the latest visible badge, and avoids treating denied or unknown permissions as granted.

Exit / next state: permission actions route through `5.1`, `5.2`, `5.3`, or `5.4`; stable review continues to `6 Stable Permissions State`.

### 6 Stable Permissions State

User entry: command policy, allowlist, and system permission states have reached a visible stable state.

User action: the user stays on the tab, clicks `Back to app`, or later starts a Chat or Code task.

Visible UI state:

- The selected command execution policy is visible.
- The allowlist shows its saved or last known state.
- Each system permission row shows the latest known badge.
- Any unresolved state remains visible rather than being hidden.

Client state change: later runtime behavior uses the saved command policy and the current permission status.

Exit / next state: the record ends here unless later runtime behavior follows `6.1`, `6.2`, or `6.3`.

## Branch Journeys

### 3.1 Never Run Commands Selected

Trigger: the user selects `Never run commands`.

Visible UI state: the strictest radio option becomes selected.

Allowed user actions: wait for save, choose another policy, continue reviewing Settings, or leave after the save settles.

Client state change: command execution is set toward blocked behavior for later runtime tasks.

Recovery / next state: the change proceeds to `3.4 Policy Saving`.

Blocks progress: temporarily while saving.

### 3.2 Ask Before Running Selected

Trigger: the user selects `Ask before running`.

Visible UI state: the approval-required radio option becomes selected.

Allowed user actions: wait for save, choose another policy, continue reviewing Settings, or leave after the save settles.

Client state change: command execution is set toward runtime approval prompts for later tasks.

Recovery / next state: the change proceeds to `3.4 Policy Saving`.

Blocks progress: temporarily while saving.

### 3.3 Auto Run All Commands Selected

Trigger: the user selects `Auto-run all commands`.

Visible UI state: the auto-run radio option becomes selected and remains marked with `Danger`.

Allowed user actions: wait for save, choose another policy, continue reviewing Settings, or leave after the save settles.

Client state change: command execution is set toward auto-run behavior for later tasks when other gates allow it.

Recovery / next state: the change proceeds to `3.4 Policy Saving`.

Blocks progress: temporarily while saving.

### 3.4 Policy Saving

Trigger: the user has selected a different command execution policy.

Visible UI state: the selected radio remains visible and controls may be temporarily disabled or show saving feedback.

Allowed user actions: wait for the save to settle.

Client state change: the changed policy is being persisted.

Recovery / next state: success moves to `3.4.1 Policy Saved`; failure moves to `3.4.2 Policy Save Failed`.

Blocks progress: temporarily.

### 3.4.1 Policy Saved

Trigger: the changed command execution policy is saved successfully.

Visible UI state: saving feedback clears and the selected policy remains visible.

Allowed user actions: continue to allowlist, system permissions, Back to app, or leave the policy as-is.

Client state change: the saved policy becomes the source for later runtime command behavior.

Recovery / next state: continue to `4 Command Allowlist` or `6 Stable Permissions State`.

Blocks progress: no.

### 4.1 Add Allowlist Pattern

Trigger: the user types a path pattern and clicks the plus action.

Visible UI state: the submitted value is validated; valid entries appear in the list immediately.

Allowed user actions: wait for save, correct validation errors, add another pattern, remove an entry, or continue after the save settles.

Client state change: a valid pattern is added optimistically and persistence begins.

Recovery / next state: saved entries move to `4.1.1 Allowlist Add Saved`; invalid or duplicate entries move to `4.1.2` or `4.1.3`.

Blocks progress: temporarily for the submitted entry.

### 4.1.1 Allowlist Add Saved

Trigger: the added allowlist pattern persists successfully.

Visible UI state: the new entry remains visible in the allowlist.

Allowed user actions: add another entry, remove an entry, continue to system permissions, or leave Settings.

Client state change: the saved allowlist includes the new pattern.

Recovery / next state: return to `4 Command Allowlist` or continue to `5 System Permission Review`.

Blocks progress: no.

### 4.2 Remove Allowlist Pattern

Trigger: the user clicks remove on an existing allowlist entry.

Visible UI state: the entry disappears from the visible list while the remaining entries stay visible.

Allowed user actions: wait for save, continue reviewing, or re-add the pattern later if needed.

Client state change: the entry is removed optimistically and persistence begins.

Recovery / next state: success moves to `4.2.1 Allowlist Removal Saved`; failure moves to `4.2.2 Allowlist Save Failed`.

Blocks progress: temporarily for relying on the changed list.

### 4.2.1 Allowlist Removal Saved

Trigger: the removed allowlist entry persists successfully.

Visible UI state: the removed entry remains absent.

Allowed user actions: remove another entry, add a new entry, continue to system permissions, or leave Settings.

Client state change: the saved allowlist no longer includes the removed pattern.

Recovery / next state: return to `4 Command Allowlist` or continue to `5 System Permission Review`.

Blocks progress: no.

### 5.1 System Permission Missing

Trigger: a system permission row resolves as missing, not granted, denied, or needing action.

Visible UI state: the row shows a non-granted badge and offers the relevant recovery action when possible.

Allowed user actions: open system settings, request microphone permission, recheck, leave unresolved, or leave Settings.

Client state change: the permission is not treated as granted.

Recovery / next state: continue to `5.1.1 Open System Settings`, `5.3 Microphone Permission Request`, `5.4 Accessibility Settings Handoff`, or `6 Stable Permissions State`.

Blocks progress: yes for features that require that permission.

### 5.1.1 Open System Settings

Trigger: the user opens an operating-system settings surface from a notification, accessibility, or similar permission row.

Visible UI state: the clicked action shows opening or in-progress state before the user leaves the app for system settings.

Allowed user actions: complete the external permission change, return to NoraHarness, or retry if the handoff fails.

Client state change: the app records that the user has been handed off and waits for return or recheck.

Recovery / next state: returning to the app moves to `5.1.2 Recheck System Permission`; handoff failure moves to `5.6 Open System Settings Failed`.

Blocks progress: temporarily for the permission being handled.

### 5.1.2 Recheck System Permission

Trigger: the user returns from system settings or requests a fresh permission check.

Visible UI state: the relevant row shows checking state and then refreshes its badge.

Allowed user actions: wait for the recheck, retry, open settings again, or leave the unresolved badge visible.

Client state change: the latest permission state replaces the previous visible status if the check resolves.

Recovery / next state: granted moves to `5.1.2.1 Permission Granted`; still denied moves to `5.1.2.2 Permission Still Denied`; unknown moves to `5.1.2.3 Permission Unknown`; timeout moves to `5.7 Permission Recheck Timeout`.

Blocks progress: temporarily.

### 5.1.2.1 Permission Granted

Trigger: recheck confirms the system permission is granted.

Visible UI state: the row shows `Granted` and no longer needs an urgent recovery action.

Allowed user actions: continue reviewing Settings, leave Settings, or use the feature that required the permission.

Client state change: the permission is available for later feature behavior.

Recovery / next state: continue to `6 Stable Permissions State`.

Blocks progress: no.

### 5.2 Notification Settings Handoff

Trigger: the user clicks `Open Settings` on the notifications card.

Visible UI state: the notifications card shows that it is opening settings.

Allowed user actions: complete changes in system settings, return to NoraHarness, recheck if needed, or leave the current notification state unchanged.

Client state change: notification settings are handled outside NoraHarness and then reflected by the latest visible state when checked.

Recovery / next state: continue to `5.1.1 Open System Settings`.

Blocks progress: no for command settings; yes only for notification-dependent behavior if permission remains unresolved.

### 5.3 Microphone Permission Request

Trigger: the user clicks the microphone grant action.

Visible UI state: the microphone row shows requesting or checking state, then resolves to granted, denied, or unknown.

Allowed user actions: respond to the operating-system permission request, retry, or leave unresolved.

Client state change: the app attempts to request microphone permission and refreshes permission state afterward.

Recovery / next state: continue to `5.1.2 Recheck System Permission`.

Blocks progress: temporarily, then only if the result is denied or unknown.

### 5.4 Accessibility Settings Handoff

Trigger: the user clicks `Open Settings` for Accessibility.

Visible UI state: the accessibility row sends the user to system settings rather than pretending the permission can be granted entirely in-app.

Allowed user actions: grant accessibility in system settings, return to NoraHarness, recheck, retry, or leave unresolved.

Client state change: the app waits for external settings changes and then refreshes the badge.

Recovery / next state: continue to `5.1.1 Open System Settings`.

Blocks progress: yes for accessibility-dependent behavior until granted.

### 6.1 Runtime Auto Run Handoff

Trigger: the user later starts a Chat or Code task after saving `Auto-run all commands`.

Visible UI state: the runtime surface proceeds without a per-command approval prompt when the command is otherwise allowed.

Allowed user actions: monitor the run, stop it if available, or adjust Settings later.

Client state change: the saved auto-run policy is applied outside Settings.

Recovery / next state: end of this Settings journey.

Blocks progress: no when other gates allow the command.

### 6.2 Runtime Approval Prompt Handoff

Trigger: the user later starts a Chat or Code task after saving `Ask before running`.

Visible UI state: the runtime surface can insert an approval request into the conversation and wait for the user's allow or deny response.

Allowed user actions: approve once, deny, stop, or return to Settings later to change policy.

Client state change: the saved ask policy is applied outside Settings.

Recovery / next state: end of this Settings journey.

Blocks progress: yes until the approval request is answered.

## Detail States

No separate states exist for this category. All visible detail behavior is represented in the main path, branch journeys, state language, or errors and recovery sections.

## State Language

| ID | Surface | Copy / control language | Meaning |
| --- | --- | --- | --- |
| `0.1` | Command execution | `Never run commands`, `Ask before running`, `Auto-run all commands`, `Danger` | The three command execution policies and the warning marker for the least restrictive policy. |
| `0.2` | Command allowlist | `Command Allowlist`, plus action, remove action, path-pattern hint, inline validation | The allowlist section explains which commands or paths can be allowed and when an entry cannot be saved. |
| `0.3` | Notifications | `Open Settings` | The app sends the user to operating-system notification settings instead of editing notification permission inline. |
| `0.4` | System permissions | `Granted`, `Not Granted`, `Denied`, `Unknown`, `Grant`, `Open Settings`, `Recheck` | Permission rows distinguish confirmed access from unresolved or blocked access. |
| `0.5` | Runtime handoff | approval prompt, auto-run, blocked, denied, permission required | Later Chat and Code surfaces reflect the policy chosen in Settings. |

### 0.1 Command Execution Language

The command execution section uses three plain policy labels: `Never run commands`, `Ask before running`, and `Auto-run all commands`. The `Danger` marker stays attached to auto-run so the user can distinguish the least restrictive policy from the safer choices.

### 0.2 Command Allowlist Language

The allowlist section uses direct add and remove language around path-pattern entries. Inline validation stays next to the input when a value is blank, basename-only, duplicated, or otherwise not accepted.

### 0.3 Notification Settings Language

The notifications card uses `Open Settings` because notification recovery happens in operating-system settings. The app can hand the user off and then return to a refreshed visible state.

### 0.4 System Permission Language

System permission rows use badge language such as `Granted`, `Not Granted`, `Denied`, and `Unknown`, plus actions such as `Grant`, `Open Settings`, and `Recheck`. Denied and unknown states remain visible until a later check confirms access.

### 0.5 Runtime Handoff Language

Later Chat and Code surfaces reflect the saved Settings choice with approval prompts, auto-run behavior, or blocked and permission-required states. This language belongs to the later runtime surface, but it is caused by the policy chosen in Permissions.

## Errors And Recovery

| ID | Error / branch | Where it appears | Recovery |
| --- | --- | --- | --- |
| `2.1` | Command permission settings are still loading. | Command execution and allowlist sections. | Wait until loading resolves before changing command policy or allowlist. |
| `2.2` | Command permission settings fail to load. | Command execution and allowlist sections. | Retry loading or leave Settings without treating unknown values as saved. |
| `3.4.2` | Command policy save fails. | Command execution policy section. | Retry, choose another policy, or rely on the previous saved policy. |
| `4.1.2` | Allowlist pattern is invalid. | Command allowlist input. | Edit the value into a path pattern and add again. |
| `4.1.3` | Allowlist pattern is a duplicate. | Command allowlist input. | Clear the input, edit it, or remove the existing entry if intended. |
| `4.2.2` | Allowlist save fails. | Command allowlist section. | Retry the add or remove action after the list restores or marks the unsaved state. |
| `5.1.2.2` | Permission remains denied or not granted. | System permission row. | Open settings again, retry recheck, or leave the unresolved badge visible. |
| `5.1.2.3` | Permission state is unknown. | System permission row. | Retry recheck, open settings, or continue with the unknown badge visible. |
| `5.5` | System permission bridge is unavailable. | System permission section. | Retry in the desktop app context or leave the state unknown. |
| `5.6` | Opening system settings fails. | Notification or system permission row. | Retry the handoff or open system settings manually. |
| `5.7` | Permission recheck times out. | System permission row. | Retry recheck or keep the last known badge visible. |
| `6.3` | Later runtime action is blocked. | Later Chat or Code runtime surface. | Return to Permissions, grant the missing permission, change policy, or retry after recovery. |

### 2.1 Command Permissions Loading

Trigger: command policy or allowlist values are still being read after the tab opens.

Visible UI state: command execution and allowlist controls show loading, disabled, or not-yet-populated state while the rest of the Settings frame remains visible.

Allowed user actions: wait, inspect other visible sections if enabled, or leave Settings.

Client state change: the tab is waiting for saved command permission state before allowing confirmed edits.

Recovery / next state: successful loading returns to `3 Command Execution Policy`.

Blocks progress: temporarily, for command policy and allowlist edits.

### 2.2 Command Permissions Load Failed

Trigger: command policy or allowlist values cannot be loaded.

Visible UI state: the command permission area shows a retryable failure or keeps controls unavailable instead of presenting unknown values as saved.

Allowed user actions: retry loading, leave Settings, or continue reviewing unrelated visible sections if available.

Client state change: command policy and allowlist changes are blocked until the saved state is available.

Recovery / next state: retry returns to `2 Load Permissions State`; leaving Settings can land at `6 Stable Permissions State` with no confirmed changes.

Blocks progress: yes for command permission changes.

### 3.4.2 Policy Save Failed

Trigger: the changed command execution policy cannot be saved.

Visible UI state: the policy area shows a retryable failure while preserving a clear selected or restored policy state.

Allowed user actions: retry, choose another policy, leave Settings, or continue only after accepting that the change did not save.

Client state change: the previous saved policy is restored or the current visible state is flagged as unsaved.

Recovery / next state: return to `3 Command Execution Policy`.

Blocks progress: yes for relying on the new policy.

### 4.1.2 Invalid Allowlist Pattern

Trigger: the submitted allowlist value is blank, basename-only, or otherwise not accepted as a path pattern.

Visible UI state: inline validation explains the required path-pattern shape and the invalid entry is not saved.

Allowed user actions: edit the input, clear it, or leave the section.

Client state change: no new allowlist entry is added.

Recovery / next state: return to `4 Command Allowlist`.

Blocks progress: yes for adding that value.

### 4.1.3 Duplicate Allowlist Pattern

Trigger: the submitted allowlist value already exists in the list.

Visible UI state: inline validation identifies the duplicate and the existing saved row remains visible.

Allowed user actions: edit the input, clear it, or remove the existing entry if that is the intended action.

Client state change: no duplicate entry is added.

Recovery / next state: return to `4 Command Allowlist`.

Blocks progress: yes for adding that value.

### 4.2.2 Allowlist Save Failed

Trigger: an add or remove action cannot be saved.

Visible UI state: the allowlist shows a retryable failure and the list is restored or marked as unsaved.

Allowed user actions: retry, edit the list, re-add or remove the entry again, or leave Settings.

Client state change: the saved allowlist remains unchanged or returns to the last known saved state.

Recovery / next state: return to `4 Command Allowlist`.

Blocks progress: yes for relying on the unsaved allowlist change.

### 5.1.2.2 Permission Still Denied

Trigger: recheck confirms the permission remains denied, not granted, or blocked.

Visible UI state: the row keeps a denied or not-granted badge and explanatory recovery copy.

Allowed user actions: open settings again, retry recheck, leave the permission unresolved, or leave Settings.

Client state change: features requiring that permission remain unavailable or degraded.

Recovery / next state: return to `5 System Permission Review` or continue to `6 Stable Permissions State` with the unresolved badge visible.

Blocks progress: yes for features that require that permission.

### 5.1.2.3 Permission Unknown

Trigger: recheck cannot confidently determine whether the permission is granted.

Visible UI state: the row shows `Unknown` and keeps recovery available when possible.

Allowed user actions: retry recheck, open settings, continue with the unknown state visible, or leave Settings.

Client state change: the permission is not treated as granted.

Recovery / next state: return to `5 System Permission Review` or continue to `6 Stable Permissions State`.

Blocks progress: yes for features that require confirmed permission.

### 5.5 System Permission Bridge Unavailable

Trigger: system permission actions or status checks are not available in the current app context.

Visible UI state: permission rows show unknown or unavailable state with recovery copy instead of pretending a grant is possible.

Allowed user actions: retry in the desktop app context, continue reviewing command settings, or leave Settings.

Client state change: the client cannot refresh or request permission state from the current context.

Recovery / next state: return to `5 System Permission Review`.

Blocks progress: yes for confirming system permission state.

### 5.6 Open System Settings Failed

Trigger: the app cannot open the requested system settings surface.

Visible UI state: the user sees that the settings handoff did not complete and can retry or open system settings manually.

Allowed user actions: retry the handoff, manually open system settings, recheck, or leave unresolved.

Client state change: the permission state remains unchanged.

Recovery / next state: return to `5 System Permission Review`.

Blocks progress: yes for resolving that permission from the app.

### 5.7 Permission Recheck Timeout

Trigger: a permission recheck does not resolve in time or returns no new state.

Visible UI state: the row leaves checking state and returns to the last known badge with retry available.

Allowed user actions: retry recheck, open settings again, or leave the badge visible.

Client state change: no new permission state replaces the last known value.

Recovery / next state: return to `5 System Permission Review`.

Blocks progress: yes for confirming the new permission state.

### 6.3 Runtime Blocked Handoff

Trigger: the user later starts a Chat or Code task while command execution is blocked or a required system permission remains unresolved.

Visible UI state: the runtime surface shows a blocked, denied, or permission-required state instead of silently running the action.

Allowed user actions: return to Settings, grant the missing permission, change command policy, retry the run, or leave the task blocked.

Client state change: saved policy or unresolved permission state prevents the runtime action.

Recovery / next state: end of this Settings journey, or a later return to `1 Open Settings Permissions`.

Blocks progress: yes for the affected runtime action.

## Wireframe

The wireframe must use this record as its route and state source. It must keep six separate first-level canvas bands in this order: `Main happy path`, `Route map`, `Branch journeys`, `Detail states`, `State language`, and `Errors and recovery`.

Route rows must cover every route in the route table as full-size product wireframe cards. Route rows do not replace the separate branch, detail, state-language, or error bands.
