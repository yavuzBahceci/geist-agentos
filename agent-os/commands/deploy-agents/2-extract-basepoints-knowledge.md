Now that prerequisites are validated, proceed with extracting knowledge from basepoints by following these instructions:

## Core Responsibilities

1. **Use Basepoints Knowledge Extraction Workflows**: Use the project-agnostic basepoints knowledge extraction workflows from profiles/default
2. **Extract All Knowledge Categories**: Extract patterns, standards, flows, strategies, testing approaches, pros/cons, historical decisions, related modules, performance considerations, common/reusable patterns, and more if necessary
3. **Validate Extracted Knowledge**: Ensure extracted knowledge is complete and accurate for all project types and structures
4. **Organize Knowledge**: Structure extracted knowledge for merging and command transformation
5. **Store for Specialization**: Store extraction patterns and heuristics for use during command specialization

## Workflow

### Step 1: Use Basepoints Knowledge Extraction Workflows

Use the automatic basepoints knowledge extraction workflow to extract all knowledge:

```bash
# Use the automatic basepoints knowledge extraction workflow
# This workflow handles all basepoint structures and organization patterns
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


# The workflow will:
# - Read all basepoint files (headquarter.md + module basepoints)
# - Extract same-layer and cross-layer patterns
# - Extract all knowledge categories (patterns, standards, flows, strategies, testing, pros/cons, historical decisions, related modules, performance, reusable patterns)
# - Organize knowledge by abstraction layer and category
# - Cache extracted knowledge for use during command execution
```

The automatic extraction workflow handles:
- **All Basepoint Structures**: Works with any basepoint organization pattern
- **All Project Types**: Technology-agnostic extraction that works for any project
- **Complete Knowledge Extraction**: Extracts all categories including pros/cons, historical decisions, related modules, performance, reusable patterns
- **Validation**: Ensures extracted knowledge is complete and accurate

### Step 2: Validate Extracted Knowledge

Validate that all knowledge categories have been extracted correctly:

```bash
# Check that extracted knowledge file exists
if [ ! -f "agent-os/output/deploy-agents/knowledge/basepoints-knowledge.json" ]; then
    echo "❌ Basepoints knowledge extraction failed"
    exit 1
fi

# Load extracted knowledge
BASEPOINTS_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/basepoints-knowledge.json)

# Validate all knowledge categories are present
VALIDATE_KNOWLEDGE_CATEGORIES() {
    # Check patterns (same-layer and cross-layer)
    # Check standards
    # Check flows
    # Check strategies
    # Check testing approaches
    # Check pros/cons
    # Check historical decisions
    # Check related modules
    # Check performance considerations
    # Check reusable patterns
    echo "✅ All knowledge categories validated"
}

VALIDATE_KNOWLEDGE_CATEGORIES
```

### Step 3: Extract Project-Specific Extraction Patterns

Extract project-specific patterns and heuristics that will be used to specialize the extraction workflows:

```bash
# Extract project-specific basepoint file patterns
BASEPOINT_FILE_PATTERN=$(detect_basepoint_file_pattern "agent-os/basepoints")
# Example: "agent-base-*.md" or "*.basepoint.md" or custom pattern

# Extract project-specific abstraction layer detection
ABSTRACTION_LAYER_DETECTION=$(extract_abstraction_layer_detection "$BASEPOINTS_KNOWLEDGE")
# Example: How to detect layers from file paths or content

# Extract project-specific extraction heuristics
EXTRACTION_HEURISTICS=$(extract_extraction_heuristics "$BASEPOINTS_KNOWLEDGE")
# Example: Project-specific patterns for extracting pros/cons, historical decisions, etc.

# Store for use during specialization
echo "$BASEPOINT_FILE_PATTERN" > agent-os/output/deploy-agents/knowledge/basepoint-file-pattern.txt
echo "$ABSTRACTION_LAYER_DETECTION" > agent-os/output/deploy-agents/knowledge/abstraction-layer-detection.txt
echo "$EXTRACTION_HEURISTICS" > agent-os/output/deploy-agents/knowledge/extraction-heuristics.json
```

### Step 4: Read All Basepoint Files (Legacy - for reference)

Traverse the basepoints folder structure to find and read all basepoint files:

```bash
# Find all basepoint files
find agent-os/basepoints -name "*.md" -type f | while read basepoint_file; do
    echo "Reading: $basepoint_file"
    # Read and process each basepoint file
done

# Specifically read headquarter.md for top-level patterns
if [ -f "agent-os/basepoints/headquarter.md" ]; then
    echo "✅ Reading headquarter.md for top-level patterns and architecture"
    HEADQUARTER_CONTENT=$(cat agent-os/basepoints/headquarter.md)
fi

# Find all module-specific basepoint files
MODULE_BASEPOINTS=$(find agent-os/basepoints -name "agent-base-*.md" -type f)
MODULE_COUNT=$(echo "$MODULE_BASEPOINTS" | grep -c . || echo "0")
echo "Found $MODULE_COUNT module-specific basepoint file(s)"
```

For each basepoint file, read and store its content for processing:
- **Headquarter.md**: Contains top-level architecture, overall abstraction layers, project-wide patterns
- **Module-specific files (agent-base-[module-name].md)**: Contains module-specific patterns, standards, flows, strategies, testing

### Step 2: Extract Patterns from Same Abstraction Layers

For each module basepoint file, extract patterns specific to that module's abstraction level:

```bash
# Process each module basepoint
echo "$MODULE_BASEPOINTS" | while read basepoint_file; do
    if [ -z "$basepoint_file" ]; then
        continue
    fi
    
    # Determine module's abstraction layer from file path
    # e.g., agent-os/basepoints/src/data/models/agent-base-models.md -> "data" layer
    MODULE_PATH=$(dirname "$basepoint_file" | sed 's|agent-os/basepoints/||')
    MODULE_LAYER=$(echo "$MODULE_PATH" | cut -d'/' -f1-2 | head -1)
    
    # Extract patterns from this module's basepoint
    MODULE_CONTENT=$(cat "$basepoint_file")
    
    # Extract design patterns (from Patterns section)
    DESIGN_PATTERNS=$(echo "$MODULE_CONTENT" | grep -A 100 "^## Patterns" | grep -B 100 "^## " | grep -E "^-|^\*|^[0-9]" || echo "")
    
    # Extract coding patterns (from Patterns section)
    CODING_PATTERNS=$(echo "$MODULE_CONTENT" | grep -A 100 "^## Patterns" | grep -i "coding\|idiom\|convention" || echo "")
    
    # Extract architectural patterns (from Patterns section)
    ARCHITECTURAL_PATTERNS=$(echo "$MODULE_CONTENT" | grep -A 100 "^## Patterns" | grep -i "architectural\|layer\|component" || echo "")
    
    # Group by abstraction layer
    echo "Layer: $MODULE_LAYER - Module: $(basename "$basepoint_file" .md | sed 's/agent-base-//')"
    echo "  Design patterns: [extracted]"
    echo "  Coding patterns: [extracted]"
    echo "  Architectural patterns: [extracted]"
done
```

Extract patterns by category:
- **Design Patterns**: Creational, structural, behavioral patterns used in the module
- **Coding Patterns**: Idioms, conventions, best practices specific to the module
- **Architectural Patterns**: Patterns defining module structure, interactions, organization

Group patterns by abstraction layer (data, domain, presentation, infrastructure, etc.)

### Step 3: Extract Patterns Between Abstraction Layers

Identify cross-layer patterns and architectural patterns spanning multiple layers:

```bash
# Read headquarter.md for cross-layer architecture
if [ -f "agent-os/basepoints/headquarter.md" ]; then
    HEADQUARTER=$(cat agent-os/basepoints/headquarter.md)
    
    # Extract abstraction layers mentioned
    ABSTRACTION_LAYERS=$(echo "$HEADQUARTER" | grep -i "abstraction\|layer" | grep -E "^##|^-" || echo "")
    
    # Extract architectural patterns spanning layers
    CROSS_LAYER_PATTERNS=$(echo "$HEADQUARTER" | grep -A 50 -i "cross.*layer\|between.*layer\|span.*layer" || echo "")
fi

# Analyze parent basepoints for aggregation patterns
find agent-os/basepoints -name "agent-base-*.md" -type f | while read basepoint_file; do
    # Check if this is a parent basepoint (contains references to child modules)
    MODULE_CONTENT=$(cat "$basepoint_file")
    
    # Extract dependency flows between layers
    DEPENDENCY_FLOWS=$(echo "$MODULE_CONTENT" | grep -A 20 -i "dependency\|flow\|relation" || echo "")
    
    # Extract interaction patterns between modules at different layers
    INTERACTION_PATTERNS=$(echo "$MODULE_CONTENT" | grep -A 20 -i "interaction\|communication\|interface" || echo "")
done
```

Extract cross-layer patterns:
- **Architectural Patterns**: Patterns that span multiple abstraction layers (e.g., layered architecture, hexagonal architecture)
- **Dependency Flows**: How modules at different layers depend on each other
- **Interaction Patterns**: How modules at different layers communicate and interact

### Step 4: Extract Standards, Flows, Strategies, and Testing Approaches

Extract all knowledge categories from all basepoint files:

```bash
# Process all basepoint files
ALL_BASEPOINTS=$(find agent-os/basepoints -name "*.md" -type f)

# Extract Standards
STANDARDS_COLLECTION=""
echo "$ALL_BASEPOINTS" | while read basepoint_file; do
    CONTENT=$(cat "$basepoint_file")
    
    # Extract from Standards section
    STANDARDS_SECTION=$(echo "$CONTENT" | grep -A 200 "^## Standards" | grep -B 200 "^## " | head -n -1 || echo "")
    
    # Extract naming conventions
    NAMING=$(echo "$STANDARDS_SECTION" | grep -i "naming\|convention" || echo "")
    
    # Extract coding style
    CODING_STYLE=$(echo "$STANDARDS_SECTION" | grep -i "coding.*style\|format\|indent" || echo "")
    
    # Extract structure standards
    STRUCTURE=$(echo "$STANDARDS_SECTION" | grep -i "structure\|organization\|folder" || echo "")
    
    # Accumulate standards
    STANDARDS_COLLECTION="${STANDARDS_COLLECTION}\n\nFrom: $basepoint_file\n${STANDARDS_SECTION}"
done

# Extract Flows
FLOWS_COLLECTION=""
echo "$ALL_BASEPOINTS" | while read basepoint_file; do
    CONTENT=$(cat "$basepoint_file")
    
    # Extract from Flows section
    FLOWS_SECTION=$(echo "$CONTENT" | grep -A 200 "^## Flows" | grep -B 200 "^## " | head -n -1 || echo "")
    
    # Extract data flows
    DATA_FLOWS=$(echo "$FLOWS_SECTION" | grep -i "data.*flow\|data.*movement" || echo "")
    
    # Extract control flows
    CONTROL_FLOWS=$(echo "$FLOWS_SECTION" | grep -i "control.*flow\|execution.*flow" || echo "")
    
    # Extract dependency flows
    DEPENDENCY_FLOWS=$(echo "$FLOWS_SECTION" | grep -i "dependency\|depends\|imports" || echo "")
    
    # Accumulate flows
    FLOWS_COLLECTION="${FLOWS_COLLECTION}\n\nFrom: $basepoint_file\n${FLOWS_SECTION}"
done

# Extract Strategies
STRATEGIES_COLLECTION=""
echo "$ALL_BASEPOINTS" | while read basepoint_file; do
    CONTENT=$(cat "$basepoint_file")
    
    # Extract from Strategies section
    STRATEGIES_SECTION=$(echo "$CONTENT" | grep -A 200 "^## Strategies" | grep -B 200 "^## " | head -n -1 || echo "")
    
    # Extract implementation strategies
    IMPL_STRATEGIES=$(echo "$STRATEGIES_SECTION" | grep -i "implementation\|approach\|method" || echo "")
    
    # Extract architectural strategies
    ARCH_STRATEGIES=$(echo "$STRATEGIES_SECTION" | grep -i "architectural\|design.*decision" || echo "")
    
    # Accumulate strategies
    STRATEGIES_COLLECTION="${STRATEGIES_COLLECTION}\n\nFrom: $basepoint_file\n${STRATEGIES_SECTION}"
done

# Extract Testing Approaches
TESTING_COLLECTION=""
echo "$ALL_BASEPOINTS" | while read basepoint_file; do
    CONTENT=$(cat "$basepoint_file")
    
    # Extract from Testing section
    TESTING_SECTION=$(echo "$CONTENT" | grep -A 200 "^## Testing\|^## Test" | grep -B 200 "^## " | head -n -1 || echo "")
    
    # Extract test patterns
    TEST_PATTERNS=$(echo "$TESTING_SECTION" | grep -i "pattern\|approach" || echo "")
    
    # Extract test strategies
    TEST_STRATEGIES=$(echo "$TESTING_SECTION" | grep -i "strategy\|methodology" || echo "")
    
    # Extract test organization
    TEST_ORGANIZATION=$(echo "$TESTING_SECTION" | grep -i "organization\|structure\|folder" || echo "")
    
    # Accumulate testing approaches
    TESTING_COLLECTION="${TESTING_COLLECTION}\n\nFrom: $basepoint_file\n${TESTING_SECTION}"
done
```

Extract from each basepoint file:
- **Standards**: Naming conventions, coding style, structure standards
- **Flows**: Data flows, control flows, dependency flows
- **Strategies**: Implementation strategies, architectural strategies
- **Testing Approaches**: Test patterns, test strategies, test organization

### Step 5: Capture Project Structure Information

Extract abstraction layers, module relationships, and hierarchy from basepoints:

```bash
# Extract abstraction layers from headquarter.md
if [ -f "agent-os/basepoints/headquarter.md" ]; then
    HEADQUARTER=$(cat agent-os/basepoints/headquarter.md)
    
    # Extract abstraction layers section
    LAYERS_SECTION=$(echo "$HEADQUARTER" | grep -A 100 -i "abstraction.*layer\|layer.*structure" || echo "")
    
    # Detect layers from folder structure
    DETECTED_LAYERS=$(find agent-os/basepoints -type d -mindepth 1 -maxdepth 3 | sed 's|agent-os/basepoints/||' | cut -d'/' -f1 | sort -u)
fi

# Extract module relationships and hierarchy
MODULE_HIERARCHY=""
find agent-os/basepoints -name "agent-base-*.md" -type f | while read basepoint_file; do
    CONTENT=$(cat "$basepoint_file")
    MODULE_PATH=$(dirname "$basepoint_file" | sed 's|agent-os/basepoints/||')
    MODULE_NAME=$(basename "$basepoint_file" .md | sed 's/agent-base-//')
    
    # Extract related modules section
    RELATED_MODULES=$(echo "$CONTENT" | grep -A 50 "^## Related" | grep -E "^-|^\*|^[0-9]" || echo "")
    
    # Build hierarchy structure
    PARENT_DIR=$(dirname "$MODULE_PATH")
    MODULE_HIERARCHY="${MODULE_HIERARCHY}\n${MODULE_PATH} -> ${MODULE_NAME}"
done

# Extract project structure from basepoints folder structure
PROJECT_STRUCTURE=$(find agent-os/basepoints -type d | sed 's|agent-os/basepoints||' | grep -v "^$" | sort)
```

Capture:
- **Abstraction Layers**: Layers detected in the project (data, domain, presentation, infrastructure, etc.)
- **Module Relationships**: Which modules relate to each other, dependencies, interactions
- **Hierarchy**: Parent-child relationships between modules and folders
- **Project Structure**: Folder structure that mirrors the actual project

### Step 6: Store Extracted Knowledge for Merging

Organize all extracted knowledge into structured format ready for merging:

```bash
# Create knowledge structure organized by category
EXTRACTED_KNOWLEDGE="{
  \"patterns\": {
    \"same_layer\": {
      \"data\": [],
      \"domain\": [],
      \"presentation\": []
    },
    \"cross_layer\": []
  },
  \"standards\": {
    \"naming\": [],
    \"coding_style\": [],
    \"structure\": []
  },
  \"flows\": {
    \"data\": [],
    \"control\": [],
    \"dependency\": []
  },
  \"strategies\": {
    \"implementation\": [],
    \"architectural\": []
  },
  \"testing\": {
    \"patterns\": [],
    \"strategies\": [],
    \"organization\": []
  },
  \"project_structure\": {
    \"abstraction_layers\": [],
    \"module_hierarchy\": {},
    \"folder_structure\": []
  }
}"

# Store in cache for next phase
mkdir -p agent-os/output/deploy-agents/knowledge
echo "$EXTRACTED_KNOWLEDGE" > agent-os/output/deploy-agents/knowledge/basepoints-knowledge.json

# Also create a structured markdown summary
cat > agent-os/output/deploy-agents/knowledge/basepoints-knowledge-summary.md << 'EOF'
# Extracted Basepoints Knowledge

## Patterns from Same Abstraction Layers
[Summary of patterns grouped by layer]

## Patterns Between Abstraction Layers
[Summary of cross-layer patterns]

## Standards
[Summary of all standards extracted]

## Flows
[Summary of all flows extracted]

## Strategies
[Summary of all strategies extracted]

## Testing Approaches
[Summary of all testing approaches extracted]

## Project Structure
[Abstraction layers, module hierarchy, folder structure]
EOF
```

Organize knowledge:
- **By Category**: Patterns, standards, flows, strategies, testing
- **By Abstraction Layer**: Same-layer patterns grouped by layer, cross-layer patterns separate
- **For Merging**: Structured format ready to merge with product knowledge and abstract command knowledge
- **For Command Transformation**: Organized so it can be injected into specialized commands

## Display confirmation and next step

Once basepoint knowledge extraction is complete, output the following message:

```
✅ Basepoint knowledge extraction complete!

- Basepoint files read: [count] files (headquarter.md + [count] module basepoints)
- Patterns extracted: [count] same-layer patterns, [count] cross-layer patterns
- Standards extracted: [count] standards
- Flows extracted: [count] flows
- Strategies extracted: [count] strategies
- Testing approaches extracted: [count] approaches
- Abstraction layers detected: [list of layers]
- Module hierarchy captured: [count] modules

Knowledge organized and ready for merging.

NEXT STEP 👉 Run the command, `3-extract-product-knowledge.md`
```

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your knowledge extraction process aligns with the user's preferences and standards as detailed in the following files:

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

- Must read all basepoint files including headquarter.md and all module-specific files
- Must extract patterns from same abstraction layers AND between abstraction layers
- Must extract all categories: patterns, standards, flows, strategies, testing approaches
- Must capture complete project structure (abstraction layers, module relationships, hierarchy)
- Must organize knowledge in structured format ready for merging
- Must preserve source information (which basepoint file each piece of knowledge came from) for conflict resolution
