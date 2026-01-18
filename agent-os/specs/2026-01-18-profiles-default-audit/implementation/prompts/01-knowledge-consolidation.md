# Task Group 1: Knowledge Consolidation

## Overview

**Goal**: Clarify and optimize the relationship between `config/enriched-knowledge/` and `basepoints/libraries/`

## Context

The Geist system has two knowledge systems that contain library information:
1. `config/enriched-knowledge/` - Created by `adapt-to-product`, contains generic web research
2. `basepoints/libraries/` - Created by `create-basepoints`, contains project-specific knowledge

This task group merges security and version information from enriched-knowledge into library basepoints.

## Standards to Follow

- @agent-os/standards/global/conventions.md
- @agent-os/standards/global/codebase-analysis.md
- @agent-os/standards/documentation/standards.md

## Tasks

### Task 1.1: Update generate-library-basepoints.md to read security info

**File**: `profiles/default/workflows/codebase-analysis/generate-library-basepoints.md`

**Changes**:
1. Add a step after "Step 5: Research Official Documentation" to check for `config/enriched-knowledge/security-notes.md`
2. Extract relevant CVEs and security notes for each library being processed
3. Store in a variable like `$SECURITY_NOTES`

**Implementation**:
```bash
### Step 5.5: Extract Security Information from Enriched Knowledge

SECURITY_NOTES=""
if [ -f "$AGENT_OS_PATH/config/enriched-knowledge/security-notes.md" ]; then
    echo "🔒 Extracting security notes for $LIBRARY_NAME..."
    # Search for library-specific security notes
    SECURITY_NOTES=$(grep -A 20 -i "$LIBRARY_NAME" "$AGENT_OS_PATH/config/enriched-knowledge/security-notes.md" | head -25)
fi
```

### Task 1.2: Update generate-library-basepoints.md to read version info

**File**: `profiles/default/workflows/codebase-analysis/generate-library-basepoints.md`

**Changes**:
1. Add a step to check for `config/enriched-knowledge/version-analysis.md`
2. Extract version status for each library
3. Store in a variable like `$VERSION_STATUS`

**Implementation**:
```bash
### Step 5.6: Extract Version Information from Enriched Knowledge

VERSION_STATUS=""
if [ -f "$AGENT_OS_PATH/config/enriched-knowledge/version-analysis.md" ]; then
    echo "📦 Extracting version status for $LIBRARY_NAME..."
    # Search for library-specific version info
    VERSION_STATUS=$(grep -A 10 -i "$LIBRARY_NAME" "$AGENT_OS_PATH/config/enriched-knowledge/version-analysis.md" | head -15)
fi
```

### Task 1.3: Update library basepoint template

**File**: `profiles/default/workflows/codebase-analysis/generate-library-basepoints.md`

**Changes**: Update the template in "Step 8: Generate Library Basepoint Files" to include new sections.

**New template sections** (add after "## Best Practices"):
```markdown
## Security Notes

[Security vulnerabilities, CVEs, and security considerations for this library]

$SECURITY_NOTES

## Version Status

[Current version, latest available, upgrade recommendations]

$VERSION_STATUS
```

### Task 1.4: Create KNOWLEDGE-SYSTEMS.md documentation

**File**: `profiles/default/docs/KNOWLEDGE-SYSTEMS.md`

**Content**:
- Title: "Knowledge Systems Reference"
- Section 1: Overview of the two knowledge systems
- Section 2: When each is created (with timeline diagram)
- Section 3: What each contains
- Section 4: When each is used (with command table)
- Section 5: The merge strategy
- Section 6: Visual diagram showing data flow

## Acceptance Criteria

- [ ] `generate-library-basepoints.md` reads from `security-notes.md` if it exists
- [ ] `generate-library-basepoints.md` reads from `version-analysis.md` if it exists
- [ ] Library basepoint template includes "Security Notes" and "Version Status" sections
- [ ] `KNOWLEDGE-SYSTEMS.md` exists with clear documentation
- [ ] All changes follow the conventions standard

## Files to Modify

1. `profiles/default/workflows/codebase-analysis/generate-library-basepoints.md`
2. NEW: `profiles/default/docs/KNOWLEDGE-SYSTEMS.md`

## Validation

After completing this task group:
1. Review `generate-library-basepoints.md` to confirm new steps are added
2. Verify the template includes new sections
3. Verify `KNOWLEDGE-SYSTEMS.md` exists and is comprehensive
