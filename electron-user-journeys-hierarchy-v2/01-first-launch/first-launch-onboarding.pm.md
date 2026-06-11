# First Launch Onboarding Journey Record

## Journey Context

### Current Work

This artifact turns the first journey from the user-journey inventory into an interaction record. It documents the implemented first-run onboarding experience: Welcome, provider setup, personality setup, notification permission, completion, and the handoff into the first usable Chat screen.

### Current User-Visible Behavior

On a fresh install, users see a focused five-step onboarding flow before the normal app surface is available.

1. Welcome introduces the product and offers one primary action.
2. Provider setup asks users to pick an AI service, enter credentials or a local endpoint, select a default model, verify the connection, or explicitly skip verification.
3. Personality setup lets users choose a tone, add optional instructions, preview the generated agent identity, then save or skip.
4. Notifications asks whether to enable system notifications now or continue later.
5. Completion summarizes the connected provider/model and sends the user into a new Chat surface.

### Implementation Maturity

`implemented`

- Current user-visible evidence: the flow exists today in the app and is visible in current first-run behavior.
- Assumptions: none. This wireframe does not introduce new screens; it documents the current user-visible flow and recovery cases.
- Dependencies: provider verification depends on the selected AI service or local endpoint being reachable; notification permission depends on the operating system prompt.

### Documentation Goal

Capture the existing five-step first-run onboarding flow as the baseline for Journey 01 documentation and later HTML interaction drafts.

### Documentation Focus

Record the behavior users experience during first launch: what is required, what can be skipped, what state changes are visible, and where the user lands after setup.

### Scope Boundaries

- Included: implemented first-run onboarding screens, visible states, happy path, recoverable errors, and the final handoff into Chat.
- Excluded: Settings provider management, Code workspace creation, channel setup, post-onboarding Chat behavior, and the full provider catalog.

### Non-Goals

- Do not redesign provider settings after onboarding.
- Do not include Code workspace creation after onboarding enters the main app.
- Do not cover mobile preview, catalog publishing, channels, or advanced settings.
- Do not expose implementation details in this journey record.

### Recorded Flow

The wireframe keeps the current product shape and makes the state transitions explicit:

- A single centered onboarding window remains the first-run shell.
- The happy path appears as a left-to-right chain from Welcome to first Chat.
- Provider setup shows three important states: empty input, key entered but unverified, and verified or skipped.
- Personality shows the selected tone, optional instruction field, preview, skip, and saving state.
- Notifications has two valid exits: Maybe later and Turn on notifications.
- Completion confirms the connected provider/model before writing the onboarding-complete state and opening Chat.
- Recovery notes stay attached to the relevant onboarding step so the journey record remains focused on the main first-run path.

### Benefits

- The whole first-run path can be reviewed without reading implementation notes.
- Required and optional choices are visible in one artifact.
- The provider verification skip path is documented as an existing branch.
- The artifact draws a clean boundary between Journey 01 and later Code workspace creation.

### Downsides / Tradeoffs

- A five-step flow may feel long for users who only want to try Chat quickly.
- Asking for notification permission during first run may be premature.
- Allowing provider verification skip reduces first-run friction but can move failures into the first message.
- Personality setup creates a stronger agent identity, but it adds another step before the user sees value.

### Follow-Up Notes

- Code workspace creation after onboarding belongs to a later journey.
- Provider verification skip is recorded as an existing branch, not a new recommendation.
- Notification permission is recorded as part of the current flow, even though users can change it later.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/01-first-launch/first-launch-onboarding.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/01-first-launch/first-launch-onboarding.wireframe.html`
