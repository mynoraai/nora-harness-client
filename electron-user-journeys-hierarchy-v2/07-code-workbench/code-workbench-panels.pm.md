# Journey 07: Code Workbench Panels Record

## Journey Context

### Current Work

This artifact turns Journey 07 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user inspects the right workbench and bottom panels inside a Code session.

### User Behavior

The user switches among Files, Changes, Diff/File tabs, Terminal, and Device Log to inspect files, changes, command output, and device logs.

### Client State Changes

- After Code loads, the right panel shows a default tab; clicking another tab changes panel content without changing the active Code session.
- The Files panel shows the workspace file tree; selecting a file opens a file tab or preview.
- The Changes panel shows the change list; selecting a change opens a diff view and keeps selection and diff content in sync.
- The Terminal panel shows the command area for the current session; while running it streams output and active state, and after completion it preserves results.
- The Device Log panel shows log stream, filters, pause/resume, and autoscroll state; changing filters narrows or restores visible log rows.
- Collapsing, expanding, or resizing panels changes layout size and visible area without losing the active session or message stream.

### End State

The user rests in a selected workbench view. File, diff, terminal, or log state remains aligned with the current Code session.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 07 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 07 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 07.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user inspects the right workbench and bottom panels inside a Code session.
2. User action: The user switches among Files, Changes, Diff/File tabs, Terminal, and Device Log to inspect files, changes, command output, and device logs.
3. Visible state changes: the client moves through the states listed above.
4. End: The user rests in a selected workbench view. File, diff, terminal, or log state remains aligned with the current Code session.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/07-code-workbench/code-workbench-panels.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/07-code-workbench/code-workbench-panels.wireframe.html`
