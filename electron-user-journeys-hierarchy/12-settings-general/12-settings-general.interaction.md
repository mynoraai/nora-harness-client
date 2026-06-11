# 12. Settings - General

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: not provided
HTML wireframe: [12-settings-general.wireframe.html](12-settings-general.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户从应用设置入口打开 Settings，并停留在 General tab。

## User Behavior

用户查看 Gateway status，重启 Gateway，配置 proxy、login item、dock、debug tools 等通用设置。

## Client State Changes

- General tab 加载当前本地设置和 gateway 状态。
- Gateway 从 loading 到 running/stopped/restarting/error，按钮随状态变化。
- 切换 login item、dock、debug tools 后立即反映目标状态，失败时反馈。
- 编辑 proxy 后表单 changed；保存成功回到 clean，失败保留输入。
- 关闭 Settings 后主应用按新设置更新 dock、debug UI 或连接行为。

## Recovery And Blocking States

- **Gateway restart failed**: 展示 error 和重试。
- **Proxy save failed**: 保留输入并提示。

## End State

通用设置保存成功或停在可重试错误；Gateway 当前状态可见。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 未提供 Settings 录屏；按 inventory 当前行为梳理。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
