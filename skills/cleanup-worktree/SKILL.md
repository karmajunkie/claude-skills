---
name: cleanup-worktree
description: Remove a git worktree created for a Linear ticket
argument-hint: "<ticket-id>"
allowed-tools: Bash, Read, Glob, Grep
---

# Cleanup Worktree for Linear Ticket

Remove a git worktree that was created for parallel development on a Linear ticket.

## Arguments

- `$0` — The Linear ticket ID (e.g., `INT-70`)

## Steps

1. **Determine the main repo and switch to it**:
   ```bash
   git worktree list
   ```
   - Identify the main worktree (the one without a branch suffix in brackets, or marked as `(bare)`)
   - If currently in a worktree that will be deleted, change to the main repo first:
     ```bash
     cd <main-repo-path>
     ```
   - This ensures cleanup commands run from the main repo context

2. **Determine the base repo name**:
   ```bash
   basename $(git rev-parse --show-toplevel)
   ```
   - Use this as `<repo-name>` in subsequent steps

3. **Find the worktree** by listing existing worktrees:
   ```bash
   git worktree list
   ```
   - Look for a worktree matching the ticket ID pattern (case-insensitive)
   - The directory should be named `<repo-name>-<ticket-id-lowercase>` (e.g., `MyApp-int-70`)

4. **Verify the worktree exists**:
   - If no matching worktree is found, inform the user and exit
   - If found, note the worktree path and branch name

5. **Check for uncommitted changes** in the worktree:
   ```bash
   git -C <worktree-path> status --porcelain
   ```
   - If there are uncommitted changes, warn the user and ask for confirmation before proceeding

6. **Check for unpushed commits** in the worktree:
   ```bash
   git -C <worktree-path> log @{u}.. --oneline 2>/dev/null || echo "no upstream"
   ```
   - If there are unpushed commits, warn the user and ask for confirmation

7. **Remove the worktree**:
   ```bash
   git worktree remove <worktree-path>
   ```
   - If this fails due to modified/untracked files and user confirmed, use:
     ```bash
     git worktree remove --force <worktree-path>
     ```
   - If force removal fails, manually remove and prune:
     ```bash
     rm -rf <worktree-path>
     git worktree prune
     ```

8. **Delete the branch**:
   - Try safe delete first:
     ```bash
     git branch -d <branch-name>
     ```
   - If not merged, force delete:
     ```bash
     git branch -D <branch-name>
     ```

9. **Report the results** to the user:
   - Confirm worktree was removed
   - Confirm branch was deleted

## Notes

- Always verify with the user before deleting uncommitted work
- The worktree directory is in the parent directory (`../<repo-name>-<ticket-id-lowercase>`)
- If `git worktree remove` fails, fall back to manual removal + prune
