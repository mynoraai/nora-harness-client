# Journey 1 — Firmware tools sign-in gate across NoraHarness clients

This journey records what a user sees when the assistant uses **firmware tools** in NoraHarness, and how those tools become usable once the user signs in. The same firmware tools are reachable from three client surfaces, and all three share one account sign-in: signing in once anywhere makes firmware tools work everywhere on that machine.

The three client surfaces:

- **Gateway chat** — the built-in NoraHarness assistant (the default conversation runtime).
- **In-app agent** — an in-app Claude or Codex conversation started from the runtime picker.
- **Terminal client** — a separate Claude Code or Codex running in a terminal window, outside the app.

Signed-out is fail-closed: firmware tools refuse to run until the user has an active NoraHarness account credential. Signed-out never silently succeeds.

## Numbering And Route Tables

### State inventory — Main path (signed in, tools work)

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
|----|------|-------------|--------------------|-------------|------------------|---------------------|-------------------|
| 1 | Main step | — | Firmware chat surface, signed in | Opens a conversation where the assistant can use firmware tools | Full app: left conversation list, center chat, right workbench (All Files / Changes / Device / Preview). Bottom-left shows the account name + Sign out | Account credential present | 2 |
| 2 | Main step | 1 | Assistant runs a firmware tool | Asks the assistant to do a firmware action | A tool-call card appears inline in the chat stream with a **Running** pill | Tool call in progress | 3 |
| 3 | Main step | 2 | Authorized result | — | The tool card turns to a green **Completed** pill | Tool result received | 3.1 |
| 3.1 | Detail state | 3 | Expanded firmware result | Clicks the tool card to expand | Card expands to show the raw firmware result (project status, USB serial ports, detected board) | — | End (assistant continues) |

### State inventory — Not-authorized branch (signed out, in-app)

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
|----|------|-------------|--------------------|-------------|------------------|---------------------|-------------------|
| 2.1 | Branch state | 2 | Gateway chat, not authorized | Assistant tried a firmware tool while signed out | The tool card shows a red **Not Authorized** pill (not green), and an app-owned **Sign in to use firmware tools** card appears directly under it with a **Sign in to NoraHarness** button | Tool refused, fail-closed | 5 (via the card button) or stays blocked |
| 2.2 | Branch state | 2 | In-app agent (Claude / Codex), not authorized | Same, inside an in-app Claude or Codex conversation | Same red **Not Authorized** pill + same **Sign in to NoraHarness** card | Tool refused, fail-closed | 5 or stays blocked |

### State inventory — Terminal client (separate surface)

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
|----|------|-------------|--------------------|-------------|------------------|---------------------|-------------------|
| 4 | Main step | — | Terminal firmware call | Runs a firmware tool from a terminal Claude Code / Codex | Terminal window with the assistant's tool call | Tool call in progress | 4.1 or authorized |
| 4.1 | Branch state | 4 | Terminal, not authorized | — | Plain-text refusal in the terminal: run the browser sign-in tool, or create a key on the dashboard and set it in the client's environment. No app card (a terminal has no app UI) | Tool refused, fail-closed | 7 (browser sign-in) or stays blocked |

### State inventory — Sign-in and access

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
|----|------|-------------|--------------------|-------------|------------------|---------------------|-------------------|
| 5 | Main step | — | Start sign-in | Clicks **Log in** (bottom-left) or the **Sign in to NoraHarness** button on the card | — | Sign-in requested | 5.1 |
| 5.1 | Detail state | 5 | Browser sign-in | Enters account email + password in the browser page | The system browser opens the NoraHarness sign-in page | — | 5.2 |
| 5.2 | Main step | 5.1 | Return to app | — | Browser hands back to the app; bottom-left now shows the account name and **Sign out** | Account signed in | 6 |
| 6 | Main step | 5.2 | Firmware access prepared | — | No visible change in chat; access is prepared for the account | Firmware credential written for all clients | 6.1 |
| 6.1 | State | 6 | Tools authorized everywhere | Runs a firmware tool in any client | Gateway chat, in-app agent, and terminal client all return real results | — | 5.3 |
| 5.3 | Main step | 6.1 | After sign-in — conversation continues | Asks again, or the assistant re-calls the firmware tool | Same conversation: the earlier **Not Authorized** card stays as history (dimmed); a new call to the same tool now shows green **Completed** with the real answer. The sign-in card is gone | Refusal loop closed for this conversation | 3 |

### State inventory — Terminal browser sign-in (zero-key)

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
|----|------|-------------|--------------------|-------------|------------------|---------------------|-------------------|
| 7 | Main step | 4.1 | Terminal browser sign-in | Runs the browser sign-in tool in the terminal | The tool prints a short code and an approval link | Sign-in started | 7.1 |
| 7.1 | Detail state | 7 | Approve in browser | Opens the link and approves | Browser approval page confirms the code | — | 7.2 |
| 7.2 | State | 7.1 | Terminal authorized | Re-runs the firmware tool | The terminal firmware tool now returns a real result | Credential cached for the terminal client | End |

### State inventory — Recovery and errors

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
|----|------|-------------|--------------------|-------------|------------------|---------------------|-------------------|
| 6.2 | Recovery state | 6 | Mid-session sign-in gap | Signed in while the app was already running, and only used gateway chat | Firmware tool in gateway chat still shows **Not Authorized** because access was not re-prepared this session | Blocks progress until reset | 6.2.1 |
| 6.2.1 | Recovery state | 6.2 | Access prepared after reset | Restarts the app, or opens an in-app agent conversation | Next firmware tool call returns a real result | Firmware credential prepared | 3 |
| 9.1 | Error state | 2 | Service unreachable | — | Tool card shows **Not Authorized** with an "unreachable" note; never silently allowed | Fail-closed | 2.1 |
| 9.2 | Error state | 3 | Access revoked | Account firmware access revoked on the dashboard | Next firmware tool call returns to the **Not Authorized** state | Fail-closed | 2.1 |

### Route table

| Route | Composition | Result / next state |
|-------|-------------|---------------------|
| R1 Signed-in gateway happy path | 1 → 2 → 3 → 3.1 | Firmware tool runs, real result shown and expandable |
| R2 Gateway sign-in recovery | 1 → 2 → 2.1 → 5 → 5.1 → 5.2 → 6 → 6.1 → 5.3 → 3 | Refused, user signs in, back in the same conversation the tool now completes |
| R3 In-app agent sign-in recovery | 1 → 2 → 2.2 → 5 → 5.1 → 5.2 → 6 → 6.1 → 5.3 → 3 | Same recovery from an in-app Claude / Codex conversation |
| R4 Terminal not authorized | 4 → 4.1 | Terminal refusal with sign-in guidance |
| R5 Terminal browser sign-in | 4 → 4.1 → 7 → 7.1 → 7.2 | Terminal signs in via browser, tool authorized |
| R6 Mid-session sign-in gap | 2.1 → 5 → 5.2 → 6.2 → 6.2.1 → 3 | Sign-in did not auto-prepare access; reset prepares it |
| R7 Service unreachable | 2 → 9.1 → 2.1 | Fail-closed with unreachable note |
| R8 Access revoked | 3 → 9.2 → 2.1 | Previously working tool returns to not-authorized |

## Main Path

**1 Firmware chat surface.** The user is signed in and in a NoraHarness conversation whose runtime can use firmware tools. The full app is visible: the left column lists workspaces and conversations with **Log in / account** at the bottom; the center is the conversation with a composer and a runtime chip (for example the built-in assistant); the right column is the workbench (All Files / Changes / Device / Preview, plus Terminal / Device Log).

**2 Assistant runs a firmware tool.** The user asks for a firmware action. A tool-call card appears inline in the chat stream showing the tool name and a **Running** pill.

**3 Authorized result.** With an account credential present, the tool runs. The card settles to a green **Completed** pill.

**3.1 Expanded firmware result.** The user clicks the card to expand it and sees the raw firmware result — project status, the list of USB serial ports, and any detected board.

## Branch Journeys

**2.1 Gateway chat, not authorized.** When the user is signed out, the same tool call is refused. The card shows a red **Not Authorized** pill instead of green, and an app-owned **Sign in to use firmware tools** card appears directly beneath the card with a **Sign in to NoraHarness** button. This is a blocking state for the firmware action until sign-in.

**2.2 In-app agent, not authorized.** The identical red pill and sign-in card appear when the firmware tool is called inside an in-app Claude or Codex conversation. Same copy, same button, same recovery.

**4 / 4.1 Terminal firmware call and refusal.** A terminal Claude Code or Codex calls a firmware tool. Signed out, the tool returns a plain-text refusal in the terminal telling the user to run the browser sign-in tool (no key pasting) or to create a key on the NoraHarness dashboard and set it in the client's environment. There is no app card here — a terminal has no app UI — so the guidance is text.

## Detail States

**3.1 Expanded firmware result.** Reused expandable tool card; shows the raw firmware status payload.

**5.1 Browser sign-in.** The system browser opens the NoraHarness sign-in page. The user enters account email and password. On success the browser hands control back to the app.

**5.3 After sign-in — the conversation continues.** Sign-in does not rewrite the card that refused earlier; that **Not Authorized** card stays in the scrollback as history. What changes is the *next* call: the user asks again (or the assistant re-calls the firmware tool) in the same conversation, and this time the tool returns green **Completed** with the real answer, and the sign-in card is gone. Today this is a fresh call, not an automatic retry of the refused one — the wireframe flags whether it should become an auto-retry as an open interaction decision.

**7.1 Approve in browser.** For terminal browser sign-in, an approval page confirms a short code the terminal printed. Approving completes the terminal's sign-in.

**Runtime picker.** The runtime picker (used to open an in-app Claude / Codex conversation) is where surface 2.2 originates. Selecting an in-app agent creates a new conversation on that runtime.

## State Language

- **Running** — the firmware tool is in progress (neutral pill).
- **Completed** — the firmware tool returned a real result (green pill).
- **Not Authorized** — the firmware tool was refused because there is no active account credential (red pill). Shown in gateway chat and in-app agent conversations.
- **Sign in to use firmware tools** — the heading of the app-owned card shown under a refused firmware tool. Body: firmware tools need a NoraHarness account, and signing in once authorizes every firmware tool here and in the terminal. Button: **Sign in to NoraHarness**.
- **Terminal refusal text** — in a terminal client there is no card; the refusal is plain text that names the browser sign-in tool and the dashboard-key alternative. The wording is deliberately the terminal wording, not the app's "Sign in to NoraHarness," because a terminal user is not inside the app.

## Errors And Recovery

**6.2 Mid-session sign-in gap.** If the user signs in while the app is already running and only uses the gateway chat, firmware access may not have been prepared for this session yet. The firmware tool still shows **Not Authorized**. This is a real gap in the current behavior.

**6.2.1 Access prepared after reset.** Restarting the app, or opening an in-app agent conversation, prepares firmware access for the signed-in account. The next firmware tool call then returns a real result.

**9.1 Service unreachable.** If the NoraHarness service cannot be reached, the firmware tool fails closed and shows **Not Authorized** with an "unreachable" note. It never silently allows the tool to run.

**9.2 Access revoked.** If the account's firmware access is revoked, a previously working tool returns to the **Not Authorized** state on its next call.

## Wireframe

The wireframe draws the full NoraHarness app for every in-app state (left column, center chat with the tool-call card in its real position, right workbench), and a terminal window for the terminal states. Bands, in order: Main happy path, Route path, Detail states, State language, Errors and recovery. Route rows use full app frames; Detail / State language / Errors bands use compact summary cards.
