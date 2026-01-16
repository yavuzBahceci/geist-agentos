# Validate Output Exists

## Core Responsibilities

1. **Define Required Files**: Specify required files per command (shape-spec, write-spec, etc.)
2. **Check File Existence**: Verify each required file exists
3. **Check Files Not Empty**: Verify files have content
4. **Output Validation Status**: Report pass/fail for each check
5. **Return Exit Code**: Exit 0 if all pass, exit 1 if any fail

## Workflow

### Step 1: Define Required Files Per Command

```bash
# SPEC_PATH and COMMAND should be set by the calling workflow
if [ -z "$SPEC_PATH" ]; then
    echo "❌ SPEC_PATH not set"
    exit 1
fi

COMMAND="${COMMAND:-unknown}"
echo "🔍 Validating output existence for: $COMMAND"

# Define required files per command
case "$COMMAND" in
    "shape-spec")
        REQUIRED_FILES="planning/initialization.md planning/requirements.md"
        ;;
    "write-spec")
        REQUIRED_FILES="planning/initialization.md planning/requirements.md spec.md implementation/cache/basepoints-knowledge.md implementation/cache/detected-layer.txt"
        ;;
    "create-tasks")
        REQUIRED_FILES="planning/initialization.md planning/requirements.md spec.md tasks.md"
        ;;
    "implement-tasks")
        REQUIRED_FILES="spec.md tasks.md"
        ;;
    "orchestrate-tasks")
        REQUIRED_FILES="tasks.md orchestration.yml"
        ;;
    *)
        REQUIRED_FILES="spec.md"
        echo "⚠️ Unknown command: $COMMAND. Using minimal validation."
        ;;
esac
```

### Step 2: Check File Existence

```bash
echo ""
echo "📁 Checking required files..."
echo ""

ERRORS=0
PASSED=0

for file in $REQUIRED_FILES; do
    FILE_PATH="$SPEC_PATH/$file"
    
    if [ -f "$FILE_PATH" ]; then
        echo "  ✅ EXISTS: $file"
        PASSED=$((PASSED + 1))
    elif [ -d "$FILE_PATH" ]; then
        echo "  ✅ EXISTS (dir): $file"
        PASSED=$((PASSED + 1))
    else
        echo "  ❌ MISSING: $file"
        ERRORS=$((ERRORS + 1))
    fi
done

EXISTENCE_ERRORS=$ERRORS
```

### Step 3: Check Files Not Empty

```bash
echo ""
echo "📏 Checking file contents..."
echo ""

CONTENT_ERRORS=0
WARNINGS=0

for file in $REQUIRED_FILES; do
    FILE_PATH="$SPEC_PATH/$file"
    
    # Skip if file doesn't exist or is directory
    if [ ! -f "$FILE_PATH" ]; then
        continue
    fi
    
    # Check file size
    FILE_SIZE=$(wc -c < "$FILE_PATH" 2>/dev/null | tr -d ' ')
    
    if [ "$FILE_SIZE" -eq 0 ]; then
        echo "  ❌ EMPTY: $file (0 bytes)"
        CONTENT_ERRORS=$((CONTENT_ERRORS + 1))
    elif [ "$FILE_SIZE" -lt 100 ]; then
        echo "  ⚠️ MINIMAL: $file ($FILE_SIZE bytes)"
        WARNINGS=$((WARNINGS + 1))
    else
        echo "  ✅ HAS CONTENT: $file ($FILE_SIZE bytes)"
    fi
done
```

### Step 4: Check Required Sections

```bash
echo ""
echo "📋 Checking required sections..."
echo ""

SECTION_ERRORS=0

# Check requirements.md sections
if [ -f "$SPEC_PATH/planning/requirements.md" ]; then
    echo "  Checking requirements.md:"
    for section in "## Overview" "## Requirements"; do
        if grep -q "$section" "$SPEC_PATH/planning/requirements.md" 2>/dev/null; then
            echo "    ✅ Found: $section"
        else
            echo "    ❌ Missing: $section"
            SECTION_ERRORS=$((SECTION_ERRORS + 1))
        fi
    done
fi

# Check spec.md sections
if [ -f "$SPEC_PATH/spec.md" ]; then
    echo "  Checking spec.md:"
    for section in "## Goal" "## Requirements"; do
        if grep -q "$section" "$SPEC_PATH/spec.md" 2>/dev/null; then
            echo "    ✅ Found: $section"
        else
            echo "    ⚠️ Missing: $section"
        fi
    done
fi

# Check tasks.md sections
if [ -f "$SPEC_PATH/tasks.md" ]; then
    echo "  Checking tasks.md:"
    TASK_COUNT=$(grep -c "^\- \[ \]" "$SPEC_PATH/tasks.md" 2>/dev/null || echo "0")
    if [ "$TASK_COUNT" -gt 0 ]; then
        echo "    ✅ Found $TASK_COUNT unchecked tasks"
    else
        echo "    ⚠️ No unchecked tasks found"
    fi
fi
```

### Step 5: Generate Output and Exit Code

```bash
echo ""
echo "═══════════════════════════════════════════════════════"
echo "  OUTPUT EXISTENCE VALIDATION RESULTS"
echo "═══════════════════════════════════════════════════════"
echo ""
echo "  Command: $COMMAND"
echo "  Spec Path: $SPEC_PATH"
echo ""
echo "  Files Passed: $PASSED"
echo "  Files Missing: $EXISTENCE_ERRORS"
echo "  Files Empty: $CONTENT_ERRORS"
echo "  Warnings: $WARNINGS"
echo ""

# Determine overall status
TOTAL_ERRORS=$((EXISTENCE_ERRORS + CONTENT_ERRORS))

if [ "$TOTAL_ERRORS" -eq 0 ]; then
    echo "  Status: ✅ PASSED"
    VALIDATION_STATUS="PASSED"
    EXIT_CODE=0
else
    echo "  Status: ❌ FAILED ($TOTAL_ERRORS errors)"
    VALIDATION_STATUS="FAILED"
    EXIT_CODE=1
fi

echo ""
echo "═══════════════════════════════════════════════════════"

# Store result for validation report
mkdir -p "$SPEC_PATH/implementation/cache"
cat > "$SPEC_PATH/implementation/cache/output-exists-validation.txt" << EOF
VALIDATOR=output-exists
COMMAND=$COMMAND
TIMESTAMP=$(date +%s)
PASSED=$PASSED
ERRORS=$TOTAL_ERRORS
WARNINGS=$WARNINGS
STATUS=$VALIDATION_STATUS
EOF

# Export and exit
export OUTPUT_EXISTS_STATUS="$VALIDATION_STATUS"
export OUTPUT_EXISTS_ERRORS="$TOTAL_ERRORS"
exit $EXIT_CODE
```

## Important Constraints

- Must define required files per command type
- Must check both existence and non-empty content
- Must return exit code 0 for success, 1 for failure
- Works for any project (project-agnostic)
- **CRITICAL**: This is a core validator that runs for ALL projects
