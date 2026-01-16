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
{{workflows/validation/detect-placeholders}}

# Run unnecessary logic detection
echo "2️⃣ Running unnecessary logic detection..."
{{workflows/validation/detect-unnecessary-logic}}

# Run technology-agnostic validation
echo "3️⃣ Running technology-agnostic validation..."
{{workflows/validation/validate-technology-agnostic}}

# Run command cycle validation
echo "4️⃣ Running command cycle validation..."
{{workflows/validation/validate-command-cycle}}

# Run project structure alignment validation
echo "5️⃣ Running project structure alignment validation..."
{{workflows/validation/validate-project-structure-alignment}}
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

# Calculate total issues
TOTAL_ISSUES=$((PLACEHOLDER_TOTAL + UNNECESSARY_LOGIC_TOTAL + TECH_VALIDATION_TOTAL + CYCLE_VALIDATION_TOTAL + STRUCTURE_VALIDATION_TOTAL))
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
    "total_issues": $TOTAL_ISSUES
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
