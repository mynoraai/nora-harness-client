# Journey 06: Runtime / Model / Permission

This record describes the implemented NoraHarness Electron journey. It is the text source for the matching HTML wireframe and keeps the journey IDs aligned with visible wireframe cards.

## Journey Scope

User entry: The user opens runtime or model selectors in the Chat or Code composer, or returns to a Chat or Code session while runtime readiness, model availability, active-run reconnect, or a permission request is in progress.

User goal: The user chooses the runtime, model, and available thinking level before sending; after sending, the user can understand blocked provider/model states, runtime startup or reconnect states, and approve or deny protected tool or command work.

End state: The selected runtime and model remain visible in the composer or session surface. The current turn is complete, stopped, failed with recovery, waiting for a permission decision, or safely continued after an approval or denial.

Implementation maturity: Implemented.

## Numbering And Route Tables

### State Inventory

Main runtime/model path:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main step | Journey 06 | Entry from Chat or Code composer. | The user focuses a Chat or Code session, opens the composer controls, or returns to a session with runtime state to resolve. | The composer is visible with the current runtime trigger, model trigger, prompt input, and send or stop control. Code mode keeps the workspace frame visible. | The session surface loads the current runtime, model, run, and permission state for this conversation. | `2` |
| `2` | Main step | Journey 06 | Runtime selector. | The user opens the runtime selector and reviews choices such as NoraHarness, Claude Code, or Codex. | A runtime picker opens from the composer tools row with searchable runtime choices and a selected check state. Locked sessions show the current runtime but do not allow changing it. | The client reads the session runtime and determines whether it can still be changed. | Unchanged runtime moves to `3`; a runtime change moves to `2.1`; locked runtime detail is `7.1`. |
| `2.1` | Sequential child | `2` | Runtime change reloads model choices. | The user selects a different runtime before the session is locked. | The runtime trigger updates to the chosen runtime; the model trigger moves into loading or selection state. | The session runtime is updated, prior model selection is cleared when needed, and the model catalog request restarts for the selected runtime. | `3.2` |
| `3` | Main step | Journey 06 | Model selector. | The user opens the model selector, searches, scans grouped model rows, or keeps the current model. | The model trigger shows a selected model, loading, retry, unavailable model, select-model, or no-enabled-model state. The picker groups available models by provider or runtime family. | The client compares the session model with the current catalog and prepares the effective model used for send. | Valid current model moves to `4`; explicit model choice moves to `3.1`; loading or blocked model states move to provider readiness branches. |
| `3.1` | Sequential child | `3` | Model selection saved for this session. | The user selects a model row. | The picker closes, the model trigger updates, and the selected checkmark moves to the chosen row the next time the picker opens. | The session records the selected model. Saved sessions also ask the runtime to use that model for later turns. | `4` |
| `4` | Main step | Journey 06 | Thinking level selector when available. | The user opens the thinking selector or leaves the current level unchanged. | In eligible Chat sessions, the composer shows the thinking control beside runtime/model controls. In Code sessions or unsupported runtime states, the thinking control is absent or unavailable. | The selected thinking level remains part of the send context only where the current runtime and mode support it. | `5` |
| `5` | Main step | Journey 06 | Send with ready runtime and model. | The user sends a prompt after the required runtime, model, attachment, and workspace gates pass. | The user message appears, the composer switches from send-ready to running constraints, and stop becomes available while the turn is active. | The client materializes a draft if needed, binds the turn to the selected runtime/model, and starts the runtime turn. | `5.1` |
| `7` | End state | Journey 06 | Stable turn resting state. | The user continues the conversation, retries, changes selectors for a future turn, stops working, or leaves the session. | The conversation shows a completed answer, stopped turn, visible failure, denied tool result, or approval-required card. The composer is ready or blocked according to the current state. | Runtime, model, run, and permission state are settled enough for the next user action. | End |

Provider readiness:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.2` | Branch step | `3` | Model catalog loading or retry. | The user waits, opens the retry state, or triggers retry from the model control. | The model trigger shows loading, or shows `Retry` after a catalog load timeout or failure. | The client requests the selected runtime's model catalog and ignores stale results from earlier runtime changes. | Success moves to `3.2.1`; failure can remain here or move to `7.3`. |
| `3.2.1` | Sequential child | `3.2` | Model catalog ready after loading or retry. | The user reviews the refreshed model choices. | The picker can show grouped model rows, a selected model, or an empty search result. | The client resolves the initial model from stored session choice, default model, runtime-selected model, default catalog row, or first available row. | `3` |
| `3.3` | Branch step | `3` | No enabled models. | The user tries to send or inspect the model control when no usable model is enabled. | The model trigger reads `No enabled models`; send remains unavailable while the model requirement is unresolved. | The client blocks the turn before runtime startup. | `7.2` |
| `3.4` | Branch step | `3` | Stored model unavailable. | The user opens a session whose previous model is no longer present. | The model trigger shows `Model unavailable`, or moves to an available fallback when one can be selected safely. | The client prevents silent use of a missing model and waits for a valid model selection or repair. | User selects another model through `3.1` or repairs provider/model configuration through `7.2`. |

Runtime startup/reconnect:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `5.1` | Sequential child | `5` | Runtime starting. | The user waits after sending. | The message stream shows pending, status, or early assistant activity. Stop is available while startup is in progress. | The selected runtime starts or attaches to a session, and the run becomes active. | Success moves to `5.1.1`; startup failure moves to `5.2`; user stop moves to `5.3`. |
| `5.1.1` | Sequential child | `5.1` | Runtime running. | The user watches the response, tool/status blocks, or running output. | Text, reasoning, tool cards, command results, status blocks, or file-change summaries can appear in the message list. | Streaming events update the current assistant turn until completion, cancellation, failure, or permission pause. | Completion moves to `7`; permission request moves to `5.1.1.1`; user stop moves to `5.3`. |
| `5.2` | Branch step | `5.1` | Runtime startup or send failure. | The user reads the failure and decides whether to retry, repair setup, or switch runtime/model. | The message stream or status area shows a runtime-specific failure such as missing runtime, authentication, startup, workspace, permission, or Gateway readiness. | The active turn is marked failed and the session keeps enough context for retry or repair. | `7.4` |
| `5.3` | Branch step | `5.1.1` | User stops the running turn. | The user clicks Stop while the turn is active. | Streaming stops, partial output remains, and the composer becomes available for the next prompt when cancellation settles. | The run is cancelled and the session records the stopped state. | `7` |
| `6.1` | Branch step | `1` | Active run reconnect after refresh or return. | The user returns to a session that may still have an active runtime turn. | The conversation shows reconnecting or recovered stream state instead of losing the active turn. | The client asks for active run identity, replays stored events after the last seen point, then follows live events. | Success moves to `6.1.1`; missing or failed replay moves to `7.5`. |
| `6.1.1` | Sequential child | `6.1` | Reconnect restores a running turn. | The user waits or responds to restored stream content. | The message list resumes with recovered text, tool, status, or permission content. | Stored events and live events are merged into the visible turn. | Running work continues through `5.1.1`; completion moves to `7`. |

Permission approval/deny:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `5.1.1.1` | Branch step | `5.1.1` | Permission request message. | The user reviews the requested read, write, or execute action. | An assistant message appears in the conversation explaining that approval is required, shows the requested operation, and provides `/approve <id> allow-once`, `/approve <id> allow-always`, or `/approve <id> deny` command options. | The running turn pauses on a pending permission request. | An allow command moves to `5.1.1.1.1`; a deny command moves to `5.1.1.1.2`; an allow-always command moves to `5.1.1.1.3`; stale or failed response moves to `7.6`. |
| `5.1.1.1.1` | Sequential child | `5.1.1.1` | Approval submitted from the conversation. | The user replies with `/approve <id> allow-once`. | The user command appears as a right-aligned chat bubble, followed by a confirmation that the allow decision was submitted. | The permission decision is sent back to the runtime as an allow decision. | `5.1.1.1.1.1` |
| `5.1.1.1.1.1` | Sequential child | `5.1.1.1.1` | Work continues after approval. | The user watches the resumed turn. | The stream continues in the same assistant turn with tool progress, result, or follow-up assistant text. | The runtime resumes the protected operation and appends the resulting stream events. | Completion moves to `7`; another permission can return to `5.1.1.1`. |
| `5.1.1.1.2` | Sequential child | `5.1.1.1` | Denial submitted from the conversation. | The user replies with `/approve <id> deny`. | The user command appears as a right-aligned chat bubble, followed by a confirmation that the allow decision was submitted. | The permission decision is sent back to the runtime as a deny decision. | `5.1.1.1.2.1` |
| `5.1.1.1.2.1` | Sequential child | `5.1.1.1.2` | Denied result shown in the stream. | The user reads the outcome and may send a safer follow-up. | The message list shows that the protected action did not proceed, and any tool result or assistant explanation remains in the conversation. | The runtime closes or reroutes that protected operation according to the denial. | `7` |
| `5.1.1.1.3` | Branch step | `5.1.1.1` | Allow-always conversation command. | The user replies with `/approve <id> allow-always` to approve the current protected operation and unlock similar follow-up operations. | The command is shown in the conversation and accepted as a permission response rather than a normal prompt. | The pending request is resolved from the conversation command path. | `5.1.1.1.3.1` |
| `5.1.1.1.3.1` | Sequential child | `5.1.1.1.3` | Command decision resolved. | The user watches the turn after the command is accepted. | If the command allowed the action, the turn continues. If it denied the action, the stream shows the denied outcome. | The runtime receives the selected allow or deny decision and the run leaves the permission pause. | Approval result continues through `5.1.1.1.1.1`; denial result continues through `5.1.1.1.2.1`. |

Errors and recovery:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `7.2` | Recovery state | `3.3` | Provider or model repair. | The user opens provider settings, enables or saves a model, returns to the session, or chooses another available model. | The blocked composer state remains understandable while the user repairs provider/model configuration. After repair, the model trigger can reload and show available choices. | Model availability is refreshed before send is allowed again. | `3.2` |
| `7.3` | Recovery state | `3.2` | Model catalog retry remains available. | The user clicks retry from the model trigger or waits and tries again. | The same model control carries the retry affordance; the user does not have to leave the composer to retry loading. | A fresh model catalog request replaces the failed or timed-out attempt. | `3.2` |
| `7.4` | Recovery state | `5.2` | Runtime failure recovery. | The user retries, changes runtime/model, repairs authentication or installation, restarts Gateway where relevant, or fixes workspace context. | The failure remains in the session with enough explanation to choose the next action. The composer can become ready again when the blocker is cleared. | The failed run is terminal; a later send starts a new run with corrected state. | Retry goes to `5`; selector changes return to `2` or `3`; unresolved failure rests at `7`. |
| `7.5` | Recovery state | `6.1` | Reconnect cannot restore the active turn. | The user reads the reconnect failure or missing-run message and decides whether to retry or send again. | The message list shows that the previous active turn is no longer available or failed while reconnecting. Existing conversation content remains visible. | Recovery stops trying to tail that run and marks the session as no longer actively streaming. | `7` |
| `7.6` | Recovery state | `5.1.1.1` | Permission command is stale or fails to submit. | The user retries the decision, waits for the current state to settle, or continues after the prompt disappears. | The conversation shows an inline recovery message such as the request no longer being pending or the command failing to submit. | The pending permission state is not resolved until a later accepted command or runtime update. | Retry returns to `5.1.1.1`; if the runtime already resolved it, the journey moves to `7`. |

Detail states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `7.1` | Detail state | `2` | Runtime is locked for this session. | The user tries to change runtime on a session that already has a native runtime identity or saved runtime binding. | The runtime selector is disabled or the user sees `Runtime is locked for this session`. | The session keeps its existing runtime so later messages stay attached to the same runtime history. | `3` |

State language:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `7.7` | State language | Journey 06 | Runtime and run state words. | The user reads runtime state in the composer, status blocks, or recovered stream. | User-facing states include ready, loading, retry, running, reconnecting, stopped, failed, and unavailable-style status. | The visible state follows the current runtime, run, and reconnect state. | Returns to the active journey state. |
| `7.8` | State language | Journey 06 | Model state words. | The user reads the model trigger or picker state. | User-facing states include selected model, loading, retry, `Model unavailable`, `Select model`, `No enabled models`, and empty search result. | The visible model language follows current catalog, selected model, and provider readiness. | Returns to `3` or the active provider readiness branch. |
| `7.9` | State language | Journey 06 | Permission state words. | The user reads the approval message, command examples, submitted command, or denied result. | User-facing states include `Permission required`, `Read`, `Write`, `Execute`, `/approve <id> allow-once`, `/approve <id> allow-always`, `/approve <id> deny`, submitted response, response error, continued work, and denied result. | The visible permission state follows whether the request is pending, accepted, denied, stale, or already resolved. | Returns to the active permission branch. |

### Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Main ready send route | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 7` | The user sends with the current runtime and model, the turn runs, and the session rests after completion. |
| Runtime changed before send | `1 -> 2 -> 2.1 -> 3.2 -> 3.2.1 -> 3 -> 3.1 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 7` | The user changes runtime, waits for the matching model catalog, selects a model, and sends successfully. |
| Runtime locked for saved session | `1 -> 2 -> 7.1 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 7` | The runtime stays fixed for the existing session, and the user continues with model/send controls. |
| Model selected explicitly | `1 -> 2 -> 3 -> 3.1 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 7` | The selected model is visible and used for the next turn. |
| Model catalog retry succeeds | `1 -> 2 -> 3 -> 3.2 -> 7.3 -> 3.2 -> 3.2.1 -> 3 -> 3.1 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 7` | The user recovers from loading failure or timeout and proceeds with a valid model. |
| No enabled models repaired | `1 -> 2 -> 3 -> 3.3 -> 7.2 -> 3.2 -> 3.2.1 -> 3 -> 3.1 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 7` | Provider/model repair returns the composer to a send-ready model state. |
| Stored model unavailable repaired by selection | `1 -> 2 -> 3 -> 3.4 -> 3.1 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 7` | The user replaces an unavailable stored model with a valid model and sends. |
| Runtime startup failure then retry | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.2 -> 7.4 -> 5 -> 5.1 -> 5.1.1 -> 7` | The first runtime start or send fails, the user repairs or retries, and a later send succeeds. |
| Runtime startup failure then selector change | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.2 -> 7.4 -> 2 -> 2.1 -> 3.2 -> 3.2.1 -> 3 -> 3.1 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 7` | The user changes runtime/model after failure and completes a later turn. |
| User stops running turn | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 5.3 -> 7` | Partial output remains and the session rests in a stopped, usable state. |
| Active run reconnect succeeds | `1 -> 6.1 -> 6.1.1 -> 5.1.1 -> 7` | Refresh or return restores the active turn and follows it to completion. |
| Active run reconnect cannot restore | `1 -> 6.1 -> 7.5 -> 7` | The user sees a recoverable reconnect failure without losing visible conversation context. |
| Permission approved by conversation command | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 5.1.1.1 -> 5.1.1.1.1 -> 5.1.1.1.1.1 -> 7` | The user approves the protected action, the turn resumes, and the session rests after completion. |
| Permission denied by conversation command | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 5.1.1.1 -> 5.1.1.1.2 -> 5.1.1.1.2.1 -> 7` | The user denies the protected action, and the stream records the denied outcome. |
| Permission resolved by conversation command | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 5.1.1.1 -> 5.1.1.1.3 -> 5.1.1.1.3.1 -> 7` | A valid approval command resolves the pending request as allow or deny and the stream continues to the matching outcome. |
| Permission response failure then approve | `1 -> 2 -> 3 -> 4 -> 5 -> 5.1 -> 5.1.1 -> 5.1.1.1 -> 7.6 -> 5.1.1.1 -> 5.1.1.1.1 -> 5.1.1.1.1.1 -> 7` | A stale or failed decision is shown inline, then the user retries and the approved work continues. |

## Main Path

### 1 Composer Runtime And Model Entry

User entry: the user is in Chat or Code, or returns to a session with runtime, model, reconnect, or permission state to resolve.

User action: the user focuses the composer or message list.

Visible UI state: the composer shows the current runtime trigger, model trigger, prompt input, and send or stop control. Code mode keeps the workspace shell and panels visible around the same conversation surface.

Client state change: the session surface loads selected runtime, selected model, current run state, and pending permission state for the conversation.

Exit / next state: the user can inspect or change runtime through `2 Runtime Selector`, or reconnect state can continue through `6.1 Active Run Reconnect After Refresh Or Return`.

### 2 Runtime Selector

User entry: the composer is visible and runtime selection is available or visibly locked.

User action: the user opens the runtime picker, searches, or chooses a runtime.

Visible UI state: the runtime picker lists NoraHarness, Claude Code, and Codex-style choices with current selection. If the session is locked, the control is disabled or communicates that the runtime cannot be changed for this session.

Client state change: the client checks the session's current runtime and whether existing runtime identity or history prevents switching.

Exit / next state: unchanged runtime continues to `3 Model Selector`; a runtime change continues to `2.1 Runtime Change Reloads Model Choices`; locked runtime detail is `7.1 Runtime Is Locked For This Session`.

### 3 Model Selector

User entry: the selected runtime is known and the composer needs a usable model.

User action: the user opens the model picker, searches grouped models, selects a row, retries loading, or repairs a blocked model state.

Visible UI state: the model trigger shows selected model, loading, retry, unavailable model, select-model, or no-enabled-model language. The picker groups rows by provider or runtime family and can show no search results.

Client state change: the client compares the stored session model with the loaded catalog and determines the model that can be used for send.

Exit / next state: selected model continues to `4 Thinking Level Selector`; explicit selection goes through `3.1 Model Selection Saved For This Session`; blocked states move to `3.2`, `3.3`, or `3.4`.

### 4 Thinking Level Selector

User entry: runtime and model state are ready enough for the composer.

User action: the user opens or ignores the thinking selector.

Visible UI state: in eligible Chat sessions, the thinking control is visible beside the runtime/model controls. In Code or unsupported runtime states, the control is absent or unavailable, and sending does not depend on it.

Client state change: the chosen thinking level is retained for eligible Chat sends; unsupported modes do not add thinking selection to the turn.

Exit / next state: `5 Send With Ready Runtime And Model`.

### 5 Send With Ready Runtime And Model

User entry: the prompt has content or accepted attachments, the model is usable, and any required Code workspace context is available.

User action: the user sends the prompt.

Visible UI state: the user message appears immediately. The composer switches into running constraints and the stop control becomes available.

Client state change: a draft session is materialized when needed, the selected runtime/model is bound to the turn, and the runtime turn starts.

Exit / next state: `5.1 Runtime Starting`.

### 7 Stable Turn Resting State

User entry: the turn has completed, stopped, failed, denied a protected action, or paused waiting for a permission decision.

User action: the user continues chatting, retries, changes selectors for the next turn, repairs setup, or leaves the session.

Visible UI state: the message list preserves the completed answer, stopped output, visible error, permission message, denied outcome, or recovered stream. Runtime and model controls remain visible for the next allowed action.

Client state change: the session keeps the final visible state and is ready for the next user action that the current state allows.

Exit / next state: End.

## Branch Journeys

### 2.1 Runtime Change Reloads Model Choices

Trigger: the user chooses a different runtime before the session is locked.

Visible UI state: the runtime trigger updates to the new runtime. The model trigger clears the old selection when required and moves into loading, retry, or select-model state.

Allowed user actions: wait for model choices, open the model picker, retry loading, or choose another runtime while still allowed.

Client state change: the session records the new runtime and starts a fresh model catalog request for that runtime.

Recovery / next state: model loading continues through `3.2 Model Catalog Loading Or Retry`.

Blocks progress: yes, until a usable model is selected or confirmed.

### 3.1 Model Selection Saved For This Session

Trigger: the user selects a model row from the picker.

Visible UI state: the picker closes and the composer model trigger shows the selected model.

Allowed user actions: send, reopen the picker, change runtime if still allowed, or continue editing the prompt.

Client state change: the selected model is saved to the session. If the session already exists beyond draft state, the runtime is also asked to use the selected model for future turns.

Recovery / next state: `4 Thinking Level Selector`.

Blocks progress: no.

### 3.2 Model Catalog Loading Or Retry

Trigger: runtime changes, the model picker needs a catalog, or the previous catalog request times out or fails.

Visible UI state: the model trigger shows loading or `Retry`; the composer does not claim a usable model until one is resolved.

Allowed user actions: wait, click retry, change runtime if allowed, or open provider repair when no models are available.

Client state change: the active model catalog request belongs to the currently selected runtime; older request results are ignored.

Recovery / next state: success moves to `3.2.1 Model Catalog Ready After Loading Or Retry`; persistent failure can move to `7.3 Model Catalog Retry Remains Available`.

Blocks progress: yes, when no effective model is available.

### 3.2.1 Model Catalog Ready After Loading Or Retry

Trigger: the selected runtime returns model choices.

Visible UI state: the model trigger and picker update to available grouped models, selected model, or empty search state.

Allowed user actions: choose a model, search, keep the resolved current model, or return to the prompt.

Client state change: the initial model is resolved from stored session choice, default model, runtime selection, default catalog row, or first available row.

Recovery / next state: returns to `3 Model Selector`.

Blocks progress: no, if a usable model is resolved.

### 3.3 No Enabled Models

Trigger: the selected runtime returns no usable enabled models.

Visible UI state: the model trigger reads `No enabled models`, and send remains unavailable while the model requirement is unresolved.

Allowed user actions: open provider settings, enable or save models, change runtime, or wait and retry loading.

Client state change: the send gate stays closed before runtime startup.

Recovery / next state: `7.2 Provider Or Model Repair`.

Blocks progress: yes.

### 3.4 Stored Model Unavailable

Trigger: the session references a model that is not present in the current catalog.

Visible UI state: the model trigger shows `Model unavailable`, or the user sees an available fallback after the app safely resolves one.

Allowed user actions: select a different model, repair provider/model configuration, change runtime if allowed, or leave the session unchanged.

Client state change: the client avoids silently sending with a missing model.

Recovery / next state: explicit selection goes to `3.1 Model Selection Saved For This Session`; provider/model repair goes to `7.2 Provider Or Model Repair`.

Blocks progress: yes, while no usable model is selected.

### 5.1 Runtime Starting

Trigger: the user sends a prompt with a usable runtime and model.

Visible UI state: the message list shows pending, early status, or first assistant activity. Stop is available while the turn is active.

Allowed user actions: wait or stop.

Client state change: the runtime starts, attaches, or resumes a session and marks the run active.

Recovery / next state: success moves to `5.1.1 Runtime Running`; failure moves to `5.2 Runtime Startup Or Send Failure`; stop moves to `5.3 User Stops The Running Turn`.

Blocks progress: temporarily.

### 5.1.1 Runtime Running

Trigger: runtime startup succeeds and the turn begins streaming.

Visible UI state: the message list can show streaming text, reasoning, tool cards, command results, status blocks, and modified file summaries. Stop remains available while the turn is active.

Allowed user actions: wait, stop, approve or deny permission requests, or inspect output.

Client state change: stream events update the active assistant turn until completion, cancellation, failure, or permission pause.

Recovery / next state: completion moves to `7 Stable Turn Resting State`; permission pause moves to `5.1.1.1 Permission Request Card`; stop moves to `5.3 User Stops The Running Turn`.

Blocks progress: yes, for a new send in the same composer while the turn is running.

### 5.2 Runtime Startup Or Send Failure

Trigger: the selected runtime cannot start, attach, authenticate, use the workspace, contact its backing service, or complete the send request.

Visible UI state: the message list or status area shows an actionable runtime failure instead of a generic blank state.

Allowed user actions: retry, change runtime/model, repair installation or authentication, restart Gateway where relevant, fix workspace context, or leave the failed result visible.

Client state change: the turn is terminal failed, and the session remains available for recovery.

Recovery / next state: `7.4 Runtime Failure Recovery`.

Blocks progress: yes, until the user retries or chooses another path.

### 5.3 User Stops The Running Turn

Trigger: the user clicks Stop while the runtime turn is active.

Visible UI state: streaming stops, partial output remains visible, and the composer returns to an available state after cancellation settles.

Allowed user actions: send a follow-up, retry manually, change selectors for the next turn, or leave the session.

Client state change: the active run is cancelled and recorded as stopped for the session.

Recovery / next state: `7 Stable Turn Resting State`.

Blocks progress: briefly, while cancellation is settling.

### 6.1 Active Run Reconnect After Refresh Or Return

Trigger: the user refreshes, relaunches, or returns to a session that may still have an active runtime run.

Visible UI state: the session shows reconnecting or recovered stream state rather than replacing the whole conversation with an empty or failed surface.

Allowed user actions: wait, inspect recovered output, stop when available, or continue after reconnect settles.

Client state change: the client replays stored events after the last delivered point and then follows live events for the same active run.

Recovery / next state: success moves to `6.1.1 Reconnect Restores A Running Turn`; failure moves to `7.5 Reconnect Cannot Restore The Active Turn`.

Blocks progress: temporarily, for the affected run.

### 6.1.1 Reconnect Restores A Running Turn

Trigger: active-run replay and live event following succeed.

Visible UI state: recovered text, status, tool, or permission content appears in the message list and continues in place.

Allowed user actions: wait, stop, approve or deny restored permission prompts, or continue after completion.

Client state change: recovered and live runtime events merge into the visible assistant turn.

Recovery / next state: active running content continues through `5.1.1 Runtime Running`; completion moves to `7 Stable Turn Resting State`.

Blocks progress: yes, while the restored run is active.

### 5.1.1.1 Permission Request Card

Trigger: the running runtime requests approval for a protected read, write, or execute action.

Visible UI state: an assistant message appears in the conversation. It explains approval is required, shows the operation details, and includes `/approve` command options for allow-once, allow-always, and deny.

Allowed user actions: copy or type an `/approve` command, choose allow-once, allow-always, or deny, or wait.

Client state change: the active turn pauses until the permission request receives an accepted allow or deny decision.

Recovery / next state: approval moves to `5.1.1.1.1 Approval Submitted From The Permission Card`; denial moves to `5.1.1.1.2 Denial Submitted From The Permission Card`; command response moves to `5.1.1.1.3 Conversation Approval Command`; response failure moves to `7.6 Permission Response Is Stale Or Fails To Submit`.

Blocks progress: yes, for the protected operation.

### 5.1.1.1.1 Approval Submitted From The Permission Card

Trigger: the user clicks `Approve`.

Visible UI state: the submitted `/approve` command appears in the conversation, then the stream confirms the allow decision and continues.

Allowed user actions: wait for the response to settle.

Client state change: an allow decision is sent to the runtime.

Recovery / next state: `5.1.1.1.1.1 Work Continues After Approval`.

Blocks progress: temporarily.

### 5.1.1.1.1.1 Work Continues After Approval

Trigger: the runtime accepts the approval decision.

Visible UI state: the same assistant turn continues with tool progress, command output, file-change summary, or assistant follow-up text.

Allowed user actions: wait, stop, or answer another permission prompt if one appears later.

Client state change: the runtime resumes the protected operation and appends the resulting stream events.

Recovery / next state: completion moves to `7 Stable Turn Resting State`; another permission request returns to `5.1.1.1 Permission Request Card`.

Blocks progress: no, once the turn has completed; yes while the turn is still running.

### 5.1.1.1.2 Denial Submitted From The Permission Card

Trigger: the user clicks `Deny`.

Visible UI state: the submitted deny command appears in the conversation, then the stream confirms the denied decision.

Allowed user actions: wait for the response to settle.

Client state change: a deny decision is sent to the runtime.

Recovery / next state: `5.1.1.1.2.1 Denied Result Shown In The Stream`.

Blocks progress: temporarily.

### 5.1.1.1.2.1 Denied Result Shown In The Stream

Trigger: the runtime accepts the denial decision.

Visible UI state: the message list shows that the protected operation did not proceed, with any tool result or assistant explanation preserved in the same conversation.

Allowed user actions: send a safer follow-up, adjust the request, or leave the denied result as the final outcome for that tool step.

Client state change: the runtime closes or reroutes the protected operation according to the denial.

Recovery / next state: `7 Stable Turn Resting State`.

Blocks progress: no, after the denial result is visible.

### 5.1.1.1.3 Conversation Approval Command

Trigger: the user replies with a valid approval command while a permission request is pending.

Visible UI state: the command is accepted as a permission response when it names the request and contains an allow or deny decision. Invalid approval-like text is not treated as a resolved permission decision.

Allowed user actions: send a valid allow-once, allow-always, or deny command.

Client state change: the pending permission request is resolved through the conversation response path.

Recovery / next state: `5.1.1.1.3.1 Command Decision Resolved`.

Blocks progress: yes, until the command resolves or the user chooses another response path.

### 5.1.1.1.3.1 Command Decision Resolved

Trigger: the conversation approval command is accepted.

Visible UI state: an allow command resumes the protected work; a deny command shows the denied outcome in the stream.

Allowed user actions: wait for resumed work, read the denied result, or send a follow-up after the turn settles.

Client state change: the runtime receives the selected allow or deny decision and leaves the permission pause.

Recovery / next state: allow result continues through `5.1.1.1.1.1 Work Continues After Approval`; deny result continues through `5.1.1.1.2.1 Denied Result Shown In The Stream`.

Blocks progress: temporarily.

## Detail States

### 7.1 Runtime Is Locked For This Session

Trigger: the user tries to change runtime for a session whose runtime identity is already fixed.

Visible UI state: the runtime selector is disabled or reports `Runtime is locked for this session`.

Allowed user actions: continue with the current runtime, create a new session for a different runtime, or change model where allowed.

Client state change: the session keeps its existing runtime binding so future messages stay connected to the same runtime history.

Recovery / next state: `3 Model Selector`.

Blocks progress: no, unless the current runtime also has a readiness failure.

## State Language

### 7.7 Runtime And Run State Words

Trigger: the user reads runtime state in the composer, status blocks, reconnect surface, or message list.

Visible UI state: ready, loading, retry, running, reconnecting, stopped, failed, and unavailable-style language appears where the user needs to decide the next action.

Allowed user actions: follow the state-specific action such as wait, retry, stop, repair, or continue.

Client state change: visible language follows the current runtime, run, and reconnect state.

Recovery / next state: returns to the active journey state.

Blocks progress: depends on the active state.

### 7.8 Model State Words

Trigger: the user reads the model trigger or picker.

Visible UI state: selected model, loading, retry, `Model unavailable`, `Select model`, `No enabled models`, and empty search result language appears in the model control.

Allowed user actions: wait, retry, select a model, search, open provider settings, or change runtime.

Client state change: visible model language follows the current catalog, selected model, and provider readiness.

Recovery / next state: returns to `3 Model Selector` or the active provider readiness branch.

Blocks progress: yes when no effective model is usable.

### 7.9 Permission State Words

Trigger: the user reads a permission prompt, response state, or denied result.

Visible UI state: `Permission required`, `Read`, `Write`, `Execute`, `/approve <id> allow-once`, `/approve <id> allow-always`, `/approve <id> deny`, submitted response, response error, continued work, and denied result language appears in the message list.

Allowed user actions: approve, deny, send a valid approval command, retry a failed response, or send a safer follow-up after denial.

Client state change: visible permission language follows whether the request is pending, accepted, denied, stale, failed, or already resolved.

Recovery / next state: returns to the active permission branch.

Blocks progress: yes while a protected operation waits for a decision.

| Surface | Copy / control language | Meaning |
| --- | --- | --- |
| Runtime selector | NoraHarness, Claude Code, Codex | User-visible runtime choices for the session before send or while choosing a new session path. |
| Runtime selector | `Runtime is locked for this session` | The current session cannot switch runtime without starting a different session. |
| Model selector | `Loading...`, `Retry`, `Model unavailable`, `Select model`, `No enabled models` | The composer tells the user whether model choice is ready, recoverable, missing, or blocked. |
| Thinking selector | Thinking level control, when visible | Chat-only eligible thinking level choice for supported runtime state. |
| Running turn | Stop, running, reconnecting, failed, stopped | The user can tell whether the current turn is active, recoverable, or terminal. |
| Permission message | `Permission required`, `Read`, `Write`, `Execute`, `/approve <id> allow-once`, `/approve <id> allow-always`, `/approve <id> deny` | The protected operation waits for the user's allow or deny command. |
| Conversation approval command | Approval command with request id and allow or deny decision | A conversational response can resolve a pending permission request when it matches the expected approval format. |

## Errors And Recovery

### 7.2 Provider Or Model Repair

Trigger: no enabled model, unavailable stored model, or provider/model configuration prevents send.

Visible UI state: the composer remains blocked with clear model language. The user can enter provider settings or choose another model/runtime.

Allowed user actions: enable models, save provider settings, choose a different model, change runtime, or retry loading.

Client state change: the model catalog must refresh before send is available again.

Recovery / next state: `3.2 Model Catalog Loading Or Retry`.

Blocks progress: yes, until a usable model exists.

### 7.3 Model Catalog Retry Remains Available

Trigger: model catalog loading times out or fails.

Visible UI state: the model trigger shows `Retry`, and the same control is the recovery entry.

Allowed user actions: click retry, wait and retry, change runtime, or repair provider settings.

Client state change: a fresh model catalog request replaces the failed request.

Recovery / next state: `3.2 Model Catalog Loading Or Retry`.

Blocks progress: yes, when no usable model is already selected.

### 7.4 Runtime Failure Recovery

Trigger: runtime startup, send, authentication, workspace, permission profile, or Gateway readiness fails.

Visible UI state: the failed turn remains visible with actionable status. The user can retry or change the setup without losing the conversation.

Allowed user actions: retry send, switch runtime, choose another model, repair runtime setup, fix workspace context, or restart Gateway where relevant.

Client state change: the failed run is terminal; a later send creates a new run after the user chooses a recovery path.

Recovery / next state: retry returns to `5 Send With Ready Runtime And Model`; selector repair returns to `2 Runtime Selector` or `3 Model Selector`; unresolved failure rests at `7 Stable Turn Resting State`.

Blocks progress: yes, for the failed turn.

### 7.5 Reconnect Cannot Restore The Active Turn

Trigger: reconnect replay cannot find the active run, or the run fails while reconnecting.

Visible UI state: the message list shows a recoverable missing-run or reconnect failure message while preserving existing conversation content.

Allowed user actions: retry manually, send again, change setup, or leave the recovered transcript as-is.

Client state change: the client stops tailing the missing or failed run and clears active streaming state for the session.

Recovery / next state: `7 Stable Turn Resting State`.

Blocks progress: no, once the failure is shown.

### 7.6 Permission Response Is Stale Or Fails To Submit

Trigger: the user submits a permission response after the runtime has already resolved it, or the response cannot be submitted.

Visible UI state: the permission card shows an inline error such as the request no longer being pending or the response failing to submit.

Allowed user actions: retry the decision, wait for the runtime state to settle, or continue after the permission prompt disappears.

Client state change: the permission request remains unresolved from the user's response until an accepted decision or runtime update arrives.

Recovery / next state: retry returns to `5.1.1.1 Permission Request Card`; already-resolved runtime state moves to `7 Stable Turn Resting State`.

Blocks progress: yes, while the card remains pending.

| Error / branch | Where it appears | Recovery |
| --- | --- | --- |
| Model catalog loading fails or times out | Model trigger and picker | User clicks retry, changes runtime, or repairs provider settings. |
| No enabled models | Model trigger and send gate | User enables/saves models in provider settings, changes runtime, or retries loading after repair. |
| Stored model unavailable | Model trigger | User selects another model or repairs provider/model configuration. |
| Runtime startup or send failure | Message stream or runtime status area | User retries, switches runtime/model, repairs setup, fixes workspace context, or restarts Gateway where relevant. |
| User stops turn | Message stream | Partial output remains; user can send a follow-up or retry manually. |
| Active run reconnect cannot restore | Message stream recovery state | User retries manually or sends again from the restored conversation. |
| Permission response stale or failed | Permission request card | User retries the approval command, waits for runtime resolution, or continues once the prompt disappears. |
| Permission denied | Message stream | The protected action does not proceed; user can send a safer follow-up. |

## Wireframe

The matching HTML wireframe must use this record as the only route source. It must keep six first-level canvas bands in this order: Main happy path, Route map, Branch journeys, Detail states, State language, and Errors and recovery. Route map rows must cover every row in the route table as full-size product wireframe cards; they do not replace the branch, detail, state-language, or recovery bands.
