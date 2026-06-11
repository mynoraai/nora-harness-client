# Agent Authoring Mobile Preview Secondary Boundary

Source rows: `AUTH-04`
Entry path: Agent Authoring -> Preview on Mobile
Status: Secondary priority / historical implementation anchors

Mobile Preview remains documented as a secondary-priority Agent Authoring
capability. It is not part of the primary end-to-end journey inventory for this
pass, so do not expand it ahead of the primary authoring and publish flows.

## Current Contract

| Row | Treatment | Notes |
| --- | --------- | ----- |
| `AUTH-04` | Secondary priority | Keep the entry and historical anchors; do not treat it as a primary journey. |

## Historical Anchors

Older Electron code and tests may contain `Preview on Mobile`,
`MobilePreviewDialog`, mobile preview tokens, and QR/link state handling. Treat
those as secondary-priority references unless the inventory promotes Mobile
Preview into a primary journey.

## Primary Journey Relationship

Primary Agent Authoring journey work should prioritize:

- `file-edit.md` for the Edit Agent surface.
- `publish-export.md` for Publish to Catalog and package export.
- `capabilities.md` and `skills.md` for the remaining authoring sections.
