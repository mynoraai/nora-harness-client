# MOB-05 Agents

This document defines the Agents journey for HH Mobile Chat. It covers browsing agents, searching the catalog, opening agent detail, installing an agent, handling install failure, and recognizing installed state.

## User Journey

### 1. User opens the Agents tab

The catalog may load before any cards are visible. During this state, the user should understand that the agent list is being fetched.

<img src="../assets/8-agents-page/1-1agents-loading.png" alt="Agents loading" width="320" />

After loading succeeds, the user sees the available agents. Tapping a card opens the detail page for that agent.

<img src="../assets/8-agents-page/1-1agents-list.png" alt="Agents list" width="320" />

### 2. User searches the catalog

Typing in search narrows the catalog while staying on the Agents tab. The query should not affect onboarding agent lists or other surfaces.

<img src="../assets/8-agents-page/1-2agents-search.png" alt="Agents search" width="320" />

If no agents match the query, the user gets an empty state and can recover by editing or clearing the search text.

<img src="../assets/8-agents-page/1-3agent-search-no-result.png" alt="Agents search empty" width="320" />

### 3. User opens an agent detail page

The detail page explains the selected agent and shows whether it can be installed. This is the decision point before changing the active device/runtime state.

<img src="../assets/8-agents-page/2-agent-detail.png" alt="Agent detail" width="320" />

When the user taps install, the detail page enters an installing state and should prevent duplicate install submissions.

<img src="../assets/8-agents-page/2-1agent-detail-install.png" alt="Agent install" width="320" />

If install fails, the error should stay scoped to this agent detail attempt. The user can retry or go back without polluting the catalog state.

<img src="../assets/8-agents-page/2-2agent-detail-install-failed.png" alt="Agent install failed" width="320" />

After install succeeds, the page shows the installed state so the user understands the agent is already available.

<img src="../assets/8-agents-page/2-3agent-detail-installed.png" alt="Agent installed" width="320" />

## Control Contract

| Control             | Required behavior                                                                   |
| ------------------- | ----------------------------------------------------------------------------------- |
| Search              | Filters visible agents without destroying the fetched list cache.                   |
| Agent card          | Opens detail for that agent.                                                        |
| Back from detail    | Returns to the previous list/search state.                                          |
| Install             | Starts install for the selected agent and disables duplicate submits while pending. |
| Retry after failure | Clears the previous install error and restarts install.                             |

## State Contract

| State           | Required UI                                  | Exit condition                            |
| --------------- | -------------------------------------------- | ----------------------------------------- |
| Loading catalog | Progress/loading surface.                    | Catalog fetch succeeds or fails.          |
| Catalog ready   | Agent cards, search, and navigation.         | User searches or opens detail.            |
| Empty search    | No-result message with editable search.      | Query changes or clears.                  |
| Detail ready    | Agent metadata and install/installed status. | User installs or navigates away.          |
| Installing      | Pending install affordance.                  | Install succeeds or fails.                |
| Install failed  | Recoverable error state.                     | Retry or navigation clears state.         |
| Installed       | Installed badge/action state.                | Agent state changes from backend refresh. |

## Notes

- The screenshots cover install failure on agent detail, but not catalog fetch failure. Settings captures a related agent-load failure in MOB-07.
- Search should remain local to the Agents tab and not affect onboarding agent lists.
