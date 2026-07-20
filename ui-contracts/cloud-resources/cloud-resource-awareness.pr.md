## Summary

- Add PM, journey-record, and wireframe artifacts for Cloud resource awareness.
- Decision to unblock: use one stateful operation card, a current coding-session right Cloud view, and global Cloud system information inside Settings.
- Scope: partial.

## Links

- [Journey record](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.record.md)
- [PM doc](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.pm.md)
- [Wireframe preview](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)
- [Wireframe source](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)

## Review Context

- Mode: PM decision converted into a confirmed journey record and wireframe.
- Source: current Code Workspace, ordinary tool, right-workbench, Settings, coding-session, Run Record Usage, and user-confirmed product boundaries.
- Review focus: verify that Settings owns global system information, the right Cloud tab leads with readable Agent behavior and LLM/listening/speaking capabilities, local development adds no resource card, and Cloud resources are never presented as Workspace- or session-owned.

## PM Alignment Check

- PM audience: product owner and design reviewer.
- Discuss: semantic Agent summaries, technical-detail boundaries, scope labels, operation-card continuity, deduplication, and handoff between session and Settings levels.
- Do not discuss: a new app-level Cloud destination outside Settings, local CPU/build-cache confirmation, full NoraCloud mutation controls, billing, or secret display.

## Files

- `ui-contracts/cloud-resources/cloud-resource-awareness.pm.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.record.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html`
- `ui-contracts/cloud-resources/cloud-resource-awareness.pr.md`
- `ui-contracts/README.md`

## Verification

- Opened the static HTML wireframe locally and verified the canvas renders.
- Verified all eight route rows, 41 full product cards, seven detail cards, four state-language cards, and four recovery cards.
- Rendered the updated HTML in headless Chrome and visually checked the current-session Cloud and Settings frames.
- Ran `git diff --check`.
