# 11. LVGL Preview

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: preview-lvgl.mov
HTML wireframe: [11-lvgl-preview.wireframe.html](11-lvgl-preview.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Code 工作台打开 Preview tab 或 LVGL Preview 面板。

## User Behavior

用户启动 preview，触发 build/capture/rebuild，查看截图结果、错误和日志。

## Client State Changes

- Preview tab 打开时检查 LVGL preview 能力、依赖和工作区状态。
- 不可用时展示缺失依赖或不可运行原因并禁用启动。
- 点击启动或 rebuild 后进入 building/capturing，显示进度或 loading overlay。
- 成功后截图区域从空状态切到最新 preview 图片，并显示时间、尺寸、来源 metadata。
- 失败时显示 error、日志摘要和重试；旧截图按规则保留或替换。

## Recovery And Blocking States

- **Preview unavailable**: 禁用启动并说明原因。
- **Build failed**: 显示日志摘要和 retry。

## End State

用户看到最新 LVGL 截图，或看到明确失败原因和可重试动作。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 录屏缩略图显示 Preview tab 选中，右侧为 LVGL 9.5.0 / 320x240 / C source preview、Ready badge、刷新按钮、预览截图、File/Size/Captured/SHA 和 Build log。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
