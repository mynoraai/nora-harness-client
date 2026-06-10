# Journey 12: Settings - General Record

## Journey Context

### Current Work

This artifact turns Journey 12 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user opens Settings from the app settings entry and lands on the General tab.

### User Behavior

The user checks Gateway status, restarts Gateway, and configures proxy, login item, dock, debug tools, and other general settings.

### Client State Changes

- After Settings opens, the General tab loads current local settings and Gateway state.
- Gateway state updates from loading to running, stopped, restarting, or error; the restart button enables or disables based on state.
- After the user toggles login item, dock, debug tools, or similar switches, the switch immediately reflects the target state; saving or failure shows matching feedback.
- After the user edits proxy or similar fields, the form enters changed state; save success returns it to clean, while failure preserves input and shows an error.
- After Settings closes, the main app updates dock, debug UI, or connection behavior based on the new settings.

### End State

General settings are saved or left in a retryable error state. Current Gateway state remains visible in Settings.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 12 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 12 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 12.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user opens Settings from the app settings entry and lands on the General tab.
2. User action: The user checks Gateway status, restarts Gateway, and configures proxy, login item, dock, debug tools, and other general settings.
3. Visible state changes: the client moves through the states listed above.
4. End: General settings are saved or left in a retryable error state. Current Gateway state remains visible in Settings.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/12-settings-general/settings-general.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/12-settings-general/settings-general.wireframe.html`
