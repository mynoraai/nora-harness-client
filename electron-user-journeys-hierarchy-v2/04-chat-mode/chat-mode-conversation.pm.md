# Journey 04: Chat Mode Record

## Journey Context

### Current Work

This artifact turns Journey 04 from `user-journey-inventory.md` into a companion interaction record. It documents the implemented NoraHarness Electron journey from the user's entry point through visible client state changes and the final resting state.

### User Entry

The user enters the default Chat mode in the main app, or switches back to Chat from the sidebar mode switcher.

### User Behavior

The user views the conversation list, searches or creates a conversation, types in the composer, attaches content or commands, selects runtime/model/thinking, sends a message, and handles stop, permission requests, tool execution, and recovery.

### Client State Changes

- The sidebar shows the Chat conversation list, search box, and new conversation entry; when no conversation exists, the main area shows an empty state and composer.
- After the user selects a conversation, the main area loads its messages; older messages can continue loading in pages.
- After the user types content, the send button becomes available; after send, the user message is appended immediately and the composer enters running constraints.
- While running, the UI shows assistant pending/streaming state, stop button, tool blocks, permission request cards, and possible file/command results.
- If the runtime requires user approval, the message stream stops at approval state; after approval or denial, the client continues running or shows the denied end state.
- If the user stops the run, the current assistant turn ends as stopped/cancelled; if the run fails, the stream shows an error card with retry or regenerate.
- On success, the assistant message moves from streaming to finalized, the composer becomes editable again, and the title, timestamp, and sidebar summary refresh.

### End State

The conversation rests after a completed, stopped, or failed turn. The user can continue sending, retry, switch conversations, or leave Chat mode.

### Implementation Maturity

`implemented`

- Current user-visible basis: this record follows the Journey 04 section in `user-journey-inventory.md`.
- Assumptions: none added in this companion document. Visual references are only used to match layout, controls, density, and placement.
- Dependencies: only the dependencies already implied by the journey text are recorded; this document does not introduce new product behavior.

### Documentation Goal

Capture Journey 04 as an implemented user journey so the HTML wireframe can be reviewed against a stable text source before visual details are adjusted.

### Documentation Focus

Record what the user does, what changes in the client UI, and where the journey ends. The HTML wireframe should use this document for state and recovery content.

### Scope Boundaries

- Included: the visible entry point, user behavior, state changes, recovery states, and end state described for Journey 04.
- Excluded: unrelated journeys, future product proposals, and implementation details.

### Non-Goals

- Do not add decision callouts to this journey artifact.
- Do not infer product states from visual references when they are not present in the journey text.
- Do not redesign the flow beyond documenting the implemented behavior.
- Do not expose code paths, components, IPC details, or internal implementation names.

### Recorded Flow

1. Entry: The user enters the default Chat mode in the main app, or switches back to Chat from the sidebar mode switcher.
2. User action: The user views the conversation list, searches or creates a conversation, types in the composer, attaches content or commands, selects runtime/model/thinking, sends a message, and handles stop, permission requests, tool execution, and recovery.
3. Visible state changes: the client moves through the states listed above.
4. End: The conversation rests after a completed, stopped, or failed turn. The user can continue sending, retry, switch conversations, or leave Chat mode.

### Wireframe Source Rule

The HTML wireframe for this journey should derive state, language, and error/recovery frames from this document. Visual references may be used only to match visible UI structure, density, controls, and placement.

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes-v2/electron-user-journeys-hierarchy-v2/04-chat-mode/chat-mode-conversation.wireframe.html)

File: `electron-user-journeys-hierarchy-v2/04-chat-mode/chat-mode-conversation.wireframe.html`
