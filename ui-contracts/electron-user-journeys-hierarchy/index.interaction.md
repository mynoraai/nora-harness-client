# Electron User Journeys - Current Hierarchy Map

This is the text description for `index.wireframe.html`.

The hierarchy follows the v2 inventory and contains only current explicit Electron user journeys. Historical, future, or removed surfaces are excluded from the active map.

### 01. 首次启动 / Onboarding

- Entry: 首次安装并打开 NoraHarness，本地尚未完成 onboarding。
- End state: 主应用侧边栏和 Chat/Code 主界面可见；后续启动不再显示 onboarding，除非用户重置数据。
- Files: [HTML](01-first-launch/01-first-launch-onboarding.wireframe.html), [MD](01-first-launch/01-first-launch-onboarding.interaction.md)

### 02. 主应用启动 / 恢复状态

- Entry: 已完成 onboarding 后再次启动应用，或 renderer 刷新/重启。
- End state: 停在可操作主界面；用户能继续上次会话、新建会话或处理 gateway/更新恢复动作。
- Files: [HTML](02-main-app-startup/02-main-app-startup-recovery.wireframe.html), [MD](02-main-app-startup/02-main-app-startup-recovery.interaction.md)

### 03. 登录 / 账户认证

- Entry: 用户从侧边栏账户入口点击登录，或从其他账户依赖功能触发认证。
- End state: 用户处于已登录或未登录的明确状态；所有账户依赖入口按状态继续、拦截或要求重新登录。
- Files: [HTML](03-account-login/03-account-login-authentication.wireframe.html), [MD](03-account-login/03-account-login-authentication.interaction.md)

### 04. Chat 模式

- Entry: 用户进入默认 Chat 模式，或从模式切换器切回 Chat。
- End state: 会话停在完成、停止或失败的 turn 后；用户可继续发送、重试、切换会话或离开 Chat。
- Files: [HTML](04-chat-mode/04-chat-mode-conversation.wireframe.html), [MD](04-chat-mode/04-chat-mode-conversation.interaction.md)

### 05. Code 模式 / 工作区

- Entry: 用户从模式切换器点击 Code，或从已有 Code workspace/session 进入。
- End state: 用户停在已打开的 Code session；工作区、活动 tab、工作目录和 composer 均可继续工作。
- Files: [HTML](05-code-workspace/05-code-workspace-start-session.wireframe.html), [MD](05-code-workspace/05-code-workspace-start-session.interaction.md)

### 06. Runtime / Model / Permission

- Entry: 用户在 Chat 或 Code composer 打开 runtime/model 选择器，或发送消息时触发 runtime/provider/permission 检查。
- End state: 运行时、model 和权限选择在界面上清楚可见；turn 完成、可恢复、可重试或等待批准。
- Files: [HTML](06-runtime-model-permission/06-runtime-model-permission.wireframe.html), [MD](06-runtime-model-permission/06-runtime-model-permission.interaction.md)

### 07. Code 工作台面板

- Entry: 用户在 Code session 中查看右侧工作台和底部面板。
- End state: 用户停在某个工作台视图；文件、diff、terminal 或日志状态与当前 Code session 对齐。
- Files: [HTML](07-code-workbench-panels/07-code-workbench-panels.wireframe.html), [MD](07-code-workbench-panels/07-code-workbench-panels.interaction.md)

### 08. Agent Authoring

- Entry: 用户在 Code 工作区点击 Edit Agent，或从 agent 配置相关入口进入。
- End state: agent 配置处于已保存、保存失败待处理或未保存改动状态；用户可回 Code 或继续发布。
- Files: [HTML](08-agent-authoring/08-agent-authoring.wireframe.html), [MD](08-agent-authoring/08-agent-authoring.interaction.md)

### 09. 统一发布中心 / Catalog Review / Firmware Publish

- Entry: 用户在 Agent Authoring 点击 Publish，或从发布状态行进入发布中心。
- End state: App record、agent package 和 firmware 在同一中心内有清晰的未提交、审核、通过、拒绝、撤销、可发布、已发布或失败状态。
- Files: [HTML](09-publish-center/09-unified-publish-center.wireframe.html), [MD](09-publish-center/09-unified-publish-center.interaction.md)

### 10. Device / PlatformIO / Logs

- Entry: 用户在 Code 工作台打开 Device tab 或 Device Log 面板。
- End state: 用户能确认设备、PlatformIO 和日志链路是 ready、missing、disconnected 还是 error。
- Files: [HTML](10-device-platformio-logs/10-device-platformio-logs.wireframe.html), [MD](10-device-platformio-logs/10-device-platformio-logs.interaction.md)

### 11. LVGL Preview

- Entry: 用户在 Code 工作台打开 Preview tab 或 LVGL Preview 面板。
- End state: 用户看到最新 LVGL 截图，或看到明确失败原因和可重试动作。
- Files: [HTML](11-lvgl-preview/11-lvgl-preview.wireframe.html), [MD](11-lvgl-preview/11-lvgl-preview.interaction.md)

### 12. Settings - General

- Entry: 用户从应用设置入口打开 Settings，并停留在 General tab。
- End state: 通用设置保存成功或停在可重试错误；Gateway 当前状态可见。
- Files: [HTML](12-settings-general/12-settings-general.wireframe.html), [MD](12-settings-general/12-settings-general.interaction.md)

### 13. Settings - Workspace Files

- Entry: 用户在 Settings 中打开 Workspace Files tab。
- End state: 用户完成查看、保存、重置，或停在有未保存改动的编辑状态。
- Files: [HTML](13-settings-workspace-files/13-settings-workspace-files.wireframe.html), [MD](13-settings-workspace-files/13-settings-workspace-files.interaction.md)

### 14. Settings - Providers

- Entry: 用户在 Settings 中打开 Providers tab，或从 provider 缺失提示跳转。
- End state: Provider 配置 ready，或明确提示缺失认证、验证失败、没有默认 model 等待处理。
- Files: [HTML](14-settings-providers/14-settings-providers.wireframe.html), [MD](14-settings-providers/14-settings-providers.interaction.md)

### 15. Settings - Permissions

- Entry: 用户在 Settings 中打开 Permissions tab，或从运行中的权限/系统权限提示跳转。
- End state: 权限页展示当前执行策略和系统权限最终状态；后续运行按权限自动执行、请求批准或阻止。
- Files: [HTML](15-settings-permissions/15-settings-permissions.wireframe.html), [MD](15-settings-permissions/15-settings-permissions.interaction.md)

### 16. Settings - About / Updates

- Entry: 用户在 Settings 中打开 About / Updates tab，或点击主界面更新提示。
- End state: 用户确认最新版、完成下载等待安装，或停在可重试更新错误。
- Files: [HTML](16-settings-about-updates/16-settings-about-updates.wireframe.html), [MD](16-settings-about-updates/16-settings-about-updates.interaction.md)

## Boundary Register

| Surface | Status | Notes |
| --- | --- | --- |
| Mobile Preview | secondary | Agent Authoring `AUTH-04` remains a secondary one-time QR preview journey, not a primary inventory journey. |
| Cloud Deploy right-panel tab | removed | Deleted right-panel tab interaction; do not correct it through the unified publish entry. |
| Settings Extensions / ACP Runtime Management | removed | Deleted Settings extension ACP module. |
