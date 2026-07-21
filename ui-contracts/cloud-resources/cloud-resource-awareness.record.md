# Journey 1: Build And Publish A Weather Agent

This record is the source of truth for `cloud-resource-awareness.wireframe.html`.

The main path is one readable vibe-coding conversation: the user requests a weather application,
the Agent authors and builds firmware, asks for the missing Agent identity and personality, authors
the Agent definition, and publishes it through one permission-aware NoraCloud Tool Call. Settings
remains a separate direct-management surface and does not enter Conversation.

Implementation maturity: **partial today; proposed end state not implemented yet**. Local editing,
the existing permission surface, Cloud API resources, and browse-only Settings are present in part.
The semantic NoraCloud Tool families, complete resource lifecycle, direct Settings mutations, and
their final renderers are proposed.

## Numbering And Route Tables

### Main Path States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main step | Journey 1 | The user requests a weather product and the Agent implements its firmware. | Describes the desired weather experience. | One Conversation turn shows the user's goal, the Agent's concise intent, and real Read/Edit/Write/Build Tool Calls. | Firmware application files change and the selected target builds; NoraCloud is unchanged. | `2` |
| `2` | Main step | Journey 1 | The Agent collects the missing identity and personality, then authors the Agent definition. | Provides the Agent name and desired tone. | The Conversation shows the Agent question, the user's answer, and Read/Edit Tool Calls for `SOUL.md`, `IDENTITY.md`, and `cloud-agent.json`. | The local Agent definition becomes ready to publish. | `3`, `4`, or `3.2` |
| `3` | Main step | Journey 1 | The example permission profile requires approval for the first publish of a new Agent. | Reviews the normalized definition and exact persistent effects, then chooses `Approve once`, `Always allow in this workspace`, or `Deny`. | A compact dedicated `Publish Weather Buddy Agent` approval shows the destination, four Cloud changes, effective Agent values, readable instruction summaries with `View file`/`View diff`, exclusions, and approval actions. Hashes stay inside collapsed technical details. | No Cloud mutation starts. The system binds approval to the hidden definition digest. | `4`, `3.1`, or `3.3` |
| `4` | Main step | Journey 1 | The Tool Call returns a stable first-publish receipt. | Reviews the result and affected resources. | The same expanded Tool Call becomes `Completed` or `Failed` and shows Agent ID, Version ID/status/digest, Live Version, Workspace binding, exclusions, and request ID. The right panel remains on Changes; there is no duplicate receipt card or Cloud summary. | `5`, `4.1`, `4.2`, or `4.3` |
| `5` | Main step | Journey 1 | The completed publish receipt causes Workspace Cloud to reflect canonical state. | Reviews the Assistant summary or opens Settings for account management. | The Assistant names the firmware and Agent result while the right Cloud panel updates automatically. No follow-up status Tool Call appears in Conversation. | The client refreshes Workspace Cloud outside Conversation using the confirmed receipt as the trigger. | `5.1`, `5.2`, `5.3`, or end |

### Permission And Operation Branches

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Branch | `3 Approval Required` | The user denies the pending Tool Call. | Clicks `Deny`. | The same card settles to `Denied`; no receipt claims a Cloud mutation. | Local firmware and Agent files remain available. | `2` or end |
| `3.2` | Branch | `2 Agent Definition Authored` | The active policy blocks Cloud execution before an approval prompt. | Reads the blocked state and changes policy later if needed. | The Tool Call shows `Blocked`; no API progress is shown. | No Cloud mutation starts. | `2` or end |
| `3.3` | Recovery state | `3 Approval Required` | A source file changes after the approval snapshot was prepared. | Reviews the regenerated effective definition and decides again. | `Source changed` names the changed file, cancels the prior approval, and returns to a new semantic preview. Digest comparison remains in technical details. | The old approval token cannot execute a different definition. | `3` or end |
| `4.1` | Recovery state | `4 NoraCloud Tool Receipt` | The resource changed before the Tool Call could apply its premise. | Refreshes current state and decides whether to retry. | `Conflict` names the changed premise and offers read-only refresh before retry. | The client discards stale assumptions and keeps the confirmed current resource. | `2`, `3`, or end |
| `4.2` | Recovery state | `4 NoraCloud Tool Receipt` | Some effects completed and another stage failed. | Reviews completed IDs and the failed stage. | `Partial` separates confirmed first-publish effects from the unresolved stage and does not offer a duplicate full publish. | Confirmed effects remain recorded; recovery starts from the failed stage. | `5` or `2` |
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
| `0.1` | State language | Journey 1 | Publish Tool Call language. | Reads the Tool card. | `Approval required`, `Blocked`, `Completed`, and `Failed` distinguish permission from stable outcomes; transient execution is not a separate journey state. | None. | Active state |
| `0.2` | State language | Journey 1 | Settings operation language. | Reads a Settings action or confirmation. | `Confirm`, `Updating`, `Updated`, `Conflict`, `In use`, `Revoked`, and `Ended` distinguish direct resource management. | None. | Active state |
| `0.3` | State language | Journey 1 | Recovery language. | Reads an error or recovery action. | `Partial` and `Outcome unknown` preserve confirmed facts and prevent unsafe duplicate mutation. | None. | Active state |
| `0.4` | State language | Journey 1 | Secret language. | Reads a credential-related detail. | Credential type, protected destination, reconnect guidance, and status may appear; secret values never appear. | None. | Active state |

### Errors And Recovery

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.3` | Recovery summary | `3 Approval Required` | The approval snapshot no longer matches its source files. | Reviews the regenerated definition or stops. | `Source changed` names the changed source and cancels the old approval without requiring the user to compare hashes. | No mutation starts under the stale approval. | `3` or end |
| `4.1` | Recovery summary | `4 NoraCloud Tool Receipt` | Conflict after the operation premise became stale. | Refreshes and retries only after reviewing current state. | `Conflict` keeps the last known resource visible and disables blind duplicate execution. | Stale assumptions are discarded. | `2`, `3`, or end |
| `4.2` | Recovery summary | `4 NoraCloud Tool Receipt` | Partial operation. | Reviews completed IDs and failed stages. | Confirmed effects remain visible; retry starts at the unresolved stage. | The client keeps the receipt and avoids redoing completed work. | `5` or `2` |
| `4.3` | Recovery summary | `4 NoraCloud Tool Receipt` | Unknown operation outcome. | Performs read-only discovery or stops. | `Outcome unknown` remains visible until the resource state is confirmed. | Duplicate mutation stays blocked. | `5`, `2`, or end |
| `5.1.4` | Recovery summary | `5.1.3 Delete Agent Confirmation` | Agent deletion is blocked because Devices still depend on it. | Opens Device management and resolves dependencies. | `In use` names the blocking Devices and provides safe navigation. | No deletion occurs. | `5.1` or `5.2` |

### Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Ask before running, approve | `1 -> 2 -> 3 -> 4 -> 5` | The example moves from firmware coding through Agent authoring and approval directly to the publish receipt and outcome. |
| Auto-run publish | `1 -> 2 -> 4 -> 5` | Auto-run skips approval and settles the same Tool Call directly to its receipt. |
| Ask before running, deny | `1 -> 2 -> 3 -> 3.1 -> 2` | No Cloud mutation occurs; firmware and Agent definition remain local. |
| Never run policy | `1 -> 2 -> 3.2 -> 2` | Cloud execution is blocked until the user changes policy. |
| Approval source changed | `1 -> 2 -> 3 -> 3.3 -> 3` | The old approval is invalidated and the regenerated definition must be reviewed again. |
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

### 1 Firmware Application Authored

User entry: the user asks for a weather application that shows current temperature and condition.

User action: watches the Agent inspect the existing project, edit the application and weather UI,
and build the selected firmware target.

Visible UI state: a normal Assistant message states the intended work. Real `Read`, `Edit`, `Write`,
and `Build Firmware` Tool Calls show their actual completion states. The build is labeled as build
evidence, not physical-device proof. NoraCloud is unchanged.

Client state change: firmware application files change and the selected target build completes.

Exit / next state: `2 Agent Definition Authored`.

### 2 Agent Definition Authored

User entry: firmware is ready and the Agent still needs a user-facing name and personality.

User action: answers the Agent's question, for example `Weather Buddy` and `humorous and friendly`.

Visible UI state: the question and answer remain in normal Conversation messages. The Agent then
uses Read/Edit Tool Calls for `SOUL.md`, `IDENTITY.md`, and `cloud-agent.json`, followed by a concise
summary of the persistent Cloud effect that is ready to run.

Client state change: the local Agent definition becomes complete; no Cloud resource exists yet.

Exit / next state: `3 Approval Required`, `4 NoraCloud Tool Receipt` under Auto-run, or `3.2 Blocked`.

### 3 Publish Approval Required

User entry: the example permission profile asks before protected Cloud execution.

User action: reviews the resource effect and selects `Approve once`, `Always allow in this
workspace`, or `Deny`.

Visible UI state: one semantic Tool Call is titled `Publish Weather Buddy Agent` and shows
`noracloud_publish` as secondary technical detail. Its expanded approval preview shows:

- destination Cloud, signed-in account, and unbound Workspace;
- `Create Agent`, `Create initial immutable Version`, `Set initial Version Live`, and
  `Bind Workspace` as the exact persistent effects;
- the normalized `name`, `description`, LLM, STT, TTS, and Cron values loaded from the Agent config,
  plus `View config`;
- `SOUL.md`, `IDENTITY.md`, and `USER.md` with change status, a readable content summary, and
  `View file` or `View diff`;
- excluded Device registration, Cloud turn verification, and firmware flash.

The primary card tells the user they are approving the displayed content and consequences. Raw
config, complete instruction content, and diffs are optional disclosures. Tool name, file sizes,
file/bundle digests, and the effective definition digest stay inside collapsed `Technical details`;
the user does not need to understand or compare hashes to approve.

Client state change: no Cloud mutation starts while the Tool Call is awaiting permission. The
approval is cryptographically bound to the prepared definition digest; any source change
invalidates it.

Exit / next state: `4 NoraCloud Tool Receipt`, `3.1 Tool Denied`, or `3.3 Approval Snapshot Changed`.

### 4 NoraCloud Tool Receipt

User entry: the Tool Call reaches a stable result.

User action: reviews the result, affected resources, and next action.

Visible UI state: the same Tool Call changes to `Completed` or `Failed` and shows created Agent,
initial Version and digest, Live Version, Workspace binding, request ID, and excluded work. The
right panel stays on Changes in this state. There is no separate generic receipt or Cloud card.

Client state change: confirmed effects are persisted in the conversation event and become the basis
for the Workspace Cloud refresh.

Exit / next state: `5 Workspace Cloud Refreshed`, or a recovery branch.

### 5 Workspace Cloud Refreshed

User entry: the completed publish receipt is stored, or the user opens Cloud after recovery.

User action: reviews the bound Agent and opens Settings when account-level management is needed.

Visible UI state: one compact `Workspace Cloud` card shows the bound Agent, current Live Version,
Device count, freshness, and `Manage in Settings`. A neighboring `Live configuration` card shows
the effective LLM, STT, TTS, and Cron values, then names `cloud-agent.json`, `SOUL.md`, `IDENTITY.md`,
and `USER.md`, shows whether each local source matches the published Live snapshot, and offers
`Compare Workspace to Live`. The comparison is based on the publish receipt manifest and digests;
the panel does not imply that NoraCloud returns raw instruction contents. It does not split the same relationship into separate `Bound Agent` and
`Related Cloud Resources` cards. Settings remains account-scoped and does not inherit the coding
Conversation. Conversation contains only the Assistant's outcome summary; it does not add
`Refresh NoraCloud status` or another Tool Call.

Client state change: the completed receipt invalidates the last Workspace Cloud snapshot and the
client silently reloads canonical state outside Conversation. Opening Cloud, manually refreshing
Cloud, completing a Settings resource action, or reconnecting may also refresh this panel without
creating a Conversation Tool Call.

Exit / next state: `5.1`, `5.2`, `5.3`, or end.

## Branch Journeys

### 3.1 Tool Denied

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

### 3.3 Approval Snapshot Changed

Trigger: `cloud-agent.json` or an included instruction file changes after the approval preview is
computed and before mutation starts.

Visible UI state: the Tool Call shows `Source changed`, identifies the changed source, cancels the
prior approval, and regenerates the semantic definition preview. Old/new digests remain available
only under technical details; the user is not asked to compare them.

Allowed user actions: review the new preview and approve again, or stop.

Recovery / next state: `3` or end.

Blocks progress: yes; an approval cannot execute a different snapshot.

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

Visible UI state: confirmed Agent/Version/Live/Workspace effects retain IDs; failed and unstarted stages are
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

`Approval required`, `Blocked`, `Completed`, `Denied`, and `Failed` are reserved for the semantic
publish Tool Call. A transient execution phase may exist in runtime events but is not a separate
user journey frame.

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
