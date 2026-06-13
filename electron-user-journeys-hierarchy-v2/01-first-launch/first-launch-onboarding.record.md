# Journey 1: First Launch Onboarding

This Markdown file is the text counterpart to `first-launch-onboarding.wireframe.html`. It documents the current implemented journey. Each section ID matches a card in the wireframe.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches and supporting states use decimal IDs scoped to the step where the state starts.

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `1` | Main path | Journey 1 | Fresh-install welcome entry. |
| `2` | Main path | Journey 1 | Provider setup and verification gate. |
| `3` | Main path | Journey 1 | Notification opt-in step. |
| `4` | Main path | Journey 1 | Completion confirmation. |
| `5` | Main path | Journey 1 | First normal Chat surface after onboarding. |

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `2.1` | Branch journey | `2 Provider Setup` | Verification succeeds and the provider form becomes continuable. |
| `2.1.1` | Branch follow-up | `2.1 Verification success` | User opens the returned default model list after verification succeeds. |
| `2.2` | Branch journey | `2 Provider Setup` | Missing provider credential blocks verification. |
| `2.3` | Branch journey | `2 Provider Setup` | Remote provider key fails local shape validation. |
| `2.4` | Branch journey | `2 Provider Setup` | Local endpoint URL fails local validation. |
| `2.5` | Branch journey | `2 Provider Setup` | Provider rejects the credential during verification. |
| `2.6` | Branch journey | `2 Provider Setup` | Provider cannot be reached during verification. |
| `2.7` | Branch journey | `2 Provider Setup` | Verification fails with a generic or unexpected result. |
| `2.8` | Branch journey | `2 Provider Setup` | User opens the advanced skip-verification confirmation. |
| `2.8.1` | Branch follow-up | `2.8 Skip confirmation` | User confirms verification skip and Continue becomes available. |
| `2.9` | Branch journey | `2 Provider Setup` | Provider settings fail to save after Continue. |
| `3.1` | Branch journey | `3 Notifications` | Notification permission resolves as blocked, unknown, or otherwise not granted. |
| `4.1` | Branch journey | `4 Completion` | User clicks Start chatting and leaves onboarding. |
| `4.2` | Branch journey | `4 Completion` | User launches later after onboarding has already completed. |
| `4.3` | Branch journey | `4 Completion` | Onboarding is reset and the user returns to Welcome. |

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `2.10` | Detail state | `2 Provider Setup` | Editable provider form states before verification or skip. |
| `2.11` | Detail state | `2 Provider Setup` | Expanded provider list, More Models, and provider switching behavior. |
| `2.12` | Detail state | `2 Provider Setup` | Verification in progress, success, and failure routing. |
| `2.13` | Detail state | `2 Provider Setup` | Advanced skip-verification confirmation route. |
| `2.14` | Detail state | `2 Provider Setup` | Back, Continue, save-in-progress, save-success, and save-failure behavior. |
| `3.2` | Detail state | `3 Notifications` | Maybe later and Turn on notifications branches. |
| `2.15` | State language | `2 Provider Setup` | Provider action labels across idle, checking, verified, skipped, and saving states. |
| `2.16` | State language | `2 Provider Setup` | Provider status labels such as Verified and Verification skipped. |
| `0.1` | State language | Whole journey | Shared action labels across the onboarding flow. |
| `2.17` | Recovery summary | `2 Provider Setup` | Input validation summary for `2.2`, `2.3`, and `2.4`. |
| `2.18` | Recovery summary | `2 Provider Setup` | Verification failure summary for `2.5`, `2.6`, and `2.7`. |
| `2.19` | Recovery summary | `2 Provider Setup` | Skip and save recovery summary for `2.8`, `2.8.1`, and `2.9`. |
| `3.3` | Recovery summary | `3 Notifications` | Notification recovery summary for `3.1`. |
| `4.4` | Recovery summary | `4 Completion` | Completion, relaunch, and reset summary for `4.1`, `4.2`, and `4.3`. |

| Route | Composition | Result / next state |
| --- | --- | --- |
| Happy path with model review | `1 → 2 → 2.1 → 2.1.1 → 2.14 → 3 → 4 → 5` | User verifies the provider, optionally changes the default model, saves, finishes onboarding, and reaches Chat. |
| Happy path without model review | `1 → 2 → 2.1 → 2.14 → 3 → 4 → 5` | User verifies the provider, keeps the selected model, saves, finishes onboarding, and reaches Chat. |
| Missing credential | `1 → 2 → 2.2 → 2.10` | The form blocks verification until the user enters a credential. |
| Remote key local validation failure | `1 → 2 → 2.3 → 2.10` | The form blocks verification until the user corrects the key shape. |
| Local endpoint validation failure | `1 → 2 → 2.4 → 2.10` | The form blocks connection testing until the user corrects the endpoint URL. |
| Provider rejects key | `1 → 2 → 2.12 → 2.5 → 2.12 or 2.8` | The user can edit and retry verification, switch provider, or open advanced skip. |
| Provider unreachable | `1 → 2 → 2.12 → 2.6 → 2.12 or 2.8` | The user can retry after connection recovery, edit input, switch provider, or open advanced skip. |
| Unexpected verification result | `1 → 2 → 2.12 → 2.7 → 2.12 or 2.8` | The user remains in Provider Setup until retry succeeds or skip is confirmed. |
| Skip verification | `1 → 2 → 2.8 → 2.8.1 → 2.14 → 3 → 4 → 5` | User confirms the advanced skip branch, saves provider settings without verification, and continues onboarding. |
| Skip canceled | `1 → 2 → 2.8 → 2.10` | User cancels skip confirmation and returns to the editable provider form. |
| Provider save failure after verification | `1 → 2 → 2.1 → 2.14 → 2.9 → 2.14 or 2.10` | The verified state is preserved; user retries Continue or edits the form. |
| Provider save failure after skip | `1 → 2 → 2.8.1 → 2.14 → 2.9 → 2.14 or 2.10` | The skipped state is preserved; user retries Continue or edits the form. |
| Notification skipped | `1 → 2 → 2.1 or 2.8.1 → 2.14 → 3 → 3.2 → 4 → 5` | User chooses Maybe later; onboarding proceeds without requesting OS notification permission. |
| Notification requested and granted | `1 → 2 → 2.1 or 2.8.1 → 2.14 → 3 → 3.2 → 4 → 5` | User requests notifications; once the request resolves, onboarding proceeds. |
| Notification unresolved | `1 → 2 → 2.1 or 2.8.1 → 2.14 → 3 → 3.1 → 4 → 5` | Blocked, unknown, or non-granted permission does not block onboarding. |
| Completion handoff | `4 → 4.1 → 5` | Start chatting stores completion and opens the normal Chat surface. |
| Later launch | `4.2 → 5` | Completed onboarding causes later launches to open the normal Chat surface directly. |
| Reset onboarding | `4.3 → 1` | Clearing onboarding completion returns the next run to Welcome. |

## Main Path

### 1 Welcome

User entry: the user opens NoraHarness on a fresh install with no completed onboarding state.

User action: the user reviews the welcome message and clicks `Get started`.

Visible UI state:

- The centered onboarding shell is the first surface.
- Progress shows `Step 1 of 4`.
- The screen shows the product promise, three feature chips, and one primary action.

Client state change: onboarding is active; no provider, notification, or completion state has been committed.

Exit / next state: clicking `Get started` advances to `2 Provider Setup`.

### 2 Provider Setup

User entry: the user arrives from `1 Welcome`.

User action: the user chooses a provider, enters an API key or local endpoint, chooses a default model, then verifies the input or explicitly skips verification.

Visible UI state:

- Progress shows `Step 2 of 4`.
- Provider cards show Anthropic selected by default, plus OpenAI, Gemini, and `More Models`.
- The credential field, default model field, `Back`, `Verify key`, `Continue`, and sometimes `Skip verification (advanced)` are visible.
- `Continue` remains disabled until verification is satisfied or skipped.
- After verification succeeds, the default model field can be opened as a model selection list before continuing.

Client state change: the step owns selected provider, credential/local endpoint, selected model, returned models, verification state, skip state, submit state, and inline errors.

Exit / next state: successful provider save advances to `3 Notifications`. Back returns to `1 Welcome` without saving the provider.

### 3 Notifications

User entry: the user reaches this step after Provider Setup saves successfully.

User action: the user clicks `Turn on notifications` or `Maybe later`.

Visible UI state:

- Progress shows `Step 3 of 4`.
- The screen shows a large notification illustration, a sample `NoraHarness` notification, and the `Stay in the loop` title.
- The copy says notifications provide timely reply alerts from NoraHarness and message updates from connected channels.
- The two actions are `Maybe later` and `Turn on notifications`.
- Any operating-system permission surface is outside the product frame.

Client state change: `Turn on notifications` asks the desktop app to request notification permission. `Maybe later` skips the request.

Exit / next state: both paths advance to `4 Completion` after the chosen action resolves.

### 4 Completion

User entry: the user reaches this step after the notification branch resolves.

User action: the user reviews the connected provider/model summary and clicks `Start chatting`.

Visible UI state:

- Progress shows `Step 4 of 4`.
- The screen shows a circular check mark, the `You're all set` title, and the `Start chatting` primary action.
- The screen confirms that NoraHarness is connected to `Mistral` with `codestral-2508` as the default model.
- The screen points to `Settings → Channels` for Telegram or Discord.

Client state change: clicking `Start chatting` marks onboarding complete and ensures a draft Chat session exists if one is needed.

Exit / next state: clicking `Start chatting` opens `5 First Chat Surface`.

### 5 First Chat Surface

User entry: the user exits onboarding after completion.

User action: the user can type into the Chat composer or choose a suggestion.

Visible UI state:

- The normal Chat surface appears with sidebar, empty conversation area, suggestions, and composer.
- Settings and sign-in controls remain in the normal app chrome.

Client state change: onboarding is no longer the active shell; future launches skip onboarding until it is explicitly reset.

Exit / next state: later workspace creation, code mode, or runtime tasks belong to separate journeys.

## Branch Journeys

### 2.1 Verification success

Trigger: the user verifies a valid provider key or local endpoint successfully.

Visible UI state:

- Provider Setup stays on screen.
- The credential remains masked and shows `Verified`.
- `Verify again` remains available.
- `Continue` becomes enabled.
- The user can open `Default model` before continuing.

Allowed user actions: continue, verify again, open the model selector, edit input, switch provider, or go Back.

Client state change: the provider form records a verified state and keeps the selected provider/model editable until Continue saves it.

Recovery / next state: opening the model selector moves to `2.1.1 Verified model selection`; Continue proceeds through `2.14 Provider save, back, and advance`.

Blocks progress: no, because verification has succeeded.

### 2.1.1 Verified model selection

Trigger: verification succeeds and the user opens the `Default model` field.

Visible UI state:

- The credential remains masked and shows `Verified`.
- `Verify again` remains available.
- `Continue` is enabled.
- The default model field opens a model list with the current model checked.
- Returned model options appear in the dropdown, including provider-prefixed model names.

Allowed user actions: choose a model, close the dropdown, verify again, continue, edit inputs, switch provider, or go back.

Client state change: choosing a model updates the selected default model while keeping the provider verification state.

Recovery / next state: closing the list keeps the selected model in Provider Setup; `Continue` proceeds through `2.14 Provider save, back, and advance`.

Blocks progress: no, because the provider is already verified and `Continue` remains available.

### 2.2 Missing credential

Trigger: the user opens Provider Setup and tries to proceed before entering a provider key.

Visible UI state: Provider Setup remains on screen. The key field is empty, inline validation says a key is required, and verification/Continue are disabled.

Allowed user actions: enter a key, switch provider, change model, or go Back.

Client state change: the provider form stays in an invalid local state and does not start verification.

Recovery / next state: entering a valid-looking credential returns to `2.10 Provider entry and edits`.

Blocks progress: yes.

### 2.3 Remote key validation failed

Trigger: the user enters a remote provider key that fails basic local validation.

Visible UI state: Provider Setup shows the typed key, an inline message that the key does not look complete, disabled verification, and disabled Continue.

Allowed user actions: edit the key, switch provider, choose another model, or go Back.

Client state change: no verification call runs; the form stays editable.

Recovery / next state: correcting the key returns to `2.10 Provider entry and edits`.

Blocks progress: yes.

### 2.4 Local endpoint validation failed

Trigger: the user selects Local Models and enters an endpoint without a valid `http://` or `https://` prefix.

Visible UI state: Provider Setup changes to local endpoint language, shows the bad URL inline, disables `Test connection`, and keeps Continue disabled.

Allowed user actions: edit the URL, switch provider, type a model, or go Back.

Client state change: no local connection test runs; the local provider form stays invalid.

Recovery / next state: entering a valid URL returns to `2.10 Provider entry and edits`.

Blocks progress: yes.

### 2.5 Key rejected

Trigger: verification runs and the provider rejects the entered key.

Visible UI state: Provider Setup stays open with the credential preserved, an inline rejected-key message, `Verify key`, disabled Continue, and `Skip verification (advanced)`.

Allowed user actions: edit the key, retry verification, switch provider, go Back, or open the advanced skip branch.

Client state change: the form records verification failure but keeps the user-entered values.

Recovery / next state: successful retry returns to `2.12 Verification route`; skip follows `2.8 Skip confirmation`.

Blocks progress: yes unless the user later verifies successfully or confirms skip.

### 2.6 Provider unreachable

Trigger: verification runs but the provider cannot be reached.

Visible UI state: Provider Setup stays open with a network recovery message. The key remains in the field, and retry/skip options remain available.

Allowed user actions: retry verification, edit the input, switch provider, open skip confirmation, or go Back.

Client state change: the form records a provider-unreachable error and preserves typed values.

Recovery / next state: successful retry returns to `2.12 Verification route`; skip follows `2.8 Skip confirmation`.

Blocks progress: yes unless the user later verifies successfully or confirms skip.

### 2.7 Unexpected validation result

Trigger: verification runs but the result cannot be shown as a specific rejection or network failure.

Visible UI state: Provider Setup stays open with a generic verification error, preserved input, retry, and advanced skip.

Allowed user actions: retry verification, edit input, switch provider, open skip confirmation, or go Back.

Client state change: the form records a generic verification error and keeps the current provider settings editable.

Recovery / next state: successful retry returns to `2.12 Verification route`; skip follows `2.8 Skip confirmation`.

Blocks progress: yes unless the user later verifies successfully or confirms skip.

### 2.8 Skip confirmation

Trigger: the user clicks `Skip verification (advanced)` while input is otherwise valid.

Visible UI state: a confirmation dialog appears over Provider Setup and warns that the first message may fail if the key is wrong or the provider cannot be reached.

Allowed user actions: cancel the dialog or confirm `Skip verification`.

Client state change: cancel keeps the form unverified; confirm sets the verification-skipped state.

Recovery / next state: cancel returns to the unverified form; confirm advances to `2.8.1 Verification skipped`.

Blocks progress: yes until the user confirms skip or verifies successfully.

### 2.8.1 Verification skipped

Trigger: the user confirms the skip verification dialog.

Visible UI state: Provider Setup shows `Verification skipped`, keeps the credential value, and enables Continue.

Allowed user actions: Continue, edit input, switch provider, verify instead, or go Back.

Client state change: the form allows saving without a successful verification result.

Recovery / next state: Continue proceeds through `2.14 Provider save, back, and advance`.

Blocks progress: no.

### 2.9 Provider save failed

Trigger: the user clicks Continue after verification or skip, but saving provider settings fails.

Visible UI state: Provider Setup remains visible with the same values, the verified/skipped state preserved when applicable, and an inline save error.

Allowed user actions: retry Continue, edit fields, switch provider, verify again, or go Back.

Client state change: no provider settings are committed for the next step.

Recovery / next state: successful retry advances to `3 Notifications`; continued failure keeps the user in Provider Setup.

Blocks progress: yes until save succeeds.

### 3.1 Notification permission unresolved

Trigger: the user clicks `Turn on notifications`, and the desktop permission request resolves with unknown, blocked, or otherwise non-granted status.

Visible UI state: Notifications remains the product frame for this branch. The copy says notification settings can be changed later in System Settings.

Allowed user actions: continue through onboarding; no extra in-flow recovery is required.

Client state change: the permission result does not prevent onboarding from advancing.

Recovery / next state: the flow advances to `4 Completion`.

Blocks progress: no.

### 4.1 Start chatting handoff

Trigger: the user reaches Completion and clicks `Start chatting`.

Visible UI state: the Completion screen confirms the connected provider/model and shows the primary `Start chatting` action.

Allowed user actions: start chatting.

Client state change: onboarding completion is stored and a draft Chat session is prepared if needed.

Recovery / next state: the app opens `5 First Chat Surface`.

Blocks progress: no.

### 4.2 Later launch after completion

Trigger: the user launches the app after onboarding completion has already been stored.

Visible UI state: the normal Chat surface appears directly. Welcome and Provider Setup do not appear.

Allowed user actions: use Chat, switch modes, open Settings, or continue into other journeys.

Client state change: onboarding remains complete.

Recovery / next state: normal app usage continues outside Journey 1.

Blocks progress: no.

### 4.3 Reset onboarding

Trigger: onboarding completion is reset from a debug or recovery path.

Visible UI state: the next launch shows Welcome again.

Allowed user actions: start Journey 1 again from `Get started`.

Client state change: the local onboarding-complete state is cleared.

Recovery / next state: the user returns to `1 Welcome`.

Blocks progress: no.

## Detail States

### 2.10 Provider entry and edits

Trigger: the user enters Provider Setup, types a credential, edits a credential, changes model, or switches provider.

Visible UI state:

- Empty credential: `Verify key` and `Continue` are disabled.
- Invalid credential or URL: inline validation appears.
- Valid credential and model: `Verify key` or `Test connection` is enabled, but `Continue` is still disabled.

Allowed user actions: type or edit the key/endpoint, choose a different model, switch provider, open `More Models`, go `Back`, or verify when inputs are valid.

Client state change: switching provider clears the key, returned model list, verified state, skipped state, and inline error.

Recovery / next state: valid input leads to `2.12 Verification route`; invalid input stays in this state.

Blocks progress: yes, until verification is satisfied or skipped.

### 2.11 More Models and provider switching

Trigger: the user clicks `More Models` or selects a provider beyond the primary three.

Visible UI state:

- The provider grid expands in the same step.
- OpenRouter shows an additional provider dropdown inside the credential card.
- Local Models changes the credential input into a local service address and uses a free-text model field.

Allowed user actions: choose any visible provider, enter a key or endpoint, select or type a model, or return with `Back`.

Client state change: provider changes reset unsaved provider form state.

Recovery / next state: after valid provider-specific input, the user can verify through `2.12 Verification route` or use `2.13 Skip verification route`.

Blocks progress: yes.

### 2.12 Verification route

Trigger: the user clicks `Verify key`, or `Test connection` for Local Models.

Visible UI state:

- Remote provider verification shows `Checking...`.
- Local endpoint verification shows `Testing...`.
- Inputs are disabled while verification runs.
- Success shows `Verified`, enables `Continue`, and keeps the default model selectable; failure shows an inline recovery message.

Allowed user actions: wait, retry after failure, edit input, switch provider, or use the advanced skip branch when available.

Client state change: success records verified state and may replace model choices with returned models. Failure records an error state while preserving entered values.

Recovery / next state: success enables `Continue`, can open `2.1.1 Verified model selection`, and can move to `2.14 Provider save, back, and advance`; failure moves to `2.18 Provider verification failed`.

Blocks progress: yes while verifying; after success, no.

### 2.13 Skip verification route

Trigger: the user clicks `Skip verification (advanced)` after inputs are otherwise valid and verification is not satisfied.

Visible UI state:

- A confirmation warns that first-message errors may happen later if the key is wrong or the network blocks verification.
- Cancel returns to the unverified form.
- Confirm shows `Verification skipped` and enables `Continue`.

Allowed user actions: cancel, confirm skip, run verification instead, edit inputs, or continue after confirmed skip.

Client state change: confirmed skip sets the skip state and clears inline error. Cancel keeps the form unverified.

Recovery / next state: confirmed skip can proceed to `2.14 Provider save, back, and advance`; cancel returns to `2.10 Provider entry and edits`.

Blocks progress: yes until the user verifies successfully or confirms skip.

### 2.14 Provider save, back, and advance

Trigger: the user clicks `Back` or clicks `Continue` after verification is satisfied or skipped.

Visible UI state:

- Back is always visible at the bottom-left of Provider Setup.
- During save, `Continue` changes to `Saving and continuing...`.
- Save success advances to the next step.
- Save failure keeps the user on Provider Setup with an inline error.

Allowed user actions: go back before save, wait during save, retry after failure, edit inputs, switch provider, verify again, or use skip verification.

Client state change: Back does not save the provider. Save success persists provider id, provider name, key or local endpoint, enabled model, and enabled state for use in the rest of onboarding.

Recovery / next state: Back returns to `1 Welcome`; save success advances to `3 Notifications`; save failure stays in Provider Setup.

Blocks progress: yes while saving and after save failure.

### 3.2 Notification branches

Trigger: the user reaches Notifications.

Visible UI state:

- `Maybe later` and `Turn on notifications` are both visible.
- The helper says the user can change the permission later in System Settings.

Allowed user actions: skip notification permission for now or request it.

Client state change: `Maybe later` changes only the onboarding step. `Turn on notifications` asks the desktop app to request permission and may show a test notification.

Recovery / next state: both paths advance to `4 Completion` once the action resolves.

Blocks progress: no.

## State Language

### 2.15 Provider action language

Trigger: Provider Setup changes between idle, verifying, verified, skipped, and submitting states.

Visible UI state:

- `Verify key`: remote provider input is valid but not verified.
- `Checking...`: remote verification is running.
- `Verify again`: remote provider is verified and can be re-checked.
- `Test connection` / `Testing...` / `Test again`: Local Models endpoint language.
- `Saving and continuing...`: provider save is in progress.

Allowed user actions: click the available action, wait during in-progress states, or edit fields after returning to idle/error.

Client state change: action copy mirrors provider form state.

Recovery / next state: failed verification or save returns to editable form state with inline recovery language.

Blocks progress: copy alone does not block progress; the underlying state does.

### 2.16 Provider status language

Trigger: the provider form reaches verified, skipped, helper, or warning states.

Visible UI state:

- `Verified`: the key or local endpoint has passed verification.
- Default model menu: after verification, the selected model appears in a dropdown and can be changed before `Continue`.
- `Verification skipped`: the user confirmed the advanced skip branch.
- Settings helper: the user can change provider settings later.
- Skip warning: first-message errors may appear later if verification is skipped.

Allowed user actions: continue, verify again, edit input, or cancel/confirm skip depending on the active state.

Client state change: status copy is shown near the relevant credential field or helper line.

Recovery / next state: verified or skipped states enable save; warning/cancel keeps the user in Provider Setup.

Blocks progress: `Verified` and `Verification skipped` unblock progress; warning/cancel does not.

### 0.1 Whole-flow language

Trigger: the user moves through onboarding.

Visible UI state:

- Welcome uses `Get started`.
- Provider uses `Back`, `Verify key`, `Skip verification (advanced)`, and `Continue`.
- Notifications uses `Turn on notifications` and `Maybe later`.
- Completion uses `Start chatting`.

Allowed user actions: click the action shown in the current step.

Client state change: each action maps to the current step state without exposing internal implementation details.

Recovery / next state: welcome advances to provider, provider advances to notifications, notifications advances to completion, and completion opens Chat.

Blocks progress: only provider verification/save blocks progress.

## Errors And Recovery

### 2.17 Provider input validation summary

Trigger: the user enters a missing, malformed, or invalid credential before verification starts.

Visible UI state: this summary points to the full branch journey cards above for `2.2`, `2.3`, and `2.4`.

Allowed user actions: follow the recovery shown in the corresponding branch journey.

Client state change: invalid input is blocked before verification.

Recovery / next state: correcting the input returns to Provider Setup.

Blocks progress: yes until input is valid.

### 2.18 Provider verification failure summary

Trigger: verification runs and fails because the key is rejected, the provider is unreachable, or the result is unexpected.

Visible UI state: this summary points to the full branch journey cards above for `2.5`, `2.6`, and `2.7`.

Allowed user actions: retry, edit, switch provider, go Back, or open the skip branch when available.

Client state change: the form records a verification error and preserves user-entered values.

Recovery / next state: successful retry returns to verified Provider Setup; skip follows the skip confirmation branch.

Blocks progress: yes unless the user verifies successfully or confirms skip.

### 2.19 Provider skip and save recovery summary

Trigger: the user uses advanced skip or provider settings fail to save.

Visible UI state: this summary points to the full branch journey cards above for `2.8`, `2.8.1`, and `2.9`.

Allowed user actions: cancel skip, confirm skip, retry save, edit input, switch provider, or go Back.

Client state change: skip allows saving without verification; save failure preserves the current form values.

Recovery / next state: confirmed skip can continue; save failure stays in Provider Setup until retry succeeds.

Blocks progress: only save failure and unresolved skip confirmation block progress.

### 3.3 Notification recovery summary

Trigger: notification permission is blocked, unknown, or otherwise unresolved.

Visible UI state: this summary points to the full branch journey card above for `3.1`.

Allowed user actions: continue through onboarding.

Client state change: the permission result does not block onboarding.

Recovery / next state: the flow advances to Completion.

Blocks progress: no.

### 4.4 Completion and relaunch summary

Trigger: the user completes onboarding, launches later, or resets onboarding.

Visible UI state: this summary points to the full branch journey cards above for `4.1`, `4.2`, and `4.3`.

Allowed user actions: start chatting, use the main app on later launch, or re-enter onboarding after reset.

Client state change: completion stores the onboarding state; reset clears it.

Recovery / next state: normal launch opens Chat; reset returns to Welcome.

Blocks progress: no.

## Wireframe

[Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/01-first-launch/first-launch-onboarding.wireframe.html)
