# 09. 统一发布中心 / Catalog Review / Firmware Publish

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: user9-update-agent-updateapp-infopublish.mov
HTML wireframe: [09-unified-publish-center.wireframe.html](09-unified-publish-center.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Agent Authoring 点击 Publish，或从发布状态行进入发布中心。

## User Behavior

用户在同一个发布中心查看 App record、Icon & screenshots、Agent package、Firmware 四个状态行，编辑 app info/media，提交 review，查看结果，并发布 production package 或 firmware。

## Client State Changes

- 点击 Publish 后进入发布面板或弹窗，展示 App record、Icon & screenshots、Agent package、Firmware 的当前状态。
- 发布中心展示四个 row，并用 draft/ready/missing/pending/approved/rejected/revoked/optional 等状态表达完整度。
- 编辑 app info/media 时显示表单，修改后 unsaved，保存中禁用，成功后刷新 row。
- 提交 app 或 agent package review 后按钮 pending，成功后变成 submitted/in review。
- review 返回后用 badge、提示和动作反映 approved/rejected/revoked。
- Firmware row 读取 build/manifest 版本、channel 和设备 metadata；缺失时禁用，发布时 uploading/publishing，成功后刷新 latest，失败允许重试。

## Recovery And Blocking States

- **Missing metadata**: 保留当前 row 状态，补齐 app info、media、package 或 firmware metadata 后重试。
- **Review rejected**: 保留状态和可编辑入口。
- **Firmware failed**: 停在 row 并允许重试。

## End State

App record、agent package 和 firmware 在同一中心内有清晰的未提交、审核、通过、拒绝、撤销、可发布、已发布或失败状态。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 录屏缩略图显示 Agent authoring 上方弹出 Submit for review dialog；这里把它作为发布/审核入口，不把它归类为账户认证旅程。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
