# Journey 20 - Scheduled Tasks Cron Jobs

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [20-scheduled-tasks-cron-jobs.wireframe.html](20-scheduled-tasks-cron-jobs.wireframe.html)
- Status: Status: future/unverified
- Verification note: No reliable current screenshot found; do not treat this as existing UI.

## User Goal

Scheduled tasks are not confirmed in current screenshots; this artifact records the proposed interaction contract as a future or unverified journey.

## Entry And Preconditions

- Entry: Unverified entry
- Initial user state: Scheduled tasks need a product decision or screenshot before being drawn as current UI.
   - Do not place it in Settings or Code without evidence.
   - Ask for screenshot before implementation handoff.

## Interaction Flow

1. Unverified entry
   - User sees: Scheduled tasks need a product decision or screenshot before being drawn as current UI.
   - Do not place it in Settings or Code without evidence.
   - Ask for screenshot before implementation handoff.
2. Task list concept
   - User sees: If approved, list should show schedule, next run, last result, and enabled state.
   - Future contract only.
3. Task detail concept
   - User sees: Detail should include command, workspace, permissions, dry run, and history.
   - Requires new design direction.
4. Run result concept
   - User sees: Run result should keep logs, retry, and disable unsafe edits while running.
   - Contract only until screenshot exists.

## Branches And Recovery States

### Risk states

- Missing permission: Task cannot run if workspace or provider permissions are missing.
   - Show disabled action plus reason.
- Overlapping run: A second scheduled run should not silently overlap the first.
   - Show skip, queue, or stop policy.
- Schedule invalid: Invalid cron expression blocks save with inline error.
   - No background failure after save.

## End State

- Ends at: Schedule invalid
  - Invalid cron expression blocks save with inline error.
   - No background failure after save.

## Notes

- User outcome: This is not a current UI wireframe; it is an unverified future contract placeholder.
- Constraints: Needs product/design input before any concrete shell placement.
- Evidence gaps: Need screenshot, product decision, or explicit new-design direction.
