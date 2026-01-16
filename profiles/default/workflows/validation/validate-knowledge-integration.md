# Validate Knowledge Integration

## Core Responsibilities

1. **Check Knowledge Cache Directory**: Verify cache directory exists
2. **Check basepoints-knowledge.md**: Verify file exists and has content
3. **Check detected-layer.txt**: Verify layer detection ran
4. **Verify Knowledge Usage**: Check if output references extracted knowledge
5. **Return Status**: Warning if knowledge not used (not failure)

## Workflow

### Step 1: Define Paths

```bash
# SPEC_PATH should be set by the calling workflow
if [ -z "$SPEC_PATH" ]; then
    echo "❌ SPEC_PATH not set"
    exit 1
fi

echo "🔍 Validating knowledge integration..."

CACHE_PATH="$SPEC_PATH/implementation/cache"
```

### Step 2: Check Knowledge Cache Directory

```bash
echo ""
echo "📁 Checking knowledge cache..."
echo ""

ERRORS=0
WARNINGS=0

if [ -d "$CACHE_PATH" ]; then
    echo "  ✅ Cache directory exists: $CACHE_PATH"
else
    echo "  ⚠️ Cache directory missing: $CACHE_PATH"
    WARNINGS=$((WARNINGS + 1))
fi
```

### Step 3: Check basepoints-knowledge.md

```bash
KNOWLEDGE_FILE="$CACHE_PATH/basepoints-knowledge.md"

if [ -f "$KNOWLEDGE_FILE" ]; then
    KNOWLEDGE_SIZE=$(wc -c < "$KNOWLEDGE_FILE" | tr -d ' ')
    KNOWLEDGE_LINES=$(wc -l < "$KNOWLEDGE_FILE" | tr -d ' ')
    
    if [ "$KNOWLEDGE_SIZE" -gt 100 ]; then
        echo "  ✅ Knowledge file exists: $KNOWLEDGE_SIZE bytes, $KNOWLEDGE_LINES lines"
        HAS_KNOWLEDGE="true"
    else
        echo "  ⚠️ Knowledge file minimal: $KNOWLEDGE_SIZE bytes"
        WARNINGS=$((WARNINGS + 1))
        HAS_KNOWLEDGE="partial"
    fi
else
    echo "  ⚠️ Knowledge file missing: basepoints-knowledge.md"
    WARNINGS=$((WARNINGS + 1))
    HAS_KNOWLEDGE="false"
fi
```

### Step 4: Check detected-layer.txt

```bash
LAYER_FILE="$CACHE_PATH/detected-layer.txt"

if [ -f "$LAYER_FILE" ]; then
    DETECTED_LAYER=$(cat "$LAYER_FILE")
    if [ -n "$DETECTED_LAYER" ] && [ "$DETECTED_LAYER" != "unknown" ]; then
        echo "  ✅ Layer detected: $DETECTED_LAYER"
        HAS_LAYER="true"
    else
        echo "  ⚠️ Layer detection inconclusive: $DETECTED_LAYER"
        WARNINGS=$((WARNINGS + 1))
        HAS_LAYER="partial"
    fi
else
    echo "  ⚠️ Layer file missing: detected-layer.txt"
    WARNINGS=$((WARNINGS + 1))
    HAS_LAYER="false"
fi
```

### Step 5: Verify Knowledge Usage in Output

```bash
echo ""
echo "📋 Checking knowledge usage in outputs..."
echo ""

KNOWLEDGE_USED="false"

# Check if spec.md references basepoints or patterns
if [ -f "$SPEC_PATH/spec.md" ]; then
    if grep -qi "basepoint\|pattern\|extracted\|knowledge" "$SPEC_PATH/spec.md"; then
        echo "  ✅ spec.md references extracted knowledge"
        KNOWLEDGE_USED="true"
    else
        echo "  ⚠️ spec.md may not reference extracted knowledge"
        WARNINGS=$((WARNINGS + 1))
    fi
fi

# Check if tasks.md references patterns
if [ -f "$SPEC_PATH/tasks.md" ]; then
    if grep -qi "pattern\|basepoint\|layer\|standard" "$SPEC_PATH/tasks.md"; then
        echo "  ✅ tasks.md references extracted knowledge"
        KNOWLEDGE_USED="true"
    else
        echo "  ⚠️ tasks.md may not reference extracted knowledge"
        WARNINGS=$((WARNINGS + 1))
    fi
fi

# Check resources-consulted.md
if [ -f "$CACHE_PATH/resources-consulted.md" ]; then
    echo "  ✅ Resources checklist exists"
else
    echo "  ⚠️ Resources checklist missing"
    WARNINGS=$((WARNINGS + 1))
fi
```

### Step 6: Generate Output and Status

```bash
echo ""
echo "═══════════════════════════════════════════════════════"
echo "  KNOWLEDGE INTEGRATION VALIDATION RESULTS"
echo "═══════════════════════════════════════════════════════"
echo ""
echo "  Spec Path: $SPEC_PATH"
echo ""
echo "  Knowledge File: $HAS_KNOWLEDGE"
echo "  Layer Detection: $HAS_LAYER"
echo "  Knowledge Used: $KNOWLEDGE_USED"
echo "  Warnings: $WARNINGS"
echo ""

# Knowledge integration is a warning, not a failure
# Commands can work without basepoints, just less effectively
if [ "$WARNINGS" -eq 0 ]; then
    echo "  Status: ✅ PASSED"
    VALIDATION_STATUS="PASSED"
    EXIT_CODE=0
else
    echo "  Status: ⚠️ PASSED WITH WARNINGS ($WARNINGS warnings)"
    VALIDATION_STATUS="PASSED_WITH_WARNINGS"
    EXIT_CODE=0  # Not a failure, just a warning
fi

echo ""
echo "═══════════════════════════════════════════════════════"

# Store result for validation report
cat > "$CACHE_PATH/knowledge-integration-validation.txt" << EOF
VALIDATOR=knowledge-integration
TIMESTAMP=$(date +%s)
HAS_KNOWLEDGE=$HAS_KNOWLEDGE
HAS_LAYER=$HAS_LAYER
KNOWLEDGE_USED=$KNOWLEDGE_USED
WARNINGS=$WARNINGS
STATUS=$VALIDATION_STATUS
EOF

# Export and exit
export KNOWLEDGE_INTEGRATION_STATUS="$VALIDATION_STATUS"
export KNOWLEDGE_INTEGRATION_WARNINGS="$WARNINGS"
exit $EXIT_CODE
```

## Important Constraints

- Must check knowledge cache population
- Must verify layer detection ran
- Must check if outputs reference extracted knowledge
- **Warning** if knowledge not used, NOT failure (graceful degradation)
- Works for any project (project-agnostic)
- **CRITICAL**: This is a core validator that runs for ALL projects
