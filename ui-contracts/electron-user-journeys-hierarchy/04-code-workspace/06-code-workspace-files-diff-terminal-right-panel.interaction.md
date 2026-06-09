# Journey 06 - Code Workspace Files Diff Terminal Right Panel

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [06-code-workspace-files-diff-terminal-right-panel.wireframe.html](06-code-workspace-files-diff-terminal-right-panel.wireframe.html)
- Status: Status: current-shell reference
- Verification note: Based on code-workspace-runtime-layout.jpg.

## User Goal

The Code workspace keeps file tree, changes, device state, and terminal logs in the right/bottom panels while the session output stays central.

## Entry And Preconditions

- Entry: All Files tab
- Initial user state: Right panel starts on All Files with project-root, firmware-app, and logs rows.
   - File context is always adjacent to output.
   - Terminal strip remains at bottom.

## Interaction Flow

1. All Files tab
   - User sees: Right panel starts on All Files with project-root, firmware-app, and logs rows.
   - File context is always adjacent to output.
   - Terminal strip remains at bottom.
2. Changes tab
   - User sees: User switches to Changes to review generated edits without leaving the session.
   - Diff is scoped to current workspace.
   - Changed rows are highlighted.
3. Terminal output
   - User sees: Terminal and Device Log stay docked for command feedback.
   - Bottom dock is persistent.
   - Main output references command state.
4. Device panel
   - User sees: Device tab summarizes connected hardware and log state.
   - No separate page jump.
   - Device status stays beside code context.

## Branches And Recovery States

### Recovery states

- No changes: Changes tab shows an empty reviewed state when no diff exists.
   - Empty state is inside the right panel.
- Command failure: Failed command output appears in the main stream and terminal context remains visible.
   - Error is not hidden in terminal only.
   - Retry is near the output.
- Device disconnected: Device tab shows disconnected state while code session remains usable.
   - Device actions are disabled with reason.

## End State

- Ends at: Device disconnected
  - Device tab shows disconnected state while code session remains usable.
   - Device actions are disabled with reason.

## Notes

- User outcome: Reviewing files, diffs, and logs does not break the code conversation.
- Constraints: The exact file-row icons are simplified but panel placement follows the screenshot.
- Evidence gaps: Need screenshot for expanded file tree and device log detail rows.
