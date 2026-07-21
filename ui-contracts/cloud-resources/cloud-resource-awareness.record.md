# NoraCloud Resource Control Journey Record

This record is the source of truth for `cloud-resource-awareness.wireframe.html`.

Journey A is one readable vibe-coding example: the user requests a weather application, the Agent
authors and builds firmware, completes and publishes the Agent definition, registers a physical
Device under that Agent, securely provisions it, and performs a separately authorized flash. The
Device's first connection creates its first Device Session. Separate Agent-operated examples cover
Read, Update, credential rotation, Device management, hardware-free test Sessions, Session end, and
Agent deletion. Settings remains a parallel direct-management surface and does not enter Conversation.

Implementation maturity: **partial today; proposed end state not implemented yet**. NoraCloud APIs
already expose the Agent, Version, Device, and Session operations described here. Today's CLI covers
many reads and common publish, release, Device, and Session actions but intentionally excludes some
high-impact operations. Semantic NoraCloud MCP Tool families and direct Settings mutations do not
yet provide the complete end state drawn here.

## Numbering And Route Tables

### Build, First Publish, And Device Connection States

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main step | Journey A | The user requests a weather product and the Agent implements its firmware. | Describes the desired weather experience. | One Conversation turn shows the user's goal, the Agent's concise intent, and real Read/Edit/Write/Build Tool Calls. | Firmware application files change and the selected target builds; NoraCloud is unchanged. | `2` |
| `2` | Main step | Journey A | The Agent collects the missing identity and personality, then authors the Agent definition. | Provides the Agent name and desired tone. | The Conversation shows the Agent question, the user's answer, and Read/Edit Tool Calls for `SOUL.md`, `IDENTITY.md`, and `cloud-agent.json`. | The local Agent definition becomes ready to publish. | `3`, `4`, or `3.2` |
| `3` | Main step | Journey A | The example permission profile requires approval for the first publish of a new Agent. | Reviews the normalized definition and exact persistent effects, then chooses `Approve once`, `Always allow in this workspace`, or `Deny`. | A compact dedicated `Publish Weather Buddy Agent` approval shows the destination, four Cloud changes, effective Agent values, readable instruction summaries with `View file`/`View diff`, exclusions, and approval actions. Hashes stay inside collapsed technical details. | No Cloud mutation starts. The system binds approval to the hidden definition digest. | `4`, `3.1`, or `3.3` |
| `4` | Main step | Journey A | The Tool Call returns a stable first-publish receipt. | Reviews the result and affected resources. | The same expanded Tool Call becomes `Completed` or `Failed` and shows Agent ID, Version ID/status/digest, Live Version, Workspace binding, exclusions, and request ID. The right panel remains on Changes; there is no duplicate receipt card or Cloud summary. | `5`, `4.1`, `4.2`, or `4.3` |
| `5` | Main step | Journey A | The completed publish receipt causes Workspace Cloud to reflect canonical state. | Reviews the Assistant summary or continues to connect the physical Device. | The Assistant names the firmware and Agent result while the right Cloud panel updates automatically to `Devices: None registered`. No follow-up status Tool Call appears in Conversation. | The client refreshes Workspace Cloud outside Conversation using the confirmed receipt as the trigger. | `6` or another Agent-operated/Settings route |
| `6` | Main step | Journey A | The Agent prepares the exact physical provisioning target before requesting a one-time credential. | Asks to connect the physical Device. | Real `hw_fw_status` and `hw_fw_list_ports` Tool Calls show the firmware project, environment, Harness availability, selected port, and port provenance. | No NoraCloud Device exists and no credential is issued. | `7` |
| `7` | Main step | Journey A | Device registration and initial Agent binding await permission. | Reviews Device name, Agent, track, firmware target, upload port, credential destination, and exclusions, then approves or denies. | `Register Desk Display` explains that `POST /agents/{agent_id}/devices` creates the identity already owned by Weather Buddy and writes the one-time credential to protected local firmware configuration. | No Device exists until the semantic `noracloud_device` call executes. | `8`, deny, or blocked |
| `8` | Main step | Journey A | Registration returns a canonical Device receipt. | Reviews the new Device and the required physical next step. | The same Tool Call confirms Device ID, owning Agent, Follow Live track, `Never connected`, protected credential destination, and request ID; secret text never appears. | Device identity is created and bound to Weather Buddy; the credential is written to `platformio_override.ini`. | `9` |
| `9` | Main step | Journey A | Provisioned firmware is rebuilt, flashed, and verified physically and in NoraCloud. | Gives a separate reply immediately authorizing the physical flash. | Real `hw_fw_build`, `hw_fw_flash`, and `hw_device_state` Tool Calls prove the build, physical write, and device-side state; a read-only `noracloud_status` call separately proves Cloud connection, resolved Version, and first active Session. | Firmware runs with the Device credential; connection creates the Device Session and refreshes Workspace Cloud outside Conversation. | Agent-operated or Settings resource routes, or end |

### Agent-Operated Resource States

| ID | Resource operation | Visible Tool Call behavior | Persistent result |
| --- | --- | --- | --- |
| `R` | Read the Workspace resource graph | `noracloud_status` returns Agent, Live Version, Version count, Devices, and Sessions as a completed read-only Tool Call. No mutation approval appears. | None; facts only. |
| `U1` | Update an existing Agent definition | `noracloud_publish` shows the new immutable Version, Live pointer transition, modified source, two following Devices, and preserved Session context. | Waits for the active permission policy. |
| `U2` | Agent update receipt | The same Tool Call confirms the new Version and Live pointer. | New immutable Version exists; Agent Live changes; following Devices resolve it on their next turn. |
| `D1` | Rebind a Device | `noracloud_device` shows current/target Agent, current Session end, reconnect requirement, and protected credential handling. | Waits for the active permission policy. |
| `D2` | Device rebind receipt | The same Tool Call confirms new ownership, ended Session, retained history, and reconnect state. | Device points to the target Agent; the previous Session is terminal. |
| `C1` | Replace a Device access credential | `noracloud_device` uses the technical `rotate` action, requires an explicit `graceful` or `revoke_now` mode, and explains identity, connection, Session, and delivery consequences. | Waits for the active permission policy. |
| `C2` | Access credential replacement receipt | Graceful mode confirms that the replacement is protected and exposes `pending Device ack` without showing either credential. | Device ID, binding, Session, and history stay intact; the old credential remains temporarily valid until acknowledgement. |
| `V1` | Revoke a Device | `noracloud_device` names the Device identity, connection, active Session, credential revocation, and in-flight work consequences. | Waits for the active permission policy. |
| `V2` | Device revoke receipt | The same Tool Call confirms deactivation, revoked credential, closed connection, and ended Session without rendering a secret. | Device can no longer authenticate; history remains inspectable. |
| `T1` | Create a hardware-free test Session | `noracloud_session` names the Agent, pinned Version, absence of a Device, persistent Session record, and active-test quota effect. | Waits for the active permission policy. |
| `T2` | Test Session and first Turn result | The Session receipt is followed by a separate `noracloud_turn` under its own policy; this example auto-runs that requested Turn and shows reply, transcript, and Usage. | An active Device-free Session contains one recorded Turn. |
| `S1` | End a Session | `noracloud_session` identifies the active Session and states that transcript, export, and recorded Usage remain available. | Waits for the active permission policy. |
| `S2` | Session end receipt | The same Tool Call becomes `Completed` with the terminal status and retained facts. | Further turns stop; Session history remains inspectable. |
| `X1` | Delete an Agent | `noracloud_agent` first discovers Device, Version, Session, and Workspace dependencies, then shows irreversible effects. Any bound Device blocks with `409 in_use`. | Waits for the active permission policy only after the dependency check passes. |
| `X2` | Agent deletion receipt | The same Tool Call names the deleted Agent and Versions and the automatically ended active test Session. | Agent and Versions are removed; no Device was silently deleted. |

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
| `5.1.3` | Sequential child | `5.1 Agent And Version Management` | Delete Agent confirmation. | Selects `Delete Agent`, reviews dependencies and irreversible effects, exports needed Session data, and confirms. | Confirmation names the Agent, zero bound Devices, Versions that will be deleted, and active test Sessions that will end. | Delete request is sent only after confirmation; a bound Device returns `In use`. | `5.1.4`, `5.1.3.1`, or end |
| `5.1.3.1` | Sequential child | `5.1.3 Delete Agent Confirmation` | Agent deletion completes. | Reviews the refreshed Agent list. | `Deleted` confirms the Agent and Versions are gone and names automatically ended test Sessions. | The deleted Agent is removed from the account list and the Settings context refreshes. | `5` or end |
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
| `0.1` | State language | All Agent-operated routes | Semantic NoraCloud Tool Call language. | Reads the Tool card. | `Approval required`, `Blocked`, `Completed`, and `Failed` distinguish permission from stable outcomes; transient execution is not a separate journey state. | None. | Active state |
| `0.2` | State language | Settings routes | Settings operation language. | Reads a Settings action or confirmation. | `Confirm`, `Updating`, `Updated`, `Conflict`, `In use`, `Revoked`, and `Ended` distinguish direct resource management. | None. | Active state |
| `0.3` | State language | All routes | Recovery language. | Reads an error or recovery action. | `Partial` and `Outcome unknown` preserve confirmed facts and prevent unsafe duplicate mutation. | None. | Active state |
| `0.4` | State language | All routes | Secret language. | Reads a credential-related detail. | Credential type, protected destination, reconnect guidance, and status may appear; secret values never appear. | None. | Active state |

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
| Register, provision, and connect Device | `5 -> 6 -> 7 -> 8 -> 9` | Registration creates the Device already bound to Weather Buddy, writes the credential locally, and a separately authorized flash leads to connection and the first Device Session. |
| Auto-run publish | `1 -> 2 -> 4 -> 5` | Auto-run skips approval and settles the same Tool Call directly to its receipt. |
| Ask before running, deny | `1 -> 2 -> 3 -> 3.1 -> 2` | No Cloud mutation occurs; firmware and Agent definition remain local. |
| Never run policy | `1 -> 2 -> 3.2 -> 2` | Cloud execution is blocked until the user changes policy. |
| Approval source changed | `1 -> 2 -> 3 -> 3.3 -> 3` | The old approval is invalidated and the regenerated definition must be reviewed again. |
| Publish conflict | `1 -> 2 -> 3 -> 4 -> 4.1 -> 2` | Current state is refreshed before another operation is attempted. |
| Partial publish | `1 -> 2 -> 3 -> 4 -> 4.2 -> 5` | Confirmed effects remain visible and retry starts at the unresolved stage. |
| Unknown publish outcome | `1 -> 2 -> 3 -> 4 -> 4.3 -> 5` | Read-only discovery confirms the state before any retry. |
| Agent reads Cloud resources | `R` | A read-only Tool Call returns the Agent/Version/Device/Session graph without approval. |
| Agent publishes an update | `U1 -> U2` | A new immutable Version is created and made Live; Session context is preserved. |
| Agent rebinds a Device | `D1 -> D2` | Device ownership changes, the prior Session ends, and reconnect guidance is returned. |
| Agent replaces a Device access credential | `C1 -> C2` | The technical rotate action uses explicit graceful mode, preserving identity and Session while the replacement waits for Device acknowledgement. |
| Agent revokes a Device | `V1 -> V2` | Device identity is deactivated, credential access is revoked, and active work stops. |
| Agent creates a test Session and runs a Turn | `T1 -> T2` | A Device-free Session is pinned to a Version; its separately governed first Turn returns reply, transcript, and Usage evidence. |
| Agent ends a Session | `S1 -> S2` | The Session becomes terminal while transcript, export, and recorded Usage remain available. |
| Agent deletes an unused Agent | `X1 -> X2` | Dependency discovery precedes approval; Agent and Versions are deleted only when no Device remains bound. |
| Agent rename | `5 -> 5.1 -> 5.1.1 -> 5.1` | Settings directly updates the Agent name and refreshes canonical detail. |
| Make Live or roll back | `5 -> 5.1 -> 5.1.2 -> 5.1` | Settings directly changes the Live pointer or shows a conflict. |
| Delete Agent succeeds | `5 -> 5.1 -> 5.1.3 -> 5.1.3.1 -> 5` | The Agent disappears from the refreshed Settings list. |
| Delete Agent is in use | `5 -> 5.1 -> 5.1.3 -> 5.1.4 -> 5.2` | Settings names dependent Devices and leaves the Agent unchanged. |
| Device track management | `5 -> 5.2 -> 5.2.1 -> 5.2` | Settings directly changes Follow Live or pinned Version state. |
| Device rebind | `5 -> 5.2 -> 5.2.2 -> 5.2.2.1 -> 5.2` | Settings changes ownership and shows reconnect guidance. |
| Device credential rotation | `5 -> 5.2 -> 5.2.3 -> 5.2` | Settings confirms protected delivery without rendering a secret. |
| Device revoke | `5 -> 5.2 -> 5.2.4 -> 5.2` | Settings revokes access and refreshes Device/Session state. |
| Session end | `5 -> 5.3 -> 5.3.1 -> 5.3.2 -> 5.3` | The Session becomes ended and retained facts remain inspectable. |

## Build, First Publish, And Connect The Device

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

Exit / next state: `6 Physical Device Prepared`, another Agent-operated route, Settings, or end.

### 6 Physical Device Prepared

User entry: after first publish, the user asks to connect the physical desk display to Weather Buddy.

User action: identifies the intended hardware when needed; no Cloud approval is requested yet.

Visible UI state: real `hw_fw_status` and `hw_fw_list_ports` calls resolve the `desk-weather/firmware`
project, `m5stack-cores3` environment, Harness availability, `/dev/cu.usbmodem2101`, and unique
USB-port evidence. The UI explicitly says this is a read-only preflight: no Device exists and no
credential has been issued.

Client state change: the exact local project and physical upload target become inputs to the later
registration approval. NoraCloud remains unchanged.

Exit / next state: `7 Register Device Approval Required`.

### 7 Register Device Approval Required

User entry: preflight has established a safe provisioning target.

User action: reviews and approves once, always allows this operation in the Workspace, or denies.

Visible UI state: `Register Desk Display` shows the target Weather Buddy Agent, Live Version,
Device name, Follow Live track, firmware environment, upload port, and protected destination
`platformio_override.ini`. Its exact effects are `Register Device`, `Issue credential once`, and
`Write firmware config`. The card explains that `POST /v1/agents/{agent_id}/devices` performs the
initial Agent binding; physical build, flash, connection, and Session creation are excluded.

Client state change: none while approval is pending. Approval binds the Device request and local
credential destination; it does not authorize the later physical flash.

Exit / next state: `8 Device Registration Receipt`, denied, or blocked.

### 8 Device Registration Receipt

User entry: the registration Tool Call reaches a stable result.

User action: reviews the Device identity, Agent binding, track, and required next step.

Visible UI state: the same Tool Call shows `Completed`, `dev_…228`, Weather Buddy ownership,
Follow Live, `Never connected`, the protected `platformio_override.ini` destination, and request
ID. The one-time credential value never appears in Conversation or technical details.

Client state change: NoraCloud creates the Device under Weather Buddy and returns a credential once;
the trusted adapter writes it locally. No claim of build, flash, connection, or Session is made.

Exit / next state: `9 Firmware Flashed And Device Connected`.

### 9 Firmware Flashed And Device Connected

User entry: Device registration and local provisioning are complete.

User action: gives a separate reply immediately authorizing `hw_fw_flash` for the resolved physical
port.

Visible UI state: `hw_fw_build` proves the provisioned firmware links, `hw_fw_flash` names the exact
port, port source, and immediately preceding authorization, and `hw_device_state` reports physical
Wi-Fi, battery, and heap facts. A separate read-only `noracloud_status` Tool Call reports Desk Display
as connected on `ver_…b12` with active `ses_…91c`. The right Cloud panel then shows one registered
Device, the registration-time Agent binding, Follow Live, connection, and Session.

Client state change: provisioned firmware runs on the physical Device. Its authenticated connection
creates the first Device Session automatically; the client refreshes Cloud state outside Conversation.

Exit / next state: another Agent-operated route, Settings, or end.

## Agent-Operated Cloud Resource Journeys

These examples define the proposed MCP end state. They do not claim that semantic NoraCloud MCP
Tool Calls exist today. NoraCloud APIs already provide the underlying resource operations; the CLI
covers many but not all of them. Settings and MCP are parallel adapters over the same operation
contract.

### R Read Cloud Resource Graph

Trigger: the user asks the Agent what Cloud resources are related to the current Workspace.

Visible UI state: one completed read-only `Inspect Workspace Cloud` Tool Call returns the bound
Agent, Live Version, Version count, Device summary, and active/ended Session summary. It names
`noracloud_status` only as technical detail.

Authorization: ordinary read policy; no mutation approval appears.

Persistent effect: none.

### U1 / U2 Update Existing Agent

Trigger: the user changes a local Agent source such as `SOUL.md` and asks the Agent to publish it.

Visible UI state: `Publish Weather Buddy Update` shows that the operation creates one new immutable
Version, moves Live from the current Version to the new Version, and affects two Devices following
Live. It explicitly says active Session context is preserved. Approval or Auto-run settles that same
Tool Call to a receipt containing the new Version, Live transition, Device behavior, and request ID.

Authorization: current Agent permission profile. A current-Live mismatch returns conflict instead
of overwriting a newer pointer.

Persistent effect: a new Version exists and Agent Live points to it; Version content is never
mutated in place.

### D1 / D2 Rebind Device

Trigger: the user asks the Agent to move a Device from one Agent to another.

Visible UI state: `Rebind Desk Display` shows Device ID, current Agent, target Agent, target track,
the current Session that will end, and reconnect requirements. Credential values never render. The
receipt confirms ownership, ended Session, retained history, and reconnect state.

Authorization: current Agent permission profile; this is a protected memory-ownership mutation.

Persistent effect: Device ownership changes and the old active Session becomes terminal.

### C1 / C2 Replace Device Access Credential

Trigger: the user asks for routine credential rotation without interrupting the Device.

Visible UI state: `Replace Desk Display Access Credential` requires the explicit `graceful` mode and names
the Device, Agent, active connection, Session preservation, one-time replacement, protected delivery,
and old-credential revocation after Device acknowledgement. It separately explains that
`revoke_now` immediately disconnects and requires physical re-provisioning; no mode is inferred.
The receipt reports the canonical `pending Device ack` state and never shows old or new secrets.

Authorization: current Agent permission profile; credential mode and destination are part of the
approved parameters.

Persistent effect: the Device identity, binding, Session, and history remain. In graceful mode the
old credential coexists until the Device persists and acknowledges the replacement, after which the
old credential is revoked.

### V1 / V2 Revoke Device

Trigger: the user asks the Agent to remove a Device from NoraCloud.

Visible UI state: `Revoke Office Display` names the Device, owning Agent, current connection and
Session, and the exact effects: deactivate identity, revoke credential, close access, and abort
in-flight work. The credential value is never rendered. The receipt confirms terminal access state
and retained Session history.

Authorization: current Agent permission profile; this is an irreversible access mutation.

Persistent effect: the Device can no longer authenticate. Reusing the hardware requires a new
registration and credential.

### T1 / T2 Create Test Session And Run First Turn

Trigger: the user asks to test an unpublished Weather Buddy Version once without hardware.

Visible UI state: `Create Weather Buddy Test Session` names the Agent, pinned Version, `Device: None`,
zero initial Turns, persistent Session record, and active-test quota effect. Approval creates only
the Session. The following `noracloud_turn` remains a separate Tool Call under its own permission
policy; this example auto-runs the requested first Turn and shows its prompt, reply, transcript, and
recorded Usage alongside the active Session receipt.

Authorization: current Agent permission profile for `noracloud_session`; the later
`noracloud_turn` is evaluated independently and would pause separately under Ask-before-running.

Persistent effect: an active hardware-free Session pinned to `ver_…c30` contains one recorded Turn.
No Device identity, credential, firmware flash, or physical connection is created.

### S1 / S2 End Session

Trigger: the user asks the Agent to end an active Cloud Session.

Visible UI state: `End Cloud Session` names the Session, status, turn count, Version, and retained
transcript/export/Usage facts. The receipt shows `Ended` and no longer offers another end mutation.

Authorization: current Agent permission profile.

Persistent effect: further turns stop; Agent, Version, Device, and retained Session facts remain.

### X1 / X2 Delete Agent

Trigger: the user asks the Agent to delete an unused Agent.

Visible UI state: a read-only dependency check runs first. `Delete Voice Notes Agent` then names the
Agent ID, zero bound Devices, two Versions that will be deleted, one active test Session that will
end automatically, and the irreversible consequence. The user is told to export needed Session
data before approval. Any bound Device yields `409 in_use`; no force-delete route appears.

Authorization: current Agent permission profile. Ask-before-running pauses on the exact deletion;
Auto-run may execute directly only when policy allows it.

Persistent effect: Agent and Versions are deleted; the active test Session ends automatically; no
Device is silently deleted.

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

### Device Provisioning Partial

If NoraCloud creates the Device but protected local credential delivery fails, the Tool Call keeps
the confirmed Device ID and reports `Partial`. It must not register another Device blindly. Recovery
first discovers the Device, then uses a protected re-provisioning path or revokes/deletes the unused
identity according to the confirmed credential state.

If build, flash, or connection verification fails after successful provisioning, the Device remains
`Never connected`. Recovery retries only the physical stage; it does not create another Device or
issue another credential.

### Test Turn Conflict

NoraCloud returns `409 turn_in_progress` rather than queueing a second Turn. The active test Session
and current Turn remain visible, and retry is offered only after read-only Session discovery.

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

Visible UI state: confirmation names the Agent, zero bound Devices, Versions that will be deleted,
active test Sessions that will end automatically, and the need to export data before deletion.

Allowed user actions: export needed Session data, confirm, cancel, or open dependent Devices when
the dependency check reports them.

Exit / next state: `5.1.3.1`, `5.1.4`, `5.1`, or end.

### 5.1.3.1 Agent Deleted

Trigger: the user confirms Delete Agent and NoraCloud reports success.

Visible UI state: the Settings list shows `Deleted` feedback, removes the Agent from the current
account inventory, and reports deleted Versions and automatically ended test Sessions.

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

`Approval required`, `Blocked`, `Completed`, `Denied`, and `Failed` are reserved for semantic
NoraCloud Tool Calls. A transient execution phase may exist in runtime events but is not a separate
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
