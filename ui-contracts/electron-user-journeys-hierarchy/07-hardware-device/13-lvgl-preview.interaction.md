# Journey 13 - LVGL Preview

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [13-lvgl-preview.wireframe.html](13-lvgl-preview.wireframe.html)
- Status: Status: current-shell reference
- Verification note: No dedicated LVGL preview screenshot found; uses the Code/device shell pending reference.

## User Goal

LVGL preview should live inside the current Code/device workspace, beside generated files and device logs.

## Entry And Preconditions

- Entry: Open preview
- Initial user state: User opens LVGL preview from the Code/device context.
   - Entry placement needs screenshot verification.
   - Right panel remains available.

## Interaction Flow

1. Open preview
   - User sees: User opens LVGL preview from the Code/device context.
   - Entry placement needs screenshot verification.
   - Right panel remains available.
2. Preview running
   - User sees: Main output shows preview render state and linked source files.
   - Preview is tied to current workspace.
   - Logs remain docked.
3. Select screen state
   - User sees: User selects a screen/state and sees the preview refresh.
   - State selector stays near output.
4. Export snapshot
   - User sees: Preview can export or reference a snapshot for review.
   - Export does not publish firmware.

## Branches And Recovery States

### Recovery states

- Preview build failed: Build failure keeps source file links and error summary visible.
   - Retry and open file are available.
- No display target: Missing display target blocks preview with setup guidance.
   - Do not show blank canvas as success.
- Stale preview: When source changes, preview marks itself stale until refreshed.
   - Refresh action visible.

## End State

- Ends at: Stale preview
  - When source changes, preview marks itself stale until refreshed.
   - Refresh action visible.

## Notes

- User outcome: LVGL preview is treated as a workspace tool, not a detached gallery.
- Constraints: Actual preview canvas is not final without screenshots.
- Evidence gaps: Need screenshot for preview pane, target selector, and failure states.
