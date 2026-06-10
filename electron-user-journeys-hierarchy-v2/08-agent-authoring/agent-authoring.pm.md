# Journey 08: Agent Authoring Record

## Journey Context

### Current Work

This artifact turns Journey 08 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user clicks Edit Agent in a Code workspace, or enters Agent Authoring from an agent-configuration entry.

### User Behavior

The user edits Soul, Style, Tools, Heartbeat, Bootstrap, Skills, and Capabilities, reviews capability differences, adds or tests skills, and saves changes.

### Client State Changes

- Entering Authoring switches the UI from a code session to the agent configuration editor and loads the current workspace agent files and config state.
- Switching among Soul, Style, Tools, Heartbeat, Bootstrap, and other areas updates the main editor and marks the matching nav item active.
- After the user edits text or config, the page enters dirty state; save button or status changes from clean to unsaved/saving.
- After save succeeds, dirty state clears and saved feedback appears; save failure preserves user input and shows an error.
- The Skills area lists existing skills; the user can create, edit, preview, or run probes, with pending during execution and success/failure afterward.
- The Capabilities area shows agent and device/manifest capability state; refreshing updates lists, difference hints, and availability.

### End State

Agent configuration is clearly saved, failed-to-save, or dirty. The user can return to the Code session or continue to publishing.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 08 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 08 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 08.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user clicks Edit Agent in a Code workspace, or enters Agent Authoring from an agent-configuration entry.
2. User action: The user edits Soul, Style, Tools, Heartbeat, Bootstrap, Skills, and Capabilities, reviews capability differences, adds or tests skills, and saves changes.
3. Visible state changes: the client moves through the states listed above.
4. End: Agent configuration is clearly saved, failed-to-save, or dirty. The user can return to the Code session or continue to publishing.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/08-agent-authoring/agent-authoring.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/08-agent-authoring/agent-authoring.wireframe.html`
