# Journey 07 - Agent Authoring

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [07-agent-authoring.wireframe.html](07-agent-authoring.wireframe.html)
- Status: Status: current-shell reference
- Verification note: Based on agent-authoring-soul-editor.jpg.

## User Goal

Agent authoring uses the current Code shell with Agent tab, Soul/Style/Tools navigation, editor actions, and right-side inspection panels.

## Entry And Preconditions

- Entry: Open Edit Agent
- Initial user state: User enters Agent authoring from Code and sees the Agent surface.
   - Top right Edit Agent entry leads here.
   - Workspace shell remains.

## Interaction Flow

1. Open Edit Agent
   - User sees: User enters Agent authoring from Code and sees the Agent surface.
   - Top right Edit Agent entry leads here.
   - Workspace shell remains.
2. Edit Soul
   - User sees: Soul tab shows agent/SOUL.md with rich text controls and Save/Revert.
   - Soul tab is selected.
   - Personality panel stays visible.
3. Switch tab
   - User sees: Switching Style or Tools changes the editor body, not the whole shell.
   - Tabs stay inside the authoring surface.
   - Unsaved state is visible.
4. Save draft
   - User sees: Saving leaves the user in authoring with Preview and Publish still available.
   - No navigation jump.
   - Status confirms saved.

## Branches And Recovery States

### Recovery states

- Unsaved changes: Leaving with edits shows a confirm state in the authoring shell.
   - Save, discard, and cancel are explicit.
- Invalid content: Invalid agent metadata is shown near the edited field.
   - Publish remains disabled until fixed.
- Revert changes: Revert restores the last saved version without leaving the editor.
   - User can review before saving again.

## End State

- Ends at: Revert changes
  - Revert restores the last saved version without leaving the editor.
   - User can review before saving again.

## Notes

- User outcome: Agent editing feels like part of the Code workspace, not a separate generic form.
- Constraints: Only visible tabs from the screenshot are used.
- Evidence gaps: Need screenshots for each non-Soul tab before finalizing their exact fields.
