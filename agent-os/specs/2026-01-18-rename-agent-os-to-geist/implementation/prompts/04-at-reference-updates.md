# Prompt: Task Group 4 - @ Reference Updates

## Objective

Replace all `@agent-os/` references with `@geist/` throughout the codebase.

## Context

The `@` prefix is used for internal file references in the Geist system. These references point to files within the installed folder structure.

## Scope

All files in `profiles/` directory that contain `@agent-os/` references.

## Tasks

### Task 4.1-4.9: Replace @ References

Replace ALL occurrences of these patterns:

| Find | Replace |
|------|---------|
| `@agent-os/commands/` | `@geist/commands/` |
| `@agent-os/workflows/` | `@geist/workflows/` |
| `@agent-os/standards/` | `@geist/standards/` |
| `@agent-os/basepoints/` | `@geist/basepoints/` |
| `@agent-os/product/` | `@geist/product/` |
| `@agent-os/config/` | `@geist/config/` |
| `@agent-os/specs/` | `@geist/specs/` |
| `@agent-os/output/` | `@geist/output/` |
| `@agent-os/agents/` | `@geist/agents/` |

**Simplified approach:** Replace `@agent-os/` with `@geist/` globally.

## Implementation

### Find all files with @ references:
```bash
grep -rl "@agent-os/" profiles/
```

### For each file, replace:
```bash
# Using sed (or your preferred method):
sed -i '' 's/@agent-os\//@geist\//g' <filename>
```

### Or bulk replace:
```bash
find profiles/ -type f -name "*.md" -exec sed -i '' 's/@agent-os\//@geist\//g' {} +
find profiles/ -type f -name "*.yml" -exec sed -i '' 's/@agent-os\//@geist\//g' {} +
```

## Verification

After completion:

```bash
# This should return 0 results:
grep -r "@agent-os/" profiles/

# This should show the new references:
grep -r "@geist/" profiles/ | head -20
```

## Acceptance Criteria

- [ ] `grep -r "@agent-os/" profiles/` returns 0 results
- [ ] All `@geist/` references are valid paths
- [ ] No partial replacements (e.g., `@@geist/` or `@geist-os/`)

## Files Affected (Estimated ~27)

Based on analysis, these file types contain @ references:
- Command files (`profiles/default/commands/**/*.md`)
- Workflow files (`profiles/default/workflows/**/*.md`)
- Documentation files (`profiles/default/docs/**/*.md`)

## Notes

- This is a straightforward find-replace operation
- The @ symbol is literal, not a regex special character
- Make sure to replace in ALL file types (.md, .yml, .sh, etc.)
- Do NOT replace in this spec folder (agent-os/specs/) - those will be handled separately
