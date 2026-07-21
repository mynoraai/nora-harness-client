# Cloud Resource Control PM Review

## PM Decision To Unblock

Define the proposed end state in which coding Agents use structured NoraCloud tools for the full
Cloud workflow, while Settings directly manages account resources through NoraCloud.

This decision supersedes the earlier proposal that kept the Agent Cloud surface CLI-only and made
Settings browse-only.

## Intended Audience

Product owners, design PMs, engineering owners, and reviewers deciding the final NoraHarness Cloud
resource-control model before implementation planning begins.

## Required Review Links

- [Pull request](https://github.com/mynoraai/nora-harness-client/pull/13)
- [PM document](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.pm.md)
- [Current journey record](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.record.md)
- [Current PR #13 wireframe preview (single HTML)](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)

## Current State

Today, coding Agents reach NoraCloud by running the `noracloud` CLI. The CLI covers publishing,
Live changes, test turns, observation, and part of the Agent, Version, Device, and Session
lifecycle. It does not yet expose the full resource lifecycle supported by NoraCloud.

NoraHarness already has a shared Agent permission model that can automatically allow, ask before,
or block protected work. However, NoraCloud does not yet provide the proposed structured MCP tools.
The current Tool Call presentation therefore cannot show semantic Cloud actions such as `Rebind
Device` or `Publish Agent` as dedicated resource operations.

Settings does not yet provide the proposed account-level Cloud resource management. The existing
PR draft shows browse-only Agents, Versions, Devices, Sessions, and Recorded Usage.

## Implementation Maturity

**Partial overall; proposed interaction not implemented yet.**

- NoraCloud already owns the account resources and much of the required resource API behavior.
- The `noracloud` CLI and Agent permission profiles exist, but CLI resource coverage is incomplete.
- The proposed shared operation core, NoraCloud MCP tools, semantic Cloud Tool Call renderer, and
  Settings resource mutations do not yet exist as one end-to-end product flow.

## Discussion Boundaries

PM should decide and review:

- how Agent-initiated and Settings-initiated Cloud operations differ;
- which MCP Tool families form the final Agent surface;
- how the Agent permission profile changes the visible Tool Call path;
- which post-creation resource actions Settings exposes;
- which current PR frames must be replaced to describe the proposed end state honestly.

Engineering constraints already fixed for this review:

- Version objects remain immutable;
- secrets never enter the Conversation, Agent context, ordinary logs, or visible Tool output;
- NoraCloud remains the final authority for account access, resource ownership, dependencies,
  concurrency, idempotency, and audit;
- CLI and MCP must not develop separate resource semantics.

## Non-Goals

- Removing the `noracloud` CLI from the product.
- Making Settings operations pass through a Conversation or coding Agent.
- Forcing every mutation through a visible plan-and-apply sequence.
- Adding a second NoraCloud-specific approval system beside Agent tool permissions.
- Allowing Settings to edit an immutable Version or author a Version without Workspace content.
- Showing raw device credentials, machine credentials, or full unavailable remote Markdown bodies.
- Treating Recorded Usage as billing, balance, or remaining quota.

## Decisions

<table>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Agent and LLM Cloud work uses NoraCloud MCP Tools as the proposed primary path.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> MCP and CLI are adapters over one shared NoraCloud operation core.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Agent permission policy—not a NoraCloud-specific proposal card—decides whether a Tool Call runs, waits, or is blocked.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Settings performs selected resource-management operations directly through NoraCloud, without entering Conversation.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> CLI remains a supported interface for people, scripts, CI, headless use, and troubleshooting, but the final Agent Skill teaches Tool Calls instead of shell commands.</td>
  </tr>
  <tr>
    <td bgcolor="#f8d7da"><strong>Release-impacting decision:</strong> The older principles of “zero Cloud MCP tools” and “no destructive Agent resource operations” must be explicitly replaced rather than left contradictory.</td>
  </tr>
</table>

## Proposed Product Model

The proposed end state has two user-visible control paths and one shared resource contract.

| Entry | User intent | Execution surface | Authorization experience | Result surface |
| --- | --- | --- | --- | --- |
| Conversation | Ask an Agent to build, publish, test, inspect, or manage NoraCloud | NoraCloud MCP Tool Call | Current Agent permission policy | The same Tool Call reaches Completed, Failed, or Blocked |
| Settings | Directly manage a known account resource | Settings resource action | User click plus confirmation when impact requires it | Updated resource detail plus success or error feedback |
| CLI | Human, script, CI, headless, or troubleshooting work | `noracloud` command | CLI caller identity and NoraCloud authorization | Human-readable or structured command result |

Settings does not display Tool Calls. Conversation does not imitate Settings dialogs. Both paths
use the same NoraCloud operation definitions and reach the same server-side safety rules.

## Shared Operation Contract

One shared operation registry defines each Cloud action:

- operation identifier;
- input and result shapes;
- read or write effect;
- MCP Tool family;
- CLI command mapping;
- idempotency behavior;
- secret-handling policy;
- stable error and request identifiers.

The adapters only translate their input and output. They do not independently decide resource
semantics. NoraCloud rechecks authorization and current resource state when the operation executes.

## Proposed MCP Tool Families

The Agent surface uses a small set of resource and workflow families instead of one universal tool
or dozens of per-action tools.

| Tool | Responsibility | Example operations |
| --- | --- | --- |
| `noracloud_status` | Cloud and Workspace readiness | auth state, binding, current Live summary |
| `noracloud_publish` | Version creation and Live movement | publish Workspace, set Live, rollback |
| `noracloud_agent` | Agent resource lifecycle | list, get, create, rename, metadata, delete |
| `noracloud_version` | Immutable Version discovery | list, get |
| `noracloud_device` | Device resource lifecycle | register, get, rename, follow Live, pin, rebind, rotate, revoke |
| `noracloud_session` | Cloud Session lifecycle | list, get, create test Session, end |
| `noracloud_turn` | Cloud Agent test interaction | run one test turn |
| `noracloud_observe` | Operational evidence | logs, transcript, schedules |

Product titles are semantic and action-specific—such as `Publish Desk Weather Agent`, `Rebind
Kitchen Display`, or `End Cloud Session`—rather than the internal Tool family name.

## Conversation Interaction

The Agent directly starts a structured NoraCloud Tool Call after the user requests Cloud work. The
permission profile determines the route:

| Policy behavior | Visible Tool Call route |
| --- | --- |
| Auto-run | The Tool Call executes without a permission pause and settles to `Completed` or `Failed` |
| Ask before running | `Approval required` → `Approve once`, `Always allow in this workspace`, or `Deny` → execution or denial |
| Never run | `Blocked`; no NoraCloud mutation starts |

There is no mandatory visible `plan` Tool Call followed by an `apply` Tool Call. An Agent may read
current state before a complex operation, but that is normal Agent behavior rather than a forced
two-stage protocol.

`noracloud_publish` does have one internal read-only preparation phase. It resolves the local Agent
config and selected instruction files into the effective Version definition, computes the file,
bundle, and definition digests, and reads the destination Cloud/Workspace binding. Ask-before-running
renders that normalized preview inside the same Tool Call before mutation. Approval binds to the
definition digest; a source change invalidates the approval and requires a regenerated preview.

The approval decision is semantic, not cryptographic. The user reviews the destination, readable
Cloud effects, effective Agent values, and instruction content or diffs. Hashes are system evidence:
they remain in collapsed technical details and bind execution to the reviewed snapshot, but the UI
must never ask a person to approve by comparing digest strings.

The review example is specifically a first publish from an unbound Workspace. Its approval preview
must show the destination Cloud/account, unbound Workspace, new Agent name, initial immutable
Version, initial Live behavior, Workspace binding, effective LLM/Voice/Cron values, readable
instruction summaries with `View file`/`View diff`, and excluded work. It must not show an old Live Version or affected
Devices, because those resources do not exist in this first-publish story. Candidate creation and
promotion of an existing Agent remain separate routes.

The completed Tool output is the factual receipt. For first publish it retains the created Agent ID,
initial Version ID/status/digest, Live Version, Workspace binding, exclusions, stable status, and
`request_id`. The Assistant may summarize the result, but the product does not duplicate it in a
separate NoraCloud receipt card or in the right Cloud panel. That panel refreshes only in the next
outcome state.

The completed receipt itself triggers that Workspace Cloud refresh. The client invalidates the last
panel snapshot and performs a silent read outside Conversation; the Agent does not issue a second
`noracloud_status` Tool Call. Opening or manually refreshing Cloud, completing a Settings action, or
reconnecting may use the same direct UI refresh path and also stays out of Conversation.

Workspace Cloud also keeps the published definition understandable after approval. The panel names
`cloud-agent.json`, `SOUL.md`, `IDENTITY.md`, and `USER.md`, and compares their local digests with the
Live Version manifest captured by the publish receipt. It does not claim that NoraCloud returns or
renders the remote Markdown contents.

The existing wireframe's fabricated validation Terminal, standalone Cloud proposal, typed
`Approve` shortcut, and separate resource receipt are not part of the proposed end state.

## Settings Resource Management

Settings remains account-scoped and retains browsing, detail, refresh, freshness, and Recorded
Usage. It adds post-creation management at the resource detail level.

### Agent And Version

- Rename an Agent.
- Make an immutable Version Live.
- Roll back by making an older Version Live.
- Delete an Agent when no Device dependency blocks deletion.
- Inspect Versions, but never edit or delete an immutable Version.

### Device

- Rename a Device.
- Follow Live or pin a Version.
- Rebind to another Agent.
- Rotate the Device credential through a protected delivery path.
- Revoke the Device.

### Cloud Session

- End an active Session idempotently.
- Keep ended Session facts available for inspection according to retention policy.

Rename may save directly. Live changes, rollback, pin, rebind, rotate, revoke, end, and delete use a
Settings confirmation dialog that names the target and visible impact. Confirmation is the user's
authorization for that Settings action; it is not an Agent permission response.

## Result And Recovery Rules

- **Success:** refresh from canonical NoraCloud state before presenting the stable result.
- **Conflict:** show that the resource changed after the displayed premise; refresh before retry.
- **In use:** show the relationships that block deletion and the relevant next management action.
- **Unauthorized:** keep readable details visible while disabling or rejecting the operation.
- **Partial:** retain confirmed identifiers and completed effects; do not repeat completed work.
- **Outcome unknown:** perform read-only discovery before offering retry.
- **Secret-bearing result:** route secret material through a protected destination; never render it
  in Agent context, Conversation, ordinary logs, or general Settings history.

## Agent Skill Migration

Migration is staged:

1. **MCP preferred:** the Agent Skill uses a NoraCloud MCP Tool whenever the matching capability is
   present; CLI remains a temporary fallback for missing Tool coverage.
2. **MCP parity:** all current CLI Agent capabilities and the required resource lifecycle exist in
   the eight Tool families; CLI fallback and shell examples leave the Agent Skill.
3. **Tool-only Agent guidance:** the Agent Skill teaches intent-to-Tool selection. CLI documentation
   remains available separately for human and non-LLM automation.

## Wireframe Revision Scope

The revised journey should show:

1. one readable vibe-coding example from a weather application request through firmware
   Read/Edit/Write/Build activity and user-supplied Agent identity/personality;
2. an Ask-before-running NoraCloud publish Tool Call as the review example, with Auto-run skipping
   the approval state and no separate progress frame;
3. approval, completed, denied, blocked, conflict, partial, and unknown states on the same semantic
   Tool Call surface;
4. an expanded first-publish approval preview derived from `cloud-agent.json` and the selected
   instruction files, with digest-bound approval and source-change invalidation;
5. a Completed Tool receipt that stays inside Conversation while the right panel remains on Changes;
6. direct Settings Agent/Version, Device, and Session management routes;
7. distinct Conversation permission language and Settings confirmation language;
8. an Engineering Readiness band that separates today's CLI-first partial implementation from the
   proposed MCP-first end state.

The journey record remains the source of truth for route IDs and visible states before the HTML is
redrawn.

## Dependencies And Risks

- A shared operation core and registry must be defined without weakening NoraCloud server checks.
- MCP Tool effect classification must come from the shared operation definition, not untrusted
  free-form Tool text.
- The permission renderer needs a trusted normalized preview and must bind approval to the exact
  source digest so changed local files cannot reuse stale approval.
- CLI behavior and error compatibility need an explicit migration contract when implementations
  move behind the shared core.
- Settings credential rotation needs a protected destination and recovery path before shipping.
- Agent Skill migration must not remove CLI fallback until MCP coverage is proven complete.
- A broad Auto-run profile can execute Cloud mutations without pausing; the UI must make the active
  policy understandable, while NoraCloud continues enforcing authorization and consistency.

## Benefits

- LLMs receive structured Cloud capabilities and results without composing shell commands.
- Human and automated CLI users retain a stable, scriptable interface.
- Conversation uses the same permission behavior as other Agent tools.
- Settings becomes a real account management surface without routing direct human actions through
  a coding Agent.
- Shared operation definitions reduce drift among MCP, CLI, Settings, and NoraCloud behavior.

## Downsides And Tradeoffs

- The product adds an MCP surface that the earlier CLI-only design intentionally avoided.
- The shared core and adapter migration add engineering work before the Tool-first Skill can become
  authoritative.
- Semantic Tool rendering and direct Settings management are separate client experiences that both
  require state and error coverage.
- Auto-run optimizes expert workflows but increases the importance of visible permission settings,
  server-side constraints, idempotency, and audit.

## Open Questions

No PM question blocks the journey-record rewrite. Detailed credential-delivery mechanics,
operation schema versioning, and implementation sequencing remain engineering design work.
