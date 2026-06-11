# Agent UI Contract Screenshots

This directory stores screenshots referenced by the Agent UI contract docs.

## Screenshot Files

Screenshot filenames use lowercase kebab-case and stable UI state names so links stay readable when the docs move around.

## Current Set

| File | Contract surface |
| ---- | ---------------- |
| `onboarding-welcome.jpg` | Onboarding welcome step |
| `onboarding-provider-setup.jpg` | Onboarding provider setup |
| `onboarding-notifications.jpg` | Onboarding notification permission step |
| `onboarding-completion.jpg` | Onboarding completion handoff |
| `chat-empty-composer.jpg` | Chat empty state, sidebar, composer, model and thinking controls |
| `chat-model-picker.jpg` | Chat model picker |
| `code-workspace-runtime-layout.jpg` | Code workspace layout with Preview tab active and LVGL preview ready |
| `agent-authoring-soul-editor.jpg` | Agent Authoring app info edit state over the Soul editor |
| `settings-general-gateway-network.jpg` | Settings General tab |
| `login-page.jpg` | Account login page |
| `publish-to-catalog.jpg` | Unified publish center with app/package review pending and firmware draft |

## Agent Publish Screenshots

The `agent-publish/` filenames are legacy-stable names. Their contents now reflect
the Journey 09 unified publish center rather than the older standalone publish
dialog.

| File | Current state |
| ---- | ------------- |
| `agent-publish/1-code-edit-agent-entry.jpg` | Publish center opened from Agent Authoring with draft rows. |
| `agent-publish/2-authoring-tab-publish-action.jpg` | Publish center after review submission, with pending review rows. |
| `agent-publish/5-dialog-unauthenticated.png` | App info load state inside the publish center. |
| `agent-publish/8-dialog-metadata-new-version.jpg` | App info and media edit form. |
| `agent-publish/9-dialog-metadata-new-agent.jpg` | Publish center with pending app/package review and firmware draft. |
| `agent-publish/11-dialog-success.png` | Firmware publish form checking latest firmware. |
| `agent-publish/14-dialog-version-conflict.png` | Firmware publish form with no uploaded firmware result yet. |

## Remaining Asset Inventory

The latest video did not include every possible terminal state. Add focused
assets when those states are available:

| Needed asset | Capture setup |
| ------------ | ------------- |
| `edit-agent-approved-header` | Agent Authoring tab after review approval, with `Approved` badge visible. |
| `publish-center-approved-package-draft-firmware` | Publish center modal showing App record approved, Icon & screenshots optional, Agent package approved, Firmware draft. |
| `publish-center-firmware-published` | Firmware row after a successful firmware publish. |
| `publish-center-failed-row` | A row-level failure with retry/recovery action visible. |
