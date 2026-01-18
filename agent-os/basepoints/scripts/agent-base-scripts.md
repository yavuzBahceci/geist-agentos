# Basepoint: scripts/

## Overview

The `scripts/` folder contains shell scripts for installing, updating, and managing Agent OS installations in target projects.

## Purpose

Provides automation for:
- Installing Agent OS templates into projects
- Updating existing installations
- Creating new profiles
- Shared utility functions

## Files

| File | Purpose | Lines |
|------|---------|-------|
| `project-install.sh` | Main installation script | ~660 |
| `project-update.sh` | Update existing installation | ~300 |
| `common-functions.sh` | Shared utilities | ~1550 |
| `create-profile.sh` | Create new profile | ~100 |

## Key Patterns

### 1. Script Structure
```bash
#!/bin/bash
set -e  # Exit on error

SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
source "$SCRIPT_DIR/common-functions.sh"
```

### 2. Color Output
```bash
RED='\033[38;2;255;32;86m'
GREEN='\033[38;2;0;234;179m'
YELLOW='\033[38;2;255;185;0m'
BLUE='\033[38;2;0;208;255m'
NC='\033[0m'

print_color() {
    local color=$1
    shift
    echo -e "${color}$@${NC}"
}
```

### 3. Argument Parsing
```bash
parse_arguments() {
    while [[ $# -gt 0 ]]; do
        local flag="${1//_/-}"  # Normalize underscores to hyphens
        case $flag in
            --profile) PROFILE="$2"; shift 2 ;;
            --dry-run) DRY_RUN="true"; shift ;;
            *) shift ;;
        esac
    done
}
```

### 4. YAML Parsing
```bash
get_yaml_value() {
    local file=$1
    local key=$2
    local default=$3
    # Uses awk for robust parsing
}
```

## Dependencies

- **Bash 4.0+** - Required for associative arrays
- **Standard Unix tools** - `sed`, `awk`, `grep`, `find`
- **No external dependencies** - Pure shell

## Entry Points

| Script | Invocation | Purpose |
|--------|------------|---------|
| `project-install.sh` | `./scripts/project-install.sh --profile default` | Fresh install |
| `project-update.sh` | `./scripts/project-update.sh` | Update existing |
| `create-profile.sh` | `./scripts/create-profile.sh my-profile` | New profile |

## Error Handling

- Uses `set -e` for exit on error
- Validates prerequisites before operations
- Provides colored error messages
- Supports `--dry-run` for testing

## Configuration

Reads from `config.yml`:
- `profile` - Default profile to use
- `claude_code_commands` - Install Claude Code commands
- `agent_os_commands` - Install agent-os commands
- `use_claude_code_subagents` - Enable subagents

## Output Locations

All outputs go to target project's `agent-os/` directory:
- `agent-os/commands/` - Compiled commands
- `agent-os/workflows/` - Compiled workflows
- `agent-os/standards/` - Compiled standards
- `agent-os/agents/` - Compiled agents
- `agent-os/config.yml` - Installation config
