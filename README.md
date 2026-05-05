# openclaw-nsmg Organization Configuration

Central configuration repository for the **openclaw-nsmg** GitHub organization.

## Claude Code Ecosystem

This repository provides organization-wide Claude Code configuration through two mechanisms:

### 1. Reusable Workflows (GitHub Actions Inheritance)

Child repositories call the centrally-managed reusable workflows instead of duplicating plugin lists:

```yaml
# In your repo's .github/workflows/claude.yml
name: Claude Code
on:
  issue_comment:
    types: [created]
  issues:
    types: [opened, assigned]
jobs:
  claude:
    if: |
      (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')) ||
      (github.event_name == 'issues' && github.event.action == 'assigned' && github.event.assignee.login == 'claude')
    uses: openclaw-nsmg/.github/.github/workflows/claude-base.yml@main
    secrets: inherit
```

```yaml
# In your repo's .github/workflows/claude-review.yml
name: Claude Code Review
on:
  pull_request:
    types: [opened, synchronize]
jobs:
  review:
    uses: openclaw-nsmg/.github/.github/workflows/claude-review.yml@main
    secrets: inherit
```

### 2. Project-level Settings (Local CLI Inheritance)

Each repository should include a `.claude/settings.json` that references the official marketplace. The organization-level `enabledPlugins` in this repo's `.claude/settings.json` serves as the canonical reference for which plugins are approved.

For local CLI usage, each project's `.claude/settings.json` should declare:

```json
{
  "extraKnownMarketplaces": {
    "claude-plugins-official": {
      "source": {
        "source": "github",
        "repo": "anthropics/claude-plugins-official"
      }
    }
  },
  "enabledPlugins": {
    "code-review@claude-plugins-official": true
  }
}
```

## Enabled Plugins (32 of 34)

All official plugins are enabled except `example-plugin` (demo only) and `math-olympiad` (competition math, not relevant).

| Category | Plugins |
|----------|---------|
| Code Quality | `code-review`, `code-simplifier`, `code-modernization` |
| Development Workflow | `feature-dev`, `commit-commands`, `pr-review-toolkit` |
| Security | `security-guidance` |
| Configuration | `claude-code-setup`, `claude-md-management`, `hookify` |
| Knowledge | `session-report`, `skill-creator` |
| Output Style | `explanatory-output-style`, `learning-output-style` |
| Frontend | `frontend-design`, `playground` |
| Language Servers | `typescript-lsp`, `pyright-lsp`, `gopls-lsp`, `rust-analyzer-lsp`, `clangd-lsp`, `jdtls-lsp`, `kotlin-lsp`, `ruby-lsp`, `php-lsp`, `swift-lsp`, `csharp-lsp`, `lua-lsp` |
| Advanced | `ralph-loop`, `mcp-server-dev`, `agent-sdk-dev`, `plugin-dev` |

### 3. Quality Gate Reusable Workflows

Centralized CI quality gates that all repos can call:

```yaml
# In your repo's .github/workflows/vale.yml
name: Vale
on:
  pull_request:
    paths: ['src/**/*.md', 'src/**/*.mdx', 'src/**/*.astro']
jobs:
  vale:
    uses: openclaw-nsmg/.github/.github/workflows/reusable-vale.yml@main
```

```yaml
# In your repo's .github/workflows/i18n-lint.yml
name: i18n
on:
  pull_request:
    paths: ['src/**/*.tsx', 'src/**/*.ts']
jobs:
  i18n:
    uses: openclaw-nsmg/.github/.github/workflows/reusable-i18n-lint.yml@main
```

```yaml
# In your repo's .github/workflows/accessibility.yml
name: Accessibility
on:
  pull_request:
jobs:
  a11y:
    uses: openclaw-nsmg/.github/.github/workflows/reusable-accessibility.yml@main
    with:
      base_url: 'http://localhost:4321'
```

```yaml
# In your repo's .github/workflows/lighthouse.yml
name: Lighthouse
on:
  pull_request:
jobs:
  perf:
    uses: openclaw-nsmg/.github/.github/workflows/reusable-lighthouse.yml@main
```

```yaml
# In your repo's .github/workflows/link-check.yml
name: Link Check
on:
  pull_request:
    paths: ['**.md', '**.html']
jobs:
  links:
    uses: openclaw-nsmg/.github/.github/workflows/reusable-link-check.yml@main
```

```yaml
# In your repo's .github/workflows/security-review.yml
name: Security Review
on:
  pull_request:
jobs:
  security:
    uses: openclaw-nsmg/.github/.github/workflows/reusable-security-review.yml@main
    permissions:
      security-events: write
```

## File Structure

```
.github/
└── workflows/
    ├── routine-bug-triage.yml           # Routine: auto-triage new issues
    ├── routine-changelog.yml            # Routine: auto-generate changelogs
    ├── routine-pr-reviewer.yml          # Routine: auto PR review
    ├── routine-release-notes.yml        # Routine: auto release notes
    ├── reusable-vale.yml                # Reusable: prose linting (errata-ai/vale)
    ├── reusable-i18n-lint.yml           # Reusable: i18n hardcode detection
    ├── reusable-accessibility.yml       # Reusable: axe-core via pa11y-ci (REPORT-110)
    ├── reusable-lighthouse.yml          # Reusable: performance budget (REPORT-110)
    ├── reusable-link-check.yml          # Reusable: broken link detection (lychee)
    └── reusable-security-review.yml     # Reusable: CodeQL + Trivy (REPORT-098)
.claude/
└── settings.json                        # Canonical plugin list (reference for all repos)
README.md                                # This file
```

## Adding a New Plugin

1. Add to `.claude/settings.json` `enabledPlugins`
2. Add to `.github/workflows/claude-base.yml` `plugins` list
3. If review-relevant, also add to `claude-review.yml`
4. Commit and push — all child repos inherit automatically
