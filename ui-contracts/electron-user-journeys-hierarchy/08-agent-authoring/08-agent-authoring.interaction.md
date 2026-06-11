# 08. Agent Authoring

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: user9-update-agent-updateapp-infopublish.mov
HTML wireframe: [08-agent-authoring.wireframe.html](08-agent-authoring.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Code 工作区点击 Edit Agent，或从 agent 配置相关入口进入。

## User Behavior

用户编辑 Soul、Style、Tools、Heartbeat、Bootstrap、Skills、Capabilities，查看能力差异，新增或测试 skill，并保存改动。

## Client State Changes

- 进入 Authoring 后从代码会话切到 agent 配置编辑视图。
- 切换各区域时主编辑内容更新，导航 active 同步。
- 修改文本或配置后进入 dirty，保存状态从 clean 到 unsaved/saving。
- 保存成功清除 dirty 并显示 saved；失败保留输入并展示错误。
- Skills 可创建、编辑、预览或 probe，运行中 pending，结束显示结果。
- Capabilities 展示 agent 与设备/manifest 能力，刷新后更新列表和差异。

## Recovery And Blocking States

- **Save failed**: 保留输入和错误。
- **Probe failed**: 显示失败结果但不关闭编辑器。

## End State

agent 配置处于已保存、保存失败待处理或未保存改动状态；用户可回 Code 或继续发布。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 发布录屏缩略图显示 Code shell 内 Agent authoring，左侧 Soul/Style/Tools/Heartbeat/Bootstrap/Skills/Capabilities，编辑器为 agent/SOUL.md，右上有 Edit Agent 和 Publish。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
