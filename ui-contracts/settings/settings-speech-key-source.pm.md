# Settings — Speech (STT/TTS) Key Source · PM Decision

## PM decision to unblock

Should Settings let a user **bring their own speech key** (for voice input and spoken reply)
separately per capability, with the platform's built-in key as the default — and how should the
active source and its fallback be shown?

## Intended PM audience

Product owner and design PM. (Inferred from the request; the engineering decision record already
fixes the security/storage shape, so what remains is product placement and interaction.)

## Discussion boundaries

Already fixed by engineering / product constraints — **not** up for PM debate here:

- The user key is **encrypted on upload and stored sealed** by the platform; it is never kept in
  readable form anywhere. This matches how the model (LLM) key is already handled.
- When the agent runs, it uses **the user's key if one was uploaded, otherwise the platform's built-in
  key**. The user key always wins over the platform default.
- The platform can still pin its own key for managed deployments.

What PM should decide is **where this lives, how granular it is, how the key is entered/validated, and
how the active source is presented.**

## Non-goals

- The model (LLM) API key already has its own upload path; unifying it into one screen is optional, not
  required here.
- Key rotation schedules, team-shared keys, and billing/usage are out of scope.
- The agent's own connection credential (managed by the platform) is not a user-entered key and is out
  of scope.

## Current state

Today there is **no place for a user to supply their own speech-to-text or text-to-speech key** — the
cloud agent always uses the platform's built-in speech key. A user who wants to use their own speech
provider account cannot. The **Providers** tab already proves the "bring your own key" pattern for model
keys; speech keys need the same.

The **Providers** tab already proves the "bring your own key" pattern (masked field, saved credential);
the same pattern applies to speech keys on the Voice tab.

## Implementation maturity

**Not implemented yet (entry point exists).** The Voice tab exists (though currently hidden) and the
Providers tab proves the key-entry pattern, but the speech key-source interaction itself does not exist
in the app. Every screen below is `proposed`.

## Discussion focus

How a user chooses, per speech capability, between the platform default key and their own key — and how
the app makes the active source and the fallback obvious and safe.

## PM decisions

<table>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision 1 — Placement:</strong> Put "Speech keys" in a dedicated <em>Voice</em> tab, or as a second group inside <em>Providers</em>?</td>
  </tr>
</table>

**Decided: a dedicated Voice tab.** The **Voice** settings tab holds only the speech-to-text and
text-to-speech key configuration — separate from model providers. Nothing else lives on it.

<table>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision 2 — Granularity:</strong> Offer <em>separate</em> source choices for Speech-to-text and Text-to-speech, or one combined "speech key"?</td>
  </tr>
</table>

Recommendation: **separate per capability.** They can use different providers and a user may want their
own key for one but not the other.

<table>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision 3 — Provider choice (both STT and TTS):</strong> Both speech-to-text (openai-asr / qwen-asr) and text-to-speech (openai / qwen-realtime) support more than one provider. Let the user pick the provider for each, or infer it?</td>
  </tr>
</table>

Recommendation: **let them pick, consistently for both.** A key only makes sense against a specific
provider account, and STT and TTS should present the same picker + key shape — not a picker for one and
a bare key for the other.

<table>
  <tr>
    <td bgcolor="#fff3cd"><strong>PM decision 4 — Validation:</strong> Validate the key when saved (a quick test), or accept it and let the agent surface a failure later?</td>
  </tr>
</table>

Recommendation: **quick validate on save**, so a bad key is caught in Settings, not silently at the next
agent run.

<table>
  <tr>
    <td bgcolor="#f8d7da"><strong>Blocking PM decision 5 — Fallback visibility:</strong> When a user's key is invalid, should the agent silently fall back to the platform key, or must the user see that their key failed and their speech is running on the platform default?</td>
  </tr>
</table>

Recommendation: **never silently fall back to the platform key when the user chose "my key".** Show the
failure and the active source. Silent fallback hides that the user is being billed to / limited by the
wrong account and makes "why isn't my key used" undiagnosable.

## Proposed design

A "Speech keys" group on the Voice tab with two rows — **Speech-to-text** and **Text-to-speech** —
each showing a source badge: **Platform default** or **Your key**. Toggling a row to "Use my own key"
reveals a provider picker + masked key field (both STT and TTS support more than one provider). Saving uploads the key
encrypted; a quick validation confirms it; the row then shows "Your key ✓" with a short fingerprint.
Reverting to platform default removes the user key after a confirm. Each row always names the *source*,
never the key value.

## Assumptions and dependencies

- Assumption: the same masked-field + save pattern used for model provider keys is acceptable for speech
  keys.
- Dependency: the platform accepts an encrypted speech-key upload and resolves user-key-over-default at
  agent start (engineering decision record covers this).
- Assumption: a running agent adopts a changed key on its **next** start, not mid-session.

## Benefits

- Users can use their own speech provider account (their own cost/limits/voice), matching the model-key
  experience.
- Keys are handled with the same encrypted, sealed posture as model keys — no readable key in config.
- The default cloud agent still works with zero input (platform default).

## Downsides / tradeoffs

- Two more source choices to understand (STT and TTS separately).
- A user key that later becomes invalid fails the user's own speech until fixed — mitigated by decision 5
  (make the failure and source visible).
- Two parallel "bring your own key" surfaces (model keys and speech keys, unless grouped together) unless
  a future pass unifies them.

## Open questions

- Do we need a per-provider "test voice" preview for text-to-speech on save, or is a plain validation
  enough?
- Should the section also show the model-provider key status for a single "keys" overview, or stay
  speech-only?

## Links

- Engineering decision record and journey for this flow live with the sprint's M4 work; the wireframe
  preview link will be added once the wireframe is drawn from the confirmed record.
