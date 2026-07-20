# Journey 1: Cloud Resource Awareness Across Conversation, Workspace, And Settings

This record is the source of truth for `cloud-resource-awareness.wireframe.html`. It defines three
connected scopes:

1. one stateful Cloud operation card in the Conversation that performs the mutation;
2. one right-workbench Cloud view for the current Workspace;
3. one Settings Cloud category for the signed-in identity's global system information.

The Conversation records the decision and receipt. The Workspace contains the local Agent
definition and NoraCloud binding. NoraCloud resources persist independently of both. The Cloud UI
in this journey is proposed; the surrounding Conversation, Workspace, right-workbench, and Settings
shapes already exist.

## Numbering And Route Tables

### Main Path States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main step | Journey 1 | Local Agent work stays in existing UI. | Edits and verifies the Agent locally. | Existing conversation, file, tool, Terminal, Device, and Preview surfaces report local work; no Cloud card appears. | Workspace files may change; no Cloud resource changes. | `2` |
| `2` | Main step | Journey 1 | An existing linked Agent is ready for a remote update. | Reviews changed sources and selects `Create Version`, `Create and make Live`, or `Not now`. | One Cloud operation card compares Workspace changes with Live and states Device impact. | One exact operation scope waits for a decision. | `3`, `2.1`, `2.2`, or `2.3` |
| `3` | Main step | Journey 1 | The selected Version-and-Live update runs. | Watches factual stages or opens technical details. | The same card creates a Version, moves Live, and verifies the returned remote state. | Confirmed stage results accumulate on one operation record. | `4`, `3.1`, `3.2`, `4.1`, or `4.2` |
| `4` | Main step | Journey 1 | The Agent update reaches a stable result. | Reviews the receipt or opens Workspace Cloud. | The same card states the created Version, Live transition, affected Devices, changed configuration, and work not performed. | The operation closes; the receipt remains in this Conversation. | `5` or end |
| `5` | Main step | Journey 1 | Current Workspace Cloud state is visible. | Reviews local-vs-Live configuration and linked resources. | Right `Cloud` identifies the current Workspace, readable Agent configuration, source drift, bound Agent, Live Version, Devices, Cloud Conversation, and access readiness. | Workspace binding and remote facts refresh; no Conversation ownership is created. | `6` or end |
| `6` | Main step | Journey 1 | Global Cloud system information is visible. | Opens `View all in Settings`. | Settings shows current NoraCloud identity, deduplicated resources, credential attention, and honest NoraCloud Usage availability. | Remote inventory and Usage availability refresh independently. | End |

### Branch States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Branch | `2 Agent Update Decision` | The user's instruction already explicitly requests the displayed Version-and-Live consequence. | Reads the consequence while execution continues without a redundant question. | The same card says `Scope covered by instruction`; all consequences remain visible. | The displayed scope becomes approved. | `3` |
| `2.2` | Branch | `2 Agent Update Decision` | The user defers Cloud work. | Chooses `Not now`. | The same card says `Deferred`; local files remain available and no remote change is claimed. | The operation closes without mutation. | `1` or end |
| `2.3` | Branch | `2 Agent Update Decision` | The user creates a new immutable Version without changing Live. | Chooses `Create Version`. | The card explicitly says Live remains v12 and Devices following Live are unaffected. | Version-only scope becomes approved. | `2.3.1` |
| `2.3.1` | Branch | `2.3 Create Version Without Changing Live` | The non-Live Version is created. | Reviews the receipt or opens Workspace Cloud. | The same card says `Version v13 created`, `Live remains v12`, and `Device impact: none`. | The new Version persists; Live does not move. | `5` or end |
| `5.1` | Branch | `5 Current Workspace Cloud` | The Workspace has no valid NoraCloud binding. | Reads the empty state or starts a publish request in Conversation. | `This Workspace is not linked to NoraCloud` appears; global Settings remains available. | Absence of a valid Workspace binding becomes known. | `2`, `6`, or end |
| `5.2` | Branch | `5 Current Workspace Cloud` | Local configuration or remote Live changed since the last trusted comparison. | Reviews the new comparison or returns to Conversation. | The panel names `Changed locally` sources or a changed remote Live Version and prevents stale mutation assumptions. | Workspace comparison advances to current local and remote facts. | `2` or end |
| `6.1` | Branch | `6 Settings Cloud System Information` | A remote Agent has no link from a local Workspace. | Reviews the Agent and last activity. | The global inventory keeps it and labels `No local Workspace link`. | No local ownership is invented. | `6` or end |
| `6.2` | Branch | `6 Settings Cloud System Information` | Global inventory refresh fails. | Reviews saved values, retries, or closes Settings. | Last trusted values remain `Stale`; without a snapshot the page says `Unavailable`, never zero. | Historical values remain timestamped. | `6` or end |

### Detail States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.4` | Detail state | `2 Agent Update Decision` | One card supports several NoraCloud mutations. | Compares operation examples. | Existing-Agent update, first publish, release, Device registration, and bounded Cloud tests reuse the same component with operation-specific consequences. | No mutation occurs. | `2` |
| `2.5` | Detail state | `2 Agent Update Decision` | First publish has a different invariant. | Reviews first-publish details. | The card says first publish creates the Agent and first Version and sets that Version Live; Version-only is not offered. | No mutation occurs. | `2` |
| `5.3` | Detail state | `5 Current Workspace Cloud` | Readable Agent configuration. | Scans the primary card. | Identity, Behavior, Live Version, LLM, listening, and speaking are readable without interpreting IDs. | No configuration changes. | `5` |
| `5.4` | Detail state | `5 Current Workspace Cloud` | Configuration source comparison. | Opens a source or compares with Live. | `IDENTITY.md`, `SOUL.md`, `USER.md`, and `cloud-agent.json` show `Live`, `Changed locally`, `Not published`, or `Missing`; full content opens in the main area. | No configuration changes. | `5` |
| `5.5` | Detail state | `5 Current Workspace Cloud` | Workspace binding and linked resources. | Scans related resources. | The bound Agent, Live Version, Devices on that Agent, stored Cloud Conversation, and Cloud access readiness are visible without claiming Workspace ownership. | No ownership changes. | `5` |
| `6.3` | Detail state | `6 Settings Cloud System Information` | Global resource deduplication. | Reviews counts or expands a resource. | Each NoraCloud resource is counted once; local Workspace links are secondary context. | No mutation occurs. | `6` |
| `6.4` | Detail state | `6 Settings Cloud System Information` | NoraCloud Usage availability. | Reads totals or the unavailable state. | Complete NoraCloud LLM, STT, and TTS totals show only when account-wide metering exists; otherwise the card says `Unavailable`. | No total is inferred from coding Run Records. | `6` |

### State Language

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Journey 1 | Operation status. | None. | `Waiting for decision`, `Scope covered`, `Running`, `Completed`, `Partial`, `Outcome unknown`, `Deferred`, or `Blocked`. | None. | None |
| `0.2` | State language | Journey 1 | Resource freshness. | None. | `Fresh`, `Refreshing`, `Stale`, or `Unavailable`, with a timestamp when one exists. | None. | None |
| `0.3` | State language | Journey 1 | Scope terminology. | None. | `Conversation` is the NoraHarness interaction history; `Workspace` is the shared local project context; `Cloud Conversation` is the NoraCloud remote conversation. | None. | None |
| `0.4` | State language | Journey 1 | Version terminology. | None. | `Version` is immutable; `Live` is the Agent pointer used by Devices following Live. `Candidate` is not a separate resource label. | None. | None |
| `0.5` | State language | Journey 1 | Usage terminology. | None. | `NoraCloud Usage` means complete Cloud metering. Coding Agent tokens and Run Records are not Cloud Usage. | None. | None |

### Errors And Recovery

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Recovery summary | `3 Agent Update Running` | Required NoraCloud access is unavailable. | Opens the approved Settings surface or defers. | The same operation card becomes `Blocked`, names the access problem, and never displays a secret. | Only the consuming operation pauses. | `2` or end |
| `3.2` | Recovery summary | `3 Agent Update Running` | Live moved after the user reviewed the operation. | Reviews current Live or cancels. | The card shows reviewed Live, current Live, and target; the previous decision is invalidated. | Live mutation stops until reviewed again. | `2` or end |
| `4.1` | Recovery summary | `4 Agent Update Receipt` | Version creation succeeded but changing Live failed. | Reviews retained Version and current Live. | The same card says `Partial`, keeps the created Version, and states that Live and Devices did not change. | The Version persists; confirmed facts remain available. | `5` or end |
| `4.2` | Recovery summary | `4 Agent Update Receipt` | Remote outcome cannot be proven. | Requests a safe read or stops. | The same card says `Outcome unknown`; duplicate mutation remains paused. | The operation remains unresolved. | End |

### Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Update and make Live | `1 -> 2 -> 3 -> 4 -> 5 -> 6` | A Version is created, Live moves, Workspace truth refreshes, and Settings shows global state. |
| Precise instruction | `1 -> 2 -> 2.1 -> 3 -> 4 -> 5` | Consequences stay visible without repeating a decision already made by the user. |
| Create Version only | `1 -> 2 -> 2.3 -> 2.3.1 -> 5` | A new Version persists while Live and Devices remain unchanged. |
| Defer Cloud work | `1 -> 2 -> 2.2 -> 1` | Local development continues without remote mutation. |
| Partial result | `1 -> 2 -> 3 -> 4.1 -> 5 -> 6` | The created Version remains visible while Live stays unchanged. |
| Outcome unknown | `1 -> 2 -> 3 -> 4.2` | Duplicate mutation stops until remote truth is known. |
| Workspace comparison changed | `5 -> 5.2 -> 2` | Local and remote facts refresh before another mutation. |
| Unlinked global Agent | `6 -> 6.1 -> 6` | Settings retains remote resources independently of local Workspace links. |
| Global refresh recovery | `6 -> 6.2 -> 6` | Trusted global facts remain historical until refresh succeeds. |

## Main Path

### 1 Existing Local Agent Work

User entry: the user asks the coding Agent to modify an Agent linked to a Workspace.

User action: edits or asks the coding Agent to edit identity, behavior, models, voice, firmware, or
other local product files, then reviews local results.

Visible UI state: ordinary conversation, files, tools, Terminal, Device, and Preview report local
work. No Cloud resource-awareness card appears merely because local files changed.

Client state change: Workspace files and local artifacts may change. NoraCloud remains unchanged.

Exit / next state: `2 Agent Update Decision` when a remote mutation is about to occur.

### 2 Agent Update Decision

User entry: the next action would publish the current Workspace Agent definition.

User action: chooses `Create Version`, `Create and make Live`, or `Not now`. If the original user
instruction already explicitly includes the displayed outcome, the card records that scope without
asking the same question again.

Visible UI state: one operation card shows the linked Agent, current Live Version, changed source
files, readable behavior/model changes, and Device impact. It explains that `Create Version` leaves
Live unchanged and `Create and make Live` moves Live after creating the Version.

Client state change: one exact operation scope becomes pending or approved. No mutation occurs
while a required decision is pending.

Exit / next state: `3 Agent Update Running`, `2.1 Scope Covered By Instruction`, `2.2 Cloud Work
Deferred`, or `2.3 Create Version Without Changing Live`.

### 3 Agent Update Running

User entry: Version creation and Live movement are approved.

User action: watches progress or expands technical details.

Visible UI state: the same operation card advances through collecting the Workspace configuration,
creating the Version, moving Live, and verifying current remote state. IDs appear only after they
are returned. Completed stages remain visible after a later failure.

Client state change: factual stage results accumulate on the same operation record.

Exit / next state: `4 Agent Update Receipt`, `3.1 Cloud Access Blocked`, `3.2 Live Changed During
Update`, `4.1 Partial Result`, or `4.2 Outcome Unknown`.

### 4 Agent Update Receipt

User entry: the update reaches a stable result.

User action: reviews the result, continues the task, or opens Workspace Cloud.

Visible UI state: the same card states the created Version, previous and current Live Versions,
Devices following Live, changed Agent sources, verification time, and work not performed. It offers
`Open Workspace Cloud` and configuration details.

Client state change: the receipt remains in this Conversation. The Workspace binding and remote
resources persist independently.

Exit / next state: `5 Current Workspace Cloud` or end.

### 5 Current Workspace Cloud

User entry: the user selects `Cloud` beside `All Files`, `Changes`, `Device`, and `Preview`, or opens
it from an operation receipt.

User action: reviews the current Workspace's local Agent definition, comparison with Live, and
linked NoraCloud resources.

Visible UI state:

- Header: `Cloud · Current Workspace`, Workspace name, freshness, and refresh.
- `Agent Configuration`: readable Identity and Behavior, Live Version, LLM, listening, speaking,
  and per-source status for `IDENTITY.md`, `SOUL.md`, `USER.md`, and `cloud-agent.json`.
- `Linked Cloud Resources`: bound Agent, Live Version, Devices on the Agent, stored Cloud
  Conversation when present, and NoraCloud access readiness.
- `Compare with Live` opens a local-versus-Live comparison; selecting a source opens it in the main
  area.
- `View all in Settings` opens the global system-information level.
- No `Used By This Coding Session` or Coding Session Usage card appears.

Switching between Conversations in the same Workspace leaves this panel unchanged. Switching to a
tab in another Workspace changes the panel to that Workspace. A Conversation with no Workspace
context does not show Workspace panels.

Client state change: local sources, Workspace binding, and remote facts refresh independently.

Exit / next state: `6 Settings Cloud System Information` or end.

### 6 Settings Cloud System Information

User entry: the user opens Settings and selects `Cloud`, or clicks `View all in Settings` from the
right panel.

User action: reviews global Cloud inventory, credential attention, and NoraCloud Usage
availability.

Visible UI state:

- Settings keeps its left category navigation; `Cloud` is selected inside Settings.
- Header: current NoraCloud identity/environment, signed-in state, freshness, and refresh.
- `Cloud Resources`: deduplicated Agents, Versions, Devices, and Cloud Conversations; readable Agent
  rows; current Live state; related resource counts; optional local Workspace link count.
- `NoraCloud Usage`: complete account-level LLM, STT, and TTS totals when available. Otherwise it
  says `Unavailable` and explains that coding Run Records are not substituted.
- Direct Cloud mutation is absent. Settings may link back to a relevant Workspace or approved
  credential surface.

Client state change: global inventory and Usage availability refresh from NoraCloud.

Exit / next state: end.

## Branch Journeys

### 2.1 Scope Covered By Instruction

Trigger: the user explicitly requested the same Agent, Version creation, Live movement, Device
impact, and verification scope displayed by the card.

Visible UI state: the same card says `Scope covered by instruction`; consequences remain visible
and redundant decision controls are absent.

Allowed user actions: stop if still safe, inspect details, or let execution continue.

Recovery / next state: `3 Agent Update Running`.

Blocks progress: no.

### 2.2 Cloud Work Deferred

Trigger: the user chooses `Not now`.

Visible UI state: the same card says `Deferred`, `No remote changes`, and `Local Workspace changes
remain available`.

Allowed user actions: continue local work or request publication later.

Recovery / next state: `1 Existing Local Agent Work` or end.

Blocks progress: only the deferred Cloud outcome.

### 2.3 Create Version Without Changing Live

Trigger: the user chooses `Create Version`.

Visible UI state: the card says a new immutable Version will be created, Live remains v12, and
Devices following Live are unaffected.

Allowed user actions: proceed, inspect details, or cancel before mutation.

Recovery / next state: `2.3.1 Version-Only Receipt`.

Blocks progress: no after approval.

### 2.3.1 Version-Only Receipt

Trigger: the Version-only operation completes.

Visible UI state: the same card says `Version v13 created`, `Live remains v12`, changed sources, and
`Device impact: none`.

Allowed user actions: open Workspace Cloud, compare with Live, or continue locally.

Recovery / next state: `5 Current Workspace Cloud` or end.

Blocks progress: no.

### 5.1 Workspace Not Linked

Trigger: the current Workspace has no valid NoraCloud binding.

Visible UI state: `This Workspace is not linked to NoraCloud`; no Agent, Version, Device, or Cloud
Conversation relationship is invented. `View all in Settings` remains available.

Allowed user actions: request first publish in Conversation, refresh, or open Settings.

Recovery / next state: `2 Agent Update Decision`, `6 Settings Cloud System Information`, or end.

Blocks progress: only Workspace-scoped Cloud inspection and mutation.

### 5.2 Workspace Or Live Changed

Trigger: a local source differs from Live, or remote Live differs from the last trusted comparison.

Visible UI state: source rows name `Changed locally`, `Not published`, or `Missing`; a remote change
names previous and current Live Versions. IDs remain in technical details.

Allowed user actions: compare with Live, open a source, refresh, or return to Conversation.

Recovery / next state: `2 Agent Update Decision` before mutation, or end.

Blocks progress: only mutation based on stale facts.

### 6.1 Remote Agent Without Local Workspace Link

Trigger: NoraCloud inventory contains an Agent not linked by any local Workspace.

Visible UI state: the Agent remains counted and says `No local Workspace link`, with remote last
activity when available.

Allowed user actions: inspect its remote status or close Settings.

Recovery / next state: `6 Settings Cloud System Information`.

Blocks progress: no.

### 6.2 Global Inventory Refresh Failure

Trigger: global Cloud refresh fails.

Visible UI state: a trusted inventory stays visible as `Stale` with its timestamp. Without a
trusted inventory, the page says `Unavailable`; it never replaces unknown state with zero counts.

Allowed user actions: retry, check sign-in, or close Settings.

Recovery / next state: `6 Settings Cloud System Information` after recovery, or end.

Blocks progress: only actions requiring fresh remote truth.

## Detail States

### 2.4 One Component, Different Operations

Trigger: the reviewer compares operation families.

Visible UI state: existing-Agent update, first publish, release, Device registration, and bounded
Cloud tests reuse one stateful operation component. Each operation displays only its real resource
and consumption consequences.

Allowed user actions: return to the operation.

Exit / next state: `2 Agent Update Decision`.

### 2.5 First Publish Rule

Trigger: the Workspace has no binding and the user requests first publish.

Visible UI state: the operation card says it will create the Agent and first Version and set that
Version Live. It does not offer Version-only because first publish cannot produce that outcome.

Allowed user actions: publish or defer.

Exit / next state: `2 Agent Update Decision`.

### 5.3 Readable Agent Configuration

Trigger: the Workspace is linked to an Agent.

Visible UI state: the primary card identifies what the Agent is, how it behaves, which Version is
Live, and its LLM, listening, and speaking capabilities.

Allowed user actions: open configuration details, compare with Live, refresh, or return to chat.

Exit / next state: `5 Current Workspace Cloud`.

### 5.4 Configuration Source Comparison

Trigger: the Agent configuration card is visible.

Visible UI state: `IDENTITY.md`, `SOUL.md`, `USER.md`, and `cloud-agent.json` show deterministic
comparison states. Identity and safe behavior excerpts remain readable; full Markdown opens in the
main editor. `USER.md` body is not exposed by default.

Allowed user actions: open a source, compare with Live, or close details.

Exit / next state: `5 Current Workspace Cloud`.

### 5.5 Workspace Binding And Linked Resources

Trigger: the Workspace has a valid NoraCloud binding.

Visible UI state: the bound Agent, its current Live Version, Devices on that Agent, the stored Cloud
Conversation when present, and Cloud access readiness appear as linked facts. The panel never says
the Workspace owns these remote resources.

Allowed user actions: refresh, return to Conversation, or open Settings Cloud.

Exit / next state: `5 Current Workspace Cloud`.

### 6.3 Global Resource Deduplication

Trigger: several local Workspaces link the same remote Agent or no local Workspace links it.

Visible UI state: Settings counts the remote resource once and shows local Workspace links only as
secondary context.

Allowed user actions: inspect the resource or a linked Workspace.

Exit / next state: `6 Settings Cloud System Information`.

### 6.4 NoraCloud Usage Availability

Trigger: Settings renders the NoraCloud Usage section.

Visible UI state: complete account-level LLM, STT, and TTS totals appear only when NoraCloud reports
complete coverage. Otherwise the section says `Unavailable` and explains the missing dependency.

Allowed user actions: read or refresh.

Exit / next state: `6 Settings Cloud System Information`.

## State Language

### 0.1 Operation Status

Visible language: `Waiting for decision`, `Scope covered`, `Running`, `Completed`, `Partial`,
`Outcome unknown`, `Deferred`, and `Blocked`.

Usage: the single operation card shows one status at a time.

### 0.2 Resource Freshness

Visible language: `Fresh`, `Refreshing`, `Stale`, or `Unavailable`, with a trusted timestamp when one
exists.

Usage: Workspace Cloud and Settings each state the scope of the refreshed data.

### 0.3 Scope Terminology

Visible language: `Conversation`, `Current Workspace`, and `Cloud Conversation`.

Usage: distinguish interaction history, shared local project context, and NoraCloud remote
conversation.

### 0.4 Version And Live Terminology

Visible language: `Version v13 created`, `Live remains v12`, or `Live v12 → v13`.

Usage: describe the real immutable Version and mutable Live pointer. Do not present `Candidate` as a
separate resource.

### 0.5 Usage Terminology

Visible language: `NoraCloud Usage` or `Usage unavailable`.

Usage: only complete NoraCloud metering. Coding Agent tokens and Run Records remain outside Cloud
surfaces.

## Errors And Recovery

### 3.1 Cloud Access Blocked

Trigger: the operation requires missing, expired, or unsafe NoraCloud access.

Visible UI state: the same operation card says `Blocked`, names the access requirement and safe
Settings destination, and never displays or requests a secret in plain conversation text.

Allowed user actions: configure safely, defer, or cancel.

Recovery / next state: `2 Agent Update Decision` or end.

### 3.2 Live Changed During Update

Trigger: current Live differs from the Version the user reviewed before the Live mutation.

Visible UI state: the same card shows reviewed Live, current Live, and target Version, then says the
previous decision no longer applies.

Allowed user actions: review the new state, plan again, or cancel.

Recovery / next state: `2 Agent Update Decision` or end.

### 4.1 Partial Result

Trigger: Version creation succeeds but changing Live fails.

Visible UI state: the same operation card says `Partial`, keeps the created Version, states that Live
remains v12, and confirms that Devices following Live did not switch.

Allowed user actions: inspect the Version, open Workspace Cloud, or continue later.

Recovery / next state: `5 Current Workspace Cloud` or end.

### 4.2 Outcome Unknown

Trigger: a request may have reached NoraCloud but its result cannot be proven.

Visible UI state: the same operation card shows the last confirmed fact, offers a safe read, and
pauses duplicate mutation.

Allowed user actions: check current state, inspect technical details, or stop.

Recovery / next state: end after the safe read establishes reality, or remain unresolved.

## Wireframe

- Main happy path: local Agent edit → Agent update decision → Version creation and Live movement →
  factual receipt → current Workspace Cloud → Settings Cloud.
- Route path: full rows for precise instruction, Version-only, defer, partial, unknown, Workspace
  comparison change, unlinked global Agent, and refresh recovery.
- Detail states: operation families, first-publish rule, readable Agent configuration,
  configuration sources, Workspace binding, global deduplication, and Usage availability.
- State language: operation, freshness, scope, Version/Live, and Usage labels.
- Errors and recovery: Cloud access, concurrent Live change, partial result, and unknown outcome.
