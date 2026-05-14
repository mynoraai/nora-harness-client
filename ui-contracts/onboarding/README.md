# Onboarding Contracts

Source rows: `ONB-01` through `ONB-05`

## What This Covers

This folder covers the first-run flow users see before Chat or Code is available. It follows the path from app boot into onboarding, through provider setup, personality setup, notification choice, and the final handoff into the first usable chat.

Do not put Settings provider management here. Onboarding may reuse provider APIs, but the Settings provider editor belongs to `settings/`.

## First-Time Reading Order

1. Start with `onboarding-flow.md#purpose` to understand why the flow exists and where it sits before Chat/Code.
2. Read `onboarding-flow.md#core-responsibilities` to see which component handles each step.
3. Follow `onboarding-flow.md#step-by-step-reader-guide` before using the interaction table.
4. Use the table below as the checklist that maps every `ONB-*` row back to source evidence and test coverage.

## Contract Split

| Row      | Contract file        | User-visible surface                                                                                                   | Primary evidence                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Test mapping              |
| -------- | -------------------- | ---------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| `ONB-01` | `onboarding-flow.md` | App boot gate into onboarding                                                                                          | `apps/electron/src/renderer/src/App.tsx:224`; `apps/electron/src/renderer/src/components/onboarding/OnboardingFlow.tsx:77`; `apps/electron/src/renderer/src/components/onboarding/WelcomeStep.tsx:59`; `apps/electron/src/renderer/src/components/onboarding/WelcomeStep.tsx:62`                                                                                                                                                                                                                                                                                                                                                                                                                                     | L2 partial; L3 no test    |
| `ONB-02` | `onboarding-flow.md` | Provider cards, credentials or local endpoint, model, verify/test, skip verification, validation state, Back, Continue | `apps/electron/src/renderer/src/components/onboarding/ProviderStep.tsx:295`; `apps/electron/src/renderer/src/components/onboarding/ProviderStep.tsx:454`; `apps/electron/src/renderer/src/components/onboarding/ProviderStep.tsx:519`; `apps/electron/src/renderer/src/components/onboarding/ProviderStep.tsx:545`; `apps/electron/src/renderer/src/components/onboarding/ProviderStep.tsx:568`; `apps/electron/src/renderer/src/components/onboarding/ProviderStep.tsx:597`; `apps/electron/src/renderer/src/components/onboarding/ProviderStep.tsx:608`; `apps/electron/src/renderer/src/components/onboarding/ProviderStep.tsx:622`; `apps/electron/src/renderer/src/components/onboarding/OnboardingFlow.tsx:46` | L2 partial; L3 no test    |
| `ONB-03` | `onboarding-flow.md` | Tone cards, custom instructions, Markdown preview, Skip, Save & Continue, saving/error states                          | `apps/electron/src/renderer/src/components/onboarding/PersonalityStep.tsx:196`; `apps/electron/src/renderer/src/components/onboarding/PersonalityStep.tsx:203`; `apps/electron/src/renderer/src/components/onboarding/PersonalityStep.tsx:236`; `apps/electron/src/renderer/src/components/onboarding/PersonalityStep.tsx:276`; `apps/electron/src/renderer/src/components/onboarding/PersonalityStep.tsx:288`; `apps/electron/src/renderer/src/components/onboarding/PersonalityStep.tsx:315`                                                                                                                                                                                                                       | L3 no test                |
| `ONB-04` | `onboarding-flow.md` | Maybe later, Turn on notifications, native permission bridge; either choice advances                                   | `apps/electron/src/renderer/src/components/onboarding/NotificationsStep.tsx:80`; `apps/electron/src/renderer/src/components/onboarding/NotificationsStep.tsx:91`; `apps/electron/src/renderer/src/components/onboarding/OnboardingFlow.tsx:68`; `apps/electron/src/renderer/src/components/onboarding/OnboardingFlow.tsx:110`                                                                                                                                                                                                                                                                                                                                                                                        | L3 no test                |
| `ONB-05` | `onboarding-flow.md` | Start chatting from completion and land in IDE                                                                         | `apps/electron/src/renderer/src/components/onboarding/CompletionStep.tsx:40`; `apps/electron/src/renderer/src/components/onboarding/CompletionStep.tsx:47`; `apps/electron/src/renderer/src/App.tsx:217`; `apps/electron/src/renderer/src/App.tsx:219`; `apps/electron/src/renderer/src/App.tsx:221`; `apps/electron/src/renderer/src/App.tsx:246`                                                                                                                                                                                                                                                                                                                                                                   | L1/L2 partial; L3 no test |

## Detail Files

- `onboarding-flow.md` covers the complete first-run flow from app boot to first usable chat.

Each file must include the visible step state, valid/invalid user actions, the renderer state transition, IPC/RPC path when present, and test gaps.

## E2E Scenarios

Use `ONB-*` as scenario IDs:

| Scenario                              | Expected E2E path                                                         |
| ------------------------------------- | ------------------------------------------------------------------------- |
| `ONB-01..05 first run`                | Launch from `npm run dev:electron`, complete onboarding, land in Chat.    |
| `ONB-02 provider validation failure`  | Enter invalid credentials or missing model and verify visible validation. |
| `ONB-04 notification permission skip` | Skip notification permission and verify completion is not blocked.        |
