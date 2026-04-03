# Installation Guide

This repository uses **GitHub as the single source of truth**. All distribution channels pull from here.

## Quick Start

### Skills CLI (Recommended)

```bash
npx skills add gutomec/ai-public-arsenal@squads
```

### npm

```bash
# From npm registry (synced from GitHub)
npm install @gutomec/ai-public-arsenal

# Direct from GitHub
npm install github:gutomec/ai-public-arsenal
```

### Git Clone

```bash
git clone https://github.com/gutomec/ai-public-arsenal.git
cd ai-public-arsenal/skills/squads
```

## What Gets Installed

The `squads` skill — a protocol-driven engine for managing multi-agent teams. It includes:

```
skills/squads/
├── SKILL.md              # Skill definition (entry point)
├── SQUAD_PROTOCOL.md     # Protocol specification
├── references/           # 10 protocol docs (discovery, creation, validation, ...)
├── schemas/              # JSON Schema (agent, squad, task)
├── templates/            # Scaffolding templates (agent, squad, task, workflow)
├── scripts/              # Shell utilities (activate, validate)
└── lib/                  # JS helpers (discovery, display)
```

## All Installation Methods

| Method | Command | Always Latest? |
|---|---|---|
| **Skills CLI** | `npx skills add gutomec/ai-public-arsenal@squads` | ✅ |
| **npm registry** | `npm install @gutomec/ai-public-arsenal` | ✅ |
| **GitHub direct** | `npm install github:gutomec/ai-public-arsenal` | ✅ |
| **Git clone** | `git clone https://github.com/gutomec/ai-public-arsenal.git` | ✅ |

## Updating

```bash
# Skills CLI
npx skills add gutomec/ai-public-arsenal@squads

# npm
npm update @gutomec/ai-public-arsenal
```

## Verification

```bash
# Check installed version
npm list @gutomec/ai-public-arsenal

# Verify it points to GitHub
npm view @gutomec/ai-public-arsenal repository.url
# Expected: git+https://github.com/gutomec/ai-public-arsenal.git
```

## Support

- [GitHub Issues](https://github.com/gutomec/ai-public-arsenal/issues)
- [Repository](https://github.com/gutomec/ai-public-arsenal)
