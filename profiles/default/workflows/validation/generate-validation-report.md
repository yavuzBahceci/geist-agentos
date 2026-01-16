# Generate Validation Report

## Core Responsibilities

1. **Collect Validator Results**: Gather results from all validators
2. **Generate Markdown Report**: Create structured validation report
3. **Include Metadata**: Add timestamp, command, overall status
4. **Include Per-Validator Results**: Show each validator's results with icons
5. **Output to Cache**: Store report in spec's cache directory

## Workflow

### Step 1: Collect Validator Results

```bash
# SPEC_PATH and COMMAND should be set by the calling workflow
if [ -z "$SPEC_PATH" ]; then
    echo "❌ SPEC_PATH not set"
    exit 1
fi

COMMAND="${COMMAND:-unknown}"
CACHE_PATH="$SPEC_PATH/implementation/cache"

echo "📊 Generating validation report..."

# Initialize overall status
OVERALL_STATUS="PASSED"
TOTAL_ERRORS=0
TOTAL_WARNINGS=0

# Collect results from validation files
declare -A VALIDATOR_RESULTS

# Read output-exists validation
if [ -f "$CACHE_PATH/output-exists-validation.txt" ]; then
    source "$CACHE_PATH/output-exists-validation.txt"
    VALIDATOR_RESULTS["output-exists"]="$STATUS"
    [ "$STATUS" = "FAILED" ] && OVERALL_STATUS="FAILED"
    TOTAL_ERRORS=$((TOTAL_ERRORS + ${ERRORS:-0}))
    TOTAL_WARNINGS=$((TOTAL_WARNINGS + ${WARNINGS:-0}))
fi

# Read knowledge-integration validation
if [ -f "$CACHE_PATH/knowledge-integration-validation.txt" ]; then
    source "$CACHE_PATH/knowledge-integration-validation.txt"
    VALIDATOR_RESULTS["knowledge-integration"]="$STATUS"
    TOTAL_WARNINGS=$((TOTAL_WARNINGS + ${WARNINGS:-0}))
fi

# Read references validation
if [ -f "$CACHE_PATH/references-validation.txt" ]; then
    source "$CACHE_PATH/references-validation.txt"
    VALIDATOR_RESULTS["references"]="$STATUS"
    [ "$STATUS" = "FAILED" ] && OVERALL_STATUS="FAILED"
    TOTAL_ERRORS=$((TOTAL_ERRORS + ${CRITICAL_BROKEN:-0}))
    TOTAL_WARNINGS=$((TOTAL_WARNINGS + ${WARNING_BROKEN:-0}))
fi
```

### Step 2: Generate Report Header

```bash
REPORT_FILE="$CACHE_PATH/validation-report.md"

cat > "$REPORT_FILE" << REPORT_HEADER
# Validation Report

**Generated:** $(date)
**Command:** $COMMAND
**Spec Path:** $SPEC_PATH
**Overall Status:** $([ "$OVERALL_STATUS" = "PASSED" ] && echo "✅ PASSED" || echo "❌ FAILED")

---

## Summary

| Metric | Value |
|--------|-------|
| Total Errors | $TOTAL_ERRORS |
| Total Warnings | $TOTAL_WARNINGS |
| Validators Run | ${#VALIDATOR_RESULTS[@]} |

---

REPORT_HEADER
```

### Step 3: Add Per-Validator Results

```bash
cat >> "$REPORT_FILE" << VALIDATORS_HEADER
## Validator Results

VALIDATORS_HEADER

# Output exists validator
if [ -f "$CACHE_PATH/output-exists-validation.txt" ]; then
    source "$CACHE_PATH/output-exists-validation.txt"
    STATUS_ICON=$([ "$STATUS" = "PASSED" ] && echo "✅" || [ "$STATUS" = "FAILED" ] && echo "❌" || echo "⚠️")
    
    cat >> "$REPORT_FILE" << OUTPUT_EXISTS
### $STATUS_ICON Output Exists Validator

- **Status:** $STATUS
- **Files Passed:** $PASSED
- **Errors:** $ERRORS
- **Warnings:** $WARNINGS

OUTPUT_EXISTS
fi

# Knowledge integration validator
if [ -f "$CACHE_PATH/knowledge-integration-validation.txt" ]; then
    source "$CACHE_PATH/knowledge-integration-validation.txt"
    STATUS_ICON=$([ "$STATUS" = "PASSED" ] && echo "✅" || [ "$STATUS" = "FAILED" ] && echo "❌" || echo "⚠️")
    
    cat >> "$REPORT_FILE" << KNOWLEDGE
### $STATUS_ICON Knowledge Integration Validator

- **Status:** $STATUS
- **Has Knowledge:** $HAS_KNOWLEDGE
- **Has Layer Detection:** $HAS_LAYER
- **Knowledge Used:** $KNOWLEDGE_USED
- **Warnings:** $WARNINGS

KNOWLEDGE
fi

# References validator
if [ -f "$CACHE_PATH/references-validation.txt" ]; then
    source "$CACHE_PATH/references-validation.txt"
    STATUS_ICON=$([ "$STATUS" = "PASSED" ] && echo "✅" || [ "$STATUS" = "FAILED" ] && echo "❌" || echo "⚠️")
    
    cat >> "$REPORT_FILE" << REFERENCES
### $STATUS_ICON References Validator

- **Status:** $STATUS
- **Total References:** $TOTAL_REFS
- **Valid:** $VALID
- **Broken:** $BROKEN
  - Critical: $CRITICAL_BROKEN
  - Warnings: $WARNING_BROKEN

REFERENCES
fi
```

### Step 4: Add Project Validators Section

```bash
cat >> "$REPORT_FILE" << PROJECT_VALIDATORS
---

## Project-Specific Validators

{{PROJECT_VALIDATORS}}

*Project-specific validators are added during \`deploy-agents\` specialization.*

PROJECT_VALIDATORS
```

### Step 5: Add Recommendations

```bash
cat >> "$REPORT_FILE" << RECOMMENDATIONS
---

## Recommendations

RECOMMENDATIONS

if [ "$OVERALL_STATUS" = "FAILED" ]; then
    cat >> "$REPORT_FILE" << FAILED_REC
⛔ **Validation Failed**

Please address the following before proceeding:
1. Review the failed validators above
2. Fix any missing required files
3. Resolve any broken critical references
4. Re-run validation after fixes

FAILED_REC
elif [ "$TOTAL_WARNINGS" -gt 0 ]; then
    cat >> "$REPORT_FILE" << WARNING_REC
⚠️ **Validation Passed with Warnings**

Consider addressing the following:
1. Review warning-level issues
2. Ensure knowledge integration is complete
3. Fix non-critical broken references

WARNING_REC
else
    cat >> "$REPORT_FILE" << PASSED_REC
✅ **Validation Passed**

All validators passed successfully. You may proceed with the next step.

PASSED_REC
fi
```

### Step 6: Finalize Report

```bash
cat >> "$REPORT_FILE" << REPORT_FOOTER
---

*Generated by generate-validation-report workflow*
*Exit Code: $([ "$OVERALL_STATUS" = "PASSED" ] && echo "0" || echo "1")*
REPORT_FOOTER

echo "✅ Validation report generated: $REPORT_FILE"
echo ""
cat "$REPORT_FILE"
echo ""

# Return exit code based on overall status
if [ "$OVERALL_STATUS" = "PASSED" ]; then
    exit 0
else
    exit 1
fi
```

## Important Constraints

- Must collect results from all validators
- Must generate markdown report with metadata
- Must include status icons for each validator
- Must include `{{PROJECT_VALIDATORS}}` placeholder for specialization
- Must output to `$SPEC_PATH/implementation/cache/validation-report.md`
- **CRITICAL**: This is a core workflow that runs for ALL projects
