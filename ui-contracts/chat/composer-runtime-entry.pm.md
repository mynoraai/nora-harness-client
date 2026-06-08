# Composer Runtime Entry PM Brief

## Summary

- Add PM wireframe for the NoraHarness composer area shown in the screenshot: prompt input, attachment entry, runtime selector, model selector, thinking selector, and send/stop button.
- Ground current behavior in `apps/electron` code and mark proposed product language separately.

## PM Context

### Current Work

This surface is the first action point in Chat/Code sessions. The user types a task, chooses which runtime should answer, chooses the model for that runtime, and sends or stops a turn. In NoraHarness this is also a runtime-routing decision: OpenClaw runs through the Gateway, while Claude Code and Codex are ACP stdio runtimes owned by the Electron main process.

### Current Behavior From Code

- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:169`: runtime options are `OpenClaw`, `Claude Code`, and `Codex`.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:371`: composer resolves the selected runtime from session metadata.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:424`: composer loads models for the selected runtime and agent mode.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:800`: prompt input configures attachment behavior by selected runtime.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:810`: prompt placeholder is `Assign a task or ask any question`.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:831`: runtime selector is in the composer footer.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:868`: model selector is in the composer footer.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:973`: thinking selector appears for non-code OpenClaw sessions.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:981`: submit button switches between send and stop behavior.
- `apps/electron/src/renderer/src/components/chat/ChatArea.tsx:119`: ChatArea creates the runtime chat adapter registry.
- `apps/electron/src/renderer/src/components/chat/ChatArea.tsx:126`: ChatArea resolves the adapter from the session runtime.
- `apps/electron/src/renderer/src/components/chat/ChatArea.tsx:786`: draft sessions materialize differently for OpenClaw vs client-owned runtimes.
- `apps/electron/src/shared/runtime-types.ts:11`: `openclaw` is Gateway-backed; other runtime ids are ACP stdio.
- `apps/electron/src/preload/index.ts:160`: renderer reaches runtime list, probe, model list, start, send, cancel, run events, sessions, and history through typed preload APIs.

### Implementation Maturity

`implemented`

- Current evidence: the composer, runtime selector, model selector, attachment handling, send/stop state, adapter binding, and `runtime:*` IPC contracts already exist.
- Proposed assumptions: the wireframe proposes clearer visible language for runtime health, locked runtime reasons, no-model recovery, and install/auth recovery near the composer.
- Dependencies: no new backend dependency is required for the base wireframe. Richer health copy depends on existing `RuntimeDiagnosisLayer` mapping staying available to the renderer.

### Discussion Focus

The PM decision is not whether this area exists. It already exists. The discussion is whether the current compact control row is enough for users to understand runtime choice, model choice, locked sessions, and recovery states without reading logs or opening Settings first.

### PM Decision Points

<table>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Keep runtime and model as two compact composer pills, or move runtime status into the header?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should runtime rows show health labels such as Ready, Auth required, and Install needed?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> When a session runtime is locked, should the UI explain why and offer New conversation?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should no-model and unavailable-model recovery link directly to Settings Providers?</td>
  </tr>
  <tr>
    <td bgcolor="#f8d7da"><strong>Blocking PM decision:</strong> Should runtime/auth/install failures block send only, or also show a composer-level recovery card?</td>
  </tr>
</table>

### Proposed Design

The proposed UX keeps one composer as the primary entry. The footer remains compact:

- Attachment button at the left.
- Runtime selector: OpenClaw, Claude Code, Codex.
- Model selector: loaded from the selected runtime.
- Thinking selector only when the selected session supports it.
- Send/stop button at the right.

The proposed additions are state copy and recovery paths:

- Runtime selector rows can expose readiness labels from the existing runtime diagnosis vocabulary.
- Locked runtime sessions can show an explanation: continue this session or start a new conversation to switch runtime.
- No enabled models can point to Settings Providers.
- Install/auth/runtime failures can show one composer-near recovery card while keeping detailed error cards in the message area.

### Benefits

- Users can understand why a send action is blocked before they click.
- PM and engineering can keep one unified composer instead of introducing a second runtime-specific prompt.
- Runtime health, model state, and session lock rules become explainable in product language.
- The proposal uses existing runtime contracts instead of requiring new backend states.

### Downsides / Tradeoffs

- More visible state in a compact composer can make the input feel heavier.
- Health labels add copy and ordering decisions PM must own.
- Deep links to Settings Providers or runtime install flows need stable route behavior and test selectors.
- Showing recovery near the composer may duplicate message-area error cards unless the content hierarchy is clear.

### Open Questions

- Should OpenClaw be described as a runtime to end users, or as the default NoraHarness mode?
- Should Claude Code and Codex appear only when installed/available, or always appear with install guidance?
- Do we want to show the selected runtime in the message header after the first send, when the composer control is lower on the screen?

## Wireframe Preview

Preview: https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/composer-runtime-entry-wireframe/ui-contracts/chat/composer-runtime-entry.wireframe.html

File: `ui-contracts/chat/composer-runtime-entry.wireframe.html`

Note: the preview URL resolves after this branch is pushed to GitHub.

## Verification

- Static HTML wireframe should be opened locally before PR handoff.
- Run `git diff --check` in `mynoraai/nora-harness-client`.
