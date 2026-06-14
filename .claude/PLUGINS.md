# Claude Code virtual dev team — plugin setup

This repo is configured with a set of Claude Code skills/plugins that form a
"virtual development team": plan-before-coding, polished frontend output,
multi-agent review, security scanning, and cross-session memory.

## How it's wired up

Two mechanisms are used:

1. **Vendored skills** — committed directly under `.claude/skills/`. These are
   active immediately, no install step.
2. **Marketplace plugins** — declared in `.claude/settings.json` via
   `extraKnownMarketplaces` + `enabledPlugins`. When you open this repo in
   Claude Code and **trust the folder**, Claude Code prompts you to install
   them, then run `/reload-plugins` (or restart) to activate.

## The six tools

| # | Tool | How it's installed here | Status |
|---|------|-------------------------|--------|
| 1 | **Superpowers** (`obra/superpowers`) — plan-before-code, TDD, debugging, review workflows | Vendored into `.claude/skills/` (14 skills) | ✅ Active now |
| 2 | **Frontend Design** (`frontend-design@claude-plugins-official`) — production-grade UI | `enabledPlugins` in settings | ⏳ Installs on folder trust |
| 3 | **Code Review** (`code-review@claude-plugins-official`) — multiple specialized review agents with confidence scoring | `enabledPlugins` in settings | ⏳ Installs on folder trust |
| 4 | **Security Guidance** (`security-guidance@claude-plugins-official`) — reviews each change for vulnerabilities | `enabledPlugins` in settings | ⏳ Installs on folder trust |
| 5 | **Claude-mem** (`thedotmack/claude-mem`) — cross-session memory | `extraKnownMarketplaces` + `enabledPlugins` | ⏳ See note below |
| 6 | **GStack** (`garrytan/gstack`) — 23 opinionated CEO/Designer/Eng-Manager/QA roles | Manual (see below) | ⚠️ Manual step required |

## Notes / caveats

### Claude-mem (#5)
The plugin wires up SessionStart/Stop hooks, but the actual memory worker is a
runtime service. For full functionality also run once per machine:

```bash
npx claude-mem install
```

(`npm install -g claude-mem` installs the library only — it does NOT register
the hooks/worker.)

### GStack (#6)
GStack is **not** marketplace-installable: it ships as a plugin backed by a
git submodule and a Bun-based `./setup` step (browser automation, etc.), so it
cannot be vendored as plain repo files. Install it per-machine:

```bash
# Original setup (Garry Tan's):
git clone --single-branch --depth 1 https://github.com/garrytan/gstack.git \
  ~/.claude/skills/gstack && cd ~/.claude/skills/gstack && ./setup

# Or the plugin wrapper:
#   git clone --recurse-submodules https://github.com/Ahacad/gstack.git
#   claude --plugin-dir ./gstack
```

Requires Bun. See https://github.com/garrytan/gstack for details.

## Sources
- Superpowers: https://github.com/obra/superpowers
- Frontend Design / Code Review / Security Guidance: https://github.com/anthropics/claude-plugins-official
- Claude-mem: https://github.com/thedotmack/claude-mem
- GStack: https://github.com/garrytan/gstack
