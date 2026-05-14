# UI Contracts

These source-anchored contracts document user-visible Electron UI behavior, gateway/provider boundaries, and the planned L1/L2/L3 test map.

## Maintenance Rules

- Keep every behavior row tied to current source evidence. When changing any anchor such as `Component.tsx:123`, run a repository search for that exact `file:line` pair and update sibling contracts in the same change.
- Prefer stable behavior anchors over decorative markup lines. If a source line moves from a visible control to incidental state setup, retarget the evidence to the closest current owner of the documented behavior.
- For diagrams, keep the readable follow-up table near the diagram: sequence diagrams need a read-order table, and state diagrams need state responsibilities plus transition labels.
- Do not edit generated translated docs from this area unless the task explicitly asks for the generated output.
