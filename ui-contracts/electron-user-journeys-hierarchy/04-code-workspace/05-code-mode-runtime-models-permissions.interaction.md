# Journey 05 - Code Mode Runtime Models Permissions

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [05-code-mode-runtime-models-permissions.wireframe.html](05-code-mode-runtime-models-permissions.wireframe.html)
- Status: Status: current-shell reference
- Verification note: Based on Code shell screenshot; permission-specific UI needs current screenshot verification.

## User Goal

Code requests use the current Code shell to surface model readiness, runtime work, permissions, and stop/retry controls without hiding workspace context.

## Entry And Preconditions

- Entry: Model ready
- Initial user state: Path bar and composer show the selected model before the request starts.
   - Model pill stays visible.
   - Workspace remains selected.

## Interaction Flow

1. Model ready
   - User sees: Path bar and composer show the selected model before the request starts.
   - Model pill stays visible.
   - Workspace remains selected.
2. Runtime starts
   - User sees: The Code output tab shows thinking rows and a running tool card.
   - Stop is available while running.
   - Right files panel remains usable.
3. Permission request
   - User sees: A permission card appears in the output stream before a risky action proceeds.
   - Approve and deny are near the request.
   - Composer does not hide the permission.
4. Continue after approval
   - User sees: Approved work continues in the same session output.
   - Approval is recorded.
   - Tool result is visible.

## Branches And Recovery States

### Recovery states

- Permission denied: Denied action leaves a readable explanation and a safer alternative.
   - No silent failure.
   - Retry can ask for a different command.
- Runtime unavailable: If runtime is unavailable, output explains the missing dependency and keeps the workspace open.
   - Composer can be disabled.
   - Retry remains visible.
- Stop running task: User can stop a long-running operation without closing the session.
   - Partial output remains.
   - Terminal context stays docked.

## End State

- Ends at: Stop running task
  - User can stop a long-running operation without closing the session.
   - Partial output remains.
   - Terminal context stays docked.

## Notes

- User outcome: Runtime, model, and permission state are reviewed inside the same Code workspace.
- Constraints: Permission card layout is inferred from the current output-stream pattern.
- Evidence gaps: Need exact screenshots for allow/deny UI and long-running stop state.
