# Journey 08 - Publish Agent Review Edit Catalog Info

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [08-publish-agent-review-edit-catalog-info.wireframe.html](08-publish-agent-review-edit-catalog-info.wireframe.html)
- Status: Status: current-shell reference
- Verification note: Based on agent-publish/8-dialog-metadata-new-version.jpg and related publish screenshots.

## User Goal

Publishing opens the current Publish to Catalog modal over Agent authoring, showing sign-in, package metadata, version mode, and publish action.

## Entry And Preconditions

- Entry: Click Publish
- Initial user state: User clicks Publish from the Agent authoring toolbar.
   - Background remains Agent authoring.
   - Modal is centered.

## Interaction Flow

1. Click Publish
   - User sees: User clicks Publish from the Agent authoring toolbar.
   - Background remains Agent authoring.
   - Modal is centered.
2. Review metadata
   - User sees: Modal shows Agent ID, version, SHA, package size, and publish mode.
   - Metadata is reviewable before send.
   - Cancel remains available.
3. Publish new version
   - User sees: User confirms a new version publish from the modal.
   - Primary button is explicit.
   - Mode is visible.
4. Publish success
   - User sees: Success state keeps catalog information and close/open actions.
   - User can return to editor.
   - Catalog entry can be opened.

## Branches And Recovery States

### Recovery states

- Unauthenticated: If not signed in, the modal asks the user to sign in before publish.
   - No hidden publish attempt.
- Version conflict: A version conflict shows the existing version and offers edit/retry.
   - Metadata remains visible.
- Validation failed: Invalid catalog info is shown inside the modal before publishing.
   - Primary publish action disabled.

## End State

- Ends at: Validation failed
  - Invalid catalog info is shown inside the modal before publishing.
   - Primary publish action disabled.

## Notes

- User outcome: Publish review happens in-place over the real Agent editor.
- Constraints: Catalog fields follow available publish screenshots.
- Evidence gaps: Need final screenshot for success and version conflict if copy changes.
