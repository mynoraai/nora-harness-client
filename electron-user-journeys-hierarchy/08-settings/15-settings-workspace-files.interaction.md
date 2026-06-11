# Journey 15 - Settings Workspace Files

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [15-settings-workspace-files.wireframe.html](15-settings-workspace-files.wireframe.html)
- Status: Status: current-shell reference
- Verification note: No dedicated screenshot found; uses settings-general-gateway-network.jpg shell as product reference.

## User Goal

Workspace and file settings should use the same Settings shell, switching the left nav to Workspace and showing path, file access, and cleanup controls.

## Entry And Preconditions

- Entry: Open Workspace settings
- Initial user state: User selects Workspace from the Settings left nav.
   - Same Settings shell.
   - No separate wizard unless product screenshot proves it.

## Interaction Flow

1. Open Workspace settings
   - User sees: User selects Workspace from the Settings left nav.
   - Same Settings shell.
   - No separate wizard unless product screenshot proves it.
2. Review file access
   - User sees: Settings show file access scope and recent folders.
   - Scope is explicit.
   - Changes are local settings.
3. Change workspace root
   - User sees: User changes default path and saves after validation.
   - Validation happens inline.
4. Cleanup files
   - User sees: Cleanup action shows what will be removed before confirmation.
   - No silent delete.

## Branches And Recovery States

### Recovery states

- Path unavailable: Unavailable path shows an inline error and restore option.
   - Current path remains visible.
- Cleanup blocked: Cleanup is blocked while a code session is running.
   - Reason is visible.
- Permission revoked: Revoked file permission can be granted again from settings.
   - Grant action is explicit.

## End State

- Ends at: Permission revoked
  - Revoked file permission can be granted again from settings.
   - Grant action is explicit.

## Notes

- User outcome: Workspace settings inherit the current Settings shell.
- Constraints: Specific controls need current screenshots.
- Evidence gaps: Need screenshot for Workspace tab contents.
