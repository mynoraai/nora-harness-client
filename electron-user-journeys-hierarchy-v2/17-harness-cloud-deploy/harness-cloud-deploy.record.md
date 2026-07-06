# Journey 17: Harness Cloud Deploy

Text version of the journey, source of truth for the wireframe. Records what the journey is: entry, visible UI state, branches, exits, and recovery. Resolved from `harness-cloud-deploy.pm.md` recommended decisions (agent-tool invocation; inline sign-in gate; error codes surfaced on the tool card; device-provisioning handoff after success).

## Journey Scope

The user asks the agent to deploy the current project's application harness to the cloud. The desktop app is a **deployer**: it deploys and ensures/wakes a cloud runtime, then hands off to the hardware device to connect. Cloud deploy requires a signed-in cloud account. This journey covers invocation, the sign-in gate, success, the precondition/error branches, and the handoff to device provisioning. The device→runtime connection itself (device_ws + BLE provisioning) is a separate channel and is out of this journey's scope.

## Numbering And Route Tables

### Main path states

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 17 | User asks the agent to deploy to cloud. | Types a "deploy to cloud" request in the conversation composer. | The conversation shows the user's request; the agent begins working. | The agent decides to call the cloud deploy tool. | `2` |
| `2` | Main path | Journey 17 | Cloud deploy tool starts. | Waits. | A tool card `hw_application_harness_cloud_deploy` appears with a `Running` status (pulsing clock). | The client uploads the bundle and requests a cloud runtime. | `2.1`, `3`, or an error branch |
| `3` | Main path | Journey 17 | Cloud deploy succeeds. | Reads the tool card. | The tool card flips to `Completed` (green check), showing project + cloud instance summary and a **Next step** note: "Connect your device to this cloud instance." | A cloud runtime is running for this project; connection credentials exist server-side. | `4` |
| `4` | End state | Journey 17 | Handoff to device provisioning. | Continues to the device provisioning path (separate channel) or keeps working. | The Next-step note points to device provisioning; the desktop does not connect to the runtime itself. | The desktop's deploy responsibility is complete. | End |

### Sign-in gate branch (from step 2)

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Branch | `2` | Not signed in. | Reads the gate. | The tool card shows an inline sign-in gate: "Sign in to NoraHarness to deploy to cloud" + `Sign in` (amber), mirroring the publish sign-in gate. | The client detected no cloud session (`AUTH_REQUIRED`). Blocks progress. | `2.1.1`, `2.1.2`, or `2.1.3` |
| `2.1.1` | Branch | `2.1` | Sign-in succeeds. | Clicks `Sign in`, completes login. | The gate turns green "Signed in as …", then the tool card resumes to `Running`. | A cloud session is established; the agent retries the deploy. | `3` or an error branch |
| `2.1.2` | Recovery | `2.1` | Sign-in fails / cancelled. | Cancels or the login errors. | The gate stays; a short error line "Sign-in failed — try again" appears with `Sign in`. | No session; deploy stays blocked. | `2.1` |
| `2.1.3` | Recovery | `2.1` | Account does not match the project. | Signs in with a different account than the project owner. | The card shows "This account can't deploy this project. Sign out and sign back in with the project's account." + `Sign out`. | Session exists but is `FORBIDDEN` for this project. Blocks progress. | `2.1` |

### Precondition / error branch (from step 2)

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Error | `2` | Nothing deployed yet. | Reads the card. | Tool card `Error`: "No cloud deployment yet — run a cloud deploy first." | Server returned `RUNTIME_NOT_DEPLOYED`. | `2` on retry |
| `3.2` | Error | `2` | No model key configured. | Reads the card. | Tool card `Error`: "No model key for this project — configure a model key, then retry." + a pointer to the one-time key setup. | Server returned `LLM_KEY_NOT_CONFIGURED`. | `3.2.1` then `2` |
| `3.2.1` | Recovery | `3.2` | One-time model key setup. | Configures a provider key once. | The provider key setup surface (existing Providers pattern); on success returns to the conversation. | A server-held key is configured for the project. | `2` on retry |
| `3.3` | Error | `2` | Runtime failed to start. | Reads the card. | Tool card `Error`: "Cloud runtime failed to start — retry, or check the model/config." | Server returned a `RUNTIME_FAILED`/`RUNTIME_UNREACHABLE`-class outcome; no credentials. Must not hand off. | `2` on retry |

### Route table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Happy path | `1 -> 2 -> 3 -> 4` | Cloud runtime running; handoff to device provisioning. |
| Sign-in then deploy | `1 -> 2 -> 2.1 -> 2.1.1 -> 3 -> 4` | Signs in inline, deploy resumes, succeeds. |
| Sign-in failed loop | `1 -> 2 -> 2.1 -> 2.1.2 -> 2.1` | Blocked until sign-in succeeds. |
| Account mismatch | `1 -> 2 -> 2.1 -> 2.1.3 -> 2.1` | Blocked until the project's account signs in. |
| Not deployed yet | `1 -> 2 -> 3.1 -> 2` | Retry after a first cloud deploy. |
| Missing model key | `1 -> 2 -> 3.2 -> 3.2.1 -> 2 -> 3 -> 4` | Configure key once, retry, succeed. |
| Runtime failed | `1 -> 2 -> 3.3 -> 2` | Retry; no handoff until running. |

## Main Path

### 1 Deploy Request
User entry: the user is in a conversation on a project workspace. User action: asks the agent to "deploy to cloud". Visible UI state: the request appears in the conversation; the agent starts working. Client state change: the agent selects the cloud deploy tool. Exit: `2`.

### 2 Cloud Deploy Tool Running
User entry: from `1`. User action: waits. Visible UI state: a tool card titled `hw_application_harness_cloud_deploy` with a `Running` status pill and a pulsing clock; the card can be expanded to see progress. Client state change: the bundle is uploaded and a cloud runtime is requested. Exit: `2.1` (not signed in), `3` (success), or an error branch (`3.1`/`3.2`/`3.3`).

### 3 Cloud Deploy Completed
User entry: from `2` or a resolved branch. User action: reads the result. Visible UI state: tool card flips to `Completed` (green check) and auto-collapses to a summary showing the project and cloud instance, plus a **Next step** note: "Connect your device to this cloud instance." Client state change: a cloud runtime is running; credentials exist server-side (not shown to the user). Exit: `4`.

### 4 Device Provisioning Handoff (End)
User entry: from `3`. User action: proceeds to connect the device (separate provisioning path) or keeps working. Visible UI state: the Next-step note is the only pointer; the desktop does not open a runtime connection. Client state change: the desktop's deploy job is done. End state.

## Branch Journeys

### 2.1 Sign-in Gate
Trigger: step `2` finds no cloud session (`AUTH_REQUIRED`). Visible UI state: inline amber gate inside the tool card — "Sign in to NoraHarness to deploy to cloud" + `Sign in`, reusing the publish sign-in gate language and style. Allowed actions: `Sign in`, or dismiss. Blocks progress until resolved.

### 2.1.1 Sign-in Success
Trigger: user completes login. Visible UI state: gate turns green "Signed in as <name>"; tool card returns to `Running`. Next state: `3` or an error branch. Does not block.

### 2.1.2 Sign-in Failed
Trigger: login cancelled or errored. Visible UI state: gate stays, short "Sign-in failed — try again" line + `Sign in`. Recovery: retry sign-in. Blocks progress.

### 2.1.3 Account Mismatch
Trigger: signed in, but the account is not the project's owner (`FORBIDDEN`). Visible UI state: "This account can't deploy this project. Sign out and sign back in with the project's account." + `Sign out`. Recovery: sign out, sign in with the correct account. Blocks progress.

### 3.1 Not Deployed Yet
Trigger: `RUNTIME_NOT_DEPLOYED`. Visible UI state: tool card `Error`, "No cloud deployment yet — run a cloud deploy first." Recovery: retry (the deploy path itself creates the deployment). Blocks handoff.

### 3.2 Missing Model Key
Trigger: `LLM_KEY_NOT_CONFIGURED`. Visible UI state: tool card `Error`, "No model key for this project — configure a model key, then retry." + pointer to key setup. Recovery: `3.2.1`. Blocks handoff.

### 3.2.1 Model Key Setup
Trigger: user opens key setup from `3.2`. Visible UI state: the existing provider-key setup surface; on success returns to the conversation. Next state: `2` on retry. Blocks until a key exists.

### 3.3 Runtime Failed
Trigger: `RUNTIME_FAILED` / `RUNTIME_UNREACHABLE`. Visible UI state: tool card `Error`, "Cloud runtime failed to start — retry, or check the model/config." Recovery: retry. Must not hand off (no credentials). Blocks handoff.

## Detail States

- `2 tool card (expanded)`: the running tool card expanded shows upload/ensure progress lines. Same card component as local deploy, cloud variant.
- `3 completed summary`: collapsed success card with project + cloud instance name + Next-step note.

No other detail dialogs are opened by this journey (key setup reuses the existing Providers surface, documented as `3.2.1`).

## State Language

- Tool card statuses (reused from the existing tool card): `Running`, `Completed`, `Error`, plus `Pending` / `Awaiting approval` if the tool requires confirmation.
- Sign-in gate states: `unauthenticated` (amber), `authenticated` (green "Signed in as …"), `sign-in failed`, `account mismatch`.
- No separate cloud runtime status surface exists in this journey (that is a deferred, out-of-scope slice).

## Errors And Recovery

- `2.1.2 Sign-in failed`: retry sign-in; blocks until success.
- `2.1.3 Account mismatch`: sign out + sign in with project's account; blocks until correct account.
- `3.1 Not deployed yet`: retry deploy.
- `3.2 Missing model key` -> `3.2.1`: configure a key once, then retry.
- `3.3 Runtime failed`: retry; no device handoff until the runtime is running.

Every error is shown on the tool card (or the inline gate for auth), with a single clear next action. No error crosses to the user as an opaque failure.

## Wireframe

The HTML wireframe renders: a main happy-path row (steps 1–4) as app-conversation product frames with the cloud deploy tool card; a route-path row per route above; and compact bands for detail states, state language, and errors and recovery. Visual reference: the current NoraHarness desktop (conversation surface + tool cards + right-side tabs), warm cream theme.
