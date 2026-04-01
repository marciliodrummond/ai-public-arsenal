# AI Public Arsenal

Open-source skills, squads, and agents for AI coding assistants — v3 Squad Manager with harness engineering, real validation, doom loop detection, and dual-path discovery.

**GitHub is the single source of truth.** All installation methods (npm, skills CLI, git) always pull from here.

> Works with **Claude Code** · **Codex** · **Cursor** · **Gemini CLI** · **Antigravity** · **Windsurf** · **OpenCode**

## Installation

### Recommended: Skills CLI
```bash
npx skills add @gutomec/ai-public-arsenal@squads
```

### Alternative: npm
```bash
# From npm registry (mirrors GitHub)
npm install @gutomec/ai-public-arsenal

# Direct from GitHub
npm install github:gutomec/ai-public-arsenal

# Clone repository
git clone https://github.com/gutomec/ai-public-arsenal.git
```

See [INSTALLATION.md](INSTALLATION.md) for detailed instructions and GitHub-first approach.

## What Are Squads?

Imagine you need to review a legal document. You'd want a lawyer to extract key clauses, an analyst to flag risks, a writer to summarize it, and someone to verify everything is correct. That's a squad — a team of AI agents working together, each handling what they do best.

A squad is reusable. You define it once in a configuration file, and then any project can use it. The squad handles coordination automatically: passing work between agents, checking quality, fixing problems when they happen.

### Inside a Squad

- **Agents** — specialized AI workers with different expertise
- **Tasks** — specific work assignments
- **Workflows** — how agents hand off work to each other
- **Configuration** — settings that keep everything running smoothly

### How Squads Stay Reliable

Squad Manager v3 ensures your teams work flawlessly:

- **Stuck detector** — If an agent keeps giving the same wrong answer, the system notices and tries a different approach
- **Smart retry** — When something fails, agents get a fresh attempt with just what they need
- **Real validation** — Every output is checked before moving to the next agent
- **Full visibility** — See exactly what each agent did, how long it took, and where problems occurred
- **Self-checking** — Agents verify their own work before passing it along

### The Power of Squads

You write the squad once. Then you use it everywhere. Need to review contracts? Create content? Analyze code? Check legal compliance? Build the squad once, run it forever.

The real magic: squads are deterministic and portable. You're not manually juggling AI agents for each project. You describe how your team should work, commit it to your codebase, and every tool that understands squads runs it the same way.

**Define once, run everywhere.**

## Skills

Skills are AI agent instructions installed via `npx skills add`. They live in `skills/` and follow the [Agent Skills Spec](https://agentskills.io/specification).

### Squad Manager v3

| Skill | Version | Features |
|---|---|---|
| **[squads](skills/squads/SKILL.md)** | 3.0.0 | Harness-engineered multi-agent teams with doom loop detection, Ralph loop retry, real validation (ajv), context compaction, filesystem collaboration, execution traces, reasoning sandwich model routing, DAG workflows, self-verify steps. Discovers squads from both `./squads/` and `~/squads/` |

**Key v3 Features:**
- ✅ **Doom Loop Detection** — Abort when output is identical N times
- ✅ **Ralph Loop Retry** — Fresh context retry with state persistence
- ✅ **Real Validation** — In-process ajv (no shell execution)
- ✅ **Context Compaction** — key-fields, truncate, summarize strategies
- ✅ **Filesystem Collaboration** — Artifacts in `.squad-state/{run-id}/artifacts/`
- ✅ **Execution Traces** — Step-level timing, I/O, retry events
- ✅ **Reasoning Sandwich** — Model routing: planning → implementation → verification
- ✅ **DAG Workflows** — Dependency-based execution (not just pipelines)
- ✅ **Self-Verify per Step** — Checklist + test commands
- ✅ **Dual-Path Discovery** — Squads from `./squads/` AND `~/squads/`

**100% Backwards Compatible** — v1 and v2 squads run without changes.

## Squads

Squads are self-contained multi-agent teams managed by the `squads` skill. They are directories with agents, tasks, and workflows.

| Squad | Agents | Description |
|---|---|---|
| **[nirvana-squad-creator](squads/nirvana-squad-creator/)** | 9 | Meta-squad that generates new squads from requirements |
| **[ultimate-landingpage](squads/ultimate-landingpage/)** | 9 | Full landing page pipeline — research, copy, design, build, review |

## Squad Lifecycle Triggers

Squads can emit lifecycle events that track execution in real time. Triggers are **opt-in** per squad via `squad.yaml`:

```yaml
triggers:
  enabled: true
  display: inline    # inline | log | both
  events:
    squad: true      # squad start/end
    agent: true      # agent start/end
    task: true       # task start/end
  flow:
    enabled: true    # flow tracking between agents
    live: true       # real-time transitions
    preview: true    # show planned flow before execution
    summary: true    # show summary after execution
```

### How It Works

Triggers are emitted as **stream markers** — structured HTML comments in the Claude output:

```
<!-- squad:event {"type":"squad-start","squad":"brandcraft","prefix":"bc","version":"1.0.0"} -->
<!-- squad:event {"type":"agent-start","squad":"brandcraft","agent":"bc-extractor","progress":"1/6"} -->
<!-- squad:event {"type":"flow-transition","squad":"brandcraft","from":"bc-extractor","to":"bc-inspector","handoff":"brand-assets.json"} -->
<!-- squad:event {"type":"flow-complete","squad":"brandcraft","totalDuration":"13m 45s","agentsExecuted":6} -->
```

**Universal compatibility:**
- Frontends that understand the format (like [squad-chat](https://github.com/gutomec/squad-chat)) parse them into rich visual surfaces — flow graphs, progress bars, agent status indicators
- Terminals and other frontends simply ignore them (they're HTML comments)
- No hooks, files on disk, or separate servers required

**Optional JSONL logging** — set `display: log` or `display: both` to also persist events to `.aios/squad-triggers/{squad}.jsonl` for offline analysis.

### Frontend Detection (Dual Mode)

Smart frontends can detect squad activity from two complementary sources:

| Source | How | What you get |
|---|---|---|
| **Stream markers** (primary) | Parse `<!-- squad:event {...} -->` from text | Rich data: squad name, version, agent icons, handoff artifacts, progress |
| **Tool call patterns** (inference) | Detect `Read squads/X/squad.yaml`, `Read agents/*.md` sequences | Coverage even when markers aren't emitted |

See [triggers-protocol.md](skills/squads/references/triggers-protocol.md) and [flow-tracker-protocol.md](skills/squads/references/flow-tracker-protocol.md) for full specs.

## Structure

```
ai-public-arsenal/
├── skills/                    # Installable skills
│   └── squads/
│       ├── SKILL.md           # Squad Manager v3 skill definition
│       └── references/        # 15+ protocol documentation files
│           ├── harness-protocol.md
│           ├── execution-engine.md
│           ├── schemas-protocol.md
│           └── ... (12 more)
├── squads/                    # Squad definitions (managed by squads skill)
│   ├── nirvana-squad-creator/  # Meta-squad that generates new squads
│   └── ultimate-landingpage/   # Full landing page pipeline
├── package.json               # npm configuration (points to GitHub)
├── INSTALLATION.md            # GitHub-first installation guide
├── .npminstallrc              # npm registry instructions
└── README.md                  # This file
```

## Distribution

**GitHub is the single source of truth** — all files, skills, and squads live here.

| Distribution Channel | Always Latest? | Purpose |
|---|---|---|
| **GitHub** (main branch) | ✅ Yes | Source of truth |
| **npm** (`@gutomec/ai-public-arsenal`) | ✅ Yes | Registry mirror |
| **skills.sh** | ✅ Yes | Skill marketplace |
| **Git clone** | ✅ Yes | Direct repository access |

All channels automatically stay in sync. GitHub is the only place changes happen.

## Commands

```bash
# List all squads in both ./squads/ and ~/squads/
*list-squads

# Create a new squad
*create-squad my-awesome-squad

# Run a squad workflow
*run-workflow nirvana-squad-creator main-pipeline

# For more commands
*help
```

## Version History

- **v3.0.0** (current) — Harness engineering, dual-path discovery, npm package
- **v2.0.0** — Validation gates, state checkpoints
- **v1.0.0** — Initial release

See [CHANGELOG.md](https://github.com/gutomec/ai-public-arsenal/releases) for detailed changes.

## Contributing

1. Fork or clone the repository
2. Make changes on a branch
3. Test locally
4. Push to GitHub
5. Create a pull request
6. After merge, bump version in `package.json` and push (npm publishes automatically)

All changes must happen on GitHub first — that's the source of truth.

## Resources

- 📖 [Squad Manager v3 Protocol](skills/squads/references/harness-protocol.md)
- 🔧 [Execution Engine](skills/squads/references/execution-engine.md)
- 📋 [Squad Creation Protocol](skills/squads/references/squad-creation-protocol.md)
- ✅ [Validation Checklist](skills/squads/references/validation-checklist.md)
- 🔀 [Workflow Patterns](skills/squads/references/workflow-patterns.md)
- 🚀 [Installation Guide](INSTALLATION.md)

## License

MIT — See [LICENSE](LICENSE) file for details
