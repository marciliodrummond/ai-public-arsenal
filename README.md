# AI Public Arsenal

Open-source skills, squads, and agents for AI coding assistants — installable via [skills.sh](https://skills.sh).

> Works with **Claude Code** · **Codex** · **Cursor** · **Gemini CLI** · **Antigravity** · **Windsurf** · **OpenCode**

## Quick Start

```bash
# Install the squads skill
npx skills add gutomec/ai-public-arsenal@squads
```

## Skills

Skills are AI agent instructions installed via `npx skills add`. They live in `skills/` and follow the [Agent Skills Spec](https://agentskills.io/specification).

| Skill | What it does |
|---|---|
| **[squads](skills/squads/)** | Creates, inspects, validates, and manages multi-agent squads — scaffolds agents, tasks, workflows, and config |

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
├── skills/           # Installable via skills.sh
│   └── squads/       # Squad manager skill (SKILL.md + references/)
├── squads/           # Squad definitions (managed by the squads skill)
│   ├── nirvana-squad-creator/
│   └── ultimate-landingpage/
└── README.md
```

## License

MIT
