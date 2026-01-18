# Prompt: Task Group 9 - Validation and Cleanup

## Objective

Validate that all renaming tasks were completed successfully and generate a final report.

## Context

This is the final task group. All previous renaming operations should be complete. This task verifies everything worked correctly.

## Tasks

### Task 9.1: Run Validation Grep Checks

Execute these commands and verify results:

```bash
echo "=== Checking @agent-os/ references ==="
grep -r "@agent-os/" profiles/
# Expected: 0 results

echo "=== Checking AGENT_OS_ variables ==="
grep -r "AGENT_OS_" profiles/
# Expected: 0 results

echo "=== Checking agent_os_ variables ==="
grep -r "agent_os_" profiles/
# Expected: 0 results

echo "=== Checking /cleanup-agent-os command ==="
grep -r "/cleanup-agent-os" profiles/
# Expected: 0 results

echo "=== Checking cleanup-agent-os references ==="
grep -r "cleanup-agent-os" profiles/
# Expected: 0 results

echo "=== Checking agent-os/ paths (excluding credits) ==="
grep -r "agent-os/" profiles/ | grep -v "Agent OS" | grep -v "buildermethods"
# Expected: 0 results (or only in specs folder)
```

### Task 9.2: Verify Folder Structure

```bash
echo "=== Verifying new folder exists ==="
ls -la profiles/default/commands/cleanup-geist/
# Expected: Directory listing

echo "=== Verifying old folder removed ==="
ls profiles/default/commands/cleanup-agent-os/ 2>&1
# Expected: "No such file or directory"
```

### Task 9.3: Verify File Existence

```bash
echo "=== Checking renamed command file ==="
ls profiles/default/commands/cleanup-geist/single-agent/cleanup-geist.md
# Expected: File exists

echo "=== Checking renamed doc file ==="
ls profiles/default/docs/command-references/cleanup-geist.md
# Expected: File exists

echo "=== Checking old files removed ==="
ls profiles/default/commands/cleanup-geist/single-agent/cleanup-agent-os.md 2>&1
ls profiles/default/docs/command-references/cleanup-agent-os.md 2>&1
# Expected: "No such file or directory" for both
```

### Task 9.4: Verify Credits Preserved

```bash
echo "=== Checking Agent OS credits preserved ==="
grep -r "Agent OS" profiles/ --include="*.md"
# Expected: Results in README files (credit sections)

echo "=== Checking buildermethods URL preserved ==="
grep -r "buildermethods.com/agent-os" profiles/ --include="*.md"
# Expected: Results in README files (credit sections)
```

### Task 9.5: Generate Validation Report

Create a validation report summarizing results:

```markdown
# Validation Report: Rename agent-os to geist

## Date: [Current Date]

## Validation Results

### Pattern Checks
| Pattern | Expected | Actual | Status |
|---------|----------|--------|--------|
| `@agent-os/` | 0 | [X] | ✅/❌ |
| `AGENT_OS_` | 0 | [X] | ✅/❌ |
| `agent_os_` | 0 | [X] | ✅/❌ |
| `/cleanup-agent-os` | 0 | [X] | ✅/❌ |
| `cleanup-agent-os` | 0 | [X] | ✅/❌ |
| `agent-os/` (non-credit) | 0 | [X] | ✅/❌ |

### Folder/File Checks
| Item | Expected | Status |
|------|----------|--------|
| `commands/cleanup-geist/` exists | Yes | ✅/❌ |
| `commands/cleanup-agent-os/` removed | Yes | ✅/❌ |
| `cleanup-geist.md` exists | Yes | ✅/❌ |
| `cleanup-agent-os.md` removed | Yes | ✅/❌ |
| `command-references/cleanup-geist.md` exists | Yes | ✅/❌ |
| `command-references/cleanup-agent-os.md` removed | Yes | ✅/❌ |

### Credit Preservation
| Item | Expected | Status |
|------|----------|--------|
| "Agent OS" in credits | Yes | ✅/❌ |
| buildermethods.com URL | Yes | ✅/❌ |

## Summary
- Total patterns checked: 6
- Passed: [X]
- Failed: [X]

## Issues Found
[List any issues that need manual fixing]

## Conclusion
[PASS/FAIL with notes]
```

## Acceptance Criteria

- [ ] All grep checks pass (0 results except allowed exceptions)
- [ ] New folder `profiles/default/commands/cleanup-geist/` exists
- [ ] Old folder `profiles/default/commands/cleanup-agent-os/` does NOT exist
- [ ] New file `cleanup-geist.md` exists in correct location
- [ ] Old file `cleanup-agent-os.md` does NOT exist
- [ ] New doc file `command-references/cleanup-geist.md` exists
- [ ] Old doc file `command-references/cleanup-agent-os.md` does NOT exist
- [ ] Credits preserved with "Agent OS" mentions
- [ ] buildermethods.com/agent-os URLs preserved
- [ ] Validation report generated

## If Issues Found

If any validation checks fail:
1. Document the specific issue
2. Identify which task group should have fixed it
3. Apply the fix manually
4. Re-run validation

## Final Steps

After all validations pass:
1. Save the validation report to `agent-os/specs/2026-01-18-rename-agent-os-to-geist/output/validation-report.md`
2. Mark all tasks as complete in `tasks.md`
3. The rename operation is complete!
