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

Squads are self-contained multi-agent teams managed by the `squads` skill. They are **not** installed via skills.sh — they are directories with agents, tasks, and workflows.

| Squad | Agents | Description |
|---|---|---|
| **[nirvana-squad-creator](squads/nirvana-squad-creator/)** | 9 | Meta-squad that generates new squads from requirements |
| **[ultimate-landingpage](squads/ultimate-landingpage/)** | 9 | Full landing page pipeline — research, copy, design, build, review |

## Squad Flow Tracker

Two modes to visualize squad execution:

### Demo (Static — GitHub Pages)

Zero-setup interactive replay with embedded scenarios. No server needed.

**[View Live Demo](https://gutomec.github.io/ai-public-arsenal/demo/)** · [Source](demo/)

- Embedded replay of recorded squad executions
- BFS-level graph layout with parallel team visualization
- Includes: **sales-funnel-masters** (21 agents, hub-and-spoke) and **nirvana-squad-creator** (8 agents, sequential pipeline)
- Works directly from `file://` or GitHub Pages

### Dashboard (Live — SSE Server)

Real-time dashboard that connects to your running project, monitoring agents, squads, tasks, and everything being executed as it happens.

```bash
cd demo
node server.js
# → http://localhost:3001
```

- Reads `.jsonl` scenario files from `demo/scenarios/` and replays via SSE
- Auto-detects new recordings from `.aios/squad-triggers/`
- Live event stream with real-time graph updates
- Speed control: 1x, 2x, 5x, 10x, or instant

**Recording your own executions:**

1. Enable triggers in your squad's `squad.yaml`:
   ```yaml
   triggers:
     enabled: true
   ```

2. Run the squad — events are written to `.aios/squad-triggers/{squad-name}.jsonl`

3. Copy to scenarios:
   ```bash
   cp .aios/squad-triggers/my-squad.jsonl demo/scenarios/
   ```

4. Restart the server — the new scenario appears in the dropdown automatically.

## Structure

```
ai-public-arsenal/
├── skills/           # Installable via skills.sh
│   └── squads/       # Squad manager skill (SKILL.md + references/)
├── squads/           # Squad definitions (managed by the squads skill)
│   ├── nirvana-squad-creator/
│   └── ultimate-landingpage/
└── demo/             # Squad Flow Tracker (Demo + Dashboard)
    ├── index.html    # Dual-mode viewer (static demo / SSE dashboard)
    ├── server.js     # SSE replay server (zero dependencies)
    └── scenarios/    # JSONL scenario recordings
```

## License

MIT
