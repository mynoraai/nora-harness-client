# Journey 1: First Launch And Base Setup PM Review

## PM Context

### Current Work

This PM review covers the first-run setup path a new NoraHarness desktop user sees before the normal app is available. The wireframe is meant to make the setup order, required choices, optional choices, and final handoff into Chat easy to review in one pass.

### Current User-Visible Behavior

On a first launch, the app shows a setup flow instead of the main workspace. A local connection status banner can appear above the setup flow when the local app service is reconnecting or unavailable. The user starts on Welcome, connects an AI provider, optionally chooses personality settings, chooses whether to enable notifications, sees a completion screen, and then enters Chat.

Provider setup is the main required step. The user must choose a provider, enter a key or local service address, choose a default model, and either verify the connection or intentionally skip verification before continuing. Personality and notifications can be skipped.

### Implementation Maturity

`implemented`

- Current user-visible evidence: the full five-step first-launch flow exists in the desktop app, including Welcome, Provider, Personality, Notifications, Completion, local connection banner, and final Chat handoff.
- Coverage note: the current journey guard strongly protects Welcome, Provider entry, disabled Continue, default provider selection, and Back to Welcome. The complete five-step run and several recovery states are not yet fully guarded by end-to-end automation.
- Proposed assumptions: this wireframe does not add a new product area. It proposes PM-facing language and decision framing for how the existing flow should be reviewed.
- Dependencies: no new backend, mobile, firmware, Application Harness, or NoraCloud dependency is required for the wireframe itself.

### PM Decision To Unblock

Confirm whether Journey 1 should keep Provider setup as the only hard blocker while allowing Personality and Notifications to remain optional before the user lands in Chat.

### Discussion Focus

Review the first-run setup sequence, what each step asks from the user, what can be skipped, what error recovery is visible, and what success state should define "ready to start."

### Discussion Boundaries

- PM should decide: required versus optional setup steps, copy around verification skipping, whether the local connection banner should be visible during onboarding, and whether the completion screen should go directly to Chat.
- PM should not debate: the broader Chat or Code workspace IA, Voice, Channels, Advanced settings, provider API internals, credential storage mechanics, or post-onboarding Settings management.

### PM Decision Points

<table>
  <tr>
    <td bgcolor="#f8d7da"><strong>Blocking PM decision:</strong> Is Provider setup the only required step before Chat, or should users be allowed into Chat without a configured provider?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should "Skip verification" remain visible as an advanced escape hatch, or move behind a help/error state?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should Personality stay before Notifications, or should optional steps be collapsed into one "Customize later" moment?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should the local connection banner stay visible during onboarding when the setup screens can still be used?</td>
  </tr>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision:</strong> Should Completion always land in Chat, or offer a first-run choice between Chat and Code?</td>
  </tr>
</table>

### Proposed Design

The wireframe preserves the existing five-step sequence:

1. Welcome introduces the app and starts setup.
2. Provider asks for the first usable AI service, model, and verification state.
3. Personality lets the user choose a tone, add optional instructions, preview the result, or skip.
4. Notifications asks for permission, with "Maybe later" as a first-class path.
5. Completion confirms the selected provider/model and sends the user into Chat.

The PM-facing addition is stronger state language around what is required, what is optional, and what recovery action the user can take when provider setup fails. The design also calls out that current automation coverage protects the start of the path more strongly than the whole five-step journey.

### Benefits

- Makes the first-run promise explicit: configure one usable provider, then start chatting.
- Avoids treating notification permission as a blocker.
- Keeps optional personalization from delaying the first successful chat.
- Gives PM a clear place to decide whether verification skipping is acceptable.
- Separates current behavior from coverage gaps and proposed review decisions.

### Downsides / Tradeoffs

- A five-step setup can feel long if optional steps are not visually lightweight.
- Keeping the local connection banner visible may confuse users if setup is still usable.
- Letting users skip verification can defer failure until the first message.
- Sending every user directly into Chat may underserve first-run users who installed NoraHarness primarily for Code or hardware authoring.

### Open Questions

- Should provider verification be mandatory for hosted providers, with skip only for local or network-constrained cases?
- Should the final screen include "Start coding" or "Open Code mode" for developer-first installs?
- Should the coverage gap become a release requirement before treating Journey 1 as fully protected?

## Wireframe Preview

Preview: [Open wireframe](https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/feat/runtime-workbench/ui-contracts/onboarding/journey-1-first-launch-base-setup.wireframe.html)

File: `ui-contracts/onboarding/journey-1-first-launch-base-setup.wireframe.html`
