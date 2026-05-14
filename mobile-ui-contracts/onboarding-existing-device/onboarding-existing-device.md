# MOB-03 Existing-device Onboarding

This document defines the existing-device onboarding journey for HH Mobile Chat. It covers reconnecting through an existing agent, reinstalling or reusing runtime state, and returning to the main app after runtime preparation completes.

## User Journey

### 1. User chooses the existing-device path

When BLE is unavailable or the user already has a paired device, they enter this flow from "Existing device?". The first useful screen is an agent list, because the user is reconnecting through an existing agent instead of pairing new hardware.

<img src="../assets/6-onboarding-existing-device/1-onboarding-existing-device-list-agents.png" alt="Existing-device agent list" width="320" />

If the user refreshes the list, the page should show a loading state while keeping the journey context clear: they are still choosing an existing agent.

<img src="../assets/6-onboarding-existing-device/1-1onboarding-existing-device-loading(refresh)-agents.png" alt="Refreshing existing-device agents" width="320" />

The bottom controls finish the list step. Continue should require a selected agent; refresh should restart list loading and clear any list-load error.

<img src="../assets/6-onboarding-existing-device/1-2onboarding-existing-device-agents-list-bottom.png" alt="Existing-device list bottom controls" width="320" />

### 2. User selects an agent to reconnect

After tapping an agent, the selected row is highlighted. This selected agent becomes the target for the reinstall/runtime preparation step.

<img src="../assets/6-onboarding-existing-device/2-onboarding-existing-device-selected-agent.png" alt="Selected existing-device agent" width="320" />

If loading the agent list fails, the error should stay scoped to the list. The user can retry refresh without losing the broader existing-device journey.

<img src="../assets/6-onboarding-existing-device/2-1loading-failed.png" alt="Existing-device loading failed" width="320" />

If reinstall fails after the user continues, the error belongs to the selected agent attempt. When the user goes back to the list, both the selected agent and this error should be cleared so the next choice starts clean.

<img src="../assets/6-onboarding-existing-device/2-2onboarding-existing-device-reinstall-error.png" alt="Existing-device reinstall error" width="320" />

### 3. App reinstalls or reuses the runtime

Once the user continues with a selected agent, the app prepares the runtime. The user should see a loading screen until the reinstall/reuse path has a ready endpoint.

<img src="../assets/6-onboarding-existing-device/3-onboarding-existing-device-reinstalling-agent-.png" alt="Existing-device reinstalling agent" width="320" />

## Control Contract

| Control                   | Required behavior                                                                                       |
| ------------------------- | ------------------------------------------------------------------------------------------------------- |
| Agent card                | Toggles the current selected agent. Only one agent can be selected.                                     |
| Continue                  | Disabled or ineffective until an agent is selected. Starts reinstall/runtime preparation once selected. |
| Refresh agents            | Clears list-load error state and reloads agents.                                                        |
| Back from reinstall/error | Returns to the agent list and clears both selected agent and reinstall error state.                     |
| Retry reinstall           | Restarts the runtime preparation for the current selected agent.                                        |

## State Contract

| State             | Required UI                                        | Exit condition                                             |
| ----------------- | -------------------------------------------------- | ---------------------------------------------------------- |
| Listing agents    | Agent cards plus continue/refresh controls.        | Agent is selected or refresh starts.                       |
| Refreshing agents | Loading indicator over the list state.             | Agent fetch succeeds or fails.                             |
| Agent load failed | Recoverable error message and refresh action.      | Refresh starts.                                            |
| Selected agent    | Highlighted selected card and enabled continue.    | Continue starts reinstall, or back/reset clears selection. |
| Reinstalling      | Loading surface for reinstall/runtime preparation. | Runtime preparation succeeds or fails.                     |
| Reinstall failed  | Error banner with retry/back path.                 | Retry starts or back clears state.                         |

## Notes

- This flow is the required fallback when BLE is unavailable in simulator or hardware discovery cannot be used.
- Error and selection are transient state. They should not survive a deliberate back navigation to the agent list after a failed install attempt.
