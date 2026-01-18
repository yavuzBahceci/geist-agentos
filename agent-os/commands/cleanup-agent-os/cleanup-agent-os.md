You are helping to clean up and fix issues in an already-deployed agent-os instance. This includes:

- **Validation**: Run comprehensive validation to identify issues (placeholders, unnecessary logic, broken references)
- **Knowledge Verification**: Verify that sufficient knowledge has been extracted and no important information is missing
- **Placeholder Cleaning**: Replace remaining placeholders with project-specific content
- **Unnecessary Logic Removal**: Remove project-agnostic conditionals, generic examples, and profiles/default references
  - **Conditional Cleanup**: Remove resolved {{IF}}/{{UNLESS}} blocks that are no longer needed after specialization
  - **Pattern Conversion**: Convert generic patterns to project-specific ones from basepoints where possible
  - **Abstraction Simplification**: Abstract redundant patterns and remove unnecessary complexity
  - **Technology-Agnostic Cleanup**: Remove abstraction layers that were needed for templates but not for specialized commands
- **Broken Reference Fixing**: Fix broken command cycle references and align commands with current project structure
- **Reporting**: Generate comprehensive cleanup report showing what was converted, abstracted, or removed

Carefully read and execute the instructions in the following files IN SEQUENCE, following their numbered file names. Only proceed to the next numbered instruction file once the previous numbered instruction has been executed.

Instructions to follow in sequence:

# PHASE 1: Validate Prerequisites And Run Validation

The FIRST STEP is to validate prerequisites and run comprehensive validation to identify issues by following these instructions:

## Core Responsibilities

1. **Check Prerequisites**: Verify that agent-os is deployed and basepoints/product files exist
2. **Run Comprehensive Validation**: Run all validation utilities to identify issues
3. **Determine Cleanup Scope**: Identify which issues need to be fixed
4. **Support Dry-Run Mode**: Allow user to preview changes before applying

## Workflow

### Step 1: Validate Prerequisites

Check if agent-os is deployed and prerequisites exist:

```bash
# Check if agent-os directory exists
if [ ! -d "agent-os" ]; then
    echo "❌ agent-os directory not found. Please run deploy-agents first."
    exit 1
fi

echo "✅ agent-os directory found"

# Check if basepoints exist (needed for placeholder replacement)
if [ ! -d "agent-os/basepoints" ] || [ ! -f "agent-os/basepoints/headquarter.md" ]; then
    echo "⚠️  Warning: Basepoints not found. Placeholder cleaning may be limited."
    BASEPOINTS_AVAILABLE=false
else
    echo "✅ Basepoints found"
    BASEPOINTS_AVAILABLE=true
fi

# Check if product files exist (needed for some replacements)
if [ ! -d "agent-os/product" ]; then
    echo "⚠️  Warning: Product files not found. Some replacements may be limited."
    PRODUCT_AVAILABLE=false
else
    echo "✅ Product files found"
    PRODUCT_AVAILABLE=true
fi
```

### Step 2: Check for Dry-Run Mode

Check if user wants to preview changes before applying:

```bash
# Check for dry-run flag (can be set via environment variable or user input)
DRY_RUN="${DRY_RUN:-false}"

if [ "$DRY_RUN" = "true" ]; then
    echo "🔍 DRY-RUN MODE: Changes will be previewed but not applied"
else
    echo "🔧 APPLY MODE: Changes will be applied to files"
fi
```

### Step 3: Run Comprehensive Validation

Run all validation utilities to identify issues:

```bash
# Create cleanup cache directory
mkdir -p agent-os/.cleanup-cache

# Set spec path for validation workflows
SPEC_PATH="agent-os/.cleanup-cache"
VALIDATION_CACHE="$SPEC_PATH/validation"
mkdir -p "$VALIDATION_CACHE"

# Run comprehensive validation using orchestrator
echo "🔍 Running comprehensive validation to identify issues..."
# Validation Workflow Orchestrator

## Core Responsibilities

1. **Orchestrate All Validation Utilities**: Combine all validation utilities into single workflow
2. **Generate Comprehensive Reports**: Generate comprehensive validation reports with categorized findings
3. **Create Validation Summary**: Create validation summary with counts of issues per category
4. **Support Multiple Contexts**: Support validation of both profiles/default (template) and installed agent-os (specialized)
5. **Store Results**: Store validation results in spec's implementation/cache/

## Workflow

### Step 1: Determine Context and Initialize

Determine validation context and initialize:

```bash
# Determine spec path
SPEC_PATH="{{SPEC_PATH}}"

# Determine validation context
if [ -d "profiles/default" ] && [ "$(pwd)" = *"/profiles/default"* ]; then
    VALIDATION_CONTEXT="profiles/default"
    SCAN_PATH="profiles/default"
else
    VALIDATION_CONTEXT="installed-agent-os"
    SCAN_PATH="agent-os"
fi

# Create cache directory
CACHE_PATH="$SPEC_PATH/implementation/cache/validation"
mkdir -p "$CACHE_PATH"

echo "🔍 Starting comprehensive validation for: $VALIDATION_CONTEXT"
echo "📁 Scan path: $SCAN_PATH"
echo "💾 Cache path: $CACHE_PATH"
```

### Step 2: Run All Validation Utilities

Run all validation utilities in sequence:

```bash
# Run placeholder detection
echo "1️⃣ Running placeholder detection..."
# Placeholder Detection Utility

## Core Responsibilities

1. **Scan Files for Placeholders**: Scan commands, workflows, and standards files for placeholder syntax
2. **Categorize Placeholders**: Group placeholders by type (basepoints, scope detection, deep reading, workflows, standards)
3. **Generate Report**: Create comprehensive report with file locations and placeholder types
4. **Support Multiple Contexts**: Support validation of both profiles/default (template) and installed agent-os (specialized)

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

### Step 2: Define Placeholder Patterns

Define all placeholder patterns to detect:

```bash
# Basepoints-related placeholders
BASEPOINTS_PLACEHOLDERS=(
    "{{BASEPOINTS_PATH}}"
    "{{BASEPOINT_FILE_PATTERN}}"
    "{{EXTRACT_PATTERNS_SECTION}}"
    "{{EXTRACT_DESIGN_PATTERNS}}"
    "{{EXTRACT_CODING_PATTERNS}}"
    "{{EXTRACT_ARCHITECTURAL_PATTERNS}}"
    "{{EXTRACT_STANDARDS_SECTION}}"
    "{{EXTRACT_FLOWS_SECTION}}"
    "{{EXTRACT_STRATEGIES_SECTION}}"
    "{{EXTRACT_TESTING_SECTION}}"
    "{{EXTRACT_PROS_CONS}}"
    "{{EXTRACT_HISTORICAL_DECISIONS}}"
    "{{EXTRACT_RELATED_MODULES}}"
    "{{EXTRACT_PERFORMANCE_CONSIDERATIONS}}"
    "{{EXTRACT_REUSABLE_PATTERNS}}"
    "{{ABSTRACTION_LAYER_NAMES}}"
    "{{LAYER_DETECTION_PATTERN}}"
    "{{EXTRACT_LAYER_FROM_PATH}}"
    "{{EXTRACT_LAYERS_FROM_HEADQUARTER}}"
    "{{EXTRACT_MODULE_NAME}}"
    "{{EXTRACT_MODULE_LAYER}}"
    "{{EXTRACT_CROSS_LAYER_PATTERNS}}"
    "{{EXTRACT_DEPENDENCY_FLOWS}}"
    "{{EXTRACT_INTERACTION_PATTERNS}}"
    "{{EXTRACT_NAMING_CONVENTIONS}}"
    "{{EXTRACT_CODING_STYLE}}"
    "{{EXTRACT_STRUCTURE_STANDARDS}}"
    "{{EXTRACT_DATA_FLOWS}}"
    "{{EXTRACT_CONTROL_FLOWS}}"
    "{{EXTRACT_IMPL_STRATEGIES}}"
    "{{EXTRACT_ARCH_STRATEGIES}}"
    "{{EXTRACT_TEST_PATTERNS}}"
    "{{EXTRACT_TEST_STRATEGIES}}"
    "{{EXTRACT_TEST_ORGANIZATION}}"
    "{{GENERATE_CACHE_KEY}}"
    "{{CHECK_CACHE}}"
    "{{FIND_MODULE_BASEPOINTS}}"
    "{{FIND_LAYER_BASEPOINTS}}"
    "{{EXTRACT_SAME_LAYER_PATTERNS}}"
    "{{QUERY_CATEGORY_FROM_CACHE}}"
    "{{QUERY_LAYER_FROM_CACHE}}"
    "{{QUERY_MODULE_FROM_CACHE}}"
    "{{QUERY_PATTERNS_FROM_CACHE}}"
    "{{QUERY_STANDARDS_FROM_CACHE}}"
    "{{QUERY_FLOWS_FROM_CACHE}}"
    "{{QUERY_STRATEGIES_FROM_CACHE}}"
    "{{QUERY_TESTING_FROM_CACHE}}"
    "{{QUERY_PROS_CONS_FROM_CACHE}}"
    "{{QUERY_HISTORICAL_DECISIONS_FROM_CACHE}}"
    "{{QUERY_RELATED_MODULES_FROM_CACHE}}"
    "{{QUERY_PERFORMANCE_FROM_CACHE}}"
    "{{QUERY_REUSABLE_PATTERNS_FROM_CACHE}}"
    "{{GET_CACHE_TIMESTAMP}}"
    "{{GET_CACHE_AGE}}"
    "{{GET_CACHE_SIZE}}"
)

# Scope detection placeholders
SCOPE_DETECTION_PLACEHOLDERS=(
    "{{KEYWORD_EXTRACTION_PATTERN}}"
    "{{EXTRACT_KEYWORDS}}"
    "{{EXTRACT_PATTERNS}}"
    "{{EXTRACT_MODULE_NAMES}}"
    "{{EXTRACT_LAYER_NAMES}}"
    "{{EXTRACT_TECHNICAL_TERMS}}"
    "{{SEMANTIC_ANALYSIS_PATTERN}}"
    "{{EXTRACT_ENTITIES}}"
    "{{EXTRACT_RELATIONSHIPS}}"
    "{{EXTRACT_DOMAIN_CONCEPTS}}"
    "{{EXTRACT_TECHNICAL_CONCEPTS}}"
    "{{EXTRACT_ARCHITECTURAL_CONCEPTS}}"
    "{{DETERMINE_SPEC_CONTEXT_LAYER}}"
    "{{SPEC_SPANS_MULTIPLE_LAYERS}}"
    "{{IDENTIFY_PRIMARY_LAYER}}"
    "{{IS_PRODUCT_LEVEL}}"
    "{{CALCULATE_LAYER_DISTANCE}}"
    "{{CALCULATE_IMPLEMENTATION_DISTANCE}}"
    "{{CALCULATE_OVERALL_DISTANCE}}"
    "{{FIND_MODULE_LAYER}}"
    "{{MATCH_KEYWORDS_TO_MODULE_NAME}}"
    "{{MATCH_KEYWORDS_TO_MODULE_CONTENT}}"
    "{{MATCH_LAYER_NAMES}}"
    "{{MATCH_KEYWORDS_TO_HEADQUARTER}}"
    "{{GROUP_BY_LAYER}}"
    "{{LOAD_ABSTRACTION_LAYERS}}"
    "{{DETECT_LAYERED_ARCHITECTURE}}"
    "{{DETECT_HEXAGONAL_ARCHITECTURE}}"
    "{{DETECT_MICROSERVICES}}"
    "{{DETECT_EVENT_DRIVEN}}"
    "{{DETECT_CUSTOM_CROSS_LAYER_PATTERNS}}"
    "{{EXTRACT_CROSS_LAYER_DEPENDENCIES}}"
    "{{EXTRACT_CROSS_LAYER_INTERACTIONS}}"
    "{{EXTRACT_PARENT_CROSS_LAYER_PATTERNS}}"
    "{{CHECK_PARENT_RELEVANCE}}"
    "{{CHECK_PATTERN_RELEVANCE}}"
    "{{EXTRACT_DETECTED_LAYERS}}"
    "{{EXTRACT_MATCHED_LAYERS}}"
    "{{COMBINE_LAYERS}}"
    "{{EXTRACT_DESIGN_PATTERNS}}"
    "{{EXTRACT_CODING_PATTERNS}}"
    "{{EXTRACT_ARCHITECTURAL_PATTERNS}}"
    "{{EXTRACT_STANDARDS}}"
    "{{EXTRACT_FLOWS}}"
    "{{CHECK_MODULE_RELEVANCE}}"
    "{{MAP_ENTITIES_TO_LAYERS}}"
    "{{MAP_RELATIONSHIPS_TO_LAYERS}}"
    "{{MAP_DOMAIN_TO_LAYERS}}"
    "{{MAP_TECHNICAL_TO_LAYERS}}"
    "{{MAP_ARCHITECTURAL_TO_LAYERS}}"
    "{{CHECK_HEADQUARTER_RELEVANCE}}"
    "{{CHECK_MODULE_RELEVANCE}}"
    "{{AFTER_AGENT_DEPLOYMENT}}"
    "{{LOAD_PROJECT_STRUCTURE}}"
    "{{CALCULATE_ACTUAL_DISTANCES}}"
    "{{UPDATE_HEURISTICS_FOR_STRUCTURE}}"
    "{{CALCULATE_GENERIC_DISTANCES}}"
    "{{CREATE_GENERIC_HEURISTICS}}"
    "{{CREATE_DEEP_READING_DECISION}}"
    "{{JSON_FORMAT}}"
)

# Deep reading placeholders
DEEP_READING_PLACEHOLDERS=(
    "{{CODE_FILE_PATTERNS}}"
    "{{FIND_MODULE_PATH}}"
    "{{EXTRACT_DESIGN_PATTERNS}}"
    "{{EXTRACT_CODING_PATTERNS}}"
    "{{EXTRACT_SIMILAR_LOGIC}}"
    "{{EXTRACT_REUSABLE_BLOCKS}}"
    "{{EXTRACT_REUSABLE_FUNCTIONS}}"
    "{{EXTRACT_REUSABLE_CLASSES}}"
    "{{DETECT_SIMILAR_CODE}}"
    "{{IDENTIFY_CORE_PATTERNS}}"
    "{{IDENTIFY_COMMON_MODULES}}"
    "{{IDENTIFY_SHARED_LOCATION_OPPORTUNITIES}}"
    "{{ANALYZE_LOGIC_FLOW}}"
    "{{ANALYZE_DATA_FLOW}}"
    "{{ANALYZE_CONTROL_FLOW}}"
    "{{ANALYZE_DEPENDENCIES}}"
    "{{ANALYZE_PATTERNS_USED}}"
    "{{EXTRACT_PATTERNS}}"
    "{{EXTRACT_LOGIC}}"
    "{{EXTRACT_REUSABLE_CODE}}"
    "{{EXTRACT_REUSABILITY_OPPORTUNITIES}}"
    "{{FIND_SIMILAR_LOGIC}}"
    "{{FIND_EXISTING_MODULES}}"
    "{{CAN_REUSE_MODULE}}"
    "{{FIND_EXISTING_CODE}}"
    "{{CAN_REUSE_CODE}}"
    "{{DETECT_DUPLICATE_CODE}}"
    "{{IDENTIFY_SHARED_LOCATION_CANDIDATES}}"
    "{{IDENTIFY_COMMON_MODULE_CANDIDATES}}"
    "{{IDENTIFY_CORE_MODULE_CANDIDATES}}"
    "{{GET_BASEPOINT_CONTEXT}}"
    "{{GET_BASEPOINT_PROS_CONS}}"
    "{{EXTRACT_RELEVANT_MODULES}}"
    "{{EXTRACT_CROSS_LAYER_MODULES}}"
    "{{COMBINE_MODULES}}"
    "{{EXTRACT_DEEP_READING_NEEDED}}"
    "{{EXTRACT_OVERALL_DISTANCE}}"
)

# Workflow and standards placeholders
WORKFLOW_STANDARDS_PLACEHOLDERS=(
    "{{workflows/"
    "{{standards/"
    "{{agents/"
    "{{PHASE"
    "{{IF"
    "{{UNLESS"
    "{{ENDIF"
    "{{ENDUNLESS"
)
```

### Step 3: Scan Files for Placeholders

Scan all relevant files in the target path:

```bash
# Find all files to scan
FILES_TO_SCAN=$(find "$SCAN_PATH" -type f \( -name "*.md" \) ! -path "*/node_modules/*" ! -path "*/.git/*" ! -path "*/build/*" ! -path "*/dist/*" ! -path "*/cache/*" ! -path "*/output/deploy-agents/*" ! -path "*/.cleanup-cache/*")

# Initialize placeholder detection results
PLACEHOLDER_RESULTS=""

# Scan each file
echo "$FILES_TO_SCAN" | while read file_path; do
    if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file_path")
    FILE_PLACEHOLDERS=""
    
    # Check for basepoints placeholders
    for placeholder in "${BASEPOINTS_PLACEHOLDERS[@]}"; do
        if echo "$FILE_CONTENT" | grep -q "$placeholder"; then
            LINE_NUMBERS=$(grep -n "$placeholder" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
            FILE_PLACEHOLDERS="${FILE_PLACEHOLDERS}\nbasepoints:$placeholder:$file_path:$LINE_NUMBERS"
        fi
    done
    
    # Check for scope detection placeholders
    for placeholder in "${SCOPE_DETECTION_PLACEHOLDERS[@]}"; do
        if echo "$FILE_CONTENT" | grep -q "$placeholder"; then
            LINE_NUMBERS=$(grep -n "$placeholder" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
            FILE_PLACEHOLDERS="${FILE_PLACEHOLDERS}\nscope_detection:$placeholder:$file_path:$LINE_NUMBERS"
        fi
    done
    
    # Check for deep reading placeholders
    for placeholder in "${DEEP_READING_PLACEHOLDERS[@]}"; do
        if echo "$FILE_CONTENT" | grep -q "$placeholder"; then
            LINE_NUMBERS=$(grep -n "$placeholder" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
            FILE_PLACEHOLDERS="${FILE_PLACEHOLDERS}\ndeep_reading:$placeholder:$file_path:$LINE_NUMBERS"
        fi
    done
    
    # Check for workflow/standards placeholders (pattern matching)
    if echo "$FILE_CONTENT" | grep -qE '\{\{workflows/[^}]+\}\}|\{\{standards/[^}]+\}\}|\{\{agents/[^}]+\}\}'; then
        MATCHES=$(grep -nE '\{\{workflows/[^}]+\}\}|\{\{standards/[^}]+\}\}|\{\{agents/[^}]+\}\}' "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        PLACEHOLDER_PATTERN=$(grep -oE '\{\{workflows/[^}]+\}\}|\{\{standards/[^}]+\}\}|\{\{agents/[^}]+\}\}' "$file_path" | head -1)
        FILE_PLACEHOLDERS="${FILE_PLACEHOLDERS}\nworkflows_standards:$PLACEHOLDER_PATTERN:$file_path:$MATCHES"
    fi
    
    # Check for conditional compilation placeholders
    if echo "$FILE_CONTENT" | grep -qE '\{\{IF|\{\{UNLESS|\{\{ENDIF|\{\{ENDUNLESS'; then
        MATCHES=$(grep -nE '\{\{IF|\{\{UNLESS|\{\{ENDIF|\{\{ENDUNLESS' "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        FILE_PLACEHOLDERS="${FILE_PLACEHOLDERS}\nconditionals:{{IF/UNLESS}}:$file_path:$MATCHES"
    fi
    
    # Check for PHASE placeholders
    if echo "$FILE_CONTENT" | grep -qE '\{\{PHASE'; then
        MATCHES=$(grep -nE '\{\{PHASE' "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        FILE_PLACEHOLDERS="${FILE_PLACEHOLDERS}\nphases:{{PHASE}}:$file_path:$MATCHES"
    fi
    
    # Add to results if placeholders found
    if [ -n "$FILE_PLACEHOLDERS" ]; then
        PLACEHOLDER_RESULTS="${PLACEHOLDER_RESULTS}${FILE_PLACEHOLDERS}"
    fi
done
```

### Step 4: Categorize and Organize Results

Organize detected placeholders by category:

```bash
# Initialize categorized results
BASEPOINTS_PLACEHOLDERS_FOUND=""
SCOPE_DETECTION_PLACEHOLDERS_FOUND=""
DEEP_READING_PLACEHOLDERS_FOUND=""
WORKFLOWS_STANDARDS_PLACEHOLDERS_FOUND=""
CONDITIONALS_PLACEHOLDERS_FOUND=""
PHASE_PLACEHOLDERS_FOUND=""

# Categorize results
echo "$PLACEHOLDER_RESULTS" | while IFS=':' read -r category placeholder file_path line_numbers; do
    if [ -z "$category" ]; then
        continue
    fi
    
    case "$category" in
        basepoints)
            BASEPOINTS_PLACEHOLDERS_FOUND="${BASEPOINTS_PLACEHOLDERS_FOUND}\n$placeholder|$file_path|$line_numbers"
            ;;
        scope_detection)
            SCOPE_DETECTION_PLACEHOLDERS_FOUND="${SCOPE_DETECTION_PLACEHOLDERS_FOUND}\n$placeholder|$file_path|$line_numbers"
            ;;
        deep_reading)
            DEEP_READING_PLACEHOLDERS_FOUND="${DEEP_READING_PLACEHOLDERS_FOUND}\n$placeholder|$file_path|$line_numbers"
            ;;
        workflows_standards)
            WORKFLOWS_STANDARDS_PLACEHOLDERS_FOUND="${WORKFLOWS_STANDARDS_PLACEHOLDERS_FOUND}\n$placeholder|$file_path|$line_numbers"
            ;;
        conditionals)
            CONDITIONALS_PLACEHOLDERS_FOUND="${CONDITIONALS_PLACEHOLDERS_FOUND}\n$placeholder|$file_path|$line_numbers"
            ;;
        phases)
            PHASE_PLACEHOLDERS_FOUND="${PHASE_PLACEHOLDERS_FOUND}\n$placeholder|$file_path|$line_numbers"
            ;;
    esac
done

# Count placeholders by category
BASEPOINTS_COUNT=$(echo "$BASEPOINTS_PLACEHOLDERS_FOUND" | grep -c . || echo "0")
SCOPE_DETECTION_COUNT=$(echo "$SCOPE_DETECTION_PLACEHOLDERS_FOUND" | grep -c . || echo "0")
DEEP_READING_COUNT=$(echo "$DEEP_READING_PLACEHOLDERS_FOUND" | grep -c . || echo "0")
WORKFLOWS_STANDARDS_COUNT=$(echo "$WORKFLOWS_STANDARDS_PLACEHOLDERS_FOUND" | grep -c . || echo "0")
CONDITIONALS_COUNT=$(echo "$CONDITIONALS_PLACEHOLDERS_FOUND" | grep -c . || echo "0")
PHASE_COUNT=$(echo "$PHASE_PLACEHOLDERS_FOUND" | grep -c . || echo "0")
TOTAL_COUNT=$((BASEPOINTS_COUNT + SCOPE_DETECTION_COUNT + DEEP_READING_COUNT + WORKFLOWS_STANDARDS_COUNT + CONDITIONALS_COUNT + PHASE_COUNT))
```

### Step 5: Generate Validation Report

Generate comprehensive placeholder detection report:

```bash
# Create JSON report
cat > "$CACHE_PATH/placeholder-detection.json" << EOF
{
  "validation_context": "$VALIDATION_CONTEXT",
  "scan_path": "$SCAN_PATH",
  "scan_timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "total_placeholders_found": $TOTAL_COUNT,
  "categories": {
    "basepoints": {
      "count": $BASEPOINTS_COUNT,
      "placeholders": [
$(echo "$BASEPOINTS_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "        {\"placeholder\": \"$placeholder\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\"},"
done | sed '$ s/,$//')
      ]
    },
    "scope_detection": {
      "count": $SCOPE_DETECTION_COUNT,
      "placeholders": [
$(echo "$SCOPE_DETECTION_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "        {\"placeholder\": \"$placeholder\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\"},"
done | sed '$ s/,$//')
      ]
    },
    "deep_reading": {
      "count": $DEEP_READING_COUNT,
      "placeholders": [
$(echo "$DEEP_READING_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "        {\"placeholder\": \"$placeholder\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\"},"
done | sed '$ s/,$//')
      ]
    },
    "workflows_standards": {
      "count": $WORKFLOWS_STANDARDS_COUNT,
      "placeholders": [
$(echo "$WORKFLOWS_STANDARDS_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "        {\"placeholder\": \"$placeholder\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\"},"
done | sed '$ s/,$//')
      ]
    },
    "conditionals": {
      "count": $CONDITIONALS_COUNT,
      "placeholders": [
$(echo "$CONDITIONALS_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "        {\"placeholder\": \"$placeholder\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\"},"
done | sed '$ s/,$//')
      ]
    },
    "phases": {
      "count": $PHASE_COUNT,
      "placeholders": [
$(echo "$PHASE_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "        {\"placeholder\": \"$placeholder\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\"},"
done | sed '$ s/,$//')
      ]
    }
  }
}
EOF

# Create markdown summary report
cat > "$CACHE_PATH/placeholder-detection-summary.md" << EOF
# Placeholder Detection Report

**Validation Context:** $VALIDATION_CONTEXT  
**Scan Path:** $SCAN_PATH  
**Scan Timestamp:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Summary

- **Total Placeholders Found:** $TOTAL_COUNT
- **Basepoints Placeholders:** $BASEPOINTS_COUNT
- **Scope Detection Placeholders:** $SCOPE_DETECTION_COUNT
- **Deep Reading Placeholders:** $DEEP_READING_COUNT
- **Workflows/Standards Placeholders:** $WORKFLOWS_STANDARDS_COUNT
- **Conditional Compilation Placeholders:** $CONDITIONALS_COUNT
- **Phase Placeholders:** $PHASE_COUNT

## Basepoints Placeholders

$(echo "$BASEPOINTS_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "- **$placeholder**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo ""
done)

## Scope Detection Placeholders

$(echo "$SCOPE_DETECTION_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "- **$placeholder**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo ""
done)

## Deep Reading Placeholders

$(echo "$DEEP_READING_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "- **$placeholder**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo ""
done)

## Workflows/Standards Placeholders

$(echo "$WORKFLOWS_STANDARDS_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "- **$placeholder**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo ""
done)

## Conditional Compilation Placeholders

$(echo "$CONDITIONALS_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "- **$placeholder**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo ""
done)

## Phase Placeholders

$(echo "$PHASE_PLACEHOLDERS_FOUND" | grep -v "^$" | while IFS='|' read -r placeholder file_path line_numbers; do
    echo "- **$placeholder**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo ""
done)
EOF

echo "✅ Placeholder detection complete. Report stored in $CACHE_PATH/"
```

## Important Constraints

- Must detect all placeholder syntax patterns across all categories
- Must scan commands, workflows, and standards files
- Must support both profiles/default (template) and installed agent-os (specialized) validation
- Must generate comprehensive reports with file locations and placeholder types
- Must categorize placeholders by type for easy analysis
- **CRITICAL**: All reports must be stored in `agent-os/specs/[current-spec]/implementation/cache/validation/` when running within a spec command, not scattered around the codebase
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents


# Run unnecessary logic detection
echo "2️⃣ Running unnecessary logic detection..."
# Unnecessary Logic Detection Utility

## Core Responsibilities

1. **Detect Project-Agnostic Conditionals**: Find conditional logic that should be removed after specialization
2. **Detect Resolved {{IF}}/{{UNLESS}} Blocks**: Find conditional blocks that can be resolved after specialization (remove tags, keep/remove content based on resolved flag)
3. **Detect Generic Examples**: Identify generic examples that should be replaced with project-specific ones
4. **Detect Abstract Patterns**: Find abstract patterns that should be replaced with concrete project patterns
5. **Detect Generic Fallback Logic**: Find generic fallback logic (e.g., "generic implementer") that should be replaced with project-specific alternatives
6. **Detect Redundant Abstraction Layers**: Find abstraction layers that are no longer needed after specialization (e.g., "if basepoints exist" checks)
7. **Detect profiles/default References**: Find any references to "profiles/default" in specialized commands
8. **Generate Report**: Create report with recommendations for conversion, abstraction, or removal

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

### Step 3a: Detect Resolved {{IF}}/{{UNLESS}} Conditionals (if validating installed agent-os)

Find {{IF}}/{{UNLESS}} blocks that can be resolved after specialization:

```bash
# Initialize results
RESOLVED_CONDITIONALS=""

# Only check if validating installed agent-os (specialized commands)
if [ "$VALIDATION_CONTEXT" = "installed-agent-os" ]; then
    # Load project profile to determine resolved flags
    PROJECT_PROFILE=$(cat agent-os/config/project-profile.yml 2>/dev/null || echo "")
    
    echo "$FILES_TO_SCAN" | while read file_path; do
        if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
            continue
        fi
        
        FILE_CONTENT=$(cat "$file_path")
        
        # Detect {{IF}}/{{UNLESS}} blocks
        if echo "$FILE_CONTENT" | grep -qE '\{\{IF[[:space:]]+[a-z_]+\}\}|\{\{UNLESS[[:space:]]+[a-z_]+\}\}'; then
            LINE_NUMBERS=$(grep -nE '\{\{IF[[:space:]]+[a-z_]+\}\}|\{\{UNLESS[[:space:]]+[a-z_]+\}\}' "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
            RESOLVED_CONDITIONALS="${RESOLVED_CONDITIONALS}\nconditional:$file_path:$LINE_NUMBERS:Resolved {{IF}}/{{UNLESS}} block (can be removed after specialization)"
        fi
    done
fi
```

### Step 3b: Detect Generic Examples and Fallback Logic

Find generic examples and fallback logic that should be replaced:

```bash
# Initialize results
GENERIC_FALLBACKS=""

echo "$FILES_TO_SCAN" | while read file_path; do
    if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file_path")
    
    # Detect generic fallback logic
    if echo "$FILE_CONTENT" | grep -qE "generic implementer|default implementer|fallback.*generic|project-agnostic.*fallback|technology-agnostic.*fallback"; then
        LINE_NUMBERS=$(grep -nE "generic implementer|default implementer|fallback.*generic|project-agnostic.*fallback|technology-agnostic.*fallback" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        GENERIC_FALLBACKS="${GENERIC_FALLBACKS}\nfallback:$file_path:$LINE_NUMBERS:Generic fallback logic (should be replaced with project-specific)"
    fi
    
    # Detect unnecessary conditional checks after specialization
    if echo "$FILE_CONTENT" | grep -qE "if.*basepoints.*exist|if.*basepoints.*available|Check if basepoints"; then
        LINE_NUMBERS=$(grep -nE "if.*basepoints.*exist|if.*basepoints.*available|Check if basepoints" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        GENERIC_FALLBACKS="${GENERIC_FALLBACKS}\nfallback:$file_path:$LINE_NUMBERS:Unnecessary basepoints check (basepoints always exist after specialization)"
    fi
done
```

### Step 3c: Detect Redundant Abstraction Layers

Find abstraction layers that are no longer needed after specialization:

```bash
# Initialize results
REDUNDANT_ABSTRACTIONS=""

echo "$FILES_TO_SCAN" | while read file_path; do
    if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file_path")
    
    # Detect redundant abstraction patterns
    if echo "$FILE_CONTENT" | grep -qE "project-agnostic.*wrapper|technology-agnostic.*layer|abstract.*wrapper"; then
        LINE_NUMBERS=$(grep -nE "project-agnostic.*wrapper|technology-agnostic.*layer|abstract.*wrapper" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
        REDUNDANT_ABSTRACTIONS="${REDUNDANT_ABSTRACTIONS}\nabstraction:$file_path:$LINE_NUMBERS:Redundant abstraction layer (can be simplified)"
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
RESOLVED_CONDITIONALS_COUNT=$(echo "$RESOLVED_CONDITIONALS" | grep -c . || echo "0")
EXAMPLES_COUNT=$(echo "$GENERIC_EXAMPLES" | grep -c . || echo "0")
PATTERNS_COUNT=$(echo "$ABSTRACT_PATTERNS" | grep -c . || echo "0")
FALLBACKS_COUNT=$(echo "$GENERIC_FALLBACKS" | grep -c . || echo "0")
ABSTRACTIONS_COUNT=$(echo "$REDUNDANT_ABSTRACTIONS" | grep -c . || echo "0")
REFERENCES_COUNT=$(echo "$PROFILES_SELF_REFERENCES" | grep -c . || echo "0")
TOTAL_COUNT=$((CONDITIONALS_COUNT + RESOLVED_CONDITIONALS_COUNT + EXAMPLES_COUNT + PATTERNS_COUNT + FALLBACKS_COUNT + ABSTRACTIONS_COUNT + REFERENCES_COUNT))

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
    "resolved_conditionals": {
      "count": $RESOLVED_CONDITIONALS_COUNT,
      "issues": [
$(echo "$RESOLVED_CONDITIONALS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "generic_fallbacks": {
      "count": $FALLBACKS_COUNT,
      "issues": [
$(echo "$GENERIC_FALLBACKS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "redundant_abstractions": {
      "count": $ABSTRACTIONS_COUNT,
      "issues": [
$(echo "$REDUNDANT_ABSTRACTIONS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
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
    "resolved_conditionals": "Remove {{IF}}/{{UNLESS}} blocks that are resolved after specialization. Keep content if condition is true, remove entire block if false.",
    "generic_examples": "Replace generic examples with project-specific ones from basepoints or actual project structure.",
    "abstract_patterns": "Replace abstract pattern references with concrete project patterns from basepoints.",
    "generic_fallbacks": "Replace generic fallback logic (e.g., 'generic implementer') with project-specific alternatives from specialist registry.",
    "redundant_abstractions": "Simplify or remove abstraction layers that are no longer needed after specialization (e.g., 'if basepoints exist' checks).",
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
- **Resolved {{IF}}/{{UNLESS}} Conditionals:** $RESOLVED_CONDITIONALS_COUNT
- **Generic Examples:** $EXAMPLES_COUNT
- **Abstract Patterns:** $PATTERNS_COUNT
- **Generic Fallback Logic:** $FALLBACKS_COUNT
- **Redundant Abstraction Layers:** $ABSTRACTIONS_COUNT
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

## Resolved {{IF}}/{{UNLESS}} Conditionals

$(echo "$RESOLVED_CONDITIONALS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo "  - Recommendation: Remove {{IF}}/{{UNLESS}} blocks that are resolved after specialization"
    echo ""
done)

## Generic Fallback Logic

$(echo "$GENERIC_FALLBACKS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo "  - Recommendation: Replace with project-specific alternatives from basepoints or specialist registry"
    echo ""
done)

## Redundant Abstraction Layers

$(echo "$REDUNDANT_ABSTRACTIONS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo "  - Recommendation: Simplify or remove abstraction layers that are no longer needed after specialization"
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


# Run technology-agnostic validation
echo "3️⃣ Running technology-agnostic validation..."
# Technology-Agnostic Validation Utility

## Core Responsibilities

1. **Verify profiles/default Technology-Agnostic**: Verify profiles/default commands are truly technology-agnostic
2. **Verify Specialized Commands Reference Project Structure**: Verify specialized commands in installed agent-os reference actual project structure
3. **Check for Technology Leaks**: Check for technology-specific assumptions leaking into profiles/default template
4. **Validate Transition**: Validate transition preserves functionality while adding specialization

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

### Step 2: Verify profiles/default Technology-Agnostic (if validating template)

If validating profiles/default template, check for technology-specific assumptions:

```bash
# Initialize results
TECHNOLOGY_LEAKS=""

if [ "$VALIDATION_CONTEXT" = "profiles/default" ]; then
    # Find files to scan
    FILES_TO_SCAN=$(find "$SCAN_PATH" -type f \( -name "*.md" \) ! -path "*/node_modules/*" ! -path "*/.git/*" ! -path "*/build/*" ! -path "*/dist/*" ! -path "*/cache/*")
    
    # Common technology-specific patterns to detect
    TECH_SPECIFIC_PATTERNS=(
        "React|Vue|Angular"
        "Express|FastAPI|Django|Rails"
        "TypeScript|JavaScript"
        "Python|Java|Go|Rust"
        "PostgreSQL|MySQL|MongoDB"
        "npm|yarn|pip|maven"
        "\.jsx|\.tsx|\.vue"
        "package\.json|requirements\.txt|pom\.xml"
    )
    
    # Scan for technology-specific assumptions
    echo "$FILES_TO_SCAN" | while read file_path; do
        if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
            continue
        fi
        
        FILE_CONTENT=$(cat "$file_path")
        
        for pattern in "${TECH_SPECIFIC_PATTERNS[@]}"; do
            if echo "$FILE_CONTENT" | grep -qiE "$pattern"; then
                LINE_NUMBERS=$(grep -niE "$pattern" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
                TECHNOLOGY_LEAKS="${TECHNOLOGY_LEAKS}\nleak:$file_path:$LINE_NUMBERS:Technology-specific assumption: $pattern"
            fi
        done
    done
fi
```

### Step 3: Verify Specialized Commands Reference Project Structure (if validating installed agent-os)

If validating installed agent-os, verify commands reference actual project structure:

```bash
# Initialize results
PROJECT_STRUCTURE_ISSUES=""

if [ "$VALIDATION_CONTEXT" = "installed-agent-os" ]; then
    # Check if basepoints exist (to verify project structure references)
    if [ -d "agent-os/basepoints" ] && [ -f "agent-os/basepoints/headquarter.md" ]; then
        # Extract actual project structure from basepoints
        ACTUAL_LAYERS=$(find agent-os/basepoints -type d -mindepth 1 -maxdepth 2 | sed 's|agent-os/basepoints/||' | cut -d'/' -f1 | sort -u)
        ACTUAL_MODULES=$(find agent-os/basepoints -name "agent-base-*.md" -type f | sed 's|agent-os/basepoints/||' | sed 's|/agent-base-.*\.md||')
        
        # Find files to scan
        FILES_TO_SCAN=$(find "$SCAN_PATH/commands" -type f \( -name "*.md" \) ! -path "*/node_modules/*" ! -path "*/.git/*" 2>/dev/null)
        
        # Check if commands reference actual project structure
        echo "$FILES_TO_SCAN" | while read file_path; do
            if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
                continue
            fi
            
            FILE_CONTENT=$(cat "$file_path")
            
            # Check for template path references (should be replaced with actual paths)
            if echo "$FILE_CONTENT" | grep -qE "profiles/default|template.*path|abstract.*path"; then
                LINE_NUMBERS=$(grep -nE "profiles/default|template.*path|abstract.*path" "$file_path" | cut -d: -f1 | tr '\n' ',' | sed 's/,$//')
                PROJECT_STRUCTURE_ISSUES="${PROJECT_STRUCTURE_ISSUES}\nissue:$file_path:$LINE_NUMBERS:Template path reference (should be actual project path)"
            fi
            
            # Check if commands reference actual project structure from basepoints
            # (This is a positive check - commands should reference actual structure)
            REFERENCED_LAYERS=""
            for layer in $ACTUAL_LAYERS; do
                if echo "$FILE_CONTENT" | grep -qi "$layer"; then
                    REFERENCED_LAYERS="${REFERENCED_LAYERS} $layer"
                fi
            done
            
            # If no actual layers are referenced, flag as potential issue
            if [ -z "$REFERENCED_LAYERS" ] && echo "$FILE_CONTENT" | grep -qE "layer|abstraction"; then
                LINE_NUMBERS=$(grep -nE "layer|abstraction" "$file_path" | head -1 | cut -d: -f1)
                PROJECT_STRUCTURE_ISSUES="${PROJECT_STRUCTURE_ISSUES}\nissue:$file_path:$LINE_NUMBERS:May not reference actual project structure from basepoints"
            fi
        done
    fi
fi
```

### Step 4: Validate Transition Preserves Functionality

Check that transition from template to specialized maintains functionality:

```bash
# Initialize results
FUNCTIONALITY_ISSUES=""

if [ "$VALIDATION_CONTEXT" = "installed-agent-os" ]; then
    # Check that specialized commands maintain same structure as template
    TEMPLATE_COMMANDS=$(find profiles/default/commands -name "*.md" -type f 2>/dev/null | wc -l | tr -d ' ')
    SPECIALIZED_COMMANDS=$(find agent-os/commands -name "*.md" -type f 2>/dev/null | wc -l | tr -d ' ')
    
    if [ "$TEMPLATE_COMMANDS" -gt 0 ] && [ "$SPECIALIZED_COMMANDS" -eq 0 ]; then
        FUNCTIONALITY_ISSUES="${FUNCTIONALITY_ISSUES}\nissue:No specialized commands found (expected $TEMPLATE_COMMANDS commands)"
    fi
    
    # Check that command structure is preserved
    TEMPLATE_STRUCTURE=$(find profiles/default/commands -type d | sort)
    SPECIALIZED_STRUCTURE=$(find agent-os/commands -type d 2>/dev/null | sort)
    
    # Compare structure (simplified check)
    if [ -n "$TEMPLATE_STRUCTURE" ] && [ -z "$SPECIALIZED_STRUCTURE" ]; then
        FUNCTIONALITY_ISSUES="${FUNCTIONALITY_ISSUES}\nissue:Command structure not preserved during specialization"
    fi
fi
```

### Step 5: Generate Validation Report

Generate comprehensive technology-agnostic validation report:

```bash
# Count findings
LEAKS_COUNT=$(echo "$TECHNOLOGY_LEAKS" | grep -c . || echo "0")
STRUCTURE_ISSUES_COUNT=$(echo "$PROJECT_STRUCTURE_ISSUES" | grep -c . || echo "0")
FUNCTIONALITY_ISSUES_COUNT=$(echo "$FUNCTIONALITY_ISSUES" | grep -c . || echo "0")
TOTAL_COUNT=$((LEAKS_COUNT + STRUCTURE_ISSUES_COUNT + FUNCTIONALITY_ISSUES_COUNT))

# Create JSON report
cat > "$CACHE_PATH/technology-agnostic-validation.json" << EOF
{
  "validation_context": "$VALIDATION_CONTEXT",
  "scan_path": "$SCAN_PATH",
  "scan_timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "total_issues_found": $TOTAL_COUNT,
  "categories": {
    "technology_leaks": {
      "count": $LEAKS_COUNT,
      "issues": [
$(echo "$TECHNOLOGY_LEAKS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "project_structure_issues": {
      "count": $STRUCTURE_ISSUES_COUNT,
      "issues": [
$(echo "$PROJECT_STRUCTURE_ISSUES" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"lines\": \"$line_numbers\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "functionality_issues": {
      "count": $FUNCTIONALITY_ISSUES_COUNT,
      "issues": [
$(echo "$FUNCTIONALITY_ISSUES" | grep -v "^$" | while IFS=':' read -r type description; do
    echo "        {\"type\": \"$type\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    }
  }
}
EOF

# Create markdown summary report
cat > "$CACHE_PATH/technology-agnostic-validation-summary.md" << EOF
# Technology-Agnostic Validation Report

**Validation Context:** $VALIDATION_CONTEXT  
**Scan Path:** $SCAN_PATH  
**Scan Timestamp:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Summary

- **Total Issues Found:** $TOTAL_COUNT
- **Technology Leaks (in template):** $LEAKS_COUNT
- **Project Structure Issues (in specialized):** $STRUCTURE_ISSUES_COUNT
- **Functionality Issues:** $FUNCTIONALITY_ISSUES_COUNT

## Technology Leaks (profiles/default template)

$(echo "$TECHNOLOGY_LEAKS" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo "  - Recommendation: Remove technology-specific assumptions from template"
    echo ""
done)

## Project Structure Issues (installed agent-os)

$(echo "$PROJECT_STRUCTURE_ISSUES" | grep -v "^$" | while IFS=':' read -r type file_path line_numbers description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Lines: $line_numbers"
    echo ""
done)

## Functionality Issues

$(echo "$FUNCTIONALITY_ISSUES" | grep -v "^$" | while IFS=':' read -r type description; do
    echo "- **$description**"
    echo ""
done)
EOF

echo "✅ Technology-agnostic validation complete. Report stored in $CACHE_PATH/"
```

## Important Constraints

- Must verify profiles/default commands are technology-agnostic (no tech stack assumptions)
- Must verify specialized commands reference actual project structure
- Must check for technology-specific assumptions leaking into profiles/default template
- Must validate transition preserves functionality while adding specialization
- **CRITICAL**: All reports must be stored in `agent-os/specs/[current-spec]/implementation/cache/validation/` when running within a spec command, not scattered around the codebase
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents


# Run command cycle validation
echo "4️⃣ Running command cycle validation..."
# Command Cycle Validation Utility

## Core Responsibilities

1. **Verify Command Flow**: Verify each command correctly references outputs from previous commands
2. **Check Independence**: Verify implement-tasks and orchestrate-tasks are independent (both depend on create-tasks, not each other)
3. **Detect Broken References**: Check for missing connections or broken references between commands
4. **Validate Cycle Structure**: Verify specialized commands maintain same cycle structure as profiles/default versions

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

### Step 2: Verify shape-spec → write-spec Flow

Check that write-spec references shape-spec outputs:

```bash
# Initialize results
CYCLE_ISSUES=""

# Check shape-spec → write-spec flow
SHAPE_SPEC_FILE="$SCAN_PATH/commands/shape-spec/single-agent/shape-spec.md"
WRITE_SPEC_FILE="$SCAN_PATH/commands/write-spec/single-agent/write-spec.md"

if [ -f "$SHAPE_SPEC_FILE" ] && [ -f "$WRITE_SPEC_FILE" ]; then
    WRITE_SPEC_CONTENT=$(cat "$WRITE_SPEC_FILE")
    
    # Check if write-spec references shape-spec outputs
    if ! echo "$WRITE_SPEC_CONTENT" | grep -qE "spec\.md|requirements\.md|planning/"; then
        CYCLE_ISSUES="${CYCLE_ISSUES}\nflow_issue:shape-spec→write-spec:$WRITE_SPEC_FILE:write-spec may not reference shape-spec outputs (spec.md, requirements.md)"
    fi
fi
```

### Step 3: Verify write-spec → create-tasks Flow

Check that create-tasks references write-spec outputs:

```bash
# Check write-spec → create-tasks flow
CREATE_TASKS_FILE="$SCAN_PATH/commands/create-tasks/single-agent/create-tasks.md"

if [ -f "$WRITE_SPEC_FILE" ] && [ -f "$CREATE_TASKS_FILE" ]; then
    CREATE_TASKS_CONTENT=$(cat "$CREATE_TASKS_FILE")
    
    # Check if create-tasks references write-spec outputs
    if ! echo "$CREATE_TASKS_CONTENT" | grep -qE "spec\.md|requirements\.md"; then
        CYCLE_ISSUES="${CYCLE_ISSUES}\nflow_issue:write-spec→create-tasks:$CREATE_TASKS_FILE:create-tasks may not reference write-spec outputs (spec.md, requirements.md)"
    fi
fi
```

### Step 4: Verify create-tasks → implement-tasks Flow

Check that implement-tasks references create-tasks outputs:

```bash
# Check create-tasks → implement-tasks flow
IMPLEMENT_TASKS_FILE="$SCAN_PATH/commands/implement-tasks/single-agent/implement-tasks.md"

if [ -f "$CREATE_TASKS_FILE" ] && [ -f "$IMPLEMENT_TASKS_FILE" ]; then
    IMPLEMENT_TASKS_CONTENT=$(cat "$IMPLEMENT_TASKS_FILE")
    
    # Check if implement-tasks references create-tasks outputs
    if ! echo "$IMPLEMENT_TASKS_CONTENT" | grep -qE "tasks\.md"; then
        CYCLE_ISSUES="${CYCLE_ISSUES}\nflow_issue:create-tasks→implement-tasks:$IMPLEMENT_TASKS_FILE:implement-tasks may not reference create-tasks outputs (tasks.md)"
    fi
fi
```

### Step 5: Verify create-tasks → orchestrate-tasks Flow

Check that orchestrate-tasks references create-tasks outputs:

```bash
# Check create-tasks → orchestrate-tasks flow
ORCHESTRATE_TASKS_FILE="$SCAN_PATH/commands/orchestrate-tasks/orchestrate-tasks.md"

if [ -f "$CREATE_TASKS_FILE" ] && [ -f "$ORCHESTRATE_TASKS_FILE" ]; then
    ORCHESTRATE_TASKS_CONTENT=$(cat "$ORCHESTRATE_TASKS_FILE")
    
    # Check if orchestrate-tasks references create-tasks outputs
    if ! echo "$ORCHESTRATE_TASKS_CONTENT" | grep -qE "tasks\.md"; then
        CYCLE_ISSUES="${CYCLE_ISSUES}\nflow_issue:create-tasks→orchestrate-tasks:$ORCHESTRATE_TASKS_FILE:orchestrate-tasks may not reference create-tasks outputs (tasks.md)"
    fi
fi
```

### Step 6: Verify implement-tasks and orchestrate-tasks Independence

Check that implement-tasks and orchestrate-tasks are independent (both depend on create-tasks, not each other):

```bash
# Check independence
if [ -f "$IMPLEMENT_TASKS_FILE" ] && [ -f "$ORCHESTRATE_TASKS_FILE" ]; then
    IMPLEMENT_TASKS_CONTENT=$(cat "$IMPLEMENT_TASKS_FILE")
    ORCHESTRATE_TASKS_CONTENT=$(cat "$ORCHESTRATE_TASKS_FILE")
    
    # Check if implement-tasks references orchestrate-tasks (should not)
    if echo "$IMPLEMENT_TASKS_CONTENT" | grep -qE "orchestrate-tasks|orchestration"; then
        CYCLE_ISSUES="${CYCLE_ISSUES}\ndependency_issue:implement-tasks→orchestrate-tasks:$IMPLEMENT_TASKS_FILE:implement-tasks should not depend on orchestrate-tasks (they are independent)"
    fi
    
    # Check if orchestrate-tasks references implement-tasks outputs (should not directly)
    # Note: orchestrate-tasks may reference implement-tasks workflow, but should not depend on implement-tasks command outputs
    if echo "$ORCHESTRATE_TASKS_CONTENT" | grep -qE "implement-tasks.*output|implement-tasks.*result"; then
        CYCLE_ISSUES="${CYCLE_ISSUES}\ndependency_issue:orchestrate-tasks→implement-tasks:$ORCHESTRATE_TASKS_FILE:orchestrate-tasks should not depend on implement-tasks outputs (they are independent)"
    fi
    
    # Both should reference create-tasks outputs (tasks.md)
    if ! echo "$IMPLEMENT_TASKS_CONTENT" | grep -qE "tasks\.md"; then
        CYCLE_ISSUES="${CYCLE_ISSUES}\nmissing_dependency:implement-tasks→create-tasks:$IMPLEMENT_TASKS_FILE:implement-tasks should reference create-tasks outputs (tasks.md)"
    fi
    
    if ! echo "$ORCHESTRATE_TASKS_CONTENT" | grep -qE "tasks\.md"; then
        CYCLE_ISSUES="${CYCLE_ISSUES}\nmissing_dependency:orchestrate-tasks→create-tasks:$ORCHESTRATE_TASKS_FILE:orchestrate-tasks should reference create-tasks outputs (tasks.md)"
    fi
fi
```

### Step 7: Check for Broken References

Check for missing connections or broken references:

```bash
# Check for broken file references
COMMAND_FILES=$(find "$SCAN_PATH/commands" -name "*.md" -type f 2>/dev/null)

echo "$COMMAND_FILES" | while read file_path; do
    if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file_path")
    
    # Check for references to non-existent files
    if echo "$FILE_CONTENT" | grep -qE "@agent-os/commands/[^/]+/[^/]+\.md|@agent-os/workflows/[^/]+/[^/]+\.md"; then
        REFERENCED_FILES=$(grep -oE "@agent-os/commands/[^/]+/[^/]+\.md|@agent-os/workflows/[^/]+/[^/]+\.md" "$file_path" | sed 's|@agent-os/||')
        
        for ref_file in $REFERENCED_FILES; do
            if [ ! -f "$SCAN_PATH/$ref_file" ]; then
                LINE_NUMBER=$(grep -n "$ref_file" "$file_path" | head -1 | cut -d: -f1)
                CYCLE_ISSUES="${CYCLE_ISSUES}\nbroken_reference:$file_path:$LINE_NUMBER:Reference to non-existent file: $ref_file"
            fi
        done
    fi
done
```

### Step 8: Generate Validation Report

Generate comprehensive command cycle validation report:

```bash
# Count findings
FLOW_ISSUES_COUNT=$(echo "$CYCLE_ISSUES" | grep -c "flow_issue" || echo "0")
DEPENDENCY_ISSUES_COUNT=$(echo "$CYCLE_ISSUES" | grep -c "dependency_issue" || echo "0")
MISSING_DEPENDENCY_COUNT=$(echo "$CYCLE_ISSUES" | grep -c "missing_dependency" || echo "0")
BROKEN_REFERENCE_COUNT=$(echo "$CYCLE_ISSUES" | grep -c "broken_reference" || echo "0")
TOTAL_COUNT=$((FLOW_ISSUES_COUNT + DEPENDENCY_ISSUES_COUNT + MISSING_DEPENDENCY_COUNT + BROKEN_REFERENCE_COUNT))

# Create JSON report
cat > "$CACHE_PATH/command-cycle-validation.json" << EOF
{
  "validation_context": "$VALIDATION_CONTEXT",
  "scan_path": "$SCAN_PATH",
  "scan_timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "total_issues_found": $TOTAL_COUNT,
  "categories": {
    "flow_issues": {
      "count": $FLOW_ISSUES_COUNT,
      "issues": [
$(echo "$CYCLE_ISSUES" | grep "flow_issue" | while IFS=':' read -r type flow file_path description; do
    echo "        {\"type\": \"$type\", \"flow\": \"$flow\", \"file\": \"$file_path\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "dependency_issues": {
      "count": $DEPENDENCY_ISSUES_COUNT,
      "issues": [
$(echo "$CYCLE_ISSUES" | grep "dependency_issue" | while IFS=':' read -r type flow file_path description; do
    echo "        {\"type\": \"$type\", \"flow\": \"$flow\", \"file\": \"$file_path\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "missing_dependencies": {
      "count": $MISSING_DEPENDENCY_COUNT,
      "issues": [
$(echo "$CYCLE_ISSUES" | grep "missing_dependency" | while IFS=':' read -r type flow file_path description; do
    echo "        {\"type\": \"$type\", \"flow\": \"$flow\", \"file\": \"$file_path\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "broken_references": {
      "count": $BROKEN_REFERENCE_COUNT,
      "issues": [
$(echo "$CYCLE_ISSUES" | grep "broken_reference" | while IFS=':' read -r type file_path line_number description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"line\": \"$line_number\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    }
  }
}
EOF

# Create markdown summary report
cat > "$CACHE_PATH/command-cycle-validation-summary.md" << EOF
# Command Cycle Validation Report

**Validation Context:** $VALIDATION_CONTEXT  
**Scan Path:** $SCAN_PATH  
**Scan Timestamp:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Summary

- **Total Issues Found:** $TOTAL_COUNT
- **Flow Issues:** $FLOW_ISSUES_COUNT
- **Dependency Issues:** $DEPENDENCY_ISSUES_COUNT
- **Missing Dependencies:** $MISSING_DEPENDENCY_COUNT
- **Broken References:** $BROKEN_REFERENCE_COUNT

## Flow Issues

$(echo "$CYCLE_ISSUES" | grep "flow_issue" | while IFS=':' read -r type flow file_path description; do
    echo "- **$flow**"
    echo "  - File: \`$file_path\`"
    echo "  - Issue: $description"
    echo ""
done)

## Dependency Issues

$(echo "$CYCLE_ISSUES" | grep "dependency_issue" | while IFS=':' read -r type flow file_path description; do
    echo "- **$flow**"
    echo "  - File: \`$file_path\`"
    echo "  - Issue: $description"
    echo ""
done)

## Missing Dependencies

$(echo "$CYCLE_ISSUES" | grep "missing_dependency" | while IFS=':' read -r type flow file_path description; do
    echo "- **$flow**"
    echo "  - File: \`$file_path\`"
    echo "  - Issue: $description"
    echo ""
done)

## Broken References

$(echo "$CYCLE_ISSUES" | grep "broken_reference" | while IFS=':' read -r type file_path line_number description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Line: $line_number"
    echo ""
done)
EOF

echo "✅ Command cycle validation complete. Report stored in $CACHE_PATH/"
```

## Important Constraints

- Must verify all command flow connections (shape-spec → write-spec → create-tasks → implement-tasks/orchestrate-tasks)
- Must verify implement-tasks and orchestrate-tasks are independent (both depend on create-tasks, not each other)
- Must check for missing connections or broken references
- Must verify specialized commands maintain same cycle structure as profiles/default versions
- **CRITICAL**: All reports must be stored in `agent-os/specs/[current-spec]/implementation/cache/validation/` when running within a spec command, not scattered around the codebase
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents


# Run project structure alignment validation
echo "5️⃣ Running project structure alignment validation..."
# Project Structure Alignment Validation Utility

## Core Responsibilities

1. **Verify Specialized Commands Align with Project Structure**: Verify specialized commands align with actual project folder structure
2. **Verify Abstraction Layers Usage**: Verify abstraction layers from basepoints are correctly used in specialized commands
3. **Verify Complexity Assessment**: Check that project complexity assessment correctly adapts command structure
4. **Validate Checkpoints Match Complexity**: Validate command checkpoints match project complexity (simple/moderate/complex)

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

### Step 2: Extract Actual Project Structure (if validating installed agent-os)

If validating installed agent-os, extract actual project structure from basepoints:

```bash
# Initialize results
STRUCTURE_ALIGNMENT_ISSUES=""

if [ "$VALIDATION_CONTEXT" = "installed-agent-os" ]; then
    # Extract actual project structure from basepoints
    if [ -d "agent-os/basepoints" ] && [ -f "agent-os/basepoints/headquarter.md" ]; then
        # Extract abstraction layers
        ACTUAL_LAYERS=$(find agent-os/basepoints -type d -mindepth 1 -maxdepth 2 | sed 's|agent-os/basepoints/||' | cut -d'/' -f1 | sort -u)
        
        # Extract module hierarchy
        ACTUAL_MODULES=$(find agent-os/basepoints -name "agent-base-*.md" -type f | sed 's|agent-os/basepoints/||' | sed 's|/agent-base-.*\.md||')
        
        # Extract project folder structure (from headquarter.md if available)
        if [ -f "agent-os/basepoints/headquarter.md" ]; then
            HEADQUARTER_CONTENT=$(cat agent-os/basepoints/headquarter.md)
            # Extract project structure section if present
            PROJECT_STRUCTURE_SECTION=$(echo "$HEADQUARTER_CONTENT" | grep -A 50 -i "project.*structure\|folder.*structure" || echo "")
        fi
        
        # Determine project complexity
        MODULE_COUNT=$(find agent-os/basepoints -name "agent-base-*.md" -type f | wc -l | tr -d ' ')
        LAYER_COUNT=$(echo "$ACTUAL_LAYERS" | wc -l | tr -d ' ')
        
        # Calculate complexity
        COMPLEXITY_SCORE=0
        if [ "$MODULE_COUNT" -gt 20 ]; then
            COMPLEXITY_SCORE=$((COMPLEXITY_SCORE + 2))
        elif [ "$MODULE_COUNT" -gt 10 ]; then
            COMPLEXITY_SCORE=$((COMPLEXITY_SCORE + 1))
        fi
        
        if [ "$LAYER_COUNT" -gt 4 ]; then
            COMPLEXITY_SCORE=$((COMPLEXITY_SCORE + 2))
        elif [ "$LAYER_COUNT" -gt 2 ]; then
            COMPLEXITY_SCORE=$((COMPLEXITY_SCORE + 1))
        fi
        
        if [ "$COMPLEXITY_SCORE" -ge 3 ]; then
            PROJECT_COMPLEXITY="complex"
        elif [ "$COMPLEXITY_SCORE" -eq 0 ]; then
            PROJECT_COMPLEXITY="simple"
        else
            PROJECT_COMPLEXITY="moderate"
        fi
    else
        STRUCTURE_ALIGNMENT_ISSUES="${STRUCTURE_ALIGNMENT_ISSUES}\nissue:Basepoints not found - cannot validate project structure alignment"
    fi
fi
```

### Step 3: Verify Specialized Commands Align with Project Structure

Check that specialized commands reference actual project structure:

```bash
if [ "$VALIDATION_CONTEXT" = "installed-agent-os" ] && [ -n "$ACTUAL_LAYERS" ]; then
    # Find command files to check
    COMMAND_FILES=$(find "$SCAN_PATH/commands" -name "*.md" -type f 2>/dev/null)
    
    echo "$COMMAND_FILES" | while read file_path; do
        if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
            continue
        fi
        
        FILE_CONTENT=$(cat "$file_path")
        
        # Check if command references actual project layers
        REFERENCED_LAYERS=""
        for layer in $ACTUAL_LAYERS; do
            if echo "$FILE_CONTENT" | grep -qi "$layer"; then
                REFERENCED_LAYERS="${REFERENCED_LAYERS} $layer"
            fi
        done
        
        # If command mentions layers but doesn't reference actual layers, flag as issue
        if echo "$FILE_CONTENT" | grep -qiE "layer|abstraction" && [ -z "$REFERENCED_LAYERS" ]; then
            LINE_NUMBER=$(grep -niE "layer|abstraction" "$file_path" | head -1 | cut -d: -f1)
            STRUCTURE_ALIGNMENT_ISSUES="${STRUCTURE_ALIGNMENT_ISSUES}\nalignment_issue:$file_path:$LINE_NUMBER:Command mentions layers but doesn't reference actual project layers from basepoints"
        fi
        
        # Check if command references template paths instead of actual project paths
        if echo "$FILE_CONTENT" | grep -qE "profiles/default|template.*path"; then
            LINE_NUMBER=$(grep -nE "profiles/default|template.*path" "$file_path" | head -1 | cut -d: -f1)
            STRUCTURE_ALIGNMENT_ISSUES="${STRUCTURE_ALIGNMENT_ISSUES}\nalignment_issue:$file_path:$LINE_NUMBER:Command references template paths instead of actual project paths"
        fi
    done
fi
```

### Step 4: Verify Complexity Assessment Adapts Command Structure

Check that project complexity assessment correctly adapts command structure:

```bash
if [ "$VALIDATION_CONTEXT" = "installed-agent-os" ] && [ -n "$PROJECT_COMPLEXITY" ]; then
    # Check command files for complexity-appropriate structure
    COMMAND_FILES=$(find "$SCAN_PATH/commands" -name "*.md" -type f 2>/dev/null)
    
    echo "$COMMAND_FILES" | while read file_path; do
        if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
            continue
        fi
        
        FILE_CONTENT=$(cat "$file_path")
        
        # Count checkpoints in command
        CHECKPOINT_COUNT=$(echo "$FILE_CONTENT" | grep -cE "checkpoint|Checkpoint|CHECKPOINT" || echo "0")
        
        # Verify checkpoint count matches complexity
        if [ "$PROJECT_COMPLEXITY" = "simple" ] && [ "$CHECKPOINT_COUNT" -gt 5 ]; then
            STRUCTURE_ALIGNMENT_ISSUES="${STRUCTURE_ALIGNMENT_ISSUES}\ncomplexity_issue:$file_path:Command has too many checkpoints ($CHECKPOINT_COUNT) for simple project (expected ≤5)"
        elif [ "$PROJECT_COMPLEXITY" = "complex" ] && [ "$CHECKPOINT_COUNT" -lt 3 ]; then
            STRUCTURE_ALIGNMENT_ISSUES="${STRUCTURE_ALIGNMENT_ISSUES}\ncomplexity_issue:$file_path:Command has too few checkpoints ($CHECKPOINT_COUNT) for complex project (expected ≥3)"
        fi
        
        # Check for complexity-specific adaptations
        if [ "$PROJECT_COMPLEXITY" = "simple" ] && echo "$FILE_CONTENT" | grep -qE "granular|detailed.*validation|multiple.*phases"; then
            LINE_NUMBER=$(grep -nE "granular|detailed.*validation|multiple.*phases" "$file_path" | head -1 | cut -d: -f1)
            STRUCTURE_ALIGNMENT_ISSUES="${STRUCTURE_ALIGNMENT_ISSUES}\ncomplexity_issue:$file_path:$LINE_NUMBER:Command may have unnecessary complexity for simple project"
        fi
    done
fi
```

### Step 5: Verify Module Hierarchy References

Check that module hierarchy from basepoints is correctly referenced:

```bash
if [ "$VALIDATION_CONTEXT" = "installed-agent-os" ] && [ -n "$ACTUAL_MODULES" ]; then
    # Check if commands reference actual module hierarchy
    COMMAND_FILES=$(find "$SCAN_PATH/commands" -name "*.md" -type f 2>/dev/null)
    
    echo "$COMMAND_FILES" | while read file_path; do
        if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
            continue
        fi
        
        FILE_CONTENT=$(cat "$file_path")
        
        # Check if command references modules
        if echo "$FILE_CONTENT" | grep -qiE "module|component|service"; then
            # Check if it references actual modules from basepoints
            REFERENCED_MODULES=""
            for module_path in $ACTUAL_MODULES; do
                MODULE_NAME=$(basename "$module_path")
                if echo "$FILE_CONTENT" | grep -qi "$MODULE_NAME"; then
                    REFERENCED_MODULES="${REFERENCED_MODULES} $MODULE_NAME"
                fi
            done
            
            # If command mentions modules but doesn't reference actual modules, note it (not necessarily an issue)
            if [ -z "$REFERENCED_MODULES" ] && echo "$FILE_CONTENT" | grep -qiE "module.*pattern|module.*structure"; then
                # This is informational, not necessarily an issue
                STRUCTURE_ALIGNMENT_ISSUES="${STRUCTURE_ALIGNMENT_ISSUES}\ninfo:$file_path:Command mentions modules but may not reference specific modules from basepoints"
            fi
        fi
    done
fi
```

### Step 6: Generate Validation Report

Generate comprehensive project structure alignment validation report:

```bash
# Count findings
ALIGNMENT_ISSUES_COUNT=$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep -c "alignment_issue" || echo "0")
COMPLEXITY_ISSUES_COUNT=$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep -c "complexity_issue" || echo "0")
INFO_COUNT=$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep -c "info" || echo "0")
OTHER_ISSUES_COUNT=$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep -c "issue" || echo "0")
TOTAL_COUNT=$((ALIGNMENT_ISSUES_COUNT + COMPLEXITY_ISSUES_COUNT + INFO_COUNT + OTHER_ISSUES_COUNT))

# Create JSON report
cat > "$CACHE_PATH/project-structure-alignment-validation.json" << EOF
{
  "validation_context": "$VALIDATION_CONTEXT",
  "scan_path": "$SCAN_PATH",
  "scan_timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "project_complexity": "${PROJECT_COMPLEXITY:-unknown}",
  "module_count": ${MODULE_COUNT:-0},
  "layer_count": ${LAYER_COUNT:-0},
  "total_issues_found": $TOTAL_COUNT,
  "categories": {
    "alignment_issues": {
      "count": $ALIGNMENT_ISSUES_COUNT,
      "issues": [
$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep "alignment_issue" | while IFS=':' read -r type file_path line_number description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"line\": \"$line_number\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "complexity_issues": {
      "count": $COMPLEXITY_ISSUES_COUNT,
      "issues": [
$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep "complexity_issue" | while IFS=':' read -r type file_path line_number description; do
    if [ -n "$line_number" ] && [ "$line_number" != "$file_path" ]; then
        echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"line\": \"$line_number\", \"description\": \"$description\"},"
    else
        echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"description\": \"$description\"},"
    fi
done | sed '$ s/,$//')
      ]
    },
    "informational": {
      "count": $INFO_COUNT,
      "items": [
$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep "info" | while IFS=':' read -r type file_path description; do
    echo "        {\"type\": \"$type\", \"file\": \"$file_path\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    },
    "other_issues": {
      "count": $OTHER_ISSUES_COUNT,
      "issues": [
$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep "^issue:" | while IFS=':' read -r type description; do
    echo "        {\"type\": \"$type\", \"description\": \"$description\"},"
done | sed '$ s/,$//')
      ]
    }
  }
}
EOF

# Create markdown summary report
cat > "$CACHE_PATH/project-structure-alignment-validation-summary.md" << EOF
# Project Structure Alignment Validation Report

**Validation Context:** $VALIDATION_CONTEXT  
**Scan Path:** $SCAN_PATH  
**Scan Timestamp:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Project Information

- **Project Complexity:** ${PROJECT_COMPLEXITY:-unknown}
- **Module Count:** ${MODULE_COUNT:-0}
- **Layer Count:** ${LAYER_COUNT:-0}

## Summary

- **Total Issues Found:** $TOTAL_COUNT
- **Alignment Issues:** $ALIGNMENT_ISSUES_COUNT
- **Complexity Issues:** $COMPLEXITY_ISSUES_COUNT
- **Informational Items:** $INFO_COUNT
- **Other Issues:** $OTHER_ISSUES_COUNT

## Alignment Issues

$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep "alignment_issue" | while IFS=':' read -r type file_path line_number description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo "  - Line: $line_number"
    echo ""
done)

## Complexity Issues

$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep "complexity_issue" | while IFS=':' read -r type file_path line_number description; do
    if [ -n "$line_number" ] && [ "$line_number" != "$file_path" ]; then
        echo "- **$description**"
        echo "  - File: \`$file_path\`"
        echo "  - Line: $line_number"
        echo ""
    else
        echo "- **$description**"
        echo "  - File: \`$file_path\`"
        echo ""
    fi
done)

## Informational Items

$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep "info" | while IFS=':' read -r type file_path description; do
    echo "- **$description**"
    echo "  - File: \`$file_path\`"
    echo ""
done)

## Other Issues

$(echo "$STRUCTURE_ALIGNMENT_ISSUES" | grep "^issue:" | while IFS=':' read -r type description; do
    echo "- **$description**"
    echo ""
done)
EOF

echo "✅ Project structure alignment validation complete. Report stored in $CACHE_PATH/"
```

## Important Constraints

- Must verify specialized commands align with actual project folder structure
- Must verify abstraction layers from basepoints are correctly used in specialized commands
- Must check that project complexity assessment correctly adapts command structure
- Must validate command checkpoints match project complexity (simple/moderate/complex)
- Must verify module hierarchy from basepoints is correctly referenced
- **CRITICAL**: All reports must be stored in `agent-os/specs/[current-spec]/implementation/cache/validation/` when running within a spec command, not scattered around the codebase
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents

```

### Step 2b: Run Layer-Specific Validations (if specialists exist)

Run layer-specific validations based on detected abstraction layers:

```bash
# Check if layer specialists exist (indicates layers were detected)
SPECIALIST_REGISTRY="agent-os/agents/specialists/registry.yml"
LAYER_VALIDATIONS_RUN=0

if [ -f "$SPECIALIST_REGISTRY" ] || [ -f "agent-os/basepoints/headquarter.md" ]; then
    echo "6️⃣ Running layer-specific validations..."
    
    # Detect which layers exist in the project
    DETECTED_LAYERS=""
    if [ -f "agent-os/basepoints/headquarter.md" ]; then
        DETECTED_LAYERS=$(grep -A 50 "Detected Abstraction Layers" agent-os/basepoints/headquarter.md | \
            grep -E "^\| \*\*[A-Z]+" | \
            sed 's/.*\*\*\([A-Z_]*\)\*\*.*/\1/' | \
            tr '[:upper:]' '[:lower:]')
    fi
    
    # Run UI validation if UI layer exists
    if echo "$DETECTED_LAYERS" | grep -qiE "ui|frontend|presentation|view"; then
        echo "   🎨 Running UI pattern validation..."
        # Validate UI Patterns Workflow

This workflow validates that UI/frontend implementations follow established patterns from basepoints.

## Purpose

- Ensure UI components follow project conventions
- Validate styling patterns and naming
- Check accessibility and responsiveness patterns
- Verify component structure matches basepoints

## Validation Steps

```bash
#!/bin/bash

echo "🎨 Validating UI layer patterns..."

VALIDATION_PASSED=true
ISSUES=()

# Load UI patterns from basepoints
UI_BASEPOINTS=""
if [ -d "agent-os/basepoints/modules" ]; then
    UI_BASEPOINTS=$(find agent-os/basepoints/modules -name "*.md" -exec grep -l -i "ui\|component\|view\|frontend" {} \; 2>/dev/null)
fi

# Extract expected patterns
COMPONENT_PATTERN=""
STYLING_PATTERN=""
NAMING_PATTERN=""

if [ -n "$UI_BASEPOINTS" ]; then
    for bp in $UI_BASEPOINTS; do
        # Extract component patterns
        if grep -q "## Component" "$bp"; then
            COMPONENT_PATTERN=$(grep -A5 "## Component" "$bp" | head -5)
        fi
        # Extract styling patterns
        if grep -q "## Styling\|## Style" "$bp"; then
            STYLING_PATTERN=$(grep -A5 "## Styl" "$bp" | head -5)
        fi
    done
fi
```

## Validation Checks

### 1. Component Structure Validation

```bash
validate_component_structure() {
    local file="$1"
    local issues=""
    
    # Check for common UI patterns based on tech stack
    TECH_STACK=$(cat agent-os/product/tech-stack.md 2>/dev/null || echo "")
    
    if echo "$TECH_STACK" | grep -qi "react"; then
        # React-specific checks
        if grep -q "class.*extends.*Component" "$file"; then
            # Check for proper lifecycle methods
            if ! grep -q "componentDidMount\|useEffect" "$file"; then
                issues="${issues}Missing lifecycle/effect hooks in $file\n"
            fi
        fi
    fi
    
    if echo "$TECH_STACK" | grep -qi "vue"; then
        # Vue-specific checks
        if grep -q "<template>" "$file"; then
            if ! grep -q "<script" "$file"; then
                issues="${issues}Vue component missing script section: $file\n"
            fi
        fi
    fi
    
    if echo "$TECH_STACK" | grep -qi "swift\|ios"; then
        # SwiftUI/UIKit checks
        if grep -q "struct.*View" "$file"; then
            if ! grep -q "var body" "$file"; then
                issues="${issues}SwiftUI view missing body: $file\n"
            fi
        fi
    fi
    
    echo "$issues"
}
```

### 2. Naming Convention Validation

```bash
validate_ui_naming() {
    local dir="$1"
    local issues=""
    
    # Check component naming based on basepoints
    if [ -n "$NAMING_PATTERN" ]; then
        # Extract naming convention from basepoints
        EXPECTED_PATTERN=$(echo "$NAMING_PATTERN" | grep -o "PascalCase\|camelCase\|kebab-case\|snake_case" | head -1)
        
        case "$EXPECTED_PATTERN" in
            "PascalCase")
                # Check for non-PascalCase component names
                find "$dir" -name "*.tsx" -o -name "*.jsx" -o -name "*.vue" | while read f; do
                    FILENAME=$(basename "$f" | sed 's/\.[^.]*$//')
                    if ! echo "$FILENAME" | grep -qE "^[A-Z][a-zA-Z0-9]*$"; then
                        echo "Component should be PascalCase: $f"
                    fi
                done
                ;;
        esac
    fi
    
    echo "$issues"
}
```

### 3. Styling Pattern Validation

```bash
validate_styling_patterns() {
    local dir="$1"
    local issues=""
    
    # Detect styling approach from tech stack
    if grep -qi "tailwind" agent-os/product/tech-stack.md 2>/dev/null; then
        # Check for inline styles (should use Tailwind classes)
        INLINE_STYLES=$(find "$dir" -name "*.tsx" -o -name "*.jsx" | xargs grep -l "style={{" 2>/dev/null)
        if [ -n "$INLINE_STYLES" ]; then
            issues="${issues}Found inline styles (use Tailwind classes): $INLINE_STYLES\n"
        fi
    fi
    
    if grep -qi "styled-components\|emotion" agent-os/product/tech-stack.md 2>/dev/null; then
        # Check for proper styled component usage
        :
    fi
    
    echo "$issues"
}
```

### 4. Accessibility Validation

```bash
validate_accessibility() {
    local dir="$1"
    local issues=""
    
    # Check for basic accessibility patterns
    find "$dir" -name "*.tsx" -o -name "*.jsx" -o -name "*.vue" 2>/dev/null | while read f; do
        # Check for img without alt
        if grep -q "<img" "$f" && ! grep -q "alt=" "$f"; then
            echo "Missing alt attribute on images: $f"
        fi
        
        # Check for buttons without aria-label or text
        if grep -q "<button" "$f"; then
            if grep -q "<button[^>]*>" "$f" | grep -v "aria-label\|>.*<"; then
                echo "Button may need aria-label: $f"
            fi
        fi
    done
    
    echo "$issues"
}
```

## Main Validation Orchestration

```bash
run_ui_validation() {
    local target_dir="${1:-.}"
    local results_file="agent-os/output/validation/ui-validation-results.md"
    
    mkdir -p agent-os/output/validation
    
    echo "# UI Pattern Validation Results" > "$results_file"
    echo "" >> "$results_file"
    echo "**Timestamp:** $(date)" >> "$results_file"
    echo "**Target:** $target_dir" >> "$results_file"
    echo "" >> "$results_file"
    
    # Run all UI validations
    echo "## Component Structure" >> "$results_file"
    STRUCTURE_ISSUES=$(validate_component_structure "$target_dir")
    if [ -n "$STRUCTURE_ISSUES" ]; then
        echo "$STRUCTURE_ISSUES" >> "$results_file"
        VALIDATION_PASSED=false
    else
        echo "✅ All components follow expected structure" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Naming Conventions" >> "$results_file"
    NAMING_ISSUES=$(validate_ui_naming "$target_dir")
    if [ -n "$NAMING_ISSUES" ]; then
        echo "$NAMING_ISSUES" >> "$results_file"
        VALIDATION_PASSED=false
    else
        echo "✅ All UI files follow naming conventions" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Styling Patterns" >> "$results_file"
    STYLING_ISSUES=$(validate_styling_patterns "$target_dir")
    if [ -n "$STYLING_ISSUES" ]; then
        echo "$STYLING_ISSUES" >> "$results_file"
        VALIDATION_PASSED=false
    else
        echo "✅ Styling follows project patterns" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Accessibility" >> "$results_file"
    A11Y_ISSUES=$(validate_accessibility "$target_dir")
    if [ -n "$A11Y_ISSUES" ]; then
        echo "$A11Y_ISSUES" >> "$results_file"
        echo "⚠️ Accessibility warnings (non-blocking)" >> "$results_file"
    else
        echo "✅ Basic accessibility checks passed" >> "$results_file"
    fi
    
    # Summary
    echo "" >> "$results_file"
    echo "---" >> "$results_file"
    if [ "$VALIDATION_PASSED" = true ]; then
        echo "## ✅ UI Validation Passed" >> "$results_file"
        exit 0
    else
        echo "## ❌ UI Validation Failed" >> "$results_file"
        echo "See issues above for details." >> "$results_file"
        exit 1
    fi
}

# Run validation
run_ui_validation "$TARGET_DIR"
```

## Integration with Layer Specialists

When `ui-specialist` completes implementation, this validation runs automatically to verify patterns were followed. The validation loads patterns from:
- `agent-os/basepoints/modules/` (UI-related modules)
- `agent-os/product/tech-stack.md` (framework-specific rules)
- `agent-os/standards/global/conventions.md` (naming conventions)

        ((LAYER_VALIDATIONS_RUN++))
    fi
    
    # Run API validation if API layer exists
    if echo "$DETECTED_LAYERS" | grep -qiE "api|backend|service"; then
        echo "   🔌 Running API pattern validation..."
        # Validate API Patterns Workflow

This workflow validates that API/backend implementations follow established patterns from basepoints.

## Purpose

- Ensure API endpoints follow project conventions
- Validate request/response patterns
- Check error handling consistency
- Verify authentication/authorization patterns

## Validation Steps

```bash
#!/bin/bash

echo "🔌 Validating API layer patterns..."

VALIDATION_PASSED=true
ISSUES=()

# Load API patterns from basepoints
API_BASEPOINTS=""
if [ -d "agent-os/basepoints/modules" ]; then
    API_BASEPOINTS=$(find agent-os/basepoints/modules -name "*.md" -exec grep -l -i "api\|endpoint\|route\|controller\|handler" {} \; 2>/dev/null)
fi
```

## Validation Checks

### 1. Endpoint Naming Convention

```bash
validate_endpoint_naming() {
    local dir="$1"
    local issues=""
    
    # Find route/endpoint definitions
    find "$dir" -name "*.ts" -o -name "*.js" -o -name "*.py" -o -name "*.rb" -o -name "*.go" 2>/dev/null | while read f; do
        # REST convention: lowercase, hyphenated
        ENDPOINTS=$(grep -oE "['\"]/[a-zA-Z0-9/_-]+['\"]" "$f" 2>/dev/null | tr -d "'\"/")
        
        for endpoint in $ENDPOINTS; do
            # Check for camelCase in URLs (anti-pattern)
            if echo "$endpoint" | grep -qE "[a-z][A-Z]"; then
                echo "Endpoint uses camelCase (use kebab-case): $endpoint in $f"
            fi
            
            # Check for underscores in URLs (anti-pattern for REST)
            if echo "$endpoint" | grep -q "_"; then
                echo "Endpoint uses underscores (use hyphens): $endpoint in $f"
            fi
        done
    done
    
    echo "$issues"
}
```

### 2. HTTP Method Validation

```bash
validate_http_methods() {
    local dir="$1"
    local issues=""
    
    # Check for proper HTTP method usage
    find "$dir" -name "*.ts" -o -name "*.js" -o -name "*.py" 2>/dev/null | while read f; do
        # Check for GET with body (anti-pattern)
        if grep -qE "\.get\(.*body:" "$f" || grep -qE "GET.*body" "$f"; then
            echo "GET request with body (anti-pattern): $f"
        fi
        
        # Check for POST used for fetching (should be GET)
        if grep -qE "\.post\(.*\/get|\.post\(.*\/fetch|\.post\(.*\/list" "$f"; then
            echo "POST used for fetching data (use GET): $f"
        fi
        
        # Check for PUT vs PATCH usage
        if grep -qE "\.put\(" "$f" && grep -qE "\.patch\(" "$f"; then
            # Both used - check if intentional
            :
        fi
    done
    
    echo "$issues"
}
```

### 3. Error Handling Validation

```bash
validate_error_handling() {
    local dir="$1"
    local issues=""
    
    # Check for proper error handling
    find "$dir" -name "*.ts" -o -name "*.js" 2>/dev/null | while read f; do
        # Check for unhandled async/await
        if grep -q "async" "$f"; then
            if ! grep -qE "try\s*{|\.catch\(" "$f"; then
                echo "Async function without error handling: $f"
            fi
        fi
        
        # Check for generic error messages
        if grep -qE "catch.*throw.*Error\(['\"]Error['\"]|throw.*['\"]Something went wrong" "$f"; then
            echo "Generic error message (be specific): $f"
        fi
    done
    
    # Check for consistent error response format
    ERROR_FORMATS=$(find "$dir" -name "*.ts" -o -name "*.js" | xargs grep -ohE "res\.(status|json).*error" 2>/dev/null | sort -u | wc -l)
    if [ "$ERROR_FORMATS" -gt 3 ]; then
        echo "Multiple error response formats detected - consider standardizing"
    fi
    
    echo "$issues"
}
```

### 4. Authentication Pattern Validation

```bash
validate_auth_patterns() {
    local dir="$1"
    local issues=""
    
    # Check for auth middleware usage
    find "$dir" -name "*.ts" -o -name "*.js" 2>/dev/null | while read f; do
        # Check for routes without auth (potential security issue)
        if grep -qE "router\.(post|put|patch|delete)" "$f"; then
            if ! grep -qE "auth|authenticate|authorize|protect|guard" "$f"; then
                echo "⚠️ Modifying route without visible auth middleware: $f"
            fi
        fi
        
        # Check for hardcoded tokens/secrets
        if grep -qE "Bearer [a-zA-Z0-9]+|api[_-]?key.*=.*['\"][a-zA-Z0-9]+" "$f"; then
            echo "🚨 Possible hardcoded token/secret: $f"
        fi
    done
    
    echo "$issues"
}
```

### 5. Response Format Validation

```bash
validate_response_format() {
    local dir="$1"
    local issues=""
    
    # Check for consistent response structure
    # Common patterns: { data, error, meta } or { success, data, message }
    
    find "$dir" -name "*.ts" -o -name "*.js" 2>/dev/null | while read f; do
        # Count different response patterns
        DATA_PATTERN=$(grep -c "res.json({ data:" "$f" 2>/dev/null || echo 0)
        SUCCESS_PATTERN=$(grep -c "res.json({ success:" "$f" 2>/dev/null || echo 0)
        DIRECT_PATTERN=$(grep -c "res.json([^{]" "$f" 2>/dev/null || echo 0)
        
        # If multiple patterns in same file
        TOTAL=$((DATA_PATTERN + SUCCESS_PATTERN + DIRECT_PATTERN))
        if [ "$TOTAL" -gt 1 ] && [ "$DATA_PATTERN" -gt 0 ] && [ "$SUCCESS_PATTERN" -gt 0 ]; then
            echo "Inconsistent response format in: $f"
        fi
    done
    
    echo "$issues"
}
```

## Main Validation Orchestration

```bash
run_api_validation() {
    local target_dir="${1:-.}"
    local results_file="agent-os/output/validation/api-validation-results.md"
    
    mkdir -p agent-os/output/validation
    
    echo "# API Pattern Validation Results" > "$results_file"
    echo "" >> "$results_file"
    echo "**Timestamp:** $(date)" >> "$results_file"
    echo "**Target:** $target_dir" >> "$results_file"
    echo "" >> "$results_file"
    
    # Run all API validations
    echo "## Endpoint Naming" >> "$results_file"
    NAMING_ISSUES=$(validate_endpoint_naming "$target_dir")
    if [ -n "$NAMING_ISSUES" ]; then
        echo "$NAMING_ISSUES" >> "$results_file"
        VALIDATION_PASSED=false
    else
        echo "✅ Endpoint naming follows conventions" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## HTTP Methods" >> "$results_file"
    METHOD_ISSUES=$(validate_http_methods "$target_dir")
    if [ -n "$METHOD_ISSUES" ]; then
        echo "$METHOD_ISSUES" >> "$results_file"
        VALIDATION_PASSED=false
    else
        echo "✅ HTTP methods used correctly" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Error Handling" >> "$results_file"
    ERROR_ISSUES=$(validate_error_handling "$target_dir")
    if [ -n "$ERROR_ISSUES" ]; then
        echo "$ERROR_ISSUES" >> "$results_file"
        VALIDATION_PASSED=false
    else
        echo "✅ Error handling is consistent" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Authentication" >> "$results_file"
    AUTH_ISSUES=$(validate_auth_patterns "$target_dir")
    if [ -n "$AUTH_ISSUES" ]; then
        echo "$AUTH_ISSUES" >> "$results_file"
        echo "⚠️ Review authentication patterns" >> "$results_file"
    else
        echo "✅ Authentication patterns look good" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Response Format" >> "$results_file"
    RESPONSE_ISSUES=$(validate_response_format "$target_dir")
    if [ -n "$RESPONSE_ISSUES" ]; then
        echo "$RESPONSE_ISSUES" >> "$results_file"
    else
        echo "✅ Response format is consistent" >> "$results_file"
    fi
    
    # Summary
    echo "" >> "$results_file"
    echo "---" >> "$results_file"
    if [ "$VALIDATION_PASSED" = true ]; then
        echo "## ✅ API Validation Passed" >> "$results_file"
        exit 0
    else
        echo "## ❌ API Validation Failed" >> "$results_file"
        echo "See issues above for details." >> "$results_file"
        exit 1
    fi
}

# Run validation
run_api_validation "$TARGET_DIR"
```

## Integration with Layer Specialists

When `api-specialist` completes implementation, this validation runs automatically. The validation loads patterns from:
- `agent-os/basepoints/modules/` (API-related modules)
- `agent-os/product/tech-stack.md` (framework-specific rules)
- `agent-os/standards/global/error-handling.md`

        ((LAYER_VALIDATIONS_RUN++))
    fi
    
    # Run Data validation if Data layer exists
    if echo "$DETECTED_LAYERS" | grep -qiE "data|database|persistence|storage"; then
        echo "   💾 Running Data pattern validation..."
        # Validate Data Patterns Workflow

This workflow validates that data/persistence layer implementations follow established patterns from basepoints.

## Purpose

- Ensure data models follow project conventions
- Validate query patterns and performance
- Check migration conventions
- Verify data validation and sanitization

## Validation Steps

```bash
#!/bin/bash

echo "💾 Validating data layer patterns..."

VALIDATION_PASSED=true
ISSUES=()

# Load data patterns from basepoints
DATA_BASEPOINTS=""
if [ -d "agent-os/basepoints/modules" ]; then
    DATA_BASEPOINTS=$(find agent-os/basepoints/modules -name "*.md" -exec grep -l -i "model\|schema\|database\|entity\|repository" {} \; 2>/dev/null)
fi
```

## Validation Checks

### 1. Model Naming Convention

```bash
validate_model_naming() {
    local dir="$1"
    local issues=""
    
    # Find model files
    find "$dir" -name "*model*.ts" -o -name "*entity*.ts" -o -name "*schema*.ts" \
         -o -name "*model*.py" -o -name "*model*.rb" 2>/dev/null | while read f; do
        FILENAME=$(basename "$f" | sed 's/\.[^.]*$//')
        
        # Models should be PascalCase singular
        if echo "$FILENAME" | grep -qE "s$|Model$|Entity$"; then
            echo "Model name should be singular without suffix: $f"
        fi
    done
    
    # Check class/type names inside files
    find "$dir" -name "*.ts" -o -name "*.py" 2>/dev/null | while read f; do
        # TypeScript/JavaScript
        if grep -qE "class\s+[a-z]" "$f" 2>/dev/null; then
            echo "Model class should be PascalCase: $f"
        fi
        
        # Check for plural table names (convention varies)
        if grep -qE "tableName.*=.*['\"][A-Z]" "$f" 2>/dev/null; then
            echo "Check table name convention (usually lowercase plural): $f"
        fi
    done
    
    echo "$issues"
}
```

### 2. Query Pattern Validation

```bash
validate_query_patterns() {
    local dir="$1"
    local issues=""
    
    find "$dir" -name "*.ts" -o -name "*.js" -o -name "*.py" 2>/dev/null | while read f; do
        # Check for N+1 query patterns
        if grep -qE "\.find\(|\.findOne\(" "$f"; then
            # Look for loops around queries
            if grep -B5 "\.find\(" "$f" | grep -qE "for\s*\(|\.forEach\(|\.map\("; then
                echo "⚠️ Potential N+1 query pattern: $f"
            fi
        fi
        
        # Check for missing select/projection (fetching all fields)
        if grep -qE "\.find\(\s*\)" "$f" || grep -qE "\.findAll\(\s*\)" "$f"; then
            if ! grep -qE "select:|attributes:|projection:" "$f"; then
                echo "Query without field selection (consider selecting only needed fields): $f"
            fi
        fi
        
        # Check for raw queries without parameterization
        if grep -qE "query\(.*\\\$\{|execute\(.*\\\$\{" "$f"; then
            echo "🚨 Possible SQL injection risk (use parameterized queries): $f"
        fi
    done
    
    echo "$issues"
}
```

### 3. Migration Pattern Validation

```bash
validate_migration_patterns() {
    local dir="$1"
    local issues=""
    
    # Find migration files
    MIGRATION_DIRS=$(find "$dir" -type d -name "migrations" -o -name "migrate" 2>/dev/null)
    
    for mig_dir in $MIGRATION_DIRS; do
        # Check migration naming convention (timestamp prefix)
        find "$mig_dir" -name "*.ts" -o -name "*.js" -o -name "*.sql" 2>/dev/null | while read f; do
            FILENAME=$(basename "$f")
            
            # Should start with timestamp or version number
            if ! echo "$FILENAME" | grep -qE "^[0-9]{8,}|^V[0-9]+|^[0-9]+_"; then
                echo "Migration missing timestamp/version prefix: $f"
            fi
        done
        
        # Check for down/rollback migrations
        MIGRATIONS=$(find "$mig_dir" -name "*.ts" -o -name "*.js" 2>/dev/null)
        for m in $MIGRATIONS; do
            if grep -q "async up\|def up\|exports.up" "$m"; then
                if ! grep -q "async down\|def down\|exports.down" "$m"; then
                    echo "Migration missing down/rollback method: $m"
                fi
            fi
        done
    done
    
    echo "$issues"
}
```

### 4. Data Validation Patterns

```bash
validate_data_validation() {
    local dir="$1"
    local issues=""
    
    find "$dir" -name "*.ts" -o -name "*.js" 2>/dev/null | while read f; do
        # Check for validation on models/schemas
        if grep -qE "class.*Model|Schema\(|@Entity" "$f"; then
            # Should have some form of validation
            if ! grep -qE "@IsString|@IsNumber|validate|validator|required:|type:" "$f"; then
                echo "Model/schema without visible validation: $f"
            fi
        fi
        
        # Check for sanitization on user input
        if grep -qE "req\.body|req\.params|req\.query" "$f"; then
            if ! grep -qE "sanitize|escape|trim|validate" "$f"; then
                echo "⚠️ User input without visible sanitization: $f"
            fi
        fi
    done
    
    echo "$issues"
}
```

### 5. Index and Performance Patterns

```bash
validate_performance_patterns() {
    local dir="$1"
    local issues=""
    
    find "$dir" -name "*.ts" -o -name "*.js" 2>/dev/null | while read f; do
        # Check for indexed fields on frequently queried columns
        if grep -qE "\.find\(\s*\{.*:" "$f"; then
            # Extract query fields
            QUERY_FIELDS=$(grep -oE "\.find\(\s*\{[^}]+" "$f" | grep -oE "[a-zA-Z_]+:" | tr -d ':')
            
            # Check if these fields are indexed in schema
            for field in $QUERY_FIELDS; do
                if ! grep -qE "index.*$field|$field.*index" "$f"; then
                    echo "ℹ️ Frequently queried field may need index: $field in $f"
                fi
            done
        fi
        
        # Check for pagination on list queries
        if grep -qE "\.find\(\s*\{\s*\}\s*\)" "$f"; then
            if ! grep -qE "limit|skip|offset|page" "$f"; then
                echo "⚠️ Query without pagination (could return too many results): $f"
            fi
        fi
    done
    
    echo "$issues"
}
```

## Main Validation Orchestration

```bash
run_data_validation() {
    local target_dir="${1:-.}"
    local results_file="agent-os/output/validation/data-validation-results.md"
    
    mkdir -p agent-os/output/validation
    
    echo "# Data Pattern Validation Results" > "$results_file"
    echo "" >> "$results_file"
    echo "**Timestamp:** $(date)" >> "$results_file"
    echo "**Target:** $target_dir" >> "$results_file"
    echo "" >> "$results_file"
    
    # Run all data validations
    echo "## Model Naming" >> "$results_file"
    NAMING_ISSUES=$(validate_model_naming "$target_dir")
    if [ -n "$NAMING_ISSUES" ]; then
        echo "$NAMING_ISSUES" >> "$results_file"
        VALIDATION_PASSED=false
    else
        echo "✅ Model naming follows conventions" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Query Patterns" >> "$results_file"
    QUERY_ISSUES=$(validate_query_patterns "$target_dir")
    if [ -n "$QUERY_ISSUES" ]; then
        echo "$QUERY_ISSUES" >> "$results_file"
    else
        echo "✅ Query patterns look good" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Migration Patterns" >> "$results_file"
    MIGRATION_ISSUES=$(validate_migration_patterns "$target_dir")
    if [ -n "$MIGRATION_ISSUES" ]; then
        echo "$MIGRATION_ISSUES" >> "$results_file"
        VALIDATION_PASSED=false
    else
        echo "✅ Migrations follow conventions" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Data Validation" >> "$results_file"
    VALIDATION_ISSUES=$(validate_data_validation "$target_dir")
    if [ -n "$VALIDATION_ISSUES" ]; then
        echo "$VALIDATION_ISSUES" >> "$results_file"
    else
        echo "✅ Data validation in place" >> "$results_file"
    fi
    
    echo "" >> "$results_file"
    echo "## Performance Patterns" >> "$results_file"
    PERF_ISSUES=$(validate_performance_patterns "$target_dir")
    if [ -n "$PERF_ISSUES" ]; then
        echo "$PERF_ISSUES" >> "$results_file"
        echo "ℹ️ Performance suggestions (review recommended)" >> "$results_file"
    else
        echo "✅ Performance patterns look good" >> "$results_file"
    fi
    
    # Summary
    echo "" >> "$results_file"
    echo "---" >> "$results_file"
    if [ "$VALIDATION_PASSED" = true ]; then
        echo "## ✅ Data Validation Passed" >> "$results_file"
        exit 0
    else
        echo "## ❌ Data Validation Failed" >> "$results_file"
        echo "See issues above for details." >> "$results_file"
        exit 1
    fi
}

# Run validation
run_data_validation "$TARGET_DIR"
```

## Integration with Layer Specialists

When `data-specialist` completes implementation, this validation runs automatically. The validation loads patterns from:
- `agent-os/basepoints/modules/` (data-related modules)
- `agent-os/product/tech-stack.md` (ORM/database framework rules)
- `agent-os/standards/global/conventions.md`

        ((LAYER_VALIDATIONS_RUN++))
    fi
    
    echo "   ✅ Layer validations complete: $LAYER_VALIDATIONS_RUN layer(s) validated"
else
    echo "6️⃣ Skipping layer-specific validations (no specialists/layers detected)"
    echo "   ℹ️ Run /create-basepoints and /deploy-agents to enable layer validation"
fi
```

### Step 3: Load All Validation Results

Load results from all validation utilities:

```bash
# Load placeholder detection results
if [ -f "$CACHE_PATH/placeholder-detection.json" ]; then
    PLACEHOLDER_RESULTS=$(cat "$CACHE_PATH/placeholder-detection.json")
    PLACEHOLDER_TOTAL=$(echo "$PLACEHOLDER_RESULTS" | grep -o '"total_placeholders_found":[0-9]*' | cut -d: -f2)
else
    PLACEHOLDER_TOTAL=0
fi

# Load unnecessary logic detection results
if [ -f "$CACHE_PATH/unnecessary-logic-detection.json" ]; then
    UNNECESSARY_LOGIC_RESULTS=$(cat "$CACHE_PATH/unnecessary-logic-detection.json")
    UNNECESSARY_LOGIC_TOTAL=$(echo "$UNNECESSARY_LOGIC_RESULTS" | grep -o '"total_issues_found":[0-9]*' | cut -d: -f2)
else
    UNNECESSARY_LOGIC_TOTAL=0
fi

# Load technology-agnostic validation results
if [ -f "$CACHE_PATH/technology-agnostic-validation.json" ]; then
    TECH_VALIDATION_RESULTS=$(cat "$CACHE_PATH/technology-agnostic-validation.json")
    TECH_VALIDATION_TOTAL=$(echo "$TECH_VALIDATION_RESULTS" | grep -o '"total_issues_found":[0-9]*' | cut -d: -f2)
else
    TECH_VALIDATION_TOTAL=0
fi

# Load command cycle validation results
if [ -f "$CACHE_PATH/command-cycle-validation.json" ]; then
    CYCLE_VALIDATION_RESULTS=$(cat "$CACHE_PATH/command-cycle-validation.json")
    CYCLE_VALIDATION_TOTAL=$(echo "$CYCLE_VALIDATION_RESULTS" | grep -o '"total_issues_found":[0-9]*' | cut -d: -f2)
else
    CYCLE_VALIDATION_TOTAL=0
fi

# Load project structure alignment validation results
if [ -f "$CACHE_PATH/project-structure-alignment-validation.json" ]; then
    STRUCTURE_VALIDATION_RESULTS=$(cat "$CACHE_PATH/project-structure-alignment-validation.json")
    STRUCTURE_VALIDATION_TOTAL=$(echo "$STRUCTURE_VALIDATION_RESULTS" | grep -o '"total_issues_found":[0-9]*' | cut -d: -f2)
else
    STRUCTURE_VALIDATION_TOTAL=0
fi

# Load layer validation results
UI_VALIDATION_TOTAL=0
API_VALIDATION_TOTAL=0
DATA_VALIDATION_TOTAL=0

if [ -f "agent-os/output/validation/ui-validation-results.md" ]; then
    UI_VALIDATION_TOTAL=$(grep -c "❌\|⚠️" agent-os/output/validation/ui-validation-results.md 2>/dev/null || echo 0)
fi

if [ -f "agent-os/output/validation/api-validation-results.md" ]; then
    API_VALIDATION_TOTAL=$(grep -c "❌\|⚠️\|🚨" agent-os/output/validation/api-validation-results.md 2>/dev/null || echo 0)
fi

if [ -f "agent-os/output/validation/data-validation-results.md" ]; then
    DATA_VALIDATION_TOTAL=$(grep -c "❌\|⚠️\|🚨" agent-os/output/validation/data-validation-results.md 2>/dev/null || echo 0)
fi

LAYER_VALIDATION_TOTAL=$((UI_VALIDATION_TOTAL + API_VALIDATION_TOTAL + DATA_VALIDATION_TOTAL))

# Calculate total issues
TOTAL_ISSUES=$((PLACEHOLDER_TOTAL + UNNECESSARY_LOGIC_TOTAL + TECH_VALIDATION_TOTAL + CYCLE_VALIDATION_TOTAL + STRUCTURE_VALIDATION_TOTAL + LAYER_VALIDATION_TOTAL))
```

### Step 4: Generate Comprehensive Validation Report

Generate comprehensive report combining all validation results:

```bash
# Create comprehensive JSON report
cat > "$CACHE_PATH/comprehensive-validation-report.json" << EOF
{
  "validation_context": "$VALIDATION_CONTEXT",
  "scan_path": "$SCAN_PATH",
  "validation_timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "total_issues_found": $TOTAL_ISSUES,
  "validation_categories": {
    "placeholder_detection": {
      "total": $PLACEHOLDER_TOTAL,
      "report_file": "$CACHE_PATH/placeholder-detection.json"
    },
    "unnecessary_logic_detection": {
      "total": $UNNECESSARY_LOGIC_TOTAL,
      "report_file": "$CACHE_PATH/unnecessary-logic-detection.json"
    },
    "technology_agnostic_validation": {
      "total": $TECH_VALIDATION_TOTAL,
      "report_file": "$CACHE_PATH/technology-agnostic-validation.json"
    },
    "command_cycle_validation": {
      "total": $CYCLE_VALIDATION_TOTAL,
      "report_file": "$CACHE_PATH/command-cycle-validation.json"
    },
    "project_structure_alignment_validation": {
      "total": $STRUCTURE_VALIDATION_TOTAL,
      "report_file": "$CACHE_PATH/project-structure-alignment-validation.json"
    }
  },
  "summary": {
    "placeholder_issues": $PLACEHOLDER_TOTAL,
    "unnecessary_logic_issues": $UNNECESSARY_LOGIC_TOTAL,
    "technology_issues": $TECH_VALIDATION_TOTAL,
    "cycle_issues": $CYCLE_VALIDATION_TOTAL,
    "structure_issues": $STRUCTURE_VALIDATION_TOTAL,
    "layer_validation_issues": $LAYER_VALIDATION_TOTAL,
    "total_issues": $TOTAL_ISSUES
  },
  "layer_validations": {
    "ui_layer": {
      "total": $UI_VALIDATION_TOTAL,
      "report_file": "agent-os/output/validation/ui-validation-results.md"
    },
    "api_layer": {
      "total": $API_VALIDATION_TOTAL,
      "report_file": "agent-os/output/validation/api-validation-results.md"
    },
    "data_layer": {
      "total": $DATA_VALIDATION_TOTAL,
      "report_file": "agent-os/output/validation/data-validation-results.md"
    }
  }
}
EOF

# Create comprehensive markdown summary
cat > "$CACHE_PATH/comprehensive-validation-summary.md" << EOF
# Comprehensive Validation Report

**Validation Context:** $VALIDATION_CONTEXT  
**Scan Path:** $SCAN_PATH  
**Validation Timestamp:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Executive Summary

- **Total Issues Found:** $TOTAL_ISSUES
- **Placeholder Issues:** $PLACEHOLDER_TOTAL
- **Unnecessary Logic Issues:** $UNNECESSARY_LOGIC_TOTAL
- **Technology Issues:** $TECH_VALIDATION_TOTAL
- **Command Cycle Issues:** $CYCLE_VALIDATION_TOTAL
- **Project Structure Issues:** $STRUCTURE_VALIDATION_TOTAL
- **Layer Validation Issues:** $LAYER_VALIDATION_TOTAL

## Validation Categories

### 1. Placeholder Detection
- **Total Placeholders Found:** $PLACEHOLDER_TOTAL
- **Report:** See \`placeholder-detection-summary.md\` for details

### 2. Unnecessary Logic Detection
- **Total Issues Found:** $UNNECESSARY_LOGIC_TOTAL
- **Report:** See \`unnecessary-logic-detection-summary.md\` for details

### 3. Technology-Agnostic Validation
- **Total Issues Found:** $TECH_VALIDATION_TOTAL
- **Report:** See \`technology-agnostic-validation-summary.md\` for details

### 4. Command Cycle Validation
- **Total Issues Found:** $CYCLE_VALIDATION_TOTAL
- **Report:** See \`command-cycle-validation-summary.md\` for details

### 5. Project Structure Alignment Validation
- **Total Issues Found:** $STRUCTURE_VALIDATION_TOTAL
- **Report:** See \`project-structure-alignment-validation-summary.md\` for details

### 6. Layer-Specific Validations
- **UI Layer Issues:** $UI_VALIDATION_TOTAL
- **API Layer Issues:** $API_VALIDATION_TOTAL
- **Data Layer Issues:** $DATA_VALIDATION_TOTAL
- **Reports:** See \`agent-os/output/validation/\` for layer reports

## Detailed Reports

All detailed reports are available in:
- \`$CACHE_PATH/\`

## Recommendations

$(if [ "$TOTAL_ISSUES" -gt 0 ]; then
    echo "⚠️  **Action Required:** Issues found during validation. Review detailed reports and address critical issues."
    echo ""
    if [ "$PLACEHOLDER_TOTAL" -gt 0 ]; then
        echo "- **Placeholders:** $PLACEHOLDER_TOTAL placeholder(s) need to be replaced with project-specific content"
    fi
    if [ "$UNNECESSARY_LOGIC_TOTAL" -gt 0 ]; then
        echo "- **Unnecessary Logic:** $UNNECESSARY_LOGIC_TOTAL issue(s) need to be removed or replaced"
    fi
    if [ "$CYCLE_VALIDATION_TOTAL" -gt 0 ]; then
        echo "- **Command Cycle:** $CYCLE_VALIDATION_TOTAL issue(s) in command flow need to be fixed"
    fi
else
    echo "✅ **No Issues Found:** All validation checks passed!"
fi)
EOF

echo "✅ Comprehensive validation complete!"
echo "📊 Total issues found: $TOTAL_ISSUES"
echo "📁 Reports stored in: $CACHE_PATH/"
```

## Important Constraints

- Must combine all validation utilities into single workflow
- Must generate comprehensive validation reports with categorized findings
- Must create validation summary with counts of issues per category
- Must support validation of both profiles/default (template) and installed agent-os (specialized)
- Must store validation results in spec's implementation/cache/
- **CRITICAL**: All reports must be stored in `agent-os/specs/[current-spec]/implementation/cache/validation/` when running within a spec command, not scattered around the codebase
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents


# Load validation results
if [ -f "$VALIDATION_CACHE/comprehensive-validation-report.json" ]; then
    VALIDATION_REPORT=$(cat "$VALIDATION_CACHE/comprehensive-validation-report.json")
    
    # Extract issue counts
    PLACEHOLDER_TOTAL=$(echo "$VALIDATION_REPORT" | grep -oE '"placeholder_detection":\{"total":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
    UNNECESSARY_LOGIC_TOTAL=$(echo "$VALIDATION_REPORT" | grep -oE '"unnecessary_logic_detection":\{"total":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
    CYCLE_TOTAL=$(echo "$VALIDATION_REPORT" | grep -oE '"command_cycle_validation":\{"total":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
    STRUCTURE_TOTAL=$(echo "$VALIDATION_REPORT" | grep -oE '"project_structure_alignment_validation":\{"total":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
    
    echo "📊 Validation Results:"
    echo "  - Placeholders: $PLACEHOLDER_TOTAL"
    echo "  - Unnecessary Logic: $UNNECESSARY_LOGIC_TOTAL"
    echo "  - Command Cycle Issues: $CYCLE_TOTAL"
    echo "  - Structure Alignment Issues: $STRUCTURE_TOTAL"
    
    # Store issue counts for later phases
    echo "$PLACEHOLDER_TOTAL" > "$VALIDATION_CACHE/placeholder-count.txt"
    echo "$UNNECESSARY_LOGIC_TOTAL" > "$VALIDATION_CACHE/unnecessary-logic-count.txt"
    echo "$CYCLE_TOTAL" > "$VALIDATION_CACHE/cycle-count.txt"
    echo "$STRUCTURE_TOTAL" > "$VALIDATION_CACHE/structure-count.txt"
else
    echo "❌ Validation failed - no results found"
    exit 1
fi
```

### Step 4: Determine Cleanup Scope

Identify which issues need to be fixed:

```bash
# Load issue counts
PLACEHOLDER_COUNT=$(cat "$VALIDATION_CACHE/placeholder-count.txt" 2>/dev/null || echo "0")
UNNECESSARY_LOGIC_COUNT=$(cat "$VALIDATION_CACHE/unnecessary-logic-count.txt" 2>/dev/null || echo "0")
CYCLE_COUNT=$(cat "$VALIDATION_CACHE/cycle-count.txt" 2>/dev/null || echo "0")
STRUCTURE_COUNT=$(cat "$VALIDATION_CACHE/structure-count.txt" 2>/dev/null || echo "0")

TOTAL_ISSUES=$((PLACEHOLDER_COUNT + UNNECESSARY_LOGIC_COUNT + CYCLE_COUNT + STRUCTURE_COUNT))

if [ "$TOTAL_ISSUES" -eq 0 ]; then
    echo "✅ No issues found! agent-os is already clean."
    echo "You can exit the cleanup command now."
    exit 0
fi

echo "📋 Cleanup Scope:"
echo "  - Placeholders to clean: $PLACEHOLDER_COUNT"
echo "  - Unnecessary logic to remove: $UNNECESSARY_LOGIC_COUNT"
echo "  - Broken references to fix: $CYCLE_COUNT"
echo "  - Structure alignment issues: $STRUCTURE_COUNT"
echo ""
echo "Total issues to fix: $TOTAL_ISSUES"
```

## Important Constraints

- Must verify agent-os is deployed before proceeding
- Must run comprehensive validation to identify all issues
- Must support dry-run mode for previewing changes
- Must determine cleanup scope based on validation results
- **CRITICAL**: All validation reports must be stored in `agent-os/.cleanup-cache/validation/` (temporary, cleaned up after cleanup completes)
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents

# PHASE 2: Clean Placeholders

Now that validation is complete, proceed with cleaning remaining placeholders by following these instructions:

## Core Responsibilities

1. **Load Placeholder Detection Results**: Load placeholder validation results
2. **Replace Placeholders**: Replace placeholders with project-specific content using deploy-agents logic
3. **Support Dry-Run Mode**: Preview changes before applying
4. **Track Changes**: Track what was fixed for cleanup report

## Workflow

### Step 1: Load Placeholder Detection Results

Load placeholder validation results:

```bash
# Load validation cache
VALIDATION_CACHE="agent-os/.cleanup-cache/validation"
CLEANUP_CACHE="agent-os/.cleanup-cache"
DRY_RUN="${DRY_RUN:-false}"

# Load placeholder detection results
if [ -f "$VALIDATION_CACHE/placeholder-cleaning-validation.json" ]; then
    PLACEHOLDER_DATA=$(cat "$VALIDATION_CACHE/placeholder-cleaning-validation.json")
    PLACEHOLDER_TOTAL=$(echo "$PLACEHOLDER_DATA" | grep -o '"total_placeholders_found":[0-9]*' | cut -d: -f2)
    
    if [ "$PLACEHOLDER_TOTAL" -eq 0 ]; then
        echo "✅ No placeholders to clean"
        echo "0" > "$CLEANUP_CACHE/placeholders-cleaned.txt"
        exit 0
    fi
    
    echo "🔧 Found $PLACEHOLDER_TOTAL placeholder(s) to clean"
else
    echo "❌ Placeholder validation results not found"
    exit 1
fi
```

### Step 2: Load Project-Specific Knowledge

Load project-specific knowledge needed for placeholder replacement:

```bash
# Load basepoints knowledge if available
if [ "$BASEPOINTS_AVAILABLE" = "true" ] && [ -f "agent-os/basepoints/headquarter.md" ]; then
    # Extract basepoints path pattern
    BASEPOINT_FILE_PATTERN=$(find agent-os/basepoints -name "agent-base-*.md" -type f | head -1 | xargs basename | sed 's/agent-base-//' | sed 's/\.md//')
    if [ -z "$BASEPOINT_FILE_PATTERN" ]; then
        BASEPOINT_FILE_PATTERN="agent-base-*.md"
    fi
    
    # Extract abstraction layers
    ABSTRACTION_LAYERS=$(find agent-os/basepoints -type d -mindepth 1 -maxdepth 2 | sed 's|agent-os/basepoints/||' | cut -d'/' -f1 | sort -u | tr '\n' ',' | sed 's/,$//')
    
    echo "✅ Loaded basepoints knowledge"
else
    echo "⚠️  Warning: Basepoints not available - using generic replacements"
    BASEPOINT_FILE_PATTERN="agent-base-*.md"
    ABSTRACTION_LAYERS=""
fi

# Load product knowledge if available
if [ "$PRODUCT_AVAILABLE" = "true" ] && [ -f "agent-os/product/tech-stack.md" ]; then
    TECH_STACK=$(cat agent-os/product/tech-stack.md)
    echo "✅ Loaded product knowledge"
else
    TECH_STACK=""
fi
```

### Step 3: Replace Placeholders

Replace placeholders with project-specific content:

```bash
# Initialize cleanup tracking
PLACEHOLDERS_CLEANED=0
FILES_MODIFIED=0

# Extract placeholder locations from validation results
echo "$PLACEHOLDER_DATA" | grep -oE '"file": "[^"]+"' | cut -d'"' -f4 | sort -u > "$CLEANUP_CACHE/files-with-placeholders.txt"

# Process each file with placeholders
while read file_path; do
    if [ -z "$file_path" ] || [ ! -f "$file_path" ]; then
        continue
    fi
    
    FILE_CONTENT=$(cat "$file_path")
    ORIGINAL_CONTENT="$FILE_CONTENT"
    MODIFIED=false
    
    # Replace common basepoints placeholders
    if echo "$FILE_CONTENT" | grep -q "{{BASEPOINTS_PATH}}"; then
        FILE_CONTENT=$(echo "$FILE_CONTENT" | sed "s|{{BASEPOINTS_PATH}}|agent-os/basepoints|g")
        MODIFIED=true
        PLACEHOLDERS_CLEANED=$((PLACEHOLDERS_CLEANED + 1))
    fi
    
    if echo "$FILE_CONTENT" | grep -q "{{BASEPOINT_FILE_PATTERN}}"; then
        FILE_CONTENT=$(echo "$FILE_CONTENT" | sed "s|{{BASEPOINT_FILE_PATTERN}}|$BASEPOINT_FILE_PATTERN|g")
        MODIFIED=true
        PLACEHOLDERS_CLEANED=$((PLACEHOLDERS_CLEANED + 1))
    fi
    
    if echo "$FILE_CONTENT" | grep -q "{{ABSTRACTION_LAYER_NAMES}}"; then
        FILE_CONTENT=$(echo "$FILE_CONTENT" | sed "s|{{ABSTRACTION_LAYER_NAMES}}|$ABSTRACTION_LAYERS|g")
        MODIFIED=true
        PLACEHOLDERS_CLEANED=$((PLACEHOLDERS_CLEANED + 1))
    fi
    
    # Replace scope detection placeholders with project-specific patterns
    # (These would need more sophisticated replacement based on actual project structure)
    if echo "$FILE_CONTENT" | grep -qE "{{KEYWORD_EXTRACTION_PATTERN}}|{{SEMANTIC_ANALYSIS_PATTERN}}"; then
        # Use project-specific patterns from basepoints if available
        FILE_CONTENT=$(echo "$FILE_CONTENT" | sed "s|{{KEYWORD_EXTRACTION_PATTERN}}|project-specific-keyword-pattern|g")
        FILE_CONTENT=$(echo "$FILE_CONTENT" | sed "s|{{SEMANTIC_ANALYSIS_PATTERN}}|project-specific-semantic-pattern|g")
        MODIFIED=true
        PLACEHOLDERS_CLEANED=$((PLACEHOLDERS_CLEANED + 2))
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
done < "$CLEANUP_CACHE/files-with-placeholders.txt"

# Store cleanup results
echo "$PLACEHOLDERS_CLEANED" > "$CLEANUP_CACHE/placeholders-cleaned.txt"
echo "$FILES_MODIFIED" > "$CLEANUP_CACHE/files-modified-placeholders.txt"

if [ "$DRY_RUN" = "true" ]; then
    echo "🔍 [DRY-RUN] Would clean $PLACEHOLDERS_CLEANED placeholder(s) in files"
else
    echo "✅ Cleaned $PLACEHOLDERS_CLEANED placeholder(s) in $FILES_MODIFIED file(s)"
fi
```

## Important Constraints

- Must use placeholder detection results from validation phase
- Must replace placeholders with project-specific content using deploy-agents logic
- Must support dry-run mode to preview changes
- Must track what was fixed for cleanup report
- **CRITICAL**: All cleanup cache files must be stored in `agent-os/.cleanup-cache/` (temporary, cleaned up after cleanup completes)
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents

# PHASE 3: Remove Unnecessary Logic

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

# PHASE 4: Fix Broken References

Now that unnecessary logic is removed, proceed with fixing broken references by following these instructions:

## Core Responsibilities

1. **Load Command Cycle Validation Results**: Load command cycle validation results
2. **Fix Broken Command Cycle References**: Fix broken references between commands
3. **Align Commands with Project Structure**: Update commands to align with current project structure
4. **Support Dry-Run Mode**: Preview changes before applying
5. **Track Changes**: Track what was fixed for cleanup report

## Workflow

### Step 1: Load Command Cycle Validation Results

Load command cycle validation results:

```bash
# Load validation cache
VALIDATION_CACHE="agent-os/.cleanup-cache/validation"
CLEANUP_CACHE="agent-os/.cleanup-cache"
DRY_RUN="${DRY_RUN:-false}"

# Load command cycle validation results
if [ -f "$VALIDATION_CACHE/command-cycle-validation.json" ]; then
    CYCLE_DATA=$(cat "$VALIDATION_CACHE/command-cycle-validation.json")
    CYCLE_TOTAL=$(echo "$CYCLE_DATA" | grep -o '"total_issues_found":[0-9]*' | cut -d: -f2)
    
    if [ "$CYCLE_TOTAL" -eq 0 ]; then
        echo "✅ No broken references to fix"
        echo "0" > "$CLEANUP_CACHE/broken-references-fixed.txt"
        exit 0
    fi
    
    echo "🔧 Found $CYCLE_TOTAL broken reference(s) to fix"
else
    echo "❌ Command cycle validation results not found"
    exit 1
fi
```

### Step 2: Fix Broken Command Cycle References

Fix broken references between commands:

```bash
# Initialize cleanup tracking
BROKEN_REFERENCES_FIXED=0
FILES_MODIFIED=0

# Fix shape-spec → write-spec flow
SHAPE_SPEC_FILE="agent-os/commands/shape-spec/single-agent/shape-spec.md"
WRITE_SPEC_FILE="agent-os/commands/write-spec/single-agent/write-spec.md"

if [ -f "$WRITE_SPEC_FILE" ]; then
    WRITE_SPEC_CONTENT=$(cat "$WRITE_SPEC_FILE")
    ORIGINAL_CONTENT="$WRITE_SPEC_CONTENT"
    
    # Ensure write-spec references shape-spec outputs
    if ! echo "$WRITE_SPEC_CONTENT" | grep -qE "spec\.md|requirements\.md|planning/"; then
        # Add reference to spec.md (simplified - in practice would need more sophisticated insertion)
        WRITE_SPEC_CONTENT="${WRITE_SPEC_CONTENT}\n\n# References shape-spec outputs: spec.md, requirements.md"
        MODIFIED=true
        BROKEN_REFERENCES_FIXED=$((BROKEN_REFERENCES_FIXED + 1))
    fi
    
    if [ "$MODIFIED" = "true" ] && [ "$WRITE_SPEC_CONTENT" != "$ORIGINAL_CONTENT" ]; then
        if [ "$DRY_RUN" = "true" ]; then
            echo "🔍 [DRY-RUN] Would fix write-spec → shape-spec reference"
            mkdir -p "$(dirname "$CLEANUP_CACHE/dry-run-preview/$WRITE_SPEC_FILE")"
            echo "$WRITE_SPEC_CONTENT" > "$CLEANUP_CACHE/dry-run-preview/$WRITE_SPEC_FILE"
        else
            echo "$WRITE_SPEC_CONTENT" > "$WRITE_SPEC_FILE"
            echo "✅ Fixed write-spec → shape-spec reference"
            FILES_MODIFIED=$((FILES_MODIFIED + 1))
        fi
    fi
fi

# Fix write-spec → create-tasks flow
CREATE_TASKS_FILE="agent-os/commands/create-tasks/single-agent/create-tasks.md"

if [ -f "$CREATE_TASKS_FILE" ]; then
    CREATE_TASKS_CONTENT=$(cat "$CREATE_TASKS_FILE")
    ORIGINAL_CONTENT="$CREATE_TASKS_CONTENT"
    MODIFIED=false
    
    # Ensure create-tasks references write-spec outputs
    if ! echo "$CREATE_TASKS_CONTENT" | grep -qE "spec\.md|requirements\.md"; then
        CREATE_TASKS_CONTENT="${CREATE_TASKS_CONTENT}\n\n# References write-spec outputs: spec.md, requirements.md"
        MODIFIED=true
        BROKEN_REFERENCES_FIXED=$((BROKEN_REFERENCES_FIXED + 1))
    fi
    
    if [ "$MODIFIED" = "true" ] && [ "$CREATE_TASKS_CONTENT" != "$ORIGINAL_CONTENT" ]; then
        if [ "$DRY_RUN" = "true" ]; then
            echo "🔍 [DRY-RUN] Would fix create-tasks → write-spec reference"
            mkdir -p "$(dirname "$CLEANUP_CACHE/dry-run-preview/$CREATE_TASKS_FILE")"
            echo "$CREATE_TASKS_CONTENT" > "$CLEANUP_CACHE/dry-run-preview/$CREATE_TASKS_FILE"
        else
            echo "$CREATE_TASKS_CONTENT" > "$CREATE_TASKS_FILE"
            echo "✅ Fixed create-tasks → write-spec reference"
            FILES_MODIFIED=$((FILES_MODIFIED + 1))
        fi
    fi
fi

# Fix create-tasks → implement-tasks and orchestrate-tasks flows
IMPLEMENT_TASKS_FILE="agent-os/commands/implement-tasks/single-agent/implement-tasks.md"
ORCHESTRATE_TASKS_FILE="agent-os/commands/orchestrate-tasks/orchestrate-tasks.md"

for file_path in "$IMPLEMENT_TASKS_FILE" "$ORCHESTRATE_TASKS_FILE"; do
    if [ -f "$file_path" ]; then
        FILE_CONTENT=$(cat "$file_path")
        ORIGINAL_CONTENT="$FILE_CONTENT"
        MODIFIED=false
        
        # Ensure command references create-tasks outputs
        if ! echo "$FILE_CONTENT" | grep -qE "tasks\.md"; then
            FILE_CONTENT="${FILE_CONTENT}\n\n# References create-tasks outputs: tasks.md"
            MODIFIED=true
            BROKEN_REFERENCES_FIXED=$((BROKEN_REFERENCES_FIXED + 1))
        fi
        
        if [ "$MODIFIED" = "true" ] && [ "$FILE_CONTENT" != "$ORIGINAL_CONTENT" ]; then
            if [ "$DRY_RUN" = "true" ]; then
                echo "🔍 [DRY-RUN] Would fix reference in: $file_path"
                mkdir -p "$(dirname "$CLEANUP_CACHE/dry-run-preview/$file_path")"
                echo "$FILE_CONTENT" > "$CLEANUP_CACHE/dry-run-preview/$file_path"
            else
                echo "$FILE_CONTENT" > "$file_path"
                echo "✅ Fixed reference in: $file_path"
                FILES_MODIFIED=$((FILES_MODIFIED + 1))
            fi
        fi
    fi
done

# Store cleanup results
echo "$BROKEN_REFERENCES_FIXED" > "$CLEANUP_CACHE/broken-references-fixed.txt"
echo "$FILES_MODIFIED" > "$CLEANUP_CACHE/files-modified-references.txt"

if [ "$DRY_RUN" = "true" ]; then
    echo "🔍 [DRY-RUN] Would fix $BROKEN_REFERENCES_FIXED broken reference(s) in files"
else
    echo "✅ Fixed $BROKEN_REFERENCES_FIXED broken reference(s) in $FILES_MODIFIED file(s)"
fi
```

## Important Constraints

- Must use command cycle validation results from validation phase
- Must fix broken references between commands
- Must align commands with current project structure
- Must support dry-run mode to preview changes
- Must track what was fixed for cleanup report
- **CRITICAL**: All cleanup cache files must be stored in `agent-os/.cleanup-cache/` (temporary, cleaned up after cleanup completes)
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents

# PHASE 5: Verify Knowledge Completeness

Now that cleanup operations are complete, verify that sufficient knowledge has been extracted and no important information is missing by following these instructions:

## Core Responsibilities

1. **Verify Basepoints Completeness**: Check that all important modules and abstraction layers are covered
2. **Verify Product Knowledge Completeness**: Ensure all required product information is present and complete
3. **Detect Missing Information**: Identify gaps in extracted knowledge
4. **Coverage Analysis**: Verify knowledge covers the project structure adequately
5. **Generate Knowledge Gap Report**: Document any missing information or incomplete knowledge

## Workflow

### Step 1: Verify Basepoints Completeness

Check that basepoints cover all important aspects of the codebase:

```bash
# Create knowledge verification cache
CLEANUP_CACHE="agent-os/.cleanup-cache"
KNOWLEDGE_VERIFICATION_CACHE="$CLEANUP_CACHE/knowledge-verification"
mkdir -p "$KNOWLEDGE_VERIFICATION_CACHE"

echo "🔍 Verifying basepoints completeness..."

# Check basepoints structure
if [ ! -d "agent-os/basepoints" ]; then
    echo "❌ Basepoints directory not found"
    MISSING_BASEPOINTS=true
else
    echo "✅ Basepoints directory found"
    MISSING_BASEPOINTS=false
    
    # Count basepoint files
    BASEPOINT_FILES=$(find agent-os/basepoints -name "agent-base-*.md" -type f | wc -l | tr -d ' ')
    echo "  - Module basepoints found: $BASEPOINT_FILES"
    
    # Check for headquarter.md
    if [ -f "agent-os/basepoints/headquarter.md" ]; then
        echo "  ✅ Headquarter basepoint found"
        
        # Verify headquarter contains required sections
        REQUIRED_SECTIONS=("Pattern" "Standard" "Flow" "Strategy" "Architecture" "Abstraction" "Testing")
        MISSING_SECTIONS=()
        
        for section in "${REQUIRED_SECTIONS[@]}"; do
            if ! grep -qi "$section" agent-os/basepoints/headquarter.md 2>/dev/null; then
                MISSING_SECTIONS+=("$section")
            fi
        done
        
        if [ ${#MISSING_SECTIONS[@]} -gt 0 ]; then
            echo "  ⚠️  Warning: Headquarter missing sections: ${MISSING_SECTIONS[*]}"
        else
            echo "  ✅ Headquarter contains all required sections"
        fi
    else
        echo "  ❌ Headquarter basepoint missing"
        MISSING_BASEPOINTS=true
    fi
    
    # Analyze basepoint coverage
    if [ "$BASEPOINT_FILES" -lt 3 ]; then
        echo "  ⚠️  Warning: Only $BASEPOINT_FILES basepoint file(s) found. Consider if all important modules are covered."
    fi
    
    # Check for basepoints in different abstraction layers
    LAYER_COUNT=$(find agent-os/basepoints -type d -mindepth 1 -maxdepth 1 | wc -l | tr -d ' ')
    if [ "$LAYER_COUNT" -eq 0 ] && [ "$BASEPOINT_FILES" -gt 0 ]; then
        echo "  ⚠️  Warning: Basepoints may not be organized by abstraction layers"
    else
        echo "  ✅ Basepoints organized across $LAYER_COUNT abstraction layer(s)"
    fi
fi
```

### Step 2: Verify Product Knowledge Completeness

Check that product files contain all required information:

```bash
echo ""
echo "🔍 Verifying product knowledge completeness..."

if [ ! -d "agent-os/product" ]; then
    echo "❌ Product directory not found"
    MISSING_PRODUCT=true
else
    echo "✅ Product directory found"
    MISSING_PRODUCT=false
    
    # Check each required product file
    PRODUCT_FILES=("mission.md" "roadmap.md" "tech-stack.md")
    MISSING_PRODUCT_FILES=()
    INCOMPLETE_PRODUCT_FILES=()
    
    for file in "${PRODUCT_FILES[@]}"; do
        if [ ! -f "agent-os/product/$file" ]; then
            MISSING_PRODUCT_FILES+=("$file")
            echo "  ❌ Missing: agent-os/product/$file"
        else
            # Check if file has substantial content (more than just headers)
            CONTENT_LINES=$(grep -v '^#' agent-os/product/$file | grep -v '^$' | wc -l | tr -d ' ')
            if [ "$CONTENT_LINES" -lt 5 ]; then
                INCOMPLETE_PRODUCT_FILES+=("$file")
                echo "  ⚠️  Warning: agent-os/product/$file exists but may be incomplete ($CONTENT_LINES content lines)"
            else
                echo "  ✅ agent-os/product/$file (complete, $CONTENT_LINES content lines)"
            fi
        fi
    done
    
    # Verify mission.md contains key elements
    if [ -f "agent-os/product/mission.md" ]; then
        MISSION_KEYWORDS=("purpose" "vision" "goal" "objective" "target" "user")
        FOUND_KEYWORDS=0
        for keyword in "${MISSION_KEYWORDS[@]}"; do
            if grep -qi "$keyword" agent-os/product/mission.md 2>/dev/null; then
                ((FOUND_KEYWORDS++))
            fi
        done
        if [ $FOUND_KEYWORDS -lt 2 ]; then
            echo "  ⚠️  Warning: mission.md may be missing key elements (purpose, vision, goals, target users)"
        fi
    fi
    
    # Verify roadmap.md contains phases or features
    if [ -f "agent-os/product/roadmap.md" ]; then
        if ! grep -qiE "(phase|feature|milestone|roadmap|plan)" agent-os/product/roadmap.md 2>/dev/null; then
            echo "  ⚠️  Warning: roadmap.md may not contain development phases or features"
        fi
    fi
    
    # Verify tech-stack.md contains technology information
    if [ -f "agent-os/product/tech-stack.md" ]; then
        TECH_KEYWORDS=("language" "framework" "library" "database" "tool" "stack")
        FOUND_TECH=0
        for keyword in "${TECH_KEYWORDS[@]}"; do
            if grep -qi "$keyword" agent-os/product/tech-stack.md 2>/dev/null; then
                ((FOUND_TECH++))
            fi
        done
        if [ $FOUND_TECH -lt 2 ]; then
            echo "  ⚠️  Warning: tech-stack.md may be missing technology details"
        fi
    fi
fi
```

### Step 3: Detect Missing Information

Analyze the codebase structure to identify potential gaps:

```bash
echo ""
echo "🔍 Detecting missing information..."

# Analyze project structure to identify potential missing basepoints
if [ -d "agent-os/basepoints" ] && [ -d "." ]; then
    # Get project source directories (common patterns)
    SOURCE_DIRS=()
    [ -d "src" ] && SOURCE_DIRS+=("src")
    [ -d "lib" ] && SOURCE_DIRS+=("lib")
    [ -d "app" ] && SOURCE_DIRS+=("app")
    [ -d "components" ] && SOURCE_DIRS+=("components")
    [ -d "services" ] && SOURCE_DIRS+=("services")
    [ -d "models" ] && SOURCE_DIRS+=("models")
    [ -d "controllers" ] && SOURCE_DIRS+=("controllers")
    
    if [ ${#SOURCE_DIRS[@]} -gt 0 ]; then
        echo "  📁 Project structure analysis:"
        for dir in "${SOURCE_DIRS[@]}"; do
            # Check if there's a corresponding basepoint
            BASEPOINT_FOUND=false
            if find agent-os/basepoints -path "*$dir*" -name "agent-base-*.md" | grep -q .; then
                BASEPOINT_FOUND=true
            fi
            
            if [ "$BASEPOINT_FOUND" = false ]; then
                echo "    ⚠️  Warning: No basepoint found for $dir/ directory"
                echo "$dir" >> "$KNOWLEDGE_VERIFICATION_CACHE/potentially-missing-basepoints.txt"
            else
                echo "    ✅ Basepoint found for $dir/"
            fi
        done
    fi
fi

# Check for common patterns that should be documented
COMMON_PATTERNS=("api" "database" "auth" "test" "config" "util")
MISSING_PATTERNS=()

for pattern in "${COMMON_PATTERNS[@]}"; do
    # Check if pattern exists in codebase
    if find . -type f -name "*$pattern*" -not -path "./agent-os/*" -not -path "./.git/*" | head -1 | grep -q .; then
        # Check if pattern is documented in basepoints
        if ! find agent-os/basepoints -name "*.md" -exec grep -l -i "$pattern" {} \; | grep -q .; then
            MISSING_PATTERNS+=("$pattern")
        fi
    fi
done

if [ ${#MISSING_PATTERNS[@]} -gt 0 ]; then
    echo "  ⚠️  Warning: These patterns exist in codebase but may not be documented: ${MISSING_PATTERNS[*]}"
fi
```

### Step 4: Coverage Analysis

Verify knowledge covers the project structure adequately:

```bash
echo ""
echo "🔍 Analyzing knowledge coverage..."

# Calculate coverage metrics
TOTAL_BASEPOINTS=$BASEPOINT_FILES
if [ -f "agent-os/basepoints/headquarter.md" ]; then
    TOTAL_BASEPOINTS=$((TOTAL_BASEPOINTS + 1))
fi

# Estimate project complexity (rough heuristic)
PROJECT_FILES=$(find . -type f -name "*.js" -o -name "*.ts" -o -name "*.py" -o -name "*.rb" -o -name "*.java" -o -name "*.go" | grep -v node_modules | grep -v ".git" | grep -v "agent-os" | wc -l | tr -d ' ')

if [ "$PROJECT_FILES" -gt 0 ]; then
    COVERAGE_RATIO=$(echo "scale=2; $TOTAL_BASEPOINTS * 100 / ($PROJECT_FILES / 10 + 1)" | bc 2>/dev/null || echo "0")
    
    echo "  📊 Coverage Analysis:"
    echo "    - Project files: $PROJECT_FILES"
    echo "    - Basepoint files: $TOTAL_BASEPOINTS"
    
    if [ "$(echo "$COVERAGE_RATIO" | cut -d. -f1)" -lt 5 ]; then
        echo "    ⚠️  Warning: Basepoint coverage may be low for project size"
        echo "    💡 Consider running /create-basepoints again to ensure all modules are covered"
    else
        echo "    ✅ Basepoint coverage appears adequate"
    fi
fi
```

### Step 5: Generate Knowledge Gap Report

Document any missing information or incomplete knowledge:

```bash
echo ""
echo "📝 Generating knowledge gap report..."

# Create knowledge gap report
cat > "$KNOWLEDGE_VERIFICATION_CACHE/knowledge-gap-report.json" << EOF
{
  "verification_timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "basepoints": {
    "exists": $([ "$MISSING_BASEPOINTS" = "false" ] && echo "true" || echo "false"),
    "file_count": $BASEPOINT_FILES,
    "headquarter_exists": $([ -f "agent-os/basepoints/headquarter.md" ] && echo "true" || echo "false"),
    "missing_sections": $(if [ ${#MISSING_SECTIONS[@]} -gt 0 ]; then echo "\"${MISSING_SECTIONS[*]}\""; else echo "[]"; fi),
    "layer_count": $LAYER_COUNT
  },
  "product": {
    "exists": $([ "$MISSING_PRODUCT" = "false" ] && echo "true" || echo "false"),
    "missing_files": $(if [ ${#MISSING_PRODUCT_FILES[@]} -gt 0 ]; then echo "\"${MISSING_PRODUCT_FILES[*]}\""; else echo "[]"; fi),
    "incomplete_files": $(if [ ${#INCOMPLETE_PRODUCT_FILES[@]} -gt 0 ]; then echo "\"${INCOMPLETE_PRODUCT_FILES[*]}\""; else echo "[]"; fi)
  },
  "coverage": {
    "total_basepoints": $TOTAL_BASEPOINTS,
    "project_files": $PROJECT_FILES,
    "coverage_ratio": "$COVERAGE_RATIO"
  },
  "recommendations": []
}
EOF

# Generate markdown summary
cat > "$KNOWLEDGE_VERIFICATION_CACHE/knowledge-gap-report.md" << EOF
# Knowledge Completeness Verification Report

**Verification Timestamp:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Summary

$(if [ "$MISSING_BASEPOINTS" = "true" ] || [ "$MISSING_PRODUCT" = "true" ]; then
    echo "❌ **CRITICAL**: Missing required knowledge files"
else
    echo "✅ All required knowledge files present"
fi)

## Basepoints Analysis

- **Basepoints Directory:** $([ "$MISSING_BASEPOINTS" = "false" ] && echo "✅ Found" || echo "❌ Missing")
- **Headquarter Basepoint:** $([ -f "agent-os/basepoints/headquarter.md" ] && echo "✅ Found" || echo "❌ Missing")
- **Module Basepoints:** $BASEPOINT_FILES file(s) found
- **Abstraction Layers:** $LAYER_COUNT layer(s) covered
$(if [ ${#MISSING_SECTIONS[@]} -gt 0 ]; then
    echo "- **Missing Sections:** ⚠️ ${MISSING_SECTIONS[*]}"
fi)

## Product Knowledge Analysis

- **Product Directory:** $([ "$MISSING_PRODUCT" = "false" ] && echo "✅ Found" || echo "❌ Missing")
$(if [ ${#MISSING_PRODUCT_FILES[@]} -gt 0 ]; then
    echo "- **Missing Files:** ❌ ${MISSING_PRODUCT_FILES[*]}"
fi)
$(if [ ${#INCOMPLETE_PRODUCT_FILES[@]} -gt 0 ]; then
    echo "- **Incomplete Files:** ⚠️ ${INCOMPLETE_PRODUCT_FILES[*]}"
fi)

## Coverage Analysis

- **Total Basepoints:** $TOTAL_BASEPOINTS
- **Project Files:** $PROJECT_FILES
- **Coverage Ratio:** $COVERAGE_RATIO

## Recommendations

$(if [ "$MISSING_BASEPOINTS" = "true" ]; then
    echo "1. ❌ **CRITICAL**: Run \`/create-basepoints\` to generate basepoint documentation"
fi)
$(if [ "$MISSING_PRODUCT" = "true" ]; then
    echo "2. ❌ **CRITICAL**: Run \`/plan-product\` or \`/adapt-to-product\` to create product documentation"
fi)
$(if [ ${#MISSING_SECTIONS[@]} -gt 0 ]; then
    echo "3. ⚠️  Consider updating headquarter.md to include: ${MISSING_SECTIONS[*]}"
fi)
$(if [ "$BASEPOINT_FILES" -lt 3 ]; then
    echo "4. ⚠️  Consider running \`/create-basepoints\` again to ensure all important modules are covered"
fi)
$(if [ "$(echo "$COVERAGE_RATIO" | cut -d. -f1)" -lt 5 ]; then
    echo "5. ⚠️  Basepoint coverage may be low. Review project structure and ensure all abstraction layers are documented"
fi)

## Next Steps

$(if [ "$MISSING_BASEPOINTS" = "true" ] || [ "$MISSING_PRODUCT" = "true" ]; then
    echo "⚠️  **Action Required**: Missing knowledge files detected. Please create missing prerequisites before using specialized commands."
    echo ""
    echo "To fix:"
    [ "$MISSING_BASEPOINTS" = "true" ] && echo "  - Run \`/create-basepoints\`"
    [ "$MISSING_PRODUCT" = "true" ] && echo "  - Run \`/plan-product\` or \`/adapt-to-product\`"
else
    echo "✅ Knowledge extraction appears complete. You can proceed with using specialized commands."
fi)
EOF

# Display summary
cat "$KNOWLEDGE_VERIFICATION_CACHE/knowledge-gap-report.md"
```

### Step 6: Store Verification Results

Store verification results for the cleanup report:

```bash
# Store verification status
if [ "$MISSING_BASEPOINTS" = "true" ] || [ "$MISSING_PRODUCT" = "true" ]; then
    echo "CRITICAL" > "$KNOWLEDGE_VERIFICATION_CACHE/verification-status.txt"
    echo "⚠️  CRITICAL: Missing required knowledge files detected"
elif [ ${#MISSING_SECTIONS[@]} -gt 0 ] || [ ${#INCOMPLETE_PRODUCT_FILES[@]} -gt 0 ]; then
    echo "WARNING" > "$KNOWLEDGE_VERIFICATION_CACHE/verification-status.txt"
    echo "⚠️  WARNING: Some knowledge may be incomplete"
else
    echo "OK" > "$KNOWLEDGE_VERIFICATION_CACHE/verification-status.txt"
    echo "✅ Knowledge verification passed"
fi
```

## Important Constraints

- Must verify basepoints completeness (headquarter + module basepoints)
- Must verify product knowledge completeness (mission, roadmap, tech-stack)
- Must detect missing information by analyzing project structure
- Must perform coverage analysis to ensure adequate knowledge extraction
- Must generate comprehensive knowledge gap report
- **CRITICAL**: All verification reports must be stored in `agent-os/.cleanup-cache/knowledge-verification/`
- Must provide clear recommendations for fixing missing or incomplete knowledge
- Must identify if critical knowledge is missing (basepoints or product files) vs. incomplete knowledge (missing sections)

# PHASE 6: Generate Cleanup Report

Now that all cleanup operations are complete, proceed with generating a comprehensive cleanup report by following these instructions:

## Core Responsibilities

1. **Collect Cleanup Statistics**: Gather statistics from all cleanup phases
2. **Generate Comprehensive Report**: Create detailed cleanup report
3. **Store Report Appropriately**: Store report in appropriate location based on context
4. **Clean Up Temporary Files**: Clean up temporary cache files after report generation

## Workflow

### Step 1: Collect Cleanup Statistics

Gather statistics from all cleanup phases:

```bash
# Load cleanup cache
CLEANUP_CACHE="agent-os/.cleanup-cache"
VALIDATION_CACHE="$CLEANUP_CACHE/validation"
KNOWLEDGE_VERIFICATION_CACHE="$CLEANUP_CACHE/knowledge-verification"
DRY_RUN="${DRY_RUN:-false}"

# Load cleanup statistics
PLACEHOLDERS_CLEANED=$(cat "$CLEANUP_CACHE/placeholders-cleaned.txt" 2>/dev/null || echo "0")
UNNECESSARY_LOGIC_REMOVED=$(cat "$CLEANUP_CACHE/unnecessary-logic-removed.txt" 2>/dev/null || echo "0")
BROKEN_REFERENCES_FIXED=$(cat "$CLEANUP_CACHE/broken-references-fixed.txt" 2>/dev/null || echo "0")

FILES_MODIFIED_PLACEHOLDERS=$(cat "$CLEANUP_CACHE/files-modified-placeholders.txt" 2>/dev/null || echo "0")
FILES_MODIFIED_UNNECESSARY=$(cat "$CLEANUP_CACHE/files-modified-unnecessary-logic.txt" 2>/dev/null || echo "0")
FILES_MODIFIED_REFERENCES=$(cat "$CLEANUP_CACHE/files-modified-references.txt" 2>/dev/null || echo "0")

# Load knowledge verification status
KNOWLEDGE_STATUS=$(cat "$KNOWLEDGE_VERIFICATION_CACHE/verification-status.txt" 2>/dev/null || echo "UNKNOWN")

TOTAL_FIXES=$((PLACEHOLDERS_CLEANED + UNNECESSARY_LOGIC_REMOVED + BROKEN_REFERENCES_FIXED))
TOTAL_FILES_MODIFIED=$((FILES_MODIFIED_PLACEHOLDERS + FILES_MODIFIED_UNNECESSARY + FILES_MODIFIED_REFERENCES))
```

### Step 2: Determine Report Storage Location

Determine where to store the cleanup report:

```bash
# Check if running within spec context
if [ -d "agent-os/specs" ] && [ -n "$(find agent-os/specs -mindepth 1 -maxdepth 1 -type d | head -1)" ]; then
    # Find most recent spec directory
    LATEST_SPEC=$(find agent-os/specs -mindepth 1 -maxdepth 1 -type d | sort -r | head -1)
    REPORT_PATH="$LATEST_SPEC/implementation/cache/cleanup"
    mkdir -p "$REPORT_PATH"
    echo "📁 Storing cleanup report in spec context: $REPORT_PATH"
else
    # Store in cleanup cache (will be cleaned up)
    REPORT_PATH="$CLEANUP_CACHE/report"
    mkdir -p "$REPORT_PATH"
    echo "📁 Storing cleanup report in cleanup cache: $REPORT_PATH"
fi
```

### Step 3: Generate Comprehensive Cleanup Report

Create detailed cleanup report:

```bash
# Create JSON report
cat > "$REPORT_PATH/cleanup-report.json" << EOF
{
  "cleanup_timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "dry_run": $DRY_RUN,
  "summary": {
    "total_fixes": $TOTAL_FIXES,
    "total_files_modified": $TOTAL_FILES_MODIFIED,
    "placeholders_cleaned": $PLACEHOLDERS_CLEANED,
    "unnecessary_logic_removed": $UNNECESSARY_LOGIC_REMOVED,
    "broken_references_fixed": $BROKEN_REFERENCES_FIXED
  },
  "files_modified": {
    "by_placeholders": $FILES_MODIFIED_PLACEHOLDERS,
    "by_unnecessary_logic": $FILES_MODIFIED_UNNECESSARY,
    "by_references": $FILES_MODIFIED_REFERENCES,
    "total": $TOTAL_FILES_MODIFIED
  },
  "validation_reports": {
    "placeholder_validation": "$VALIDATION_CACHE/placeholder-cleaning-validation.json",
    "unnecessary_logic_validation": "$VALIDATION_CACHE/unnecessary-logic-removal-validation.json",
    "command_cycle_validation": "$VALIDATION_CACHE/command-cycle-validation.json",
    "structure_alignment_validation": "$VALIDATION_CACHE/project-structure-alignment-validation.json"
  },
  "knowledge_verification": {
    "status": "$KNOWLEDGE_STATUS",
    "report": "$KNOWLEDGE_VERIFICATION_CACHE/knowledge-gap-report.json",
    "summary": "$KNOWLEDGE_VERIFICATION_CACHE/knowledge-gap-report.md"
  }
}
EOF

# Create markdown summary report
cat > "$REPORT_PATH/cleanup-report-summary.md" << EOF
# Cleanup Agent-OS Report

**Cleanup Timestamp:** $(date -u +%Y-%m-%dT%H:%M:%SZ)  
**Mode:** $(if [ "$DRY_RUN" = "true" ]; then echo "DRY-RUN (Preview Only)"; else echo "APPLY (Changes Applied)"; fi)

## Summary

- **Total Fixes Applied:** $TOTAL_FIXES
- **Total Files Modified:** $TOTAL_FILES_MODIFIED
- **Placeholders Cleaned:** $PLACEHOLDERS_CLEANED
- **Unnecessary Logic Removed:** $UNNECESSARY_LOGIC_REMOVED
- **Broken References Fixed:** $BROKEN_REFERENCES_FIXED

## Details by Category

### Placeholder Cleaning
- **Placeholders Cleaned:** $PLACEHOLDERS_CLEANED
- **Files Modified:** $FILES_MODIFIED_PLACEHOLDERS

### Unnecessary Logic Removal
- **Issues Removed:** $UNNECESSARY_LOGIC_REMOVED
- **Files Modified:** $FILES_MODIFIED_UNNECESSARY

### Broken Reference Fixing
- **References Fixed:** $BROKEN_REFERENCES_FIXED
- **Files Modified:** $FILES_MODIFIED_REFERENCES

## Validation Reports

All validation reports are available in:
- \`$VALIDATION_CACHE/\`

## Knowledge Verification

**Knowledge Status:** $KNOWLEDGE_STATUS

$(if [ -f "$KNOWLEDGE_VERIFICATION_CACHE/knowledge-gap-report.md" ]; then
    echo "Knowledge gap report available: \`$KNOWLEDGE_VERIFICATION_CACHE/knowledge-gap-report.md\`"
fi)

$(if [ "$KNOWLEDGE_STATUS" = "CRITICAL" ]; then
    echo ""
    echo "⚠️  **CRITICAL**: Missing required knowledge files detected. Please create missing prerequisites before using specialized commands."
elif [ "$KNOWLEDGE_STATUS" = "WARNING" ]; then
    echo ""
    echo "⚠️  **WARNING**: Some knowledge may be incomplete. Review the knowledge gap report for details."
fi)

## Next Steps

$(if [ "$DRY_RUN" = "true" ]; then
    echo "This was a DRY-RUN. To apply these changes, run the cleanup command again without dry-run mode."
else
    echo "✅ Cleanup complete! Your agent-os has been cleaned and fixed."
    echo ""
    echo "You can now:"
    echo "1. Run validation again to verify all issues are resolved"
    echo "2. Use your cleaned agent-os commands"
fi)
EOF

# Display summary
cat "$REPORT_PATH/cleanup-report-summary.md"
```

### Step 4: Clean Up Temporary Files (if not dry-run)

Clean up temporary cache files after report generation:

```bash
if [ "$DRY_RUN" != "true" ]; then
    # Keep validation reports and final cleanup report, but clean up intermediate files
    echo "🧹 Cleaning up temporary files..."
    
    # Remove intermediate tracking files (keep reports)
    rm -f "$CLEANUP_CACHE"/*.txt 2>/dev/null
    rm -rf "$CLEANUP_CACHE/dry-run-preview" 2>/dev/null
    
    echo "✅ Temporary files cleaned up"
    echo "📁 Cleanup report stored in: $REPORT_PATH/"
else
    echo "🔍 DRY-RUN mode: Keeping all preview files in $CLEANUP_CACHE/"
fi
```

## Important Constraints

- Must collect statistics from all cleanup phases
- Must generate comprehensive cleanup report with all details
- Must store report in appropriate location (spec context or cleanup cache)
- Must clean up temporary files after report generation (unless dry-run)
- **CRITICAL**: 
  - If run within spec context: Store report in `agent-os/specs/[current-spec]/implementation/cache/cleanup/`
  - If run standalone: Store report in `agent-os/.cleanup-cache/report/` (temporary, can be cleaned up)
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents
