# Workflow: Validate Detection Accuracy

## Purpose

Validate that detected values match actual project characteristics, calculate confidence scores, and flag low-confidence detections for user questions.

---

## Workflow

### Step 1: Initialize Validation

```bash
echo "Validating detection accuracy..."

VALIDATION_ERRORS=0
VALIDATION_WARNINGS=0
TOTAL_CHECKS=0
PASSED_CHECKS=0
```

### Step 2: Validate Language Detection

```bash
# Verify detected language matches actual files
validate_language() {
    TOTAL_CHECKS=$((TOTAL_CHECKS + 1))
    
    case "$DETECTED_LANGUAGE" in
        "typescript")
            if [ -f "tsconfig.json" ] || find . -name "*.ts" -type f | head -1 | grep -q "."; then
                echo "  ✓ TypeScript detection confirmed"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
            else
                echo "  ✗ TypeScript detected but no .ts files or tsconfig.json found"
                VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
            fi
            ;;
        "javascript")
            if find . -name "*.js" -type f ! -path "*/node_modules/*" | head -1 | grep -q "."; then
                echo "  ✓ JavaScript detection confirmed"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
            else
                echo "  ✗ JavaScript detected but no .js files found"
                VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
            fi
            ;;
        "rust")
            if [ -f "Cargo.toml" ]; then
                echo "  ✓ Rust detection confirmed"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
            else
                echo "  ✗ Rust detected but no Cargo.toml found"
                VALIDATION_ERRORS=$((VALIDATION_ERRORS + 1))
            fi
            ;;
        "python")
            if [ -f "requirements.txt" ] || [ -f "pyproject.toml" ] || [ -f "setup.py" ]; then
                echo "  ✓ Python detection confirmed"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
            else
                echo "  ✗ Python detected but no Python config files found"
                VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
            fi
            ;;
        "go")
            if [ -f "go.mod" ]; then
                echo "  ✓ Go detection confirmed"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
            else
                echo "  ✗ Go detected but no go.mod found"
                VALIDATION_ERRORS=$((VALIDATION_ERRORS + 1))
            fi
            ;;
        "unknown")
            echo "  ⚠ Language unknown - user input required"
            VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
            NEEDS_USER_INPUT_LANGUAGE=true
            ;;
    esac
}

validate_language
```

### Step 3: Validate Framework Detection

```bash
validate_framework() {
    if [ -z "$DETECTED_FRAMEWORK" ]; then
        return 0  # No framework detected is valid
    fi
    
    TOTAL_CHECKS=$((TOTAL_CHECKS + 1))
    
    # Check if framework is in dependencies
    FRAMEWORK_FOUND=false
    
    if [ -f "package.json" ]; then
        if grep -q "\"$DETECTED_FRAMEWORK\"" package.json 2>/dev/null; then
            FRAMEWORK_FOUND=true
        fi
    fi
    
    if [ -f "Cargo.toml" ]; then
        if grep -qi "$DETECTED_FRAMEWORK" Cargo.toml 2>/dev/null; then
            FRAMEWORK_FOUND=true
        fi
    fi
    
    if [ -f "requirements.txt" ] || [ -f "pyproject.toml" ]; then
        if grep -qi "$DETECTED_FRAMEWORK" requirements.txt pyproject.toml 2>/dev/null; then
            FRAMEWORK_FOUND=true
        fi
    fi
    
    if [ "$FRAMEWORK_FOUND" = "true" ]; then
        echo "  ✓ Framework '$DETECTED_FRAMEWORK' confirmed in dependencies"
        PASSED_CHECKS=$((PASSED_CHECKS + 1))
    else
        echo "  ⚠ Framework '$DETECTED_FRAMEWORK' not found in dependencies"
        VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
    fi
}

validate_framework
```

### Step 4: Validate Commands

```bash
validate_commands() {
    # Validate build command
    if [ -n "$DETECTED_BUILD_CMD" ]; then
        TOTAL_CHECKS=$((TOTAL_CHECKS + 1))
        
        # Check if command would work (basic validation)
        CMD_BASE=$(echo "$DETECTED_BUILD_CMD" | awk '{print $1}')
        
        case "$CMD_BASE" in
            "npm"|"yarn"|"pnpm")
                if [ -f "package.json" ]; then
                    SCRIPT_NAME=$(echo "$DETECTED_BUILD_CMD" | grep -oE 'run [a-z]+' | awk '{print $2}')
                    if [ -n "$SCRIPT_NAME" ] && grep -q "\"$SCRIPT_NAME\"" package.json 2>/dev/null; then
                        echo "  ✓ Build command '$DETECTED_BUILD_CMD' validated"
                        PASSED_CHECKS=$((PASSED_CHECKS + 1))
                    else
                        echo "  ⚠ Build script may not exist in package.json"
                        VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
                    fi
                fi
                ;;
            "cargo"|"go"|"make")
                echo "  ✓ Build command '$DETECTED_BUILD_CMD' is standard"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
                ;;
            *)
                echo "  ⚠ Could not validate build command"
                VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
                ;;
        esac
    fi
    
    # Similar validation for test and lint commands
    if [ -n "$DETECTED_TEST_CMD" ]; then
        TOTAL_CHECKS=$((TOTAL_CHECKS + 1))
        echo "  ✓ Test command detected: $DETECTED_TEST_CMD"
        PASSED_CHECKS=$((PASSED_CHECKS + 1))
    fi
    
    if [ -n "$DETECTED_LINT_CMD" ]; then
        TOTAL_CHECKS=$((TOTAL_CHECKS + 1))
        echo "  ✓ Lint command detected: $DETECTED_LINT_CMD"
        PASSED_CHECKS=$((PASSED_CHECKS + 1))
    fi
}

validate_commands
```

### Step 5: Validate Project Type

```bash
validate_project_type() {
    TOTAL_CHECKS=$((TOTAL_CHECKS + 1))
    
    case "$DETECTED_PROJECT_TYPE" in
        "web_app")
            if [ -d "src/components" ] || [ -d "components" ] || [ -d "pages" ]; then
                echo "  ✓ Web app detection confirmed (components/pages found)"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
            else
                echo "  ⚠ Web app detected but no components/pages directory"
                VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
            fi
            ;;
        "api")
            if [ -d "src/routes" ] || [ -d "routes" ] || [ -d "src/api" ] || [ -d "api" ]; then
                echo "  ✓ API detection confirmed (routes/api found)"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
            else
                echo "  ⚠ API detected but no routes/api directory"
                VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
            fi
            ;;
        "cli")
            if [ -d "bin" ] || grep -q '"bin":' package.json 2>/dev/null; then
                echo "  ✓ CLI detection confirmed"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
            else
                echo "  ⚠ CLI detected but no bin directory or bin field"
                VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
            fi
            ;;
        "library")
            echo "  ✓ Library detection accepted"
            PASSED_CHECKS=$((PASSED_CHECKS + 1))
            ;;
        "monorepo")
            if [ -d "packages" ] || [ -f "lerna.json" ] || [ -f "pnpm-workspace.yaml" ]; then
                echo "  ✓ Monorepo detection confirmed"
                PASSED_CHECKS=$((PASSED_CHECKS + 1))
            else
                echo "  ⚠ Monorepo detected but no packages directory"
                VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
            fi
            ;;
        "unknown")
            echo "  ⚠ Project type unknown - user input required"
            VALIDATION_WARNINGS=$((VALIDATION_WARNINGS + 1))
            NEEDS_USER_INPUT_TYPE=true
            ;;
    esac
}

validate_project_type
```

### Step 6: Calculate Accuracy Score

```bash
# Calculate accuracy
if [ $TOTAL_CHECKS -gt 0 ]; then
    ACCURACY=$(echo "scale=2; $PASSED_CHECKS / $TOTAL_CHECKS" | bc)
else
    ACCURACY="0.00"
fi

# Adjust confidence based on validation
if [ $VALIDATION_ERRORS -gt 0 ]; then
    ADJUSTED_CONFIDENCE=$(echo "scale=2; $DETECTION_CONFIDENCE - 0.20" | bc)
elif [ $VALIDATION_WARNINGS -gt 2 ]; then
    ADJUSTED_CONFIDENCE=$(echo "scale=2; $DETECTION_CONFIDENCE - 0.10" | bc)
else
    ADJUSTED_CONFIDENCE="$DETECTION_CONFIDENCE"
fi

# Ensure confidence stays in valid range
if [ $(echo "$ADJUSTED_CONFIDENCE < 0" | bc) -eq 1 ]; then
    ADJUSTED_CONFIDENCE="0.00"
fi

echo ""
echo "Validation Summary"
echo "=================="
echo "  Total checks: $TOTAL_CHECKS"
echo "  Passed: $PASSED_CHECKS"
echo "  Warnings: $VALIDATION_WARNINGS"
echo "  Errors: $VALIDATION_ERRORS"
echo "  Accuracy: $ACCURACY"
echo "  Adjusted Confidence: $ADJUSTED_CONFIDENCE"
```

### Step 7: Flag Low-Confidence Items

```bash
# Flag items needing user input
if [ $(echo "$ADJUSTED_CONFIDENCE < 0.70" | bc) -eq 1 ]; then
    echo ""
    echo "Low confidence detected. The following items may need user clarification:"
    
    [ "$NEEDS_USER_INPUT_LANGUAGE" = "true" ] && echo "  - Programming language"
    [ "$NEEDS_USER_INPUT_TYPE" = "true" ] && echo "  - Project type"
    
    LOW_CONFIDENCE_FLAG=true
fi
```

---

## Output

Sets the following variables:
- `VALIDATION_ERRORS` - Count of validation errors
- `VALIDATION_WARNINGS` - Count of warnings
- `ACCURACY` - Validation accuracy score (0.0 - 1.0)
- `ADJUSTED_CONFIDENCE` - Confidence adjusted for validation results
- `LOW_CONFIDENCE_FLAG` - true if confidence < 0.70

---

## Important Constraints

- Validation should not fail the workflow, only adjust confidence
- Low confidence should trigger questions, not errors
- Validation results should be logged for debugging
- Should work offline (no external validation)
