# Discovery Engine Protocol — Robust Squad Discovery & Troubleshooting

The Discovery Engine is responsible for finding squads in both `./squads/` (local workspace) and `~/squads/` (home directory). This protocol defines how discovery MUST work, failure modes, and debugging.

## Discovery Algorithm (Bulletproof Implementation)

### Phase 1: Path Expansion & Validation

```javascript
// CRITICAL: Tilde expansion MUST happen first
function expandPath(path) {
  if (path.startsWith('~')) {
    return path.replace('~', process.env.HOME);
  }
  if (path.startsWith('./')) {
    return path.replace('./', process.cwd() + '/');
  }
  return path;
}

// Resolve both locations with proper expansion
const localRoot = expandPath('./squads');      // → {cwd}/squads
const homeRoot = expandPath('~/squads');       // → {HOME}/squads
```

### Phase 2: Glob Squad Locations

```javascript
// MUST search BOTH locations independently
const localSquads = glob(`${localRoot}/*/squad.yaml`);
const homeSquads = glob(`${homeRoot}/*/squad.yaml`);

// Merge with deduplication (local takes precedence)
const allSquads = mergeAndDeduplicate(localSquads, homeSquads);
// Result: Map<squad-name, resolved-path>
```

### Phase 3: Validate Squad Integrity

```javascript
function validateSquad(squadPath) {
  const checks = [
    { name: 'squad.yaml exists', path: `${squadPath}/squad.yaml` },
    { name: 'squad.yaml is valid YAML', fn: validateYAML },
    { name: 'required fields present', fn: validateFields },
    { name: 'agents/ or README.md exists', fn: validateContent },
  ];

  return checks.map(check => ({
    ...check,
    passed: check.fn ? check.fn(squadPath) : fileExists(check.path),
    severity: 'warn' | 'error'
  }));
}
```

### Phase 4: Report Results

```javascript
function reportDiscovery(results) {
  if (results.found === 0) {
    // CRITICAL: Provide helpful debugging info
    console.error(`
⚠️  NO SQUADS FOUND

Searched locations:
  • Local workspace: ${localRoot}
  • Home directory: ${homeRoot}

Next steps:
  1. Verify directories exist:
     ls -la ${localRoot}/
     ls -la ${homeRoot}/

  2. Create a squad:
     *create-squad my-first-squad

  3. Check for permission issues:
     ls -ld ${homeRoot}

  4. Debug with verbose mode:
     *list-squads --debug
    `);
    return;
  }

  // Success: Group by location and version
  const local = results.filter(s => s.location === 'local');
  const home = results.filter(s => s.location === 'home');

  console.log(`✅ Found ${results.found} squads\n`);

  if (local.length > 0) {
    console.log('📂 Local Workspace (.squads/):');
    local.forEach(s => reportSquad(s));
  }

  if (home.length > 0) {
    console.log('\n🏠 Home Directory (~squads/):');
    home.forEach(s => reportSquad(s));
  }
}

function reportSquad(squad) {
  const agents = squad.agents?.length || 0;
  const workflows = squad.workflows?.length || 0;
  const version = squad.version || 'unknown';

  console.log(`  • ${squad.name} (v${version}, ${agents} agents, ${workflows} workflows)`);
}
```

---

## Debugging Checklist

When `*list-squads` returns 0 results, follow this systematic checklist:

### ✅ Step 1: Verify Directories Exist

```bash
# Check local workspace squads directory
ls -la ./squads/ 2>&1

# Check home directory squads
ls -la ~/squads/ 2>&1
```

**Expected Output**: Both show directory listing (or "No such file" if doesn't exist yet)

**Common Issue**: Directory doesn't exist yet
- **Fix**: `mkdir -p ~/squads/` to create it

---

### ✅ Step 2: Check Permissions

```bash
# Home directory must be readable
ls -ld ~/squads/

# Each squad subdirectory must be readable
ls -d ~/squads/*/ | head -3
```

**Expected Output**: `drwx------` or `drwxr-xr-x` (readable by owner)

**Common Issue**: Permission denied
- **Fix**: `chmod 755 ~/squads/` and `chmod 755 ~/squads/*/`

---

### ✅ Step 3: Check squad.yaml Files Exist

```bash
# Count squad.yaml files in home directory
find ~/squads/ -name "squad.yaml" | wc -l

# Show first few squad.yaml locations
find ~/squads/ -name "squad.yaml" | head -5
```

**Expected Output**: Positive count (e.g., `126`)

**Common Issue**: squad.yaml files missing or in wrong location
- **Fix**: Ensure each squad has `~/squads/{squad-name}/squad.yaml`

---

### ✅ Step 4: Test YAML Parsing

```bash
# Pick a squad and validate its squad.yaml
cat ~/squads/amazon-book-writer/squad.yaml | head -20

# Validate with a YAML linter (if available)
yamllint ~/squads/amazon-book-writer/squad.yaml
```

**Expected Output**: Valid YAML content with `name:`, `version:`, etc.

**Common Issue**: Invalid YAML syntax
- **Fix**: Fix the YAML (missing colons, bad indentation, quotes)

---

### ✅ Step 5: Test Path Expansion

If using skill within a CLI, test path expansion:

```javascript
// In Node.js or equivalent:
const path = require('path');
const homeDir = process.env.HOME;
const expandedPath = path.join(homeDir, 'squads');
console.log('Expanded path:', expandedPath);
```

**Expected Output**: `/Users/{username}/squads` (full absolute path)

**Common Issue**: Tilde (~) not expanded to home directory
- **Fix**: Use `process.env.HOME` or `path.expanduser()` equivalent

---

### ✅ Step 6: Enable Debug Mode

```bash
*list-squads --debug
# OR
*list-squads --verbose
```

**Expected Output**: Detailed logging showing:
```
[DEBUG] Searching local: /Users/guto/paperclip-projects/squads/
[DEBUG] Searching home: /Users/guto/squads/
[DEBUG] Found in home: amazon-book-writer/squad.yaml
[DEBUG] Found in home: brandcraft-nirvana/squad.yaml
[DEBUG] Validating amazon-book-writer... OK
[DEBUG] Validating brandcraft-nirvana... OK
[DEBUG] Total: 2 squads discovered
```

---

## Common Failure Modes & Fixes

### Failure Mode 1: "No squads found" but directories exist

**Root Cause**: Glob pattern not matching

**Debugging**:
```bash
# Manual glob test
ls ~/squads/*/squad.yaml | wc -l

# If this shows squads but skill doesn't find them:
# The glob pattern is broken or the skill isn't running it correctly
```

**Fix**:
- Verify glob pattern: `~/squads/*/squad.yaml` should match all squads
- Check if skill is using correct tool (Glob vs Bash)
- Ensure tilde expansion happens before glob

---

### Failure Mode 2: Permission denied on home directory

**Root Cause**: `~squads/` directory not readable

**Debugging**:
```bash
ls -ld ~/squads/
# Should show: drwxr-xr-x or similar with 'r' for owner
```

**Fix**:
```bash
chmod 755 ~/squads/
chmod 755 ~/squads/*/
```

---

### Failure Mode 3: squad.yaml not found in squad directories

**Root Cause**: Squad missing manifest file

**Debugging**:
```bash
# List all directories in ~/squads/
ls -d ~/squads/*/

# Find which ones don't have squad.yaml
find ~/squads/ -maxdepth 1 -type d ! -exec test -e {}/squad.yaml \; -print
```

**Fix**:
```bash
# For each squad missing squad.yaml:
cat > ~/squads/{squad-name}/squad.yaml << EOF
name: {squad-name}
version: "1.0.0"
description: "Squad description"

agents: []
tasks: []
workflows: []
EOF
```

---

### Failure Mode 4: Invalid YAML in squad.yaml

**Root Cause**: Syntax errors in manifest file

**Debugging**:
```bash
# Try to parse YAML file
python3 -c "import yaml; yaml.safe_load(open('~/squads/amazon-book-writer/squad.yaml'))"

# Or with Node.js
node -e "const yaml = require('yaml'); console.log(yaml.parse(require('fs').readFileSync('./squad.yaml', 'utf-8')))"
```

**Fix**: Correct the YAML syntax

---

## Self-Healing Discovery

The Discovery Engine SHOULD implement these self-healing strategies:

### Strategy 1: Detect Missing squad.yaml

If a directory exists in `~/squads/` but has no `squad.yaml`:
```
⚠️  Found directory without manifest:
    ~/squads/incomplete-squad/

Option 1: Create minimal squad.yaml:
  *create-squad-manifest incomplete-squad

Option 2: Remove directory:
  rm -rf ~/squads/incomplete-squad/
```

### Strategy 2: Detect Invalid YAML

If `squad.yaml` exists but contains syntax errors:
```
❌ Squad manifest invalid: ~/squads/malformed-squad/squad.yaml
   Error: Line 15: expected <block end>, but found '-'

Option 1: Try to repair automatically
Option 2: Show the file for manual fix
Option 3: Roll back to backup (if available)
```

### Strategy 3: Suggest Creation

If no squads found at all:
```
No squads found. Create one:

*create-squad my-first-squad
  → Creates ~/squads/my-first-squad/ with:
    - squad.yaml (manifest)
    - agents/ (empty)
    - workflows/ (empty)
    - README.md (template)
```

---

## Expected Behavior by Scenario

### Scenario 1: First Run (No squads exist yet)

```
$ *list-squads

No squads found.

Getting started:
  1. Create your first squad:
     *create-squad my-first-squad

  2. Or import existing squads:
     cp -r /path/to/squads/* ~/squads/

  3. Then list again:
     *list-squads
```

### Scenario 2: Squads in ~/squads/ but not ./squads/

```
$ *list-squads

✅ Found 126 squads

🏠 Home Directory (~/squads/):
  • amazon-book-writer (v1, 7 agents, 5 workflows)
  • brandcraft-nirvana (v3, 13 agents, 8 workflows)
  • ...

To use local workspace squads, put them in ./squads/
```

### Scenario 3: Squads in both locations (local takes precedence)

```
$ *list-squads

✅ Found 5 squads (3 local + 2 global)

📂 Local Workspace (./squads/):
  • my-local-squad (v3, 9 agents, 4 workflows)
  • project-specific-squad (v2, 5 agents, 2 workflows)

🏠 Home Directory (~/squads/):
  • global-tool (v1, 3 agents, 1 workflow)

Note: my-local-squad exists in both locations
→ Using local version (./squads/my-local-squad/)
```

---

## Implementation Checklist

When implementing the Discovery Engine, verify:

- ✅ Tilde expansion works (e.g., `~/squads` → `/Users/{user}/squads`)
- ✅ Glob pattern matches both locations independently
- ✅ Deduplication prefers local over home
- ✅ squad.yaml validation catches syntax errors
- ✅ Helpful error messages when 0 squads found
- ✅ Debug mode shows detailed logging
- ✅ Self-healing suggests fixes for common issues
- ✅ Permission errors are caught and reported
- ✅ Performance acceptable (glob is fast, but validate in parallel if possible)
- ✅ Works with symlinks (if squad is symlinked to different location)

---

## Performance Considerations

For large squad collections (100+ squads):

1. **Lazy validation**: Don't validate all squads on `*list-squads`
   - Load only name, version, agent/workflow counts
   - Full validation only when squad is used

2. **Parallel globbing**: Run both glob operations concurrently
   - Local glob vs home glob can happen in parallel
   - Merge results after both complete

3. **Caching**: Cache discovery results with TTL
   - Cache for 30 seconds unless explicitly refreshed
   - Invalidate cache when new squads created/deleted

4. **Pagination**: Support `--limit` and `--offset` for large lists
   ```bash
   *list-squads --limit 20 --offset 0
   *list-squads --limit 20 --offset 20
   ```
