# Workflow: Detect Tech Stack

## Purpose

Parse project configuration files to detect languages, frameworks, and databases. Supports Node.js, Rust, Go, and Python projects.

## Outputs

Sets the following variables:
- `DETECTED_LANGUAGE` - Primary language (javascript, typescript, rust, python, go)
- `DETECTED_FRAMEWORK` - Web framework if detected (react, vue, express, fastapi, etc.)
- `DETECTED_BACKEND` - Backend runtime (nodejs, rust, python, go)
- `DETECTED_DATABASE` - Database if detected (postgresql, mongodb, mysql, redis)
- `TECH_STACK_CONFIDENCE` - Confidence score (0.0 - 1.0)

---

## Detection Logic

### Step 1: Detect from package.json (Node.js/JavaScript/TypeScript)

```bash
if [ -f "package.json" ]; then
    echo "   Found package.json - analyzing..."
    
    # Base language
    DETECTED_LANGUAGE="javascript"
    DETECTED_BACKEND="nodejs"
    
    # Check for TypeScript
    if [ -f "tsconfig.json" ] || grep -q '"typescript"' package.json 2>/dev/null; then
        DETECTED_LANGUAGE="typescript"
    fi
    
    # Detect frameworks from dependencies
    DEPS=$(cat package.json)
    
    # Frontend frameworks
    if echo "$DEPS" | grep -q '"react"'; then
        DETECTED_FRAMEWORK="react"
    elif echo "$DEPS" | grep -q '"vue"'; then
        DETECTED_FRAMEWORK="vue"
    elif echo "$DEPS" | grep -q '"angular"'; then
        DETECTED_FRAMEWORK="angular"
    elif echo "$DEPS" | grep -q '"svelte"'; then
        DETECTED_FRAMEWORK="svelte"
    fi
    
    # Meta-frameworks (override if found)
    if echo "$DEPS" | grep -q '"next"'; then
        DETECTED_FRAMEWORK="nextjs"
    elif echo "$DEPS" | grep -q '"nuxt"'; then
        DETECTED_FRAMEWORK="nuxt"
    elif echo "$DEPS" | grep -q '"remix"'; then
        DETECTED_FRAMEWORK="remix"
    fi
    
    # Backend frameworks
    if echo "$DEPS" | grep -q '"express"'; then
        DETECTED_BACKEND="nodejs-express"
    elif echo "$DEPS" | grep -q '"fastify"'; then
        DETECTED_BACKEND="nodejs-fastify"
    elif echo "$DEPS" | grep -q '"koa"'; then
        DETECTED_BACKEND="nodejs-koa"
    elif echo "$DEPS" | grep -q '"hono"'; then
        DETECTED_BACKEND="nodejs-hono"
    fi
    
    # Database detection
    if echo "$DEPS" | grep -qE '"(pg|postgres|postgresql)"'; then
        DETECTED_DATABASE="postgresql"
    elif echo "$DEPS" | grep -qE '"(mysql|mysql2)"'; then
        DETECTED_DATABASE="mysql"
    elif echo "$DEPS" | grep -qE '"(mongodb|mongoose)"'; then
        DETECTED_DATABASE="mongodb"
    elif echo "$DEPS" | grep -q '"redis"'; then
        DETECTED_DATABASE="redis"
    elif echo "$DEPS" | grep -qE '"(prisma|@prisma)"'; then
        # Prisma - check schema for db type
        if [ -f "prisma/schema.prisma" ]; then
            if grep -q 'provider = "postgresql"' prisma/schema.prisma 2>/dev/null; then
                DETECTED_DATABASE="postgresql"
            elif grep -q 'provider = "mysql"' prisma/schema.prisma 2>/dev/null; then
                DETECTED_DATABASE="mysql"
            elif grep -q 'provider = "mongodb"' prisma/schema.prisma 2>/dev/null; then
                DETECTED_DATABASE="mongodb"
            fi
        fi
    fi
    
    TECH_STACK_CONFIDENCE="0.95"
fi
```

### Step 2: Detect from Cargo.toml (Rust)

```bash
if [ -f "Cargo.toml" ]; then
    echo "   Found Cargo.toml - analyzing..."
    
    DETECTED_LANGUAGE="rust"
    DETECTED_BACKEND="rust"
    
    CARGO=$(cat Cargo.toml)
    
    # Web frameworks
    if echo "$CARGO" | grep -q 'actix-web'; then
        DETECTED_FRAMEWORK="actix-web"
    elif echo "$CARGO" | grep -q 'axum'; then
        DETECTED_FRAMEWORK="axum"
    elif echo "$CARGO" | grep -q 'rocket'; then
        DETECTED_FRAMEWORK="rocket"
    elif echo "$CARGO" | grep -q 'warp'; then
        DETECTED_FRAMEWORK="warp"
    fi
    
    # Database
    if echo "$CARGO" | grep -qE '(sqlx|postgres)'; then
        DETECTED_DATABASE="postgresql"
    elif echo "$CARGO" | grep -q 'mysql'; then
        DETECTED_DATABASE="mysql"
    elif echo "$CARGO" | grep -q 'mongodb'; then
        DETECTED_DATABASE="mongodb"
    fi
    
    TECH_STACK_CONFIDENCE="0.95"
fi
```

### Step 3: Detect from go.mod (Go)

```bash
if [ -f "go.mod" ]; then
    echo "   Found go.mod - analyzing..."
    
    DETECTED_LANGUAGE="go"
    DETECTED_BACKEND="go"
    
    GOMOD=$(cat go.mod)
    
    # Web frameworks
    if echo "$GOMOD" | grep -q 'github.com/gin-gonic/gin'; then
        DETECTED_FRAMEWORK="gin"
    elif echo "$GOMOD" | grep -q 'github.com/labstack/echo'; then
        DETECTED_FRAMEWORK="echo"
    elif echo "$GOMOD" | grep -q 'github.com/gofiber/fiber'; then
        DETECTED_FRAMEWORK="fiber"
    elif echo "$GOMOD" | grep -q 'github.com/gorilla/mux'; then
        DETECTED_FRAMEWORK="gorilla-mux"
    fi
    
    # Database
    if echo "$GOMOD" | grep -qE '(lib/pq|jackc/pgx)'; then
        DETECTED_DATABASE="postgresql"
    elif echo "$GOMOD" | grep -q 'go-sql-driver/mysql'; then
        DETECTED_DATABASE="mysql"
    elif echo "$GOMOD" | grep -q 'mongo-driver'; then
        DETECTED_DATABASE="mongodb"
    fi
    
    TECH_STACK_CONFIDENCE="0.95"
fi
```

### Step 4: Detect from Python files

```bash
if [ -f "requirements.txt" ] || [ -f "pyproject.toml" ] || [ -f "setup.py" ]; then
    echo "   Found Python project files - analyzing..."
    
    DETECTED_LANGUAGE="python"
    DETECTED_BACKEND="python"
    
    # Combine all dependency sources
    PYDEPS=""
    [ -f "requirements.txt" ] && PYDEPS="$PYDEPS $(cat requirements.txt)"
    [ -f "pyproject.toml" ] && PYDEPS="$PYDEPS $(cat pyproject.toml)"
    [ -f "setup.py" ] && PYDEPS="$PYDEPS $(cat setup.py)"
    
    # Web frameworks
    if echo "$PYDEPS" | grep -qi 'fastapi'; then
        DETECTED_FRAMEWORK="fastapi"
    elif echo "$PYDEPS" | grep -qi 'django'; then
        DETECTED_FRAMEWORK="django"
    elif echo "$PYDEPS" | grep -qi 'flask'; then
        DETECTED_FRAMEWORK="flask"
    elif echo "$PYDEPS" | grep -qi 'starlette'; then
        DETECTED_FRAMEWORK="starlette"
    fi
    
    # Database
    if echo "$PYDEPS" | grep -qiE '(psycopg|asyncpg|postgres)'; then
        DETECTED_DATABASE="postgresql"
    elif echo "$PYDEPS" | grep -qi 'mysql'; then
        DETECTED_DATABASE="mysql"
    elif echo "$PYDEPS" | grep -qiE '(pymongo|motor)'; then
        DETECTED_DATABASE="mongodb"
    elif echo "$PYDEPS" | grep -qi 'redis'; then
        DETECTED_DATABASE="redis"
    fi
    
    TECH_STACK_CONFIDENCE="0.90"
fi
```

### Step 5: Fallback - Detect from file extensions

```bash
if [ -z "$DETECTED_LANGUAGE" ]; then
    echo "   No config files found - detecting from file extensions..."
    
    # Count files by extension
    TS_COUNT=$(find . -name "*.ts" -o -name "*.tsx" 2>/dev/null | wc -l)
    JS_COUNT=$(find . -name "*.js" -o -name "*.jsx" 2>/dev/null | wc -l)
    RS_COUNT=$(find . -name "*.rs" 2>/dev/null | wc -l)
    GO_COUNT=$(find . -name "*.go" 2>/dev/null | wc -l)
    PY_COUNT=$(find . -name "*.py" 2>/dev/null | wc -l)
    
    # Pick the most common
    MAX_COUNT=0
    if [ "$TS_COUNT" -gt "$MAX_COUNT" ] 2>/dev/null; then
        MAX_COUNT=$TS_COUNT
        DETECTED_LANGUAGE="typescript"
        DETECTED_BACKEND="nodejs"
    fi
    if [ "$JS_COUNT" -gt "$MAX_COUNT" ] 2>/dev/null; then
        MAX_COUNT=$JS_COUNT
        DETECTED_LANGUAGE="javascript"
        DETECTED_BACKEND="nodejs"
    fi
    if [ "$RS_COUNT" -gt "$MAX_COUNT" ] 2>/dev/null; then
        MAX_COUNT=$RS_COUNT
        DETECTED_LANGUAGE="rust"
        DETECTED_BACKEND="rust"
    fi
    if [ "$GO_COUNT" -gt "$MAX_COUNT" ] 2>/dev/null; then
        MAX_COUNT=$GO_COUNT
        DETECTED_LANGUAGE="go"
        DETECTED_BACKEND="go"
    fi
    if [ "$PY_COUNT" -gt "$MAX_COUNT" ] 2>/dev/null; then
        MAX_COUNT=$PY_COUNT
        DETECTED_LANGUAGE="python"
        DETECTED_BACKEND="python"
    fi
    
    TECH_STACK_CONFIDENCE="0.60"
fi
```

---

## Important Constraints

- Must not fail if config files are missing
- Must handle malformed JSON/TOML gracefully
- Should detect the MOST SPECIFIC framework possible
- Confidence score reflects detection certainty
