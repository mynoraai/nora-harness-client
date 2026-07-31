# Settings Extensions Removed Boundary

Source rows: `SET-06`
Entry path: Settings
Status: Removed / historical

The Settings Extensions / ACP module is marked removed for the current journey
inventory. Do not expand this file as an active Settings tab journey and do not
use it to define ACP runtime-management behavior in Settings.

## Current Contract

| Row | Treatment | Notes |
| --- | --------- | ----- |
| `SET-06` | Removed | Settings Extensions / ACP runtime management is not a current journey. |

ACP runtime semantics, permission flow, renderer bridge behavior, and visible
Chat/Code ACP markers remain covered by the root ACP contract and provider
gateway boundary docs when applicable.

## Historical Anchors

Older Electron code may still contain `ExtensionsTab` and `AcpCard` anchors.
Treat them as historical implementation references only, not as active journey
requirements for this UI-contract pass.
