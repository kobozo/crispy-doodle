# Stack Agents Configuration

## Agent Delegation Rules

**ALWAYS delegate to specialized agents** using the Task tool for non-trivial tasks.

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

These hooks can be enabled via `/stack-agents:setup`:

| Hook | Behavior |
|------|----------|
| Require commit | Blocks stopping if uncommitted changes exist |
| Require push | Blocks stopping if unpushed commits exist |
| Require feature branch | Blocks stopping if on main/master branch |

### Available Commands

- `/stack-agents:setup` - Reconfigure plugin
- `/stack-agents:agents` - List all agents
- `/stack-agents:worktree` - Git worktree helper
- `/stack-agents:debug` - Systematic debugging guide
