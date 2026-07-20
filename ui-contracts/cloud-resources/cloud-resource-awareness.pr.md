## Summary

- Add PM, journey-record, and wireframe artifacts for Cloud resource awareness.
- Decision to unblock: separate Conversation operation history, current Workspace Cloud truth, and global NoraCloud system information in Settings.
- Scope: partial.

## Links

- [Journey record](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.record.md)
- [PM doc](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.pm.md)
- [Wireframe preview](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)
- [Wireframe source](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)

## Review Context

- Mode: PM decision converted into a confirmed journey record and wireframe.
- Source: current Conversation and Workspace model, NoraCloud Agent/Version/Live behavior, right-workbench and Settings shapes, and user-confirmed product boundaries.
- Review focus: verify the real Version/Live operation choices, Workspace-scoped configuration comparison, global Settings inventory, and removal of coding Run Records from Cloud Usage.

## PM Alignment Check

- PM audience: product owner and design reviewer.
- Discuss: semantic Agent summaries, Version/Live consequences, configuration-source comparison, operation-card continuity, global deduplication, and truthful Usage availability.
- Do not discuss: a new app-level Cloud destination outside Settings, local CPU/build-cache confirmation, full NoraCloud mutation controls, billing, or secret display.

## Files

- `ui-contracts/cloud-resources/cloud-resource-awareness.pm.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.record.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html`
- `ui-contracts/cloud-resources/cloud-resource-awareness.pr.md`
- `ui-contracts/README.md`

## Verification

- Opened the static HTML wireframe locally and verified the canvas renders.
- Verified all nine route rows, 46 full product cards, eight detail cards, five state-language cards, and four recovery cards.
- Rendered the updated HTML in headless Chrome and visually checked the Agent update card, Workspace Cloud, and Settings frames.
- Ran `git diff --check`.
