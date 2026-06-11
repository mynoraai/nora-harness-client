# Journey 18 - Settings Permissions

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [18-settings-permissions.wireframe.html](18-settings-permissions.wireframe.html)
- Status: Status: current-shell reference
- Verification note: No dedicated Permissions screenshot found; uses Settings shell as product reference.

## User Goal

Permissions settings use the Settings shell to review granted scopes, revoke access, and explain effects before destructive changes.

## Entry And Preconditions

- Entry: Open Permissions
- Initial user state: User selects Permissions from Settings left nav.
   - Same settings window.
   - Scope list is the main content.

## Interaction Flow

1. Open Permissions
   - User sees: User selects Permissions from Settings left nav.
   - Same settings window.
   - Scope list is the main content.
2. Review scope
   - User sees: A selected permission row explains what it allows and where it is used.
   - Effects are explicit before revoke.
3. Revoke permission
   - User sees: Revoke shows confirmation and the affected surfaces.
   - No one-click destructive revoke.
4. Permission updated
   - User sees: Settings confirms the updated permission list.
   - State is visible after action.

## Branches And Recovery States

### Recovery states

- Revoke blocked: A running session blocks revoke until stopped or confirmed.
   - Reason and stop action visible.
- Grant expired: Expired grant shows reconnect/grant again.
   - No silent permission loss.
- Partial revoke: If one scope fails to revoke, successful scopes remain updated.
   - Partial result is explicit.

## End State

- Ends at: Partial revoke
  - If one scope fails to revoke, successful scopes remain updated.
   - Partial result is explicit.

## Notes

- User outcome: Permission management remains in Settings with explicit consequences.
- Constraints: Exact scope names need screenshots or implementation references.
- Evidence gaps: Need screenshot for Permissions tab.
