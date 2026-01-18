[38;2;255;185;0m⚠️  Unclosed conditional block detected (nesting level: 3)[0m
Now that shape-spec and write-spec commands are specialized, proceed with specializing the create-tasks, implement-tasks, and orchestrate-tasks commands by following these instructions:

## Core Responsibilities

1. **Read Abstract Command Templates**: Load create-tasks, implement-tasks, and orchestrate-tasks templates from profiles/default
2. **Inject Project-Specific Knowledge**: Inject patterns, checkpoints, strategies, and workflows from merged knowledge
3. **Replace Abstract Placeholders**: Replace {{workflows/...}}
⚠️ This workflow file was not found in profiles/default/workflows/....md,  with project-specific content
4. **Adapt Checkpoints**: Adapt task checkpoints based on project structure complexity
5. **Write Specialized Commands**: Write specialized commands to agent-os/commands/ (replace abstract versions)

## Workflow

### Step 1: Load Merged Knowledge

Load merged knowledge from previous phases:

```bash
# Load merged knowledge from cache
if [ -f "agent-os/output/deploy-agents/knowledge/merged-knowledge.json" ]; then
    MERGED_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/merged-knowledge.json)
    echo "✅ Loaded merged knowledge"
fi

# Load command-specific knowledge
if [ -f "agent-os/output/deploy-agents/knowledge/create-tasks-knowledge.json" ]; then
    CREATE_TASKS_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/create-tasks-knowledge.json)
fi

if [ -f "agent-os/output/deploy-agents/knowledge/implement-tasks-knowledge.json" ]; then
    IMPLEMENT_TASKS_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/implement-tasks-knowledge.json)
fi

if [ -f "agent-os/output/deploy-agents/knowledge/orchestrate-tasks-knowledge.json" ]; then
    ORCHESTRATE_TASKS_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/orchestrate-tasks-knowledge.json)
fi
```

### Step 2: Read Abstract create-tasks Command Template

Read abstract create-tasks command template and all its phase files:

```bash
# Read single-agent version
if [ -f "profiles/default/commands/create-tasks/single-agent/create-tasks.md" ]; then
    CREATE_TASKS_SINGLE=$(cat profiles/default/commands/create-tasks/single-agent/create-tasks.md)
fi

# Read all phase files
CREATE_TASKS_PHASES=""
for phase_file in profiles/default/commands/create-tasks/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_NAME=$(basename "$phase_file")
        PHASE_CONTENT=$(cat "$phase_file")
        CREATE_TASKS_PHASES="${CREATE_TASKS_PHASES}\n\n=== $PHASE_NAME ===\n$PHASE_CONTENT"
    fi
done

# Read referenced workflows
if [ -f "profiles/default/workflows/implementation/create-tasks-list.md" ]; then
    CREATE_TASKS_WORKFLOW=$(cat profiles/default/workflows/implementation/create-tasks-list.md)
fi

echo "✅ Loaded abstract create-tasks command template and phase files"
```

### Step 3: Specialize create-tasks Command

Inject project-specific knowledge into create-tasks command:

```bash
# Start with abstract template
SPECIALIZED_CREATE_TASKS="$CREATE_TASKS_SINGLE"

# Inject project-specific task creation patterns from basepoints
# Replace abstract task creation patterns with project-specific patterns from merged knowledge
# Example: Replace generic task grouping with project-specific grouping patterns from basepoints
SPECIALIZED_CREATE_TASKS=$(echo "$SPECIALIZED_CREATE_TASKS" | \
    sed "s|generic task pattern|$(extract_task_pattern_from_merged "$MERGED_KNOWLEDGE" "create-tasks")|g")

# Inject project-specific checkpoints based on project structure
# Adapt checkpoints based on project structure complexity (from merged knowledge)
PROJECT_STRUCTURE=$(extract_structure_from_merged "$MERGED_KNOWLEDGE")
CHECKPOINTS=$(generate_checkpoints_from_structure "$PROJECT_STRUCTURE")
SPECIALIZED_CREATE_TASKS=$(inject_checkpoints_into_command "$SPECIALIZED_CREATE_TASKS" "$CHECKPOINTS")

# Replace abstract placeholders with project-specific content
SPECIALIZED_CREATE_TASKS=$(echo "$SPECIALIZED_CREATE_TASKS" | \
    sed "s|# Task List Creation

## Core Responsibilities

1. **Analyze spec and requirements**: Read and analyze the spec.md and/or requirements.md to inform the tasks list you will create.
2. **Plan task execution order**: Break the requirements into a list of tasks in an order that takes their dependencies into account.
3. **Group tasks by specialization**: Group tasks that require the same skill or domain specialization together (data layer, interface layer, business logic, etc.)
4. **Create Tasks list**: Create the markdown tasks list broken into groups with sub-tasks.

## Workflow

### Step 1: Analyze Spec & Requirements

Read each of these files (whichever are available) and analyze them to understand the requirements for this feature implementation:
- `agent-os/specs/[this-spec]/spec.md`
- `agent-os/specs/[this-spec]/planning/requirements.md`

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

Use your learnings from spec/requirements AND basepoints knowledge (if available) to inform the tasks list and groupings you will create in the next step.

**From Basepoints Knowledge (if available):**
- Use extracted patterns to inform task breakdown
- Suggest existing patterns and checkpoints from basepoints
- Reference project-specific implementation strategies
- Include relevant testing approaches in task creation


### Step 2: Check if Deep Reading is Needed

Before creating tasks, check if deep implementation reading is needed:

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


# Load deep reading results if available
if [ -f "$SPEC_PATH/implementation/cache/deep-reading/implementation-analysis.json" ]; then
    DEEP_READING_RESULTS=$(cat "$SPEC_PATH/implementation/cache/deep-reading/implementation-analysis.json")
    
    # Detect reusable code
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

If deep reading was performed, use the results to:
- Inform task breakdown with actual implementation patterns
- Suggest existing patterns and checkpoints from actual implementation
- Include tasks for reusing existing code
- Include tasks for refactoring opportunities

### Step 2.5: Check for Trade-offs (if needed)

Before creating tasks, check if trade-offs need to be reviewed:

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

If trade-offs are detected, present them to the user and wait for their decision before proceeding.

### Step 3: Check for Checkpoints (if needed)

Before creating tasks, check if checkpoints are needed for big changes:

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

If a checkpoint is needed, present it to the user and wait for their confirmation before proceeding.

### Step 4: Create Tasks Breakdown (SDD-aligned)

Generate `agent-os/specs/[current-spec]/tasks.md`.

**SDD Task Decomposition Best Practices:**
- Respect SDD phase order: Specify → Tasks → Implement (spec should be complete before tasks)
- Ensure each task can be validated against spec acceptance criteria
- Break work into small, testable, isolated tasks
- Order tasks by dependency (respecting SDD phase order)
- Reference spec acceptance criteria when creating task validation

**INVEST Criteria for Task Quality:**
When creating tasks, ensure they are:
- **Independent**: Can be done in any order where dependencies allow
- **Valuable**: Deliver standalone value (not just technical subtasks)
- **Small**: Manageable size that can be completed efficiently
- **Estimable**: Can be estimated with reasonable accuracy
- **Testable**: Have clear acceptance criteria from spec

**Atomic Task Principles:**
- Tasks should be independently valuable (not just technical subtasks)
- Tasks should be independently testable (have clear acceptance criteria)
- Task decomposition should follow SDD principles (small, testable, isolated)

**Important**: The exact tasks, task groups, and organization will vary based on the feature's specific requirements. The following is an example format - adapt the content of the tasks list to match what THIS feature actually needs.

```markdown
# Task Breakdown: [Feature Name]

## Overview
Total Tasks: [count]

## Task List

### Core Functionality Layer

#### Task Group 1: Core Logic and Data Structures
**Dependencies:** None

- [ ] 1.0 Complete core functionality layer
  - [ ] 1.1 Write 2-8 focused tests for core functionality
    - Limit to 2-8 highly focused tests maximum
    - Test only critical behaviors (e.g., primary validation, key relationships, core methods)
    - Skip exhaustive coverage of all methods and edge cases
  - [ ] 1.2 Create core data structures or classes
    - Fields/Properties: [list]
    - Validations/Constraints: [list]
    - Reuse pattern from: [existing structure if applicable]
  - [ ] 1.3 Implement data persistence (if applicable)
    - Schema or structure: [description]
    - Relationships: [if applicable]
  - [ ] 1.4 Set up relationships or dependencies
    - [Structure] relates to [related structure]
    - [Structure] depends on [dependency]
  - [ ] 1.5 Ensure core layer tests pass
    - Run ONLY the 2-8 tests written in 1.1
    - Verify data structures work correctly
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 1.1 pass
- Core structures pass validation tests
- Relationships work correctly

### Interface Layer (if applicable)

#### Task Group 2: Interface Implementation
**Dependencies:** Task Group 1

- [ ] 2.0 Complete interface layer
  - [ ] 2.1 Write 2-8 focused tests for interface
    - Limit to 2-8 highly focused tests maximum
    - Test only critical interface behaviors (e.g., primary operations, key error cases)
    - Skip exhaustive testing of all operations and scenarios
  - [ ] 2.2 Create interface implementation
    - Operations: [list of key operations]
    - Follow pattern from: [existing interface]
  - [ ] 2.3 Implement authentication/authorization (if applicable)
    - Use existing auth pattern
    - Add permission checks
  - [ ] 2.4 Add response formatting and error handling
    - Response format: [description]
    - Error handling: [approach]
    - Status indicators: [how success/failure is communicated]
  - [ ] 2.5 Ensure interface layer tests pass
    - Run ONLY the 2-8 tests written in 2.1
    - Verify critical operations work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 2.1 pass
- All critical operations work
- Proper authorization enforced (if applicable)
- Consistent response format

### User Interface Layer (if applicable)

#### Task Group 3: User Interface Design
**Dependencies:** Task Group 2 (if interface layer exists) or Task Group 1

- [ ] 3.0 Complete user interface
  - [ ] 3.1 Write 2-8 focused tests for UI components
    - Limit to 2-8 highly focused tests maximum
    - Test only critical component behaviors (e.g., primary user interaction, key form submission, main rendering case)
    - Skip exhaustive testing of all component states and interactions
  - [ ] 3.2 Create UI components or views
    - Reuse: [existing component] as base
    - Properties/Props: [list]
    - State: [list]
  - [ ] 3.3 Implement user input handling
    - Fields/Inputs: [list]
    - Validation: [approach]
    - Submit/Process handling
  - [ ] 3.4 Build main view or screen
    - Layout: [description]
    - Components: [list]
    - Match mockup: `planning/visuals/[file]` (if applicable)
  - [ ] 3.5 Apply styling and design
    - Follow existing design system
    - Use design tokens from: [style system]
  - [ ] 3.6 Implement responsive or adaptive design (if applicable)
    - Breakpoints or contexts: [description]
  - [ ] 3.7 Add interactions and feedback
    - User feedback mechanisms
    - Loading states
    - Error states
  - [ ] 3.8 Ensure UI component tests pass
    - Run ONLY the 2-8 tests written in 3.1
    - Verify critical component behaviors work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 3.1 pass
- Components render correctly
- Forms/inputs validate and process correctly
- Matches visual design (if provided)

### Testing

#### Task Group 4: Test Review & Gap Analysis
**Dependencies:** Task Groups 1-3

- [ ] 4.0 Review existing tests and fill critical gaps only
  - [ ] 4.1 Review tests from Task Groups 1-3
    - Review the 2-8 tests written in Task Group 1
    - Review the 2-8 tests written in Task Group 2 (if applicable)
    - Review the 2-8 tests written in Task Group 3 (if applicable)
    - Total existing tests: approximately 6-24 tests
  - [ ] 4.2 Analyze test coverage gaps for THIS feature only
    - Identify critical user workflows that lack test coverage
    - Focus ONLY on gaps related to this spec's feature requirements
    - Do NOT assess entire application test coverage
    - Prioritize end-to-end workflows over unit test gaps
  - [ ] 4.3 Write up to 10 additional strategic tests maximum
    - Add maximum of 10 new tests to fill identified critical gaps
    - Focus on integration points and end-to-end workflows
    - Do NOT write comprehensive coverage for all scenarios
    - Skip edge cases, performance tests, and accessibility tests unless business-critical
  - [ ] 4.4 Run feature-specific tests only
    - Run ONLY tests related to this spec's feature (tests from 1.1, 2.1, 3.1, and 4.3)
    - Expected total: approximately 16-34 tests maximum
    - Do NOT run the entire application test suite
    - Verify critical workflows pass

**Acceptance Criteria:**
- All feature-specific tests pass (approximately 16-34 tests total)
- Critical user workflows for this feature are covered
- No more than 10 additional tests added when filling in testing gaps
- Testing focused exclusively on this spec's feature requirements

## Execution Order

Recommended implementation sequence:
1. Core Functionality Layer (Task Group 1)
2. Interface Layer (Task Group 2) - if applicable
3. User Interface Layer (Task Group 3) - if applicable
4. Test Review & Gap Analysis (Task Group 4)
```

**Note**: When creating tasks, leverage basepoints knowledge (if available) to:
- Suggest existing patterns and checkpoints from basepoints
- Reference project-specific implementation strategies
- Include relevant testing approaches from basepoints
- Avoid unnecessary work by leveraging existing patterns

**Note**: Adapt this structure based on the actual feature requirements. Some features may need:
- Different task groups (e.g., notification systems, data processing, integration work)
- Different execution order based on dependencies
- More or fewer sub-tasks per group
- Task groups for specific domains (e.g., algorithms, data processing, system integration)

### Step 5: Validate Tasks Against SDD Principles (SDD-aligned)

After creating tasks, validate them against SDD best practices and INVEST criteria:

```bash
# SDD Task Validation
TASKS_FILE="$SPEC_PATH/tasks.md"
SPEC_FILE="$SPEC_PATH/spec.md"

if [ -f "$TASKS_FILE" ] && [ -f "$SPEC_FILE" ]; then
    echo "🔍 Validating tasks against SDD principles..."
    
    # INVEST Criteria Validation
    INVEST_ISSUES=""
    
    # Check for Independent tasks (can be done in any order where dependencies allow)
    # Validation: tasks should not have circular dependencies
    # This is checked by ensuring task dependencies follow a DAG structure
    
    # Check for Valuable tasks (deliver standalone value)
    # Look for tasks that are too granular (technical subtasks without standalone value)
    GRANULAR_TASKS=$(grep -iE "TODO|FIXME|refactor|cleanup|optimize" "$TASKS_FILE" | grep -v "Write.*tests" | wc -l)
    if [ "$GRANULAR_TASKS" -gt 5 ]; then
        INVEST_ISSUES="${INVEST_ISSUES}⚠️ Too many technical subtasks detected (may violate 'Valuable' principle). Consider grouping technical subtasks into independently valuable tasks. "
    fi
    
    # Check for Small tasks (manageable size)
    # Validation: tasks should have clear scope that can be completed efficiently
    # Check if tasks are too large (exceeding reasonable completion time)
    LARGE_TASKS=$(grep -iE "complete.*feature|implement.*system|build.*application" "$TASKS_FILE" | wc -l)
    if [ "$LARGE_TASKS" -gt 3 ]; then
        INVEST_ISSUES="${INVEST_ISSUES}⚠️ Large tasks detected (may violate 'Small' principle). Consider breaking down into smaller, manageable tasks. "
    fi
    
    # Check for Estimable tasks (can be estimated with reasonable accuracy)
    # Validation: tasks should have clear scope and acceptance criteria
    TASKS_WITHOUT_AC=$(grep -c "Acceptance Criteria:" "$TASKS_FILE" 2>/dev/null || echo "0")
    TOTAL_TASK_GROUPS=$(grep -c "^#### Task Group" "$TASKS_FILE" 2>/dev/null || echo "0")
    if [ "$TOTAL_TASK_GROUPS" -gt 0 ] && [ "$TASKS_WITHOUT_AC" -lt "$TOTAL_TASK_GROUPS" ]; then
        INVEST_ISSUES="${INVEST_ISSUES}⚠️ Some task groups lack acceptance criteria (may violate 'Estimable' and 'Testable' principles). Ensure all task groups have clear acceptance criteria from spec. "
    fi
    
    # Check for Testable tasks (have clear acceptance criteria from spec)
    # Validate that tasks reference spec acceptance criteria
    TASKS_REFERENCING_SPEC=$(grep -iE "spec|acceptance criteria|requirement" "$TASKS_FILE" | wc -l)
    if [ "$TASKS_REFERENCING_SPEC" -eq 0 ]; then
        INVEST_ISSUES="${INVEST_ISSUES}⚠️ Tasks may not be validating against spec acceptance criteria (may violate 'Testable' principle). Ensure tasks can be validated against spec. "
    fi
    
    # Atomic Task Principles Validation
    ATOMIC_ISSUES=""
    
    # Check for independently valuable tasks
    # Validation: tasks should deliver standalone value, not just be technical subtasks
    SUBTASK_INDICATORS=$(grep -iE "setup|configure|initialize|prepare|helper|utility" "$TASKS_FILE" | grep -v "Write.*tests" | wc -l)
    if [ "$SUBTASK_INDICATORS" -gt 3 ] && [ "$TOTAL_TASK_GROUPS" -lt 5 ]; then
        ATOMIC_ISSUES="${ATOMIC_ISSUES}⚠️ Many technical subtasks detected. Consider ensuring tasks are independently valuable, not just setup/preparation steps. "
    fi
    
    # Check for independently testable tasks
    # Validation: tasks should have clear acceptance criteria
    # Already checked above in INVEST validation
    
    # SDD Phase Order Validation
    SDD_PHASE_ISSUES=""
    
    # Check that tasks respect SDD phase order (Specify → Tasks → Implement)
    # Validation: spec should be complete before tasks are created
    # This is implicit - if we're creating tasks, spec should already exist
    
    # Check that tasks can be validated against spec acceptance criteria
    if [ -f "$SPEC_FILE" ]; then
        SPEC_AC=$(grep -iE "acceptance criteria|Acceptance Criteria" "$SPEC_FILE" | wc -l)
        if [ "$SPEC_AC" -eq 0 ]; then
            SDD_PHASE_ISSUES="${SDD_PHASE_ISSUES}⚠️ Spec may lack acceptance criteria. Tasks should be validated against spec acceptance criteria (SDD best practice). "
        fi
    fi
    
    # Report validation results
    if [ -n "$INVEST_ISSUES$ATOMIC_ISSUES$SDD_PHASE_ISSUES" ]; then
        echo "📋 SDD Task Validation: Issues detected"
        echo ""
        if [ -n "$INVEST_ISSUES" ]; then
            echo "INVEST Criteria Issues:"
            echo "$INVEST_ISSUES"
            echo ""
        fi
        if [ -n "$ATOMIC_ISSUES" ]; then
            echo "Atomic Task Principles Issues:"
            echo "$ATOMIC_ISSUES"
            echo ""
        fi
        if [ -n "$SDD_PHASE_ISSUES" ]; then
            echo "SDD Phase Order Issues:"
            echo "$SDD_PHASE_ISSUES"
            echo ""
        fi
        echo "Consider reviewing tasks to align with SDD best practices."
        echo "Proceed anyway? [Yes/No]"
        # In actual execution, wait for user decision
    else
        echo "✅ SDD Task Validation: All checks passed"
        echo "Tasks align with INVEST criteria, atomic task principles, and SDD best practices."
    fi
fi
```

## Important Constraints

- **Create tasks that are specific and verifiable**
- **Group related tasks:** For example, group core logic tasks together, interface tasks together, and UI tasks together (if applicable).
- **Limit test writing during development**:
  - Each task group (1-3) should write 2-8 focused tests maximum
  - Tests should cover only critical behaviors, not exhaustive coverage
  - Test verification should run ONLY the newly written tests, not the entire suite
  - If there is a dedicated test coverage group for filling in gaps in test coverage, this group should add only a maximum of 10 additional tests IF NECESSARY to fill critical gaps
- **Use a focused test-driven approach** where each task group starts with writing 2-8 tests (x.1 sub-task) and ends with running ONLY those tests (final sub-task)
- **Include acceptance criteria** for each task group
- **Reference visual assets** if visuals are available

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Principles Integrated:**
- **SDD Phase Order**: Tasks respect SDD phase order (Specify → Tasks → Implement)
- **Spec as Source of Truth**: Tasks can be validated against spec acceptance criteria
- **Task Decomposition Best Practices**: Tasks are broken into small, testable, isolated units

**INVEST Criteria Integration:**
- **Independent**: Tasks can be done in any order where dependencies allow
- **Valuable**: Tasks deliver standalone value (not just technical subtasks)
- **Small**: Tasks are manageable size that can be completed efficiently
- **Estimable**: Tasks can be estimated with reasonable accuracy
- **Testable**: Tasks have clear acceptance criteria from spec

**Atomic Task Principles:**
- Tasks are independently valuable (not just technical subtasks)
- Tasks are independently testable (have clear acceptance criteria)
- Task decomposition follows SDD principles (small, testable, isolated)

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD framework references are abstract (e.g., "task decomposition frameworks" not technology-specific tools)
- No hardcoded technology-specific task management tool references in default templates
- Task validation maintains technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

**Workflow Steps Enhanced:**
- Step 4: Enhanced task creation guidance with SDD best practices and INVEST criteria
- Step 5: Added SDD task validation against INVEST criteria, atomic task principles, and SDD phase order
|$(generate_project_workflow_ref "$MERGED_KNOWLEDGE" "create-tasks-list")|g")

SPECIALIZED_CREATE_TASKS=$(echo "$SPECIALIZED_CREATE_TASKS" | \
    sed "s|@agent-os/standards/documentation/standards.md
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
@agent-os/standards/testing/test-writing.md|$(generate_project_standards_content "$MERGED_KNOWLEDGE")|g")

# Inject project-specific task creation patterns into phases
for phase_file in profiles/default/commands/create-tasks/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_CONTENT=$(cat "$phase_file")
        
        # Inject project-specific task patterns
        PHASE_CONTENT=$(inject_task_patterns_into_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Inject project-specific checkpoints
        PHASE_CONTENT=$(inject_checkpoints_into_phase "$PHASE_CONTENT" "$CHECKPOINTS")
        
        # Replace abstract placeholders
        PHASE_CONTENT=$(replace_placeholders_in_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Specialize basepoints knowledge extraction workflows
        PHASE_CONTENT=$(specialize_basepoints_extraction_workflows "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Specialize scope detection workflows
        PHASE_CONTENT=$(specialize_scope_detection_workflows "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Store specialized phase content
        SPECIALIZED_CREATE_TASKS_PHASES="${SPECIALIZED_CREATE_TASKS_PHASES}\n\n=== $(basename "$phase_file") ===\n$PHASE_CONTENT"
    fi
done

# Specialize the create-tasks-list workflow itself
SPECIALIZED_CREATE_TASKS_WORKFLOW="$CREATE_TASKS_WORKFLOW"

# Inject project-specific task creation patterns into workflow
SPECIALIZED_CREATE_TASKS_WORKFLOW=$(inject_task_patterns_into_workflow "$SPECIALIZED_CREATE_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

# Inject project-specific checkpoints into workflow
SPECIALIZED_CREATE_TASKS_WORKFLOW=$(inject_checkpoints_into_workflow "$SPECIALIZED_CREATE_TASKS_WORKFLOW" "$CHECKPOINTS")

# Replace abstract placeholders in workflow
SPECIALIZED_CREATE_TASKS_WORKFLOW=$(replace_placeholders_in_workflow "$SPECIALIZED_CREATE_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize basepoints knowledge extraction workflows in create-tasks workflow
SPECIALIZED_CREATE_TASKS_WORKFLOW=$(specialize_basepoints_extraction_workflows "$SPECIALIZED_CREATE_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize scope detection workflows in create-tasks workflow
SPECIALIZED_CREATE_TASKS_WORKFLOW=$(specialize_scope_detection_workflows "$SPECIALIZED_CREATE_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize deep reading workflows in create-tasks workflow
SPECIALIZED_CREATE_TASKS_WORKFLOW=$(specialize_deep_reading_workflows "$SPECIALIZED_CREATE_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

Specialize create-tasks command:
- **Inject Project-Specific Task Creation Patterns**: Replace abstract task creation patterns with project-specific patterns from basepoints
  - Extract task creation patterns relevant to task breakdown from merged knowledge
  - Inject project-specific task grouping patterns based on basepoints
  - Use project-specific task organization structures from basepoints

- **Inject Project-Specific Checkpoints**: Adapt checkpoints based on project structure complexity
  - Extract project structure information from merged knowledge (abstraction layers, module hierarchy, complexity)
  - Generate checkpoints based on project structure (more checkpoints for complex projects, fewer for simple ones)
  - Inject project-specific checkpoints into create-tasks phases and workflows

- **Replace Abstract Placeholders**: Replace workflow and standards placeholders with project-specific content
  - Replace `# Task List Creation

## Core Responsibilities

1. **Analyze spec and requirements**: Read and analyze the spec.md and/or requirements.md to inform the tasks list you will create.
2. **Plan task execution order**: Break the requirements into a list of tasks in an order that takes their dependencies into account.
3. **Group tasks by specialization**: Group tasks that require the same skill or domain specialization together (data layer, interface layer, business logic, etc.)
4. **Create Tasks list**: Create the markdown tasks list broken into groups with sub-tasks.

## Workflow

### Step 1: Analyze Spec & Requirements

Read each of these files (whichever are available) and analyze them to understand the requirements for this feature implementation:
- `agent-os/specs/[this-spec]/spec.md`
- `agent-os/specs/[this-spec]/planning/requirements.md`

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

Use your learnings from spec/requirements AND basepoints knowledge (if available) to inform the tasks list and groupings you will create in the next step.

**From Basepoints Knowledge (if available):**
- Use extracted patterns to inform task breakdown
- Suggest existing patterns and checkpoints from basepoints
- Reference project-specific implementation strategies
- Include relevant testing approaches in task creation


### Step 2: Check if Deep Reading is Needed

Before creating tasks, check if deep implementation reading is needed:

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


# Load deep reading results if available
if [ -f "$SPEC_PATH/implementation/cache/deep-reading/implementation-analysis.json" ]; then
    DEEP_READING_RESULTS=$(cat "$SPEC_PATH/implementation/cache/deep-reading/implementation-analysis.json")
    
    # Detect reusable code
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

If deep reading was performed, use the results to:
- Inform task breakdown with actual implementation patterns
- Suggest existing patterns and checkpoints from actual implementation
- Include tasks for reusing existing code
- Include tasks for refactoring opportunities

### Step 2.5: Check for Trade-offs (if needed)

Before creating tasks, check if trade-offs need to be reviewed:

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

If trade-offs are detected, present them to the user and wait for their decision before proceeding.

### Step 3: Check for Checkpoints (if needed)

Before creating tasks, check if checkpoints are needed for big changes:

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

If a checkpoint is needed, present it to the user and wait for their confirmation before proceeding.

### Step 4: Create Tasks Breakdown (SDD-aligned)

Generate `agent-os/specs/[current-spec]/tasks.md`.

**SDD Task Decomposition Best Practices:**
- Respect SDD phase order: Specify → Tasks → Implement (spec should be complete before tasks)
- Ensure each task can be validated against spec acceptance criteria
- Break work into small, testable, isolated tasks
- Order tasks by dependency (respecting SDD phase order)
- Reference spec acceptance criteria when creating task validation

**INVEST Criteria for Task Quality:**
When creating tasks, ensure they are:
- **Independent**: Can be done in any order where dependencies allow
- **Valuable**: Deliver standalone value (not just technical subtasks)
- **Small**: Manageable size that can be completed efficiently
- **Estimable**: Can be estimated with reasonable accuracy
- **Testable**: Have clear acceptance criteria from spec

**Atomic Task Principles:**
- Tasks should be independently valuable (not just technical subtasks)
- Tasks should be independently testable (have clear acceptance criteria)
- Task decomposition should follow SDD principles (small, testable, isolated)

**Important**: The exact tasks, task groups, and organization will vary based on the feature's specific requirements. The following is an example format - adapt the content of the tasks list to match what THIS feature actually needs.

```markdown
# Task Breakdown: [Feature Name]

## Overview
Total Tasks: [count]

## Task List

### Core Functionality Layer

#### Task Group 1: Core Logic and Data Structures
**Dependencies:** None

- [ ] 1.0 Complete core functionality layer
  - [ ] 1.1 Write 2-8 focused tests for core functionality
    - Limit to 2-8 highly focused tests maximum
    - Test only critical behaviors (e.g., primary validation, key relationships, core methods)
    - Skip exhaustive coverage of all methods and edge cases
  - [ ] 1.2 Create core data structures or classes
    - Fields/Properties: [list]
    - Validations/Constraints: [list]
    - Reuse pattern from: [existing structure if applicable]
  - [ ] 1.3 Implement data persistence (if applicable)
    - Schema or structure: [description]
    - Relationships: [if applicable]
  - [ ] 1.4 Set up relationships or dependencies
    - [Structure] relates to [related structure]
    - [Structure] depends on [dependency]
  - [ ] 1.5 Ensure core layer tests pass
    - Run ONLY the 2-8 tests written in 1.1
    - Verify data structures work correctly
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 1.1 pass
- Core structures pass validation tests
- Relationships work correctly

### Interface Layer (if applicable)

#### Task Group 2: Interface Implementation
**Dependencies:** Task Group 1

- [ ] 2.0 Complete interface layer
  - [ ] 2.1 Write 2-8 focused tests for interface
    - Limit to 2-8 highly focused tests maximum
    - Test only critical interface behaviors (e.g., primary operations, key error cases)
    - Skip exhaustive testing of all operations and scenarios
  - [ ] 2.2 Create interface implementation
    - Operations: [list of key operations]
    - Follow pattern from: [existing interface]
  - [ ] 2.3 Implement authentication/authorization (if applicable)
    - Use existing auth pattern
    - Add permission checks
  - [ ] 2.4 Add response formatting and error handling
    - Response format: [description]
    - Error handling: [approach]
    - Status indicators: [how success/failure is communicated]
  - [ ] 2.5 Ensure interface layer tests pass
    - Run ONLY the 2-8 tests written in 2.1
    - Verify critical operations work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 2.1 pass
- All critical operations work
- Proper authorization enforced (if applicable)
- Consistent response format

### User Interface Layer (if applicable)

#### Task Group 3: User Interface Design
**Dependencies:** Task Group 2 (if interface layer exists) or Task Group 1

- [ ] 3.0 Complete user interface
  - [ ] 3.1 Write 2-8 focused tests for UI components
    - Limit to 2-8 highly focused tests maximum
    - Test only critical component behaviors (e.g., primary user interaction, key form submission, main rendering case)
    - Skip exhaustive testing of all component states and interactions
  - [ ] 3.2 Create UI components or views
    - Reuse: [existing component] as base
    - Properties/Props: [list]
    - State: [list]
  - [ ] 3.3 Implement user input handling
    - Fields/Inputs: [list]
    - Validation: [approach]
    - Submit/Process handling
  - [ ] 3.4 Build main view or screen
    - Layout: [description]
    - Components: [list]
    - Match mockup: `planning/visuals/[file]` (if applicable)
  - [ ] 3.5 Apply styling and design
    - Follow existing design system
    - Use design tokens from: [style system]
  - [ ] 3.6 Implement responsive or adaptive design (if applicable)
    - Breakpoints or contexts: [description]
  - [ ] 3.7 Add interactions and feedback
    - User feedback mechanisms
    - Loading states
    - Error states
  - [ ] 3.8 Ensure UI component tests pass
    - Run ONLY the 2-8 tests written in 3.1
    - Verify critical component behaviors work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 3.1 pass
- Components render correctly
- Forms/inputs validate and process correctly
- Matches visual design (if provided)

### Testing

#### Task Group 4: Test Review & Gap Analysis
**Dependencies:** Task Groups 1-3

- [ ] 4.0 Review existing tests and fill critical gaps only
  - [ ] 4.1 Review tests from Task Groups 1-3
    - Review the 2-8 tests written in Task Group 1
    - Review the 2-8 tests written in Task Group 2 (if applicable)
    - Review the 2-8 tests written in Task Group 3 (if applicable)
    - Total existing tests: approximately 6-24 tests
  - [ ] 4.2 Analyze test coverage gaps for THIS feature only
    - Identify critical user workflows that lack test coverage
    - Focus ONLY on gaps related to this spec's feature requirements
    - Do NOT assess entire application test coverage
    - Prioritize end-to-end workflows over unit test gaps
  - [ ] 4.3 Write up to 10 additional strategic tests maximum
    - Add maximum of 10 new tests to fill identified critical gaps
    - Focus on integration points and end-to-end workflows
    - Do NOT write comprehensive coverage for all scenarios
    - Skip edge cases, performance tests, and accessibility tests unless business-critical
  - [ ] 4.4 Run feature-specific tests only
    - Run ONLY tests related to this spec's feature (tests from 1.1, 2.1, 3.1, and 4.3)
    - Expected total: approximately 16-34 tests maximum
    - Do NOT run the entire application test suite
    - Verify critical workflows pass

**Acceptance Criteria:**
- All feature-specific tests pass (approximately 16-34 tests total)
- Critical user workflows for this feature are covered
- No more than 10 additional tests added when filling in testing gaps
- Testing focused exclusively on this spec's feature requirements

## Execution Order

Recommended implementation sequence:
1. Core Functionality Layer (Task Group 1)
2. Interface Layer (Task Group 2) - if applicable
3. User Interface Layer (Task Group 3) - if applicable
4. Test Review & Gap Analysis (Task Group 4)
```

**Note**: When creating tasks, leverage basepoints knowledge (if available) to:
- Suggest existing patterns and checkpoints from basepoints
- Reference project-specific implementation strategies
- Include relevant testing approaches from basepoints
- Avoid unnecessary work by leveraging existing patterns

**Note**: Adapt this structure based on the actual feature requirements. Some features may need:
- Different task groups (e.g., notification systems, data processing, integration work)
- Different execution order based on dependencies
- More or fewer sub-tasks per group
- Task groups for specific domains (e.g., algorithms, data processing, system integration)

### Step 5: Validate Tasks Against SDD Principles (SDD-aligned)

After creating tasks, validate them against SDD best practices and INVEST criteria:

```bash
# SDD Task Validation
TASKS_FILE="$SPEC_PATH/tasks.md"
SPEC_FILE="$SPEC_PATH/spec.md"

if [ -f "$TASKS_FILE" ] && [ -f "$SPEC_FILE" ]; then
    echo "🔍 Validating tasks against SDD principles..."
    
    # INVEST Criteria Validation
    INVEST_ISSUES=""
    
    # Check for Independent tasks (can be done in any order where dependencies allow)
    # Validation: tasks should not have circular dependencies
    # This is checked by ensuring task dependencies follow a DAG structure
    
    # Check for Valuable tasks (deliver standalone value)
    # Look for tasks that are too granular (technical subtasks without standalone value)
    GRANULAR_TASKS=$(grep -iE "TODO|FIXME|refactor|cleanup|optimize" "$TASKS_FILE" | grep -v "Write.*tests" | wc -l)
    if [ "$GRANULAR_TASKS" -gt 5 ]; then
        INVEST_ISSUES="${INVEST_ISSUES}⚠️ Too many technical subtasks detected (may violate 'Valuable' principle). Consider grouping technical subtasks into independently valuable tasks. "
    fi
    
    # Check for Small tasks (manageable size)
    # Validation: tasks should have clear scope that can be completed efficiently
    # Check if tasks are too large (exceeding reasonable completion time)
    LARGE_TASKS=$(grep -iE "complete.*feature|implement.*system|build.*application" "$TASKS_FILE" | wc -l)
    if [ "$LARGE_TASKS" -gt 3 ]; then
        INVEST_ISSUES="${INVEST_ISSUES}⚠️ Large tasks detected (may violate 'Small' principle). Consider breaking down into smaller, manageable tasks. "
    fi
    
    # Check for Estimable tasks (can be estimated with reasonable accuracy)
    # Validation: tasks should have clear scope and acceptance criteria
    TASKS_WITHOUT_AC=$(grep -c "Acceptance Criteria:" "$TASKS_FILE" 2>/dev/null || echo "0")
    TOTAL_TASK_GROUPS=$(grep -c "^#### Task Group" "$TASKS_FILE" 2>/dev/null || echo "0")
    if [ "$TOTAL_TASK_GROUPS" -gt 0 ] && [ "$TASKS_WITHOUT_AC" -lt "$TOTAL_TASK_GROUPS" ]; then
        INVEST_ISSUES="${INVEST_ISSUES}⚠️ Some task groups lack acceptance criteria (may violate 'Estimable' and 'Testable' principles). Ensure all task groups have clear acceptance criteria from spec. "
    fi
    
    # Check for Testable tasks (have clear acceptance criteria from spec)
    # Validate that tasks reference spec acceptance criteria
    TASKS_REFERENCING_SPEC=$(grep -iE "spec|acceptance criteria|requirement" "$TASKS_FILE" | wc -l)
    if [ "$TASKS_REFERENCING_SPEC" -eq 0 ]; then
        INVEST_ISSUES="${INVEST_ISSUES}⚠️ Tasks may not be validating against spec acceptance criteria (may violate 'Testable' principle). Ensure tasks can be validated against spec. "
    fi
    
    # Atomic Task Principles Validation
    ATOMIC_ISSUES=""
    
    # Check for independently valuable tasks
    # Validation: tasks should deliver standalone value, not just be technical subtasks
    SUBTASK_INDICATORS=$(grep -iE "setup|configure|initialize|prepare|helper|utility" "$TASKS_FILE" | grep -v "Write.*tests" | wc -l)
    if [ "$SUBTASK_INDICATORS" -gt 3 ] && [ "$TOTAL_TASK_GROUPS" -lt 5 ]; then
        ATOMIC_ISSUES="${ATOMIC_ISSUES}⚠️ Many technical subtasks detected. Consider ensuring tasks are independently valuable, not just setup/preparation steps. "
    fi
    
    # Check for independently testable tasks
    # Validation: tasks should have clear acceptance criteria
    # Already checked above in INVEST validation
    
    # SDD Phase Order Validation
    SDD_PHASE_ISSUES=""
    
    # Check that tasks respect SDD phase order (Specify → Tasks → Implement)
    # Validation: spec should be complete before tasks are created
    # This is implicit - if we're creating tasks, spec should already exist
    
    # Check that tasks can be validated against spec acceptance criteria
    if [ -f "$SPEC_FILE" ]; then
        SPEC_AC=$(grep -iE "acceptance criteria|Acceptance Criteria" "$SPEC_FILE" | wc -l)
        if [ "$SPEC_AC" -eq 0 ]; then
            SDD_PHASE_ISSUES="${SDD_PHASE_ISSUES}⚠️ Spec may lack acceptance criteria. Tasks should be validated against spec acceptance criteria (SDD best practice). "
        fi
    fi
    
    # Report validation results
    if [ -n "$INVEST_ISSUES$ATOMIC_ISSUES$SDD_PHASE_ISSUES" ]; then
        echo "📋 SDD Task Validation: Issues detected"
        echo ""
        if [ -n "$INVEST_ISSUES" ]; then
            echo "INVEST Criteria Issues:"
            echo "$INVEST_ISSUES"
            echo ""
        fi
        if [ -n "$ATOMIC_ISSUES" ]; then
            echo "Atomic Task Principles Issues:"
            echo "$ATOMIC_ISSUES"
            echo ""
        fi
        if [ -n "$SDD_PHASE_ISSUES" ]; then
            echo "SDD Phase Order Issues:"
            echo "$SDD_PHASE_ISSUES"
            echo ""
        fi
        echo "Consider reviewing tasks to align with SDD best practices."
        echo "Proceed anyway? [Yes/No]"
        # In actual execution, wait for user decision
    else
        echo "✅ SDD Task Validation: All checks passed"
        echo "Tasks align with INVEST criteria, atomic task principles, and SDD best practices."
    fi
fi
```

## Important Constraints

- **Create tasks that are specific and verifiable**
- **Group related tasks:** For example, group core logic tasks together, interface tasks together, and UI tasks together (if applicable).
- **Limit test writing during development**:
  - Each task group (1-3) should write 2-8 focused tests maximum
  - Tests should cover only critical behaviors, not exhaustive coverage
  - Test verification should run ONLY the newly written tests, not the entire suite
  - If there is a dedicated test coverage group for filling in gaps in test coverage, this group should add only a maximum of 10 additional tests IF NECESSARY to fill critical gaps
- **Use a focused test-driven approach** where each task group starts with writing 2-8 tests (x.1 sub-task) and ends with running ONLY those tests (final sub-task)
- **Include acceptance criteria** for each task group
- **Reference visual assets** if visuals are available

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Principles Integrated:**
- **SDD Phase Order**: Tasks respect SDD phase order (Specify → Tasks → Implement)
- **Spec as Source of Truth**: Tasks can be validated against spec acceptance criteria
- **Task Decomposition Best Practices**: Tasks are broken into small, testable, isolated units

**INVEST Criteria Integration:**
- **Independent**: Tasks can be done in any order where dependencies allow
- **Valuable**: Tasks deliver standalone value (not just technical subtasks)
- **Small**: Tasks are manageable size that can be completed efficiently
- **Estimable**: Tasks can be estimated with reasonable accuracy
- **Testable**: Tasks have clear acceptance criteria from spec

**Atomic Task Principles:**
- Tasks are independently valuable (not just technical subtasks)
- Tasks are independently testable (have clear acceptance criteria)
- Task decomposition follows SDD principles (small, testable, isolated)

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD framework references are abstract (e.g., "task decomposition frameworks" not technology-specific tools)
- No hardcoded technology-specific task management tool references in default templates
- Task validation maintains technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

**Workflow Steps Enhanced:**
- Step 4: Enhanced task creation guidance with SDD best practices and INVEST criteria
- Step 5: Added SDD task validation against INVEST criteria, atomic task principles, and SDD phase order
` with project-specific workflow content
  - Replace `@agent-os/standards/documentation/standards.md
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
@agent-os/standards/testing/test-writing.md` with actual project-specific standards content
  - Include project-specific examples and patterns in task creation

### Step 4: Read Abstract implement-tasks Command Template

Read abstract implement-tasks command template and all its phase files:

```bash
# Read single-agent version
if [ -f "profiles/default/commands/implement-tasks/single-agent/implement-tasks.md" ]; then
    IMPLEMENT_TASKS_SINGLE=$(cat profiles/default/commands/implement-tasks/single-agent/implement-tasks.md)
fi

# Read all phase files
IMPLEMENT_TASKS_PHASES=""
for phase_file in profiles/default/commands/implement-tasks/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_NAME=$(basename "$phase_file")
        PHASE_CONTENT=$(cat "$phase_file")
        IMPLEMENT_TASKS_PHASES="${IMPLEMENT_TASKS_PHASES}\n\n=== $PHASE_NAME ===\n$PHASE_CONTENT"
    fi
done

# Read referenced workflows
if [ -f "profiles/default/workflows/implementation/implement-tasks.md" ]; then
    IMPLEMENT_TASKS_WORKFLOW=$(cat profiles/default/workflows/implementation/implement-tasks.md)
fi

echo "✅ Loaded abstract implement-tasks command template and phase files"
```

### Step 5: Specialize implement-tasks Command

Inject project-specific knowledge into implement-tasks command:

```bash
# Start with abstract template
SPECIALIZED_IMPLEMENT_TASKS="$IMPLEMENT_TASKS_SINGLE"

# Inject project-specific implementation patterns from basepoints
# Replace abstract implementation patterns with project-specific patterns from merged knowledge
# Example: Replace generic "create component" with project-specific component creation pattern from basepoints
SPECIALIZED_IMPLEMENT_TASKS=$(echo "$SPECIALIZED_IMPLEMENT_TASKS" | \
    sed "s|generic implementation|$(extract_implementation_pattern_from_merged "$MERGED_KNOWLEDGE" "implement-tasks")|g")

# Inject project-specific strategies from basepoints
# Replace abstract strategies with project-specific strategies from merged knowledge
# Example: Replace generic "test-driven development" with project-specific test strategy from basepoints
SPECIALIZED_IMPLEMENT_TASKS=$(echo "$SPECIALIZED_IMPLEMENT_TASKS" | \
    sed "s|generic strategy|$(extract_strategy_from_merged "$MERGED_KNOWLEDGE" "implement-tasks")|g")

# Replace abstract placeholders with project-specific content
SPECIALIZED_IMPLEMENT_TASKS=$(echo "$SPECIALIZED_IMPLEMENT_TASKS" | \
    sed "s|Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

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
|$(generate_project_workflow_ref "$MERGED_KNOWLEDGE" "implement-tasks")|g")

SPECIALIZED_IMPLEMENT_TASKS=$(echo "$SPECIALIZED_IMPLEMENT_TASKS" | \
    sed "s|@agent-os/standards/documentation/standards.md
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
@agent-os/standards/testing/test-writing.md|$(generate_project_standards_content "$MERGED_KNOWLEDGE")|g")

# Inject project-specific implementation patterns into phases
for phase_file in profiles/default/commands/implement-tasks/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_CONTENT=$(cat "$phase_file")
        
        # Inject project-specific implementation patterns
        PHASE_CONTENT=$(inject_implementation_patterns_into_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Inject project-specific strategies
        PHASE_CONTENT=$(inject_strategies_into_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Replace abstract placeholders
        PHASE_CONTENT=$(replace_placeholders_in_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Specialize basepoints knowledge extraction workflows
        PHASE_CONTENT=$(specialize_basepoints_extraction_workflows "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Specialize scope detection workflows
        PHASE_CONTENT=$(specialize_scope_detection_workflows "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Specialize deep reading workflows
        PHASE_CONTENT=$(specialize_deep_reading_workflows "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Store specialized phase content
        SPECIALIZED_IMPLEMENT_TASKS_PHASES="${SPECIALIZED_IMPLEMENT_TASKS_PHASES}\n\n=== $(basename "$phase_file") ===\n$PHASE_CONTENT"
    fi
done

# Specialize the implement-tasks workflow itself
SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW="$IMPLEMENT_TASKS_WORKFLOW"

# Inject project-specific implementation patterns into workflow
SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW=$(inject_implementation_patterns_into_workflow "$SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

# Inject project-specific strategies into workflow
SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW=$(inject_strategies_into_workflow "$SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

# Replace abstract placeholders in workflow
SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW=$(replace_placeholders_in_workflow "$SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize basepoints knowledge extraction workflows in implement-tasks workflow
SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW=$(specialize_basepoints_extraction_workflows "$SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize scope detection workflows in implement-tasks workflow
SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW=$(specialize_scope_detection_workflows "$SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize deep reading workflows in implement-tasks workflow
SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW=$(specialize_deep_reading_workflows "$SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW" "$MERGED_KNOWLEDGE")

Specialize implement-tasks command:
- **Inject Project-Specific Implementation Patterns**: Replace abstract implementation patterns with project-specific patterns from basepoints
  - Extract implementation patterns relevant to task implementation from merged knowledge
  - Inject project-specific component creation patterns, module creation patterns, service creation patterns
  - Use project-specific implementation structures from basepoints

- **Inject Project-Specific Strategies**: Replace abstract strategies with project-specific strategies from basepoints
  - Extract implementation strategies and architectural strategies from merged knowledge
  - Inject project-specific test strategies, code organization strategies, dependency management strategies
  - Use project-specific implementation approaches from basepoints

- **Replace Abstract Placeholders**: Replace workflow and standards placeholders with project-specific content
  - Replace `Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

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
` with project-specific workflow content
  - Replace `@agent-os/standards/documentation/standards.md
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
@agent-os/standards/testing/test-writing.md` with actual project-specific standards content
  - Include project-specific examples and patterns in implementation

### Step 6: Read Abstract orchestrate-tasks Command Template

Read abstract orchestrate-tasks command template:

```bash
# Read orchestrate-tasks command
if [ -f "profiles/default/commands/orchestrate-tasks/orchestrate-tasks.md" ]; then
    ORCHESTRATE_TASKS_MAIN=$(cat profiles/default/commands/orchestrate-tasks/orchestrate-tasks.md)
fi

echo "✅ Loaded abstract orchestrate-tasks command template"
```

### Step 7: Specialize orchestrate-tasks Command

Inject project-specific knowledge into orchestrate-tasks command:

```bash
# Start with abstract template
SPECIALIZED_ORCHESTRATE_TASKS="$ORCHESTRATE_TASKS_MAIN"

# Inject project-specific orchestration patterns from basepoints
# Replace abstract orchestration patterns with project-specific patterns from merged knowledge
# Example: Replace generic "delegate to subagent" with project-specific delegation pattern from basepoints
SPECIALIZED_ORCHESTRATE_TASKS=$(echo "$SPECIALIZED_ORCHESTRATE_TASKS" | \
    sed "s|generic orchestration|$(extract_orchestration_pattern_from_merged "$MERGED_KNOWLEDGE" "orchestrate-tasks")|g")

# Inject project-specific workflows based on basepoints patterns
# Replace abstract workflows with project-specific workflows from merged knowledge
# Example: Replace generic "compile standards" with project-specific standards compilation workflow
SPECIALIZED_ORCHESTRATE_TASKS=$(echo "$SPECIALIZED_ORCHESTRATE_TASKS" | \
    sed "s|#### Compile Implementation Standards

Use the following logic to compile a list of file references to standards that should guide implementation:

##### Steps to Compile Standards List

1. Find the current task group in `orchestration.yml`
2. Check the list of `standards` specified for this task group in `orchestration.yml`
3. Compile the list of file references to those standards, one file reference per line, using this logic for determining which files to include:
   a. If the value for `standards` is simply `all`, then include every single file, folder, sub-folder and files within sub-folders in your list of files.
   b. If the item under standards ends with "*" then it means that all files within this folder or sub-folder should be included. For example, `global/*` means include all files and sub-folders and their files located inside of `agent-os/standards/global/`.
   c. If a file ends in `.md` then it means this is one specific file you must include in your list of files. For example `global/coding-style.md` means you must include the file located at `agent-os/standards/global/coding-style.md`.
   d. De-duplicate files in your list of file references.

##### Output Format

The compiled list of standards should look something like this, where each file reference is on its own line and begins with `@`. The exact list of files will vary:

```
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/testing/test-writing.md
@agent-os/standards/quality/assurance.md
```
|$(generate_project_workflow_content "$MERGED_KNOWLEDGE" "compile-implementation-standards")|g")

# Replace abstract placeholders with project-specific content
SPECIALIZED_ORCHESTRATE_TASKS=$(echo "$SPECIALIZED_ORCHESTRATE_TASKS" | \
    sed "s|Implement all tasks assigned to you and ONLY those task(s) that have been assigned to you.

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
|$(generate_project_workflow_ref "$MERGED_KNOWLEDGE" "implement-tasks")|g")

# Inject project-specific conditional compilation logic
