# Journey 02: Main App Startup / Recovery State Record

## Journey Context

### Current Work

This artifact turns Journey 02 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user launches the app after onboarding is complete, or the renderer refreshes/restarts while the app is running.

### User Behavior

The user waits for the main surface to load and reviews the Gateway banner, current mode, recent conversations, update notice, and available input area.

### Client State Changes

- The client first enters a startup check and reads persisted onboarding, global mode, sessions, workspaces, open tabs, and settings.
- The Gateway area moves from checking/starting to connected, disconnected, or error; each state changes banner copy, retry/restart actions, and some runtime capabilities.
- The app restores the previous Chat or Code mode; if no session can be restored, it shows an empty state and creation entry.
- If an active runtime run exists, the client tries to reconnect; success restores running indicators, the message stream, or permission request card, while failure shows retry/resend recovery.
- If an update is available, the UI shows an update notice; the user can defer it or enter download/install.

### End State

The client rests on an operable main surface. The user can continue the previous session, create a new one, or recover from Gateway/update state.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 02 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 02 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 02.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user launches the app after onboarding is complete, or the renderer refreshes/restarts while the app is running.
2. User action: The user waits for the main surface to load and reviews the Gateway banner, current mode, recent conversations, update notice, and available input area.
3. Visible state changes: the client moves through the states listed above.
4. End: The client rests on an operable main surface. The user can continue the previous session, create a new one, or recover from Gateway/update state.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/02-main-app-startup/main-app-startup-recovery.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/02-main-app-startup/main-app-startup-recovery.wireframe.html`
