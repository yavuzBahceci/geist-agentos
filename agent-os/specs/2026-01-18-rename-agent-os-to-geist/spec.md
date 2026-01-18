# Specification: Rename agent-os to geist

## Overview

This specification details the complete renaming of all "agent-os" references throughout the Geist repository to "geist", establishing a consistent brand identity while preserving attribution to the original Agent OS project.

---

## Goals

1. **Brand Consistency**: Establish "Geist" as the sole product name throughout the codebase
2. **Clean Installation**: Projects install to `geist/` folder, not `agent-os/`
3. **Preserved Attribution**: Maintain credit to Agent OS in documentation
4. **Zero Breakage**: All references, paths, and commands continue to work

---

## Non-Goals

- Changing the underlying architecture or functionality
- Modifying the credit/attribution to Agent OS
- Renaming the repository itself (already named Geist-v1)

---

## Technical Specification

### 1. Folder Structure Changes

#### 1.1 Command Folder Rename

**Before:**
```
profiles/default/commands/
├── cleanup-agent-os/
│   └── single-agent/
│       ├── cleanup-agent-os.md
│       ├── 1-validate-prerequisites-and-run-validation.md
│       ├── 2-clean-placeholders.md
│       ├── 3-remove-unnecessary-logic.md
│       ├── 4-fix-broken-references.md
│       ├── 5-verify-knowledge-completeness.md
│       └── 6-generate-cleanup-report.md
```

**After:**
```
profiles/default/commands/
├── cleanup-geist/
│   └── single-agent/
│       ├── cleanup-geist.md
│       ├── 1-validate-prerequisites-and-run-validation.md
│       ├── 2-clean-placeholders.md
│       ├── 3-remove-unnecessary-logic.md
│       ├── 4-fix-broken-references.md
│       ├── 5-verify-knowledge-completeness.md
│       └── 6-generate-cleanup-report.md
```

#### 1.2 Documentation File Rename

**Before:**
```
profiles/default/docs/command-references/
├── cleanup-agent-os.md
```

**After:**
```
profiles/default/docs/command-references/
├── cleanup-geist.md
```

#### 1.3 Installation Target

**Before:** Scripts create `your-project/agent-os/`
**After:** Scripts create `your-project/geist/`

---

### 2. Reference Pattern Changes

#### 2.1 @ Reference Pattern

| Before | After |
|--------|-------|
| `@agent-os/commands/` | `@geist/commands/` |
| `@agent-os/workflows/` | `@geist/workflows/` |
| `@agent-os/standards/` | `@geist/standards/` |
| `@agent-os/basepoints/` | `@geist/basepoints/` |
| `@agent-os/product/` | `@geist/product/` |
| `@agent-os/config/` | `@geist/config/` |
| `@agent-os/specs/` | `@geist/specs/` |
| `@agent-os/output/` | `@geist/output/` |
| `@agent-os/agents/` | `@geist/agents/` |

#### 2.2 Path String Pattern

| Before | After |
|--------|-------|
| `agent-os/commands` | `geist/commands` |
| `agent-os/workflows` | `geist/workflows` |
| `agent-os/basepoints` | `geist/basepoints` |
| `agent-os/product` | `geist/product` |
| `agent-os/config` | `geist/config` |
| `agent-os/specs` | `geist/specs` |
| `agent-os/output` | `geist/output` |
| `"agent-os"` (quoted) | `"geist"` |

---

### 3. Variable Name Changes

#### 3.1 Environment Variables

| Before | After |
|--------|-------|
| `AGENT_OS_PATH` | `GEIST_PATH` |
| `AGENT_OS_COMMANDS` | `GEIST_COMMANDS` |
| `AGENT_OS_DIR` | `GEIST_DIR` |

#### 3.2 Script Variables

| Before | After |
|--------|-------|
| `agent_os_dir` | `geist_dir` |
| `agent_os_path` | `geist_path` |
| `$AGENT_OS_PATH` | `$GEIST_PATH` |

#### 3.3 Config Keys

| Before | After |
|--------|-------|
| `agent_os_commands:` | `geist_commands:` |

---

### 4. Command Name Changes

#### 4.1 Command Rename

| Before | After |
|--------|-------|
| `/cleanup-agent-os` | `/cleanup-geist` |
| `cleanup-agent-os` (folder) | `cleanup-geist` |
| `cleanup-agent-os.md` (file) | `cleanup-geist.md` |

#### 4.2 Navigation Messages

| Before | After |
|--------|-------|
| `"Run /cleanup-agent-os"` | `"Run /cleanup-geist"` |
| `"NEXT: /cleanup-agent-os"` | `"NEXT: /cleanup-geist"` |

---

### 5. Script Changes

#### 5.1 project-install.sh

```bash
# Before
AGENT_OS_DIR="$PROJECT_DIR/agent-os"
mkdir -p "$AGENT_OS_DIR"

# After
GEIST_DIR="$PROJECT_DIR/geist"
mkdir -p "$GEIST_DIR"
```

#### 5.2 project-update.sh

```bash
# Before
AGENT_OS_DIR="$PROJECT_DIR/agent-os"
if [ ! -d "$AGENT_OS_DIR" ]; then

# After
GEIST_DIR="$PROJECT_DIR/geist"
if [ ! -d "$GEIST_DIR" ]; then
```

#### 5.3 common-functions.sh

```bash
# Before
AGENT_OS_PATH="${AGENT_OS_PATH:-agent-os}"

# After
GEIST_PATH="${GEIST_PATH:-geist}"
```

---

### 6. Config File Changes

#### 6.1 config.yml

```yaml
# Before
agent_os_commands: true

# After
geist_commands: true
```

#### 6.2 .gitignore

```gitignore
# Before
agent-os/

# After  
geist/
```

---

### 7. Documentation Changes

#### 7.1 Text Replacements

| Context | Before | After |
|---------|--------|-------|
| Product name | "Agent OS" | "Geist" |
| Folder reference | "agent-os folder" | "geist folder" |
| Deployment | "Agent OS deployment" | "Geist deployment" |
| Installation | "Install Agent OS" | "Install Geist" |

#### 7.2 Preserved Attribution (DO NOT CHANGE)

These patterns must remain unchanged:
- `"Built on Agent OS by Brian Casel"`
- `"concepts from Agent OS"`
- `"https://buildermethods.com/agent-os"`
- `"Agent OS" in credit sections`

---

### 8. Files Affected by Category

#### 8.1 Scripts (3 files)
- `scripts/project-install.sh`
- `scripts/project-update.sh`
- `scripts/common-functions.sh`

#### 8.2 Config (2 files)
- `config.yml`
- `.gitignore`

#### 8.3 Commands (~50 files)
All files in `profiles/default/commands/`

#### 8.4 Workflows (~60 files)
All files in `profiles/default/workflows/`

#### 8.5 Standards (~10 files)
All files in `profiles/default/standards/`

#### 8.6 Documentation (~30 files)
All files in `profiles/default/docs/`

#### 8.7 Root Documentation (5 files)
- `README.md`
- `MANIFEST.md`
- `CONTRIBUTING.md`
- `LICENSE`
- `COMMANDS-WORKFLOW-MAP.md`

---

## Implementation Strategy

### Phase 1: Folder/File Renames
1. Rename `commands/cleanup-agent-os/` → `commands/cleanup-geist/`
2. Rename `cleanup-agent-os.md` → `cleanup-geist.md`
3. Rename `docs/command-references/cleanup-agent-os.md` → `cleanup-geist.md`

### Phase 2: Script Updates
1. Update `project-install.sh`
2. Update `project-update.sh`
3. Update `common-functions.sh`

### Phase 3: Config Updates
1. Update `config.yml`
2. Update `.gitignore`

### Phase 4: Bulk Text Replacement
Execute in order (most specific first):
1. `@agent-os/` → `@geist/`
2. `AGENT_OS_` → `GEIST_`
3. `agent_os_` → `geist_`
4. `/cleanup-agent-os` → `/cleanup-geist`
5. `cleanup-agent-os` → `cleanup-geist`
6. `agent-os/` → `geist/` (paths)
7. `"agent-os"` → `"geist"` (quoted strings)

### Phase 5: Documentation Review
1. Verify credits preserved
2. Update any remaining product name references
3. Fix any broken links

### Phase 6: Validation
1. Grep for remaining `agent-os` (except credits)
2. Test installation script
3. Verify all references resolve

---

## Validation Checklist

### Must Pass
- [ ] `grep -r "@agent-os/" profiles/` returns 0 results
- [ ] `grep -r "AGENT_OS_" profiles/` returns 0 results
- [ ] `grep -r "agent_os_" profiles/` returns 0 results
- [ ] `grep -r "/cleanup-agent-os" profiles/` returns 0 results
- [ ] `grep -r "agent-os/" profiles/` returns 0 results (except credits)
- [ ] Folder `profiles/default/commands/cleanup-geist/` exists
- [ ] File `profiles/default/commands/cleanup-geist/single-agent/cleanup-geist.md` exists
- [ ] File `profiles/default/docs/command-references/cleanup-geist.md` exists

### Allowed Exceptions
- `"Agent OS"` in credit sections
- `buildermethods.com/agent-os` URLs
- Attribution text mentioning Agent OS

---

## Rollback Plan

If issues are discovered:
1. Git revert to pre-rename commit
2. Identify specific issue
3. Fix and re-apply changes

---

## Success Metrics

1. **Zero grep matches** for `agent-os` patterns (except credits)
2. **All commands work** with new naming
3. **Installation creates** `geist/` folder
4. **Documentation accurate** with new naming
5. **Credits preserved** for Agent OS

---

## Appendix: Full Replacement Matrix

| Pattern | Replacement | Files Affected |
|---------|-------------|----------------|
| `@agent-os/` | `@geist/` | ~27 |
| `AGENT_OS_` | `GEIST_` | ~6 |
| `agent_os_` | `geist_` | ~5 |
| `cleanup-agent-os` | `cleanup-geist` | ~15 |
| `agent-os/` (path) | `geist/` | ~198 |
| `"agent-os"` | `"geist"` | ~10 |

**Total estimated changes: 1,874 occurrences across 198 files**
