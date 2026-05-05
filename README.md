# AI Toolkit

A collection of Claude Code plugins to enhance your AI-assisted development workflow.

---

## Plugins

### Decision Driven Development (DDD)

DDD is a four-phase workflow that front-loads all ambiguity resolution before a single line of code is written. Each phase produces a document that becomes the input for the next, giving you a clear audit trail from business intent to working software.

```
/ddd_req  →  /ddd_des  →  /ddd_cl  →  /ddd_imp
```

| Phase | Command | Purpose |
|-------|---------|---------|
| Requirements | `/ddd_req` | Capture all business and technical requirements via structured Q&A |
| Design | `/ddd_des` | Translate requirements into a concrete design with diagrams |
| Checklist | `/ddd_cl` | Decompose the design into dependency-ordered, implementable tasks |
| Implement | `/ddd_imp` | Execute one task at a time, pausing for approval between each |

See [`plugins/ddd/README.md`](plugins/ddd/README.md) for full phase documentation.

---

## Installation

### 1. Add the marketplace

Add this repository as a marketplace source in your Claude Code settings.

**Via `.claude/settings.json`:**

```json
{
  "extraKnownMarketplaces": {
    "ai-toolkit": {
      "source": {
        "source": "github",
        "repo": "DmytroPaliichuk/ai-toolkit"
      }
    }
  }
}
```

**Or via the Claude Code CLI:**

```bash
/plugin marketplace add https://github.com/DmytroPaliichuk/ai-toolkit
```

### 2. Install the DDD plugin

Once the marketplace is added, install the plugin from within Claude Code:

```bash
/plugin install ddd
```

Alternatively, install directly from the local path (useful during development):

```bash
claude --plugin-dir ./plugins/ddd
```

### 3. Activate

After installation, reload plugins to activate the new skills:

```bash
/reload-plugins
```

The `/ddd_req`, `/ddd_des`, `/ddd_cl`, and `/ddd_imp` commands will then be available in your Claude Code session.
