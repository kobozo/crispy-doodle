---
name: setup
description: Initialize crispy-doodle plugin - configures CLAUDE.md with agent delegation rules and optional hooks. Supports resume if interrupted.
allowed_tools:
  - Read
  - Write
  - Edit
  - Bash
  - AskUserQuestion
  - WebFetch
---

# Crispy Doodle Setup

You are setting up the crispy-doodle plugin. This setup:
1. Configures CLAUDE.md with agent delegation rules
2. Optionally adds test enforcement hooks
3. Optionally adds git enforcement hooks (commit, push, feature branch)
4. Optionally configures a git-aware status line
5. Optionally enables Agent Teams (experimental feature for parallel work)
6. Saves progress for resume capability

## Setup Flow

### Step 1: Check for Existing Setup / Resume

```bash
mkdir -p ~/.claude/.crispy-doodle
cat ~/.claude/.crispy-doodle/setup-state.json 2>/dev/null || echo '{"step": 0}'
```

If `step > 0` and `step < 9`, ask user:
- "Resume from step [N]?"
- "Start fresh?"

### Step 2: Welcome & Scope Selection

Display:
```
╔══════════════════════════════════════════════════════════════╗
║                    Crispy Doodle Setup                        ║
║   29 specialized agents for full-stack development           ║
╚══════════════════════════════════════════════════════════════╝
```

Use AskUserQuestion:
```json
{
  "question": "Where should crispy-doodle be configured?",
  "header": "Scope",
  "options": [
    {"label": "Global (recommended)", "description": "Applies to ALL projects (~/.claude/CLAUDE.md)"},
    {"label": "Local", "description": "Only this project (.claude/CLAUDE.md)"}
  ]
}
```

Save state after choice:
```bash
echo '{"step": 2, "scope": "global|local"}' > ~/.claude/.crispy-doodle/setup-state.json
```

### Step 3: Configure CLAUDE.md

Read the template:
```bash
cat ${CLAUDE_PLUGIN_ROOT}/templates/CLAUDE.md
```

Determine target path:
- Global: `~/.claude/CLAUDE.md`
- Local: `.claude/CLAUDE.md`

#### 3a. Check for Stack-Agents Section

If target exists, read existing content and check if `# Crispy Doodle Configuration` section exists:
- If exists: ask to replace or skip (existing behavior)
- If not exists: proceed to conflict detection (Step 3b)

If target doesn't exist:
1. Create directory if needed
2. Write template content
3. Skip to Step 4

#### 3b. Conflict Detection

Before appending, scan the existing CLAUDE.md for content that may conflict with crispy-doodle:

**Conflict Patterns to Detect:**

| Pattern (case-insensitive) | Conflict Type | Description |
|----------------------------|---------------|-------------|
| `## Agents?` or `Agent.*Triggering` or `Agent.*Auto` | Agent Definitions | Existing agent delegation rules |
| `## Model Tiering` or `haiku.*opus` or `opus.*haiku` | Model Tiering | Existing model tier assignments |
| `## Testing.*MANDATORY` or `Testing Requirements` | Testing Rules | Existing mandatory testing rules |
| `frontend-agent\|python-backend\|backend-nodejs\|testing-agent\|code-reviewer` (outside comments) | Agent References | References to specific crispy-doodle |
| `Task\(.*-agent\)` or `Task tool.*agent` | Delegation Patterns | Existing delegation rules |

**Detection Logic:**

```
For each pattern:
  1. Search existing content (case-insensitive)
  2. If match found:
     a. Record conflict type
     b. Find line number(s) where conflict occurs
     c. Extract context (the matching line + 1 line before/after)
```

#### 3c. Handle Conflicts

**If conflicts detected:**

Display conflict report:
```
Found potential conflicts in existing CLAUDE.md:

⚠️  Agent Definitions (line 32-45)
    Existing: "## Agent Auto-Triggering" with patterns for frontend-agent, backend-nodejs

⚠️  Model Tiering (line 58-65)
    Existing: haiku/opus model assignments

⚠️  Testing Requirements (line 12-25)
    Existing: "## Testing Requirements (MANDATORY)"
```

Use AskUserQuestion:
```json
{
  "question": "Found existing configuration that may conflict with crispy-doodle. How should we proceed?",
  "header": "Conflicts",
  "options": [
    {"label": "Merge (keep both)", "description": "Append crispy-doodle config after existing content - may have duplicates"},
    {"label": "Replace conflicts", "description": "Comment out conflicting sections, then add crispy-doodle"},
    {"label": "Skip CLAUDE.md", "description": "Only configure hooks, don't modify CLAUDE.md"}
  ]
}
```

**Resolution Actions:**

- **Merge (keep both)**: Append crispy-doodle template to end of file with a separator:
  ```
  ---
  <!-- Crispy Doodle Configuration (added by /crispy-doodle:setup) -->
  [template content]
  ```

- **Replace conflicts**: For each conflicting section:
  1. Wrap in HTML comments: `<!-- [Commented by crispy-doodle setup]\n[original content]\n-->`
  2. Then append crispy-doodle template

- **Skip CLAUDE.md**: Set `claudeMdSkipped: true` in state, continue to Step 4

**If no conflicts detected:**

Append template normally with separator.

Save state:
```bash
echo '{"step": 3, "scope": "...", "claudeMdPath": "...", "conflicts": [...], "resolution": "merge|replace|skip"}' > ~/.claude/.crispy-doodle/setup-state.json
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

If yes, read `~/.claude/settings.json` and merge the test enforcement hook (see Step 7 for hook definitions).

Save state:
```bash
echo '{"step": 4, "scope": "...", "claudeMdPath": "...", "testHook": true|false}' > ~/.claude/.crispy-doodle/setup-state.json
```

### Step 5: Git Enforcement Hooks (Optional)

Use AskUserQuestion with multiSelect:
```json
{
  "question": "Which git enforcement hooks do you want?",
  "header": "Git Hooks",
  "multiSelect": true,
  "options": [
    {"label": "Require commit", "description": "Block stopping if uncommitted changes exist"},
    {"label": "Require push", "description": "Block stopping if unpushed commits exist"},
    {"label": "Require feature branch", "description": "Block stopping if on main/master branch"},
    {"label": "None", "description": "No git enforcement"}
  ]
}
```

If user selects "None", skip adding git hooks.
Otherwise, add selected hooks to `~/.claude/settings.json` (see Step 7 for hook definitions).

Save state:
```bash
echo '{"step": 5, "scope": "...", "claudeMdPath": "...", "testHook": ..., "gitHooks": {"requireCommit": true|false, "requirePush": true|false, "requireFeatureBranch": true|false}}' > ~/.claude/.crispy-doodle/setup-state.json
```

### Step 6: Git-Aware Status Line (Optional)

Use AskUserQuestion:
```json
{
  "question": "Enable git-aware status line?",
  "header": "Status Line",
  "options": [
    {"label": "Yes (recommended)", "description": "Shows current directory and git branch with colors"},
    {"label": "No", "description": "Keep default status line"}
  ]
}
```

If yes, add to `~/.claude/settings.json`:
```json
{
  "statusLine": {
    "type": "command",
    "command": "branch=$(git rev-parse --abbrev-ref HEAD 2>/dev/null); printf '\\033[01;34m%s\\033[00m' \"$(pwd)\"; [ -n \"$branch\" ] && printf ' \\033[01;36mon\\033[00m \\033[01;32m%s\\033[00m' \"$branch\""
  }
}
```

This displays:
- Current working directory (blue)
- "on" keyword (cyan)
- Current git branch (green)

Example output: `/Users/dev/myproject on feature/auth`

Save state:
```bash
echo '{"step": 6, "scope": "...", "claudeMdPath": "...", "testHook": ..., "gitHooks": {...}, "statusLine": true|false}' > ~/.claude/.crispy-doodle/setup-state.json
```

### Step 7: Agent Teams (Experimental)

Use AskUserQuestion:
```json
{
  "question": "Enable Agent Teams? (Experimental feature for parallel work)",
  "header": "Agent Teams",
  "options": [
    {"label": "Yes (recommended)", "description": "Enable coordinated multi-agent teams for complex tasks - uses more tokens but enables parallel work"},
    {"label": "No", "description": "Use standard single-agent mode only"}
  ]
}
```

If yes, add to `~/.claude/settings.json`:
```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

**Agent Teams Benefits:**
- Multiple agents work in parallel on different aspects
- Agents can communicate and coordinate with each other
- Great for: research/review, new features, debugging, cross-layer changes
- Falls back gracefully to normal agents if not enabled

Save state:
```bash
echo '{"step": 7, "scope": "...", "claudeMdPath": "...", "testHook": ..., "gitHooks": {...}, "statusLine": ..., "agentTeams": true|false}' > ~/.claude/.crispy-doodle/setup-state.json
```

### Step 8: Apply Hooks to settings.json

Read `~/.claude/settings.json` and merge selected hooks:

**Test Enforcement Hook** (if enabled in Step 4):
```json
{
  "hooks": [
    {
      "type": "prompt",
      "prompt": "Check if implementation work was done in this session. If code was written/modified (not just research/exploration), verify: 1) Tests were written or delegated to testing-agent, 2) Acceptance criteria were considered. Return {\"ok\": true} if tests covered or no implementation occurred. Return {\"ok\": false, \"reason\": \"Tests needed for: [brief description]\"} if implementation lacks tests.",
      "timeout": 30
    }
  ]
}
```

**Require Commit Hook** (if selected):
```json
{
  "hooks": [
    {
      "type": "prompt",
      "prompt": "Check if there are uncommitted changes. Run: git status --porcelain\n\nIf output is empty or only untracked files (?? prefix), return {\"ok\": true}.\nIf there are staged or modified tracked files (M, A, D, R, C prefixes), return {\"ok\": false, \"reason\": \"Uncommitted changes detected. Please commit your work before stopping.\"}",
      "timeout": 30
    }
  ]
}
```

**Require Push Hook** (if selected):
```json
{
  "hooks": [
    {
      "type": "prompt",
      "prompt": "Check if there are unpushed commits. Run: git log @{u}.. --oneline 2>/dev/null\n\nIf output is empty or command fails (no upstream), return {\"ok\": true}.\nIf there are unpushed commits listed, return {\"ok\": false, \"reason\": \"Unpushed commits detected. Please push your work before stopping.\"}",
      "timeout": 30
    }
  ]
}
```

**Require Feature Branch Hook** (if selected):
```json
{
  "hooks": [
    {
      "type": "prompt",
      "prompt": "Check current git branch. Run: git branch --show-current\n\nIf branch is NOT 'main' or 'master', return {\"ok\": true}.\nIf on main or master, return {\"ok\": false, \"reason\": \"You are on the default branch. Please create a feature branch for your work.\"}",
      "timeout": 30
    }
  ]
}
```

IMPORTANT: Merge hooks into existing Stop array, don't replace entire settings.json!

### Step 9: Completion

Save final state:
```bash
echo '{"step": 9, "version": "1.3.0", "setupDate": "'$(date -Iseconds)'", "scope": "...", "claudeMdPath": "...", "testHook": ..., "gitHooks": {...}, "statusLine": ..., "agentTeams": ...}' > ~/.claude/.crispy-doodle/setup-state.json
```

Display completion message:

```
╔══════════════════════════════════════════════════════════════╗
║                  ✓ Setup Complete!                           ║
╚══════════════════════════════════════════════════════════════╝

Configuration:
  • CLAUDE.md: [path]
  • Test enforcement: [enabled/disabled]
  • Git hooks: [list enabled hooks or "none"]
  • Status line: [enabled/disabled]
  • Agent Teams: [enabled/disabled]

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
  /crispy-doodle:agents    - List all agents
  /crispy-doodle:worktree  - Git worktree helper
  /crispy-doodle:debug     - Systematic debugging
  /crispy-doodle:setup     - Reconfigure

Happy coding! 🚀
```

## Error Handling

If any step fails:
1. Save current state with error info
2. Display error message
3. Suggest running `/crispy-doodle:setup` to resume

## Resume Logic

When resuming:
1. Read state from `~/.claude/.crispy-doodle/setup-state.json`
2. Skip completed steps
3. Continue from last incomplete step
