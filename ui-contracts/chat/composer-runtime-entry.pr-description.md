## Summary

- Add Composer Runtime Entry PM wireframe for the Chat composer surface.
- Ground implemented behavior in current `apps/electron` code and mark proposed recovery/status language separately.

## PM Context

### Current Work

The screenshot focuses on the main composer entry where users type a task, attach files, select OpenClaw/Claude/Codex, select the runtime model, and send or stop a run. This is the product point where users choose who answers the task and what model configuration applies.

### Current Behavior From Code

- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:169`: runtime options are `OpenClaw`, `Claude Code`, and `Codex`.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:424`: composer loads models for the selected runtime and agent mode.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:800`: attachment behavior is scoped by selected runtime.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:810`: prompt placeholder matches the screenshot.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:831`: runtime selector is mounted in the composer footer.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:868`: model selector is mounted in the composer footer.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:981`: submit button owns send/stop behavior.
- `apps/electron/src/renderer/src/components/chat/ChatArea.tsx:126`: ChatArea resolves the runtime adapter from session runtime metadata.
- `apps/electron/src/shared/runtime-types.ts:11`: `openclaw` is Gateway-backed; Claude/Codex are ACP stdio runtime ids.
- `apps/electron/src/preload/index.ts:160`: renderer reaches runtime operations through typed preload IPC.

### Implementation Maturity

`implemented`

- Current evidence: unified composer, runtime selector, model selector, attachment support, send/stop state, runtime adapter binding, and runtime IPC all exist.
- Proposed assumptions: the wireframe adds product-language guidance for runtime health, locked runtime reasons, model recovery, and install/auth failures.
- Dependencies: no new backend dependency for the wireframe. Productizing health labels depends on existing runtime diagnosis values being surfaced where the selector can render them.

### Discussion Focus

Should the composer remain the single compact place for runtime/model decisions, and what recovery language should appear when users cannot send?

### PM Decision Points

<table>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Keep runtime and model as composer pills, or move runtime status to the header?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Show runtime health labels inside the runtime picker?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Explain locked runtime sessions inline and offer New conversation?</td>
  </tr>
  <tr>
    <td bgcolor="#f8d7da"><strong>Blocking PM decision:</strong> Should runtime/auth/install failures block send only, or show composer-level recovery?</td>
  </tr>
</table>

### Proposed Design

Keep one composer. The footer keeps attachment, runtime selector, model selector, optional thinking selector, and send/stop. Add clearer status and recovery language for runtime readiness, locked sessions, missing models, auth required, and install needed.

### Benefits

- Users can diagnose blocked send states without reading logs.
- PM can discuss runtime choice and model choice in one artifact.
- Engineering can build on existing runtime-neutral contracts.

### Downsides / Tradeoffs

- More visible state can make the compact composer busier.
- Recovery links may duplicate message-area error cards.
- Stable Settings/runtime deep links and test selectors may be needed before implementation.

### Open Questions

- Should OpenClaw be user-facing as a runtime label or as the default NoraHarness mode?
- Should unavailable Claude/Codex runtimes appear with install guidance or stay hidden until installed?

## Wireframe Preview

Preview: https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/composer-runtime-entry-wireframe/ui-contracts/chat/composer-runtime-entry.wireframe.html

File: `ui-contracts/chat/composer-runtime-entry.wireframe.html`

## Verification

- Opened the static HTML wireframe locally and verified the canvas renders.
- Ran `git diff --check`.
