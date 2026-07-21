# Journey 1: Cloud Resource Control Across Conversation And Settings

This record is the source of truth for `cloud-resource-awareness.wireframe.html`.

The journey describes the proposed end state: a coding Agent uses NoraCloud Tools for Cloud work,
the Agent's permission policy determines whether a Tool Call runs or waits, and Settings directly
manages account resources without entering Conversation. Local editing, the Cloud Tool surface, and
Settings resource actions have different visible states and recovery paths.

Implementation maturity: **partial today; proposed end state not implemented yet**. Local editing,
the existing permission surface, Cloud API resources, and browse-only Settings are present in part.
The semantic NoraCloud Tool families, complete resource lifecycle, direct Settings mutations, and
their final renderers are proposed.

## Numbering And Route Tables

### Main Path States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main step | Journey 1 | Local Workspace changes are being made. | Asks the Agent to change behavior or the reasoning model. | Real Edit Tool cards show completed or running file work. No fabricated validation command appears. NoraCloud is unchanged. | Workspace files change; Cloud and firmware remain unchanged. | `2` |
| `2` | Main step | Journey 1 | The user requests a persistent Cloud action. | Asks the Agent to publish the current Workspace changes. | The Agent explains the requested Cloud effect and starts one semantic NoraCloud Tool Call according to the active permission policy. | The operation enters `3`, `3.1`, or `3.2`. | `3`, `3.1`, or `3.2` |
| `3` | Main step | Journey 1 | The publish Tool Call is executing. | Watches the operation or stops the turn if the runtime allows. | `Publish Desk Weather Agent` shows `Running`; the card names the Agent, Version creation, Live movement, and affected Devices. | `4` or `4.1`/`4.2`/`4.3` |
| `4` | Main step | Journey 1 | The Tool Call returns a stable receipt. | Reviews the result and any affected resources. | The same Tool Call becomes `Completed` or `Failed` and shows resource IDs, before/after state, effects, and request ID. There is no duplicate receipt card. | `5`, `4.1`, `4.2`, or `4.3` |
| `5` | Main step | Journey 1 | Workspace Cloud reflects canonical NoraCloud state. | Reviews the refreshed Cloud panel or opens Settings for account management. | The Workspace Cloud snapshot refreshes from NoraCloud and shows freshness. Settings remains account-scoped. | `5.1`, `5.2`, `5.3`, or end |

### Permission And Operation Branches

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Branch | `2 Cloud Intent Requested` | The Agent policy requires approval before the Tool Call runs. | Reviews the semantic operation and chooses `Approve`, `Always allow`, or `Deny`. | The same Tool Call is `Approval required`; it names the resource and visible impact. | The Tool Call remains pending; no Cloud mutation starts. | `3.1.1` or `3.1.2` |
| `3.1.1` | Sequential child | `3.1 Approval Required` | The user approves the pending Tool Call. | Clicks `Approve` or the offered remembered-approval option. | The card changes to `Running`; the approved operation is not duplicated. | The runtime resumes the pending Tool Call once. | `3` |
| `3.1.2` | Sequential child | `3.1 Approval Required` | The user denies the pending Tool Call. | Clicks `Deny`. | The Tool Call settles to `Blocked` or `Denied`; no Cloud mutation occurs. | The pending permission is resolved as denied and local changes remain. | `2` or end |
| `3.2` | Branch | `2 Cloud Intent Requested` | The Agent policy blocks Cloud execution. | Reads the blocked result and changes policy later if needed. | The Tool Call shows `Blocked`; no approval action or API progress is shown. | No Cloud mutation starts. | `2` or end |
| `4.1` | Recovery state | `4 NoraCloud Tool Receipt` | The resource changed before the Tool Call could apply its premise. | Refreshes current state and decides whether to retry. | `Conflict` names the changed premise and offers read-only refresh before retry. | The client discards stale assumptions and keeps the confirmed current resource. | `2`, `3`, or end |
| `4.2` | Recovery state | `4 NoraCloud Tool Receipt` | Some effects completed and another stage failed. | Reviews completed IDs and the failed stage. | `Partial` separates confirmed Version/Live/Device effects and does not offer a duplicate full publish. | Confirmed effects remain recorded; recovery starts from the failed stage. | `5` or `2` |
| `4.3` | Recovery state | `4 NoraCloud Tool Receipt` | The final outcome cannot be confirmed. | Opens the Cloud snapshot or retries read-only discovery. | `Outcome unknown` pauses mutation retry until NoraCloud state is discovered. | The client keeps the request ID and prevents an unsafe duplicate operation. | `5`, `2`, or end |

### Settings Resource Management States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `5.1` | Detail state | `5 Workspace Cloud Refreshed` | Agent and Version management in Settings. | Opens an Agent, a Version, or an Agent action menu. | Agent Detail shows Live, Versions, Devices, and actions; Version Detail shows immutable configuration and `Make Live`/rollback entry points. | Settings selects the account Agent context. | `5.1.1`, `5.1.2`, `5.1.3`, or `5` |
| `5.1.1` | Sequential child | `5.1 Agent And Version Management` | Rename Agent. | Edits the name and saves. | Inline saving becomes `Updated` or returns an actionable error. | Canonical Agent detail refreshes after the API result. | `5.1` or `5` |
| `5.1.2` | Sequential child | `5.1 Agent And Version Management` | Make a Version Live or roll back. | Selects `Make Live`, reviews the effect, and confirms. | Confirmation names current Live, target Version, and Devices following Live; success refreshes the Agent. | Live pointer changes only after server confirmation and current-state checks. | `5.1`, `4.1`, or end |
| `5.1.3` | Sequential child | `5.1 Agent And Version Management` | Delete Agent confirmation. | Selects `Delete Agent`, reviews dependencies, and confirms. | Confirmation names the Agent and warns that the action is persistent. | Delete request is sent only after confirmation. | `5.1.4`, `5.1`, or end |
| `5.1.3.1` | Sequential child | `5.1.3 Delete Agent Confirmation` | Agent deletion completes. | Reviews the refreshed Agent list. | `Deleted` confirms the Agent is gone; dependent resources are not silently removed. | The deleted Agent is removed from the account list and the Settings context refreshes. | `5`, `5.2`, or end |
| `5.1.4` | Sequential child | `5.1.3 Delete Agent Confirmation` | Agent deletion is blocked by dependent Devices. | Opens the dependency details and chooses a safe next action. | `In use` lists the blocking Devices and does not offer an unsafe force-delete path. | Agent remains unchanged. | `5.1`, `5.2`, or end |
| `5.2` | Detail state | `5 Workspace Cloud Refreshed` | Device management in Settings. | Opens a Device from its owning Agent. | Device Detail shows ownership, connection, track, resolved Version, last seen, Session, and management actions. | Settings selects the Device context without showing credentials. | `5.2.1`, `5.2.2`, `5.2.3`, or `5.2.4` |
| `5.2.1` | Sequential child | `5.2 Device Management` | Follow Live or pin a Version. | Selects a track and confirms when the change affects resolution. | The detail shows current track, target Version, and `Updating`/`Updated`. | Device track is refreshed from canonical state. | `5.2`, `4.1`, or end |
| `5.2.2` | Sequential child | `5.2 Device Management` | Rebind Device confirmation. | Chooses another Agent, reviews Session impact, and confirms. | Confirmation names current Agent, target Agent, and that the current Session will end. | Rebind request is sent only after confirmation. | `5.2.2.1`, `4.1`, or end |
| `5.2.2.1` | Sequential child | `5.2.2 Rebind Device Confirmation` | Device rebind completes. | Reviews the updated ownership and reconnect state. | `Updated` shows the target Agent and `Reconnect required`; no credential is shown. | Device ownership is refreshed and the ended Session is no longer current. | `5.2` or `5.3` |
| `5.2.3` | Sequential child | `5.2 Device Management` | Rotate Device credential. | Reviews protected delivery details and confirms. | `Updating` then `Updated`; only credential type, destination status, and reconnect guidance appear. | New credential is delivered through a protected path; secret text is not rendered. | `5.2`, `4.1`, or end |
| `5.2.4` | Sequential child | `5.2 Device Management` | Revoke Device. | Reviews Session and device consequences, then confirms. | Confirmation names the Device and active Session impact; success shows `Revoked`. | Device access is revoked and current Session is closed according to service rules. | `5.2`, `5.3`, or end |
| `5.3` | Detail state | `5 Workspace Cloud Refreshed` | Cloud Session management in Settings. | Opens a Session from the account list or Device detail. | Session Detail shows status, Agent, Device, Version at start, timestamps, turns, and available recorded Usage. | Settings selects the Session context. | `5.3.1` or end |
| `5.3.1` | Sequential child | `5.3 Session Management` | End Session confirmation. | Selects `End Session`, reviews the effect, and confirms. | Confirmation names the Session and shows that it will stop accepting further turns. | End request is sent once and remains idempotent. | `5.3.2`, `4.1`, or end |
| `5.3.2` | Sequential child | `5.3.1 End Session Confirmation` | Session ends. | Reviews the ended Session record. | `Ended` shows end time and retained facts; the action is no longer available. | Session status and related Device state refresh from NoraCloud. | `5.3` or end |

### State Language

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Journey 1 | Tool execution language. | Reads the Tool card. | `Running`, `Approval required`, `Blocked`, `Completed`, and `Failed` distinguish execution from permission. | None. | Active state |
| `0.2` | State language | Journey 1 | Settings operation language. | Reads a Settings action or confirmation. | `Confirm`, `Updating`, `Updated`, `Conflict`, `In use`, `Revoked`, and `Ended` distinguish direct resource management. | None. | Active state |
| `0.3` | State language | Journey 1 | Recovery language. | Reads an error or recovery action. | `Partial` and `Outcome unknown` preserve confirmed facts and prevent unsafe duplicate mutation. | None. | Active state |
| `0.4` | State language | Journey 1 | Secret language. | Reads a credential-related detail. | Credential type, protected destination, reconnect guidance, and status may appear; secret values never appear. | None. | Active state |

### Errors And Recovery

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.1` | Recovery summary | `4 NoraCloud Tool Receipt` | Conflict after the operation premise became stale. | Refreshes and retries only after reviewing current state. | `Conflict` keeps the last known resource visible and disables blind duplicate execution. | Stale assumptions are discarded. | `2`, `3`, or end |
| `4.2` | Recovery summary | `4 NoraCloud Tool Receipt` | Partial operation. | Reviews completed IDs and failed stages. | Confirmed effects remain visible; retry starts at the unresolved stage. | The client keeps the receipt and avoids redoing completed work. | `5` or `2` |
| `4.3` | Recovery summary | `4 NoraCloud Tool Receipt` | Unknown operation outcome. | Performs read-only discovery or stops. | `Outcome unknown` remains visible until the resource state is confirmed. | Duplicate mutation stays blocked. | `5`, `2`, or end |
| `5.1.4` | Recovery summary | `5.1.3 Delete Agent Confirmation` | Agent deletion is blocked because Devices still depend on it. | Opens Device management and resolves dependencies. | `In use` names the blocking Devices and provides safe navigation. | No deletion occurs. | `5.1` or `5.2` |

### Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Auto-run publish | `1 -> 2 -> 3 -> 4 -> 5` | The publish Tool Call runs without a prompt and ends with a canonical receipt. |
| Ask before running, approve | `1 -> 2 -> 3.1 -> 3.1.1 -> 3 -> 4 -> 5` | The same Tool Call resumes once after approval. |
| Ask before running, deny | `1 -> 2 -> 3.1 -> 3.1.2 -> 2` | No Cloud mutation occurs; local Workspace changes remain available. |
| Never run policy | `1 -> 2 -> 3.2 -> 2` | Cloud execution is blocked until the user changes policy. |
| Publish conflict | `1 -> 2 -> 3 -> 4 -> 4.1 -> 2` | Current state is refreshed before another operation is attempted. |
| Partial publish | `1 -> 2 -> 3 -> 4 -> 4.2 -> 5` | Confirmed effects remain visible and retry starts at the unresolved stage. |
| Unknown publish outcome | `1 -> 2 -> 3 -> 4 -> 4.3 -> 5` | Read-only discovery confirms the state before any retry. |
| Agent rename | `5 -> 5.1 -> 5.1.1 -> 5.1` | Settings directly updates the Agent name and refreshes canonical detail. |
| Make Live or roll back | `5 -> 5.1 -> 5.1.2 -> 5.1` | Settings directly changes the Live pointer or shows a conflict. |
| Delete Agent succeeds | `5 -> 5.1 -> 5.1.3 -> 5.1.3.1 -> 5` | The Agent disappears from the refreshed Settings list. |
| Delete Agent is in use | `5 -> 5.1 -> 5.1.3 -> 5.1.4 -> 5.2` | Settings names dependent Devices and leaves the Agent unchanged. |
| Device track management | `5 -> 5.2 -> 5.2.1 -> 5.2` | Settings directly changes Follow Live or pinned Version state. |
| Device rebind | `5 -> 5.2 -> 5.2.2 -> 5.2.2.1 -> 5.2` | Settings changes ownership and shows reconnect guidance. |
| Device credential rotation | `5 -> 5.2 -> 5.2.3 -> 5.2` | Settings confirms protected delivery without rendering a secret. |
| Device revoke | `5 -> 5.2 -> 5.2.4 -> 5.2` | Settings revokes access and refreshes Device/Session state. |
| Session end | `5 -> 5.3 -> 5.3.1 -> 5.3.2 -> 5.3` | The Session becomes ended and retained facts remain inspectable. |

## Main Path

### 1 Local Workspace Changes

User entry: the user asks the Agent to change local Agent behavior or the reasoning model.

User action: watches real file Edit Tool cards and may inspect completed work.

Visible UI state: `Edit SOUL.md` and `Edit cloud-agent.json` use the existing tool-card hierarchy.
No fake validation command or Cloud operation appears. The Assistant states that NoraCloud is not
changed during the local step.

Client state change: Workspace files change; Cloud and firmware remain unchanged.

Exit / next state: `2 Cloud Intent Requested` after the user asks for a persistent Cloud update, or
the local journey ends.

### 2 Cloud Intent Requested

User entry: local changes are ready and the user asks to publish them to the bound Agent.

User action: confirms the intended Cloud action in normal conversation.

Visible UI state: the Assistant names the Agent, the immutable Version that will be created, the
Live transition, affected Devices, and work that is not included. It then starts one semantic
NoraCloud Tool Call according to the active permission policy.

Client state change: the Tool Call receives an operation request; no separate proposal resource is
created.

Exit / next state: `3`, `3.1`, or `3.2`.

### 3 NoraCloud Publish Tool Running

User entry: the active policy allows the Tool Call to run immediately, or the user approved it.

User action: watches the semantic Tool Call.

Visible UI state: `Publish Desk Weather Agent` shows `Running`, resource scope, and progress. The
card is not labeled Terminal and does not show a shell command.

Client state change: the operation is in flight with its request identifier.

Exit / next state: `4 NoraCloud Tool Receipt`, `4.1 Conflict`, `4.2 Partial`, or `4.3 Outcome Unknown`.

### 4 NoraCloud Tool Receipt

User entry: the Tool Call reaches a stable result.

User action: reviews the result, affected resources, and next action.

Visible UI state: the same Tool Call changes to `Completed` or `Failed` and shows created Version,
Live before/after, affected Devices, request ID, and excluded work. There is no separate generic
receipt card.

Client state change: confirmed effects are persisted in the conversation event and become the basis
for the Workspace Cloud refresh.

Exit / next state: `5 Workspace Cloud Refreshed`, or a recovery branch.

### 5 Workspace Cloud Refreshed

User entry: the Cloud operation is complete or the user opens Cloud after recovery.

User action: reviews the bound Agent and opens Settings when account-level management is needed.

Visible UI state: the Workspace Cloud panel shows canonical Agent/Live information, freshness, and
related resources. Settings is clearly account-scoped and does not inherit the coding Conversation.

Client state change: the last trusted Cloud snapshot is replaced by the refreshed canonical state.

Exit / next state: `5.1`, `5.2`, `5.3`, or end.

## Branch Journeys

### 3.1 Approval Required

Trigger: the active Agent policy asks before protected Tool execution.

Visible UI state: the semantic Tool Call is `Approval required`, identifies the target and effect,
and offers `Approve`, `Always allow` when the runtime offers it, and `Deny`.

Allowed user actions: approve once, choose the offered remembered approval, or deny.

Recovery / next state: approval goes to `3.1.1`; denial goes to `3.1.2`.

Blocks progress: yes, until the user chooses.

### 3.1.1 Approved Tool Continues

Trigger: the user approves the pending Tool Call.

Visible UI state: the same card changes to `Running`; no second operation card appears.

Allowed user actions: watch the operation or stop the run if the runtime exposes Stop.

Recovery / next state: `3`.

Blocks progress: no, after the permission response resolves.

### 3.1.2 Tool Denied

Trigger: the user denies the pending Tool Call.

Visible UI state: the card settles to `Denied` or `Blocked`; no NoraCloud receipt claims a mutation.

Allowed user actions: continue local work or change permission policy for a later request.

Recovery / next state: `2` or end.

Blocks progress: only the requested Cloud operation.

### 3.2 Tool Blocked By Policy

Trigger: the active policy never allows the requested operation.

Visible UI state: `Blocked` appears without an approval button or API progress.

Allowed user actions: continue locally or change policy before requesting the Cloud action again.

Recovery / next state: `2` or end.

Blocks progress: only Cloud execution.

### 4.1 Conflict After Read

Trigger: Live, Agent, Device, or another premise changed before execution completed.

Visible UI state: `Conflict` names the stale premise and offers read-only refresh. The UI does not
silently repeat a publish or resource mutation.

Allowed user actions: refresh, inspect the current state, and ask the Agent to retry with the new
premise.

Recovery / next state: `2`, `3`, or end.

Blocks progress: the stale operation only.

### 4.2 Partial Result

Trigger: one or more Cloud stages completed and a later stage failed.

Visible UI state: confirmed Version/Live/Device effects retain IDs; failed and unstarted stages are
separate. Retry starts at the unresolved stage.

Allowed user actions: open the refreshed Cloud state, inspect details, or retry the unresolved stage.

Recovery / next state: `5` or `2`.

Blocks progress: only the unresolved operation.

### 4.3 Outcome Unknown

Trigger: the client cannot confirm whether the remote operation completed.

Visible UI state: `Outcome unknown` includes the request ID and a read-only discovery action.

Allowed user actions: refresh Cloud state or stop; a duplicate mutation is not offered first.

Recovery / next state: `5`, `2`, or end.

Blocks progress: related mutation retry until state is confirmed.

## Detail States

### 5.1 Agent And Version Management

Trigger: the user opens `Settings → Cloud → Agents` and selects an Agent.

Visible UI state: Agent Detail shows identity, Live, Versions, Devices, and action controls. Version
Detail shows immutable status, configuration, and `Make Live`/rollback entry points without editable
remote Markdown bodies.

Allowed user actions: rename the Agent, make a Version Live, roll back, delete the Agent, open a
Device, or return to the Agent list.

Exit / next state: `5.1.1`, `5.1.2`, `5.1.3`, `5.2`, or `5`.

### 5.1.1 Rename Agent

Trigger: the user edits the Agent name.

Visible UI state: the row shows `Updating` and then `Updated`, or an actionable error; no unrelated
Conversation state appears.

Allowed user actions: save, retry after a failure, or return to Agent Detail.

Exit / next state: `5.1` or `5`.

### 5.1.2 Make Version Live / Roll Back

Trigger: the user selects a non-Live or previous Version.

Visible UI state: a confirmation names current Live, target Version, and Devices that follow Live.
After confirmation the page shows `Updating`, then the canonical Live state.

Allowed user actions: confirm, cancel, refresh after conflict, or return.

Exit / next state: `5.1`, `4.1`, or end.

### 5.1.3 Delete Agent Confirmation

Trigger: the user chooses Delete Agent.

Visible UI state: confirmation names the Agent, persistent consequence, and dependent-resource check.

Allowed user actions: confirm, cancel, or open dependent Devices.

Exit / next state: `5.1.4`, `5.1`, or end.

### 5.1.3.1 Agent Deleted

Trigger: the user confirms Delete Agent and NoraCloud reports success.

Visible UI state: the Settings list shows `Deleted` feedback and removes the Agent from the current
account inventory. Dependent resources are not silently removed.

Allowed user actions: return to the Cloud overview, open another Agent, or stop.

Exit / next state: `5` or end.

### 5.1.4 Agent In Use

Trigger: NoraCloud rejects deletion because Devices remain bound.

Visible UI state: `In use` lists blocking Devices and provides navigation to Device management; no
force-delete control appears.

Allowed user actions: rebind or revoke dependent Devices, then retry later.

Exit / next state: `5.1`, `5.2`, or end.

### 5.2 Device Management

Trigger: the user opens a Device from its owning Agent.

Visible UI state: Device Detail shows ownership, connection, Follow Live/Pinned track, resolved
Version, last seen, current Session, and management actions. Credential values never appear.

Allowed user actions: rename, change track, rebind, rotate, revoke, open Session, or return.

Exit / next state: `5.2.1`, `5.2.2`, `5.2.3`, `5.2.4`, `5.3`, or end.

### 5.2.1 Follow Live / Pin Version

Trigger: the user changes the Device track.

Visible UI state: confirmation or inline save names the target track and Version; the row shows
`Updating` then `Updated` or `Conflict`.

Allowed user actions: confirm, cancel, refresh, or retry with current state.

Exit / next state: `5.2`, `4.1`, or end.

### 5.2.2 Rebind Device Confirmation

Trigger: the user chooses another Agent for the Device.

Visible UI state: confirmation names current Agent, target Agent, and that the current Session will
end. No credential is shown.

Allowed user actions: confirm, cancel, or inspect the current Session.

Exit / next state: `5.2.2.1`, `4.1`, or end.

### 5.2.2.1 Rebind Completed

Trigger: the rebind succeeds.

Visible UI state: Device Detail shows new ownership, `Updated`, and `Reconnect required`.

Allowed user actions: wait for reconnect, open the ended Session, or return to Agent Detail.

Exit / next state: `5.2`, `5.3`, or end.

### 5.2.3 Rotate Credential

Trigger: the user chooses Rotate credential.

Visible UI state: confirmation and result show credential type, protected destination, reconnect
guidance, and status only. Secret text is never displayed.

Allowed user actions: confirm, cancel, refresh, or follow the protected delivery guidance.

Exit / next state: `5.2`, `4.1`, or end.

### 5.2.4 Revoke Device

Trigger: the user chooses Revoke Device.

Visible UI state: confirmation names the Device and current Session effect; success shows `Revoked`.

Allowed user actions: confirm, cancel, inspect the Session, or return to Agent Detail.

Exit / next state: `5.2`, `5.3`, or end.

### 5.3 Session Management

Trigger: the user opens a Cloud Session from the account list or Device Detail.

Visible UI state: Session Detail shows status, Agent, optional Device, Version at start, timestamps,
turn count, and available recorded Usage. No action exposes credentials.

Allowed user actions: inspect logs/export, open related resources, or select End Session.

Exit / next state: `5.3.1` or end.

### 5.3.1 End Session Confirmation

Trigger: the user selects End Session.

Visible UI state: confirmation names the Session and states that further turns will stop.

Allowed user actions: confirm or cancel.

Exit / next state: `5.3.2`, `4.1`, or end.

### 5.3.2 Session Ended

Trigger: the end request succeeds.

Visible UI state: Session status is `Ended` with end time and retained facts; End is no longer an
available action.

Allowed user actions: inspect retained details, return to Sessions, or open the related Device.

Exit / next state: `5.3` or end.

## State Language

### 0.1 Tool Execution Language

`Running`, `Approval required`, `Blocked`, `Completed`, `Denied`, and `Failed` are reserved for the
semantic NoraCloud Tool Call and its permission state.

### 0.2 Settings Operation Language

`Confirm`, `Updating`, `Updated`, `Conflict`, `In use`, `Revoked`, and `Ended` are used in Settings
confirmation, progress, and result states.

### 0.3 Recovery Language

`Partial` and `Outcome unknown` preserve confirmed facts and prevent unsafe duplicate mutation.

### 0.4 Secret Language

Settings may show credential type, protected destination, reconnect guidance, and status. Secret
values never appear in a product frame, Tool output, Conversation, or ordinary log.

## Errors And Recovery

The journey uses the recovery states `4.1`, `4.2`, `4.3`, and `5.1.4` above. Every recovery state
keeps the last confirmed resource facts, names the next safe action, and avoids claiming that an
unconfirmed mutation completed. Settings refreshes canonical resource state after every successful
or conflicting direct API operation.

## Wireframe

The matching artifact is `cloud-resource-awareness.wireframe.html`.
