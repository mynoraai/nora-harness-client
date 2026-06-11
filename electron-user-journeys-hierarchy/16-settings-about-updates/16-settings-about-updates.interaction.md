# 16. Settings - About / Updates

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: user9-update-agent-updateapp-infopublish.mov
HTML wireframe: [16-settings-about-updates.wireframe.html](16-settings-about-updates.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户在 Settings 中打开 About / Updates tab，或点击主界面更新提示。

## User Behavior

用户查看版本信息，检查更新，下载更新，并选择安装或稍后处理。

## Client State Changes

- About/Updates 展示当前版本、构建信息和 updater 状态。
- 点击检查更新后 idle 到 checking；无更新时 up to date。
- 有更新时展示新版本信息和下载入口；下载中显示进度。
- 下载完成后 ready to install，安装/重启可用。
- 下载或检查失败时显示 error、失败原因和重试。

## Recovery And Blocking States

- **Check failed**: 显示错误和重试。
- **Download failed**: 保留可重试状态。

## End State

用户确认最新版、完成下载等待安装，或停在可重试更新错误。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 用户提供的发布录屏名称包含 update app 信息；具体 About UI 未在缩略图首屏出现，本旅程按 inventory 描述。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
