# 13. Settings - Workspace Files

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: not provided
HTML wireframe: [13-settings-workspace-files.wireframe.html](13-settings-workspace-files.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Settings 中打开 Workspace Files tab。

## User Behavior

用户查看并编辑 SOUL、IDENTITY、USER、AGENTS、TOOLS、HEARTBEAT、MEMORY 等 workspace 文件。

## Client State Changes

- tab 打开后加载当前 workspace 文件列表和选中文件内容。
- 选择不同文件时编辑器内容、文件名和说明同步更新。
- 修改内容后 dirty，保存/重置可用。
- 保存中进入 saving；成功清除 dirty，失败保留编辑内容和错误。
- 文件缺失时展示 missing/empty，并按能力提供创建或恢复入口。

## Recovery And Blocking States

- **Missing file**: 展示 empty/missing 和恢复入口。
- **Save failed**: 保留输入并提示。

## End State

用户完成查看、保存、重置，或停在有未保存改动的编辑状态。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 未提供 Settings 录屏；按 inventory 当前行为梳理。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
