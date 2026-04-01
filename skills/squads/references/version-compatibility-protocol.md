# Version Compatibility Protocol — v1/v2/v3 Full Support

The Squad Manager v3 skill supports **all squad versions** (v1, v2, v3) with automatic version detection and format-appropriate execution.

## Version Detection Algorithm

When resolving a squad from `./squads/{name}` or `~/squads/{name}`, detect the version by reading `squad.yaml` and checking these keys in order:

```
1. Does squad.yaml have `harness:` key?
   YES → v3 squad (has harness block)
   NO  → Continue to step 2

2. Does squad.yaml have `state:` OR `model_strategy:` OR `components.schemas:`?
   YES → v2 squad (has state/validation gates)
   NO  → v1 squad (basic squad definition)
```

### Detection Truth Table

| `harness:` | `state:` | `model_strategy:` | `schemas:` | **Version** |
|---|---|---|---|---|
| ✅ | - | - | - | **v3** |
| ❌ | ✅ | ✅ | ✅ | **v2** |
| ❌ | ❌ | ❌ | ❌ | **v1** |

## Version Characteristics

### v1 Squad Structure
- **Purpose**: Basic multi-agent workflows with simple coordination
- **Manifest**: `squad.yaml` with agents, tasks, workflows (simple pipelines only)
- **Agents**: `.md` files in `agents/` directory
- **Tasks**: `.md` files in `tasks/` directory
- **Workflows**: `.yaml` files in `workflows/` directory (pipeline type only)
- **State**: None — no checkpoint, no resume
- **Validation**: Text checklists only
- **Artifacts**: Simple text handoff between agents
- **Model**: Same model for all agents
- **Execution**: Simple sequential dispatch with text context accumulation

**Example v1 squad.yaml**:
```yaml
name: legal-reviewer-v1
version: "1.0.0"
description: "Review legal documents"

agents:
  - id: lawyer
    role: "Legal Expert"
    model: "claude-opus"

tasks:
  - id: extract-clauses
    description: "Extract key clauses"
    assignee: lawyer

workflows:
  - id: main-workflow
    type: pipeline
    sequence:
      - task: extract-clauses
```

**Execution Mode**: `run-workflow {squad} {workflow}` → Sequential step dispatch → Text accumulation → Output to stdout

---

### v2 Squad Structure
- **Purpose**: Reliable, validated multi-agent workflows with checkpoints and quality gates
- **Manifest**: `squad.yaml` with `state:` and/or `model_strategy:`
- **New Features**:
  - `state:` block → Checkpoint/resume capability
  - `schemas/` directory → JSON Schema validation for artifacts
  - `model_strategy:` block → Per-agent/per-step model selection
  - Validation gates on workflow steps
- **State Persistence**: `.squad-state/{run-id}/` with checkpoint files
- **Resume Capability**: Failed workflows can resume from last successful step
- **Validation**: JSON Schema validation on step handoffs (optional gates)

**Example v2 squad.yaml**:
```yaml
name: legal-reviewer-v2
version: "1.0.0"
description: "Review legal documents with validation gates"

state:
  checkpoints: true
  max_checkpoint_size: 10000
  cleanup_policy: on_success

model_strategy:
  lawyer:
    model: "claude-opus"
  analyst:
    model: "claude-sonnet"

agents:
  - id: lawyer
    role: "Legal Expert"

workflows:
  - id: main-workflow
    type: pipeline
    sequence:
      - agent: lawyer
        action: "Extract clauses"
        validates: { schema: "clauses.json" }  # v2 validation gate
```

**Execution Mode**: `run-workflow {squad} {workflow}` → Sequential dispatch with checkpoints → Validation gates checked → Can resume from `.squad-state/{run-id}`

---

### v3 Squad Structure
- **Purpose**: Fault-tolerant, self-healing multi-agent teams with harness engineering
- **Manifest**: `squad.yaml` with required `harness:` block
- **New Features**:
  - Doom loop detection (identical output abort)
  - Ralph loop (fresh context retry with state persistence)
  - Context compaction strategies
  - Filesystem collaboration (artifacts on disk)
  - Execution traces (step-level observability)
  - Reasoning sandwich (model routing by phase)
  - DAG workflows (not just pipelines)
  - Self-verify per step

**Example v3 squad.yaml**:
```yaml
name: legal-reviewer-v3
version: "1.0.0"
description: "Review legal documents with harness engineering"

harness:
  doom_loop:
    enabled: true
    max_identical_outputs: 3
    on_detect: change-strategy

  ralph_loop:
    enabled: true
    max_iterations: 5

  context_compaction:
    enabled: true
    strategy: key-fields

  self_verify:
    default_enabled: true

agents:
  - id: lawyer
    role: "Legal Expert"

workflows:
  - id: main-workflow
    type: dag  # v3 supports DAG
    sequence:
      - agent: lawyer
        phase: planning      # v3 reasoning sandwich phase
        action: "Extract clauses"
        self_verify:
          enabled: true
          checklist:
            - "All clauses identified"
```

**Execution Mode**: `run-workflow {squad} {workflow}` → Full v3 harness execution → Doom loop detection → Filesystem artifacts → Execution traces → `.squad-state/{run-id}/traces/`

---

## Execution Rules by Version

### How v1 Squads Execute

**Signature**: `*run-workflow {squad} {workflow}`

1. Resolve squad from `./squads/{squad}` or `~/squads/{squad}`
2. Detect version → v1 (no harness, no state)
3. Load `workflows/{workflow}.yaml`
4. For each step in `sequence`:
   - Read step definition
   - Dispatch to agent via Claude with accumulated context
   - Collect output
   - Add output to context for next step
5. Return final output (no checkpoint, no filesystem artifacts)

**Features Available**:
- ✅ Sequential execution
- ✅ Text context accumulation
- ❌ State checkpoints
- ❌ Validation gates
- ❌ Resume capability
- ❌ Doom loop detection
- ❌ Filesystem artifacts

**Fallback Behavior**: If a step fails, the workflow fails (no retry). User must re-run with `*run-workflow {squad} {workflow}` from scratch.

---

### How v2 Squads Execute

**Signature**: `*run-workflow {squad} {workflow}` OR `*resume-workflow {squad} {run-id}`

1. Resolve squad → detect version → v2 (has state/schemas but no harness)
2. Initialize `.squad-state/{run-id}/` directory
3. Load workflow and check for validation gates
4. For each step:
   - Dispatch to agent with accumulated context
   - Collect output
   - **If gate defined**: Validate output against schema
   - Save step state to `.squad-state/{run-id}/step-{N}.json`
   - Add output to context (or abort if validation fails)
5. Return final output + checkpoint location

**Resume Support**:
- `*show-state {squad}` → List all runs
- `*show-run {squad} {run-id}` → Show run details
- `*resume-workflow {squad} {run-id}` → Pick up from last successful checkpoint

**Features Available**:
- ✅ Sequential execution
- ✅ Text context accumulation
- ✅ State checkpoints (`.squad-state/{run-id}/`)
- ✅ Validation gates (JSON Schema)
- ✅ Resume from checkpoint
- ❌ Doom loop detection
- ❌ Filesystem artifacts
- ❌ DAG workflows

---

### How v3 Squads Execute

**Signature**: `*run-workflow {squad} {workflow}` (automatically uses v3 runtime)

1. Resolve squad → detect version → v3 (has harness block)
2. Load harness configuration from `squad.yaml`
3. Initialize `.squad-state/{run-id}/` with traces and artifacts subdirs
4. For each step:
   - **Check doom loop**: Compare output to last N outputs (if detection enabled)
   - Dispatch to agent with **compacted context** (key-fields/truncate/summarize)
   - Collect output
   - **Check validation**: Schema + assertions (if configured)
   - **Check self-verify**: Run checklist/tests (if configured)
   - **If validation fails**: Ralph loop (fresh context retry, up to max_iterations)
   - **If doom loop detected**: Change strategy, escalate model, or abort
   - Save step to `.squad-state/{run-id}/step-{N}.json`
   - Save artifacts to `.squad-state/{run-id}/artifacts/`
   - Save trace to `.squad-state/{run-id}/traces/trace-{run-id}.jsonl`
5. Return final output + artifacts + execution traces

**V3 Harness Features Available**:
- ✅ All v2 features (state, validation, resume)
- ✅ Doom loop detection
- ✅ Ralph loop (fresh context retry)
- ✅ Context compaction
- ✅ Filesystem artifacts
- ✅ Execution traces (step-level observability)
- ✅ Reasoning sandwich (model routing by phase)
- ✅ DAG workflows
- ✅ Self-verify per step
- ✅ Diminishing returns detection

---

## Upgrade Paths

### v1 → v2
**Command**: `*upgrade-squad {name}`

Changes made:
1. Add `state:` block to squad.yaml
2. Create `schemas/` directory (if not exists)
3. Optionally add validation gates to workflow steps

```yaml
# Before (v1)
workflows:
  - id: main-workflow
    sequence:
      - agent: lawyer
        action: "Extract clauses"

# After (v2)
state:
  checkpoints: true

workflows:
  - id: main-workflow
    sequence:
      - agent: lawyer
        action: "Extract clauses"
        validates: { schema: "clauses.json" }
```

---

### v2 → v3
**Command**: `*upgrade-squad {name}`

Changes made:
1. Add required `harness:` block with sensible defaults
2. Optionally add self-verify blocks to steps
3. Optionally convert pipeline workflows to DAG format

```yaml
# Before (v2)
state:
  checkpoints: true

# After (v3)
harness:
  doom_loop:
    enabled: true
    max_identical_outputs: 3
    on_detect: change-strategy

  ralph_loop:
    enabled: true
    max_iterations: 5

  context_compaction:
    enabled: true
    strategy: key-fields
```

---

## Compatibility Matrix

| Operation | v1 | v2 | v3 |
|---|---|---|---|
| `*run-workflow` | ✅ | ✅ | ✅ |
| `*list-squads` | ✅ | ✅ | ✅ |
| `*inspect-squad` | ✅ | ✅ | ✅ |
| `*add-agent` | ✅ | ✅ | ✅ |
| `*add-workflow` | ✅ | ✅ | ✅ |
| `*validate-squad` | ✅ | ✅ | ✅ |
| `*show-state` | ❌ | ✅ | ✅ |
| `*resume-workflow` | ❌ | ✅ | ✅ |
| `*show-run` | ❌ | ✅ | ✅ |
| `*show-traces` | ❌ | ❌ | ✅ |
| `*show-artifacts` | ❌ | ❌ | ✅ |
| `*configure-harness` | ❌ | ❌ | ✅ |
| `*add-self-verify` | ❌ | ❌ | ✅ |
| `*upgrade-squad` | ✅→v2 | ✅→v3 | N/A |

---

## Practical Examples

### Running a v1 Squad
```bash
*run-workflow amazon-book-writer main-pipeline
# Output: Final book content (text)
# No state saved, no resumable checkpoints
```

### Running a v2 Squad with Resume
```bash
*run-workflow legal-reviewer-v2 main-workflow
# Output: Review document (validated)
# State saved to: .squad-state/{run-id}/

*show-state legal-reviewer-v2
# Lists all runs with their status

*resume-workflow legal-reviewer-v2 {run-id}
# Picks up from last successful step
```

### Running a v3 Squad with Full Harness
```bash
*run-workflow brandcraft-nirvana main-pipeline
# Execution includes:
# - Doom loop detection
# - Context compaction
# - Filesystem artifacts at .squad-state/{run-id}/artifacts/
# - Execution traces at .squad-state/{run-id}/traces/trace-*.jsonl
# - Self-verification per step
# - Fresh context retries (Ralph loop) on validation failure

*show-artifacts brandcraft-nirvana {run-id}
# Lists all artifacts created during the run

*show-traces brandcraft-nirvana {run-id}
# Shows step-level execution timeline with timing and events
```

---

## Migration Considerations

### When to Upgrade

- **v1 → v2**: When you need reproducibility, checkpoints, or validation gates
- **v2 → v3**: When you need reliability features (doom loop detection, fresh context retries) for production use

### Backwards Compatibility Guarantee

- All v1 squads work unchanged in the v3 runtime (no harness features used)
- All v2 squads work unchanged in the v3 runtime (state and validation preserved, harness optional)
- Upgrading is always additive (old features still work)

---

## Anti-Patterns (Cross-Version)

❌ **Don't**: Assume a v1 squad has checkpoints (it doesn't)
✅ **Do**: Check `squad.yaml` version before using `*resume-workflow`

❌ **Don't**: Mix v1/v2/v3 agents in the same workflow without version awareness
✅ **Do**: Upgrade all agents to the same version for consistency

❌ **Don't**: Use `*show-traces` on a v1/v2 squad (won't exist)
✅ **Do**: Check squad version first via `*inspect-squad {name}`

❌ **Don't**: Assume validation gates are automatic (v1 has none)
✅ **Do**: Declare validation gates explicitly in workflow steps (v2+)

❌ **Don't**: Rely on context compaction in v1/v2 (only v3)
✅ **Do**: Monitor context usage and manually trim old context in v1/v2
