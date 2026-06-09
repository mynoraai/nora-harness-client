# Journey 19 - Settings About And Updates

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [19-settings-about-and-updates.wireframe.html](19-settings-about-and-updates.wireframe.html)
- Status: Status: current-shell reference
- Verification note: Uses Settings shell; update-specific tab needs screenshot confirmation.

## User Goal

About/update information should live in the Settings shell, separate from Journey 02 because current startup UI does not show an update panel.

## Entry And Preconditions

- Entry: Open About
- Initial user state: User selects About in Settings.
   - Startup journey does not include update prompt.
   - About is settings content.

## Interaction Flow

1. Open About
   - User sees: User selects About in Settings.
   - Startup journey does not include update prompt.
   - About is settings content.
2. Check for updates
   - User sees: User checks for updates from About/settings.
   - Action is user-initiated.
   - No startup prompt is drawn here.
3. Update available
   - User sees: Settings shows available version and release details.
   - Install action is explicit.
   - Later action remains available.
4. Restart to finish
   - User sees: After install, Settings offers restart to finish.
   - Restart is not mixed into startup journey.

## Branches And Recovery States

### Recovery states

- Already current: Check result says the app is up to date.
   - No install action shown.
- Check failed: Network failure keeps retry and proxy settings available.
   - Connects to General network settings.
- Install failed: Failed install shows retry and keeps current version usable.
   - No forced loop.

## End State

- Ends at: Install failed
  - Failed install shows retry and keeps current version usable.
   - No forced loop.

## Notes

- User outcome: Update status belongs in Settings, not startup Journey 02.
- Constraints: Specific update UI is pending screenshot.
- Evidence gaps: Need screenshot for About/Updates tab or implementation reference.
