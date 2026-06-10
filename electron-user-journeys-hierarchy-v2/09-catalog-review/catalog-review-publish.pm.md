# Journey 09: Unified Publish Center / Catalog Review / Firmware Publish Record

## Journey Context

### Current Work

This artifact turns Journey 09 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user clicks Publish in Agent Authoring, or enters the unified publish center from a publishing status row.

### User Behavior

The user confirms login state, then reviews App record, Icon & screenshots, Agent package, and Firmware in one publish center; the user can edit app info/media, submit app/agent package review, review the result, and continue to production package or firmware publish from the same center.

### Client State Changes

- When signed out, publish does not submit directly and instead shows a login gate; after login, the user returns to publishing.
- After entering the publish center, the UI shows App record, Icon & screenshots, Agent package, and Firmware rows; each row shows draft, ready, missing, pending, approved, rejected, revoked, optional, or similar status based on completeness and backend state.
- When editing app info/media, the dialog shows display name, description, icon, and screenshots; changes enter unsaved state, saving disables the button, and success refreshes the row.
- After submitting app or agent package review, the submit button becomes pending; success changes state to submitted/in review and shows later editing constraints.
- When review returns approved/rejected/revoked, the publish center reflects the state through badges, copy, and available actions; approved rows can proceed to publish to production.
- The Firmware row reads version, channel, and device metadata from build/manifest; incomplete form or build output disables publish, publishing shows uploading/publishing, success refreshes available/latest state, and failure remains on the row with retry.

### End State

App record, agent package, and firmware all sit in one publish center with clear states: unsubmitted, in review, approved, rejected, revoked, ready for production, published, or failed and retryable.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 09 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 09 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 09.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user clicks Publish in Agent Authoring, or enters the unified publish center from a publishing status row.
2. User action: The user confirms login state, then reviews App record, Icon & screenshots, Agent package, and Firmware in one publish center; the user can edit app info/media, submit app/agent package review, review the result, and continue to production package or firmware publish from the same center.
3. Visible state changes: the client moves through the states listed above.
4. End: App record, agent package, and firmware all sit in one publish center with clear states: unsubmitted, in review, approved, rejected, revoked, ready for production, published, or failed and retryable.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/09-catalog-review/catalog-review-publish.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/09-catalog-review/catalog-review-publish.wireframe.html`
