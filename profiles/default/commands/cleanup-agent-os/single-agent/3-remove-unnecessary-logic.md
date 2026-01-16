Now that placeholders are cleaned, proceed with removing unnecessary logic by following these instructions:

## Core Responsibilities

1. **Load Unnecessary Logic Detection Results**: Load unnecessary logic validation results
2. **Remove Project-Agnostic Conditionals**: Remove conditional logic that checks for project-agnostic scenarios
3. **Replace Generic Examples**: Replace generic examples with project-specific ones
4. **Remove profiles/default References**: Remove all references to "profiles/default" from specialized commands
5. **Support Dry-Run Mode**: Preview changes before applying
6. **Track Changes**: Track what was removed for cleanup report

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

### Step 3: Remove profiles/default References

Remove all references to "profiles/default" from specialized commands:

```bash
# Find all files in agent-os/commands that reference profiles/default
FILES_WITH_PROFILES_DEFAULT=$(find agent-os/commands -name "*.md" -type f -exec grep -l "profiles/default\|profiles\.default\|@profiles/default" {} \; 2>/dev/null)

if [ -n "$FILES_WITH_PROFILES_DEFAULT" ]; then
    echo "$FILES_WITH_PROFILES_DEFAULT" | while read file_path; do
        if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
            continue
        fi
        
        FILE_CONTENT=$(cat "$file_path")
        ORIGINAL_CONTENT="$FILE_CONTENT"
        
        # Remove profiles/default references
        FILE_CONTENT=$(echo "$FILE_CONTENT" | sed "s|profiles/default|agent-os|g")
        FILE_CONTENT=$(echo "$FILE_CONTENT" | sed "s|profiles\.self|agent-os|g")
        FILE_CONTENT=$(echo "$FILE_CONTENT" | sed "s|@profiles/default|@agent-os|g")
        
        if [ "$FILE_CONTENT" != "$ORIGINAL_CONTENT" ]; then
            if [ "$DRY_RUN" = "true" ]; then
                echo "🔍 [DRY-RUN] Would remove profiles/default references from: $file_path"
                mkdir -p "$(dirname "$CLEANUP_CACHE/dry-run-preview/$file_path")"
                echo "$FILE_CONTENT" > "$CLEANUP_CACHE/dry-run-preview/$file_path"
            else
                echo "$FILE_CONTENT" > "$file_path"
                echo "✅ Removed profiles/default references from: $file_path"
                UNNECESSARY_LOGIC_REMOVED=$((UNNECESSARY_LOGIC_REMOVED + 1))
                FILES_MODIFIED=$((FILES_MODIFIED + 1))
            fi
        fi
    done
else
    echo "✅ No profiles/default references found"
fi

# Store cleanup results
echo "$UNNECESSARY_LOGIC_REMOVED" > "$CLEANUP_CACHE/unnecessary-logic-removed.txt"
echo "$FILES_MODIFIED" > "$CLEANUP_CACHE/files-modified-unnecessary-logic.txt"

if [ "$DRY_RUN" = "true" ]; then
    echo "🔍 [DRY-RUN] Would remove $UNNECESSARY_LOGIC_REMOVED unnecessary logic issue(s) in files"
else
    echo "✅ Removed $UNNECESSARY_LOGIC_REMOVED unnecessary logic issue(s) in $FILES_MODIFIED file(s)"
fi
```

## Important Constraints

- Must use unnecessary logic detection results from validation phase
- Must remove project-agnostic conditional logic
- Must remove profiles/default references from specialized commands
- Must support dry-run mode to preview changes
- Must track what was removed for cleanup report
- **CRITICAL**: All cleanup cache files must be stored in `agent-os/.cleanup-cache/` (temporary, cleaned up after cleanup completes)
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents
