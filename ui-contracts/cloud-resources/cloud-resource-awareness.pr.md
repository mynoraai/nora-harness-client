## Summary

- Add journey records and wireframe artifacts for NoraCloud resource awareness in conversation and the Electron Workspace overview.
- Scope: not implemented yet.

## Links

- [Electron overview journey record](https://github.com/mynoraai/nora-harness-client/blob/pm/cloud-resource-awareness-wireframe/ui-contracts/cloud-resources/cloud-resource-awareness.record.md)
- [Conversation journey record](https://github.com/mynoraai/nora-harness-client/blob/pm/cloud-resource-awareness-wireframe/ui-contracts/cloud-resources/cloud-resource-conversation.record.md)
- [Electron overview wireframe preview](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/cloud-resource-awareness-wireframe/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)
- [Electron overview wireframe source](https://github.com/mynoraai/nora-harness-client/blob/pm/cloud-resource-awareness-wireframe/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)
- [Conversation wireframe preview](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/cloud-resource-awareness-wireframe/ui-contracts/cloud-resources/cloud-resource-conversation.wireframe.html)
- [Conversation wireframe source](https://github.com/mynoraai/nora-harness-client/blob/pm/cloud-resource-awareness-wireframe/ui-contracts/cloud-resources/cloud-resource-conversation.wireframe.html)

## Review Context

- Mode: resolved journey records and proposed wireframes.
- Source: the approved NoraCloud resource-awareness PRD, the current NoraHarness Code workspace shape, and verified current NoraCloud product constraints.
- Review focus: whether conversation decisions remain primary, whether the right-side Cloud overview is sufficiently lightweight, and whether unsupported candidate/testing behavior is represented honestly.

## Files

- `ui-contracts/README.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.record.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html`
- `ui-contracts/cloud-resources/cloud-resource-awareness.pr.md`
- `ui-contracts/cloud-resources/cloud-resource-conversation.record.md`
- `ui-contracts/cloud-resources/cloud-resource-conversation.wireframe.html`

## Verification

- Opened both static HTML wireframes locally and verified that the canvases, route rows, zoom, pan, jump list, and product frames render.
- Verified that every route in each record has a matching full product-frame row.
- Ran `git diff --check`.
