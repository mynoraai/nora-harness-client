# Journey 12 - Device Status PlatformIO Device Logs

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [12-device-status-platformio-device-logs.wireframe.html](12-device-status-platformio-device-logs.wireframe.html)
- Status: Status: current-shell reference
- Verification note: Uses code-workspace-runtime-layout.jpg right panel; device detail needs current screenshot confirmation.

## User Goal

Hardware device status is reviewed inside the Code workspace right Device panel and bottom Device Log dock.

## Entry And Preconditions

- Entry: Open Device tab
- Initial user state: User opens Device in the right panel beside the code session.
   - Device tab is not a separate page.
   - Code output remains central.

## Interaction Flow

1. Open Device tab
   - User sees: User opens Device in the right panel beside the code session.
   - Device tab is not a separate page.
   - Code output remains central.
2. Review status
   - User sees: Panel shows connected board, PlatformIO readiness, and current log stream.
   - Connection state is visible.
   - Actions are scoped to selected workspace.
3. Stream logs
   - User sees: Bottom Device Log stays docked while logs stream.
   - Logs do not replace the main output.
4. Run device action
   - User sees: Flash/build/test action reports progress in the session output.
   - Progress and terminal context stay visible.

## Branches And Recovery States

### Recovery states

- Device disconnected: Disconnected state disables flash/read actions with reason.
   - Reconnect guidance is inline.
- PlatformIO missing: Missing PlatformIO is shown in Device panel and output stream.
   - Install/fix action is explicit.
- Log paused: User can pause/resume logs without losing session output.
   - Pause state belongs to Device Log.

## End State

- Ends at: Log paused
  - User can pause/resume logs without losing session output.
   - Pause state belongs to Device Log.

## Notes

- User outcome: Device work stays adjacent to the code session.
- Constraints: Device row details are current-shell approximations.
- Evidence gaps: Need screenshot for exact connected/disconnected PlatformIO controls.
