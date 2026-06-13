# Journey 09: Unified Publish Center / Catalog Review / Firmware Publish

This Markdown file is the text counterpart to `catalog-review-publish.wireframe.html`. It documents the current Journey 09 publish flow as one unified publish center. Firmware Publish is part of this journey and is not a separate journey.

## Journey Scope

User entry: the user clicks `Publish` in Agent Authoring, or returns to publishing from an existing publishing status row.

User goal: the user confirms sign-in state, reviews App record, Icon & screenshots, Agent package, and Firmware in one publish center, edits app information or media when needed, submits review, handles review outcomes, and publishes production agent package or firmware from the same center.

End state: the publish center shows a stable, actionable state for App record, Icon & screenshots, Agent package, and Firmware: unsubmitted, in review, approved, rejected, revoked, ready for production, published, failed, or retryable.

Implementation maturity: Implemented.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches and nested states use decimal IDs scoped to the step where the state starts. Sequential follow-up states use another decimal level.

Main path states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | `Journey 09` | Publish entry and sign-in check. | Clicks Publish or opens a publishing status row. | Agent Authoring stays visible while the publish surface opens and checks whether the user can publish. | The client starts the publish journey and resolves account readiness before upload or review submission is allowed. | `1.1 or 1.2` |
| `2` | Main path | `Journey 09` | Unified publish center overview. | Reviews the rows and chooses the next action. | The center lists App record, Icon & screenshots, Agent package, and Firmware with row status, primary action, and recovery copy. | The client refreshes each row from local package state and remote review or publish state. | `2.1, 2.2, 2.3, 2.4, or 3` |
| `3` | Main path | `Journey 09` | App record and media editing. | Opens Edit from App record or Icon & screenshots. | An edit surface shows Display name, Description, icon, and screenshots with save and cancel actions. | The client opens editable catalog-facing fields without leaving the publish center. | `3.1, 3.2, or 4` |
| `4` | Main path | `Journey 09` | Review readiness and submission. | Reviews App record and Agent package readiness, then submits review when enabled. | Review controls are enabled only when required app information, media, and package readiness satisfy the publish center checks. | The client verifies row readiness before allowing review submission. | `4.1, 4.2, 4.3, or 4.4` |
| `5` | Main path | `Journey 09` | Review outcome refresh. | Opens or refreshes the publish center after review status changes. | The publish center updates badges, row copy, and available actions for approved, rejected, or revoked results. | Review results replace pending state and determine whether production publish can proceed. | `5.1, 5.2, or 5.3` |
| `6` | Main path | `Journey 09` | Production package publish. | Clicks Publish to production from the approved Agent package row. | The row or dialog shows package identity, version, digest, and final publish action before upload. | The client confirms approved package readiness before production upload begins. | `6.1, 6.2, or 6.4` |
| `7` | Main path | `Journey 09` | Firmware publish. | Opens Publish firmware from the Firmware row. | The firmware publish surface stays attached to the same center and shows version, channel, device metadata, and readiness. | Firmware metadata is refreshed before publish is enabled. | `7.1, 7.2, or 7.3` |
| `8` | End state | `Journey 09` | Stable publish center. | Pauses, continues another row, retries, opens a published URL, or closes the center. | The center shows latest App record, Icon & screenshots, Agent package, and Firmware states without requiring the user to remember prior dialogs. | The client rests with the latest known row readiness, review state, production package state, and firmware state. | `End or 2` |

Branch journey states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1.1` | Branch journey | `1 Publish Entry And Auth Gate` | Signed-in user continues into the publish center. | Waits for the sign-in check to finish. | The publish surface shows signed-in context and continues to package, review, and row readiness. | Account readiness is satisfied for publish actions in this session. | `2` |
| `1.2` | Branch journey | `1 Publish Entry And Auth Gate` | Signed-out user reaches a login gate. | Clicks Sign in or closes the publish surface. | The publish surface explains that sign-in is required before catalog review or publish actions can continue. | The client blocks review and publish submission while preserving the user entry context. | `1.2.1 or previous usable surface` |
| `1.2.1` | Branch follow-up | `1.2 Signed Out Login Gate` | User returns from sign-in. | Completes sign-in and returns to NoraHarness. | The publish surface resumes from the gate instead of requiring the user to restart from Agent Authoring. | Account readiness refreshes and the pending publish journey continues. | `2` |
| `3.1` | Branch journey | `3 App Record And Media Editing` | Unsaved app record or media changes. | Edits text, icon, or screenshots. | Changed fields remain visible, save becomes available, and the user can cancel before committing. | The row enters dirty state and keeps edits local until save succeeds. | `3.2 or 2` |
| `3.2` | Branch journey | `3 App Record And Media Editing` | Save in progress. | Clicks Save. | Save is disabled or shows saving progress while the edit surface stays open. | The client submits the changed app information or media and prevents duplicate save. | `3.3 or 3.4` |
| `3.3` | Branch follow-up | `3.2 Save In Progress` | Save succeeds. | Reviews the saved feedback or closes the edit surface. | Saved feedback appears and the matching publish center row refreshes to ready, optional, or still missing. | Dirty state clears and row completeness refreshes. | `2 or 4` |
| `4.1` | Branch journey | `4 Review Readiness And Submission` | App record review submission. | Clicks the submit action for app information review. | The submit action becomes pending, and the row communicates that review is being requested. | App record review submission is in flight and duplicate submission is blocked. | `4.3 or 4.4` |
| `4.2` | Branch journey | `4 Review Readiness And Submission` | Agent package review submission. | Clicks the submit action for the agent package. | Package summary, version, digest, and capability information remain visible while the submit action becomes pending. | Agent package review submission is in flight and duplicate submission is blocked. | `4.3 or 4.4` |
| `4.3` | Branch follow-up | `4.1 App Record Review Submission` | Submitted and in review. | Waits or returns later to check status. | The relevant row shows submitted or in review status, and later editing is constrained. | The client records the review request and refreshes status until an outcome appears. | `5` |
| `5.1` | Branch journey | `5 Review Outcome Refresh` | Approved review state. | Reviews the approved row and proceeds to production when desired. | App record and/or Agent package rows show approved state; production actions become available when all gates are satisfied. | Approved state unlocks the matching production publish path. | `6 or 8` |
| `5.2` | Branch journey | `5 Review Outcome Refresh` | Rejected review state. | Reads rejection copy, edits the needed information, and prepares resubmission. | The row shows rejected state, keeps the relevant details visible, and offers edit or retry paths instead of production publish. | Production action stays blocked for the rejected item. | `5.2.1` |
| `5.2.1` | Branch follow-up | `5.2 Rejected Review State` | Rejection correction and resubmission. | Updates app information, media, or package version and submits again. | The publish center returns the item to editable or review-ready state after the user corrects the rejection reason. | Corrected content can be saved and resubmitted through the review path. | `3 or 4` |
| `5.3` | Branch journey | `5 Review Outcome Refresh` | Revoked review state. | Reads revoked status and starts a fresh correction path if allowed. | The row shows revoked state and removes production publish until the item is made eligible again. | Previously approved readiness is no longer usable for production. | `5.3.1 or 8` |
| `5.3.1` | Branch follow-up | `5.3 Revoked Review State` | Recovery after revocation. | Edits, rebuilds, or resubmits the affected item. | The row moves back to draft, missing, or ready state based on what must be corrected. | The client clears production readiness for the revoked item and waits for a new review path. | `3, 4, or 2` |
| `6.1` | Branch journey | `6 Production Package Publish` | Production publish ready. | Reviews package summary and confirms publish. | The package shows ready state with Agent ID, version, digest, and destination action. | Production upload is allowed but not yet started. | `6.2` |
| `6.2` | Branch follow-up | `6.1 Production Publish Ready` | Production publish in progress. | Waits while publish completes. | The publish action shows uploading or publishing progress and prevents duplicate submit. | Production publish is in flight. | `6.3, 6.4, or 6.5` |
| `6.3` | Branch follow-up | `6.2 Production Publish In Progress` | Production publish succeeds. | Copies the catalog URL, opens the catalog page, closes the surface, or returns to the center. | Success state shows published version, catalog identifiers, digest, catalog URL, and completion actions. | Last published package state refreshes and the row becomes published. | `8` |
| `7.1` | Branch journey | `7 Firmware Publish` | Firmware metadata ready. | Reviews firmware version, channel, device, and release target. | The row shows ready state, version, channel, device metadata, and an enabled publish action. | Firmware publish gates are satisfied. | `7.3` |
| `7.3` | Branch follow-up | `7.1 Firmware Metadata Ready` | Firmware publish in progress. | Clicks publish and waits. | The Firmware row shows uploading or publishing progress, and the publish action is disabled. | Firmware publish is in flight and duplicate publish is blocked. | `7.4, 7.5, or 7.6` |
| `7.4` | Branch follow-up | `7.3 Firmware Publish In Progress` | Firmware publish succeeds. | Reviews latest or available state and returns to the center. | Success refreshes available/latest firmware state for the row. | Firmware publish state becomes published and the row refreshes. | `8` |

Detail states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Detail state | `2 Publish Center Overview` | App record row. | Reviews app information completeness or opens edit. | The row shows draft, missing, ready, in review, approved, rejected, or revoked state and an edit or submit action when allowed. | App information readiness is reflected in the row and can gate review submission. | `3 or 4` |
| `2.2` | Detail state | `2 Publish Center Overview` | Icon & screenshots row. | Reviews media completeness or opens edit. | The row shows optional, missing, ready, saving, or failed state for catalog media. | Media readiness refreshes independently from package readiness. | `3 or 2` |
| `2.3` | Detail state | `2 Publish Center Overview` | Agent package row. | Reviews package digest, review state, and production action. | The row shows package readiness, review status, approved state, production publish action, or retryable failure. | Package readiness and catalog review state are aligned before production publish is enabled. | `4, 5, or 6` |
| `2.4` | Detail state | `2 Publish Center Overview` | Firmware row. | Reviews firmware version, channel, device metadata, and publish readiness. | The row shows draft, missing, ready, publishing, published, failed, or version-conflict state with a Publish firmware action only when ready. | Firmware readiness is refreshed from available build and device metadata. | `7 or 2` |
| `4.5` | Detail state | `4 Review Readiness And Submission` | Editing constraints during review. | Opens a row that is already submitted or in review. | The row explains which information can still be viewed and which changes require a later resubmission. | Review state constrains edits until an outcome returns. | `5 or 2` |

State language states:

No separate state-language inventory rows exist for Journey 09. Status language is embedded in row badges and recovery copy for each state.

Errors and recovery states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1.2` | Branch journey | `1 Publish Entry And Auth Gate` | Signed-out user reaches a login gate. | Clicks Sign in or closes the publish surface. | The publish surface explains that sign-in is required before catalog review or publish actions can continue. | The client blocks review and publish submission while preserving the user entry context. | `1.2.1 or previous usable surface` |
| `3.4` | Branch follow-up | `3.2 Save In Progress` | Save fails. | Reads the error, corrects input if needed, and retries or cancels. | The edit surface preserves the user input and shows an error near the failed save. | Dirty state remains and the publish center row does not advance. | `3.1 or 2` |
| `3.5` | Branch journey | `3 App Record And Media Editing` | Required app information is incomplete. | Reviews validation messages and fills required fields. | Missing Display name, Description, or required review information blocks submission and points to the field that needs attention. | App record readiness stays missing until required fields are valid and saved. | `3.1` |
| `4.4` | Branch journey | `4 Review Readiness And Submission` | Review submission failure. | Reads the failure, corrects the blocking condition, and retries. | The row remains in the publish center with a retryable error and does not pretend the item is in review. | Submission state resets to retryable while preserving row context. | `3, 4.1, or 4.2` |
| `5.2` | Branch journey | `5 Review Outcome Refresh` | Rejected review state. | Reads rejection copy, edits the needed information, and prepares resubmission. | The row shows rejected state, keeps the relevant details visible, and offers edit or retry paths instead of production publish. | Production action stays blocked for the rejected item. | `5.2.1` |
| `5.3` | Branch journey | `5 Review Outcome Refresh` | Revoked review state. | Reads revoked status and starts a fresh correction path if allowed. | The row shows revoked state and removes production publish until the item is made eligible again. | Previously approved readiness is no longer usable for production. | `5.3.1 or 8` |
| `6.4` | Branch journey | `6 Production Package Publish` | Production publish fails. | Reads the error and retries, cancels, or changes the package version. | The row or dialog shows publish failed state with retry guidance and keeps context visible. | Production state remains retryable and does not mark the package as published. | `6.1 or 2` |
| `6.5` | Branch follow-up | `6.2 Production Publish In Progress` | Production version conflict. | Bumps version or rebuilds package, then retries. | A conflict message explains that the version already exists and offers rebuild or retry after correction. | The failed version remains blocked until a fresh package version is available. | `6.1 or 2` |
| `7.2` | Branch journey | `7 Firmware Publish` | Firmware publish disabled. | Reads the disabled reason and fixes missing form or build output. | Missing version, channel, device metadata, or build output disables publish and names what needs attention. | Firmware publish remains blocked until required metadata or build output is available. | `7.1 or 2` |
| `7.5` | Branch follow-up | `7.3 Firmware Publish In Progress` | Firmware publish fails. | Reads the failure, fixes the issue, and retries. | The failure remains on the Firmware row with retry available when the required inputs are still present. | Firmware publish state remains failed and retryable. | `7.1 or 2` |
| `7.6` | Branch follow-up | `7.3 Firmware Publish In Progress` | Firmware version conflict. | Changes firmware version or channel, rebuilds if needed, and retries. | The row explains that the firmware version or release target already exists and blocks overwrite by default. | The conflicting firmware publish attempt stays blocked until a corrected target is available. | `7.1 or 2` |
| `0.1` | Recovery summary | `Whole journey` | Auth recovery across the publish center. | Signs in, cancels, or returns later. | Sign-in recovery keeps the publish context and does not submit anything while signed out. | Account readiness is refreshed before any publish or review action resumes. | `1.1, 1.2, or previous usable surface` |
| `0.2` | Recovery summary | `Whole journey` | App record and media save recovery. | Corrects validation, retries save, or cancels. | User-entered app information and media choices stay visible after validation or save failure. | Unsaved state remains until save succeeds. | `3.1, 3.3, or 2` |
| `0.3` | Recovery summary | `Whole journey` | Review recovery. | Edits, resubmits, waits for review, or acts on rejected or revoked state. | Review rows keep their status visible and only expose actions allowed by that status. | Review state controls whether production publish can proceed. | `4, 5, or 2` |
| `0.4` | Recovery summary | `Whole journey` | Production package recovery. | Retries, rebuilds, changes version, or exits. | Package publish errors keep the package context visible and avoid marking failed uploads as published. | Production package state remains retryable or blocked until corrected. | `6.1, 6.3, or 2` |
| `0.5` | Recovery summary | `Whole journey` | Firmware recovery. | Fixes metadata or build output, retries publish, or returns to the center. | Firmware errors remain on the Firmware row with clear disabled, failed, retry, or conflict state. | Firmware publish state refreshes only after required inputs or successful publish change. | `7.1, 7.4, or 2` |

Route table:

| Route | Composition | Result / next state |
| --- | --- | --- |
| Main path overview | `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> 8` | The compact main journey moves from publish entry, through the unified center, app and media editing, review, review outcome, production package publish, firmware publish, and a stable center. |
| Main approved package and firmware route | `1 -> 1.1 -> 2 -> 2.1 -> 3 -> 3.1 -> 3.2 -> 3.3 -> 2 -> 2.3 -> 4 -> 4.1 -> 4.3 -> 5 -> 5.1 -> 6 -> 6.1 -> 6.2 -> 6.3 -> 2 -> 2.4 -> 7 -> 7.1 -> 7.3 -> 7.4 -> 8` | The user reaches an approved package state, publishes the production package, publishes firmware, and returns to a stable publish center. |
| Signed out then returned | `1 -> 1.2 -> 1.2.1 -> 2 -> 2.1 -> 3 -> 3.3 -> 2 -> 4 -> 4.3 -> 5 -> 5.1 -> 8` | Sign-in blocks submission until complete, then the user resumes the center and reaches approved review state. |
| App record incomplete then corrected | `1 -> 1.1 -> 2 -> 2.1 -> 3 -> 3.5 -> 3.1 -> 3.2 -> 3.3 -> 2 -> 4 -> 4.3 -> 5 -> 5.1 -> 8` | Missing app information blocks review until the user saves corrected catalog-facing fields. |
| Media save failure then retry | `1 -> 1.1 -> 2 -> 2.2 -> 3 -> 3.1 -> 3.2 -> 3.4 -> 3.1 -> 3.2 -> 3.3 -> 2 -> 8` | The edit surface preserves input after failure and the user retries until the row refreshes. |
| Agent package review route | `1 -> 1.1 -> 2 -> 2.3 -> 4 -> 4.2 -> 4.3 -> 5 -> 5.1 -> 6 -> 6.1 -> 6.2 -> 6.3 -> 8` | The agent package is submitted, approved, and published to production. |
| Review submission failure | `1 -> 1.1 -> 2 -> 2.3 -> 4 -> 4.2 -> 4.4 -> 4.2 -> 4.3 -> 5 -> 5.1 -> 8` | A failed review request stays retryable and can be submitted again without losing row context. |
| In-review editing constrained | `1 -> 1.1 -> 2 -> 2.3 -> 4 -> 4.2 -> 4.3 -> 4.5 -> 5 -> 5.1 -> 8` | The user can view in-review content, but production actions wait for a review outcome. |
| Rejected review corrected and resubmitted | `1 -> 1.1 -> 2 -> 2.1 -> 4 -> 4.1 -> 4.3 -> 5 -> 5.2 -> 5.2.1 -> 3 -> 3.3 -> 4 -> 4.1 -> 4.3 -> 5 -> 5.1 -> 8` | Rejection blocks production until the user corrects the affected item and receives approval. |
| Revoked review recovery | `1 -> 1.1 -> 2 -> 2.3 -> 5 -> 5.3 -> 5.3.1 -> 4 -> 4.2 -> 4.3 -> 5 -> 5.1 -> 8` | Revocation removes production readiness until the user follows a fresh correction and review path. |
| Production publish failure then retry | `1 -> 1.1 -> 2 -> 2.3 -> 5 -> 5.1 -> 6 -> 6.1 -> 6.2 -> 6.4 -> 6.1 -> 6.2 -> 6.3 -> 8` | Failed production publish remains retryable and only becomes published after a later success. |
| Production version conflict | `1 -> 1.1 -> 2 -> 2.3 -> 5 -> 5.1 -> 6 -> 6.1 -> 6.2 -> 6.5 -> 6.1 -> 6.2 -> 6.3 -> 8` | A version conflict blocks reuse of the same version until the user provides a corrected package version. |
| Firmware publish happy path | `1 -> 1.1 -> 2 -> 2.4 -> 7 -> 7.1 -> 7.3 -> 7.4 -> 8` | The Firmware row reads ready metadata, publishes, and refreshes to latest or available state. |
| Firmware disabled until metadata is fixed | `1 -> 1.1 -> 2 -> 2.4 -> 7 -> 7.2 -> 7.1 -> 7.3 -> 7.4 -> 8` | Missing firmware metadata or build output disables publish until corrected. |
| Firmware publish failure then retry | `1 -> 1.1 -> 2 -> 2.4 -> 7 -> 7.1 -> 7.3 -> 7.5 -> 7.1 -> 7.3 -> 7.4 -> 8` | Firmware failure remains on the row with retry until a later publish succeeds. |
| Firmware version conflict | `1 -> 1.1 -> 2 -> 2.4 -> 7 -> 7.1 -> 7.3 -> 7.6 -> 7.1 -> 7.3 -> 7.4 -> 8` | Firmware target conflict blocks overwrite until the user changes the version, channel, or build target. |

## Main Path

### 1 Publish entry and sign-in check

User entry: the user clicks Publish from Agent Authoring or opens a publishing status row.

User action: the user waits for the publish surface to open and for account readiness to resolve.

Visible UI state: Agent Authoring stays visible while the publish surface opens and checks whether the user can publish.

Client state change: The client starts the publish journey and resolves account readiness before upload or review submission is allowed.

Exit / next state: signed-in users continue to 1.1; signed-out users go to 1.2.

### 2 Unified publish center overview

User entry: the user is signed in or has returned from sign-in.

User action: the user reviews the publish center rows and chooses what to edit, submit, publish, retry, or leave for later.

Visible UI state: The center lists App record, Icon & screenshots, Agent package, and Firmware with row status, primary action, and recovery copy.

Client state change: The client refreshes each row from local package state and remote review or publish state.

Exit / next state: the user can edit app information, submit review, publish package, publish firmware, or pause at 8.

### 3 App record and media editing

User entry: the user opens Edit from App record or Icon & screenshots.

User action: the user reviews or changes Display name, Description, icon, or screenshots.

Visible UI state: An edit surface shows Display name, Description, icon, and screenshots with save and cancel actions.

Client state change: The client opens editable catalog-facing fields without leaving the publish center.

Exit / next state: saving succeeds through 3.3, fails through 3.4, or required information blocks progress through 3.5.

### 4 Review readiness and submission

User entry: the user has enough row readiness to submit App record or Agent package review.

User action: the user clicks the available submit action for App record or Agent package.

Visible UI state: Review controls are enabled only when required app information, media, and package readiness satisfy the publish center checks.

Client state change: The client verifies row readiness before allowing review submission.

Exit / next state: successful submission goes to 4.3; failure goes to 4.4.

### 5 Review outcome refresh

User entry: the user returns after review status changes, or the center refreshes while the user is present.

User action: the user reads the outcome and chooses the next available action.

Visible UI state: The publish center updates badges, row copy, and available actions for approved, rejected, or revoked results.

Client state change: Review results replace pending state and determine whether production publish can proceed.

Exit / next state: approved goes to 5.1; rejected goes to 5.2; revoked goes to 5.3.

### 6 Production package publish

User entry: the Agent package has approved status and required publish gates are satisfied.

User action: the user clicks Publish to production, reviews the package summary, and confirms publish.

Visible UI state: The row or dialog shows package identity, version, digest, and final publish action before upload.

Client state change: The client confirms approved package readiness before production upload begins.

Exit / next state: success goes to 6.3 and then 8; failure or conflict stays recoverable through 6.4 or 6.5.

### 7 Firmware publish

User entry: the user opens Publish firmware from the Firmware row in the unified publish center.

User action: the user reviews version, channel, device metadata, and publish readiness, then publishes when enabled.

Visible UI state: The firmware publish surface stays attached to the same center and shows version, channel, device metadata, and readiness.

Client state change: Firmware metadata is refreshed before publish is enabled.

Exit / next state: success goes to 7.4 and then 8; blocked or failed publish stays recoverable through 7.2, 7.5, or 7.6.

### 8 Stable publish center

User entry: the user finishes a publish action, pauses after review status changes, or returns to the center after recovery.

User action: the user continues another row, retries a failed row, opens a published catalog URL, or closes the publish center.

Visible UI state: The center shows latest App record, Icon & screenshots, Agent package, and Firmware states without requiring the user to remember prior dialogs.

Client state change: The client rests with the latest known row readiness, review state, production package state, and firmware state.

Exit / next state: the journey ends, or the user starts another row action from 2.

## Branch Journeys

### 1.1 Signed-in user continues into the publish center

Trigger: Waits for the sign-in check to finish.

Visible UI state: The publish surface shows signed-in context and continues to package, review, and row readiness.

Allowed user actions: Waits for the sign-in check to finish, continue where available, retry when offered, or return to the publish center.

Client state change: Account readiness is satisfied for publish actions in this session.

Recovery / next state: 2.

Blocks progress: no.

### 1.2.1 User returns from sign-in

Trigger: Completes sign-in and returns to NoraHarness.

Visible UI state: The publish surface resumes from the gate instead of requiring the user to restart from Agent Authoring.

Allowed user actions: Completes sign-in and returns to NoraHarness, continue where available, retry when offered, or return to the publish center.

Client state change: Account readiness refreshes and the pending publish journey continues.

Recovery / next state: 2.

Blocks progress: no.

### 3.1 Unsaved app record or media changes

Trigger: Edits text, icon, or screenshots.

Visible UI state: Changed fields remain visible, save becomes available, and the user can cancel before committing.

Allowed user actions: Edits text, icon, or screenshots, continue where available, retry when offered, or return to the publish center.

Client state change: The row enters dirty state and keeps edits local until save succeeds.

Recovery / next state: 3.2 or 2.

Blocks progress: no.

### 3.2 Save in progress

Trigger: Clicks Save.

Visible UI state: Save is disabled or shows saving progress while the edit surface stays open.

Allowed user actions: Clicks Save, continue where available, retry when offered, or return to the publish center.

Client state change: The client submits the changed app information or media and prevents duplicate save.

Recovery / next state: 3.3 or 3.4.

Blocks progress: temporarily.

### 3.3 Save succeeds

Trigger: Reviews the saved feedback or closes the edit surface.

Visible UI state: Saved feedback appears and the matching publish center row refreshes to ready, optional, or still missing.

Allowed user actions: Reviews the saved feedback or closes the edit surface, continue where available, retry when offered, or return to the publish center.

Client state change: Dirty state clears and row completeness refreshes.

Recovery / next state: 2 or 4.

Blocks progress: no.

### 4.1 App record review submission

Trigger: Clicks the submit action for app information review.

Visible UI state: The submit action becomes pending, and the row communicates that review is being requested.

Allowed user actions: Clicks the submit action for app information review, continue where available, retry when offered, or return to the publish center.

Client state change: App record review submission is in flight and duplicate submission is blocked.

Recovery / next state: 4.3 or 4.4.

Blocks progress: temporarily.

### 4.2 Agent package review submission

Trigger: Clicks the submit action for the agent package.

Visible UI state: Package summary, version, digest, and capability information remain visible while the submit action becomes pending.

Allowed user actions: Clicks the submit action for the agent package, continue where available, retry when offered, or return to the publish center.

Client state change: Agent package review submission is in flight and duplicate submission is blocked.

Recovery / next state: 4.3 or 4.4.

Blocks progress: temporarily.

### 4.3 Submitted and in review

Trigger: Waits or returns later to check status.

Visible UI state: The relevant row shows submitted or in review status, and later editing is constrained.

Allowed user actions: Waits or returns later to check status, continue where available, retry when offered, or return to the publish center.

Client state change: The client records the review request and refreshes status until an outcome appears.

Recovery / next state: 5.

Blocks progress: no.

### 5.1 Approved review state

Trigger: Reviews the approved row and proceeds to production when desired.

Visible UI state: App record and/or Agent package rows show approved state; production actions become available when all gates are satisfied.

Allowed user actions: Reviews the approved row and proceeds to production when desired, continue where available, retry when offered, or return to the publish center.

Client state change: Approved state unlocks the matching production publish path.

Recovery / next state: 6 or 8.

Blocks progress: no.

### 5.2.1 Rejection correction and resubmission

Trigger: Updates app information, media, or package version and submits again.

Visible UI state: The publish center returns the item to editable or review-ready state after the user corrects the rejection reason.

Allowed user actions: Updates app information, media, or package version and submits again, continue where available, retry when offered, or return to the publish center.

Client state change: Corrected content can be saved and resubmitted through the review path.

Recovery / next state: 3 or 4.

Blocks progress: temporarily.

### 5.3.1 Recovery after revocation

Trigger: Edits, rebuilds, or resubmits the affected item.

Visible UI state: The row moves back to draft, missing, or ready state based on what must be corrected.

Allowed user actions: Edits, rebuilds, or resubmits the affected item, continue where available, retry when offered, or return to the publish center.

Client state change: The client clears production readiness for the revoked item and waits for a new review path.

Recovery / next state: 3, 4, or 2.

Blocks progress: no.

### 6.1 Production publish ready

Trigger: Reviews package summary and confirms publish.

Visible UI state: The package shows ready state with Agent ID, version, digest, and destination action.

Allowed user actions: Reviews package summary and confirms publish, continue where available, retry when offered, or return to the publish center.

Client state change: Production upload is allowed but not yet started.

Recovery / next state: 6.2.

Blocks progress: no.

### 6.2 Production publish in progress

Trigger: Waits while publish completes.

Visible UI state: The publish action shows uploading or publishing progress and prevents duplicate submit.

Allowed user actions: Waits while publish completes, continue where available, retry when offered, or return to the publish center.

Client state change: Production publish is in flight.

Recovery / next state: 6.3, 6.4, or 6.5.

Blocks progress: temporarily.

### 6.3 Production publish succeeds

Trigger: Copies the catalog URL, opens the catalog page, closes the surface, or returns to the center.

Visible UI state: Success state shows published version, catalog identifiers, digest, catalog URL, and completion actions.

Allowed user actions: Copies the catalog URL, opens the catalog page, closes the surface, or returns to the center, continue where available, retry when offered, or return to the publish center.

Client state change: Last published package state refreshes and the row becomes published.

Recovery / next state: 8.

Blocks progress: no.

### 7.1 Firmware metadata ready

Trigger: Reviews firmware version, channel, device, and release target.

Visible UI state: The row shows ready state, version, channel, device metadata, and an enabled publish action.

Allowed user actions: Reviews firmware version, channel, device, and release target, continue where available, retry when offered, or return to the publish center.

Client state change: Firmware publish gates are satisfied.

Recovery / next state: 7.3.

Blocks progress: no.

### 7.3 Firmware publish in progress

Trigger: Clicks publish and waits.

Visible UI state: The Firmware row shows uploading or publishing progress, and the publish action is disabled.

Allowed user actions: Clicks publish and waits, continue where available, retry when offered, or return to the publish center.

Client state change: Firmware publish is in flight and duplicate publish is blocked.

Recovery / next state: 7.4, 7.5, or 7.6.

Blocks progress: temporarily.

### 7.4 Firmware publish succeeds

Trigger: Reviews latest or available state and returns to the center.

Visible UI state: Success refreshes available/latest firmware state for the row.

Allowed user actions: Reviews latest or available state and returns to the center, continue where available, retry when offered, or return to the publish center.

Client state change: Firmware publish state becomes published and the row refreshes.

Recovery / next state: 8.

Blocks progress: no.

## Detail States

### 2.1 App record row

Trigger: Reviews app information completeness or opens edit.

Visible UI state: The row shows draft, missing, ready, in review, approved, rejected, or revoked state and an edit or submit action when allowed.

Allowed user actions: Reviews app information completeness or opens edit, continue where available, retry when offered, or return to the publish center.

Client state change: App information readiness is reflected in the row and can gate review submission.

Recovery / next state: 3 or 4.

Blocks progress: no.

### 2.2 Icon & screenshots row

Trigger: Reviews media completeness or opens edit.

Visible UI state: The row shows optional, missing, ready, saving, or failed state for catalog media.

Allowed user actions: Reviews media completeness or opens edit, continue where available, retry when offered, or return to the publish center.

Client state change: Media readiness refreshes independently from package readiness.

Recovery / next state: 3 or 2.

Blocks progress: no.

### 2.3 Agent package row

Trigger: Reviews package digest, review state, and production action.

Visible UI state: The row shows package readiness, review status, approved state, production publish action, or retryable failure.

Allowed user actions: Reviews package digest, review state, and production action, continue where available, retry when offered, or return to the publish center.

Client state change: Package readiness and catalog review state are aligned before production publish is enabled.

Recovery / next state: 4, 5, or 6.

Blocks progress: no.

### 2.4 Firmware row

Trigger: Reviews firmware version, channel, device metadata, and publish readiness.

Visible UI state: The row shows draft, missing, ready, publishing, published, failed, or version-conflict state with a Publish firmware action only when ready.

Allowed user actions: Reviews firmware version, channel, device metadata, and publish readiness, continue where available, retry when offered, or return to the publish center.

Client state change: Firmware readiness is refreshed from available build and device metadata.

Recovery / next state: 7 or 2.

Blocks progress: no.

### 4.5 Editing constraints during review

Trigger: Opens a row that is already submitted or in review.

Visible UI state: The row explains which information can still be viewed and which changes require a later resubmission.

Allowed user actions: Opens a row that is already submitted or in review, continue where available, retry when offered, or return to the publish center.

Client state change: Review state constrains edits until an outcome returns.

Recovery / next state: 5 or 2.

Blocks progress: no.

## State Language

No separate states exist for this category. Journey 09 uses row badge and action language inside the publish center: Draft, Missing, Ready, Saving, Saved, Submitted, In review, Approved, Rejected, Revoked, Publishing, Published, Failed, Retry, Disabled, Version exists, and Latest.

## Errors And Recovery

### 1.2 Signed-out user reaches a login gate

Trigger: Clicks Sign in or closes the publish surface.

Visible UI state: The publish surface explains that sign-in is required before catalog review or publish actions can continue.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: The client blocks review and publish submission while preserving the user entry context.

Recovery / next state: 1.2.1 or previous usable surface.

Blocks progress: yes until the recovery condition is resolved.

### 3.4 Save fails

Trigger: Reads the error, corrects input if needed, and retries or cancels.

Visible UI state: The edit surface preserves the user input and shows an error near the failed save.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Dirty state remains and the publish center row does not advance.

Recovery / next state: 3.1 or 2.

Blocks progress: yes until the recovery condition is resolved.

### 3.5 Required app information is incomplete

Trigger: Reviews validation messages and fills required fields.

Visible UI state: Missing Display name, Description, or required review information blocks submission and points to the field that needs attention.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: App record readiness stays missing until required fields are valid and saved.

Recovery / next state: 3.1.

Blocks progress: yes until the recovery condition is resolved.

### 4.4 Review submission failure

Trigger: Reads the failure, corrects the blocking condition, and retries.

Visible UI state: The row remains in the publish center with a retryable error and does not pretend the item is in review.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Submission state resets to retryable while preserving row context.

Recovery / next state: 3, 4.1, or 4.2.

Blocks progress: yes until the recovery condition is resolved.

### 5.2 Rejected review state

Trigger: Reads rejection copy, edits the needed information, and prepares resubmission.

Visible UI state: The row shows rejected state, keeps the relevant details visible, and offers edit or retry paths instead of production publish.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Production action stays blocked for the rejected item.

Recovery / next state: 5.2.1.

Blocks progress: yes until the recovery condition is resolved.

### 5.3 Revoked review state

Trigger: Reads revoked status and starts a fresh correction path if allowed.

Visible UI state: The row shows revoked state and removes production publish until the item is made eligible again.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Previously approved readiness is no longer usable for production.

Recovery / next state: 5.3.1 or 8.

Blocks progress: yes until the recovery condition is resolved.

### 6.4 Production publish fails

Trigger: Reads the error and retries, cancels, or changes the package version.

Visible UI state: The row or dialog shows publish failed state with retry guidance and keeps context visible.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Production state remains retryable and does not mark the package as published.

Recovery / next state: 6.1 or 2.

Blocks progress: yes until the recovery condition is resolved.

### 6.5 Production version conflict

Trigger: Bumps version or rebuilds package, then retries.

Visible UI state: A conflict message explains that the version already exists and offers rebuild or retry after correction.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: The failed version remains blocked until a fresh package version is available.

Recovery / next state: 6.1 or 2.

Blocks progress: yes until the recovery condition is resolved.

### 7.2 Firmware publish disabled

Trigger: Reads the disabled reason and fixes missing form or build output.

Visible UI state: Missing version, channel, device metadata, or build output disables publish and names what needs attention.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Firmware publish remains blocked until required metadata or build output is available.

Recovery / next state: 7.1 or 2.

Blocks progress: yes until the recovery condition is resolved.

### 7.5 Firmware publish fails

Trigger: Reads the failure, fixes the issue, and retries.

Visible UI state: The failure remains on the Firmware row with retry available when the required inputs are still present.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Firmware publish state remains failed and retryable.

Recovery / next state: 7.1 or 2.

Blocks progress: yes until the recovery condition is resolved.

### 7.6 Firmware version conflict

Trigger: Changes firmware version or channel, rebuilds if needed, and retries.

Visible UI state: The row explains that the firmware version or release target already exists and blocks overwrite by default.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: The conflicting firmware publish attempt stays blocked until a corrected target is available.

Recovery / next state: 7.1 or 2.

Blocks progress: yes until the recovery condition is resolved.

### 0.1 Auth recovery across the publish center

Trigger: Signs in, cancels, or returns later.

Visible UI state: Sign-in recovery keeps the publish context and does not submit anything while signed out.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Account readiness is refreshed before any publish or review action resumes.

Recovery / next state: 1.1, 1.2, or previous usable surface.

Blocks progress: yes until the recovery condition is resolved.

### 0.2 App record and media save recovery

Trigger: Corrects validation, retries save, or cancels.

Visible UI state: User-entered app information and media choices stay visible after validation or save failure.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Unsaved state remains until save succeeds.

Recovery / next state: 3.1, 3.3, or 2.

Blocks progress: yes until the recovery condition is resolved.

### 0.3 Review recovery

Trigger: Edits, resubmits, waits for review, or acts on rejected or revoked state.

Visible UI state: Review rows keep their status visible and only expose actions allowed by that status.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Review state controls whether production publish can proceed.

Recovery / next state: 4, 5, or 2.

Blocks progress: yes until the recovery condition is resolved.

### 0.4 Production package recovery

Trigger: Retries, rebuilds, changes version, or exits.

Visible UI state: Package publish errors keep the package context visible and avoid marking failed uploads as published.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Production package state remains retryable or blocked until corrected.

Recovery / next state: 6.1, 6.3, or 2.

Blocks progress: yes until the recovery condition is resolved.

### 0.5 Firmware recovery

Trigger: Fixes metadata or build output, retries publish, or returns to the center.

Visible UI state: Firmware errors remain on the Firmware row with clear disabled, failed, retry, or conflict state.

Allowed user actions: retry, correct the blocking input or state, wait when pending, close the surface, or return to the publish center when available.

Client state change: Firmware publish state refreshes only after required inputs or successful publish change.

Recovery / next state: 7.1, 7.4, or 2.

Blocks progress: yes until the recovery condition is resolved.

## Wireframe

The matching HTML must keep six first-level bands in this order: Main happy path, Route map, Branch journeys, Detail states, State language, and Errors and recovery. Route rows in the Route map must cover every row in the Route table as full-size product wireframe cards, and the Branch, Detail, State language, and Error bands must remain separate.
