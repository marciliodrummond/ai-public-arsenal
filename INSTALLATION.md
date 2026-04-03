# Installation Guide

This repository uses **GitHub as the single source of truth**. All distribution channels pull from here.

## Quick Start

### Skills CLI (Recommended)

Install skills individually:

```bash
# Squad management engine
npx skills add gutomec/ai-public-arsenal@squads

# Autonomous agile lifecycle
npx skills add gutomec/ai-public-arsenal@aiox-autopilot
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

Each skill is self-contained:

**`squads`** — protocol-driven engine for managing multi-agent teams:

| Path | What |
|---|---|
| `SKILL.md` + `SQUAD_PROTOCOL.md` | Skill + protocol spec |
| `references/` | 10 protocol docs |
| `schemas/` + `templates/` + `scripts/` + `lib/` | Schemas, scaffolding, utilities |

**`aiox-autopilot`** — autonomous agile lifecycle (idea → deploy):

| Path | What |
|---|---|
| `SKILL.md` | Full pipeline instructions |
| `templates/` | AIOX document templates (PRD, Architecture, Story, Frontend Spec) |
| `checklists/` | Quality gates (PO master, story draft, DoD, security) |
| `schemas/` | 9 JSON schemas for phase outputs |
| `config/` | Coding standards, tech stack defaults |

## All Installation Methods

| Method | Command | Always Latest? |
|---|---|---|
| **Skills CLI** | `npx skills add gutomec/ai-public-arsenal@squads` | ✅ |
| **Skills CLI** | `npx skills add gutomec/ai-public-arsenal@aiox-autopilot` | ✅ |
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
