# 06. Runtime / Model / Permission

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: create-workspace->coding.mov
HTML wireframe: [06-runtime-model-permission.wireframe.html](06-runtime-model-permission.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Chat 或 Code composer 打开 runtime/model 选择器，或发送消息时触发 runtime/provider/permission 检查。

## User Behavior

用户选择运行时、model 和思考强度；发送后根据提示处理 provider 缺失、runtime 启动失败或工具权限请求。

## Client State Changes

- Runtime selector 展示当前运行时，切换后记录到会话状态。
- Model selector 展示 provider/model，切换后影响后续发送。
- 发送前 provider 未准备好时阻止运行并显示配置/认证/模型不可用提示。
- 发送后 runtime 从 idle 到 starting，成功到 running，失败到 failed。
- 工具或命令需要权限时插入权限卡，批准继续，拒绝结束该步骤。
- 完成后 runtime 回到 idle/ready；可恢复 run 刷新后进入 reconnecting 再恢复。

## Recovery And Blocking States

- **Provider missing**: 阻止发送并引导设置。
- **Runtime failed**: 显示失败原因和重试。

## End State

运行时、model 和权限选择在界面上清楚可见；turn 完成、可恢复、可重试或等待批准。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: Code 录屏中可见 composer 左侧 plus、auto/runtime pill、model pill 和 thinking medium 状态。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
