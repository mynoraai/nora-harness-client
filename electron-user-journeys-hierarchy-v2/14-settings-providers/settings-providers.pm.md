# Journey 14: Settings - Providers Record

## Journey Context

### Current Work

This artifact turns Journey 14 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user opens the Providers tab in Settings, or jumps to Provider configuration from a provider-missing prompt.

### User Behavior

The user adds, edits, or removes providers, fills API key, base URL, enabled models, and selects a default model.

### Client State Changes

- After Providers opens, the client loads existing providers and default model state.
- When adding a provider, the form starts empty; save/verify stays unavailable until required fields are present.
- After API key or base URL input, verification becomes available; pending is shown during verification, success shows verified, and failure shows inline error.
- After changing enabled models or default model, list and default badge update; save success returns configuration to clean state.
- When deleting a provider, the UI asks for confirmation; after deletion succeeds, the provider is removed and related default model may fall back or require reselection.

### End State

Provider configuration is ready for runs, or clearly indicates missing authentication, verification failure, or no default model waiting for user action.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 14 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 14 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 14.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user opens the Providers tab in Settings, or jumps to Provider configuration from a provider-missing prompt.
2. User action: The user adds, edits, or removes providers, fills API key, base URL, enabled models, and selects a default model.
3. Visible state changes: the client moves through the states listed above.
4. End: Provider configuration is ready for runs, or clearly indicates missing authentication, verification failure, or no default model waiting for user action.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/14-settings-providers/settings-providers.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/14-settings-providers/settings-providers.wireframe.html`
