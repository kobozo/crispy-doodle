# Stack Agents

29 specialized Claude Code agents for full-stack development with model tiering and test enforcement.

## Tech Stack Coverage

| Layer | Technologies | Agents |
|-------|-------------|--------|
| Frontend | React, Vite, TypeScript | frontend-agent |
| BFF | Fastify, Node.js | bff-agent, backend-nodejs |
| Backend | FastAPI, Pydantic | python-backend |
| Database | PostgreSQL, Drizzle ORM | postgres-engineer |
| NoSQL | Firestore | firestore-data |
| Auth | JWT, Casbin RBAC | auth-agent |
| Infra | GCP, Terraform, Docker | gcp-infra, docker-agent |
| Events | Pub/Sub | events-agent |
| Testing | pytest, Vitest | testing-agent |

## Model Tiering

Agents are optimized for cost and capability:

### Analysis Agents (haiku)
Fast, cost-effective for read-only tasks:
- `code-reviewer` - PR reviews, code audits
- `accessibility-auditor` - WCAG compliance
- `api-designer` - REST API design
- `architecture-agent` - System design
- `compliance-advisor` - GDPR, data privacy
- `performance-analyst` - Profiling, optimization
- `ux-consultant` - UX reviews

### Implementation Agents (opus)
Full capability for code changes:
- `frontend-agent` - React/Vite components
- `backend-nodejs` - Fastify routes
- `python-backend` - FastAPI endpoints
- `postgres-engineer` - Database schemas
- `testing-agent` - Test creation
- And 15+ more...

## Test Enforcement

Implementation agents include SubagentStop hooks that verify:
1. Tests were written or delegated to testing-agent
2. Test files exist for new code

This prevents agents from completing without test coverage.

## Skills

| Skill | Description |
|-------|-------------|
| `git-worktree` | Parallel development with git worktrees |
| `systematic-debug` | 4-phase root cause analysis methodology |
| `testing-conventions` | Project testing patterns and conventions |

## Agent List

### Analysis (haiku)
| Agent | Purpose |
|-------|---------|
| accessibility-auditor | WCAG compliance, a11y audits |
| api-designer | REST API design, OpenAPI specs |
| architecture-agent | System design, service boundaries |
| code-reviewer | PR reviews, code quality |
| compliance-advisor | GDPR, data privacy |
| performance-analyst | Profiling, caching |
| ux-consultant | UX reviews, usability |

### Implementation (opus)
| Agent | Purpose |
|-------|---------|
| ai-integrator | LLM APIs, prompt engineering |
| auth-agent | JWT, sessions, Casbin RBAC |
| backend-nodejs | Fastify routes, Node.js |
| bff-agent | Backend-for-Frontend |
| billing-engineer | Stripe, subscriptions |
| docker-agent | Docker Compose, nginx |
| documentation-writer | API docs, READMEs |
| email-engineer | IMAP/SMTP, email sync |
| events-agent | Pub/Sub, event-driven |
| firestore-data | Firestore repositories |
| frontend-agent | React/Vite, hooks |
| gcp-infra | Terraform, Cloud Run |
| i18n-agent | Translations, localization |
| migrations-agent | Database migrations |
| postgres-engineer | PostgreSQL, Drizzle ORM |
| python-backend | FastAPI, Pydantic |
| queue-worker | BullMQ, background jobs |
| realtime-engineer | WebSockets, live updates |
| search-engineer | Vector search, full-text |
| security-agent | OWASP, vulnerability assessment |
| storage-engineer | MinIO/S3, file uploads |
| testing-agent | pytest, Vitest, E2E |

## Customization

### Adapting to Your Stack

1. Fork this repository
2. Modify agent system prompts in `agents/*.md`
3. Update project structure references
4. Add your own patterns and conventions

### Adding New Agents

Create `agents/my-agent.md`:

```yaml
---
name: my-agent
description: Description for auto-triggering
model: opus  # or haiku for analysis-only
tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Edit
  - Write
---

# Agent System Prompt

Your expertise and guidelines here...
```

### Adding SubagentStop Hooks

For test enforcement:

```yaml
hooks:
  SubagentStop:
    - type: prompt
      prompt: |
        Verify tests were written.
        Return {"ok": true} if covered.
        Return {"ok": false, "reason": "..."} if missing.
      timeout: 30
```

## License

MIT
