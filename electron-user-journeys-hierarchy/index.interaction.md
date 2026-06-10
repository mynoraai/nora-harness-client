# Electron User Journeys - Hierarchical Map

This file is the text description for the hierarchy index HTML. It explains how level-1 journeys contain the original 20 level-2 journeys.

- Source HTML: [index.wireframe.html](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/index.wireframe.html)

## Goal

Level 1 journeys are user goals or product areas. Level 2 journeys preserve the original 20 numbered inventory items under those goals, including removed and not-current surfaces. Journey 21 is a newly identified missing login/account journey.

## Level 1 To Level 2 Map

### First Launch

- Description: A new user completes onboarding before the normal Chat/Code shell appears.
- Status: current
- Level-2 journey: 01. First Launch Onboarding - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/01-first-launch/01-first-launch-and-base-setup.wireframe.html), [MD](01-first-launch/01-first-launch-and-base-setup.interaction.md) (current)

### Main App Startup

- Description: A returning user opens the app, sees gateway health, and resumes the last useful mode.
- Status: current
- Level-2 journey: 02. Startup And Gateway Status - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/02-main-app-startup/02-main-app-startup-gateway-status.wireframe.html), [MD](02-main-app-startup/02-main-app-startup-gateway-status.interaction.md) (current)

### Account Login

- Description: A user signs in, creates an account, restores authenticated state, and recovers when authentication is interrupted.
- Status: current screenshot reference / needs follow-up visual verification
- Level-2 journey: 21. Account Login And Session - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/03-account-login/21-account-login-and-session.wireframe.html), [MD](03-account-login/21-account-login-and-session.interaction.md) (current screenshot reference)

### Chat

- Description: A user creates or resumes a conversation, chooses model/runtime, sends messages, and recovers from blocked states.
- Status: current / needs visual verification
- Level-2 journey: 03. Chat Mode Conversation - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/03-chat/03-chat-mode-conversation.wireframe.html), [MD](03-chat/03-chat-mode-conversation.interaction.md) (needs visual verification)

### Code Workspace

- Description: A developer opens a local workspace, starts code sessions, manages runtime/model/permission state, and reviews files, diffs, terminal output, and panels.
- Status: current / needs visual verification
- Level-2 journey: 04. Create Code Workspace And Start Code Session - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/04-code-workspace/04-create-code-workspace-and-start-code-session.wireframe.html), [MD](04-code-workspace/04-create-code-workspace-and-start-code-session.interaction.md) (needs visual verification)
- Level-2 journey: 05. Code Mode Runtime, Models, And Permissions - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/04-code-workspace/05-code-mode-runtime-models-permissions.wireframe.html), [MD](04-code-workspace/05-code-mode-runtime-models-permissions.interaction.md) (needs visual verification)
- Level-2 journey: 06. Code Workspace Files, Diff, Terminal, Right Panel - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/04-code-workspace/06-code-workspace-files-diff-terminal-right-panel.wireframe.html), [MD](04-code-workspace/06-code-workspace-files-diff-terminal-right-panel.interaction.md) (needs visual verification)

### Agent Authoring

- Description: A developer edits agent/package content and reaches a publish-ready state.
- Status: current / needs visual verification
- Level-2 journey: 07. Agent Authoring - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/05-agent-authoring/07-agent-authoring.wireframe.html), [MD](05-agent-authoring/07-agent-authoring.interaction.md) (needs visual verification)

### Publish And Release

- Description: An author publishes package metadata and manages firmware release artifacts where applicable.
- Status: needs verification
- Level-2 journey: 08. Publish Agent, Review, Edit Catalog Info - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/06-publish-preview-release/08-publish-agent-review-edit-catalog-info.wireframe.html), [MD](06-publish-preview-release/08-publish-agent-review-edit-catalog-info.interaction.md) (needs visual verification)
- Level-2 journey: 10. Firmware Publish - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/06-publish-preview-release/10-firmware-publish.wireframe.html), [MD](06-publish-preview-release/10-firmware-publish.interaction.md) (needs visual verification)

### Hardware Device Work

- Description: A hardware developer checks device readiness, logs, PlatformIO state, and LVGL preview behavior.
- Status: needs verification
- Level-2 journey: 12. Device Status, PlatformIO, Device Logs - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/07-hardware-device/12-device-status-platformio-device-logs.wireframe.html), [MD](07-hardware-device/12-device-status-platformio-device-logs.interaction.md) (needs visual verification)
- Level-2 journey: 13. LVGL Preview - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/07-hardware-device/13-lvgl-preview.wireframe.html), [MD](07-hardware-device/13-lvgl-preview.interaction.md) (needs visual verification)

### Settings

- Description: A user manages app settings, workspace files, providers/default models, permissions, and updates.
- Status: current / needs visual verification
- Level-2 journey: 14. Settings - General - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/08-settings/14-settings-general.wireframe.html), [MD](08-settings/14-settings-general.interaction.md) (needs visual verification)
- Level-2 journey: 15. Settings - Workspace Files - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/08-settings/15-settings-workspace-files.wireframe.html), [MD](08-settings/15-settings-workspace-files.interaction.md) (needs visual verification)
- Level-2 journey: 16. Settings - Providers And Default Model - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/08-settings/16-settings-providers-default-model.wireframe.html), [MD](08-settings/16-settings-providers-default-model.interaction.md) (needs visual verification)
- Level-2 journey: 18. Settings - Permissions - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/08-settings/18-settings-permissions.wireframe.html), [MD](08-settings/18-settings-permissions.interaction.md) (needs visual verification)
- Level-2 journey: 19. Settings - About And Updates - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/08-settings/19-settings-about-and-updates.wireframe.html), [MD](08-settings/19-settings-about-and-updates.interaction.md) (needs visual verification)

### Scheduled Tasks / Automations

- Description: A priority concept for reminders, recurring jobs, and conversation-derived task drafts; not yet a confirmed current UI surface.
- Status: not current surface
- Level-2 journey: 20. Scheduled Tasks / Cron Jobs - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/09-scheduled-tasks/20-scheduled-tasks-cron-jobs.wireframe.html), [MD](09-scheduled-tasks/20-scheduled-tasks-cron-jobs.interaction.md) (not current surface)

### Removed / Historical Surfaces

- Description: Numbered slots retained to show removed surfaces that should not be drawn as active journeys.
- Status: removed
- Level-2 journey: 09. Mobile Preview - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/06-publish-preview-release/09-mobile-preview.wireframe.html), [MD](06-publish-preview-release/09-mobile-preview.interaction.md) (document hidden; related product code retained)
- Level-2 journey: 11. Cloud Deploy - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/10-removed-historical/11-cloud-deploy-removed.wireframe.html), [MD](10-removed-historical/11-cloud-deploy-removed.interaction.md) (removed)
- Level-2 journey: 17. Settings - Extensions / ACP Runtime Management - [HTML](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/codex/electron-user-journey-wireframes/ui-contracts/electron-user-journeys-hierarchy/10-removed-historical/17-settings-extensions-acp-runtime-management-removed.wireframe.html), [MD](10-removed-historical/17-settings-extensions-acp-runtime-management-removed.interaction.md) (removed)

## End State

- Reviewers can enter all original 20 level-2 HTML wireframes plus the newly identified login journey from the level-1 map and distinguish current, needs-verification, not-current, and removed journeys.
