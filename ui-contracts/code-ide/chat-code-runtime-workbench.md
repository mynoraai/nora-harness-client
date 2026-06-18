# Chat Code Runtime Workbench Contract

Source row: `CODE-14`

Entry path: Code mode -> workspace session list -> chat tab composer

Status: Draft, source-anchored

## Purpose

This file defines the Code workbench runtime-selection contract shown in [chat-code-runtime-workbench.html](./chat-code-runtime-workbench.html). It covers the visible difference between a Draft Session and persisted Codex, Claude Code, and OpenClaw sessions.

The key behavior: a Draft Session can change runtime before creation. Once a session is created or has native runtime identity, its runtime selector is disabled and the session stays bound to Codex, Claude Code, or OpenClaw. The model selector remains scoped to the selected runtime.

## Workbench Session States

| State            | Runtime selector                 | Main content                                                                 | Model selector                                             | Evidence                                                                                                                                                                                                                                                                           |
| ---------------- | -------------------------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Draft Session    | Enabled unless a turn is active. | No transcript, ACP blocks, modified-files block, editor tab, or terminal yet. | Shows the selected draft runtime's model catalog.          | `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:831`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:844`; `apps/electron/src/renderer/src/stores/session-store.ts:120`                                |
| Codex Session    | Disabled after native identity.  | Existing Code chat transcript and workspace panes can render.                 | Shows Codex runtime models and can restart cached session. | `apps/electron/src/renderer/src/stores/session-store.ts:120`; `apps/electron/src/renderer/src/lib/acp-stdio-runtime-chat-transport.ts:375`; `apps/electron/src/renderer/src/lib/acp-stdio-runtime-chat-transport.ts:406`                 |
| Claude Session   | Disabled after native identity.  | Existing Code chat transcript and workspace panes can render.                 | Shows Claude Code runtime models.                          | `apps/electron/src/renderer/src/stores/session-store.ts:120`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:316`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:831`                                 |
| OpenClaw Session | Disabled for saved OpenClaw.     | Existing Gateway-backed Code chat transcript and workspace panes can render.  | Shows OpenClaw/Gateway model choices.                      | `apps/electron/src/renderer/src/stores/session-store.ts:120`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:831`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:868`                                 |

## Runtime Selector Contract

| User action                         | UI precondition                         | Visible result                                                                                 | State/API path                                                                                               | Evidence                                                                                                                                                                                                                                                          |
| ----------------------------------- | --------------------------------------- | ---------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Open Runtime Selector               | Active session is a Draft Session.      | Runtime menu opens with OpenClaw, Claude Code, and Codex rows, each with provider icon/check.   | Local composer state.                                                                                        | `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:831`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:844`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:849`                         |
| Choose another runtime              | Draft Session is not submitted/running. | Runtime trigger updates; model selection clears; runtime model catalog reloads.                 | `useSessionStore.setSessionRuntime`, `clearSessionModel`, local catalog reload state.                         | `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:582`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:597`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:600`                         |
| Try to change runtime after binding | Session has resume/native identity.     | Trigger is disabled or selection is ignored; user keeps the existing runtime binding.           | `isSessionRuntimeSelectionLocked` blocks mutation.                                                            | `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:834`; `apps/electron/src/renderer/src/stores/session-store.ts:120`; `apps/electron/src/renderer/src/stores/session-store.ts:1177`                                         |
| Submit or stream a turn             | Runtime may already be selected.        | Runtime trigger is disabled while submitted or streaming.                                       | `ChatComposer` disables the trigger for submitted/streaming status.                                           | `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:834`                                                                                                                                                                                             |
| Runtime becomes ready/running/error | ACP runtime reports status.             | Working directory bar shows compact ACP runtime status pill; detailed recovery lives elsewhere. | `setAcpRuntimeStatus` updates session state; `WorkingDirBar` renders `AcpRuntimeStatusPill`.                  | `apps/electron/src/renderer/src/stores/session-store.ts:1212`; `apps/electron/src/renderer/src/components/chat/WorkingDirBar.tsx:121`; `apps/electron/src/renderer/src/lib/acp-stdio-runtime-chat-transport.ts:397`                         |

## Runtime And Model Linkage

The runtime selector and model selector are a two-key contract:

1. Runtime selection chooses the owning runtime adapter and model catalog.
2. Model selection chooses a runtime-native model id in that catalog.
3. Changing runtime on a draft clears the current model so the next model is selected from the new runtime catalog.
4. Selecting a new model on a persisted ACP session can restart the cached runtime session so the next turn uses the visible model.
5. Provider icons and model groups are display hints; the renderer must not infer runtime ownership from model id or provider.

| Visible control/state | Contract                                                                                 | Evidence                                                                                                                                                                                                                                                                             |
| --------------------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Runtime model loading | Composer loads models through `listRuntimeModels({ runtimeId, agentMode })`.             | `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:303`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:455`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:470`                         |
| Runtime change reset  | Draft runtime change clears model and model-load errors before loading the next catalog. | `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:596`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:600`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:603`                         |
| Model trigger states  | Loading, retry, unavailable, selected, and empty catalog states remain on model selector. | `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:880`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:885`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:891`; `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:896` |
| ACP model switch      | Cached ACP session restarts when selected model changes.                                 | `apps/electron/src/renderer/src/lib/acp-stdio-runtime-chat-transport.ts:375`; `apps/electron/src/renderer/src/lib/acp-stdio-runtime-chat-transport.ts:383`; `apps/electron/src/renderer/src/lib/acp-stdio-runtime-chat-transport.ts:406`       |

## Message Blocks In The Workbench

The persisted Codex, Claude Code, and OpenClaw sessions use the normal Code chat tab content. Draft Session intentionally shows no transcript or generated blocks before creation.

| Visible block              | Contract                                                                                       | Evidence                                                                                                                                                                                                                                                                  |
| -------------------------- | ---------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ACP tool block             | Renders `data-acp-tool` parts only after a session/run exists.                                  | `apps/electron/src/renderer/src/components/chat/ChatMessages.tsx:398`; `apps/electron/src/renderer/src/components/chat/ChatMessages.tsx:403`                                                                                                                              |
| ACP permission card        | Renders unresolved `data-acp-permission` requests inline in the assistant message list.         | `apps/electron/src/renderer/src/components/chat/ChatMessages.tsx:411`; `apps/electron/src/renderer/src/components/chat/ChatMessages.tsx:417`                                                                                                                              |
| ACP status block           | Renders compact `data-acp-status` rows.                                                         | `apps/electron/src/renderer/src/components/chat/ChatMessages.tsx:425`; `apps/electron/src/renderer/src/components/chat/ChatMessages.tsx:430`                                                                                                                              |
| Modified files block       | Renders non-empty `data-acp-modified-files` summaries and links to Code workspace file tabs.    | `apps/electron/src/renderer/src/components/chat/ChatMessages.tsx:433`; `apps/electron/src/renderer/src/components/chat/ChatMessages.tsx:439`; `apps/electron/src/renderer/src/lib/acp-ui-chunk-bridge.ts:441`                                                             |

## Wireframe Review Checklist

- Left Sessions list includes exactly Draft Session, Codex Session, Claude Session, and OpenClaw Session.
- Draft Session is the only state where the Runtime Selector is enabled.
- Draft Session does not show chat content, ACP blocks, modified files, code editor, or terminal output.
- Codex, Claude, and OpenClaw sessions show disabled Runtime Selector with their bound runtime label.
- Model Selector remains visible for each session and stays scoped to the selected runtime.
- Runtime status pill states remain compact; recovery belongs to banners/cards, not the pill.
