# AUTH-05 Historical Publish Flow Boundary

Status: Removed from current journey mapping

This file is intentionally retained only as a historical boundary note. Do not
use it as the active `AUTH-05` contract and do not use it to reframe Agent
Authoring as an account-owned or four-row publish journey.

Current source of truth:

- `agent-authoring/publish-export.md` owns the active Publish to Catalog and package export contract.
- `agent-authoring/file-edit.md` owns the Agent Authoring edit surface and the Publish entry point.
- `overview.md` maps Journey 09 to `AUTH-05` without adding `MAIN-04` as a publish row.

## Removed Assumptions

The previous version of this note described a modal with App record,
Icon & screenshots, Agent package, and Firmware rows. That framing is not the
active UI contract for this branch.

The active contract keeps these boundaries separate:

| Boundary | Current treatment |
| -------- | ----------------- |
| Publish to Catalog | Active `AUTH-05` behavior in `agent-authoring/publish-export.md`. |
| Account prerequisite | Shared login flow may be required by catalog APIs, but login is not the publish journey. |
| Mobile Preview | Secondary `AUTH-04` authoring capability, not the primary inventory journey. |
| Firmware publish | Do not use this file to fold firmware into Agent Authoring publish behavior. |

## Replacement

When updating Agent Authoring publish behavior, edit
`agent-authoring/publish-export.md` and the relevant test mapping files instead
of expanding this historical note.
