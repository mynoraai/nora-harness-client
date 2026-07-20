# Cloud Resource Awareness PM Review

## Product Decision

NoraHarness needs to make NoraCloud mutations and persistent resources understandable at the point
where the user is working. The final proposal separates three information scopes:

1. Conversation: the requested Cloud mutation, the user's approval, and the factual receipt.
2. Workspace: the local Agent definition, NoraCloud binding, and last trusted Cloud state relevant
   to that Workspace.
3. Settings: the signed-in account's browsable NoraCloud resources and cumulative recorded Usage.

Cloud resources are not owned by a coding Conversation. A Conversation records what it requested
and what happened; the remote Agent, Versions, Devices, and Cloud Sessions persist independently.

## Existing And Proposed Behavior

Local file edits, validation, firmware builds, flash, Terminal, Device Log, Device, and Preview
continue to use existing Agent messages and tool calls. Editing a Markdown or configuration file
does not create a new resource-awareness card.

The following surfaces are proposed:

- one approval card immediately before a persistent NoraCloud mutation;
- one factual receipt after the mutation reaches a stable result;
- one Workspace-scoped right-workbench Cloud tab;
- one account-scoped Cloud category inside Settings with resource drill-down.

There is no additional product-level execution screen between approval and receipt. Underlying CLI
and tool progress may remain available in technical details.

## Conversation Interaction

When the user asks to update an existing Agent from Workspace changes, the proposal states:

- the Agent and current Live Version;
- that a new immutable Version will be created;
- the resulting Live transition;
- how many Devices follow Live;
- work outside the operation, such as firmware build, flash, Device registration, or Cloud test.

The only visible actions are `Approve` and `Not now`. A normal reply of `Approve` accepts the same
single pending proposal as the button. The card does not expose CLI flags, `Candidate`, or a generic
`Change scope` action.

After approval, the next product state is a factual receipt. It separates the created Version,
Live transition, affected Devices, persistent consequences, and excluded work. Partial and unknown
outcomes retain confirmed identifiers and prevent unsafe duplicate mutation.

## Workspace Cloud

The right `Cloud` tab is scoped to the current Workspace, not the currently selected coding
Conversation. Switching Conversations inside the same Workspace leaves the panel unchanged;
switching Workspaces changes it.

The panel shows:

- the bound Agent and current Live Version;
- readable Agent configuration such as identity, behavior, LLM, listening, and speaking;
- the status of important local definition files such as `SOUL.md`, `IDENTITY.md`, `USER.md`, and
  `cloud-agent.json`;
- related Devices, Cloud Session state when relevant, and NoraCloud access readiness;
- freshness and refresh state.

Coding Agent token totals and Run Records are not shown here as Cloud Usage.

## Settings Cloud

Cloud remains inside Settings as account-level system information. The overview provides two
browsable resource roots plus cumulative recorded Usage:

- `Agents`: opens the account Agent list. Opening an Agent reveals its current Live configuration,
  Versions, and Devices.
- `Cloud Sessions`: opens account Sessions with status, relationships, activity, turns, and
  available recorded Usage.
- `Recorded Usage`: shows NoraCloud-recorded account totals and opens the same Sessions list focused
  by Usage. It is not billing, balance, or remaining quota, and it never substitutes local coding
  Run Records.

Versions and Devices are children of their owning Agent rather than unsupported account-wide
categories. The global overview does not repeat credential inventory.

### Agent And Version Detail

Agent Detail shows identity, current Live, creation/update time, readable Live configuration,
Versions, and Devices. Version Detail shows the immutable Version's status, description, LLM, STT,
TTS, schedules, and published instruction filenames.

The current NoraCloud read surface returns instruction filename, size, and digest rather than full
remote Markdown content. Therefore the product may open a matching local file or compare a changed
local file, but it must not fabricate or present unavailable remote Markdown bodies.

### Device And Session Detail

Device Detail shows ownership, connection, follow-Live or pinned track, resolved Version, last seen,
and current Session. It never displays credential material.

Session Detail shows status, Agent, optional Device, Version at start, timestamps, turn count,
context tokens, and available LLM/STT/TTS Usage. Logs and export are secondary read surfaces. This
proposal does not add destructive controls such as deleting resources or ending a Session.

## Scope Rules

| Surface | Scope | Primary question |
| --- | --- | --- |
| Conversation proposal and receipt | One requested NoraCloud mutation | What will persist, may affect Devices, and actually happened? |
| Right `Cloud` tab | Current Workspace | Which Agent is this Workspace linked to and what is the current relevant Cloud truth? |
| Settings `Cloud` | Signed-in NoraCloud account | Which persistent resources exist overall and what recorded Cloud Usage is available? |

## Non-Goals

- No resource card for ordinary local build, cache, CPU, disk, or Preview activity.
- No `Used By This Coding Session` resource inventory.
- No coding-session token Usage in the right Cloud tab.
- No separate Candidate resource type or unsupported `Change scope` control.
- No account-wide Version or Device category detached from its Agent.
- No secret values or full remote Markdown body.
- No direct create, delete, release, rollback, reset, or Session termination controls in Settings.
- No claim that recorded Usage is billing or remaining quota.

## Dependencies And Risks

- Electron needs structured NoraCloud operation results to render approval consequences and factual
  receipts without parsing prose.
- Remote Live may change outside the current Workspace; approval premises must be rechecked before
  mutation.
- Workspace-to-Agent binding and local-file digests are required for safe local/open/compare actions.
- Resource and Usage refreshes need explicit freshness, stale, and unavailable states.
- The product must preserve the distinction between a NoraHarness Conversation and a NoraCloud
  runtime Session.
