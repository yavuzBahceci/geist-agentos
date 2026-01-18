# Prompt: Task Group 1 - Folder and File Renames

## Objective

Rename the `cleanup-agent-os` command folder and files to `cleanup-geist`.

## Context

This is the first step in renaming all "agent-os" references to "geist". These folder/file renames must be done before text replacements to avoid broken references.

## Tasks

### Task 1.1: Rename Command Folder

Rename the entire command folder:
```
profiles/default/commands/cleanup-agent-os/ → profiles/default/commands/cleanup-geist/
```

**Command:**
```bash
mv profiles/default/commands/cleanup-agent-os profiles/default/commands/cleanup-geist
```

### Task 1.2: Rename Main Command File

Inside the renamed folder, rename the main command file:
```
profiles/default/commands/cleanup-geist/single-agent/cleanup-agent-os.md → cleanup-geist.md
```

**Command:**
```bash
mv profiles/default/commands/cleanup-geist/single-agent/cleanup-agent-os.md profiles/default/commands/cleanup-geist/single-agent/cleanup-geist.md
```

### Task 1.3: Rename Documentation File

Rename the command reference documentation:
```
profiles/default/docs/command-references/cleanup-agent-os.md → cleanup-geist.md
```

**Command:**
```bash
mv profiles/default/docs/command-references/cleanup-agent-os.md profiles/default/docs/command-references/cleanup-geist.md
```

## Verification

After completion, verify:

```bash
# These should exist:
ls profiles/default/commands/cleanup-geist/
ls profiles/default/commands/cleanup-geist/single-agent/cleanup-geist.md
ls profiles/default/docs/command-references/cleanup-geist.md

# These should NOT exist:
ls profiles/default/commands/cleanup-agent-os/  # Should fail
ls profiles/default/docs/command-references/cleanup-agent-os.md  # Should fail
```

## Acceptance Criteria

- [ ] Folder `profiles/default/commands/cleanup-geist/` exists
- [ ] File `profiles/default/commands/cleanup-geist/single-agent/cleanup-geist.md` exists
- [ ] File `profiles/default/docs/command-references/cleanup-geist.md` exists
- [ ] Old folder `profiles/default/commands/cleanup-agent-os/` does NOT exist
- [ ] Old file `profiles/default/docs/command-references/cleanup-agent-os.md` does NOT exist

## Notes

- Do NOT modify file contents yet - that happens in later task groups
- The files inside the folder keep their original names (except cleanup-agent-os.md)
- Files like `1-validate-prerequisites-and-run-validation.md` stay as-is
