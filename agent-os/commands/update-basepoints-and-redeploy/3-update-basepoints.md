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
