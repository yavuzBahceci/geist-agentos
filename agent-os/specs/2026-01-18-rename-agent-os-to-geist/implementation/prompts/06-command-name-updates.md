# Prompt: Task Group 6 - Command Name Updates

## Objective

Replace all references to the `cleanup-agent-os` command with `cleanup-geist`.

## Context

The `cleanup-agent-os` command was renamed in Task Group 1. Now all references to this command throughout the codebase need to be updated.

## Scope

All files in `profiles/` directory that reference the cleanup command.

## Tasks

### Task 6.1-6.4: Replace Command References

Replace ALL occurrences of these patterns:

| Find | Replace |
|------|---------|
| `/cleanup-agent-os` | `/cleanup-geist` |
| `cleanup-agent-os` | `cleanup-geist` |
| `"Run /cleanup-agent-os"` | `"Run /cleanup-geist"` |
| `NEXT: /cleanup-agent-os` | `NEXT: /cleanup-geist` |

## Implementation

### Find all files with command references:
```bash
grep -rl "cleanup-agent-os" profiles/
```

### Replace patterns:
```bash
# Replace command invocations (with /)
sed -i '' 's/\/cleanup-agent-os/\/cleanup-geist/g' <filename>

# Replace remaining references (folder names, etc.)
sed -i '' 's/cleanup-agent-os/cleanup-geist/g' <filename>
```

### Bulk approach:
```bash
find profiles/ -type f -name "*.md" -exec sed -i '' 's/cleanup-agent-os/cleanup-geist/g' {} +
```

## Verification

After completion:

```bash
# These should return 0 results:
grep -r "cleanup-agent-os" profiles/
grep -r "/cleanup-agent-os" profiles/

# Verify new command references exist:
grep -r "cleanup-geist" profiles/ | head -10
grep -r "/cleanup-geist" profiles/ | head -10
```

## Acceptance Criteria

- [ ] `grep -r "cleanup-agent-os" profiles/` returns 0 results
- [ ] `grep -r "/cleanup-agent-os" profiles/` returns 0 results
- [ ] All navigation messages updated
- [ ] All NEXT directives updated
- [ ] Command documentation references updated

## Files Affected (Estimated ~15)

Based on analysis, these locations reference the cleanup command:
- Navigation sections in command files
- COMMAND-FLOWS.md
- Command reference documentation
- Workflow files that mention cleanup

## Notes

- The folder was already renamed in Task Group 1
- This task updates TEXT references to the command
- Check both `/cleanup-agent-os` (command invocation) and `cleanup-agent-os` (folder/file references)
- Some files may have multiple references
