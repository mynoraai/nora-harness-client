## Summary

- Define how NoraHarness exposes persistent NoraCloud consequences before and after an Agent update.
- Separate Conversation operation history, current Workspace Cloud truth, and account-level Cloud system information inside Settings.
- Add Settings drill-down for Agents, immutable Versions, Devices, Cloud Sessions, and NoraCloud-recorded Usage.
- Scope: proposed product interaction; no Electron or NoraCloud runtime implementation is included.

## Links

- [Journey record](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.record.md)
- [PM review](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.pm.md)
- [Wireframe preview](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)
- [Wireframe source](https://github.com/mynoraai/nora-harness-client/blob/pm/workspace-cloud-awareness-v2/ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html)

## Product Decisions Captured

- Local file, validation, firmware, Terminal, Device Log, Device, and Preview work stays on existing Agent/tool surfaces.
- A persistent Agent update offers only `Approve` and `Not now`; a typed `Approve` accepts the same pending proposal.
- There is no separate product-level execution screen or generic `Change scope` action between approval and receipt.
- The right Cloud panel follows the current Workspace rather than the selected coding Conversation.
- Global resources remain inside Settings. Versions and Devices are reached through their owning Agent.
- Version Detail shows configuration and the published file manifest without fabricating unavailable remote Markdown bodies.
- Account Usage is NoraCloud-recorded activity, not local coding Run Records, billing, balance, or remaining quota.

## Review Focus

- Is the approval card specific enough for a user to understand the persistent Version/Live and Device consequences?
- Does the receipt report facts without adding an unnecessary execution dashboard?
- Do Workspace Cloud and Settings Cloud answer distinct questions without implying resource ownership by a coding Conversation?
- Are Agent, Version, Device, and Session drill-downs readable while remaining faithful to current NoraCloud data?

## Files

- `ui-contracts/cloud-resources/cloud-resource-awareness.pm.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.record.md`
- `ui-contracts/cloud-resources/cloud-resource-awareness.wireframe.html`
- `ui-contracts/cloud-resources/cloud-resource-awareness.pr.md`
- `ui-contracts/README.md`

## Verification

- Parsed the inline wireframe JavaScript successfully.
- Verified all seven Settings states have renderers and all three Settings drill-down paths are present.
- Rendered the static HTML locally and visually checked the canvas, navigation, route rows, and Settings paths.
- Ran `git diff --check`.
