## Summary

- Tell one readable vibe-coding story from a weather-app request through firmware tools, Agent identity authoring, and an inspectable NoraCloud publish Tool Call.
- Make Settings the direct API control plane for Agent, Device, and Session actions, with confirmations, conflicts, dependency blocking, and protected credential handling.
- Scope: proposed product interaction; no Electron or NoraCloud runtime implementation is included.

## Links

- [Journey record](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.record.md)
- [PM review](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.pm.md)
- [Current PR #13 wireframe preview (single HTML)](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)
- [Wireframe source](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)

PR #13 contains one wireframe HTML. The similarly named files on the closed predecessor branch are
historical artifacts and are not part of this PR.

## Product Decisions Captured

- Agent/LLM uses `noracloud_status`, `noracloud_publish`, `noracloud_agent`, `noracloud_version`, `noracloud_device`, `noracloud_session`, `noracloud_turn`, and `noracloud_observe` through the shared NoraCloud operation contract.
- Auto-run completes directly; ask-before-running pauses the same Tool Call for Approve once/Always allow in this workspace/Deny; never-run blocks before mutation. A read-only preparation phase resolves `agent/cloud-agent.json` and its instruction files into the effective definition shown for approval.
- The approval preview exposes the first-publish destination, effective LLM/Voice/Cron values, readable instruction summaries with file/diff access, exact persistent changes, and explicit exclusions. Users approve the displayed meaning and consequences; hashes remain collapsed system evidence that binds execution to the reviewed snapshot.
- The example is a first publish from an unbound Workspace: it creates the Agent, initial immutable Version, initial Live pointer, and Workspace binding. It does not claim a previous Live Version or affected Devices.
- The completed Tool result is the canonical receipt. The right panel remains on Changes during completion and refreshes to canonical Cloud state only in the following state; no duplicate proposal or receipt card is required.
- The completed receipt triggers a silent Workspace Cloud refresh outside Conversation. Opening Cloud, manual refresh, Settings mutations, and reconnect may use the same UI path; none creates a follow-up `noracloud_status` Tool Call.
- The review example uses Ask before running so PM can see the control point. Approval and Auto-run both settle the same Tool Call directly to its receipt; the journey does not add a separate progress frame.
- Settings calls typed NoraCloud APIs directly. Agent rename/live/rollback/delete, Device track/rebind/rotate/revoke, and Session end never enter Conversation or create a visible Tool Call.
- The right Cloud panel follows the current Workspace and refreshes after the canonical tool receipt. One compact Workspace Cloud card replaces the ambiguous split between Bound Agent and Related Cloud Resources.
- Versions remain immutable and read-only except for Make Live/Rollback from their owning Agent. Rotation never displays a credential value.
- Account Usage is NoraCloud-recorded activity, not local coding Run Records, billing, balance, or remaining quota.

## Review Focus

- Can PM follow the user goal through firmware coding, the identity question, Agent-file edits, approval, execution, and persistent result without needing implementation context?
- Does the first-publish approval show enough resolved configuration, instruction provenance, effects, exclusions, and snapshot identity for an informed decision?
- Is the completed tool result sufficient as the canonical receipt without duplicating it in the right Cloud panel?
- Do Conversation MCP operations and direct Settings API actions remain visibly distinct?
- Are Agent, Version, Device, and Session controls explicit about dependencies, reconnect impact, and secret non-disclosure?

## Files

- `ui-contracts/cloud-resources/cloud-resource-awareness.pm.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.record.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html`
- `ui-contracts/cloud-resources/cloud-resource-awareness.pr.md`
- `ui-contracts/README.md`

## Verification

- Parsed the inline wireframe JavaScript successfully.
- Verified the MCP-first main path, permission/recovery branches, and direct Settings action paths have renderers.
- Rendered the static HTML locally and visually checked the canvas, navigation, route rows, and Settings paths.
- Ran `git diff --check`.
