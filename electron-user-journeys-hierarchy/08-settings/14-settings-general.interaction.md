# Journey 14 - Settings General

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [14-settings-general.wireframe.html](14-settings-general.wireframe.html)
- Status: Status: current screenshot reference
- Verification note: Based on settings-general-gateway-network.jpg.

## User Goal

General settings use the current Settings shell with Back to app, left navigation, gateway status, network proxy, feature toggles, and thinking level.

## Entry And Preconditions

- Entry: Open Settings
- Initial user state: User opens Settings and lands on General.
   - Left nav highlights General.
   - Back to app remains visible.

## Interaction Flow

1. Open Settings
   - User sees: User opens Settings and lands on General.
   - Left nav highlights General.
   - Back to app remains visible.
2. Review gateway
   - User sees: Gateway card shows running state and restart action.
   - Status is not hidden in onboarding.
3. Change proxy
   - User sees: User edits proxy without leaving General settings.
   - Input state is local to network section.
4. Set thinking level
   - User sees: Default Thinking Level dropdown is changed and saved.
   - Dropdown follows current Settings layout.

## Branches And Recovery States

### Recovery states

- Gateway stopped: Gateway status card shows stopped state and restart.
   - Settings remains usable.
- Invalid proxy: Proxy input shows validation error before save.
   - Bad value is not silently accepted.
- Restart pending: Restart action shows pending state while keeping navigation available.
   - No full-screen blocker.

## End State

- Ends at: Restart pending
  - Restart action shows pending state while keeping navigation available.
   - No full-screen blocker.

## Notes

- User outcome: General settings match the current screenshot structure.
- Constraints: Exact copy may change with product localization.
- Evidence gaps: Need screenshots for stopped/restarting gateway states.
