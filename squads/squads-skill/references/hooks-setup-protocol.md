# Hooks Setup Protocol — Optional Enhancement for JSONL Logging

## Visão Geral

Claude Code Hooks são uma **enhancement opcional** para persistir triggers em disco como JSONL. O mecanismo primário de emissão são **stream markers** (HTML comments no output) — hooks complementam gravando em arquivo para histórico.

**Quando usar hooks:**
- Quando `triggers.display` é `log` ou `both`
- Quando você quer logs persistentes para análise posterior
- Quando roda no terminal sem frontend (standalone)

**Quando NÃO precisa de hooks:**
- Quando `triggers.display` é `inline` (default) — markers no stream são suficientes
- Quando usa squad-chat ou outro frontend que parseia o stream em tempo real

## Verificação de Hooks

```bash
# Hook file existe?
ls .claude/hooks/squad-trigger-logger.cjs 2>/dev/null

# Settings tem o hook registrado?
cat .claude/settings.local.json 2>/dev/null | grep "squad-trigger"
```

## Setup Protocol

### Passo 1: Criar o Hook File

Criar `.claude/hooks/squad-trigger-logger.cjs`:

```javascript
#!/usr/bin/env node
'use strict';

/**
 * Squad Trigger Logger — Claude Code Hook (Optional)
 *
 * Persists squad lifecycle events to JSONL files on disk.
 * This is a COMPLEMENT to stream markers, not a replacement.
 *
 * Registered on: PostToolUse (all tools)
 * Output: {cwd}/.aios/squad-triggers/{squad}.jsonl
 */

const fs = require('fs');
const path = require('path');
const os = require('os');

function main() {
  let input = '';
  process.stdin.setEncoding('utf8');
  process.stdin.on('data', chunk => { input += chunk; });
  process.stdin.on('end', () => {
    try {
      processEvent(JSON.parse(input));
    } catch { /* silent — never block Claude */ }
  });
}

function processEvent(data) {
  const { hook_event_name, tool_name, tool_input, session_id, cwd } = data;
  if (!cwd || !session_id || hook_event_name !== 'PostToolUse') return;

  const stateFile = path.join(os.tmpdir(), `squad-log-${session_id}.json`);

  // Detect squad.yaml reads → start tracking
  if (tool_name === 'Read' && tool_input?.file_path?.match(/squads\/[^/]+\/squad\.yaml$/)) {
    const squadDir = path.dirname(tool_input.file_path);
    const squadName = path.basename(squadDir);
    const triggersDir = path.join(cwd, '.aios', 'squad-triggers');
    fs.mkdirSync(triggersDir, { recursive: true });

    const state = {
      squad: squadName,
      triggersDir,
      startTime: Date.now(),
      currentAgent: null,
      agents: [],
    };
    fs.writeFileSync(stateFile, JSON.stringify(state));
    appendEvent(triggersDir, squadName, { type: 'squad-start', squad: squadName });
    return;
  }

  // Track agent reads
  if (!fs.existsSync(stateFile)) return;
  const state = safeReadJSON(stateFile);
  if (!state) return;

  if (tool_name === 'Read' && tool_input?.file_path?.match(/\/agents\/[^/]+\.md$/)) {
    const agentId = path.basename(tool_input.file_path, '.md');
    if (agentId !== state.currentAgent) {
      if (state.currentAgent) {
        appendEvent(state.triggersDir, state.squad, {
          type: 'flow-transition',
          squad: state.squad,
          from: state.currentAgent,
          to: agentId,
          progress: `${state.agents.length + 1}/?`,
        });
      }
      state.currentAgent = agentId;
      if (!state.agents.includes(agentId)) state.agents.push(agentId);
      appendEvent(state.triggersDir, state.squad, {
        type: 'agent-start', squad: state.squad, agent: agentId,
      });
      fs.writeFileSync(stateFile, JSON.stringify(state));
    }
  }
}

function appendEvent(dir, squad, eventData) {
  const ts = new Date().toISOString().replace(/\.\d{3}Z$/, 'Z');
  const event = { ...eventData, timestamp: ts };
  try {
    fs.appendFileSync(path.join(dir, `${squad}.jsonl`), JSON.stringify(event) + '\n');
  } catch {}
}

function safeReadJSON(fp) {
  try { return JSON.parse(fs.readFileSync(fp, 'utf8')); } catch { return null; }
}

main();
```

### Passo 2: Registrar no Settings

Adicionar a `.claude/settings.local.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "node \"$CLAUDE_PROJECT_DIR/.claude/hooks/squad-trigger-logger.cjs\"",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

**IMPORTANTE:** Fazer merge com hooks existentes, não sobrescrever.

### Passo 3: Verificação

```bash
node -c .claude/hooks/squad-trigger-logger.cjs
cat .claude/settings.local.json | grep squad-trigger
```

## Quando Executar o Setup

- `*enable-triggers {name}` com `display: log | both` → verificar e configurar hooks
- `*validate-squad {name}` com triggers habilitados → verificar hooks como item de validação

## Desinstalação

No `*disable-triggers`, se nenhum outro squad usa triggers com `display: log | both`:

1. Remover: `rm .claude/hooks/squad-trigger-logger.cjs`
2. Remover entries do `settings.local.json`
3. Manter logs existentes em `.aios/squad-triggers/` (histórico)

## Compatibilidade

| Plataforma | Hooks | Stream Markers |
|---|---|---|
| Claude Code ≥ 1.0 | ✅ Suportado | ✅ Sempre funciona |
| Codex CLI | ❌ Sem hooks | ✅ Sempre funciona |
| Gemini CLI | ❌ Sem hooks | ✅ Sempre funciona |
| Cursor | ❌ Sem hooks | ✅ Sempre funciona |
| squad-chat | Desnecessário | ✅ Parseado automaticamente |

**Stream markers são o mecanismo universal. Hooks são enhancement para persistência em disco.**
