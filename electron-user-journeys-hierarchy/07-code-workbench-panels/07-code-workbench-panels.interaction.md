# 07. Code 工作台面板

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: create-workspace->coding.mov
HTML wireframe: [07-code-workbench-panels.wireframe.html](07-code-workbench-panels.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Code session 中查看右侧工作台和底部面板。

## User Behavior

用户在 Files、Changes、Diff/File tab、Terminal、Device Log 等面板之间切换，查看文件、改动、命令输出和设备日志。

## Client State Changes

- 右侧面板显示默认 tab；切换 tab 不改变当前 Code session。
- Files 展示工作区文件树；选择文件后打开 file tab 或预览。
- Changes 展示改动；选择后打开 diff，选中项和 diff 同步。
- Terminal 展示当前 session 命令输出，运行中流式追加，结束后保留结果。
- Device Log 展示日志流、过滤、暂停/继续和自动滚动。
- 折叠、展开或调整面板只改变布局，不丢失活动会话和消息流。

## Recovery And Blocking States

- **No visible files**: 显示 refresh files。
- **Panel collapsed**: 可重新展开，不影响会话。

## End State

用户停在某个工作台视图；文件、diff、terminal 或日志状态与当前 Code session 对齐。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 录屏缩略图右侧清楚显示 All Files / Changes / Device / Preview 顶部 tabs 和底部 Terminal / Device Log。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
