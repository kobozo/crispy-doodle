# Yannick Agents

A Claude Code plugin marketplace with specialized agents for modern full-stack development.

## Installation

```bash
claude plugins:add /path/to/yannick-agents
```

Or add the git repository:

```bash
claude plugins:add https://github.com/yannick/yannick-agents
```

## Plugins

### stack-agents

29 specialized agents optimized for full-stack development with:

- **Model Tiering**: Analysis agents use `haiku` (cost-optimized), implementation agents use `opus`
- **Test Enforcement**: SubagentStop hooks verify tests before completion
- **Project-Specific Knowledge**: Agents know your stack patterns and conventions

See [stack-agents README](./plugins/stack-agents/README.md) for details.

## Philosophy

> Your agents should know YOUR codebase, not just generic patterns.

This plugin provides deep integration with specific tech stacks rather than generic methodology. Each agent understands:

- WHERE files go in your monorepo
- WHAT patterns to use (contexts, hooks, repositories)
- HOW your stack works together

## License

MIT
