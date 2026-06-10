# Journey 16 - Settings Providers Default Model

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [16-settings-providers-default-model.wireframe.html](16-settings-providers-default-model.wireframe.html)
- Status: Status: current-shell reference
- Verification note: No dedicated Providers screenshot found; uses Settings shell and current model labels from chat/code screenshots.

## User Goal

Provider settings use the Settings shell with Providers selected, showing connection state, default model, and provider-specific actions.

## Entry And Preconditions

- Entry: Open Providers
- Initial user state: User selects Providers in Settings.
   - Same Settings shell.
   - Provider state is settings content, not onboarding.

## Interaction Flow

1. Open Providers
   - User sees: User selects Providers in Settings.
   - Same Settings shell.
   - Provider state is settings content, not onboarding.
2. Choose default model
   - User sees: User changes default model from a settings dropdown.
   - Chat picker remains separate.
   - Default applies to new sessions.
3. Manage provider
   - User sees: Provider row exposes test, reconnect, and revoke actions.
   - Sensitive actions remain explicit.
4. Save provider defaults
   - User sees: Saved defaults are reflected when returning to Chat/Code.
   - Settings confirms save.

## Branches And Recovery States

### Recovery states

- Provider disconnected: Disconnected provider shows reconnect and keeps model selection disabled.
   - Reason visible.
- Test failed: Connection test failure appears on provider row.
   - No global blocker.
- No default model: If no default model is available, Chat/Code prompt user to choose one.
   - Settings shows missing default.

## End State

- Ends at: No default model
  - If no default model is available, Chat/Code prompt user to choose one.
   - Settings shows missing default.

## Notes

- User outcome: Provider defaults are managed in Settings and referenced by Chat/Code.
- Constraints: Exact provider rows need screenshots.
- Evidence gaps: Need screenshot for Providers tab.
