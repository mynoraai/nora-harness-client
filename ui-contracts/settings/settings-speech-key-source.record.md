# Journey 17: Settings — Speech (STT/TTS) Key Source

This record is the textual source of truth for the wireframe of the same name. It describes how a user,
in the Voice settings tab, chooses per speech capability (Speech-to-text and Text-to-speech) whether the
cloud agent uses the **platform default** key or the user's **own uploaded key**. The user key is
uploaded encrypted and stored sealed by the platform; the user key wins over the platform default when
the agent runs.

This flow is **not implemented yet**; the Voice tab exists (currently hidden) but the speech key-source section is proposed.

## Numbering And Route Tables

Main steps use plain numbers. Branches, detail states, state-language notes, and recovery states use
decimal IDs scoped to the step where the state starts.

### Main path states

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| -- | ---- | ----------- | ------------------ | ----------- | ---------------- | ------------------- | ----------------- |
| 1 | Main step | — | Speech keys section entry | Open Settings → Voice → Speech keys | Speech-to-text and Text-to-speech rows, each with a source badge | Reads the saved source per capability | 2 |
| 2 | Main step | 1 | Review the active source | Read the two source badges | Each row shows "Platform default" or "Your key" (+ provider for TTS) | none | 3 |
| 3 | Main step | 2 | Choose a source | Toggle a row to "Use my own key" | Row expands a masked key field (and a provider picker for TTS) | Stages the choice, not yet saved | 4 |
| 4 | Main step | 3 | Upload the key | Click "Save key" | Uploading / validating indicator on the row | Uploads the key encrypted and validates it | 5 |
| 5 | Main step | 4 | Active-source end state | — | Row shows "Your key ✓" and a short fingerprint, or "Platform default" | Saved; the agent uses it on its next start | stable |

### Branch states under step 3

| ID | Type | Parent step | What it represents | Trigger | Visible UI state | Allowed actions | Recovery / next | Blocks progress |
| -- | ---- | ----------- | ------------------ | ------- | ---------------- | --------------- | --------------- | --------------- |
| 3.1 | Branch | 3 | Speech-to-text — use my own key | Toggle STT to "my key" | Masked key field for the STT provider | Enter key, Save, Cancel | 4 on save | No |
| 3.2 | Branch | 3 | Text-to-speech — use my own key | Toggle TTS to "my key" | Provider picker + masked key field | Pick provider, Enter key, Save, Cancel | 4 on save | No |
| 3.3 | Branch | 3 | Revert a capability to platform default | Toggle a row back to "Platform default" | Confirm prompt: user key will be removed | Confirm, Cancel | 5 on confirm | No |

### Detail states

| ID | Type | Parent step | What it represents |
| -- | ---- | ----------- | ------------------ |
| 4.1 | Detail state | 4 | How the source is chosen when the agent starts |
| 5.1 | Detail state | 5 | How the key stays hidden and how the source is shown |

### State-language states

| ID | Type | Parent step | What it represents |
| -- | ---- | ----------- | ------------------ |
| 0.1 | State language | — | Shared source badges, status words, and actions |

### Recovery states under step 4

| ID | Type | Parent step | What it represents | Trigger | Visible UI state | Allowed actions | Recovery / next | Blocks progress |
| -- | ---- | ----------- | ------------------ | ------- | ---------------- | --------------- | --------------- | --------------- |
| 4.2 | Recovery | 4 | Key invalid / rejected | Validation fails | "Invalid key" inline; previous source stays active | Correct key, Retry, Revert to platform | 4 on retry | Yes, for that capability |
| 4.3 | Recovery | 4 | Upload failed | Storage error | "Upload failed"; previous source stays active | Retry | 4 on retry | Yes, for that capability |
| 0.2 | Recovery summary | — | Correct / retry / revert / leave pattern | any failure | Section stays open, previous source intact | Correct, Retry, Revert, Leave | back to 4 or 2 | — |

## Route Table

| Route | Composition | Result / next state |
| ----- | ----------- | ------------------- |
| Bring your own STT key | 1 → 2 → 3 → 3.1 → 4 → 5 | STT runs on the user's key |
| Bring your own TTS key | 1 → 2 → 3 → 3.2 → 4 → 5 | TTS runs on the user's key |
| Invalid key, correct, retry | 1 → 2 → 3 → 3.1 → 4 → 4.2 → 4 → 5 | User's key accepted after correction |
| Upload failed, retry | 1 → 2 → 3 → 3.2 → 4 → 4.3 → 4 → 5 | User's key saved after retry |
| Revert to platform default | 1 → 2 → 3 → 3.3 → 5 | Capability runs on the platform default |

## Main Path

### 1 Speech Keys Section Entry

The user opens Settings, goes to the Voice tab, and finds a "Speech keys" group listing two
capabilities: Speech-to-text and Text-to-speech. Each shows the source the cloud agent will use. The
default for both is Platform default, so nothing is required to keep using the built-in key.

### 2 Review The Active Source

Each row shows a source badge — "Platform default" (the built-in platform key) or "Your key" (the user's
uploaded key). Text-to-speech also shows which provider the key is for.

### 3 Choose A Source

Toggling a row to "Use my own key" expands that capability's entry (3.1 or 3.2). The choice is staged
and not committed until the key is saved in step 4. Toggling back to Platform default starts the revert
branch (3.3).

### 4 Upload The Key

On save, the key is uploaded encrypted and validated. On success the row moves to the active-source end
state (5); on failure it moves to a recovery state (4.2 or 4.3).

### 5 Active-Source End State

The row reads "Your key ✓" with a short fingerprint, or "Platform default". This is the source the agent
uses on its **next** start; a running agent is not changed mid-session.

## Branch Journeys

### 3.1 Speech-to-text — Use My Own Key

A masked key field for the speech-to-text provider. The field never shows a previously saved value; it
offers "replace". Saving proceeds to step 4.

### 3.2 Text-to-speech — Use My Own Key

A provider picker plus a masked key field. The saved key belongs to the chosen provider; changing the
provider shows that provider's own source state. Saving proceeds to step 4.

### 3.3 Revert To Platform Default

Toggling a capability back to Platform default asks the user to confirm, then removes the user key for
that capability. The next agent start uses the platform default.

## Detail States

### 4.1 How The Source Is Chosen When The Agent Starts

When the agent starts, it uses the user's uploaded key for a capability if one exists, otherwise the
platform default. This Settings section decides which key is on file; it does not change a currently
running agent.

### 5.1 How The Key Stays Hidden And How The Source Is Shown

Uploaded keys are always masked in the UI, stored sealed by the platform, and never shown again in
readable form. The row identifies the active source by name and a short fingerprint, never by the key
value.

## State Language

### 0.1 Shared Source Badges, Status Words, And Actions

- Source badge: **Platform default** · **Your key** (Text-to-speech also shows the provider).
- Status: **Saved ✓** · **Uploading…** · **Invalid key** · **Upload failed**.
- Actions: **Use my own key** · **Save key** · **Replace** · **Revert to platform default** · **Retry**.
- The active source is always named by source and fingerprint, never by showing the key.

## Errors And Recovery

### 4.2 Key Invalid / Rejected

Validation rejects the key. The row shows "Invalid key" inline; nothing is saved and the previously
active source stays in effect. After correcting the key, Retry returns to step 4.

### 4.3 Upload Failed

A storage error prevents saving. The row shows "Upload failed" with Retry; nothing partial is saved and
the previous source stays in effect.

### 0.2 Correct, Retry, Revert, Or Leave Pattern

Every failure keeps the user in the section with the previous active source intact, names the cause, and
offers the matching action — correct and retry a bad key, retry a storage error, or revert to platform
default to step out. Leaving the section discards a staged-but-unsaved key change.

## Wireframe

The wireframe of this record is `settings-speech-key-source.wireframe.html` (drawn from this confirmed
record, using the current NoraHarness desktop settings visual style as the reference).
