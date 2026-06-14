# Journey 11: LVGL Preview

This record documents the current implemented journey for opening the LVGL preview from the Code workbench, starting or rebuilding the preview, and inspecting the resulting screenshot, metadata, errors, and logs. Each section ID is intended to match the HTML wireframe cards.

## Journey Scope

User entry: The user opens the Preview tab or LVGL Preview panel in the Code workbench.

User goal: The user starts preview, triggers build, capture, or rebuild, and inspects the screenshot result, errors, and logs.

End state: The user sees the latest LVGL screenshot, or sees a clear failure reason with retry and recovery actions.

Implementation maturity: Implemented.

## Numbering And Route Tables

Main journey steps use plain numbers. Branches, detail states, state-language states, and recovery states use decimal IDs scoped to the step or whole journey where the state starts. Sequential follow-up states use another decimal level.

### State Inventory

Main preview path:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `1` | Main path | Journey 11 | Code workbench preview entry. | Opens the Preview tab or LVGL Preview panel. | The Code workbench remains visible with the Preview surface selected and workspace context still present. | The preview journey becomes the active workbench state for the current workspace. | `2` |
| `2` | Main path | Journey 11 | Preview readiness checks. | Waits while the surface checks whether preview can run. | The Preview surface shows readiness, loading, or disabled action state instead of a blank canvas. | Preview capability, dependencies, target state, and current workspace state are checked. | `3`, `2.1`, `2.2`, or `2.3` |
| `3` | Main path | Journey 11 | Start preview request. | Clicks the start action, or uses the available rebuild action when a prior preview exists. | The start action changes to an in-progress state and the preview area prepares for build and capture. | A preview run is started for the current workspace. | `4` or `3.1` |
| `4` | Main path | Journey 11 | Build in progress. | Waits while the preview build runs. | Progress steps, loading copy, or build activity appear; duplicate start actions are unavailable. | The current run records build progress and keeps the previous stable preview state available when one exists. | `5`, `4.1`, or `4.2` |
| `5` | Main path | Journey 11 | Capture in progress. | Waits while the screenshot is captured. | The preview area shows capture progress and keeps logs or progress details reachable. | The current run advances from build output to screenshot capture. | `6`, `5.1`, or `5.2` |
| `6` | Main path | Journey 11 | Screenshot result. | Reviews the preview image. | The screenshot area changes from empty or loading state to the latest LVGL preview image. | The successful capture becomes the latest preview result for the workspace. | `7`, `6.1`, or `6.2` |
| `7` | Main path | Journey 11 | Stable preview surface. | Pauses, inspects logs, rebuilds, changes source, or leaves the surface. | The latest screenshot, metadata, and available actions remain visible; failure states remain clearly retryable. | The preview surface rests in the latest success, stale, unavailable, or recoverable failure state. | End, `7.1`, `7.2`, or `7.3` |

Branch journeys:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `2.1` | Branch journey | `2 Preview readiness checks` | Preview capability is unavailable for the current workspace. | Reads the disabled state and leaves the surface or corrects setup outside the preview run. | The Preview surface explains that LVGL preview is unavailable and the start action is disabled. | No preview run is started. | `7` |
| `2.2` | Branch journey | `2 Preview readiness checks` | Required preview dependency is missing or not ready. | Follows setup guidance, retries readiness, or returns to another workbench panel. | The Preview surface shows missing dependency or setup guidance instead of a runnable preview. | The preview remains blocked until the dependency becomes ready. | `7` |
| `2.3` | Branch journey | `2 Preview readiness checks` | No display or screen target can be previewed. | Reviews setup guidance and corrects the target before retrying. | The preview area avoids showing a blank canvas as success and explains that no preview target is available. | The run is blocked before build or capture begins. | `7` |
| `3.1` | Branch journey | `3 Start preview request` | Rebuild is requested when an earlier screenshot or stale preview exists. | Clicks rebuild or refresh preview. | The prior screenshot can remain visible while new progress begins. | A new preview run starts without discarding the last stable result immediately. | `4` |
| `7.1` | Branch journey | `7 Stable preview surface` | Source changes make the current screenshot stale. | Edits or receives changes, then returns to the Preview surface. | The latest screenshot remains visible but is marked stale or ready to rebuild. | The previous result is preserved while the client notes that it no longer reflects the latest source. | `7.1.1` or `7` |
| `7.1.1` | Branch follow-up | `7.1 Source changes make current screenshot stale` | Rebuild from stale preview. | Clicks rebuild or refresh preview. | The stale state changes to progress while the old screenshot remains available until a new result or failure resolves. | A new preview run starts from the stale state. | `4` |

Detail states:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.1` | Detail state | `4 Build in progress` | Build progress details. | Waits or opens visible logs while the build continues. | The surface shows build status, progress steps, and disabled duplicate run controls. | Build progress is tracked for the active preview run. | `5` |
| `5.1` | Detail state | `5 Capture in progress` | Capture progress details. | Waits while the screenshot is captured. | The surface shows capture or render progress and keeps the workbench usable around it. | Capture progress is tracked until success or failure resolves. | `6` |
| `6.1` | Detail state | `6 Screenshot result` | Screenshot metadata is shown with the image. | Reviews time, size, source, or similar metadata. | The screenshot is accompanied by metadata that identifies the latest preview result. | The latest result metadata is kept with the visible screenshot state. | `7` |
| `6.2` | Detail state | `6 Screenshot result` | Screenshot can be referenced during review. | Uses the visible result as a review reference while staying in the workbench. | The screenshot result remains tied to the current workspace and does not imply firmware publish. | The latest captured result remains available for continued review. | `7` |
| `7.3` | Detail state | `7 Stable preview surface` | Logs remain available for success and failure review. | Opens or reads logs while staying on the Preview surface. | The user can inspect log details without losing the current screenshot or error state. | Log visibility changes without changing the latest preview result. | `7` |

State language:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Preview readiness language. | Reads checking, ready, unavailable, missing setup, or missing target language. | The readiness state tells the user whether preview can run and whether Start is available. | The visible readiness language stays aligned with the current preview availability. | Active journey state |
| `0.2` | State language | Whole journey | Preview progress language. | Reads build, capture, retry, or rebuild progress language. | Active preview work uses loading and progress language, with duplicate actions unavailable. | The visible progress language stays attached to the current preview run. | Active journey state |
| `0.3` | State language | Whole journey | Preview result language. | Reads latest, stale, previous, metadata, and review-reference labels. | Result language tells the user whether the screenshot is current, stale, or preserved. | The visible result language stays aligned with the latest known screenshot state. | Active journey state |
| `0.4` | State language | Whole journey | Preview recovery language. | Reads blocked, failed, retry, log, and correction language. | Recovery language names the failure or block and shows retry or correction actions. | The visible recovery language stays aligned with the current blocked or failed state. | Active journey state |

Errors and recovery:

| ID | Type | Parent step | What it represents | User action | Visible UI state | Client state change | Exit / next state |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `4.2` | Recovery state | `4 Build in progress` | Build fails before screenshot capture. | Reads the error, opens relevant logs, fixes inputs, or retries. | The preview surface shows an error summary, log summary, and retry action while keeping source context reachable. | The active run ends as failed and the preview surface records a recoverable error. | `4.2.1` or `7` |
| `4.2.1` | Recovery follow-up | `4.2 Build fails before screenshot capture` | Retry after build failure. | Clicks retry after correcting the cause or waiting for recovery. | The error state changes back into preview progress. | A new build starts for the same preview target. | `4` |
| `5.2` | Recovery state | `5 Capture in progress` | Capture fails after build activity. | Reads the capture failure, opens logs, fixes inputs, or retries. | The preview surface shows a capture error, log summary, and retry action. | The active run ends as failed after build but before a new screenshot replaces the current result. | `5.2.1` or `7` |
| `5.2.1` | Recovery follow-up | `5.2 Capture fails after build activity` | Retry after capture failure. | Clicks retry after correcting the cause or waiting for recovery. | The capture error state changes back into preview progress. | A new preview run starts from the retry action. | `4` |
| `7.2` | Recovery state | `7 Stable preview surface` | Previous screenshot is preserved after a failed rebuild. | Reviews the failure while comparing against the last successful screenshot. | The surface shows the old screenshot or a clear preserved-result area alongside the current failure state. | Failed rebuild state does not erase the last successful screenshot. | `7.2.1` or `7.3` |
| `7.2.1` | Recovery follow-up | `7.2 Previous screenshot is preserved after a failed rebuild` | Recovery returns to a stable screenshot state. | Retries successfully, or leaves the preserved screenshot as the latest usable reference. | A successful retry replaces the old screenshot; otherwise the preserved screenshot remains clearly labeled as previous. | The preview surface resolves to either a new latest result or the preserved previous result. | `6` or `7` |

### Route Table

| Route | Composition | Result / next state |
| --- | --- | --- |
| First successful preview | `1 -> 2 -> 3 -> 4 -> 4.1 -> 5 -> 5.1 -> 6 -> 6.1 -> 7` | The user reaches a stable preview surface with the latest screenshot and metadata visible. |
| Rebuild from an existing preview | `1 -> 2 -> 3 -> 3.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 6 -> 6.1 -> 7` | The previous screenshot can remain visible during rebuild, then a new screenshot becomes the latest result. |
| Preview capability unavailable | `1 -> 2 -> 2.1 -> 7` | The user sees why Preview cannot run and no build or capture starts. |
| Missing dependency | `1 -> 2 -> 2.2 -> 7` | The user sees setup guidance and can return after the dependency becomes ready. |
| No display target | `1 -> 2 -> 2.3 -> 7` | The user sees target guidance instead of an empty screenshot being treated as success. |
| Build fails, then retry succeeds | `1 -> 2 -> 3 -> 4 -> 4.1 -> 4.2 -> 4.2.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 6 -> 6.1 -> 7` | The user reads the build failure, retries, and reaches a successful screenshot result. |
| Build fails and user reviews logs | `1 -> 2 -> 3 -> 4 -> 4.1 -> 4.2 -> 7 -> 7.3 -> 7` | The user remains in a recoverable failure state with logs available. |
| Capture fails, then retry succeeds | `1 -> 2 -> 3 -> 4 -> 4.1 -> 5 -> 5.1 -> 5.2 -> 5.2.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 6 -> 6.1 -> 7` | The user reads the capture failure, retries, and reaches a successful screenshot result. |
| Capture fails and user reviews logs | `1 -> 2 -> 3 -> 4 -> 4.1 -> 5 -> 5.1 -> 5.2 -> 7 -> 7.3 -> 7` | The user remains in a recoverable capture failure state with logs available. |
| Source changes after success | `1 -> 2 -> 3 -> 4 -> 4.1 -> 5 -> 5.1 -> 6 -> 6.1 -> 7 -> 7.1 -> 7.1.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 6 -> 6.1 -> 7` | The old screenshot is marked stale, rebuild runs, and the latest screenshot updates. |
| Failed rebuild preserves old screenshot | `1 -> 2 -> 3 -> 3.1 -> 4 -> 4.1 -> 4.2 -> 7.2 -> 7.3 -> 7` | The user sees a failed rebuild while the last successful screenshot remains available as a previous result. |
| Recovery replaces preserved screenshot | `1 -> 2 -> 3 -> 3.1 -> 4 -> 4.1 -> 4.2 -> 7.2 -> 7.2.1 -> 6 -> 6.1 -> 7` | A later successful retry replaces the previous screenshot and returns the surface to latest-result state. |

## Main Path

### 1 Code Workbench Preview Entry

User entry: The user is already in the Code workbench with a workspace selected.

User action: The user opens the Preview tab or LVGL Preview panel.

Visible UI state:

- The Preview surface becomes active inside the Code workbench.
- The surrounding workspace context remains visible.
- The surface is ready to show capability checks, progress, screenshot output, or recovery guidance.

Client state change: The preview journey becomes active for the current workspace.

Exit / next state: The surface begins preview readiness checks in `2 Preview Readiness Checks`.

### 2 Preview Readiness Checks

User entry: The Preview surface has opened.

User action: The user waits while readiness is checked, or responds if the surface shows a blocked state.

Visible UI state:

- The surface communicates checking, ready, disabled, or blocked state.
- If preview cannot run, the start action is unavailable and the reason is shown.
- If preview can run, the start or rebuild action becomes available.

Client state change: Preview capability, dependencies, target state, and current workspace state are checked before a run starts.

Exit / next state: Ready state proceeds to `3 Start Preview Request`; blocked states route to `2.1 Preview Capability Unavailable`, `2.2 Missing Dependency`, or `2.3 No Display Target`.

### 3 Start Preview Request

User entry: The Preview surface is ready to run.

User action: The user clicks the start action, or chooses rebuild when an earlier screenshot exists.

Visible UI state:

- The run action enters an in-progress state.
- Duplicate start or rebuild actions are disabled while the run is active.
- If an older screenshot exists, it may remain visible until the new result resolves.

Client state change: A preview run starts for the current workspace.

Exit / next state: A new preview proceeds to `4 Build In Progress`; rebuild from an existing result may pass through `3.1 Rebuild Requested From Existing Preview`.

### 4 Build In Progress

User entry: A preview run has started.

User action: The user waits, reads progress, or opens visible logs while the build continues.

Visible UI state:

- Build progress, status text, or a loading overlay appears.
- The preview area avoids showing the build as complete until capture can proceed.
- Retry is not shown unless the build fails.

Client state change: The active preview run records build progress and keeps the last stable result available when one exists.

Exit / next state: Build success proceeds through `4.1 Build Progress Details` to `5 Capture In Progress`; build failure routes to `4.2 Build Fails Before Screenshot Capture`.

### 5 Capture In Progress

User entry: The build phase has produced a runnable preview target.

User action: The user waits while the screenshot is captured.

Visible UI state:

- The surface shows capture or render progress.
- Logs or progress details remain reachable.
- The old screenshot is not silently replaced by a failed capture.

Client state change: The current run advances from build output to screenshot capture.

Exit / next state: Capture success proceeds through `5.1 Capture Progress Details` to `6 Screenshot Result`; capture failure routes to `5.2 Capture Fails After Build Activity`.

### 6 Screenshot Result

User entry: Capture succeeds.

User action: The user reviews the latest LVGL preview image and its metadata.

Visible UI state:

- The screenshot area changes from empty, loading, or stale state to the latest preview image.
- Metadata such as time, size, or source appears near the result when available.
- The screenshot remains tied to the current workspace and Preview surface.

Client state change: The successful capture becomes the latest preview result and updates visible metadata.

Exit / next state: Metadata review proceeds through `6.1 Screenshot Metadata Visible`; continued review rests in `7 Stable Preview Surface`.

### 7 Stable Preview Surface

User entry: The preview is either successful, blocked, stale, or in a recoverable failure state.

User action: The user pauses, inspects logs, rebuilds, fixes setup, changes source, switches workbench panels, or leaves the surface.

Visible UI state:

- Successful state shows the latest screenshot and metadata.
- Blocked state shows the reason and does not pretend a preview result exists.
- Failure state shows an error summary, log summary, and retry action.
- Stale or preserved-result state labels the screenshot clearly so the user can tell whether it is current.

Client state change: The preview surface rests in the latest known stable state until the user starts another run or leaves.

Exit / next state: End, or source/recovery branches such as `7.1 Source Changes Make Current Screenshot Stale`, `7.2 Previous Screenshot Preserved After Failed Rebuild`, or `7.3 Logs Remain Available`.

## Branch Journeys

### 2.1 Preview Capability Unavailable

Trigger: The readiness check finds that LVGL preview is unavailable for the current workspace.

Visible UI state: The Preview surface explains that preview cannot run and disables the start action.

Allowed user actions: read the guidance, switch panels, correct workspace setup outside the preview run, retry readiness if available, or leave the surface.

Client state change: No build or capture run starts.

Recovery / next state: The surface rests in `7 Stable Preview Surface` until conditions change.

Blocks progress: yes.

### 2.2 Missing Dependency

Trigger: The readiness check finds that a required preview dependency is missing or not ready.

Visible UI state: The Preview surface shows setup or missing-dependency guidance and keeps the start action disabled.

Allowed user actions: follow setup guidance, retry readiness if available, switch to logs or device context, or leave the surface.

Client state change: The preview remains blocked before build or capture begins.

Recovery / next state: The surface rests in `7 Stable Preview Surface`; after the dependency is corrected, the user can return through `2 Preview Readiness Checks`.

Blocks progress: yes.

### 2.3 No Display Target

Trigger: The readiness check finds no LVGL display, screen, or target state that can be captured.

Visible UI state: The preview area explains that no target is available and avoids presenting a blank canvas as a successful screenshot.

Allowed user actions: correct the target, switch panels, retry readiness if available, or leave the surface.

Client state change: The run is blocked before build or capture begins.

Recovery / next state: The surface rests in `7 Stable Preview Surface` until a target becomes available.

Blocks progress: yes.

### 3.1 Rebuild Requested From Existing Preview

Trigger: The user starts a rebuild while a previous screenshot, stale preview, or preserved result is visible.

Visible UI state: The previous screenshot may remain visible while the new build and capture progress begins.

Allowed user actions: wait, inspect logs, or leave the surface while the rebuild is active.

Client state change: A new preview run starts without immediately discarding the previous stable screenshot.

Recovery / next state: Rebuild proceeds to `4 Build In Progress`.

Blocks progress: temporarily, only while the rebuild is active.

### 7.1 Source Changes Make Current Screenshot Stale

Trigger: Source changes happen after a successful preview result.

Visible UI state: The current screenshot remains visible but is marked stale or ready to rebuild.

Allowed user actions: rebuild, inspect the previous result, inspect logs, switch panels, or leave the surface.

Client state change: The previous result is preserved while the client notes that it no longer reflects the latest source.

Recovery / next state: Rebuild moves to `7.1.1 Rebuild From Stale Preview`; otherwise the surface remains in `7 Stable Preview Surface`.

Blocks progress: no, but the result is no longer current.

### 7.1.1 Rebuild From Stale Preview

Trigger: The user clicks rebuild or refresh from a stale preview state.

Visible UI state: The stale state changes to progress while the old screenshot remains available until a new result or failure resolves.

Allowed user actions: wait, inspect logs, switch panels, or leave the surface.

Client state change: A new preview run starts from the stale state.

Recovery / next state: The run returns to `4 Build In Progress`.

Blocks progress: temporarily, while rebuild is active.

## Detail States

### 4.1 Build Progress Details

Trigger: The active preview run is in the build phase.

Visible UI state: Build progress details, loading status, and disabled duplicate-run controls are visible.

Allowed user actions: wait, inspect visible logs, or leave the surface.

Client state change: Build progress remains associated with the active preview run.

Recovery / next state: Build success proceeds to `5 Capture In Progress`; build failure routes to `4.2 Build Fails Before Screenshot Capture`.

Blocks progress: temporarily, until build succeeds or fails.

### 5.1 Capture Progress Details

Trigger: The active preview run has moved from build to capture.

Visible UI state: Capture progress or render progress is visible, with logs or progress details still reachable.

Allowed user actions: wait, inspect logs, or leave the surface.

Client state change: Capture progress remains associated with the active preview run.

Recovery / next state: Capture success proceeds to `6 Screenshot Result`; capture failure routes to `5.2 Capture Fails After Build Activity`.

Blocks progress: temporarily, until capture succeeds or fails.

### 6.1 Screenshot Metadata Visible

Trigger: A screenshot capture succeeds.

Visible UI state: The latest screenshot is shown with metadata such as time, size, source, or similar result details.

Allowed user actions: inspect the image, inspect logs, rebuild, switch panels, or leave the surface.

Client state change: The latest screenshot and metadata are recorded as the current preview result.

Recovery / next state: The surface rests in `7 Stable Preview Surface`.

Blocks progress: no.

### 6.2 Screenshot Used As Review Reference

Trigger: The latest screenshot is visible and the user reviews it in context.

Visible UI state: The preview result remains tied to the current workspace and does not imply firmware publishing.

Allowed user actions: compare with source changes, inspect logs, rebuild, switch panels, or leave the surface.

Client state change: The latest result remains available for continued review.

Recovery / next state: The surface rests in `7 Stable Preview Surface`.

Blocks progress: no.

### 7.3 Logs Remain Available

Trigger: The user opens or reads logs from success, failure, stale, or preserved-result state.

Visible UI state: Log details are visible without replacing the current screenshot, stale marker, or failure summary.

Allowed user actions: continue reviewing logs, retry when available, rebuild, switch panels, or leave the surface.

Client state change: Log visibility changes without changing the latest preview result.

Recovery / next state: The surface returns to `7 Stable Preview Surface`.

Blocks progress: no.

## State Language

### 0.1 Preview Readiness Language

Trigger: The Preview surface is checking whether preview can run, or a readiness check has resolved.

Visible UI state: Readiness language uses checking, ready, unavailable, missing setup, and missing target labels. Blocked readiness states disable the start action and explain what must change before preview can run.

Allowed user actions: read the status, retry readiness if available, correct setup, or leave the surface.

Client state change: The visible readiness language stays aligned with the current preview availability.

Recovery / next state: Returns to the active journey state.

Blocks progress: only when the readiness language represents a blocked state.

### 0.2 Preview Progress Language

Trigger: A start, build, capture, retry, or rebuild action is active.

Visible UI state: Progress language uses loading, building, capturing, retrying, and rebuilding labels. Duplicate run actions remain unavailable while progress is active.

Allowed user actions: wait, inspect logs, switch panels, or leave the surface.

Client state change: The visible progress language stays attached to the current preview run.

Recovery / next state: Returns to the active journey state.

Blocks progress: temporarily, while the active run is unresolved.

### 0.3 Preview Result Language

Trigger: A screenshot result, stale result, previous result, or metadata state is visible.

Visible UI state: Result language uses latest, stale, previous, metadata, and review-reference labels so the user can tell whether the screenshot is current.

Allowed user actions: inspect the screenshot, inspect metadata, rebuild, compare with source changes, or leave the surface.

Client state change: The visible result language stays aligned with the latest known screenshot state.

Recovery / next state: Returns to the active journey state.

Blocks progress: no.

### 0.4 Preview Recovery Language

Trigger: Preview is blocked, build fails, capture fails, or a failed rebuild preserves an old screenshot.

Visible UI state: Recovery language names the blocked or failed state, keeps logs reachable, and shows retry or correction actions.

Allowed user actions: retry, inspect logs, correct setup or source, compare a preserved screenshot, or leave the surface.

Client state change: The visible recovery language stays aligned with the current blocked or failed state.

Recovery / next state: Returns to the active journey state or the matching retry state.

Blocks progress: yes when a new current screenshot cannot be produced.

## Errors And Recovery

### 4.2 Build Fails Before Screenshot Capture

Trigger: The build phase fails before a screenshot can be captured.

Visible UI state: The Preview surface shows a build error summary, log summary, and retry action. Source context remains reachable from the surrounding workbench.

Allowed user actions: inspect logs, fix the cause, retry, switch panels, or leave the surface.

Client state change: The active run ends as failed and becomes recoverable.

Recovery / next state: Retry moves to `4.2.1 Retry After Build Failure`; reviewing logs can move to `7.3 Logs Remain Available`.

Blocks progress: yes, for the current preview run.

### 4.2.1 Retry After Build Failure

Trigger: The user clicks retry from the build failure state.

Visible UI state: The error state changes back into build progress.

Allowed user actions: wait, inspect logs, or leave the surface.

Client state change: A new build starts for the same preview target.

Recovery / next state: The run returns to `4 Build In Progress`.

Blocks progress: temporarily, while retry is active.

### 5.2 Capture Fails After Build Activity

Trigger: The build phase completes but screenshot capture fails.

Visible UI state: The Preview surface shows a capture error, log summary, and retry action.

Allowed user actions: inspect logs, fix the cause, retry, switch panels, or leave the surface.

Client state change: The active run ends as failed after build but before a new screenshot replaces the current result.

Recovery / next state: Retry moves to `5.2.1 Retry After Capture Failure`; reviewing logs can move to `7.3 Logs Remain Available`.

Blocks progress: yes, for the current preview run.

### 5.2.1 Retry After Capture Failure

Trigger: The user clicks retry from the capture failure state.

Visible UI state: The capture error changes back into preview progress.

Allowed user actions: wait, inspect logs, or leave the surface.

Client state change: A new preview run starts from the retry action.

Recovery / next state: The run returns to `4 Build In Progress`.

Blocks progress: temporarily, while retry is active.

### 7.2 Previous Screenshot Preserved After Failed Rebuild

Trigger: A rebuild fails while an earlier successful screenshot exists.

Visible UI state: The surface shows the previous screenshot or preserved-result area alongside the failure state, with clear labeling that it is not the new result.

Allowed user actions: compare against the previous screenshot, inspect logs, retry, switch panels, or leave the surface.

Client state change: The failed rebuild does not erase the last successful screenshot.

Recovery / next state: Recovery can move to `7.2.1 Recovery Returns To Stable Screenshot State` or `7.3 Logs Remain Available`.

Blocks progress: yes, for producing a new screenshot; no, for reviewing the previous result.

### 7.2.1 Recovery Returns To Stable Screenshot State

Trigger: The user retries after a preserved-result failure or chooses to keep the previous screenshot as the usable reference.

Visible UI state: A successful retry replaces the old screenshot; otherwise the preserved screenshot remains clearly labeled as previous.

Allowed user actions: inspect the result, retry again if still failed, inspect logs, switch panels, or leave the surface.

Client state change: The preview surface resolves to either a new latest result or the preserved previous result.

Recovery / next state: Success returns to `6 Screenshot Result`; preserved review returns to `7 Stable Preview Surface`.

Blocks progress: no once a stable screenshot state is available.

### Recovery Summary

| Error / branch | Where it appears | Recovery |
| --- | --- | --- |
| Preview capability unavailable | `2.1` | Correct workspace setup or return later; no preview run starts. |
| Missing dependency | `2.2` | Follow setup guidance, retry readiness when available, or leave the surface. |
| No display target | `2.3` | Add or select a previewable target, then return through readiness checks. |
| Build failure | `4.2` | Inspect logs, correct the cause, retry through `4.2.1`, or keep the stable surface visible. |
| Capture failure | `5.2` | Inspect logs, correct the cause, retry through `5.2.1`, or keep the stable surface visible. |
| Stale screenshot | `7.1` | Rebuild through `7.1.1` or continue reviewing the clearly marked previous result. |
| Failed rebuild with previous screenshot | `7.2` | Compare against the preserved screenshot, inspect logs, retry, or keep the preserved result clearly labeled. |

## Wireframe

The HTML wireframe should use this record as the route source. It must keep six first-level canvas bands in this order: Main happy path, Route map, Branch journeys, Detail states, State language, and Errors and recovery. Route rows should be full-size product wireframe rows and should not replace the Branch, Detail, State language, or Errors and recovery bands.
