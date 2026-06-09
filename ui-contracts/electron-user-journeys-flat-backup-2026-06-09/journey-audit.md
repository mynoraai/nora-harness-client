# Electron User Journey Audit

Last updated: 2026-06-09

This audit tracks whether each numbered journey is a current product journey, a removed/historical placeholder, or a not-current proposed surface. It is the working checklist for bringing the HTML wireframes and Markdown summary back into alignment with the actual Electron product.

Status labels:

- `current`: the journey exists in current product/docs and can be represented as a current interaction.
- `needs visual verification`: the journey is documented, but the HTML still needs to be checked against current screenshots/product UI before it is treated as authoritative.
- `removed`: the numbered slot is retained only to record that the journey is not an active user surface.
- `not current surface`: the journey describes a desired or priority concept that is not yet a current UI surface.

| Journey | Status | HTML state | Markdown state | Evidence / Next Check |
| --- | --- | --- | --- | --- |
| 01 First Launch Onboarding | current | Updated to product-shaped onboarding frames; no Base Folder step. | Updated to match onboarding flow. | `ui-contracts/onboarding/onboarding-flow.md` and onboarding screenshots. |
| 02 Main App Startup, Gateway Status, Update Prompt | current | Updated to product-shaped startup/chat/code frames. | Needs final wording pass after visual review. | `ui-contracts/assets/screenshots/chat-empty-composer.jpg`, `code-workspace-runtime-layout.jpg`. |
| 03 Chat Mode Conversation | needs visual verification | Summary cards only; canvas interaction fixed, but not yet product-shaped. | Current journey summary exists. | Check chat screenshots/contracts before redrawing. |
| 04 Create Code Workspace And Start Code Session | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check Code mode empty/workspace state. |
| 05 Code Mode Runtime, Models, And Permissions | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check runtime selector, model selector, permission prompt states. |
| 06 Code Workspace Files, Diff, Terminal, And Right Panel | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check current Code workspace screenshot and right panel behavior. |
| 07 Agent Authoring | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check agent authoring screenshot/contracts. |
| 08 Publish Agent, View Review, And Edit Catalog Info | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check publish screenshot and catalog docs. |
| 09 Mobile Preview | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check whether current Electron UI exposes this as a live path. |
| 10 Firmware Publish | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check current firmware publish ownership and UI entry point. |
| 11 Cloud Deploy | removed | Removal placeholder only; canvas interaction fixed. | Marked removed. | Confirm no active Cloud Deploy UI should be represented. |
| 12 Device Status, PlatformIO, And Device Logs | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check hardware/device status UI references. |
| 13 LVGL Preview | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check whether LVGL preview is current, hidden, or planned. |
| 14 Settings - General | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check settings screenshots/contracts. |
| 15 Settings - Workspace Files | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check Settings Workspace docs and current UI. |
| 16 Settings - Providers And Default Model | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check provider settings against onboarding/provider contracts. |
| 17 Settings - Extensions / ACP Runtime Management | removed | Removal placeholder only; canvas interaction fixed. | Marked removed. | Confirm runtime-management settings are not current user surface. |
| 18 Settings - Permissions | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check permission settings and prompt behavior. |
| 19 Settings - About And Updates | needs visual verification | Summary cards only; canvas interaction fixed. | Current journey summary exists. | Check About/update prompt screenshots or current UI. |
| 20 Scheduled Tasks / Cron Jobs | not current surface | Concept summary only; canvas interaction fixed. | Documented as priority concept, not current UI. | Confirm whether it should stay in journey inventory as future/prioritized work. |

Today’s working rule: do not add new product steps to a journey unless a current screenshot, contract, or code path supports them. If the evidence is missing, keep the HTML as a verification placeholder and mark the evidence gap here.
