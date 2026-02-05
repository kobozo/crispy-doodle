---
name: setup
description: Initialize stack-agents plugin - configures CLAUDE.md with agent delegation rules and optional test enforcement hooks
allowed_tools:
  - Read
  - Write
  - Edit
  - Bash
  - AskUserQuestion
---

# Stack Agents Setup

You are setting up the stack-agents plugin for the user. This command configures Claude Code to use the specialized agents.

## Setup Steps

### Step 1: Check Existing Setup

First, check if setup has already been completed:

```bash
cat ~/.claude/.stack-agents-setup.json 2>/dev/null || echo "NOT_SETUP"
```

If setup exists, ask if they want to reconfigure or skip.

### Step 2: Choose Configuration Scope

Ask the user:

<question>
Where should I configure stack-agents?

**Global** (~/.claude/CLAUDE.md) - Applies to ALL projects
**Local** (.claude/CLAUDE.md) - Only this project
</question>

Use AskUserQuestion tool with options:
- "Global (recommended)" - All projects use agents
- "Local" - Only current project

### Step 3: Create/Update CLAUDE.md

Based on their choice, create or append to the appropriate CLAUDE.md file.

**Content to add:**

```markdown
## Stack Agents Configuration

**ALWAYS delegate to specialized agents** using the Task tool for non-trivial tasks.

### Agent Delegation Rules

When the user's request matches these patterns, delegate to the corresponding agent:

| Pattern | Agent |
|---------|-------|
| React, Vite, component, hook, frontend | frontend-agent |
| FastAPI, Pydantic, Python backend | python-backend |
| Fastify, Node.js, BFF, route | backend-nodejs, bff-agent |
| PostgreSQL, Drizzle, schema, SQL | postgres-engineer |
| Firestore, collection, document | firestore-data |
| JWT, auth, session, RBAC, Casbin | auth-agent |
| test, pytest, Vitest, mock | testing-agent |
| Docker, nginx, container | docker-agent |
| Terraform, Cloud Run, GCP | gcp-infra |
| Pub/Sub, event, message | events-agent |
| security, OWASP, XSS | security-agent |
| PR review, code audit | code-reviewer |

### Testing Requirements

After ANY implementation:
1. Delegate to testing-agent for unit tests
2. Run tests before marking complete

### Model Tiering

Analysis agents (code-reviewer, architecture-agent, etc.) use haiku for cost efficiency.
Implementation agents use opus for full capability.
```

### Step 4: Optional Test Enforcement Hook

Ask the user:

<question>
Enable automatic test enforcement?

This adds a Stop hook that reminds you to write tests when code is modified.
</question>

If yes, add to settings.json:

```json
{
  "hooks": {
    "Stop": [
      {
        "type": "prompt",
        "prompt": "Review if implementation work was done. If code was written (not just research), verify tests were written or delegated to testing-agent. Return {\"ok\": true} if covered or no implementation. Return {\"ok\": false, \"reason\": \"Tests needed for: [description]\"} if missing.",
        "timeout": 30
      }
    ]
  }
}
```

### Step 5: Save Setup State

Write setup completion state:

```bash
echo '{"version": "1.0.0", "setupDate": "'$(date -Iseconds)'", "scope": "global|local", "testHook": true|false}' > ~/.claude/.stack-agents-setup.json
```

### Step 6: Completion Message

Display:

```
✓ Stack Agents Setup Complete!

Configuration:
- CLAUDE.md: [path]
- Test enforcement: [enabled/disabled]

Your 29 specialized agents are now active:
- 7 analysis agents (haiku) - code-reviewer, architecture-agent, etc.
- 22 implementation agents (opus) - frontend-agent, python-backend, etc.

Try it: "Create a React component for user settings"
→ Claude will delegate to frontend-agent automatically

Skills available:
- /stack-agents:worktree - Git worktree management
- /stack-agents:debug - Systematic debugging methodology

Run /stack-agents:setup again to reconfigure.
```

## Important Notes

- If CLAUDE.md already exists, APPEND to it (don't overwrite)
- Use Edit tool to add content, preserving existing rules
- For settings.json, merge hooks (don't replace entire file)
- Always confirm before making changes
