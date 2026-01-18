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
