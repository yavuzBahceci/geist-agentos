# Workflow: Detect Architecture

## Purpose

Analyze directory structure to detect project type, architecture patterns (monolith vs microservices), module boundaries, and project size metrics.

## Outputs

Sets the following variables:
- `DETECTED_PROJECT_TYPE` - web_app, cli, api, library, monorepo
- `DETECTED_ARCHITECTURE` - monolith, microservices, modular
- `DETECTED_FILE_COUNT` - Total number of source files
- `DETECTED_LINE_COUNT` - Approximate total lines of code
- `DETECTED_MODULE_COUNT` - Number of detected modules
- `DETECTED_MODULES` - Comma-separated list of module names
- `ARCHITECTURE_CONFIDENCE` - Confidence score (0.0 - 1.0)

---

## Detection Logic

### Step 1: Count Files and Lines

```bash
echo "   Counting files and lines..."

# Count source files (excluding node_modules, vendor, etc.)
DETECTED_FILE_COUNT=$(find . \
    -type f \
    \( -name "*.ts" -o -name "*.tsx" -o -name "*.js" -o -name "*.jsx" \
       -o -name "*.rs" -o -name "*.go" -o -name "*.py" \
       -o -name "*.java" -o -name "*.kt" -o -name "*.swift" \
       -o -name "*.c" -o -name "*.cpp" -o -name "*.h" \) \
    ! -path "*/node_modules/*" \
    ! -path "*/vendor/*" \
    ! -path "*/.git/*" \
    ! -path "*/dist/*" \
    ! -path "*/build/*" \
    ! -path "*/target/*" \
    ! -path "*/__pycache__/*" \
    2>/dev/null | wc -l | tr -d ' ')

# Estimate line count (faster than wc -l on all files)
if [ "$DETECTED_FILE_COUNT" -gt 0 ] 2>/dev/null; then
    # Sample up to 50 files and extrapolate
    SAMPLE_LINES=$(find . \
        -type f \
        \( -name "*.ts" -o -name "*.tsx" -o -name "*.js" -o -name "*.jsx" \
           -o -name "*.rs" -o -name "*.go" -o -name "*.py" \) \
        ! -path "*/node_modules/*" \
        ! -path "*/vendor/*" \
        ! -path "*/.git/*" \
        2>/dev/null | head -50 | xargs wc -l 2>/dev/null | tail -1 | awk '{print $1}')
    
    SAMPLE_COUNT=$(find . \
        -type f \
        \( -name "*.ts" -o -name "*.tsx" -o -name "*.js" -o -name "*.jsx" \
           -o -name "*.rs" -o -name "*.go" -o -name "*.py" \) \
        ! -path "*/node_modules/*" \
        ! -path "*/vendor/*" \
        ! -path "*/.git/*" \
        2>/dev/null | head -50 | wc -l | tr -d ' ')
    
    if [ "$SAMPLE_COUNT" -gt 0 ] 2>/dev/null; then
        AVG_LINES_PER_FILE=$((SAMPLE_LINES / SAMPLE_COUNT))
        DETECTED_LINE_COUNT=$((DETECTED_FILE_COUNT * AVG_LINES_PER_FILE))
    else
        DETECTED_LINE_COUNT=0
    fi
else
    DETECTED_LINE_COUNT=0
fi

echo "   Found $DETECTED_FILE_COUNT files, ~$DETECTED_LINE_COUNT lines"
```

### Step 2: Detect Project Type

```bash
echo "   Detecting project type..."

DETECTED_PROJECT_TYPE="unknown"
ARCHITECTURE_CONFIDENCE="0.50"

# Web Application indicators
if [ -d "src/components" ] || [ -d "app/components" ] || [ -d "components" ]; then
    DETECTED_PROJECT_TYPE="web_app"
    ARCHITECTURE_CONFIDENCE="0.90"
elif [ -d "pages" ] || [ -d "src/pages" ] || [ -d "app" ]; then
    # Could be Next.js, Nuxt, etc.
    if [ -f "next.config.js" ] || [ -f "next.config.mjs" ] || [ -f "nuxt.config.ts" ]; then
        DETECTED_PROJECT_TYPE="web_app"
        ARCHITECTURE_CONFIDENCE="0.95"
    fi
fi

# CLI Tool indicators
if [ -d "bin" ] || [ -f "cli.js" ] || [ -f "cli.ts" ]; then
    DETECTED_PROJECT_TYPE="cli"
    ARCHITECTURE_CONFIDENCE="0.85"
elif grep -q '"bin":' package.json 2>/dev/null; then
    DETECTED_PROJECT_TYPE="cli"
    ARCHITECTURE_CONFIDENCE="0.90"
fi

# API/Backend Service indicators
if [ -d "src/routes" ] || [ -d "src/api" ] || [ -d "routes" ] || [ -d "api" ]; then
    DETECTED_PROJECT_TYPE="api"
    ARCHITECTURE_CONFIDENCE="0.85"
elif [ -d "src/controllers" ] || [ -d "controllers" ]; then
    DETECTED_PROJECT_TYPE="api"
    ARCHITECTURE_CONFIDENCE="0.85"
fi

# Library indicators
if [ -f "src/index.ts" ] || [ -f "src/index.js" ] || [ -f "src/lib.rs" ]; then
    if [ ! -d "src/components" ] && [ ! -d "src/pages" ] && [ ! -d "src/routes" ]; then
        DETECTED_PROJECT_TYPE="library"
        ARCHITECTURE_CONFIDENCE="0.80"
    fi
fi

# Monorepo indicators
if [ -d "packages" ] || [ -f "lerna.json" ] || [ -f "pnpm-workspace.yaml" ]; then
    DETECTED_PROJECT_TYPE="monorepo"
    ARCHITECTURE_CONFIDENCE="0.95"
elif [ -f "turbo.json" ] || [ -f "nx.json" ]; then
    DETECTED_PROJECT_TYPE="monorepo"
    ARCHITECTURE_CONFIDENCE="0.95"
fi

echo "   Project type: $DETECTED_PROJECT_TYPE (confidence: $ARCHITECTURE_CONFIDENCE)"
```

### Step 3: Detect Architecture Pattern

```bash
echo "   Detecting architecture pattern..."

DETECTED_ARCHITECTURE="monolith"

# Check for microservices indicators
if [ -d "services" ] && [ $(ls -d services/*/ 2>/dev/null | wc -l) -gt 1 ]; then
    DETECTED_ARCHITECTURE="microservices"
elif [ -f "docker-compose.yml" ] || [ -f "docker-compose.yaml" ]; then
    # Check if docker-compose defines multiple services with different build contexts
    SERVICE_COUNT=$(grep -c "build:" docker-compose.yml docker-compose.yaml 2>/dev/null || echo 0)
    if [ "$SERVICE_COUNT" -gt 2 ]; then
        DETECTED_ARCHITECTURE="microservices"
    fi
fi

# Check for modular architecture
if [ -d "src/modules" ] || [ -d "modules" ] || [ -d "src/features" ]; then
    DETECTED_ARCHITECTURE="modular"
fi

# Monorepo is a form of modular
if [ "$DETECTED_PROJECT_TYPE" = "monorepo" ]; then
    DETECTED_ARCHITECTURE="modular"
fi

echo "   Architecture: $DETECTED_ARCHITECTURE"
```

### Step 4: Detect Module Boundaries

```bash
echo "   Detecting module boundaries..."

DETECTED_MODULE_COUNT=0
DETECTED_MODULES=""

# For monorepos, packages are modules
if [ -d "packages" ]; then
    DETECTED_MODULES=$(ls -d packages/*/ 2>/dev/null | xargs -n1 basename | tr '\n' ',' | sed 's/,$//')
    DETECTED_MODULE_COUNT=$(echo "$DETECTED_MODULES" | tr ',' '\n' | wc -l | tr -d ' ')
fi

# For services-based
if [ -d "services" ]; then
    SERVICES=$(ls -d services/*/ 2>/dev/null | xargs -n1 basename | tr '\n' ',' | sed 's/,$//')
    if [ -n "$SERVICES" ]; then
        DETECTED_MODULES="$SERVICES"
        DETECTED_MODULE_COUNT=$(echo "$DETECTED_MODULES" | tr ',' '\n' | wc -l | tr -d ' ')
    fi
fi

# For src/modules or src/features structure
if [ -d "src/modules" ]; then
    MODULES=$(ls -d src/modules/*/ 2>/dev/null | xargs -n1 basename | tr '\n' ',' | sed 's/,$//')
    if [ -n "$MODULES" ]; then
        DETECTED_MODULES="$MODULES"
        DETECTED_MODULE_COUNT=$(echo "$DETECTED_MODULES" | tr ',' '\n' | wc -l | tr -d ' ')
    fi
elif [ -d "src/features" ]; then
    FEATURES=$(ls -d src/features/*/ 2>/dev/null | xargs -n1 basename | tr '\n' ',' | sed 's/,$//')
    if [ -n "$FEATURES" ]; then
        DETECTED_MODULES="$FEATURES"
        DETECTED_MODULE_COUNT=$(echo "$DETECTED_MODULES" | tr ',' '\n' | wc -l | tr -d ' ')
    fi
fi

# Fallback: count top-level src directories
if [ "$DETECTED_MODULE_COUNT" -eq 0 ] && [ -d "src" ]; then
    SRC_DIRS=$(ls -d src/*/ 2>/dev/null | xargs -n1 basename | grep -v "test" | grep -v "__" | tr '\n' ',' | sed 's/,$//')
    if [ -n "$SRC_DIRS" ]; then
        DETECTED_MODULES="$SRC_DIRS"
        DETECTED_MODULE_COUNT=$(echo "$DETECTED_MODULES" | tr ',' '\n' | wc -l | tr -d ' ')
    fi
fi

echo "   Modules: $DETECTED_MODULE_COUNT ($DETECTED_MODULES)"
```

### Step 5: Output Summary

```bash
echo ""
echo "   Architecture Detection Summary:"
echo "   - Type: $DETECTED_PROJECT_TYPE"
echo "   - Pattern: $DETECTED_ARCHITECTURE"
echo "   - Files: $DETECTED_FILE_COUNT"
echo "   - Lines: ~$DETECTED_LINE_COUNT"
echo "   - Modules: $DETECTED_MODULE_COUNT"
echo "   - Confidence: $ARCHITECTURE_CONFIDENCE"
```

---

## Important Constraints

- Must handle empty directories gracefully
- Should not count files in ignored directories (node_modules, vendor, etc.)
- Line count is an estimate, not exact
- Module detection is heuristic-based
