# Workflow: Detect Commands

## Purpose

Extract build, test, and lint commands from project configuration files (package.json scripts, Makefile targets, CI configs). Falls back to language-specific defaults when not found.

## Outputs

Sets the following variables:
- `DETECTED_BUILD_CMD` - Build/compile command
- `DETECTED_TEST_CMD` - Test command
- `DETECTED_LINT_CMD` - Lint/static analysis command
- `DETECTED_TYPECHECK_CMD` - Type checking command (if applicable)
- `COMMANDS_CONFIDENCE` - Confidence score (0.0 - 1.0)

---

## Detection Logic

### Step 1: Detect from package.json scripts

```bash
if [ -f "package.json" ]; then
    echo "   Checking package.json scripts..."
    
    # Extract scripts section
    SCRIPTS=$(cat package.json | grep -A 100 '"scripts"' | grep -B 100 -m 1 '^  }' 2>/dev/null || cat package.json)
    
    # Build command
    if echo "$SCRIPTS" | grep -q '"build"'; then
        DETECTED_BUILD_CMD="npm run build"
    elif echo "$SCRIPTS" | grep -q '"compile"'; then
        DETECTED_BUILD_CMD="npm run compile"
    fi
    
    # Test command
    if echo "$SCRIPTS" | grep -q '"test"'; then
        DETECTED_TEST_CMD="npm test"
    elif echo "$SCRIPTS" | grep -q '"test:unit"'; then
        DETECTED_TEST_CMD="npm run test:unit"
    fi
    
    # Lint command
    if echo "$SCRIPTS" | grep -q '"lint"'; then
        DETECTED_LINT_CMD="npm run lint"
    elif echo "$SCRIPTS" | grep -q '"eslint"'; then
        DETECTED_LINT_CMD="npm run eslint"
    fi
    
    # Type check command
    if echo "$SCRIPTS" | grep -q '"typecheck"'; then
        DETECTED_TYPECHECK_CMD="npm run typecheck"
    elif echo "$SCRIPTS" | grep -q '"type-check"'; then
        DETECTED_TYPECHECK_CMD="npm run type-check"
    elif [ -f "tsconfig.json" ]; then
        DETECTED_TYPECHECK_CMD="tsc --noEmit"
    fi
    
    COMMANDS_CONFIDENCE="0.95"
fi
```

### Step 2: Detect from Makefile

```bash
if [ -f "Makefile" ]; then
    echo "   Checking Makefile targets..."
    
    # Build target
    if grep -q "^build:" Makefile; then
        [ -z "$DETECTED_BUILD_CMD" ] && DETECTED_BUILD_CMD="make build"
    fi
    
    # Test target
    if grep -q "^test:" Makefile; then
        [ -z "$DETECTED_TEST_CMD" ] && DETECTED_TEST_CMD="make test"
    fi
    
    # Lint target
    if grep -q "^lint:" Makefile; then
        [ -z "$DETECTED_LINT_CMD" ] && DETECTED_LINT_CMD="make lint"
    fi
    
    # Check target (often type-check or full validation)
    if grep -q "^check:" Makefile; then
        [ -z "$DETECTED_TYPECHECK_CMD" ] && DETECTED_TYPECHECK_CMD="make check"
    fi
    
    [ -z "$COMMANDS_CONFIDENCE" ] && COMMANDS_CONFIDENCE="0.90"
fi
```

### Step 3: Detect from CI config files

```bash
# GitHub Actions
if [ -d ".github/workflows" ]; then
    echo "   Checking GitHub Actions workflows..."
    
    for workflow in .github/workflows/*.yml .github/workflows/*.yaml; do
        [ -f "$workflow" ] || continue
        
        WORKFLOW_CONTENT=$(cat "$workflow" 2>/dev/null)
        
        # Look for common CI steps
        if echo "$WORKFLOW_CONTENT" | grep -q "npm run build" && [ -z "$DETECTED_BUILD_CMD" ]; then
            DETECTED_BUILD_CMD="npm run build"
        fi
        if echo "$WORKFLOW_CONTENT" | grep -q "npm test" && [ -z "$DETECTED_TEST_CMD" ]; then
            DETECTED_TEST_CMD="npm test"
        fi
        if echo "$WORKFLOW_CONTENT" | grep -q "cargo test" && [ -z "$DETECTED_TEST_CMD" ]; then
            DETECTED_TEST_CMD="cargo test"
        fi
        if echo "$WORKFLOW_CONTENT" | grep -q "pytest" && [ -z "$DETECTED_TEST_CMD" ]; then
            DETECTED_TEST_CMD="pytest"
        fi
        if echo "$WORKFLOW_CONTENT" | grep -q "go test" && [ -z "$DETECTED_TEST_CMD" ]; then
            DETECTED_TEST_CMD="go test ./..."
        fi
    done
    
    [ -z "$COMMANDS_CONFIDENCE" ] && COMMANDS_CONFIDENCE="0.80"
fi

# GitLab CI
if [ -f ".gitlab-ci.yml" ]; then
    echo "   Checking GitLab CI config..."
    
    GITLAB_CI=$(cat .gitlab-ci.yml 2>/dev/null)
    
    if echo "$GITLAB_CI" | grep -q "npm run build" && [ -z "$DETECTED_BUILD_CMD" ]; then
        DETECTED_BUILD_CMD="npm run build"
    fi
    if echo "$GITLAB_CI" | grep -q "npm test" && [ -z "$DETECTED_TEST_CMD" ]; then
        DETECTED_TEST_CMD="npm test"
    fi
    
    [ -z "$COMMANDS_CONFIDENCE" ] && COMMANDS_CONFIDENCE="0.80"
fi
```

### Step 4: Detect from Cargo.toml (Rust)

```bash
if [ -f "Cargo.toml" ]; then
    echo "   Detecting Rust commands..."
    
    # Rust has standard commands
    [ -z "$DETECTED_BUILD_CMD" ] && DETECTED_BUILD_CMD="cargo build"
    [ -z "$DETECTED_TEST_CMD" ] && DETECTED_TEST_CMD="cargo test"
    [ -z "$DETECTED_LINT_CMD" ] && DETECTED_LINT_CMD="cargo clippy"
    [ -z "$DETECTED_TYPECHECK_CMD" ] && DETECTED_TYPECHECK_CMD="cargo check"
    
    [ -z "$COMMANDS_CONFIDENCE" ] && COMMANDS_CONFIDENCE="0.95"
fi
```

### Step 5: Detect from go.mod (Go)

```bash
if [ -f "go.mod" ]; then
    echo "   Detecting Go commands..."
    
    [ -z "$DETECTED_BUILD_CMD" ] && DETECTED_BUILD_CMD="go build ./..."
    [ -z "$DETECTED_TEST_CMD" ] && DETECTED_TEST_CMD="go test ./..."
    [ -z "$DETECTED_LINT_CMD" ] && DETECTED_LINT_CMD="golangci-lint run ./..."
    [ -z "$DETECTED_TYPECHECK_CMD" ] && DETECTED_TYPECHECK_CMD="go vet ./..."
    
    [ -z "$COMMANDS_CONFIDENCE" ] && COMMANDS_CONFIDENCE="0.95"
fi
```

### Step 6: Detect from Python files

```bash
if [ -f "requirements.txt" ] || [ -f "pyproject.toml" ]; then
    echo "   Detecting Python commands..."
    
    # Python typically doesn't have a build step
    
    # Test framework detection
    if [ -f "pytest.ini" ] || [ -f "pyproject.toml" ] && grep -q "pytest" pyproject.toml 2>/dev/null; then
        [ -z "$DETECTED_TEST_CMD" ] && DETECTED_TEST_CMD="pytest"
    elif [ -f "setup.py" ]; then
        [ -z "$DETECTED_TEST_CMD" ] && DETECTED_TEST_CMD="python -m pytest"
    fi
    
    # Lint detection
    if grep -q "flake8" requirements.txt pyproject.toml 2>/dev/null; then
        [ -z "$DETECTED_LINT_CMD" ] && DETECTED_LINT_CMD="flake8"
    elif grep -q "ruff" requirements.txt pyproject.toml 2>/dev/null; then
        [ -z "$DETECTED_LINT_CMD" ] && DETECTED_LINT_CMD="ruff check"
    elif grep -q "pylint" requirements.txt pyproject.toml 2>/dev/null; then
        [ -z "$DETECTED_LINT_CMD" ] && DETECTED_LINT_CMD="pylint"
    fi
    
    # Type checking
    if grep -q "mypy" requirements.txt pyproject.toml 2>/dev/null; then
        [ -z "$DETECTED_TYPECHECK_CMD" ] && DETECTED_TYPECHECK_CMD="mypy ."
    elif grep -q "pyright" requirements.txt pyproject.toml 2>/dev/null; then
        [ -z "$DETECTED_TYPECHECK_CMD" ] && DETECTED_TYPECHECK_CMD="pyright"
    fi
    
    [ -z "$COMMANDS_CONFIDENCE" ] && COMMANDS_CONFIDENCE="0.85"
fi
```

### Step 7: Output Detection Summary

```bash
# Set default confidence if nothing was detected
[ -z "$COMMANDS_CONFIDENCE" ] && COMMANDS_CONFIDENCE="0.50"

# Log what was found
if [ -n "$DETECTED_BUILD_CMD" ] || [ -n "$DETECTED_TEST_CMD" ] || [ -n "$DETECTED_LINT_CMD" ]; then
    echo "   Commands detected with confidence: $COMMANDS_CONFIDENCE"
else
    echo "   ⚠️ No commands detected - will use language defaults"
fi
```

---

## Important Constraints

- Must not fail if config files are missing
- Should prefer explicit configs (package.json scripts) over inferred
- CI configs are lower confidence than direct project configs
- Must handle both YAML and JSON gracefully
