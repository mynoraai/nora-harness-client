# Journey 09 - Mobile Preview (Document Hidden)

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It describes entry, visible states, completion, and recovery.

- Source HTML: [09-mobile-preview.wireframe.html](09-mobile-preview.wireframe.html)
- Status: hidden from the active journey map for now
- Scope note: this is documentation hiding only; related product code does not need to be deleted.
- Previous status: current-shell reference
- Verification note: Current screenshots only show the Preview on Mobile entry; preview detail needs screenshot confirmation.

## User Goal

Mobile preview starts from Agent authoring and opens a preview overlay while the editor context remains visible behind it.

## Entry And Preconditions

- Entry: Click Preview on Mobile
- Initial user state: User clicks Preview on Mobile from the Agent toolbar.
   - Entry belongs in Agent authoring toolbar.
   - Editor context remains visible behind overlay.

## Interaction Flow

1. Click Preview on Mobile
   - User sees: User clicks Preview on Mobile from the Agent toolbar.
   - Entry belongs in Agent authoring toolbar.
   - Editor context remains visible behind overlay.
2. Preview overlay
   - User sees: Overlay shows connection info and a phone-sized preview.
   - This is a proposed placement pending screenshot.
   - Phone frame is separate from desktop shell.
3. Refresh preview
   - User sees: User refreshes the preview after saving edits.
   - Refresh does not close authoring.
   - Copy link remains visible.
4. Return to editor
   - User sees: Closing overlay returns to the same authoring tab.
   - No editor state loss.

## Branches And Recovery States

### Recovery states

- Preview not available: If the preview service is unavailable, the overlay explains retry steps.
   - Do not invent a separate preview app.
- Unsaved changes: Preview warns when local edits are not saved.
   - Save first remains the primary fix.
- Mobile disconnected: Disconnected mobile device keeps QR/copy link visible for reconnect.
   - Reconnect state stays in overlay.

## End State

- Ends at: Mobile disconnected
  - Disconnected mobile device keeps QR/copy link visible for reconnect.
   - Reconnect state stays in overlay.

## Notes

- User outcome: Preview is attached to Agent authoring rather than drawn as a standalone mobile flow.
- Constraints: Phone preview content is explicitly marked as needing screenshot verification.
- Evidence gaps: Need actual preview overlay/mobile screenshot.
