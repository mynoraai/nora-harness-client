# Agent UI Contract Test Matrix

Source rows: `ONB-*`, `MAIN-*`, `CHAT-*`, `CODE-*`, `AUTH-*`, `SET-*`, `BND-*`

## What This Covers

This folder maps the Agent UI contracts to tests. Do not invent new product categories here; every scenario must point back to one or more source rows from `docs/hardware_harness/ui-contracts/agent-ui-user-journey.md` and the matching screen README in this tree.

## Test Layers

| Layer                 | Purpose                                                          |
| --------------------- | ---------------------------------------------------------------- |
| L1 unit               | Pure helpers, stores, reducers, validators                       |
| L2 integration        | Renderer component state, transport, IPC/RPC boundary with mocks |
| L3 e2e / computer-use | Full visible journey through Electron UI                         |

## Surface Mapping

| Contract directory             | Source rows                              | Test mapping file           |
| ------------------------------ | ---------------------------------------- | --------------------------- |
| `onboarding/`                  | `ONB-*`                                  | `onboarding-tests.md`       |
| `main-window/`                 | `MAIN-*`                                 | `main-window-tests.md`      |
| `chat/`                        | `CHAT-*`                                 | `chat-tests.md`             |
| `code-ide/`                    | `CODE-*`                                 | `code-ide-tests.md`         |
| `agent-authoring/`             | `AUTH-*`                                 | `agent-authoring-tests.md`  |
| `settings/`                    | `SET-*`                                  | `settings-tests.md`         |
| `provider-gateway-boundaries/` | `BND-*` plus shared transport/state rows | `provider-gateway-tests.md` |

## Scenario Naming

Every scenario title must start with the source row:

```text
CHAT-05 selects a model from the grouped model picker
CODE-07 shows the no-git Retry state in Changes
SET-04 disables Save until provider credentials or service URL are present
BND-03 reconnects to an active run after renderer refresh
```

## Detail Files

- `three-tier-matrix.md` summarizes the L1/L2/L3 matrix for the split contracts.
- `coverage-index.md` lists current L1/L2 coverage, test anchors, and the coverage gap punch list.
- `l3-computer-use-scenarios.md` lists target Electron e2e and computer-use journeys plus selector backlog.
- `onboarding-tests.md` maps tests for `ONB-*`.
- `main-window-tests.md` maps tests for `MAIN-*`.
- `chat-tests.md` maps tests for `CHAT-*`.
- `code-ide-tests.md` maps tests for `CODE-*`.
- `agent-authoring-tests.md` maps tests for `AUTH-*`.
- `settings-tests.md` maps tests for `SET-*`.
- `provider-gateway-tests.md` maps tests for `BND-*`.

## Stable Selector Rule

L3 scenarios should prefer stable selectors when they exist. If the UI lacks an accessible name, role, `aria-label`, or `data-testid`, mark the test as blocked by `No stable selector` instead of relying on fragile pixel coordinates.

## Coverage Rules

1. Preserve existing test file anchors.
2. Keep `No test`, `Partial`, and live-test markers explicit.
3. Add the matching source row ID to each scenario.
4. Leave provider-live tests under provider/gateway coverage, not Chat or Settings.
