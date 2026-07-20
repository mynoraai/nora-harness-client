# Journey 2: Cloud Resource Decisions In Conversation

This record is the source of truth for `cloud-resource-conversation.wireframe.html`.
It describes the proposed NoraHarness V2 conversation journey for creating, changing, testing, and
connecting long-lived NoraCloud resources while a user is vibe coding.

The current product can perform many of the underlying cloud operations, and it already has generic
tool progress, permission requests, and user questions. The resource-impact cards, user-visible operation ledger,
partial/unknown receipts, and Cloud-aware scope controls in this journey are not implemented yet.

## Numbering And Route Tables

### Main Path States

| ID   | Type      | Parent step | What it represents                                         | User action                                                                                            | Visible UI state                                                                                                                                              | Client state change                                                                                         | Exit / next state           |
| ---- | --------- | ----------- | ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- | --------------------------- |
| `1`  | Main step | Journey 2   | Local product work proceeds normally.                      | Asks the Agent to build a cloud-connected weather device.                                              | Author, preview, build, and an already-targeted flash use ordinary progress; no cloud-resource decision is shown yet.                                         | Local Workspace artifacts change; no NoraCloud resource is created.                                         | `2`                         |
| `2`  | Main step | Journey 2   | The task reaches the first cloud-resource boundary.        | Reviews what the first publish will do.                                                                | A proposed resource-impact card says a new Agent and first Version will be created and the Version must become Live; optional verification scope is separate. | The operation waits for a supported scope decision unless the user's precise instruction already covers it. | `3`, `2.1`, `2.2`, or `2.3` |
| `3`  | Main step | Journey 2   | The user confirms the supported first-publish scope.       | Clicks `Publish`, adjusts only the verification range, or gives an equivalent precise instruction.     | The card settles to the chosen Cloud, Workspace, Agent name, forced first Live change, and bounded test count.                                                | The approved result scope is recorded.                                                                      | `4`                         |
| `4`  | Main step | Journey 2   | The Agent executes the resource operation.                 | Watches or expands technical details.                                                                  | One card advances through real resource stages: Agent created, Version created, Live changed, optional Cloud test, final refresh.                             | Each returned resource ID and completed stage is retained.                                                  | `5`, `4.1`, or `4.2`        |
| `5`  | Main step | Journey 2   | The user receives a factual resource receipt.              | Reviews the result or clicks `View in Cloud`.                                                          | Real Agent, Live Version, Session, LLM tokens, turns, and any unfinished stage are shown; no result is inferred from the plan.                                | The operation closes as complete or partial and links to the Electron Cloud overview.                       | `6` or `8`                  |
| `6`  | Main step | Journey 2   | Device registration reaches a second independent boundary. | Reviews Device, Agent, follow-Live behavior, credential destination, and subsequent build/flash scope. | A separate proposed card offers `Register and connect`, `Change device`, and `Not now`.                                                                       | Registration waits for a distinct decision even if publish or flash was already approved.                   | `7` or `6.1`                |
| `7`  | Main step | Journey 2   | Registration and connection report separate evidence.      | Reviews the result.                                                                                    | Cloud Device registration, Device credential state, firmware flash, Cloud connection, and physical behavior appear as separate rows.                          | Completed evidence is retained independently.                                                               | End or `8`                  |
| `8`  | Main step | Journey 2   | A later Agent-only iteration begins.                       | Asks to make responses shorter, test, then go live.                                                    | The Agent creates a new non-Live Version and completes local/static checks while current Live remains unchanged.                                              | A candidate Version exists; NoraCloud runtime has not executed it.                                          | `9`                         |
| `9`  | Main step | Journey 2   | The runtime limitation becomes a user decision.            | Reviews temporary-Live impact.                                                                         | A proposed card explains that Cloud testing requires release, lists current Live, candidate, following Devices, five tests, and rollback rule.                | No Live mutation occurs until this new scope is accepted.                                                   | `10`, `9.1`, or End         |
| `10` | Main step | Journey 2   | Temporary release and bounded validation complete.         | Reviews the final receipt.                                                                             | The receipt shows candidate release, five Cloud tests, Usage, affected Devices, and whether candidate remained Live or rollback restored the previous Live.   | The final Live pointer and validation result are recorded from real state.                                  | End                         |

### Branch States

| ID     | Type   | Parent step                      | What it represents                                               | User action                                                  | Visible UI state                                                                                                                   | Client state change                                                                 | Exit / next state               |
| ------ | ------ | -------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- | ------------------------------- |
| `2.1`  | Branch | `2 First Cloud Boundary`         | The user defers publication.                                     | Clicks `Not now`.                                            | The cloud card settles to `Deferred`; local build and preview remain available.                                                    | No cloud mutation occurs and the local task remains active.                         | `1`                             |
| `2.2`  | Branch | `2 First Cloud Boundary`         | The user chooses an exact existing Agent.                        | Enters or selects a verified Agent ID.                       | The card states that Workspace binding will change and a new non-Live Version will be created; this is not shown as a pure attach. | The supported attach-and-push scope replaces new-Agent publication.                 | `4`                             |
| `2.3`  | Branch | `2 First Cloud Boundary`         | The user asks for a new Agent without a Live Version.            | Requests `Create the Agent but do not set Live`.             | The Agent explains that new-Agent candidate-only is not supported and offers first publish, exact existing-Agent attach, or defer. | No mutation occurs.                                                                 | `2`                             |
| `4.1`  | Branch | `4 Resource Execution`           | Some stages succeed and a later stage fails.                     | Reviews the partial result.                                  | Completed resources stay checked with real IDs; failed and unstarted stages remain distinct.                                       | The operation becomes partial and preserves known resources.                        | `5`                             |
| `4.2`  | Branch | `4 Resource Execution`           | The remote outcome cannot be confirmed.                          | Waits for read-only checks or stops.                         | The card says `Outcome unknown`, shows the last confirmed stage, and disables automatic duplicate mutation.                        | The operation pauses until safe discovery or manual recovery can determine reality. | End or `4` after recovery       |
| `6.1`  | Branch | `6 Device Registration Decision` | The user defers Device registration.                             | Clicks `Not now`.                                            | Registration is marked deferred; the Cloud Agent and local Workspace remain usable.                                                | No Device or Device credential is created.                                          | End or `8`                      |
| `7.1`  | Branch | `7 Device Result`                | Device registration succeeds but a later connection stage fails. | Reviews the partial connection result.                       | Cloud Device and credential remain complete; build, flash, network, or physical verification identifies the failed stage.          | Recovery resumes from the failed stage without registering another Device.          | `7` after recovery              |
| `9.1`  | Branch | `9 Temporary Live Decision`      | Current Live changed after the user reviewed the plan.           | Reviews the new current Live and chooses whether to re-plan. | The old authorization is invalid; the card shows confirmed Live, current Live, and target candidate.                               | Release stops before mutation.                                                      | `9` after a new decision or End |
| `10.1` | Branch | `10 Validation Result`           | Candidate validation fails after temporary release.              | Reviews the rollback result.                                 | Failed tests and their Usage are shown; the previous Version is shown as restored Live.                                            | Rollback is verified and the candidate remains non-Live.                            | End or `8`                      |

### Detail States

| ID    | Type         | Parent step              | What it represents                            | User action              | Visible UI state                                                                                                                                           | Client state change                                     | Exit / next state   |
| ----- | ------------ | ------------------------ | --------------------------------------------- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- | ------------------- |
| `2.4` | Detail state | `2 First Cloud Boundary` | What one resource decision may cover.         | Expands scope details.   | Cloud environment, Workspace, Agent/Device, current and target Version, credential destination, test count, stop condition, and rollback rule are visible. | No mutation occurs.                                     | `2` or `3`          |
| `4.3` | Detail state | `4 Resource Execution`   | Technical details under a resource operation. | Expands details.         | Command details, structured result, request diagnostic, and logs appear below the product-level stages.                                                    | No resource state changes merely from expanding.        | `4`                 |
| `5.1` | Detail state | `5 Resource Receipt`     | Link from the receipt to long-term overview.  | Clicks `View in Cloud`.  | The Electron right workbench opens the proposed Cloud overview without leaving the conversation.                                                           | The same Workspace resource snapshot begins refreshing. | Journey 1, step `2` |
| `7.2` | Detail state | `7 Device Result`        | Evidence categories.                          | Reviews or expands rows. | Cloud runtime, Device connection, firmware flash, and physical verification remain separate.                                                               | Evidence cannot substitute for another category.        | `7`                 |

### State Language

| ID    | Type           | Parent step | What it represents         | User action | Visible UI state                                                                                                           | Client state change | Exit / next state |
| ----- | -------------- | ----------- | -------------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------------- |
| `0.1` | State language | Journey 2   | Resource operation status. | None.       | `Waiting for decision`, `Running`, `Completed`, `Partial`, `Outcome unknown`, `Deferred`, and `Blocked`.                   | None.               | None              |
| `0.2` | State language | Journey 2   | Version language.          | None.       | Real `ver_…` IDs plus descriptions such as `Live`, `Latest non-Live`, or `Previous Live`; no invented v1/v2 numbering.     | None.               | None              |
| `0.3` | State language | Journey 2   | Usage language.            | None.       | LLM tokens, turns, optional STT/TTS, explicit Session or Workspace scope, and coverage; no unsupported input/output split. | None.               | None              |
| `0.4` | State language | Journey 2   | Secret language.           | None.       | Credential type, scope, destination, and configured/attention state; no secret value.                                      | None.               | None              |

### Errors And Recovery

| ID     | Type             | Parent step                      | What it represents                              | User action                                      | Visible UI state                                                                                               | Client state change                                              | Exit / next state |
| ------ | ---------------- | -------------------------------- | ----------------------------------------------- | ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ----------------- |
| `4.4`  | Recovery summary | `4 Resource Execution`           | Partial and unknown results.                    | Reads the preserved stages and next safe action. | Known-created resources remain visible; unknown results stop duplicate mutation.                               | Recovery begins from confirmed state, not the original plan.     | `4` or `5`        |
| `6.2`  | Recovery summary | `6 Device Registration Decision` | Device credential write or connection recovery. | Follows the stated next action.                  | Cloud Device creation, one-time credential save, build, flash, network, and physical checks remain distinct.   | A local credential-save failure stops automatic re-registration. | `6` or `7`        |
| `9.2`  | Recovery summary | `9 Temporary Live Decision`      | Concurrent Live change.                         | Re-reads the new state and decides again.        | The product never offers a shortcut that silently reuses the old authorization.                                | A new current-Live premise is required.                          | `9`               |
| `10.2` | Recovery summary | `10 Validation Result`           | Failed temporary validation.                    | Reviews failures and rollback.                   | The result proves whether previous Live was restored; if rollback cannot be proven, the state becomes unknown. | Candidate remains available for later fixes.                     | End or `8`        |

### Route Table

| Route                                     | Composition             | Result / next state                                                                  |
| ----------------------------------------- | ----------------------- | ------------------------------------------------------------------------------------ |
| First publish happy path                  | `1 -> 2 -> 3 -> 4 -> 5` | The user crosses the cloud boundary knowingly and receives a factual receipt.        |
| Defer publication                         | `1 -> 2 -> 2.1 -> 1`    | Local work continues with no cloud mutation.                                         |
| Existing Agent attach                     | `2 -> 2.2 -> 4 -> 5`    | Binding changes and a new non-Live Version is reported together.                     |
| Unsupported new-Agent candidate-only      | `2 -> 2.3 -> 2`         | The product explains the constraint instead of presenting a fake option.             |
| Partial first publish                     | `3 -> 4 -> 4.1 -> 5`    | Created resources remain visible and recovery starts after the failed stage.         |
| Unknown remote outcome                    | `3 -> 4 -> 4.2`         | Duplicate mutation stops until discovery or manual recovery resolves the outcome.    |
| Device registration happy path            | `5 -> 6 -> 7`           | Device, credential, connection, flash, and physical evidence are separately visible. |
| Device connection partial                 | `5 -> 6 -> 7.1 -> 7`    | Registration is preserved and recovery resumes from the failed connection stage.     |
| Later candidate validation succeeds       | `5 -> 8 -> 9 -> 10`     | Candidate temporarily becomes Live, passes bounded tests, and remains Live.          |
| Live conflict                             | `8 -> 9 -> 9.1 -> 9`    | Release stops and the user decides again from the new current Live.                  |
| Candidate validation fails and rolls back | `8 -> 9 -> 10.1 -> 10`  | Previous Live is restored and the failed candidate remains non-Live.                 |

## Main Path

### 1 Local Development

User entry: the user asks the Agent to build a cloud-connected hardware product.

User action: the user reviews preview/build progress and iterates on the product.

Visible UI state: ordinary author, preview, build, and an already-targeted flash appear as normal
development steps. No Agent, Version, Device, or Cloud Session is implied to exist.

Client state change: local Workspace artifacts and build results change.

Exit / next state: `2 First Cloud Boundary` when the next required action would create NoraCloud
resources.

### 2 First Cloud Boundary

User entry: local work is ready and the task now requires a new Cloud Agent.

User action: the user reviews the proposed product outcome.

Visible UI state: a proposed resource-impact card shows the actual Cloud environment, Workspace,
new Agent name, first Version creation, required first Live change, optional one-turn verification,
and what will not happen yet.

Client state change: no mutation occurs until the supported scope is decided, unless the user's
precise current instruction already covers exactly that scope.

Exit / next state: `3 Confirmed First Publish`, `2.1 Deferred`, `2.2 Existing Agent Attach`, or
`2.3 Unsupported New-Agent Candidate-Only`.

### 3 Confirmed First Publish

User entry: the user selects the supported new-Agent publication path or has already given an
equivalent precise instruction.

User action: clicks `Publish`, adjusts the bounded verification range, or proceeds from the already
precise request.

Visible UI state: the impact card settles to one approved scope. It does not ask about low-level implementation parameters.

Client state change: the approved Cloud, Workspace, Agent, required Live change, and test boundary
are attached to the operation.

Exit / next state: `4 Resource Execution`.

### 4 Resource Execution

User entry: the first-publish scope is approved.

User action: watches the resource stages or expands technical details.

Visible UI state: one card advances through Agent creation, Version creation, Live change, optional
Cloud test, and final refresh. Each completed stage shows its real masked ID as soon as it is known.

Client state change: returned IDs, stages, Usage, and diagnostics accumulate in the operation
record.

Exit / next state: `5 Resource Receipt`, `4.1 Partial Execution`, or `4.2 Outcome Unknown`.

### 5 Resource Receipt

User entry: execution reaches a stable result.

User action: reviews the result, continues the product task, or clicks `View in Cloud`.

Visible UI state: complete or partial result with real Agent, Version, Live, Session, Usage, and
unfinished stages. Input/output token values are not invented.

Client state change: the operation closes with a factual state and becomes a source for the
Electron Cloud overview.

Exit / next state: `6 Device Registration Decision`, `8 Later Iteration`, or Journey 1 step `2`.

### 6 Device Registration Decision

User entry: the original goal includes connecting the selected physical device.

User action: reviews and separately approves or defers registration.

Visible UI state: Device name, Agent, follow-Live rule, protected credential destination, selected
port, subsequent build/flash, and verification categories appear in a separate card.

Client state change: no Device or credential exists until `Register and connect` is accepted.

Exit / next state: `7 Device Result` or `6.1 Device Registration Deferred`.

### 7 Device Result

User entry: Device registration and subsequent connection steps settle.

User action: reviews the separate evidence rows.

Visible UI state: Cloud Device registration, Device credential, firmware flash, Cloud connection,
and physical behavior each show their own state.

Client state change: successful stages remain available for later recovery.

Exit / next state: end or `8 Later Iteration`.

### 8 Later Iteration

User entry: the user asks to change Agent behavior, test it, and then go live.

User action: waits through local changes and local/static checks.

Visible UI state: a new real `ver_…` ID appears as non-Live; the current Live ID remains unchanged.
The Agent explicitly says that NoraCloud runtime has not executed the candidate.

Client state change: a candidate Version exists without a Live change.

Exit / next state: `9 Temporary Live Decision`.

### 9 Temporary Live Decision

User entry: local checks pass and real Cloud tests are required.

User action: reviews and accepts temporary release, bounded tests, and rollback behavior, keeps the
candidate without Cloud testing, or cancels.

Visible UI state: current Live, candidate, following Devices, five tests, stop condition, and
previous-Live rollback target are explicit. The product does not promise non-Live execution.

Client state change: no release occurs until the temporary impact is accepted.

Exit / next state: `10 Validation Result`, `9.1 Live Conflict`, or end.

### 10 Validation Result

User entry: the temporary release and bounded Cloud tests settle.

User action: reviews the result.

Visible UI state: current and previous Live, test pass/fail count, Recorded Usage, affected Devices,
and rollback state are factual. A successful candidate remains Live; a failed candidate is shown
with the previous Live restored.

Client state change: final Live and validation result are recorded.

Exit / next state: end or `8 Later Iteration` for another fix.

## Branch Journeys

### 2.1 Publication Deferred

Trigger: the user clicks `Not now` at the first cloud boundary.

Visible UI state: the card settles to `Deferred` and confirms that local build and preview remain
available.

Allowed user actions: continue local work or ask to publish later.

Recovery / next state: `1 Local Development`.

Blocks progress: only cloud runtime behavior.

### 2.2 Existing Agent Attach

Trigger: the user supplies or selects an exact existing Agent ID.

Visible UI state: the card shows both Workspace binding change and a new non-Live Version creation.

Allowed user actions: attach and push, choose a different exact Agent, or cancel.

Recovery / next state: `4 Resource Execution`.

Blocks progress: yes until a precise, accessible Agent is chosen.

### 2.3 Unsupported New-Agent Candidate-Only

Trigger: the user asks to create a new Agent without setting its first Version Live.

Visible UI state: a constraint explanation with three truthful choices: publish the new Agent with
its first Live Version, attach an exact existing Agent and create a non-Live Version, or defer.

Allowed user actions: choose one supported path or continue local work.

Recovery / next state: `2 First Cloud Boundary`.

Blocks progress: yes for the unsupported path.

### 4.1 Partial Execution

Trigger: an early resource stage succeeds and a later stage fails.

Visible UI state: successful stages keep checks and real IDs; the failed stage and all unstarted
stages remain visible.

Allowed user actions: follow the safe continuation, open details, or stop.

Recovery / next state: `5 Resource Receipt`.

Blocks progress: only unfinished stages.

### 4.2 Outcome Unknown

Trigger: a mutation may have reached NoraCloud, but no complete response proves the outcome.

Visible UI state: `Outcome unknown`, last confirmed stage, diagnostic reference, and an explanation
that duplicate mutation is paused while state is checked.

Allowed user actions: wait for read-only discovery, open details, or stop for manual recovery.

Recovery / next state: `4 Resource Execution` only after reality is safely determined.

Blocks progress: yes for related mutations.

### 6.1 Device Registration Deferred

Trigger: the user clicks `Not now` on the Device card.

Visible UI state: Device registration is deferred; the Cloud Agent and local Workspace remain
available.

Allowed user actions: continue product work or register later.

Recovery / next state: end or `8 Later Iteration`.

Blocks progress: only real-device Cloud connection.

### 7.1 Device Connection Partial

Trigger: Device and credential creation succeed, but build, flash, network, or physical validation
fails.

Visible UI state: the completed Device and credential remain checked; the exact later failure is
separate.

Allowed user actions: repair and continue from the failed stage, open details, or stop.

Recovery / next state: `7 Device Result` after recovery.

Blocks progress: only unfinished connection/verification.

### 9.1 Live Conflict

Trigger: current Live no longer equals the Version shown when the user accepted the temporary
release plan.

Visible UI state: confirmed Live, new current Live, and target candidate appear together. The prior
decision is invalid.

Allowed user actions: inspect the new Live, re-plan from current state, or cancel.

Recovery / next state: `9 Temporary Live Decision` after a new decision.

Blocks progress: yes.

### 10.1 Validation Failed And Rolled Back

Trigger: one or more bounded tests fail after candidate release.

Visible UI state: failed test count and Usage appear; previous Live is shown as restored; candidate
remains available as non-Live.

Allowed user actions: inspect failures, keep the candidate for fixes, or start another iteration.

Recovery / next state: `10 Validation Result` or `8 Later Iteration`.

Blocks progress: no after rollback is proven.

## Detail States

### 2.4 Resource Scope Details

Trigger: the user expands the proposed operation before deciding.

Visible UI state: Cloud environment, Workspace, Agent/Device, current and target Version,
credential destination, test count, stop condition, and rollback rule.

Allowed user actions: collapse, adjust supported scope, approve, or defer.

Exit / next state: `2 First Cloud Boundary` or `3 Confirmed First Publish`.

### 4.3 Technical Details

Trigger: the user expands technical details during execution.

Visible UI state: command details, structured output, request diagnostic, and logs beneath the product-level
resource stages.

Allowed user actions: collapse or copy non-secret diagnostics.

Exit / next state: `4 Resource Execution`.

### 5.1 View In Cloud

Trigger: the user clicks `View in Cloud` in a receipt.

Visible UI state: the Electron right workbench opens the proposed Cloud overview while the current
conversation stays active.

Allowed user actions: review, refresh, close the panel, or return to the operation message.

Exit / next state: Journey 1 step `2 Cloud Panel Entry`.

### 7.2 Separate Evidence Categories

Trigger: the user reviews a connection result.

Visible UI state: Cloud runtime, Cloud Device connection, firmware flash, and physical behavior use
separate rows and cannot silently substitute for one another.

Allowed user actions: expand evidence or continue from a failed category.

Exit / next state: `7 Device Result`.

## State Language

### 0.1 Resource Operation Status

Visible language: `Waiting for decision`, `Running`, `Completed`, `Partial`, `Outcome unknown`,
`Deferred`, and `Blocked`.

Usage: proposed resource-impact, execution, and receipt cards.

### 0.2 Version Language

Visible language: real `ver_…` identifiers with `Live`, `Latest non-Live`, `Previous Live`, or a
human description. The UI does not invent v1/v2 numbering.

Usage: publish, release, validation, and rollback cards.

### 0.3 Usage Language

Visible language: LLM tokens, turns, optional STT/TTS, explicit Session or Workspace scope, and
coverage. No unsupported input/output split.

Usage: Cloud test receipts and Electron overview.

### 0.4 Secret Language

Visible language: credential type, scope, protected destination, and configured/attention state.
Secret values never appear.

Usage: Device registration plan and result.

## Errors And Recovery

### 4.4 Partial And Unknown Recovery

Trigger: the operation is partial or its remote outcome is unknown.

Visible UI state: confirmed resources remain visible. Unknown results stop duplicate mutation and
name the next safe read-only or manual recovery step.

Allowed user actions: continue from a confirmed failure stage, wait for discovery, open technical
details, or stop.

Recovery / next state: `4 Resource Execution` or `5 Resource Receipt`.

### 6.2 Device Credential And Connection Recovery

Trigger: Device registration, credential save, or later connection work fails.

Visible UI state: Device creation, one-time credential save, build, flash, network, and physical
evidence remain distinct. A credential-save failure never triggers automatic re-registration.

Allowed user actions: follow the safe recovery for the exact failed stage.

Recovery / next state: `6 Device Registration Decision` or `7 Device Result`.

### 9.2 Live Conflict Recovery

Trigger: current Live changes after the user sees the plan.

Visible UI state: the old plan is invalid and no shortcut silently reuses it.

Allowed user actions: inspect, re-plan, or cancel.

Recovery / next state: `9 Temporary Live Decision`.

### 10.2 Failed Validation Recovery

Trigger: temporary candidate validation fails.

Visible UI state: test failures, Usage, and proven rollback state. If rollback cannot be proven, the
operation becomes `Outcome unknown` instead of claiming recovery.

Allowed user actions: inspect, fix the candidate, or stop.

Recovery / next state: end or `8 Later Iteration`.

## Wireframe

- Main happy path: full NoraHarness conversation frames for steps `1` through `10`.
- Route path: one full-frame row for every route in the Route Table.
- Detail states: compact cards for `2.4`, `4.3`, `5.1`, and `7.2`.
- State language: compact cards for `0.1` through `0.4`.
- Errors and recovery: compact cards for `4.4`, `6.2`, `9.2`, and `10.2`; route states use full
  product frames where they occur.
