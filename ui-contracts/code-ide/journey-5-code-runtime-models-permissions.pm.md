# Journey 5: Code Mode Runtime, Models, And Permissions PM Review

## PM Context

### Current Work

This PM review covers the Code-mode runtime choice flow: a user works inside a Code conversation, chooses OpenClaw, Claude Code, or Codex, picks a compatible model, sends a task, then handles runtime health and permission prompts without leaving the main conversation.

### Current User-Visible Behavior

Code mode already gives users a workspace-backed conversation with a compact runtime picker and model picker in the composer. The same message list renders the user's task, assistant output, tool activity, status blocks, permission requests, modified-file summaries, and visible errors. New Code sessions can use Claude Code or Codex without requiring OpenClaw to complete the turn. OpenClaw remains available as a runtime choice for sessions that use the OpenClaw path.

When a runtime or model is not ready, the app can show model loading, retry, unavailable model text, no enabled models, runtime status blocks, workspace guidance, and permission cards. Runtime choice can become locked once a session has a native runtime identity or resume state, so the user cannot casually switch ownership of an existing transcript.

### Implementation Maturity

`partial`

- Current user-visible evidence: runtime picker, model picker, Code-session default runtime, runtime-specific model catalogs, image attachment support for ACP-backed runtimes, unified message rendering, runtime status blocks, active-run recovery, permission cards, and distinct failure categories exist today.
- Proposed assumptions: the wireframe adds PM-facing labels for readiness, locking, and recovery so users can understand why a runtime or model is unavailable. It also proposes a clearer recovery drawer for runtime health states without changing the main conversation surface.
- Dependencies: no new NoraCloud, Mobile, Firmware, or Application Harness dependency is required for this review. Real success still depends on the selected local runtime being installed, authenticated, allowed to access the workspace, and able to answer permission prompts.

### PM Decision To Unblock

Confirm how much runtime readiness, model availability, permission, and locked-session state should be surfaced inline in the Code composer versus in a separate recovery detail panel.

### Discussion Focus

Review the user-facing language and placement for runtime selection, model selection, runtime lock, permission prompts, and recovery after install, sign-in, workspace, permission, or OpenClaw readiness failures.

### Discussion Boundaries

- PM should decide: whether runtime readiness is a compact composer concern or a visible detail panel, what language users should see for each failure category, whether locked runtime sessions need a tooltip or full callout, and how permission decisions should recover after allow/deny.
- PM should not debate: process ownership, transport implementation, transcript storage, internal event naming, runtime protocol details, OpenClaw internals, or adding a second chat UI.

### PM Decision Points

<table>
  <tr>
    <td bgcolor="#f8d7da"><strong>Blocking PM decision:</strong> Should runtime readiness failures block Send before the user composes, or appear only after the first failed send/retry?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should the runtime picker remain compact in the composer, or should Code mode expose a larger runtime readiness panel?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> What user-facing words should represent the six failure families: install, sign in, startup, permission, workspace, and OpenClaw readiness?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should a locked runtime session show only a disabled picker tooltip, or a persistent explanation near the conversation header?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> After a permission denial, should the conversation offer Retry, edit task, or change permission defaults as the primary recovery action?</td>
  </tr>
</table>

### Proposed Design

The wireframe preserves the current Journey 5 shape:

1. Start inside a Code conversation.
2. Choose a runtime from the compact composer control.
3. Load the model list for that runtime.
4. Choose or accept a model.
5. Send a task with text and optional supported images.
6. The message list streams output, tool activity, status, permission prompts, and modified-file summaries in one place.
7. A permission card pauses protected work until the user allows or denies.
8. If the runtime is not ready, a visible recovery card explains the cause and the next action.
9. Once the session is bound to a runtime, the picker becomes locked for that session.

The proposed addition is not a new chat surface. It is clearer product language for readiness, locked ownership, and recovery, with a lightweight detail panel only when the compact composer state cannot explain the problem.

### Benefits

- Keeps OpenClaw, Claude Code, and Codex in one Code conversation model.
- Reduces user confusion when model availability changes after runtime selection.
- Makes permission pauses visible in context instead of hiding them in logs.
- Gives users a recovery action without exposing implementation details.
- Protects transcript ownership by making runtime lock understandable.

### Downsides / Tradeoffs

- More visible readiness language can make the composer feel busier.
- A detail panel adds another place PM must maintain state vocabulary.
- Blocking Send before compose may feel safer but can slow exploratory users.
- Allow/deny recovery language can imply guarantees that depend on the selected runtime's behavior.
- Explaining runtime lock may distract users who never switch runtimes mid-session.

### Open Questions

- Should runtime readiness be probed automatically when the picker opens?
- Should model list failures use one generic retry message or runtime-specific copy?
- Should permission cards include "allow once" and "always allow" as separate visible actions when available?
- Should locked sessions offer "Start a new session with another runtime" as the primary escape hatch?

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/feat/user-journeys/ui-contracts/code-ide/journey-5-code-runtime-models-permissions.wireframe.html)

File: `ui-contracts/code-ide/journey-5-code-runtime-models-permissions.wireframe.html`
