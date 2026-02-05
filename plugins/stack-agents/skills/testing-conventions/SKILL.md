---
name: testing-conventions
description: Testing conventions and patterns for this codebase
user-invocable: false
---

# Testing Conventions

## Unit Testing Stack
- **Python**: pytest with fixtures, mock, parametrize
- **TypeScript/Node.js**: Vitest with React Testing Library
- **BFF/Fastify**: Fastify inject for route testing

## Test Structure
- One test file per source file: `foo.ts` → `foo.test.ts` or `foo.py` → `test_foo.py`
- Group tests by behavior, not implementation
- Use descriptive test names: "should [behavior] when [condition]"

## Acceptance Criteria Format
```
Given [precondition]
When [action]
Then [expected result]
```

## Test Commands
- Python: `pytest` or `uv run pytest`
- Node.js: `pnpm test` or `make test`
- Frontend: `pnpm --filter <workspace> test`

## After Implementation
ALWAYS delegate to testing-agent for test coverage.
