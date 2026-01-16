Now that agents are specialized, proceed with specializing workflows based on project needs by following these instructions:

## Core Responsibilities

1. **Analyze Workflow Requirements**: Determine which workflows are needed based on project complexity
2. **Evaluate Existing Workflows**: Check which workflows from templates are relevant to this project
3. **Specialize Workflows**: Update workflows with project-specific patterns and context
4. **Remove Unnecessary Workflows**: For simple projects, remove workflows that add unnecessary complexity
5. **Create Project-Specific Workflows**: Add new workflows based on project-specific patterns

## Workflow

### Step 1: Load Knowledge and Complexity Assessment

Load knowledge and complexity assessment:

```bash
# Load merged knowledge
if [ -f "agent-os/output/deploy-agents/knowledge/merged-knowledge.json" ]; then
    MERGED_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/merged-knowledge.json)
    echo "✅ Loaded merged knowledge"
fi

# Load basepoints knowledge
if [ -f "agent-os/output/deploy-agents/knowledge/basepoints-knowledge.json" ]; then
    BASEPOINTS_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/basepoints-knowledge.json)
fi

# Load complexity assessment
if [ -f "agent-os/output/deploy-agents/reports/complexity-assessment.json" ]; then
    COMPLEXITY=$(cat agent-os/output/deploy-agents/reports/complexity-assessment.json)
    PROJECT_NATURE=$(echo "$COMPLEXITY" | grep -o '"nature": *"[^"]*"' | cut -d'"' -f4)
else
    PROJECT_NATURE="moderate"
fi

echo "Project Nature: $PROJECT_NATURE"
```

### Step 2: Evaluate Workflow Relevance

Determine which workflows are needed for this project:

```bash
# List existing workflows
EXISTING_WORKFLOWS=$(find agent-os/workflows -name "*.md" -type f 2>/dev/null)

# Core workflow categories
WORKFLOW_CATEGORIES=(
    "specification"    # Always needed
    "implementation"   # Always needed
    "planning"         # Needed for moderate/complex
    "validation"       # Needed for complex
    "codebase-analysis" # Needed for complex
)

# For each workflow, evaluate relevance based on complexity
WORKFLOWS_EVALUATION=""
for workflow_file in $EXISTING_WORKFLOWS; do
    WORKFLOW_NAME=$(basename "$workflow_file" .md)
    WORKFLOW_DIR=$(dirname "$workflow_file" | sed 's|agent-os/workflows/||')
    
    # Evaluate based on complexity and category
    case "$PROJECT_NATURE" in
        "simple")
            # Simple projects need only core workflows
            if [[ "$WORKFLOW_DIR" =~ ^(specification|implementation)$ ]]; then
                RELEVANCE="required"
            else
                RELEVANCE="optional"
            fi
            ;;
        "moderate")
            # Moderate projects need most workflows
            if [[ "$WORKFLOW_DIR" =~ ^(validation|scope-detection)$ ]]; then
                RELEVANCE="optional"
            else
                RELEVANCE="required"
            fi
            ;;
        "complex")
            # Complex projects need all workflows
            RELEVANCE="required"
            ;;
    esac
    
    WORKFLOWS_EVALUATION="${WORKFLOWS_EVALUATION}${WORKFLOW_DIR}/${WORKFLOW_NAME}:${RELEVANCE}\n"
done
```

### Step 3: Specialize Workflows with Project Patterns

Update workflows with project-specific patterns:

```bash
# Extract workflow-relevant patterns from basepoints
WORKFLOW_PATTERNS=$(extract_workflow_patterns "$BASEPOINTS_KNOWLEDGE")

# For each existing workflow
for workflow_file in $EXISTING_WORKFLOWS; do
    WORKFLOW_NAME=$(basename "$workflow_file" .md)
    WORKFLOW_CONTENT=$(cat "$workflow_file")
    
    # Extract project-specific patterns relevant to this workflow
    RELEVANT_PATTERNS=$(extract_patterns_for_workflow "$WORKFLOW_PATTERNS" "$WORKFLOW_NAME")
    
    if [ -n "$RELEVANT_PATTERNS" ]; then
        # Inject project patterns into workflow
        UPDATED_CONTENT=$(inject_patterns_into_workflow "$WORKFLOW_CONTENT" "$RELEVANT_PATTERNS")
        echo "$UPDATED_CONTENT" > "$workflow_file"
        echo "✅ Updated workflow with project patterns: $WORKFLOW_NAME"
    fi
done
```

### Step 4: Simplify Workflows for Simple Projects

For simple projects, remove or combine unnecessary workflows:

```bash
if [ "$PROJECT_NATURE" = "simple" ]; then
    echo "📋 Simplifying workflows for simple project..."
    
    # List of workflow categories that can be simplified for simple projects
    SIMPLIFIABLE_CATEGORIES=(
        "validation"
        "scope-detection"
        "deep-reading"
        "human-review"
    )
    
    for category in "${SIMPLIFIABLE_CATEGORIES[@]}"; do
        CATEGORY_DIR="agent-os/workflows/$category"
        if [ -d "$CATEGORY_DIR" ]; then
            echo "ℹ️  Workflow category marked as optional for simple project: $category"
            # Could optionally remove or mark these workflows
        fi
    done
    
    echo "✅ Workflows simplified for simple project"
fi
```

### Step 5: Create Project-Specific Workflows

Create new workflows based on project-specific patterns:

```bash
# Analyze if project needs specialized workflows based on:
# - Unique data flows from basepoints
# - Specific control flows from basepoints
# - Complex dependency flows

PROJECT_WORKFLOW_NEEDS=$(analyze_workflow_needs "$BASEPOINTS_KNOWLEDGE")

if [ -n "$PROJECT_WORKFLOW_NEEDS" ]; then
    # Create project-specific workflows directory
    mkdir -p agent-os/workflows/project
    
    for workflow_need in $PROJECT_WORKFLOW_NEEDS; do
        WORKFLOW_NAME=$(echo "$workflow_need" | cut -d':' -f1)
        WORKFLOW_PURPOSE=$(echo "$workflow_need" | cut -d':' -f2-)
        
        cat > "agent-os/workflows/project/${WORKFLOW_NAME}.md" << EOF
# Workflow: ${WORKFLOW_NAME}

## Purpose

${WORKFLOW_PURPOSE}

## Core Responsibilities

1. [Responsibility based on project patterns]
2. [Responsibility based on project flows]

## Workflow Steps

### Step 1: [Step Name]

[Step description based on project patterns]

### Step 2: [Step Name]

[Step description based on project patterns]

## Context

This workflow was created based on patterns detected in the project's codebase during deploy-agents specialization.

## Important Constraints

- Follow project standards and conventions
- Reference basepoints knowledge for patterns
- Maintain consistency with project architecture
EOF
        
        echo "✅ Created project-specific workflow: ${WORKFLOW_NAME}"
    done
fi
```

### Step 6: Verify Workflows Consistency

Verify all workflows are consistent and properly configured:

```bash
# Count workflows
WORKFLOWS_COUNT=$(find agent-os/workflows -name "*.md" -type f | wc -l | tr -d ' ')

# Count workflow categories
CATEGORY_COUNT=$(find agent-os/workflows -type d | wc -l | tr -d ' ')

# Log to reports
mkdir -p agent-os/output/deploy-agents/reports
cat > agent-os/output/deploy-agents/reports/workflows-specialization.md << EOF
# Workflows Specialization Report

## Summary
- Project Nature: $PROJECT_NATURE
- Total Workflows: $WORKFLOWS_COUNT
- Workflow Categories: $CATEGORY_COUNT

## Workflow Categories
$(find agent-os/workflows -type d | sed 's|agent-os/workflows/||' | while read d; do
    [ -n "$d" ] && echo "- $d: $(find "agent-os/workflows/$d" -name "*.md" -type f 2>/dev/null | wc -l | tr -d ' ') workflows"
done)

## Actions Taken
- Updated workflows with project patterns from basepoints
- Simplified workflows for project complexity level
- Created project-specific workflows where needed

## Next Steps
Proceed to finalize deployment in phase 11.
EOF

echo "✅ Workflows specialization complete"
echo "📁 Report saved to: agent-os/output/deploy-agents/reports/workflows-specialization.md"
```

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once you've specialized workflows, output the following message:

```
✅ Workflows specialized for project!

**Project Nature:** [simple/moderate/complex]
**Workflows Updated:** [count]
**Project-Specific Workflows Created:** [count]

Report: `agent-os/output/deploy-agents/reports/workflows-specialization.md`

NEXT STEP 👉 Run the command, `11-adapt-structure-and-finalize.md`
```
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

Ensure workflows specialization follows the user's preferences:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
