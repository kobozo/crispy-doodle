---
name: git-worktree
description: >-
  Use this skill for parallel development branches, experimental work isolation,
  and working on multiple features simultaneously. Triggers on: worktree, parallel branch,
  experimental, isolate feature, multiple branches, side work.
context: fork
---

# Git Worktree Management

Use git worktrees for parallel development without stashing or switching branches.

## When to Use Worktrees

- **Experimental features**: Test risky changes without affecting main work
- **Parallel development**: Work on multiple features simultaneously
- **Code review**: Check out a PR while keeping your work intact
- **Hotfix isolation**: Fix production issues while feature work is in progress
- **Comparison**: Keep two versions side-by-side for comparison

## Quick Reference

```bash
# Create worktree for new branch
git worktree add ../project-feature feature-branch

# Create worktree from existing branch
git worktree add ../project-hotfix hotfix/urgent-fix

# List all worktrees
git worktree list

# Remove a worktree
git worktree remove ../project-feature

# Prune stale worktree references
git worktree prune
```

## Workflow Patterns

### Pattern 1: Experimental Feature

```bash
# From main worktree, create experimental branch
git worktree add ../myproject-experiment experiment/new-approach

# Work in the experimental worktree
cd ../myproject-experiment
# ... make changes, commit ...

# If experiment succeeds, merge back
cd ../myproject
git merge experiment/new-approach

# Clean up
git worktree remove ../myproject-experiment
git branch -d experiment/new-approach
```

### Pattern 2: Parallel Feature Development

```bash
# Main development
git worktree add ../myproject-feature-a feature/user-auth
git worktree add ../myproject-feature-b feature/billing-system

# Now you have:
# - Main worktree for main/develop
# - feature-a worktree for auth work
# - feature-b worktree for billing work
```

### Pattern 3: Review a PR

```bash
# Check out PR without switching branches
git fetch origin pull/123/head:pr-123
git worktree add ../myproject-pr-123 pr-123

# Review the code
cd ../myproject-pr-123
# ... run tests, inspect code ...

# Clean up after review
cd ../myproject
git worktree remove ../myproject-pr-123
git branch -D pr-123
```

### Pattern 4: Hotfix While Feature in Progress

```bash
# You're working on a feature, but need an urgent fix
git worktree add ../myproject-hotfix -b hotfix/critical-bug main

cd ../myproject-hotfix
# ... fix the bug ...
git commit -m "fix: critical bug in auth"
git push origin hotfix/critical-bug

# Back to feature work
cd ../myproject
```

## Directory Structure Convention

```
~/code/
├── myproject/           # Main worktree (main/develop branch)
├── myproject-feature-x/ # Feature worktree
├── myproject-hotfix/    # Hotfix worktree
└── myproject-review/    # Code review worktree
```

## Best Practices

1. **Naming**: Use `{project}-{purpose}` naming for clarity
2. **Location**: Keep worktrees in the same parent directory
3. **Cleanup**: Remove worktrees when done to avoid confusion
4. **Prune**: Run `git worktree prune` periodically
5. **Dependencies**: Each worktree needs its own `node_modules` or venv

## Important Notes

- Each worktree shares the same `.git` directory (saves space)
- You cannot check out the same branch in multiple worktrees
- Changes committed in any worktree are visible to all (same repo)
- Run dependency installs in each worktree separately

## Common Issues

### "Branch is already checked out"
```bash
# The branch is in use in another worktree
git worktree list  # Find which worktree has it
```

### Stale worktree after manual deletion
```bash
# If you deleted the folder manually
git worktree prune
```

### Need same branch in two places
```bash
# Create a new branch from the same commit
git worktree add ../myproject-copy -b copy-of-feature feature-branch
```
