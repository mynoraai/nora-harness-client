# Runtime Provider Choice PM Review

## PM Context

### Current Work

This review covers the composer area in NoraHarness Chat/Code mode: the user types a task, optionally attaches files, chooses the coding runtime, chooses a model for that runtime, and sends the turn. The PM question is about product language and choice structure, not about whether the composer exists.

PM decision to unblock: decide whether users should see `OpenClaw` as a peer option beside `Claude Code` and `Codex`, and decide how runtime choice should differ from provider/model choice.

### Current Behavior From Code

- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:169`: the composer defines code runtime choices as `OpenClaw`, `Claude Code`, and `Codex`.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:371`: the selected runtime is read from session metadata before rendering the picker.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:424`: changing runtime triggers a runtime-scoped model catalog load.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:831`: runtime selection is mounted as a compact composer control.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:868`: model selection is a separate compact composer control.
- `apps/electron/src/renderer/src/components/chat/ChatComposer.tsx:973`: thinking selection is shown only for non-code OpenClaw sessions.
- `apps/electron/src/main/runtime-control-plane.ts:41`: main process registers `openclaw` with label `OpenClaw` and kind `openclaw-gateway`.
- `apps/electron/src/main/runtime-control-plane.ts:49`: main process registers `claude` as an ACP stdio runtime.
- `apps/electron/src/main/runtime-control-plane.ts:72`: main process registers `codex` as an ACP stdio runtime.
- `apps/electron/src/shared/runtime-types.ts:11`: `openclaw` is the Gateway-backed runtime id; the rest are ACP stdio runtime ids.
- `apps/electron/src/shared/runtime-types.ts:33`: runtime diagnosis already separates binary, auth, handshake, permission, workspace, and gateway failures.
- `apps/electron/src/renderer/src/components/settings/ProvidersTab.tsx:65`: Settings Providers lists API/model providers such as Anthropic, OpenAI, Google, OpenRouter, MiniMax, and Local Models.
- `apps/electron/src/renderer/src/components/settings/ProvidersTab.tsx:316`: Settings builds enabled model refs from configured providers.
- `apps/electron/src/renderer/src/components/settings/ProvidersTab.tsx:716`: the provider editor has provider, key/base URL, region, and enabled model controls.
- `apps/electron/src/main/runtime/model-catalog.ts:9`: Codex has runtime-owned static model defaults.
- `apps/electron/src/main/runtime/model-catalog.ts:30`: Claude has runtime-owned static model defaults.

### Implementation Maturity

`partial`

- Current evidence: runtime registry, runtime picker, model picker, provider settings, runtime-scoped models, session lock behavior, and diagnosis types exist.
- Proposed assumptions: the wireframe introduces product-facing naming, recommendation copy, and a two-layer selection model that separates "who drives the coding session" from "which model/provider account powers it".
- Dependencies: no new backend state is required for the PM decision. Implementing the final copy may require renderer changes, Settings deep links, and tests for runtime/provider wording.

### Discussion Focus

The PM review should decide what the user thinks they are choosing. The proposed direction is that the composer first asks for a coding runtime, then asks for the model/account options available to that runtime.

### Discussion Boundaries

- PM should decide: visible runtime names, whether `OpenClaw` is exposed, the default recommendation, and how users recover when a runtime or provider model is unavailable.
- PM should not debate: the current engineering boundary where OpenClaw is Gateway-backed and Claude/Codex are ACP stdio runtimes; adding a second prompt surface; new backend error codes.
- Non-goal: this artifact does not redesign message cards, permission cards, tool rendering, or provider credential storage.

### PM Decision Points

<table>
  <tr>
    <td bgcolor="#f8d7da"><strong>Blocking PM decision:</strong> Should the user-facing runtime list say OpenClaw, or should that option be renamed to NoraHarness Default / Local Agent while `openclaw` remains internal?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should runtime choices be presented as coding backends, coding agents, or execution modes?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should provider/model choice be nested under the selected runtime, or remain a separate composer pill?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should Claude Code and Codex appear when not installed, with setup guidance, or stay hidden until ready?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> What should be the default recommendation for hardware-authoring work?</td>
  </tr>
</table>

### Proposed Design

The wireframe keeps one composer and splits the decision into two visible layers:

1. Runtime: who runs this coding session.
2. Model/provider: which model or account is used by that runtime.

The user-facing runtime options should avoid leaking "OpenClaw is the backend" unless PM intentionally wants OpenClaw to be a product-visible coding runtime. Two naming options are shown:

- Option A: `OpenClaw`, `Claude Code`, `Codex`.
- Option B: `NoraHarness Default`, `Claude Code`, `Codex`, with `openclaw` kept as the internal runtime id.

The proposed default for PM review is Option B because it keeps NoraHarness as the product center and treats OpenClaw as implementation detail for the Gateway-backed path.

### Benefits

- Users understand that Claude Code and Codex are alternative coding runtimes, not just model providers.
- NoraHarness can hide inherited OpenClaw implementation details while preserving existing runtime ids and contracts.
- Provider setup remains clear: API keys and enabled models live in Settings Providers; composer model choice uses only what is available.
- The PM decision can be implemented mostly in renderer copy and selector hierarchy.

### Downsides / Tradeoffs

- Renaming OpenClaw in the UI creates a vocabulary split between internal ids and user-facing labels.
- "NoraHarness Default" may sound vague unless the picker explains why it is recommended.
- Keeping runtime and model as separate pills is compact but can make the hierarchy less obvious.
- Nesting model choice under runtime is clearer but adds an extra interaction.

### Open Questions

- Should "NoraHarness Default" mention local Gateway anywhere in secondary copy?
- Should OpenClaw appear in advanced/debug surfaces for supportability?
- Should Settings Providers be renamed or grouped to distinguish runtime credentials from model API providers?

## Wireframe Preview

Preview: https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/runtime-provider-choice-wireframe/ui-contracts/chat/runtime-provider-choice.wireframe.html

File: `ui-contracts/chat/runtime-provider-choice.wireframe.html`

Note: the preview URL resolves after this branch is pushed to GitHub.
