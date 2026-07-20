# Journey 1: Cloud Resource Awareness Across Conversation, Workspace, And Settings

This record is the source of truth for `cloud-resource-awareness.wireframe.html`.
It covers one focused journey: local Workspace changes are ready, the user asks to update the bound
NoraCloud Agent, approves one explicit proposal, and receives a factual result.

Ordinary Agent messages and tool calls already exist. The Cloud proposal, factual resource receipt,
Workspace Cloud overview, and global Cloud system page inside Settings are proposed.

## Numbering And Route Tables

### Main Path States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main step | Journey 1 | Local Agent change is in progress. | Asks for a behavior or model change. | The Agent explains the local scope. Completed file tools are collapsed; the active Terminal tool shows its real `Running` state and command detail. | Workspace files change; NoraCloud and firmware remain unchanged. | `2` |
| `2` | Main step | Journey 1 | Local work reaches a stable result. | Reviews the Assistant response and Workspace Changes. | A normal Assistant message summarizes the changes and states that firmware and NoraCloud were not changed. | The local turn closes with validated Workspace changes. | `3` after an explicit Cloud-update request, or end |
| `3` | Main step | Journey 1 | One exact Cloud update waits for approval. | Reviews the proposal, clicks `Approve`, replies `Approve`, or chooses `Not now`. | The card names the Agent, current Live, new Version, Live transition, affected Devices, and excluded work. Only `Approve` and `Not now` are actions. | One proposal becomes the pending approval target; no mutation starts before approval. | `4` or `3.1` |
| `4` | Main step | Journey 1 | The Cloud update reaches a stable result. | Reviews the receipt or opens Cloud overview. | The receipt separates the created Version, Live transition, affected Devices, and work that did not occur. There is no separate product-level execution screen. | The operation closes and the Workspace Cloud snapshot begins refreshing. | End, `4.1`, or `4.2` |

### Branch States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Branch | `3 Cloud Update Proposal` | The user defers the update. | Clicks `Not now` or gives an equivalent reply. | The proposal settles to `Deferred`; validated Workspace changes remain available. | No Cloud mutation occurs. | `2` or end |
| `4.1` | Branch | `4 Resource Receipt` | The result is partial or cannot be confirmed. | Reviews known facts and the next safe action. | Confirmed resources remain visible. Failed stages are distinct; an unknown outcome pauses duplicate mutation. | Recovery starts from confirmed state rather than repeating the whole operation. | `4` after recovery or end |

### Detail States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1.1` | Detail state | `1 Local Agent Change In Progress` | Firmware is part of another local task. | Watches the actual firmware tool. | The active Build or Flash tool is expanded; completed file and preview tools remain collapsed. | Firmware evidence changes only when the corresponding tool returns. | `1` or `2` |
| `3.2` | Detail state | `3 Cloud Update Proposal` | Approval input method. | Clicks `Approve` or sends `Approve` as a normal reply. | Button approval resolves the confirmation in place; conversational approval appears as a user message. Both accept the same one pending proposal exactly once. | The proposal becomes approved and execution starts. | `4` |
| `4.2` | Detail state | `4 Resource Receipt` | Open the Workspace Cloud overview. | Clicks `Open Cloud overview`. | The right workbench selects `Cloud`, preserves the conversation, shows the last trusted snapshot, and refreshes. | The Workspace Cloud snapshot starts refreshing. | Journey 1, step `2` |
| `4.3` | Detail state | `4 Resource Receipt` | Technical details. | Expands technical details. | Command summary, returned identifiers, diagnostics, and logs are available below the product result. | Expanding details changes no Cloud state. | `4` |
| `4.5` | Detail state | `4 Resource Receipt` | Open the global Cloud system overview in Settings. | Opens `Settings`, then selects `Cloud`. | A proposed Settings category provides account-level entry points for Agents and Cloud Sessions plus cumulative Recorded Usage. Versions and Devices are reached through their owning Agents. | The global Cloud snapshot begins refreshing; no Cloud mutation occurs. | `4.5.1`, `4.5.2`, `4`, or end |
| `4.5.1` | Detail state | `4.5 Global Cloud Overview In Settings` | Browse account Agents. | Opens `Agents`. | The list shows each Agent's name, masked ID, Live Version, and creation/update time. | The selected resource category becomes Agents; no Cloud mutation occurs. | `4.5.1.1` or `4.5` |
| `4.5.1.1` | Detail state | `4.5.1 Agents List` | Inspect one Agent. | Opens an Agent row. | The detail shows Agent identity, current Live, creation/update time, Live configuration summary, Versions, and Devices. | The selected Agent becomes the Settings resource context. | `4.5.1.1.1`, `4.5.1.1.2`, or `4.5.1` |
| `4.5.1.1.1` | Detail state | `4.5.1.1 Agent Detail` | Inspect an immutable Version. | Opens a Version row. | The detail shows Version status, description, LLM/voice/cron configuration, and published instruction filenames. Matching local files may be opened or compared; remote Markdown content is not fabricated. | No Cloud state changes. | `4.5.1.1` |
| `4.5.1.1.2` | Detail state | `4.5.1.1 Agent Detail` | Inspect one Device. | Opens a Device row. | The detail shows owning Agent, connection, follow-Live or pinned track, resolved Version, last seen time, and current Session. | No Cloud state changes. | `4.5.1.1`, `4.5.2.1`, or end |
| `4.5.2` | Detail state | `4.5 Global Cloud Overview In Settings` | Browse account Cloud Sessions. | Opens `Cloud Sessions` or `View by Session` from Recorded Usage. | The account-wide list supports active/ended and Device filters and shows Agent, Device, Version at start, last activity, turns, and recorded Usage. | The selected resource category becomes Sessions; no Cloud mutation occurs. | `4.5.2.1` or `4.5` |
| `4.5.2.1` | Detail state | `4.5.2 Sessions List` | Inspect one Cloud Session. | Opens a Session row. | The detail shows status, Agent, Device, Version at start, timestamps, turn count, and available LLM/STT/TTS Usage. Logs/export remain secondary and no destructive action is offered. | No Cloud state changes. | `4.5.2` or end |

### State Language

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Journey 1 | Proposal and result status. | None. | `Waiting for you`, `Deferred`, `Completed`, `Partial`, and `Outcome unknown`. | None. | None |
| `0.2` | State language | Journey 1 | Version language. | None. | Real masked `ver_…` IDs with `Live`, `Non-Live`, or `Previous Live`; no invented v1/v2 numbering. | None. | None |
| `0.3` | State language | Journey 1 | Approval language. | None. | `Approve` and `Not now`. Clicking or replying `Approve` accepts the same one pending proposal. | None. | None |
| `0.4` | State language | Journey 1 | Secret language. | None. | Credential type, protected destination, and status may appear; secret values do not. | None. | None |

### Errors And Recovery

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.4` | Recovery summary | `4 Resource Receipt` | Partial and unknown outcomes. | Reads confirmed resources and the next safe action. | Known IDs survive failure. Unknown outcomes stop automatic duplicate mutation until read-only discovery establishes reality. | Recovery resumes from the last confirmed stage. | `4` or end |

### Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Approve Cloud update | `1 -> 2 -> 3 -> 4` | Button approval or a typed `Approve` produces the same factual receipt. |
| Defer Cloud update | `1 -> 2 -> 3 -> 3.1 -> 2` | Local validated changes remain available with no Cloud mutation. |
| Result needs attention | `1 -> 2 -> 3 -> 4.1` | Confirmed facts remain visible and recovery avoids unsafe duplicate mutation. |

## Main Path

### 1 Local Agent Change In Progress

User entry: the user asks to change Agent behavior or its reasoning model.

User action: watches ordinary Agent progress and may inspect completed tools.

Visible UI state:

- The Agent says it is changing the local Agent definition and not changing NoraCloud in this step.
- Completed file tools use the existing collapsed tool-card format.
- The active validation command appears as a `Terminal` tool with `Running` status and expandable command detail.
- No synthesized task-stage or file-summary card appears.

Client state change: local Agent files change; Cloud and firmware state do not.

Exit / next state: `2 Local Agent Change Ready`.

### 2 Local Agent Change Ready

User entry: local validation completes.

User action: reviews the normal Assistant message and Workspace Changes.

Visible UI state: the message names the behavioral and model changes, says firmware was not
modified, and says NoraCloud was not changed. A Cloud proposal does not appear until the user asks
for a Cloud update.

Client state change: the local turn closes with validated Workspace changes.

Exit / next state: end, or `3 Cloud Update Proposal`.

### 3 Cloud Update Proposal

User entry: the user asks to update the bound NoraCloud Agent with the Workspace changes.

User action: reviews the exact proposal, approves it by button or reply, or defers it.

Visible UI state: the proposal names current Live, the new Version, the Live transition, affected
Devices, and excluded firmware/device work. Actions are `Approve` and `Not now`; the user may also
reply `Approve`.

Client state change: one proposal becomes the pending approval target; no mutation occurs before
approval.

Exit / next state: `4 Resource Receipt` after execution, or `3.1 Cloud Update Deferred`.

### 4 Resource Receipt

User entry: the approved update reaches a stable result.

User action: reviews consequences or opens Cloud overview.

Visible UI state: created Version, Live transition, affected Devices, and explicitly excluded
firmware, Device registration, and Cloud testing are separated. The PM journey does not insert a
separate execution-progress screen between approval and receipt.

Client state change: the operation closes and the Workspace Cloud snapshot begins refreshing.

Exit / next state: end, `4.1 Result Needs Attention`, or `4.2 Open Cloud Overview`.

## Branch Journeys

### 3.1 Cloud Update Deferred

Trigger: the user selects `Not now` or gives an equivalent reply.

Visible UI state: the proposal settles to `Deferred`; local validated changes remain available.

Allowed user actions: continue local work or request the Cloud update later.

Recovery / next state: `2 Local Agent Change Ready` or end.

Blocks progress: only the Cloud update.

### 4.1 Result Needs Attention

Trigger: some resource stages completed before failure, or the remote outcome cannot be confirmed.

Visible UI state: confirmed resources retain their IDs. Failed and unstarted stages remain distinct.
For an unknown outcome, automatic retry is paused and a safe read-only check is offered.

Allowed user actions: inspect technical details, check current Cloud state, or stop.

Recovery / next state: `4 Resource Receipt` after recovery, or end.

Blocks progress: only related Cloud mutation.

## Detail States

### 1.1 Firmware Tool Progress

Trigger: the local task actually includes firmware work.

Visible UI state: the active firmware tool is expanded with its real Running state; completed file
and preview tools stay collapsed.

Allowed user actions: inspect completed tools, watch the running tool, or stop the run.

Exit / next state: `1 Local Agent Change In Progress` or `2 Local Agent Change Ready`.

### 3.2 Approval Input Method

Trigger: one proposal is pending.

Visible UI state: clicking `Approve` resolves the confirmation in place. Sending `Approve` appears
as a normal user message. Both accept the same proposal and lead to the same result.

Allowed user actions: use either approval input, or select `Not now`.

Exit / next state: `4 Resource Receipt` or `3.1 Cloud Update Deferred`.

### 4.2 Open Cloud Overview

Trigger: the user clicks `Open Cloud overview`.

Visible UI state: the right workbench selects `Cloud`, keeps the conversation active, restores the
last trusted snapshot, and refreshes it.

Allowed user actions: review, refresh, return to the conversation, or open Settings for the global
Cloud inventory.

Exit / next state: Journey 1, step `2`.

### 4.3 Technical Details

Trigger: the user expands technical details from the receipt.

Visible UI state: command summary, returned identifiers, diagnostics, and logs appear below the
product result.

Allowed user actions: inspect or collapse details.

Exit / next state: `4 Resource Receipt`.

### 4.5 Global Cloud Overview In Settings

Trigger: the user opens `Settings` and selects the proposed `Cloud` category.

Visible UI state: the existing full-screen Settings shell remains intact. The proposed Cloud page
shows an account-wide Agents entry, an account-wide Cloud Sessions entry, and cumulative Recorded
Usage. Versions and Devices are described as children of an Agent rather than presented as
unsupported account-wide totals. `View by Session` opens the same Sessions list rather than creating
another unrelated Usage destination. Credential information is not repeated on this overview.

The page states its global account scope and update time. It does not present current coding-session
context usage, Workspace-only totals, billing, balance, or remaining quota.

Allowed user actions: refresh the global snapshot, open Agents, open Cloud Sessions, view Usage by
Session, or return to the app.

Exit / next state: `4.5.1 Agents List`, `4.5.2 Sessions List`, `4 Resource Receipt`, or end.

### 4.5.1 Agents List

Trigger: the user opens Agents from the global Cloud overview.

Visible UI state: the Settings shell remains. `Cloud / Agents` lists the account's Agents with name,
masked ID, Live Version, and created/updated time. Versions and Devices are not presented as
unsupported account-wide lists or counts.

Allowed user actions: open an Agent or return to the Cloud overview.

Exit / next state: `4.5.1.1 Agent Detail` or `4.5 Global Cloud Overview In Settings`.

### 4.5.1.1 Agent Detail

Trigger: the user selects one Agent.

Visible UI state: Agent name and masked ID, current Live, created/updated time, and the Live
configuration summary appear first. Versions show real IDs, Live/non-Live state, description, and
creation time. Devices show name, connection, track/resolved Version, and last seen state.

Allowed user actions: open a Version, open a Device, or return to Agents.

Exit / next state: `4.5.1.1.1 Version Detail`, `4.5.1.1.2 Device Detail`, or `4.5.1 Agents List`.

### 4.5.1.1.1 Version Detail

Trigger: the user opens one Version.

Visible UI state: the immutable Version shows Agent, real ID, Live/status, description, creation
time, LLM, STT, TTS, and cron summary. The instruction bundle lists filenames such as `SOUL.md`,
`IDENTITY.md`, `USER.md`, and Skill Markdown. Full remote Markdown is not shown because the current
read API returns filename, size, and digest rather than content.

When a known local Workspace contains the same file and its digest matches, the row offers
`Open local file`. When the digest differs, it offers `Compare`. A file with no matching local source
is labeled `Not available locally`. Digests and sizes remain under technical details.

Allowed user actions: open a verified local file, compare a changed local file, expand technical
details, or return to the Agent.

Exit / next state: `4.5.1.1 Agent Detail`.

### 4.5.1.1.2 Device Detail

Trigger: the user opens one Device from Agent Detail.

Visible UI state: Device name and masked ID, owning Agent, connection, follow-Live or pinned track,
resolved Version, last seen time, and current Session are shown. Credential material is not shown.

Allowed user actions: open the current Session when present or return to the Agent.

Exit / next state: `4.5.2.1 Session Detail`, `4.5.1.1 Agent Detail`, or end.

### 4.5.2 Sessions List

Trigger: the user opens Cloud Sessions or selects `View by Session` from Recorded Usage.

Visible UI state: an account-wide list shows active/ended status, Agent, optional Device, Version at
start, last activity, turns, and available recorded Usage. Active/ended and Device filters are
available. Entering from Usage sorts or focuses the same list by Usage without changing resource
semantics.

Allowed user actions: filter, open a Session, or return to the Cloud overview.

Exit / next state: `4.5.2.1 Session Detail` or `4.5 Global Cloud Overview In Settings`.

### 4.5.2.1 Session Detail

Trigger: the user opens one Cloud Session.

Visible UI state: Session ID and status, Agent, Device when present, Version at start, started/ended
timestamps, last interaction, turn count, context tokens, and available LLM/STT/TTS Usage are shown.
Logs and export are secondary read surfaces. No `End Session` action is included in this
system-information proposal.

Allowed user actions: inspect logs, export the Session record, open related resources, or return to
Sessions.

Exit / next state: `4.5.2 Sessions List` or end.

## State Language

### 0.1 Proposal And Result Status

Visible language: `Waiting for you`, `Deferred`, `Completed`, `Partial`, and `Outcome unknown`.

### 0.2 Version Language

Visible language: real masked `ver_…` IDs with `Live`, `Non-Live`, or `Previous Live`.

### 0.3 Approval Language

Visible language: `Approve` and `Not now`. Button and conversational approval accept the same one
pending proposal.

### 0.4 Secret Language

Visible language: credential type, protected destination, and status. Secret values never appear.

## Errors And Recovery

### 4.4 Partial And Unknown Outcomes

Known resource IDs and completed stages remain visible. Partial execution resumes from the failed
stage. Unknown execution pauses duplicate mutation until read-only discovery or manual recovery
establishes reality.

## Wireframe

The matching artifact is `cloud-resource-awareness.wireframe.html`.
