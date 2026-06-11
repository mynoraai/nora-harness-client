# 01. 首次启动 / Onboarding

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: onboarding2.mov
HTML wireframe: [01-first-launch-onboarding.wireframe.html](01-first-launch-onboarding.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

首次安装并打开 NoraHarness，本地尚未完成 onboarding。

## User Behavior

用户从 Welcome 进入分步引导，选择 provider，输入并验证 key，完成个性化、通知和完成页。

## Client State Changes

- Welcome 切换到分步骤 onboarding。
- Provider key 输入前验证/继续不可用，格式通过后验证可用。
- 验证中显示 pending，成功后显示 verified，失败时保留当前步骤和 inline error。
- 每一步继续后标记完成并切到下一步；返回时保留或恢复表单。
- 完成后写入 onboarding complete，本地 shell 切到主应用。

## Recovery And Blocking States

- **Provider invalid**: 保留输入，显示 inline error，允许重新验证。
- **Skip allowed**: 如果用户选择跳过，主界面保留 setup debt。

## End State

主应用侧边栏和 Chat/Code 主界面可见；后续启动不再显示 onboarding，除非用户重置数据。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 录屏缩略图显示 Step 2 of 5 provider grid、OpenRouter 选中、API key verified、Default model 和 Continue 按钮。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
