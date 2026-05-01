# Organization Workflows

All Claude Code workflows in this organization now use official Anthropic templates directly:

| Workflow | Source |
|----------|--------|
| `claude.yml` | [anthropics/claude-code-action/examples/claude.yml](https://github.com/anthropics/claude-code-action/tree/main/examples) |
| `claude-review.yml` | [anthropics/claude-code-action/examples/pr-review-comprehensive.yml](https://github.com/anthropics/claude-code-action/tree/main/examples) |
| `auto-fix-ci.yml` | [anthropics/claude-code-action/examples/ci-failure-auto-fix.yml](https://github.com/anthropics/claude-code-action/tree/main/examples) |
| `security-review.yml` | [anthropics/claude-code-security-review](https://github.com/anthropics/claude-code-security-review) |

## Skills

Install via Plugin marketplace in Claude Code:
```
/plugin marketplace add anthropics/skills
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

Source: [anthropics/skills](https://github.com/anthropics/skills) (127k stars)
