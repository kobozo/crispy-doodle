---
name: debug
description: Systematic 4-phase debugging methodology for root cause analysis
allowed_tools:
  - Read
  - Glob
  - Grep
  - Bash
---

# Systematic Debugging

Load the systematic-debug skill and guide the user through the 4-phase debugging process.

Read the skill content first:
```
Read: ${CLAUDE_PLUGIN_ROOT}/skills/systematic-debug/SKILL.md
```

## Phases

1. **Reproduce** - Reliably reproduce the issue
2. **Gather Context** - Collect logs, metrics, git history
3. **Hypothesize** - Form multiple possible causes
4. **Test & Verify** - Systematically test until root cause found

Ask the user to describe their issue, then guide them through each phase.
