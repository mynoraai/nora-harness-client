# Journey 03 - Chat Mode Conversation

This file is the text interaction description for the corresponding HTML wireframe. This version is grounded in `chat-empty-composer.jpg` and `chat-model-picker.jpg`.

- Source HTML: [03-chat-mode-conversation.wireframe.html](03-chat-mode-conversation.wireframe.html)
- Status: current screenshot reference
- Verification note: Keep the Chat wireframe aligned with the current screenshots instead of redesigning the Chat surface.

## User Goal

Users open a real Chat shell, confirm or change the model, write a prompt, and recover from response failure without leaving the conversation.

## Entry And Preconditions

- Entry: Empty conversation
- Initial user state: The `heartbeat` conversation is selected, the main pane shows `Start a focused conversation`, and the bottom composer is available.

## Interaction Flow

1. Empty conversation
   - User sees: The selected heartbeat conversation opens to the current empty Chat state.
   - Left sidebar uses Today/Yesterday groups.
   - Composer is anchored at the bottom.
2. Open model picker
   - User sees: Clicking the model pill dims the app and opens the centered model search menu.
   - Background remains visible behind overlay.
   - Selected MiniMax-M2.7 keeps a check mark.
3. Write prompt
   - User sees: User types in the bottom composer while the selected model and effort remain visible.
   - Send affordance becomes primary.
   - Prompt stays in the same conversation.
4. Streaming response
   - User sees: After send, the conversation area fills with a user turn and an assistant response with stop available.
   - Composer stays anchored.
   - Stop/retry area stays close to the turn.

## Branches And Recovery States

### Detail states

- Search conversations: The search field filters the same sidebar without changing the main Chat shell.
  - Current selection remains visible.
- Switch Chat / Code: The segmented control shows Chat selected and Code available without leaving the window shell.
  - No separate marketing or mode page.
- Model search: Typing in the model menu filters providers and models inside the overlay.
  - Escape or close returns to the composer.

### Recovery states

- No model selected: Composer keeps the model pill location but explains the missing model before send.
  - Send is disabled until model selection.
- Failed response: A failed assistant turn appears inline and can be retried without losing the draft or history.
  - Conversation context is preserved.

## End State

- Ends at: The same `heartbeat` Chat conversation after streaming or inline failure recovery.
- The user does not leave the current Chat shell; history and draft context remain in place.

## Notes

- User outcome: Users start from the real Chat shell, select or confirm a model, write a prompt, and keep recovery controls in the same conversation.
- Constraints: Only states visible in or directly implied by the current screenshots are drawn; the wireframe avoids redesigning the Chat surface.
- Evidence gaps: Need a current screenshot for an actual streamed answer/error turn before finalizing message-row details.
