# Journey 11: LVGL Preview Record

## Journey Context

### Current Work

This artifact turns Journey 11 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user opens the Preview tab or LVGL Preview panel in the Code workbench.

### User Behavior

The user starts preview, triggers build/capture/rebuild, and inspects screenshot result, errors, and logs.

### Client State Changes

- When the Preview tab opens, the client checks LVGL preview capability, dependencies, and current workspace state.
- If preview is unavailable, the UI shows missing dependency or non-runnable reason and disables the start action.
- After the user starts or rebuilds, the UI enters building/capturing state and shows progress steps or a loading overlay.
- On success, the screenshot area changes from empty state to the latest preview image and shows metadata such as time, size, or source.
- If build or capture fails, the UI shows error state, log summary, and retry; any old screenshot is preserved or replaced with an error placeholder according to UI rules.

### End State

The user sees the latest LVGL screenshot, or sees a clear failure reason and retry action.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 11 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 11 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 11.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user opens the Preview tab or LVGL Preview panel in the Code workbench.
2. User action: The user starts preview, triggers build/capture/rebuild, and inspects screenshot result, errors, and logs.
3. Visible state changes: the client moves through the states listed above.
4. End: The user sees the latest LVGL screenshot, or sees a clear failure reason and retry action.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/11-lvgl-preview/lvgl-preview.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/11-lvgl-preview/lvgl-preview.wireframe.html`
