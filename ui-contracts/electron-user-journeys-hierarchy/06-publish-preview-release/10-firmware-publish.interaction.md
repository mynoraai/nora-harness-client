# Journey 10 - Firmware Publish

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [10-firmware-publish.wireframe.html](10-firmware-publish.wireframe.html)
- Status: Status: current-shell reference
- Verification note: No dedicated firmware publish screenshot found; this wireframe uses the Agent publish modal pattern as a current-shell reference.

## User Goal

Firmware publishing should stay anchored in the existing Code or Agent shell, with package review, build state, and publish result visible before release.

## Entry And Preconditions

- Entry: Open firmware publish
- Initial user state: User starts publish from the current authoring/release surface.
   - Uses existing toolbar/modal pattern.
   - Needs real entry screenshot.

## Interaction Flow

1. Open firmware publish
   - User sees: User starts publish from the current authoring/release surface.
   - Uses existing toolbar/modal pattern.
   - Needs real entry screenshot.
2. Review build artifact
   - User sees: Modal shows target board, version, artifact hash, and build size.
   - Artifact metadata visible before publish.
   - Cancel remains available.
3. Publishing
   - User sees: Publish button switches to progress and keeps the modal open.
   - No background navigation.
   - Errors return into the same modal.
4. Published artifact
   - User sees: Success state exposes artifact URL and close/back actions.
   - Release metadata remains copyable.

## Branches And Recovery States

### Recovery states

- Build failed: Build failure shows target, error summary, and retry.
   - Failure is visible before publish.
- Missing target: Missing board or firmware target disables publish with a reason.
   - Select target is the fix.
- Version already exists: Existing version conflict offers edit version or publish as new build.
   - No overwrite by default.

## End State

- Ends at: Version already exists
  - Existing version conflict offers edit version or publish as new build.
   - No overwrite by default.

## Notes

- User outcome: Firmware publish is framed as a product contract, not a final UI because screenshot evidence is missing.
- Constraints: Uses current publish-modal language and density.
- Evidence gaps: Need actual firmware release screen before implementation handoff.
