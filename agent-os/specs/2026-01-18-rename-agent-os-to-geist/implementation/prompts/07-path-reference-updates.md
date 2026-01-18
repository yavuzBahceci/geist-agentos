# Prompt: Task Group 7 - Path Reference Updates

## Objective

Replace all remaining `agent-os/` path references with `geist/` throughout the codebase.

## Context

This is the most general replacement and should be done LAST to avoid interfering with more specific patterns (which were handled in Task Groups 4-6).

## Scope

All files in `profiles/` directory with `agent-os/` path references.

## Tasks

### Task 7.1-7.9: Replace Path References

Replace ALL occurrences of these patterns:

| Find | Replace |
|------|---------|
| `agent-os/commands` | `geist/commands` |
| `agent-os/workflows` | `geist/workflows` |
| `agent-os/basepoints` | `geist/basepoints` |
| `agent-os/product` | `geist/product` |
| `agent-os/config` | `geist/config` |
| `agent-os/specs` | `geist/specs` |
| `agent-os/output` | `geist/output` |
| `agent-os/agents` | `geist/agents` |
| `agent-os/` (remaining) | `geist/` |
| `"agent-os"` | `"geist"` |

## Implementation

### Find all files with path references:
```bash
grep -rl "agent-os/" profiles/
grep -rl '"agent-os"' profiles/
```

### Replace patterns:
```bash
# Replace all agent-os/ paths
sed -i '' 's/agent-os\//geist\//g' <filename>

# Replace quoted "agent-os"
sed -i '' 's/"agent-os"/"geist"/g' <filename>
```

### Bulk approach:
```bash
# Replace path references
find profiles/ -type f \( -name "*.md" -o -name "*.yml" -o -name "*.sh" \) -exec sed -i '' 's/agent-os\//geist\//g' {} +

# Replace quoted strings
find profiles/ -type f \( -name "*.md" -o -name "*.yml" -o -name "*.sh" \) -exec sed -i '' 's/"agent-os"/"geist"/g' {} +
```

## IMPORTANT: Preserve Credits

Do NOT replace these patterns (they should remain as "Agent OS"):
- `"Agent OS"` (capitalized, product name in credits)
- `buildermethods.com/agent-os` (URL to original project)
- `"Built on Agent OS"` or similar attribution text

### How to handle:
1. Run the bulk replacement
2. Check credit sections are intact
3. If credits were modified, restore them manually

### Credit patterns to preserve:
```markdown
## Credits
Built on [Agent OS](https://buildermethods.com/agent-os) by Brian Casel
```

## Verification

After completion:

```bash
# This should return 0 results (except credits):
grep -r "agent-os/" profiles/ | grep -v "Agent OS" | grep -v "buildermethods"

# Verify credits are preserved:
grep -r "Agent OS" profiles/
grep -r "buildermethods.com/agent-os" profiles/

# Verify new paths exist:
grep -r "geist/" profiles/ | head -20
```

## Acceptance Criteria

- [ ] `grep -r "agent-os/" profiles/` returns only credit-related results
- [ ] All path references updated to `geist/`
- [ ] All quoted `"agent-os"` strings updated to `"geist"`
- [ ] Credit sections preserved with "Agent OS" mentions
- [ ] `buildermethods.com/agent-os` URLs preserved

## Files Affected (Estimated ~198)

This is the largest task group, affecting most files in the profiles directory.

## Notes

- This is the MOST GENERAL replacement - do it last
- Be very careful to preserve credit sections
- The `agent-os/` pattern with trailing slash helps avoid matching "Agent OS" (no slash)
- Double-check README files which have credit sections
