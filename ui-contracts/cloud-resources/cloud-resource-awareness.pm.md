# Cloud Resource Awareness PM Review

## PM Context

### Current Work

NoraHarness needs to expose persistent NoraCloud resources at two levels without treating them as
Workspace-owned data. The conversation and right workbench explain the resources used or managed by
the active coding session. Settings provides the deduplicated system-level inventory and accumulated
recorded Usage.

### Current User-Visible Behavior

Local authoring, tool calls, Terminal, Device Log, Device, and Preview already report ordinary
development work. Settings already uses a left category list for application configuration and
system information. Cloud mutations can happen through Agent-controlled tools, but there is no
product-level operation record, coding-session Cloud view, or Settings inventory.

### Implementation Maturity

`partial`

- Existing product shape: coding sessions, ordinary tool cards, the right workbench, Settings
  categories, and Run Record Usage data.
- Proposed UI: one stateful Cloud operation card, one coding-session Cloud tab, and one Settings
  Cloud Resources & Usage category.
- Dependencies: structured operation results, coding-session-to-resource references, remote
  inventory reads, resource deduplication, credential-status reads, and Usage aggregation.

### Discussion Focus

Confirm the minimum two-level information architecture for cloud-resource awareness.

### Discussion Boundaries

- PM should decide: where global and current-session state appear, what each scope means, and how
  the user moves between them.
- PM should not debate: local build resource confirmation, a full NoraCloud console, billing,
  pricing, secret display, or direct mutation controls in Settings.

### Confirmed Product Rules

<table>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> Local development adds no resource-awareness card. Existing conversation, tool, Terminal, Device Log, Device, and Preview surfaces continue to report local work.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> One cloud mutation uses one stateful Cloud Resource Operation card. Decision, running, completed, partial, blocked, and unknown are states of the same component.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> The right-workbench Cloud tab shows only resources used or managed by the active coding session and that coding session's recorded Usage.</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>Confirmed:</strong> Settings contains the global, deduplicated Cloud Resources & Usage system-information category. It is not an app-level navigation destination outside Settings.</td>
  </tr>
</table>

## Proposed Design

### Conversation And Right Workbench

The active coding session receives one stateful operation card when work reaches a persistent Cloud
boundary. Its receipt opens the right-workbench `Cloud` tab. That tab shows the session's Cloud
resource working set, current remote state, sharing/activity elsewhere, and Usage aggregated from
the coding session's Run Records.

### Settings System Information

Settings adds `Cloud` to its existing left category list. The page shows current NoraCloud identity
and environment, deduplicated remote resource counts and states, credential status, and explicitly
scoped accumulated Usage. `View all in Settings` from the right panel opens this category.

The global inventory is sourced from NoraCloud, not by unioning local coding-session references.
Otherwise resources created on another device or no longer referenced by a local session would be
invisible.

## Scope Rules

| Surface                     | Scope                                                                       | Answers                                                                                                                    |
| --------------------------- | --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Conversation operation card | One cloud operation in one coding session                                   | What is about to happen, what is running, and what actually happened?                                                      |
| Right `Cloud` tab           | Active coding session                                                       | Which Cloud resources is this coding session using or managing, and what Usage has this coding session recorded?           |
| Settings `Cloud`            | Current NoraCloud identity/environment plus this app's recorded Usage scope | How many remote resources exist overall, which need attention, and how much Usage is covered by the displayed aggregation? |

Cloud resources remain independent of coding-session and Workspace lifecycles. A resource can appear
in several coding-session views but is counted once in Settings. Deleting or archiving a coding
session never deletes the remote resource.

## Non-Goals

- No new card for PlatformIO, build cache, CPU, disk, or ordinary Preview.
- No claim that Cloud resources belong to a Workspace or coding session.
- No direct create, delete, release, rollback, reset, or secret reveal in Settings.
- No account-wide Usage claim when the available Run Records cover only this NoraHarness
  installation.
- No mixing Coding Usage with NoraCloud Runtime Usage or context-window percentage.

## Risks And Dependencies

- Remote inventory and local session references have different sources and must not be conflated.
- The same resource can change outside the active coding session; current state must refresh from
  NoraCloud and invalidate stale operation assumptions.
- `Session` is ambiguous. Product copy must say `Coding session` for NoraHarness work and
  `Cloud Runtime Session` for the NoraCloud resource.
- Global Usage must state whether it covers this installation, the signed-in user, or a server-side
  account aggregation.

## Open Questions

No PM question blocks the wireframe. Exact service contracts and final labels for Usage coverage
remain engineering follow-up work.
