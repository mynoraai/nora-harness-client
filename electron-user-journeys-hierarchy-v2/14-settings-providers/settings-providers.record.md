# Journey 14: Settings - Providers

This Markdown file is the text counterpart to `settings-providers.wireframe.html`. It documents the current implemented Settings Providers journey. Each section ID matches a card or state that can be represented in the wireframe.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches, detail states, state-language states, and recovery states use decimal IDs scoped to the step where the state starts. Sequential follow-up states use a deeper decimal level.

Main path states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 14 | Providers tab entry. | Opens Settings and chooses `Providers`, or follows a provider-missing prompt. | Settings remains in its normal shell with Providers selected. A loading state may appear before provider rows and the default model card settle. | Provider list and default model state begin loading for this tab. | `1.1`, then `2`, or `1.2` |
| `2` | Main path | Journey 14 | Provider list and default model review. | Reviews current providers, enabled model counts, and the default model card. | The tab shows `Default model`, a default-model selector when models are available, provider rows with enabled-model counts, edit controls, and `Add Provider`; empty state gives a direct add action. | Current provider rows, enabled model counts, and default model label are available for editing decisions. | `3`, `5.2`, `6`, or `7` |
| `3` | Main path | Journey 14 | Provider editor opens. | Clicks `Add Provider` or an edit control on an existing provider row. | The list view is replaced by an editor with `Back`, `Add Provider` or `Edit Provider`, provider selection, credential or endpoint fields, model controls, action buttons, and disabled-save guidance when needed. | A provider draft becomes active. | `3.1`, `3.2`, or `3.3` |
| `4` | Main path | Journey 14 | Provider verification or model loading starts. | Waits for automatic model loading or clicks `Load models` / `Reload models` after required fields are present. | The action shows loading. The editor remains visible and duplicate ambiguous actions are blocked while verification is pending. | The app attempts to reach the provider and refresh available model choices. | `4.1`, `4.2`, `4.3`, `4.4`, `4.5`, `4.6`, or `4.7` |
| `5` | Main path | Journey 14 | Enabled model selection. | Checks or unchecks provider model rows and uses the model filter when needed. | The enabled model count updates. Model rows show checked, unchecked, stale, or filtered states. | The draft enabled-model list changes locally. | `5.1`, `5.2`, `5.4`, `5.7`, or `3.3` |
| `6` | Main path | Journey 14 | Remove provider action. | Opens an existing provider and clicks `Remove provider`. | A destructive confirmation appears before the provider is removed. | The saved provider is not removed until the user confirms. | `6.1` or `6.2` |
| `7` | Main path | Journey 14 | Stable provider settings end state. | Leaves Settings, stays on the list, or returns to Chat or Code. | Provider configuration is either ready for runs or visibly blocked by missing authentication, failed verification, missing enabled models, save failure, or no default model. | Saved provider and default model state are the current source for later model use. | End |

Provider tab entry branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1.1` | Branch follow-up | `1 Providers tab entry` | Provider data is still loading. | Waits. | The tab shows a providers loading state instead of editable rows. | Provider and default model state are still being read. | `2` or `1.2` |
| `1.2` | Recovery state | `1 Providers tab entry` | Provider data could not be loaded. | Retries by reopening the tab or returning later. | The Providers tab remains visible but provider rows or default model state are unavailable, with error feedback. | Provider configuration is not changed. | `1` or `7` |

Add and edit provider branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Branch journey | `3 Provider editor opens` | Add Provider draft. | Starts a new provider. | The editor opens with a new draft and the first available provider option selected. Required fields are empty; verify, load, or save actions stay unavailable until enough information is present. | A new unsaved provider draft is created. | `3.3` |
| `3.2` | Branch journey | `3 Provider editor opens` | Edit Provider draft. | Edits an existing provider row. | The editor opens with existing provider values, existing enabled models, and current verification or cached model context where available. Provider identity is treated as the existing provider being edited. | A draft copy of the saved provider is active. | `3.3`, `5`, or `6` |
| `3.3` | Branch follow-up | `3 Provider editor opens` | Credential, base URL, region, or endpoint entry. | Types or changes provider credentials, base URL, region, local endpoint, or provider-specific input. | The edited field updates in place. Any previous verified status no longer counts as fresh once credential or endpoint fields change. | The draft becomes dirty and verification state returns to editable idle. | `4`, `4.2`, `4.3`, `5.7`, or `3.4` |
| `3.4` | Branch journey | `3 Provider editor opens` | Back from editor. | Clicks `Back`. | The Providers list returns. Unsaved draft edits are not shown as saved provider rows. | The active draft is closed without persisting new changes. | `2` |

Verify success and model selection branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.1` | Branch journey | `4 Provider verification or model loading starts` | Verification succeeds. | Reviews the verified result. | The editor shows `Verified`, refreshed model rows, and enabled-model controls. Save can become available once at least one model is enabled and other required fields are valid. | The draft records the provider as verified for the current credential or endpoint state and updates available model choices. | `4.1.1`, `5`, or `3.3` |
| `4.1.1` | Branch follow-up | `4.1 Verification succeeds` | Verified model selection list. | Opens the returned model list, filters it, or changes the selected model. | The verified state remains visible while returned provider models appear as selectable rows, with the current choice marked. | The selected model changes within the provider draft without losing the verified status. | `5` |

Verify validation and failure branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.2` | Branch journey | `4 Provider verification or model loading starts` | Missing required input. | Attempts verification or save without a required key, endpoint, provider value, or model value. | The editor stays in place. Required fields show inline guidance and the relevant action remains disabled or immediately blocked. | No provider verification starts and no provider configuration is saved. | `3.3` |
| `4.3` | Branch journey | `4 Provider verification or model loading starts` | Field shape validation fails. | Enters a key, base URL, local endpoint, or local model id that does not match the expected visible format. | Inline validation explains the field problem. Verification and Save remain unavailable until the field is corrected. | The draft remains invalid and does not use stale verified state. | `3.3` |
| `4.4` | Branch journey | `4 Provider verification or model loading starts` | Provider service rejects the credential or endpoint request. | Reads the provider error and corrects credentials or endpoint details. | The editor shows an inline validation error. Typed values and selected provider remain visible for retry. | Verification fails; the draft is kept for correction. | `3.3` or `4` |
| `4.5` | Branch journey | `4 Provider verification or model loading starts` | Provider network is unavailable, timed out, or cannot be reached. | Waits, changes network or endpoint, then retries. | The editor shows a reachability or loading failure near the provider action. Existing draft fields remain intact. | Verification fails because the provider could not be reached; saved provider state is unchanged. | `3.3` or `4` |
| `4.6` | Branch journey | `4 Provider verification or model loading starts` | No usable models returned. | Reviews the empty or unavailable catalog state, retries, or edits the provider. | The editor shows an inline model-loading problem. Cached or previously enabled models may stay visible only when the UI identifies them as existing choices rather than fresh verification. | The available model list is not refreshed into a usable verified catalog. | `3.3`, `4`, or `5.1` |
| `4.7` | Branch journey | `4 Provider verification or model loading starts` | Unexpected verification failure. | Retries, edits fields, switches provider, or leaves without saving. | The editor shows a generic inline error while preserving the draft. | Verification remains failed and provider configuration is not changed. | `3.3` or `4` |

Enabled models, default model, and save branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `5.1` | Branch journey | `5 Enabled model selection` | No enabled models. | Unchecks all models or reaches the editor before any model is enabled. | Save is disabled and the disabled reason explains that at least one model must be enabled. | The provider draft cannot be saved as runnable. | `5` |
| `5.2` | Branch journey | `5 Enabled model selection` | Default model selection. | Opens `Default model` from the Providers list and chooses an enabled model. | The default model card opens a selector of enabled models. The chosen label appears on the default model card after the change succeeds. | The default model preference is updated for future sessions and model lists refresh when needed. | `2` or `7` |
| `5.3` | Branch journey | `5 Enabled model selection` | Default model is missing, stale, or no longer enabled. | Reads the warning and selects another enabled model, or returns to provider editing. | The default model card indicates that the current default is unavailable or that no default model is selected. | The app does not silently treat the stale default as usable. | `5.2`, `3`, or `7` |
| `5.4` | Branch journey | `5 Enabled model selection` | Save provider in progress. | Clicks `Save` on a valid provider draft. | The Save action shows a saving state and duplicate saves are disabled. The editor remains visible. | The provider draft is being persisted. | `5.5` or `5.6` |
| `5.5` | Branch follow-up | `5.4 Save provider in progress` | Save succeeds. | Reviews the returned provider list. | The editor closes, the Providers list updates, enabled counts refresh, and success feedback can appear. | Saved provider configuration becomes current and available to model selection. | `2`, `5.2`, or `7` |
| `5.6` | Branch follow-up | `5.4 Save provider in progress` | Save fails. | Reads the failure, retries Save, edits the draft, or backs out. | The editor stays open with the draft preserved and error feedback visible. | Saved provider configuration remains unchanged. | `5.4`, `3.3`, or `3.4` |
| `5.7` | Branch journey | `5 Enabled model selection` | Local model entry. | Enters or edits a local model id instead of choosing from a remote model catalog. | The editor shows local model input rather than remote model rows. Save depends on a usable local model id and valid endpoint details. | The local model id becomes the enabled model choice in the draft. | `5`, `5.4`, `4.2`, or `4.3` |

Delete confirmation, deleted provider, and fallback branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `6.1` | Branch journey | `6 Remove provider action` | Delete confirmation. | Confirms removal. | The confirmation closes and the editor enters a removing state or waits for removal to finish. | Provider removal begins. | `6.1.1` or `6.3` |
| `6.1.1` | Branch follow-up | `6.1 Delete confirmation` | Provider deleted. | Reviews the Providers list after removal. | The provider row is gone, the editor closes, and the list/default model card refreshes. | The provider configuration is removed from saved settings. | `6.1.1.1`, `2`, or `7` |
| `6.1.1.1` | Branch follow-up | `6.1.1 Provider deleted` | Default model fallback or reselection after deletion. | Selects a replacement default model if the removed provider owned the previous default. | The default model card either shows a valid fallback, a missing/default warning, or a selector for another enabled model. | The previous default is cleared or replaced so later sessions do not point at a removed provider model. | `5.2`, `2`, or `7` |
| `6.2` | Branch journey | `6 Remove provider action` | Delete canceled. | Cancels the confirmation. | The user returns to the provider editor and the provider remains listed. | Saved provider configuration is unchanged. | `3.2` |
| `6.3` | Branch follow-up | `6.1 Delete confirmation` | Delete fails. | Reads the failure, retries removal, backs out, or leaves the provider unchanged. | The editor or list shows removal failure feedback and keeps the provider available. | Saved provider configuration remains unchanged. | `6`, `3.2`, or `2` |

Detail states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Detail state | `2 Provider list and default model review` | Provider list, empty state, and default model surface details. | Reviews rows, default model status, empty list guidance, or edit/add entry points. | The list and default model card show ready, empty, stale, or selectable states. | The list surface exposes saved provider and default model state without changing it. | `3`, `5.2`, `6`, or `7` |
| `3.5` | Detail state | `3 Provider editor opens` | Provider editor field and draft behavior. | Edits credentials, endpoint, provider choice, or Back. | Dirty fields remain visible; stale verification is cleared when credential or endpoint input changes. | The provider draft changes locally until Save succeeds or Back closes it. | `3.3`, `4`, `5.7`, or `3.4` |
| `4.8` | Detail state | `4 Provider verification or model loading starts` | Verification lifecycle and returned model catalog behavior. | Starts loading, waits, opens returned model choices, retries, or edits input. | Loading, Verified, returned model dropdown, empty catalog, and inline failure states stay in the editor. | Verification state and available model choices update only for the current draft. | `4.1`, `4.1.1`, `4.2`, `4.3`, `4.4`, `4.5`, `4.6`, or `4.7` |
| `5.8` | Detail state | `5 Enabled model selection` | Enabled models, save, and default model behavior. | Checks models, opens default selector, saves, retries save, or corrects no-model state. | Enabled count, disabled save reason, saving state, success feedback, and stale default warning are visible. | Draft enabled models persist only after Save; default model changes after the list selection succeeds. | `5.1`, `5.2`, `5.3`, `5.4`, `5.5`, or `5.6` |
| `6.4` | Detail state | `6 Remove provider action` | Remove confirmation, deleted provider, and default fallback behavior. | Opens remove confirmation, confirms, cancels, retries, or chooses a fallback default. | Confirmation, removing/deleted feedback, canceled state, failure state, and default fallback selector are visible. | Saved provider state changes only after confirmed removal succeeds. | `6.1`, `6.1.1`, `6.1.1.1`, `6.2`, or `6.3` |

State language:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Shared provider status and action language. | Reads or acts on provider setup labels. | Labels include `Providers`, `Default model`, `Add Provider`, `Edit Provider`, `Back`, `Load models`, `Reload models`, `Loading...`, `Verified`, `Save`, `Saving`, `Remove provider`, and disabled-save reasons. | Visible labels stay aligned with provider list, editor, verification, save, and removal state. | Any state |

Errors and recovery:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.9` | Recovery summary | `4 Provider verification or model loading starts` | Verification validation and failure recovery summary. | Corrects missing or malformed input, retries provider loading, or edits provider details. | The editor keeps the draft visible while validation, rejection, network, empty-catalog, or unexpected failures are corrected. | No saved provider state changes until a later successful Save. | `3.3`, `4`, or `7` |
| `5.9` | Recovery summary | `5 Enabled model selection` | Enabled model, default model, and save recovery summary. | Enables a model, selects a replacement default, retries Save, or backs out. | Disabled save, stale default, and save-failure feedback remain visible near the affected model or action. | Draft and saved state remain separate until Save or default selection succeeds. | `5`, `5.2`, `5.4`, `3.4`, or `7` |
| `6.5` | Recovery summary | `6 Remove provider action` | Delete, cancel, failure, and default fallback recovery summary. | Cancels removal, retries deletion, or selects a replacement default after deletion. | The provider remains available unless confirmed removal succeeds; fallback default issues remain visible. | Saved provider/default state changes only after confirmed deletion or default replacement succeeds. | `6`, `3.2`, `5.2`, `2`, or `7` |
| `0.2` | Recovery summary | Whole journey | Correct, retry, cancel, or leave recovery pattern. | Corrects input, retries verification or save, cancels destructive actions, backs out, or leaves Settings. | The active surface preserves user-entered draft values unless the user explicitly closes the editor or confirms deletion. | The app changes saved provider state only after a successful save, default selection, or confirmed removal. | Active state, `2`, or `7` |

## Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Review configured providers | `1 -> 1.1 -> 2 -> 7` | The user opens Providers, reviews current provider/default state, and leaves without changing configuration. |
| Empty Providers add path | `1 -> 1.1 -> 2 -> 3 -> 3.1 -> 3.3 -> 4 -> 4.1 -> 4.1.1 -> 5 -> 5.4 -> 5.5 -> 2 -> 5.2 -> 7` | The user adds a provider, verifies it, reviews returned models, enables models, saves, then selects a default model. |
| Edit provider and save enabled models | `1 -> 1.1 -> 2 -> 3 -> 3.2 -> 5 -> 5.4 -> 5.5 -> 2 -> 7` | The user edits an existing provider's model selections and returns to the refreshed list. |
| Local provider edit path | `1 -> 1.1 -> 2 -> 3 -> 3.2 -> 3.3 -> 5.7 -> 5 -> 5.4 -> 5.5 -> 2 -> 5.2 -> 7` | The user edits a local provider, enters a local model id, saves it, and can choose it as default. |
| Missing input recovery | `1 -> 1.1 -> 2 -> 3 -> 3.1 -> 3.3 -> 4.2 -> 3.3 -> 4 -> 4.1 -> 5 -> 5.4 -> 5.5 -> 2 -> 7` | The editor blocks verification or saving until the user fills required fields, then the provider can be verified and saved. |
| Field shape validation recovery | `1 -> 1.1 -> 2 -> 3 -> 3.1 -> 3.3 -> 4.3 -> 3.3 -> 4 -> 4.1 -> 4.1.1 -> 5 -> 5.4 -> 5.5 -> 2 -> 7` | The user corrects a malformed key, URL, endpoint, or local model entry before verification succeeds. |
| Provider service rejects credential | `1 -> 1.1 -> 2 -> 3 -> 3.1 -> 3.3 -> 4 -> 4.4 -> 3.3 -> 4 -> 4.1 -> 4.1.1 -> 5 -> 5.4 -> 5.5 -> 2 -> 7` | The service-side rejection stays inline; after the user fixes credentials, verification and save can succeed. |
| Network unreachable then retry | `1 -> 1.1 -> 2 -> 3 -> 3.1 -> 3.3 -> 4 -> 4.5 -> 4 -> 4.1 -> 4.1.1 -> 5 -> 5.4 -> 5.5 -> 2 -> 7` | The editor preserves the draft through a reachability failure and allows retry when network or endpoint conditions recover. |
| No usable model catalog | `1 -> 1.1 -> 2 -> 3 -> 3.1 -> 3.3 -> 4 -> 4.6 -> 3.3 -> 4 -> 4.1 -> 4.1.1 -> 5 -> 5.4 -> 5.5 -> 2 -> 7` | The user edits or retries until usable model choices are available. |
| Unexpected verification failure | `1 -> 1.1 -> 2 -> 3 -> 3.1 -> 3.3 -> 4 -> 4.7 -> 3.3 -> 4 -> 4.1 -> 5 -> 5.4 -> 5.5 -> 2 -> 7` | The generic failure does not save a provider; the user can edit and retry. |
| No enabled models blocks save | `1 -> 1.1 -> 2 -> 3 -> 3.2 -> 5 -> 5.1 -> 5 -> 5.4 -> 5.5 -> 2 -> 7` | Save remains blocked until the user enables at least one model. |
| Default model missing recovery | `1 -> 1.1 -> 2 -> 5.3 -> 5.2 -> 2 -> 7` | The user replaces a stale or missing default with an enabled model. |
| Save failure retry | `1 -> 1.1 -> 2 -> 3 -> 3.2 -> 5 -> 5.4 -> 5.6 -> 5.4 -> 5.5 -> 2 -> 7` | The draft stays open after a save failure and later persists after retry. |
| Back out of editor | `1 -> 1.1 -> 2 -> 3 -> 3.1 -> 3.3 -> 3.4 -> 2 -> 7` | The user exits the editor without saving draft changes. |
| Delete provider canceled | `1 -> 1.1 -> 2 -> 3 -> 3.2 -> 6 -> 6.2 -> 3.2 -> 3.4 -> 2 -> 7` | The confirmation is canceled and the provider remains unchanged. |
| Delete provider with default fallback | `1 -> 1.1 -> 2 -> 3 -> 3.2 -> 6 -> 6.1 -> 6.1.1 -> 6.1.1.1 -> 5.2 -> 2 -> 7` | The provider is removed, and the user resolves any default model that pointed at the removed provider. |
| Delete failure recovery | `1 -> 1.1 -> 2 -> 3 -> 3.2 -> 6 -> 6.1 -> 6.3 -> 6 -> 6.1 -> 6.1.1 -> 2 -> 7` | A failed removal keeps the provider available until a later confirmed removal succeeds. |
| Provider list load failure | `1 -> 1.2 -> 1 -> 1.1 -> 2 -> 7` | The tab remains recoverable; reopening or retrying later can return to the provider list. |

## Main Path

### 1 Providers Tab Entry

User entry: the user opens Settings and selects `Providers`, or follows a provider-missing prompt from Chat or Code.

User action: the user waits for the tab to load.

Visible UI state:

- The Settings shell remains visible with Providers selected.
- Provider rows and the default model card may be temporarily replaced by a loading state.
- The user is not moved into onboarding; this is the Settings provider surface.

Client state change: provider list and default model state begin loading for the tab.

Exit / next state: loading moves through `1.1 Provider Data Loading`, then `2 Provider List And Default Model Review`; load failure moves to `1.2 Provider Data Load Failed`.

### 2 Provider List And Default Model Review

User entry: provider list and default model state are available.

User action: the user reviews configured providers, enabled-model counts, default model status, and available actions.

Visible UI state:

- The tab shows a `Default model` card.
- Existing provider rows show provider names, enabled-model counts, and edit controls.
- The tab offers `Add Provider`.
- If there are no providers, the tab still gives a direct path to add one.
- If the default model is missing or no enabled models exist, the state is visible instead of silently treated as ready.

Client state change: current provider rows, enabled model counts, and default model label are ready for list actions.

Exit / next state: `Add Provider` or edit moves to `3 Provider Editor Opens`; changing the default model moves to `5.2 Default Model Selection`; removing a provider starts from `6 Remove Provider Action`; leaving the tab reaches `7 Stable Provider Settings End State`.

### 3 Provider Editor Opens

User entry: the user clicks `Add Provider` or edits an existing provider row.

User action: the user creates a new provider draft or edits an existing provider draft.

Visible UI state:

- The editor shows `Back`.
- The title is `Add Provider` or `Edit Provider`.
- The editor shows provider selection, credential or endpoint inputs, optional provider-specific fields, model controls, action buttons, and disabled-save guidance when needed.
- Save and verification actions are unavailable until the visible field requirements are satisfied.

Client state change: a provider draft becomes active and can differ from saved provider state.

Exit / next state: new providers use `3.1 Add Provider Draft`; existing providers use `3.2 Edit Provider Draft`; editing fields moves to `3.3 Credential, Base URL, Region, Or Endpoint Entry`; Back moves to `3.4 Back From Editor`.

### 4 Provider Verification Or Model Loading Starts

User entry: the editor has enough required input for provider verification or model loading.

User action: the user waits for automatic model loading or clicks `Load models` / `Reload models`.

Visible UI state:

- The provider action enters a loading state when a remote catalog is being fetched.
- The editor remains visible.
- Duplicate or ambiguous verification and save actions are blocked while verification is pending.
- Draft fields remain readable while the request is in progress.

Client state change: the app attempts to validate the current remote provider details and refresh available model choices.

Exit / next state: success moves to `4.1 Verification Succeeds`; missing input, shape validation, service rejection, network failure, empty catalog, or unexpected failure move to their matching `4.x` branch.

### 5 Enabled Model Selection

User entry: the provider editor has visible model choices or a local model entry path.

User action: the user filters model rows, checks or unchecks models, and prepares the provider for saving.

Visible UI state:

- The enabled-model count updates as rows are checked or unchecked.
- Rows can be checked, unchecked, filtered, or marked as stale when applicable.
- Save is available only when the provider draft is valid and at least one model is enabled.

Client state change: the draft enabled-model list changes locally until Save succeeds.

Exit / next state: zero enabled models moves to `5.1 No Enabled Models`; choosing a default from the list view moves to `5.2 Default Model Selection`; saving moves through `5.4 Save Provider In Progress`.

### 6 Remove Provider Action

User entry: the user is editing an existing provider.

User action: the user clicks `Remove provider`.

Visible UI state:

- A destructive confirmation appears before removal.
- The provider remains saved and listed until the user confirms and removal succeeds.

Client state change: no saved provider state changes before confirmation.

Exit / next state: confirming moves to `6.1 Delete Confirmation`; canceling moves to `6.2 Delete Canceled`.

### 7 Stable Provider Settings End State

User entry: the user has completed, deferred, or abandoned provider settings work.

User action: the user leaves Settings, stays on the Providers list, or returns to Chat or Code.

Visible UI state:

- Provider configuration is ready when at least one provider/model path is saved and a usable default exists where needed.
- If setup is not ready, the tab clearly shows missing authentication, failed verification, missing enabled models, save/remove failure, or missing default model.

Client state change: saved provider and default model state are the current source for later Chat and Code model use.

Exit / next state: end.

## Branch Journeys

### 1.1 Provider Data Loading

Trigger: the Providers tab has opened but provider and default model state have not finished loading.

Visible UI state:

- The Providers tab stays selected.
- A loading state appears in place of ready provider rows or default model choices.

Allowed user actions: wait, leave Settings, or return later.

Client state change: provider and default model state are still being read.

Recovery / next state: loaded state moves to `2 Provider List And Default Model Review`; failure moves to `1.2 Provider Data Load Failed`.

Blocks progress: temporarily for provider editing and default model selection.

### 1.2 Provider Data Load Failed

Trigger: the Providers tab cannot load provider rows or default model state.

Visible UI state:

- The Settings shell remains visible.
- Provider rows or default model choices are unavailable.
- Error feedback indicates that provider state could not be loaded.

Allowed user actions: leave Settings, reopen the tab, or retry later.

Client state change: saved provider configuration is not changed.

Recovery / next state: retrying returns to `1 Providers Tab Entry`; leaving rests at `7 Stable Provider Settings End State`.

Blocks progress: yes for provider editing and default model selection.

### 3.1 Add Provider Draft

Trigger: the user clicks `Add Provider`.

Visible UI state:

- The editor title is `Add Provider`.
- A new provider draft starts with empty required fields.
- Verify, load, or save controls are disabled until required fields are present.

Allowed user actions: choose provider, enter credentials or endpoint details, use Back, or leave Settings.

Client state change: a new unsaved provider draft is active.

Recovery / next state: entering fields moves to `3.3 Credential, Base URL, Region, Or Endpoint Entry`.

Blocks progress: yes until required fields and enabled model requirements are satisfied.

### 3.2 Edit Provider Draft

Trigger: the user clicks edit on an existing provider row.

Visible UI state:

- The editor title is `Edit Provider`.
- Existing credential, endpoint, enabled model, and cached or stale model context appears where available.
- Remove provider is available for the existing provider.

Allowed user actions: edit fields, enable or disable models, save, remove provider, go Back, or leave Settings.

Client state change: a draft copy of the saved provider is active.

Recovery / next state: editing fields moves to `3.3 Credential, Base URL, Region, Or Endpoint Entry`; model changes move to `5 Enabled Model Selection`; removal moves to `6 Remove Provider Action`.

Blocks progress: no unless the user needs this provider change before running a model.

### 3.3 Credential, Base URL, Region, Or Endpoint Entry

Trigger: the user types or changes provider credentials, base URL, region, local endpoint, or provider-specific input.

Visible UI state:

- Edited values update in place.
- Inline validation appears when required values are missing or visibly malformed.
- Any previous `Verified` state is no longer treated as fresh after credential or endpoint changes.

Allowed user actions: continue editing, verify or load models once available, switch provider where allowed, or go Back.

Client state change: the draft becomes dirty and verification state returns to editable idle.

Recovery / next state: valid input can move to `4 Provider Verification Or Model Loading Starts`; invalid input moves to `4.2 Missing Required Input` or `4.3 Field Shape Validation Fails`.

Blocks progress: yes when required fields are missing or malformed.

### 3.4 Back From Editor

Trigger: the user clicks `Back` from the provider editor.

Visible UI state:

- The Providers list returns.
- Unsaved draft changes are not shown as saved provider rows.

Allowed user actions: add another provider, edit a provider, choose default model, or leave Settings.

Client state change: the active draft is closed without saving new changes.

Recovery / next state: returns to `2 Provider List And Default Model Review`.

Blocks progress: no.

### 4.1 Verification Succeeds

Trigger: provider verification or model loading succeeds for the current credential or endpoint state.

Visible UI state:

- The editor shows `Verified`.
- Refreshed model rows appear for providers that return a model catalog.
- Save can become available once at least one model is enabled and all visible requirements are valid.

Allowed user actions: open model selection, enable or disable models, save, verify again, edit fields, switch provider where allowed, or go Back.

Client state change: the draft records verified status for the current field values and updates available model choices.

Recovery / next state: opening returned model choices moves to `4.1.1 Verified Model Selection List`; model enabling moves to `5 Enabled Model Selection`.

Blocks progress: no once save requirements are met.

### 4.1.1 Verified Model Selection List

Trigger: verification succeeds and the user opens or reviews the returned model list.

Visible UI state:

- `Verified` remains visible.
- Returned models appear as selectable rows.
- The current model choice is marked.
- Filtering or scrolling does not remove the verified status.

Allowed user actions: choose a model, enable or disable model rows, close the list, verify again, edit fields, save, or go Back.

Client state change: the selected model changes within the draft while verification remains valid for the current credentials.

Recovery / next state: selecting or closing the list proceeds to `5 Enabled Model Selection`.

Blocks progress: no.

### 4.2 Missing Required Input

Trigger: the user attempts verification or saving without a required key, endpoint, provider value, or model value.

Visible UI state:

- The editor stays in place.
- Required fields show inline guidance.
- Verification or Save remains disabled or blocked.

Allowed user actions: fill the missing field, change provider, go Back, or leave Settings.

Client state change: no verification starts and saved provider configuration is unchanged.

Recovery / next state: correcting the field returns to `3.3 Credential, Base URL, Region, Or Endpoint Entry`.

Blocks progress: yes.

### 4.3 Field Shape Validation Fails

Trigger: a key, base URL, local endpoint, or local model id is present but visibly malformed.

Visible UI state:

- The field remains editable.
- Inline validation explains the format problem.
- Verification and Save remain unavailable until the field is corrected.

Allowed user actions: correct the field, switch provider where allowed, go Back, or leave Settings.

Client state change: the draft remains invalid and stale verified state is not reused.

Recovery / next state: correcting the field returns to `3.3 Credential, Base URL, Region, Or Endpoint Entry`.

Blocks progress: yes.

### 4.4 Provider Service Rejects The Credential Or Endpoint Request

Trigger: the provider service responds that the credential, endpoint, or request is not accepted.

Visible UI state:

- The editor shows an inline provider validation error.
- Typed values and the selected provider remain visible for correction.
- Save does not claim the provider is verified.

Allowed user actions: correct the credential or endpoint, retry verification, switch provider, go Back, or leave Settings.

Client state change: verification fails and saved provider configuration is unchanged.

Recovery / next state: correction returns to `3.3 Credential, Base URL, Region, Or Endpoint Entry`; retry moves to `4 Provider Verification Or Model Loading Starts`.

Blocks progress: yes for saving this provider as verified.

### 4.5 Provider Network Is Unavailable

Trigger: verification cannot reach the provider, times out, or the endpoint is unavailable.

Visible UI state:

- The editor shows a reachability or model-loading failure near the provider action.
- The draft remains visible and editable.
- The user can retry without re-entering all fields.

Allowed user actions: retry, change network or endpoint details, edit fields, go Back, or leave Settings.

Client state change: verification fails due to reachability; saved provider configuration is unchanged.

Recovery / next state: retry moves to `4 Provider Verification Or Model Loading Starts`; editing moves to `3.3 Credential, Base URL, Region, Or Endpoint Entry`.

Blocks progress: yes for fresh verification.

### 4.6 No Usable Models Returned

Trigger: verification returns no usable model rows, or the model catalog cannot be used for provider setup.

Visible UI state:

- The editor shows an inline model-loading problem.
- Existing cached or previously enabled models may remain visible only as existing choices, not as a fresh verified catalog.
- Save remains blocked when no enabled model is available.

Allowed user actions: retry model loading, edit provider details, enable an available existing model if allowed, go Back, or leave Settings.

Client state change: the available model list is not refreshed into a usable verified set.

Recovery / next state: retry moves to `4 Provider Verification Or Model Loading Starts`; editing moves to `3.3 Credential, Base URL, Region, Or Endpoint Entry`; no enabled models moves to `5.1 No Enabled Models`.

Blocks progress: yes when no enabled model can be saved.

### 4.7 Unexpected Verification Failure

Trigger: verification fails with a generic or unexpected result.

Visible UI state:

- The editor shows a generic inline error.
- The draft stays visible.
- Save does not claim successful verification.

Allowed user actions: retry, edit fields, switch provider where allowed, go Back, or leave Settings.

Client state change: verification remains failed and saved provider configuration is unchanged.

Recovery / next state: editing moves to `3.3 Credential, Base URL, Region, Or Endpoint Entry`; retry moves to `4 Provider Verification Or Model Loading Starts`.

Blocks progress: yes for verified setup.

### 5.1 No Enabled Models

Trigger: the draft has zero enabled models.

Visible UI state:

- Save is disabled.
- Disabled-save guidance explains that at least one model must be enabled.
- Model rows remain available for selection when any model choices exist.

Allowed user actions: check at least one model, reload models, edit provider details, or go Back.

Client state change: the draft cannot be saved as a runnable provider.

Recovery / next state: enabling a model returns to `5 Enabled Model Selection`.

Blocks progress: yes for saving.

### 5.2 Default Model Selection

Trigger: the user opens the `Default model` selector from the Providers list while enabled models are available.

Visible UI state:

- The default model card opens or displays a selector of enabled models.
- The selected default label updates after the change succeeds.
- If no enabled models exist, the selector is unavailable or empty.

Allowed user actions: choose a default model, close the selector, add or edit providers, or leave Settings.

Client state change: the default model preference is updated for future sessions and model lists refresh when needed.

Recovery / next state: success returns to `2 Provider List And Default Model Review` or `7 Stable Provider Settings End State`.

Blocks progress: no when at least one enabled model exists; yes for choosing a usable default when none exists.

### 5.3 Default Model Is Missing, Stale, Or No Longer Enabled

Trigger: the current default model points to a removed, disabled, or unavailable model, or no default model exists.

Visible UI state:

- The default model card indicates that the current default is unavailable or missing.
- The user can choose a replacement when enabled models exist.

Allowed user actions: select a replacement default, add or edit provider models, or leave Settings with the unresolved state visible.

Client state change: the stale default is not treated as a usable hidden selection.

Recovery / next state: selecting a replacement moves to `5.2 Default Model Selection`; editing providers moves to `3 Provider Editor Opens`; leaving rests at `7 Stable Provider Settings End State`.

Blocks progress: yes for flows that require a default model before sending or creating new sessions.

### 5.4 Save Provider In Progress

Trigger: the user clicks `Save` on a valid provider draft.

Visible UI state:

- The Save action shows a saving state.
- Duplicate saves are disabled.
- The editor remains visible until save resolves.

Allowed user actions: wait.

Client state change: the provider draft is being persisted.

Recovery / next state: success moves to `5.5 Save Succeeds`; failure moves to `5.6 Save Fails`.

Blocks progress: temporarily.

### 5.5 Save Succeeds

Trigger: provider save completes successfully.

Visible UI state:

- The editor closes.
- The Providers list refreshes.
- Enabled model counts and default model availability update.
- Success feedback can appear.

Allowed user actions: choose a default model, edit another provider, add a provider, leave Settings, or return to Chat or Code.

Client state change: saved provider configuration becomes current and available to model selection.

Recovery / next state: returns to `2 Provider List And Default Model Review`, `5.2 Default Model Selection`, or `7 Stable Provider Settings End State`.

Blocks progress: no.

### 5.6 Save Fails

Trigger: the provider draft passes visible validation but cannot be saved.

Visible UI state:

- The editor stays open.
- The draft values are preserved.
- Error feedback explains that saving did not complete.

Allowed user actions: retry Save, edit the draft, go Back, or leave Settings.

Client state change: saved provider configuration remains unchanged.

Recovery / next state: retry moves to `5.4 Save Provider In Progress`; editing moves to `3.3 Credential, Base URL, Region, Or Endpoint Entry`; Back moves to `3.4 Back From Editor`.

Blocks progress: yes for persisting the intended provider change.

### 5.7 Local Model Entry

Trigger: an existing local provider or local-style provider setup uses manual model id entry instead of a remote returned catalog.

Visible UI state:

- The editor shows local model id input rather than remote model rows.
- Save remains dependent on a usable local model id and valid endpoint details.

Allowed user actions: enter a local model id, edit endpoint details, save when valid, or go Back.

Client state change: the local model id becomes the enabled model choice in the draft.

Recovery / next state: valid local model entry returns to `5 Enabled Model Selection` or `5.4 Save Provider In Progress`; malformed or missing input moves to `4.2 Missing Required Input` or `4.3 Field Shape Validation Fails`.

Blocks progress: yes until local model requirements are satisfied.

### 6.1 Delete Confirmation

Trigger: the user confirms provider removal from the destructive confirmation.

Visible UI state:

- The confirmation closes.
- The editor may show a removing state while the removal completes.

Allowed user actions: wait.

Client state change: provider removal begins.

Recovery / next state: success moves to `6.1.1 Provider Deleted`; failure moves to `6.3 Delete Fails`.

Blocks progress: temporarily.

### 6.1.1 Provider Deleted

Trigger: provider removal completes successfully.

Visible UI state:

- The provider row is gone.
- The editor closes.
- The provider list and default model card refresh.

Allowed user actions: select another default model, add or edit providers, leave Settings, or return to Chat or Code.

Client state change: the provider configuration is removed from saved settings.

Recovery / next state: if the removed provider owned the default model, move to `6.1.1.1 Default Model Fallback Or Reselection After Deletion`; otherwise return to `2 Provider List And Default Model Review` or `7 Stable Provider Settings End State`.

Blocks progress: no unless the removed provider was needed for the current default or only enabled model.

### 6.1.1.1 Default Model Fallback Or Reselection After Deletion

Trigger: the removed provider owned the previous default model or left no usable default.

Visible UI state:

- The default model card shows a valid fallback, an unavailable/default warning, or a selector for another enabled model.
- The removed provider model is not shown as a usable default.

Allowed user actions: choose another default model, add or edit providers, or leave the unresolved state visible.

Client state change: the previous default is cleared or replaced so future sessions do not point at a removed provider model.

Recovery / next state: selecting a replacement moves to `5.2 Default Model Selection`; otherwise returns to `2 Provider List And Default Model Review` or `7 Stable Provider Settings End State`.

Blocks progress: yes for flows that require a usable default model.

### 6.2 Delete Canceled

Trigger: the user cancels the remove-provider confirmation.

Visible UI state:

- The provider editor returns.
- The provider remains saved and listed.

Allowed user actions: continue editing, save changes, attempt removal again, go Back, or leave Settings.

Client state change: saved provider configuration is unchanged.

Recovery / next state: returns to `3.2 Edit Provider Draft`.

Blocks progress: no.

### 6.3 Delete Fails

Trigger: removal fails after the user confirms.

Visible UI state:

- Failure feedback appears.
- The provider remains available.
- The user can retry or return to the list.

Allowed user actions: retry removal, go Back, edit the provider, leave Settings, or return later.

Client state change: saved provider configuration remains unchanged.

Recovery / next state: retry moves to `6 Remove Provider Action`; editing returns to `3.2 Edit Provider Draft`; list return moves to `2 Provider List And Default Model Review`.

Blocks progress: yes for completing removal.

## Detail States

### 2.1 Provider List, Empty State, And Default Model Surface Details

Trigger: the user reaches the Providers list after loading finishes.

Visible UI state:

- The default model card shows a selected model, a missing/default warning, or a selectable list when enabled models are available.
- Provider rows show provider names, enabled-model counts, and edit controls.
- Empty provider state still gives a direct `Add Provider` action.

Allowed user actions: add a provider, edit an existing provider, open default model selection, leave Settings, or return later.

Client state change: no saved state changes while the user only reviews the list.

Recovery / next state: add/edit moves to `3 Provider Editor Opens`; default selection moves to `5.2 Default Model Selection`; unresolved default state moves to `5.3 Default Model Is Missing, Stale, Or No Longer Enabled`.

Blocks progress: only when the visible default or provider state is missing for later model use.

### 3.5 Provider Editor Field And Draft Behavior

Trigger: the provider editor is open and the user edits provider identity, credentials, base URL, region, local endpoint, or local model input.

Visible UI state:

- Dirty fields remain visible in the editor.
- Prior `Verified` state no longer counts as fresh after credential or endpoint input changes.
- `Back`, provider-specific fields, model controls, and disabled-save reasons remain visible.

Allowed user actions: continue editing, load models, enter local model details, go Back, save when valid, or remove an existing provider.

Client state change: the draft changes locally and stays separate from saved provider configuration until Save succeeds.

Recovery / next state: valid remote input moves to `4 Provider Verification Or Model Loading Starts`; local model entry moves to `5.7 Local Model Entry`; Back moves to `3.4 Back From Editor`.

Blocks progress: yes until required fields, verification or local model requirements, and enabled model requirements are satisfied.

### 4.8 Verification Lifecycle And Returned Model Catalog Behavior

Trigger: the user starts provider verification or model loading.

Visible UI state:

- Loading copy appears while the request is pending.
- Success shows `Verified`, refreshed model rows, enabled-model controls, and the returned model selector.
- Failure states keep the draft visible with inline guidance near the relevant input, model list, or provider action.

Allowed user actions: wait, open the returned model selector, enable models, retry loading, edit input, or leave without saving.

Client state change: verification state and returned model choices update only for the current draft.

Recovery / next state: success moves to `4.1 Verification Succeeds` or `4.1.1 Verified Model Selection List`; validation and failure states move to the matching `4.x` branch.

Blocks progress: yes while pending or failed; no after successful verification with at least one enabled model path.

### 5.8 Enabled Models, Save, And Default Model Behavior

Trigger: the user changes enabled models, saves a provider draft, or selects a default model.

Visible UI state:

- The enabled-model count updates as rows are checked or unchecked.
- Save disabled reasons appear when no models are enabled or required fields are invalid.
- Saving state disables duplicate saves.
- The default model selector shows enabled model choices and reflects stale or missing defaults.

Allowed user actions: enable a model, filter models, save, retry Save, choose a default model, or return to editing.

Client state change: enabled models remain draft-local until Save succeeds; default model preference changes after default selection succeeds.

Recovery / next state: save success moves to `5.5 Save Succeeds`; save failure moves to `5.6 Save Fails`; stale default recovery moves through `5.2 Default Model Selection`.

Blocks progress: yes when no model is enabled, Save fails, or a required default remains missing.

### 6.4 Remove Confirmation, Deleted Provider, And Default Fallback Behavior

Trigger: the user opens removal from an existing provider editor.

Visible UI state:

- A destructive confirmation appears before removal.
- Cancel returns to the editor with the provider unchanged.
- Successful deletion removes the provider row and refreshes the default model card.
- If the removed provider owned the default model, a fallback or replacement selector remains visible.

Allowed user actions: cancel, confirm removal, retry after failure, choose a replacement default, or return to the provider list.

Client state change: saved provider state changes only after confirmed removal succeeds; default state changes only after fallback or replacement selection succeeds.

Recovery / next state: confirmation moves to `6.1 Delete Confirmation`; deletion moves to `6.1.1 Provider Deleted`; fallback moves to `6.1.1.1 Default Model Fallback Or Reselection After Deletion`; cancel and failure move to `6.2 Delete Canceled` or `6.3 Delete Fails`.

Blocks progress: yes while removal or default fallback is unresolved.

## State Language

### 0.1 Shared Provider Status And Action Language

Trigger: any Providers tab state displays provider setup status, an action label, or a disabled reason.

Visible UI state:

- Shared labels include `Providers`, `Default model`, `Add Provider`, `Edit Provider`, `Back`, `Load models`, `Reload models`, `Loading...`, `Verified`, `Save`, `Saving`, and `Remove provider`.
- Disabled-save guidance appears near the blocked action rather than requiring the user to guess.
- Error text stays near the relevant field, model list, provider action, save action, or remove action.

Allowed user actions: follow the visible action, correct the relevant input, retry, cancel, or leave the tab.

Client state change: visible language follows provider list, editor, verification, save, default model, and remove state.

Recovery / next state: returns to the active journey state.

Blocks progress: no by itself.

## Errors And Recovery

### 4.9 Verification Validation And Failure Recovery Summary

Trigger: provider verification is blocked or fails before a usable model catalog is available.

Visible UI state: this summary points to the full branch journey cards for `4.2`, `4.3`, `4.4`, `4.5`, `4.6`, and `4.7`.

Allowed user actions: fill missing input, correct malformed fields, retry loading, change network or endpoint details, switch provider, or leave the draft unsaved.

Client state change: saved provider state remains unchanged until verification later succeeds and Save succeeds.

Recovery / next state: successful correction returns to `3.3 Credential, Base URL, Region, Or Endpoint Entry` or `4 Provider Verification Or Model Loading Starts`.

Blocks progress: yes until the provider can be verified or a valid local model path can be saved.

### 5.9 Enabled Model, Default Model, And Save Recovery Summary

Trigger: model selection, default model, or provider save cannot complete.

Visible UI state: this summary points to the full branch journey cards for `5.1`, `5.3`, and `5.6`.

Allowed user actions: enable at least one model, choose a replacement default, retry Save, edit draft fields, or back out.

Client state change: draft enabled models and saved provider/default state remain separate until the relevant action succeeds.

Recovery / next state: enabling a model returns to `5 Enabled Model Selection`; default recovery moves to `5.2 Default Model Selection`; save retry moves through `5.4 Save Provider In Progress`.

Blocks progress: yes until a runnable saved model path or acceptable visible unresolved state exists.

### 6.5 Delete, Cancel, Failure, And Default Fallback Recovery Summary

Trigger: provider removal is canceled, fails, or removes the previous default model.

Visible UI state: this summary points to the full branch journey cards for `6.2`, `6.3`, and `6.1.1.1`.

Allowed user actions: cancel removal, retry deletion, return to the editor, choose another default model, or leave the unresolved default visibly blocked.

Client state change: the provider remains saved unless confirmed deletion succeeds; default model state changes only after fallback or replacement succeeds.

Recovery / next state: retry moves to `6 Remove Provider Action`; fallback moves to `5.2 Default Model Selection`; returning to the list moves to `2 Provider List And Default Model Review`.

Blocks progress: yes when deletion or replacement default is still unresolved.

### 0.2 Correct, Retry, Cancel, Or Leave Recovery Pattern

Trigger: the user reaches a validation, verification, save, load, default, or removal problem.

Visible UI state:

- The active surface remains visible.
- Draft values are preserved unless the user explicitly backs out of the editor.
- Saved provider state changes only after successful Save, default selection, or confirmed removal.

Allowed user actions: correct input, retry verification or save, cancel destructive actions, back out, or leave Settings.

Client state change: recovery actions keep unsaved draft state separate from saved provider configuration.

Recovery / next state: returns to the active branch, `2 Provider List And Default Model Review`, or `7 Stable Provider Settings End State`.

Blocks progress: depends on the active branch.

| Error / branch | Where it appears | Recovery |
| --- | --- | --- |
| Provider data load failed | `1.2 Provider Data Load Failed` | Reopen the tab or retry later; saved provider state is not changed. |
| Missing required input | `4.2 Missing Required Input` | Fill the missing field, then verify or save again. |
| Field shape validation failed | `4.3 Field Shape Validation Fails` | Correct the key, base URL, endpoint, or local model id before retrying. |
| Provider service rejected the request | `4.4 Provider Service Rejects The Credential Or Endpoint Request` | Correct credentials or endpoint details, then load models again. |
| Provider network unavailable | `4.5 Provider Network Is Unavailable` | Retry after reachability recovers, or edit endpoint/provider details. |
| No usable models returned | `4.6 No Usable Models Returned` | Retry model loading, edit provider details, or enable a valid existing model if available. |
| Unexpected verification failure | `4.7 Unexpected Verification Failure` | Retry or edit the provider draft; no provider is saved as verified. |
| No enabled models | `5.1 No Enabled Models` | Enable at least one model before Save can proceed. |
| Missing or stale default model | `5.3 Default Model Is Missing, Stale, Or No Longer Enabled` | Choose another enabled model or add/edit providers until one is available. |
| Save failed | `5.6 Save Fails` | Keep the draft open, then retry Save or edit fields. |
| Delete canceled | `6.2 Delete Canceled` | Return to the editor with the provider unchanged. |
| Delete failed | `6.3 Delete Fails` | Retry removal or return to the provider list with the provider unchanged. |
| Default points at deleted provider | `6.1.1.1 Default Model Fallback Or Reselection After Deletion` | Select a replacement enabled model or leave the missing default visibly unresolved. |

## Wireframe

The matching HTML wireframe must use this record as its route source and keep these first-level canvas bands in this order:

1. Main happy path
2. Route path
3. Detail states
4. State language
5. Errors and recovery

Route rows must remain full-size product wireframe rows. The `Branch Journeys` section remains in this Markdown record as the textual source of branch behavior, but it is not a separate HTML band. Branch states that appear in route compositions are shown inside the corresponding Route path rows.
