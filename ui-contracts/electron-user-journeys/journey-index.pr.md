## Summary

- Add PM artifacts for Electron desktop user journeys.
- Decision to unblock: confirm how PM should review the 20 numbered Electron user journeys and which are active scope.
- Scope: mixed.

## PM Links

- [PM doc](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/journey-index.pm.md)
- [Wireframe preview - Journey 01](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/01-first-launch-and-base-setup.wireframe.html)
- [Wireframe source - Journey 01](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/01-first-launch-and-base-setup.wireframe.html)

## Alignment Check

- PM audience: product owner, design PM, and release PM.
- Discuss: priority, labels, entry points, required states, and active versus removed journey scope.
- Do not discuss: internal implementation details or low-level runtime mechanics.

## Files

- `ui-contracts/electron-user-journeys/journey-index.pm.md`
- `ui-contracts/electron-user-journeys/01-first-launch-and-base-setup.wireframe.html`
- `ui-contracts/electron-user-journeys/02-main-app-startup-gateway-status-update-prompt.wireframe.html`
- `ui-contracts/electron-user-journeys/03-chat-mode-conversation.wireframe.html`
- `ui-contracts/electron-user-journeys/04-create-code-workspace-and-start-code-session.wireframe.html`
- `ui-contracts/electron-user-journeys/05-code-mode-runtime-models-permissions.wireframe.html`
- `ui-contracts/electron-user-journeys/06-code-workspace-files-diff-terminal-right-panel.wireframe.html`
- `ui-contracts/electron-user-journeys/07-agent-authoring.wireframe.html`
- `ui-contracts/electron-user-journeys/08-publish-agent-review-edit-catalog-info.wireframe.html`
- `ui-contracts/electron-user-journeys/09-mobile-preview.wireframe.html`
- `ui-contracts/electron-user-journeys/10-firmware-publish.wireframe.html`
- `ui-contracts/electron-user-journeys/11-cloud-deploy-removed.wireframe.html`
- `ui-contracts/electron-user-journeys/12-device-status-platformio-device-logs.wireframe.html`
- `ui-contracts/electron-user-journeys/13-lvgl-preview.wireframe.html`
- `ui-contracts/electron-user-journeys/14-settings-general.wireframe.html`
- `ui-contracts/electron-user-journeys/15-settings-workspace-files.wireframe.html`
- `ui-contracts/electron-user-journeys/16-settings-providers-default-model.wireframe.html`
- `ui-contracts/electron-user-journeys/17-settings-extensions-acp-runtime-management-removed.wireframe.html`
- `ui-contracts/electron-user-journeys/18-settings-permissions.wireframe.html`
- `ui-contracts/electron-user-journeys/19-settings-about-and-updates.wireframe.html`
- `ui-contracts/electron-user-journeys/20-scheduled-tasks-cron-jobs.wireframe.html`

## Verification

- Opened the static HTML wireframes locally and verified the canvases render.
- Ran `git diff --check`.
