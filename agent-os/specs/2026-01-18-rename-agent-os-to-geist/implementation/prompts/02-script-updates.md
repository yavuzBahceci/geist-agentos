# Prompt: Task Group 2 - Script Updates

## Objective

Update all installation and utility scripts to use "geist" instead of "agent-os".

## Context

These scripts control where Geist gets installed in target projects. They must create a `geist/` folder instead of `agent-os/`.

## Files to Update

1. `scripts/project-install.sh`
2. `scripts/project-update.sh`
3. `scripts/common-functions.sh`

## Tasks

### Task 2.1: Update project-install.sh

Find and replace in `scripts/project-install.sh`:

| Find | Replace |
|------|---------|
| `AGENT_OS_DIR` | `GEIST_DIR` |
| `AGENT_OS_PATH` | `GEIST_PATH` |
| `agent-os` (in paths/folder names) | `geist` |
| `"agent-os"` (quoted strings) | `"geist"` |
| `agent_os` (variable names) | `geist` |

**Key changes:**
```bash
# Before
AGENT_OS_DIR="$PROJECT_DIR/agent-os"
mkdir -p "$AGENT_OS_DIR"

# After
GEIST_DIR="$PROJECT_DIR/geist"
mkdir -p "$GEIST_DIR"
```

### Task 2.2: Update project-update.sh

Find and replace in `scripts/project-update.sh`:

| Find | Replace |
|------|---------|
| `AGENT_OS_DIR` | `GEIST_DIR` |
| `AGENT_OS_PATH` | `GEIST_PATH` |
| `agent-os` (in paths) | `geist` |
| `"agent-os"` | `"geist"` |

**Key changes:**
```bash
# Before
AGENT_OS_DIR="$PROJECT_DIR/agent-os"
if [ ! -d "$AGENT_OS_DIR" ]; then

# After
GEIST_DIR="$PROJECT_DIR/geist"
if [ ! -d "$GEIST_DIR" ]; then
```

### Task 2.3: Update common-functions.sh

Find and replace in `scripts/common-functions.sh`:

| Find | Replace |
|------|---------|
| `AGENT_OS_PATH` | `GEIST_PATH` |
| `AGENT_OS_` | `GEIST_` |
| `agent_os_` | `geist_` |
| `agent-os` (default values) | `geist` |

**Key changes:**
```bash
# Before
AGENT_OS_PATH="${AGENT_OS_PATH:-agent-os}"

# After
GEIST_PATH="${GEIST_PATH:-geist}"
```

## Verification

After completion, verify no old references remain:

```bash
# These should return 0 results:
grep -n "AGENT_OS" scripts/*.sh
grep -n "agent_os" scripts/*.sh
grep -n "agent-os" scripts/*.sh
```

## Acceptance Criteria

- [ ] No `AGENT_OS_` variables remain in any script
- [ ] No `agent_os_` variables remain in any script
- [ ] No `agent-os` path references remain in any script
- [ ] Scripts would create `geist/` folder when run
- [ ] All echo/print messages updated to reference "geist"

## Notes

- Be careful with string replacements - don't break shell syntax
- Update both variable declarations AND usages
- Update help text and echo messages
- Preserve script functionality - only change naming
