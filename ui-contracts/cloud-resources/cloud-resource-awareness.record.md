# Journey 1: Cloud Resource Awareness At Operation, Session, And System Levels

This record is the source of truth for `cloud-resource-awareness.wireframe.html`. It defines three
connected scopes without assigning Cloud resource ownership to a Workspace or coding session:

1. one stateful operation card in the conversation;
2. one right-workbench Cloud view for the active coding session;
3. one Settings Cloud category for global system information.

The Code Workspace shell, ordinary tool cards, right workbench, Settings category layout, coding
sessions, and Run Record Usage data exist today. The Cloud operation card, right Cloud tab, remote
inventory, resource references, and Usage summaries are proposed.

## Numbering And Route Tables

### Main Path States

| ID  | Type      | Parent step | What it represents                              | User action                                                           | Visible UI state                                                                                                                   | Client state change                                                        | Exit / next state    |
| --- | --------- | ----------- | ----------------------------------------------- | --------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | -------------------- |
| `1` | Main step | Journey 1   | Local development stays in existing UI.         | Authors, builds, previews, and verifies locally.                      | Normal conversation and existing tool results appear; no new resource card is added.                                               | Local work may change; no Cloud resource is implied.                       | `2`                  |
| `2` | Main step | Journey 1   | Work reaches a persistent Cloud boundary.       | Reviews consequence and proceeds, adjusts supported scope, or defers. | One `Cloud Resource Operation` card shows what will be created, changed, recorded, and excluded.                                   | One operation identity waits with a pending or approved scope.             | `3`, `2.1`, or `2.2` |
| `3` | Main step | Journey 1   | The approved Cloud operation runs.              | Watches stages or expands technical details.                          | The same operation card and ID show factual progress and returned resource IDs.                                                    | Confirmed results accumulate on the operation record.                      | `4`, `4.1`, or `4.2` |
| `4` | Main step | Journey 1   | The operation reaches a stable result.          | Reviews result or opens the current-session Cloud view.               | The same card becomes a factual receipt with Created, Changed, Recorded, Persists, and Not done.                                   | The operation closes and resource references attach to the coding session. | `5` or end           |
| `5` | Main step | Journey 1   | Current coding-session Cloud state is visible.  | Reviews related resources and current-session Usage.                  | Right `Cloud` shows resources used or managed by the active coding session, plus current Coding Session Usage.                     | Session references restore and remote resource state refreshes.            | `6` or end           |
| `6` | Main step | Journey 1   | The user opens global Cloud system information. | Clicks `View all in Settings`.                                        | Settings opens its `Cloud` category with deduplicated resource counts, status, credential attention, and scoped accumulated Usage. | Remote inventory and Usage summaries refresh independently.                | End                  |

### Branch States

| ID    | Type   | Parent step                           | What it represents                                                  | User action                                                                     | Visible UI state                                                                                            | Client state change                                                 | Exit / next state |
| ----- | ------ | ------------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- | ----------------- |
| `2.1` | Branch | `2 Cloud Operation Decision`          | The user's instruction already covers the displayed scope.          | Reads the consequence while execution continues without redundant confirmation. | The same card says `Scope covered by instruction`; no second card appears.                                  | The exact displayed scope becomes approved.                         | `3`               |
| `2.2` | Branch | `2 Cloud Operation Decision`          | The user defers Cloud work.                                         | Chooses `Not now` or continues locally.                                         | The same card says `Deferred`; no resource is claimed.                                                      | The operation closes without mutation.                              | `1` or end        |
| `5.1` | Branch | `5 Current Coding Session Cloud`      | The active coding session has not used or managed a Cloud resource. | Reads the empty state or continues the conversation.                            | `No Cloud resources in this coding session` appears with `View all in Settings`.                            | The empty session reference set becomes known.                      | End or `6`        |
| `5.2` | Branch | `5 Current Coding Session Cloud`      | A related resource changed elsewhere.                               | Reviews fresh remote state or returns to the conversation.                      | The row says `Changed outside this coding session` and shows the current remote state.                      | The session reference remains while cached resource state advances. | `2` or end        |
| `6.1` | Branch | `6 Settings Cloud System Information` | A remote resource has no local coding-session reference.            | Reviews the resource and last activity.                                         | The global inventory keeps the resource and labels `No local coding session reference`.                     | No local session ownership is invented.                             | End               |
| `6.2` | Branch | `6 Settings Cloud System Information` | Global inventory refresh fails.                                     | Reviews saved values, retries, or closes Settings.                              | Last trusted counts remain with `Stale`; without a trusted snapshot the page shows `Unavailable`, not zero. | Historical values remain timestamped until a successful refresh.    | `6` or end        |

### Detail States

| ID    | Type         | Parent step                           | What it represents                                    | User action                           | Visible UI state                                                                                                                       | Client state change                           | Exit / next state |
| ----- | ------------ | ------------------------------------- | ----------------------------------------------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------- | ----------------- |
| `2.3` | Detail state | `2 Cloud Operation Decision`          | One card component covers different Cloud operations. | Expands scope or compares examples.   | First publish, Device registration, Live change, and bounded tests reuse the same component with consequence-specific fields.          | No mutation occurs.                           | `2`               |
| `5.3` | Detail state | `5 Current Coding Session Cloud`      | Session resource relationship.                        | Scans rows.                           | A related Agent, Version, Device, Cloud Runtime Session, or Credential shows remote ID, current state, and sharing/activity elsewhere. | No ownership changes.                         | `5`               |
| `6.3` | Detail state | `6 Settings Cloud System Information` | Global deduplication.                                 | Reviews counts or expands a resource. | Each remote ID is counted once even when several coding sessions reference it.                                                         | No mutation occurs.                           | `6`               |
| `6.4` | Detail state | `6 Settings Cloud System Information` | Usage coverage.                                       | Reads totals and coverage.            | Settings labels the aggregation scope, period, source, and update time; the right panel labels current coding-session Usage.           | No account-wide or billing total is inferred. | `6`               |

### State Language

| ID    | Type           | Parent step | What it represents   | User action | Visible UI state                                                                                                                                         | Client state change | Exit / next state |
| ----- | -------------- | ----------- | -------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------------- |
| `0.1` | State language | Journey 1   | Operation status.    | None.       | `Waiting for decision`, `Scope covered`, `Running`, `Completed`, `Partial`, `Outcome unknown`, `Deferred`, or `Blocked`.                                 | None.               | None              |
| `0.2` | State language | Journey 1   | Resource freshness.  | None.       | `Fresh`, `Refreshing`, `Stale`, or `Unavailable`, with a timestamp when one exists.                                                                      | None.               | None              |
| `0.3` | State language | Journey 1   | Session terminology. | None.       | `Coding session` means NoraHarness work; `Cloud Runtime Session` means the remote NoraCloud resource.                                                    | None.               | None              |
| `0.4` | State language | Journey 1   | Usage terminology.   | None.       | `Coding Session Usage` or `Recorded Usage · All coding sessions on this installation`; never unqualified `Total Usage`, billing, or Cloud Runtime Usage. | None.               | None              |

### Errors And Recovery

| ID    | Type             | Parent step                 | What it represents                                     | User action                                              | Visible UI state                                                                                              | Client state change                                         | Exit / next state |
| ----- | ---------------- | --------------------------- | ------------------------------------------------------ | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- | ----------------- |
| `3.1` | Recovery summary | `3 Cloud Operation Running` | A required credential is missing or unsafe to collect. | Configures it in an approved Settings surface or defers. | The same operation card becomes `Blocked`, naming type, scope, and safe next action without showing a secret. | Only the consuming action pauses.                           | `2` or end        |
| `3.2` | Recovery summary | `3 Cloud Operation Running` | Current Live changed elsewhere after review.           | Reviews the new state or cancels.                        | The same card shows reviewed Live, current Live, target, and invalidated decision.                            | Mutation stops until a new scope is approved.               | `2` or end        |
| `4.1` | Recovery summary | `4 Cloud Operation Receipt` | Some resource stages succeeded and later work failed.  | Reviews retained resources and continues later.          | The same card says `Partial`; created IDs remain visible and duplicate creation is not offered.               | Confirmed references remain attached to the coding session. | `5` or end        |
| `4.2` | Recovery summary | `4 Cloud Operation Receipt` | Remote outcome cannot be proven.                       | Requests a safe read or stops.                           | The same card says `Outcome unknown`; duplicate mutation pauses.                                              | The operation remains unresolved.                           | End               |

### Route Table

| Route                        | Composition                    | Result / next state                                                                     |
| ---------------------------- | ------------------------------ | --------------------------------------------------------------------------------------- |
| Happy path                   | `1 -> 2 -> 3 -> 4 -> 5 -> 6`   | One operation card leads to current-session context and then Settings global awareness. |
| Precise instruction          | `1 -> 2 -> 2.1 -> 3 -> 4 -> 5` | Consequences remain visible without asking the same decision twice.                     |
| Defer Cloud work             | `1 -> 2 -> 2.2 -> 1`           | Local development continues and no Cloud resource is claimed.                           |
| Partial result               | `1 -> 2 -> 3 -> 4.1 -> 5 -> 6` | Confirmed resources remain visible at both information levels.                          |
| Outcome unknown              | `1 -> 2 -> 3 -> 4.2`           | Duplicate mutation stops until remote reality is known.                                 |
| Resource changed elsewhere   | `5 -> 5.2 -> 2`                | Shared remote state is refreshed before another mutation decision.                      |
| Unreferenced global resource | `6 -> 6.1 -> 6`                | Settings preserves resources that no local coding session references.                   |
| Global refresh recovery      | `6 -> 6.2 -> 6`                | Trusted global state remains historical until refresh succeeds.                         |

## Main Path

### 1 Existing Local Development

User entry: the user asks the Agent to build or change a Cloud-connected product.

User action: authors, reviews ordinary tool results, opens Preview, and watches Terminal or Device
Log.

Visible UI state:

- Existing conversation and tool cards report local work.
- Build output remains in existing tool/Terminal surfaces.
- Preview and physical-device information remain in their existing tabs.
- No `Local development`, CPU, disk, cache, or PlatformIO resource-awareness card is added.

Client state change: local files, artifacts, preview, or selected-device state may change. No Cloud
resource is created merely because the task may later use NoraCloud.

Exit / next state: `2 Cloud Operation Decision` only when the next action has a persistent Cloud,
credential, shared runtime, or bounded external-consumption consequence.

### 2 Cloud Operation Decision

User entry: the next action creates or changes a persistent NoraCloud resource.

User action: reviews the exact consequence and proceeds, adjusts a supported field, or defers.

Visible UI state: one `Cloud Resource Operation` card shows a stable operation ID, Cloud identity,
`Will create`, `Will change`, `Will record`, affected shared resources, credential destination when
relevant, bounded test scope, and `Not included`.

Client state change: one operation scope is pending or approved. No mutation occurs while waiting.

Exit / next state: `3 Cloud Operation Running`, `2.1 Scope Covered By Instruction`, or
`2.2 Cloud Work Deferred`.

### 3 Cloud Operation Running

User entry: the exact displayed scope is approved.

User action: watches progress or expands technical details.

Visible UI state: the same operation card and ID remain. Decision controls become factual stages;
real resource IDs appear only when returned; successful stages remain visible after later failure;
raw commands and logs stay collapsed.

Client state change: factual stage results accumulate on the same operation record.

Exit / next state: `4 Cloud Operation Receipt`, a recovery state, `4.1 Partial Result`, or
`4.2 Outcome Unknown`.

### 4 Cloud Operation Receipt

User entry: the operation reaches a stable result.

User action: reviews the result, continues the task, or opens the right-workbench Cloud view.

Visible UI state: the same card becomes a receipt separating `Created`, `Changed`, `Recorded this
operation`, `Persists after this chat`, and `Not done`. It shows true IDs and provides
`Open session Cloud`.

Client state change: the operation closes and factual Cloud resource references attach to the
coding session. Remote resources remain independent of session lifecycle.

Exit / next state: `5 Current Coding Session Cloud` or end.

### 5 Current Coding Session Cloud

User entry: the user selects `Cloud` beside `All Files`, `Changes`, `Device`, and `Preview`, or opens
it from a receipt.

User action: reviews the active coding session's resource working set and Usage.

Visible UI state:

- Header: `Cloud · Current coding session`, session title, freshness, and refresh.
- `Related Cloud Resources`: Agent, Versions, Devices, Cloud Runtime Sessions, and Credential status
  used or managed by this coding session.
- Shared resources say `Also used by …` or `Changed outside this coding session` when applicable.
- `Coding Session Usage`: tokens, runs, turns, and update time from this coding session's Run Records.
- `View all in Settings` opens the global system-information level.
- No resource is described as Workspace-owned or coding-session-owned.

Client state change: session references restore, then each resource refreshes from remote truth.

Exit / next state: `6 Settings Cloud System Information` or end.

### 6 Settings Cloud System Information

User entry: the user opens Settings and selects `Cloud`, or clicks `View all in Settings` from the
right panel.

User action: reviews global Cloud inventory, attention, and explicitly scoped accumulated Usage.

Visible UI state:

- Settings keeps its existing left category navigation; `Cloud` is selected inside Settings.
- System header: current NoraCloud identity/environment, signed-in state, freshness, and refresh.
- `Cloud Resources`: deduplicated counts for Agents, Versions, Devices, and Cloud Runtime Sessions;
  credential attention; resource list with coding-session relationship and last activity.
- `Recorded Usage`: aggregate values from all covered coding-session Run Records with installation,
  period, source, and update time.
- Direct Cloud resource mutation is absent. Settings may link to the relevant coding session or the
  existing credential configuration surface.

Client state change: remote inventory and local recorded Usage refresh independently and retain
separate coverage.

Exit / next state: end.

## Branch Journeys

### 2.1 Scope Covered By Instruction

Trigger: the user's precise instruction already covers the same Cloud, target, consequence, and
bounded test scope displayed by the operation card.

Visible UI state: the same operation card says `Scope covered by instruction`; consequences remain
visible and redundant confirmation controls are absent.

Allowed user actions: stop if still safe, inspect details, or let execution continue.

Recovery / next state: `3 Cloud Operation Running`.

Blocks progress: no.

### 2.2 Cloud Work Deferred

Trigger: the user chooses `Not now` or asks to continue locally.

Visible UI state: the same operation card says `Deferred`, `Cloud resources: none created`, and
`Local work remains available`.

Allowed user actions: continue local work or request the operation later.

Recovery / next state: `1 Existing Local Development` or end.

Blocks progress: only the deferred Cloud outcome.

### 5.1 No Resources In This Coding Session

Trigger: the active coding session has no Cloud resource reference.

Visible UI state: `No Cloud resources in this coding session`; `View all in Settings` remains
available because global resources may still exist.

Allowed user actions: continue the conversation, close the panel, or open Settings Cloud.

Recovery / next state: end or `6 Settings Cloud System Information`.

Blocks progress: no.

### 5.2 Resource Changed Elsewhere

Trigger: a referenced remote resource differs from the last fact recorded by this coding session.

Visible UI state: the affected row shows the current value, prior value, and
`Changed outside this coding session`.

Allowed user actions: return to the conversation, refresh, or continue read-only work.

Recovery / next state: `2 Cloud Operation Decision` before another mutation, or end.

Blocks progress: only mutation based on the old premise.

### 6.1 Resource Without Local Session Reference

Trigger: NoraCloud inventory contains a resource that no local coding session references.

Visible UI state: the resource remains counted and says `No local coding session reference`, with
remote last activity when available.

Allowed user actions: inspect its remote status or close Settings.

Recovery / next state: `6 Settings Cloud System Information`.

Blocks progress: no.

### 6.2 Global Inventory Refresh Failure

Trigger: global Cloud refresh fails.

Visible UI state: a trusted inventory stays visible as `Stale` with its timestamp. Without a trusted
inventory, the page says `Unavailable`; it never replaces unknown state with zero counts.

Allowed user actions: retry, check sign-in, or close Settings.

Recovery / next state: `6 Settings Cloud System Information` after recovery, or end.

Blocks progress: only actions requiring fresh remote truth.

## Detail States

### 2.3 One Component, Different Operations

Trigger: the user expands scope details or the reviewer compares operation families.

Visible UI state: first publish, Device registration, Live change, and bounded tests reuse
`Cloud Resource Operation`; only consequence-specific fields change.

Allowed user actions: return to the operation.

Exit / next state: `2 Cloud Operation Decision`.

### 5.3 Coding Session Resource Relationship

Trigger: the current-session Cloud view has related resources.

Visible UI state: each row shows a true remote ID, current remote status, the coding session's
relationship, and sharing/activity elsewhere where permitted.

Allowed user actions: refresh, return to chat, or open Settings Cloud.

Exit / next state: `5 Current Coding Session Cloud`.

### 6.3 Global Resource Deduplication

Trigger: several coding sessions reference the same remote resource.

Visible UI state: Settings counts the remote ID once and summarizes `Used by 3 coding sessions`
rather than duplicating resource ownership.

Allowed user actions: inspect the resource or associated coding sessions.

Exit / next state: `6 Settings Cloud System Information`.

### 6.4 Usage Coverage

Trigger: the right panel or Settings displays recorded Usage.

Visible UI state: the right panel says `Coding Session Usage`; Settings says
`Recorded Usage · All coding sessions on this installation`, with period, source, coverage, and
updated time. Neither claims account billing or cross-device completeness.

Allowed user actions: read or refresh.

Exit / next state: `5 Current Coding Session Cloud` or `6 Settings Cloud System Information`.

## State Language

### 0.1 Operation Status

Visible language: `Waiting for decision`, `Scope covered`, `Running`, `Completed`, `Partial`,
`Outcome unknown`, `Deferred`, and `Blocked`.

Usage: the single operation card shows one status at a time.

### 0.2 Resource Freshness

Visible language: `Fresh`, `Refreshing`, `Stale`, or `Unavailable`, with a timestamp when one exists.

Usage: the right session view and Settings global inventory use their own clearly scoped freshness.

### 0.3 Session Terminology

Visible language: `Coding session` for NoraHarness work and `Cloud Runtime Session` for the remote
NoraCloud resource.

Usage: every surface where both concepts could appear.

### 0.4 Usage Terminology

Visible language: `Coding Session Usage` and
`Recorded Usage · All coding sessions on this installation`.

Usage: distinguish Run Record aggregation from context-window usage, NoraCloud Runtime Usage,
billing, balance, or an unqualified account total.

## Errors And Recovery

### 3.1 Credential Blocked

Trigger: the operation requires a missing, expired, or unsafe credential path.

Visible UI state: the same operation card says `Blocked`, names credential type and consuming action,
and links to an approved Settings surface without displaying a secret.

Allowed user actions: configure safely, defer, or cancel.

Recovery / next state: `2 Cloud Operation Decision` or end.

### 3.2 Live Changed Elsewhere

Trigger: current Live differs from the value shown when the operation scope was approved.

Visible UI state: the same card shows reviewed Live, current Live, and target, then says the previous
decision no longer applies.

Allowed user actions: review the new state, plan again, or cancel.

Recovery / next state: `2 Cloud Operation Decision` or end.

### 4.1 Partial Result

Trigger: at least one real resource stage succeeds and a later stage fails.

Visible UI state: the same operation card says `Partial`, keeps real IDs, separates completed and
unfinished work, and does not offer duplicate creation.

Allowed user actions: inspect, open session Cloud, continue locally, or resume later.

Recovery / next state: `5 Current Coding Session Cloud` or end.

### 4.2 Outcome Unknown

Trigger: a request may have reached NoraCloud but its result cannot be proven.

Visible UI state: the same operation card says `Outcome unknown`, shows the last confirmed fact,
offers a safe read, and pauses duplicate mutation.

Allowed user actions: check current state, inspect diagnostics, or stop.

Recovery / next state: end until remote reality becomes known.

## Wireframe

- Main happy path: existing local UI, one stateful operation card, current-session right Cloud, and
  Settings Cloud system information.
- Route path: one full product-frame row for every route in the route table.
- Detail states: compact cards for component reuse, session relationship, deduplication, and Usage
  coverage.
- State language: compact cards for operation, freshness, session terminology, and Usage scope.
- Errors and recovery: compact cards for credential, Live conflict, partial, and unknown states.

- [PM review](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.pm.md)
- [Wireframe preview](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)
- [Wireframe source](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)
