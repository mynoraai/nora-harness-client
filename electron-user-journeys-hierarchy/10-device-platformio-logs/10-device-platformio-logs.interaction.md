# 10. Device / PlatformIO / Logs

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: preview-lvgl.mov
HTML wireframe: [10-device-platformio-logs.wireframe.html](10-device-platformio-logs.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Code 工作台打开 Device tab 或 Device Log 面板。

## User Behavior

用户检查 PlatformIO 状态，刷新设备列表，查看已连接设备、设备指标、USB fallback 和日志输出。

## Client State Changes

- Device tab 打开时加载 PlatformIO 可用性、安装和连接状态。
- PlatformIO 不可用时显示安装/修复；可用时显示 ready 和设备动作。
- 刷新设备列表进入 loading，完成后显示 connected/disconnected 卡片、固件版本、内存、延迟等。
- 日志面板接收设备或 USB monitor 输出；暂停时停止视觉滚动，继续后恢复。
- 过滤条件即时按级别、来源或搜索词更新。
- 设备断开或探测失败时卡片和日志切到 disconnected/error，并保留刷新或重试。

## Recovery And Blocking States

- **PlatformIO missing**: 显示安装/修复入口。
- **Device disconnected**: 显示重试和断开状态。

## End State

用户能确认设备、PlatformIO 和日志链路是 ready、missing、disconnected 还是 error。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: LVGL 录屏里右侧工作台和底部 Device Log 与 Terminal 共同出现，可作为设备面板布局参考。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
