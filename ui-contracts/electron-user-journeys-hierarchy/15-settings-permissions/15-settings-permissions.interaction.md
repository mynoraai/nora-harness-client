# 15. Settings - Permissions

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: not provided
HTML wireframe: [15-settings-permissions.wireframe.html](15-settings-permissions.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Settings 中打开 Permissions tab，或从运行中的权限/系统权限提示跳转。

## User Behavior

用户配置 exec approval、allowlist，查看或处理通知、麦克风、辅助功能等系统权限。

## Client State Changes

- Permissions tab 加载执行策略、allowlist 和系统权限状态。
- 切换 exec approval 策略后展示新选择，保存后影响后续工具执行。
- 新增/移除 allowlist 项时列表即时变化；保存失败时恢复或提示未保存。
- 系统权限缺失时显示 missing/needs action，并提供打开系统设置或重新检查。
- 授权后返回应用，重新检查从 missing 更新为 granted；仍未授权则保持 blocked。

## Recovery And Blocking States

- **Permission missing**: 显示 needs action。
- **Save failed**: 提示未保存或恢复。

## End State

权限页展示当前执行策略和系统权限最终状态；后续运行按权限自动执行、请求批准或阻止。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 未提供 Permissions 录屏；按 inventory 当前行为梳理。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
