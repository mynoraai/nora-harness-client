# Journey 3: Login / Account Authentication

This Markdown file is the text counterpart to `account-login-authentication.wireframe.html`. It documents the current implemented account-authentication journey. Each section ID matches a card in the wireframe.

## Journey Scope

User entry: the user clicks `Log in` from the shared sidebar account footer, opens the full account login page, or starts a publish action that requires a NoraClaw account while signed out.

User goal: sign in, return to the originating app context with account state visible, and continue any account-required action that was blocked before authentication.

End state: the app is clearly signed in or signed out. Account-required actions can continue, block for sign-in, or ask the user to sign in again when the current session is not usable.

Implementation maturity: implemented.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches, details, state language, and recovery cases use decimal IDs scoped to the step where the state starts.

Main path states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 3 | Signed-out entry. | Reviews the sidebar account footer or opens an account-required action. | Sidebar footer shows `Log in`, or the account-required surface begins checking sign-in. | Account state is empty or unresolved for the current action. | `1.1`, `1.2`, or `2` |
| `2` | Main path | Journey 3 | Browser login surface opens from the app. | Chooses Google, enters email/password, creates an account, or uses password recovery. | The standalone account login page or browser auth surface is visible; the original app context waits. | A sign-in request is active, but the app does not show signed-in identity until a valid account returns. | `2.1` or authentication branch |
| `3` | Main path | Journey 3 | Signed-in app context after welcome-back return. | Returns to the app, inspects identity, continues a gated action, or signs out. | Sidebar footer shows user identity, role badge, and `Sign out`; gated dialogs can show `Signed in as ...`. | Current account state is available for account-required actions. | End, `3.2`, `3.3`, or `3.4` |

Entry and account-required gates:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1.1` | Branch journey | `1 Signed-Out Entry` | Sidebar login entry. | Clicks `Log in` in the account footer. | The footer remains signed out while login opens. | The app records the sidebar as the login origin. | `2` |
| `1.2` | Branch journey | `1 Signed-Out Entry` | Publish action starts while signed out. | Clicks `Publish` or another account-required publish/review action. | Publish dialog checks sign-in before package upload or catalog submission. | The publish action is held until account state resolves. | `1.2.1` |
| `1.2.1` | Branch follow-up | `1.2 Publish action starts while signed out` | Publish sign-in gate. | Reviews the sign-in requirement and clicks `Sign in` or closes the dialog. | Dialog says `Sign in to NoraClaw to publish.` and shows a `Sign in` button. | Publish remains unauthenticated; the blocked publish context is preserved. | `2` or `1.4` |
| `1.3` | Detail state | `1 Signed-Out Entry` | Signed-out footer and local-only app behavior. | Continues using Chat, Code, Settings, or other local surfaces without signing in. | Local surfaces remain usable; account-required actions still show sign-in gating. | Account state stays empty without forcing the user out of the app. | `1`, `1.1`, or `1.2` |
| `1.4` | Recovery summary | `1.2.1 Publish sign-in gate` | Publish gate closed or deferred. | Closes the dialog or returns to the app without signing in. | The app returns to the original local context; publishing has not started. | No account session is stored and no package upload is attempted. | `1` |

Authentication branches:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Branch journey | `2 Browser Login` | Account login page. | Reviews sign-in options. | Login page shows `Log in to NoraClaw`, `Continue with Google`, email/password fields, `Continue with Email`, `Forgot your password?`, and `Create account`. | Login form state is active; no account is committed yet. | `2.1.1`, `2.1.2`, `2.1.3`, or `2.1.4` |
| `2.1.1` | Branch follow-up | `2.1 Account login page` | Google sign-in choice. | Clicks `Continue with Google`. | Browser or auth handoff opens; the app waits for completion or cancellation. | Google authentication is requested from the account surface. | `2.2` |
| `2.1.2` | Branch follow-up | `2.1 Account login page` | Email/password sign-in choice. | Enters email and password, then clicks `Continue with Email`. | Email/password fields stay visible; submit becomes meaningful after required fields are present. | Email credentials are submitted to the account flow. | `2.2` or `2.1.2.1` |
| `2.1.2.1` | Branch follow-up | `2.1.2 Email/password sign-in choice` | Email/password validation failure. | Corrects email or password after an inline failure. | Login page shows an inline error, keeps the email available for retry, and does not expose the password. | No signed-in account is stored. | `2.1` |
| `2.1.3` | Branch follow-up | `2.1 Account login page` | Create account entry. | Clicks `Create account`. | Account creation runs in the same account surface or browser-auth context. | A new account request replaces normal sign-in until it succeeds or is abandoned. | `2.2` or `2.1` |
| `2.1.4` | Branch follow-up | `2.1 Account login page` | Forgot password entry. | Clicks `Forgot your password?`. | Password recovery opens without removing the normal sign-in options from the account route. | Recovery flow is active; no account is stored until sign-in completes later. | `2.1` |
| `2.2` | Branch journey | `2 Browser Login` | Waiting for authentication result. | Completes browser/account steps or returns to the app. | The login/auth surface is pending; the originating app context remains the return target. | The app waits for success, cancellation, or failure. | `2.2.1`, `2.2.2`, or `2.2.3` |
| `2.2.1` | Branch follow-up | `2.2 Waiting for authentication result` | Welcome back / authentication success return. | Finishes sign-in and returns to NoraHarness. | The app returns to the sidebar or publish origin instead of showing onboarding again. | A signed-in account object becomes available to the app. | `3` |
| `2.2.2` | Branch follow-up | `2.2 Waiting for authentication result` | Authentication canceled. | Closes the browser/auth surface or backs out before completion. | The original signed-out surface remains available. | Account state stays empty and blocked work remains blocked. | `2.3`, then `1` or `1.2.1` |
| `2.2.3` | Branch follow-up | `2.2 Waiting for authentication result` | Authentication failed. | Reads failure feedback and retries or cancels. | Login page, toast, or dialog feedback explains that sign-in did not complete. | No signed-in account is stored. | `2.3`, then `2.1` or `1.2.1` |
| `2.3` | Recovery summary | `2 Browser Login` | Authentication retry recovery. | Retries sign-in, changes method, or cancels back to the origin. | Retry controls stay near the original login or gate surface. | The app keeps the original return context but does not advance account-required work. | `2.1`, `1`, or `1.2.1` |

Signed-in, sign-out, and account-required continuation states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `3.1` | Branch journey | `3 Signed-In App` | Sidebar signed-in identity. | Reads identity, role, and account action in the sidebar footer. | Footer shows user name or email, a role badge such as `developer`, and `Sign out`. | Account state is set for account-required actions. | End, `3.2`, or `3.3` |
| `3.2` | Branch journey | `3 Signed-In App` | Account-required publish action continues. | Returns to the publish/review action that was blocked or opens it while signed in. | Publish dialog moves beyond sign-in gating into packaging, agent loading, metadata entry, upload, success, or publish error states. | Account-required catalog work can use the current signed-in session. | End or `3.4` |
| `3.3` | Branch journey | `3 Signed-In App` | User signs out. | Clicks `Sign out` from the sidebar account footer. | The footer immediately stops showing identity, role, and `Sign out`. | Local account state is cleared. | `3.3.1` |
| `3.3.1` | Branch follow-up | `3.3 User signs out` | Signed-out state after sign-out. | Continues using the app, logs in again, or opens account-required work. | Sidebar footer returns to `Log in`; publish returns to the sign-in gate when attempted. | Account-dependent actions are blocked until a new login succeeds. | `1` |
| `3.4` | Branch journey | `3 Signed-In App` | Current account session cannot be used for an account-required action. | Attempts to list, submit, upload, or publish after the stored session becomes unusable. | The publish dialog shows an account-required or sign-in-again error instead of continuing upload. | The account-required call is rejected; the blocked action waits for refreshed authentication. | `3.4.1` or `3.6` |
| `3.4.1` | Branch follow-up | `3.4 Current account session cannot be used` | Re-login from an account-required error. | Chooses to sign in again from the publish error. | The same login/auth route opens while the publish dialog remains the recovery context. | A fresh authentication request starts for the blocked action. | `2` |
| `3.5` | Detail state | `3 Signed-In App` | Signed-in local-only continuation. | Keeps using Chat, Code, Settings, or Agent Authoring after sign-in. | The account footer remains signed in while local work continues normally. | Account state stays available without changing local app state. | End, `3.2`, or `3.3` |
| `3.6` | Recovery summary | `3.4 Current account session cannot be used` | Sign-out and session recovery summary. | Signs in again, retries after refresh, cancels the dialog, or signs out. | The user can recover account-required work without losing the originating app context. | Account state becomes refreshed, cleared, or still blocked based on the chosen recovery. | `2`, `3.2`, `3.3.1`, or `1` |

State language:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Account footer language. | Reads or uses sidebar account controls. | Labels include `Log in`, signed-in identity, role badge, and `Sign out`. | Footer language follows signed-out or signed-in account state. | Any state |
| `0.2` | State language | `2 Browser Login` | Login page language. | Chooses an account route. | Labels include `Log in to NoraClaw`, `Continue with Google`, `Continue with Email`, `Forgot your password?`, and `Create account`. | Form language follows the active account route. | `2.1` |
| `0.3` | State language | `1.2 Publish action starts while signed out` | Publish authentication gate language. | Reads the gate, signs in, or closes it. | Labels include `Sign in to NoraClaw to publish.`, `Sign in`, and `Signed in as ...`. | Publish language follows auth checking, signed-out, or signed-in state. | `1.2.1`, `3.2`, or `3.4` |
| `0.4` | State language | Whole journey | Failure and recovery language. | Retries, cancels, signs in again, or returns to the app. | Copy explains cancellation, incomplete sign-in, invalid credentials, or sign-in-again requirements near the originating surface. | Error language does not create a signed-in session by itself. | `2.3`, `3.6`, or origin state |

Route table:

| Route | Composition | Result / next state |
| --- | --- | --- |
| Sidebar Google login | `1 -> 1.1 -> 2 -> 2.1 -> 2.1.1 -> 2.2 -> 2.2.1 -> 3 -> 3.1` | The user signs in from the sidebar with Google and sees signed-in account footer state. |
| Sidebar email login | `1 -> 1.1 -> 2 -> 2.1 -> 2.1.2 -> 2.2 -> 2.2.1 -> 3 -> 3.1` | The user signs in from the sidebar with email/password and sees signed-in account footer state. |
| Email validation retry | `1 -> 1.1 -> 2 -> 2.1 -> 2.1.2 -> 2.1.2.1 -> 2.1 -> 2.1.2 -> 2.2 -> 2.2.1 -> 3 -> 3.1` | The login page keeps the user in context after invalid credentials, then succeeds after retry. |
| Create account from login | `1 -> 1.1 -> 2 -> 2.1 -> 2.1.3 -> 2.2 -> 2.2.1 -> 3 -> 3.1` | The user enters account creation from the login page and returns signed in after completion. |
| Forgot password return | `1 -> 1.1 -> 2 -> 2.1 -> 2.1.4 -> 2.1` | The user uses password recovery and returns to the account login page. |
| Sidebar authentication canceled | `1 -> 1.1 -> 2 -> 2.1 -> 2.1.1 -> 2.2 -> 2.2.2 -> 2.3 -> 1` | The user cancels authentication and remains signed out with local app surfaces available. |
| Sidebar authentication failed then retried | `1 -> 1.1 -> 2 -> 2.1 -> 2.1.1 -> 2.2 -> 2.2.3 -> 2.3 -> 2.1 -> 2.1.2 -> 2.2 -> 2.2.1 -> 3 -> 3.1` | The failed sign-in keeps the user in account recovery, then a later attempt succeeds. |
| Publish blocked while signed out | `1 -> 1.2 -> 1.2.1` | The publish/review action stops before package upload and asks the user to sign in. |
| Publish sign-in happy path | `1 -> 1.2 -> 1.2.1 -> 2 -> 2.1 -> 2.1.1 -> 2.2 -> 2.2.1 -> 3 -> 3.2` | The user signs in from the publish gate and the publish dialog proceeds into signed-in publishing states. |
| Publish gate closed | `1 -> 1.2 -> 1.2.1 -> 1.4 -> 1` | The user defers sign-in; no package upload starts and the app returns to the local context. |
| Already signed-in publish | `3 -> 3.1 -> 3.2` | The user opens publish while signed in and skips the sign-in gate. |
| Publish session unusable with re-login | `3 -> 3.1 -> 3.2 -> 3.4 -> 3.4.1 -> 2 -> 2.1 -> 2.1.2 -> 2.2 -> 2.2.1 -> 3 -> 3.2` | A session problem blocks the account-required action, then re-login returns the user to the publish flow. |
| Sign out | `3 -> 3.1 -> 3.3 -> 3.3.1 -> 1` | Account state is cleared, the sidebar returns to `Log in`, and account-required actions become gated again. |
| Sign out then hit publish gate | `3 -> 3.1 -> 3.3 -> 3.3.1 -> 1 -> 1.2 -> 1.2.1` | After sign-out, publishing again opens the unauthenticated sign-in gate. |

## Main Path

The main happy path visual baseline is the old Journey 3 four-card structure:

1. `Signed-out entry`
2. `Browser login`
3. `Welcome back`
4. `Signed-in app`

Route rows can expand the underlying record IDs, but the HTML `Main happy path` band should keep this four-card `app -> browser -> app` layout.

### 1 Signed-Out Entry

User entry: the user is in NoraHarness without a signed-in account, or an account-required action cannot yet confirm a current user.

User action: the user can click `Log in`, continue local-only app work, or start a publish/review action that requires an account.

Visible UI state:

- The shared sidebar footer shows `Log in`.
- No user name, role badge, avatar, or `Sign out` action is shown.
- If the user opens account-required publishing, the dialog checks sign-in before showing upload or catalog-submission controls.

Client state change: account state remains empty or unresolved for the current action. Local-only Chat, Code, Settings, and authoring surfaces stay available.

Exit / next state: sidebar login moves to `1.1 Sidebar login entry`; account-required publish moves to `1.2 Publish action starts while signed out`; opening the login page directly moves to `2 Browser Login`.

### 2 Browser Login

User entry: the user has chosen to sign in from the sidebar, the login page, or an account-required gate.

User action: the user chooses Google, enters email/password, creates an account, uses password recovery, or cancels authentication.

Visible UI state:

- The account route shows `Log in to NoraClaw`.
- The user can choose `Continue with Google`, fill email/password and click `Continue with Email`, use `Forgot your password?`, or click `Create account`.
- Browser or embedded auth surfaces may appear while the desktop app waits for a result.
- The original Chat, Code, or Publish origin remains the return context.

Client state change: a sign-in request is active. The app does not expose signed-in account state until a valid account result returns.

Exit / next state: sign-in choices start from `2.1 Account login page`; successful authentication reaches `2.2.1 Welcome Back / Authentication Success Return` and then `3 Signed-In App`.

### 3 Signed-In App

User entry: authentication succeeds, or the user opens account-required work while already signed in.

User action: the user can inspect identity, continue local work, resume publish/review, or click `Sign out`.

Visible UI state:

- The sidebar footer shows the user name or email, a role badge such as `developer`, and `Sign out`.
- Account-required dialogs can show `Signed in as ...` before continuing to package, metadata, upload, or success states.
- Local Chat, Code, Settings, and authoring surfaces remain in their current context.

Client state change: account state is set and can be used by account-required actions until it is cleared or rejected.

Exit / next state: continuing publish moves to `3.2 Account-required publish action continues`; signing out moves to `3.3 User signs out`; a rejected session moves to `3.4 Current account session cannot be used`.

## Branch Journeys

### 1.1 Sidebar Login Entry

Trigger: the user clicks `Log in` in the sidebar account footer.

Visible UI state:

- The footer still reads `Log in` until authentication succeeds.
- The login route opens without changing Chat or Code mode.

Allowed user actions: continue into login, return to the app, or use local surfaces if login is abandoned.

Client state change: the app records the sidebar as the return origin for this auth attempt.

Recovery / next state: login continues to `2 Browser Login`.

Blocks progress: no for local-only app use; yes for account-required actions.

### 1.2 Publish Action Starts While Signed Out

Trigger: the user starts a publish or review action that requires an account while no signed-in account is available.

Visible UI state:

- Publish dialog opens from the current authoring context.
- The dialog checks account state before package upload, metadata submission, or catalog submission.
- The sign-in gate appears instead of publish controls when the check resolves as signed out.

Allowed user actions: wait for sign-in check, continue to the gate, sign in, or close the dialog.

Client state change: the blocked publish context is preserved but no account-required submission begins.

Recovery / next state: the flow moves to `1.2.1 Publish sign-in gate`.

Blocks progress: yes for publishing.

### 2.1 Account Login Page

Trigger: authentication opens the account route.

Visible UI state:

- Page title says `Log in to NoraClaw`.
- The page offers `Continue with Google`.
- Email and password fields are visible with `Continue with Email`.
- `Forgot your password?` and `Create account` are visible.
- The page is an account login surface, not onboarding and not a settings panel.

Allowed user actions: choose Google, enter email/password, open create-account, open password recovery, or return to the originating app context.

Client state change: login form state is active; no account is stored yet.

Recovery / next state: Google moves to `2.1.1 Google sign-in choice`; email/password moves to `2.1.2 Email/password sign-in choice`; create-account moves to `2.1.3 Create account entry`; password recovery moves to `2.1.4 Forgot password entry`.

Blocks progress: yes for account-required actions until authentication succeeds.

### 2.1.1 Google Sign-In Choice

Trigger: the user clicks `Continue with Google`.

Visible UI state:

- Browser or auth handoff opens.
- The desktop app waits for the account result.
- The user can abandon the auth surface and return to the login origin.

Allowed user actions: complete Google authentication, cancel the browser/auth surface, or return to the app.

Client state change: a Google auth request is active.

Recovery / next state: waiting moves to `2.2 Waiting for authentication result`.

Blocks progress: temporarily, while the app waits for auth result.

### 2.1.2 Email/Password Sign-In Choice

Trigger: the user fills email/password and clicks `Continue with Email`.

Visible UI state:

- Email and password fields stay in the account page.
- Submit is meaningful only after required fields are present.
- Password entry stays hidden.

Allowed user actions: submit, edit fields, switch to Google, use recovery, create account, or return to the origin.

Client state change: email/password credentials are submitted to the auth flow.

Recovery / next state: successful submission moves to `2.2 Waiting for authentication result`; invalid credentials move to `2.1.2.1 Email/password validation failure`.

Blocks progress: temporarily.

### 2.1.3 Create Account Entry

Trigger: the user clicks `Create account` from the login page.

Visible UI state:

- Account creation opens in the account route or browser auth context.
- The user can complete account creation or return to normal login.

Allowed user actions: complete account creation, cancel, or return to login.

Client state change: account creation is active; no signed-in account is committed until completion returns a valid account.

Recovery / next state: completed account creation moves to `2.2 Waiting for authentication result`; cancellation returns to `2.1 Account login page`.

Blocks progress: temporarily.

### 2.1.4 Forgot Password Entry

Trigger: the user clicks `Forgot your password?`.

Visible UI state:

- Password recovery opens from the account route.
- Normal login remains the return path.

Allowed user actions: request recovery, return to login, or cancel to the origin.

Client state change: recovery state is active; no signed-in account is stored.

Recovery / next state: returns to `2.1 Account login page` when the user exits recovery.

Blocks progress: yes for account-required actions until later sign-in succeeds.

### 2.2 Waiting For Authentication Result

Trigger: Google, email/password, or account creation has been submitted and the app is waiting for completion.

Visible UI state:

- Browser or account auth surface is pending.
- The desktop app preserves the return origin.
- Publish gate or sidebar entry does not switch to signed-in state until the account result returns.

Allowed user actions: finish authentication, cancel the auth surface, or return to the origin if the auth surface allows it.

Client state change: pending auth state is active.

Recovery / next state: success moves to `2.2.1 Welcome Back / Authentication Success Return`; cancellation moves to `2.2.2 Authentication canceled`; failure moves to `2.2.3 Authentication failed`.

Blocks progress: temporarily.

### 2.2.1 Welcome Back / Authentication Success Return

Trigger: the account flow returns a valid signed-in user.

Visible UI state:

- The app returns to the originating sidebar or publish context.
- First-run onboarding does not reappear.
- The account footer or gated dialog updates to signed-in state.

Allowed user actions: continue local work, continue publish, or sign out.

Client state change: account state is set from the successful auth result.

Recovery / next state: moves to `3 Signed-In App`.

Blocks progress: no.

### 3.1 Sidebar Signed-In Identity

Trigger: account state is available after authentication or account refresh.

Visible UI state:

- Sidebar footer shows the user name or email.
- A role badge such as `developer` can be visible.
- `Sign out` is available from the account footer.

Allowed user actions: continue work, open account-required publish, or sign out.

Client state change: account state is available to account-required features.

Recovery / next state: account-required work moves to `3.2 Account-required publish action continues`; sign-out moves to `3.3 User signs out`.

Blocks progress: no.

### 3.2 Account-Required Publish Action Continues

Trigger: the user resumes publish after sign-in or opens publish while already signed in.

Visible UI state:

- The publish dialog no longer shows the sign-in gate.
- It can show `Signed in as ...`, packaging progress, package digest, agent loading, metadata entry, upload, success, or publish errors.
- The surrounding authoring context remains intact.

Allowed user actions: continue publish, cancel, retry publish-specific errors, or sign in again if the session is rejected.

Client state change: account-required catalog work can use the current signed-in session.

Recovery / next state: successful publish leaves this journey; unusable session moves to `3.4 Current account session cannot be used`.

Blocks progress: no unless a later account check rejects the session.

### 3.3 User Signs Out

Trigger: the signed-in user clicks `Sign out` from the sidebar account footer.

Visible UI state:

- The account footer immediately stops showing user identity, role badge, and `Sign out`.
- The app remains on the current Chat, Code, Settings, or authoring surface.

Allowed user actions: continue local work, log in again, or open account-required actions.

Client state change: local account state is cleared.

Recovery / next state: moves to `3.3.1 Signed-out state after sign-out`.

Blocks progress: only for later account-required actions.

### 3.3.1 Signed-Out State After Sign-Out

Trigger: sign-out completes.

Visible UI state:

- Sidebar footer returns to `Log in`.
- Account-required publishing surfaces return to sign-in gating.
- Local-only work remains visible.

Allowed user actions: continue local work, click `Log in`, or attempt publishing again.

Client state change: account-dependent actions are blocked until a new login succeeds.

Recovery / next state: returns to `1 Signed-Out Entry`.

Blocks progress: yes for account-required actions.

## Detail States

### 1.3 Signed-Out Footer And Local-Only App Behavior

Trigger: the user remains signed out and keeps working locally.

Visible UI state:

- Sidebar footer continues to show `Log in`.
- Chat, Code, Settings, and authoring work stay available when they do not require account services.
- Account-required entries still resolve to a sign-in gate.

Allowed user actions: continue local work, click `Log in`, or open an account-required entry.

Client state change: no account object is stored.

Recovery / next state: login starts through `1.1 Sidebar login entry`; publish starts through `1.2 Publish action starts while signed out`.

Blocks progress: only for account-required actions.

### 3.5 Signed-In Local-Only Continuation

Trigger: the user is signed in and continues using local app surfaces instead of immediately publishing.

Visible UI state:

- Chat, Code, Settings, and authoring surfaces keep their current state.
- Sidebar account footer remains signed in.
- Account-required actions are available when later opened.

Allowed user actions: continue work, start publish, or sign out.

Client state change: account state stays available without replacing the current app surface.

Recovery / next state: publish moves to `3.2 Account-required publish action continues`; sign-out moves to `3.3 User signs out`.

Blocks progress: no.

## State Language

### 0.1 Account Footer Language

Trigger: the user reads or uses account controls in the shared sidebar footer.

Visible UI state: signed-out state uses `Log in`; signed-in state shows identity, a role badge such as `developer`, and `Sign out`.

Allowed user actions: start login, inspect identity, sign out, or continue local app work.

Client state change: footer copy follows the current account state.

Recovery / next state: any account state can route to login, signed-in continuation, or sign-out.

Blocks progress: no by itself.

### 0.2 Login Page Language

Trigger: the account login page is visible.

Visible UI state: the page uses `Log in to NoraClaw`, `Continue with Google`, `Continue with Email`, `Forgot your password?`, and `Create account`.

Allowed user actions: choose a method, recover password, create an account, or cancel.

Client state change: form language follows the active sign-in route.

Recovery / next state: routes remain under `2.1 Account login page`.

Blocks progress: yes for account-required actions until a valid account returns.

### 0.3 Publish Authentication Gate Language

Trigger: publishing needs an account or has just received a usable account.

Visible UI state: signed-out publish uses `Sign in to NoraClaw to publish.` and `Sign in`; signed-in publish can show `Signed in as ...`.

Allowed user actions: sign in, close the gate, or continue publish when signed in.

Client state change: publish language follows auth checking, signed-out, signed-in, and rejected-session states.

Recovery / next state: signed-out gate routes to `1.2.1 Publish sign-in gate`; signed-in context routes to `3.2 Account-required publish action continues`.

Blocks progress: yes while the gate is signed out.

### 0.4 Failure And Recovery Language

Trigger: login fails, login is canceled, credentials are invalid, or an account-required call asks for sign-in again.

Visible UI state: recovery copy appears near the active login page, sidebar origin, or publish dialog instead of replacing the whole app.

Allowed user actions: retry, switch method, sign in again, close the dialog, or continue local work.

Client state change: failure copy does not create a signed-in account; account-required actions remain blocked until recovery succeeds.

Recovery / next state: authentication failures route through `2.3 Authentication retry recovery`; session recovery routes through `3.6 Sign-out and session recovery summary`.

Blocks progress: yes for account-required actions while unresolved.

## Errors And Recovery

### 1.2.1 Publish Sign-In Gate

Trigger: publish sign-in check resolves with no current account.

Visible UI state:

- Dialog says `Sign in to NoraClaw to publish.`
- The primary action is `Sign in`.
- The current authoring or publish origin remains visible around the dialog.

Allowed user actions: click `Sign in`, close the dialog, or return to the app without publishing.

Client state change: publish remains unauthenticated; no package upload starts.

Recovery / next state: `Sign in` moves to `2 Browser Login`; closing moves to `1.4 Publish gate closed or deferred`.

Blocks progress: yes.

### 1.4 Publish Gate Closed Or Deferred

Trigger: the user closes the publish sign-in gate or decides not to sign in.

Visible UI state:

- The publish dialog closes or returns to the authoring context.
- No upload, package submission, or catalog state appears.
- Sidebar remains signed out.

Allowed user actions: resume local work, reopen publish, or start login from the sidebar.

Client state change: the blocked publish attempt ends without storing account state.

Recovery / next state: returns to `1 Signed-Out Entry`.

Blocks progress: yes for the abandoned publish action.

### 2.1.2.1 Email/Password Validation Failure

Trigger: the email/password sign-in attempt fails local validation or returns an invalid-credentials result.

Visible UI state:

- Login page shows inline failure near the form.
- Email remains available for correction.
- Password can be re-entered without exposing the previous value.

Allowed user actions: correct fields, retry, switch method, use password recovery, or cancel.

Client state change: no signed-in account is stored.

Recovery / next state: correcting input returns to `2.1 Account login page`.

Blocks progress: yes for account-required actions.

### 2.2.2 Authentication Canceled

Trigger: the user closes or abandons the auth surface before a valid account returns.

Visible UI state:

- The app returns to the original signed-out entry or remains on the login page.
- If authentication started from publish, the publish dialog can remain on the sign-in gate.
- The sidebar still shows `Log in`.

Allowed user actions: retry sign-in, close the gate, return to login, or continue local-only work.

Client state change: no account state is stored.

Recovery / next state: moves through `2.3 Authentication retry recovery` to `1 Signed-Out Entry` or `1.2.1 Publish sign-in gate`.

Blocks progress: yes for account-required actions.

### 2.2.3 Authentication Failed

Trigger: the auth surface reports failure instead of returning a valid account.

Visible UI state:

- The login page, toast, or gated dialog explains that sign-in did not complete.
- Retry remains available from the same origin.

Allowed user actions: retry, choose another sign-in method, close the dialog, or continue local-only work.

Client state change: no account state is stored.

Recovery / next state: moves through `2.3 Authentication retry recovery` to `2.1 Account login page` or `1.2.1 Publish sign-in gate`.

Blocks progress: yes for account-required actions.

### 2.3 Authentication Retry Recovery

Trigger: sign-in was canceled, failed, or returned without a usable account.

Visible UI state:

- The recovery message stays near the login surface, sidebar origin, or publish gate.
- Retry does not erase the app context that launched login.
- Local-only app use remains available when the user leaves the auth flow.

Allowed user actions: retry, switch sign-in method, close the publish gate, or return to the app.

Client state change: the original return context is preserved, but account state stays empty.

Recovery / next state: retry returns to `2.1 Account login page`; cancel returns to `1 Signed-Out Entry` or `1.2.1 Publish sign-in gate`.

Blocks progress: yes for account-required actions.

### 3.4 Current Account Session Cannot Be Used

Trigger: the app appears signed in, but an account-required publish/review action cannot use the current session.

Visible UI state:

- Publish dialog shows an account-required or sign-in-again error.
- The dialog does not continue upload or submission until authentication is refreshed.
- The originating publish context remains visible.

Allowed user actions: sign in again, cancel, retry after refresh, or sign out.

Client state change: the account-required call is rejected and the blocked action waits for a usable session.

Recovery / next state: choosing sign-in recovery moves to `3.4.1 Re-login from an account-required error`; cancel or sign-out moves through `3.6 Sign-out and session recovery summary`.

Blocks progress: yes.

### 3.4.1 Re-Login From An Account-Required Error

Trigger: the user chooses to refresh authentication from an account-required error.

Visible UI state:

- The same account login route opens again.
- The publish dialog remains the recovery context.
- The user can use Google, email/password, create-account, or password recovery paths as available.

Allowed user actions: complete sign-in, cancel, or return to the publish error.

Client state change: a new auth request starts for the blocked action.

Recovery / next state: successful re-login follows `2 Browser Login` and returns to `3.2 Account-required publish action continues`.

Blocks progress: temporarily.

### 3.6 Sign-Out And Session Recovery Summary

Trigger: account state is cleared, rejected, or refreshed while the user is working.

Visible UI state:

- Sign-out returns the footer to `Log in`.
- Session rejection keeps the account-required dialog near the blocked action.
- Re-login returns the user to the original context after success.

Allowed user actions: sign in again, retry after refresh, close the gate, sign out, or continue local work.

Client state change: account state becomes refreshed, cleared, or still unavailable based on the recovery action.

Recovery / next state: re-login starts at `2 Browser Login`; refreshed publish returns to `3.2 Account-required publish action continues`; cleared state returns to `1 Signed-Out Entry`.

Blocks progress: yes only for account-required actions while account state is unavailable.

## Wireframe

The matching HTML wireframe must keep six first-level canvas bands in this order: `Main happy path`, `Route map`, `Branch journeys`, `Detail states`, `State language`, and `Errors and recovery`.

The `Main happy path` band must preserve the old Journey 3 visual baseline as four product cards: `Signed-out entry -> Browser login -> Welcome back -> Signed-in app`. It should not be expanded into the long route composition from the Route Table.

The `Route map` band includes one full-size route row for each route in the route table. Route rows do not replace the separate Branch, Detail, State Language, or Errors bands.

The `Branch journeys` band contains the branch IDs documented under `## Branch Journeys`.

The `Detail states` band contains `1.3` and `3.5`.

The `State language` band contains `0.1`, `0.2`, `0.3`, and `0.4`.

The `Errors and recovery` band contains `1.2.1`, `1.4`, `2.1.2.1`, `2.2.2`, `2.2.3`, `2.3`, `3.4`, `3.4.1`, and `3.6`.
