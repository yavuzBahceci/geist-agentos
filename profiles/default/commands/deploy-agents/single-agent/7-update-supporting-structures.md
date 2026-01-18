Now that all five core commands are specialized, proceed with updating supporting structures (standards, workflows, and agents) based on project-specific patterns by following these instructions:

## Core Responsibilities

1. **Create or Update Standards**: Create new standards and update existing ones in geist/standards/ based on project-specific patterns from basepoints
2. **Create or Merge Workflows**: Create new workflows or merge existing ones in geist/workflows/ based on project structure requirements
3. **Create or Update Agents**: Create new agents or update existing ones in geist/agents/ based on project-specific needs and patterns
4. **Verify References**: Ensure specialized commands reference updated standards, workflows, and agents correctly

## Workflow

### Step 1: Load Merged Knowledge

Load merged knowledge from previous phases:

```bash
# Load merged knowledge from cache
if [ -f "geist/output/deploy-agents/knowledge/merged-knowledge.json" ]; then
    MERGED_KNOWLEDGE=$(cat geist/output/deploy-agents/knowledge/merged-knowledge.json)
    echo "✅ Loaded merged knowledge"
fi

# Load basepoints knowledge for standards extraction
if [ -f "geist/output/deploy-agents/knowledge/basepoints-knowledge.json" ]; then
    BASEPOINTS_KNOWLEDGE=$(cat geist/output/deploy-agents/knowledge/basepoints-knowledge.json)
fi
```

### Step 2: Create or Update Standards in geist/standards/

Create new standards or update existing ones based on project-specific patterns:

```bash
# Ensure geist/standards/ directories exist
mkdir -p geist/standards/global
mkdir -p geist/standards

# Extract project-specific standards patterns from merged knowledge
PROJECT_STANDARDS=$(extract_standards_from_merged "$MERGED_KNOWLEDGE")

# For each standard type (naming, coding-style, structure, etc.)
for standard_type in naming coding-style structure conventions error-handling; do
    STANDARD_CONTENT=$(extract_standard_of_type "$PROJECT_STANDARDS" "$standard_type")
    
    # Check if standard exists in profiles/default for pattern reference
    if [ -f "profiles/default/standards/global/${standard_type}.md" ]; then
        STANDARD_PATTERN=$(cat "profiles/default/standards/global/${standard_type}.md")
        
        # Merge project-specific patterns into standard template
        SPECIALIZED_STANDARD=$(merge_standard_pattern "$STANDARD_PATTERN" "$STANDARD_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Write specialized standard
        echo "$SPECIALIZED_STANDARD" > "geist/standards/global/${standard_type}.md"
        echo "✅ Created/updated standard: geist/standards/global/${standard_type}.md"
    else
        # Create new standard if pattern doesn't exist
        NEW_STANDARD=$(create_new_standard "$STANDARD_CONTENT" "$standard_type" "$MERGED_KNOWLEDGE")
        echo "$NEW_STANDARD" > "geist/standards/global/${standard_type}.md"
        echo "✅ Created new standard: geist/standards/global/${standard_type}.md"
    fi
done

# Create project-specific standards that don't exist in global template
# Example: If basepoints show project uses specific framework conventions
PROJECT_SPECIFIC_STANDARDS=$(extract_project_specific_standards "$BASEPOINTS_KNOWLEDGE")
for project_standard in $PROJECT_SPECIFIC_STANDARDS; do
    STANDARD_NAME=$(echo "$project_standard" | cut -d':' -f1)
    STANDARD_CONTENT=$(echo "$project_standard" | cut -d':' -f2-)
    
    # Determine appropriate directory (global/ or project-specific/)
    STANDARD_DIR="geist/standards"
    if [[ "$STANDARD_NAME" =~ ^(global|framework|library)- ]]; then
        STANDARD_DIR="geist/standards/global"
    fi
    
    echo "$STANDARD_CONTENT" > "${STANDARD_DIR}/${STANDARD_NAME}.md"
    echo "✅ Created project-specific standard: ${STANDARD_DIR}/${STANDARD_NAME}.md"
done
```

Create or update standards:
- **Follow Standards Patterns**: Use patterns from profiles/default/standards/global/ as templates
- **Create New Standards**: Create new standards based on project-specific patterns extracted from basepoints
  - Extract naming conventions, coding styles, structure patterns from merged knowledge
  - Create standards for project-specific frameworks, libraries, or technologies
  - Include project-specific conventions and patterns

- **Update Existing Standards**: Update existing standards in geist/standards/ with project-specific conventions
  - Merge project-specific patterns into standard templates
  - Update naming conventions based on basepoints
  - Update coding styles based on project patterns
  - Update structure standards based on project organization

### Step 3: Create or Merge Workflows in geist/workflows/

Create new workflows or merge existing ones based on project structure:

```bash
# Ensure geist/workflows/ directories exist
mkdir -p geist/workflows/specification
mkdir -p geist/workflows/implementation
mkdir -p geist/workflows/planning
mkdir -p geist/workflows/codebase-analysis

# Extract project-specific workflow patterns from merged knowledge
PROJECT_WORKFLOWS=$(extract_workflows_from_merged "$MERGED_KNOWLEDGE")

# Analyze project structure to determine if specialized workflows are needed
PROJECT_STRUCTURE=$(extract_structure_from_merged "$MERGED_KNOWLEDGE")
WORKFLOW_REQUIREMENTS=$(analyze_workflow_requirements "$PROJECT_STRUCTURE" "$BASEPOINTS_KNOWLEDGE")

# For each workflow category (specification, implementation, planning, codebase-analysis)
for workflow_category in specification implementation planning codebase-analysis; do
    CATEGORY_WORKFLOWS=$(echo "$PROJECT_WORKFLOWS" | grep "^${workflow_category}/")
    
    for workflow_path in $CATEGORY_WORKFLOWS; do
        WORKFLOW_NAME=$(basename "$workflow_path" .md)
        
        # Check if abstract workflow exists in profiles/default
        if [ -f "profiles/default/workflows/${workflow_path}" ]; then
            ABSTRACT_WORKFLOW=$(cat "profiles/default/workflows/${workflow_path}")
            
            # Check if multiple basepoint patterns suggest workflow consolidation
            if needs_workflow_merge "$workflow_path" "$BASEPOINTS_KNOWLEDGE"; then
                # Merge workflows based on multiple basepoint patterns
                MERGED_WORKFLOWS=$(merge_workflows "$ABSTRACT_WORKFLOW" "$BASEPOINTS_KNOWLEDGE" "$workflow_path")
                echo "$MERGED_WORKFLOWS" > "geist/workflows/${workflow_path}"
                echo "✅ Merged workflow: geist/workflows/${workflow_path}"
            else
                # Specialize workflow with project-specific patterns
                SPECIALIZED_WORKFLOW=$(specialize_workflow "$ABSTRACT_WORKFLOW" "$MERGED_KNOWLEDGE" "$workflow_path")
                echo "$SPECIALIZED_WORKFLOW" > "geist/workflows/${workflow_path}"
                echo "✅ Specialized workflow: geist/workflows/${workflow_path}"
            fi
        else
            # Create new workflow if project structure requires specialized workflow
            if needs_new_workflow "$workflow_path" "$WORKFLOW_REQUIREMENTS"; then
                NEW_WORKFLOW=$(create_new_workflow "$workflow_path" "$MERGED_KNOWLEDGE" "$BASEPOINTS_KNOWLEDGE")
                
                # Create directory if needed
                WORKFLOW_DIR=$(dirname "geist/workflows/${workflow_path}")
                mkdir -p "$WORKFLOW_DIR"
                
                echo "$NEW_WORKFLOW" > "geist/workflows/${workflow_path}"
                echo "✅ Created new workflow: geist/workflows/${workflow_path}"
            fi
        fi
    done
done
```

Create or merge workflows:
- **Follow Workflow Patterns**: Use patterns from profiles/default/workflows/ as templates
- **Create New Workflows**: Create new workflows if project structure requires specialized workflows
  - Analyze project structure to identify workflow requirements
  - Create workflows for project-specific patterns or processes
  - Include project-specific steps, checks, or validations

- **Merge Existing Workflows**: Merge existing workflows if multiple basepoint patterns suggest workflow consolidation
  - Identify workflows that need consolidation based on basepoints
  - Merge complementary workflow patterns from different basepoints
  - Consolidate duplicate or overlapping workflow steps

- **Specialize Workflows**: Specialize abstract workflows with project-specific patterns
  - Inject project-specific patterns into workflow steps
  - Replace abstract placeholders with project-specific content
  - Include project-specific examples and patterns

### Step 4: Create or Update Agents in geist/agents/

Create new agents or update existing ones based on project-specific needs:

```bash
# Ensure geist/agents/ directory exists
mkdir -p geist/agents

# Extract project-specific agent patterns from merged knowledge
PROJECT_AGENTS=$(extract_agents_from_merged "$MERGED_KNOWLEDGE")

# Analyze project needs to determine if specialized agents are required
PROJECT_NEEDS=$(analyze_project_agent_needs "$BASEPOINTS_KNOWLEDGE" "$MERGED_KNOWLEDGE")

# For each agent type from profiles/default/agents/
for agent_file in profiles/default/agents/*.md; do
    AGENT_NAME=$(basename "$agent_file" .md)
    
    if [ -f "$agent_file" ]; then
        ABSTRACT_AGENT=$(cat "$agent_file")
        
        # Check if agent needs project-specific specialization
        if needs_agent_update "$AGENT_NAME" "$PROJECT_NEEDS"; then
            # Update agent with project-specific knowledge
            SPECIALIZED_AGENT=$(specialize_agent "$ABSTRACT_AGENT" "$MERGED_KNOWLEDGE" "$AGENT_NAME")
            echo "$SPECIALIZED_AGENT" > "geist/agents/${AGENT_NAME}.md"
            echo "✅ Updated agent: geist/agents/${AGENT_NAME}.md"
        else
            # Copy agent as-is if no specialization needed
            cp "$agent_file" "geist/agents/${AGENT_NAME}.md"
            echo "✅ Copied agent: geist/agents/${AGENT_NAME}.md"
        fi
    fi
done

# Create new agents based on project-specific needs and patterns
NEW_AGENTS=$(identify_new_agents_needed "$PROJECT_NEEDS" "$BASEPOINTS_KNOWLEDGE")
for new_agent in $NEW_AGENTS; do
    AGENT_NAME=$(echo "$new_agent" | cut -d':' -f1)
    AGENT_PATTERNS=$(echo "$new_agent" | cut -d':' -f2-)
    
    # Create new agent following agent patterns from profiles/default/agents/
    NEW_AGENT_CONTENT=$(create_new_agent "$AGENT_NAME" "$AGENT_PATTERNS" "$MERGED_KNOWLEDGE")
    echo "$NEW_AGENT_CONTENT" > "geist/agents/${AGENT_NAME}.md"
    echo "✅ Created new agent: geist/agents/${AGENT_NAME}.md"
done
```

Create or update agents:
- **Follow Agent Patterns**: Use patterns from profiles/default/agents/ as templates
- **Create New Agents**: Create new agents based on project-specific needs and patterns
  - Identify agents needed for project-specific patterns or processes
  - Create agents for project-specific domains or technologies
  - Include project-specific knowledge and patterns

- **Update Existing Agents**: Update existing agents with project-specific knowledge
  - Inject project-specific patterns into agent instructions
  - Update agent knowledge with project-specific standards and conventions
  - Include project-specific examples and patterns

### Step 5: Verify Standards, Workflows, and Agents are Referenced Correctly

Verify that specialized commands reference updated standards, workflows, and agents correctly:

```bash
# Check all specialized commands for correct references
SPECIALIZED_COMMANDS=(
    "geist/commands/shape-spec/shape-spec.md"
    "geist/commands/write-spec/write-spec.md"
    "geist/commands/create-tasks/create-tasks.md"
    "geist/commands/implement-tasks/implement-tasks.md"
    "geist/commands/orchestrate-tasks/orchestrate-tasks.md"
)

# Verify standards references
echo "🔍 Verifying standards references..."
for cmd_file in "${SPECIALIZED_COMMANDS[@]}"; do
    if [ -f "$cmd_file" ]; then
        # Check for standards references
        STANDARDS_REFS=$(grep -o '@geist/standards/[^}]*' "$cmd_file" || grep -o '{{standards/[^}]*}}' "$cmd_file" || true)
        
        for ref in $STANDARDS_REFS; do
            # Clean reference (remove @geist/ or {{}})
            CLEAN_REF=$(echo "$ref" | sed 's|@geist/standards/||' | sed 's|{{standards/||' | sed 's|}}||')
            
            # Check if standard file exists
            if [[ "$CLEAN_REF" == *"/*" ]]; then
                # Wildcard reference - check if directory exists
                STANDARD_DIR=$(echo "$CLEAN_REF" | sed 's|/*||')
                if [ ! -d "geist/standards/${STANDARD_DIR}" ]; then
                    echo "⚠️  Warning: Standards directory not found: geist/standards/${STANDARD_DIR} (referenced in $cmd_file)"
                fi
            else
                # Specific file reference
                if [ ! -f "geist/standards/${CLEAN_REF}.md" ] && [ ! -f "geist/standards/${CLEAN_REF}" ]; then
                    echo "⚠️  Warning: Standard file not found: geist/standards/${CLEAN_REF} (referenced in $cmd_file)"
                fi
            fi
        done
    fi
done

# Verify workflows references
echo "🔍 Verifying workflows references..."
for cmd_file in "${SPECIALIZED_COMMANDS[@]}"; do
    if [ -f "$cmd_file" ]; then
        # Check for workflow references
        WORKFLOW_REFS=$(grep -o '@geist/workflows/[^}]*' "$cmd_file" || grep -o '{{workflows/[^}]*}}' "$cmd_file" || true)
        
        for ref in $WORKFLOW_REFS; do
            # Clean reference (remove @geist/ or {{}})
            CLEAN_REF=$(echo "$ref" | sed 's|@geist/workflows/||' | sed 's|{{workflows/||' | sed 's|}}||' | sed 's|\.md||')
            
            # Check if workflow file exists
            if [ ! -f "geist/workflows/${CLEAN_REF}.md" ]; then
                echo "⚠️  Warning: Workflow file not found: geist/workflows/${CLEAN_REF}.md (referenced in $cmd_file)"
            fi
        done
    fi
done

# Verify agents references
echo "🔍 Verifying agents references..."
for cmd_file in "${SPECIALIZED_COMMANDS[@]}"; do
    if [ -f "$cmd_file" ]; then
        # Check for agent references
        AGENT_REFS=$(grep -o '@geist/agents/[^}]*' "$cmd_file" || grep -o '{{agents/[^}]*}}' "$cmd_file" || true)
        
        for ref in $AGENT_REFS; do
            # Clean reference (remove @geist/ or {{}})
            CLEAN_REF=$(echo "$ref" | sed 's|@geist/agents/||' | sed 's|{{agents/||' | sed 's|}}||' | sed 's|\.md||')
            
            # Check if agent file exists
            if [ ! -f "geist/agents/${CLEAN_REF}.md" ]; then
                echo "⚠️  Warning: Agent file not found: geist/agents/${CLEAN_REF}.md (referenced in $cmd_file)"
            fi
        done
    fi
done

echo "✅ Reference verification complete"
```

Verify references:
- **Standards References**: Ensure specialized commands reference updated standards correctly
  - Check all `@geist/standards/...` and `{{standards/...}}` references
  - Verify referenced standard files exist in geist/standards/
  - Report missing or incorrect references

- **Workflows References**: Ensure specialized commands reference updated workflows correctly
  - Check all `@geist/workflows/...` and `{{workflows/...}}` references
  - Verify referenced workflow files exist in geist/workflows/
  - Report missing or incorrect references

- **Agents References**: Ensure specialized commands reference updated agents correctly
  - Check all `@geist/agents/...` and `{{agents/...}}` references
  - Verify referenced agent files exist in geist/agents/
  - Report missing or incorrect references

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once standards, workflows, and agents are updated and verified, output the following message:

```
✅ Standards, workflows, and agents updated!

- Standards: Created/updated based on project-specific patterns from basepoints
- Workflows: Created/merged as needed for project structure
- Agents: Created/updated based on project-specific needs and patterns
- References: Verified all specialized commands reference updated standards, workflows, and agents correctly

Supporting structures are ready for use with specialized commands.

NEXT STEP 👉 Run the command, `8-specialize-standards.md`
```
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure that the updated standards, workflows, and agents align with the user's preferences and standards as detailed in the following files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}

## Important Constraints

- Must create new standards based on project-specific patterns extracted from basepoints
- Must update existing standards with project-specific conventions from merged knowledge
- Must create new workflows if project structure requires specialized workflows
- Must merge existing workflows if multiple basepoint patterns suggest workflow consolidation
- Must create new agents based on project-specific needs and patterns
- Must update existing agents with project-specific knowledge
- Must verify all references in specialized commands are correct
- Must follow patterns from profiles/default/standards/, profiles/default/workflows/, profiles/default/agents/
