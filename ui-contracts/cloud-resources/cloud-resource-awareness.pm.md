# Cloud Resource Awareness PM Review

## PM Context

### Current Work

NoraHarness needs to make NoraCloud mutations and persistent resources understandable without
pretending that a Conversation owns the remote objects. The interaction has three scopes:

1. the Conversation where a Cloud operation is reviewed and recorded;
2. the current Workspace, which holds the local Agent definition and NoraCloud binding;
3. Settings, which shows the signed-in identity's global NoraCloud inventory.

### Current User-Visible Behavior

Local authoring, tool calls, Terminal, Device Log, Device, and Preview already report ordinary
development work. A coding Agent can run NoraCloud CLI commands, but Electron does not currently
render a NoraCloud-specific decision card, Workspace Cloud view, or global Cloud inventory.

### Implementation Maturity

`partial`

- Existing product shape: Conversations, Workspace grouping, ordinary tool cards, right-workbench
  tabs, Settings navigation, Agent source files, and direct NoraCloud CLI execution.
- Existing NoraCloud behavior: a bound Workspace can create an immutable Version, optionally move
  the Agent's Live pointer, release an existing Version, and keep its Agent and Cloud Conversation
  binding in the project directory.
- Proposed UI: one stateful Cloud operation card, one Workspace-scoped right Cloud tab, and one
  Settings Cloud category.
- Dependencies: structured operation events, safe remote reads, Workspace binding inspection,
  configuration comparison, global inventory reads, credential-status reads, and complete
  NoraCloud metering before any account-wide Usage total is shown.

### Decision To Unblock

Confirm that Conversation, Workspace, and Settings are separate information scopes, and that the
product uses the real Agent → Version → Live model rather than inventing session ownership or a
separate Candidate resource.

### Discussion Boundaries

- Decide: visible Cloud consequences, Version/Live choices, Workspace comparison, Settings
  inventory, and recovery language.
- Do not debate: local CPU/build-cache confirmation, a full NoraCloud console, pricing, secret
  display, or direct create/delete controls in Settings.

## Confirmed Product Rules

<table>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> Local development adds no resource-awareness card. Existing conversation, tool, Terminal, Device Log, Device, and Preview surfaces continue to report local work.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> One Cloud mutation uses one stateful operation card. Decision, running, completed, partial, blocked, deferred, and outcome-unknown are states of the same card.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> A Conversation owns its operation history and receipt. It does not own the Workspace binding or NoraCloud resources.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> The right-workbench Cloud tab is scoped to the current Workspace. Switching between Conversations in the same Workspace does not change this panel.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> The right Cloud tab compares local Agent configuration with the remote Live Version. It shows readable identity and behavior plus source status for IDENTITY.md, SOUL.md, USER.md, and cloud-agent.json.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> Settings contains the global, deduplicated Cloud inventory. It remains inside Settings as system information.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> Coding token totals and Run Records do not appear as Cloud Usage. Account-wide Usage appears only when NoraCloud can provide complete LLM, STT, and TTS metering.</td>
  </tr>
</table>

## Proposed Design

### Conversation Operation Card

When an existing Agent configuration is ready to publish, the card shows:

- the linked Agent and current Live Version;
- changed configuration sources and readable consequences;
- `Create Version`, which leaves Live unchanged;
- `Create and make Live`, which creates a Version and moves Live;
- Device impact for Devices following Live;
- factual running stages and a receipt from real remote results.

`Candidate` is not presented as a resource or command. A non-Live Version may be described in a
sentence, but visible actions use Version and Live terminology. First publish is a special case: it
creates the Agent and first Version and sets that Version Live.

### Current Workspace Cloud

The right `Cloud` tab contains two primary cards:

1. `Agent Configuration`: readable Identity and Behavior, LLM/listening/speaking settings, current
   Live Version, and per-source comparison with Live;
2. `Linked Cloud Resources`: the bound Agent, Live Version, Devices on that Agent, the Workspace's
   stored Cloud Conversation when present, and Cloud access readiness.

The panel does not show Coding Session Usage. An operation receipt may open this Workspace view,
but the receipt remains in the Conversation that performed the operation.

### Settings Cloud

Settings shows current NoraCloud identity and environment, deduplicated counts and readable Agent
rows for Agents, Versions, Devices, and Cloud Conversations, plus credential attention. Local
Workspace links are secondary context and never determine the global inventory.

A small `NoraCloud Usage` section is proposed for complete account-level metering. Until that data
exists, the section shows `Unavailable` with an explanation; it must not substitute coding Agent
tokens or partial device coverage.

## Scope Rules

| Surface | Scope | Answers |
| --- | --- | --- |
| Conversation operation card | One remote mutation performed from one Conversation | What will change, which Version/Live action was selected, what is running, and what actually happened? |
| Right `Cloud` tab | Current Workspace | Which Agent is this project linked to, what is Live, how does local configuration differ, and which related resources are reachable through the binding? |
| Settings `Cloud` | Current NoraCloud identity and environment | Which Cloud resources exist overall, which need attention, and is complete NoraCloud Usage available? |

The relationship is `Workspace 1 → many Conversations`. The Workspace binding and local files are
shared by those Conversations. Closing a Conversation never deletes the Workspace or remote
resources.

## Non-Goals

- No new card for PlatformIO, build cache, CPU, disk, or ordinary Preview.
- No `Used By This Coding Session` resource list.
- No Coding Session Usage in the right Cloud tab.
- No separate Candidate resource type.
- No full Markdown body, secret, digest, or raw resource ID in default summary cards.
- No direct create, delete, release, rollback, reset, or secret reveal in Settings.
- No account-wide Usage claim from local Run Records or incomplete NoraCloud meters.

## Risks And Dependencies

- Electron currently does not receive a NoraCloud-specific structured lifecycle from direct CLI
  calls; the card cannot be truthful until that contract exists.
- Multiple Conversations can mutate the same Workspace files and binding. Cloud mutation state and
  refresh must therefore be coordinated at Workspace scope rather than active-Conversation scope.
- Remote Live can move outside the current Workspace. A stale decision must stop before changing
  Live and require a fresh comparison.
- Configuration summaries must be deterministic and traceable. Identity and behavior can use safe
  excerpts, while complete Markdown opens in the main editor.
- `Conversation`, runtime resume state, and NoraCloud `Cloud Conversation` are distinct concepts and
  require distinct product labels.
- NoraCloud Usage remains unavailable until the service covers relevant Agent, Device, LLM, STT,
  and TTS activity consistently.

No PM question currently blocks the revised wireframe. Structured event contracts, complete Usage
metering, and final service APIs remain engineering dependencies.
