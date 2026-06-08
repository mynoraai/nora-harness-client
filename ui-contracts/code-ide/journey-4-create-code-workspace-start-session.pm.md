# Journey 4: Create A Code Workspace And Start A Code Session PM Review

## PM Context

### Current Work

This PM review covers the first Code-mode adoption path: a user starts in the main app, switches from Chat to Code, creates a workspace from a local directory, lands in a new code conversation, and sends the first task to a coding runtime.

### Current User-Visible Behavior

After onboarding, the app opens in Chat by default. The user can switch the sidebar mode from Chat to Code. If the user has no Code workspaces yet, the Code sidebar shows an empty state with a Create Workspace action. Creating a workspace opens a dialog where the user chooses a local folder and can edit the workspace name. After creation, the app opens a New Conversation tab for that workspace and shows the Code composer.

The composer lets the user choose a runtime: OpenClaw, Claude Code, or Codex. New Code draft sessions default to Claude Code. The user can pick a model, type a task, attach supported files, and send. If a Code session has no working directory, the app asks the user to choose one before sending.

### Implementation Maturity

`implemented`

- Current user-visible evidence: Code mode, empty workspace state, create-workspace dialog, local folder picker, derived workspace name, active New Conversation tab, composer, runtime selector, model selector, and first-send routing all exist today.
- Coverage note: the active Journey 4 guard covers the happy path through first send at the runtime boundary. Streamed assistant reply rendering is intentionally covered elsewhere, not in this journey.
- Proposed assumptions: the wireframe adds PM-facing framing for first-run Code adoption and highlights places where product language may need to be clearer. It does not propose a new backend.
- Dependencies: no new NoraCloud, Mobile, Firmware, or Application Harness dependency is required for this wireframe. Real first-send success still depends on the selected runtime being installed, authenticated, and ready.

### PM Decision To Unblock

Confirm whether the first Code-mode journey should remain a workspace-first flow with an automatically opened draft conversation and Claude Code as the default runtime.

### Discussion Focus

Review the user's first Code-mode path, the visibility of workspace creation, how much runtime choice should be explained before first send, and how the product recovers when directory or runtime readiness fails.

### Discussion Boundaries

- PM should decide: workspace-first versus task-first entry, whether Claude Code should remain the default, how visible runtime choice should be, what the user should see after creating a workspace, and how much recovery guidance appears before first send.
- PM should not debate: full file tree/diff/terminal behavior, Agent Authoring, hardware device panels, provider credential setup, runtime implementation internals, or streamed reply rendering details.

### PM Decision Points

<table>
  <tr>
    <td bgcolor="#f8d7da"><strong>Blocking PM decision:</strong> Should Code mode require creating or selecting a workspace before first send, or allow users to type a task first and choose a folder only at send time?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should new Code sessions default to Claude Code, or should first-time users explicitly choose OpenClaw, Claude Code, or Codex?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> After workspace creation, should the sidebar show a visible draft conversation row immediately, or is the active New Conversation tab enough?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should the runtime picker be treated as a primary setup choice or stay compact inside the composer?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should runtime readiness failures be shown before the user types, or only after Send/Retry?</td>
  </tr>
</table>

### Proposed Design

The wireframe preserves the current Journey 4 shape:

1. Start in Chat after onboarding.
2. Switch to Code in the sidebar.
3. See an empty Code workspace state when no workspaces exist.
4. Open Create Workspace.
5. Choose a local folder and optionally edit the derived workspace name.
6. Create the workspace.
7. Land in an active New Conversation tab with a code composer.
8. Select or accept the runtime and model.
9. Type the first task and send.
10. The first runtime turn starts for that workspace.

The PM-facing addition is clearer framing around the onboarding promise: "pick a local project, then assign a task." It also separates required setup from optional controls and highlights recovery states for canceled folder selection, missing workspace, unavailable model, and runtime readiness.

### Benefits

- Makes the first Code-mode value path concrete and reviewable.
- Keeps local folder choice explicit before a coding agent can operate on files.
- Opens an active tab and composer immediately after workspace creation, reducing dead space.
- Preserves runtime flexibility without creating a separate chat UI.
- Gives PM a focused decision set for default runtime, draft visibility, and recovery language.

### Downsides / Tradeoffs

- Requiring workspace creation up front adds friction for users who want to type the task first.
- Defaulting to Claude Code may be efficient for many users but opaque if they expected Codex or OpenClaw.
- A compact runtime picker can hide an important first-run choice.
- If the sidebar does not visibly show the draft conversation row, users may wonder whether the workspace has a session yet.
- Runtime readiness failures can feel late if the user only discovers them after composing a task.

### Open Questions

- Should first-time Code mode show a short "choose project first" explanation in the empty state?
- Should the default runtime be remembered globally, per workspace, or per session?
- Should the first New Conversation row be visible in the workspace group before the draft is sent?
- Should a runtime readiness card appear as soon as the user enters Code mode?

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/feat/runtime-workbench/ui-contracts/code-ide/journey-4-create-code-workspace-start-session.wireframe.html)

File: `ui-contracts/code-ide/journey-4-create-code-workspace-start-session.wireframe.html`
