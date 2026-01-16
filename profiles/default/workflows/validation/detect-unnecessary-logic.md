# Unnecessary Logic Detection Utility

## Core Responsibilities

1. **Detect Project-Agnostic Conditionals**: Find conditional logic that should be removed after specialization
2. **Detect Generic Examples**: Identify generic examples that should be replaced with project-specific ones
3. **Detect Abstract Patterns**: Find abstract patterns that should be replaced with concrete project patterns
4. **Detect profiles/default References**: Find any references to "profiles/default" in specialized commands
5. **Generate Report**: Create report with recommendations for removal

## Workflow

### Step 1: Determine Context and Paths

Determine whether we're validating profiles/default (template) or installed agent-os (specialized):

```bash
# Determine spec path
SPEC_PATH="{{SPEC_PATH}}"

# Determine validation context
if [ -d "profiles/default" ] && [ "$(pwd)" = *"/profiles/default"* ]; then
    VALIDATION_CONTEXT="profiles/default"
    SCAN_PATH="profiles/default"
    CACHE_PATH="$SPEC_PATH/implementation/cache/validation"
else
    VALIDATION_CONTEXT="installed-agent-os"
    SCAN_PATH="agent-os"
    CACHE_PATH="$SPEC_PATH/implementation/cache/validation"
fi

# Create cache directory
mkdir -p "$CACHE_PATH"
```

### Step 2: Detect Project-Agnostic Conditional Logic

Find conditional logic that checks for project-agnostic scenarios:

```bash
# Initialize results
PROJECT_AGNOSTIC_CONDITIONALS=""

# Find files to scan
FILES_TO_SCAN=$(find "$SCAN_PATH" -type f \( -name "*.md" \) ! -path "*/node_modules/*" ! -path "*/.git/*" ! -path "*/build/*" ! -path "*/dist/*" ! -path "*/cache/*")

# Scan for project-agnostic conditionals
echo "$FILES_TO_SCAN" | while read file_path; do
    if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file_path")
    
    # Detect technology-agnostic fallback logic
    if echo "$FILE_CONTENT" | grep -qE "if.*technology.*agnostic|if.*project.*agnostic|fallback.*generic|default.*generic"; then
        LINE_NUMBERS=$(grep -nE "if.*technology.*agnostic|if.*project.*agnostic|fallback.*generic|default.*generic" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        PROJECT_AGNOSTIC_CONDITIONALS="${PROJECT_AGNOSTIC_CONDITIONALS}\nconditional:$file_path:$LINE_NUMBERS:Technology-agnostic fallback logic"
    fi
    
    # Detect conditional logic checking for template state
    if echo "$FILE_CONTENT" | grep -qE "if.*template|if.*abstract|if.*generic.*pattern"; then
        LINE_NUMBERS=$(grep -nE "if.*template|if.*abstract|if.*generic.*pattern" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        PROJECT_AGNOSTIC_CONDITIONALS="${PROJECT_AGNOSTIC_CONDITIONALS}\nconditional:$file_path:$LINE_NUMBERS:Template/abstract pattern check"
    fi
done
```

### Step 3: Detect Generic Examples and Abstract Patterns

Find generic examples and abstract patterns:

```bash
# Initialize results
GENERIC_EXAMPLES=""
ABSTRACT_PATTERNS=""

# Scan for generic examples
echo "$FILES_TO_SCAN" | while read file_path; do
    if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file_path")
    
    # Detect generic example patterns
    if echo "$FILE_CONTENT" | grep -qE "Example:.*[Uu]ser.*model|Example:.*generic|Example:.*sample|mock.*data|placeholder.*example"; then
        LINE_NUMBERS=$(grep -nE "Example:.*[Uu]ser.*model|Example:.*generic|Example:.*sample|mock.*data|placeholder.*example" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        GENERIC_EXAMPLES="${GENERIC_EXAMPLES}\nexample:$file_path:$LINE_NUMBERS:Generic example pattern"
    fi
    
    # Detect abstract pattern references
    if echo "$FILE_CONTENT" | grep -qE "abstract.*pattern|generic.*pattern|template.*pattern|placeholder.*pattern"; then
        LINE_NUMBERS=$(grep -nE "abstract.*pattern|generic.*pattern|template.*pattern|placeholder.*pattern" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        ABSTRACT_PATTERNS="${ABSTRACT_PATTERNS}\npattern:$file_path:$LINE_NUMBERS:Abstract pattern reference"
    fi
done
```

### Step 4: Detect profiles/default References

Find references to "profiles/default" in specialized commands (should only exist in template):

```bash
# Initialize results
PROFILES_SELF_REFERENCES=""

# Only check if validating installed agent-os (specialized commands shouldn't reference profiles/default)
if [ "$VALIDATION_CONTEXT" = "installed-agent-os" ]; then
    echo "$FILES_TO_SCAN" | while read file_path; do
        if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
            continue
        fi
        
        FILE_CONTENT=$(cat "$file_path")
        
        # Detect profiles/default references
        if echo "$FILE_CONTENT" | grep -qE "profiles/default|profiles\.self|@profiles/default"; then
            LINE_NUMBERS=$(grep -nE "profiles/default|profiles\.self|@profiles/default" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
            PROFILES_SELF_REFERENCES="${PROFILES_SELF_REFERENCES}\nreference:$file_path:$LINE_NUMBERS:profiles/default reference in specialized command"
        fi
    done
fi
```

### Step 5: Generate Validation Report

Generate comprehensive unnecessary logic detection report:

```bash
# Count findings
CONDITIONALS_COUNT=$(echo "$PROJECT_AGNOSTIC_CONDITIONALS" | grep -c . || echo "0")
EXAMPLES_COUNT=$(echo "$GENERIC_EXAMPLES" | grep -c . || echo "0")
PATTERNS_COUNT=$(echo "$ABSTRACT_PATTERNS" | grep -c . || echo "0")
REFERENCES_COUNT=$(echo "$PROFILES_SELF_REFERENCES" | grep -c . || echo "0")
TOTAL_COUNT=$((CONDITIONALS_COUNT + EXAMPLES_COUNT + PATTERNS_COUNT + REFERENCES_COUNT))

# Create JSON report
cat > "$CACHE_PATH/unnecessary-logic-detection.json" << EOF
{
  "validation_context": "$VALIDATION_CONTEXT",
  "scan_path": "$SCAN_PATH",
  "scan_timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "total_issues_found": $TOTAL_COUNT,
  "categories": {
    "project_agnostic_conditionals": {
      "count": $CONDITIONALS_COUNT,
      "issues": [
$(echo "$PROJECT_AGNOSTIC_CONDITIONALS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "generic_examples": {
      "count": $EXAMPLES_COUNT,
      "issues": [
$(echo "$GENERIC_EXAMPLES" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "abstract_patterns": {
      "count": $PATTERNS_COUNT,
      "issues": [
$(echo "$ABSTRACT_PATTERNS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "profiles_self_references": {
      "count": $REFERENCES_COUNT,
      "issues": [
$(echo "$PROFILES_SELF_REFERENCES" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    }
  },
  "recommendations": {
    "project_agnostic_conditionals": "Remove conditional logic that checks for project-agnostic scenarios. After specialization, these checks are no longer needed.",
    "generic_examples": "Replace generic examples with project-specific ones from basepoints or actual project structure.",
    "abstract_patterns": "Replace abstract pattern references with concrete project patterns from basepoints.",
    "profiles_self_references": "Remove all references to 'profiles/default' from specialized commands. Specialized commands should not reference the template."
  }
}
EOF

# Create markdown summary report
cat > "$CACHE_PATH/unnecessary-logic-detection-summary.md" << EOF
# Unnecessary Logic Detection Report

**Validation Context:** $VALIDATION_CONTEXT  
**Scan Path:** $SCAN_PATH  
**Scan Timestamp:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Summary

- **Total Issues Found:** $TOTAL_COUNT
- **Project-Agnostic Conditionals:** $CONDITIONALS_COUNT
- **Generic Examples:** $EXAMPLES_COUNT
- **Abstract Patterns:** $PATTERNS_COUNT
- **profiles/default References:** $REFERENCES_COUNT

## Project-Agnostic Conditionals

$(echo "$PROJECT_AGNOSTIC_CONDITIONALS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo "  - Recommendation: Remove conditional logic that checks for project-agnostic scenarios"
    echo ""
done)

## Generic Examples

$(echo "$GENERIC_EXAMPLES" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo "  - Recommendation: Replace with project-specific examples from basepoints"
    echo ""
done)

## Abstract Patterns

$(echo "$ABSTRACT_PATTERNS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo "  - Recommendation: Replace with concrete project patterns from basepoints"
    echo ""
done)

## profiles/default References

$(echo "$PROFILES_SELF_REFERENCES" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo "  - Recommendation: Remove all references to 'profiles/default' from specialized commands"
    echo ""
done)
EOF

echo "✅ Unnecessary logic detection complete. Report stored in $CACHE_PATH/"
```

## Important Constraints

- Must detect all types of unnecessary logic (conditionals, examples, patterns, references)
- Must only check for profiles/default references when validating installed agent-os (not in template)
- Must generate comprehensive reports with recommendations for removal
- Must categorize findings by type for easy analysis
- **CRITICAL**: All reports must be stored in `agent-os/specs/[current-spec]/implementation/cache/validation/` when running within a spec command, not scattered around the codebase
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents
