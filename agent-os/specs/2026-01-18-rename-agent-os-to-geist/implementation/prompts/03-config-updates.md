# Prompt: Task Group 3 - Config File Updates

## Objective

Update configuration files to use "geist" instead of "agent-os".

## Context

Config files define default settings and ignore patterns. They need to reference the new `geist/` folder name.

## Files to Update

1. `profiles/default/config.yml`
2. `.gitignore` (root level, if exists)

## Tasks

### Task 3.1: Update config.yml

Find and replace in `profiles/default/config.yml`:

| Find | Replace |
|------|---------|
| `agent_os_commands` | `geist_commands` |
| `agent_os_` | `geist_` |
| `agent-os/` | `geist/` |
| `"agent-os"` | `"geist"` |

**Example changes:**
```yaml
# Before
agent_os_commands: true
default_path: agent-os/

# After
geist_commands: true
default_path: geist/
```

### Task 3.2: Update .gitignore

If `.gitignore` exists at the root level, find and replace:

| Find | Replace |
|------|---------|
| `agent-os/` | `geist/` |

**Example changes:**
```gitignore
# Before
agent-os/

# After
geist/
```

**Note:** The `.gitignore` may not exist or may not have agent-os references. Check first.

## Verification

After completion:

```bash
# Check config.yml for any remaining references:
grep -n "agent" profiles/default/config.yml

# Check .gitignore if it exists:
grep -n "agent" .gitignore 2>/dev/null || echo "No .gitignore or no matches"
```

## Acceptance Criteria

- [ ] No `agent_os` keys remain in config.yml
- [ ] No `agent-os` path references remain in config.yml
- [ ] `.gitignore` updated (if applicable)
- [ ] YAML syntax remains valid

## Notes

- Preserve YAML formatting and indentation
- Don't change comments unless they reference agent-os
- Config file may have other settings - only change agent-os related ones
