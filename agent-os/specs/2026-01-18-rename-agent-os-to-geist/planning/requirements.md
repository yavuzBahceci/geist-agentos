# Requirements: Rename agent-os to geist

## Overview

Rename all "agent-os" references throughout the repository to "geist" while preserving credit to the original Agent OS project in documentation sections.

---

## Scope Analysis

### Files Affected

Based on codebase analysis:
- **Total matches**: 1,874 occurrences across 198 files
- **@agent-os references**: 27 files
- **AGENT_OS variables**: 6 files
- **agent_os variables**: 5 files
- **cleanup-agent-os command**: 15 files (needs special handling - rename command itself)

### Categories of Changes

1. **Folder Names**
   - `agent-os/` → `geist/` (the installed folder in projects)
   - `cleanup-agent-os/` → `cleanup-geist/` (command folder)

2. **File References**
   - `@agent-os/` → `@geist/`
   - `agent-os/` paths → `geist/` paths

3. **Variable Names**
   - `AGENT_OS_*` → `GEIST_*`
   - `agent_os_*` → `geist_*`
   - `agent-os-*` → `geist-*`

4. **Command Names**
   - `/cleanup-agent-os` → `/cleanup-geist`
   - References to "cleanup-agent-os" command

5. **Documentation Text**
   - "Agent OS" as a product name → "Geist" (except in credits)
   - "agent-os" in descriptions → "geist"

---

## Requirements

### R1: Folder Renaming

#### R1.1: Installation Target Folder
- **Current**: Scripts create `agent-os/` folder in target project
- **New**: Scripts must create `geist/` folder instead
- **Files**: `scripts/project-install.sh`, `scripts/project-update.sh`, `scripts/common-functions.sh`

#### R1.2: Command Folder Renaming
- **Current**: `profiles/default/commands/cleanup-agent-os/`
- **New**: `profiles/default/commands/cleanup-geist/`
- **Impact**: All references to this command path must be updated

#### R1.3: Documentation Command References
- **Current**: `docs/command-references/cleanup-agent-os.md`
- **New**: `docs/command-references/cleanup-geist.md`

---

### R2: Path Reference Updates

#### R2.1: @ References
Update all `@agent-os/` references to `@geist/`:
```
@agent-os/commands/... → @geist/commands/...
@agent-os/workflows/... → @geist/workflows/...
@agent-os/standards/... → @geist/standards/...
@agent-os/basepoints/... → @geist/basepoints/...
@agent-os/product/... → @geist/product/...
@agent-os/config/... → @geist/config/...
@agent-os/specs/... → @geist/specs/...
```

#### R2.2: Path Strings
Update all path strings:
```
agent-os/commands → geist/commands
agent-os/workflows → geist/workflows
agent-os/basepoints → geist/basepoints
agent-os/product → geist/product
agent-os/config → geist/config
agent-os/specs → geist/specs
agent-os/output → geist/output
```

---

### R3: Variable Renaming

#### R3.1: Environment Variables
```bash
AGENT_OS_PATH → GEIST_PATH
AGENT_OS_COMMANDS → GEIST_COMMANDS
AGENT_OS_DIR → GEIST_DIR
```

#### R3.2: Script Variables
```bash
agent_os_dir → geist_dir
agent_os_path → geist_path
```

#### R3.3: Config Keys
```yaml
agent_os_commands → geist_commands
```

---

### R4: Command Name Updates

#### R4.1: cleanup-agent-os → cleanup-geist
- Rename folder: `commands/cleanup-agent-os/` → `commands/cleanup-geist/`
- Rename main file: `cleanup-agent-os.md` → `cleanup-geist.md`
- Update all references to the command name
- Update navigation messages ("Run /cleanup-agent-os" → "Run /cleanup-geist")

#### R4.2: Command References in Documentation
Update all documentation that references:
- `/cleanup-agent-os` → `/cleanup-geist`
- "cleanup-agent-os command" → "cleanup-geist command"

---

### R5: Documentation Updates

#### R5.1: Product Name References
- "Agent OS" as the product being installed → "Geist"
- "agent-os folder" → "geist folder"
- "Agent OS deployment" → "Geist deployment"

#### R5.2: Preserve Credits (EXCLUSIONS)
Keep "Agent OS" in these contexts ONLY:
- Credit sections: "Built on Agent OS by Brian Casel"
- Links to original: "https://buildermethods.com/agent-os"
- Historical references: "concepts from Agent OS"

#### R5.3: README Updates
- Update installation examples
- Update folder structure diagrams
- Update command examples

---

### R6: Script Updates

#### R6.1: project-install.sh
- Change target folder from `agent-os` to `geist`
- Update help text
- Update variable names
- Update echo messages

#### R6.2: project-update.sh
- Change target folder references
- Update variable names
- Update messages

#### R6.3: common-functions.sh
- Update function names if any contain "agent_os"
- Update variable names
- Update path references

---

### R7: Config File Updates

#### R7.1: config.yml
- Update any `agent_os` keys to `geist`
- Update path references

#### R7.2: .gitignore
- Update any agent-os specific ignores

---

## Validation Criteria

### V1: No Remaining References
After completion, the following grep patterns should return NO results (except in credit sections):
- `agent-os/` (as a path)
- `@agent-os/`
- `AGENT_OS_`
- `agent_os_`
- `/cleanup-agent-os`

### V2: Allowed Exceptions
These patterns ARE allowed to remain:
- "Agent OS" in credit/acknowledgment sections
- "buildermethods.com/agent-os" URLs
- "based on Agent OS" or similar attribution text

### V3: Functional Validation
- Scripts should create `geist/` folder, not `agent-os/`
- All `@geist/` references should resolve correctly
- `/cleanup-geist` command should work

---

## Implementation Order

1. **Phase 1**: Rename folders
   - `commands/cleanup-agent-os/` → `commands/cleanup-geist/`
   - `docs/command-references/cleanup-agent-os.md` → `cleanup-geist.md`

2. **Phase 2**: Update scripts
   - `project-install.sh`
   - `project-update.sh`
   - `common-functions.sh`

3. **Phase 3**: Update config files
   - `config.yml`
   - `.gitignore`

4. **Phase 4**: Bulk replace in all files
   - `@agent-os/` → `@geist/`
   - `agent-os/` → `geist/` (paths)
   - `AGENT_OS_` → `GEIST_`
   - `agent_os_` → `geist_`
   - `/cleanup-agent-os` → `/cleanup-geist`
   - `cleanup-agent-os` → `cleanup-geist`

5. **Phase 5**: Documentation cleanup
   - Update README files
   - Update COMMAND-FLOWS.md
   - Update all docs/
   - Ensure credits preserved

6. **Phase 6**: Validation
   - Run grep checks
   - Test script execution
   - Verify no broken references

---

## Risk Mitigation

### R1: Backup
- Commit all current changes before starting
- Create a branch for the rename

### R2: Incremental Commits
- Commit after each phase
- Allow rollback if issues found

### R3: Testing
- Test installation script after changes
- Verify command references work

---

## Files to Rename (Folders/Files)

### Folders
1. `profiles/default/commands/cleanup-agent-os/` → `cleanup-geist/`

### Files
1. `profiles/default/commands/cleanup-geist/single-agent/cleanup-agent-os.md` → `cleanup-geist.md`
2. `profiles/default/docs/command-references/cleanup-agent-os.md` → `cleanup-geist.md`

---

## Estimated Impact

| Category | Count |
|----------|-------|
| Total file matches | 198 |
| Total occurrences | 1,874 |
| Folders to rename | 1 |
| Files to rename | 2 |
| Scripts to update | 3 |
| Config files | 2 |

---

## Success Criteria

1. ✅ All `agent-os` folder references changed to `geist`
2. ✅ All `@agent-os/` references changed to `@geist/`
3. ✅ All variable names updated
4. ✅ `cleanup-agent-os` command renamed to `cleanup-geist`
5. ✅ Scripts create `geist/` folder
6. ✅ Credits to original Agent OS preserved
7. ✅ No broken references
8. ✅ Documentation accurate
