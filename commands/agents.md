---
name: agents
description: List all available crispy-doodle and their purposes
---

# Crispy Doodle Reference

## Analysis Agents (haiku - cost-optimized)

| Agent | Triggers On | Purpose |
|-------|-------------|---------|
| `accessibility-auditor` | WCAG, a11y, screen reader | Accessibility compliance |
| `api-designer` | API design, OpenAPI, REST | API architecture |
| `architecture-agent` | system design, service boundaries | Architecture decisions |
| `code-reviewer` | PR review, code audit | Code quality analysis |
| `compliance-advisor` | GDPR, privacy, audit | Compliance guidance |
| `performance-analyst` | profiling, caching, optimization | Performance analysis |
| `ux-consultant` | UX, usability, user experience | UX recommendations |

## Implementation Agents (opus - full capability)

| Agent | Triggers On | Purpose |
|-------|-------------|---------|
| `frontend-agent` | React, Vite, component, hook | React/TypeScript frontend |
| `backend-nodejs` | Fastify, Node.js, route | Node.js backend services |
| `python-backend` | FastAPI, Pydantic, Python | Python backend services |
| `bff-agent` | BFF, aggregation, session proxy | Backend-for-Frontend |
| `postgres-engineer` | PostgreSQL, Drizzle, SQL, schema | Database operations |
| `firestore-data` | Firestore, collection, document | NoSQL data access |
| `auth-agent` | JWT, session, RBAC, Casbin | Authentication/authorization |
| `testing-agent` | test, pytest, Vitest, mock | Test creation |
| `docker-agent` | Docker, nginx, container | Containerization |
| `gcp-infra` | Terraform, Cloud Run, GCP | Infrastructure |
| `events-agent` | Pub/Sub, event, message | Event-driven architecture |
| `security-agent` | OWASP, XSS, injection | Security implementation |
| `i18n-agent` | translation, locale, i18n | Internationalization |
| `migrations-agent` | migration, seed, schema change | Database migrations |
| `ai-integrator` | LLM, Claude API, embeddings | AI integration |
| `search-engineer` | vector search, Qdrant, full-text | Search functionality |
| `billing-engineer` | Stripe, subscription, payment | Payment processing |
| `queue-worker` | BullMQ, background job, worker | Job processing |
| `realtime-engineer` | WebSocket, live updates | Real-time features |
| `storage-engineer` | S3, MinIO, file upload | Object storage |
| `email-engineer` | IMAP, SMTP, email sync | Email integration |
| `documentation-writer` | docs, README, API docs | Documentation |

## Usage

Agents are triggered automatically based on your request. You can also explicitly request:

> "Use the frontend-agent to create a modal component"

## Commands

- `/crispy-doodle:setup` - Configure agent delegation
- `/crispy-doodle:worktree` - Git worktree helper
- `/crispy-doodle:debug` - Systematic debugging guide
- `/crispy-doodle:agents` - This reference
