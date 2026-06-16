# Agent Authoring Contracts

Source rows: `AUTH-01`, `AUTH-02`, `AUTH-03`, `AUTH-04`, `AUTH-05`
Entry path: Code mode -> active workspace -> `Edit Agent`
Status: Draft, evidence-only

This folder covers Agent Authoring in the Electron IDE. Use it when you need to understand the `Edit Agent` tab, editable agent files, skills, capabilities, mobile preview, publish, or export behavior.

## Owned Rows

| Source row | Contract file                            | User-visible surface                                                    |
| ---------- | ---------------------------------------- | ----------------------------------------------------------------------- |
| `AUTH-01`  | [file-edit.md](./file-edit.md)           | Agent authoring tab, editable core agent files, save/revert/status loop |
| `AUTH-02`  | [capabilities.md](./capabilities.md)     | Device capability manifest and agent-vs-device diff                     |
| `AUTH-03`  | [skills.md](./skills.md)                 | Skill list, SKILL.md editor/preview, new skill dialog, probe panel      |
| `AUTH-04`  | [mobile-preview.md](./mobile-preview.md) | One-time mobile preview QR dialog                                       |
| `AUTH-05`  | [publish-export.md](./publish-export.md) | Publish to Catalog dialog and package export component boundary         |

## Design Wireframes

Forward-looking design, not source-anchored evidence. Use these to see the proposed shape before it ships.

| Wireframe                                                                       | Covers                                                                                                                                                                            |
| ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [unified-publish-flow.wireframe.html](./unified-publish-flow.wireframe.html)    | Proposed end-to-end publish journey: author and publish to test, validate on the test mobile, promote, review and approve, live on the public mobile. Includes the Release hub, the reused dialogs, the two-track board, status language, the review state machine, and error states. |

Provenance: the decisions behind this wireframe are recorded in the HardwareHarness ADRs `056-unified-publish-flow-ux` (publish flow UX) and `057-test-prod-tracks` (test and production tracks via one catalog). Open the file in a browser; it is a pan and zoom canvas.

## Shared Entry Contract

| User action                       | UI precondition                                                     | UI result                                                                | Backend/API path           | Evidence                                                                                                                                                                                                                                                                                                    | Test coverage                                                                                                                                                                                                             |
| --------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------ | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Click `Edit Agent` from Code mode | `WorkingDirBar` has resolved an agent root for the active workspace | Opens or focuses an `agent-authoring` tab for that workspace             | Local tab store only       | [WorkingDirBar.tsx:76](../../../../apps/electron/src/renderer/src/components/chat/WorkingDirBar.tsx#L76), [WorkingDirBar.tsx:128](../../../../apps/electron/src/renderer/src/components/chat/WorkingDirBar.tsx#L128), [tab-store.ts:90](../../../../apps/electron/src/renderer/src/stores/tab-store.ts#L90) | L1 partial: [agent-files-resolve.test.ts:26](../../../../apps/electron/src/renderer/test/agent-files-resolve.test.ts#L26), [tab-store.test.ts](../../../../apps/electron/src/renderer/test/tab-store.test.ts); L3 no test |
| Render the tab content            | Active Code tab type is `agent-authoring`                           | `TabContentArea` renders `AgentAuthoringTab` with the tab workspace path | None beyond renderer state | [TabContentArea.tsx:45](../../../../apps/electron/src/renderer/src/components/code/TabContentArea.tsx#L45), [AgentAuthoringTab.tsx:63](../../../../apps/electron/src/renderer/src/components/agent-authoring/AgentAuthoringTab.tsx#L63)                                                                     | L2 no direct test                                                                                                                                                                                                         |

## Cross-Surface Data

Agent Authoring uses these shared boundaries:

| Boundary                      | Used by                                                          | Evidence                                                                                                                                                                                                                                                                                                                                                           |
| ----------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Workspace file read/write IPC | File editor, Skills, Capabilities diff, package export           | [workspace-client.ts:263](../../../../apps/electron/src/renderer/src/lib/workspace-client.ts#L263), [workspace-client.ts:298](../../../../apps/electron/src/renderer/src/lib/workspace-client.ts#L298), [ipc-channels.ts:47](../../../../apps/electron/src/main/ipc-channels.ts#L47), [ipc-channels.ts:72](../../../../apps/electron/src/main/ipc-channels.ts#L72) |
| Agent authoring store         | Dirty/saving/error state, saved timestamp, last published footer | [agent-authoring-store.ts:40](../../../../apps/electron/src/renderer/src/stores/agent-authoring-store.ts#L40), [AgentAuthoringTab.tsx:72](../../../../apps/electron/src/renderer/src/components/agent-authoring/AgentAuthoringTab.tsx#L72)                                                                                                                         |
| Device stream                 | Capabilities and mobile preview crash detection                  | [CapabilitiesSection.tsx:66](../../../../apps/electron/src/renderer/src/components/agent-authoring/CapabilitiesSection.tsx#L66), [MobilePreviewDialog.tsx:103](../../../../apps/electron/src/renderer/src/components/agent-authoring/MobilePreviewDialog.tsx#L103)                                                                                                 |
| Cloud catalog IPC             | Publish dialog                                                   | [ipc-cloud-catalog.ts:42](../../../../apps/electron/src/main/ipc-cloud-catalog.ts#L42), [cloud-catalog-client.ts:101](../../../../apps/electron/src/renderer/src/lib/cloud-catalog-client.ts#L101)                                                                                                                                                                 |

## Gaps

- TODO: add a stable `data-testid="agent-authoring-tab"` before L3 automation can reliably assert the tab content. The test index tracks this selector gap in `../tests/l3-computer-use-scenarios.md`.
- No L3 Electron harness exists for Agent Authoring. Existing coverage is L1/L2 component, store, and main-process helper tests.
- `ExportPackageDialog` exists and is tested, but this directory documents it as a component boundary because current `AgentAuthoringTab` exposes `Publish`, not a standalone export button.
