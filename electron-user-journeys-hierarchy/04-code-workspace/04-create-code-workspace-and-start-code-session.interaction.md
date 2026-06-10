# Journey 04 - Create Code Workspace And Start Code Session

This is the text interaction description for the corresponding HTML wireframe, not a PM decision summary. It follows the Code Mode Quick Start tutorial for entering Code mode, creating/selecting a workspace, and entering the development prompt.

- Source HTML: [04-create-code-workspace-and-start-code-session.wireframe.html](04-create-code-workspace-and-start-code-session.wireframe.html)
- Status: Status: tutorial-aligned current flow
- Verification note: Based on NoraHarness Code Mode Quick Start and code-workspace-runtime-layout.jpg.

## User Goal

The Code quick start follows the tutorial: switch to Code mode, click Create Workspace, select the created workspace, then enter a hardware development prompt in the Code composer.

## Entry And Preconditions

- Entry: Switch to Code mode
- Initial user state: User switches from normal Chat into Code mode before starting any development prompt.
   - Chat/Code switch shows Code selected.
   - Workspaces section and Create Workspace entry are visible.

## Interaction Flow

1. Switch to Code mode
   - User sees: User switches from normal Chat into Code mode before starting any development prompt.
   - Chat/Code switch shows Code selected.
   - Workspaces section and Create Workspace entry are visible.
2. Create workspace
   - User sees: User clicks Create Workspace so the agent has a local project context for reading files, editing code, and running commands.
   - Create Workspace is in the Code sidebar.
   - This step must happen before the coding prompt.
3. Select workspace
   - User sees: After creating it, user enters or selects the workspace that will hold the hardware project context.
   - Selected workspace is highlighted in the left list.
   - The center tab is Code session, ready for the first task.
4. Enter hardware prompt
   - User sees: User describes board, goal, device connection, flashing need, and constraints in the Code mode input box.
   - Composer contains the development prompt.
   - Model pill and effort remain visible before send.

## Branches And Recovery States

### Guardrails

- Wrong mode warning: If the user is still in normal Chat, the journey warns that coding prompts should start in Code mode.
   - Do not paste development prompts into normal Chat.
   - Switch to Code before starting.
- No workspace selected: If no workspace is selected, the composer stays visible but the primary next action is to create or select a workspace.
   - Workspace is required for file edits and commands.
   - The prompt should not start a code run without context.
- Prompt checklist: The composer helper reminds the user to include board model, feature goal, USB/device state, flashing need, and provisioning constraints.
   - Board and flashing intent are explicit.
   - Mobile/BLE provisioning constraints are stated up front.

## End State

- Ends at: Enter hardware prompt
  - User describes board, goal, device connection, flashing need, and constraints in the Code mode input box.
   - Composer contains the development prompt.
   - Model pill and effort remain visible before send.

## Notes

- User outcome: The user starts hardware development from the correct Code mode context with a selected workspace and a usable development prompt.
- Constraints: Provider/model checks are covered by Journey 05; build, flash, deploy, and publish belong to later journeys.
- Evidence gaps: The tutorial image attachments are not available as files in this chat, so visual details are aligned to the available Code workspace screenshot and the pasted tutorial steps.
