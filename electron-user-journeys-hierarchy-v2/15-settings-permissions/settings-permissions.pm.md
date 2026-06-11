# Journey 15: Settings - Permissions Record

## Journey Context

### Current Work

This artifact turns Journey 15 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user opens the Permissions tab in Settings, or arrives from a runtime permission/system permission prompt.

### User Behavior

The user configures exec approval and allowlist, and reviews or handles notifications, microphone, accessibility, and other system permissions.

### Client State Changes

- After Permissions opens, the client loads current execution policy, allowlist, and system permission state.
- After changing exec approval policy, the UI shows the new policy choice and after saving it affects whether later tool execution requires confirmation.
- Adding or removing allowlist entries updates the list immediately; save failure restores or flags unsaved state.
- When system permission is missing, the UI shows missing/needs action and offers open system settings or recheck.
- After the user grants permission and returns to the app, recheck changes state from missing to granted; if still denied, it remains blocked with explanation.

### End State

Permissions shows final execution policy and system permission state. Later Chat/Code runs follow these permissions as auto-run, request approval, or blocked.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 15 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 15 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 15.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user opens the Permissions tab in Settings, or arrives from a runtime permission/system permission prompt.
2. User action: The user configures exec approval and allowlist, and reviews or handles notifications, microphone, accessibility, and other system permissions.
3. Visible state changes: the client moves through the states listed above.
4. End: Permissions shows final execution policy and system permission state. Later Chat/Code runs follow these permissions as auto-run, request approval, or blocked.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/15-settings-permissions/settings-permissions.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/15-settings-permissions/settings-permissions.wireframe.html`
