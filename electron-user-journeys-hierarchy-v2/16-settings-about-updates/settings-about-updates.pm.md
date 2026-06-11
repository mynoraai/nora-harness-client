# Journey 16: Settings - About / Updates Record

## Journey Context

### Current Work

This artifact turns Journey 16 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user opens the About / Updates tab in Settings, or clicks an update notice in the main UI.

### User Behavior

The user checks version information, checks for updates, downloads an update, and chooses install or later.

### Client State Changes

- After About/Updates opens, the UI shows current app version, build information, and updater state.
- After the user checks for updates, state moves from idle to checking; if none exists, it shows up to date.
- When an update exists, the UI shows new version information and a download entry; after clicking download it enters downloading and shows progress.
- After download completes, state changes to ready to install and the install/restart button becomes available.
- If checking or download fails, state becomes error and the UI shows failure reason and retry.

### End State

The user confirms the app is up to date, finishes download and waits to install, or rests on a retryable update error.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 16 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 16 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 16.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user opens the About / Updates tab in Settings, or clicks an update notice in the main UI.
2. User action: The user checks version information, checks for updates, downloads an update, and chooses install or later.
3. Visible state changes: the client moves through the states listed above.
4. End: The user confirms the app is up to date, finishes download and waits to install, or rests on a retryable update error.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/16-settings-about-updates/settings-about-updates.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/16-settings-about-updates/settings-about-updates.wireframe.html`
