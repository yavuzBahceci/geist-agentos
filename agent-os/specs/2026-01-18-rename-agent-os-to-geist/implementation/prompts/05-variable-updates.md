# Prompt: Task Group 5 - Variable Name Updates

## Objective

Replace all `AGENT_OS_` and `agent_os_` variable names with `GEIST_` and `geist_` equivalents.

## Context

Variables are used in bash scripts embedded in markdown files and in shell scripts. They follow two conventions:
- Environment variables: `AGENT_OS_*` (uppercase)
- Script variables: `agent_os_*` (lowercase)

## Scope

All files in `profiles/` directory (scripts already done in Task Group 2).

## Tasks

### Task 5.1-5.6: Replace Variable Names

Replace ALL occurrences of these patterns:

| Find | Replace |
|------|---------|
| `AGENT_OS_PATH` | `GEIST_PATH` |
| `AGENT_OS_DIR` | `GEIST_DIR` |
| `AGENT_OS_COMMANDS` | `GEIST_COMMANDS` |
| `$AGENT_OS_PATH` | `$GEIST_PATH` |
| `$AGENT_OS_DIR` | `$GEIST_DIR` |
| `${AGENT_OS_PATH}` | `${GEIST_PATH}` |
| `${AGENT_OS_DIR}` | `${GEIST_DIR}` |
| `agent_os_dir` | `geist_dir` |
| `agent_os_path` | `geist_path` |

## Implementation

### Find all files with variable references:
```bash
grep -rl "AGENT_OS_" profiles/
grep -rl "agent_os_" profiles/
```

### Replace patterns (in order):
```bash
# Uppercase variables (with $ prefix variations)
sed -i '' 's/\$AGENT_OS_PATH/\$GEIST_PATH/g' <filename>
sed -i '' 's/\${AGENT_OS_PATH}/\${GEIST_PATH}/g' <filename>
sed -i '' 's/AGENT_OS_PATH/GEIST_PATH/g' <filename>

sed -i '' 's/\$AGENT_OS_DIR/\$GEIST_DIR/g' <filename>
sed -i '' 's/\${AGENT_OS_DIR}/\${GEIST_DIR}/g' <filename>
sed -i '' 's/AGENT_OS_DIR/GEIST_DIR/g' <filename>

sed -i '' 's/AGENT_OS_COMMANDS/GEIST_COMMANDS/g' <filename>

# Lowercase variables
sed -i '' 's/agent_os_dir/geist_dir/g' <filename>
sed -i '' 's/agent_os_path/geist_path/g' <filename>
```

### Bulk approach:
```bash
# Replace all AGENT_OS_ with GEIST_
find profiles/ -type f \( -name "*.md" -o -name "*.yml" -o -name "*.sh" \) -exec sed -i '' 's/AGENT_OS_/GEIST_/g' {} +

# Replace all agent_os_ with geist_
find profiles/ -type f \( -name "*.md" -o -name "*.yml" -o -name "*.sh" \) -exec sed -i '' 's/agent_os_/geist_/g' {} +
```

## Verification

After completion:

```bash
# These should return 0 results:
grep -r "AGENT_OS_" profiles/
grep -r "agent_os_" profiles/

# Verify new variables exist:
grep -r "GEIST_" profiles/ | head -10
grep -r "geist_" profiles/ | head -10
```

## Acceptance Criteria

- [ ] `grep -r "AGENT_OS_" profiles/` returns 0 results
- [ ] `grep -r "agent_os_" profiles/` returns 0 results
- [ ] Variable references maintain correct bash syntax
- [ ] No partial replacements breaking variable names

## Files Affected (Estimated ~11)

Based on analysis:
- Workflow files with bash code blocks
- Command files with environment setup
- Standards files with configuration examples

## Notes

- Be careful with `$` - it's a special character in regex
- Replace the longer patterns first to avoid partial matches
- Check that variable assignments AND usages are both updated
- Preserve bash syntax (quotes, braces, etc.)
