# shomod-777

A Claude Code plugin marketplace with specialized agents for modern full-stack development.

## Installation

```bash
claude plugins:add /path/to/shomod-777
```

Or add the git repository:

```bash
claude plugins:add https://github.com/shomod-777/shomod-777
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

## Acknowledgements

This plugin was inspired by and builds upon ideas from:

- [oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) by Yeachan Heo
- [superpowers](https://github.com/obra/superpowers) by Jesse Vincent

## License

MIT
