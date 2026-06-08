# Electron User Journeys PM Review

## PM Context

### Current Work

This package splits the Electron desktop user journeys into 20 standalone wireframes so PM can review each journey independently while keeping the numbered source journey list intact.

### Current User-Visible Behavior

The desktop app already exposes several implemented or partially implemented areas: onboarding, chat, code workspaces, provider setup, agent authoring, publishing, mobile preview, hardware status, LVGL preview, settings, and scheduled-task-adjacent concepts. Some journeys are intentionally marked as removed from this review because they should not be presented as active product surfaces.

### Implementation Maturity

`mixed`

- Current user-visible evidence: multiple flows exist today in partial or implemented form, but not every journey is release-ready as a polished end-to-end experience.
- Proposed assumptions: the split wireframes propose PM-facing interaction structure and state language where the app behavior is not yet complete.
- Dependencies: publish/review, mobile preview, firmware release, hardware device support, LVGL preview, and scheduled tasks depend on their owning product/runtime surfaces staying aligned.

### Discussion Focus

Review the Electron user journeys as 20 independent PM artifacts, with special attention to journeys that are partial, proposed, or intentionally removed.

### Discussion Boundaries

- PM should decide: journey priority, labels, entry points, required states, and whether each proposed flow is in scope for the next milestone.
- PM should not debate: internal implementation details, source file layout, or low-level runtime mechanics in this review.

### PM Decision Points

<table>
<tr><td bgcolor="#fff3cd"><strong>PM decision:</strong> Should these remain 20 one-journey artifacts, or should PM review use a smaller executive index plus per-journey detail pages?</td></tr>
<tr><td bgcolor="#fff3cd"><strong>PM decision:</strong> Which journeys should be promoted to release-blocking UX review for the next milestone?</td></tr>
<tr><td bgcolor="#f8d7da"><strong>Blocking PM decision:</strong> Confirm journeys 11 and 17 are removal markers, not active UX surfaces for this review.</td></tr>
</table>

### Proposed Design

Each numbered journey has one HTML wireframe. The files share a consistent canvas shell with zoom, pan, fit, jump list, main path, detail states, error/recovery frames, and PM decision frames. Journeys 11 and 17 are explicit removal markers so reviewers know they were accounted for and intentionally excluded from active UX design.

### Wireframe Index

- Journey 01: [First Launch And Base Setup](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/01-first-launch-and-base-setup.wireframe.html) - source: [ui-contracts/electron-user-journeys/01-first-launch-and-base-setup.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/01-first-launch-and-base-setup.wireframe.html)
- Journey 02: [Main App Startup, Gateway Status, Update Prompt](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/02-main-app-startup-gateway-status-update-prompt.wireframe.html) - source: [ui-contracts/electron-user-journeys/02-main-app-startup-gateway-status-update-prompt.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/02-main-app-startup-gateway-status-update-prompt.wireframe.html)
- Journey 03: [Chat Mode Conversation](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/03-chat-mode-conversation.wireframe.html) - source: [ui-contracts/electron-user-journeys/03-chat-mode-conversation.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/03-chat-mode-conversation.wireframe.html)
- Journey 04: [Create Code Workspace And Start Code Session](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/04-create-code-workspace-and-start-code-session.wireframe.html) - source: [ui-contracts/electron-user-journeys/04-create-code-workspace-and-start-code-session.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/04-create-code-workspace-and-start-code-session.wireframe.html)
- Journey 05: [Code Mode Runtime, Models, Permissions](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/05-code-mode-runtime-models-permissions.wireframe.html) - source: [ui-contracts/electron-user-journeys/05-code-mode-runtime-models-permissions.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/05-code-mode-runtime-models-permissions.wireframe.html)
- Journey 06: [Code Workspace Files, Diff, Terminal, Right Panel](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/06-code-workspace-files-diff-terminal-right-panel.wireframe.html) - source: [ui-contracts/electron-user-journeys/06-code-workspace-files-diff-terminal-right-panel.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/06-code-workspace-files-diff-terminal-right-panel.wireframe.html)
- Journey 07: [Agent Authoring](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/07-agent-authoring.wireframe.html) - source: [ui-contracts/electron-user-journeys/07-agent-authoring.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/07-agent-authoring.wireframe.html)
- Journey 08: [Publish Agent, Review, Edit Catalog Info](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/08-publish-agent-review-edit-catalog-info.wireframe.html) - source: [ui-contracts/electron-user-journeys/08-publish-agent-review-edit-catalog-info.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/08-publish-agent-review-edit-catalog-info.wireframe.html)
- Journey 09: [Mobile Preview](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/09-mobile-preview.wireframe.html) - source: [ui-contracts/electron-user-journeys/09-mobile-preview.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/09-mobile-preview.wireframe.html)
- Journey 10: [Firmware Publish](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/10-firmware-publish.wireframe.html) - source: [ui-contracts/electron-user-journeys/10-firmware-publish.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/10-firmware-publish.wireframe.html)
- Journey 11: [Cloud Deploy - Removed From User Journey Review](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/11-cloud-deploy-removed.wireframe.html) - source: [ui-contracts/electron-user-journeys/11-cloud-deploy-removed.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/11-cloud-deploy-removed.wireframe.html)
- Journey 12: [Device Status, PlatformIO, Device Logs](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/12-device-status-platformio-device-logs.wireframe.html) - source: [ui-contracts/electron-user-journeys/12-device-status-platformio-device-logs.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/12-device-status-platformio-device-logs.wireframe.html)
- Journey 13: [LVGL Preview](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/13-lvgl-preview.wireframe.html) - source: [ui-contracts/electron-user-journeys/13-lvgl-preview.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/13-lvgl-preview.wireframe.html)
- Journey 14: [Settings - General](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/14-settings-general.wireframe.html) - source: [ui-contracts/electron-user-journeys/14-settings-general.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/14-settings-general.wireframe.html)
- Journey 15: [Settings - Workspace Files](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/15-settings-workspace-files.wireframe.html) - source: [ui-contracts/electron-user-journeys/15-settings-workspace-files.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/15-settings-workspace-files.wireframe.html)
- Journey 16: [Settings - Providers And Default Model](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/16-settings-providers-default-model.wireframe.html) - source: [ui-contracts/electron-user-journeys/16-settings-providers-default-model.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/16-settings-providers-default-model.wireframe.html)
- Journey 17: [Settings - Extensions And ACP Runtime Management Removed](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/17-settings-extensions-acp-runtime-management-removed.wireframe.html) - source: [ui-contracts/electron-user-journeys/17-settings-extensions-acp-runtime-management-removed.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/17-settings-extensions-acp-runtime-management-removed.wireframe.html)
- Journey 18: [Settings - Permissions](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/18-settings-permissions.wireframe.html) - source: [ui-contracts/electron-user-journeys/18-settings-permissions.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/18-settings-permissions.wireframe.html)
- Journey 19: [Settings - About And Updates](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/19-settings-about-and-updates.wireframe.html) - source: [ui-contracts/electron-user-journeys/19-settings-about-and-updates.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/19-settings-about-and-updates.wireframe.html)
- Journey 20: [Scheduled Tasks / Cron Jobs Priority Journey](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/20-scheduled-tasks-cron-jobs.wireframe.html) - source: [ui-contracts/electron-user-journeys/20-scheduled-tasks-cron-jobs.wireframe.html](https://github.com/mynoraai/nora-harness-client/blob/main/ui-contracts/electron-user-journeys/20-scheduled-tasks-cron-jobs.wireframe.html)

### Benefits

- PM can review one journey at a time without navigating a giant canvas.
- Engineering and design can prioritize partial/proposed journeys without losing the full numbered map.
- Removed journeys are visible as intentional exclusions, reducing review confusion.

### Downsides / Tradeoffs

- Twenty HTML files are heavier to maintain than five grouped canvases.
- Cross-journey consistency now depends on keeping the shared template aligned when future edits happen.
- PM may still want a smaller executive summary for milestone planning.

### Open Questions

- Which of the 20 journeys should be treated as release-blocking for the next desktop milestone?
- Should Scheduled Tasks use that label publicly, or should the product use Reminders or Automations?
- Should removed journeys remain as placeholder HTML files in future revisions, or only be tracked in the PM index?
