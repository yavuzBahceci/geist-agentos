# Accumulated Knowledge: Rename agent-os to geist

## From shape-spec

### Scope
- 1,874 occurrences across 198 files
- 1 folder to rename (cleanup-agent-os → cleanup-geist)
- 2 files to rename
- 3 scripts to update
- 2 config files to update

### Patterns Identified
- `@agent-os/` references (27 files)
- `AGENT_OS_` variables (6 files)
- `agent_os_` variables (5 files)
- `cleanup-agent-os` command (15 files)

### Exclusions
- Credit sections mentioning Agent OS
- URLs to buildermethods.com/agent-os

## From write-spec

### Implementation Strategy
6 phases identified:
1. Folder/file renames
2. Script updates
3. Config updates
4. Bulk text replacement
5. Documentation review
6. Validation

### Replacement Order (Most Specific First)
1. `@agent-os/` → `@geist/`
2. `AGENT_OS_` → `GEIST_`
3. `agent_os_` → `geist_`
4. `/cleanup-agent-os` → `/cleanup-geist`
5. `cleanup-agent-os` → `cleanup-geist`
6. `agent-os/` → `geist/`
7. `"agent-os"` → `"geist"`

### Key Files
- `scripts/project-install.sh` - Creates installation folder
- `scripts/project-update.sh` - Updates existing installation
- `scripts/common-functions.sh` - Shared utilities
- `config.yml` - Default configuration
- `.gitignore` - Ignore patterns

### Validation Requirements
- Zero grep matches for agent-os patterns (except credits)
- All commands work with new naming
- Installation creates geist/ folder

## From create-tasks

### Task Groups (9 total)
1. **Folder/File Renames** - 3 operations (Critical, parallel)
2. **Script Updates** - 3 files (Critical, parallel)
3. **Config Updates** - 2 files (High, parallel)
4. **@ Reference Updates** - ~27 files (High, sequential)
5. **Variable Name Updates** - ~11 files (High, sequential)
6. **Command Name Updates** - ~15 files (High, sequential)
7. **Path Reference Updates** - ~198 files (High, sequential - last)
8. **Documentation Updates** - ~35 files (Medium)
9. **Validation** - Final verification (Critical)

### Execution Strategy
- Groups 1-3 can run in parallel
- Groups 4-7 must run sequentially (most specific first)
- Group 8 after all replacements
- Group 9 validates everything

### Key Insight
Replace in order from most specific to least specific to avoid partial matches:
1. `@agent-os/` (most specific)
2. `AGENT_OS_`
3. `agent_os_`
4. `/cleanup-agent-os`
5. `cleanup-agent-os`
6. `agent-os/` (least specific - do last)
