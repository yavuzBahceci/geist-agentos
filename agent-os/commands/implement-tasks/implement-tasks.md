Now that we have a spec and tasks list ready for implementation, we will proceed with implementation of this spec by following this multi-phase process:

PHASE 1: Determine which task group(s) from tasks.md should be implemented
PHASE 2: Implement the given task(s)
PHASE 3: After ALL task groups have been implemented, produce the final verification report.

Carefully read and execute the instructions in the following files IN SEQUENCE, following their numbered file names.  Only proceed to the next numbered instruction file once the previous numbered instruction has been executed.

Instructions to follow in sequence:

# PHASE 1: Determine Tasks

First, check if the user has already provided instructions about which task group(s) to implement.

**If the user HAS provided instructions:** Proceed to PHASE 2 to delegate implementation of those specified task group(s) to the **implementer** subagent.

**If the user has NOT provided instructions:**

Read `agent-os/specs/[this-spec]/tasks.md` to review the available task groups, then output the following message to the user and WAIT for their response:

```
Should we proceed with implementation of all task groups in tasks.md?

If not, then please specify which task(s) to implement.
```

# PHASE 2: Implement Tasks

Now that you have the task group(s) to be implemented, proceed with implementation.

## Step 1: Extract Basepoints Knowledge (if basepoints exist)

Before implementing tasks, extract relevant basepoints knowledge to inform implementation:

```bash
[38;2;255;185;0m⚠️  Circular workflow reference detected: common/extract-basepoints-with-scope-detection[0m
# Extract Basepoints Knowledge with Scope Detection

## Core Responsibilities

1. **Check Basepoints Availability**: Verify that basepoints exist before attempting extraction
2. **Determine Spec Context**: Establish the spec path for caching extracted knowledge
3. **Extract Basepoints Knowledge**: Automatically extract knowledge from all basepoint files
4. **Detect Abstraction Layer**: Identify which abstraction layer the spec targets
5. **Perform Scope Detection**: Run semantic analysis and keyword matching to narrow scope
6. **Load Extracted Knowledge**: Load cached knowledge files for use in command execution
7. **Load Detected Layer**: Load detected abstraction layer information

## Workflow

### Step 1: Check if Basepoints Exist

Before attempting extraction, verify that basepoints are available:

```bash
# Check if basepoints exist
if [ -d "agent-os/basepoints" ] && [ -f "agent-os/basepoints/headquarter.md" ]; then
    BASEPOINTS_AVAILABLE="true"
else
    BASEPOINTS_AVAILABLE="false"
    echo "⚠️ Basepoints not found. Continuing without basepoints knowledge."
    # Exit early - this workflow requires basepoints
    return 0
fi
```

### Step 2: Determine Spec Path

Establish the spec path for caching extracted knowledge:

```bash
# Determine spec path
SPEC_PATH="agent-os/specs/[current-spec]"
```

### Step 3: Extract Basepoints Knowledge Automatically

Extract knowledge from all basepoint files using the automatic extraction workflow:

```bash
# Extract basepoints knowledge using scope detection
# Automatic Basepoints Knowledge Extraction

## Core Responsibilities

1. **Read All Basepoint Files**: Traverse basepoints folder structure and read all basepoint files
2. **Extract Same-Layer Patterns**: Extract patterns specific to each abstraction layer
3. **Extract Cross-Layer Patterns**: Identify patterns spanning multiple abstraction layers
4. **Extract All Knowledge Categories**: Extract standards, flows, strategies, testing approaches, pros/cons, historical decisions
5. **Organize Knowledge**: Structure extracted knowledge by category and abstraction layer
6. **Cache Knowledge**: Store extracted knowledge for use during command execution

## Workflow

### Step 1: Validate Basepoints Existence

Check if basepoints folder exists and contains basepoint files:

```bash
# Define paths
BASEPOINTS_PATH="agent-os/basepoints"
BASEPOINT_FILE_PATTERN="agent-base-*.md"

# Check if basepoints folder exists
if [ ! -d "$BASEPOINTS_PATH" ]; then
    echo "⚠️ Basepoints folder not found at $BASEPOINTS_PATH"
    echo "Continuing without basepoints knowledge. Run /create-basepoints to generate basepoints."
    # Set flag for graceful fallback
    BASEPOINTS_AVAILABLE="false"
else
    BASEPOINTS_AVAILABLE="true"
    
    # Check for headquarter.md
    if [ ! -f "$BASEPOINTS_PATH/headquarter.md" ]; then
        echo "⚠️ Warning: headquarter.md not found. Continuing with module basepoints only."
        HAS_HEADQUARTER="false"
    else
        HAS_HEADQUARTER="true"
        echo "✅ Found headquarter.md"
    fi
    
    # Count basepoint files
    BASEPOINT_COUNT=$(find "$BASEPOINTS_PATH" -name "$BASEPOINT_FILE_PATTERN" -type f 2>/dev/null | wc -l | tr -d ' ')
    if [ "$BASEPOINT_COUNT" -eq 0 ]; then
        echo "⚠️ No module basepoint files found. Using headquarter.md only."
    else
        echo "✅ Found $BASEPOINT_COUNT basepoint file(s)"
    fi
fi
```

**Graceful Fallback**: If basepoints don't exist, continue without knowledge extraction. Commands should still function, just without basepoints-derived context.

### Step 2: Determine Cache Location

Set up the cache path based on context:

```bash
# Determine spec path from context
# SPEC_PATH should be set by the calling command
if [ -n "$SPEC_PATH" ]; then
    CACHE_PATH="$SPEC_PATH/implementation/cache"
else
    # Fallback for non-spec contexts (e.g., deploy-agents)
    CACHE_PATH="agent-os/output/basepoints-extraction"
fi

# Create cache directory
mkdir -p "$CACHE_PATH"
echo "📁 Cache location: $CACHE_PATH"
```

### Step 3: Extract Abstraction Layers from Headquarter

If headquarter.md exists, extract the documented abstraction layers:

```bash
if [ "$HAS_HEADQUARTER" = "true" ]; then
    echo "📖 Extracting abstraction layers from headquarter.md..."
    
    # Read headquarter content
    HEADQUARTER_CONTENT=$(cat "$BASEPOINTS_PATH/headquarter.md")
    
    # Extract layers from "Detected Abstraction Layers" or "Architecture Overview" section
    # Look for table rows with layer names or bullet points
    ABSTRACTION_LAYERS=$(echo "$HEADQUARTER_CONTENT" | grep -E "^\| \*\*[A-Z]+\*\*|^- \*\*[A-Z]+\*\*" | sed 's/.*\*\*\([A-Z_]*\)\*\*.*/\1/' | tr '\n' ',' | sed 's/,$//')
    
    if [ -z "$ABSTRACTION_LAYERS" ]; then
        # Fallback: try to find layer mentions in headers
        ABSTRACTION_LAYERS=$(echo "$HEADQUARTER_CONTENT" | grep -E "^### Layer:" | sed 's/### Layer: //' | tr '\n' ',' | sed 's/,$//')
    fi
    
    if [ -z "$ABSTRACTION_LAYERS" ]; then
        # Default layers if none detected
        ABSTRACTION_LAYERS="ROOT,PROFILES,SCRIPTS"
    fi
    
    echo "✅ Detected layers: $ABSTRACTION_LAYERS"
    
    # Save detected layers
    echo "$ABSTRACTION_LAYERS" > "$CACHE_PATH/detected-layer.txt"
fi
```

### Step 4: Read and Extract from Headquarter.md

Extract top-level architecture and patterns from headquarter:

```bash
if [ "$HAS_HEADQUARTER" = "true" ]; then
    echo "📖 Extracting knowledge from headquarter.md..."
    
    # Initialize knowledge sections
    HQ_PATTERNS=""
    HQ_STANDARDS=""
    HQ_FLOWS=""
    HQ_STRATEGIES=""
    
    # Extract Architecture Patterns section
    HQ_PATTERNS=$(echo "$HEADQUARTER_CONTENT" | sed -n '/## Architecture Patterns/,/^## /p' | head -n -1)
    if [ -z "$HQ_PATTERNS" ]; then
        HQ_PATTERNS=$(echo "$HEADQUARTER_CONTENT" | sed -n '/### Core Patterns/,/^### /p' | head -n -1)
    fi
    
    # Extract Standards section
    HQ_STANDARDS=$(echo "$HEADQUARTER_CONTENT" | sed -n '/## Standards/,/^## /p' | head -n -1)
    if [ -z "$HQ_STANDARDS" ]; then
        HQ_STANDARDS=$(echo "$HEADQUARTER_CONTENT" | sed -n '/### Naming Conventions/,/^### /p' | head -n -1)
    fi
    
    # Extract Workflow Patterns / Flows
    HQ_FLOWS=$(echo "$HEADQUARTER_CONTENT" | sed -n '/## Development Workflow/,/^## /p' | head -n -1)
    if [ -z "$HQ_FLOWS" ]; then
        HQ_FLOWS=$(echo "$HEADQUARTER_CONTENT" | sed -n '/### Workflow Patterns/,/^### /p' | head -n -1)
    fi
    
    # Extract Strategies
    HQ_STRATEGIES=$(echo "$HEADQUARTER_CONTENT" | sed -n '/## Testing Strategy/,/^## /p' | head -n -1)
    
    echo "✅ Extracted from headquarter: patterns, standards, flows, strategies"
fi
```

### Step 5: Read All Module Basepoints

Traverse the basepoints folder and extract knowledge from each module:

```bash
if [ "$BASEPOINTS_AVAILABLE" = "true" ]; then
    echo "📖 Reading module basepoints..."
    
    # Initialize collections
    ALL_PATTERNS=""
    ALL_STANDARDS=""
    ALL_FLOWS=""
    ALL_STRATEGIES=""
    ALL_TESTING=""
    ALL_RELATED=""
    
    # Find all basepoint files
    find "$BASEPOINTS_PATH" -name "$BASEPOINT_FILE_PATTERN" -type f | sort | while read basepoint_file; do
        if [ -z "$basepoint_file" ]; then
            continue
        fi
        
        echo "  📄 Reading: $basepoint_file"
        
        # Determine module layer from path
        MODULE_PATH=$(dirname "$basepoint_file" | sed "s|$BASEPOINTS_PATH/||")
        MODULE_NAME=$(basename "$basepoint_file" .md | sed 's/agent-base-//')
        
        # Read content
        MODULE_CONTENT=$(cat "$basepoint_file")
        
        # Extract Patterns section
        PATTERNS=$(echo "$MODULE_CONTENT" | sed -n '/## Patterns/,/^## /p' | head -n -1)
        if [ -n "$PATTERNS" ]; then
            ALL_PATTERNS="${ALL_PATTERNS}

### From: $MODULE_PATH ($MODULE_NAME)
$PATTERNS"
        fi
        
        # Extract Standards section
        STANDARDS=$(echo "$MODULE_CONTENT" | sed -n '/## Standards/,/^## /p' | head -n -1)
        if [ -n "$STANDARDS" ]; then
            ALL_STANDARDS="${ALL_STANDARDS}

### From: $MODULE_PATH ($MODULE_NAME)
$STANDARDS"
        fi
        
        # Extract Flows section
        FLOWS=$(echo "$MODULE_CONTENT" | sed -n '/## Flows/,/^## /p' | head -n -1)
        if [ -n "$FLOWS" ]; then
            ALL_FLOWS="${ALL_FLOWS}

### From: $MODULE_PATH ($MODULE_NAME)
$FLOWS"
        fi
        
        # Extract Strategies section
        STRATEGIES=$(echo "$MODULE_CONTENT" | sed -n '/## Strategies/,/^## /p' | head -n -1)
        if [ -n "$STRATEGIES" ]; then
            ALL_STRATEGIES="${ALL_STRATEGIES}

### From: $MODULE_PATH ($MODULE_NAME)
$STRATEGIES"
        fi
        
        # Extract Testing section
        TESTING=$(echo "$MODULE_CONTENT" | sed -n '/## Testing/,/^## /p' | head -n -1)
        if [ -n "$TESTING" ]; then
            ALL_TESTING="${ALL_TESTING}

### From: $MODULE_PATH ($MODULE_NAME)
$TESTING"
        fi
        
        # Extract Related Modules section
        RELATED=$(echo "$MODULE_CONTENT" | sed -n '/## Related/,/^## /p' | head -n -1)
        if [ -n "$RELATED" ]; then
            ALL_RELATED="${ALL_RELATED}

### From: $MODULE_PATH ($MODULE_NAME)
$RELATED"
        fi
    done
fi
```

### Step 6: Compile and Cache Knowledge

Generate the basepoints-knowledge.md file:

```bash
echo "📝 Compiling extracted knowledge..."

# Create the comprehensive knowledge document
cat > "$CACHE_PATH/basepoints-knowledge.md" << 'KNOWLEDGE_EOF'
# Extracted Basepoints Knowledge

## Extraction Metadata
- **Extracted**: $(date)
- **Source**: agent-os/basepoints/
- **Basepoints Available**: $BASEPOINTS_AVAILABLE

---

## Detected Abstraction Layers

$ABSTRACTION_LAYERS

---

## Patterns

### From Headquarter (Cross-Layer)
$HQ_PATTERNS

### From Module Basepoints (Same-Layer)
$ALL_PATTERNS

---

## Standards

### Global Standards (from Headquarter)
$HQ_STANDARDS

### Module-Specific Standards
$ALL_STANDARDS

---

## Flows

### Project-Level Flows (from Headquarter)
$HQ_FLOWS

### Module-Level Flows
$ALL_FLOWS

---

## Strategies

### Global Strategies
$HQ_STRATEGIES

### Module-Specific Strategies
$ALL_STRATEGIES

---

## Testing Approaches

$ALL_TESTING

---

## Related Modules and Dependencies

$ALL_RELATED

---

*Knowledge extracted automatically from basepoints.*
KNOWLEDGE_EOF

echo "✅ Knowledge cached to: $CACHE_PATH/basepoints-knowledge.md"
```

### Step 7: Generate Resources Checklist

Track which resources were consulted:

```bash
echo "📋 Generating resources checklist..."

cat > "$CACHE_PATH/resources-consulted.md" << 'RESOURCES_EOF'
# Resources Consulted

## Basepoints
RESOURCES_EOF

if [ "$HAS_HEADQUARTER" = "true" ]; then
    echo "- [x] \`$BASEPOINTS_PATH/headquarter.md\`" >> "$CACHE_PATH/resources-consulted.md"
else
    echo "- [ ] \`$BASEPOINTS_PATH/headquarter.md\` (not found)" >> "$CACHE_PATH/resources-consulted.md"
fi

# List all module basepoints consulted
find "$BASEPOINTS_PATH" -name "$BASEPOINT_FILE_PATTERN" -type f 2>/dev/null | sort | while read f; do
    echo "- [x] \`$f\`" >> "$CACHE_PATH/resources-consulted.md"
done

cat >> "$CACHE_PATH/resources-consulted.md" << 'RESOURCES_EOF'

## Product Files
- [ ] `agent-os/product/mission.md` (check separately)
- [ ] `agent-os/product/roadmap.md` (check separately)
- [ ] `agent-os/product/tech-stack.md` (check separately)

## Standards
- [ ] `agent-os/standards/global/*.md` (check separately)

---

*Generated: $(date)*
RESOURCES_EOF

echo "✅ Resources checklist generated"
```

### Step 8: Return Status

Provide summary of extraction:

```bash
echo ""
echo "═══════════════════════════════════════════════════════"
echo "  BASEPOINTS KNOWLEDGE EXTRACTION COMPLETE"
echo "═══════════════════════════════════════════════════════"
echo ""
echo "  Basepoints Available: $BASEPOINTS_AVAILABLE"
echo "  Headquarter Found: $HAS_HEADQUARTER"
echo "  Module Basepoints: $BASEPOINT_COUNT"
echo "  Detected Layers: $ABSTRACTION_LAYERS"
echo ""
echo "  Cache Location: $CACHE_PATH"
echo "  Files Created:"
echo "    - basepoints-knowledge.md"
echo "    - detected-layer.txt"
echo "    - resources-consulted.md"
echo ""
echo "═══════════════════════════════════════════════════════"
```

## Graceful Fallback Behavior

When basepoints don't exist or are incomplete:

1. **No basepoints folder**: Set `BASEPOINTS_AVAILABLE=false`, continue without knowledge
2. **No headquarter.md**: Use module basepoints only, default layer names
3. **No module basepoints**: Use headquarter.md only
4. **Empty sections**: Skip empty sections in output

Commands should check `BASEPOINTS_AVAILABLE` flag and adjust behavior accordingly.

## Important Constraints

- Must read all basepoint files including headquarter.md and all module-specific files
- Must extract patterns, standards, flows, strategies, testing approaches
- Must organize knowledge by abstraction layer and category
- Must preserve source information (which basepoint file each piece of knowledge came from)
- Must cache extracted knowledge to `$SPEC_PATH/implementation/cache/`
- Must provide graceful fallback when basepoints don't exist
- Must be technology-agnostic and work with any basepoint structure
- **CRITICAL**: All cached documents must be stored in `$SPEC_PATH/implementation/cache/` when running within a spec command

```

### Step 4: Detect Abstraction Layer

Identify which abstraction layer this spec targets:

```bash
# Detect abstraction layer
# Detect Abstraction Layer

## Core Responsibilities

1. **Analyze Feature Description**: Parse feature/spec description for layer indicators
2. **Match Against Documented Layers**: Compare against layers from headquarter.md
3. **Detect Primary Layer**: Determine the primary abstraction layer for the scope
4. **Store Detection Result**: Save detected layer for use by other workflows
5. **Provide Default Behavior**: Default to "unknown" if detection fails

## Workflow

### Step 1: Load Feature/Spec Description

```bash
# SPEC_PATH should be set by the calling command
if [ -z "$SPEC_PATH" ]; then
    echo "⚠️ SPEC_PATH not set. Using FEATURE_DESCRIPTION if available."
fi

echo "🎯 Detecting abstraction layer..."

# Gather text to analyze
ANALYSIS_TEXT=""

# Check feature description first
if [ -n "$FEATURE_DESCRIPTION" ]; then
    ANALYSIS_TEXT="$FEATURE_DESCRIPTION"
fi

# Add spec documents if available
if [ -n "$SPEC_PATH" ]; then
    if [ -f "$SPEC_PATH/planning/initialization.md" ]; then
        ANALYSIS_TEXT="${ANALYSIS_TEXT}
$(cat "$SPEC_PATH/planning/initialization.md")"
    fi
    
    if [ -f "$SPEC_PATH/planning/requirements.md" ]; then
        ANALYSIS_TEXT="${ANALYSIS_TEXT}
$(head -100 "$SPEC_PATH/planning/requirements.md")"
    fi
    
    if [ -f "$SPEC_PATH/spec.md" ]; then
        ANALYSIS_TEXT="${ANALYSIS_TEXT}
$(head -100 "$SPEC_PATH/spec.md")"
    fi
fi

if [ -z "$ANALYSIS_TEXT" ]; then
    echo "⚠️ No text to analyze. Defaulting to unknown."
    DETECTED_LAYER="unknown"
else
    echo "   Text to analyze: $(echo "$ANALYSIS_TEXT" | wc -w | tr -d ' ') words"
fi
```

### Step 2: Load Documented Layers from Headquarter

```bash
BASEPOINTS_PATH="agent-os/basepoints"
DOCUMENTED_LAYERS=""

if [ -f "$BASEPOINTS_PATH/headquarter.md" ]; then
    echo "📖 Loading layers from headquarter.md..."
    
    # Extract layer names from headquarter
    # Look for patterns like "**ROOT**", "**PROFILES**", etc.
    DOCUMENTED_LAYERS=$(grep -oE '\*\*[A-Z_]+\*\*' "$BASEPOINTS_PATH/headquarter.md" | \
        sed 's/\*//g' | sort -u | tr '\n' ' ')
    
    if [ -z "$DOCUMENTED_LAYERS" ]; then
        # Try alternative patterns
        DOCUMENTED_LAYERS=$(grep -E "^### Layer:" "$BASEPOINTS_PATH/headquarter.md" | \
            sed 's/### Layer: //' | tr '\n' ' ')
    fi
fi

# Default layers if none found
if [ -z "$DOCUMENTED_LAYERS" ]; then
    DOCUMENTED_LAYERS="ROOT PROFILES SCRIPTS"
    echo "   Using default layers: $DOCUMENTED_LAYERS"
else
    echo "   Documented layers: $DOCUMENTED_LAYERS"
fi
```

### Step 3: Define Layer Indicator Keywords

```bash
# Define keywords that indicate each layer
# These are specific to Geist/Agent OS architecture

# ROOT layer indicators
ROOT_KEYWORDS="readme|manifest|changelog|config|license|documentation|root|base|top-level|project|repository"

# PROFILES layer indicators  
PROFILES_KEYWORDS="profile|command|workflow|agent|standard|template|spec|shape|write|create|implement|orchestrate|validation|basepoint|knowledge|extraction|scope|detection"

# SCRIPTS layer indicators
SCRIPTS_KEYWORDS="script|install|update|shell|bash|function|utility|helper|common|project-install|project-update"

echo "   Layer indicators defined"
```

### Step 4: Score Each Layer

```bash
echo "📊 Scoring layers..."

# Convert text to lowercase for matching
ANALYSIS_LOWER=$(echo "$ANALYSIS_TEXT" | tr '[:upper:]' '[:lower:]')

# Score ROOT layer
ROOT_SCORE=0
for keyword in $(echo "$ROOT_KEYWORDS" | tr '|' ' '); do
    MATCHES=$(echo "$ANALYSIS_LOWER" | grep -oE "$keyword" | wc -l | tr -d ' ')
    ROOT_SCORE=$((ROOT_SCORE + MATCHES))
done

# Score PROFILES layer
PROFILES_SCORE=0
for keyword in $(echo "$PROFILES_KEYWORDS" | tr '|' ' '); do
    MATCHES=$(echo "$ANALYSIS_LOWER" | grep -oE "$keyword" | wc -l | tr -d ' ')
    PROFILES_SCORE=$((PROFILES_SCORE + MATCHES))
done

# Score SCRIPTS layer
SCRIPTS_SCORE=0
for keyword in $(echo "$SCRIPTS_KEYWORDS" | tr '|' ' '); do
    MATCHES=$(echo "$ANALYSIS_LOWER" | grep -oE "$keyword" | wc -l | tr -d ' ')
    SCRIPTS_SCORE=$((SCRIPTS_SCORE + MATCHES))
done

# Add bonus for explicit layer mentions
if echo "$ANALYSIS_TEXT" | grep -qi "ROOT"; then
    ROOT_SCORE=$((ROOT_SCORE + 10))
fi
if echo "$ANALYSIS_TEXT" | grep -qi "PROFILES\|profiles/default"; then
    PROFILES_SCORE=$((PROFILES_SCORE + 10))
fi
if echo "$ANALYSIS_TEXT" | grep -qi "SCRIPTS\|scripts/"; then
    SCRIPTS_SCORE=$((SCRIPTS_SCORE + 10))
fi

echo "   ROOT score: $ROOT_SCORE"
echo "   PROFILES score: $PROFILES_SCORE"  
echo "   SCRIPTS score: $SCRIPTS_SCORE"
```

### Step 5: Determine Primary Layer

```bash
echo "🎯 Determining primary layer..."

# Find highest scoring layer
DETECTED_LAYER="unknown"
HIGHEST_SCORE=0

if [ "$ROOT_SCORE" -gt "$HIGHEST_SCORE" ]; then
    HIGHEST_SCORE=$ROOT_SCORE
    DETECTED_LAYER="ROOT"
fi

if [ "$PROFILES_SCORE" -gt "$HIGHEST_SCORE" ]; then
    HIGHEST_SCORE=$PROFILES_SCORE
    DETECTED_LAYER="PROFILES"
fi

if [ "$SCRIPTS_SCORE" -gt "$HIGHEST_SCORE" ]; then
    HIGHEST_SCORE=$SCRIPTS_SCORE
    DETECTED_LAYER="SCRIPTS"
fi

# If no clear winner (all scores = 0), default behavior
if [ "$HIGHEST_SCORE" -eq 0 ]; then
    echo "   No clear layer detected. Defaulting to 'unknown'."
    DETECTED_LAYER="unknown"
    NEEDS_HUMAN_REVIEW="true"
else
    echo "   Primary layer: $DETECTED_LAYER (score: $HIGHEST_SCORE)"
    NEEDS_HUMAN_REVIEW="false"
fi

# Determine secondary layers (scores > 0 but not highest)
SECONDARY_LAYERS=""
if [ "$ROOT_SCORE" -gt 0 ] && [ "$DETECTED_LAYER" != "ROOT" ]; then
    SECONDARY_LAYERS="${SECONDARY_LAYERS} ROOT"
fi
if [ "$PROFILES_SCORE" -gt 0 ] && [ "$DETECTED_LAYER" != "PROFILES" ]; then
    SECONDARY_LAYERS="${SECONDARY_LAYERS} PROFILES"
fi
if [ "$SCRIPTS_SCORE" -gt 0 ] && [ "$DETECTED_LAYER" != "SCRIPTS" ]; then
    SECONDARY_LAYERS="${SECONDARY_LAYERS} SCRIPTS"
fi

if [ -n "$SECONDARY_LAYERS" ]; then
    echo "   Secondary layers:$SECONDARY_LAYERS"
fi
```

### Step 6: Store Detection Result

```bash
echo "💾 Storing detection result..."

# Determine cache path
if [ -n "$SPEC_PATH" ]; then
    CACHE_PATH="$SPEC_PATH/implementation/cache"
else
    CACHE_PATH="agent-os/output/layer-detection"
fi

mkdir -p "$CACHE_PATH"

# Store detected layer (simple text file for easy consumption)
echo "$DETECTED_LAYER" > "$CACHE_PATH/detected-layer.txt"

# Store detailed detection report
cat > "$CACHE_PATH/layer-detection-report.md" << LAYER_EOF
# Abstraction Layer Detection Report

**Detected**: $(date)
**Primary Layer**: $DETECTED_LAYER
**Confidence**: $([ "$HIGHEST_SCORE" -gt 5 ] && echo "High" || echo "Low")
**Needs Human Review**: $NEEDS_HUMAN_REVIEW

## Layer Scores

| Layer | Score | Status |
|-------|-------|--------|
| ROOT | $ROOT_SCORE | $([ "$DETECTED_LAYER" = "ROOT" ] && echo "✅ Primary" || echo "-") |
| PROFILES | $PROFILES_SCORE | $([ "$DETECTED_LAYER" = "PROFILES" ] && echo "✅ Primary" || echo "-") |
| SCRIPTS | $SCRIPTS_SCORE | $([ "$DETECTED_LAYER" = "SCRIPTS" ] && echo "✅ Primary" || echo "-") |

## Secondary Layers

$([ -n "$SECONDARY_LAYERS" ] && echo "$SECONDARY_LAYERS" | tr ' ' '\n' | sed 's/^/- /' || echo "None")

## Layer Descriptions

- **ROOT**: Top-level documentation, configuration, and project-wide files
- **PROFILES**: Templates for commands, workflows, agents, and standards
- **SCRIPTS**: Shell scripts for installation, updates, and utilities

## Detection Method

Keywords were extracted from:
- Feature description
- Spec initialization
- Requirements document
- Spec document

Each layer's score is based on keyword frequency matching.

---

*If "Needs Human Review" is true, please verify the detected layer.*
LAYER_EOF

echo "✅ Layer detection stored:"
echo "   - $CACHE_PATH/detected-layer.txt"
echo "   - $CACHE_PATH/layer-detection-report.md"
```

### Step 7: Return Result

```bash
echo ""
echo "═══════════════════════════════════════════════════════"
echo "  ABSTRACTION LAYER DETECTION COMPLETE"
echo "═══════════════════════════════════════════════════════"
echo ""
echo "  Primary Layer: $DETECTED_LAYER"
echo "  Confidence: $([ "$HIGHEST_SCORE" -gt 5 ] && echo "High ($HIGHEST_SCORE)" || echo "Low ($HIGHEST_SCORE)")"
echo "  Secondary Layers:${SECONDARY_LAYERS:- none}"
echo "  Needs Review: $NEEDS_HUMAN_REVIEW"
echo ""
echo "═══════════════════════════════════════════════════════"

# Export for use by other workflows
export DETECTED_LAYER="$DETECTED_LAYER"
export LAYER_CONFIDENCE="$HIGHEST_SCORE"
export NEEDS_HUMAN_REVIEW="$NEEDS_HUMAN_REVIEW"

# Return the detected layer
echo "$DETECTED_LAYER"
```

## Default Behavior

When detection fails or confidence is low:

1. **Default Layer**: "unknown"
2. **Human Review Flag**: Set to `true`
3. **All Basepoints**: When layer is "unknown", all basepoints are considered relevant
4. **Warning Message**: Inform user that manual layer specification may be needed

## Important Constraints

- Must analyze feature/spec description for layer indicator keywords
- Must match against documented layers from headquarter.md
- Must provide scoring to indicate confidence
- Must default to "unknown" when detection fails
- Must flag for human review when confidence is low
- Must store detection result in `$SPEC_PATH/implementation/cache/detected-layer.txt`
- Must be technology-agnostic but include sensible defaults for Geist projects
- **CRITICAL**: Detection result is used by knowledge extraction to filter relevant basepoints

```

### Step 5: Perform Scope Detection

Run scope detection workflows to narrow the context:

```bash
# Perform scope semantic analysis
# Semantic Analysis for Scope Detection

## Core Responsibilities

1. **Analyze Spec Requirements/Description**: Extract semantic meaning from spec text
2. **Extract Key Concepts**: Identify entities, relationships, and domain concepts
3. **Map to Abstraction Layers**: Infer relevant abstraction layers from semantic concepts
4. **Identify Relevant Basepoints**: Map semantic concepts to basepoint files
5. **Store Detection Results**: Cache scope detection results for use in knowledge extraction

## Workflow

### Step 1: Load Spec Requirements/Description

Read the spec requirements or description to analyze:

```bash
# Determine spec path from context
# SPEC_PATH should be set by the calling command
if [ -z "$SPEC_PATH" ]; then
    echo "⚠️ SPEC_PATH not set. Cannot perform semantic analysis."
    exit 1
fi

echo "📖 Loading spec from: $SPEC_PATH"

# Read spec requirements or description (try multiple sources)
SPEC_TEXT=""

if [ -f "$SPEC_PATH/planning/requirements.md" ]; then
    SPEC_TEXT=$(cat "$SPEC_PATH/planning/requirements.md")
    echo "✅ Loaded requirements.md"
elif [ -f "$SPEC_PATH/planning/initialization.md" ]; then
    SPEC_TEXT=$(cat "$SPEC_PATH/planning/initialization.md")
    echo "✅ Loaded initialization.md"
elif [ -f "$SPEC_PATH/spec.md" ]; then
    SPEC_TEXT=$(cat "$SPEC_PATH/spec.md")
    echo "✅ Loaded spec.md"
else
    echo "⚠️ No spec documents found. Using feature description if provided."
    SPEC_TEXT="${FEATURE_DESCRIPTION:-}"
fi

if [ -z "$SPEC_TEXT" ]; then
    echo "❌ No text to analyze"
    exit 1
fi
```

### Step 2: Extract Key Concepts, Entities, and Relationships

Analyze the spec text to extract semantic concepts:

```bash
echo "🔍 Extracting semantic concepts..."

# Define layer indicator keywords for this project type (Geist/Agent OS)
# These map concepts to abstraction layers
declare -A LAYER_INDICATORS
LAYER_INDICATORS["ROOT"]="readme|manifest|changelog|config|documentation|license"
LAYER_INDICATORS["PROFILES"]="command|workflow|agent|standard|template|profile"
LAYER_INDICATORS["SCRIPTS"]="script|install|update|shell|bash|function"

# Extract entities (nouns that represent modules/components)
# Convert to lowercase and extract significant words
ENTITIES=$(echo "$SPEC_TEXT" | tr '[:upper:]' '[:lower:]' | \
    grep -oE '\b[a-z]{4,}\b' | \
    grep -vE '^(this|that|with|from|have|will|when|what|which|there|their|these|those|would|should|could|about|after|before|being|between|during|through|where|while|until|because|since|without|within|above|below|under|over|such|only|some|also|than|then|into|other|more|most|very|just|even|back|down|well|much|same|still|each|both|many|make|made|like|take|find|give|tell|know|want|look|work|seem|come|good|long|great|little|own|old|right|high|different|small|large|next|early|young|important|public|able|last|sure|better|best|certain|likely|true|full|whole|free|clear|real|less|main|late|hard|past|present|possible|local|major|must|else|here|away|always|never|often|already|together|however|another|maybe|sometimes|instead|along|nothing|everything|everyone|anything|anyone|actually|something|someone|probably|especially|although|whether|either|neither|among|against|several|including|regarding|according|following)$' | \
    sort | uniq -c | sort -rn | head -20 | awk '{print $2}')

echo "   Entities found: $(echo "$ENTITIES" | wc -w | tr -d ' ')"

# Extract technical concepts (patterns, standards, workflows, etc.)
TECHNICAL_CONCEPTS=$(echo "$SPEC_TEXT" | tr '[:upper:]' '[:lower:]' | \
    grep -oE '(basepoint|knowledge|extraction|validation|pattern|standard|workflow|command|spec|implementation|cache|layer|abstraction|module|scope|detection)s?' | \
    sort | uniq)

echo "   Technical concepts: $(echo "$TECHNICAL_CONCEPTS" | wc -w | tr -d ' ')"

# Extract architectural concepts (layers, components, relationships)
ARCHITECTURAL_CONCEPTS=$(echo "$SPEC_TEXT" | tr '[:upper:]' '[:lower:]' | \
    grep -oE '(layer|component|module|service|interface|architecture|structure|hierarchy|pattern|design|integration|dependency)' | \
    sort | uniq)

echo "   Architectural concepts: $(echo "$ARCHITECTURAL_CONCEPTS" | wc -w | tr -d ' ')"
```

### Step 3: Map Semantic Concepts to Abstraction Layers

Map extracted concepts to abstraction layers:

```bash
echo "🎯 Mapping concepts to abstraction layers..."

# Load abstraction layers from headquarter.md if available
BASEPOINTS_PATH="agent-os/basepoints"
DETECTED_LAYERS=""

if [ -f "$BASEPOINTS_PATH/headquarter.md" ]; then
    # Extract layers from headquarter
    DETECTED_LAYERS=$(grep -E "^\| \*\*[A-Z]+\*\*|^- \*\*[A-Z]+\*\*" "$BASEPOINTS_PATH/headquarter.md" | \
        sed 's/.*\*\*\([A-Z_]*\)\*\*.*/\1/' | sort -u)
fi

if [ -z "$DETECTED_LAYERS" ]; then
    # Default layers for Geist-type projects
    DETECTED_LAYERS="ROOT
PROFILES
SCRIPTS"
fi

echo "   Known layers: $(echo "$DETECTED_LAYERS" | tr '\n' ', ' | sed 's/,$//')"

# Score each layer based on concept matches
LAYER_SCORES=""
PRIMARY_LAYER=""
HIGHEST_SCORE=0

for layer in $DETECTED_LAYERS; do
    SCORE=0
    LAYER_LOWER=$(echo "$layer" | tr '[:upper:]' '[:lower:]')
    
    # Get indicator pattern for this layer
    INDICATOR_PATTERN="${LAYER_INDICATORS[$layer]:-$LAYER_LOWER}"
    
    # Count matches in spec text
    MATCHES=$(echo "$SPEC_TEXT" | tr '[:upper:]' '[:lower:]' | grep -oE "$INDICATOR_PATTERN" | wc -l | tr -d ' ')
    SCORE=$((SCORE + MATCHES))
    
    # Check if layer is explicitly mentioned
    if echo "$SPEC_TEXT" | grep -qi "$layer"; then
        SCORE=$((SCORE + 5))
    fi
    
    LAYER_SCORES="${LAYER_SCORES}$layer:$SCORE
"
    
    if [ "$SCORE" -gt "$HIGHEST_SCORE" ]; then
        HIGHEST_SCORE=$SCORE
        PRIMARY_LAYER=$layer
    fi
done

# Default to PROFILES if no clear winner
if [ -z "$PRIMARY_LAYER" ] || [ "$HIGHEST_SCORE" -eq 0 ]; then
    PRIMARY_LAYER="PROFILES"
fi

echo "   Primary layer detected: $PRIMARY_LAYER (score: $HIGHEST_SCORE)"
```

### Step 4: Identify Relevant Basepoint Files

Map semantic concepts to relevant basepoint files:

```bash
echo "📁 Identifying relevant basepoints..."

RELEVANT_BASEPOINTS=""
BASEPOINT_FILE_PATTERN="agent-base-*.md"

# Always include headquarter.md for cross-layer context
if [ -f "$BASEPOINTS_PATH/headquarter.md" ]; then
    RELEVANT_BASEPOINTS="$BASEPOINTS_PATH/headquarter.md"
    echo "   + headquarter.md (cross-layer context)"
fi

# Find basepoints matching detected layer
if [ -d "$BASEPOINTS_PATH" ]; then
    PRIMARY_LOWER=$(echo "$PRIMARY_LAYER" | tr '[:upper:]' '[:lower:]')
    
    # Look for basepoints in the layer's directory
    LAYER_BASEPOINTS=$(find "$BASEPOINTS_PATH" -path "*/$PRIMARY_LOWER/*" -name "$BASEPOINT_FILE_PATTERN" -type f 2>/dev/null)
    
    if [ -n "$LAYER_BASEPOINTS" ]; then
        echo "$LAYER_BASEPOINTS" | while read bp; do
            echo "   + $(basename "$bp") (primary layer match)"
        done
        RELEVANT_BASEPOINTS="${RELEVANT_BASEPOINTS}
$LAYER_BASEPOINTS"
    fi
    
    # Also search by entity/concept name matches
    for entity in $ENTITIES; do
        MATCHED=$(find "$BASEPOINTS_PATH" -name "*$entity*" -type f 2>/dev/null | head -3)
        if [ -n "$MATCHED" ]; then
            RELEVANT_BASEPOINTS="${RELEVANT_BASEPOINTS}
$MATCHED"
        fi
    done
fi

# Remove duplicates
RELEVANT_BASEPOINTS=$(echo "$RELEVANT_BASEPOINTS" | grep -v '^$' | sort -u)
BASEPOINT_COUNT=$(echo "$RELEVANT_BASEPOINTS" | grep -c '.' || echo "0")
echo "   Total relevant basepoints: $BASEPOINT_COUNT"
```

### Step 5: Store Detection Results

Cache scope detection results:

```bash
echo "💾 Storing detection results..."

# Determine cache path
CACHE_PATH="$SPEC_PATH/implementation/cache"
mkdir -p "$CACHE_PATH/scope-detection"

# Store primary detected layer
echo "$PRIMARY_LAYER" > "$CACHE_PATH/detected-layer.txt"

# Store semantic analysis results as markdown (human-readable)
cat > "$CACHE_PATH/scope-detection/semantic-analysis.md" << SEMANTIC_EOF
# Semantic Analysis Scope Detection Results

**Analyzed**: $(date)
**Spec Path**: $SPEC_PATH

## Primary Abstraction Layer

**Detected**: $PRIMARY_LAYER

### Layer Scores
$(echo "$LAYER_SCORES" | grep -v '^$' | sed 's/^/- /')

## Extracted Concepts

### Entities (Top 20)
$(echo "$ENTITIES" | head -20 | sed 's/^/- /')

### Technical Concepts
$(echo "$TECHNICAL_CONCEPTS" | sed 's/^/- /')

### Architectural Concepts
$(echo "$ARCHITECTURAL_CONCEPTS" | sed 's/^/- /')

## Relevant Basepoints Identified

$(echo "$RELEVANT_BASEPOINTS" | sed 's/^/- /')

---

*Use this analysis to guide knowledge extraction and scope filtering.*
SEMANTIC_EOF

echo "✅ Semantic analysis stored at: $CACHE_PATH/scope-detection/semantic-analysis.md"
```

### Step 6: Return Results

```bash
echo ""
echo "═══════════════════════════════════════════════════════"
echo "  SEMANTIC SCOPE DETECTION COMPLETE"
echo "═══════════════════════════════════════════════════════"
echo ""
echo "  Primary Layer: $PRIMARY_LAYER"
echo "  Entities Found: $(echo "$ENTITIES" | wc -w | tr -d ' ')"
echo "  Relevant Basepoints: $BASEPOINT_COUNT"
echo ""
echo "  Results at: $CACHE_PATH/scope-detection/semantic-analysis.md"
echo ""
echo "═══════════════════════════════════════════════════════"

# Export for use by other workflows
export DETECTED_LAYER="$PRIMARY_LAYER"
export RELEVANT_BASEPOINTS="$RELEVANT_BASEPOINTS"
```

## Important Constraints

- Must analyze spec requirements/description semantically to infer relevant modules and abstraction layers
- Must extract key concepts, entities, and relationships from spec text
- Must map semantic concepts to abstraction layers (ROOT, PROFILES, SCRIPTS for Geist)
- Must identify relevant basepoint files based on semantic analysis
- Must provide graceful fallback when basepoints don't exist
- Must be technology-agnostic but include sensible defaults for Geist projects
- **CRITICAL**: All detection results must be stored in `$SPEC_PATH/implementation/cache/scope-detection/`


# Perform scope keyword matching
# Keyword/Pattern Matching for Scope Detection

## Core Responsibilities

1. **Extract Keywords and Patterns**: Extract keywords and patterns from spec requirements/description
2. **Match Against Basepoint Modules**: Match keywords against basepoint module names and content
3. **Identify Relevant Modules**: Identify relevant modules within abstraction layers
4. **Create Affected Modules List**: Generate list of modules affected by this spec
5. **Store Detection Results**: Cache keyword matching results for use in knowledge extraction

## Workflow

### Step 1: Extract Keywords and Patterns from Spec

Extract keywords and patterns from spec text:

```bash
# Determine spec path from context
if [ -z "$SPEC_PATH" ]; then
    echo "⚠️ SPEC_PATH not set. Cannot perform keyword matching."
    exit 1
fi

echo "📖 Loading spec from: $SPEC_PATH"

# Read spec text from available sources
SPEC_TEXT=""

if [ -f "$SPEC_PATH/planning/requirements.md" ]; then
    SPEC_TEXT=$(cat "$SPEC_PATH/planning/requirements.md")
elif [ -f "$SPEC_PATH/planning/initialization.md" ]; then
    SPEC_TEXT=$(cat "$SPEC_PATH/planning/initialization.md")
elif [ -f "$SPEC_PATH/spec.md" ]; then
    SPEC_TEXT=$(cat "$SPEC_PATH/spec.md")
else
    SPEC_TEXT="${FEATURE_DESCRIPTION:-}"
fi

if [ -z "$SPEC_TEXT" ]; then
    echo "❌ No text to analyze"
    exit 1
fi

echo "🔍 Extracting keywords and patterns..."

# Extract keywords (significant terms, 4+ characters)
KEYWORDS=$(echo "$SPEC_TEXT" | tr '[:upper:]' '[:lower:]' | \
    tr -cs '[:alnum:]' '\n' | \
    grep -E '^[a-z]{4,}$' | \
    grep -vE '^(this|that|with|from|have|will|when|what|which|there|their|these|those|would|should|could|about|after|before|being|between|during|through|where|while|until|because|since|without|within|must|also|than|then|into|other|more|most|very|just|even|make|like|each|some|only)$' | \
    sort | uniq -c | sort -rn | awk '{print $2}')

echo "   Keywords extracted: $(echo "$KEYWORDS" | wc -w | tr -d ' ')"

# Extract module-like names (kebab-case or camelCase patterns)
MODULE_PATTERNS=$(echo "$SPEC_TEXT" | \
    grep -oE '[a-z]+-[a-z]+(-[a-z]+)*|[a-z]+[A-Z][a-zA-Z]*' | \
    tr '[:upper:]' '[:lower:]' | \
    sort | uniq)

echo "   Module patterns: $(echo "$MODULE_PATTERNS" | wc -w | tr -d ' ')"

# Extract file path patterns
FILE_PATTERNS=$(echo "$SPEC_TEXT" | \
    grep -oE '[a-z]+/[a-z-]+(/[a-z-]+)*|[a-z-]+\.md' | \
    sort | uniq)

echo "   File patterns: $(echo "$FILE_PATTERNS" | wc -w | tr -d ' ')"

# Extract layer keywords (specific to Geist)
LAYER_KEYWORDS=$(echo "$SPEC_TEXT" | tr '[:upper:]' '[:lower:]' | \
    grep -oE '(profile|command|workflow|standard|agent|script|template|basepoint)s?' | \
    sort | uniq)

echo "   Layer keywords: $(echo "$LAYER_KEYWORDS" | wc -w | tr -d ' ')"
```

### Step 2: Load Basepoint Module Names and Content

Load basepoint information for matching:

```bash
echo "📁 Loading basepoint modules..."

BASEPOINTS_PATH="agent-os/basepoints"
BASEPOINT_FILE_PATTERN="agent-base-*.md"

# Check if basepoints exist
if [ ! -d "$BASEPOINTS_PATH" ]; then
    echo "⚠️ Basepoints not found. Using default module list."
    BASEPOINTS_AVAILABLE="false"
else
    BASEPOINTS_AVAILABLE="true"
fi

# Build module info list
MODULE_INFO=""

if [ "$BASEPOINTS_AVAILABLE" = "true" ]; then
    # Process each basepoint file
    find "$BASEPOINTS_PATH" -name "$BASEPOINT_FILE_PATTERN" -type f | while read basepoint_file; do
        if [ -z "$basepoint_file" ]; then
            continue
        fi
        
        # Extract module name from filename
        MODULE_NAME=$(basename "$basepoint_file" .md | sed 's/agent-base-//')
        
        # Extract path relative to basepoints
        MODULE_PATH=$(dirname "$basepoint_file" | sed "s|$BASEPOINTS_PATH/||")
        
        # Determine layer from path
        if echo "$MODULE_PATH" | grep -qi "profile"; then
            MODULE_LAYER="PROFILES"
        elif echo "$MODULE_PATH" | grep -qi "script"; then
            MODULE_LAYER="SCRIPTS"
        else
            MODULE_LAYER="ROOT"
        fi
        
        # Extract keywords from basepoint content (first 500 lines)
        MODULE_KEYWORDS=$(head -500 "$basepoint_file" | tr '[:upper:]' '[:lower:]' | \
            tr -cs '[:alnum:]' '\n' | \
            grep -E '^[a-z]{4,}$' | sort | uniq | head -30 | tr '\n' ',')
        
        echo "$MODULE_NAME|$MODULE_LAYER|$MODULE_PATH|$basepoint_file|$MODULE_KEYWORDS"
    done > /tmp/module_info_$$
    
    MODULE_INFO=$(cat /tmp/module_info_$$ 2>/dev/null)
    rm -f /tmp/module_info_$$
fi

MODULE_COUNT=$(echo "$MODULE_INFO" | grep -c '|' || echo "0")
echo "   Modules loaded: $MODULE_COUNT"
```

### Step 3: Match Keywords Against Basepoint Modules

Match extracted keywords against basepoint modules:

```bash
echo "🔗 Matching keywords to modules..."

MATCHED_MODULES=""
MATCHED_BASEPOINTS=""
MATCH_SCORES=""

if [ -n "$MODULE_INFO" ]; then
    echo "$MODULE_INFO" | while IFS='|' read -r module_name module_layer module_path basepoint_file module_keywords; do
        if [ -z "$module_name" ]; then
            continue
        fi
        
        SCORE=0
        
        # Check if keywords match module name
        for keyword in $KEYWORDS; do
            if echo "$module_name" | grep -qi "$keyword"; then
                SCORE=$((SCORE + 3))
            fi
        done
        
        # Check if module patterns match
        for pattern in $MODULE_PATTERNS; do
            if echo "$module_name" | grep -qi "$pattern"; then
                SCORE=$((SCORE + 2))
            fi
            if echo "$module_path" | grep -qi "$pattern"; then
                SCORE=$((SCORE + 2))
            fi
        done
        
        # Check if keywords match module content keywords
        for keyword in $KEYWORDS; do
            if echo "$module_keywords" | grep -qi "$keyword"; then
                SCORE=$((SCORE + 1))
            fi
        done
        
        # Check layer keyword matches
        for layer_kw in $LAYER_KEYWORDS; do
            if echo "$module_layer" | grep -qi "$layer_kw"; then
                SCORE=$((SCORE + 2))
            fi
            if echo "$module_path" | grep -qi "$layer_kw"; then
                SCORE=$((SCORE + 1))
            fi
        done
        
        # If score > 0, this module is relevant
        if [ "$SCORE" -gt 0 ]; then
            echo "$module_name:$SCORE:$basepoint_file"
        fi
    done > /tmp/match_scores_$$
    
    MATCH_SCORES=$(cat /tmp/match_scores_$$ 2>/dev/null | sort -t: -k2 -rn)
    rm -f /tmp/match_scores_$$
fi

# Also always check headquarter.md
if [ -f "$BASEPOINTS_PATH/headquarter.md" ]; then
    HQ_CONTENT=$(cat "$BASEPOINTS_PATH/headquarter.md" | tr '[:upper:]' '[:lower:]')
    HQ_SCORE=0
    
    for keyword in $KEYWORDS; do
        if echo "$HQ_CONTENT" | grep -q "$keyword"; then
            HQ_SCORE=$((HQ_SCORE + 1))
        fi
    done
    
    if [ "$HQ_SCORE" -gt 0 ]; then
        MATCH_SCORES="headquarter:$HQ_SCORE:$BASEPOINTS_PATH/headquarter.md
$MATCH_SCORES"
    fi
fi

echo "   Matched modules: $(echo "$MATCH_SCORES" | grep -c ':' || echo "0")"
```

### Step 4: Generate Affected Modules List

Create list of modules affected by this spec:

```bash
echo "📋 Generating affected modules list..."

# Extract top matched modules (score > 2)
AFFECTED_MODULES=$(echo "$MATCH_SCORES" | awk -F: '$2 > 2 {print $1}' | head -10)

# Extract corresponding basepoint files
AFFECTED_BASEPOINTS=$(echo "$MATCH_SCORES" | awk -F: '$2 > 2 {print $3}' | head -10)

echo "   High-relevance modules:"
echo "$MATCH_SCORES" | head -5 | while IFS=: read -r mod score bp; do
    echo "     - $mod (score: $score)"
done
```

### Step 5: Store Detection Results

Cache keyword matching results:

```bash
echo "💾 Storing detection results..."

# Determine cache path
CACHE_PATH="$SPEC_PATH/implementation/cache"
mkdir -p "$CACHE_PATH/scope-detection"

# Store keyword matching results as markdown
cat > "$CACHE_PATH/scope-detection/keyword-matching.md" << KEYWORD_EOF
# Keyword Matching Scope Detection Results

**Analyzed**: $(date)
**Spec Path**: $SPEC_PATH

## Extracted Keywords

### Top Keywords
$(echo "$KEYWORDS" | head -20 | sed 's/^/- /')

### Module Patterns
$(echo "$MODULE_PATTERNS" | sed 's/^/- /')

### File Patterns
$(echo "$FILE_PATTERNS" | sed 's/^/- /')

### Layer Keywords
$(echo "$LAYER_KEYWORDS" | sed 's/^/- /')

## Module Match Scores

| Module | Score | Basepoint |
|--------|-------|-----------|
$(echo "$MATCH_SCORES" | head -15 | while IFS=: read -r mod score bp; do
    echo "| $mod | $score | $bp |"
done)

## Affected Modules (High Relevance)

$(echo "$AFFECTED_MODULES" | sed 's/^/- /')

## Affected Basepoints

$(echo "$AFFECTED_BASEPOINTS" | sed 's/^/- /')

---

*Use this matching to guide targeted knowledge extraction.*
KEYWORD_EOF

# Also create simple affected modules file
echo "$AFFECTED_MODULES" > "$CACHE_PATH/scope-detection/affected-modules.txt"

echo "✅ Keyword matching stored at: $CACHE_PATH/scope-detection/keyword-matching.md"
```

### Step 6: Return Results

```bash
echo ""
echo "═══════════════════════════════════════════════════════"
echo "  KEYWORD MATCHING SCOPE DETECTION COMPLETE"
echo "═══════════════════════════════════════════════════════"
echo ""
echo "  Keywords Extracted: $(echo "$KEYWORDS" | wc -w | tr -d ' ')"
echo "  Modules Matched: $(echo "$MATCH_SCORES" | grep -c ':' || echo "0")"
echo "  High-Relevance Modules: $(echo "$AFFECTED_MODULES" | grep -c '.' || echo "0")"
echo ""
echo "  Results at: $CACHE_PATH/scope-detection/keyword-matching.md"
echo ""
echo "═══════════════════════════════════════════════════════"

# Export for use by other workflows
export AFFECTED_MODULES="$AFFECTED_MODULES"
export AFFECTED_BASEPOINTS="$AFFECTED_BASEPOINTS"
```

## Important Constraints

- Must extract keywords and patterns from spec requirements/description
- Must match keywords against basepoint module names and content
- Must identify relevant modules within abstraction layers using scoring
- Must create mapping from spec context to relevant basepoints
- Must provide graceful fallback when basepoints don't exist
- Must be technology-agnostic and work with any basepoint structure
- **CRITICAL**: All detection results must be stored in `$SPEC_PATH/implementation/cache/scope-detection/`

```

### Step 6: Load Extracted Knowledge

Load the cached knowledge file for use in command execution:

```bash
# Load extracted knowledge for use in command
if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.md" ]; then
    EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.md")
    echo "✅ Loaded basepoints knowledge"
else
    EXTRACTED_KNOWLEDGE=""
    echo "⚠️ Basepoints knowledge file not found after extraction"
fi
```

### Step 7: Load Detected Layer

Load the detected abstraction layer information:

```bash
# Load detected layer
if [ -f "$SPEC_PATH/implementation/cache/detected-layer.txt" ]; then
    DETECTED_LAYER=$(cat "$SPEC_PATH/implementation/cache/detected-layer.txt")
    echo "✅ Detected layer: $DETECTED_LAYER"
else
    DETECTED_LAYER="unknown"
    echo "⚠️ Layer detection file not found"
fi
```

## Usage Notes

This workflow is designed to be used in commands that need to extract and load basepoints knowledge with scope detection. It wraps the common pattern of:
1. Checking basepoints availability
2. Extracting knowledge
3. Running scope detection
4. Loading cached results

**Typical Usage Pattern:**

Commands can replace their inline basepoints extraction + scope detection blocks with:

```bash
{{workflows/common/extract-basepoints-with-scope-detection}}
```

This eliminates redundancy and provides a single source of truth for this common pattern.

## Important Constraints

- **Requires Basepoints**: This workflow requires basepoints to exist. If they don't exist, it will exit gracefully.
- **Spec Path Required**: The `[current-spec]` placeholder must be replaced with the actual spec name.
- **Cache Dependencies**: This workflow depends on the cache directory structure created by the referenced workflows.

```

If basepoints exist, the extracted knowledge (`$EXTRACTED_KNOWLEDGE` and `$DETECTED_LAYER`) will be used to:
- Guide implementation with extracted patterns
- Suggest reusable code and modules during implementation
- Reference project-specific standards and coding patterns
- Use extracted testing approaches for test writing

## Step 2: Check for Trade-offs (if needed)

Before implementing, check if trade-offs need to be reviewed:

```bash
# Human Review for Trade-offs

## Core Responsibilities

1. **Orchestrate Trade-off Detection**: Trigger detection workflows for trade-offs and contradictions
2. **Present Trade-offs**: Format and present detected issues for human review
3. **Capture Human Decision**: Wait for and record user decision
4. **Store Review Results**: Cache decisions for use in subsequent workflow steps

## Workflow

### Step 1: Determine If Review Is Needed

```bash
# SPEC_PATH should be set by the calling command
if [ -z "$SPEC_PATH" ]; then
    echo "⚠️ SPEC_PATH not set. Cannot perform human review."
    exit 1
fi

echo "🔍 Checking if human review is needed..."

CACHE_PATH="$SPEC_PATH/implementation/cache"
REVIEW_PATH="$CACHE_PATH/human-review"
mkdir -p "$REVIEW_PATH"

# Initialize review flags
NEEDS_TRADE_OFF_REVIEW="false"
NEEDS_CONTRADICTION_REVIEW="false"
REVIEW_TRIGGERED="false"
```

### Step 2: Run Trade-off Detection

```bash
# Determine workflow base path (agent-os when installed, profiles/default for template)
if [ -d "agent-os/workflows" ]; then
    WORKFLOWS_BASE="agent-os/workflows"
else
    WORKFLOWS_BASE="profiles/default/workflows"
fi

echo "📊 Running trade-off detection..."

# Execute detect-trade-offs workflow
# This workflow compares proposed approach against basepoints patterns
source "$WORKFLOWS_BASE/human-review/detect-trade-offs.md"

# Check results
if [ -f "$REVIEW_PATH/trade-offs.md" ]; then
    TRADE_OFF_COUNT=$(grep -c "TRADE-OFF-" "$REVIEW_PATH/trade-offs.md" 2>/dev/null || echo "0")
    
    if [ "$TRADE_OFF_COUNT" -gt 0 ]; then
        NEEDS_TRADE_OFF_REVIEW="true"
        echo "   Found $TRADE_OFF_COUNT trade-offs"
    else
        echo "   No significant trade-offs found"
    fi
fi
```

### Step 2.5: Run SDD Trade-off Detection (SDD-aligned)

After running standard trade-off detection, check for SDD-specific trade-offs:

```bash
echo "📊 Running SDD trade-off detection..."

SPEC_FILE="$SPEC_PATH/spec.md"
REQUIREMENTS_FILE="$SPEC_PATH/planning/requirements.md"
TASKS_FILE="$SPEC_PATH/tasks.md"
IMPLEMENTATION_PATH="$SPEC_PATH/implementation"

# Initialize SDD trade-off tracking
SDD_TRADE_OFF_COUNT=0
SDD_TRADE_OFFS=""

# Check for spec-implementation drift (when implementation exists and diverges from spec)
if [ -f "$SPEC_FILE" ] && [ -d "$IMPLEMENTATION_PATH" ]; then
    # Check if implementation exists
    if find "$IMPLEMENTATION_PATH" -name "*.md" -o -name "*.js" -o -name "*.py" -o -name "*.ts" 2>/dev/null | head -1 | grep -q .; then
        # Implementation exists - check for drift
        # This is a simplified check - actual drift detection would compare spec requirements to implementation
        # For now, we check if spec and implementation align structurally
        SPEC_AC_COUNT=$(grep -c "Acceptance Criteria:" "$SPEC_FILE" 2>/dev/null || echo "0")
        TASKS_AC_COUNT=$(grep -c "Acceptance Criteria:" "$TASKS_FILE" 2>/dev/null || echo "0")
        
        if [ "$SPEC_AC_COUNT" -gt 0 ] && [ "$TASKS_AC_COUNT" -gt 0 ] && [ "$SPEC_AC_COUNT" -ne "$TASKS_AC_COUNT" ]; then
            SDD_TRADE_OFF_COUNT=$((SDD_TRADE_OFF_COUNT + 1))
            SDD_TRADE_OFFS="${SDD_TRADE_OFFS}\nTRADE-OFF-SDD-001: Spec-implementation drift detected. Spec has $SPEC_AC_COUNT acceptance criteria, but tasks have $TASKS_AC_COUNT. Implementation may be diverging from spec (SDD principle: spec as source of truth)."
        fi
    fi
fi

# Check for premature technical decisions in spec phase (violates SDD "What & Why" principle)
if [ -f "$SPEC_FILE" ] || [ -f "$REQUIREMENTS_FILE" ]; then
    # Check spec file for premature technical details
    if [ -f "$SPEC_FILE" ]; then
        PREMATURE_TECH=$(grep -iE "implementation details|code structure|database schema|api endpoints|class hierarchy|architecture diagram|tech stack|framework|library|npm package|import|require" "$SPEC_FILE" | wc -l)
        
        if [ "$PREMATURE_TECH" -gt 5 ]; then
            SDD_TRADE_OFF_COUNT=$((SDD_TRADE_OFF_COUNT + 1))
            SDD_TRADE_OFFS="${SDD_TRADE_OFFS}\nTRADE-OFF-SDD-002: Premature technical decisions detected in spec ($PREMATURE_TECH instances). Spec should focus on What & Why, not How (SDD principle). Technical details belong in task creation/implementation phase."
        fi
    fi
    
    # Check requirements file for premature technical details
    if [ -f "$REQUIREMENTS_FILE" ]; then
        PREMATURE_TECH_REQ=$(grep -iE "implementation details|code structure|database schema|api endpoints|class hierarchy|architecture diagram|tech stack|framework|library|npm package|import|require" "$REQUIREMENTS_FILE" | wc -l)
        
        if [ "$PREMATURE_TECH_REQ" -gt 5 ]; then
            SDD_TRADE_OFF_COUNT=$((SDD_TRADE_OFF_COUNT + 1))
            SDD_TRADE_OFFS="${SDD_TRADE_OFFS}\nTRADE-OFF-SDD-003: Premature technical decisions detected in requirements ($PREMATURE_TECH_REQ instances). Requirements should focus on What & Why, not How (SDD principle)."
        fi
    fi
fi

# Check for over-specification or feature bloat (violates SDD "minimal, intentional scope" principle)
if [ -f "$SPEC_FILE" ]; then
    # Check spec file size (over-specification indicator)
    SPEC_LINE_COUNT=$(wc -l < "$SPEC_FILE" 2>/dev/null || echo "0")
    SPEC_SECTION_COUNT=$(grep -c "^##" "$SPEC_FILE" 2>/dev/null || echo "0")
    
    # Heuristic: If spec has more than 500 lines or more than 15 sections, it might be over-specified
    if [ "$SPEC_LINE_COUNT" -gt 500 ] || [ "$SPEC_SECTION_COUNT" -gt 15 ]; then
        SDD_TRADE_OFF_COUNT=$((SDD_TRADE_OFF_COUNT + 1))
        SDD_TRADE_OFFS="${SDD_TRADE_OFFS}\nTRADE-OFF-SDD-004: Over-specification detected. Spec has $SPEC_LINE_COUNT lines and $SPEC_SECTION_COUNT sections. May violate SDD 'minimal, intentional scope' principle. Consider breaking into smaller, focused specs."
    fi
fi

# If SDD trade-offs found, add to trade-offs file
if [ "$SDD_TRADE_OFF_COUNT" -gt 0 ]; then
    echo "   Found $SDD_TRADE_OFF_COUNT SDD-specific trade-offs"
    
    # Append SDD trade-offs to trade-offs file
    if [ -f "$REVIEW_PATH/trade-offs.md" ]; then
        echo "" >> "$REVIEW_PATH/trade-offs.md"
        echo "## SDD-Specific Trade-offs" >> "$REVIEW_PATH/trade-offs.md"
        echo -e "$SDD_TRADE_OFFS" >> "$REVIEW_PATH/trade-offs.md"
    else
        # Create new trade-offs file with SDD trade-offs
        cat > "$REVIEW_PATH/trade-offs.md" << EOF
# Trade-offs Detected

## SDD-Specific Trade-offs
$(echo -e "$SDD_TRADE_OFFS")

EOF
    fi
    
    NEEDS_TRADE_OFF_REVIEW="true"
else
    echo "   No SDD-specific trade-offs found"
fi
```

### Step 3: Run Contradiction Detection

```bash
echo "📏 Running contradiction detection..."

# Execute detect-contradictions workflow
# This workflow compares proposed approach against standards
source "$WORKFLOWS_BASE/human-review/detect-contradictions.md"

# Check results
if [ -f "$REVIEW_PATH/contradictions.md" ]; then
    CRITICAL_COUNT=$(grep "Critical:" "$REVIEW_PATH/contradictions.md" 2>/dev/null | grep -oE '[0-9]+' | head -1 || echo "0")
    WARNING_COUNT=$(grep "Warnings:" "$REVIEW_PATH/contradictions.md" 2>/dev/null | grep -oE '[0-9]+' | head -1 || echo "0")
    
    if [ "$CRITICAL_COUNT" -gt 0 ]; then
        NEEDS_CONTRADICTION_REVIEW="true"
        REVIEW_URGENCY="REQUIRED"
        echo "   ⛔ Found $CRITICAL_COUNT critical contradictions - Review REQUIRED"
    elif [ "$WARNING_COUNT" -gt 0 ]; then
        NEEDS_CONTRADICTION_REVIEW="true"
        REVIEW_URGENCY="RECOMMENDED"
        echo "   ⚠️ Found $WARNING_COUNT warning contradictions - Review RECOMMENDED"
    else
        echo "   No contradictions found"
    fi
fi
```

### Step 4: Determine Review Necessity

```bash
# Determine if any review is needed
if [ "$NEEDS_TRADE_OFF_REVIEW" = "true" ] || [ "$NEEDS_CONTRADICTION_REVIEW" = "true" ]; then
    REVIEW_TRIGGERED="true"
fi

echo ""
echo "═══════════════════════════════════════════════════════"
echo "  REVIEW NECESSITY CHECK"
echo "═══════════════════════════════════════════════════════"
echo ""
echo "  Trade-off Review Needed: $NEEDS_TRADE_OFF_REVIEW"
echo "  Contradiction Review Needed: $NEEDS_CONTRADICTION_REVIEW"
echo "  Overall Review Triggered: $REVIEW_TRIGGERED"
if [ -n "$REVIEW_URGENCY" ]; then
    echo "  Review Urgency: $REVIEW_URGENCY"
fi
echo ""
echo "═══════════════════════════════════════════════════════"
```

### Step 5: Present For Human Review (If Needed)

```bash
if [ "$REVIEW_TRIGGERED" = "true" ]; then
    echo ""
    echo "👤 Presenting for human review..."
    echo ""
    
    # Execute present-human-decision workflow
    source "$WORKFLOWS_BASE/human-review/present-human-decision.md"
    
    # The presentation workflow will:
    # 1. Format all detected issues
    # 2. Provide AI recommendation
    # 3. Present decision options
    # 4. Wait for user input
else
    echo ""
    echo "✅ No human review needed. Proceeding automatically."
    echo ""
    
    # Create a "no review needed" log
    cat > "$REVIEW_PATH/review-result.md" << NO_REVIEW_EOF
# Trade-off Review Result

**Date**: $(date)
**Spec Path**: $SPEC_PATH
**Review Triggered**: No

## Summary

No significant trade-offs or contradictions were detected that require human review.

The analysis checked:
- Multiple valid patterns from basepoints
- Conflicts between proposal and documented patterns
- Mission/roadmap alignment
- Standard compliance

**Result**: Proceed with implementation.

NO_REVIEW_EOF
fi
```

### Step 6: Process User Decision (When Review Is Triggered)

```bash
# This section handles user response after presentation
# USER_RESPONSE should be provided by the user

process_user_decision() {
    USER_RESPONSE="$1"
    
    echo "📝 Processing user decision: $USER_RESPONSE"
    
    # Parse decision type
    case "$USER_RESPONSE" in
        "proceed"|"Proceed"|"PROCEED")
            DECISION="proceed"
            DECISION_REASON="User approved proceeding as-is"
            ;;
        "stop"|"Stop"|"STOP")
            DECISION="stop"
            DECISION_REASON="User requested halt"
            ;;
        "accept"|"Accept"|"ACCEPT")
            DECISION="accept_recommendation"
            DECISION_REASON="User accepted AI recommendation"
            ;;
        *)
            DECISION="custom"
            DECISION_REASON="$USER_RESPONSE"
            ;;
    esac
    
    # Log the decision
    cat > "$REVIEW_PATH/review-result.md" << REVIEW_RESULT_EOF
# Trade-off Review Result

**Date**: $(date)
**Spec Path**: $SPEC_PATH
**Review Triggered**: Yes

## Human Decision

**Decision**: $DECISION
**Reason**: $DECISION_REASON

## Issues Reviewed

### Trade-offs
$([ -f "$REVIEW_PATH/trade-offs.md" ] && grep "TRADE-OFF-" "$REVIEW_PATH/trade-offs.md" | head -5 || echo "None")

### Contradictions
$([ -f "$REVIEW_PATH/contradictions.md" ] && grep -E "⛔|⚠️" "$REVIEW_PATH/contradictions.md" | head -5 || echo "None")

## Outcome

$(if [ "$DECISION" = "proceed" ] || [ "$DECISION" = "accept_recommendation" ]; then
    echo "✅ Approved to proceed with implementation"
elif [ "$DECISION" = "stop" ]; then
    echo "⛔ Implementation halted by user"
else
    echo "📝 Custom resolution applied"
fi)

---

*Review completed by human-review workflow*
REVIEW_RESULT_EOF

    echo "✅ Decision logged to: $REVIEW_PATH/review-result.md"
    
    # Return decision for calling workflow
    echo "$DECISION"
}

# Export function
export -f process_user_decision 2>/dev/null || true
```

### Step 7: Return Review Status

```bash
# Store final review status
cat > "$REVIEW_PATH/review-status.txt" << STATUS_EOF
REVIEW_TRIGGERED=$REVIEW_TRIGGERED
NEEDS_TRADE_OFF_REVIEW=$NEEDS_TRADE_OFF_REVIEW
NEEDS_CONTRADICTION_REVIEW=$NEEDS_CONTRADICTION_REVIEW
REVIEW_URGENCY=${REVIEW_URGENCY:-NONE}
STATUS_EOF

echo ""
echo "═══════════════════════════════════════════════════════"
echo "  HUMAN REVIEW WORKFLOW COMPLETE"
echo "═══════════════════════════════════════════════════════"
echo ""
echo "  Review Triggered: $REVIEW_TRIGGERED"
echo "  Status File: $REVIEW_PATH/review-status.txt"
echo ""
echo "═══════════════════════════════════════════════════════"

# Export for use by calling command
export REVIEW_TRIGGERED="$REVIEW_TRIGGERED"
export REVIEW_URGENCY="${REVIEW_URGENCY:-NONE}"
```

## Integration with Commands

Commands should call this workflow at key decision points:

1. **shape-spec**: After gathering requirements, before finalizing
2. **write-spec**: Before completing spec document
3. **create-tasks**: When tasks affect multiple layers
4. **implement-tasks**: Before implementing cross-cutting changes

## Important Constraints

- Must orchestrate both trade-off and contradiction detection
- Must present formatted issues for human review
- Must wait for user confirmation before proceeding on critical issues
- Must log all decisions for future reference
- Must integrate with basepoints knowledge for context
- **CRITICAL**: All review results stored in `$SPEC_PATH/implementation/cache/human-review/`

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Trade-off Detection:**
- **Spec-Implementation Drift**: Detects when implementation exists and diverges from spec (violates SDD "spec as source of truth" principle)
- **Premature Technical Decisions**: Identifies technical details in spec phase (violates SDD "What & Why, not How" principle)
- **Over-Specification**: Flags excessive scope or feature bloat (violates SDD "minimal, intentional scope" principle)

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD trade-off detection is structure-based, not technology-specific
- No hardcoded technology-specific references in default templates
- Detection maintains technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

```

## Step 3: Check for Checkpoints (if needed)

Before implementing, check if checkpoints are needed for big changes:

```bash
# Create Checkpoint for Big Changes

## Core Responsibilities

1. **Detect Big Changes**: Identify big changes or abstraction layer transitions in decision making
2. **Create Optional Checkpoint**: Create optional checkpoint following default profile structure
3. **Present Recommendations**: Present recommendations with context before proceeding
4. **Allow User Review**: Allow user to review and confirm before continuing
5. **Store Checkpoint Results**: Cache checkpoint decisions

## Workflow

### Step 1: Detect Big Changes or Abstraction Layer Transitions

Check if a big change or abstraction layer transition is detected:

```bash
# Determine spec path
SPEC_PATH="{{SPEC_PATH}}"
if [ -z "$SPEC_PATH" ]; then
    SPEC_PATH="agent-os/specs/[current-spec]"
fi

# Load scope detection results
if [ -f "$SPEC_PATH/implementation/cache/scope-detection/abstraction-layer-distance.json" ]; then
    DISTANCE_RESULTS=$(cat "$SPEC_PATH/implementation/cache/scope-detection/abstraction-layer-distance.json")
fi

# Detect abstraction layer transitions
LAYER_TRANSITION=$({{DETECT_LAYER_TRANSITION}})

# Detect big changes
BIG_CHANGE=$({{DETECT_BIG_CHANGE}})

# Determine if checkpoint is needed
if [ "$LAYER_TRANSITION" = "true" ] || [ "$BIG_CHANGE" = "true" ]; then
    CHECKPOINT_NEEDED="true"
else
    CHECKPOINT_NEEDED="false"
fi
```

### Step 2: Prepare Checkpoint Presentation

If checkpoint is needed, prepare the checkpoint presentation:

```bash
if [ "$CHECKPOINT_NEEDED" = "true" ]; then
    # Extract recommendations with context
    RECOMMENDATIONS=$({{EXTRACT_RECOMMENDATIONS}})
    
    # Extract context from basepoints
    RECOMMENDATION_CONTEXT=$({{EXTRACT_RECOMMENDATION_CONTEXT}})
    
    # Prepare checkpoint presentation
    CHECKPOINT_PRESENTATION=$({{PREPARE_CHECKPOINT_PRESENTATION}})
fi
```

### Step 3: Present Checkpoint to User

Present the checkpoint following default profile human-in-the-loop structure:

```bash
if [ "$CHECKPOINT_NEEDED" = "true" ]; then
    # Output checkpoint presentation to user
    echo "$CHECKPOINT_PRESENTATION"
    
    # Wait for user confirmation
fi
```

**Presentation Format:**

**For Standard Checkpoints:**
```
⚠️ Checkpoint: Big Change or Abstraction Layer Transition Detected

Before proceeding, I'd like to confirm the following significant decision:

**Change Type:** [Abstraction Layer Transition / Big Change]

**Current Context:**
[Current abstraction layer or state]

**Proposed Change:**
[Description of the change]

**Recommendation:**
Based on basepoints knowledge and project structure, I recommend:
- [Recommendation 1]
- [Recommendation 2]

**Context from Basepoints:**
[Relevant context from basepoints that informs this decision]

**Impact:**
- [Impact on architecture]
- [Impact on existing patterns]
- [Impact on related modules]

**Your Confirmation:**
Please confirm to proceed, or provide modifications:
1. Confirm: Proceed with recommendation
2. Modify: [Your modification]
3. Cancel: Do not proceed with this change
```

**For SDD Checkpoints:**

**SDD Checkpoint: Spec Completeness Before Task Creation**
```
🔍 SDD Checkpoint: Spec Completeness Validation

Before proceeding to task creation (SDD phase order: Specify → Tasks → Implement), let's ensure the specification is complete:

**SDD Principle:** "Specify" phase should be complete before "Tasks" phase

**Current Spec Status:**
- User stories: [Present / Missing]
- Acceptance criteria: [Present / Missing]
- Scope boundaries: [Present / Missing]

**Recommendation:**
Based on SDD best practices, ensure the spec includes:
- Clear user stories in format "As a [user], I want [action], so that [benefit]"
- Explicit acceptance criteria for each requirement
- Defined scope boundaries (in-scope vs out-of-scope)

**Your Decision:**
1. Enhance spec: Review and enhance spec before creating tasks
2. Proceed anyway: Create tasks despite incomplete spec
3. Cancel: Do not proceed with task creation
```

**SDD Checkpoint: Spec Alignment Before Implementation**
```
🔍 SDD Checkpoint: Spec Alignment Validation

Before proceeding to implementation (SDD: spec as source of truth), let's validate that tasks align with the spec:

**SDD Principle:** Spec is the source of truth - implementation should validate against spec

**Current Alignment:**
- Spec acceptance criteria: [Count]
- Task acceptance criteria: [Count]
- Alignment: [Aligned / Misaligned]

**Recommendation:**
Based on SDD best practices, ensure tasks can be validated against spec acceptance criteria:
- Each task should reference spec acceptance criteria
- Tasks should align with spec scope and requirements
- Implementation should validate against spec as source of truth

**Your Decision:**
1. Align tasks: Review and align tasks with spec before implementation
2. Proceed anyway: Begin implementation despite misalignment
3. Cancel: Do not proceed with implementation
```

### Step 4: Process User Confirmation

Process the user's confirmation:

```bash
# Wait for user response
USER_CONFIRMATION=$({{GET_USER_CONFIRMATION}})

# Process confirmation
if [ "$USER_CONFIRMATION" = "confirm" ]; then
    PROCEED="true"
    MODIFICATIONS=""
elif [ "$USER_CONFIRMATION" = "modify" ]; then
    PROCEED="true"
    MODIFICATIONS=$({{GET_USER_MODIFICATIONS}})
elif [ "$USER_CONFIRMATION" = "cancel" ]; then
    PROCEED="false"
    MODIFICATIONS=""
fi

# Store checkpoint decision
CHECKPOINT_RESULT="{
  \"checkpoint_type\": \"big_change_or_layer_transition\",
  \"change_type\": \"$LAYER_TRANSITION_OR_BIG_CHANGE\",
  \"recommendations\": $(echo "$RECOMMENDATIONS" | {{JSON_FORMAT}}),
  \"user_confirmation\": \"$USER_CONFIRMATION\",
  \"proceed\": \"$PROCEED\",
  \"modifications\": \"$MODIFICATIONS\"
}"
```

### Step 2.5: Check for SDD Checkpoints (SDD-aligned)

Before presenting checkpoints, check if SDD-specific checkpoints are needed:

```bash
# SDD Checkpoint Detection
SDD_CHECKPOINT_NEEDED="false"
SDD_CHECKPOINT_TYPE=""

SPEC_FILE="$SPEC_PATH/spec.md"
TASKS_FILE="$SPEC_PATH/tasks.md"
REQUIREMENTS_FILE="$SPEC_PATH/planning/requirements.md"
IMPLEMENTATION_PATH="$SPEC_PATH/implementation"

# Checkpoint 1: Before task creation - ensure spec is complete (SDD: "Specify" phase complete before "Tasks" phase)
# This checkpoint should trigger when transitioning from spec creation to task creation
if [ -f "$SPEC_FILE" ] && [ ! -f "$TASKS_FILE" ]; then
    # Spec exists but tasks don't - this is the transition point
    
    # Check if spec is complete (has user stories, acceptance criteria, scope boundaries)
    HAS_USER_STORIES=$(grep -iE "as a .*i want .*so that|user story" "$SPEC_FILE" | wc -l)
    HAS_ACCEPTANCE_CRITERIA=$(grep -iE "acceptance criteria|Acceptance Criteria" "$SPEC_FILE" | wc -l)
    HAS_SCOPE=$(grep -iE "in scope|out of scope|scope boundary|Scope:" "$SPEC_FILE" | wc -l)
    
    # Only trigger checkpoint if spec appears incomplete
    if [ "$HAS_USER_STORIES" -eq 0 ] || [ "$HAS_ACCEPTANCE_CRITERIA" -eq 0 ] || [ "$HAS_SCOPE" -eq 0 ]; then
        SDD_CHECKPOINT_NEEDED="true"
        SDD_CHECKPOINT_TYPE="spec_completeness_before_tasks"
        echo "🔍 SDD Checkpoint: Spec completeness validation needed before task creation"
    fi
fi

# Checkpoint 2: Before implementation - validate spec alignment (SDD: spec as source of truth validation)
# This checkpoint should trigger when transitioning from task creation to implementation
if [ -f "$TASKS_FILE" ] && [ ! -d "$IMPLEMENTATION_PATH" ] || [ -z "$(find "$IMPLEMENTATION_PATH" -name "*.md" -o -name "*.js" -o -name "*.py" -o -name "*.ts" 2>/dev/null | head -1)" ]; then
    # Tasks exist but implementation doesn't - this is the transition point
    
    # Check if tasks can be validated against spec acceptance criteria
    if [ -f "$SPEC_FILE" ]; then
        SPEC_AC_COUNT=$(grep -c "Acceptance Criteria:" "$SPEC_FILE" 2>/dev/null || echo "0")
        TASKS_AC_COUNT=$(grep -c "Acceptance Criteria:" "$TASKS_FILE" 2>/dev/null || echo "0")
        
        # Only trigger checkpoint if tasks don't align with spec
        if [ "$SPEC_AC_COUNT" -gt 0 ] && [ "$TASKS_AC_COUNT" -eq 0 ]; then
            SDD_CHECKPOINT_NEEDED="true"
            SDD_CHECKPOINT_TYPE="spec_alignment_before_implementation"
            echo "🔍 SDD Checkpoint: Spec alignment validation needed before implementation"
        fi
    fi
fi

# If SDD checkpoint needed, add to checkpoint list
if [ "$SDD_CHECKPOINT_NEEDED" = "true" ]; then
    # Merge with existing checkpoint detection
    if [ "$CHECKPOINT_NEEDED" = "false" ]; then
        CHECKPOINT_NEEDED="true"
        echo "   SDD checkpoint added: $SDD_CHECKPOINT_TYPE"
    else
        echo "   Additional SDD checkpoint: $SDD_CHECKPOINT_TYPE"
    fi
    
    # Store SDD checkpoint info for presentation
    SDD_CHECKPOINT_INFO="{
      \"type\": \"$SDD_CHECKPOINT_TYPE\",
      \"sdd_aligned\": true,
      \"principle\": \"$(if [ "$SDD_CHECKPOINT_TYPE" = "spec_completeness_before_tasks" ]; then echo "Specify phase complete before Tasks phase"; else echo "Spec as source of truth validation"; fi)\"
    }"
fi
```

### Step 3: Present Checkpoint to User (Enhanced with SDD Checkpoints)

Present the checkpoint following default profile human-in-the-loop structure, including SDD-specific checkpoints:

```bash
# Determine cache path
if [ -n "$SPEC_PATH" ]; then
    CACHE_PATH="$SPEC_PATH/implementation/cache"
else
    CACHE_PATH="agent-os/output/deploy-agents/knowledge"
fi

mkdir -p "$CACHE_PATH/human-review"

# Store checkpoint decision
echo "$CHECKPOINT_RESULT" > "$CACHE_PATH/human-review/checkpoint-review.json"

# Also create human-readable summary
cat > "$CACHE_PATH/human-review/checkpoint-review-summary.md" << EOF
# Checkpoint Review Results

## Change Type
[Type of change: abstraction layer transition / big change]

## Recommendations
[Summary of recommendations presented]

## User Confirmation
[User's confirmation: confirm/modify/cancel]

## Proceed
[Whether to proceed: true/false]

## Modifications
[Any modifications requested by user]
EOF
```

## Important Constraints

- Must detect big changes or abstraction layer transitions in decision making
- Must create optional checkpoints following default profile structure
- Must present recommendations with context before proceeding
- Must allow user to review and confirm before continuing
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents
- Must be technology-agnostic and work with any basepoint structure
- **CRITICAL**: All checkpoint results must be stored in `agent-os/specs/[current-spec]/implementation/cache/human-review/`, not scattered around the codebase

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Checkpoints:**
- **Before Task Creation**: Validates spec completeness (SDD: "Specify" phase complete before "Tasks" phase)
- **Before Implementation**: Validates spec alignment (SDD: spec as source of truth validation)
- **Conditional Triggering**: SDD checkpoints only trigger when they add value and don't create unnecessary friction
- **Human Review at Every Checkpoint**: Integrates SDD principle to prevent spec drift

**SDD Checkpoint Types:**
- `spec_completeness_before_tasks`: Ensures spec has user stories, acceptance criteria, and scope boundaries before creating tasks
- `spec_alignment_before_implementation`: Validates that tasks can be validated against spec acceptance criteria before implementation

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD checkpoint detection is structure-based, not technology-specific
- No hardcoded technology-specific references in default templates
- Checkpoints maintain technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

```

## Step 4: Implement Tasks

Proceed with implementation by following these instructions:

Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

## Implementation process:

1. Check if deep reading is needed and perform if necessary:
   ```bash
   # Check abstraction layer distance
   # Abstraction Layer Distance Calculation

## Core Responsibilities

1. **Determine Abstraction Layer Distance**: Calculate distance from spec context to actual implementation
2. **Calculate Distance Metrics**: Create distance metrics for deep reading decisions
3. **Create Heuristics**: Define heuristics for when deep implementation reading is needed
4. **Base on Project Structure**: Use project structure (after agent-deployment) for calculations
5. **Store Calculation Results**: Cache distance calculation results

## Workflow

### Step 1: Load Scope Detection Results

Load previous scope detection results:

```bash
# Determine spec path and cache path
SPEC_PATH="{{SPEC_PATH}}"
if [ -z "$SPEC_PATH" ]; then
    SPEC_PATH="agent-os/specs/[current-spec]"
fi

if [ -n "$SPEC_PATH" ]; then
    CACHE_PATH="$SPEC_PATH/implementation/cache"
else
    CACHE_PATH="agent-os/output/deploy-agents/knowledge"
fi

# Load scope detection results
if [ -f "$CACHE_PATH/scope-detection/semantic-analysis.json" ]; then
    SEMANTIC_RESULTS=$(cat "$CACHE_PATH/scope-detection/semantic-analysis.json")
fi

if [ -f "$CACHE_PATH/scope-detection/keyword-matching.json" ]; then
    KEYWORD_RESULTS=$(cat "$CACHE_PATH/scope-detection/keyword-matching.json")
fi

if [ -f "$CACHE_PATH/scope-detection/cross-layer-detection.json" ]; then
    CROSS_LAYER_RESULTS=$(cat "$CACHE_PATH/scope-detection/cross-layer-detection.json")
fi

if [ -f "$CACHE_PATH/scope-detection/same-layer-detection.json" ]; then
    SAME_LAYER_RESULTS=$(cat "$CACHE_PATH/scope-detection/same-layer-detection.json")
fi
```

### Step 2: Determine Spec Context Abstraction Layer

Identify the abstraction layer of the spec context:

```bash
# Determine spec context layer from scope detection
SPEC_CONTEXT_LAYER=$({{DETERMINE_SPEC_CONTEXT_LAYER}})

# If spec spans multiple layers, identify the highest/primary layer
if {{SPEC_SPANS_MULTIPLE_LAYERS}}; then
    PRIMARY_LAYER=$({{IDENTIFY_PRIMARY_LAYER}})
    SPEC_CONTEXT_LAYER="$PRIMARY_LAYER"
fi

# If spec is at product/architecture level, mark as highest abstraction
if {{IS_PRODUCT_LEVEL}}; then
    SPEC_CONTEXT_LAYER="product"
fi
```

### Step 3: Load Project Structure and Abstraction Layers

Load project structure for distance calculation:

```bash
# Load abstraction layers from basepoints
BASEPOINTS_PATH="{{BASEPOINTS_PATH}}"

# Read headquarter.md for layer structure
if [ -f "$BASEPOINTS_PATH/headquarter.md" ]; then
    HEADQUARTER=$(cat "$BASEPOINTS_PATH/headquarter.md")
    ABSTRACTION_LAYERS=$({{EXTRACT_ABSTRACTION_LAYERS}})
fi

# Create layer hierarchy (ordered from highest to lowest abstraction)
LAYER_HIERARCHY=$({{CREATE_LAYER_HIERARCHY}})

# Example hierarchy: product > architecture > domain > data > infrastructure > implementation
```

### Step 4: Calculate Distance from Spec Context to Implementation

Calculate distance metrics:

```bash
# Calculate distance for each relevant layer
DISTANCE_METRICS=""
echo "$ABSTRACTION_LAYERS" | while read layer; do
    if [ -z "$layer" ]; then
        continue
    fi
    
    # Calculate distance from spec context to this layer
    DISTANCE=$({{CALCULATE_LAYER_DISTANCE}})
    
    # Calculate distance to actual implementation (lowest layer)
    IMPLEMENTATION_DISTANCE=$({{CALCULATE_IMPLEMENTATION_DISTANCE}})
    
    DISTANCE_METRICS="$DISTANCE_METRICS\n${layer}:${DISTANCE}:${IMPLEMENTATION_DISTANCE}"
done

# Calculate overall distance to implementation
OVERALL_DISTANCE=$({{CALCULATE_OVERALL_DISTANCE}})
```

### Step 5: Create Heuristics for Deep Reading Decisions

Define when deep reading is needed:

```bash
# Create heuristics based on distance
DEEP_READING_HEURISTICS=""

# Higher abstraction = less need for implementation reading
if [ "$OVERALL_DISTANCE" -ge 3 ]; then
    DEEP_READING_NEEDED="low"
    DEEP_READING_HEURISTICS="$DEEP_READING_HEURISTICS\nhigh_abstraction:low_need"
fi

# One or two layers above implementation = more need
if [ "$OVERALL_DISTANCE" -le 2 ] && [ "$OVERALL_DISTANCE" -ge 1 ]; then
    DEEP_READING_NEEDED="high"
    DEEP_READING_HEURISTICS="$DEEP_READING_HEURISTICS\nclose_to_implementation:high_need"
fi

# At implementation layer = very high need
if [ "$OVERALL_DISTANCE" -eq 0 ]; then
    DEEP_READING_NEEDED="very_high"
    DEEP_READING_HEURISTICS="$DEEP_READING_HEURISTICS\nat_implementation:very_high_need"
fi

# Create decision rules
DEEP_READING_DECISION=$({{CREATE_DEEP_READING_DECISION}})
```

Heuristics:
- **High Abstraction (3+ layers away)**: Low need for deep reading
- **Medium Abstraction (1-2 layers away)**: High need for deep reading
- **At Implementation Layer**: Very high need for deep reading
- **Cross-Layer Patterns**: May need deep reading for understanding interactions

### Step 6: Base Distance Calculations on Project Structure

Use actual project structure (after agent-deployment):

```bash
# After agent-deployment, use actual project structure
if {{AFTER_AGENT_DEPLOYMENT}}; then
    # Use actual project structure from basepoints
    PROJECT_STRUCTURE=$({{LOAD_PROJECT_STRUCTURE}})
    
    # Calculate distances based on actual structure
    ACTUAL_DISTANCES=$({{CALCULATE_ACTUAL_DISTANCES}})
    
    # Update heuristics based on actual structure
    DEEP_READING_HEURISTICS=$({{UPDATE_HEURISTICS_FOR_STRUCTURE}})
else
    # Before deployment, use generic/placeholder calculations
    GENERIC_DISTANCES=$({{CALCULATE_GENERIC_DISTANCES}})
    DEEP_READING_HEURISTICS=$({{CREATE_GENERIC_HEURISTICS}})
fi
```

### Step 7: Store Calculation Results

Cache distance calculation results:

```bash
mkdir -p "$CACHE_PATH/scope-detection"

# Store distance calculation results
cat > "$CACHE_PATH/scope-detection/abstraction-layer-distance.json" << EOF
{
  "spec_context_layer": "$SPEC_CONTEXT_LAYER",
  "abstraction_layers": $(echo "$ABSTRACTION_LAYERS" | {{JSON_FORMAT}}),
  "layer_hierarchy": $(echo "$LAYER_HIERARCHY" | {{JSON_FORMAT}}),
  "distance_metrics": $(echo "$DISTANCE_METRICS" | {{JSON_FORMAT}}),
  "overall_distance": "$OVERALL_DISTANCE",
  "deep_reading_needed": "$DEEP_READING_NEEDED",
  "deep_reading_heuristics": $(echo "$DEEP_READING_HEURISTICS" | {{JSON_FORMAT}}),
  "deep_reading_decision": "$DEEP_READING_DECISION"
}
EOF

# Also create human-readable summary
cat > "$CACHE_PATH/scope-detection/abstraction-layer-distance-summary.md" << EOF
# Abstraction Layer Distance Calculation Results

## Spec Context Layer
[The abstraction layer where the spec context resides]

## Abstraction Layer Hierarchy
[Ordered list of abstraction layers from highest to lowest]

## Distance Metrics

### By Layer
[Distance from spec context to each layer]

### Overall Distance to Implementation
[Overall distance to actual implementation]

## Deep Reading Decision

### Need Level
[Level of need for deep implementation reading: low/high/very_high]

### Heuristics Applied
[Summary of heuristics used to determine deep reading need]

### Decision
[Final decision on whether deep reading is needed]
EOF
```

## Important Constraints

- Must determine abstraction layer distance from spec context to actual implementation
- Must calculate distance metrics for deep reading decisions
- Must create heuristics for when deep implementation reading is needed
- Must base distance calculations on project structure (after agent-deployment)
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents
- Must be technology-agnostic and work with any basepoint structure
- **CRITICAL**: All calculation results must be stored in `agent-os/specs/[current-spec]/implementation/cache/scope-detection/`, not scattered around the codebase

   
   # If deep reading is needed, perform deep reading
   # Deep Implementation Reading

## Core Responsibilities

1. **Determine if Deep Reading is Needed**: Check abstraction layer distance to decide if deep reading is required
2. **Read Implementation Files**: Read actual implementation files from modules referenced in basepoints
3. **Extract Patterns and Logic**: Extract patterns, similar logic, and reusable code from implementation
4. **Identify Reusability Opportunities**: Identify opportunities for making code reusable
5. **Analyze Implementation**: Analyze implementation to understand logic and patterns
6. **Store Reading Results**: Cache deep reading results for use in workflows

## Workflow

### Step 1: Check if Deep Reading is Needed

Check abstraction layer distance to determine if deep reading is needed:

```bash
# Determine spec path and cache path
SPEC_PATH="{{SPEC_PATH}}"
if [ -z "$SPEC_PATH" ]; then
    SPEC_PATH="agent-os/specs/[current-spec]"
fi

if [ -n "$SPEC_PATH" ]; then
    CACHE_PATH="$SPEC_PATH/implementation/cache"
else
    CACHE_PATH="agent-os/output/deploy-agents/knowledge"
fi

# Load abstraction layer distance calculation
if [ -f "$CACHE_PATH/scope-detection/abstraction-layer-distance.json" ]; then
    DISTANCE_RESULTS=$(cat "$CACHE_PATH/scope-detection/abstraction-layer-distance.json")
    DEEP_READING_NEEDED=$({{EXTRACT_DEEP_READING_NEEDED}})
    OVERALL_DISTANCE=$({{EXTRACT_OVERALL_DISTANCE}})
else
    # If distance calculation not available, skip deep reading
    DEEP_READING_NEEDED="unknown"
    OVERALL_DISTANCE="unknown"
fi

# Determine if deep reading should proceed
if [ "$DEEP_READING_NEEDED" = "low" ] || [ "$DEEP_READING_NEEDED" = "unknown" ]; then
    echo "⚠️  Deep reading not needed (abstraction layer distance: $OVERALL_DISTANCE, need level: $DEEP_READING_NEEDED)"
    exit 0
fi

echo "✅ Deep reading needed (abstraction layer distance: $OVERALL_DISTANCE, need level: $DEEP_READING_NEEDED)"
```

### Step 2: Identify Modules Referenced in Basepoints

Find modules that are referenced in relevant basepoints:

```bash
# Load scope detection results
if [ -f "$CACHE_PATH/scope-detection/same-layer-detection.json" ]; then
    SAME_LAYER_RESULTS=$(cat "$CACHE_PATH/scope-detection/same-layer-detection.json")
    RELEVANT_MODULES=$({{EXTRACT_RELEVANT_MODULES}})
fi

if [ -f "$CACHE_PATH/scope-detection/cross-layer-detection.json" ]; then
    CROSS_LAYER_RESULTS=$(cat "$CACHE_PATH/scope-detection/cross-layer-detection.json")
    CROSS_LAYER_MODULES=$({{EXTRACT_CROSS_LAYER_MODULES}})
fi

# Combine relevant modules
ALL_RELEVANT_MODULES=$({{COMBINE_MODULES}})

# Extract module paths from basepoints
MODULE_PATHS=""
echo "$ALL_RELEVANT_MODULES" | while read module_name; do
    if [ -z "$module_name" ]; then
        continue
    fi
    
    # Find actual module path in project
    MODULE_PATH=$({{FIND_MODULE_PATH}})
    
    if [ -n "$MODULE_PATH" ]; then
        MODULE_PATHS="$MODULE_PATHS\n$MODULE_PATH"
    fi
done
```

### Step 3: Read Implementation Files from Modules

Read actual implementation files from identified modules:

```bash
# Read implementation files
IMPLEMENTATION_FILES=""
echo "$MODULE_PATHS" | while read module_path; do
    if [ -z "$module_path" ]; then
        continue
    fi
    
    # Find code files in this module
    # Use {{CODE_FILE_PATTERNS}} placeholder for project-specific file extensions
    CODE_FILES=$(find "$module_path" -type f \( {{CODE_FILE_PATTERNS}} \) ! -path "*/node_modules/*" ! -path "*/.git/*" ! -path "*/build/*" ! -path "*/dist/*")
    
    echo "$CODE_FILES" | while read code_file; do
        if [ -z "$code_file" ]; then
            continue
        fi
        
        # Read file content
        FILE_CONTENT=$(cat "$code_file")
        
        # Store file info
        IMPLEMENTATION_FILES="$IMPLEMENTATION_FILES\n${code_file}|${FILE_CONTENT}"
    done
done
```

### Step 4: Extract Patterns, Similar Logic, and Reusable Code

Analyze implementation files to extract patterns and reusable code:

```bash
# Extract patterns from implementation
EXTRACTED_PATTERNS=""
EXTRACTED_LOGIC=""
REUSABLE_CODE=""

echo "$IMPLEMENTATION_FILES" | while IFS='|' read -r code_file file_content; do
    if [ -z "$code_file" ] || [ -z "$file_content" ]; then
        continue
    fi
    
    # Extract design patterns
    DESIGN_PATTERNS=$({{EXTRACT_DESIGN_PATTERNS}})
    
    # Extract coding patterns
    CODING_PATTERNS=$({{EXTRACT_CODING_PATTERNS}})
    
    # Extract similar logic
    SIMILAR_LOGIC=$({{EXTRACT_SIMILAR_LOGIC}})
    
    # Extract reusable code blocks
    REUSABLE_BLOCKS=$({{EXTRACT_REUSABLE_BLOCKS}})
    
    # Extract functions/methods that could be reused
    REUSABLE_FUNCTIONS=$({{EXTRACT_REUSABLE_FUNCTIONS}})
    
    # Extract classes/modules that could be reused
    REUSABLE_CLASSES=$({{EXTRACT_REUSABLE_CLASSES}})
    
    EXTRACTED_PATTERNS="$EXTRACTED_PATTERNS\n${code_file}:${DESIGN_PATTERNS}:${CODING_PATTERNS}"
    EXTRACTED_LOGIC="$EXTRACTED_LOGIC\n${code_file}:${SIMILAR_LOGIC}"
    REUSABLE_CODE="$REUSABLE_CODE\n${code_file}:${REUSABLE_BLOCKS}:${REUSABLE_FUNCTIONS}:${REUSABLE_CLASSES}"
done
```

### Step 5: Identify Opportunities for Making Code Reusable

Identify opportunities to refactor code for reusability:

```bash
# Identify reusable opportunities
REUSABILITY_OPPORTUNITIES=""

# Detect similar code across modules
SIMILAR_CODE_DETECTED=$({{DETECT_SIMILAR_CODE}})

# Identify core/common patterns
CORE_PATTERNS=$({{IDENTIFY_CORE_PATTERNS}})

# Identify common modules
COMMON_MODULES=$({{IDENTIFY_COMMON_MODULES}})

# Identify opportunities to move code to shared locations
SHARED_LOCATION_OPPORTUNITIES=$({{IDENTIFY_SHARED_LOCATION_OPPORTUNITIES}})

REUSABILITY_OPPORTUNITIES="$REUSABILITY_OPPORTUNITIES\nSimilar Code: ${SIMILAR_CODE_DETECTED}\nCore Patterns: ${CORE_PATTERNS}\nCommon Modules: ${COMMON_MODULES}\nShared Locations: ${SHARED_LOCATION_OPPORTUNITIES}"
```

### Step 6: Analyze Implementation to Understand Logic and Patterns

Analyze implementation files to understand logic and patterns:

```bash
# Analyze implementation logic
IMPLEMENTATION_ANALYSIS=""

echo "$IMPLEMENTATION_FILES" | while IFS='|' read -r code_file file_content; do
    if [ -z "$code_file" ] || [ -z "$file_content" ]; then
        continue
    fi
    
    # Analyze logic flow
    LOGIC_FLOW=$({{ANALYZE_LOGIC_FLOW}})
    
    # Analyze data flow
    DATA_FLOW=$({{ANALYZE_DATA_FLOW}})
    
    # Analyze control flow
    CONTROL_FLOW=$({{ANALYZE_CONTROL_FLOW}})
    
    # Analyze dependencies
    DEPENDENCIES=$({{ANALYZE_DEPENDENCIES}})
    
    # Analyze patterns used
    PATTERNS_USED=$({{ANALYZE_PATTERNS_USED}})
    
    IMPLEMENTATION_ANALYSIS="$IMPLEMENTATION_ANALYSIS\n${code_file}:\n  Logic Flow: ${LOGIC_FLOW}\n  Data Flow: ${DATA_FLOW}\n  Control Flow: ${CONTROL_FLOW}\n  Dependencies: ${DEPENDENCIES}\n  Patterns: ${PATTERNS_USED}"
done
```

### Step 7: Store Deep Reading Results

Cache deep reading results:

```bash
mkdir -p "$CACHE_PATH/deep-reading"

# Store deep reading results
cat > "$CACHE_PATH/deep-reading/implementation-analysis.json" << EOF
{
  "deep_reading_triggered": true,
  "abstraction_layer_distance": "$OVERALL_DISTANCE",
  "need_level": "$DEEP_READING_NEEDED",
  "modules_analyzed": $(echo "$ALL_RELEVANT_MODULES" | {{JSON_FORMAT}}),
  "files_read": $(echo "$IMPLEMENTATION_FILES" | {{JSON_FORMAT}}),
  "extracted_patterns": $(echo "$EXTRACTED_PATTERNS" | {{JSON_FORMAT}}),
  "extracted_logic": $(echo "$EXTRACTED_LOGIC" | {{JSON_FORMAT}}),
  "reusable_code": $(echo "$REUSABLE_CODE" | {{JSON_FORMAT}}),
  "reusability_opportunities": $(echo "$REUSABILITY_OPPORTUNITIES" | {{JSON_FORMAT}}),
  "implementation_analysis": $(echo "$IMPLEMENTATION_ANALYSIS" | {{JSON_FORMAT}})
}
EOF

# Also create human-readable summary
cat > "$CACHE_PATH/deep-reading/implementation-analysis-summary.md" << EOF
# Deep Implementation Reading Results

## Trigger Information
- Abstraction Layer Distance: $OVERALL_DISTANCE
- Need Level: $DEEP_READING_NEEDED
- Deep Reading Triggered: Yes

## Modules Analyzed
[List of modules that were analyzed]

## Files Read
[List of implementation files that were read]

## Extracted Patterns
[Summary of patterns extracted from implementation]

## Extracted Logic
[Summary of similar logic found]

## Reusable Code Identified
[Summary of reusable code blocks, functions, and classes]

## Reusability Opportunities
[Summary of opportunities to make code reusable]

## Implementation Analysis
[Summary of logic flow, data flow, control flow, dependencies, and patterns]
EOF
```

## Important Constraints

- Must determine if deep reading is needed based on abstraction layer distance
- Must read actual implementation files from modules referenced in basepoints
- Must extract patterns, similar logic, and reusable code from implementation
- Must identify opportunities for making code reusable (moving core/common/similar modules)
- Must analyze implementation to understand logic and patterns
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents
- Must be technology-agnostic and work with any basepoint structure
- **CRITICAL**: All deep reading results must be stored in `agent-os/specs/[current-spec]/implementation/cache/deep-reading/`, not scattered around the codebase
- Must cache results to avoid redundant reads

   
   # Detect reusable code
   if [ -f "$SPEC_PATH/implementation/cache/deep-reading/implementation-analysis.json" ]; then
       # Reusable Code Detection and Suggestion

## Core Responsibilities

1. **Detect Similar Logic**: Identify similar logic and reusable code patterns from deep reading
2. **Suggest Existing Modules**: Suggest existing modules and code that can be reused
3. **Identify Refactoring Opportunities**: Identify opportunities to refactor code for reusability
4. **Present Reusable Options**: Present reusable options to user with context and pros/cons
5. **Store Detection Results**: Cache reusable code detection results

## Workflow

### Step 1: Load Deep Reading Results

Load previous deep reading results:

```bash
# Determine spec path and cache path
SPEC_PATH="{{SPEC_PATH}}"
if [ -z "$SPEC_PATH" ]; then
    SPEC_PATH="agent-os/specs/[current-spec]"
fi

if [ -n "$SPEC_PATH" ]; then
    CACHE_PATH="$SPEC_PATH/implementation/cache"
else
    CACHE_PATH="agent-os/output/deploy-agents/knowledge"
fi

# Load deep reading results
if [ -f "$CACHE_PATH/deep-reading/implementation-analysis.json" ]; then
    DEEP_READING_RESULTS=$(cat "$CACHE_PATH/deep-reading/implementation-analysis.json")
    EXTRACTED_PATTERNS=$({{EXTRACT_PATTERNS}})
    EXTRACTED_LOGIC=$({{EXTRACT_LOGIC}})
    REUSABLE_CODE=$({{EXTRACT_REUSABLE_CODE}})
    REUSABILITY_OPPORTUNITIES=$({{EXTRACT_REUSABILITY_OPPORTUNITIES}})
else
    echo "⚠️  No deep reading results found. Run deep reading first."
    exit 1
fi
```

### Step 2: Detect Similar Logic and Reusable Code Patterns

Analyze extracted code to detect similar patterns:

```bash
# Detect similar logic patterns
SIMILAR_LOGIC_PATTERNS=""
echo "$EXTRACTED_LOGIC" | while IFS=':' read -r file logic; do
    if [ -z "$file" ] || [ -z "$logic" ]; then
        continue
    fi
    
    # Compare logic with other files
    SIMILAR_FILES=$({{FIND_SIMILAR_LOGIC}})
    
    if [ -n "$SIMILAR_FILES" ]; then
        SIMILAR_LOGIC_PATTERNS="$SIMILAR_LOGIC_PATTERNS\n${file}:${SIMILAR_FILES}"
    fi
done

# Detect reusable code patterns
REUSABLE_CODE_PATTERNS=""
echo "$REUSABLE_CODE" | while IFS=':' read -r file blocks functions classes; do
    if [ -z "$file" ]; then
        continue
    fi
    
    # Identify reusable patterns
    if [ -n "$blocks" ] || [ -n "$functions" ] || [ -n "$classes" ]; then
        REUSABLE_CODE_PATTERNS="$REUSABLE_CODE_PATTERNS\n${file}:blocks=${blocks}:functions=${functions}:classes=${classes}"
    fi
done
```

### Step 3: Suggest Existing Modules and Code That Can Be Reused

Create suggestions for reusable code:

```bash
# Create reuse suggestions
REUSE_SUGGESTIONS=""

# Suggest existing modules
EXISTING_MODULES=$({{FIND_EXISTING_MODULES}})
echo "$EXISTING_MODULES" | while read module; do
    if [ -z "$module" ]; then
        continue
    fi
    
    # Check if module can be reused
    if {{CAN_REUSE_MODULE}}; then
        REUSE_SUGGESTIONS="$REUSE_SUGGESTIONS\nModule: ${module}\n  Can be reused for: [use case]\n  Pros: [advantages]\n  Cons: [disadvantages]"
    fi
done

# Suggest existing code
EXISTING_CODE=$({{FIND_EXISTING_CODE}})
echo "$EXISTING_CODE" | while read code_item; do
    if [ -z "$code_item" ]; then
        continue
    fi
    
    # Check if code can be reused
    if {{CAN_REUSE_CODE}}; then
        REUSE_SUGGESTIONS="$REUSE_SUGGESTIONS\nCode: ${code_item}\n  Can be reused for: [use case]\n  Pros: [advantages]\n  Cons: [disadvantages]"
    fi
done
```

### Step 4: Identify Opportunities to Refactor Code for Reusability

Identify refactoring opportunities:

```bash
# Identify refactoring opportunities
REFACTORING_OPPORTUNITIES=""

# Detect duplicate code
DUPLICATE_CODE=$({{DETECT_DUPLICATE_CODE}})

# Identify code that should be moved to shared locations
SHARED_LOCATION_CANDIDATES=$({{IDENTIFY_SHARED_LOCATION_CANDIDATES}})

# Identify code that should be extracted to common modules
COMMON_MODULE_CANDIDATES=$({{IDENTIFY_COMMON_MODULE_CANDIDATES}})

# Identify code that should be extracted to core modules
CORE_MODULE_CANDIDATES=$({{IDENTIFY_CORE_MODULE_CANDIDATES}})

REFACTORING_OPPORTUNITIES="$REFACTORING_OPPORTUNITIES\nDuplicate Code: ${DUPLICATE_CODE}\nShared Location Candidates: ${SHARED_LOCATION_CANDIDATES}\nCommon Module Candidates: ${COMMON_MODULE_CANDIDATES}\nCore Module Candidates: ${CORE_MODULE_CANDIDATES}"
```

### Step 5: Present Reusable Options to User with Context and Pros/Cons

Prepare presentation of reusable options:

```bash
# Prepare presentation
REUSABLE_OPTIONS_PRESENTATION=""

# Format reuse suggestions with context
echo "$REUSE_SUGGESTIONS" | while IFS='|' read -r type item use_case pros cons; do
    if [ -z "$type" ] || [ -z "$item" ]; then
        continue
    fi
    
    # Add context from basepoints
    CONTEXT=$({{GET_BASEPOINT_CONTEXT}})
    
    # Add pros/cons from basepoints
    PROS_CONS=$({{GET_BASEPOINT_PROS_CONS}})
    
    REUSABLE_OPTIONS_PRESENTATION="$REUSABLE_OPTIONS_PRESENTATION\n**${type}: ${item}**\n  Use Case: ${use_case}\n  Context: ${CONTEXT}\n  Pros: ${pros}\n  Cons: ${cons}\n  Basepoints Info: ${PROS_CONS}"
done

# Format refactoring opportunities
echo "$REFACTORING_OPPORTUNITIES" | while IFS='|' read -r category candidates; do
    if [ -z "$category" ] || [ -z "$candidates" ]; then
        continue
    fi
    
    REUSABLE_OPTIONS_PRESENTATION="$REUSABLE_OPTIONS_PRESENTATION\n**Refactoring Opportunity: ${category}**\n  Candidates: ${candidates}\n  Recommendation: [suggestion]"
done
```

**Presentation Format:**

```
🔍 Reusable Code Detection Results

## Existing Code That Can Be Reused

**Module: [Module Name]**
- Can be reused for: [Use case]
- Context: [Context from basepoints]
- Pros: [Advantages]
- Cons: [Disadvantages]
- Basepoints Info: [Additional info from basepoints]

**Code: [Code Item]**
- Can be reused for: [Use case]
- Context: [Context from basepoints]
- Pros: [Advantages]
- Cons: [Disadvantages]
- Basepoints Info: [Additional info from basepoints]

## Refactoring Opportunities

**Duplicate Code**
- Candidates: [List of duplicate code locations]
- Recommendation: Extract to shared module

**Shared Location Candidates**
- Candidates: [Code that should be moved to shared locations]
- Recommendation: Move to [suggested location]

**Common Module Candidates**
- Candidates: [Code that should be extracted to common modules]
- Recommendation: Create common module at [suggested location]

**Core Module Candidates**
- Candidates: [Code that should be extracted to core modules]
- Recommendation: Create core module at [suggested location]
```

### Step 6: Store Detection Results

Cache reusable code detection results:

```bash
mkdir -p "$CACHE_PATH/deep-reading"

# Store detection results
cat > "$CACHE_PATH/deep-reading/reusable-code-detection.json" << EOF
{
  "similar_logic_patterns": $(echo "$SIMILAR_LOGIC_PATTERNS" | {{JSON_FORMAT}}),
  "reusable_code_patterns": $(echo "$REUSABLE_CODE_PATTERNS" | {{JSON_FORMAT}}),
  "reuse_suggestions": $(echo "$REUSE_SUGGESTIONS" | {{JSON_FORMAT}}),
  "refactoring_opportunities": $(echo "$REFACTORING_OPPORTUNITIES" | {{JSON_FORMAT}}),
  "reusable_options_presentation": $(echo "$REUSABLE_OPTIONS_PRESENTATION" | {{JSON_FORMAT}})
}
EOF

# Also create human-readable summary
cat > "$CACHE_PATH/deep-reading/reusable-code-detection-summary.md" << EOF
# Reusable Code Detection Results

## Similar Logic Patterns
[Summary of similar logic patterns detected]

## Reusable Code Patterns
[Summary of reusable code patterns detected]

## Reuse Suggestions
[Summary of existing modules and code that can be reused]

## Refactoring Opportunities
[Summary of opportunities to refactor code for reusability]

## Reusable Options Presentation
[Formatted presentation of reusable options with context and pros/cons]
EOF
```

## Important Constraints

- Must detect similar logic and reusable code patterns from deep reading
- Must suggest existing modules and code that can be reused
- Must identify opportunities to refactor code for reusability
- Must present reusable options to user with context and pros/cons
- Must use placeholder syntax ({{PLACEHOLDER}}) for project-specific parts that will be replaced during deploy-agents
- Must be technology-agnostic and work with any basepoint structure
- **CRITICAL**: All detection results must be stored in `agent-os/specs/[current-spec]/implementation/cache/deep-reading/`, not scattered around the codebase

   fi
   ```

2. Load basepoints knowledge (if available):
   ```bash
   # Determine spec path
   SPEC_PATH="agent-os/specs/[this-spec]"
   
   # Load extracted basepoints knowledge if available
   if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.json" ]; then
       EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.json")
       SCOPE_DETECTION=$(cat "$SPEC_PATH/implementation/cache/scope-detection/semantic-analysis.json" 2>/dev/null || echo "{}")
       KEYWORD_MATCHING=$(cat "$SPEC_PATH/implementation/cache/scope-detection/keyword-matching.json" 2>/dev/null || echo "{}")
   fi
   ```

3. Load deep reading results (if available):
   ```bash
   if [ -f "$SPEC_PATH/implementation/cache/deep-reading/implementation-analysis.json" ]; then
       DEEP_READING_RESULTS=$(cat "$SPEC_PATH/implementation/cache/deep-reading/implementation-analysis.json")
   fi
   
   if [ -f "$SPEC_PATH/implementation/cache/deep-reading/reusable-code-detection.json" ]; then
       REUSABLE_CODE_RESULTS=$(cat "$SPEC_PATH/implementation/cache/deep-reading/reusable-code-detection.json")
   fi
   ```

4. Analyze the provided spec.md, requirements.md, and visuals (if any)
5. Analyze patterns in the codebase, basepoints knowledge (if available), AND deep reading results (if available) according to its built-in workflow
6. Use basepoints knowledge and deep reading results to:
   - Guide implementation with extracted patterns
   - Suggest reusable code and modules during implementation
   - Reference project-specific standards and coding patterns
   - Use extracted testing approaches for test writing
7. Use deep reading results to:
   - Guide implementation with actual implementation patterns
   - Suggest reusable code and modules from actual implementation
   - Reference similar logic found in implementation
   - Consider refactoring opportunities
8. Implement the assigned task group according to requirements, standards, basepoints knowledge, and deep reading results
9. Update `agent-os/specs/[this-spec]/tasks.md` to update the tasks you've implemented to mark that as done by updating their checkbox to checked state: `- [x]`

## Guide your implementation using:
- **The existing patterns** that you've found and analyzed in the codebase AND basepoints knowledge (if available) AND deep reading results (if available)
- **Basepoints knowledge** (if available):
  - Extracted patterns from basepoints
  - Reusable code and modules from basepoints
  - Project-specific standards and coding patterns from basepoints
  - Testing approaches from basepoints
- **Deep reading results** (if available):
  - Patterns extracted from actual implementation files
  - Similar logic found in implementation
  - Reusable code blocks, functions, and classes from implementation
  - Refactoring opportunities identified
  - Implementation analysis (logic flow, data flow, control flow, dependencies, patterns)
- **Specific notes provided in requirements.md, spec.md AND/OR tasks.md**
- **Visuals provided (if any)** which would be located in `agent-os/specs/[this-spec]/planning/visuals/`
- **User Standards & Preferences** which are defined below.

## Self-verify and test your work by:
- Running ONLY the tests you've written (if any) and ensuring those tests pass.
- IF your task involves user-facing UI, and IF you have access to browser testing tools, open a browser and use the feature you've implemented as if you are a user to ensure a user can use the feature in the intended way.
  - Take screenshots of the views and UI elements you've tested and store those in `agent-os/specs/[this-spec]/verification/screenshots/`.  Do not store screenshots anywhere else in the codebase other than this location.
  - Analyze the screenshot(s) you've taken to check them against your current requirements.


## Display confirmation and next step

Display a summary of what was implemented.

IF all tasks are now marked as done (with `- [x]`) in tasks.md, display this message to user:

```
All tasks have been implemented: `agent-os/specs/[this-spec]/tasks.md`.

NEXT STEP 👉 Run `3-verify-implementation.md` to verify the implementation.
```

IF there are still tasks in tasks.md that have yet to be implemented (marked unfinished with `- [ ]`) then display this message to user:

```
Would you like to proceed with implementation of the remaining tasks in tasks.md?

If not, please specify which task group(s) to implement next.
```

## Step 5: Run Validation After Implementation

After each task group is implemented, run validation:

```bash
SPEC_PATH="agent-os/specs/[current-spec]"
COMMAND="implement-tasks"
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

# Validate References

## Core Responsibilities

1. **Find @agent-os/ References**: Scan files for internal references
2. **Resolve References to Paths**: Convert references to actual file paths
3. **Check Path Existence**: Verify each referenced path exists
4. **Output Broken References**: List any references that don't resolve
5. **Return Status**: Fail if critical references are broken

## Workflow

### Step 1: Define Scope

```bash
# SPEC_PATH should be set by the calling workflow
if [ -z "$SPEC_PATH" ]; then
    echo "❌ SPEC_PATH not set"
    exit 1
fi

echo "🔍 Validating references..."

# Files to check for references
FILES_TO_CHECK=""

# Add spec files
[ -f "$SPEC_PATH/spec.md" ] && FILES_TO_CHECK="$FILES_TO_CHECK $SPEC_PATH/spec.md"
[ -f "$SPEC_PATH/tasks.md" ] && FILES_TO_CHECK="$FILES_TO_CHECK $SPEC_PATH/tasks.md"
[ -f "$SPEC_PATH/planning/requirements.md" ] && FILES_TO_CHECK="$FILES_TO_CHECK $SPEC_PATH/planning/requirements.md"
[ -f "$SPEC_PATH/planning/architecture.md" ] && FILES_TO_CHECK="$FILES_TO_CHECK $SPEC_PATH/planning/architecture.md"

FILE_COUNT=$(echo "$FILES_TO_CHECK" | wc -w | tr -d ' ')
echo "   Files to check: $FILE_COUNT"
```

### Step 2: Find @agent-os/ References

```bash
echo ""
echo "📋 Finding @agent-os/ references..."
echo ""

ALL_REFERENCES=""
REFERENCE_COUNT=0

for file in $FILES_TO_CHECK; do
    if [ -f "$file" ]; then
        # Find @agent-os/ references
        REFS=$(grep -oE '@agent-os/[^[:space:]]+' "$file" 2>/dev/null | sort -u)
        
        if [ -n "$REFS" ]; then
            REF_COUNT=$(echo "$REFS" | wc -l | tr -d ' ')
            echo "  Found $REF_COUNT references in $(basename "$file")"
            ALL_REFERENCES="$ALL_REFERENCES
$REFS"
            REFERENCE_COUNT=$((REFERENCE_COUNT + REF_COUNT))
        fi
    fi
done

# Also check for standard markdown links
for file in $FILES_TO_CHECK; do
    if [ -f "$file" ]; then
        # Find [text](path) style links to internal files
        LINK_REFS=$(grep -oE '\]\([^)]+\.md\)' "$file" 2>/dev/null | sed 's/\](\(.*\))/\1/' | sort -u)
        
        if [ -n "$LINK_REFS" ]; then
            LINK_COUNT=$(echo "$LINK_REFS" | wc -l | tr -d ' ')
            echo "  Found $LINK_COUNT markdown links in $(basename "$file")"
        fi
    fi
done

ALL_REFERENCES=$(echo "$ALL_REFERENCES" | grep -v '^$' | sort -u)
echo ""
echo "   Total unique references: $REFERENCE_COUNT"
```

### Step 3: Resolve and Check References

```bash
echo ""
echo "🔗 Checking reference resolution..."
echo ""

BROKEN_REFS=""
BROKEN_COUNT=0
VALID_COUNT=0

for ref in $ALL_REFERENCES; do
    # Skip empty lines
    [ -z "$ref" ] && continue
    
    # Convert @agent-os/ to actual path
    ACTUAL_PATH=$(echo "$ref" | sed 's/@//')
    
    # Check if path exists
    if [ -f "$ACTUAL_PATH" ] || [ -d "$ACTUAL_PATH" ]; then
        echo "  ✅ $ref"
        VALID_COUNT=$((VALID_COUNT + 1))
    else
        echo "  ❌ $ref (not found: $ACTUAL_PATH)"
        BROKEN_REFS="$BROKEN_REFS
$ref"
        BROKEN_COUNT=$((BROKEN_COUNT + 1))
    fi
done
```

### Step 4: Classify Broken References

```bash
echo ""
echo "📊 Classifying broken references..."
echo ""

CRITICAL_BROKEN=0
WARNING_BROKEN=0

for ref in $BROKEN_REFS; do
    [ -z "$ref" ] && continue
    
    # Critical: references to required files (spec, tasks, workflows)
    if echo "$ref" | grep -qE 'spec\.md|tasks\.md|workflows/|commands/'; then
        echo "  ⛔ CRITICAL: $ref"
        CRITICAL_BROKEN=$((CRITICAL_BROKEN + 1))
    else
        echo "  ⚠️ WARNING: $ref"
        WARNING_BROKEN=$((WARNING_BROKEN + 1))
    fi
done

if [ "$BROKEN_COUNT" -eq 0 ]; then
    echo "  No broken references found"
fi
```

### Step 5: Generate Output and Exit Code

```bash
echo ""
echo "═══════════════════════════════════════════════════════"
echo "  REFERENCE VALIDATION RESULTS"
echo "═══════════════════════════════════════════════════════"
echo ""
echo "  Spec Path: $SPEC_PATH"
echo ""
echo "  Total References: $REFERENCE_COUNT"
echo "  Valid: $VALID_COUNT"
echo "  Broken: $BROKEN_COUNT"
echo "    - Critical: $CRITICAL_BROKEN"
echo "    - Warnings: $WARNING_BROKEN"
echo ""

# Fail only on critical broken references
if [ "$CRITICAL_BROKEN" -gt 0 ]; then
    echo "  Status: ❌ FAILED ($CRITICAL_BROKEN critical broken references)"
    VALIDATION_STATUS="FAILED"
    EXIT_CODE=1
elif [ "$WARNING_BROKEN" -gt 0 ]; then
    echo "  Status: ⚠️ PASSED WITH WARNINGS ($WARNING_BROKEN broken references)"
    VALIDATION_STATUS="PASSED_WITH_WARNINGS"
    EXIT_CODE=0
else
    echo "  Status: ✅ PASSED"
    VALIDATION_STATUS="PASSED"
    EXIT_CODE=0
fi

echo ""
echo "═══════════════════════════════════════════════════════"

# Store result for validation report
mkdir -p "$SPEC_PATH/implementation/cache"
cat > "$SPEC_PATH/implementation/cache/references-validation.txt" << EOF
VALIDATOR=references
TIMESTAMP=$(date +%s)
TOTAL_REFS=$REFERENCE_COUNT
VALID=$VALID_COUNT
BROKEN=$BROKEN_COUNT
CRITICAL_BROKEN=$CRITICAL_BROKEN
WARNING_BROKEN=$WARNING_BROKEN
STATUS=$VALIDATION_STATUS
EOF

# Export and exit
export REFERENCES_STATUS="$VALIDATION_STATUS"
export REFERENCES_BROKEN="$BROKEN_COUNT"
exit $EXIT_CODE
```

## Important Constraints

- Must find all @agent-os/ references
- Must resolve references to actual paths
- Must classify broken references by severity
- Must fail only on critical broken references
- Works for any project (project-agnostic)
- **CRITICAL**: This is a core validator that runs for ALL projects

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

```

## Step 6: Generate Resource Checklist

Generate a checklist of all resources consulted during implementation:

```bash
# Organize and Cache Basepoints Knowledge

## Core Responsibilities

1. **Structure Extracted Knowledge**: Organize knowledge by abstraction layer, category, and module
2. **Implement Per-Spec Caching**: Store extracted knowledge in the spec's implementation folder
3. **Provide Query Utilities**: Enable querying cached knowledge by category, layer, or module
4. **Manage Cache Lifecycle**: Handle cache creation, validation, and reuse across commands

## Workflow

### Step 1: Determine Cache Location (Per-Spec)

Caching strategy: **Per-spec caching** (not global, not per-command)

```bash
# SPEC_PATH should be set by the calling command
# This ensures knowledge is cached per-spec and reused across commands in the same spec

if [ -n "$SPEC_PATH" ]; then
    CACHE_PATH="$SPEC_PATH/implementation/cache"
    echo "📁 Per-spec cache location: $CACHE_PATH"
else
    # Fallback for non-spec contexts
    CACHE_PATH="agent-os/output/basepoints-extraction"
    echo "⚠️ No SPEC_PATH set. Using fallback cache: $CACHE_PATH"
fi

# Create cache directory structure
mkdir -p "$CACHE_PATH"
mkdir -p "$CACHE_PATH/on-demand"
```

### Step 2: Check Cache Validity

Determine if existing cache can be reused:

```bash
check_cache_validity() {
    CACHE_FILE="$CACHE_PATH/basepoints-knowledge.md"
    
    if [ ! -f "$CACHE_FILE" ]; then
        echo "CACHE_STATUS=missing"
        return 1
    fi
    
    # Check cache age (optional - cache is valid for the spec's lifetime)
    CACHE_MTIME=$(stat -f %m "$CACHE_FILE" 2>/dev/null || stat -c %Y "$CACHE_FILE" 2>/dev/null)
    CURRENT_TIME=$(date +%s)
    CACHE_AGE=$((CURRENT_TIME - CACHE_MTIME))
    
    # Cache is valid if it exists (per-spec caching means it's always valid within the spec)
    echo "✅ Cache found (age: ${CACHE_AGE}s)"
    echo "CACHE_STATUS=valid"
    return 0
}

# Run check
check_cache_validity
```

### Step 3: Check Cache Invalidation Conditions

Cache should be invalidated if:

```bash
should_invalidate_cache() {
    CACHE_FILE="$CACHE_PATH/basepoints-knowledge.md"
    BASEPOINTS_PATH="agent-os/basepoints"
    
    # Condition 1: No cache exists
    if [ ! -f "$CACHE_FILE" ]; then
        echo "invalidate:no_cache"
        return 0
    fi
    
    # Condition 2: Basepoints modified after cache creation
    if [ -d "$BASEPOINTS_PATH" ]; then
        CACHE_MTIME=$(stat -f %m "$CACHE_FILE" 2>/dev/null || stat -c %Y "$CACHE_FILE" 2>/dev/null)
        
        # Find most recent basepoint modification
        LATEST_BP_MTIME=$(find "$BASEPOINTS_PATH" -name "*.md" -type f -exec stat -f %m {} \; 2>/dev/null | sort -rn | head -1)
        if [ -z "$LATEST_BP_MTIME" ]; then
            LATEST_BP_MTIME=$(find "$BASEPOINTS_PATH" -name "*.md" -type f -exec stat -c %Y {} \; 2>/dev/null | sort -rn | head -1)
        fi
        
        if [ -n "$LATEST_BP_MTIME" ] && [ "$LATEST_BP_MTIME" -gt "$CACHE_MTIME" ]; then
            echo "invalidate:basepoints_updated"
            return 0
        fi
    fi
    
    # Condition 3: Force refresh requested
    if [ "$FORCE_REFRESH" = "true" ]; then
        echo "invalidate:force_refresh"
        return 0
    fi
    
    # Cache is valid
    echo "valid"
    return 1
}

# Check invalidation
INVALIDATION_REASON=$(should_invalidate_cache)
if [ "$INVALIDATION_REASON" != "valid" ]; then
    echo "🔄 Cache invalidation: $INVALIDATION_REASON"
    NEED_EXTRACTION="true"
else
    echo "✅ Cache is valid, reusing existing knowledge"
    NEED_EXTRACTION="false"
fi
```

### Step 4: Load or Extract Knowledge

```bash
if [ "$NEED_EXTRACTION" = "true" ]; then
    echo "📖 Extracting fresh knowledge from basepoints..."
    
    # Determine workflow base path (agent-os when installed, profiles/default for template)
    if [ -d "agent-os/workflows" ]; then
        WORKFLOWS_BASE="agent-os/workflows"
    else
        WORKFLOWS_BASE="profiles/default/workflows"
    fi
    
    # Call the automatic extraction workflow
    # This will populate the cache
    source "$WORKFLOWS_BASE/basepoints/extract-basepoints-knowledge-automatic.md"
    
    echo "✅ Fresh extraction complete"
else
    echo "📂 Loading knowledge from cache..."
    
    if [ -f "$CACHE_PATH/basepoints-knowledge.md" ]; then
        CACHED_KNOWLEDGE=$(cat "$CACHE_PATH/basepoints-knowledge.md")
        echo "✅ Loaded $(wc -l < "$CACHE_PATH/basepoints-knowledge.md" | tr -d ' ') lines from cache"
    fi
fi
```

### Step 5: Provide Query Functions

Enable querying cached knowledge:

```bash
# Query patterns from cache
query_patterns() {
    local LAYER="${1:-all}"
    local CACHE_FILE="$CACHE_PATH/basepoints-knowledge.md"
    
    if [ ! -f "$CACHE_FILE" ]; then
        echo ""
        return 1
    fi
    
    if [ "$LAYER" = "all" ]; then
        sed -n '/## Patterns/,/^## [^P]/p' "$CACHE_FILE" | head -n -1
    else
        # Extract patterns for specific layer
        sed -n "/### From:.*$LAYER/,/^### From:/p" "$CACHE_FILE" | head -n -1
    fi
}

# Query standards from cache
query_standards() {
    local CATEGORY="${1:-all}"
    local CACHE_FILE="$CACHE_PATH/basepoints-knowledge.md"
    
    if [ ! -f "$CACHE_FILE" ]; then
        echo ""
        return 1
    fi
    
    sed -n '/## Standards/,/^## [^S]/p' "$CACHE_FILE" | head -n -1
}

# Query flows from cache
query_flows() {
    local CACHE_FILE="$CACHE_PATH/basepoints-knowledge.md"
    
    if [ ! -f "$CACHE_FILE" ]; then
        echo ""
        return 1
    fi
    
    sed -n '/## Flows/,/^## [^F]/p' "$CACHE_FILE" | head -n -1
}

# Query strategies from cache
query_strategies() {
    local CACHE_FILE="$CACHE_PATH/basepoints-knowledge.md"
    
    if [ ! -f "$CACHE_FILE" ]; then
        echo ""
        return 1
    fi
    
    sed -n '/## Strategies/,/^## [^S]/p' "$CACHE_FILE" | head -n -1
}

# Query by module name
query_by_module() {
    local MODULE="$1"
    local CACHE_FILE="$CACHE_PATH/basepoints-knowledge.md"
    
    if [ ! -f "$CACHE_FILE" ]; then
        echo ""
        return 1
    fi
    
    grep -A 50 "### From:.*$MODULE" "$CACHE_FILE" | sed '/^### From:/q' | head -n -1
}

# Get detected abstraction layer
get_detected_layer() {
    local LAYER_FILE="$CACHE_PATH/detected-layer.txt"
    
    if [ -f "$LAYER_FILE" ]; then
        cat "$LAYER_FILE"
    else
        echo "unknown"
    fi
}
```

### Step 6: Cache Statistics

Provide cache information:

```bash
get_cache_stats() {
    echo "═══════════════════════════════════════════════════════"
    echo "  CACHE STATISTICS"
    echo "═══════════════════════════════════════════════════════"
    
    if [ -f "$CACHE_PATH/basepoints-knowledge.md" ]; then
        CACHE_SIZE=$(wc -c < "$CACHE_PATH/basepoints-knowledge.md" | tr -d ' ')
        CACHE_LINES=$(wc -l < "$CACHE_PATH/basepoints-knowledge.md" | tr -d ' ')
        CACHE_MTIME=$(stat -f %Sm -t "%Y-%m-%d %H:%M:%S" "$CACHE_PATH/basepoints-knowledge.md" 2>/dev/null || stat -c %y "$CACHE_PATH/basepoints-knowledge.md" 2>/dev/null | cut -d. -f1)
        
        echo "  Main Cache:"
        echo "    Path: $CACHE_PATH/basepoints-knowledge.md"
        echo "    Size: $CACHE_SIZE bytes"
        echo "    Lines: $CACHE_LINES"
        echo "    Modified: $CACHE_MTIME"
    else
        echo "  Main Cache: Not found"
    fi
    
    if [ -f "$CACHE_PATH/detected-layer.txt" ]; then
        DETECTED=$(cat "$CACHE_PATH/detected-layer.txt")
        echo "  Detected Layer: $DETECTED"
    fi
    
    ON_DEMAND_COUNT=$(find "$CACHE_PATH/on-demand" -name "*.md" 2>/dev/null | wc -l | tr -d ' ')
    echo "  On-Demand Extractions: $ON_DEMAND_COUNT"
    
    echo "═══════════════════════════════════════════════════════"
}

# Display stats
get_cache_stats
```

### Step 7: Cache Reuse Across Commands

The per-spec caching strategy ensures:

```bash
# IMPORTANT: Cache Reuse Strategy
# 
# 1. shape-spec: Extracts knowledge, creates cache
# 2. write-spec: Reuses cache from shape-spec (same SPEC_PATH)
# 3. create-tasks: Reuses cache
# 4. implement-tasks: Reuses cache
# 5. orchestrate-tasks: Reuses cache
#
# Cache is stored at: $SPEC_PATH/implementation/cache/
# This location is shared across all commands for the same spec

ensure_cache_available() {
    local CACHE_FILE="$CACHE_PATH/basepoints-knowledge.md"
    
    if [ -f "$CACHE_FILE" ]; then
        echo "✅ Cache available for reuse"
        return 0
    else
        echo "⚠️ Cache not found. Triggering extraction..."
        # Trigger extraction
        NEED_EXTRACTION="true"
        return 1
    fi
}
```

### Step 8: Export Functions for Commands

Make query functions available to calling commands:

```bash
# Export the cache path for other workflows
export BASEPOINTS_CACHE_PATH="$CACHE_PATH"

# Export query functions (in zsh/bash)
export -f query_patterns 2>/dev/null || true
export -f query_standards 2>/dev/null || true
export -f query_flows 2>/dev/null || true
export -f query_strategies 2>/dev/null || true
export -f query_by_module 2>/dev/null || true
export -f get_detected_layer 2>/dev/null || true

echo "✅ Cache utilities ready"
echo "   Use: query_patterns, query_standards, query_flows, query_strategies"
echo "   Use: query_by_module <module-name>"
echo "   Use: get_detected_layer"
```

## Cache File Structure

After extraction, the cache contains:

```
$SPEC_PATH/implementation/cache/
├── basepoints-knowledge.md       # Main knowledge cache
├── detected-layer.txt            # Detected abstraction layer
├── resources-consulted.md        # List of consulted sources
└── on-demand/                    # On-demand extraction results
    ├── on-demand-*.md            # Timestamped on-demand extractions
    └── ...
```

## Important Constraints

- **Per-spec caching**: All cached documents stored in `$SPEC_PATH/implementation/cache/`
- **Reuse across commands**: Cache is shared across all commands for the same spec
- **Invalidation conditions**: Basepoints updated, force refresh, or cache missing
- **Graceful fallback**: Works even when basepoints don't exist
- **Query utilities**: Functions to query cached knowledge by category/layer/module
- Must preserve source information for traceability
- Must be technology-agnostic and work with any basepoint structure
- **CRITICAL**: Never scatter cache files around the codebase; always use the spec's implementation folder

```

Include in the implementation summary:
- ✅ Tasks implemented: [X of Y]
- ✅ Coding patterns applied: [List from basepoints]
- ✅ Standards referenced: [List from detected layer]
- ✅ Validation: [PASSED / WARNINGS]
- ✅ Resources consulted: See `implementation/cache/resources-consulted.md`

## Step 7: Save Handoff

[38;2;255;185;0m⚠️  Circular workflow reference detected: prompting/save-handoff[0m
# Save Handoff Context

Called at the END of every command to save context for the next command.

## Purpose

- Capture what was completed in the current command
- Prepare context for the next command in the workflow
- Enable seamless continuation between commands

## When Called

- At the END of every command execution
- After command logic completes successfully
- Before command returns/finishes

## Process

### Step 1: Determine Handoff Target

Identify which command typically follows the current command:

| Current Command | Next Command | Context to Pass |
|----------------|--------------|-----------------|
| /shape-spec | /write-spec | Requirements summary, features, constraints |
| /write-spec | /create-tasks | Spec summary, components, complexity |
| /create-tasks | /implement-tasks or /orchestrate-tasks | Task list, dependencies, sequence |
| /orchestrate-tasks | /implement-tasks | Task groups, assignments, standards |
| /implement-tasks | (none or /verify-implementation) | Implementation summary, outcomes |

### Step 2: Extract Key Information

From the current command's output, extract:

- **What was completed**: Files created/modified, outcomes achieved
- **Key decisions**: Important choices made, constraints identified
- **Context for next**: What the next command needs to know

### Step 3: Build Handoff Document

Create handoff markdown file:

```markdown
# Handoff: [from-command] → [to-command]

## Completed
[Summary of what was accomplished in current command]

## Key Decisions
[Important choices, constraints, patterns identified]

## For [to-command]
[Specific context needed for next command]
- Focus areas: [list]
- Architecture hints: [from patterns]
- Complexity: [assessment]
- Dependencies: [list]

## Timestamp
[ISO 8601 timestamp]
```

### Step 4: Save to Handoff Location

```bash
# Create handoff directory if it doesn't exist
mkdir -p agent-os/output/handoff

# Save handoff context
cat > agent-os/output/handoff/current.md << EOF
[Handoff document content]
EOF

echo "✅ Handoff context saved for $TO_COMMAND"
```

## Usage in Commands

At the end of each command template:

```markdown
## Step N: Save Handoff

{{workflows/prompting/save-handoff}}
```

The workflow automatically:
1. Detects current command name
2. Determines next command in sequence
3. Extracts relevant context from current output
4. Saves handoff document

## Handoff Templates

### shape-spec → write-spec

```markdown
# Handoff: shape-spec → write-spec

## Completed
- Requirements documented in `agent-os/specs/[date]-[name]/planning/requirements.md`
- Key features identified: [feature list]
- Constraints established: [constraint list]

## Key Decisions
- Architecture approach: [choice]
- Technology selection: [if relevant]
- Complexity level: [simple/moderate/complex]

## For write-spec
- Focus areas: [main areas from requirements]
- Architecture hints: [patterns from basepoints that apply]
- Complexity assessment: [simple/moderate/complex]
- Standards to reference: [list]
```

### write-spec → create-tasks

```markdown
# Handoff: write-spec → create-tasks

## Completed
- Detailed specification in `agent-os/specs/[date]-[name]/spec.md`
- Architecture defined: [components, layers]
- Interfaces documented: [APIs, contracts]

## Key Decisions
- Implementation approach: [choice]
- Layer assignments: [if relevant]
- Dependency structure: [if relevant]

## For create-tasks
- Task granularity: [based on complexity]
- Suggested groupings: [by feature/layer/domain]
- Dependencies identified: [list of task dependencies]
- Estimated complexity: [overall assessment]
```

### create-tasks → implement-tasks

```markdown
# Handoff: create-tasks → implement-tasks

## Completed
- Tasks listed in `agent-os/specs/[date]-[name]/tasks.md`
- Dependencies mapped: [dependency graph]
- Complexity estimated: [per task group]

## Key Decisions
- Task grouping strategy: [approach]
- Implementation order: [sequence]

## For implement-tasks
- Implementation order: [recommended sequence]
- Layer specialists: [suggested assignments if available]
- Validation commands: [from project profile]
- Critical tasks: [tasks requiring special attention]
```

## Integration

Handoff context is automatically loaded by `construct-prompt.md` at the start of the next command, ensuring seamless context flow between commands.


## User Standards & Preferences Compliance

IMPORTANT: Ensure that the tasks list is ALIGNED and DOES NOT CONFLICT with the user's preferences and standards as detailed in the following files:

@agent-os/standards/documentation/standards.md
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
@agent-os/standards/process/development-workflow.md
@agent-os/standards/quality/assurance.md
@agent-os/standards/testing/test-writing.md

# PHASE 3: Verify Implementation

Now that we've implemented all tasks in tasks.md, we must run final verifications and produce a verification report using the following MULTI-PHASE workflow:

## Workflow

### Step 1: Ensure tasks.md has been updated

Check `agent-os/specs/[this-spec]/tasks.md` and ensure that all tasks and their sub-tasks are marked as completed with `- [x]`.

If a task is still marked incomplete, then verify that it has in fact been completed by checking the following:
- Run a brief spot check in the code to find evidence that this task's details have been implemented
- Check for existence of an implementation report titled using this task's title in `agent-os/spec/[this-spec]/implementation/` folder.

IF you have concluded that this task has been completed, then mark it's checkbox and its' sub-tasks checkboxes as completed with `- [x]`.

IF you have concluded that this task has NOT been completed, then mark this checkbox with ⚠️ and note it's incompleteness in your verification report.


### Step 2: Update roadmap (if applicable)

If project documentation exists (e.g., roadmap, changelog, project plan), check to see whether any item(s) match the description of the current spec that has just been implemented. If so, then ensure that these item(s) are marked as completed by updating their checkbox(s) to `- [x]`.

This step is optional and only applies if the project maintains such documentation.


### Step 3: Run entire tests suite

Run the entire tests suite for the application so that ALL tests run.  Verify how many tests are passing and how many have failed or produced errors.

Include these counts and the list of failed tests in your final verification report.

DO NOT attempt to fix any failing tests.  Just note their failures in your final verification report.


### Step 4: Create final verification report

Create your final verification report in `agent-os/specs/[this-spec]/verifications/final-verification.html`.

The content of this report should follow this structure:

```markdown
# Verification Report: [Spec Title]

**Spec:** `[spec-name]`
**Date:** [Current Date]
**Verifier:** implementation-verifier
**Status:** ✅ Passed | ⚠️ Passed with Issues | ❌ Failed

---

## Executive Summary

[Brief 2-3 sentence overview of the verification results and overall implementation quality]

---

## 1. Tasks Verification

**Status:** ✅ All Complete | ⚠️ Issues Found

### Completed Tasks
- [x] Task Group 1: [Title]
  - [x] Subtask 1.1
  - [x] Subtask 1.2
- [x] Task Group 2: [Title]
  - [x] Subtask 2.1

### Incomplete or Issues
[List any tasks that were found incomplete or have issues, or note "None" if all complete]

---

## 2. Documentation Verification

**Status:** ✅ Complete | ⚠️ Issues Found

### Implementation Documentation
- [x] Task Group 1 Implementation: `implementations/1-[task-name]-implementation.md`
- [x] Task Group 2 Implementation: `implementations/2-[task-name]-implementation.md`

### Verification Documentation
[List verification documents from area verifiers if applicable]

### Missing Documentation
[List any missing documentation, or note "None"]

---

## 3. Roadmap Updates

**Status:** ✅ Updated | ⚠️ No Updates Needed | ❌ Issues Found

### Updated Roadmap Items
- [x] [Roadmap item that was marked complete]

### Notes
[Any relevant notes about roadmap updates, or note if no updates were needed]

---

## 4. Test Suite Results

**Status:** ✅ All Passing | ⚠️ Some Failures | ❌ Critical Failures

### Test Summary
- **Total Tests:** [count]
- **Passing:** [count]
- **Failing:** [count]
- **Errors:** [count]

### Failed Tests
[List any failing tests with their descriptions, or note "None - all tests passing"]

### Notes
[Any additional context about test results, known issues, or regressions]
```


### Step 5: Capture Session Feedback

After verification completes, capture the implementation outcome for session learning:

[38;2;255;185;0m⚠️  Circular workflow reference detected: learning/capture-session-feedback[0m
# Capture Session Feedback

Called automatically after /implement-tasks to record outcome.

## Purpose

Capture implementation outcome data for session learning:
- Success/failure status
- Patterns used during implementation
- Duration and performance metrics
- Errors encountered
- Validation results

## Inputs

- Spec name (from context)
- Task count (tasks implemented)
- Outcome (pass/fail)
- Validation results
- Patterns used (from implementation)
- Errors encountered (if any)
- Duration (time taken)

## Process

### Step 1: Load or Create Current Session File

```bash
SESSION_DIR="agent-os/output/session-feedback"
SESSION_FILE="$SESSION_DIR/current-session.md"

# Create directory structure if it doesn't exist
mkdir -p "$SESSION_DIR/patterns"
mkdir -p "$SESSION_DIR/prompts"
mkdir -p "$SESSION_DIR/history"

# Create session file if it doesn't exist
if [ ! -f "$SESSION_FILE" ]; then
    TODAY=$(date +%Y-%m-%d)
    cat > "$SESSION_FILE" << EOF
# Session: $TODAY

## Metadata
- Started: $(date -Iseconds)
- Last Updated: $(date -Iseconds)
- Implementations: 0
- Success Rate: 0%

## Implementations

| # | Spec | Tasks | Outcome | Duration | Notes |
|---|------|-------|---------|----------|-------|

## Patterns Observed

### Successful
(Patterns will be listed here as they are detected)

### Failed
(Failed patterns will be listed here as they are detected)

## Prompt Effectiveness

### Worked Well
(Prompts that worked well will be listed here)

### Needed Clarification
(Prompts that needed clarification will be listed here)
EOF
fi
```

### Step 2: Extract Implementation Data

```bash
# Get current implementation count
CURRENT_COUNT=$(grep -c "| [0-9]" "$SESSION_FILE" || echo "0")
NEXT_NUMBER=$((CURRENT_COUNT + 1))

# Extract data from context
SPEC_NAME="$SPEC_NAME"  # From context
TASK_COUNT="$TASK_COUNT"  # From context
OUTCOME="$OUTCOME"  # "✅ Pass" or "❌ Fail"
DURATION="$DURATION"  # From timing
NOTES="$NOTES"  # Any additional notes
PATTERNS_USED="$PATTERNS_USED"  # From implementation analysis
ERRORS="$ERRORS"  # Error messages if any
```

### Step 3: Append Implementation Record

```bash
# Append to implementations table
TEMP_FILE=$(mktemp)
awk -v line="| $NEXT_NUMBER | $SPEC_NAME | $TASK_COUNT | $OUTCOME | $DURATION | $NOTES |" \
    '/^\| # \| Spec \| Tasks \| Outcome \| Duration \| Notes \|$/ {print; print line; next} {print}' \
    "$SESSION_FILE" > "$TEMP_FILE" && mv "$TEMP_FILE" "$SESSION_FILE"
```

### Step 4: Update Metadata

```bash
# Update last updated timestamp
sed -i '' "s/- Last Updated:.*/- Last Updated: $(date -Iseconds)/" "$SESSION_FILE"

# Calculate and update success rate
TOTAL_IMPLEMENTATIONS=$NEXT_NUMBER
SUCCESSFUL=$(grep -c "✅ Pass" "$SESSION_FILE" || echo "0")
SUCCESS_RATE=$(awk "BEGIN {printf \"%.0f\", ($SUCCESSFUL / $TOTAL_IMPLEMENTATIONS) * 100}")

# Update metadata
sed -i '' "s/- Implementations:.*/- Implementations: $TOTAL_IMPLEMENTATIONS/" "$SESSION_FILE"
sed -i '' "s/- Success Rate:.*/- Success Rate: ${SUCCESS_RATE}%/" "$SESSION_FILE"
```

### Step 5: Record Patterns Used

```bash
# Extract patterns from implementation
# This is a placeholder - actual pattern extraction happens in extract-session-patterns workflow
# Here we just note which patterns were used
if [ -n "$PATTERNS_USED" ]; then
    echo "Patterns used in implementation #$NEXT_NUMBER: $PATTERNS_USED" >> "$SESSION_DIR/patterns/raw-patterns.txt"
fi
```

### Step 6: Record Errors (if any)

```bash
# If implementation failed, record error details
if [ "$OUTCOME" = "❌ Fail" ] && [ -n "$ERRORS" ]; then
    cat >> "$SESSION_FILE" << EOF

### Failed Implementation #$NEXT_NUMBER
- Spec: $SPEC_NAME
- Error: $ERRORS
- Duration: $DURATION
EOF
fi
```

### Step 7: Record Prompt Effectiveness

```bash
# Track if prompts worked well or needed clarification
if [ "$OUTCOME" = "✅ Pass" ] && [ -z "$ERRORS" ]; then
    # Mark as worked well
    echo "Implementation #$NEXT_NUMBER: Passed without issues" >> "$SESSION_DIR/prompts/effective-raw.txt"
else
    # Mark as needing improvement
    echo "Implementation #$NEXT_NUMBER: $ERRORS" >> "$SESSION_DIR/prompts/needs-improvement-raw.txt"
fi
```

## Output

Updates: `agent-os/output/session-feedback/current-session.md`

The session file is updated with:
- New implementation record in table
- Updated metadata (count, success rate, last updated)
- Error details (if failed)
- Raw pattern and prompt data for later analysis

## Usage

Called automatically at the end of `/implement-tasks` after verification:

```markdown
## Step N: Capture Session Feedback

{{workflows/learning/capture-session-feedback}}
```

## Notes

- Session file is created automatically if it doesn't exist
- Patterns are recorded in raw format for later extraction
- Prompt effectiveness is tracked for learning
- All data is timestamped for analysis
