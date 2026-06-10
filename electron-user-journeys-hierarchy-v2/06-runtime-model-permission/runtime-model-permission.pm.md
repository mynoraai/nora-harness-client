# Journey 06: Runtime / Model / Permission Record

## Journey Context

### Current Work

This artifact turns Journey 06 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user opens runtime/model selectors in the Chat or Code composer, or triggers runtime readiness, provider checks, or permission requests while sending.

### User Behavior

The user selects runtime, model, and thinking level; after sending, the user responds to provider missing, runtime startup failure, or tool permission prompts.

### Client State Changes

- The Runtime selector shows the current runtime; after the user switches it, composer and conversation state record the new runtime.
- The Model selector shows the current provider/model; after selection, later sends use the new model configuration.
- Before sending, if provider is not ready, the UI shows missing configuration, missing authentication, or unavailable model and blocks the run.
- After send, runtime state moves from idle to starting; success enters running, while failure becomes failed and shows the cause in the message stream or status bar.
- When a tool or command needs permission, the client inserts a permission request card; approval continues the run, denial ends that tool step and shows the result in the stream.
- After completion, runtime returns to idle or ready; if a recoverable run exists, refresh enters reconnecting and then restores running or failed.

### End State

Runtime, model, and permission choices are visible in the UI. The current turn is complete or rests in a clear recoverable, retryable, or approval-required state.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 06 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 06 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 06.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user opens runtime/model selectors in the Chat or Code composer, or triggers runtime readiness, provider checks, or permission requests while sending.
2. User action: The user selects runtime, model, and thinking level; after sending, the user responds to provider missing, runtime startup failure, or tool permission prompts.
3. Visible state changes: the client moves through the states listed above.
4. End: Runtime, model, and permission choices are visible in the UI. The current turn is complete or rests in a clear recoverable, retryable, or approval-required state.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/06-runtime-model-permission/runtime-model-permission.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/06-runtime-model-permission/runtime-model-permission.wireframe.html`
