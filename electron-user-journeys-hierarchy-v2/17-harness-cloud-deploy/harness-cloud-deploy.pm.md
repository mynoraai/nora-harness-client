# Harness Cloud Deploy — PM Decision

> PM decision document. Records the open product/interaction decisions, boundaries, assumptions, dependencies, and non-goals. Not the final journey and not final UI.

## PM decision to unblock (one sentence)

**When a user moves from local deploy to cloud deploy, decide how cloud deploy is triggered, how an unauthenticated user is guided to sign in, and how a successful deploy hands off to "let the device connect to the cloud" — so we can draw the journey and the wireframe.**

## Current state (today, from the user's view)

- **Local deploy is done by asking the agent in the conversation**, not by a UI button. The user asks the agent to "deploy", and a tool card (`hw_application_harness_deploy`) appears in the conversation, moving from Running to **Completed / Error**. It starts a **local** harness; the right-side Device / Preview tabs and the bottom Terminal / Device Log reflect the running harness independently.
- **Sign-in** uses the cloud account (NoraHarness). Two entry points already exist: the sidebar account footer (logged out shows `Log in`; logged in shows name + role + `Sign out`), and the publish dialog's sign-in gate (logged out shows an amber "Sign in to NoraHarness to publish" + Sign in; logged in shows a green "Signed in as …").
- **Cloud deploy is essentially absent from the user's view today.** Backend plumbing (deploy to cloud + wake the cloud instance) is partly built on the engineering side, but there is **no agent tool and no UI entry point** to trigger it. The user cannot "deploy to cloud" today.

## Implementation maturity

| Part | Maturity |
| --- | --- |
| Local deploy (agent tool + tool-card surface) | **implemented** |
| Sign-in + sign-in gate (reusable publish pattern) | **implemented** |
| Settings page (full-screen, sidebar + content, cards) | **implemented** (no account tab yet) |
| Cloud deploy backend plumbing (deploy / ensure / error classes) | **partial** (plumbing exists, no tool, no UI) |
| Cloud deploy tool `hw_application_harness_cloud_deploy` | **not built** |
| Cloud runtime status surface in the UI | **not built** |
| Device connects to cloud (device-side provisioning) | **separate product line**, out of this flow's scope |

## Discussion boundaries / Non-goals

**What PM decides:** how cloud deploy is triggered, the unauthenticated sign-in UX, error messaging and "next step" guidance, how a successful deploy hands off to device provisioning, and whether a cloud runtime status surface is needed.

**Fixed by engineering — not up for debate:**
- The desktop is a **deployer** — it only deploys the harness to the cloud + wakes the instance, and **does not connect** to the cloud runtime itself.
- The one that actually connects to the cloud runtime is the **hardware device (ESP32)**, over a device-side connection + BLE configuration, owned by a separate product line (device provisioning / mobile / firmware).
- The server deploy/wake contract and error classes are fixed (see the error mapping below).
- Sign-in mechanism is fixed (cloud account, credentials held only in the main process, the UI never holds a token).

**Non-goals (not discussed here):**
- The device-side BLE configuration UI (separate line).
- The cloud runtime backend shape (containers / autoscaling).
- The desktop "connecting" to the runtime for interaction (already rejected — the desktop does not connect).

## Discussion focus

In a conversation-driven product, what is the most consistent way to do the **entry and feedback** of cloud deploy: keep the "agent tool + tool card" mental model, or introduce a UI button; how to guide an unauthenticated user to sign in without breaking the flow and then resume the deploy; and how to clearly tell the user, after a successful deploy, that the "next step is to let the device connect to the cloud".

## PM decisions (need a call)

<table>
  <tr><td bgcolor="#fff3cd"><strong>PM decision 1 — cloud deploy entry point</strong></td></tr>
</table>

How is cloud deploy triggered? Three options:
- **A (recommended): a new agent tool `hw_application_harness_cloud_deploy`**, the same mental model as local deploy — the user asks the agent to "deploy to cloud", a tool card appears in the conversation, Running → Completed / Error. No new UI paradigm; reuses the existing tool card.
- B: add a `target: cloud` parameter to the existing deploy tool (one tool, two targets). Saves a tool, but "local vs cloud" on one card is easier to confuse, and the return body differs (cloud adds instance info).
- C: add a "Deploy to cloud" button in the UI (Settings or the right panel). But there is **no button paradigm for deploy today** — this would introduce an entry inconsistent with local deploy.

<table>
  <tr><td bgcolor="#f8d7da"><strong>Blocking PM decision 2 — the sign-in gate</strong></td></tr>
</table>

Cloud deploy **requires sign-in**. When an unauthenticated user triggers cloud deploy, how do we guide them?
- **A (recommended): an inline sign-in card**, reusing the publish dialog pattern — "Sign in to NoraHarness to deploy to cloud" + Sign in, then auto-resume the deploy after login. Non-disruptive, with existing language and visuals.
- B: a full-panel blocking sign-in card (like the agent-runtime sign-in "I've signed in — refresh"). Stronger blocking feel, fits a must-sign-in-first case, but heavier than A.
- Sub-states to cover: **checking sign-in / unauthenticated / signing in / sign-in failed / account does not match the project (needs re-login)**.

<table>
  <tr><td bgcolor="#fff3cd"><strong>PM decision 3 — error → "next step" guidance</strong></td></tr>
</table>

Cloud deploy/wake returns clear error classes, each mapping to a user-actionable next step. Surface them on the **tool card's Error state** (recommended) or an inline card? Error → guidance mapping:

| Error | What the user sees |
| --- | --- |
| Not signed in / session expired | "Sign in and retry" (triggers decision 2's sign-in card) |
| Account does not match the project | "Sign out and sign back in with the project's account" |
| Nothing deployed yet | "Run a cloud deploy first" |

<table>
  <tr><td bgcolor="#fff3cd"><strong>PM decision 4 — handoff to "device connects to cloud" after deploy</strong></td></tr>
</table>

After a successful cloud deploy, the one that actually connects is the **device**, not the desktop. Should the desktop give a clear "next step: let the device connect to this cloud instance" guide?
- **A (recommended): the tool card's Completed state shows a clear "next step" note** pointing to device provisioning (that line), so the user is not left wondering "deploy is done, now what".
- B: show nothing and leave it to the device/mobile line to guide. Risk: the user thinks the desktop still has work to do, or does not know the next step.
- Note: the desktop **can only guide, not complete the connection for the device** — the journey has a clear "handoff" point here.

<table>
  <tr><td bgcolor="#fff3cd"><strong>PM decision 5 — whether to show a cloud runtime status surface</strong></td></tr>
</table>

Should the UI show "cloud instance: starting / running / failed / stopped"? Could reuse the Settings card-row + status badge style. This is a planned-but-unbuilt part on the engineering side.
- If yes: the user can see whether the cloud instance is alive and whether to wake it.
- If no: rely purely on the deploy tool card; status is "one-shot", not live.

## Proposed design (preliminary, depends on the decisions above)

By the recommended options (1A / 2A / 3-tool-card / 4A):

1. The user asks the agent to "deploy to cloud" → the agent calls the cloud deploy tool → a tool card appears **Running**.
2. If not signed in → the card (or inline) shows the sign-in gate "Sign in to deploy to cloud" → the user signs in → the agent auto-retries.
3. Success → the card is **Completed**, showing project / cloud instance info + a "next step: let the device connect to this cloud instance" note.
4. Failure → the card is **Error** + the matching "next step" copy (see decision 3 table).
5. (optional, decision 5) show a live cloud-instance status badge somewhere in the UI.

## Assumptions and dependencies

- **Assumption:** cloud deploy reuses the agent-tool paradigm (decision 1A) — pending PM confirmation.
- **Dependency:** the server deploy/wake contract + error classes (exist); **the device provisioning line** (NoraHarness cloud device-binding + BLE + mobile) — determines where the "how the device connects next" guidance can point.
- **Dependency:** sign-in uses the existing account session + gate card (exist).

## Benefits

- **Same mental model** as local deploy (agent tool + tool card), zero learning cost.
- **Reuses** the existing sign-in gate and error language; no new sign-in UX invented.
- Errors map directly to a "next step"; users are not stuck on vague failures.
- Makes the "desktop deploys → device connects to cloud" handoff explicit, so users don't think the desktop should connect.

## Downsides / tradeoffs

- Agent-driven is less discoverable for users unused to "asking the agent to do things" (no prominent "Deploy to cloud" button).
- "How the device connects next" crosses into another product line; the journey has a handoff break — the desktop can only guide, not close the loop.
- Without a status surface (decision 5), users can't see the live cloud-instance status.

## Open questions

- Who triggers/owns the device-side provisioning (mobile app? a companion flow?) — determines where decision 4's "next step" can concretely point.
- The exact parameters and return of the cloud deploy tool (for the follow-up ADR: a new tool vs a `target=cloud` parameter).
- Whether to build the cloud runtime status surface (decision 5) now or defer it.
