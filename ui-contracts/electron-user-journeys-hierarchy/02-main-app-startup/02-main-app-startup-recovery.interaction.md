# 02. 主应用启动 / 恢复状态

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: not provided
HTML wireframe: [02-main-app-startup-recovery.wireframe.html](02-main-app-startup-recovery.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

已完成 onboarding 后再次启动应用，或 renderer 刷新/重启。

## User Behavior

用户等待主界面加载，查看 gateway banner、模式、会话、更新提示和可继续输入区。

## Client State Changes

- 读取 onboarding、全局模式、会话、工作区、打开 tab 和设置。
- Gateway 从 checking/starting 进入 connected、disconnected 或 error。
- 恢复上次 Chat 或 Code 模式；无会话时展示空状态。
- 存在活动 run 时尝试 reconnect，恢复 streaming、权限卡或错误。
- 可用更新出现时展示更新提示，可稍后处理或进入更新流程。

## Recovery And Blocking States

- **Gateway error**: 显示重试/重启 gateway 入口。
- **Reconnect failed**: 保留错误状态并允许重新发送或重试。

## End State

停在可操作主界面；用户能继续上次会话、新建会话或处理 gateway/更新恢复动作。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 未提供录屏；按 inventory 当前行为梳理。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
