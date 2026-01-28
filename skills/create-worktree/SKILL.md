---
name: create-worktree
description: Create a git worktree for a Linear ticket with deps pre-copied
argument-hint: "<ticket-id>"
allowed-tools: Bash, Read, Glob, Grep
---

# Create Worktree for Linear Ticket

Create a git worktree for parallel development on the given Linear ticket.

## Arguments

- `$0` — The Linear ticket ID (e.g., `INT-70`)

## Steps

1. **Determine the base repo name**:
   ```bash
   basename $(git rev-parse --show-toplevel)
   ```
   - Use this as `<repo-name>` in subsequent steps

2. **Fetch ticket details** using the Linear CLI:
   ```bash
   linear issue view $0
   ```

3. **Generate branch name** from the ticket:
   - Format: `<ticket-id>/<short-kebab-description>`
   - Example: `INT-70/sms-multi-client-association`
   - Keep the description concise (3-5 words max)

4. **Determine the next available MIX_TEST_PARTITION** by checking existing worktrees:
   ```bash
   git worktree list
   ```
   - Main repo uses no partition (or partition 0)
   - Assign the next unused integer (1, 2, 3, etc.)

5. **Create the worktree** in the parent directory:
   ```bash
   git worktree add ../<repo-name>-<ticket-id-lowercase> -b <branch-name> main
   ```
   - Example: `git worktree add ../MyApp-int-70 -b INT-70/sms-multi-client-association main`

6. **Copy deps and _build folders** to avoid recompilation:
   ```bash
   cp -r deps ../<repo-name>-<ticket-id-lowercase>/
   cp -r _build ../<repo-name>-<ticket-id-lowercase>/
   ```

7. **Copy environment files** from the source repo:
   ```bash
   cp .env* ../<repo-name>-<ticket-id-lowercase>/ 2>/dev/null || true
   cp -P .envrc ../<repo-name>-<ticket-id-lowercase>/ 2>/dev/null || true
   ```
   Note: `-P` preserves the symlink

8. **Set MIX_TEST_PARTITION in .env** for the new worktree:
   - Remove any existing `MIX_TEST_PARTITION` line, then append the new value
   ```bash
   sed -i '/^export MIX_TEST_PARTITION=/d' ../<repo-name>-<ticket-id-lowercase>/.env 2>/dev/null || true
   echo "export MIX_TEST_PARTITION=<N>" >> ../<repo-name>-<ticket-id-lowercase>/.env
   ```

9. **Set up the test database** with the assigned partition:
   ```bash
   cd ../<repo-name>-<ticket-id-lowercase> && MIX_TEST_PARTITION=<N> mix ash.setup
   ```

10. **Report the results** to the user:
    - Worktree location
    - Branch name
    - Assigned MIX_TEST_PARTITION number
    - Note that MIX_TEST_PARTITION is set in `.env`
    - Instructions for starting work:
      ```
      cd ../<repo-name>-<ticket-id-lowercase>
      ```
    - If using direnv, remind them to run `direnv allow`

## Notes

- Always create worktrees in the parent directory (`../`)
- Use lowercase ticket ID in the directory name (e.g., `MyApp-int-70`)
- Keep the original ticket ID case in the branch name (e.g., `INT-70/...`)
- If `mix ash.setup` fails, try `mix deps.get` first
