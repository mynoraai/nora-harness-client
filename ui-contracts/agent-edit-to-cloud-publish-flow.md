# AUTH-05 Agent Edit To Unified Publish Center

This document defines the user journey from the in-app **Edit Agent** workspace
to reviewed catalog state, production package publish, and firmware publish.
It is the cross-file narrative companion to
`agent-authoring/publish-export.md`.

Current source of truth: `electron-user-journeys-hierarchy-v2/user-journey-inventory.md`, Journey 09.

## User Journey

### 1. User opens Edit Agent from Code mode

The user is in Code mode with a workspace open. When the workspace root resolves
to an agent project, the workspace header exposes **Edit Agent**. Clicking it
opens an Agent Authoring tab without leaving the Code workbench.

If the workspace is not an agent project, **Edit Agent** is not shown. Publishing
is intentionally scoped to an authorable agent workspace.

### 2. User edits agent content and sees review state

The Agent Authoring tab keeps the section rail, active editor, file path,
Revert/Save controls, right-side file panel, and bottom terminal/log area in the
same workbench frame.

After review approval, the header can show an **Approved** badge near the authoring
actions while the user remains in the editor. This badge means the reviewed
catalog/package state is approved; it does not mean firmware or optional media
has also been published.

### 3. User opens Publish

The user clicks **Publish** from Agent Authoring. If the user is signed out, the
publish flow blocks on account login and returns to the publish context after
login succeeds.

When signed in, the publish center opens as a modal over the Agent Authoring tab.
The current reviewed state is shown as row-level badges.

```text
----------------------------------------------------------+
| Publish · xiao-zhi · agt_tozatmarubfxs2iu           [x] |
+----------------------------------------------------------+
| 1  App record                 [Approved]        [Edit]   |
|    agt_tozatmarubfxs2iu                              |
+----------------------------------------------------------+
| 2  Icon & screenshots         [Optional]        [Edit]   |
|    add any time                                      |
+----------------------------------------------------------+
| 3  Agent package              [Approved]  [Publish to production] |
|    v0.9.0 · from manifest                         |
+----------------------------------------------------------+
| 4  Firmware                   [Draft]     [Publish firmware]       |
|    from build                                       |
+----------------------------------------------------------+
```

### 4. User edits app record or media

The **App record** row owns catalog identity and text metadata. The **Icon &
screenshots** row owns optional or required media. Clicking **Edit** opens the
relevant edit surface.

The edit surface must preserve unsaved changes, show saving state, disable
duplicate save while saving, and return to the publish center with refreshed row
status after success. Failure leaves the user in context with retry.

### 5. User submits for review and reads the result

The publish center separates review state from production publish state.

- Draft or ready rows can be submitted for review.
- Submitted rows become pending/in-review.
- Review can return approved, rejected, or revoked.
- Approved rows show `Approved` and unlock only the next valid action for that row.

The latest reviewed-state screenshot shows App record and Agent package as
`Approved`, while Firmware remains `Draft`. This is the canonical example of
independent row state.

### 6. User publishes production package or firmware

When **Agent package** is approved, the user can click **Publish to production**.
The row enters publishing, then success or failure. Success refreshes the row and
last-published state.

When **Firmware** has a valid build and metadata, the user can click **Publish
firmware**. Firmware publish is no longer a separate primary journey; it is the
Firmware row inside the same publish center.

## Control Contract

| Control | Required behavior |
| ------- | ----------------- |
| Edit Agent | Visible only for an agent workspace. Opens/focuses Agent Authoring as a Code center tab. |
| Approved badge | Shows reviewed approval state in the authoring header when available. Does not imply every row is published. |
| Save / Save All | Persists authoring file edits. Publish reads from the saved/built workspace state according to the implementation boundary. |
| Publish | Opens the unified publish center. Signed-out users see an auth gate before row actions can submit. |
| App record Edit | Opens metadata editing and returns to refreshed row state after save. |
| Icon & screenshots Edit | Opens media editing/upload and returns to refreshed row state after save. |
| Submit review | Moves the selected row to pending/in-review and later approved/rejected/revoked. |
| Publish to production | Available only for an approved package row. Shows progress, success, or retryable failure. |
| Publish firmware | Available only when firmware build/metadata is ready. Shows progress, success, or retryable failure. |

## State Contract

| State | Required UI |
| ----- | ----------- |
| Authoring idle | Section rail, active file/editor, Revert/Save, right panel, bottom terminal/logs. |
| Review approved in authoring | Header shows `Approved` badge while editor stays usable. |
| Login required | Publish center shows sign-in path and does not submit review/publish actions. |
| Publish center loaded | Four rows are visible: App record, Icon & screenshots, Agent package, Firmware. |
| Optional media | Icon & screenshots can show `Optional` and still allow Edit. |
| Approved app/package | Row badge says `Approved`; package row can expose `Publish to production`. |
| Draft firmware | Firmware row says `Draft`; publish action is disabled or leads to required build/metadata handling unless ready. |
| Saving/editing | The affected edit surface disables duplicate submit and preserves draft on failure. |
| Publishing | The affected row shows progress and blocks duplicate publish. |
| Failed | The affected row keeps error copy and retry/recovery action. |

## Loading And Error Contract

- The publish center must preserve authoring context behind the modal.
- Account login must gate publish submission but should not discard the publish entry.
- Row state is independent. App/package approval must not hide incomplete media or draft firmware.
- Review states and production publish states must use different badges/copy.
- Firmware publish belongs to the Firmware row of this center, not to a separate primary flow.
- Recovery should happen in place whenever possible: edit/resubmit for review problems, rebuild/complete metadata for firmware, retry for transient publish failures.

## Asset Inventory

Screenshot assets under `ui-contracts/assets/screenshots/agent-publish/` have
been refreshed from the current edit-agent publish video and now represent the
unified publish center. Add focused assets when these terminal states are
available:

| Needed asset | Capture setup |
| ------------ | ------------- |
| `edit-agent-approved-header` | Agent Authoring tab after review approval, with `Approved` badge visible. |
| `publish-center-approved-package-draft-firmware` | Publish center modal showing App record approved, Icon & screenshots optional, Agent package approved, Firmware draft. |
| `publish-center-firmware-published` | Firmware row after a successful firmware publish. |
| `publish-center-failed-row` | A row-level failure with retry/recovery action visible. |

## Relationship To Detailed Contract

The detailed row/state contract lives in
`agent-authoring/publish-export.md`. Keep both files synchronized when Journey 09
changes.
