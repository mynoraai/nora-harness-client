# Journey 4: Chat Mode Conversation

This Markdown file is the text counterpart to `chat-mode-conversation.wireframe.html`. It documents the current implemented Chat journey in user-facing terms. Each section ID matches a state that the wireframe can render.

The journey starts when the user enters Chat mode from the normal app frame. The user can find or create a conversation, prepare the composer, choose model and thinking options, send a message, watch the assistant stream, respond to in-conversation approval requests, stop a run, recover from errors, and continue in the same Chat surface.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches and supporting states use decimal IDs scoped to the step where the state starts. Sequential follow-up states use a deeper decimal level.

## State Inventory

Main path states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 4 | Chat mode entry from the normal app frame. | Opens the app in Chat or switches from Code to Chat. | Chat is selected in the mode switcher; the sidebar and main Chat surface are visible. | Chat mode becomes the active surface and prepares the selected or draft conversation. | `2` |
| `2` | Main path | Journey 4 | Conversation list is ready for selection or creation. | Reviews the list, searches, creates a conversation, or selects a row. | Sidebar shows search, New conversation, date groups, active row, Settings, and account entry. | Conversation summaries are loaded, filtered, and sorted for Chat mode. | `3` |
| `3` | Main path | Journey 4 | Selected conversation surface loads. | Waits for history, reviews an empty state, or reads existing messages. | Header shows the conversation title and model state; the message area shows loading, empty hero, suggestions, or history. | The selected conversation becomes active and its visible transcript is prepared. | `4` |
| `4` | Main path | Journey 4 | Composer is ready for a valid prompt. | Types, attaches content, selects a slash command, changes model, or changes thinking level. | Bottom composer shows textarea, attachment button, selected model, thinking level, and disabled or enabled send action. | Draft text, attachments, model choice, and thinking level are kept with the current conversation. | `5` |
| `5` | Main path | Journey 4 | Send is accepted. | Clicks send or submits with the keyboard. | The user message appears in the conversation immediately, and duplicate send is blocked. | A draft conversation is saved when needed and the assistant turn starts. | `6` |
| `6` | Main path | Journey 4 | Assistant turn is running. | Watches the stream, expands details, responds to approval, stops, or waits. | Assistant text, reasoning, tool cards, permission cards, command results, and stop control can appear inside the conversation. | Incoming run updates are reduced into visible message parts until completion, stop, denial, or failure. | `7` |
| `7` | Main path | Journey 4 | Conversation returns to a stable resting state. | Continues the conversation, retries, switches conversations, opens Settings, or leaves Chat. | Composer is editable again, the sidebar summary and timestamp can refresh, and the latest turn is complete, stopped, denied, or failed. | The conversation rests with the latest known transcript and recoverable state. | End |

Session, search, and new-conversation states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Branch journey | `2 Conversation List` | Search filters conversation rows. | Types into Search conversations or clears the query. | The same sidebar narrows or shows all visible rows again without leaving Chat. | The visible list follows the query while the current conversation remains selected until another row is chosen. | `3` |
| `2.2` | Branch journey | `2 Conversation List` | New conversation starts a draft. | Clicks New conversation in the sidebar or header. | A draft conversation becomes active; the main area can show the focused-conversation empty hero and suggestions. | A local draft is selected before the first send creates the saved conversation. | `3.3` |
| `2.3` | Branch journey | `2 Conversation List` | Row menu changes an existing conversation. | Opens a row menu, renames, cancels delete, or confirms delete. | The row shows rename or delete confirmation in place; confirmed deletion removes the row. | The conversation list updates optimistically and active selection moves to an available conversation or draft-ready state. | `2` |

Conversation surface states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Branch journey | `3 Selected Conversation Surface` | Conversation history is loading. | Waits or reopens the conversation after a retry. | The message area shows loading before history, active run recovery, or empty state settles. | Recent messages and any unfinished turn are checked for the selected conversation. | `3` |
| `3.2` | Branch journey | `3 Selected Conversation Surface` | Older messages load while scrolling history. | Scrolls to the top when more history is available. | Older-history loading appears above the message list; existing messages stay visible. | Older messages are prepended and pagination state advances. | `4` |
| `3.3` | Branch journey | `3 Selected Conversation Surface` | Empty conversation with suggestions. | Reads the empty hero or clicks a suggestion chip. | The main area shows `Start a focused conversation`, helper copy, suggestion chips, and the bottom composer. | A suggestion click fills the normal send path for the active draft or conversation. | `4` |
| `3.4` | Detail state | `3 Selected Conversation Surface` | Header and collapsed-sidebar controls. | Opens the sidebar, starts a new conversation, or reads model state. | Header shows the title fallback, sidebar-open control, New conversation, selected model, or unavailable-model badge. | Header state follows the active conversation and selected model. | `4` |

Composer, send, and runtime states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.1` | Branch journey | `4 Composer Ready` | Attachment menu and chips. | Adds or removes attachments before sending. | Attachment chips appear in the composer next to the draft. | Attachment metadata is held with the draft and included on send. | `5` |
| `4.2` | Branch journey | `4 Composer Ready` | Slash command menu. | Types a slash query, navigates command choices, or selects a command. | A command listbox appears near the composer; selected command text enters the draft. | Command text is treated as the prompt, and configured command output can later appear as a command result card. | `5` |
| `4.3` | Branch journey | `4 Composer Ready` | Model picker opens. | Clicks the model pill, Retry, unavailable-model badge, or no-model prompt. | The app dims behind a centered `Search models...` picker with provider groups and selectable rows. | Model inventory state is shown and the current conversation remains active behind the picker. | `4.3.1` |
| `4.3.1` | Branch follow-up | `4.3 Model Picker` | Model search and selection. | Searches models or selects a row. | Matching rows remain visible; the selected model has a check mark; closing returns to the composer. | The chosen model updates the active draft or saved conversation. | `4` |
| `4.4` | Branch journey | `4 Composer Ready` | Thinking selector changes. | Chooses a thinking level such as medium or another available option. | The thinking control shows the selected level and is disabled while a run is streaming. | The selected level is held for later sends in the current conversation. | `4` |
| `4.5` | Branch journey | `4 Composer Ready` | Empty draft blocks send. | Clicks send with no text or attachment. | Send remains unavailable or no turn starts; the composer stays editable. | No user message or assistant run is created. | `4` |
| `4.6` | Branch journey | `4 Composer Ready` | No model selected blocks send. | Attempts to send without an enabled model. | The model area explains the missing model and the draft remains editable. | The run is not started until the user selects a model. | `4.3` |
| `4.7` | Branch journey | `4 Composer Ready` | Selected model is unavailable. | Opens the model control or retries model loading. | The header or composer shows unavailable-model state, no-model state, loading, or Retry. | Model readiness is refreshed or waits for the user to choose an available model. | `4.3` |
| `4.8` | Branch journey | `4 Composer Ready` | Runtime unavailable banner blocks submit. | Reads the banner, opens Settings, or waits for recovery. | A runtime-availability banner or disabled send state explains why the assistant cannot start. | The current draft is preserved while runtime readiness is unresolved. | `4` |
| `5.1` | Branch follow-up | `5 Send Accepted` | Draft conversation becomes saved on first send. | Sends from a new draft conversation. | The user message remains visible while the conversation title and sidebar row settle. | The draft is converted into a saved conversation before the assistant stream continues. | `6` |

Approval, tool, and message-part states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `6.1` | Branch journey | `6 Assistant Running` | Pending and streaming assistant response. | Waits or clicks Stop. | Assistant text can appear progressively; a stop control replaces normal send. | The turn is submitted, then streaming, until a terminal state arrives. | `6.14` or running branch |
| `6.2` | Detail state | `6 Assistant Running` | Reasoning disclosure. | Expands or collapses reasoning. | A reasoning block appears above the answer and can be opened or collapsed. | Reasoning visibility changes locally while the run continues or after it finishes. | `6.1` |
| `6.3` | Branch journey | `6 Assistant Running` | Native tool card starts or updates. | Inspects tool parameters, progress, or grouped tool cards. | Tool cards appear in the assistant message and can show parameters, progress, file, diff, or terminal-shaped output. | Tool updates attach to the same assistant turn as the run progresses. | `6.3.1` or `6.3.2` |
| `6.3.1` | Branch follow-up | `6.3 Native Tool Card` | Native tool completes. | Reviews final tool output. | The same tool card shows final result content and no longer looks in progress. | Tool state becomes complete and the assistant turn can continue. | `6.1` |
| `6.3.2` | Branch follow-up | `6.3 Native Tool Card` | Native tool fails. | Reviews the failed tool card. | The tool card changes to an error state with visible failure text. | The tool state becomes failed and the assistant turn either continues with the failure or ends in error. | `6.8` |
| `6.4` | Branch journey | `6 Assistant Running` | Permission request appears inside the conversation. | Reads the request and chooses the `approve` or `deny` command choice. | The message stream pauses at an in-conversation approval card; no separate modal appears. | The run waits for the user's decision before the protected step proceeds or is denied. | `6.4.1` or `6.4.2` |
| `6.4.1` | Branch follow-up | `6.4 Permission Request` | Permission approved. | Chooses `approve`. | The permission card resolves as approved and the assistant stream continues below it. | The protected step is allowed and the run resumes. | `6.1` |
| `6.4.2` | Branch follow-up | `6.4 Permission Request` | Permission denied. | Chooses `deny`. | The permission card resolves as denied, and the turn shows that the protected step did not run. | The protected step is denied and the turn ends or continues with denial context. | `7` |
| `6.5` | Branch journey | `6 Assistant Running` | Command result card appears. | Runs a command-style prompt and reads or dismisses the result. | A dismissible card says `Command result` and indicates it is not saved in normal history. | The command result is shown for the current surface without becoming a normal assistant message. | `7` |
| `6.6` | Detail state | `6 Assistant Running` | External runtime blocks appear in the stream. | Reads status, tool, permission, or modified-file blocks. | Compact blocks appear inside the same message list instead of taking over the page. | Runtime event updates are represented as visible blocks in the current conversation. | `6.1` |

Stop, retry, and error states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.5` | Recovery summary | `3 Selected Conversation Surface` | History load fails. | Clicks Retry or reopens the conversation. | A visible failure message indicates that conversation history could not load. | The selected conversation remains active while history recovery is attempted. | `3.1` |
| `5.2` | Recovery summary | `5 Send Accepted` | Conversation creation fails before the first send. | Retries send or keeps editing. | The draft and composer remain visible with a failure message. | No assistant run starts; the draft remains recoverable. | `4` |
| `6.7` | Branch journey | `6 Assistant Running` | Stop is requested during a run. | Clicks Stop while pending or streaming. | The stop control is active until the turn leaves the running state. | The active assistant turn is asked to stop and duplicate stop is avoided. | `6.7.1` |
| `6.7.1` | Branch follow-up | `6.7 Stop Requested` | Stopped turn rests in the conversation. | Reviews the partial or stopped answer. | The assistant turn ends as stopped or cancelled; the composer becomes editable again. | Running state clears and the conversation can continue. | `7` |
| `6.8` | Branch journey | `6 Assistant Running` | Run error appears inline. | Reads the error, opens details, dismisses, retries, or sends a new prompt. | An inline error banner or failed assistant turn appears without leaving Chat. | The run enters error state and preserves the conversation context. | `6.8.1`, `6.8.2`, `5`, or `7` |
| `6.8.1` | Branch follow-up | `6.8 Run Error` | Error details disclosure opens. | Expands Detail. | Additional failure detail appears under the error surface. | Details visibility changes locally. | `6.8` |
| `6.8.2` | Branch follow-up | `6.8 Run Error` | Error is dismissed. | Clicks Dismiss. | The visible error clears while conversation history remains. | Visible error state is cleared for the current session. | `7` |
| `6.9` | Branch journey | `6 Assistant Running` | Active run reconnect succeeds. | Returns to a conversation that still has an active run. | Buffered text, reasoning, tools, or approval state reappears and live streaming resumes. | Missed updates are replayed and the run continues in place. | `6.1` |
| `6.10` | Branch journey | `6 Assistant Running` | Active run reconnect fails. | Retries, resends manually, or continues from the visible transcript. | The conversation shows recoverable failure instead of a live stream. | The old run cannot be resumed, but the selected conversation remains usable. | `6.8` |
| `6.14` | Detail state | `6 Assistant Running` | Final history refresh after terminal state. | Waits after completion, stop, denial, or error. | The final transcript settles; title, timestamp, and sidebar summary may refresh. | The conversation reconciles the final visible turn with saved history. | `7` |

State-language states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Shared Chat surface labels. | Reads controls and status copy. | Labels include Chat, Code, Search conversations, New conversation, Settings, Log in, Start a focused conversation, and Assign a task or ask any question. | Shared copy keeps Chat mode recognizable across empty, history, and running states. | Any state |
| `4.9` | State language | `4 Composer Ready` | Composer control labels. | Reads or uses composer controls. | Labels include model names such as MiniMax-M2.7, thinking levels such as medium, Search models, Retry, and the send/stop icon state. | Composer copy follows draft validity, model readiness, and running state. | `4`, `5`, or `6` |
| `6.11` | State language | `6 Assistant Running` | Stream, tool, and approval labels. | Reads streaming and tool-state copy. | Labels include pending, streaming, reasoning, Command result, approve, deny, approved, denied, stopped, and completed. | Message-part state is mirrored in visible language. | `6` or `7` |
| `6.12` | State language | `6 Assistant Running` | Error and recovery labels. | Reads error copy or chooses a recovery action. | Labels include failed, Retry, Detail, Dismiss, reconnecting, unavailable, and failed to load chat history. | Error state remains visible until recovery or dismissal. | `3.5`, `4.8`, `5.2`, `6.8`, or `6.10` |

## Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Main completed turn | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | User sends from an existing conversation, receives a completed assistant turn, and can continue chatting. |
| New conversation first send | `1 -> 2 -> 2.2 -> 3.3 -> 4 -> 5 -> 5.1 -> 6 -> 6.1 -> 6.14 -> 7` | User starts from a draft conversation, sends the first prompt, and the conversation becomes saved. |
| Search and select conversation | `1 -> 2 -> 2.1 -> 3 -> 4 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | Search narrows the sidebar before the selected conversation loads and completes a turn. |
| Rename or delete row | `1 -> 2 -> 2.3 -> 2 -> 3` | Row management resolves in the sidebar and returns the user to a selectable conversation state. |
| Older history review | `1 -> 2 -> 3 -> 3.2 -> 4 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | User loads older messages, then continues the same conversation. |
| Empty hero suggestion | `1 -> 2 -> 2.2 -> 3.3 -> 4 -> 5 -> 5.1 -> 6 -> 6.1 -> 6.14 -> 7` | User sends a suggestion from the empty hero through the normal first-send route. |
| Attachment send | `1 -> 2 -> 3 -> 4 -> 4.1 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | User attaches content before sending and receives a completed assistant turn. |
| Slash command result | `1 -> 2 -> 3 -> 4 -> 4.2 -> 5 -> 6 -> 6.5 -> 7` | User runs a command-style prompt and receives a dismissible command result card. |
| Model change before send | `1 -> 2 -> 3 -> 4 -> 4.3 -> 4.3.1 -> 4 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | User changes the model, returns to the composer, and sends successfully. |
| Thinking change before send | `1 -> 2 -> 3 -> 4 -> 4.4 -> 4 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | User changes thinking level before sending. |
| Empty draft blocked | `1 -> 2 -> 3 -> 4 -> 4.5 -> 4` | Empty submit does not create a message or run. |
| No model recovery | `1 -> 2 -> 3 -> 4 -> 4.6 -> 4.3 -> 4.3.1 -> 4 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | Missing model blocks send until the user selects one. |
| Unavailable model recovery | `1 -> 2 -> 3 -> 4 -> 4.7 -> 4.3 -> 4.3.1 -> 4 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | Unavailable model state routes the user through model selection before send. |
| Runtime unavailable recovery | `1 -> 2 -> 3 -> 4 -> 4.8 -> 4 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | Runtime readiness blocks submit until the composer becomes usable again. |
| Draft creation failure | `1 -> 2 -> 2.2 -> 3.3 -> 4 -> 5 -> 5.2 -> 4` | First send fails before a run starts; the draft remains editable. |
| Native tool completes | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 6.3 -> 6.3.1 -> 6.1 -> 6.14 -> 7` | Tool output resolves and the assistant turn continues to completion. |
| Native tool fails | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 6.3 -> 6.3.2 -> 6.8 -> 7` | Tool failure appears in the conversation and leaves the turn recoverable. |
| Permission approved | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 6.4 -> 6.4.1 -> 6.1 -> 6.14 -> 7` | User chooses the in-conversation approve command, then the assistant continues. |
| Permission denied | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 6.4 -> 6.4.2 -> 7` | User chooses the in-conversation deny command and the turn rests with denial context. |
| Stop running turn | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 6.7 -> 6.7.1 -> 7` | User stops an active turn and returns to an editable composer. |
| Run error details | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 6.8 -> 6.8.1 -> 6.8 -> 7` | User expands error detail and then returns to the recoverable error state. |
| Run error dismissed | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 6.8 -> 6.8.2 -> 7` | User dismisses the visible error and keeps the conversation usable. |
| Retry after run error | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 6.8 -> 5 -> 6 -> 6.1 -> 6.14 -> 7` | User retries after a failed turn and completes a later run. |
| Active run reconnect | `1 -> 2 -> 3 -> 3.1 -> 6.9 -> 6.1 -> 6.14 -> 7` | Returning to a conversation with active work resumes the stream in place. |
| Active run reconnect failed | `1 -> 2 -> 3 -> 3.1 -> 6.10 -> 6.8 -> 7` | Reconnect fails visibly, but the conversation remains available for manual recovery. |
| History load retry | `1 -> 2 -> 3 -> 3.5 -> 3.1 -> 3 -> 4` | History load failure can retry without leaving Chat mode. |

## Main Path

### 1 Enter Chat Mode

User entry: the user opens NoraHarness after onboarding or switches back to Chat from Code.

User action: the user enters Chat mode.

Visible UI state:

- Chat is selected in the mode switcher.
- Code remains available as the sibling mode.
- The Chat sidebar and the main Chat surface share one desktop frame.
- Settings and account controls remain in the sidebar footer.

Client state change: Chat mode becomes active and prepares the current conversation state.

Exit / next state: continue to `2 Conversation List`.

### 2 Conversation List

User entry: Chat mode is active.

User action: the user reviews conversations, searches, creates a new conversation, selects a row, or opens a row menu.

Visible UI state:

- Sidebar shows `Search conversations...`, New conversation, date-grouped rows, active-row highlight, recency labels, Settings, and account entry.
- The selected row remains highlighted while the main surface loads that conversation.
- If the sidebar is collapsed, the header can reopen it.

Client state change: available Chat conversations are filtered, sorted, and connected to active selection.

Exit / next state: selecting or retaining a conversation leads to `3 Selected Conversation Surface`.

### 3 Selected Conversation Surface

User entry: a conversation or draft is active.

User action: the user waits for history, reads existing messages, scrolls older history, or starts from the empty hero.

Visible UI state:

- Header shows the conversation title, selected model state, and any collapsed-sidebar controls.
- Message area shows loading, empty state, suggestions, historical turns, or active-run recovery.
- The composer remains anchored at the bottom when the surface is usable.

Client state change: the selected conversation loads history, checks for unfinished work, and prepares the composer state.

Exit / next state: once the surface is usable, continue to `4 Composer Ready`.

### 4 Composer Ready

User entry: a conversation is active and the composer is available.

User action: the user types a prompt, adds attachments, opens slash commands, picks a model, changes thinking level, or submits.

Visible UI state:

- The composer shows the prompt textarea, attachment control, model pill, thinking level, and send button.
- Send is available only when the draft and model/runtime state allow it.
- The model picker and slash menu are overlays or popovers on the same Chat surface.

Client state change: draft text, attachments, selected model, and selected thinking level update without leaving the conversation.

Exit / next state: valid submit moves to `5 Send Accepted`.

### 5 Send Accepted

User entry: the composer has a valid draft and the user submits it.

User action: the user clicks send or submits by keyboard.

Visible UI state:

- The user message appears immediately in the conversation.
- Composer controls that cannot change during the run become disabled.
- A draft conversation can gain a real sidebar row and updated title after first send.

Client state change: the assistant turn starts and duplicate sends are blocked for that draft.

Exit / next state: continue to `6 Assistant Running`.

### 6 Assistant Running

User entry: a turn has started.

User action: the user watches streaming output, inspects reasoning or tools, responds to approval, clicks Stop, or waits.

Visible UI state:

- Assistant text, reasoning disclosure, tool cards, permission request cards, command result cards, and status blocks appear inside the message list.
- Stop is available while the turn is pending or streaming.
- Approval appears as an in-conversation card with `approve` and `deny` command choices, not as a modal.

Client state change: run updates change visible message parts until the turn completes, stops, is denied, or fails.

Exit / next state: terminal states settle through `6.14 Final History Refresh` and then `7 Conversation Resting`, unless a branch requires recovery.

### 7 Conversation Resting

User entry: the assistant turn is no longer running, or a branch has reached a stable stop, denial, or error state.

User action: the user sends another prompt, retries, dismisses an error, switches conversations, opens Settings, or leaves Chat.

Visible UI state:

- Composer is editable again unless a recoverable gate remains visible.
- The latest turn remains in the message list as complete, stopped, denied, or failed.
- Sidebar summary, title, and timestamp can update after the final transcript settles.

Client state change: the conversation rests with the latest transcript and visible recovery state.

Exit / next state: journey ends or returns to `4 Composer Ready` for the next prompt.

## Branch Journeys

### 2.1 Search Conversations

Trigger: the user types in `Search conversations...` or clears the query.

Visible UI state: the conversation list narrows to matching rows or shows all visible rows again; the main Chat surface does not change until a row is selected.

Allowed user actions: keep typing, clear search, select a row, create a new conversation, or leave Chat.

Client state change: only the visible sidebar filter changes.

Recovery / next state: selecting a row proceeds to `3 Selected Conversation Surface`.

Blocks progress: no.

### 2.2 New Conversation

Trigger: the user clicks New conversation in the sidebar or header.

Visible UI state: a draft conversation becomes active and the main area can show the focused-conversation empty hero with suggestions.

Allowed user actions: type a prompt, click a suggestion, change model, attach content, or switch to another conversation.

Client state change: a draft conversation is selected locally and waits for first send before becoming saved.

Recovery / next state: continue to `3.3 Empty Conversation Suggestions` or `4 Composer Ready`.

Blocks progress: no.

### 2.3 Conversation Row Actions

Trigger: the user opens a conversation row menu.

Visible UI state: rename and delete actions appear in the row menu; delete uses an inline confirmation path before the row disappears.

Allowed user actions: rename, cancel delete, confirm delete, close the menu, or select another row.

Client state change: rename or delete updates the visible list; deleted active rows yield to another available conversation or draft-ready state.

Recovery / next state: returns to `2 Conversation List`.

Blocks progress: no, except while confirmation is awaiting the user's choice.

### 3.1 Conversation History Loading

Trigger: a selected conversation opens or a retry begins after history failed.

Visible UI state: loading appears in the message area while the selected conversation remains visible.

Allowed user actions: wait, switch conversations, or retry if failure appears.

Client state change: recent transcript and active-turn state are requested for the selected conversation.

Recovery / next state: successful load returns to `3 Selected Conversation Surface`; failure moves to `3.5 History Load Failed`.

Blocks progress: temporarily for reading or continuing that conversation.

### 3.2 Older History Loading

Trigger: the user scrolls to the top when older history is available.

Visible UI state: an older-history loading marker appears above the current messages; already loaded messages remain visible.

Allowed user actions: keep reading, wait, scroll, or continue composing if the composer is ready.

Client state change: older messages are prepended to the transcript.

Recovery / next state: return to `4 Composer Ready`.

Blocks progress: no.

### 3.3 Empty Conversation Suggestions

Trigger: the selected conversation has no visible messages or a new draft is active.

Visible UI state: the center of the conversation shows `Start a focused conversation`, helper copy, and suggestion chips such as planning, review, and root-cause prompts.

Allowed user actions: click a suggestion, type manually, select a model, attach content, or switch conversations.

Client state change: clicking a suggestion routes that prompt into the normal composer send path.

Recovery / next state: continue to `4 Composer Ready`.

Blocks progress: no.

### 4.1 Attachments

Trigger: the user opens the attachment control and adds files before sending.

Visible UI state: attachment chips appear in or near the composer and can be removed before submit.

Allowed user actions: add more attachments, remove chips, type, choose model/thinking, or send.

Client state change: selected attachment metadata is held with the current draft.

Recovery / next state: valid submit proceeds to `5 Send Accepted`.

Blocks progress: no unless attachment state itself shows an error.

### 4.2 Slash Command Menu

Trigger: the user types a slash query or opens command completion.

Visible UI state: command options appear near the composer; selecting one inserts command text into the draft.

Allowed user actions: select a command, continue typing, dismiss the menu, or send.

Client state change: the draft text updates. Some command-style prompts can later render as a `6.5 Command Result Card`.

Recovery / next state: valid submit proceeds to `5 Send Accepted`.

Blocks progress: no.

### 4.3 Model Picker

Trigger: the user clicks the model pill, Retry, unavailable-model badge, or no-model prompt.

Visible UI state: the current Chat surface dims behind a centered picker with `Search models...`, provider groups, and rows such as Claude Code and MiniMax options.

Allowed user actions: search, select a model, close the picker, or retry model loading.

Client state change: the picker shows model inventory and selection state for the active conversation.

Recovery / next state: continue to `4.3.1 Model Search And Selection`.

Blocks progress: only if no available model is selected.

### 4.3.1 Model Search And Selection

Trigger: the model picker is open.

Visible UI state: search filters model rows; the selected model row shows a check mark; closing the picker returns to the composer.

Allowed user actions: choose a model, change the query, close the picker, or retry loading when available.

Client state change: selected model updates for the active draft or saved conversation.

Recovery / next state: return to `4 Composer Ready`.

Blocks progress: no after a valid model is selected.

### 4.4 Thinking Selector

Trigger: the user opens or changes the thinking level control.

Visible UI state: the current level, such as medium, is shown in the composer; the control is disabled during streaming.

Allowed user actions: choose another level when not streaming, keep the current level, or send.

Client state change: selected thinking level updates for future sends in this conversation.

Recovery / next state: return to `4 Composer Ready`.

Blocks progress: no.

### 4.5 Empty Draft

Trigger: the user attempts to send without text or attachments.

Visible UI state: send remains unavailable or no turn starts; the composer keeps focus.

Allowed user actions: type, attach content, select a suggestion, or leave the composer.

Client state change: no message is appended and no assistant turn starts.

Recovery / next state: return to `4 Composer Ready`.

Blocks progress: yes until the user adds content.

### 4.6 No Model Selected

Trigger: the user attempts to send without an enabled model.

Visible UI state: the model area indicates that a model is required, and the draft remains editable.

Allowed user actions: open model picker, choose an available model, retry model loading, or keep editing.

Client state change: no assistant turn starts.

Recovery / next state: open `4.3 Model Picker`.

Blocks progress: yes until an available model is selected.

### 4.7 Model Unavailable

Trigger: the selected model is no longer available or model inventory fails to load.

Visible UI state: the header or composer shows unavailable, loading, no models, or Retry state.

Allowed user actions: retry model loading, open model picker, choose another model, or wait.

Client state change: model readiness stays unresolved until refreshed or changed.

Recovery / next state: open `4.3 Model Picker`.

Blocks progress: yes for sending when no available model is selected.

### 4.8 Runtime Unavailable

Trigger: Chat cannot start the assistant runtime for the current conversation.

Visible UI state: a banner or disabled send state explains that the assistant is unavailable, misconfigured, or not ready.

Allowed user actions: open Settings, wait for recovery, switch model, keep editing, or try again later.

Client state change: the draft is preserved while send remains blocked.

Recovery / next state: return to `4 Composer Ready` when runtime state becomes usable.

Blocks progress: yes for sending.

### 5.1 Draft Conversation Saved

Trigger: the user sends the first message from a draft conversation.

Visible UI state: the user message stays visible while the sidebar row, title, and timestamp settle.

Allowed user actions: wait for the assistant response or stop once the run is active.

Client state change: the draft becomes a saved conversation and the send continues.

Recovery / next state: continue to `6 Assistant Running`.

Blocks progress: temporarily.

### 6.1 Pending Or Streaming Response

Trigger: send has been accepted and the assistant turn is pending or streaming.

Visible UI state: assistant text can start empty, then stream; Stop is visible while the run is active.

Allowed user actions: wait, click Stop, inspect streamed parts, or respond to an approval card if one appears.

Client state change: the turn moves from submitted to streaming and then toward a terminal state.

Recovery / next state: completion moves to `6.14 Final History Refresh`; branches can move to tool, approval, stop, or error states.

Blocks progress: yes for starting another send in the same conversation.

### 6.3 Native Tool Card

Trigger: the assistant uses a visible tool during the turn.

Visible UI state: a tool card appears in the assistant message with input, progress, grouped tool cards, file/diff/terminal-shaped output, or generic Parameters and Result sections.

Allowed user actions: inspect the card, expand grouped details if available, stop the run, or wait.

Client state change: tool updates attach to the current assistant message.

Recovery / next state: success moves to `6.3.1 Native Tool Complete`; failure moves to `6.3.2 Native Tool Error`.

Blocks progress: only while the run waits for the tool or permission.

### 6.3.1 Native Tool Complete

Trigger: a visible tool returns final output.

Visible UI state: the same tool card shows final result content and no longer appears in progress.

Allowed user actions: inspect the result, continue waiting for the assistant, or stop if the run remains active.

Client state change: tool state is complete and the assistant turn can continue.

Recovery / next state: return to `6.1 Pending Or Streaming Response`.

Blocks progress: no.

### 6.3.2 Native Tool Error

Trigger: a visible tool returns an error.

Visible UI state: the tool card shows an error state and failure text.

Allowed user actions: read the failure, stop, wait for the assistant to handle it, or retry later from the conversation.

Client state change: tool state is failed; the turn can either continue with failure context or become a run error.

Recovery / next state: move to `6.8 Run Error` when the failure ends the turn.

Blocks progress: yes if the turn ends in error.

### 6.4 Permission Request

Trigger: the assistant needs user approval for a protected action.

Visible UI state: an approval request appears inside the message stream with `approve` and `deny` command choices; the conversation pauses at that card.

Allowed user actions: choose `approve`, choose `deny`, inspect the request, or stop the run.

Client state change: the run waits for the user's decision.

Recovery / next state: `approve` moves to `6.4.1 Permission Approved`; `deny` moves to `6.4.2 Permission Denied`.

Blocks progress: yes until the user chooses or stops.

### 6.4.1 Permission Approved

Trigger: the user chooses the `approve` command choice on the in-conversation approval card.

Visible UI state: the card resolves as approved and the assistant stream continues below it.

Allowed user actions: watch the resumed stream, stop, or continue after completion.

Client state change: the protected action is allowed and the run resumes.

Recovery / next state: return to `6.1 Pending Or Streaming Response`.

Blocks progress: no after approval is accepted.

### 6.4.2 Permission Denied

Trigger: the user chooses the `deny` command choice on the in-conversation approval card.

Visible UI state: the card resolves as denied and the assistant turn shows that the protected action did not proceed.

Allowed user actions: send a revised prompt, change settings, switch conversations, or leave Chat.

Client state change: the protected action is denied and the turn reaches a stable denied state.

Recovery / next state: continue to `7 Conversation Resting`.

Blocks progress: no after denial resolves.

### 6.5 Command Result Card

Trigger: the user sends a command-style prompt whose output is shown outside normal saved assistant history.

Visible UI state: a dismissible `Command result` card appears below the message list and indicates that it is not saved.

Allowed user actions: read, dismiss, send another prompt, or switch conversations.

Client state change: command output is held as surface state rather than as a normal assistant message.

Recovery / next state: continue to `7 Conversation Resting`.

Blocks progress: no.

### 6.7 Stop Requested

Trigger: the user clicks Stop while the assistant turn is pending or streaming.

Visible UI state: the stop control remains active until the turn leaves running state.

Allowed user actions: wait for the stop to resolve.

Client state change: the current assistant turn is asked to stop and duplicate stop actions are avoided.

Recovery / next state: continue to `6.7.1 Stopped Turn`.

Blocks progress: temporarily.

### 6.7.1 Stopped Turn

Trigger: the stop request resolves or the local stop fallback completes.

Visible UI state: the assistant turn ends as stopped or cancelled, possibly with partial content still visible.

Allowed user actions: send another prompt, retry manually, switch conversations, or leave Chat.

Client state change: running state clears and the composer becomes editable again.

Recovery / next state: continue to `7 Conversation Resting`.

Blocks progress: no.

### 6.8 Run Error

Trigger: the assistant turn fails, send fails after it starts, reconnect fails, or a tool failure ends the run.

Visible UI state: an inline error surface appears in the conversation, with recovery actions such as Retry, Detail, or Dismiss when available.

Allowed user actions: retry, expand Detail, dismiss the error, edit the next prompt, or switch conversations.

Client state change: the run is marked failed while preserving the selected conversation and transcript.

Recovery / next state: detail opens `6.8.1 Error Details`; dismiss moves to `6.8.2 Error Dismissed`; retry routes through `5 Send Accepted`; otherwise the conversation can rest at `7 Conversation Resting`.

Blocks progress: no once the composer is editable, but the failed turn remains visible until recovery or dismissal.

### 6.8.1 Error Details

Trigger: the user opens Detail on a run error.

Visible UI state: additional failure text appears under the error surface.

Allowed user actions: close details when available, dismiss the error, retry, or continue manually.

Client state change: details visibility changes locally.

Recovery / next state: return to `6.8 Run Error`.

Blocks progress: no.

### 6.8.2 Error Dismissed

Trigger: the user clicks Dismiss on a visible run error.

Visible UI state: the error banner clears while the conversation history remains.

Allowed user actions: send another prompt, switch conversations, or leave Chat.

Client state change: visible error state clears for the current session.

Recovery / next state: continue to `7 Conversation Resting`.

Blocks progress: no.

### 6.9 Active Run Reconnected

Trigger: the user returns to a conversation with an unfinished assistant turn.

Visible UI state: buffered text, reasoning, tools, permission state, or status reappears in the message list and live streaming resumes.

Allowed user actions: wait, stop, respond to approval, inspect tool output, or continue after completion.

Client state change: missed updates are replayed and the live run is reattached to the selected conversation.

Recovery / next state: return to `6.1 Pending Or Streaming Response`.

Blocks progress: temporarily while reconnecting.

### 6.10 Active Run Reconnect Failed

Trigger: the user returns to an unfinished turn, but the app cannot resume the live stream.

Visible UI state: a recoverable failure appears instead of live streaming output.

Allowed user actions: retry recovery, resend manually, start a new prompt, switch conversations, or leave Chat.

Client state change: the old run is not reattached, but the selected conversation remains visible.

Recovery / next state: move to `6.8 Run Error`.

Blocks progress: no after the failure is visible and the composer is editable.

## Detail States

### 3.4 Header And Collapsed Sidebar Controls

Trigger: the user is viewing a selected conversation, with the sidebar either open or collapsed.

Visible UI state:

- Header shows the conversation title or fallback title.
- Header can show selected model or unavailable-model state.
- When the sidebar is collapsed, the header provides a way to reopen it.
- New conversation remains available from the header.

Allowed user actions: reopen sidebar, create a new conversation, inspect model state, or continue composing.

Client state change: header state follows active conversation, sidebar state, and model state.

Recovery / next state: return to `4 Composer Ready`.

Blocks progress: no.

### 6.2 Reasoning Disclosure

Trigger: the assistant response includes reasoning content.

Visible UI state: reasoning appears as a collapsible block above the final answer text.

Allowed user actions: expand or collapse reasoning, keep reading, stop if the run is still active, or continue after completion.

Client state change: reasoning visibility changes locally while the message content remains part of the turn.

Recovery / next state: return to `6.1 Pending Or Streaming Response`.

Blocks progress: no.

### 6.6 External Runtime Blocks

Trigger: the current turn includes visible runtime-specific status, tool, permission, or modified-file blocks.

Visible UI state: compact blocks appear inside the same conversation message list, alongside normal assistant text and tools.

Allowed user actions: inspect the blocks, follow links when offered, stop the run, or continue after completion.

Client state change: event updates become visible blocks in the active conversation.

Recovery / next state: return to `6.1 Pending Or Streaming Response`.

Blocks progress: no by itself.

### 6.14 Final History Refresh

Trigger: the turn reaches completion, stop, denial, or error.

Visible UI state: the message list settles; final assistant content, stopped state, denial context, or error remains visible; sidebar title, summary, and timestamp can refresh.

Allowed user actions: wait briefly, then continue composing, retry, switch conversations, or leave Chat.

Client state change: the visible transcript reconciles to the final saved state for the conversation.

Recovery / next state: continue to `7 Conversation Resting`.

Blocks progress: temporarily.

## State Language

### 0.1 Shared Chat Surface Labels

Visible UI state: shared labels include `Chat`, `Code`, `Search conversations...`, `New Conversation`, `Settings`, `Log in`, `Start a focused conversation`, and `Assign a task or ask any question`.

Meaning: these labels identify the user as staying inside the normal Chat shell rather than entering a separate setup or modal journey.

Next state: any Chat state.

### 4.9 Composer Control Labels

Visible UI state: composer labels include model names such as `MiniMax-M2.7`, model-picker copy such as `Search models...`, thinking levels such as `medium`, `Retry`, send, and stop.

Meaning: composer copy communicates whether the draft can send, needs model recovery, is waiting on runtime readiness, or is already running.

Next state: `4 Composer Ready`, `5 Send Accepted`, or `6 Assistant Running`.

### 6.11 Stream, Tool, And Approval Labels

Visible UI state: stream labels include pending, streaming, reasoning, tool progress, `Command result`, `approve`, `deny`, approved, denied, stopped, and completed.

Meaning: every running-state label appears in the conversation and keeps the user oriented inside the current turn.

Next state: `6 Assistant Running` or `7 Conversation Resting`.

### 6.12 Error And Recovery Labels

Visible UI state: recovery labels include failed, Retry, Detail, Dismiss, reconnecting, unavailable, and failed to load chat history.

Meaning: error copy explains what is blocked and keeps recovery actions close to the affected conversation.

Next state: `3.5 History Load Failed`, `4.8 Runtime Unavailable`, `5.2 Draft Creation Failed`, `6.8 Run Error`, or `6.10 Active Run Reconnect Failed`.

## Errors And Recovery

### 3.5 History Load Failed

Trigger: conversation history cannot load.

Visible UI state: the selected conversation remains active and shows a visible failure with retry.

Allowed user actions: click Retry, reopen the conversation, select another conversation, or create a new one.

Client state change: the failed load is recoverable and does not eject the user from Chat mode.

Recovery / next state: retry returns to `3.1 Conversation History Loading`.

Blocks progress: yes for reading that conversation until recovered.

### 5.2 Draft Creation Failed

Trigger: the user sends from a draft conversation, but the conversation cannot be saved before the run starts.

Visible UI state: the draft and composer remain visible with failure feedback.

Allowed user actions: retry send, edit the draft, change model, or switch conversations.

Client state change: no assistant run starts and the user's draft remains recoverable.

Recovery / next state: return to `4 Composer Ready`.

Blocks progress: yes until retry succeeds or the user chooses another path.

### Error Recovery Summary

| Error / branch | Where it appears | Recovery |
| --- | --- | --- |
| Empty draft | `4.5 Empty Draft` | Add text, attach content, or choose a suggestion. |
| No model selected | `4.6 No Model Selected` | Open the model picker and choose an available model. |
| Model unavailable | `4.7 Model Unavailable` | Retry model loading or choose another model. |
| Runtime unavailable | `4.8 Runtime Unavailable` | Open Settings, wait for runtime recovery, or try again later while the draft remains. |
| Draft creation failed | `5.2 Draft Creation Failed` | Retry send or keep editing the draft. |
| Tool error | `6.3.2 Native Tool Error` | Read the tool error, let the assistant handle it, or recover through the run error path. |
| Permission denied | `6.4.2 Permission Denied` | Send a revised prompt, change settings, or continue from the denied state. |
| Stop requested | `6.7 Stop Requested` | Wait until the stopped turn reaches `6.7.1 Stopped Turn`. |
| Run error | `6.8 Run Error` | Retry, open Detail, Dismiss, or send a new prompt. |
| Reconnect failed | `6.10 Active Run Reconnect Failed` | Retry recovery, resend manually, or continue from the visible transcript. |
| History load failed | `3.5 History Load Failed` | Click Retry or reopen the conversation. |

## Wireframe

This record is the source of truth for the Journey 4 wireframe. The wireframe should keep six first-level bands in this order: `Main happy path`, `Route map`, `Branch journeys`, `Detail states`, `State language`, and `Errors and recovery`.

The `Main happy path` band should draw the main journey steps as `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7`, without expanding branch or detail states into that first band. The `Route map` band should draw every Route Table route as a full route row with full-size Chat product cards, including the main completed turn route. The `Branch journeys` band should keep full Chat product wireframe cards for branch states. The `Detail states`, `State language`, and `Errors and recovery` bands should remain separate state-layer bands with structured summary, status, copy, and recovery cards rather than route rows.

The approval state must be drawn as part of the conversation stream with `approve` and `deny` command choices. It must not be drawn as an independent overlay.
