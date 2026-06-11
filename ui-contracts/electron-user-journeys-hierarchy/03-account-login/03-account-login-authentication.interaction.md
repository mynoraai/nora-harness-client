# 03. 登录 / 账户认证

Status: current implemented journey inventory
Source: `/Users/zhaowei/workspace/nora-harness-client/electron-user-journeys-hierarchy-v2/user-journey-inventory.md`
Video reference: login.mov
HTML wireframe: [03-account-login-authentication.wireframe.html](03-account-login-authentication.wireframe.html)

This document describes an existing or already-established NoraHarness Electron interaction.

## User Entry

用户从侧边栏账户入口点击登录，或从其他账户依赖功能触发认证。

## User Behavior

用户点击登录，完成浏览器或内嵌认证流程，再查看账户状态、登出或返回原账户依赖动作。

## Client State Changes

- 未登录时侧边栏显示登录入口；需要登录功能先拦截。
- 点击登录后进入 signing in / waiting，按钮防重复。
- 登录成功后账户区域切换为已登录，依赖登录的按钮恢复可继续。
- 失败或取消时回到未登录状态并展示反馈。
- 登出后清空本地账户态，账户依赖入口重新进入认证要求状态。

## Recovery And Blocking States

- **Cancel / fail**: 返回未登录状态，保留原入口。
- **Sign out**: 清空账户状态并重新进入拦截规则。

## End State

用户处于已登录或未登录的明确状态；所有账户依赖入口按状态继续、拦截或要求重新登录。

## Wireframe Notes

- Primary source is the v2 journey inventory.
- Video cue: 录屏缩略图显示 Chrome 打开 NoraClaw 登录页，包含 Continue with Google、Email、Password、Continue with Email、Forgot password 和 Create account。
- The HTML wireframe keeps annotations outside product frames and focuses on the implemented flow.
