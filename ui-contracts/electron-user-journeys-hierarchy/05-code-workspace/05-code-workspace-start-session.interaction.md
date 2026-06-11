# 05. Code 模式 / 工作区

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: create-workspace->coding.mov
HTML wireframe: [05-code-workspace-start-session.wireframe.html](05-code-workspace-start-session.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户从模式切换器点击 Code，或从已有 Code workspace/session 进入。

## User Behavior

用户创建或选择工作区，选择本地目录，创建 Code session，并在该工作区发送代码相关请求。

## Client State Changes

- 模式从 Chat 切换到 Code workspace shell。
- 无工作区时显示 empty state 和 Create Workspace。
- 创建 dialog 等待选择文件夹；未选择目录时创建不可用。
- 选择目录后填充路径，名称自动推导或可编辑。
- 创建成功后 dialog 关闭，侧边栏新增并展开工作区，自动创建 draft code session。
- Code composer 发送后 draft session materialize，工作目录状态条显示当前 workspace，运行进入 starting/running。

## Recovery And Blocking States

- **No directory**: 创建按钮禁用。
- **Create failed**: 保留 dialog 和错误。

## End State

用户停在已打开的 Code session；工作区、活动 tab、工作目录和 composer 均可继续工作。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 录屏缩略图显示 Code 模式、左侧 Workspaces、顶部工作目录、右侧 All Files/Changes/Device/Preview、底部 Terminal/Device Log，以及中心 focused conversation composer。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
