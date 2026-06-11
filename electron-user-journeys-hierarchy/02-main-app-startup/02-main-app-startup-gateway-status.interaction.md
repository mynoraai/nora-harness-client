# Journey 02 - Main App Startup And Gateway Status

This file is the text interaction description for the corresponding HTML wireframe. It describes how the user enters the journey, progresses through it, handles branches, and reaches the end state.

- Source HTML: [02-main-app-startup-gateway-status.wireframe.html](02-main-app-startup-gateway-status.wireframe.html)
- Status: needs verification
- Verification note: Verify startup priority for health, restored mode, and offline recovery.

## User Goal

Returning users should understand app readiness, connection health, and saved mode without being blocked from work.

## Entry And Preconditions

- Entry: Launch shell
- Initial user state: App opens directly to the last useful mode with sessions visible while checks start.
  - No marketing or splash page.
  - Recent sessions appear before checks finish.

## Interaction Flow

1. Launch shell
   - User sees: App opens directly to the last useful mode with sessions visible while checks start.
   - No marketing or splash page.
   - Recent sessions appear before checks finish.
2. Gateway health
   - User sees: A compact health banner reports starting, ready, degraded, or offline without blocking navigation.
   - Health state is visible without blocking navigation.
   - Retry and diagnostics are visible.
3. Mode restore
   - User sees: Saved mode and selected workspace return with clear fallbacks when context is missing.
   - Chat or Code state is explicit.
   - Missing workspace is recoverable.

## Branches And Recovery States

### Detail states

- Returning sessions: Recent conversations and code sessions are visible while background checks run.
  - Skeleton rows avoid blank sidebar.
  - Content becomes interactive as it loads.
- Offline work: When local service is unavailable, local navigation remains usable and disabled actions explain why.
  - Read-only work stays available.
  - Send is disabled with a reason.

### Error recovery

- Service degraded: Users can retry, open diagnostics, or continue with limited features.
  - Severity label is clear.
  - Limited mode is explicit.


## Verification Checklist

- This journey has no dedicated verification cards; verify the interaction flow and branch states.

## End State

- Ends at: Mode restore
  - Saved mode and selected workspace return with clear fallbacks when context is missing.
  - Chat or Code state is explicit.
  - Missing workspace is recoverable.

## Notes

- User outcome: Startup feels dependable while health checks are still running. Users can see recent sessions and continue available work.
- Constraints: More visible health states require careful copy. Startup should not show competing prompts.
- Evidence gaps: Verify which actions remain available offline or degraded, and how disabled actions explain why.
