## Summary

- Replace the CLI-shaped Cloud update proposal with semantic NoraCloud MCP Tool Calls, permission-aware branches, and tool receipts.
- Make Settings the direct API control plane for Agent, Device, and Session actions, with confirmations, conflicts, dependency blocking, and protected credential handling.
- Scope: proposed product interaction; no Electron or NoraCloud runtime implementation is included.

## Links

- [Journey record](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.record.md)
- [PM review](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.pm.md)
- [Wireframe preview](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)
- [Wireframe source](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)

## Product Decisions Captured

- Agent/LLM uses `noracloud_status`, `noracloud_publish`, `noracloud_agent`, `noracloud_version`, `noracloud_device`, `noracloud_session`, `noracloud_turn`, and `noracloud_observe` through the shared NoraCloud operation contract.
- Auto-run completes directly; ask-before-running pauses the same Tool Call for Approve/Always allow/Deny; never-run blocks before mutation. The tool result is the receipt; no duplicate proposal or receipt card is required.
- Settings calls typed NoraCloud APIs directly. Agent rename/live/rollback/delete, Device track/rebind/rotate/revoke, and Session end never enter Conversation or create a visible Tool Call.
- The right Cloud panel follows the current Workspace and refreshes from the canonical tool receipt.
- Versions remain immutable and read-only except for Make Live/Rollback from their owning Agent. Rotation never displays a credential value.
- Account Usage is NoraCloud-recorded activity, not local coding Run Records, billing, balance, or remaining quota.

## Review Focus

- Does the same semantic Tool Call remain understandable across auto-run, approval, denial, blocked, conflict, partial, and unknown outcomes?
- Is the tool result sufficient as the canonical receipt without a second proposal or execution dashboard?
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
