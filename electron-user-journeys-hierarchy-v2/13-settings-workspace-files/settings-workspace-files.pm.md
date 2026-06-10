# Journey 13: Settings - Workspace Files Record

## Journey Context

### Current Work

This artifact turns Journey 13 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user opens the Workspace Files tab in Settings.

### User Behavior

The user views and edits SOUL, IDENTITY, USER, AGENTS, TOOLS, HEARTBEAT, MEMORY, and other workspace files.

### Client State Changes

- After the tab opens, the client loads the current workspace file list and selected file content.
- Selecting a different file swaps editor content and updates current filename and description state.
- After editing content, the editor enters dirty state and save/reset actions become available.
- During save, the UI enters saving; success clears the dirty marker, while failure preserves edited content and shows an error.
- If a file is missing, the UI shows missing/empty state and offers create or restore when supported.

### End State

The user finishes viewing, saving, resetting, or rests with unsaved edits for a workspace file.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 13 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 13 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 13.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user opens the Workspace Files tab in Settings.
2. User action: The user views and edits SOUL, IDENTITY, USER, AGENTS, TOOLS, HEARTBEAT, MEMORY, and other workspace files.
3. Visible state changes: the client moves through the states listed above.
4. End: The user finishes viewing, saving, resetting, or rests with unsaved edits for a workspace file.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/13-settings-workspace-files/settings-workspace-files.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/13-settings-workspace-files/settings-workspace-files.wireframe.html`
