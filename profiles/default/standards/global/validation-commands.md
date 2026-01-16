# Validation Commands

This file defines the project-specific commands used for deterministic validation during implementation. These commands are executed by `validate-implementation.md` workflow before marking tasks as complete.

**Important:** This file is specialized during `deploy-agents` based on the project's tech stack.

---

## Build Command

The command to build/compile the project. Exit code 0 indicates success.

```bash
# {{PROJECT_BUILD_COMMAND}}
# Example values after specialization:
#   npm run build
#   cargo build --release
#   go build ./...
#   make build
#   python setup.py build
#   dotnet build
```

**Current:** `{{PROJECT_BUILD_COMMAND}}`

---

## Test Command

The command to run the project's test suite. Exit code 0 indicates all tests pass.

```bash
# {{PROJECT_TEST_COMMAND}}
# Example values after specialization:
#   npm test
#   cargo test
#   go test ./...
#   pytest
#   make test
#   dotnet test
#   jest
```

**Current:** `{{PROJECT_TEST_COMMAND}}`

---

## Lint Command

The command to run linting/static analysis. Exit code 0 indicates no errors.

```bash
# {{PROJECT_LINT_COMMAND}}
# Example values after specialization:
#   npm run lint
#   cargo clippy -- -D warnings
#   golangci-lint run
#   flake8 .
#   eslint .
#   shellcheck scripts/*.sh
```

**Current:** `{{PROJECT_LINT_COMMAND}}`

---

## Type Check Command

The command to run type checking (for typed languages). Exit code 0 indicates no type errors.

```bash
# {{PROJECT_TYPECHECK_COMMAND}}
# Example values after specialization:
#   npx tsc --noEmit
#   mypy .
#   cargo check
#   go vet ./...
```

**Current:** `{{PROJECT_TYPECHECK_COMMAND}}`

---

## Format Check Command

The command to check code formatting. Exit code 0 indicates code is properly formatted.

```bash
# {{PROJECT_FORMAT_CHECK_COMMAND}}
# Example values after specialization:
#   npm run format:check
#   cargo fmt --check
#   gofmt -l .
#   black --check .
#   prettier --check .
```

**Current:** `{{PROJECT_FORMAT_CHECK_COMMAND}}`

---

## Custom Validators

Project-specific validation commands beyond standard build/test/lint.

```bash
# {{PROJECT_CUSTOM_VALIDATORS}}
# Example values after specialization:
#   npm run validate:api    # API contract validation
#   ./scripts/check-migrations.sh  # Database migration check
#   npm run audit           # Security audit
```

**Current:** `{{PROJECT_CUSTOM_VALIDATORS}}`

---

## Validation Behavior

### When to Run Validation

Validation is run:
1. After completing each task group (before marking complete)
2. Before running human review checks
3. At the end of `implement-tasks`

### Handling Failures

| Status | Behavior |
|--------|----------|
| **PASSED** | Proceed normally |
| **WARNINGS** | Proceed with note, consider addressing |
| **FAILED** | Stop and request user action |

### Skipping Validation

If a command is not configured (placeholder not replaced), that validation step is skipped with a note "No [validator] configured for this project."

---

## Specialization During Deploy-Agents

During `deploy-agents`, this file is specialized by:

1. **Detecting tech stack** from `product/tech-stack.md` and basepoints
2. **Matching patterns** to known build systems:
   - `package.json` → npm/yarn commands
   - `Cargo.toml` → cargo commands
   - `go.mod` → go commands
   - `requirements.txt`/`pyproject.toml` → Python commands
   - `Makefile` → make commands
3. **Extracting commands** from project configuration files
4. **Replacing placeholders** with actual commands

The specialization preserves any custom validators added during project setup.
