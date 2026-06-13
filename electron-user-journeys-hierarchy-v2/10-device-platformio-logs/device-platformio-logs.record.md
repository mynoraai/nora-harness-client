# Journey 10: Device / PlatformIO / Logs

This Markdown file is the text counterpart to `device-platformio-logs.wireframe.html`. It documents the current implemented journey. Each section ID matches a card or summary frame in the wireframe.

## Journey Scope

User entry: The user opens the `Device` tab or `Device Log` panel inside a Code workbench session.

User goal: The user checks PlatformIO readiness, refreshes device state, inspects connected or disconnected devices, reviews device details, starts USB monitoring when needed, and filters or pauses logs.

End state: The user can tell whether the working directory, PlatformIO, connected device, device stream, USB monitor, and log path are ready, missing, disconnected, paused, or error.

Implementation maturity: Implemented.

## Numbering And Route Tables

These tables define how the journey IDs should be read before opening the wireframe. Main journey steps use plain numbers. Branches, detail states, language states, and recovery states use decimal IDs scoped to the step where the state starts. Whole-journey language and recovery summary states use `0.x` IDs.

Main path states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `1` | Main path | Journey 10 | Code workbench device entry. |
| `2` | Main path | Journey 10 | Device tab readiness check. |
| `3` | Main path | Journey 10 | Device refresh and stream resolution. |
| `4` | Main path | Journey 10 | Device cards and diagnostics. |
| `5` | Main path | Journey 10 | Device Log dock opens. |
| `6` | Main path | Journey 10 | Log controls and reading state. |
| `7` | End state | Journey 10 | Stable device and log surface. |

Workspace recovery state:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `1.1` | Recovery branch | `1 Device Entry` | No working directory is available for device tools. |

PlatformIO branch and setup states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `2.1` | Branch journey | `2 Device Tab Readiness Check` | PlatformIO is ready. |
| `2.2` | Recovery branch | `2 Device Tab Readiness Check` | PlatformIO is missing but can be installed. |
| `2.2.1` | Branch follow-up | `2.2 PlatformIO Missing` | PlatformIO installation is in progress. |
| `2.2.2` | Branch follow-up | `2.2.1 PlatformIO Installing` | PlatformIO installation completes and verifies as ready. |
| `2.3` | Recovery branch | `2 Device Tab Readiness Check` | PlatformIO repair needs Homebrew or local setup. |
| `2.4` | Error branch | `2 Device Tab Readiness Check` | PlatformIO detection or installation failed. |

Device refresh, connection, and stream states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `3.1` | Branch journey | `3 Device Refresh And Stream Resolution` | Device stream is subscribed. |
| `3.2` | Recovery branch | `3 Device Refresh And Stream Resolution` | No device is connected for the working directory. |
| `3.3` | Branch journey | `3 Device Refresh And Stream Resolution` | Device stream is connecting or reconnecting. |
| `3.4` | Error branch | `3 Device Refresh And Stream Resolution` | Device status request failed. |
| `3.5` | Recovery branch | `3 Device Refresh And Stream Resolution` | Device stream is errored or idle. |

Device detail states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `4.1` | Detail state | `4 Device Cards And Diagnostics` | Connected device summary. |
| `4.2` | Detail state | `4 Device Cards And Diagnostics` | Device metrics and details. |
| `4.3` | Recovery branch | `4 Device Cards And Diagnostics` | Device becomes offline after being known. |

Device Log, USB monitor, and log stream states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `5.1` | Branch journey | `5 Device Log Dock Opens` | Recent logs load successfully. |
| `5.2` | Branch journey | `5 Device Log Dock Opens` | USB monitor is available but off. |
| `5.2.1` | Branch follow-up | `5.2 USB Monitor Off` | USB monitor starts streaming. |
| `5.2.2` | Branch follow-up | `5.2 USB Monitor Off` | USB monitor is running but no logs have arrived. |
| `5.2.3` | Error branch | `5.2 USB Monitor Off` | USB monitor stops or fails to start. |
| `5.3` | Error branch | `5 Device Log Dock Opens` | Device logs fail to load. |
| `5.4` | Recovery branch | `5 Device Log Dock Opens` | No device logs are available yet. |

Log control detail and recovery states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `6.1` | Detail state | `6 Log Controls And Reading State` | Severity filter changes visible rows. |
| `6.2` | Branch journey | `6 Log Controls And Reading State` | Auto-scroll is paused manually. |
| `6.2.1` | Branch follow-up | `6.2 Auto-scroll Paused` | Auto-scroll is resumed manually. |
| `6.3` | Detail state | `6 Log Controls And Reading State` | Hover temporarily pauses auto-scroll. |
| `6.4` | Recovery branch | `6 Log Controls And Reading State` | Selected filter has no matching rows. |

State language and cross-journey recovery states:

| ID | Type | Parent step | What it represents |
| --- | --- | --- | --- |
| `0.1` | State language | Whole journey | Device readiness language. |
| `0.2` | State language | Whole journey | Log control language. |
| `0.3` | Recovery summary | Whole journey | Missing workspace, PlatformIO, device, stream, and log recovery. |

Route table:

| Route | Composition | Result / next state |
| --- | --- | --- |
| Ready device and logs | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 4 -> 4.1 -> 4.2 -> 5 -> 5.1 -> 6 -> 6.1 -> 7` | The user sees PlatformIO ready, a subscribed device stream, connected device details, recent logs, and a stable filtered log view. |
| Missing working directory | `1 -> 1.1 -> 7` | The user sees that device status and logs require a working directory before the flow can continue. |
| PlatformIO missing, installed, then ready | `1 -> 2 -> 2.2 -> 2.2.1 -> 2.2.2 -> 3 -> 3.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 7` | The user installs PlatformIO, then returns to device status and logs. |
| PlatformIO needs local repair | `1 -> 2 -> 2.3 -> 2 -> 2.1 -> 3 -> 3.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 7` | The user follows local setup guidance, retries detection, and then continues through device status. |
| PlatformIO detection or install error | `1 -> 2 -> 2.4 -> 2 -> 2.1 -> 3 -> 3.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 7` | The user retries after fixing the local environment and continues once PlatformIO is ready. |
| No device connected | `1 -> 2 -> 2.1 -> 3 -> 3.2 -> 5 -> 5.4 -> 5.2 -> 5.2.1 -> 6 -> 7` | The user sees no connected device, opens logs, starts USB monitoring, and reaches a readable log surface when USB output arrives. |
| Device stream reconnects | `1 -> 2 -> 2.1 -> 3 -> 3.3 -> 3.1 -> 4 -> 4.1 -> 4.2 -> 5 -> 5.1 -> 7` | The user waits through reconnecting state and then sees live device details. |
| Device status request fails, then retry succeeds | `1 -> 2 -> 2.1 -> 3 -> 3.4 -> 3 -> 3.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 7` | The user retries device status and returns to the normal device path. |
| Device stream remains unavailable with USB fallback | `1 -> 2 -> 2.1 -> 3 -> 3.5 -> 5 -> 5.2 -> 5.2.1 -> 6 -> 7` | The user reads the stream problem and uses USB monitoring for logs. |
| Known device disconnects | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 4 -> 4.1 -> 4.3 -> 3 -> 3.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 7` | The user sees the device change to offline, reconnects or refreshes, then returns to a connected card and logs. |
| USB monitor starts without immediate logs | `1 -> 2 -> 2.1 -> 3 -> 3.2 -> 5 -> 5.2 -> 5.2.2 -> 5.2.1 -> 6 -> 7` | The user sees that USB monitor is running but quiet, then logs arrive and become readable. |
| USB monitor fails or stops | `1 -> 2 -> 2.1 -> 3 -> 3.5 -> 5 -> 5.2 -> 5.2.3 -> 5.2 -> 5.2.1 -> 6 -> 7` | The user sees the monitor failure, retries, and reaches active USB logs. |
| Device logs fail, then retry succeeds | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 4 -> 4.1 -> 5 -> 5.3 -> 5 -> 5.1 -> 6 -> 7` | The user retries log loading without leaving the Code session. |
| Filter has no matching rows | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 6 -> 6.1 -> 6.4 -> 6.1 -> 7` | The user changes the filter and returns to a readable log list. |
| Pause and resume auto-scroll | `1 -> 2 -> 2.1 -> 3 -> 3.1 -> 4 -> 4.1 -> 5 -> 5.1 -> 6 -> 6.2 -> 6.2.1 -> 7` | The user pauses the moving log view, reads safely, then resumes auto-scroll. |

## Main Path

### 1 Device Entry

User entry: The user is in Code mode with a selected code session or workbench context.

User action: The user opens the `Device` top tab or the `Device Log` bottom tab.

Visible UI state:

- The Code workbench remains the main shell.
- The top panel offers `All Files`, `Changes`, `Device`, and `Preview`.
- The bottom dock offers `Terminal` and `Device Log`.
- Device and log surfaces appear beside the current Code session, not as a separate page.

Client state change: The client resolves whether the current Code session has a working directory for device and log operations.

Exit / next state: Continue to `2 Device Tab Readiness Check` when a working directory exists, or `1.1 No Working Directory` when it does not.

### 2 Device Tab Readiness Check

User entry: The user opens or returns to the `Device` tab.

User action: The user waits for the panel to check PlatformIO and device status.

Visible UI state:

- The `Device` tab begins in a loading or checking state.
- PlatformIO setup may appear above device content when it is missing or errored.
- Device stream diagnostics are prepared for the active working directory.

Client state change: PlatformIO readiness, device status, and stream state begin loading for the current working directory.

Exit / next state: Continue to `2.1 PlatformIO Ready`, `2.2 PlatformIO Missing`, `2.3 PlatformIO Repair Needed`, or `2.4 PlatformIO Detection Or Install Error`.

### 3 Device Refresh And Stream Resolution

User entry: PlatformIO is ready or the user is continuing with available device diagnostics.

User action: The user waits for device status, clicks `Refresh`, or retries after a transient issue.

Visible UI state:

- The `Device Stream` area shows a state badge.
- The panel can show connecting, reconnecting, subscribed, idle, or error diagnostics.
- Device cards appear when devices are known.

Client state change: Device status refreshes and stream updates can update the device list, log list, and status badges.

Exit / next state: Continue to `3.1 Device Stream Subscribed`, `3.2 No Device Connected`, `3.3 Device Stream Connecting Or Reconnecting`, `3.4 Device Status Request Failed`, or `3.5 Device Stream Error Or Idle`.

### 4 Device Cards And Diagnostics

User entry: The `Device` tab has at least one known device.

User action: The user reviews the device card and diagnostic fields.

Visible UI state:

- Each card shows a device display name and `Online` or `Offline`.
- The details area shows connection type, agent state, gateway status, port, firmware, memory, and latency.
- Unknown values are shown as `--`.

Client state change: Current device details are shown and retained where possible while stream updates continue.

Exit / next state: Continue to `4.1 Connected Device Summary`, `4.2 Device Metrics And Details`, `4.3 Device Offline`, or `5 Device Log Dock Opens`.

### 5 Device Log Dock Opens

User entry: The user opens the `Device Log` bottom tab.

User action: The user reads current logs, starts USB monitoring, refreshes, or handles an empty/error state.

Visible UI state:

- The bottom dock shows log level filters, USB monitor button, pause/resume button, and refresh.
- A short hint explains whether USB logs are off, requested, streaming, or stopped.
- Log rows appear when recent logs exist.

Client state change: Recent logs load for the active working directory and selected device when available.

Exit / next state: Continue to `5.1 Recent Logs Loaded`, `5.2 USB Monitor Off`, `5.3 Device Logs Failed`, or `5.4 No Device Logs Yet`.

### 6 Log Controls And Reading State

User entry: The `Device Log` dock is open.

User action: The user filters by level, pauses or resumes auto-scroll, hovers while reading, or refreshes logs.

Visible UI state:

- Level pills show `All`, `ERROR`, `WARN`, `INFO`, and `DEBUG`.
- The pause/resume control changes icon based on manual pause state.
- Refresh remains available.
- The list scroll behavior changes without replacing the Code session.

Client state change: Filtering and auto-scroll state update the visible log list while preserving loaded logs.

Exit / next state: Continue to `6.1 Severity Filter Applied`, `6.2 Auto-scroll Paused`, `6.3 Hover Auto-scroll Hold`, `6.4 No Logs Match Filter`, or `7 Stable Device And Log Surface`.

### 7 Stable Device And Log Surface

User entry: The user has reached a ready, missing, disconnected, paused, empty, or error state.

User action: The user continues work, retries a recovery action, changes filters, switches panels, or leaves the workbench.

Visible UI state:

- The Code session remains visible.
- Device and log status remain inline in the right panel and bottom dock.
- The user can see whether the next action is refresh, retry, install, repair, start USB monitor, resume, change filter, reconnect hardware, or select a working directory.

Client state change: Device and log state remain scoped to the active working directory until the user changes workbench context.

Exit / next state: End.

## Branch Journeys

### 2.1 PlatformIO Ready

Trigger: The PlatformIO readiness check succeeds.

Visible UI state: The blocking setup card is absent; the user can continue with device refresh, stream diagnostics, and logs.

Allowed user actions: refresh devices, open logs, switch panels, or continue reviewing the current device state.

Client state change: PlatformIO-dependent device actions are treated as available.

Recovery / next state: Continue to `3 Device Refresh And Stream Resolution`.

Blocks progress: no.

### 2.2.1 PlatformIO Installing

Trigger: The user clicks `Install PlatformIO`.

Visible UI state: The install action reads `Installing...` and the setup controls prevent duplicate install attempts.

Allowed user actions: wait, switch panels, or leave the journey.

Client state change: Installation state is polled while the setup card stays visible.

Recovery / next state: Successful installation moves to `2.2.2 PlatformIO Install Ready`; failure moves to `2.4 PlatformIO Detection Or Install Error`.

Blocks progress: temporarily, while setup is running.

### 2.2.2 PlatformIO Install Ready

Trigger: PlatformIO installation completes and verifies successfully.

Visible UI state: The setup card clears and device status can load again.

Allowed user actions: continue to device refresh, open logs, or switch panels.

Client state change: PlatformIO readiness and device status are refreshed.

Recovery / next state: Continue to `3 Device Refresh And Stream Resolution`.

Blocks progress: no.

### 3.1 Device Stream Subscribed

Trigger: The device stream reaches a live subscribed state.

Visible UI state: The `Device Stream` badge reads `Subscribed`; detail text can show last event or connected time.

Allowed user actions: read device cards, refresh, open `Device Log`, switch panels, or leave the journey.

Client state change: Incoming device events can refresh cards and logs.

Recovery / next state: Continue to `4 Device Cards And Diagnostics`.

Blocks progress: no.

### 3.3 Device Stream Connecting Or Reconnecting

Trigger: The stream is starting, temporarily disconnected, or reconnecting.

Visible UI state: The `Device Stream` badge reads `Connecting` or `Reconnecting`, with diagnostic detail below it.

Allowed user actions: wait, refresh device status, open logs, switch panels, or continue with cached information.

Client state change: Reconnect continues while live stream updates are temporarily unavailable.

Recovery / next state: Successful reconnect moves to `3.1 Device Stream Subscribed`; unresolved failure moves to `3.5 Device Stream Error Or Idle`.

Blocks progress: temporarily for live updates, but not for reading visible state.

### 5.1 Recent Logs Loaded

Trigger: `Device Log` loads recent entries successfully.

Visible UI state: The log list shows rows with time, severity, title, and detail.

Allowed user actions: filter by level, pause auto-scroll, refresh, start or stop USB monitor, switch panels, or leave.

Client state change: Recent logs are available for filtering and reading.

Recovery / next state: Continue to `6 Log Controls And Reading State`.

Blocks progress: no.

### 5.2 USB Monitor Off

Trigger: The `Device Log` dock is open and USB monitoring is not active.

Visible UI state: The button reads `Start USB Monitor`; hint text says USB logs are off until the monitor starts.

Allowed user actions: start USB monitor, refresh logs, filter logs, switch panels, or leave.

Client state change: USB monitoring is inactive until requested.

Recovery / next state: Starting monitor moves to `5.2.1 USB Monitor Streaming`, `5.2.2 USB Monitor Waiting For Logs`, or `5.2.3 USB Monitor Stopped Or Failed`.

Blocks progress: no for existing logs; yes for USB-only log capture.

### 5.2.1 USB Monitor Streaming

Trigger: The USB monitor starts successfully for a detected port.

Visible UI state: The button reads `Stop USB Monitor`; hint text says USB monitor is streaming from the selected port.

Allowed user actions: read logs, stop monitor, pause auto-scroll, filter logs, refresh, switch panels, or leave.

Client state change: USB serial output is added to the log list as device log entries.

Recovery / next state: Continue to `6 Log Controls And Reading State`.

Blocks progress: no.

### 5.2.2 USB Monitor Waiting For Logs

Trigger: The USB monitor is active but no log entries have arrived.

Visible UI state: Empty copy says `USB monitor is running, but no logs have arrived yet.`

Allowed user actions: wait, interact with the device, stop monitor, refresh, switch panels, or leave.

Client state change: The monitor remains active while the visible log list is empty.

Recovery / next state: Log arrival moves to `5.2.1 USB Monitor Streaming`; the user can continue to `6 Log Controls And Reading State`.

Blocks progress: temporarily for reading USB output.

### 6.2 Auto-scroll Paused

Trigger: The user clicks the pause control.

Visible UI state: The control changes to a resume icon and the log viewport stops following the newest row.

Allowed user actions: resume auto-scroll, change filters, refresh, keep reading, switch panels, or leave.

Client state change: New logs can arrive, but the scroll position is held.

Recovery / next state: Resume moves to `6.2.1 Auto-scroll Resumed`.

Blocks progress: no.

### 6.2.1 Auto-scroll Resumed

Trigger: The user clicks the resume control after manual pause.

Visible UI state: The control returns to the pause icon and the list can follow newest visible rows.

Allowed user actions: keep reading, pause again, filter, refresh, switch panels, or leave.

Client state change: Auto-scroll resumes for the current visible list.

Recovery / next state: Continue to `7 Stable Device And Log Surface`.

Blocks progress: no.

## Detail States

### 4.1 Connected Device Summary

Trigger: A known device is online.

Visible UI state: The device card header shows the display name and `Online`; connection shows `USB`, `WiFi`, or `Unknown`.

Allowed user actions: read details, refresh, open logs, switch panels, or leave.

Client state change: The online device can be used as the current status and log context when available.

Recovery / next state: Continue to `4.2 Device Metrics And Details` or `5 Device Log Dock Opens`.

Blocks progress: no.

### 4.2 Device Metrics And Details

Trigger: A device card is visible and diagnostic fields are available or partially available.

Visible UI state: The card shows `Connection`, `Agent`, `Gateway`, `Port`, `Firmware`, `Memory`, and `Latency`; unavailable values show `--`.

Allowed user actions: refresh, open logs, switch panels, or leave.

Client state change: Device details are displayed without replacing the current Code session.

Recovery / next state: Continue to `5 Device Log Dock Opens`.

Blocks progress: no.

### 6.1 Severity Filter Applied

Trigger: The user clicks `All`, `ERROR`, `WARN`, `INFO`, or `DEBUG`.

Visible UI state: The selected pill is highlighted and the visible rows change immediately.

Allowed user actions: choose another filter, pause or resume auto-scroll, refresh, start or stop USB monitor, switch panels, or leave.

Client state change: The visible list is filtered while loaded log entries remain available.

Recovery / next state: If the filter has no matches, move to `6.4 No Logs Match Filter`; otherwise the user can reach `7 Stable Device And Log Surface`.

Blocks progress: no.

### 6.3 Hover Auto-scroll Hold

Trigger: The pointer enters the log list while the user is reading.

Visible UI state: The log list remains stable while the user reads rows.

Allowed user actions: move the pointer away, filter, pause manually, refresh, or leave.

Client state change: Auto-scroll is temporarily held until the pointer leaves the log list.

Recovery / next state: Continue to `7 Stable Device And Log Surface`.

Blocks progress: no.

## State Language

### 0.1 Device Readiness Language

Trigger: Any Device tab state is visible.

Visible UI state: Status and action language uses `Device Stream`, `Subscribed`, `Connecting`, `Reconnecting`, `Error`, `Idle`, `Online`, `Offline`, `Refresh`, `Retry`, setup copy, and PlatformIO setup actions.

Allowed user actions: follow the visible action for the current state.

Client state change: Status language stays aligned with device readiness.

Recovery / next state: Return to the active device state.

Blocks progress: no.

### 0.2 Log Control Language

Trigger: Any Device Log state is visible.

Visible UI state: Log controls use `Device Log`, `Start USB Monitor`, `Stop USB Monitor`, `Starting USB Monitor...`, `Stopping USB Monitor...`, `All`, `ERROR`, `WARN`, `INFO`, `DEBUG`, pause, resume, and refresh.

Allowed user actions: follow the visible action for the current log state.

Client state change: Control labels reflect current USB monitor, filter, and auto-scroll state.

Recovery / next state: Return to the active log state.

Blocks progress: no.

## Errors And Recovery

### 1.1 No Working Directory

Trigger: The user opens `Device` or `Device Log` without a working directory available for the current Code session.

Visible UI state: The panel asks the user to pick a working directory before inspecting device status or logs.

Allowed user actions: select or create a working directory, switch sessions, switch panels, or leave the journey.

Client state change: Device status, PlatformIO status, stream state, and logs are cleared for the empty context.

Recovery / next state: Selecting a working directory returns to `1 Device Entry`.

Blocks progress: yes, for device and log inspection.

### 2.2 PlatformIO Missing

Trigger: The PlatformIO readiness check reports that PlatformIO is not available.

Visible UI state:

- A setup card says `PlatformIO is required for device development`.
- The card explains that build, flash, serial port detection, and some device actions need the PlatformIO CLI.
- Actions include `Install PlatformIO` and `Retry detection`.

Allowed user actions: install PlatformIO, retry detection, repair local setup, switch panels, or leave the journey.

Client state change: PlatformIO-dependent device work is blocked until readiness is satisfied.

Recovery / next state: `Install PlatformIO` moves to `2.2.1 PlatformIO Installing`; retry returns to `2 Device Tab Readiness Check`.

Blocks progress: yes, for PlatformIO-dependent device actions.

### 2.3 PlatformIO Repair Needed

Trigger: The setup flow cannot install PlatformIO because a local prerequisite such as Homebrew is missing.

Visible UI state: The setup card explains the missing local setup, may show a Homebrew command, and offers `Retry after Homebrew install`.

Allowed user actions: complete local setup outside the panel, retry detection, switch panels, or leave the journey.

Client state change: PlatformIO remains unavailable and no duplicate install starts.

Recovery / next state: Retry returns to `2 Device Tab Readiness Check`.

Blocks progress: yes, for PlatformIO-dependent device actions.

### 2.4 PlatformIO Detection Or Install Error

Trigger: PlatformIO detection fails, install fails, or verification still cannot confirm PlatformIO after setup.

Visible UI state: The setup card remains visible with an error message and `Retry detection`.

Allowed user actions: retry detection, retry install when available, repair local setup, switch panels, or leave the journey.

Client state change: PlatformIO stays in an error state.

Recovery / next state: Retry returns to `2 Device Tab Readiness Check`.

Blocks progress: yes, for PlatformIO-dependent device actions.

### 3.2 No Device Connected

Trigger: The device refresh completes with no device detected.

Visible UI state: The `Device` tab shows `No device connected for this workspace.` while stream diagnostics remain visible.

Allowed user actions: connect a device, click `Refresh`, open `Device Log`, start USB monitor when available, switch panels, or leave.

Client state change: The device list is empty and remains ready for later stream or refresh updates.

Recovery / next state: Refresh returns to `3 Device Refresh And Stream Resolution`; opening logs continues to `5 Device Log Dock Opens`.

Blocks progress: yes, for inspecting connected device details; no, for opening logs or retrying.

### 3.4 Device Status Request Failed

Trigger: Device status fails to load because the service is unavailable, timed out, or returned another recoverable failure.

Visible UI state: The `Device` tab shows the error and a `Retry` action while keeping stream diagnostics visible.

Allowed user actions: click `Retry`, open logs, switch panels, or leave.

Client state change: The failed request does not discard the active Code session or working directory.

Recovery / next state: Retry returns to `3 Device Refresh And Stream Resolution`.

Blocks progress: yes, for current device status.

### 3.5 Device Stream Error Or Idle

Trigger: The stream cannot subscribe, becomes idle, or reports an error.

Visible UI state: The badge reads `Error` or `Idle`; diagnostics explain the stream state when available.

Allowed user actions: refresh device status, open logs, start USB monitor, switch panels, or leave.

Client state change: Live stream updates are not arriving; cached or locally detected device/log state can still be shown.

Recovery / next state: Continue to `5 Device Log Dock Opens` for USB fallback, or `7 Stable Device And Log Surface`.

Blocks progress: yes for live stream updates; no for visible cached or USB fallback states.

### 4.3 Device Offline

Trigger: A previously known device is marked offline or the stream disconnects after a device was visible.

Visible UI state: The device card changes to `Offline`; known fields can remain visible where available.

Allowed user actions: reconnect hardware, click `Refresh`, open logs, switch panels, or leave.

Client state change: The device is marked offline while previous details are preserved when possible.

Recovery / next state: Refresh returns to `3 Device Refresh And Stream Resolution`.

Blocks progress: yes, for live connected-device work.

### 5.2.3 USB Monitor Stopped Or Failed

Trigger: The USB monitor exits, cannot start, or loses the selected port.

Visible UI state: Hint text explains that the monitor stopped or could not start for the port.

Allowed user actions: reconnect hardware, start USB monitor again, refresh, switch panels, or leave.

Client state change: USB monitor state returns to inactive and keeps the latest device or port detail when available.

Recovery / next state: Retry returns to `5.2 USB Monitor Off`.

Blocks progress: yes, for USB log capture.

### 5.3 Device Logs Failed

Trigger: Recent device logs fail to load.

Visible UI state: The log body shows a load error such as unavailable service or timeout.

Allowed user actions: refresh logs, start USB monitor when available, switch panels, or leave.

Client state change: The log request fails without leaving the active Code session.

Recovery / next state: Refresh returns to `5 Device Log Dock Opens`.

Blocks progress: yes, for current log viewing.

### 5.4 No Device Logs Yet

Trigger: Log loading succeeds but no rows are available.

Visible UI state: Empty copy says `No device logs yet. Start USB monitor to read logs from a USB device.`

Allowed user actions: start USB monitor, refresh, switch panels, or leave.

Client state change: The log list remains empty until logs arrive from the device stream or USB monitor.

Recovery / next state: Starting USB monitor moves to `5.2 USB Monitor Off`.

Blocks progress: no, because the state is clear and recoverable.

### 6.4 No Logs Match Filter

Trigger: The selected severity filter hides all loaded rows.

Visible UI state: Empty copy says `No logs match the selected filter.`

Allowed user actions: choose another filter, return to `All`, refresh, switch panels, or leave.

Client state change: The filter remains active and loaded logs remain available.

Recovery / next state: Choosing another filter returns to `6.1 Severity Filter Applied`.

Blocks progress: no.

### 0.3 Recovery Summary

Trigger: Any missing, disconnected, empty, paused, or error state appears in Journey 10.

Visible UI state: Recovery remains inline in the `Device` tab or `Device Log` dock; the Code session stays visible.

Allowed user actions: select a working directory, install or repair PlatformIO, reconnect hardware, retry device status, retry logs, start USB monitor, resume auto-scroll, change filters, switch panels, or leave the journey.

Client state change: The active Code session remains stable while the device or log path recovers.

Recovery / next state: Return to the relevant active state: `1`, `2`, `3`, `5`, `6`, or `7`.

Blocks progress: only for the specific missing or failed capability named by the visible state.

## Wireframe

The matching HTML must keep six first-level canvas bands in this order: `Main happy path`, `Route map`, `Branch journeys`, `Detail states`, `State language`, and `Errors and recovery`.

Wireframe requirements:

- The `Main happy path` band shows the Journey 10 main path only: `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7`.
- The `Route map` band contains one full-size product wireframe row for each route in the route table, with `Ready device and logs` first.
- The `Branch journeys` band contains full product wireframe cards for branch states, separate from the route rows.
- The `Detail states` band contains structured summary/status cards for `4.1`, `4.2`, `6.1`, and `6.3`.
- The `State language` band contains structured copy cards for `0.1` and `0.2`.
- The `Errors and recovery` band contains structured recovery cards for the recovery and error states, including `0.3 Recovery Summary`.
- Route rows cannot replace the branch, detail, state language, or error/recovery bands.
