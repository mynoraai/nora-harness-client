# Journey 1: Cloud Resources Awareness In A Code Workspace

This record is the source of truth for `cloud-resource-awareness.wireframe.html`.
It describes a proposed NoraHarness V2 interaction. The current desktop already has the Code
workspace shell, conversation, right workbench, Device tab, and tool-result cards; the `Cloud`
entry, Cloud Resources overview, Recorded Usage overview, resource receipts, snapshots, and
freshness states are not implemented yet.

The conversation remains the place where the user understands and decides resource-changing
work. The Electron `Cloud` panel is a compact, read-only view of the long-lived result for the
current Workspace.

## Numbering And Route Tables

### Main Path States

| ID  | Type      | Parent step | What it represents                                    | User action                                               | Visible UI state                                                                                                                   | Client state change                                                       | Exit / next state                      |
| --- | --------- | ----------- | ----------------------------------------------------- | --------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- | -------------------------------------- |
| `1` | Main step | Journey 1   | A cloud operation finishes in the conversation.       | Reviews the result and clicks `View in Cloud`.            | The conversation shows a resource receipt with the real Agent, Live Version, Session, and recorded Usage.                          | The current Workspace now has a related cloud-resource result.            | `2`                                    |
| `2` | Main step | Journey 1   | The proposed Cloud workbench entry opens.             | Waits for the panel to load.                              | The existing right workbench selects `Cloud`; the last trusted values remain visible while refresh begins.                         | The panel enters `Refreshing` without clearing the last trusted snapshot. | `3` or a recovery state                |
| `3` | Main step | Journey 1   | Fresh Cloud Resources and Recorded Usage are visible. | Reviews the current Workspace state.                      | One compact panel shows Agent, Live, latest non-Live Version, Devices, Sessions, Credentials, and Workspace-scoped Recorded Usage. | The latest trusted snapshot and update time become current.               | `4` or a detail/branch state           |
| `4` | Main step | Journey 1   | The user returns to the same Workspace later.         | Reopens the Workspace or `Cloud` panel.                   | The last trusted overview appears immediately with `Refreshing`.                                                                   | The saved snapshot is restored while a new read starts.                   | `5`, `4.1`, or `5.1`                   |
| `5` | Main step | Journey 1   | Refresh succeeds.                                     | Continues working or returns to the related conversation. | The panel returns to `Fresh`, updates changed rows and Usage, and preserves the current conversation.                              | The trusted snapshot and update time advance.                             | End or `1` through `Open related chat` |

### Branch States

| ID    | Type   | Parent step                  | What it represents                                                | User action                                                | Visible UI state                                                                                                    | Client state change                                                        | Exit / next state |
| ----- | ------ | ---------------------------- | ----------------------------------------------------------------- | ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | ----------------- |
| `2.1` | Branch | `2 Cloud Panel Entry`        | The user opens Cloud from the Workspace bar instead of a receipt. | Clicks the compact Cloud status in the Workspace bar.      | The right workbench opens on `Cloud` without leaving the active conversation.                                       | The panel begins the same snapshot-and-refresh path.                       | `2`               |
| `3.1` | Branch | `3 Fresh Workspace Overview` | The Workspace has no Cloud Agent binding.                         | Reads the empty state or clicks `Continue in chat`.        | `No Cloud Agent for this Workspace` appears; no zero Usage total or create form is shown.                           | The client records a known unbound state.                                  | End or `1`        |
| `3.2` | Branch | `3 Fresh Workspace Overview` | One credential type needs attention.                              | Reviews the affected credential or opens the related chat. | Credentials remain separated by type; for example, NoraCloud and Device can be ready while `Wi-Fi needs attention`. | The attention state is attached only to the affected credential type.      | End or `1`        |
| `3.3` | Branch | `3 Fresh Workspace Overview` | The most recent resource operation completed only partly.         | Clicks `Open related chat`.                                | Existing Agent/Version/Device rows remain visible; an amber summary says which step is incomplete.                  | The overview retains created resources and links to the operation history. | `1`               |
| `3.4` | Branch | `3 Fresh Workspace Overview` | Recorded Usage does not cover every possible turn source.         | Reads the coverage note.                                   | The Usage block shows the reliable values and `Coverage incomplete`; it does not invent a total.                    | Usage remains available with a limited-coverage marker.                    | `3`               |

### Detail States

| ID    | Type         | Parent step                  | What it represents              | User action                                              | Visible UI state                                                                                                           | Client state change                                        | Exit / next state |
| ----- | ------------ | ---------------------------- | ------------------------------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- | ----------------- |
| `2.2` | Detail state | `2 Cloud Panel Entry`        | Where the Cloud entry appears.  | Uses either `View in Cloud` or the Workspace-bar status. | `Cloud` sits beside `All Files`, `Changes`, `Device`, and `Preview`; `Device` continues to mean the local physical device. | Both entries resolve to the same Workspace Cloud overview. | `2`               |
| `3.5` | Detail state | `3 Fresh Workspace Overview` | The resource summary hierarchy. | Scans rows.                                              | Agent and Live appear first; latest non-Live Version follows; Devices, Sessions, and Credentials use summary rows.         | No mutation occurs.                                        | `3`               |
| `3.6` | Detail state | `3 Fresh Workspace Overview` | The Recorded Usage summary.     | Reads the values.                                        | LLM tokens and turns appear for the explicit Workspace scope; STT time and TTS characters appear only when reported.       | No billing state is inferred.                              | `3`               |

### State Language

| ID    | Type           | Parent step | What it represents             | User action | Visible UI state                                                                                                                                            | Client state change | Exit / next state |
| ----- | -------------- | ----------- | ------------------------------ | ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------------- |
| `0.1` | State language | Journey 1   | Snapshot freshness vocabulary. | None.       | `Fresh`, `Refreshing`, `Stale`, and `Unavailable`, each with a timestamp when one exists.                                                                   | None.               | None              |
| `0.2` | State language | Journey 1   | Credential vocabulary.         | None.       | Per-type `Configured`, `Missing`, `Expired`, or `Needs attention`; no secret or unified false-positive status.                                              | None.               | None              |
| `0.3` | State language | Journey 1   | Usage vocabulary.              | None.       | `Recorded Usage`, an explicit scope such as `Workspace · desk-weather`, and a coverage statement; never `Bill`, `Balance`, or an unqualified `Total Usage`. | None.               | None              |

### Errors And Recovery

| ID    | Type             | Parent step      | What it represents                                                | User action                                               | Visible UI state                                                                                     | Client state change                                                    | Exit / next state   |
| ----- | ---------------- | ---------------- | ----------------------------------------------------------------- | --------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- | ------------------- |
| `4.1` | Recovery state   | `4 Return Later` | Refresh fails but a trusted snapshot exists.                      | Reads the stale state, retries later, or returns to chat. | Last trusted values stay visible with `Stale`, their original time, and a short refresh-failed note. | The old snapshot remains trusted as historical data, not current fact. | `4` on retry or End |
| `5.1` | Recovery state   | `4 Return Later` | No trusted snapshot can be shown.                                 | Retries after sign-in or connectivity recovers.           | `Cloud status unavailable` appears; resource and Usage values are not replaced with zero.            | The panel has no displayable trusted state.                            | `4` on retry        |
| `5.2` | Recovery summary | Journey 1        | Remote facts are unavailable while the user continues local work. | Returns to the conversation or local editor.              | Cloud mutation shortcuts remain absent; local author, preview, and build remain available.           | Only operations requiring confirmed remote facts stay blocked.         | End or `4`          |

### Route Table

| Route                        | Composition                 | Result / next state                                                                          |
| ---------------------------- | --------------------------- | -------------------------------------------------------------------------------------------- |
| Happy path                   | `1 -> 2 -> 3 -> 4 -> 5`     | A result opens the Cloud overview; returning later restores and refreshes the trusted state. |
| Workspace-bar entry          | `2.1 -> 2 -> 3`             | The user opens the same overview without leaving the current conversation.                   |
| Unbound Workspace            | `2 -> 3.1 -> 1`             | The panel states that no Agent is bound and sends creation work back to the conversation.    |
| Credential attention         | `2 -> 3 -> 3.2 -> 1`        | The affected credential is visible by type and recovery continues in chat.                   |
| Partial operation            | `1 -> 2 -> 3.3 -> 1`        | Created resources remain visible and the user returns to the operation history.              |
| Refresh failure and recovery | `3 -> 4 -> 4.1 -> 4 -> 5`   | A stale snapshot remains visible until a later refresh succeeds.                             |
| Usage coverage incomplete    | `2 -> 3 -> 3.4 -> 3`        | Reliable Usage remains visible with an explicit coverage limitation.                         |
| No trusted snapshot          | `2 -> 5.1 -> 5.2 -> 4 -> 5` | The panel shows no invented values; local work continues until refresh can recover.          |

## Main Path

### 1 Resource Receipt In Conversation

User entry: a NoraCloud publish, release, Device registration, or Cloud test has reached a real
result in the active Code conversation.

User action: the user reviews the result and clicks `View in Cloud`.

Visible UI state:

- The result card shows real masked IDs and resource state, not the original plan.
- A successful first publish can show Agent, Live Version, Session, and the Usage recorded by the
  verification turn.
- A partial result keeps every resource already known to exist.
- `View in Cloud` is a secondary action; the next product decision still happens in conversation.

Client state change: the current Workspace is associated with the latest cloud-resource result and
the right panel is asked to open on `Cloud`.

Exit / next state: `2 Cloud Panel Entry`.

### 2 Cloud Panel Entry

User entry: the user clicks `View in Cloud` or the proposed Workspace-bar Cloud status.

User action: the user waits while the panel reads the current state.

Visible UI state:

- The existing right workbench remains beside the active conversation.
- A proposed `Cloud` tab is selected beside `All Files`, `Changes`, `Device`, and `Preview`.
- The last trusted resource values stay visible if they exist.
- The header says `Refreshing`; no mutation starts.

Client state change: the overview restores any trusted Workspace snapshot and requests fresh remote
facts.

Exit / next state: `3 Fresh Workspace Overview`, `4.1 Stale Snapshot`, or
`5.1 Unavailable Without Snapshot`.

### 3 Fresh Workspace Overview

User entry: the Cloud read succeeds.

User action: the user scans the current Workspace summary.

Visible UI state:

- The header shows the Workspace, current Cloud environment, `Fresh`, and an update time.
- Cloud Resources show Agent, Live, latest non-Live Version, Devices, Sessions, and per-type
  Credentials.
- Recorded Usage is a second compact block with an explicit Workspace scope and coverage.
- The only overview actions are refresh and `Open related chat`.
- Create, delete, release, reset, and secret viewing do not appear here.

Client state change: the returned values become the last trusted snapshot.

Exit / next state: the user continues working, opens a branch/detail state, or later reaches
`4 Return Later`.

### 4 Return Later

User entry: the user restarts NoraHarness, reopens the Workspace, or reopens Cloud after time has
passed.

User action: the user opens the Workspace or Cloud overview.

Visible UI state: the last trusted overview appears immediately with its saved time and
`Refreshing`.

Client state change: the stored snapshot is restored and a new read begins.

Exit / next state: `5 Refreshed Overview`, `4.1 Stale Snapshot`, or
`5.1 Unavailable Without Snapshot`.

### 5 Refreshed Overview

User entry: the later refresh succeeds.

User action: the user continues local work or clicks `Open related chat`.

Visible UI state: changed Live, Device, Session, Credential, or Usage rows update; the header
returns to `Fresh` with a new time.

Client state change: the trusted snapshot advances.

Exit / next state: end, or back to `1 Resource Receipt In Conversation` through related chat.

## Branch Journeys

### 2.1 Workspace-Bar Entry

Trigger: a Workspace has cloud state and the user clicks the compact Cloud status in the Workspace
bar.

Visible UI state: the right workbench opens on `Cloud`; the active conversation and editor remain
in place.

Allowed user actions: review, refresh, close the right panel, or open the related conversation.

Recovery / next state: `2 Cloud Panel Entry`.

Blocks progress: no.

### 3.1 Unbound Workspace

Trigger: the current Workspace is known not to have a Cloud Agent binding.

Visible UI state: `No Cloud Agent for this Workspace` and `Continue in chat`. No empty resource
rows, zero Usage, or create form are shown.

Allowed user actions: continue local work, close the panel, refresh, or continue in chat.

Recovery / next state: end or `1 Resource Receipt In Conversation` after a later cloud operation.

Blocks progress: no; only cloud-resource operations are absent.

### 3.2 Credential Attention

Trigger: one credential projection reports missing, expired, or needs attention.

Visible UI state: the Credentials summary names the affected type. Other credentials preserve
their own correct state.

Allowed user actions: open the related chat or the existing dedicated credential surface when one
is available.

Recovery / next state: end or `1 Resource Receipt In Conversation`.

Blocks progress: only the action that consumes the affected credential.

### 3.3 Partial Operation

Trigger: the operation linked to this Workspace created some resources but did not finish all
steps.

Visible UI state: existing resources stay in normal rows; an amber summary names the unfinished
stage and offers `Open related chat`.

Allowed user actions: open the related chat, refresh, or continue unrelated local work.

Recovery / next state: `1 Resource Receipt In Conversation`.

Blocks progress: only the unfinished cloud/device path.

### 3.4 Usage Coverage Incomplete

Trigger: Usage values exist, but their data source does not cover every possible turn source.

Visible UI state: the block shows the reliable LLM tokens and turns, plus `Coverage incomplete`.

Allowed user actions: inspect the scope note or continue working.

Recovery / next state: `3 Fresh Workspace Overview` when coverage later becomes complete.

Blocks progress: no.

## Detail States

### 2.2 Cloud Entry Placement

Trigger: the user needs to open the overview.

Visible UI state:

- `Cloud` is a proposed top tab in the current right workbench.
- `Device` remains the local physical-device surface.
- `View in Cloud` in a resource receipt and the compact Workspace-bar Cloud status both select the
  same tab.

Allowed user actions: use either entry or close the right panel.

Exit / next state: `2 Cloud Panel Entry`.

### 3.5 Resource Summary Hierarchy

Trigger: the Cloud overview has trusted resource data.

Visible UI state: Agent and Live are most prominent; latest non-Live Version appears immediately
below; Devices, Sessions, and Credentials are compact summaries. IDs are masked but distinguishable.

Allowed user actions: scan or open related chat.

Exit / next state: `3 Fresh Workspace Overview`.

### 3.6 Recorded Usage Summary

Trigger: the Workspace has reportable NoraCloud Session usage.

Visible UI state: `Recorded Usage`, explicit Workspace scope, LLM tokens, turns, optional STT/TTS
fields, coverage, and update time. It is visually separate from model context-window usage.

Allowed user actions: read only.

Exit / next state: `3 Fresh Workspace Overview`.

## State Language

### 0.1 Freshness Language

Visible language: `Fresh`, `Refreshing`, `Stale`, and `Unavailable`. `Fresh`, `Refreshing`, and
`Stale` include the relevant last-update time.

Usage: Cloud Resources and Recorded Usage header states.

### 0.2 Credential Language

Visible language: per-type `Configured`, `Missing`, `Expired`, and `Needs attention`. Secret values
never appear.

Usage: Credentials summary row and attention branch.

### 0.3 Usage Scope Language

Visible language: `Recorded Usage`, `Workspace · desk-weather`, `LLM tokens`, `Turns`, and a
coverage statement. The panel does not say `Bill`, `Balance`, or unqualified `Total Usage`.

Usage: the Usage block inside the Cloud overview.

## Errors And Recovery

### 4.1 Stale Snapshot

Trigger: a trusted snapshot exists, but refresh fails.

Visible UI state: last trusted values stay visible with `Stale`, their original time, and a short
refresh-failed note.

Allowed user actions: retry refresh, open related chat, close the panel, or continue local work.

Recovery / next state: `4 Return Later` on retry, then `5 Refreshed Overview` on success.

### 5.1 Unavailable Without Snapshot

Trigger: no trusted snapshot exists and the app cannot read current cloud state.

Visible UI state: `Cloud status unavailable`, a concise sign-in/connectivity explanation when
known, `Retry`, and `Continue in chat`. No resource or Usage value is shown as zero.

Allowed user actions: retry, continue local work, or return to chat.

Recovery / next state: `4 Return Later` when the blocking condition changes.

### 5.2 Remote-State Recovery Summary

Trigger: remote cloud facts are unavailable.

Visible UI state: the Cloud panel communicates the limitation; local author, preview, build, and
already-open conversation remain usable.

Allowed user actions: continue local work, restore sign-in/connectivity, or retry later.

Recovery / next state: `4 Return Later`, then `5 Refreshed Overview`.

## Wireframe

- Main happy path: full NoraHarness product frames for `1 -> 2 -> 3 -> 4 -> 5`.
- Route path: one full-frame row for every route in the Route Table.
- Detail states: compact cards for `2.2`, `3.5`, and `3.6`.
- State language: compact cards for `0.1`, `0.2`, and `0.3`.
- Errors and recovery: compact cards for `4.1`, `5.1`, and `5.2`; full product frames appear in
  route rows where those states are part of a route.
