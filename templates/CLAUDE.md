# Crispy Doodle Configuration

## Agent Delegation Rules

**ALWAYS delegate to specialized agents** for non-trivial tasks.

### Agent Teams (Preferred for Complex Tasks)

For tasks that benefit from parallel work or multiple perspectives, **create an agent team**:

```
Create an agent team with:
- frontend-agent: Handle UI components
- python-backend: Handle API endpoints
- testing-agent: Write tests for both
```

**When to use Agent Teams:**
- Cross-layer changes (frontend + backend + tests)
- Research and review (multiple perspectives)
- New features with multiple components
- Debugging with competing hypotheses

**When to use single agents (Task tool):**
- Focused, single-domain tasks
- Quick fixes or small changes
- Sequential work with dependencies

If agent teams are not enabled, this will gracefully fall back to normal Task tool delegation.

### Auto-Triggering

When the user's request matches these patterns, delegate to the corresponding agent:

| Pattern | Agent |
|---------|-------|
| React, Vite, component, hook, frontend, UI | frontend-agent |
| FastAPI, Pydantic, Python service, backend | python-backend |
| Fastify, Node.js, route, API | backend-nodejs |
| BFF, aggregation, session proxy | bff-agent |
| PostgreSQL, Drizzle, schema, SQL, query | postgres-engineer |
| Firestore, collection, document, NoSQL | firestore-data |
| JWT, auth, session, RBAC, Casbin, permission | auth-agent |
| test, pytest, Vitest, mock, fixture | testing-agent |
| Docker, nginx, container, compose | docker-agent |
| Terraform, Cloud Run, GCP, infra | gcp-infra |
| Pub/Sub, event, message, queue | events-agent |
| security, OWASP, XSS, injection | security-agent |
| PR review, code audit, code quality | code-reviewer |
| i18n, translation, locale | i18n-agent |
| WebSocket, real-time, live updates | realtime-engineer |

### Testing Requirements (MANDATORY)

Every implementation MUST include tests:

1. **After implementation** → Delegate to testing-agent
2. **Run tests** before marking complete

Pattern:
```
Task(testing-agent): "Write tests for [path]. Test: [behaviors]"
```

### Model Tiering

| Model | Agents | Use Case |
|-------|--------|----------|
| haiku | code-reviewer, accessibility-auditor, api-designer, architecture-agent, compliance-advisor, performance-analyst, ux-consultant | Analysis, reviews, planning |
| opus | All implementation agents | Code changes, complex reasoning |

### Git Enforcement Hooks (Optional)

These hooks can be enabled via `/crispy-doodle:setup`:

| Hook | Behavior |
|------|----------|
| Require commit | Blocks stopping if uncommitted changes exist |
| Require push | Blocks stopping if unpushed commits exist |
| Require feature branch | Blocks stopping if on main/master branch |

### Agent Teams Configuration

Enable agent teams in settings.json or settings.local.json:
```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

Or enable via `/crispy-doodle:setup`.

### Available Commands

- `/crispy-doodle:setup` - Reconfigure plugin
- `/crispy-doodle:agents` - List all agents
- `/crispy-doodle:worktree` - Git worktree helper
- `/crispy-doodle:debug` - Systematic debugging guide
