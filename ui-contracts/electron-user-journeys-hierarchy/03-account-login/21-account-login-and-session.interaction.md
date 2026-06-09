# Journey 21 - Account Login And Session

This file is the text description for the account login HTML wireframe.

- Source HTML: [21-account-login-and-session.wireframe.html](21-account-login-and-session.wireframe.html)
- Screenshot reference: [login-page.jpg](../../assets/screenshots/login-page.jpg)

## Goal

A user can sign in from the product shell, authenticate with Google or email, return to the app with account state visible, and recover from common authentication interruptions.

## Entry And Preconditions

- Entry points include the `Log in` footer action in the Chat/Code shell and authentication gates in publish or account-required flows.
- The login screen is a full-window account page, not a marketing page and not a settings panel.
- The current screenshot confirms the login form layout, copy, Google action, email/password fields, forgot-password link, and create-account link.

## Main Path

1. User opens Log in from the app shell or an auth gate.
   - User sees: the current app context remains the origin, and the next screen is account login.
   - Required UI: Log in entry, account-required reason when launched from a gated action.
2. User lands on the login page.
   - User sees: `Log in to NoraClaw`, `Continue with Google`, email/password fields, `Continue with Email`, `Forgot your password?`, and `Create account`.
   - Required UI: single centered form, no sidebar, no onboarding navigation.
3. User chooses Google.
   - User sees: browser/auth handoff or waiting state, with a clear way to cancel and return to the app.
   - Verification point: needs current screenshot of the OAuth handoff or in-app waiting state.
4. User signs in with email.
   - User sees: focused email/password inputs, disabled submit until required fields are present, and inline error on failure.
   - Required UI: password remains hidden, retry does not clear email.
5. User returns authenticated.
   - User sees: the original Chat/Code/Publish context restored and account footer changed from `Log in` to signed-in account state.
   - Verification point: needs current screenshot of the signed-in account footer/menu.

## Recovery States

- Invalid credentials: inline error near the form; email remains filled; password can be retried.
- OAuth canceled: user returns to the login page with `Continue with Google` still available.
- Session expired: user keeps local conversations/workspace context but account-required actions are gated until login succeeds again.
- Create account: same account surface, with create-account entry clearly linked from the login page.
- Forgot password: recovery entry is available without hiding the normal sign-in controls.

## End State

- User is authenticated and returned to the originating product context.
- Account-gated actions can continue without pretending the user completed onboarding again.
- Reviewers can distinguish confirmed login-page UI from follow-up states that still need screenshots.
