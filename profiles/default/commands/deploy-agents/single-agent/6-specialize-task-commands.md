Now that shape-spec and write-spec commands are specialized, proceed with specializing the create-tasks, implement-tasks, and orchestrate-tasks commands by following these instructions:

## Core Responsibilities

1. **Read Abstract Command Templates**: Load create-tasks, implement-tasks, and orchestrate-tasks templates from profiles/default
2. **Inject Project-Specific Knowledge**: Inject patterns, checkpoints, strategies, and workflows from merged knowledge
3. **Replace Abstract Placeholders**: Replace {{workflows/...}}, {{standards/...}} with project-specific content
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
    sed "s|{{workflows/implementation/create-tasks-list}}|$(generate_project_workflow_ref "$MERGED_KNOWLEDGE" "create-tasks-list")|g")

SPECIALIZED_CREATE_TASKS=$(echo "$SPECIALIZED_CREATE_TASKS" | \
    sed "s|{{standards/\*}}|$(generate_project_standards_content "$MERGED_KNOWLEDGE")|g")

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
  - Replace `{{workflows/implementation/create-tasks-list}}` with project-specific workflow content
  - Replace `{{standards/*}}` with actual project-specific standards content
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
    sed "s|{{workflows/implementation/implement-tasks}}|$(generate_project_workflow_ref "$MERGED_KNOWLEDGE" "implement-tasks")|g")

SPECIALIZED_IMPLEMENT_TASKS=$(echo "$SPECIALIZED_IMPLEMENT_TASKS" | \
    sed "s|{{standards/\*}}|$(generate_project_standards_content "$MERGED_KNOWLEDGE")|g")

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
  - Replace `{{workflows/implementation/implement-tasks}}` with project-specific workflow content
  - Replace `{{standards/*}}` with actual project-specific standards content
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
    sed "s|{{workflows/implementation/compile-implementation-standards}}|$(generate_project_workflow_content "$MERGED_KNOWLEDGE" "compile-implementation-standards")|g")

# Replace abstract placeholders with project-specific content
SPECIALIZED_ORCHESTRATE_TASKS=$(echo "$SPECIALIZED_ORCHESTRATE_TASKS" | \
    sed "s|{{workflows/implementation/implement-tasks}}|$(generate_project_workflow_ref "$MERGED_KNOWLEDGE" "implement-tasks")|g")

# Inject project-specific conditional compilation logic
# Adapt {{IF use_claude_code_subagents}} and {{UNLESS use_claude_code_subagents}} based on project setup
# Adapt {{UNLESS standards_as_claude_code_skills}} based on project setup
SPECIALIZED_ORCHESTRATE_TASKS=$(adapt_conditionals_for_project "$SPECIALIZED_ORCHESTRATE_TASKS" "$MERGED_KNOWLEDGE")
```

Specialize orchestrate-tasks command:
- **Inject Project-Specific Orchestration Patterns**: Replace abstract orchestration patterns with project-specific patterns from basepoints
  - Extract orchestration patterns relevant to task orchestration from merged knowledge
  - Inject project-specific delegation patterns, task group assignment patterns, subagent coordination patterns
  - Use project-specific orchestration structures from basepoints

- **Inject Project-Specific Workflows**: Replace abstract workflows with project-specific workflows based on basepoints patterns
  - Replace `{{workflows/implementation/compile-implementation-standards}}` with project-specific standards compilation workflow
  - Replace `{{workflows/implementation/implement-tasks}}` with project-specific implementation workflow
  - Include project-specific workflow patterns from basepoints

- **Replace Abstract Placeholders**: Replace workflow and standards placeholders with project-specific content
  - Adapt conditional compilation tags ({{IF use_claude_code_subagents}}, {{UNLESS standards_as_claude_code_skills}}) based on project setup
  - Include project-specific examples and patterns in orchestration

### Step 8: Write Specialized Commands to agent-os/commands/

Write specialized commands to agent-os/commands/, replacing abstract versions:

```bash
# Ensure agent-os/commands/ directories exist
mkdir -p agent-os/commands/create-tasks/single-agent
mkdir -p agent-os/commands/implement-tasks/single-agent
mkdir -p agent-os/commands/orchestrate-tasks

# Write specialized create-tasks command
echo "$SPECIALIZED_CREATE_TASKS" > agent-os/commands/create-tasks/create-tasks.md
echo "$SPECIALIZED_CREATE_TASKS" > agent-os/commands/create-tasks/single-agent/create-tasks.md

# Write specialized create-tasks phase files
for phase_file in profiles/default/commands/create-tasks/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_NAME=$(basename "$phase_file")
        
        # Extract specialized phase content
        PHASE_CONTENT=$(extract_phase_from_specialized "$SPECIALIZED_CREATE_TASKS_PHASES" "$PHASE_NAME")
        
        # Write specialized phase file
        echo "$PHASE_CONTENT" > "agent-os/commands/create-tasks/single-agent/$PHASE_NAME"
    fi
done

# Write specialized create-tasks workflow (if workflows directory exists)
mkdir -p agent-os/workflows/implementation
echo "$SPECIALIZED_CREATE_TASKS_WORKFLOW" > agent-os/workflows/implementation/create-tasks-list.md

# Write specialized implement-tasks command
echo "$SPECIALIZED_IMPLEMENT_TASKS" > agent-os/commands/implement-tasks/implement-tasks.md
echo "$SPECIALIZED_IMPLEMENT_TASKS" > agent-os/commands/implement-tasks/single-agent/implement-tasks.md

# Write specialized implement-tasks phase files
for phase_file in profiles/default/commands/implement-tasks/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_NAME=$(basename "$phase_file")
        
        # Extract specialized phase content
        PHASE_CONTENT=$(extract_phase_from_specialized "$SPECIALIZED_IMPLEMENT_TASKS_PHASES" "$PHASE_NAME")
        
        # Write specialized phase file
        echo "$PHASE_CONTENT" > "agent-os/commands/implement-tasks/single-agent/$PHASE_NAME"
    fi
done

# Write specialized implement-tasks workflow (if workflows directory exists)
echo "$SPECIALIZED_IMPLEMENT_TASKS_WORKFLOW" > agent-os/workflows/implementation/implement-tasks.md

# Write specialized orchestrate-tasks command
echo "$SPECIALIZED_ORCHESTRATE_TASKS" > agent-os/commands/orchestrate-tasks/orchestrate-tasks.md

echo "✅ Specialized create-tasks, implement-tasks, and orchestrate-tasks commands written to agent-os/commands/"
```

Write specialized commands:
- **create-tasks.md**: Write specialized create-tasks command to `agent-os/commands/create-tasks/create-tasks.md` (replace abstract version)
- **single-agent/create-tasks.md**: Write specialized single-agent version to `agent-os/commands/create-tasks/single-agent/create-tasks.md`
- **create-tasks phase files**: Write specialized phase files to `agent-os/commands/create-tasks/single-agent/`
- **create-tasks workflow**: Write specialized workflow to `agent-os/workflows/implementation/create-tasks-list.md`
- **implement-tasks.md**: Write specialized implement-tasks command to `agent-os/commands/implement-tasks/implement-tasks.md` (replace abstract version)
- **single-agent/implement-tasks.md**: Write specialized single-agent version to `agent-os/commands/implement-tasks/single-agent/implement-tasks.md`
- **implement-tasks phase files**: Write specialized phase files to `agent-os/commands/implement-tasks/single-agent/`
- **implement-tasks workflow**: Write specialized workflow to `agent-os/workflows/implementation/implement-tasks.md`
- **orchestrate-tasks.md**: Write specialized orchestrate-tasks command to `agent-os/commands/orchestrate-tasks/orchestrate-tasks.md` (replace abstract version)

Ensure specialized commands:
- Are ready to use immediately (no further processing needed)
- Reference project-specific patterns, standards, workflows
- Contain project-specific examples and patterns
- Are tailored for the specific codebase (not abstract/generic)

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once create-tasks, implement-tasks, and orchestrate-tasks commands are specialized and written, output the following message:

```
✅ create-tasks, implement-tasks, and orchestrate-tasks commands specialized!

- create-tasks command: Specialized with project-specific task creation patterns and checkpoints
- implement-tasks command: Specialized with project-specific implementation patterns and strategies
- orchestrate-tasks command: Specialized with project-specific orchestration patterns and workflows
- Abstract placeholders: Replaced with project-specific content
- Commands written to: agent-os/commands/create-tasks/, agent-os/commands/implement-tasks/, agent-os/commands/orchestrate-tasks/

Specialized commands are ready to use immediately.

NEXT STEP 👉 Run the command, `7-update-supporting-structures.md`
```
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure that the specialized commands align with the user's preferences and standards as detailed in the following files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}

## Specialization Functions

### Specialize Basepoints Knowledge Extraction Workflows

Replace placeholder logic in basepoints knowledge extraction workflows with project-specific patterns:

```bash
specialize_basepoints_extraction_workflows() {
    local content="$1"
    local merged_knowledge="$2"
    
    # Load project-specific extraction patterns from cache
    if [ -f "agent-os/output/deploy-agents/knowledge/basepoint-file-pattern.txt" ]; then
        BASEPOINT_FILE_PATTERN=$(cat agent-os/output/deploy-agents/knowledge/basepoint-file-pattern.txt)
    else
        BASEPOINT_FILE_PATTERN="agent-base-*.md"
    fi
    
    # Replace {{BASEPOINTS_PATH}} with actual path
    content=$(echo "$content" | sed "s|{{BASEPOINTS_PATH}}|agent-os/basepoints|g")
    
    # Replace {{BASEPOINT_FILE_PATTERN}} with project-specific pattern
    content=$(echo "$content" | sed "s|{{BASEPOINT_FILE_PATTERN}}|$BASEPOINT_FILE_PATTERN|g")
    
    # Replace extraction placeholders with project-specific extraction logic
    content=$(inject_project_extraction_logic "$content" "$merged_knowledge")
    
    # Replace {{ABSTRACTION_LAYER_NAMES}} with actual layer names from basepoints
    ABSTRACTION_LAYERS=$(extract_abstraction_layers_from_merged "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{ABSTRACTION_LAYER_NAMES}}|$ABSTRACTION_LAYERS|g")
    
    echo "$content"
}
```

### Specialize Scope Detection Workflows

Replace placeholder logic in scope detection workflows with project-specific patterns:

```bash
specialize_scope_detection_workflows() {
    local content="$1"
    local merged_knowledge="$2"
    
    # Replace {{KEYWORD_EXTRACTION_PATTERN}} with project-specific keyword extraction
    KEYWORD_PATTERN=$(extract_keyword_extraction_pattern "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{KEYWORD_EXTRACTION_PATTERN}}|$KEYWORD_PATTERN|g")
    
    # Replace {{SEMANTIC_ANALYSIS_PATTERN}} with project-specific semantic analysis
    SEMANTIC_PATTERN=$(extract_semantic_analysis_pattern "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{SEMANTIC_ANALYSIS_PATTERN}}|$SEMANTIC_PATTERN|g")
    
    # Replace {{DETERMINE_SPEC_CONTEXT_LAYER}} with project-specific layer detection
    LAYER_DETECTION=$(extract_layer_detection_logic "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{DETERMINE_SPEC_CONTEXT_LAYER}}|$LAYER_DETECTION|g")
    
    # Replace {{CALCULATE_LAYER_DISTANCE}} with project-specific distance calculation
    DISTANCE_CALC=$(extract_distance_calculation "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{CALCULATE_LAYER_DISTANCE}}|$DISTANCE_CALC|g")
    
    echo "$content"
}
```

### Specialize Deep Reading Workflows

Replace placeholder logic in deep reading workflows with project-specific patterns:

```bash
specialize_deep_reading_workflows() {
    local content="$1"
    local merged_knowledge="$2"
    
    # Replace {{CODE_FILE_PATTERNS}} with project-specific file patterns
    CODE_PATTERNS=$(extract_code_file_patterns "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{CODE_FILE_PATTERNS}}|$CODE_PATTERNS|g")
    
    # Replace {{FIND_MODULE_PATH}} with project-specific module path detection
    MODULE_PATH_LOGIC=$(extract_module_path_logic "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{FIND_MODULE_PATH}}|$MODULE_PATH_LOGIC|g")
    
    # Replace {{EXTRACT_DESIGN_PATTERNS}} with project-specific pattern extraction
    PATTERN_EXTRACTION=$(extract_pattern_extraction_logic "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{EXTRACT_DESIGN_PATTERNS}}|$PATTERN_EXTRACTION|g")
    
    # Replace {{DETECT_SIMILAR_CODE}} with project-specific similar code detection
    SIMILAR_CODE_DETECTION=$(extract_similar_code_detection "$merged_knowledge")
    content=$(echo "$content" | sed "s|{{DETECT_SIMILAR_CODE}}|$SIMILAR_CODE_DETECTION|g")
    
    echo "$content"
}
```

### Step 9: Validate Specialized Commands Use Basepoints Knowledge Extraction

Validate that all specialized commands correctly reference and use basepoints knowledge extraction:

```bash
# Validate shape-spec command
if grep -q "{{workflows/basepoints/extract-basepoints-knowledge-automatic}}" "agent-os/commands/shape-spec/single-agent/2-shape-spec.md" 2>/dev/null; then
    echo "❌ shape-spec still contains placeholder for basepoints extraction"
    exit 1
fi
if ! grep -q "extract-basepoints-knowledge\|basepoints.*extract" "agent-os/commands/shape-spec/single-agent/2-shape-spec.md" 2>/dev/null; then
    echo "⚠️  Warning: shape-spec may not reference basepoints knowledge extraction"
fi

# Validate write-spec command
if grep -q "{{workflows/basepoints/extract-basepoints-knowledge-automatic}}" "agent-os/commands/write-spec/single-agent/write-spec.md" 2>/dev/null; then
    echo "❌ write-spec still contains placeholder for basepoints extraction"
    exit 1
fi

# Validate create-tasks command
if grep -q "{{workflows/basepoints/extract-basepoints-knowledge-automatic}}" "agent-os/commands/create-tasks/single-agent/2-create-tasks-list.md" 2>/dev/null; then
    echo "❌ create-tasks still contains placeholder for basepoints extraction"
    exit 1
fi

# Validate implement-tasks command
if grep -q "{{workflows/basepoints/extract-basepoints-knowledge-automatic}}" "agent-os/commands/implement-tasks/single-agent/2-implement-tasks.md" 2>/dev/null; then
    echo "❌ implement-tasks still contains placeholder for basepoints extraction"
    exit 1
fi

# Validate workflows reference specialized basepoints extraction
if [ -f "agent-os/workflows/implementation/create-tasks-list.md" ]; then
    if grep -q "{{BASEPOINTS_PATH}}\|{{BASEPOINT_FILE_PATTERN}}" "agent-os/workflows/implementation/create-tasks-list.md"; then
        echo "❌ create-tasks-list workflow still contains placeholders"
        exit 1
    fi
fi

if [ -f "agent-os/workflows/implementation/implement-tasks.md" ]; then
    if grep -q "{{BASEPOINTS_PATH}}\|{{BASEPOINT_FILE_PATTERN}}" "agent-os/workflows/implementation/implement-tasks.md"; then
        echo "❌ implement-tasks workflow still contains placeholders"
        exit 1
    fi
fi

echo "✅ All specialized commands correctly use basepoints knowledge extraction"
```

## Important Constraints

- Must inject project-specific patterns, checkpoints, strategies, workflows from merged knowledge
- Must replace all abstract placeholders ({{workflows/...}}, {{standards/...}}) with project-specific content
- **Must specialize basepoints knowledge extraction workflows**: Replace {{BASEPOINTS_PATH}}, {{BASEPOINT_FILE_PATTERN}}, and extraction placeholders with project-specific patterns
- **Must specialize scope detection workflows**: Replace keyword extraction, semantic analysis, and layer detection placeholders with project-specific patterns
- **Must specialize deep reading workflows**: Replace code file patterns, module path detection, and pattern extraction placeholders with project-specific patterns
- **Must validate specialized commands**: Verify all specialized commands correctly reference and use basepoints knowledge extraction (no placeholders remain)
- Must adapt checkpoints based on project structure complexity (from merged knowledge)
- Must write specialized commands to agent-os/commands/ (replace abstract versions)
- Specialized commands must be ready to use immediately (no further processing needed)
- Must preserve command structure and phase organization while injecting project-specific knowledge
