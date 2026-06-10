# Journey 01 - First Launch Onboarding

This file is the text interaction description for the corresponding HTML wireframe. It describes how the user enters the journey, progresses through it, handles branches, and reaches the end state.

- Source HTML: [01-first-launch-and-base-setup.wireframe.html](01-first-launch-and-base-setup.wireframe.html)
- Status: needs verification
- Verification note: Verify the minimum first-run setup that must happen before the user reaches chat.

## User Goal

New users first see guided onboarding that connects a provider, optionally sets personality, chooses notification permission, and hands off to Chat.

## Entry And Preconditions

- Entry: Welcome
- Initial user state: First launch starts inside the product shell, with guided setup as the primary action.
  - Get started is the only welcome action.
  - No marketing hero or separate landing page.

## Interaction Flow

1. Welcome
   - User sees: First launch starts inside the product shell, with guided setup as the primary action.
   - Get started is the only welcome action.
   - No marketing hero or separate landing page.
2. Provider access
   - User sees: User selects a provider, adds credentials or local endpoint details, and verifies or explicitly skips validation.
   - Matches current onboarding provider step.
   - Continue requires verified or skipped state.
3. Personality
   - User sees: User chooses tone and optional instructions before the app writes the initial personality file.
   - Save and Continue can write SOUL.md.
   - Skip remains available.
4. Notifications
   - User sees: User chooses whether to request native notification permission now or later.
   - Maybe later is a valid path.
   - Permission copy stays explicit.
5. Completion
   - User sees: Completion summarizes the selected provider/model and hands off to the first usable Chat surface.
   - Start chatting persists onboarding complete.
   - Main IDE appears after this step.

## Branches And Recovery States

### Detail states

- Provider verification skip: Skipping verification is explicit and still continues through personality and notification steps.
  - Missing provider card stays near composer.
  - User can resume setup from Chat.
- More providers: The provider step can reveal additional provider cards without changing the onboarding shell.
  - More Models reveals a larger provider set.
  - Selection resets provider-specific inputs.
- Reopen setup: Settings exposes the same setup choices after onboarding.
  - Users can change provider, personality, and notifications.
  - Incomplete setup remains resumable.

### Recovery states

- Provider test failed: User keeps their draft key state and can retry, edit, or explicitly skip verification.
  - Error is secret-safe and field-local.
- Notification skipped: Skipping notification permission still advances to completion without an OS prompt.
  - No permission request is fired.
  - User can change this later in system settings.


## Verification Checklist

- Mandatory setup: Verify whether provider configuration is required before first chat or only recommended.
  - Impacts activation speed and first-message success.
- Profile depth: Verify whether identity/tone belongs in setup or after first successful conversation.
  - Impacts perceived setup length.
- Notification timing: Verify whether notification permission waits for scheduled tasks or appears in setup.
  - Blocking because OS prompt timing affects trust.

## End State

- Ends at: Completion
  - Completion summarizes the selected provider/model and hands off to the first usable Chat surface.
  - Start chatting persists onboarding complete.
  - Main IDE appears after this step.

## Notes

- User outcome: Reviewers can inspect the actual setup screen structure while keeping the map-like canvas overview.
- Constraints: Wireframe cards are denser than text cards, so each card focuses on the state reviewers must verify.
- Evidence gaps: Should provider verification be skippable in the same way for local and hosted providers?
