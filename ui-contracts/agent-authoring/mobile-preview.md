# Agent Authoring Mobile Preview Removed Boundary

Source rows: `AUTH-04`
Entry path: Agent Authoring
Status: Deleted / historical

Mobile Preview is marked as a deleted entry for the current Agent Authoring
inventory. Do not add it to the active wireframe, and do not expand it as a
current L3 journey.

## Current Contract

| Row | Treatment | Notes |
| --- | --------- | ----- |
| `AUTH-04` | Deleted | Mobile Preview is not a current Agent Authoring entry. |

## Historical Anchors

Older Electron code and tests may still contain `Preview on Mobile`,
`MobilePreviewDialog`, mobile preview tokens, and QR/link state handling. Treat
those as historical implementation references only for this UI-contract pass.

## Replacement

Active Agent Authoring journey work should use:

- `file-edit.md` for the Edit Agent surface.
- `publish-export.md` for Publish to Catalog and package export.
- `capabilities.md` and `skills.md` for the remaining authoring sections.
