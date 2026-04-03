# AI Public Arsenal

Open-source skills for AI coding assistants. Install individually — each skill is a self-contained capability you add to your agent.

[![npm version](https://img.shields.io/npm/v/@gutomec/ai-public-arsenal)](https://npmjs.com/package/@gutomec/ai-public-arsenal)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

> Works with **Claude Code** · **Codex** · **Gemini CLI** · **Cursor** · **Antigravity** · **Windsurf** · **OpenCode**

## Skills

| Skill | Version | What it does |
|---|---|---|
| **[squads](#squads)** | 4.0.0 | Manage multi-agent teams — create, validate, execute, and orchestrate squads |
| **[aiox-autopilot](#aiox-autopilot)** | 2.0.0 | Autonomous agile lifecycle — idea to deployed project, zero human intervention |

### Install a skill

```bash
# Install one skill at a time
npx skills add gutomec/ai-public-arsenal@squads
npx skills add gutomec/ai-public-arsenal@aiox-autopilot
```

---

## squads

**Protocol-driven multi-agent squad management with harness engineering.**

A squad is a team of AI agents that work together. You define agents, tasks, and workflows in YAML — then run the whole thing with one command. The `squads` skill handles the full lifecycle: discovery, creation, validation, activation, execution, and upgrade.

### Quick start

```bash
npx skills add gutomec/ai-public-arsenal@squads
```

Then in your AI assistant:

```bash
*list-squads                              # Find squads in ./squads/ and ~/squads/
*create-squad my-squad                    # Scaffold a new squad
*validate-squad my-squad                  # Check integrity before running
*run-workflow my-squad main-pipeline      # Execute a workflow
```

### What's inside

| Path | What | Files |
|---|---|---|
| [`SKILL.md`](skills/squads/SKILL.md) | Skill entry point | 1 |
| [`SQUAD_PROTOCOL.md`](skills/squads/SQUAD_PROTOCOL.md) | Protocol specification (source of truth) | 1 |
| [`references/`](skills/squads/references/) | Protocol docs (discovery → context-engineering) | 10 |
| [`schemas/`](skills/squads/schemas/) | JSON Schema for agent, squad, task | 3 |
| [`templates/`](skills/squads/templates/) | Scaffolding for new squads | 4 |
| [`scripts/`](skills/squads/scripts/) | Shell utilities (activate, validate) | 2 |
| [`lib/`](skills/squads/lib/) | JS helpers (discovery, display) | 2 |

### Runtime protections

- **Doom loop detection** — stops agents that keep producing identical output
- **Ralph loop retry** — retries with fresh context instead of accumulated noise
- **Real validation** — checks outputs against JSON Schema before passing to the next agent
- **Context compaction** — trims handoff data to prevent context window overflow
- **Filesystem collaboration** — large artifacts go to disk instead of through context
- **Self-verify** — agents check their own work before handing off
- **DAG workflows** — dependency-based execution, not just sequential pipelines
- **Execution traces** — records timing, I/O, retries, and failures per step

Backward compatible — v1 and v2 squads run without changes.

---

## aiox-autopilot

**Autonomous agile lifecycle — from idea to deployed project.**

Based on the [AIOX Core](https://github.com/SynkraAI/aiox-core) Agentic Agile methodology. Takes a raw project idea and runs through 5 phases autonomously: researches the market, writes the PRD, designs the architecture, creates stories, implements code, reviews quality, and deploys. Supports both greenfield and brownfield projects.

### Quick start

```bash
npx skills add gutomec/ai-public-arsenal@aiox-autopilot
```

Then in your AI assistant:

```bash
# Full cycle — idea to deploy
*autopilot "Marketplace de freelancers para devs"

# Planning only — idea to stories
*plan "App de finanças pessoais"

# Dev cycle only — stories to deploy
*build

# Check progress
*status
```

### The 5 phases

```
PHASE 0: DISCOVERY   → Web search for competitors, trending stacks, feasibility
                       Brownfield: + codebase analysis
PHASE 1: PLANNING    → PRD + Architecture + Frontend Spec (via AIOX templates)
         GATE        → PO Master Checklist + cross-reference validation
PHASE 2: SHARDING    → Stories with Given/When/Then, organized in parallel waves
                       Each story validated with Story Draft Checklist (score ≥ 8.0)
PHASE 3: DEV LOOP    → Builder implements → Guardian reviews → fix or next
                       Course correction on failure patterns
PHASE 4: DELIVERY    → Integration + tests + deploy + release notes + retrospective
```

### What's inside

| Path | What | Files |
|---|---|---|
| [`SKILL.md`](skills/aiox-autopilot/SKILL.md) | Skill entry point (full pipeline instructions) | 1 |
| [`templates/`](skills/aiox-autopilot/templates/) | AIOX document templates (PRD, Architecture, Story, Frontend Spec) | 4 |
| [`checklists/`](skills/aiox-autopilot/checklists/) | Quality gates (PO master, story draft, DoD, security) | 4 |
| [`schemas/`](skills/aiox-autopilot/schemas/) | JSON Schema for every phase output | 9 |
| [`config/`](skills/aiox-autopilot/config/) | Coding standards, tech stack defaults | 2 |

### How it decides

The skill acts autonomously at every decision point:

- **Stack selection** — researches current alternatives via web search, picks based on data
- **Scope** — classifies MVP vs full, applies MoSCoW prioritization
- **Quality gates** — runs checklists at each phase boundary, fails and retries if score < 8.0
- **Course correction** — if 2+ stories fail QA in the same wave, stops and reassesses
- **Brownfield detection** — checks for existing `package.json`/`src/` and adapts the pipeline

### Greenfield vs brownfield

| Aspect | Greenfield | Brownfield |
|---|---|---|
| Detection | No existing code | `package.json`, `src/` found |
| Discovery | Market research + stack selection | + Codebase analysis (stack, patterns, debt) |
| Planning | Full PRD + Architecture from scratch | Respects existing patterns, maps affected areas |
| Stories | Files to **create** | Files to **create** + files to **modify** |
| Builder | Follows Architecture source tree | Follows existing code style + Architecture |

---

## Installation

```bash
# Individual skills (recommended)
npx skills add gutomec/ai-public-arsenal@squads
npx skills add gutomec/ai-public-arsenal@aiox-autopilot

# npm (all skills)
npm install @gutomec/ai-public-arsenal

# Direct from GitHub
npm install github:gutomec/ai-public-arsenal

# Git clone
git clone https://github.com/gutomec/ai-public-arsenal.git
```

See [INSTALLATION.md](INSTALLATION.md) for details.

## Distribution

GitHub is the single source of truth. All channels sync automatically.

| Channel | Command |
|---|---|
| **Skills CLI** | `npx skills add gutomec/ai-public-arsenal@<skill>` |
| **npm** | `npm install @gutomec/ai-public-arsenal` |
| **Git clone** | `git clone https://github.com/gutomec/ai-public-arsenal.git` |

## License

MIT — see [LICENSE](LICENSE) for details.
