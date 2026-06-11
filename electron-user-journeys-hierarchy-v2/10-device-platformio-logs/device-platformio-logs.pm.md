# Journey 10: Device / PlatformIO / Logs Record

## Journey Context

### Current Work

This artifact turns Journey 10 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user opens the Device tab or Device Log panel in the Code workbench.

### User Behavior

The user checks PlatformIO state, refreshes devices, inspects connected devices, device metrics, USB fallback, and logs.

### Client State Changes

- When the Device tab opens, the client loads PlatformIO availability, installation state, and device connection state.
- When PlatformIO is unavailable, the UI shows install/repair guidance; when available, it shows ready state and available device actions.
- While refreshing devices, the UI enters loading; completion shows connected/disconnected device cards, firmware version, memory, latency, and similar information.
- Opening logs starts receiving device or USB monitor output; pausing stops visual autoscroll, and resuming continues it.
- Changing filters updates the visible log list immediately by level, source, or search term.
- If the device disconnects or probing fails, device cards and log area switch to disconnected/error state while preserving refresh or retry.

### End State

The user can tell whether the development device, PlatformIO, and log path are ready, missing, disconnected, or error.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 10 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 10 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 10.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user opens the Device tab or Device Log panel in the Code workbench.
2. User action: The user checks PlatformIO state, refreshes devices, inspects connected devices, device metrics, USB fallback, and logs.
3. Visible state changes: the client moves through the states listed above.
4. End: The user can tell whether the development device, PlatformIO, and log path are ready, missing, disconnected, or error.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/10-device-platformio-logs/device-platformio-logs.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/10-device-platformio-logs/device-platformio-logs.wireframe.html`
