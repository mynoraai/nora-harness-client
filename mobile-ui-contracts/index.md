# Mobile UI User Journeys

This document defines the HH Mobile Chat mobile user journeys. Product, design, engineering, and QA should use it as the shared contract for first launch, authentication, onboarding, chat, agent management, device diagnostics, and settings behavior.

Each journey document describes the user's goal, the visible screen state, and the expected system transition. Screenshots illustrate the journey moments; the journey contract is the user intent and product behavior.

## Journey Index

| ID     | Journey                    | Reference screens                           | Contract                                                                      |
| ------ | -------------------------- | ------------------------------------------- | ----------------------------------------------------------------------------- |
| MOB-01 | Sign-in and first launch   | `1-home`, `2-apple-login`, `3-google-login` | [Sign-in](./sign-in/sign-in.md)                                               |
| MOB-02 | New-device onboarding      | `4-onboarding`                              | [Onboarding](./onboarding/onboarding.md)                                      |
| MOB-03 | Existing-device onboarding | `6-onboarding-existing-device`              | [Existing Device](./onboarding-existing-device/onboarding-existing-device.md) |
| MOB-04 | Chat                       | `7-chat`                                    | [Chat](./chat/chat.md)                                                        |
| MOB-05 | Agents                     | `8-agents-page`                             | [Agents](./agents/agents.md)                                                  |
| MOB-06 | Device                     | `9-device-page`                             | [Device](./device/device.md)                                                  |
| MOB-07 | Settings                   | `10-setting-page`                           | [Settings](./settings/settings.md)                                            |

## Document Structure

- Journey IDs are stable labels for QA scripts, design review, and hardware harness notes.
- Each journey is written as user action, visible screen, and expected next state.
- Screenshots support the flow explanation and should stay aligned with the described user journey.
- Failure and recovery states are part of the user journey, not separate implementation notes.

## Implementation Anchors

| Area                        | Primary mobile surfaces                                                                                                                                                 |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Sign-in and onboarding      | `apps/hh-mobile-chat/src/pages/OnboardingPage.tsx`                                                                                                                      |
| Provisioning and BLE bridge | `apps/hh-mobile-chat/src/services/provisioning.ts`, `apps/hh-mobile-chat/plugins/capacitor-hh-provisioning/ios/Sources/HHProvisioningPlugin/HHProvisioningPlugin.swift` |
| Install/runtime polling     | `apps/hh-mobile-chat/src/services/install-runtime.ts`, `apps/hh-mobile-chat/src/services/cloud-device-api.ts`                                                           |
| Device runtime API          | `apps/hh-mobile-chat/src/services/device-api.ts`                                                                                                                        |
| Main app shell              | `apps/hh-mobile-chat/src/App.tsx`                                                                                                                                       |

## Contract Coverage

| ID     | Happy path                                         | Loading path                 | Empty/error path                      | Permission path                |
| ------ | -------------------------------------------------- | ---------------------------- | ------------------------------------- | ------------------------------ |
| MOB-01 | Google sign-in reaches BLE loading                 | Google loading state         | Apple account unavailable dialog      | Notification permission prompt |
| MOB-02 | Scan, pair, select agent, configure Wi-Fi, success | Agent check, install runtime | BLE unavailable, install failed       | BLE capability surface         |
| MOB-03 | Existing agent selected and reinstalled            | Agent refresh and reinstall  | Agent loading failed, reinstall error | Not applicable                 |
| MOB-04 | Text chat and voice send                           | Agent response pending       | Not captured                          | Voice permission dialogs       |
| MOB-05 | Agent list, detail, installed state                | Agent list loading           | Search empty, install failed          | Not applicable                 |
| MOB-06 | Device overview, health, logs                      | Not captured                 | Not captured                          | Not applicable                 |
| MOB-07 | Account, agent, device, about settings             | Agent reload                 | Could not load agents                 | Pair another device            |
