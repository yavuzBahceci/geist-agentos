[38;2;255;185;0m⚠️  Unknown conditional flag: flag[0m
[38;2;255;185;0m⚠️  Unknown conditional flag: flag[0m
[38;2;255;185;0m⚠️  Unknown conditional flag: flag[0m
[38;2;255;185;0m⚠️  Unknown conditional flag: flag[0m
[38;2;255;185;0m⚠️  Unclosed conditional block detected (nesting level: 5)[0m
Now that placeholders are cleaned, proceed with removing unnecessary logic by following these instructions:

## Core Responsibilities

1. **Load Unnecessary Logic Detection Results**: Load unnecessary logic validation results
2. **Remove Project-Agnostic Conditionals**: Remove conditional logic that checks for project-agnostic scenarios ({{IF}}/{{UNLESS}} blocks that are no longer needed)
3. **Convert Generic Patterns to Project-Specific**: Replace generic examples with project-specific ones from basepoints where possible
4. **Abstract Redundant Patterns**: Simplify patterns that can be abstracted without losing functionality
5. **Remove Unnecessary Complexity**: Remove abstraction layers that are no longer needed after specialization
6. **Remove profiles/default References**: Remove all references to "profiles/default" from specialized commands
7. **Support Dry-Run Mode**: Preview changes before applying
8. **Track Changes**: Track what was converted, abstracted, or removed for cleanup report

## Workflow

### Step 1: Load Unnecessary Logic Detection Results

Load unnecessary logic validation results:

```bash
# Load validation cache
VALIDATION_CACHE="agent-os/.cleanup-cache/validation"
CLEANUP_CACHE="agent-os/.cleanup-cache"
DRY_RUN="${DRY_RUN:-false}"

# Load unnecessary logic detection results
if [ -f "$VALIDATION_CACHE/unnecessary-logic-removal-validation.json" ]; then
    UNNECESSARY_LOGIC_DATA=$(cat "$VALIDATION_CACHE/unnecessary-logic-removal-validation.json")
    UNNECESSARY_LOGIC_TOTAL=$(echo "$UNNECESSARY_LOGIC_DATA" | grep -o '"total_issues_found":[0-9]*' | cut -d: -f2)
    
    if [ "$UNNECESSARY_LOGIC_TOTAL" -eq 0 ]; then
        echo "✅ No unnecessary logic to remove"
        echo "0" > "$CLEANUP_CACHE/unnecessary-logic-removed.txt"
        exit 0
    fi
    
    echo "🔧 Found $UNNECESSARY_LOGIC_TOTAL unnecessary logic issue(s) to remove"
else
    echo "❌ Unnecessary logic validation results not found"
    exit 1
fi
```

### Step 2: Remove Project-Agnostic Conditionals

Remove conditional logic that checks for project-agnostic scenarios:

```bash
# Initialize cleanup tracking
UNNECESSARY_LOGIC_REMOVED=0
FILES_MODIFIED=0

# Extract files with unnecessary logic
echo "$UNNECESSARY_LOGIC_DATA" | grep -oE '"file": "[^"]+"' | cut -d'"' -f4 | sort -u > "$CLEANUP_CACHE/files-with-unnecessary-logic.txt"

# Process each file
while read file_path; do
    if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file_path")
    ORIGINAL_CONTENT="$FILE_CONTENT"
    MODIFIED=false
    
    # Remove technology-agnostic fallback logic
    if echo "$FILE_CONTENT" | grep -qE "if.*technology.*agnostic|if.*project.*agnostic|fallback.*generic|default.*generic"; then
        # Remove lines with technology-agnostic checks (simplified - in practice would need more sophisticated parsing)
        FILE_CONTENT=$(echo "$FILE_CONTENT" | grep -vE "if.*technology.*agnostic|if.*project.*agnostic|fallback.*generic|default.*generic")
        MODIFIED=true
        UNNECESSARY_LOGIC_REMOVED=$((UNNECESSARY_LOGIC_REMOVED + 1))
    fi
    
    # Remove conditional logic checking for template state
    if echo "$FILE_CONTENT" | grep -qE "if.*template|if.*abstract|if.*generic.*pattern"; then
        FILE_CONTENT=$(echo "$FILE_CONTENT" | grep -vE "if.*template|if.*abstract|if.*generic.*pattern")
        MODIFIED=true
        UNNECESSARY_LOGIC_REMOVED=$((UNNECESSARY_LOGIC_REMOVED + 1))
    fi
    
    # Apply changes if modified
    if [ "$MODIFIED" = "true" ]; then
        if [ "$DRY_RUN" = "true" ]; then
            echo "🔍 [DRY-RUN] Would modify: $file_path"
            echo "   Changes preview available in: $CLEANUP_CACHE/dry-run-preview/$file_path"
            mkdir -p "$(dirname "$CLEANUP_CACHE/dry-run-preview/$file_path")"
            echo "$FILE_CONTENT" > "$CLEANUP_CACHE/dry-run-preview/$file_path"
        else
            echo "$FILE_CONTENT" > "$file_path"
            echo "✅ Modified: $file_path"
            FILES_MODIFIED=$((FILES_MODIFIED + 1))
        fi
    fi
done < "$CLEANUP_CACHE/files-with-unnecessary-logic.txt"
```

### Step 3: Remove Unnecessary {{IF}}/{{UNLESS}} Conditional Blocks

After specialization, many conditional blocks are no longer needed. Remove resolved conditionals:

```bash
# Initialize tracking
CONDITIONALS_REMOVED=0

# Scan all agent-os command files for {{IF}}/{{UNLESS}} blocks
FILES_TO_SCAN=$(find agent-os/commands agent-os/workflows -name "*.md" -type f 2>/dev/null)

echo "$FILES_TO_SCAN" | while read file_path; do
    if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file_path")
    ORIGINAL_CONTENT="$FILE_CONTENT"
    MODIFIED=false
    
    # Detect {{IF}}/{{UNLESS}} blocks that are always true/false after specialization
