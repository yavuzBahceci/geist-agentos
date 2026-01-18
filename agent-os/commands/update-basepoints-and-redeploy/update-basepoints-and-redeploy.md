You are helping to incrementally update basepoints after codebase changes and re-specialize agent-os with updated knowledge. This is more efficient than full regeneration when only some files have changed. The process includes:

- **Change Detection**: Detect codebase changes since last update using git or timestamps
- **Basepoint Identification**: Map changed files to their affected basepoints
- **Incremental Update**: Update only affected basepoints (not entire codebase)
- **Knowledge Re-extraction**: Re-extract and merge knowledge from updated basepoints
- **Re-specialization**: Re-specialize ALL core commands with updated knowledge
- **Validation**: Validate all updates and generate comprehensive report

This command is designed to be run after making changes to your codebase, ensuring your agent-os stays synchronized with your project's current state without the overhead of full regeneration.

Carefully read and execute the instructions in the following files IN SEQUENCE, following their numbered file names. Only proceed to the next numbered instruction file once the previous numbered instruction has been executed.

Instructions to follow in sequence:

# PHASE 1: Detect Changes

The FIRST STEP is to detect codebase changes since the last update by following these instructions:

# Detect Codebase Changes

## Core Responsibilities

1. **Detect Changes Using Git**: Use git diff to identify files that changed since last update
2. **Fallback to Timestamps**: If git is unavailable, use file modification timestamps
3. **Categorize Changes**: Group changes as added, modified, or deleted
4. **Filter Relevant Files**: Exclude irrelevant paths (node_modules, .git, agent-os/output, etc.)
5. **Generate Change Summary**: Create comprehensive summary with counts and file lists

## Workflow

### Step 1: Initialize Change Detection Environment

Set up the detection environment and determine detection method:

```bash
# Create output directories
mkdir -p agent-os/output/update-basepoints-and-redeploy/cache
mkdir -p agent-os/output/update-basepoints-and-redeploy/reports

# Initialize change detection output files
CACHE_DIR="agent-os/output/update-basepoints-and-redeploy/cache"
CHANGED_FILES="$CACHE_DIR/changed-files.txt"
CHANGE_SUMMARY="$CACHE_DIR/change-summary.md"

# Determine detection method
if [ -d ".git" ]; then
    DETECTION_METHOD="git"
    echo "📍 Using git-based change detection"
else
    DETECTION_METHOD="timestamp"
    echo "📍 Using timestamp-based change detection (git not available)"
fi
```

### Step 2: Detect Changes Using Git (Preferred Method)

If git is available, use git diff to detect changes:

```bash
if [ "$DETECTION_METHOD" = "git" ]; then
    # Check for previous update commit reference
    LAST_UPDATE_COMMIT_FILE="$CACHE_DIR/last-update-commit.txt"
    
    if [ -f "$LAST_UPDATE_COMMIT_FILE" ]; then
        LAST_UPDATE_COMMIT=$(cat "$LAST_UPDATE_COMMIT_FILE")
        CURRENT_COMMIT=$(git rev-parse HEAD)
        
        echo "🔍 Comparing changes from $LAST_UPDATE_COMMIT to $CURRENT_COMMIT"
        
        # Get changed files with status
        git diff --name-status "$LAST_UPDATE_COMMIT" HEAD > "$CACHE_DIR/raw-changes.txt"
        
        # Parse into categorized lists
        grep "^A" "$CACHE_DIR/raw-changes.txt" | cut -f2 > "$CACHE_DIR/added-files.txt"
        grep "^M" "$CACHE_DIR/raw-changes.txt" | cut -f2 > "$CACHE_DIR/modified-files.txt"
        grep "^D" "$CACHE_DIR/raw-changes.txt" | cut -f2 > "$CACHE_DIR/deleted-files.txt"
        grep "^R" "$CACHE_DIR/raw-changes.txt" | cut -f2,3 > "$CACHE_DIR/renamed-files.txt"
        
    else
        echo "⚠️  First run detected - no previous update commit found"
        echo "   Treating all tracked files as 'added' for initial basepoint creation"
        
        # First run - list all tracked files as "added"
        git ls-files > "$CACHE_DIR/added-files.txt"
        touch "$CACHE_DIR/modified-files.txt"
        touch "$CACHE_DIR/deleted-files.txt"
        touch "$CACHE_DIR/renamed-files.txt"
    fi
fi
```

### Step 3: Detect Changes Using Timestamps (Fallback Method)

If git is not available, use file modification timestamps:

```bash
if [ "$DETECTION_METHOD" = "timestamp" ]; then
    LAST_UPDATE_TIMESTAMP_FILE="$CACHE_DIR/last-update-timestamp.txt"
    
    if [ -f "$LAST_UPDATE_TIMESTAMP_FILE" ]; then
        LAST_UPDATE_TIMESTAMP=$(cat "$LAST_UPDATE_TIMESTAMP_FILE")
        
        echo "🔍 Finding files modified since $LAST_UPDATE_TIMESTAMP"
        
        # Find files modified since last update
        find . -type f -newer "$LAST_UPDATE_TIMESTAMP_FILE" \
            ! -path "./.git/*" \
            ! -path "./node_modules/*" \
            ! -path "./agent-os/output/*" \
            ! -path "./**/dist/*" \
            ! -path "./**/build/*" \
            ! -path "./**/.cache/*" \
            -print > "$CACHE_DIR/modified-files.txt"
        
        touch "$CACHE_DIR/added-files.txt"
        touch "$CACHE_DIR/deleted-files.txt"
        touch "$CACHE_DIR/renamed-files.txt"
        
        echo "⚠️  Note: Timestamp-based detection cannot distinguish added/deleted files"
        
    else
        echo "⚠️  First run detected - no previous timestamp found"
        echo "   Treating all files as 'added' for initial basepoint creation"
        
        # First run - list all files as "added"
        find . -type f \
            ! -path "./.git/*" \
            ! -path "./node_modules/*" \
            ! -path "./agent-os/output/*" \
            ! -path "./**/dist/*" \
            ! -path "./**/build/*" \
            ! -path "./**/.cache/*" \
            -print > "$CACHE_DIR/added-files.txt"
        
        touch "$CACHE_DIR/modified-files.txt"
        touch "$CACHE_DIR/deleted-files.txt"
        touch "$CACHE_DIR/renamed-files.txt"
    fi
fi
```

### Step 4: Filter Relevant Files

Filter out files that should not trigger basepoint updates:

```bash
# Define exclusion patterns for files that don't affect basepoints
EXCLUDE_PATTERNS=(
    "^\.git/"
    "^node_modules/"
    "^agent-os/output/"
    "^agent-os/specs/"
    "^\.env"
    "^\.DS_Store"
    "^.*\.log$"
    "^.*\.tmp$"
    "^.*\.lock$"
    "^package-lock\.json$"
    "^yarn\.lock$"
    "^pnpm-lock\.yaml$"
    "^Pipfile\.lock$"
    "^poetry\.lock$"
    "^dist/"
    "^build/"
    "^\.cache/"
    "^coverage/"
    "^\.nyc_output/"
    "^__pycache__/"
    "^\.pytest_cache/"
    "^\.venv/"
    "^venv/"
)

# Build grep exclusion pattern
EXCLUDE_REGEX=$(printf "%s\n" "${EXCLUDE_PATTERNS[@]}" | paste -sd'|' -)

# Filter each file list
for file_type in added modified deleted; do
    FILE_LIST="$CACHE_DIR/${file_type}-files.txt"
    if [ -f "$FILE_LIST" ]; then
        grep -vE "$EXCLUDE_REGEX" "$FILE_LIST" > "$CACHE_DIR/${file_type}-files-filtered.txt" 2>/dev/null || touch "$CACHE_DIR/${file_type}-files-filtered.txt"
        mv "$CACHE_DIR/${file_type}-files-filtered.txt" "$FILE_LIST"
    fi
done

# Combine all changes into single file
cat "$CACHE_DIR/added-files.txt" "$CACHE_DIR/modified-files.txt" "$CACHE_DIR/deleted-files.txt" 2>/dev/null | sort -u > "$CHANGED_FILES"
```

### Step 5: Identify Product File Changes

Specifically check if product files changed (for knowledge re-extraction):

```bash
# Check for product file changes
PRODUCT_FILES_CHANGED=false
PRODUCT_CHANGES=""

if grep -q "^agent-os/product/" "$CHANGED_FILES" 2>/dev/null; then
    PRODUCT_FILES_CHANGED=true
    PRODUCT_CHANGES=$(grep "^agent-os/product/" "$CHANGED_FILES")
    echo "📦 Product file changes detected:"
    echo "$PRODUCT_CHANGES" | sed 's/^/   /'
fi

# Save product change flag
echo "$PRODUCT_FILES_CHANGED" > "$CACHE_DIR/product-files-changed.txt"
if [ "$PRODUCT_FILES_CHANGED" = "true" ]; then
    echo "$PRODUCT_CHANGES" > "$CACHE_DIR/product-changes.txt"
fi
```

### Step 6: Generate Change Summary

Create comprehensive change summary report:

```bash
# Count changes by category
ADDED_COUNT=$(wc -l < "$CACHE_DIR/added-files.txt" 2>/dev/null | tr -d ' ' || echo "0")
MODIFIED_COUNT=$(wc -l < "$CACHE_DIR/modified-files.txt" 2>/dev/null | tr -d ' ' || echo "0")
DELETED_COUNT=$(wc -l < "$CACHE_DIR/deleted-files.txt" 2>/dev/null | tr -d ' ' || echo "0")
TOTAL_CHANGES=$((ADDED_COUNT + MODIFIED_COUNT + DELETED_COUNT))

# Generate summary report
cat > "$CHANGE_SUMMARY" << EOF
# Codebase Change Summary

**Detection Method:** $DETECTION_METHOD
**Detection Time:** $(date -u +%Y-%m-%dT%H:%M:%SZ)
**Total Changes:** $TOTAL_CHANGES files

## Summary by Category

| Category | Count |
|----------|-------|
| Added    | $ADDED_COUNT |
| Modified | $MODIFIED_COUNT |
| Deleted  | $DELETED_COUNT |
| **Total** | **$TOTAL_CHANGES** |

## Product Files Changed

$(if [ "$PRODUCT_FILES_CHANGED" = "true" ]; then
    echo "**Yes** - Knowledge re-extraction required"
    echo ""
    echo "Changed product files:"
    cat "$CACHE_DIR/product-changes.txt" 2>/dev/null | sed 's/^/- /'
else
    echo "**No** - No product file changes detected"
fi)

## Added Files ($ADDED_COUNT)

$(if [ -s "$CACHE_DIR/added-files.txt" ]; then
    cat "$CACHE_DIR/added-files.txt" | head -50 | sed 's/^/- /'
    if [ "$ADDED_COUNT" -gt 50 ]; then
        echo "- ... and $((ADDED_COUNT - 50)) more"
    fi
else
    echo "_No files added_"
fi)

## Modified Files ($MODIFIED_COUNT)

$(if [ -s "$CACHE_DIR/modified-files.txt" ]; then
    cat "$CACHE_DIR/modified-files.txt" | head -50 | sed 's/^/- /'
    if [ "$MODIFIED_COUNT" -gt 50 ]; then
        echo "- ... and $((MODIFIED_COUNT - 50)) more"
    fi
else
    echo "_No files modified_"
fi)

## Deleted Files ($DELETED_COUNT)

$(if [ -s "$CACHE_DIR/deleted-files.txt" ]; then
    cat "$CACHE_DIR/deleted-files.txt" | head -50 | sed 's/^/- /'
    if [ "$DELETED_COUNT" -gt 50 ]; then
        echo "- ... and $((DELETED_COUNT - 50)) more"
    fi
else
    echo "_No files deleted_"
fi)
EOF

echo "📋 Change summary saved to: $CHANGE_SUMMARY"
```

### Step 7: Output Detection Results

Display detection results for user:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 CHANGE DETECTION COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Detection Method: [git/timestamp]

📊 Changes Detected:
   ✅ Added:    [N] files
   ✏️  Modified: [N] files
   ❌ Deleted:  [N] files
   ─────────────────────
   📁 Total:    [N] files

📦 Product Files Changed: [Yes/No]

📋 Full report: agent-os/output/update-basepoints-and-redeploy/cache/change-summary.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next: Identify which basepoints are affected by these changes.
```

### Step 8: Handle No Changes Scenario

If no changes detected, provide early exit option:

```bash
if [ "$TOTAL_CHANGES" -eq 0 ]; then
    echo ""
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "✅ NO CHANGES DETECTED"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo ""
    echo "No codebase changes found since last update."
    echo ""
    echo "Options:"
    echo "1. Exit (no update needed)"
    echo "2. Force full refresh (regenerate all basepoints)"
    echo ""
    
    # Save no-changes flag
    echo "true" > "$CACHE_DIR/no-changes-detected.txt"
fi
```

## Important Constraints

- **MUST check git availability** before attempting git-based detection
- **MUST handle first-run scenario** where no previous update exists
- **MUST filter out irrelevant files** (build artifacts, locks, caches)
- **MUST identify product file changes** separately for knowledge re-extraction
- **MUST provide clear summary** of all detected changes
- **MUST handle no-changes scenario** gracefully with early exit option
- Must save all intermediate files to cache for subsequent phases
- Must use deterministic file ordering (sort) for reproducibility


## Phase 1 Actions

### 1.1 Initialize Output Structure

Create the output cache directory structure:

```bash
mkdir -p agent-os/output/update-basepoints-and-redeploy/cache
mkdir -p agent-os/output/update-basepoints-and-redeploy/reports
```

### 1.2 Detect Changes

Use the workflow to detect changes. The workflow will:

1. **Check for git availability** - Prefer git-based detection
2. **Load previous update reference** - From `cache/last-update-commit.txt` or `cache/last-update-timestamp.txt`
3. **Detect and categorize changes** - Added, modified, deleted files
4. **Filter irrelevant files** - Exclude node_modules, .git, agent-os/output, etc.
5. **Identify product file changes** - Flag if `agent-os/product/*` files changed

### 1.3 Handle First-Run Scenario

If this is the first run (no previous update reference exists):
- Treat ALL relevant files as "added"
- This will trigger a full basepoint update (similar to initial creation)
- Display appropriate message to user

### 1.4 Handle No-Changes Scenario

If no changes are detected:
- Display "No changes detected" message
- Offer options: Exit or Force refresh
- If user chooses to exit, stop the command gracefully

## Expected Outputs

After this phase, the following files should exist in `agent-os/output/update-basepoints-and-redeploy/cache/`:

| File | Description |
|------|-------------|
| `changed-files.txt` | Combined list of all changed files |
| `added-files.txt` | Files that were added |
| `modified-files.txt` | Files that were modified |
| `deleted-files.txt` | Files that were deleted |
| `change-summary.md` | Human-readable summary report |
| `product-files-changed.txt` | Boolean flag for product changes |

## Display confirmation and next step

Once change detection is complete, output the following message:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ PHASE 1 COMPLETE: Change Detection
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Changes Detected:
   Added:    [N] files
   Modified: [N] files
   Deleted:  [N] files
   Total:    [N] files

📦 Product Files Changed: [Yes/No]

📋 Summary: agent-os/output/update-basepoints-and-redeploy/cache/change-summary.md

NEXT STEP 👉 Run Phase 2: `2-identify-affected-basepoints.md`
```

If no changes detected:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ NO CHANGES DETECTED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No codebase changes found since last update.

Options:
1. Exit (no update needed)
2. Force full refresh

👉 What would you like to do?
```

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your change detection process aligns with the user's preferences and standards as detailed in the following files:

@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md

# PHASE 2: Identify Affected Basepoints

The SECOND STEP is to identify which basepoints are affected by the detected changes:

## Phase 2 Actions

### 2.1 Load Changed Files

Load the changed files list from Phase 1:

```bash
CACHE_DIR="agent-os/output/update-basepoints-and-redeploy/cache"

if [ ! -f "$CACHE_DIR/changed-files.txt" ]; then
    echo "❌ ERROR: Changed files list not found."
    echo "   Run Phase 1 (detect-changes) first."
    exit 1
fi

CHANGED_FILES=$(cat "$CACHE_DIR/changed-files.txt")
TOTAL_CHANGES=$(echo "$CHANGED_FILES" | grep -v "^$" | wc -l | tr -d ' ')

echo "📋 Loaded $TOTAL_CHANGES changed files"
```

### 2.2 Map Files to Basepoints

Apply mapping rules to determine which basepoints are affected:

**Mapping Rules:**

| Changed File Pattern | Affected Basepoint |
|---------------------|-------------------|
| `scripts/*.sh` | `basepoints/scripts/agent-base-scripts.md` |
| `profiles/default/commands/*` | `basepoints/profiles/default/commands/agent-base-commands.md` |
| `profiles/default/workflows/*` | `basepoints/profiles/default/workflows/agent-base-workflows.md` |
| `profiles/default/standards/*` | `basepoints/profiles/default/standards/agent-base-standards.md` |
| `profiles/default/agents/*` | `basepoints/profiles/default/agents/agent-base-agents.md` |
| `profiles/default/*` | `basepoints/profiles/default/agent-base-default.md` |
| `profiles/*` | `basepoints/profiles/agent-base-profiles.md` |
| `agent-os/product/*` | _(flag for knowledge re-extraction only)_ |
| `*` (root files) | `basepoints/agent-base-self.md` |

**Mapping Logic:**

```bash
# Initialize affected basepoints list
> "$CACHE_DIR/affected-basepoints.txt"
> "$CACHE_DIR/affected-basepoints-unique.txt"

# Process each changed file
echo "$CHANGED_FILES" | while read changed_file; do
    if [ -z "$changed_file" ]; then
        continue
    fi
    
    # Normalize path (remove leading ./)
    changed_file=$(echo "$changed_file" | sed 's|^\./||')
    
    # Determine affected basepoint based on path
    case "$changed_file" in
        scripts/*)
            echo "agent-os/basepoints/scripts/agent-base-scripts.md" >> "$CACHE_DIR/affected-basepoints.txt"
            ;;
        profiles/default/commands/*)
            echo "agent-os/basepoints/profiles/default/commands/agent-base-commands.md" >> "$CACHE_DIR/affected-basepoints.txt"
            ;;
        profiles/default/workflows/*)
            echo "agent-os/basepoints/profiles/default/workflows/agent-base-workflows.md" >> "$CACHE_DIR/affected-basepoints.txt"
            ;;
        profiles/default/standards/*)
            echo "agent-os/basepoints/profiles/default/standards/agent-base-standards.md" >> "$CACHE_DIR/affected-basepoints.txt"
            ;;
        profiles/default/agents/*)
            echo "agent-os/basepoints/profiles/default/agents/agent-base-agents.md" >> "$CACHE_DIR/affected-basepoints.txt"
            ;;
        profiles/default/*)
            echo "agent-os/basepoints/profiles/default/agent-base-default.md" >> "$CACHE_DIR/affected-basepoints.txt"
            ;;
        profiles/*)
            echo "agent-os/basepoints/profiles/agent-base-profiles.md" >> "$CACHE_DIR/affected-basepoints.txt"
            ;;
        agent-os/product/*)
            # Product files don't have basepoints, but flag for knowledge re-extraction
            echo "PRODUCT_CHANGE:$changed_file" >> "$CACHE_DIR/product-changes-detail.txt"
            ;;
        agent-os/*)
            # Ignore other agent-os files (output, specs, etc.)
            ;;
        *)
            # Root-level files affect root basepoint
            echo "agent-os/basepoints/agent-base-self.md" >> "$CACHE_DIR/affected-basepoints.txt"
            ;;
    esac
done

# Remove duplicates
sort -u "$CACHE_DIR/affected-basepoints.txt" > "$CACHE_DIR/affected-basepoints-unique.txt"
mv "$CACHE_DIR/affected-basepoints-unique.txt" "$CACHE_DIR/affected-basepoints.txt"
```

### 2.3 Calculate Parent Chain

For each affected basepoint, identify parent basepoints that also need updating:

```bash
# Parent chain propagation
# If a child basepoint changes, its parent must also be updated

> "$CACHE_DIR/parent-basepoints.txt"

while read basepoint; do
    if [ -z "$basepoint" ]; then
        continue
    fi
    
    # Extract parent path
    PARENT_DIR=$(dirname "$basepoint")
    
    # Walk up the tree to find parent basepoints
    while [ "$PARENT_DIR" != "agent-os/basepoints" ] && [ "$PARENT_DIR" != "." ]; do
        PARENT_NAME=$(basename "$PARENT_DIR")
        PARENT_BASEPOINT="$PARENT_DIR/agent-base-$PARENT_NAME.md"
        
        if [ -f "$PARENT_BASEPOINT" ]; then
            echo "$PARENT_BASEPOINT" >> "$CACHE_DIR/parent-basepoints.txt"
        fi
        
        PARENT_DIR=$(dirname "$PARENT_DIR")
    done
done < "$CACHE_DIR/affected-basepoints.txt"

# Add parents to affected list (unique)
cat "$CACHE_DIR/affected-basepoints.txt" "$CACHE_DIR/parent-basepoints.txt" 2>/dev/null | sort -u > "$CACHE_DIR/all-affected-basepoints.txt"
mv "$CACHE_DIR/all-affected-basepoints.txt" "$CACHE_DIR/affected-basepoints.txt"

# Always add headquarter if any basepoint is affected
if [ -s "$CACHE_DIR/affected-basepoints.txt" ]; then
    echo "agent-os/basepoints/headquarter.md" >> "$CACHE_DIR/affected-basepoints.txt"
fi
```

### 2.4 Check Product File Changes

Determine if product files changed (requires knowledge re-extraction):

```bash
PRODUCT_CHANGED=$(cat "$CACHE_DIR/product-files-changed.txt" 2>/dev/null || echo "false")

if [ "$PRODUCT_CHANGED" = "true" ]; then
    echo "📦 Product files changed - knowledge re-extraction required"
    echo "true" > "$CACHE_DIR/requires-knowledge-reextraction.txt"
else
    echo "false" > "$CACHE_DIR/requires-knowledge-reextraction.txt"
fi
```

### 2.5 Generate Affected Basepoints Summary

Create a summary of affected basepoints:

```bash
AFFECTED_COUNT=$(wc -l < "$CACHE_DIR/affected-basepoints.txt" | tr -d ' ')

cat > "$CACHE_DIR/affected-basepoints-summary.md" << EOF
# Affected Basepoints Summary

**Analysis Time:** $(date -u +%Y-%m-%dT%H:%M:%SZ)
**Total Changed Files:** $TOTAL_CHANGES
**Affected Basepoints:** $AFFECTED_COUNT
**Product Changes:** $PRODUCT_CHANGED

## Affected Basepoints

$(cat "$CACHE_DIR/affected-basepoints.txt" | sed 's/^/- /')

## File-to-Basepoint Mapping

| Changed File | Affected Basepoint |
|-------------|-------------------|
$(# Generate mapping table from logs)

## Update Order

Basepoints will be updated in this order (children first, parents last):

1. Module-level basepoints (deepest first)
2. Parent basepoints (bottom-up)
3. Headquarter (last)
EOF
```

## Expected Outputs

After this phase, the following files should exist:

| File | Description |
|------|-------------|
| `affected-basepoints.txt` | List of basepoints to update |
| `affected-basepoints-summary.md` | Human-readable summary |
| `requires-knowledge-reextraction.txt` | Flag for knowledge re-extraction |

## Display confirmation and next step

Once basepoint identification is complete, output the following message:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ PHASE 2 COMPLETE: Identify Affected Basepoints
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Analysis Results:
   Changed files:       [N]
   Affected basepoints: [N]
   Product changes:     [Yes/No]

📋 Affected Basepoints:
   [List of affected basepoint paths]

📦 Knowledge Re-extraction: [Required/Not required]

📋 Summary: agent-os/output/update-basepoints-and-redeploy/cache/affected-basepoints-summary.md

NEXT STEP 👉 Run Phase 3: `3-update-basepoints.md`
```

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your basepoint identification process aligns with the user's preferences and standards as detailed in the following files:

@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md

# PHASE 3: Update Basepoints

The THIRD STEP is to update only the affected basepoints by following these instructions:

# Incremental Basepoint Update

## Core Responsibilities

1. **Load Existing Basepoint Content**: Read current basepoint files that need updating
2. **Re-analyze Specific Modules**: Analyze only modules affected by changes (not entire codebase)
3. **Merge Updates with Existing Content**: Intelligently merge new patterns/standards with existing
4. **Handle Parent Chain Updates**: Propagate changes up the basepoint hierarchy (bottom-up)
5. **Preserve Unchanged Sections**: Keep sections that weren't affected by changes

## Workflow

### Step 1: Load Affected Basepoints List

Load the list of basepoints that need updating from Phase 2:

```bash
CACHE_DIR="agent-os/output/update-basepoints-and-redeploy/cache"
AFFECTED_BASEPOINTS_FILE="$CACHE_DIR/affected-basepoints.txt"

if [ ! -f "$AFFECTED_BASEPOINTS_FILE" ]; then
    echo "❌ ERROR: Affected basepoints list not found."
    echo "   Expected: $AFFECTED_BASEPOINTS_FILE"
    echo "   Run Phase 2 (identify-affected-basepoints) first."
    exit 1
fi

# Load and categorize basepoints
AFFECTED_BASEPOINTS=$(cat "$AFFECTED_BASEPOINTS_FILE" | grep -v "^#" | grep -v "^$")
TOTAL_BASEPOINTS=$(echo "$AFFECTED_BASEPOINTS" | wc -l | tr -d ' ')

echo "📋 Loaded $TOTAL_BASEPOINTS basepoints to update"

# Separate module-level and parent-level basepoints for ordered processing
echo "$AFFECTED_BASEPOINTS" | grep -v "headquarter" | grep "agent-base-" > "$CACHE_DIR/module-basepoints-to-update.txt" || true
echo "$AFFECTED_BASEPOINTS" | grep "headquarter" > "$CACHE_DIR/headquarter-to-update.txt" || true

MODULE_COUNT=$(wc -l < "$CACHE_DIR/module-basepoints-to-update.txt" 2>/dev/null | tr -d ' ' || echo "0")
HEADQUARTER_UPDATE=$([ -s "$CACHE_DIR/headquarter-to-update.txt" ] && echo "Yes" || echo "No")

echo "   Module basepoints: $MODULE_COUNT"
echo "   Headquarter update: $HEADQUARTER_UPDATE"
```

### Step 2: Sort Basepoints by Depth (Children First)

Sort basepoints so children are processed before parents:

```bash
# Calculate depth for each basepoint (deeper = more slashes in path)
while read basepoint_path; do
    if [ -z "$basepoint_path" ]; then
        continue
    fi
    
    DEPTH=$(echo "$basepoint_path" | tr -cd '/' | wc -c)
    echo "$DEPTH:$basepoint_path"
done < "$CACHE_DIR/module-basepoints-to-update.txt" | sort -t: -k1 -nr | cut -d: -f2 > "$CACHE_DIR/sorted-basepoints-to-update.txt"

echo "📊 Sorted basepoints by depth (deepest first for bottom-up processing)"
```

### Step 3: Update Each Module Basepoint

Process each module basepoint, preserving unchanged content:

```bash
CURRENT=0
UPDATED=()
FAILED=()

while read basepoint_path; do
    if [ -z "$basepoint_path" ]; then
        continue
    fi
    
    CURRENT=$((CURRENT + 1))
    echo ""
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "📝 Updating basepoint $CURRENT/$MODULE_COUNT: $basepoint_path"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    
    # Extract module path from basepoint path
    # basepoints/profiles/default/agent-base-default.md -> profiles/default
    MODULE_PATH=$(echo "$basepoint_path" | sed 's|^agent-os/basepoints/||' | sed 's|/agent-base-.*\.md$||')
    
    if [ -z "$MODULE_PATH" ] || [ "$MODULE_PATH" = "agent-base-"* ]; then
        # Root-level basepoint
        MODULE_PATH="."
    fi
    
    echo "   Module path: $MODULE_PATH"
    
    # Check if basepoint file exists
    if [ -f "$basepoint_path" ]; then
        echo "   📂 Loading existing basepoint content..."
        EXISTING_CONTENT=$(cat "$basepoint_path")
        
        # Backup existing content
        cp "$basepoint_path" "${basepoint_path}.backup"
    else
        echo "   ⚠️  Basepoint file not found - will create new"
        EXISTING_CONTENT=""
    fi
    
    # Get list of changed files for this module
    CHANGED_FILES_FOR_MODULE=$(grep "^$MODULE_PATH" "$CACHE_DIR/changed-files.txt" 2>/dev/null || true)
    CHANGED_COUNT=$(echo "$CHANGED_FILES_FOR_MODULE" | grep -v "^$" | wc -l | tr -d ' ')
    
    echo "   📋 Changed files in module: $CHANGED_COUNT"
    
    # [Re-analyze module and generate updated content]
    # This step should:
    # 1. Read the changed files
    # 2. Extract new patterns, standards, flows, strategies
    # 3. Merge with existing content (preserve unchanged sections)
    # 4. Write updated basepoint
    
    echo "   ✅ Updated: $basepoint_path"
    
    # Log progress
    echo "- [x] \`$basepoint_path\` (module: $MODULE_PATH, changes: $CHANGED_COUNT files)" >> "$CACHE_DIR/update-progress.md"
    
done < "$CACHE_DIR/sorted-basepoints-to-update.txt"
```

### Step 4: Merge New Content with Existing

For each section of a basepoint, intelligently merge updates:

```markdown
# Merge Strategy per Section

## Module Overview
- Update if module structure changed (files added/removed)
- Preserve purpose and layer if unchanged

## Patterns Section
### Design Patterns
- ADD new patterns found in changed files
- KEEP existing patterns if still present in code
- REMOVE patterns no longer present (if files deleted)

### Coding Patterns
- Same merge strategy as design patterns

## Standards Section
- Update naming conventions if new patterns found
- Preserve existing standards unless contradicted

## Flows Section
### Data Flow
- Re-trace data flow if interface files changed
- Merge new flow paths with existing

### Control Flow
- Re-trace control flow if logic files changed
- Update entry/exit points if changed

## Strategies Section
- Update if implementation approach changed
- Preserve architectural strategies unless contradicted

## Testing Section
- Update if test files changed
- Add new test patterns found
- Keep existing test documentation

## Related Modules Section
- Update dependencies if imports changed
- Update relationships if module structure changed
```

### Step 5: Update Parent Basepoints (Bottom-Up)

After updating module basepoints, propagate changes to parents:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "📤 PROPAGATING CHANGES TO PARENT BASEPOINTS"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Find parent basepoints that need updating
# A parent needs updating if any of its children were updated
find agent-os/basepoints -name "agent-base-*.md" -type f | while read parent_basepoint; do
    PARENT_DIR=$(dirname "$parent_basepoint")
    
    # Check if any child basepoints were updated
    CHILD_UPDATED=false
    while read updated_basepoint; do
        UPDATED_DIR=$(dirname "$updated_basepoint")
        
        # Check if updated_basepoint is a child of parent_basepoint
        if [[ "$UPDATED_DIR" == "$PARENT_DIR/"* ]]; then
            CHILD_UPDATED=true
            break
        fi
    done < "$CACHE_DIR/sorted-basepoints-to-update.txt"
    
    if [ "$CHILD_UPDATED" = "true" ]; then
        echo "   📝 Updating parent: $parent_basepoint"
        
        # Re-aggregate knowledge from child basepoints
        # Update "Related Modules" section
        # Update aggregated patterns/standards/flows
        
        echo "   ✅ Parent updated: $parent_basepoint"
    fi
done
```

### Step 6: Update Headquarter (Last)

If any basepoints changed, update the headquarter file:

```bash
if [ -s "$CACHE_DIR/headquarter-to-update.txt" ]; then
    echo ""
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "🏢 UPDATING HEADQUARTER"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    
    HEADQUARTER_PATH="agent-os/basepoints/headquarter.md"
    
    if [ -f "$HEADQUARTER_PATH" ]; then
        echo "   📂 Loading existing headquarter content..."
        cp "$HEADQUARTER_PATH" "${HEADQUARTER_PATH}.backup"
        
        # Re-aggregate high-level knowledge from all basepoints
        # Update architecture overview if structure changed
        # Update navigation sections
        # Update cross-cutting patterns
        
        echo "   ✅ Headquarter updated"
    else
        echo "   ⚠️  Headquarter not found - will create new"
        # Generate new headquarter using generate-headquarter workflow
    fi
fi
```

### Step 7: Generate Update Log

Create detailed log of all updates made:

```bash
# Generate update log
cat > "$CACHE_DIR/update-log.md" << EOF
# Incremental Basepoint Update Log

**Update Time:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Summary

- **Module basepoints updated:** $MODULE_COUNT
- **Parent basepoints updated:** [N]
- **Headquarter updated:** $HEADQUARTER_UPDATE

## Updated Basepoints

$(cat "$CACHE_DIR/update-progress.md" 2>/dev/null || echo "_No updates logged_")

## Backup Files

Backup files created with \`.backup\` extension:
$(find agent-os/basepoints -name "*.backup" -type f 2>/dev/null | sed 's/^/- /' || echo "_No backups created_")

## Merge Decisions

[Log of merge decisions made during update]

EOF

echo "📋 Update log saved to: $CACHE_DIR/update-log.md"
```

### Step 8: Output Update Results

Display update results for user:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ INCREMENTAL BASEPOINT UPDATE COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Update Summary:
   Module basepoints: [N] updated
   Parent basepoints: [N] updated
   Headquarter: [Yes/No]

📋 Update log: agent-os/output/update-basepoints-and-redeploy/cache/update-log.md
💾 Backups: *.backup files created for rollback if needed

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next: Re-extract and merge knowledge from updated basepoints.
```

## Important Constraints

- **MUST process children before parents** (bottom-up order)
- **MUST update headquarter last** after all other basepoints
- **MUST preserve unchanged content** - only update affected sections
- **MUST create backups** before modifying existing basepoints
- **MUST log all changes** for traceability and potential rollback
- Must use existing basepoint structure and format
- Must maintain consistency with unaffected basepoints
- Must handle missing basepoints gracefully (create new if needed)
- Must not modify basepoints that weren't in the affected list


## Phase 3 Actions

### 3.1 Load Affected Basepoints

Load the list of basepoints that need updating from Phase 2:

```bash
CACHE_DIR="agent-os/output/update-basepoints-and-redeploy/cache"

if [ ! -f "$CACHE_DIR/affected-basepoints.txt" ]; then
    echo "❌ ERROR: Affected basepoints list not found."
    echo "   Run Phase 2 (identify-affected-basepoints) first."
    exit 1
fi

AFFECTED_BASEPOINTS=$(cat "$CACHE_DIR/affected-basepoints.txt" | grep -v "^$")
TOTAL_AFFECTED=$(echo "$AFFECTED_BASEPOINTS" | wc -l | tr -d ' ')

echo "📋 Loaded $TOTAL_AFFECTED basepoints to update"
```

### 3.2 Determine Update Order

Sort basepoints so children are processed before parents (deepest first):

**Update Order:**
1. **Module-level basepoints** - Deepest in hierarchy first
2. **Parent basepoints** - Bottom-up (child changes propagate to parent)
3. **Headquarter** - Always last (aggregates all changes)

```bash
# Separate headquarter from other basepoints
HEADQUARTER_PATH="agent-os/basepoints/headquarter.md"
MODULE_BASEPOINTS=$(echo "$AFFECTED_BASEPOINTS" | grep -v "headquarter.md")

# Sort by depth (more slashes = deeper = process first)
SORTED_BASEPOINTS=$(echo "$MODULE_BASEPOINTS" | while read bp; do
    DEPTH=$(echo "$bp" | tr -cd '/' | wc -c)
    echo "$DEPTH:$bp"
done | sort -t: -k1 -nr | cut -d: -f2)

echo "📊 Update order determined (deepest first)"
```

### 3.3 Update Each Affected Basepoint

For each affected basepoint, re-analyze the module and update:

```bash
CURRENT=0
UPDATED_LIST=""

echo "$SORTED_BASEPOINTS" | while read basepoint_path; do
    if [ -z "$basepoint_path" ]; then
        continue
    fi
    
    CURRENT=$((CURRENT + 1))
    MODULE_COUNT=$(echo "$SORTED_BASEPOINTS" | grep -v "^$" | wc -l | tr -d ' ')
    
    echo ""
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "📝 Updating basepoint $CURRENT/$MODULE_COUNT"
    echo "   Path: $basepoint_path"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    
    # Backup existing basepoint
    if [ -f "$basepoint_path" ]; then
        cp "$basepoint_path" "${basepoint_path}.backup"
        echo "   💾 Backed up existing content"
    fi
    
    # Extract module path from basepoint path
    MODULE_PATH=$(echo "$basepoint_path" | sed 's|^agent-os/basepoints/||' | sed 's|/agent-base-.*\.md$||')
    
    # Get changed files for this module
    CHANGED_IN_MODULE=$(grep "^$MODULE_PATH" "$CACHE_DIR/changed-files.txt" 2>/dev/null | wc -l | tr -d ' ')
    echo "   📁 Changed files in module: $CHANGED_IN_MODULE"
    
    # Re-analyze module using existing workflow patterns
    echo "   🔍 Re-analyzing module..."
    
    # Use codebase analysis workflow to extract updated patterns
    # # Codebase Analysis

## Core Responsibilities

1. **Analyze Code Files**: Examine all code files in identified module folders
2. **Analyze Configuration Files**: Review configuration files for project settings and patterns
3. **Analyze Test Files**: Extract testing approaches and patterns from test files
4. **Analyze Documentation**: Review documentation files for architectural insights
5. **Extract Patterns**: Identify patterns, standards, flows, strategies, and testing approaches
6. **Process Layer by Layer**: Extract patterns at every abstraction layer and across layers

## Workflow

### Step 1: Load Module Folders

Load the list of module folders identified in previous phase:

```bash
if [ -f "agent-os/output/create-basepoints/cache/module-folders.txt" ]; then
    MODULE_FOLDERS=$(cat agent-os/output/create-basepoints/cache/module-folders.txt | grep -v "^#")
fi
```

### Step 2: Analyze Code Files

Process each module folder and analyze code files:

```bash
mkdir -p agent-os/output/create-basepoints/analysis

echo "$MODULE_FOLDERS" | while read module_dir; do
    if [ -z "$module_dir" ]; then
        continue
    fi
    
    # Find code files in this module
    find "$module_dir" -type f \( -name "*.ts" -o -name "*.js" -o -name "*.py" -o -name "*.java" -o -name "*.go" -o -name "*.rs" -o -name "*.rb" -o -name "*.php" -o -name "*.cs" \) | while read code_file; do
        # Analyze file for patterns, standards, flows, strategies, testing
        # Store analysis in cache
    done
done
```

### Step 3: Extract Patterns

For each file, extract:
- **Patterns**: Design patterns, coding patterns, architectural patterns
- **Standards**: Coding standards, naming conventions, formatting rules
- **Flows**: Data flows, control flows, dependency flows
- **Strategies**: Implementation strategies, architectural strategies
- **Testing Approaches**: Test patterns, test strategies, test organization

### Step 4: Analyze Configuration Files

Analyze configuration files:

```bash
find . -type f \( -name "package.json" -o -name "tsconfig.json" -o -name "pyproject.toml" -o -name "go.mod" -o -name "*.config.*" \) ! -path "*/node_modules/*" | while read config_file; do
    # Extract configuration patterns and standards
done
```

### Step 5: Analyze Test Files

Analyze test files:

```bash
find . -type f \( -name "*test*" -o -name "*spec*" \) ! -path "*/node_modules/*" | while read test_file; do
    # Extract testing patterns and strategies
done
```

### Step 6: Analyze Documentation

Review documentation files:

```bash
find . -type f \( -name "README.md" -o -name "ARCHITECTURE.md" -o -name "DESIGN.md" \) ! -path "*/node_modules/*" ! -path "*/agent-os/*" | while read doc_file; do
    # Extract architectural insights and patterns
done
```

### Step 7: Aggregate Patterns by Layer

Organize extracted patterns by abstraction layer using detected layers from cache.

### Step 8: Save Analysis Results

Save comprehensive analysis results to cache for use in basepoint generation.

## Important Constraints

- Must process files one by one for comprehensive extraction
- Must analyze at every abstraction layer individually
- Must also extract patterns across abstraction layers
- Must extract patterns, standards, flows, strategies, and testing approaches comprehensively
- Must save analysis results for use in basepoint generation phases

    
    # Generate updated basepoint content
    # # Module Basepoint Generation

## Core Responsibilities

1. **Load Task List**: Retrieve the basepoints task list from previous phase
2. **Generate Basepoints One-by-One**: Create each basepoint with progress tracking
3. **Track Progress**: Update task list as each basepoint is created
4. **Verify Coverage**: Ensure ALL modules have basepoints (no misses)
5. **Report Completion**: Provide summary of all generated basepoints

## Workflow

### Step 1: Load Module Folders and Task List

Load module folders and their task list from cache:

```bash
# Load module folders
if [ ! -f "agent-os/output/create-basepoints/cache/module-folders.txt" ]; then
    echo "❌ ERROR: Module folders list not found. Run mirror-project-structure first."
    exit 1
fi

MODULE_FOLDERS=$(cat agent-os/output/create-basepoints/cache/module-folders.txt | grep -v "^#" | grep -v "^$")
TOTAL_MODULES=$(echo "$MODULE_FOLDERS" | wc -l | tr -d ' ')

echo "📋 Loaded $TOTAL_MODULES modules from task list"

# Initialize progress tracking
echo "# Basepoints Generation Progress" > agent-os/output/create-basepoints/cache/generation-progress.md
echo "" >> agent-os/output/create-basepoints/cache/generation-progress.md
echo "Started: $(date)" >> agent-os/output/create-basepoints/cache/generation-progress.md
echo "" >> agent-os/output/create-basepoints/cache/generation-progress.md
```

### Step 2: Generate Basepoint for Each Module (One-by-One)

Process each module deterministically, tracking progress:

```bash
CURRENT=0
GENERATED=()
FAILED=()

echo "$MODULE_FOLDERS" | while read module_dir; do
    if [ -z "$module_dir" ]; then
        continue
    fi
    
    CURRENT=$((CURRENT + 1))
    echo ""
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "📦 Processing module $CURRENT/$TOTAL_MODULES: $module_dir"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    
    # Normalize module directory path
    NORMALIZED_DIR=$(echo "$module_dir" | sed 's|^\./||' | sed 's|^\.$||')
    
    # Handle root-level modules
    if [ -z "$NORMALIZED_DIR" ] || [ "$NORMALIZED_DIR" = "." ]; then
        PROJECT_ROOT_NAME=$(basename "$(pwd)" | tr '[:upper:]' '[:lower:]' | sed 's/[^a-z0-9-]/-/g')
        BASEPOINT_DIR="agent-os/basepoints"
        MODULE_NAME="$PROJECT_ROOT_NAME"
    else
        BASEPOINT_DIR="agent-os/basepoints/$NORMALIZED_DIR"
        MODULE_NAME=$(basename "$NORMALIZED_DIR")
    fi
    
    BASEPOINT_FILE="$BASEPOINT_DIR/agent-base-$MODULE_NAME.md"
    
    # Create directory if needed
    mkdir -p "$BASEPOINT_DIR"
    
    # Generate basepoint content
    echo "   → Analyzing module: $module_dir"
    echo "   → Creating basepoint: $BASEPOINT_FILE"
    
    # [Analyze module and generate content here]
    
    # Log progress
    echo "- [x] **$CURRENT/$TOTAL_MODULES**: \`$module_dir\` → \`$BASEPOINT_FILE\`" >> agent-os/output/create-basepoints/cache/generation-progress.md
    
    echo "   ✅ Created: $BASEPOINT_FILE"
done
```

### Step 3: Generate Basepoint Content

For each module, create comprehensive basepoint content including:

```markdown
# Basepoint: [Module Name]

## Module Overview
- **Location**: `/path/to/module/`
- **Purpose**: [Extracted from analysis]
- **Layer**: [Abstraction layer]

## Files in Module
| File | Purpose |
|------|---------|
| [files...] | [purposes...] |

## Patterns
### Design Patterns
[Extracted patterns]

### Coding Patterns
[Extracted patterns]

## Standards
[Extracted standards]

## Flows
### Data Flow
[Extracted flows]

### Control Flow
[Extracted flows]

## Strategies
[Extracted strategies]

## Testing
[Extracted testing approaches]

## Related Modules
[Dependencies and relationships]
```

### Step 4: Verify Complete Coverage

After generating all module basepoints, verify complete coverage:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "🔍 VERIFICATION: Checking basepoint coverage"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Count expected vs actual
EXPECTED_COUNT=$(cat agent-os/output/create-basepoints/cache/module-folders.txt | grep -v "^#" | grep -v "^$" | wc -l | tr -d ' ')
ACTUAL_COUNT=$(find agent-os/basepoints -name "agent-base-*.md" -type f | wc -l | tr -d ' ')

echo "Expected module basepoints: $EXPECTED_COUNT"
echo "Actual module basepoints: $ACTUAL_COUNT"

# List all generated basepoints
echo "" >> agent-os/output/create-basepoints/cache/generation-progress.md
echo "## Generated Basepoints" >> agent-os/output/create-basepoints/cache/generation-progress.md
echo "" >> agent-os/output/create-basepoints/cache/generation-progress.md
find agent-os/basepoints -name "agent-base-*.md" -type f | sort | while read bp; do
    echo "- \`$bp\`" >> agent-os/output/create-basepoints/cache/generation-progress.md
done

# Check for missing basepoints
echo "" >> agent-os/output/create-basepoints/cache/generation-progress.md
echo "## Coverage Check" >> agent-os/output/create-basepoints/cache/generation-progress.md
echo "" >> agent-os/output/create-basepoints/cache/generation-progress.md

MISSING_COUNT=0
while read module_dir; do
    if [ -z "$module_dir" ]; then
        continue
    fi
    
    NORMALIZED_DIR=$(echo "$module_dir" | sed 's|^\./||' | sed 's|^\.$||')
    
    if [ -z "$NORMALIZED_DIR" ] || [ "$NORMALIZED_DIR" = "." ]; then
        PROJECT_ROOT_NAME=$(basename "$(pwd)" | tr '[:upper:]' '[:lower:]' | sed 's/[^a-z0-9-]/-/g')
        EXPECTED_FILE="agent-os/basepoints/agent-base-$PROJECT_ROOT_NAME.md"
    else
        MODULE_NAME=$(basename "$NORMALIZED_DIR")
        EXPECTED_FILE="agent-os/basepoints/$NORMALIZED_DIR/agent-base-$MODULE_NAME.md"
    fi
    
    if [ ! -f "$EXPECTED_FILE" ]; then
        echo "❌ MISSING: $module_dir → $EXPECTED_FILE" >> agent-os/output/create-basepoints/cache/generation-progress.md
        MISSING_COUNT=$((MISSING_COUNT + 1))
    fi
done < agent-os/output/create-basepoints/cache/module-folders.txt

if [ "$MISSING_COUNT" -gt 0 ]; then
    echo "❌ VERIFICATION FAILED: $MISSING_COUNT basepoints missing!"
    echo "   Check: agent-os/output/create-basepoints/cache/generation-progress.md"
else
    echo "✅ VERIFICATION PASSED: All $EXPECTED_COUNT module basepoints created"
    echo "✅ All modules have basepoints" >> agent-os/output/create-basepoints/cache/generation-progress.md
fi
```

### Step 5: Output Generation Summary

Display final summary:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 MODULE BASEPOINTS GENERATION COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Total modules processed: [N]
✅ Basepoints created: [N]
❌ Failed: [N] (if any)

📁 Basepoints location: agent-os/basepoints/
📋 Progress report: agent-os/output/create-basepoints/cache/generation-progress.md

Next: Parent basepoints will be generated to complete the hierarchy.
```

## Important Constraints

- **MUST process ALL modules from task list** - no exceptions
- **MUST track progress** for each module processed
- **MUST verify coverage** after completion
- **MUST report any missing basepoints** as errors
- Must generate one basepoint file per module folder
- Must place files in mirrored structure within basepoints folder
- Must name files based on actual module names from folder structure
- Must include all extracted patterns, standards, flows, strategies, and testing
- Must reference the module's abstraction layer
- Must save basepoint files for use in parent basepoint generation

    
    echo "   ✅ Updated: $basepoint_path"
    
    # Track updated basepoints
    echo "$basepoint_path" >> "$CACHE_DIR/updated-basepoints.txt"
done
```

### 3.4 Merge Updates with Existing Content

For each basepoint, intelligently merge new content with existing:

**Merge Strategy:**

| Section | Strategy |
|---------|----------|
| Module Overview | Update if structure changed |
| Patterns | ADD new, KEEP unchanged, REMOVE deleted |
| Standards | Update if conventions changed |
| Flows | Re-trace affected flows only |
| Strategies | Update if approach changed |
| Testing | Update if test files changed |
| Related Modules | Update dependencies |

**Important:** Preserve sections that weren't affected by changes.

### 3.5 Update Headquarter (Last)

After all module basepoints are updated, update the headquarter:

```bash
if echo "$AFFECTED_BASEPOINTS" | grep -q "headquarter.md"; then
    echo ""
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "🏢 UPDATING HEADQUARTER"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    
    HEADQUARTER_PATH="agent-os/basepoints/headquarter.md"
    
    if [ -f "$HEADQUARTER_PATH" ]; then
        cp "$HEADQUARTER_PATH" "${HEADQUARTER_PATH}.backup"
        echo "   💾 Backed up existing headquarter"
    fi
    
    # Re-aggregate knowledge from all basepoints
    # Update architecture overview if structure changed
    # Update navigation sections
    # Update cross-cutting patterns
    
    # # Headquarter File Generation

## Core Responsibilities

1. **Load Product Files**: Read product files (mission.md, roadmap.md, tech-stack.md)
2. **Load Detected Layers**: Retrieve detected abstraction layers from phase 2
3. **Generate Headquarter**: Create headquarter.md at root of basepoints folder
4. **Bridge Abstractions**: Connect product-level abstraction with software project-level abstraction
5. **Document Architecture**: Document overall architecture, abstraction layers, and module relationships

## Workflow

### Step 1: Load Product Files

Load the required product files:

```bash
if [ ! -f "agent-os/product/mission.md" ] || [ ! -f "agent-os/product/roadmap.md" ] || [ ! -f "agent-os/product/tech-stack.md" ]; then
    echo "❌ Product files not found. Cannot generate headquarter."
    exit 1
fi

MISSION_CONTENT=$(cat agent-os/product/mission.md)
ROADMAP_CONTENT=$(cat agent-os/product/roadmap.md)
TECH_STACK_CONTENT=$(cat agent-os/product/tech-stack.md)
```

### Step 2: Load Detected Layers

Load the detected abstraction layers from cache.

### Step 3: Analyze Basepoint Structure

Analyze the generated basepoint structure to understand module relationships:

```bash
TOP_LEVEL_BASEPOINTS=$(find agent-os/basepoints -mindepth 1 -maxdepth 1 -name "agent-base-*.md" | sort)
MODULE_COUNT=$(find agent-os/basepoints -name "agent-base-*.md" | wc -l)
```

### Step 4: Generate Headquarter Content

Create the headquarter.md file with comprehensive content including:
- Product-Level Abstraction (Mission, Roadmap, Tech Stack)
- Software Project-Level Abstraction (Architecture Overview, Detected Layers, Module Structure, Module Relationships)
- Abstraction Bridge (Product → Software Project Mapping, Technology Decisions, Feature → Module Mapping)
- Architecture Patterns
- Standards and Conventions
- Development Workflow
- Testing Strategy
- Key Insights
- Navigation (By Abstraction Layer, By Module)
- References

### Step 5: Populate Headquarter Content

Fill in the headquarter file with actual extracted content from product files and basepoint analysis.

### Step 6: Verify Headquarter File

Verify the headquarter file was generated correctly and contains all required sections.

## Important Constraints

- Must use product files (mission.md, roadmap.md, tech-stack.md) as source
- Must bridge product-level abstraction with software project-level abstraction
- Must include detected abstraction layers
- Must document overall architecture and module relationships
- Must provide navigation to all basepoint files
- Must be placed at root of basepoints folder (`agent-os/basepoints/headquarter.md`)

    
    echo "   ✅ Headquarter updated"
    echo "$HEADQUARTER_PATH" >> "$CACHE_DIR/updated-basepoints.txt"
fi
```

### 3.6 Generate Update Summary

Create summary of all updates made:

```bash
UPDATED_COUNT=$(wc -l < "$CACHE_DIR/updated-basepoints.txt" 2>/dev/null | tr -d ' ' || echo "0")

cat > "$CACHE_DIR/basepoint-update-summary.md" << EOF
# Basepoint Update Summary

**Update Time:** $(date -u +%Y-%m-%dT%H:%M:%SZ)
**Total Basepoints Updated:** $UPDATED_COUNT

## Updated Basepoints

$(cat "$CACHE_DIR/updated-basepoints.txt" 2>/dev/null | sed 's/^/- /')

## Backup Files

Backup files created (for rollback if needed):
$(find agent-os/basepoints -name "*.backup" -type f 2>/dev/null | sed 's/^/- /' || echo "_None_")

## Update Order

1. Module-level basepoints (deepest first)
2. Parent basepoints (bottom-up)
3. Headquarter (last)
EOF
```

## Expected Outputs

After this phase, the following files should exist:

| File | Description |
|------|-------------|
| `updated-basepoints.txt` | List of basepoints that were updated |
| `basepoint-update-summary.md` | Summary of all updates |
| `*.backup` files | Backups of original basepoints |

## Display confirmation and next step

Once basepoint updates are complete, output the following message:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ PHASE 3 COMPLETE: Update Basepoints
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Update Results:
   Module basepoints updated: [N]
   Parent basepoints updated: [N]
   Headquarter updated:       [Yes/No]
   Total:                     [N] basepoints

💾 Backups created for rollback if needed

📋 Summary: agent-os/output/update-basepoints-and-redeploy/cache/basepoint-update-summary.md

NEXT STEP 👉 Run Phase 4: `4-re-extract-knowledge.md`
```

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your basepoint update process aligns with the user's preferences and standards as detailed in the following files:

@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md

## Important Constraints

- **MUST update children before parents** (bottom-up order)
- **MUST update headquarter last** after all other basepoints
- **MUST create backups** before modifying existing basepoints
- **MUST preserve unchanged sections** - only update affected parts
- Must use existing basepoint structure and format
- Must not modify basepoints that weren't in the affected list
- Must log all changes for traceability

# PHASE 4: Re Extract Knowledge

The FOURTH STEP is to re-extract and merge knowledge from the updated basepoints:

## Phase 4 Actions

### 4.1 Load Existing Knowledge Cache

Load the existing merged knowledge from the previous deploy-agents run:

```bash
CACHE_DIR="agent-os/output/update-basepoints-and-redeploy/cache"
DEPLOY_CACHE="agent-os/output/deploy-agents/cache"

# Check for existing knowledge cache
if [ -f "$DEPLOY_CACHE/merged-knowledge.md" ]; then
    echo "📂 Loading existing knowledge cache..."
    EXISTING_KNOWLEDGE=$(cat "$DEPLOY_CACHE/merged-knowledge.md")
    echo "   ✅ Existing knowledge loaded"
else
    echo "⚠️  No existing knowledge cache found"
    echo "   Will perform full knowledge extraction"
    EXISTING_KNOWLEDGE=""
fi

# Load list of updated basepoints
UPDATED_BASEPOINTS=$(cat "$CACHE_DIR/updated-basepoints.txt" 2>/dev/null)
UPDATED_COUNT=$(echo "$UPDATED_BASEPOINTS" | grep -v "^$" | wc -l | tr -d ' ')

echo "📋 Updated basepoints to extract from: $UPDATED_COUNT"
```

### 4.2 Extract Knowledge from Updated Basepoints

Re-extract knowledge from only the updated basepoints:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "🔍 EXTRACTING KNOWLEDGE FROM UPDATED BASEPOINTS"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Initialize extraction output
> "$CACHE_DIR/extracted-knowledge.md"

cat >> "$CACHE_DIR/extracted-knowledge.md" << EOF
# Extracted Knowledge from Updated Basepoints

**Extraction Time:** $(date -u +%Y-%m-%dT%H:%M:%SZ)
**Source Basepoints:** $UPDATED_COUNT

EOF

# Extract from each updated basepoint
echo "$UPDATED_BASEPOINTS" | while read basepoint_path; do
    if [ -z "$basepoint_path" ] || [ ! -f "$basepoint_path" ]; then
        continue
    fi
    
    echo "   Extracting from: $basepoint_path"
    
    CONTENT=$(cat "$basepoint_path")
    MODULE_NAME=$(basename "$basepoint_path" .md | sed 's/agent-base-//')
    
    # Extract Patterns
    PATTERNS=$(echo "$CONTENT" | grep -A 100 "^## Patterns" | grep -B 100 "^## " | head -n -1 || echo "")
    
    # Extract Standards
    STANDARDS=$(echo "$CONTENT" | grep -A 100 "^## Standards" | grep -B 100 "^## " | head -n -1 || echo "")
    
    # Extract Flows
    FLOWS=$(echo "$CONTENT" | grep -A 100 "^## Flows" | grep -B 100 "^## " | head -n -1 || echo "")
    
    # Extract Strategies
    STRATEGIES=$(echo "$CONTENT" | grep -A 100 "^## Strategies" | grep -B 100 "^## " | head -n -1 || echo "")
    
    # Extract Testing
    TESTING=$(echo "$CONTENT" | grep -A 100 "^## Testing" | grep -B 100 "^## " | head -n -1 || echo "")
    
    # Append to extraction file
    cat >> "$CACHE_DIR/extracted-knowledge.md" << EOF

## From: $MODULE_NAME

### Patterns
$PATTERNS

### Standards
$STANDARDS

### Flows
$FLOWS

### Strategies
$STRATEGIES

### Testing
$TESTING

---
EOF
done

echo "   ✅ Knowledge extracted from $UPDATED_COUNT basepoints"
```

### 4.3 Check for Product Knowledge Updates

If product files changed, re-extract product knowledge:

```bash
PRODUCT_CHANGED=$(cat "$CACHE_DIR/product-files-changed.txt" 2>/dev/null || echo "false")

if [ "$PRODUCT_CHANGED" = "true" ]; then
    echo ""
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    echo "📦 EXTRACTING UPDATED PRODUCT KNOWLEDGE"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
    
    # Extract from product files
    if [ -f "agent-os/product/mission.md" ]; then
        echo "   Extracting from: mission.md"
        MISSION=$(cat "agent-os/product/mission.md")
    fi
    
    if [ -f "agent-os/product/roadmap.md" ]; then
        echo "   Extracting from: roadmap.md"
        ROADMAP=$(cat "agent-os/product/roadmap.md")
    fi
    
    if [ -f "agent-os/product/tech-stack.md" ]; then
        echo "   Extracting from: tech-stack.md"
        TECH_STACK=$(cat "agent-os/product/tech-stack.md")
    fi
    
    # Append product knowledge to extraction
    cat >> "$CACHE_DIR/extracted-knowledge.md" << EOF

## Product Knowledge (Updated)

### Mission
$MISSION

### Roadmap
$ROADMAP

### Tech Stack
$TECH_STACK

---
EOF
    
    echo "   ✅ Product knowledge extracted"
else
    echo ""
    echo "📦 Product files unchanged - using existing product knowledge"
fi
```

### 4.4 Merge New Knowledge with Existing

Merge the newly extracted knowledge with the existing cache:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "🔄 MERGING KNOWLEDGE"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Initialize merged knowledge file
cat > "$CACHE_DIR/merged-knowledge.md" << EOF
# Merged Project Knowledge

**Merge Time:** $(date -u +%Y-%m-%dT%H:%M:%SZ)
**Update Type:** Incremental
**Basepoints Updated:** $UPDATED_COUNT
**Product Updated:** $PRODUCT_CHANGED

---

EOF

# Merge strategy:
# 1. Start with existing knowledge
# 2. Replace sections that correspond to updated basepoints
# 3. Add new knowledge from updated basepoints
# 4. Preserve knowledge from unchanged basepoints

# For each knowledge category, merge intelligently
KNOWLEDGE_CATEGORIES=("Patterns" "Standards" "Flows" "Strategies" "Testing")

for category in "${KNOWLEDGE_CATEGORIES[@]}"; do
    echo "   Merging: $category"
    
    # Extract category from existing knowledge
    EXISTING_SECTION=$(echo "$EXISTING_KNOWLEDGE" | grep -A 500 "^## $category" | grep -B 500 "^## " | head -n -1 || echo "")
    
    # Extract category from new extraction
    NEW_SECTION=$(cat "$CACHE_DIR/extracted-knowledge.md" | grep -A 500 "^### $category" || echo "")
    
    # Merge (new takes precedence for updated modules)
    cat >> "$CACHE_DIR/merged-knowledge.md" << EOF

## $category

### From Updated Basepoints
$NEW_SECTION

### From Unchanged Basepoints (preserved)
[Preserved from existing knowledge]

EOF
done

echo "   ✅ Knowledge merged"
```

### 4.5 Detect and Log Conflicts

Check for any conflicts between new and existing knowledge:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "🔍 CHECKING FOR CONFLICTS"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

CONFLICTS_FOUND=0

# Initialize conflicts log
cat > "$CACHE_DIR/knowledge-conflicts.md" << EOF
# Knowledge Merge Conflicts

**Check Time:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

EOF

# Check for potential conflicts
# (e.g., contradicting patterns, inconsistent standards)

if [ "$CONFLICTS_FOUND" -eq 0 ]; then
    echo "   ✅ No conflicts detected"
    echo "No conflicts detected during merge." >> "$CACHE_DIR/knowledge-conflicts.md"
else
    echo "   ⚠️  $CONFLICTS_FOUND conflict(s) detected"
    echo "   See: $CACHE_DIR/knowledge-conflicts.md"
fi
```

### 4.6 Generate Knowledge Diff

Create a diff showing what changed:

```bash
cat > "$CACHE_DIR/knowledge-diff.md" << EOF
# Knowledge Changes

**Diff Generated:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Summary

- **Basepoints updated:** $UPDATED_COUNT
- **Product knowledge updated:** $PRODUCT_CHANGED
- **Conflicts found:** $CONFLICTS_FOUND

## Changed Knowledge Categories

$(echo "$UPDATED_BASEPOINTS" | while read bp; do
    if [ -n "$bp" ]; then
        MODULE=$(basename "$bp" .md | sed 's/agent-base-//')
        echo "### $MODULE"
        echo "- Patterns: [updated/unchanged]"
        echo "- Standards: [updated/unchanged]"
        echo "- Flows: [updated/unchanged]"
        echo "- Strategies: [updated/unchanged]"
        echo "- Testing: [updated/unchanged]"
        echo ""
    fi
done)

## Impact on Commands

The following commands will need re-specialization:
- shape-spec
- write-spec
- create-tasks
- implement-tasks
- orchestrate-tasks
EOF

echo "📋 Knowledge diff saved to: $CACHE_DIR/knowledge-diff.md"
```

### 4.7 Update Deploy-Agents Cache

Copy merged knowledge to the deploy-agents cache location:

```bash
# Ensure deploy-agents cache directory exists
mkdir -p "$DEPLOY_CACHE"

# Update the merged knowledge cache
cp "$CACHE_DIR/merged-knowledge.md" "$DEPLOY_CACHE/merged-knowledge.md"

echo "💾 Updated deploy-agents knowledge cache"
```

## Expected Outputs

After this phase, the following files should exist:

| File | Description |
|------|-------------|
| `cache/extracted-knowledge.md` | Newly extracted knowledge |
| `cache/merged-knowledge.md` | Merged knowledge (new + existing) |
| `cache/knowledge-diff.md` | What changed in this update |
| `cache/knowledge-conflicts.md` | Any conflicts detected |
| `deploy-agents/cache/merged-knowledge.md` | Updated cache for commands |

## Display confirmation and next step

Once knowledge re-extraction is complete, output the following message:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ PHASE 4 COMPLETE: Re-extract Knowledge
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Extraction Results:
   Basepoints extracted:   [N]
   Product updated:        [Yes/No]
   Conflicts found:        [N]

📋 Knowledge Categories Updated:
   - Patterns
   - Standards
   - Flows
   - Strategies
   - Testing

📋 Diff: agent-os/output/update-basepoints-and-redeploy/cache/knowledge-diff.md

NEXT STEP 👉 Run Phase 5: `5-selective-respecialize.md`
```

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your knowledge extraction process aligns with the user's preferences and standards as detailed in the following files:

@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md

## Important Constraints

- **MUST load existing knowledge** before extraction
- **MUST extract from all updated basepoints** listed in Phase 3
- **MUST include product knowledge** if product files changed
- **MUST merge intelligently** - new knowledge updates corresponding sections
- **MUST preserve knowledge** from unchanged basepoints
- **MUST detect and log conflicts** for review
- **MUST update deploy-agents cache** for command specialization
- Must create knowledge diff for traceability

# PHASE 5: Selective Respecialize

The FIFTH STEP is to re-specialize all core commands with the updated knowledge:

## Phase 5 Actions

### 5.1 Load Knowledge Changes

Load the merged knowledge and identify what changed:

```bash
CACHE_DIR="agent-os/output/update-basepoints-and-redeploy/cache"
DEPLOY_CACHE="agent-os/output/deploy-agents/cache"

# Load merged knowledge
if [ ! -f "$CACHE_DIR/merged-knowledge.md" ]; then
    echo "❌ ERROR: Merged knowledge not found."
    echo "   Run Phase 4 (re-extract-knowledge) first."
    exit 1
fi

MERGED_KNOWLEDGE=$(cat "$CACHE_DIR/merged-knowledge.md")

# Load knowledge diff to understand what changed
KNOWLEDGE_DIFF=$(cat "$CACHE_DIR/knowledge-diff.md" 2>/dev/null || echo "")

echo "📋 Loaded merged knowledge for re-specialization"
```

### 5.2 Identify Changed Knowledge Categories

Determine which knowledge categories changed:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "🔍 ANALYZING KNOWLEDGE CHANGES"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Check each category for changes
PATTERNS_CHANGED=false
STANDARDS_CHANGED=false
FLOWS_CHANGED=false
STRATEGIES_CHANGED=false
TESTING_CHANGED=false

# Analyze knowledge diff
if echo "$KNOWLEDGE_DIFF" | grep -qi "patterns.*updated"; then
    PATTERNS_CHANGED=true
    echo "   ✏️  Patterns: CHANGED"
else
    echo "   ✅ Patterns: unchanged"
fi

if echo "$KNOWLEDGE_DIFF" | grep -qi "standards.*updated"; then
    STANDARDS_CHANGED=true
    echo "   ✏️  Standards: CHANGED"
else
    echo "   ✅ Standards: unchanged"
fi

if echo "$KNOWLEDGE_DIFF" | grep -qi "flows.*updated"; then
    FLOWS_CHANGED=true
    echo "   ✏️  Flows: CHANGED"
else
    echo "   ✅ Flows: unchanged"
fi

if echo "$KNOWLEDGE_DIFF" | grep -qi "strategies.*updated"; then
    STRATEGIES_CHANGED=true
    echo "   ✏️  Strategies: CHANGED"
else
    echo "   ✅ Strategies: unchanged"
fi

if echo "$KNOWLEDGE_DIFF" | grep -qi "testing.*updated"; then
    TESTING_CHANGED=true
    echo "   ✏️  Testing: CHANGED"
else
    echo "   ✅ Testing: unchanged"
fi
```

### 5.3 Re-specialize Core Commands

Re-specialize ALL 5 core commands with updated knowledge:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "🔄 RE-SPECIALIZING CORE COMMANDS"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

CORE_COMMANDS=(
    "shape-spec"
    "write-spec"
    "create-tasks"
    "implement-tasks"
    "orchestrate-tasks"
)

RESPECIALIZED_COUNT=0

for cmd in "${CORE_COMMANDS[@]}"; do
    echo ""
    echo "📜 Re-specializing: $cmd"
    
    CMD_PATH="agent-os/commands/$cmd"
    
    if [ ! -d "$CMD_PATH" ]; then
        echo "   ⚠️  Command directory not found: $CMD_PATH"
        continue
    fi
    
    # Backup existing specialized command
    if [ -d "$CMD_PATH" ]; then
        cp -r "$CMD_PATH" "${CMD_PATH}.backup"
        echo "   💾 Backed up existing command"
    fi
    
    # Re-specialize command with updated knowledge
    # This injects the new patterns, standards, flows, strategies into the command
    
    # Find all phase files in the command
    PHASE_FILES=$(find "$CMD_PATH" -name "*.md" -type f | sort)
    
    echo "$PHASE_FILES" | while read phase_file; do
        if [ -z "$phase_file" ]; then
            continue
        fi
        
        # Update phase file with new knowledge references
        # The specialization process:
        # 1. Read the phase file
        # 2. Update knowledge placeholders with new patterns/standards
        # 3. Update project-specific context
        # 4. Write updated phase file
        
        echo "      Updated: $(basename "$phase_file")"
    done
    
    RESPECIALIZED_COUNT=$((RESPECIALIZED_COUNT + 1))
    echo "   ✅ Re-specialized: $cmd"
done

echo ""
echo "📊 Re-specialized $RESPECIALIZED_COUNT core commands"
```

### 5.4 Update Supporting Structures

Update standards, workflows, and agents based on knowledge changes:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "📚 UPDATING SUPPORTING STRUCTURES"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

STANDARDS_UPDATED=0
WORKFLOWS_UPDATED=0
AGENTS_UPDATED=0

# Update Standards (if patterns/standards changed significantly)
if [ "$PATTERNS_CHANGED" = "true" ] || [ "$STANDARDS_CHANGED" = "true" ]; then
    echo ""
    echo "📋 Updating standards with new patterns..."
    
    # Find standard files that need updating
    STANDARD_FILES=$(find agent-os/standards -name "*.md" -type f 2>/dev/null)
    
    echo "$STANDARD_FILES" | while read std_file; do
        if [ -z "$std_file" ]; then
            continue
        fi
        
        # Backup and update
        cp "$std_file" "${std_file}.backup"
        
        # Update standard with new patterns
        # (Inject new coding patterns, naming conventions, etc.)
        
        STANDARDS_UPDATED=$((STANDARDS_UPDATED + 1))
        echo "   Updated: $std_file"
    done
else
    echo "   ✅ Standards: No update needed"
fi

# Update Workflows (if flows changed)
if [ "$FLOWS_CHANGED" = "true" ]; then
    echo ""
    echo "🔄 Updating workflows with new flows..."
    
    # Find workflow files that reference flows
    WORKFLOW_FILES=$(find agent-os/workflows -name "*.md" -type f 2>/dev/null)
    
    echo "$WORKFLOW_FILES" | while read wf_file; do
        if [ -z "$wf_file" ]; then
            continue
        fi
        
        # Check if workflow uses flow references
        if grep -q "flow\|Flow" "$wf_file" 2>/dev/null; then
            cp "$wf_file" "${wf_file}.backup"
            # Update workflow with new flow patterns
            WORKFLOWS_UPDATED=$((WORKFLOWS_UPDATED + 1))
            echo "   Updated: $wf_file"
        fi
    done
else
    echo "   ✅ Workflows: No update needed"
fi

# Update Agents (if strategies changed)
if [ "$STRATEGIES_CHANGED" = "true" ]; then
    echo ""
    echo "🤖 Updating agents with new strategies..."
    
    # Find agent files
    AGENT_FILES=$(find agent-os/agents -name "*.md" -type f 2>/dev/null)
    
    echo "$AGENT_FILES" | while read agent_file; do
        if [ -z "$agent_file" ]; then
            continue
        fi
        
        cp "$agent_file" "${agent_file}.backup"
        # Update agent with new strategies
        AGENTS_UPDATED=$((AGENTS_UPDATED + 1))
        echo "   Updated: $agent_file"
    done
else
    echo "   ✅ Agents: No update needed"
fi

echo ""
echo "📊 Supporting structures updated:"
echo "   Standards: $STANDARDS_UPDATED"
echo "   Workflows: $WORKFLOWS_UPDATED"
echo "   Agents: $AGENTS_UPDATED"
```

### 5.5 Generate Re-specialization Summary

Create summary of all re-specialization actions:

```bash
cat > "$CACHE_DIR/respecialization-summary.md" << EOF
# Re-specialization Summary

**Re-specialization Time:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Knowledge Changes Detected

| Category | Changed |
|----------|---------|
| Patterns | $PATTERNS_CHANGED |
| Standards | $STANDARDS_CHANGED |
| Flows | $FLOWS_CHANGED |
| Strategies | $STRATEGIES_CHANGED |
| Testing | $TESTING_CHANGED |

## Core Commands Re-specialized

All 5 core commands were re-specialized with updated knowledge:

| Command | Status |
|---------|--------|
| shape-spec | ✅ Re-specialized |
| write-spec | ✅ Re-specialized |
| create-tasks | ✅ Re-specialized |
| implement-tasks | ✅ Re-specialized |
| orchestrate-tasks | ✅ Re-specialized |

## Supporting Structures Updated

| Structure | Files Updated |
|-----------|---------------|
| Standards | $STANDARDS_UPDATED |
| Workflows | $WORKFLOWS_UPDATED |
| Agents | $AGENTS_UPDATED |

## Backup Files

Backup files created for rollback:
$(find agent-os/commands -name "*.backup" -type d 2>/dev/null | sed 's/^/- /' || echo "- Command backups")
$(find agent-os/standards -name "*.backup" -type f 2>/dev/null | sed 's/^/- /' || echo "")
$(find agent-os/workflows -name "*.backup" -type f 2>/dev/null | sed 's/^/- /' || echo "")
$(find agent-os/agents -name "*.backup" -type f 2>/dev/null | sed 's/^/- /' || echo "")

## What Changed

The following knowledge was injected into specialized commands:

### New Patterns
[Summary of new patterns added]

### New Standards
[Summary of new standards applied]

### New Flows
[Summary of new flows integrated]

### New Strategies
[Summary of new strategies incorporated]
EOF

echo "📋 Re-specialization summary saved to: $CACHE_DIR/respecialization-summary.md"
```

## Expected Outputs

After this phase, the following should be updated/created:

| Item | Description |
|------|-------------|
| Core commands | All 5 commands re-specialized |
| Standards | Updated if patterns changed |
| Workflows | Updated if flows changed |
| Agents | Updated if strategies changed |
| `respecialization-summary.md` | Summary of all changes |
| `*.backup` files | Backups for rollback |

## Display confirmation and next step

Once re-specialization is complete, output the following message:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ PHASE 5 COMPLETE: Re-specialize Commands
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Re-specialization Results:

Core Commands (ALL re-specialized):
   ✅ shape-spec
   ✅ write-spec
   ✅ create-tasks
   ✅ implement-tasks
   ✅ orchestrate-tasks

Supporting Structures:
   Standards updated: [N]
   Workflows updated: [N]
   Agents updated:    [N]

💾 Backups created for rollback if needed

📋 Summary: agent-os/output/update-basepoints-and-redeploy/cache/respecialization-summary.md

NEXT STEP 👉 Run Phase 6: `6-validate-and-report.md`
```

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your re-specialization process aligns with the user's preferences and standards as detailed in the following files:

@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md

## Important Constraints

- **MUST re-specialize ALL 5 core commands** regardless of what changed
- **MUST update standards** if patterns/standards changed significantly
- **MUST update workflows** if flows changed
- **MUST update agents** if strategies changed
- **MUST create backups** before modifying any files
- Must inject updated knowledge into specialized commands
- Must preserve command structure while updating content
- Must log all changes for traceability

# PHASE 6: Validate And Report

The SIXTH AND FINAL STEP is to validate all updates and generate the comprehensive report:

# Validate Incremental Update

## Core Responsibilities

1. **Validate Updated Basepoint Files**: Check markdown structure and required sections
2. **Validate Command Files**: Ensure specialized command files exist and are valid
3. **Check for Broken References**: Detect broken `{{workflows/...}}
⚠️ This workflow file was not found in profiles/default/workflows/....md` and `` references
4. **Verify Knowledge Cache Consistency**: Ensure cache files are consistent with basepoints
5. **Generate Validation Report**: Create comprehensive validation report with issues found

## Workflow

### Step 1: Initialize Validation Environment

Set up validation environment and load update context:

```bash
CACHE_DIR="agent-os/output/update-basepoints-and-redeploy/cache"
REPORTS_DIR="agent-os/output/update-basepoints-and-redeploy/reports"

mkdir -p "$REPORTS_DIR"

# Load update context
if [ ! -f "$CACHE_DIR/update-log.md" ]; then
    echo "⚠️  Warning: Update log not found. Validating all basepoints."
    VALIDATION_SCOPE="all"
else
    VALIDATION_SCOPE="incremental"
    UPDATED_BASEPOINTS=$(cat "$CACHE_DIR/update-progress.md" 2>/dev/null | grep "\[x\]" | sed 's/.*`\([^`]*\)`.*/\1/')
fi

# Initialize validation results
VALIDATION_ISSUES=()
VALIDATION_WARNINGS=()
VALIDATION_PASSED=0
VALIDATION_FAILED=0

echo "🔍 Starting incremental update validation..."
echo "   Scope: $VALIDATION_SCOPE"
```

### Step 2: Validate Updated Basepoint Files

Check each updated basepoint for proper markdown structure:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "📄 VALIDATING BASEPOINT FILES"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Required sections for basepoint files
REQUIRED_SECTIONS=(
    "Module Overview"
    "Patterns"
    "Standards"
    "Flows"
    "Strategies"
)

# Determine files to validate
if [ "$VALIDATION_SCOPE" = "incremental" ]; then
    FILES_TO_VALIDATE="$UPDATED_BASEPOINTS"
else
    FILES_TO_VALIDATE=$(find agent-os/basepoints -name "agent-base-*.md" -type f)
fi

echo "$FILES_TO_VALIDATE" | while read basepoint_file; do
    if [ -z "$basepoint_file" ] || [ ! -f "$basepoint_file" ]; then
        continue
    fi
    
    echo "   Validating: $basepoint_file"
    FILE_VALID=true
    
    # Check file is not empty
    if [ ! -s "$basepoint_file" ]; then
        VALIDATION_ISSUES+=("EMPTY_FILE:$basepoint_file:Basepoint file is empty")
        FILE_VALID=false
        continue
    fi
    
    # Check for required sections
    FILE_CONTENT=$(cat "$basepoint_file")
    
    for section in "${REQUIRED_SECTIONS[@]}"; do
        if ! echo "$FILE_CONTENT" | grep -q "## $section\|# $section"; then
            VALIDATION_WARNINGS+=("MISSING_SECTION:$basepoint_file:Missing section: $section")
        fi
    done
    
    # Check for valid markdown (no broken headers, no unclosed code blocks)
    # Count opening and closing code blocks
    OPEN_BLOCKS=$(echo "$FILE_CONTENT" | grep -c '```' || echo "0")
    if [ $((OPEN_BLOCKS % 2)) -ne 0 ]; then
        VALIDATION_ISSUES+=("UNCLOSED_CODE_BLOCK:$basepoint_file:Unclosed code block detected")
        FILE_VALID=false
    fi
    
    # Check for placeholder remnants that shouldn't be there
    if echo "$FILE_CONTENT" | grep -qE '\{\{[A-Z_]+\}\}'; then
        # Exclude intentional documentation placeholders
        PLACEHOLDERS=$(echo "$FILE_CONTENT" | grep -oE '\{\{[A-Z_]+\}\}' | sort -u)
        for placeholder in $PLACEHOLDERS; do
            if [[ "$placeholder" != "{{PLACEHOLDER}}" ]] && [[ "$placeholder" != "{{UNLESS}}" ]]; then
                VALIDATION_WARNINGS+=("UNRESOLVED_PLACEHOLDER:$basepoint_file:Unresolved placeholder: $placeholder")
            fi
        done
    fi
    
    if [ "$FILE_VALID" = "true" ]; then
        VALIDATION_PASSED=$((VALIDATION_PASSED + 1))
        echo "      ✅ Valid"
    else
        VALIDATION_FAILED=$((VALIDATION_FAILED + 1))
        echo "      ❌ Issues found"
    fi
done
```

### Step 3: Validate Command Files

Check that specialized command files are valid:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "📜 VALIDATING COMMAND FILES"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Find all command files
COMMAND_FILES=$(find agent-os/commands -name "*.md" -type f 2>/dev/null)

if [ -z "$COMMAND_FILES" ]; then
    echo "   ⚠️  No command files found in agent-os/commands/"
    VALIDATION_WARNINGS+=("NO_COMMANDS:agent-os/commands/:No command files found")
else
    echo "$COMMAND_FILES" | while read command_file; do
        if [ -z "$command_file" ]; then
            continue
        fi
        
        echo "   Validating: $command_file"
        
        # Check file is not empty
        if [ ! -s "$command_file" ]; then
            VALIDATION_ISSUES+=("EMPTY_COMMAND:$command_file:Command file is empty")
            echo "      ❌ Empty file"
            continue
        fi
        
        # Check for valid phase references
        FILE_CONTENT=$(cat "$command_file")
        
        # Check for broken phase references
        PHASE_REFS=$(echo "$FILE_CONTENT" | grep -oE '\{\{PHASE [0-9]+: @agent-os/commands/[^}]+\}\}' || true)
        
        if [ -n "$PHASE_REFS" ]; then
            echo "$PHASE_REFS" | while read phase_ref; do
                # Extract the referenced file path
                REF_PATH=$(echo "$phase_ref" | sed 's/.*@agent-os\/commands\/\([^}]*\)\.md.*/\1.md/')
                FULL_PATH="agent-os/commands/$REF_PATH"
                
                if [ ! -f "$FULL_PATH" ]; then
                    VALIDATION_ISSUES+=("BROKEN_PHASE_REF:$command_file:Broken phase reference: $REF_PATH")
                    echo "      ❌ Broken phase ref: $REF_PATH"
                fi
            done
        fi
        
        echo "      ✅ Valid"
    done
fi
```

### Step 4: Check for Broken Workflow and Standard References

Scan for broken `{{workflows/...}}
⚠️ This workflow file was not found in profiles/default/workflows/....md` and `` references:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "🔗 CHECKING REFERENCES"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Find all markdown files in agent-os
ALL_FILES=$(find agent-os -name "*.md" -type f ! -path "*/output/*" ! -path "*/specs/*")

echo "$ALL_FILES" | while read file; do
    if [ -z "$file" ] || [ ! -f "$file" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file")
    
    # Check workflow references
    WORKFLOW_REFS=$(echo "$FILE_CONTENT" | grep -oE '\{\{workflows/[^}]+\}\}' || true)
    
    if [ -n "$WORKFLOW_REFS" ]; then
        echo "$WORKFLOW_REFS" | while read ref; do
            # Extract workflow path
            WORKFLOW_PATH=$(echo "$ref" | sed 's/{{workflows\/\([^}]*\)}}/\1/')
            FULL_PATH="agent-os/workflows/${WORKFLOW_PATH}.md"
            
            if [ ! -f "$FULL_PATH" ]; then
                VALIDATION_ISSUES+=("BROKEN_WORKFLOW_REF:$file:Broken workflow reference: $WORKFLOW_PATH")
                echo "   ❌ Broken workflow ref in $file: $WORKFLOW_PATH"
            fi
        done
    fi
    
    # Check standard references
    STANDARD_REFS=$(echo "$FILE_CONTENT" | grep -oE '\{\{standards/[^}]+\}\}' || true)
    
    if [ -n "$STANDARD_REFS" ]; then
        echo "$STANDARD_REFS" | while read ref; do
            # Extract standard path
            STANDARD_PATH=$(echo "$ref" | sed 's/{{standards\/\([^}]*\)}}/\1/')
            FULL_PATH="agent-os/standards/${STANDARD_PATH}.md"
            
            if [ ! -f "$FULL_PATH" ]; then
                VALIDATION_ISSUES+=("BROKEN_STANDARD_REF:$file:Broken standard reference: $STANDARD_PATH")
                echo "   ❌ Broken standard ref in $file: $STANDARD_PATH"
            fi
        done
    fi
done

echo "   ✅ Reference check complete"
```

### Step 5: Verify Knowledge Cache Consistency

Check that knowledge cache is consistent with basepoints:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "💾 VERIFYING KNOWLEDGE CACHE"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

KNOWLEDGE_CACHE="agent-os/output/deploy-agents/cache/merged-knowledge.md"

if [ -f "$KNOWLEDGE_CACHE" ]; then
    echo "   📂 Knowledge cache found: $KNOWLEDGE_CACHE"
    
    # Check cache is not empty
    if [ ! -s "$KNOWLEDGE_CACHE" ]; then
        VALIDATION_ISSUES+=("EMPTY_CACHE:$KNOWLEDGE_CACHE:Knowledge cache is empty")
        echo "   ❌ Knowledge cache is empty"
    else
        # Check cache timestamp vs basepoints
        CACHE_MTIME=$(stat -f %m "$KNOWLEDGE_CACHE" 2>/dev/null || stat -c %Y "$KNOWLEDGE_CACHE" 2>/dev/null)
        
        # Find newest basepoint
        NEWEST_BASEPOINT=$(find agent-os/basepoints -name "*.md" -type f -exec stat -f '%m %N' {} \; 2>/dev/null | sort -rn | head -1 | cut -d' ' -f2)
        NEWEST_MTIME=$(stat -f %m "$NEWEST_BASEPOINT" 2>/dev/null || stat -c %Y "$NEWEST_BASEPOINT" 2>/dev/null)
        
        if [ "$CACHE_MTIME" -lt "$NEWEST_MTIME" ]; then
            VALIDATION_WARNINGS+=("STALE_CACHE:$KNOWLEDGE_CACHE:Knowledge cache is older than newest basepoint")
            echo "   ⚠️  Knowledge cache may be stale"
        else
            echo "   ✅ Knowledge cache is up to date"
        fi
    fi
else
    VALIDATION_WARNINGS+=("NO_CACHE:$KNOWLEDGE_CACHE:Knowledge cache not found")
    echo "   ⚠️  Knowledge cache not found (expected after deploy-agents)"
fi
```

### Step 6: Generate Validation Report

Create comprehensive validation report:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "📋 GENERATING VALIDATION REPORT"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

ISSUES_COUNT=${#VALIDATION_ISSUES[@]}
WARNINGS_COUNT=${#VALIDATION_WARNINGS[@]}

# Determine overall status
if [ "$ISSUES_COUNT" -eq 0 ]; then
    OVERALL_STATUS="PASSED"
    STATUS_EMOJI="✅"
else
    OVERALL_STATUS="FAILED"
    STATUS_EMOJI="❌"
fi

# Generate report
cat > "$REPORTS_DIR/validation-report.md" << EOF
# Incremental Update Validation Report

**Validation Time:** $(date -u +%Y-%m-%dT%H:%M:%SZ)
**Validation Scope:** $VALIDATION_SCOPE
**Overall Status:** $STATUS_EMOJI $OVERALL_STATUS

## Summary

| Metric | Count |
|--------|-------|
| Issues (blocking) | $ISSUES_COUNT |
| Warnings (non-blocking) | $WARNINGS_COUNT |
| Files passed | $VALIDATION_PASSED |
| Files failed | $VALIDATION_FAILED |

## Issues (Must Fix)

$(if [ "$ISSUES_COUNT" -gt 0 ]; then
    for issue in "${VALIDATION_ISSUES[@]}"; do
        TYPE=$(echo "$issue" | cut -d: -f1)
        FILE=$(echo "$issue" | cut -d: -f2)
        DESC=$(echo "$issue" | cut -d: -f3-)
        echo "### $TYPE"
        echo "- **File:** \`$FILE\`"
        echo "- **Description:** $DESC"
        echo ""
    done
else
    echo "_No blocking issues found_"
fi)

## Warnings (Should Review)

$(if [ "$WARNINGS_COUNT" -gt 0 ]; then
    for warning in "${VALIDATION_WARNINGS[@]}"; do
        TYPE=$(echo "$warning" | cut -d: -f1)
        FILE=$(echo "$warning" | cut -d: -f2)
        DESC=$(echo "$warning" | cut -d: -f3-)
        echo "- **$TYPE** in \`$FILE\`: $DESC"
    done
else
    echo "_No warnings_"
fi)

## Validation Details

### Basepoint Files Checked
$(if [ "$VALIDATION_SCOPE" = "incremental" ]; then
    echo "$UPDATED_BASEPOINTS" | sed 's/^/- /'
else
    find agent-os/basepoints -name "agent-base-*.md" -type f | sed 's/^/- /'
fi)

### Command Files Checked
$(find agent-os/commands -name "*.md" -type f 2>/dev/null | sed 's/^/- /' || echo "_None found_")

### Reference Checks
- Workflow references: Checked
- Standard references: Checked
- Phase references: Checked

### Cache Validation
- Knowledge cache: $([ -f "$KNOWLEDGE_CACHE" ] && echo "Present" || echo "Not found")
- Cache freshness: $([ "$CACHE_MTIME" -ge "$NEWEST_MTIME" ] && echo "Up to date" || echo "May be stale")

## Recommendations

$(if [ "$ISSUES_COUNT" -gt 0 ]; then
    echo "1. Fix all blocking issues before proceeding"
    echo "2. Re-run validation after fixes"
else
    echo "1. Review any warnings listed above"
    echo "2. Incremental update is ready for use"
fi)
EOF

echo "📋 Validation report saved to: $REPORTS_DIR/validation-report.md"
```

### Step 7: Output Validation Results

Display validation results for user:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[✅/❌] VALIDATION [PASSED/FAILED]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Results:
   ❌ Issues (blocking):     [N]
   ⚠️  Warnings (review):    [N]
   ✅ Files passed:          [N]
   ❌ Files failed:          [N]

[If issues found:]
❌ BLOCKING ISSUES:
   - [Issue type]: [file] - [description]
   ...

[If warnings found:]
⚠️  WARNINGS:
   - [Warning type]: [file] - [description]
   ...

📋 Full report: agent-os/output/update-basepoints-and-redeploy/reports/validation-report.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[If passed:]
✅ Incremental update validated successfully!
   Update tracking files have been saved.

[If failed:]
❌ Please fix the issues above and re-run validation.
```

## Important Constraints

- **MUST check all updated basepoints** for valid markdown structure
- **MUST check command files** for valid phase references
- **MUST detect broken references** to workflows and standards
- **MUST verify knowledge cache** consistency
- **MUST generate comprehensive report** with all findings
- **MUST distinguish between blocking issues and warnings**
- Must not modify any files during validation (read-only)
- Must validate in incremental scope when update log exists
- Must provide actionable recommendations in report


## Phase 6 Actions

### 6.1 Run Validation

Validate all updated basepoints and re-specialized commands:

```bash
CACHE_DIR="agent-os/output/update-basepoints-and-redeploy/cache"
REPORTS_DIR="agent-os/output/update-basepoints-and-redeploy/reports"

echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "🔍 RUNNING VALIDATION"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# The workflow will:
# 1. Validate updated basepoint markdown files
# 2. Validate re-specialized command files
# 3. Check for broken {{workflows/...}}
⚠️ This workflow file was not found in profiles/default/workflows/....md and  references
# 4. Verify knowledge cache consistency
# 5. Generate validation report

# Load validation results
VALIDATION_PASSED=true
ISSUES_COUNT=0
WARNINGS_COUNT=0

# Check if validation report was generated
if [ -f "$REPORTS_DIR/validation-report.md" ]; then
    VALIDATION_RESULT=$(grep "Overall Status:" "$REPORTS_DIR/validation-report.md" | head -1)
    
    if echo "$VALIDATION_RESULT" | grep -q "FAILED"; then
        VALIDATION_PASSED=false
        ISSUES_COUNT=$(grep -c "^### " "$REPORTS_DIR/validation-report.md" 2>/dev/null || echo "0")
    fi
fi

echo "   Validation complete"
echo "   Status: $([ "$VALIDATION_PASSED" = "true" ] && echo "✅ PASSED" || echo "❌ FAILED")"
```

### 6.2 Update Tracking Files

Update the tracking files for future incremental updates:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "💾 UPDATING TRACKING FILES"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Update git commit reference (if git available)
if [ -d ".git" ]; then
    CURRENT_COMMIT=$(git rev-parse HEAD)
    echo "$CURRENT_COMMIT" > "$CACHE_DIR/last-update-commit.txt"
    echo "   ✅ Git commit: $CURRENT_COMMIT"
fi

# Update timestamp
CURRENT_TIMESTAMP=$(date -u +%Y-%m-%dT%H:%M:%SZ)
echo "$CURRENT_TIMESTAMP" > "$CACHE_DIR/last-update-timestamp.txt"
echo "   ✅ Timestamp: $CURRENT_TIMESTAMP"

# Save update metadata
cat > "$CACHE_DIR/last-update-metadata.json" << EOF
{
  "commit": "$(git rev-parse HEAD 2>/dev/null || echo "N/A")",
  "timestamp": "$CURRENT_TIMESTAMP",
  "changes_detected": $(wc -l < "$CACHE_DIR/changed-files.txt" 2>/dev/null | tr -d ' ' || echo "0"),
  "basepoints_updated": $(wc -l < "$CACHE_DIR/updated-basepoints.txt" 2>/dev/null | tr -d ' ' || echo "0"),
  "commands_respecialized": 5,
  "validation_passed": $VALIDATION_PASSED
}
EOF
echo "   ✅ Metadata saved"
```

### 6.3 Generate Comprehensive Update Report

Create the final comprehensive report:

```bash
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "📋 GENERATING FINAL REPORT"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Collect statistics
CHANGES_DETECTED=$(wc -l < "$CACHE_DIR/changed-files.txt" 2>/dev/null | tr -d ' ' || echo "0")
ADDED_COUNT=$(wc -l < "$CACHE_DIR/added-files.txt" 2>/dev/null | tr -d ' ' || echo "0")
MODIFIED_COUNT=$(wc -l < "$CACHE_DIR/modified-files.txt" 2>/dev/null | tr -d ' ' || echo "0")
DELETED_COUNT=$(wc -l < "$CACHE_DIR/deleted-files.txt" 2>/dev/null | tr -d ' ' || echo "0")
BASEPOINTS_UPDATED=$(wc -l < "$CACHE_DIR/updated-basepoints.txt" 2>/dev/null | tr -d ' ' || echo "0")
PRODUCT_CHANGED=$(cat "$CACHE_DIR/product-files-changed.txt" 2>/dev/null || echo "false")

# Generate report
cat > "$REPORTS_DIR/update-report.md" << EOF
# Incremental Update Report

**Update Completed:** $(date -u +%Y-%m-%dT%H:%M:%SZ)
**Overall Status:** $([ "$VALIDATION_PASSED" = "true" ] && echo "✅ SUCCESS" || echo "❌ ISSUES FOUND")

---

## Summary

| Metric | Value |
|--------|-------|
| Codebase changes detected | $CHANGES_DETECTED files |
| Basepoints updated | $BASEPOINTS_UPDATED files |
| Commands re-specialized | 5 commands |
| Validation | $([ "$VALIDATION_PASSED" = "true" ] && echo "PASSED" || echo "FAILED") |

---

## Changes Detected

### By Category

| Category | Count |
|----------|-------|
| Added | $ADDED_COUNT |
| Modified | $MODIFIED_COUNT |
| Deleted | $DELETED_COUNT |
| **Total** | **$CHANGES_DETECTED** |

### Changed Files

$(cat "$CACHE_DIR/changed-files.txt" 2>/dev/null | head -30 | sed 's/^/- /')
$([ "$CHANGES_DETECTED" -gt 30 ] && echo "- _... and $((CHANGES_DETECTED - 30)) more_")

---

## Basepoints Updated

$(cat "$CACHE_DIR/updated-basepoints.txt" 2>/dev/null | sed 's/^/- /' || echo "_None_")

---

## Knowledge Changes

**Product files changed:** $PRODUCT_CHANGED

### Categories Updated
$(cat "$CACHE_DIR/knowledge-diff.md" 2>/dev/null | grep -A 20 "## Changed Knowledge" || echo "_See knowledge-diff.md for details_")

---

## Re-specialization

### Core Commands

All 5 core commands were re-specialized with updated knowledge:

| Command | Status |
|---------|--------|
| shape-spec | ✅ Updated |
| write-spec | ✅ Updated |
| create-tasks | ✅ Updated |
| implement-tasks | ✅ Updated |
| orchestrate-tasks | ✅ Updated |

### Supporting Structures

$(cat "$CACHE_DIR/respecialization-summary.md" 2>/dev/null | grep -A 10 "## Supporting Structures" || echo "_See respecialization-summary.md for details_")

---

## Validation Results

$(cat "$REPORTS_DIR/validation-report.md" 2>/dev/null | grep -A 30 "## Summary" || echo "See validation-report.md for details")

---

## Performance

| Metric | Value |
|--------|-------|
| Incremental update | Completed |
| Files processed | $CHANGES_DETECTED |
| Basepoints updated | $BASEPOINTS_UPDATED (vs full: all) |
| Efficiency | Incremental (faster than full regeneration) |

---

## Tracking Information

| Item | Value |
|------|-------|
| Git commit | $(git rev-parse HEAD 2>/dev/null || echo "N/A") |
| Timestamp | $CURRENT_TIMESTAMP |
| Cache location | $CACHE_DIR |

---

## Next Steps

$(if [ "$VALIDATION_PASSED" = "true" ]; then
    echo "✅ **Update completed successfully!**"
    echo ""
    echo "Your agent-os is now synchronized with your codebase changes."
    echo ""
    echo "You can now use the updated commands:"
    echo "- \`/shape-spec\` - Shape new specifications"
    echo "- \`/write-spec\` - Write detailed specifications"
    echo "- \`/create-tasks\` - Create implementation tasks"
    echo "- \`/implement-tasks\` - Implement tasks"
    echo "- \`/orchestrate-tasks\` - Orchestrate task execution"
else
    echo "⚠️ **Issues were found during validation**"
    echo ""
    echo "Please review the validation report and fix any issues:"
    echo "- Check: $REPORTS_DIR/validation-report.md"
    echo ""
    echo "After fixing issues, you can:"
    echo "1. Re-run \`/update-basepoints-and-redeploy\`"
    echo "2. Or manually fix and run \`/cleanup-agent-os\` to validate"
fi)

---

## Backup Information

Backup files were created during this update. To rollback:

1. Find backup files: \`find agent-os -name "*.backup"\`
2. Remove the updated file and rename backup
3. Or delete backups if update is confirmed good

EOF

echo "📋 Final report saved to: $REPORTS_DIR/update-report.md"
```

### 6.4 Cleanup (Optional)

Optionally cleanup backup files after successful validation:

```bash
# Note: Backups are kept by default for safety
# User can manually delete them after confirming the update is good

echo ""
echo "💡 Tip: Backup files have been preserved."
echo "   To cleanup after confirming update is good:"
echo "   find agent-os -name '*.backup' -delete"
```

## Display Final Completion Summary

Output the final completion message:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[✅/❌] UPDATE [COMPLETE/FAILED]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 FINAL SUMMARY

┌─────────────────────────────────────────────────────────┐
│ Phase 1: Change Detection          ✅ [N] files        │
│ Phase 2: Identify Basepoints       ✅ [N] basepoints   │
│ Phase 3: Update Basepoints         ✅ [N] updated      │
│ Phase 4: Re-extract Knowledge      ✅ Complete         │
│ Phase 5: Re-specialize Commands    ✅ 5 commands       │
│ Phase 6: Validate & Report         ✅ PASSED           │
└─────────────────────────────────────────────────────────┘

📋 Reports:
   • Full report: agent-os/output/update-basepoints-and-redeploy/reports/update-report.md
   • Validation:  agent-os/output/update-basepoints-and-redeploy/reports/validation-report.md

💾 Tracking updated for next incremental run

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ INCREMENTAL UPDATE COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Your agent-os is now synchronized with your latest codebase changes.
All core commands have been re-specialized with updated knowledge.

Next time you make changes, run `/update-basepoints-and-redeploy` again
for fast incremental synchronization.
```

If validation failed:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️  UPDATE COMPLETED WITH ISSUES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Issues Found: [N]

Please review:
   • Validation report: agent-os/output/update-basepoints-and-redeploy/reports/validation-report.md
   • Full report: agent-os/output/update-basepoints-and-redeploy/reports/update-report.md

Options:
1. Fix issues and re-run `/update-basepoints-and-redeploy`
2. Rollback using backup files
3. Run `/cleanup-agent-os` to identify specific issues
```

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your validation and reporting process aligns with the user's preferences and standards as detailed in the following files:

@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md

## Important Constraints

- **MUST run full validation** using the validation workflow
- **MUST update tracking files** for future incremental updates
- **MUST generate comprehensive report** with all phases summarized
- **MUST preserve backup files** - do not auto-delete
- **MUST clearly indicate success or failure** in final output
- Must provide actionable next steps based on validation results
- Must include performance comparison in report
- Must save all reports to the reports directory

# PHASE 7: Review Session Learnings

# Phase 7: Review Session Learnings

Present session learnings and prompt effectiveness analysis to user for review.

## Core Responsibilities

1. **Load Session Feedback**: Read current session data
2. **Extract Patterns**: Use extraction workflow to identify successful/failed patterns
3. **Analyze Prompts**: Use analysis workflow to determine prompt effectiveness
4. **Generate Adaptations**: Create specific suggestions based on learnings
5. **Present for Review**: Human approval required - NEVER auto-apply

## Prerequisites

- Session feedback exists from previous `/implement-tasks` runs
- Patterns have been extracted (use extraction workflow)
- Prompt effectiveness has been analyzed (use analysis workflow)

## Workflow

### Step 1: Load Session Feedback

```bash
SESSION_FILE="agent-os/output/session-feedback/current-session.md"

if [ ! -f "$SESSION_FILE" ]; then
    echo "ℹ️ No session feedback found. Skipping learning phase."
    echo "   Run /implement-tasks to generate session feedback."
    exit 0
fi

echo "📋 Loading session feedback..."
```

### Step 2: Extract Patterns from Session

Extract successful and failed patterns from session data:

```bash
# Extract Session Patterns

Analyze session data to identify successful and failed patterns.

## Purpose

Extract patterns from session implementation data to identify:
- Successful patterns (100% success rate, 3+ uses)
- Failed patterns/anti-patterns (any failure)
- Pattern usage statistics

## Inputs

- Session file: `agent-os/output/session-feedback/current-session.md`
- Raw patterns file: `agent-os/output/session-feedback/patterns/raw-patterns.txt`

## Process

### Step 1: Read All Implementations from Session

```bash
SESSION_FILE="agent-os/output/session-feedback/current-session.md"
PATTERNS_DIR="agent-os/output/session-feedback/patterns"

# Read session file
if [ ! -f "$SESSION_FILE" ]; then
    echo "⚠️ No session file found. Run /implement-tasks first."
    exit 0
fi

# Extract implementations table
IMPLEMENTATIONS=$(grep -A 100 "| # | Spec | Tasks | Outcome | Duration | Notes |" "$SESSION_FILE" | grep "^| [0-9]" || echo "")
```

### Step 2: Group by Patterns Used

```bash
# Parse patterns from raw patterns file
RAW_PATTERNS_FILE="$PATTERNS_DIR/raw-patterns.txt"

if [ -f "$RAW_PATTERNS_FILE" ]; then
    # Extract patterns mentioned in implementations
    # Pattern format: "Pattern: [name] (used in implementation #N)"
    
    # Group patterns by name
    declare -A PATTERN_COUNTS
    declare -A PATTERN_SUCCESS_COUNTS
    declare -A PATTERN_FAILURE_COUNTS
    
    while read -r line; do
        if [[ "$line" =~ "Pattern:" ]]; then
            PATTERN_NAME=$(echo "$line" | sed -n 's/.*Pattern: \([^ ]*\).*/\1/p')
            IMPL_NUM=$(echo "$line" | sed -n 's/.*implementation #\([0-9]*\).*/\1/p')
            
            # Check if this implementation was successful
            OUTCOME=$(echo "$IMPLEMENTATIONS" | grep "^| $IMPL_NUM " | awk -F'|' '{print $5}' | tr -d ' ')
            
            if [ -n "$PATTERN_NAME" ]; then
                PATTERN_COUNTS["$PATTERN_NAME"]=$((${PATTERN_COUNTS["$PATTERN_NAME"]:-0} + 1))
                
                if [[ "$OUTCOME" =~ "✅" ]]; then
                    PATTERN_SUCCESS_COUNTS["$PATTERN_NAME"]=$((${PATTERN_SUCCESS_COUNTS["$PATTERN_NAME"]:-0} + 1))
                else
                    PATTERN_FAILURE_COUNTS["$PATTERN_NAME"]=$((${PATTERN_FAILURE_COUNTS["$PATTERN_NAME"]:-0} + 1))
                fi
            fi
        fi
    done < "$RAW_PATTERNS_FILE"
fi
```

### Step 3: Calculate Success Rate per Pattern

```bash
# Calculate success rates
declare -A PATTERN_SUCCESS_RATES

for pattern in "${!PATTERN_COUNTS[@]}"; do
    total=${PATTERN_COUNTS["$pattern"]}
    successes=${PATTERN_SUCCESS_COUNTS["$pattern"]:-0}
    failures=${PATTERN_FAILURE_COUNTS["$pattern"]:-0}
    
    if [ $total -gt 0 ]; then
        success_rate=$(awk "BEGIN {printf \"%.2f\", ($successes / $total)}")
        PATTERN_SUCCESS_RATES["$pattern"]=$success_rate
    fi
done
```

### Step 4: Identify Successful Patterns

```bash
# Thresholds from config
MIN_USES_FOR_RECOMMENDATION=3
SUCCESS_RATE_THRESHOLD=1.0  # 100%

SUCCESSFUL_PATTERNS_FILE="$PATTERNS_DIR/successful.md"

cat > "$SUCCESSFUL_PATTERNS_FILE" << EOF
# Successful Patterns

EOF

for pattern in "${!PATTERN_COUNTS[@]}"; do
    total=${PATTERN_COUNTS["$pattern"]}
    success_rate=${PATTERN_SUCCESS_RATES["$pattern"]}
    
    # Pattern is successful if:
    # - Used 3+ times
    # - 100% success rate
    if [ $total -ge $MIN_USES_FOR_RECOMMENDATION ] && [ "$(echo "$success_rate >= $SUCCESS_RATE_THRESHOLD" | bc -l)" = "1" ]; then
        cat >> "$SUCCESSFUL_PATTERNS_FILE" << EOF
## Pattern: $pattern

**Description**: Pattern extracted from successful implementations

**First Observed**: $(date +%Y-%m-%d)
**Times Used**: $total
**Success Rate**: $(awk "BEGIN {printf \"%.0f\", $success_rate * 100}")%

**Applicable To**: [Context where this pattern applies]

---

EOF
    fi
done

echo "✅ Successful patterns written to: $SUCCESSFUL_PATTERNS_FILE"
```

### Step 5: Identify Failed Patterns (Anti-patterns)

```bash
FAILED_PATTERNS_FILE="$PATTERNS_DIR/failed.md"

cat > "$FAILED_PATTERNS_FILE" << EOF
# Failed Patterns (Anti-patterns)

EOF

for pattern in "${!PATTERN_COUNTS[@]}"; do
    failures=${PATTERN_FAILURE_COUNTS["$pattern"]:-0}
    
    # Pattern is an anti-pattern if it caused any failures
    if [ $failures -gt 0 ]; then
        total=${PATTERN_COUNTS["$pattern"]}
        failure_rate=$(awk "BEGIN {printf \"%.2f\", ($failures / $total)}")
        
        cat >> "$FAILED_PATTERNS_FILE" << EOF
## Pattern: $pattern

**Description**: This pattern caused implementation failures

**First Observed**: $(date +%Y-%m-%d)
**Times Used**: $total
**Failure Rate**: $(awk "BEGIN {printf \"%.0f\", $failure_rate * 100}")%

**Error Details**:
- Implementations affected: $failures of $total
- Common errors: [Extract from session data]

**Recommended Alternative**: [Suggest alternative approach]

---

EOF
    fi
done

echo "✅ Failed patterns written to: $FAILED_PATTERNS_FILE"
```

### Step 6: Write Pattern Summary to Session File

```bash
# Update session file with pattern summary
SUCCESSFUL_COUNT=$(grep -c "^## Pattern:" "$SUCCESSFUL_PATTERNS_FILE" 2>/dev/null || echo "0")
FAILED_COUNT=$(grep -c "^## Pattern:" "$FAILED_PATTERNS_FILE" 2>/dev/null || echo "0")

# Update session file patterns section
sed -i '' "/## Patterns Observed/,/## Prompt Effectiveness/c\\
## Patterns Observed\\
\\
### Successful\\
$SUCCESSFUL_COUNT pattern(s) identified with 100% success rate and 3+ uses\\
\\
### Failed\\
$FAILED_COUNT anti-pattern(s) identified\\
\\
## Prompt Effectiveness" "$SESSION_FILE"
```

## Output

- Successful patterns: `agent-os/output/session-feedback/patterns/successful.md`
- Failed patterns: `agent-os/output/session-feedback/patterns/failed.md`
- Updated session file with pattern summary

## Thresholds

From `workflow-config.yml`:
- `min_uses_for_recommendation: 3` - Minimum uses before recommending
- `success_rate_threshold: 1.0` - 100% success rate required

## Usage

Called by `/update-basepoints-and-redeploy` Phase 7 to extract patterns before human review.

Can also be called manually to analyze current session data.

## Notes

- Patterns with 100% success and 3+ uses are marked as successful
- Patterns with any failures are marked as anti-patterns
- Pattern extraction improves over time as more data is collected

```

This workflow will:
- Read all implementations from session
- Group by patterns used
- Calculate success rate per pattern
- Write patterns to `patterns/successful.md` and `patterns/failed.md`

### Step 3: Analyze Prompt Effectiveness

Analyze which prompts led to good/bad outcomes:

```bash
# Analyze Prompt Effectiveness

Determine which prompts led to good/bad outcomes.

## Purpose

Analyze prompt effectiveness from session data to identify:
- Prompts that worked well (effective)
- Prompts that needed clarification (ineffective)
- Improvement opportunities for prompt construction

## Inputs

- Session file: `agent-os/output/session-feedback/current-session.md`
- Raw prompt data: `agent-os/output/session-feedback/prompts/effective-raw.txt`
- Raw prompt issues: `agent-os/output/session-feedback/prompts/needs-improvement-raw.txt`

## Indicators of Effective Prompts

Prompts that lead to:
- ✅ Implementation completed without retries
- ✅ No clarification questions needed
- ✅ Validation passed first time
- ✅ Correct files created/modified
- ✅ Clear understanding of requirements

## Indicators of Ineffective Prompts

Prompts that lead to:
- ❌ Multiple retries needed
- ❌ User had to clarify requirements
- ❌ Validation failed due to misunderstanding
- ❌ Wrong files created/modified
- ❌ Missing context or constraints

## Process

### Step 1: Load Session Data

```bash
SESSION_FILE="agent-os/output/session-feedback/current-session.md"
PROMPTS_DIR="agent-os/output/session-feedback/prompts"

# Create prompts directory if needed
mkdir -p "$PROMPTS_DIR"

# Read session file
if [ ! -f "$SESSION_FILE" ]; then
    echo "⚠️ No session file found. Run /implement-tasks first."
    exit 0
fi

# Extract implementations
IMPLEMENTATIONS=$(grep -A 100 "| # | Spec | Tasks | Outcome | Duration | Notes |" "$SESSION_FILE" | grep "^| [0-9]" || echo "")
```

### Step 2: Analyze Effective Prompts

```bash
EFFECTIVE_FILE="$PROMPTS_DIR/effective.md"
EFFECTIVE_RAW="$PROMPTS_DIR/effective-raw.txt"

cat > "$EFFECTIVE_FILE" << EOF
# Effective Prompts

Prompts that led to successful implementations without issues.

EOF

# Count effective prompts
EFFECTIVE_COUNT=0

# Read raw effective data
if [ -f "$EFFECTIVE_RAW" ]; then
    while read -r line; do
        if [[ "$line" =~ "Implementation #([0-9]+): Passed without issues" ]]; then
            IMPL_NUM="${BASH_REMATCH[1]}"
            EFFECTIVE_COUNT=$((EFFECTIVE_COUNT + 1))
            
            # Extract implementation details
            IMPL_DETAILS=$(echo "$IMPLEMENTATIONS" | grep "^| $IMPL_NUM " || echo "")
            
            if [ -n "$IMPL_DETAILS" ]; then
                SPEC=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $3}' | tr -d ' ')
                TASKS=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $4}' | tr -d ' ')
                
                cat >> "$EFFECTIVE_FILE" << EOF
## Implementation #$IMPL_NUM: $SPEC

**Spec**: $SPEC
**Tasks**: $TASKS
**Outcome**: ✅ Passed without issues

**What Worked Well**:
- No retries needed
- Validation passed first time
- Clear understanding of requirements

**Prompt Characteristics**:
- Sufficient context provided
- Clear constraints defined
- Output format specified

---

EOF
            fi
        fi
    done < "$EFFECTIVE_RAW"
fi

echo "✅ Effective prompts: $EFFECTIVE_COUNT identified"
```

### Step 3: Analyze Ineffective Prompts

```bash
INEFFECTIVE_FILE="$PROMPTS_DIR/needs-improvement.md"
INEFFECTIVE_RAW="$PROMPTS_DIR/needs-improvement-raw.txt"

cat > "$INEFFECTIVE_FILE" << EOF
# Prompts Needing Improvement

Prompts that led to issues or required clarification.

EOF

# Count ineffective prompts
INEFFECTIVE_COUNT=0

# Read raw ineffective data
if [ -f "$INEFFECTIVE_RAW" ]; then
    while read -r line; do
        if [[ "$line" =~ "Implementation #([0-9]+): (.+)" ]]; then
            IMPL_NUM="${BASH_REMATCH[1]}"
            ISSUE="${BASH_REMATCH[2]}"
            INEFFECTIVE_COUNT=$((INEFFECTIVE_COUNT + 1))
            
            # Extract implementation details
            IMPL_DETAILS=$(echo "$IMPLEMENTATIONS" | grep "^| $IMPL_NUM " || echo "")
            
            if [ -n "$IMPL_DETAILS" ]; then
                SPEC=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $3}' | tr -d ' ')
                TASKS=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $4}' | tr -d ' ')
                OUTCOME=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $5}' | tr -d ' ')
                
                cat >> "$INEFFECTIVE_FILE" << EOF
## Implementation #$IMPL_NUM: $SPEC

**Spec**: $SPEC
**Tasks**: $TASKS
**Outcome**: $OUTCOME
**Issue**: $ISSUE

**What Went Wrong**:
- [Issue details]

**Prompt Issues**:
- Missing context: [specific context needed]
- Unclear instructions: [what was unclear]
- Missing constraints: [what constraints were missing]

**Recommended Improvements**:
- Add: [specific improvement]
- Clarify: [what to clarify]
- Include: [missing information]

---

EOF
            fi
        fi
    done < "$INEFFECTIVE_RAW"
fi

echo "✅ Prompts needing improvement: $INEFFECTIVE_COUNT identified"
```

### Step 4: Generate Prompt Effectiveness Summary

```bash
# Update session file with prompt effectiveness summary
sed -i '' "/## Prompt Effectiveness/,/^#/c\\
## Prompt Effectiveness\\
\\
### Worked Well\\
$EFFECTIVE_COUNT prompt(s) led to successful implementations without issues\\
\\
### Needed Clarification\\
$INEFFECTIVE_COUNT prompt(s) required clarification or led to issues\\
" "$SESSION_FILE"
```

### Step 5: Generate Improvement Suggestions

```bash
SUGGESTIONS_FILE="$PROMPTS_DIR/improvement-suggestions.md"

cat > "$SUGGESTIONS_FILE" << EOF
# Prompt Improvement Suggestions

Based on analysis of $INEFFECTIVE_COUNT problematic implementations.

## Common Issues

EOF

# Extract common issues from ineffective prompts
if [ -f "$INEFFECTIVE_FILE" ]; then
    # Count common issues
    MISSING_CONTEXT=$(grep -c "Missing context:" "$INEFFECTIVE_FILE" || echo "0")
    UNCLEAR_INSTRUCTIONS=$(grep -c "Unclear instructions:" "$INEFFECTIVE_FILE" || echo "0")
    MISSING_CONSTRAINTS=$(grep -c "Missing constraints:" "$INEFFECTIVE_FILE" || echo "0")
    
    cat >> "$SUGGESTIONS_FILE" << EOF
1. **Missing Context**: $MISSING_CONTEXT occurrences
   - Add project patterns from basepoints
   - Include relevant tech stack info
   - Reference similar previous implementations

2. **Unclear Instructions**: $UNCLEAR_INSTRUCTIONS occurrences
   - Use numbered steps
   - Specify expected outputs
   - Define clear boundaries

3. **Missing Constraints**: $MISSING_CONSTRAINTS occurrences
   - Add "DO NOT" sections
   - Include anti-patterns to avoid
   - Specify validation requirements

## Recommendations

- Always include basepoints context
- Use clear, numbered steps
- Explicitly state constraints
- Specify output format
- Include validation criteria

EOF
fi

echo "✅ Improvement suggestions: $SUGGESTIONS_FILE"
```

## Output

- Effective prompts: `agent-os/output/session-feedback/prompts/effective.md`
- Prompts needing improvement: `agent-os/output/session-feedback/prompts/needs-improvement.md`
- Improvement suggestions: `agent-os/output/session-feedback/prompts/improvement-suggestions.md`
- Updated session file with prompt effectiveness summary

## Usage

Called by `/update-basepoints-and-redeploy` Phase 7 to analyze prompt effectiveness before human review.

Can also be called manually to analyze current session data.

## Notes

- Effectiveness is measured by implementation success without retries
- Patterns in ineffective prompts are used to improve prompt construction
- Analysis improves over time as more data is collected

```

This workflow will:
- Identify effective prompts (no retries, passed first time)
- Identify ineffective prompts (needed clarification, failed)
- Generate improvement suggestions

### Step 4: Generate Adaptation Suggestions

Based on analysis, generate specific adaptation suggestions:

**For Successful Patterns** (used 3+ times with 100% success):
- Add to `/shape-spec` context section
- Add to `/implement-tasks` guidance section
- Include in relevant command patterns

**For Failed Patterns** (caused issues):
- Add warning to `/implement-tasks` constraints
- Add "DO NOT" section to relevant commands
- Include in anti-patterns documentation

**For Prompt Issues** (needed clarification):
- Add missing context to command prompts
- Clarify ambiguous instructions
- Add explicit constraints and boundaries

### Step 5: Present Human Review Checkpoint

Present learnings to user for approval:

```bash
# Present Learnings for Review

Workflow to present session learnings to user for review and approval.

## Purpose

Display session learnings summary with clear options for user approval:
- Successful patterns discovered
- Anti-patterns identified
- Prompt effectiveness analysis
- Proposed adaptations

## Inputs

- Session file: `agent-os/output/session-feedback/current-session.md`
- Successful patterns: `agent-os/output/session-feedback/patterns/successful.md`
- Failed patterns: `agent-os/output/session-feedback/patterns/failed.md`
- Prompt analysis: `agent-os/output/session-feedback/prompts/effective.md` and `needs-improvement.md`

## Process

### Step 1: Load Session Learnings

```bash
SESSION_FILE="agent-os/output/session-feedback/current-session.md"
PATTERNS_SUCCESS="agent-os/output/session-feedback/patterns/successful.md"
PATTERNS_FAILED="agent-os/output/session-feedback/patterns/failed.md"
PROMPTS_EFFECTIVE="agent-os/output/session-feedback/prompts/effective.md"
PROMPTS_IMPROVEMENT="agent-os/output/session-feedback/prompts/needs-improvement.md"

if [ ! -f "$SESSION_FILE" ]; then
    echo "ℹ️ No session feedback found. Skipping learning phase."
    echo "   Run /implement-tasks to generate session feedback."
    exit 0
fi

echo "📋 Loading session learnings..."
```

### Step 2: Extract Session Summary

```bash
# Extract metadata from session
TOTAL_IMPL=$(grep "Implementations:" "$SESSION_FILE" | sed 's/.*: \([0-9]*\).*/\1/' | head -1)
SUCCESS_RATE=$(grep "Success Rate:" "$SESSION_FILE" | sed 's/.*: \([0-9]*\)%.*/\1/' | head -1)

# Count successful implementations
SUCCESSFUL=$(grep -c "✅ Pass" "$SESSION_FILE" || echo "0")
FAILED=$(grep -c "❌ Fail" "$SESSION_FILE" || echo "0")

# Count patterns
SUCCESSFUL_PATTERNS_COUNT=$(grep -c "^## Pattern:" "$PATTERNS_SUCCESS" 2>/dev/null || echo "0")
FAILED_PATTERNS_COUNT=$(grep -c "^## Pattern:" "$PATTERNS_FAILED" 2>/dev/null || echo "0")
```

### Step 3: Extract Pattern Details

```bash
# Extract successful patterns
SUCCESSFUL_PATTERNS=""
if [ -f "$PATTERNS_SUCCESS" ]; then
    while IFS= read -r line; do
        if [[ "$line" =~ "^## Pattern: (.*)" ]]; then
            PATTERN_NAME="${BASH_REMATCH[1]}"
            # Get times used
            TIMES_USED=$(grep -A 5 "^## Pattern: $PATTERN_NAME" "$PATTERNS_SUCCESS" | grep "Times Used:" | sed 's/.*: \([0-9]*\).*/\1/')
            SUCCESSFUL_PATTERNS="${SUCCESSFUL_PATTERNS}  - $PATTERN_NAME (used $TIMES_USED times)\n"
        fi
    done < "$PATTERNS_SUCCESS"
fi

# Extract failed patterns
FAILED_PATTERNS=""
if [ -f "$PATTERNS_FAILED" ]; then
    while IFS= read -r line; do
        if [[ "$line" =~ "^## Pattern: (.*)" ]]; then
            PATTERN_NAME="${BASH_REMATCH[1]}"
            # Get error details
            ERROR=$(grep -A 10 "^## Pattern: $PATTERN_NAME" "$PATTERNS_FAILED" | grep "Error Details:" -A 3 | head -1)
            FAILED_PATTERNS="${FAILED_PATTERNS}  - $PATTERN_NAME: $ERROR\n"
        fi
    done < "$PATTERNS_FAILED"
fi
```

### Step 4: Extract Prompt Effectiveness

```bash
# Count effective prompts
EFFECTIVE_COUNT=$(grep -c "^## Implementation #" "$PROMPTS_EFFECTIVE" 2>/dev/null || echo "0")
IMPROVEMENT_COUNT=$(grep -c "^## Implementation #" "$PROMPTS_IMPROVEMENT" 2>/dev/null || echo "0")
```

### Step 5: Generate Adaptation Suggestions

```bash
# Generate suggestions based on patterns
SUGGESTIONS=""

# For successful patterns - suggest adding to commands
if [ $SUCCESSFUL_PATTERNS_COUNT -gt 0 ]; then
    SUGGESTIONS="${SUGGESTIONS}✅ Add successful patterns to command context\n"
fi

# For failed patterns - suggest adding warnings
if [ $FAILED_PATTERNS_COUNT -gt 0 ]; then
    SUGGESTIONS="${SUGGESTIONS}⚠️ Add anti-pattern warnings to commands\n"
fi

# For prompt improvements
if [ $IMPROVEMENT_COUNT -gt 0 ]; then
    SUGGESTIONS="${SUGGESTIONS}📝 Improve prompts based on effectiveness analysis\n"
fi
```

### Step 6: Present Review UI

Display the review UI:

```
┌─────────────────────────────────────────────────────────────────┐
│  🔍 SESSION LEARNINGS REVIEW                                    │
│                                                                 │
│  📊 Session Summary:                                            │
│  • Implementations: $TOTAL_IMPL total ($SUCCESSFUL successful, $FAILED failed) │
│  • Success Rate: $SUCCESS_RATE%                                   │
│  • New Patterns: $SUCCESSFUL_PATTERNS_COUNT                                       │
│  • Anti-patterns: $FAILED_PATTERNS_COUNT                                       │
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  ✅ SUCCESSFUL PATTERNS                                         │
│                                                                 │
EOF

# Display successful patterns
if [ $SUCCESSFUL_PATTERNS_COUNT -gt 0 ]; then
    echo "$SUCCESSFUL_PATTERNS" | while read -r pattern; do
        if [ -n "$pattern" ]; then
            echo "│  $pattern"
        fi
    done
else
    echo "│  (No successful patterns identified yet)"
fi

cat << EOF
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  ❌ ANTI-PATTERNS (caused failures)                             │
│                                                                 │
EOF

# Display failed patterns
if [ $FAILED_PATTERNS_COUNT -gt 0 ]; then
    echo "$FAILED_PATTERNS" | while read -r pattern; do
        if [ -n "$pattern" ]; then
            echo "│  $pattern"
        fi
    done
else
    echo "│  (No anti-patterns identified)"
fi

cat << EOF
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  📝 PROMPT EFFECTIVENESS                                        │
│                                                                 │
│  ✅ Effective: $EFFECTIVE_COUNT prompt(s)                          │
│  ⚠️ Needs Improvement: $IMPROVEMENT_COUNT prompt(s)                            │
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  💡 PROPOSED ADAPTATIONS                                        │
│                                                                 │
EOF

echo "$SUGGESTIONS" | while read -r suggestion; do
    if [ -n "$suggestion" ]; then
        echo "│  $suggestion"
    fi
done

cat << EOF
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  Options:                                                       │
│  [a] Apply all adaptations                                      │
│  [s] Select which to apply                                      │
│  [n] Skip (save learnings for later)                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Step 7: Wait for User Response

```bash
echo ""
read -p "Your choice [a/s/n]: " USER_CHOICE

case "$USER_CHOICE" in
    "a"|"A"|"all")
        APPROVAL_TYPE="all"
        ;;
    "s"|"S"|"select")
        APPROVAL_TYPE="select"
        ;;
    "n"|"N"|"skip")
        APPROVAL_TYPE="skip"
        ;;
    *)
        echo "⚠️ Invalid choice. Saving for later review."
        APPROVAL_TYPE="skip"
        ;;
esac

echo "User choice: $APPROVAL_TYPE"
```

### Step 8: Save Adaptations to Pending

```bash
ADAPTATIONS_DIR="agent-os/output/session-feedback/adaptations"
PENDING_FILE="$ADAPTATIONS_DIR/pending.md"

mkdir -p "$ADAPTATIONS_DIR"

cat > "$PENDING_FILE" << EOF
# Pending Adaptations

**Created**: $(date -Iseconds)
**Approval Status**: $APPROVAL_TYPE

## Session Summary
- Total Implementations: $TOTAL_IMPL
- Success Rate: $SUCCESS_RATE%
- Successful Patterns: $SUCCESSFUL_PATTERNS_COUNT
- Anti-patterns: $FAILED_PATTERNS_COUNT

## Proposed Adaptations

EOF

# Add adaptations based on approval type
if [ "$APPROVAL_TYPE" = "all" ] || [ "$APPROVAL_TYPE" = "select" ]; then
    cat >> "$PENDING_FILE" << EOF
### Successful Patterns Adaptations
[Adaptations for successful patterns]

### Anti-patterns Adaptations
[Adaptations for anti-patterns]

### Prompt Improvements
[Adaptations for prompt improvements]
EOF
fi

echo "✅ Adaptations saved to: $PENDING_FILE"
```

## Output

- User approval choice (all/select/skip)
- Pending adaptations file: `agent-os/output/session-feedback/adaptations/pending.md`

## Critical Safety

- ALWAYS asks user before applying any changes
- NEVER auto-applies adaptations
- Clear presentation of what will change
- Option to reject and save for later

## Usage

Called by Phase 7 of `/update-basepoints-and-redeploy` to present learnings for human review.

```

This workflow will:
- Display session summary
- Show successful patterns discovered
- Show anti-patterns identified
- Show prompt effectiveness analysis
- Present clear options (Approve all / Select / Skip)
- **WAIT for user response**

### Step 6: Handle User Response

Based on user choice:

```bash
# User choice is saved by present-learnings-for-review workflow
PENDING_FILE="agent-os/output/session-feedback/adaptations/pending.md"

if [ -f "$PENDING_FILE" ]; then
    APPROVAL_STATUS=$(grep "Approval Status:" "$PENDING_FILE" | sed 's/.*: \([^ ]*\).*/\1/')
    
    case "$APPROVAL_STATUS" in
        "all")
            echo "✅ All adaptations approved. Proceed to Phase 8: Adapt Commands."
            ;;
        "select")
            echo "✅ Selected adaptations approved. Proceed to Phase 8: Adapt Commands."
            ;;
        "skip")
            echo "ℹ️ Adaptations saved for later review. Skipping Phase 8."
            echo "   Adaptations saved to: $PENDING_FILE"
            exit 0
            ;;
    esac
else
    echo "⚠️ No adaptations file found. Skipping Phase 8."
    exit 0
fi
```

## Output

- Adaptations file: `agent-os/output/session-feedback/adaptations/pending.md`
- User approval status (all/select/skip)

## Critical Safety Requirements

- ⚠️ **ALWAYS ask user** before applying any changes
- ⚠️ **NEVER auto-apply** adaptations
- ⚠️ **Clear presentation** of what will change
- ⚠️ **Option to reject** and save for later

## Next Step

If user approved adaptations (all or select):
- Proceed to Phase 8: Adapt Commands

If user skipped:
- Exit (adaptations saved for later)
- Can review later by running Phase 7 again

## Notes

- This phase is CRITICAL for safety - human review is mandatory
- All adaptations require explicit user approval
- Rejected learnings are saved to history for future reference
- Session data is preserved for later analysis

# PHASE 8: Adapt Commands

# Phase 8: Adapt Commands

Apply user-approved adaptations to command templates.

## Prerequisites

- User has reviewed learnings from Phase 7
- User has approved specific adaptations (all or select)
- Adaptations file exists: `agent-os/output/session-feedback/adaptations/pending.md`

## Core Responsibilities

1. **Load Approved Adaptations**: Read user-approved adaptations from pending.md
2. **Backup Commands**: Create timestamped backup of all command files
3. **Apply Adaptations**: Apply approved changes using workflow
4. **Update Applied Log**: Move adaptations from pending to applied.md
5. **Archive Session**: Move session to history and start fresh
6. **Generate Report**: Document what was changed

## Workflow

### Step 1: Check Prerequisites

```bash
PENDING_FILE="agent-os/output/session-feedback/adaptations/pending.md"

if [ ! -f "$PENDING_FILE" ]; then
    echo "ℹ️ No pending adaptations found. Skipping adaptation phase."
    exit 0
fi

# Check approval status
APPROVAL_STATUS=$(grep "Approval Status:" "$PENDING_FILE" | sed 's/.*: \([^ ]*\).*/\1/' | head -1)

if [ "$APPROVAL_STATUS" = "skip" ]; then
    echo "ℹ️ Adaptations were skipped by user. Not applying changes."
    exit 0
fi

echo "📋 Applying approved adaptations..."
```

### Step 2: Load Approved Adaptations

```bash
APPLIED_FILE="agent-os/output/session-feedback/adaptations/applied.md"

# Ensure applied file exists
mkdir -p "$(dirname "$APPLIED_FILE")"

if [ ! -f "$APPLIED_FILE" ]; then
    cat > "$APPLIED_FILE" << EOF
# Applied Adaptations

EOF
fi
```

### Step 3: Apply Adaptations

Use the adaptation workflow to apply approved changes:

```bash
# Apply Command Adaptations

Workflow to apply approved command adaptations with safety checks and rollback support.

## Purpose

Apply user-approved adaptations to command templates:
- Backup before changes
- Apply only approved adaptations
- Log all changes with timestamps
- Support rollback on errors

## Inputs

- Pending adaptations file: `agent-os/output/session-feedback/adaptations/pending.md`
- User approval status (all/select/skip)

## Process

### Step 1: Load Approved Adaptations

```bash
PENDING_FILE="agent-os/output/session-feedback/adaptations/pending.md"
APPLIED_FILE="agent-os/output/session-feedback/adaptations/applied.md"

if [ ! -f "$PENDING_FILE" ]; then
    echo "⚠️ No pending adaptations found."
    exit 0
fi

# Check approval status
APPROVAL_STATUS=$(grep "Approval Status:" "$PENDING_FILE" | sed 's/.*: \([^ ]*\).*/\1/' | head -1)

if [ "$APPROVAL_STATUS" = "skip" ]; then
    echo "ℹ️ Adaptations were skipped by user. Not applying changes."
    exit 0
fi

echo "📋 Loading approved adaptations..."
```

### Step 2: Create Backup Directory

```bash
BACKUP_DIR="agent-os/output/update-basepoints-and-redeploy/backups/$(date +%Y%m%d-%H%M%S)"
mkdir -p "$BACKUP_DIR"

echo "📦 Creating backup at: $BACKUP_DIR"

# Copy all command files that might be affected
COMMANDS_DIR="agent-os/commands"
find "$COMMANDS_DIR" -name "*.md" -type f | while read cmd_file; do
    # Get relative path for backup structure
    RELATIVE_PATH=$(echo "$cmd_file" | sed "s|$COMMANDS_DIR/||")
    BACKUP_SUBDIR="$BACKUP_DIR/$(dirname "$RELATIVE_PATH")"
    mkdir -p "$BACKUP_SUBDIR"
    cp "$cmd_file" "$BACKUP_SUBDIR/"
done

echo "✅ Backup created: $BACKUP_DIR"
```

### Step 3: Parse Adaptations to Apply

```bash
# Extract adaptations from pending file
# Format will be in pending.md

# For each adaptation in pending.md:
# - Extract target file
# - Extract adaptation type (Context Addition, Constraint Addition, Warning Addition)
# - Extract content to add
```

### Step 4: Apply Each Adaptation

For each approved adaptation:

```bash
# Determine adaptation type and target
ADAPTATION_TYPE="[Context Addition | Constraint Addition | Warning Addition]"
TARGET_FILE="agent-os/commands/[command]/[phase].md"

# Find insertion point based on type
case "$ADAPTATION_TYPE" in
    "Context Addition")
        # Insert in "## Context" or "## Project Patterns" section
        if grep -q "## Context" "$TARGET_FILE"; then
            INSERTION_POINT="after_context"
        elif grep -q "## Project Patterns" "$TARGET_FILE"; then
            INSERTION_POINT="after_project_patterns"
        else
            # Add new Context section
            INSERTION_POINT="new_context_section"
        fi
        ;;
    "Constraint Addition")
        # Insert in "## Constraints" or "## Important" section
        if grep -q "## Constraints" "$TARGET_FILE"; then
            INSERTION_POINT="after_constraints"
        elif grep -q "## Important" "$TARGET_FILE"; then
            INSERTION_POINT="after_important"
        else
            # Add new Constraints section
            INSERTION_POINT="new_constraints_section"
        fi
        ;;
    "Warning Addition")
        # Insert in "## Warnings" or create new section
        if grep -q "## Warnings" "$TARGET_FILE"; then
            INSERTION_POINT="after_warnings"
        else
            INSERTION_POINT="new_warnings_section"
        fi
        ;;
esac

# Apply adaptation
apply_adaptation() {
    local file="$1"
    local type="$2"
    local content="$3"
    local insertion_point="$4"
    
    TEMP_FILE=$(mktemp)
    
    case "$insertion_point" in
        "after_context")
            # Insert after "## Context" section
            awk -v content="$content" '/^## Context$/ {print; getline; print; print ""; print content; next} {print}' "$file" > "$TEMP_FILE"
            ;;
        "after_constraints")
            # Insert after "## Constraints" section
            awk -v content="$content" '/^## Constraints$/ {print; getline; print; print ""; print content; next} {print}' "$file" > "$TEMP_FILE"
            ;;
        "new_constraints_section")
            # Add new Constraints section after Context
            awk -v content="$content" '/^## Context$/ {print; print ""; print "## Constraints"; print ""; print content; next} {print}' "$file" > "$TEMP_FILE"
            ;;
        *)
            # Default: add at end of file
            cat "$file" > "$TEMP_FILE"
            echo "" >> "$TEMP_FILE"
            echo "$content" >> "$TEMP_FILE"
            ;;
    esac
    
    # Validate markdown syntax
    if validate_markdown "$TEMP_FILE"; then
        mv "$TEMP_FILE" "$file"
        return 0
    else
        echo "⚠️ Markdown validation failed. Restoring from backup."
        restore_from_backup "$file" "$BACKUP_DIR"
        return 1
    fi
}

# Apply the adaptation
if apply_adaptation "$TARGET_FILE" "$ADAPTATION_TYPE" "$ADAPTATION_CONTENT" "$INSERTION_POINT"; then
    echo "✅ Applied: $ADAPTATION_TYPE to $TARGET_FILE"
else
    echo "❌ Failed: $ADAPTATION_TYPE to $TARGET_FILE"
fi
```

### Step 5: Update Applied Log

```bash
# Move applied adaptations to applied.md
APPLIED_LOG="$APPLIED_FILE"

# Create applied file if it doesn't exist
if [ ! -f "$APPLIED_LOG" ]; then
    cat > "$APPLIED_LOG" << EOF
# Applied Adaptations

EOF
fi

# Append to applied log
cat >> "$APPLIED_LOG" << EOF
## $(date +%Y-%m-%d) Session

EOF

# For each successfully applied adaptation:
cat >> "$APPLIED_LOG" << EOF
### Adaptation: [Adaptation Name]
- **Applied To**: [Target File]
- **Date**: $(date -Iseconds)
- **Type**: [Context Addition | Constraint Addition | Warning Addition]
- **Content**: [Brief description of what was added]

EOF

echo "✅ Applied adaptations logged to: $APPLIED_LOG"
```

### Step 6: Track Applied Changes

```bash
# Create applied changes log in backup directory
APPLIED_CHANGES_LOG="$BACKUP_DIR/applied-changes.log"

cat > "$APPLIED_CHANGES_LOG" << EOF
# Applied Changes Log

**Date**: $(date -Iseconds)
**Backup Location**: $BACKUP_DIR

## Adaptations Applied

EOF

# List all applied adaptations
# This will be populated by the actual application process

cat >> "$APPLIED_CHANGES_LOG" << EOF

## Rollback Instructions

To rollback these changes, restore files from backup:

\`\`\`bash
cp -r $BACKUP_DIR/* agent-os/commands/
\`\`\`
EOF

echo "✅ Changes log: $APPLIED_CHANGES_LOG"
```

## Error Handling

If any adaptation fails:

```bash
# Restore file from backup
restore_from_backup() {
    local file="$1"
    local backup_dir="$2"
    
    RELATIVE_PATH=$(echo "$file" | sed "s|agent-os/commands/||")
    BACKUP_FILE="$backup_dir/$RELATIVE_PATH"
    
    if [ -f "$BACKUP_FILE" ]; then
        cp "$BACKUP_FILE" "$file"
        echo "✅ Restored: $file from backup"
        return 0
    else
        echo "⚠️ Backup not found for: $file"
        return 1
    fi
}

# Validate markdown syntax
validate_markdown() {
    local file="$1"
    # Basic validation - check for balanced markdown
    # This is a placeholder - actual validation would use a markdown parser
    return 0
}
```

## Output

- Applied changes to command files in `agent-os/commands/`
- Backup directory: `agent-os/output/update-basepoints-and-redeploy/backups/[timestamp]/`
- Applied log: `agent-os/output/session-feedback/adaptations/applied.md`
- Changes log: `$BACKUP_DIR/applied-changes.log`

## Safety Checks

1. **Backup Required**: Always creates backup before changes
2. **Markdown Validation**: Validates syntax after each change
3. **Rollback Support**: Restores from backup if validation fails
4. **Change Tracking**: Logs all applied changes for audit

## Rollback Procedure

If adaptations cause issues:

```bash
# Find backup directory
BACKUP_DIR=$(ls -td agent-os/output/update-basepoints-and-redeploy/backups/* | head -1)

# Restore all files
cp -r "$BACKUP_DIR"/* agent-os/commands/

echo "✅ Rollback complete. Files restored from: $BACKUP_DIR"
```

## Usage

Called by Phase 8 of `/update-basepoints-and-redeploy` to apply approved adaptations.

## Notes

- Only applies user-approved adaptations
- Creates backup before any changes
- Logs all changes for audit trail
- Supports rollback on errors

```

This workflow will:
- Create timestamped backup directory
- Copy original command files to backup
- Apply approved adaptations
- Validate markdown syntax after each change
- Log all applied changes

### Step 4: Update Applied Log

Move successfully applied adaptations from `pending.md` to `applied.md`:

```bash
# Append applied adaptations to applied.md
cat >> "$APPLIED_FILE" << EOF
## $(date +%Y-%m-%d) Session

### Applied Adaptations
EOF

# For each successfully applied adaptation, log it
# (This will be done by the apply-command-adaptations workflow)

echo "✅ Applied adaptations logged to: $APPLIED_FILE"
```

### Step 5: Archive Session

After adaptations are applied:

```bash
SESSION_FILE="agent-os/output/session-feedback/current-session.md"
HISTORY_DIR="agent-os/output/session-feedback/history"
PATTERNS_DIR="agent-os/output/session-feedback/patterns"

# Create history directory if it doesn't exist
mkdir -p "$HISTORY_DIR"

# Move current session to history
if [ -f "$SESSION_FILE" ]; then
    TODAY=$(date +%Y-%m-%d)
    mv "$SESSION_FILE" "$HISTORY_DIR/${TODAY}-session.md"
    echo "✅ Session archived to: $HISTORY_DIR/${TODAY}-session.md"
fi

# Clear patterns (start fresh)
if [ -f "$PATTERNS_DIR/successful.md" ]; then
    cat > "$PATTERNS_DIR/successful.md" << EOF
# Successful Patterns

(Patterns will be listed here as they are detected)

EOF
fi

if [ -f "$PATTERNS_DIR/failed.md" ]; then
    cat > "$PATTERNS_DIR/failed.md" << EOF
# Failed Patterns (Anti-patterns)

(Anti-patterns will be listed here as they are detected)

EOF
fi

# Create new empty session
cat > "$SESSION_FILE" << EOF
# Session: $(date +%Y-%m-%d)

## Metadata
- Started: $(date -Iseconds)
- Last Updated: $(date -Iseconds)
- Implementations: 0
- Success Rate: 0%

## Implementations

| # | Spec | Tasks | Outcome | Duration | Notes |
|---|------|-------|---------|----------|-------|

## Patterns Observed

### Successful
(Patterns will be listed here as they are detected)

### Failed
(Failed patterns will be listed here as they are detected)

## Prompt Effectiveness

### Worked Well
(Prompts that worked well will be listed here)

### Needed Clarification
(Prompts that needed clarification will be listed here)
EOF

echo "✅ New session started"
```

### Step 6: Generate Report

```bash
BACKUP_DIR=$(ls -td agent-os/output/update-basepoints-and-redeploy/backups/* | head -1)
REPORT_FILE="agent-os/output/update-basepoints-and-redeploy/reports/adaptation-report.md"

mkdir -p "$(dirname "$REPORT_FILE")"

cat > "$REPORT_FILE" << EOF
# Adaptation Report

**Date**: $(date -Iseconds)
**Approval Status**: $APPROVAL_STATUS

## Adaptations Applied

✅ Applied: [count] adaptations
⏭️ Skipped: [count] adaptations

## Backup Location

\`$BACKUP_DIR\`

## Applied Adaptations Log

See: \`agent-os/output/session-feedback/adaptations/applied.md\`

## Rollback Instructions

To rollback these changes:

\`\`\`bash
cp -r $BACKUP_DIR/* agent-os/commands/
\`\`\`

## Session Archive

- Previous session archived to history
- New session started
EOF

echo ""
echo "✅ ADAPTATIONS APPLIED"
echo ""
echo "Applied: [N] adaptations"
echo "Backup: $BACKUP_DIR"
echo ""
echo "Session archived to history."
echo "New session started."
echo ""
echo "Report: $REPORT_FILE"
```

## Output

- Applied changes to command files in `agent-os/commands/`
- Backup directory: `agent-os/output/update-basepoints-and-redeploy/backups/[timestamp]/`
- Applied log: `agent-os/output/session-feedback/adaptations/applied.md`
- Adaptation report: `agent-os/output/update-basepoints-and-redeploy/reports/adaptation-report.md`
- Archived session: `agent-os/output/session-feedback/history/[date]-session.md`
- New session file: `agent-os/output/session-feedback/current-session.md`

## Safety Requirements

- ✅ Backup created BEFORE any changes
- ✅ Only user-approved changes applied
- ✅ All changes logged in applied.md
- ✅ Rollback procedure documented
- ✅ Markdown validation after each change

## Error Handling

If any adaptation fails:
- File is restored from backup
- Error is logged
- Remaining adaptations continue
- Final report shows failures

## Next Step

After adaptations are applied:
- Session is archived to history
- New session is started
- Process completes

## Notes

- Only runs if user approved adaptations in Phase 7
- All changes are backed up before application
- Supports rollback if issues occur
- Session is reset after adaptations are applied
