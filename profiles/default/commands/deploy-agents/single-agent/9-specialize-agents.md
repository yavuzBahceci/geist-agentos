Now that standards are specialized, proceed with specializing agents based on project needs by following these instructions:

## Core Responsibilities

1. **Analyze Agent Requirements**: Determine which agents are needed based on project complexity
2. **Evaluate Existing Agents**: Check which agents from templates are relevant to this project
3. **Specialize Agents**: Update agents with project-specific context and patterns
4. **Remove Unnecessary Agents**: For simple projects, remove agents that add unnecessary complexity
5. **Create Project-Specific Agents**: Add new agents based on project-specific needs

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

### Step 2: Evaluate Agent Relevance

Determine which agents are needed for this project:

```bash
# List existing agents
EXISTING_AGENTS=$(find agent-os/agents -name "*.md" -type f 2>/dev/null)

# For each agent, evaluate relevance based on:
# 1. Project patterns from basepoints
# 2. Project complexity
# 3. Project tech stack and requirements

AGENTS_EVALUATION=""
for agent_file in $EXISTING_AGENTS; do
    AGENT_NAME=$(basename "$agent_file" .md)
    
    # Evaluate based on complexity
    case "$PROJECT_NATURE" in
        "simple")
            # Simple projects need only core agents
            if [[ "$AGENT_NAME" =~ ^(implementation|specification)$ ]]; then
                RELEVANCE="required"
            else
                RELEVANCE="optional"
            fi
            ;;
        "moderate")
            # Moderate projects need most agents
            RELEVANCE="required"
            ;;
        "complex")
            # Complex projects need all agents plus specialized
            RELEVANCE="required"
            ;;
    esac
    
    AGENTS_EVALUATION="${AGENTS_EVALUATION}${AGENT_NAME}:${RELEVANCE}\n"
done
```

### Step 3: Specialize Agents with Project Context

Update agents with project-specific context:

```bash
# Extract project context from basepoints
PROJECT_CONTEXT=$(extract_project_context "$BASEPOINTS_KNOWLEDGE" "$MERGED_KNOWLEDGE")

# For each existing agent
for agent_file in $EXISTING_AGENTS; do
    AGENT_NAME=$(basename "$agent_file" .md)
    AGENT_CONTENT=$(cat "$agent_file")
    
    # Add project-specific context to agent
    # - Project patterns and standards
    # - Project-specific terminology
    # - Project architecture context
    
    UPDATED_CONTENT=$(inject_project_context "$AGENT_CONTENT" "$PROJECT_CONTEXT")
    echo "$UPDATED_CONTENT" > "$agent_file"
    echo "✅ Updated agent with project context: $AGENT_NAME"
done
```

### Step 4: Simplify Agents for Simple Projects

For simple projects, remove or combine unnecessary agents:

```bash
if [ "$PROJECT_NATURE" = "simple" ]; then
    echo "📋 Simplifying agents for simple project..."
    
    # For simple projects, some specialized agents may not be needed
    # Instead of removing, we can mark them as optional or combine functionality
    
    # List of agents that can be combined for simple projects
    COMBINABLE_AGENTS=(
        "researcher"
        "reviewer"
    )
    
    for optional in "${COMBINABLE_AGENTS[@]}"; do
        OPTIONAL_FILE="agent-os/agents/${optional}.md"
        if [ -f "$OPTIONAL_FILE" ]; then
            echo "ℹ️  Agent marked as optional for simple project: $optional"
        fi
    done
    
    echo "✅ Agents simplified for simple project"
fi
```

### Step 5: Create Project-Specific Agents

Create new agents based on project-specific needs:

```bash
# Analyze if project needs specialized agents based on:
# - Unique abstraction layers
# - Specific technology requirements
# - Complex domain patterns

PROJECT_AGENT_NEEDS=$(analyze_agent_needs "$BASEPOINTS_KNOWLEDGE")

if [ -n "$PROJECT_AGENT_NEEDS" ]; then
    for agent_need in $PROJECT_AGENT_NEEDS; do
        AGENT_NAME=$(echo "$agent_need" | cut -d':' -f1)
        AGENT_PURPOSE=$(echo "$agent_need" | cut -d':' -f2-)
        
        cat > "agent-os/agents/${AGENT_NAME}.md" << EOF
# Agent: ${AGENT_NAME}

## Purpose

${AGENT_PURPOSE}

## Responsibilities

1. [Responsibility based on project patterns]
2. [Responsibility based on project architecture]

## Context

This agent was created based on patterns detected in the project's codebase during deploy-agents specialization.

## Guidelines

- Follow project standards and conventions
- Reference basepoints knowledge for patterns
- Maintain consistency with project architecture
EOF
        
        echo "✅ Created project-specific agent: ${AGENT_NAME}"
    done
fi
```

### Step 6: Verify Agents Consistency

Verify all agents are consistent and properly configured:

```bash
# Count agents
AGENTS_COUNT=$(find agent-os/agents -name "*.md" -type f | wc -l | tr -d ' ')

# Log to reports
mkdir -p agent-os/output/deploy-agents/reports
cat > agent-os/output/deploy-agents/reports/agents-specialization.md << EOF
# Agents Specialization Report

## Summary
- Project Nature: $PROJECT_NATURE
- Total Agents: $AGENTS_COUNT

## Agents Processed
$(find agent-os/agents -name "*.md" -type f | while read f; do
    echo "- $(basename "$f" .md)"
done)

## Actions Taken
- Updated agents with project context from basepoints
- Simplified agents for project complexity level
- Created project-specific agents where needed

## Next Steps
Proceed to specialize workflows in phase 10.
EOF

echo "✅ Agents specialization complete"
echo "📁 Report saved to: agent-os/output/deploy-agents/reports/agents-specialization.md"
```

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once you've specialized agents, output the following message:

```
✅ Agents specialized for project!

**Project Nature:** [simple/moderate/complex]
**Agents Updated:** [count]
**Project-Specific Agents Created:** [count]

Report: `agent-os/output/deploy-agents/reports/agents-specialization.md`

NEXT STEP 👉 Run the command, `10-specialize-workflows.md`
```
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

Ensure agents specialization follows the user's preferences:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
