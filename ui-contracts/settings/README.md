# Settings Contracts

Source rows: `SET-01`, `SET-02`, `SET-03`, `SET-04`, `SET-05`, `SET-06`, `SET-07`, `SET-08`, `SET-09`, `SET-10`
Entry path: App boot -> sidebar Settings button -> Settings dialog
Status: Draft

This folder covers the Settings dialog. Use it to find what each Settings tab shows, what the user can change, which backend or local state is touched, and which tests or gaps map to `SET-*` rows.

## Owned Rows

| Source row | Contract file       | Surface                                                                                          |
| ---------- | ------------------- | ------------------------------------------------------------------------------------------------ |
| `SET-01`   | `settings-frame.md` | Settings dialog frame, Back to app, left navigation, tab mounting                                |
| `SET-02`   | `general.md`        | Gateway status, app feature switches, proxy, default thinking level, debug-only reset onboarding |
| `SET-03`   | `workspace.md`      | Workspace markdown file list, editor, save, refresh, autosave-on-switch                          |
| `SET-04`   | `providers.md`      | Provider list, default model, add/edit/remove providers, model loading and validation            |
| `SET-05`   | `channels.md`       | Channel cards, configuration flows, accounts, group settings, DM policy, pairing inbox           |
| `SET-06`   | `extensions.md`     | Removed Settings Extensions / ACP module                                                        |
| `SET-07`   | `permissions.md`    | Command approval mode, allowlist, notification and macOS permissions                             |
| `SET-08`   | `voice.md`          | Voice wake, microphone selection, trigger words, sounds                                          |
| `SET-09`   | `advanced.md`       | Config editor, cron toggles, skill toggles, debug restart                                        |
| `SET-10`   | `about.md`          | Version, update state, auto-update, update actions, external links                               |

## Shared Evidence

The Settings dialog can open from both Chat and Code. `SettingsDialog` receives a `GatewayClient` and mounts one active tab at a time. Tab selection and fallback behavior live in `apps/electron/src/renderer/src/components/settings/SettingsDialog.tsx:37`; tab rendering lives in `apps/electron/src/renderer/src/components/settings/SettingsDialog.tsx:118`.

These detailed files are the source for tab-level Settings interaction contracts.

## Cross-Cutting Gaps

| Gap                                       | Scope                  | Notes                                                                                                                                            |
| ----------------------------------------- | ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| L3 harness missing                        | All Settings rows      | `../tests/three-tier-matrix.md` and `../tests/l3-computer-use-scenarios.md` mark Electron L3 as not wired.                                       |
| Stable selectors missing                  | Most Settings controls | Future Playwright or computer-use coverage needs durable `data-testid` or aria hooks for tab nav, Save buttons, toggles, dialogs, and list rows. |
| Settings tab integration coverage partial | All Settings rows      | Existing tests mostly cover lower-level helpers and selected components, not the whole Settings dialog journey.                                  |
