---
name: setup
description: Initialize stack-agents plugin - configures CLAUDE.md with agent delegation rules and optional hooks. Supports resume if interrupted.
allowed_tools:
  - Read
  - Write
  - Edit
  - Bash
  - AskUserQuestion
  - WebFetch
---

# Stack Agents Setup

You are setting up the stack-agents plugin. This setup:
1. Configures CLAUDE.md with agent delegation rules
2. Optionally adds test enforcement hooks
3. Saves progress for resume capability

## Setup Flow

### Step 1: Check for Existing Setup / Resume

```bash
mkdir -p ~/.claude/.stack-agents
cat ~/.claude/.stack-agents/setup-state.json 2>/dev/null || echo '{"step": 0}'
```

If `step > 0` and `step < 5`, ask user:
- "Resume from step [N]?"
- "Start fresh?"

### Step 2: Welcome & Scope Selection

Display:
```
╔══════════════════════════════════════════════════════════════╗
║                    Stack Agents Setup                        ║
║   29 specialized agents for full-stack development           ║
╚══════════════════════════════════════════════════════════════╝
```

Use AskUserQuestion:
```json
{
  "question": "Where should stack-agents be configured?",
  "header": "Scope",
  "options": [
    {"label": "Global (recommended)", "description": "Applies to ALL projects (~/.claude/CLAUDE.md)"},
    {"label": "Local", "description": "Only this project (.claude/CLAUDE.md)"}
  ]
}
```

Save state after choice:
```bash
echo '{"step": 2, "scope": "global|local"}' > ~/.claude/.stack-agents/setup-state.json
```

### Step 3: Configure CLAUDE.md

Read the template:
```bash
cat ${CLAUDE_PLUGIN_ROOT}/templates/CLAUDE.md
```

Determine target path:
- Global: `~/.claude/CLAUDE.md`
- Local: `.claude/CLAUDE.md`

If target exists:
1. Read existing content
2. Check if stack-agents section already exists
3. If exists, ask to replace or skip
4. If not exists, append to file

If target doesn't exist:
1. Create directory if needed
2. Write template content

Save state:
```bash
echo '{"step": 3, "scope": "...", "claudeMdPath": "..."}' > ~/.claude/.stack-agents/setup-state.json
```

### Step 4: Test Enforcement Hook (Optional)

Use AskUserQuestion:
```json
{
  "question": "Enable automatic test enforcement?",
  "header": "Testing",
  "options": [
    {"label": "Yes (recommended)", "description": "Stop hook reminds you to write tests after implementation"},
    {"label": "No", "description": "No automatic reminders"}
  ]
}
```

If yes, read `~/.claude/settings.json` and merge:
```json
{
  "hooks": {
    "Stop": [
      {
        "type": "prompt",
        "prompt": "Check if implementation work was done in this session. If code was written/modified (not just research/exploration), verify: 1) Tests were written or delegated to testing-agent, 2) Acceptance criteria were considered. Return {\"ok\": true} if tests covered or no implementation occurred. Return {\"ok\": false, \"reason\": \"Tests needed for: [brief description]\"} if implementation lacks tests.",
        "timeout": 30
      }
    ]
  }
}
```

IMPORTANT: Merge hooks, don't replace entire settings.json!

Save state:
```bash
echo '{"step": 4, "scope": "...", "claudeMdPath": "...", "testHook": true|false}' > ~/.claude/.stack-agents/setup-state.json
```

### Step 5: Completion

Save final state:
```bash
echo '{"step": 5, "version": "1.0.0", "setupDate": "'$(date -Iseconds)'", "scope": "...", "claudeMdPath": "...", "testHook": ...}' > ~/.claude/.stack-agents/setup-state.json
```

Display completion message:

```
╔══════════════════════════════════════════════════════════════╗
║                  ✓ Setup Complete!                           ║
╚══════════════════════════════════════════════════════════════╝

Configuration:
  • CLAUDE.md: [path]
  • Test enforcement: [enabled/disabled]

Your 29 specialized agents are now active:

  Analysis (haiku - fast & cheap):
    code-reviewer, accessibility-auditor, api-designer,
    architecture-agent, compliance-advisor, performance-analyst,
    ux-consultant

  Implementation (opus - full capability):
    frontend-agent, python-backend, backend-nodejs, bff-agent,
    postgres-engineer, firestore-data, auth-agent, testing-agent,
    docker-agent, gcp-infra, events-agent, security-agent,
    + 10 more specialists

Quick Start:
  • "Create a React component" → frontend-agent
  • "Add a FastAPI endpoint" → python-backend
  • "Review this PR" → code-reviewer (haiku)

Commands:
  /stack-agents:agents    - List all agents
  /stack-agents:worktree  - Git worktree helper
  /stack-agents:debug     - Systematic debugging
  /stack-agents:setup     - Reconfigure

Happy coding! 🚀
```

## Error Handling

If any step fails:
1. Save current state with error info
2. Display error message
3. Suggest running `/stack-agents:setup` to resume

## Resume Logic

When resuming:
1. Read state from `~/.claude/.stack-agents/setup-state.json`
2. Skip completed steps
3. Continue from last incomplete step
