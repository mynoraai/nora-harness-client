# Journey 3: Login / Account Authentication

This record describes the implemented account-authentication journey. It is the source text for the matching wireframe and should stay aligned with the numbered route IDs below.

## Journey Scope

User entry: The user clicks the `Log in` entry in the sidebar, or starts an account-required publishing action while signed out.

User goal: Sign in to NoraHarness/NoraClaw, return to the desktop app with account state visible, and continue any account-required action that was blocked before authentication.

End state: The app is clearly signed in or signed out. Account-required actions can proceed, block for sign-in, or ask the user to sign in again when the session is no longer usable.

Implementation maturity: Implemented.

## State Inventory

Main account path:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main step | Journey 3 | Signed-out app entry. | The user views the sidebar account area or opens a feature that requires an account. | Sidebar footer shows `Log in`; no user name, role badge, or `Sign out` action is shown. | Local account state is empty, or the latest account check returned no signed-in user. | User can start sidebar login via `2`, or hit a publish gate via `1.1`. |
| `2` | Main step | Journey 3 | Login request starts from the app. | The user clicks `Log in` in the sidebar or `Sign in` in the publish gate. | The system browser or auth surface opens. In the sidebar entry, the account area remains signed out until a sign-in result or error arrives. In the publish gate, the dialog stays on the sign-in requirement until auth state changes. | The app requests authentication and waits for the account result, user update, or auth error. | Browser authentication continues via `3`, or request failure/cancel resolves via `2.1`. |
| `3` | Main step | Journey 3 | Browser authentication. | The user completes Google or email/password login in the web auth surface. | The browser shows the Nora login flow. The desktop app continues waiting for the sign-in result. | A successful result delivers the signed-in user to the app. | Success continues to `4`; browser-side failure or cancellation goes to `3.1`. |
| `4` | Main step | Journey 3 | Signed-in account state. | The user returns to the app after successful authentication. | Sidebar footer shows the user name or email, a role badge such as `developer`, and a `Sign out` action. Account-required dialogs can show the signed-in email. | Local account state is set from the sign-in result or refreshed account check. Account-required actions become available. | User can retry/continue a blocked action via `5`, or sign out via `6`. |
| `5` | Main step | Journey 3 | Account-required action continues after sign-in. | The user resumes the publish/review action that was previously blocked, or opens it again while signed in. | The publish dialog moves past sign-in gating into package building, agent loading, metadata entry, submission, or success states. It can show `Signed in as ...`. | The action is allowed to call account-backed catalog services using the current authenticated session. | Success leaves this journey; if the session is rejected or unreadable, moves to `5.1`. |
| `6` | Main step | Journey 3 | User signs out. | The signed-in user clicks `Sign out` from the sidebar account area. | The account footer immediately stops showing the user identity and role. | Local account state is cleared. | Moves to `7`. |
| `7` | Main step | Journey 3 | Signed-out state after sign-out. | The user remains in the app after signing out. | Sidebar footer shows `Log in` again. Account-required publishing surfaces return to sign-in gating. | Account-dependent actions are blocked until a new login completes. | User can start again from `1`. |

Signed-out publish gate:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1.1` | Branch step | `1` | Account-required publish entry while signed out. | The user opens an agent publish/review action that requires a Nora account. | The publish dialog opens in a sign-in checking state, then resolves to a sign-in requirement. | The account-required flow is blocked before package submission or catalog submission begins. | Moves to `1.1.1`. |
| `1.1.1` | Sequential branch | `1.1` | Publish sign-in gate. | The user sees the publish dialog asking them to sign in. | Dialog text says `Sign in to NoraClaw to publish.` with a `Sign in` button. | Publish state remains unauthenticated; the blocked publish action is preserved in the dialog context. | User can start login via `2`, or close/cancel back to `1`. |

Authentication failure and recovery:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Branch step | `2` | Login request fails or is cancelled before a signed-in user is returned. | The user closes the browser/auth surface, the request fails to open, or the app receives an auth error. | The original entry remains signed out. A toast or inline failure message can explain that sign-in did not complete. | Account state remains empty; account-required work stays blocked. | Returns to `1` or `1.1.1` depending on the entry point. |
| `3.1` | Branch step | `3` | Browser authentication does not complete. | The user abandons login or the web auth flow fails before returning a valid account. | The desktop app returns to the original signed-out surface and can show auth failure feedback. | No signed-in user is stored; account-required work remains blocked. | Returns to `1` or `1.1.1` depending on the entry point. |
| `5.1` | Branch step | `5` | Account session becomes unusable during an account-required action. | The user submits or loads publish data after the stored account state has expired or cannot be used. | The publish dialog shows an error with guidance to sign in again, then retry. | The account-required call is rejected as auth-required; the publish action does not continue until authentication is refreshed. | User can re-authenticate via `5.1.1`, or cancel back to the signed-in/signed-out app surface. |
| `5.1.1` | Sequential branch | `5.1` | Re-login from an account-required error. | The user chooses to sign in again after the account-required error. | The same browser auth flow opens again; the publish dialog remains the recovery context. | A new auth request starts while the blocked publish operation waits for a usable session. | Continues through `2 -> 3 -> 4 -> 5`. |

## Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| Sidebar login happy path | `1 -> 2 -> 3 -> 4` | The user signs in from the sidebar. The sidebar switches from `Log in` to user identity, role badge, and `Sign out`. |
| Sidebar login request cancelled or failed | `1 -> 2 -> 2.1 -> 1` | The user remains signed out. The sidebar still shows `Log in`, and the user can retry. |
| Browser authentication cancelled or failed | `1 -> 2 -> 3 -> 3.1 -> 1` | The browser flow does not return a signed-in account. The app returns to the signed-out entry. |
| Publish blocked while signed out | `1 -> 1.1 -> 1.1.1` | The publish/review action does not continue. The dialog asks the user to sign in before publishing. |
| Publish sign-in happy path | `1 -> 1.1 -> 1.1.1 -> 2 -> 3 -> 4 -> 5` | The user signs in from the publish gate, then the publish/review dialog proceeds into the signed-in publishing states. |
| Publish session expired or unreadable | `4 -> 5 -> 5.1` | A signed-in-looking app state cannot complete the account-required call. The dialog asks the user to sign in again and retry. |
| Publish re-login recovery | `4 -> 5 -> 5.1 -> 5.1.1 -> 2 -> 3 -> 4 -> 5` | The user refreshes authentication from the publish error and returns to the blocked publish/review action. |
| Sign out | `4 -> 6 -> 7` | Local account state is cleared. The sidebar returns to `Log in`, and account-required actions become gated again. |
| Sign out then hit publish gate | `4 -> 6 -> 7 -> 1 -> 1.1 -> 1.1.1` | After signing out, publishing again opens the unauthenticated publish gate. |

## Main Path

### 1 Signed-Out App Entry

User entry: the user is in the desktop app without a signed-in account, or an account check returns no current user.

User action: the user can click `Log in` in the sidebar, continue using local-only surfaces, or open an account-required publish/review action.

Visible UI state: the sidebar footer shows `Log in`. It does not show a user name, role badge, avatar, or `Sign out` action.

Client state change: account state remains empty. Account-required surfaces must block before they submit or publish.

Exit / next state: sidebar login moves to `2 Login request starts from the app`; publishing while signed out moves to `1.1 Account-required publish entry while signed out`.

### 2 Login Request Starts From The App

User entry: the user starts login from the sidebar or from the publish sign-in gate.

User action: the user clicks `Log in` or `Sign in`.

Visible UI state: the system browser or authentication surface opens. The sidebar remains in its signed-out presentation until the sign-in result returns. The publish gate remains on the sign-in requirement until the account state changes.

Client state change: the app starts an account sign-in request and waits for a successful account result or an auth error.

Exit / next state: successful browser authentication continues to `3 Browser authentication`; an early failure or cancellation moves to `2.1 Login request fails or is cancelled`.

### 3 Browser Authentication

User entry: the sign-in request has opened the web authentication flow.

User action: the user completes Google login or email/password login.

Visible UI state: the browser shows the Nora login page. The desktop app remains waiting for the account result.

Client state change: a successful login returns a signed-in account to the desktop app.

Exit / next state: success moves to `4 Signed-in account state`; abandoned or failed browser authentication moves to `3.1 Browser authentication does not complete`.

### 4 Signed-In Account State

User entry: the account result returns to the desktop app successfully.

User action: the user can inspect the account footer, continue local work, retry the publish/review action that was blocked, or click `Sign out`.

Visible UI state: the sidebar footer shows the user name or email, a role badge such as `developer`, and `Sign out`. Account-required dialogs can show the signed-in email.

Client state change: the current account state is stored in the app session. Account-required actions become available as long as the session remains usable.

Exit / next state: continuing the blocked publish/review action moves to `5 Account-required action continues after sign-in`; signing out moves to `6 User signs out`.

### 5 Account-Required Action Continues After Sign-In

User entry: the user resumes a publish/review action after sign-in, or opens it while already signed in.

User action: the user continues through package building, agent loading, metadata entry, submission, or review.

Visible UI state: the publish dialog no longer shows the sign-in gate. It can show `Signed in as ...` and then the relevant publish/review state.

Client state change: the account-required action is allowed to use the current signed-in session for catalog/review work.

Exit / next state: success leaves this account journey and continues the publish/review journey. If the session cannot be used, the flow moves to `5.1 Account session becomes unusable during an account-required action`.

### 6 User Signs Out

User entry: the user is signed in and sees the account footer.

User action: the user clicks `Sign out`.

Visible UI state: the sidebar account area immediately stops showing the user identity, role badge, and `Sign out`.

Client state change: local account state is cleared.

Exit / next state: the app moves to `7 Signed-out state after sign-out`.

### 7 Signed-Out State After Sign-Out

User entry: sign-out has completed.

User action: the user can keep working in local surfaces, click `Log in` again, or open an account-required publish/review action.

Visible UI state: the sidebar footer returns to `Log in`. Account-required publishing surfaces return to sign-in gating.

Client state change: account-dependent actions are blocked until a new login completes.

Exit / next state: the user can start again from `1 Signed-out app entry`.

## Branch Journeys

### 1.1 Account-Required Publish Entry While Signed Out

Trigger: the user opens an agent publish/review action while no signed-in account is available.

Visible UI state: the publish dialog opens and checks account state. The dialog then resolves to a sign-in requirement instead of continuing into package submission or catalog submission.

Allowed user actions: wait for the check to resolve, continue to the sign-in gate, or close the dialog.

Recovery / next state: the flow moves to `1.1.1 Publish sign-in gate`.

Blocks progress: yes. Publishing cannot continue until the user signs in.

### 1.1.1 Publish Sign-In Gate

Trigger: the publish dialog determines that the user is not signed in.

Visible UI state: the dialog says `Sign in to NoraClaw to publish.` and shows a `Sign in` button.

Allowed user actions: click `Sign in`, close the dialog, or return to the app without publishing.

Recovery / next state: clicking `Sign in` moves to `2 Login request starts from the app`; closing the dialog returns to `1 Signed-out app entry`.

Blocks progress: yes. The publish/review action remains blocked.

### 2.1 Login Request Fails Or Is Cancelled

Trigger: the sign-in request does not produce a signed-in account because the auth surface is closed, cannot open, or reports an error.

Visible UI state: the original entry remains signed out. The app can show a toast or inline error explaining that sign-in did not complete.

Allowed user actions: click `Log in` or `Sign in` again, close the blocking dialog, or continue using local-only app surfaces.

Recovery / next state: the user returns to `1 Signed-out app entry` or `1.1.1 Publish sign-in gate`, depending on where login started.

Blocks progress: yes for account-required actions; no for local-only app use.

### 3.1 Browser Authentication Does Not Complete

Trigger: the browser login flow is abandoned or fails before returning a valid account to the desktop app.

Visible UI state: the desktop app remains or returns to the signed-out state. If the journey started from publishing, the publish dialog can remain on the sign-in gate.

Allowed user actions: retry sign-in, close the dialog, or continue using local-only app surfaces.

Recovery / next state: the user returns to `1 Signed-out app entry` or `1.1.1 Publish sign-in gate`.

Blocks progress: yes for account-required actions.

### 5.1 Account Session Becomes Unusable During An Account-Required Action

Trigger: the app appears signed in, but the account-required publish/review action cannot use the current session.

Visible UI state: the publish dialog shows an error with guidance to sign in again and retry.

Allowed user actions: sign in again, retry after authentication, close the dialog, or return to the app.

Recovery / next state: choosing sign-in recovery moves to `5.1.1 Re-login from an account-required error`.

Blocks progress: yes. The publish/review action cannot continue until authentication is refreshed.

### 5.1.1 Re-Login From An Account-Required Error

Trigger: the user chooses to sign in again after an account-required error.

Visible UI state: the browser authentication flow opens again. The publish dialog remains the recovery context for the blocked action.

Allowed user actions: complete sign-in, abandon sign-in, or return to the dialog.

Recovery / next state: successful authentication follows `2 -> 3 -> 4 -> 5`, returning the user to the publish/review action.

Blocks progress: temporarily, until a usable signed-in session returns.

## State Language

| Surface | Copy / control language | Meaning |
| --- | --- | --- |
| Sidebar signed-out entry | `Log in` | The app has no current signed-in account. |
| Sidebar signed-in entry | User name or email, role badge, `Sign out` | The app has a current account object and exposes account actions. |
| Publish unauthenticated gate | `Sign in to NoraClaw to publish.` and `Sign in` | The publish/review action is blocked until account authentication succeeds. |
| Publish signed-in context | `Signed in as ...` | The publish/review action can continue with the current account. |
| Auth-required publish error | Guidance to sign in again, then retry | The current account session cannot be used for the account-required cloud action. |

## Errors And Recovery

| Error / branch | Where it appears | Recovery |
| --- | --- | --- |
| Login request fails or is cancelled | Sidebar login entry or publish sign-in gate | Keep the user signed out and let them click `Log in` / `Sign in` again. |
| Browser auth does not complete | Browser auth flow returns no valid account | Return to the original signed-out entry and let the user retry. |
| Publish action starts while signed out | Publish/review dialog | Show the sign-in gate before package submission or catalog submission. |
| Account session is expired, missing, or unreadable during publish | Publish/review dialog error state | Ask the user to sign in again, then retry the publish/review action. |
| User signs out | Sidebar account area | Clear local account state and return account-required actions to login-gated state. |
