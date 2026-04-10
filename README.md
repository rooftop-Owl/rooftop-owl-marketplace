# rooftop-Owl Marketplace

Personal plugin registry by [rooftop-Owl](https://github.com/rooftop-Owl) — a single source of truth for Claude Code plugins across orchestration, research, presentation, and tooling domains.

## Why this exists

Before rooftop-Owl, every plugin shipped with its own `marketplace.json` — 7+ separate single-plugin marketplaces that consumers had to add one by one. This repo consolidates them all so you can:

- **Add once** — `/plugin marketplace add rooftop-Owl <url>`
- **Install à la carte** — `/plugin install astraea`, `/plugin install slideaway`, etc.
- **Enable/disable wholesale** — toggle the entire registry on or off via Claude Code's marketplace controls
- **Pin reproducibly** — every plugin is SHA-pinned; upgrades are deliberate, not automatic

## Current plugins

| Plugin | Category | What it does |
|---|---|---|
| [astraea](https://github.com/rooftop-Owl/astraea) | developer-tools | Orchestration kernel — multi-agent delegation, institutional memory, governance rules |
| [skill-factory](https://github.com/rooftop-Owl/skill-factory) | developer-tools | Portable skill lifecycle manager for Claude Code |
| [aeolus-agents](https://github.com/rooftop-Owl/aeolus-agents) | scientific-research | **Bundle** — installs the full atmospheric-science stack in one step |
| [dart-agents](https://github.com/rooftop-Owl/dart-agents) | scientific-research | DART ensemble data assimilation + Fortran development |
| [climada-agents](https://github.com/rooftop-Owl/climada-agents) | scientific-research | CLIMADA climate risk workflows |
| [wrf-chem-agents](https://github.com/rooftop-Owl/wrf-chem-agents) | scientific-research | WRF-Chem chemistry transport simulation |
| [research-zotero](https://github.com/rooftop-Owl/research-zotero) | scientific-research | Federated paper search + Zotero library management |
| [slideaway](https://github.com/rooftop-Owl/slideaway) | design | Multi-engine presentation generator (PPTX, HTML, Beamer, Marp) |
| [notion-ao-research](https://github.com/rooftop-Owl/notion-ao-research) | productivity | Natural-language Notion workflows for research operations |

## Installation

```bash
# Add this marketplace to Claude Code
/plugin marketplace add rooftop-Owl https://github.com/rooftop-Owl/rooftop-owl-marketplace.git

# Install any plugin by name
/plugin install astraea
/plugin install slideaway
/plugin install aeolus-agents   # installs the full science stack
```

## Structure

```
rooftop-owl-marketplace/
├── .claude-plugin/
│   └── marketplace.json   # the registry (single source of truth)
└── README.md              # this file
```

The registry contains **only references** — no plugin code lives here. Each plugin has its own repository with its own `plugin.json` and release cadence. This marketplace is a directory, not a monorepo.

## Governance

- **SHA pinning**: every plugin entry pins a specific commit SHA for reproducibility. Updates are deliberate edits to `marketplace.json`, never automatic.
- **Upstream trust**: the author (rooftop-Owl) maintains every listed plugin personally. This is a curated registry, not a community marketplace.
- **License diversity**: per-plugin licenses are preserved as-is. Consumers should check each plugin's repository for its license before use.
- **Meta-bundle caveat**: `aeolus-agents` is a thematic bundle that internally references `dart-agents`, `climada-agents`, and `wrf-chem-agents`. Installing aeolus-agents pulls those transitively. The individual science plugins are ALSO listed as top-level entries for consumers who want them separately. Slight duplication is accepted for ergonomic convenience.

## Updating a plugin's pinned SHA

```bash
# Get the current HEAD of the plugin
cd ~/path/to/plugin
git rev-parse HEAD

# Edit marketplace.json to update the sha field
# Commit the change
git commit -am "chore: bump <plugin-name> to <new-sha>"
```

Never trust remembered SHAs — always verify in the current session.

## Not included

- **astraeus** (the runtime at `~/.astraeus/`) is intentionally excluded. astraeus is the parent environment that rooftop-Owl and its plugins run inside, not a plugin itself. Shipping it through its own child marketplace would create a bootstrap loop.
- **Experimental plugins** — anything not production-ready lives outside this registry until it stabilizes.

## History

Created 2026-04-10 as part of the Ephemeris plan — consolidating 7 per-plugin marketplaces into a single registry so future plugins (starting with Ephemeris itself) can join a well-defined namespace rather than spawn yet another one.
