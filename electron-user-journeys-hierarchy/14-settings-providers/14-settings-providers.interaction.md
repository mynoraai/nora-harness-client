# 14. Settings - Providers

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: onboarding2.mov
HTML wireframe: [14-settings-providers.wireframe.html](14-settings-providers.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Settings 中打开 Providers tab，或从 provider 缺失提示跳转。

## User Behavior

用户新增、编辑、删除 provider，填写 API key、base URL、enabled models，并设置 default model。

## Client State Changes

- Providers tab 加载已有 provider 列表和默认 model。
- 新增 provider 时表单为空，必填缺失时保存/验证不可用。
- 输入 API key 或 base URL 后验证可用；验证中 pending，成功 verified，失败 inline error。
- 调整 enabled models 或 default model 后列表和 badge 更新；保存后 clean。
- 删除 provider 需要确认；成功后移除，默认 model 可能回退或提示重新选择。

## Recovery And Blocking States

- **Validation failed**: 显示 inline error 并保留输入。
- **Delete provider**: 确认后移除并处理默认 model。

## End State

Provider 配置 ready，或明确提示缺失认证、验证失败、没有默认 model 等待处理。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: Onboarding provider 录屏可作为 provider 选择和 key 验证的相同表单行为参考。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
