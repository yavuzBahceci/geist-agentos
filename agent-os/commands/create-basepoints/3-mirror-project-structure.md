Now that abstraction layers are detected, proceed with mirroring the project structure in the basepoints folder.

# Project Structure Mirroring

## Core Responsibilities

1. **Create Basepoints Folder**: Ensure `agent-os/basepoints/` folder exists
2. **Mirror Directory Structure**: Replicate project directory structure within basepoints folder
3. **Exclude Irrelevant Folders**: Skip generated and irrelevant folders during mirroring
4. **Identify Module Folders**: Determine which folders contain actual modules (not config/build)
5. **Include Project Root**: Include the software project's current path layer in the structure

## Workflow

### Step 1: Create Basepoints Folder

Ensure the basepoints folder exists:

```bash
mkdir -p agent-os/basepoints
```

### Step 2: Define Exclusion Patterns

Set up exclusion patterns for folders that should not be mirrored:

```bash
EXCLUDED_DIRS=(
    "node_modules" ".git" "build" "dist" ".next" "vendor"
    "agent-os" "basepoints" ".cache" "coverage" ".vscode" ".idea"
)
```

### Step 3: Cleanup Existing Duplicates

Before creating new structure, clean up any existing duplicate folders:

```bash
# Remove duplicate basepoints. folder if it exists
if [ -d "agent-os/basepoints." ]; then
    echo "⚠️  Removing duplicate basepoints. folder..."
    rm -rf "agent-os/basepoints."
fi

# Remove any incorrect workflows/basepoints/ folders
if [ -d "agent-os/workflows/basepoints" ]; then
    echo "⚠️  Removing incorrect workflows/basepoints/ folder..."
    rm -rf "agent-os/workflows/basepoints"
fi
```

### Step 4: Mirror Project Structure

Create mirrored structure in basepoints folder with proper path normalization:

```bash
find . -type d ! -path "*/\.*" | while read dir; do
    # Normalize directory path: remove leading ./ and handle root (.)
    NORMALIZED_DIR=$(echo "$dir" | sed 's|^\./||' | sed 's|^\.$||')
    
    # Check if should be excluded
    SHOULD_EXCLUDE=false
    for excluded in "${EXCLUDED_DIRS[@]}"; do
        if [[ "$dir" == *"$excluded"* ]] || [[ "$(basename "$dir")" == "$excluded" ]]; then
            SHOULD_EXCLUDE=true
            break
        fi
    done
    
    if [ "$SHOULD_EXCLUDE" = false ]; then
        # Handle root-level path correctly
        if [ -z "$NORMALIZED_DIR" ] || [ "$NORMALIZED_DIR" = "." ]; then
            # Root level - basepoints folder itself (already created in Step 1)
            # Don't create a subdirectory for root
            continue
        else
            # Normal path - create mirrored structure
            basepoints_dir="agent-os/basepoints/$NORMALIZED_DIR"
            
            # Validate no duplicate (shouldn't happen with normalized paths, but check anyway)
            if [ -d "$basepoints_dir" ] && [ "$basepoints_dir" != "agent-os/basepoints" ]; then
                echo "⚠️  Warning: Directory already exists: $basepoints_dir"
            fi
            
            mkdir -p "$basepoints_dir"
        fi
    fi
done
```

### Step 5: Identify Module Folders (Deterministic Traversal)

Identify ALL folders that contain actual content files using a deterministic traversal.

**IMPORTANT**: This traversal MUST be comprehensive and include ALL content-containing folders. Do not miss any modules.

```bash
mkdir -p agent-os/output/create-basepoints/cache

# Clear previous module folders list
> agent-os/output/create-basepoints/cache/module-folders.txt

# Define content file patterns (comprehensive list)
CONTENT_PATTERNS=(
    "*.ts" "*.tsx" "*.js" "*.jsx"      # JavaScript/TypeScript
    "*.py" "*.pyx"                      # Python
    "*.java" "*.kt" "*.scala"           # JVM languages
    "*.go"                              # Go
    "*.rs"                              # Rust
    "*.rb"                              # Ruby
    "*.php"                             # PHP
    "*.cs" "*.fs"                       # .NET
    "*.swift" "*.m"                     # Apple
    "*.c" "*.cpp" "*.h" "*.hpp"         # C/C++
    "*.sh" "*.bash"                     # Shell scripts
    "*.md"                              # Markdown (documentation/commands)
    "*.yml" "*.yaml"                    # YAML configs
    "*.json"                            # JSON configs
    "*.sql"                             # SQL
    "*.html" "*.css" "*.scss"           # Web files
)

# Build find pattern for content files
FIND_PATTERN=""
for pattern in "${CONTENT_PATTERNS[@]}"; do
    if [ -z "$FIND_PATTERN" ]; then
        FIND_PATTERN="-name \"$pattern\""
    else
        FIND_PATTERN="$FIND_PATTERN -o -name \"$pattern\""
    fi
done

# Deterministic traversal: find ALL directories, sorted for consistency
find . -type d ! -path "*/\.*" 2>/dev/null | sort | while read dir; do
    # Normalize directory path
    NORMALIZED_DIR=$(echo "$dir" | sed 's|^\./||' | sed 's|^\.$||')
    
    # Skip excluded directories
    SHOULD_EXCLUDE=false
    for excluded in "${EXCLUDED_DIRS[@]}"; do
        if [[ "$dir" == *"$excluded"* ]] || [[ "$(basename "$dir")" == "$excluded" ]]; then
            SHOULD_EXCLUDE=true
            break
        fi
    done
    
    if [ "$SHOULD_EXCLUDE" = false ]; then
        # Check if directory contains ANY content files (using expanded patterns)
        HAS_CONTENT=false
        for pattern in "${CONTENT_PATTERNS[@]}"; do
            if find "$dir" -maxdepth 1 -type f -name "$pattern" 2>/dev/null | grep -q .; then
                HAS_CONTENT=true
                break
            fi
        done
        
        if [ "$HAS_CONTENT" = true ]; then
            if [ -z "$NORMALIZED_DIR" ] || [ "$NORMALIZED_DIR" = "." ]; then
                echo "." >> agent-os/output/create-basepoints/cache/module-folders.txt
            else
                echo "$NORMALIZED_DIR" >> agent-os/output/create-basepoints/cache/module-folders.txt
            fi
        fi
    fi
done

# Sort and deduplicate for determinism
sort -u agent-os/output/create-basepoints/cache/module-folders.txt -o agent-os/output/create-basepoints/cache/module-folders.txt
```

### Step 6: Create Basepoints Task List

Create a comprehensive task list of all basepoints that will be generated:

```bash
# Create task list from module folders
echo "# Basepoints Generation Task List" > agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "Generated: $(date)" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "## Modules to Process" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md

TASK_COUNT=0
while read module_dir; do
    if [ -n "$module_dir" ]; then
        TASK_COUNT=$((TASK_COUNT + 1))
        
        # Determine basepoint path
        NORMALIZED_DIR=$(echo "$module_dir" | sed 's|^\./||' | sed 's|^\.$||')
        if [ -z "$NORMALIZED_DIR" ] || [ "$NORMALIZED_DIR" = "." ]; then
            BASEPOINT_PATH="agent-os/basepoints/agent-base-[project-root].md"
            MODULE_NAME="[project-root]"
        else
            MODULE_NAME=$(basename "$NORMALIZED_DIR")
            BASEPOINT_PATH="agent-os/basepoints/$NORMALIZED_DIR/agent-base-$MODULE_NAME.md"
        fi
        
        echo "- [ ] **Task $TASK_COUNT**: \`$module_dir\` → \`$BASEPOINT_PATH\`" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
    fi
done < agent-os/output/create-basepoints/cache/module-folders.txt

echo "" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "## Summary" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "**Total modules to process**: $TASK_COUNT" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "## Parent Basepoints (will be generated after modules)" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "" >> agent-os/output/create-basepoints/cache/basepoints-task-list.md
echo "Parent basepoints will be auto-generated for all intermediate directories." >> agent-os/output/create-basepoints/cache/basepoints-task-list.md

echo "✅ Task list created: agent-os/output/create-basepoints/cache/basepoints-task-list.md"
echo "📋 Total modules to process: $TASK_COUNT"
```

### Step 7: Present Task List for Approval

Present the task list to the user for review before proceeding:

```
📋 BASEPOINTS TASK LIST

The following modules will have basepoints generated:

[Show content of basepoints-task-list.md]

Total: [N] module basepoints + parent basepoints

Do you want to proceed with generating these basepoints? (y/n)
```

**IMPORTANT**: Wait for user approval before proceeding to basepoint generation.

## Important Constraints

- Must use deterministic traversal (sorted paths)
- Must include ALL content file types (not just code files)
- Must exclude standard generated/irrelevant folders
- Must create comprehensive task list before generation
- Must present task list for user approval
- Must maintain relative path structure in basepoints folder
- Must identify which folders are module folders for later analysis
- **MUST NOT miss any modules - complete coverage is required**


## Display Task List for Approval

After structure is mirrored and task list is created, **PRESENT THE TASK LIST TO THE USER FOR APPROVAL**:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 BASEPOINTS TASK LIST - REVIEW REQUIRED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

The following modules were detected and will have basepoints generated:

[Display full content of agent-os/output/create-basepoints/cache/basepoints-task-list.md]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️  VERIFICATION CHECKPOINT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Please review the task list above and verify:
1. All important modules are included
2. No modules are missing
3. The paths look correct

**If any modules are MISSING**, please identify them now.

Proceed with basepoint generation? (y/n)
```

**IMPORTANT**: 
- **WAIT for user approval** before proceeding
- If user identifies missing modules, add them to the task list
- Only proceed to next step after user confirms the list is complete

## Display confirmation and next step

Once structure is mirrored AND user approves the task list, output:

```
✅ Project structure mirrored to basepoints folder!
✅ Task list approved by user!

**Created structure**: agent-os/basepoints/
**Module folders identified**: [number] folders
**Excluded folders**: [list of excluded patterns]
**Task list**: agent-os/output/create-basepoints/cache/basepoints-task-list.md

Structure is ready for codebase analysis.

NEXT STEP 👉 Run the command, `4-analyze-codebase.md`
```

## User Standards & Preferences Compliance

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your structure mirroring aligns with the user's preferences and standards as detailed in the following files:

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
