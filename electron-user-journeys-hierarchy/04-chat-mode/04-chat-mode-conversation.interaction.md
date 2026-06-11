# 04. Chat 模式

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: not provided
HTML wireframe: [04-chat-mode-conversation.wireframe.html](04-chat-mode-conversation.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户进入默认 Chat 模式，或从模式切换器切回 Chat。

## User Behavior

用户查看会话列表、新建或搜索会话，在 composer 输入、附加文件或命令，选择 runtime/model/thinking，发送并处理停止、权限和错误恢复。

## Client State Changes

- 侧边栏显示 Chat 会话列表、搜索和新建入口。
- 选择会话后加载消息，旧消息可分页。
- 输入内容后发送可用；发送后用户消息立即追加，composer 进入运行约束。
- 运行中显示 assistant streaming、停止、工具块、权限卡和结果。
- 权限请求会暂停；批准继续，拒绝结束该步骤。
- 停止或失败时 turn 进入 stopped/cancelled/error，提供重试或重新生成。
- 成功后消息 finalized，composer 恢复，会话摘要更新。

## Recovery And Blocking States

- **Permission required**: 显示批准/拒绝卡。
- **Run failed**: 展示错误和重试。
- **Stop clicked**: 当前 turn 结束为 stopped。

## End State

会话停在完成、停止或失败的 turn 后；用户可继续发送、重试、切换会话或离开 Chat。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 未提供 Chat 专属录屏；Code 录屏中可见相同 composer 和模型控件。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
