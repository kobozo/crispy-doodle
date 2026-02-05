---
name: worktree
description: Git worktree management for parallel development branches
allowed_tools:
  - Read
  - Bash
---

# Git Worktree Helper

Load the git-worktree skill and help the user with parallel development.

Read the skill content first:
```
Read: ${CLAUDE_PLUGIN_ROOT}/skills/git-worktree/SKILL.md
```

Then help the user with their worktree task. Common operations:

- **Create worktree**: `git worktree add ../project-feature feature-branch`
- **List worktrees**: `git worktree list`
- **Remove worktree**: `git worktree remove ../project-feature`

Ask what they want to do if not specified.
