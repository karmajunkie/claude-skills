# Claude Skills

Custom Claude Code skills for development workflows.

## Installation

```bash
/plugin install github.com/karmajunkie/claude-skills
```

## Skills

### create-worktree

Create a git worktree for a Linear ticket with dependencies pre-copied.

```
/claude-skills:create-worktree INT-70
```

Features:
- Fetches ticket details from Linear CLI
- Generates branch name from ticket
- Creates worktree in parent directory
- Copies `deps/` and `_build/` to avoid recompilation
- Copies environment files (`.env*`, `.envrc`)
- Sets up `MIX_TEST_PARTITION` for isolated test databases
- Runs `mix ash.setup` for the new worktree

### cleanup-worktree

Remove a git worktree created for a Linear ticket.

```
/claude-skills:cleanup-worktree INT-70
```

Features:
- Finds worktree by ticket ID
- Warns about uncommitted changes and unpushed commits
- Safely removes the worktree
- Optionally deletes the branch
- Optionally drops the test database partition

## Requirements

- [Linear CLI](https://github.com/linear/linear-cli) for fetching ticket details
- Git with worktree support
- Elixir/Mix for Ash-based projects

## License

MIT
