---
name: plan-work
description: Enter plan mode and analyze the Linear ticket for the current worktree
allowed-tools: Bash, Read, Glob, Grep, AskUserQuestion, EnterPlanMode
---

# Plan Work for Current Worktree

Analyze the Linear ticket associated with the current git worktree and develop an implementation plan.

## Steps

1. **Get the current branch name**:
   ```bash
   git branch --show-current
   ```
   - The branch name format is `<ticket-id>/<description>` (e.g., `INT-70/sms-multi-client-association`)
   - Extract the ticket ID from the branch name (the part before the first `/`)

2. **Verify we're in a worktree** (not the main repo):
   ```bash
   git worktree list
   ```
   - Check that the current directory is a worktree, not the main repository
   - If in the main repo, inform the user and suggest using `/create-worktree` first

3. **Fetch the Linear ticket details**:
   ```bash
   linear issue view <ticket-id>
   ```
   - Capture the full ticket information including:
     - Title and description
     - Acceptance criteria (if any)
     - Labels and priority
     - Any linked issues or parent tickets

4. **Enter plan mode**:
   - Use the EnterPlanMode tool to switch to planning mode
   - This allows for thorough exploration before implementation

5. **Analyze the ticket and codebase**:
   - Review the ticket requirements
   - Explore relevant parts of the codebase
   - Identify files that will need modification
   - Note any dependencies or related systems

6. **Ask clarifying questions**:
   - Use AskUserQuestion to clarify any ambiguous requirements
   - Ask about:
     - Expected behavior edge cases
     - UI/UX preferences (if applicable)
     - Testing requirements
     - Any constraints or considerations not in the ticket
   - Only ask questions that are necessary - don't over-ask

7. **Develop the implementation plan**:
   - Create a step-by-step plan covering:
     - Files to create or modify
     - Key implementation decisions
     - Testing strategy
     - Any migrations or schema changes needed
   - Write the plan to the plan file for user approval

## Notes

- This skill assumes you're working in an Elixir/Phoenix project with Ash Framework
- The Linear CLI must be installed and configured
- If the branch doesn't follow the `<ticket-id>/<description>` pattern, ask the user for the ticket ID
- Focus on understanding the full scope before proposing solutions
