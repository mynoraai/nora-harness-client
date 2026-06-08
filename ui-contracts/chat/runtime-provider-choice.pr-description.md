## Summary

- Add PM artifacts for `Runtime Provider Choice`.
- Decision to unblock: decide whether users should see `OpenClaw` as a peer option beside `Claude Code` and `Codex`, and decide how runtime choice should differ from provider/model choice.
- Scope: partial.

## PM Links

- PM doc: `https://github.com/mynoraai/nora-harness-client/blob/pm/runtime-provider-choice-wireframe/ui-contracts/chat/runtime-provider-choice.pm.md`
- Wireframe preview: `https://htmlpreview.github.io/?https://github.com/mynoraai/nora-harness-client/blob/pm/runtime-provider-choice-wireframe/ui-contracts/chat/runtime-provider-choice.wireframe.html`
- Wireframe source: `https://github.com/mynoraai/nora-harness-client/blob/pm/runtime-provider-choice-wireframe/ui-contracts/chat/runtime-provider-choice.wireframe.html`

## Alignment Check

- PM audience: product owner / design PM.
- Discuss: visible runtime naming, the recommended default, and the separation between runtime choice and provider/model choice.
- Do not discuss: replacing the runtime control plane, changing OpenClaw Gateway vs ACP stdio ownership, or adding a second chat composer.

## Files

- `ui-contracts/chat/runtime-provider-choice.pm.md`
- `ui-contracts/chat/runtime-provider-choice.wireframe.html`

## Verification

- Opened the static HTML wireframe locally and verified the canvas renders.
- Ran `git diff --check`.
