# Tasks: Rename agent-os to geist

## Overview

This document breaks down the rename specification into implementable task groups following the 6-phase implementation strategy.

---

## Task Group 1: Folder and File Renames

**Priority**: Critical (must be done first)
**Dependencies**: None
**Estimated Files**: 3 operations

### Tasks

- [ ] **1.1** Rename command folder `profiles/default/commands/cleanup-agent-os/` → `profiles/default/commands/cleanup-geist/`
- [ ] **1.2** Rename main command file `cleanup-agent-os.md` → `cleanup-geist.md` inside the renamed folder
- [ ] **1.3** Rename documentation file `profiles/default/docs/command-references/cleanup-agent-os.md` → `profiles/default/docs/command-references/cleanup-geist.md`

### Acceptance Criteria
- Folder `profiles/default/commands/cleanup-geist/` exists
- File `profiles/default/commands/cleanup-geist/single-agent/cleanup-geist.md` exists
- File `profiles/default/docs/command-references/cleanup-geist.md` exists
- Old paths no longer exist

---

## Task Group 2: Script Updates

**Priority**: Critical
**Dependencies**: None (can run parallel with Task Group 1)
**Estimated Files**: 3

### Tasks

- [ ] **2.1** Update `scripts/project-install.sh`
  - Change `AGENT_OS_DIR` → `GEIST_DIR`
  - Change `agent-os` folder target → `geist`
  - Update all variable references
  - Update echo messages

- [ ] **2.2** Update `scripts/project-update.sh`
  - Change `AGENT_OS_DIR` → `GEIST_DIR`
  - Change folder references from `agent-os` → `geist`
  - Update all variable references
  - Update echo messages

- [ ] **2.3** Update `scripts/common-functions.sh`
  - Change `AGENT_OS_PATH` → `GEIST_PATH`
  - Change default value from `agent-os` → `geist`
  - Update any function names containing `agent_os`

### Acceptance Criteria
- No `AGENT_OS_` or `agent_os_` variables remain in scripts
- No `agent-os` path references remain in scripts
- Scripts would create `geist/` folder when run

---

## Task Group 3: Config File Updates

**Priority**: High
**Dependencies**: None (can run parallel)
**Estimated Files**: 2

### Tasks

- [ ] **3.1** Update `profiles/default/config.yml`
  - Change `agent_os_commands` → `geist_commands`
  - Update any `agent-os` path references

- [ ] **3.2** Update `.gitignore` (if exists)
  - Change `agent-os/` → `geist/`

### Acceptance Criteria
- No `agent_os` or `agent-os` references in config files

---

## Task Group 4: @ Reference Updates

**Priority**: High
**Dependencies**: Task Group 1 (folder renames must be done first)
**Estimated Files**: ~27

### Tasks

- [ ] **4.1** Replace all `@agent-os/commands/` → `@geist/commands/`
- [ ] **4.2** Replace all `@agent-os/workflows/` → `@geist/workflows/`
- [ ] **4.3** Replace all `@agent-os/standards/` → `@geist/standards/`
- [ ] **4.4** Replace all `@agent-os/basepoints/` → `@geist/basepoints/`
- [ ] **4.5** Replace all `@agent-os/product/` → `@geist/product/`
- [ ] **4.6** Replace all `@agent-os/config/` → `@geist/config/`
- [ ] **4.7** Replace all `@agent-os/specs/` → `@geist/specs/`
- [ ] **4.8** Replace all `@agent-os/output/` → `@geist/output/`
- [ ] **4.9** Replace all `@agent-os/agents/` → `@geist/agents/`

### Acceptance Criteria
- `grep -r "@agent-os/" profiles/` returns 0 results

---

## Task Group 5: Variable Name Updates

**Priority**: High
**Dependencies**: Task Group 2 (scripts done first)
**Estimated Files**: ~11

### Tasks

- [ ] **5.1** Replace all `AGENT_OS_PATH` → `GEIST_PATH` in all files
- [ ] **5.2** Replace all `AGENT_OS_DIR` → `GEIST_DIR` in all files
- [ ] **5.3** Replace all `AGENT_OS_COMMANDS` → `GEIST_COMMANDS` in all files
- [ ] **5.4** Replace all `$AGENT_OS_PATH` → `$GEIST_PATH` in all files
- [ ] **5.5** Replace all `agent_os_dir` → `geist_dir` in all files
- [ ] **5.6** Replace all `agent_os_path` → `geist_path` in all files

### Acceptance Criteria
- `grep -r "AGENT_OS_" profiles/` returns 0 results
- `grep -r "agent_os_" profiles/` returns 0 results

---

## Task Group 6: Command Name Updates

**Priority**: High
**Dependencies**: Task Group 1 (folder/file renames done)
**Estimated Files**: ~15

### Tasks

- [ ] **6.1** Replace all `/cleanup-agent-os` → `/cleanup-geist` (command invocations)
- [ ] **6.2** Replace all `cleanup-agent-os` → `cleanup-geist` (remaining references)
- [ ] **6.3** Update navigation messages: `"Run /cleanup-agent-os"` → `"Run /cleanup-geist"`
- [ ] **6.4** Update NEXT directives: `"NEXT: /cleanup-agent-os"` → `"NEXT: /cleanup-geist"`

### Acceptance Criteria
- `grep -r "cleanup-agent-os" profiles/` returns 0 results
- `grep -r "/cleanup-agent-os" profiles/` returns 0 results

---

## Task Group 7: Path Reference Updates

**Priority**: High
**Dependencies**: Task Groups 1-6 (do this after specific patterns)
**Estimated Files**: ~198

### Tasks

- [ ] **7.1** Replace all `agent-os/commands` → `geist/commands`
- [ ] **7.2** Replace all `agent-os/workflows` → `geist/workflows`
- [ ] **7.3** Replace all `agent-os/basepoints` → `geist/basepoints`
- [ ] **7.4** Replace all `agent-os/product` → `geist/product`
- [ ] **7.5** Replace all `agent-os/config` → `geist/config`
- [ ] **7.6** Replace all `agent-os/specs` → `geist/specs`
- [ ] **7.7** Replace all `agent-os/output` → `geist/output`
- [ ] **7.8** Replace all remaining `agent-os/` → `geist/` paths
- [ ] **7.9** Replace all `"agent-os"` → `"geist"` (quoted strings)

### Acceptance Criteria
- `grep -r "agent-os/" profiles/` returns 0 results (except credits)

---

## Task Group 8: Documentation Updates

**Priority**: Medium
**Dependencies**: Task Groups 1-7
**Estimated Files**: ~35

### Tasks

- [ ] **8.1** Update `README.md` (root)
  - Update installation examples
  - Update folder structure diagrams
  - Update command examples
  - PRESERVE credit section

- [ ] **8.2** Update `profiles/default/README.md`
  - Update all references
  - PRESERVE credit section

- [ ] **8.3** Update `MANIFEST.md`
  - Update folder structure references

- [ ] **8.4** Update `COMMANDS-WORKFLOW-MAP.md`
  - Update all path references
  - Update command references

- [ ] **8.5** Update `profiles/default/docs/COMMAND-FLOWS.md`
  - Update all command and path references

- [ ] **8.6** Update all files in `profiles/default/docs/command-references/`
  - Update path references
  - Update command examples

- [ ] **8.7** Review and update any remaining documentation files

### Acceptance Criteria
- All documentation reflects `geist` naming
- Credit sections preserved with "Agent OS" mentions
- No broken internal references

---

## Task Group 9: Validation and Cleanup

**Priority**: Critical (final step)
**Dependencies**: All previous task groups
**Estimated Files**: N/A (validation only)

### Tasks

- [ ] **9.1** Run validation grep checks:
  ```bash
  grep -r "@agent-os/" profiles/
  grep -r "AGENT_OS_" profiles/
  grep -r "agent_os_" profiles/
  grep -r "/cleanup-agent-os" profiles/
  grep -r "agent-os/" profiles/ | grep -v "Agent OS" | grep -v "buildermethods"
  ```

- [ ] **9.2** Verify folder structure:
  - Confirm `profiles/default/commands/cleanup-geist/` exists
  - Confirm `profiles/default/commands/cleanup-agent-os/` does NOT exist

- [ ] **9.3** Verify file existence:
  - `profiles/default/commands/cleanup-geist/single-agent/cleanup-geist.md`
  - `profiles/default/docs/command-references/cleanup-geist.md`

- [ ] **9.4** Verify credits preserved:
  - Search for "Agent OS" in credit contexts
  - Verify buildermethods.com/agent-os links intact

- [ ] **9.5** Generate validation report

### Acceptance Criteria
- All grep checks pass (0 results except allowed exceptions)
- All renamed files/folders exist
- All old files/folders removed
- Credits preserved

---

## Execution Order

```
┌─────────────────────────────────────────────────────────────┐
│                    PARALLEL EXECUTION                        │
├─────────────────────────────────────────────────────────────┤
│  Task Group 1        Task Group 2        Task Group 3       │
│  (Folder Renames)    (Scripts)           (Config)           │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    SEQUENTIAL EXECUTION                      │
├─────────────────────────────────────────────────────────────┤
│  Task Group 4: @ References                                  │
│  Task Group 5: Variable Names                                │
│  Task Group 6: Command Names                                 │
│  Task Group 7: Path References (most general - do last)      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  Task Group 8: Documentation Updates                         │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  Task Group 9: Validation and Cleanup                        │
└─────────────────────────────────────────────────────────────┘
```

---

## Summary

| Task Group | Description | Files | Priority |
|------------|-------------|-------|----------|
| 1 | Folder/File Renames | 3 ops | Critical |
| 2 | Script Updates | 3 | Critical |
| 3 | Config Updates | 2 | High |
| 4 | @ Reference Updates | ~27 | High |
| 5 | Variable Name Updates | ~11 | High |
| 6 | Command Name Updates | ~15 | High |
| 7 | Path Reference Updates | ~198 | High |
| 8 | Documentation Updates | ~35 | Medium |
| 9 | Validation | N/A | Critical |

**Total: 9 Task Groups, ~290 file operations, 1,874 text replacements**
