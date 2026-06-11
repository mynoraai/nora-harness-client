# Electron User Journeys - Current Hierarchy

Last updated: 2026-06-10

This folder is the canonical Electron user journey workspace for the current implemented NoraHarness flows. It follows `electron-user-journeys-hierarchy-v2/user-journey-inventory.md` and keeps only the currently explicit level-1 journeys.

## Alignment

- This hierarchy records established interaction behavior and current client states only.
- Mobile Preview remains available as a secondary Agent Authoring journey, not a primary inventory journey.
- Firmware Publish is folded into **09. Unified Publish Center / Catalog Review / Firmware Publish**.
- Cloud Deploy right-panel tab behavior and Settings Extensions / ACP Runtime Management are marked as removed.
- Scheduled Tasks, Channels, Voice, and Advanced Config are outside the current main journey set.

## Current Journey Table

| Journey | Status | HTML | Interaction | Video reference |
| --- | --- | --- | --- | --- |
| 01. 首次启动 / Onboarding | current | [HTML](01-first-launch/01-first-launch-onboarding.wireframe.html) | [MD](01-first-launch/01-first-launch-onboarding.interaction.md) | onboarding2.mov |
| 02. 主应用启动 / 恢复状态 | current | [HTML](02-main-app-startup/02-main-app-startup-recovery.wireframe.html) | [MD](02-main-app-startup/02-main-app-startup-recovery.interaction.md) | - |
| 03. 登录 / 账户认证 | current | [HTML](03-account-login/03-account-login-authentication.wireframe.html) | [MD](03-account-login/03-account-login-authentication.interaction.md) | login.mov |
| 04. Chat 模式 | current | [HTML](04-chat-mode/04-chat-mode-conversation.wireframe.html) | [MD](04-chat-mode/04-chat-mode-conversation.interaction.md) | - |
| 05. Code 模式 / 工作区 | current | [HTML](05-code-workspace/05-code-workspace-start-session.wireframe.html) | [MD](05-code-workspace/05-code-workspace-start-session.interaction.md) | create-workspace->coding.mov |
| 06. Runtime / Model / Permission | current | [HTML](06-runtime-model-permission/06-runtime-model-permission.wireframe.html) | [MD](06-runtime-model-permission/06-runtime-model-permission.interaction.md) | create-workspace->coding.mov |
| 07. Code 工作台面板 | current | [HTML](07-code-workbench-panels/07-code-workbench-panels.wireframe.html) | [MD](07-code-workbench-panels/07-code-workbench-panels.interaction.md) | create-workspace->coding.mov |
| 08. Agent Authoring | current | [HTML](08-agent-authoring/08-agent-authoring.wireframe.html) | [MD](08-agent-authoring/08-agent-authoring.interaction.md) | user9-update-agent-updateapp-infopublish.mov |
| 09. 统一发布中心 / Catalog Review / Firmware Publish | current | [HTML](09-publish-center/09-unified-publish-center.wireframe.html) | [MD](09-publish-center/09-unified-publish-center.interaction.md) | user9-update-agent-updateapp-infopublish.mov |
| 10. Device / PlatformIO / Logs | current | [HTML](10-device-platformio-logs/10-device-platformio-logs.wireframe.html) | [MD](10-device-platformio-logs/10-device-platformio-logs.interaction.md) | preview-lvgl.mov |
| 11. LVGL Preview | current | [HTML](11-lvgl-preview/11-lvgl-preview.wireframe.html) | [MD](11-lvgl-preview/11-lvgl-preview.interaction.md) | preview-lvgl.mov |
| 12. Settings - General | current | [HTML](12-settings-general/12-settings-general.wireframe.html) | [MD](12-settings-general/12-settings-general.interaction.md) | - |
| 13. Settings - Workspace Files | current | [HTML](13-settings-workspace-files/13-settings-workspace-files.wireframe.html) | [MD](13-settings-workspace-files/13-settings-workspace-files.interaction.md) | - |
| 14. Settings - Providers | current | [HTML](14-settings-providers/14-settings-providers.wireframe.html) | [MD](14-settings-providers/14-settings-providers.interaction.md) | onboarding2.mov |
| 15. Settings - Permissions | current | [HTML](15-settings-permissions/15-settings-permissions.wireframe.html) | [MD](15-settings-permissions/15-settings-permissions.interaction.md) | - |
| 16. Settings - About / Updates | current | [HTML](16-settings-about-updates/16-settings-about-updates.wireframe.html) | [MD](16-settings-about-updates/16-settings-about-updates.interaction.md) | user9-update-agent-updateapp-infopublish.mov |

## Files

- [Interactive hierarchy map](index.wireframe.html)
- [Hierarchy interaction description](index.interaction.md)
- [Journey hierarchy table](journey-hierarchy.md)

## Boundary Register

| Surface | Status | Notes |
| --- | --- | --- |
| Mobile Preview | secondary | Exists as Agent Authoring `AUTH-04`; not a primary inventory journey. |
| Cloud Deploy right-panel tab | removed | Do not remap through the unified publish entry. |
| Settings Extensions / ACP Runtime Management | removed | Settings extension ACP module is deleted from the current hierarchy. |
