# Electron User Journeys - Hierarchical Draft

Last updated: 2026-06-09

This folder reorganizes the flat 20-file journey set into level 1 user-goal journeys and level 2 interaction journeys. Journey 21 is a newly identified missing login/account journey. The original flat folder is intentionally left untouched; a backup copy exists at `../electron-user-journeys-flat-backup-2026-06-09`.

## Structure

- Level 1 journey: a real user goal or product area.
- Level 2 journey: a specific interaction path, state cluster, removed slot, or not-current concept under that goal.
- Removed and not-current surfaces are separated from current journeys so they are not reviewed as active product UI.

## Hierarchy

| Level 1 Journey | Level 2 Journey | Status | HTML |
| --- | --- | --- | --- |
| 01. First Launch | 01. First Launch Onboarding | `current` | [HTML](01-first-launch/01-first-launch-and-base-setup.wireframe.html) |
| 02. Main App Startup | 02. Startup And Gateway Status | `current` | [HTML](02-main-app-startup/02-main-app-startup-gateway-status.wireframe.html) |
| 03. Account Login | 21. Account Login And Session | `current screenshot reference` | [HTML](03-account-login/21-account-login-and-session.wireframe.html) |
| 04. Chat | 03. Chat Mode Conversation | `needs visual verification` | [HTML](03-chat/03-chat-mode-conversation.wireframe.html) |
| 05. Code Workspace | 04. Create Code Workspace And Start Code Session | `needs visual verification` | [HTML](04-code-workspace/04-create-code-workspace-and-start-code-session.wireframe.html) |
| 05. Code Workspace | 05. Code Mode Runtime, Models, And Permissions | `needs visual verification` | [HTML](04-code-workspace/05-code-mode-runtime-models-permissions.wireframe.html) |
| 05. Code Workspace | 06. Code Workspace Files, Diff, Terminal, Right Panel | `needs visual verification` | [HTML](04-code-workspace/06-code-workspace-files-diff-terminal-right-panel.wireframe.html) |
| 06. Agent Authoring | 07. Agent Authoring | `needs visual verification` | [HTML](05-agent-authoring/07-agent-authoring.wireframe.html) |
| 07. Publish And Release | 08. Publish Agent, Review, Edit Catalog Info | `needs visual verification` | [HTML](06-publish-preview-release/08-publish-agent-review-edit-catalog-info.wireframe.html) |
| 07. Publish And Release | 10. Firmware Publish | `needs visual verification` | [HTML](06-publish-preview-release/10-firmware-publish.wireframe.html) |
| 08. Hardware Device Work | 12. Device Status, PlatformIO, Device Logs | `needs visual verification` | [HTML](07-hardware-device/12-device-status-platformio-device-logs.wireframe.html) |
| 08. Hardware Device Work | 13. LVGL Preview | `needs visual verification` | [HTML](07-hardware-device/13-lvgl-preview.wireframe.html) |
| 09. Settings | 14. Settings - General | `needs visual verification` | [HTML](08-settings/14-settings-general.wireframe.html) |
| 09. Settings | 15. Settings - Workspace Files | `needs visual verification` | [HTML](08-settings/15-settings-workspace-files.wireframe.html) |
| 09. Settings | 16. Settings - Providers And Default Model | `needs visual verification` | [HTML](08-settings/16-settings-providers-default-model.wireframe.html) |
| 09. Settings | 18. Settings - Permissions | `needs visual verification` | [HTML](08-settings/18-settings-permissions.wireframe.html) |
| 09. Settings | 19. Settings - About And Updates | `needs visual verification` | [HTML](08-settings/19-settings-about-and-updates.wireframe.html) |
| 10. Scheduled Tasks / Automations | 20. Scheduled Tasks / Cron Jobs | `not current surface` | [HTML](09-scheduled-tasks/20-scheduled-tasks-cron-jobs.wireframe.html) |
| 11. Removed / Historical Surfaces | 09. Mobile Preview | `document hidden; code retained` | [HTML](06-publish-preview-release/09-mobile-preview.wireframe.html) |
| 11. Removed / Historical Surfaces | 11. Cloud Deploy | `removed` | [HTML](10-removed-historical/11-cloud-deploy-removed.wireframe.html) |
| 11. Removed / Historical Surfaces | 17. Settings - Extensions / ACP Runtime Management | `removed` | [HTML](10-removed-historical/17-settings-extensions-acp-runtime-management-removed.wireframe.html) |

## Files

- [Interactive hierarchy map](index.wireframe.html)
- [Journey hierarchy table](journey-hierarchy.md)

## Interaction Text Descriptions

Each HTML wireframe has a matching `.interaction.md` file in the same directory. The interaction file is the text version of the wireframe: user goal, entry/preconditions, main path, states, verification points, and end state.

- [Hierarchy interaction description](index.interaction.md)

## Working Rule

Do not treat the flat list of 20 files as 20 first-level user journeys. Keep the original IDs as level 2 inventory IDs while reviewing whether each belongs to a current product journey, removed slot, or future concept.
