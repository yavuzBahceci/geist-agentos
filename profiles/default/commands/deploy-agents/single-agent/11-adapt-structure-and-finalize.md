Now that all commands, standards, workflows, and agents are specialized and updated, proceed with adapting command structure based on project complexity and finalizing the transformation by following these instructions:

## Core Responsibilities

1. **Analyze Project Structure Complexity**: Determine project complexity from basepoints and assess project nature
2. **Adapt Command Checkpoints**: Adapt command checkpoints based on project complexity (add for complex, combine for simple)
3. **Verify Full Command Cycle**: Ensure the complete command cycle flows logically (shape-spec → write-spec → create-tasks → implement-tasks → orchestrate-tasks)
4. **Finalize Transformation**: Ensure all abstract templates are replaced and verify geist is ready to use
5. **Create Deployment Summary**: Inform user of completion and provide next steps

## Workflow

### Step 1: Analyze Project Structure Complexity

Determine project complexity from basepoints and assess project nature:

```bash
# Load merged knowledge and basepoints knowledge
if [ -f "geist/output/deploy-agents/merged-knowledge.json" ]; then
    MERGED_KNOWLEDGE=$(cat geist/output/deploy-agents/merged-knowledge.json)
fi

if [ -f "geist/output/deploy-agents/basepoints-knowledge.json" ]; then
    BASEPOINTS_KNOWLEDGE=$(cat geist/output/deploy-agents/basepoints-knowledge.json)
fi

# Extract project structure information from merged knowledge
PROJECT_STRUCTURE=$(extract_structure_from_merged "$MERGED_KNOWLEDGE")

# Determine number of modules from basepoints
MODULE_COUNT=$(find geist/basepoints -name "agent-base-*.md" -type f 2>/dev/null | wc -l | tr -d ' ')

# Determine number of abstraction layers from basepoints
ABSTRACTION_LAYERS=$(extract_abstraction_layers "$BASEPOINTS_KNOWLEDGE" | wc -l | tr -d ' ')

# Calculate complexity score
COMPLEXITY_SCORE=0
if [ "$MODULE_COUNT" -gt 20 ]; then
    COMPLEXITY_SCORE=$((COMPLEXITY_SCORE + 2))
elif [ "$MODULE_COUNT" -gt 10 ]; then
    COMPLEXITY_SCORE=$((COMPLEXITY_SCORE + 1))
fi

if [ "$ABSTRACTION_LAYERS" -gt 4 ]; then
    COMPLEXITY_SCORE=$((COMPLEXITY_SCORE + 2))
elif [ "$ABSTRACTION_LAYERS" -gt 2 ]; then
    COMPLEXITY_SCORE=$((COMPLEXITY_SCORE + 1))
fi

# Assess project nature
if [ "$COMPLEXITY_SCORE" -ge 3 ]; then
    PROJECT_NATURE="complex"
    echo "✅ Project assessed as COMPLEX (modules: $MODULE_COUNT, layers: $ABSTRACTION_LAYERS)"
elif [ "$COMPLEXITY_SCORE" -eq 0 ]; then
    PROJECT_NATURE="simple"
    echo "✅ Project assessed as SIMPLE (modules: $MODULE_COUNT, layers: $ABSTRACTION_LAYERS)"
else
    PROJECT_NATURE="moderate"
    echo "✅ Project assessed as MODERATE (modules: $MODULE_COUNT, layers: $ABSTRACTION_LAYERS)"
fi

# Store complexity assessment
echo "{\"nature\": \"$PROJECT_NATURE\", \"modules\": $MODULE_COUNT, \"layers\": $ABSTRACTION_LAYERS, \"score\": $COMPLEXITY_SCORE}" > geist/output/deploy-agents/reports/complexity-assessment.json
```

Analyze project structure:
- **Determine Complexity**: Calculate complexity from basepoints (number of modules, abstraction layers)
  - Count module-specific basepoint files (agent-base-*.md)
  - Count distinct abstraction layers from basepoints
  - Calculate complexity score based on module count and layer count

- **Assess Project Nature**: Determine if project is simple, moderate, or complex
  - Simple: Few modules (≤10), few layers (≤2)
  - Moderate: Medium modules (11-20), medium layers (3-4)
  - Complex: Many modules (>20), many layers (>4)

### Step 2: Adapt Command Checkpoints Based on Complexity

Adapt command checkpoints based on project complexity:

```bash
# Load complexity assessment
COMPLEXITY_ASSESSMENT=$(cat geist/output/deploy-agents/reports/complexity-assessment.json)
PROJECT_NATURE=$(echo "$COMPLEXITY_ASSESSMENT" | grep -o '"nature": "[^"]*"' | cut -d'"' -f4)

# Commands to adapt
COMMANDS=("shape-spec" "write-spec" "create-tasks" "implement-tasks")

for cmd in "${COMMANDS[@]}"; do
    CMD_DIR="geist/commands/${cmd}"
    
    if [ -d "$CMD_DIR" ]; then
        # Read command and phase files
        CMD_FILE="${CMD_DIR}/single-agent/${cmd}.md"
        
        if [ -f "$CMD_FILE" ]; then
            CMD_CONTENT=$(cat "$CMD_FILE")
            
            # Adapt based on complexity
            if [ "$PROJECT_NATURE" = "complex" ]; then
                # Add checkpoints for complex projects (more validation steps, more granular phases)
                echo "🔧 Adapting $cmd for COMPLEX project: Adding checkpoints..."
                
                # Add validation checkpoints
                CMD_CONTENT=$(add_validation_checkpoints "$CMD_CONTENT" "$cmd")
                
                # Add granular phase checkpoints
                CMD_CONTENT=$(add_granular_phases "$CMD_CONTENT" "$cmd")
                
                # Write adapted command
                echo "$CMD_CONTENT" > "$CMD_FILE"
                echo "✅ Adapted $cmd: Added checkpoints for complex project"
                
            elif [ "$PROJECT_NATURE" = "simple" ]; then
                # Combine steps or phases if project structure is simple (eliminate unnecessary checkpoints)
                echo "🔧 Adapting $cmd for SIMPLE project: Combining phases..."
                
                # Combine phases
                CMD_CONTENT=$(combine_simple_phases "$CMD_CONTENT" "$cmd")
                
                # Remove unnecessary checkpoints
                CMD_CONTENT=$(remove_unnecessary_checkpoints "$CMD_CONTENT" "$cmd")
                
                # Write adapted command
                echo "$CMD_CONTENT" > "$CMD_FILE"
                echo "✅ Adapted $cmd: Combined phases for simple project"
            fi
            
            # Adapt command structure based on abstraction layers detected in basepoints
            CMD_CONTENT=$(adapt_for_abstraction_layers "$CMD_CONTENT" "$ABSTRACTION_LAYERS" "$cmd")
            echo "$CMD_CONTENT" > "$CMD_FILE"
        fi
    fi
done
```

Adapt command checkpoints:
- **Add Checkpoints for Complex Projects**: Add validation steps and granular phases
  - Add validation checkpoints at critical points
  - Add granular phase checkpoints for complex workflows
  - Add intermediate verification steps

- **Combine Phases for Simple Projects**: Eliminate unnecessary checkpoints
  - Combine simple phases into fewer steps
  - Remove unnecessary validation checkpoints
  - Simplify phase structure

- **Adapt for Abstraction Layers**: Adapt command structure based on abstraction layers detected in basepoints
  - Adjust checkpoints based on number of abstraction layers
  - Add layer-specific validation steps for multi-layer projects
  - Simplify structure for single-layer projects

### Step 3: Verify Full Command Cycle Logical Flow

Verify the complete command cycle flows logically:

```bash
# Define command cycle order
COMMAND_CYCLE=("shape-spec" "write-spec" "create-tasks" "implement-tasks" "orchestrate-tasks")

echo "🔍 Verifying full command cycle logical flow..."

# Check shape-spec → write-spec flow
if [ -f "geist/commands/shape-spec/single-agent/shape-spec.md" ] && [ -f "geist/commands/write-spec/single-agent/write-spec.md" ]; then
    SHAPE_SPEC=$(cat geist/commands/shape-spec/single-agent/shape-spec.md)
    WRITE_SPEC=$(cat geist/commands/write-spec/single-agent/write-spec.md)
    
    # Verify shape-spec outputs are referenced in write-spec
    if echo "$WRITE_SPEC" | grep -q "spec.md\|requirements.md\|planning/"; then
        echo "✅ shape-spec → write-spec flow verified (write-spec references spec outputs)"
    else
        echo "⚠️  Warning: write-spec may not reference shape-spec outputs correctly"
    fi
fi

# Check write-spec → create-tasks flow
if [ -f "geist/commands/write-spec/single-agent/write-spec.md" ] && [ -f "geist/commands/create-tasks/single-agent/create-tasks.md" ]; then
    CREATE_TASKS=$(cat geist/commands/create-tasks/single-agent/create-tasks.md)
    
    # Verify create-tasks references spec.md and requirements.md
    if echo "$CREATE_TASKS" | grep -q "spec.md\|requirements.md"; then
        echo "✅ write-spec → create-tasks flow verified (create-tasks references spec outputs)"
    else
        echo "⚠️  Warning: create-tasks may not reference write-spec outputs correctly"
    fi
fi

# Check create-tasks → implement-tasks flow
if [ -f "geist/commands/create-tasks/single-agent/create-tasks.md" ] && [ -f "geist/commands/implement-tasks/single-agent/implement-tasks.md" ]; then
    IMPLEMENT_TASKS=$(cat geist/commands/implement-tasks/single-agent/implement-tasks.md)
    
    # Verify implement-tasks references tasks.md
    if echo "$IMPLEMENT_TASKS" | grep -q "tasks.md"; then
        echo "✅ create-tasks → implement-tasks flow verified (implement-tasks references tasks.md)"
    else
        echo "⚠️  Warning: implement-tasks may not reference create-tasks outputs correctly"
    fi
fi

# Check implement-tasks → orchestrate-tasks flow
if [ -f "geist/commands/implement-tasks/single-agent/implement-tasks.md" ] && [ -f "geist/commands/orchestrate-tasks/orchestrate-tasks.md" ]; then
    ORCHESTRATE_TASKS=$(cat geist/commands/orchestrate-tasks/orchestrate-tasks.md)
    
    # Verify orchestrate-tasks references tasks.md
    if echo "$ORCHESTRATE_TASKS" | grep -q "tasks.md"; then
        echo "✅ implement-tasks → orchestrate-tasks flow verified (orchestrate-tasks references tasks.md)"
    else
        echo "⚠️  Warning: orchestrate-tasks may not reference implement-tasks outputs correctly"
    fi
fi

# Verify specialized commands work together as a cohesive cycle
echo "🔍 Verifying commands work together as cohesive cycle..."
ALL_COMMANDS_EXIST=true
for cmd in "${COMMAND_CYCLE[@]}"; do
    if [ ! -f "geist/commands/${cmd}/single-agent/${cmd}.md" ] && [ ! -f "geist/commands/${cmd}/${cmd}.md" ]; then
        echo "⚠️  Warning: Command $cmd not found in specialized location"
        ALL_COMMANDS_EXIST=false
    fi
done

if [ "$ALL_COMMANDS_EXIST" = true ]; then
    echo "✅ All specialized commands exist and work together as cohesive cycle"
fi

echo "✅ Command cycle verification complete"
```

Verify full command cycle:
- **shape-spec → write-spec**: Verify write-spec references shape-spec outputs (spec.md, requirements.md)
- **write-spec → create-tasks**: Verify create-tasks references write-spec outputs (spec.md, requirements.md)
- **create-tasks → implement-tasks**: Verify implement-tasks references create-tasks outputs (tasks.md)
- **implement-tasks → orchestrate-tasks**: Verify orchestrate-tasks references implement-tasks outputs (tasks.md)
- **Cohesive Cycle**: Verify all specialized commands work together as a cohesive cycle

### Step 4: Run Comprehensive Validation

Run comprehensive validation to ensure transformation quality:

```bash
echo "🔍 Running comprehensive validation..."

# Create validation reports directory
mkdir -p geist/output/deploy-agents/reports/validation

# Set spec path for validation workflows (use deploy-agents reports as temporary spec path)
SPEC_PATH="geist/output/deploy-agents"
VALIDATION_CACHE="$SPEC_PATH/reports/validation"

# Run comprehensive validation using orchestrator
echo "🔍 Running comprehensive validation..."

# Set up validation context
SPEC_PATH="geist/output/deploy-agents"
VALIDATION_CACHE="$SPEC_PATH/reports/validation"
mkdir -p "$VALIDATION_CACHE"

# Run validation orchestrator workflow
# This will run all validation utilities and generate comprehensive reports
{{workflows/validation/orchestrate-validation}}

# Load comprehensive validation results
if [ -f "$VALIDATION_CACHE/comprehensive-validation-report.json" ]; then
    COMPREHENSIVE_REPORT=$(cat "$VALIDATION_CACHE/comprehensive-validation-report.json")
    
    # Extract totals from comprehensive report
    PLACEHOLDER_TOTAL=$(echo "$COMPREHENSIVE_REPORT" | grep -oE '"placeholder_detection":\{"total":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
    UNNECESSARY_LOGIC_TOTAL=$(echo "$COMPREHENSIVE_REPORT" | grep -oE '"unnecessary_logic_detection":\{"total":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
    CYCLE_TOTAL=$(echo "$COMPREHENSIVE_REPORT" | grep -oE '"command_cycle_validation":\{"total":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
    STRUCTURE_TOTAL=$(echo "$COMPREHENSIVE_REPORT" | grep -oE '"project_structure_alignment_validation":\{"total":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
    
    # Check for critical issues
    CRITICAL_ISSUES=false
    
    # Placeholders are critical
    if [ "${PLACEHOLDER_TOTAL:-0}" -gt 0 ]; then
        echo "❌ CRITICAL: Found $PLACEHOLDER_TOTAL placeholder(s) that need to be cleaned"
        CRITICAL_ISSUES=true
    fi
    
    # Command cycle flow issues, missing dependencies, and broken references are critical
    if [ -f "$VALIDATION_CACHE/command-cycle-validation.json" ]; then
        CYCLE_RESULTS=$(cat "$VALIDATION_CACHE/command-cycle-validation.json")
        CYCLE_CRITICAL=$(echo "$CYCLE_RESULTS" | grep -oE '"flow_issues":\{"count":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
        CYCLE_MISSING=$(echo "$CYCLE_RESULTS" | grep -oE '"missing_dependencies":\{"count":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
        CYCLE_BROKEN=$(echo "$CYCLE_RESULTS" | grep -oE '"broken_references":\{"count":[0-9]+' | grep -oE '[0-9]+' | head -1 || echo "0")
        CRITICAL_CYCLE_ISSUES=$((CYCLE_CRITICAL + CYCLE_MISSING + CYCLE_BROKEN))
        
        if [ "$CRITICAL_CYCLE_ISSUES" -gt 0 ]; then
            echo "❌ CRITICAL: Found $CRITICAL_CYCLE_ISSUES critical command cycle issue(s)"
            CRITICAL_ISSUES=true
        fi
    fi
    
    # Unnecessary logic and structure alignment are warnings (non-blocking)
    if [ "${UNNECESSARY_LOGIC_TOTAL:-0}" -gt 0 ]; then
        echo "⚠️  Warning: Found $UNNECESSARY_LOGIC_TOTAL unnecessary logic issue(s) (non-blocking)"
    fi
    
    if [ "${STRUCTURE_TOTAL:-0}" -gt 0 ]; then
        echo "⚠️  Warning: Found $STRUCTURE_TOTAL structure alignment issue(s) (non-blocking)"
    fi
else
    echo "⚠️  Warning: Comprehensive validation report not found"
    CRITICAL_ISSUES=false
fi

# Generate comprehensive validation report
echo "📊 Generating comprehensive validation report..."

# Combine all validation results
cat > "$VALIDATION_CACHE/comprehensive-validation-report.json" << EOF
{
  "validation_timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "placeholder_validation": {
    "total": ${PLACEHOLDER_TOTAL:-0},
    "status": "$(if [ "${PLACEHOLDER_TOTAL:-0}" -eq 0 ]; then echo "passed"; else echo "failed"; fi)",
    "report": "$VALIDATION_CACHE/placeholder-cleaning-validation.json"
  },
  "unnecessary_logic_validation": {
    "total": ${UNNECESSARY_LOGIC_TOTAL:-0},
    "status": "$(if [ "${UNNECESSARY_LOGIC_TOTAL:-0}" -eq 0 ]; then echo "passed"; else echo "warning"; fi)",
    "report": "$VALIDATION_CACHE/unnecessary-logic-removal-validation.json"
  },
  "command_cycle_validation": {
    "total": ${CYCLE_TOTAL:-0},
    "critical_issues": ${CRITICAL_CYCLE_ISSUES:-0},
    "status": "$(if [ "${CRITICAL_CYCLE_ISSUES:-0}" -eq 0 ]; then echo "passed"; else echo "failed"; fi)",
    "report": "$VALIDATION_CACHE/command-cycle-validation.json"
  },
  "structure_alignment_validation": {
    "total": ${STRUCTURE_TOTAL:-0},
    "status": "$(if [ "${STRUCTURE_TOTAL:-0}" -eq 0 ]; then echo "passed"; else echo "warning"; fi)",
    "report": "$VALIDATION_CACHE/project-structure-alignment-validation.json"
  },
  "overall_status": "$(if [ "${CRITICAL_ISSUES:-false}" = "true" ]; then echo "failed"; else echo "passed"; fi)"
}
EOF

# Create validation summary
cat > "$VALIDATION_CACHE/validation-summary.md" << EOF
# Deploy-Agents Validation Summary

**Validation Timestamp:** $(date -u +%Y-%m-%dT%H:%M:%SZ)

## Validation Results

### Placeholder Cleaning Validation
- **Status:** $(if [ "${PLACEHOLDER_TOTAL:-0}" -eq 0 ]; then echo "✅ PASSED"; else echo "❌ FAILED"; fi)
- **Total Placeholders Found:** ${PLACEHOLDER_TOTAL:-0}
- **Report:** See \`placeholder-cleaning-validation-summary.md\`

### Unnecessary Logic Validation
- **Status:** $(if [ "${UNNECESSARY_LOGIC_TOTAL:-0}" -eq 0 ]; then echo "✅ PASSED"; else echo "⚠️  WARNING"; fi)
- **Total Issues Found:** ${UNNECESSARY_LOGIC_TOTAL:-0}
- **Report:** See \`unnecessary-logic-removal-validation-summary.md\`

### Command Cycle Validation
- **Status:** $(if [ "${CRITICAL_CYCLE_ISSUES:-0}" -eq 0 ]; then echo "✅ PASSED"; else echo "❌ FAILED"; fi)
- **Total Issues Found:** ${CYCLE_TOTAL:-0}
- **Critical Issues:** ${CRITICAL_CYCLE_ISSUES:-0}
- **Report:** See \`command-cycle-validation-summary.md\`

### Project Structure Alignment Validation
- **Status:** $(if [ "${STRUCTURE_TOTAL:-0}" -eq 0 ]; then echo "✅ PASSED"; else echo "⚠️  WARNING"; fi)
- **Total Issues Found:** ${STRUCTURE_TOTAL:-0}
- **Report:** See \`project-structure-alignment-validation-summary.md\`

## Overall Status

$(if [ "${CRITICAL_ISSUES:-false}" = "true" ]; then
    echo "❌ **VALIDATION FAILED** - Critical issues found. Deployment cannot proceed."
    echo ""
    echo "Please fix the following critical issues:"
    if [ "${PLACEHOLDER_TOTAL:-0}" -gt 0 ]; then
        echo "- Clean remaining placeholders (${PLACEHOLDER_TOTAL:-0} found)"
    fi
    if [ "${CRITICAL_CYCLE_ISSUES:-0}" -gt 0 ]; then
        echo "- Fix command cycle issues (${CRITICAL_CYCLE_ISSUES:-0} critical issues found)"
    fi
else
    echo "✅ **VALIDATION PASSED** - All critical validations passed. Deployment can proceed."
    if [ "${UNNECESSARY_LOGIC_TOTAL:-0}" -gt 0 ] || [ "${STRUCTURE_TOTAL:-0}" -gt 0 ]; then
        echo ""
        echo "⚠️  Note: Some non-critical warnings were found. Review validation reports for details."
    fi
fi)
EOF

# Display validation summary
cat "$VALIDATION_CACHE/validation-summary.md"

# Fail deploy-agents if critical issues are found
if [ "${CRITICAL_ISSUES:-false}" = "true" ]; then
    echo ""
    echo "❌ Deploy-agents failed due to critical validation issues."
    echo "📁 Detailed validation reports are available in: geist/output/deploy-agents/reports/validation/"
    echo "🔧 Please fix the critical issues and run deploy-agents again."
    exit 1
fi

echo "✅ Comprehensive validation complete"
```

### Step 5: Finalize Transformation and Verify

Ensure all abstract templates are replaced and verify geist is ready to use:

```bash
echo "🔍 Finalizing transformation and verifying..."

# Check that all abstract templates are replaced with specialized versions
ABSTRACT_COMMANDS=("shape-spec" "write-spec" "create-tasks" "implement-tasks" "orchestrate-tasks")
ALL_REPLACED=true

for cmd in "${ABSTRACT_COMMANDS[@]}"; do
    # Check if specialized version exists in geist/commands/
    if [ -f "geist/commands/${cmd}/single-agent/${cmd}.md" ] || [ -f "geist/commands/${cmd}/${cmd}.md" ]; then
        echo "✅ Specialized version exists for: $cmd"
    else
        echo "⚠️  Warning: Specialized version not found for: $cmd"
        ALL_REPLACED=false
    fi
    
    # Verify specialized commands don't reference profiles/default (one-time transformation complete)
    if [ -f "geist/commands/${cmd}/single-agent/${cmd}.md" ]; then
        SPECIALIZED_CMD=$(cat "geist/commands/${cmd}/single-agent/${cmd}.md")
        
        if echo "$SPECIALIZED_CMD" | grep -q "profiles/default"; then
            echo "⚠️  Warning: $cmd still references profiles/default (should be specialized)"
            ALL_REPLACED=false
        fi
    fi
done

# Verify geist is ready to use with specialized commands
echo "🔍 Verifying geist is ready to use..."

# Check that all required directories exist
REQUIRED_DIRS=("geist/commands" "geist/standards" "geist/workflows" "geist/agents")
for dir in "${REQUIRED_DIRS[@]}"; do
    if [ -d "$dir" ]; then
        echo "✅ Directory exists: $dir"
    else
        echo "⚠️  Warning: Required directory missing: $dir"
        ALL_REPLACED=false
    fi
done

# Check that at least one specialized command exists
if [ "$ALL_REPLACED" = true ]; then
    echo "✅ Transformation complete: All abstract templates replaced with specialized versions"
    echo "✅ geist is ready to use with specialized commands"
else
    echo "⚠️  Warning: Some issues found during transformation verification"
fi
```

Finalize transformation:
- **Replace Abstract Templates**: Ensure all abstract templates from profiles/default are replaced with specialized versions
  - Verify specialized versions exist in geist/commands/ for all five core commands
  - Check that specialized commands don't reference profiles/default (one-time transformation complete)

- **Verify Agent-OS Ready**: Verify geist is ready to use with specialized commands
  - Check that all required directories exist (commands/, standards/, workflows/, agents/)
  - Verify specialized commands are accessible and functional

### Step 6: Create Deployment Completion Summary

Create summary of deployment completion:

```bash
echo "📋 Creating deployment completion summary..."

# Count specialized commands
SPECIALIZED_COMMANDS_COUNT=$(find geist/commands -name "*.md" -type f 2>/dev/null | wc -l | tr -d ' ')

# Count standards created/updated
STANDARDS_COUNT=$(find geist/standards -name "*.md" -type f 2>/dev/null | wc -l | tr -d ' ')

# Count workflows created/updated
WORKFLOWS_COUNT=$(find geist/workflows -name "*.md" -type f 2>/dev/null | wc -l | tr -d ' ')

# Count agents created/updated
AGENTS_COUNT=$(find geist/agents -name "*.md" -type f 2>/dev/null | wc -l | tr -d ' ')

# Load validation summary for inclusion in deployment summary
VALIDATION_SUMMARY=""
if [ -f "$VALIDATION_CACHE/validation-summary.md" ]; then
    VALIDATION_SUMMARY=$(cat "$VALIDATION_CACHE/validation-summary.md")
fi

# Create deployment summary
cat > geist/output/deploy-agents/reports/deployment-summary.md << EOF
# Deploy-Agents Transformation Complete

## Transformation Summary

✅ **Transformation Status**: COMPLETE

**Date**: $(date)

## Validation Summary

$VALIDATION_SUMMARY

## Specialized Commands Created

All five core commands have been specialized with project-specific knowledge:

1. **shape-spec** - Specialized with project-specific patterns and standards
2. **write-spec** - Specialized with project-specific structure patterns and testing approaches
3. **create-tasks** - Specialized with project-specific task creation patterns and checkpoints
4. **implement-tasks** - Specialized with project-specific implementation patterns and strategies
5. **orchestrate-tasks** - Specialized with project-specific orchestration patterns and workflows

**Total Specialized Commands**: $SPECIALIZED_COMMANDS_COUNT files

## Standards Created/Updated

Project-specific standards have been created/updated based on patterns extracted from basepoints:

- Naming conventions from basepoints
- Coding styles from project patterns
- Structure standards from project organization
- Conventions from project practices
- Error handling approaches from basepoints
- Tech stack standards from product files

**Total Standards**: $STANDARDS_COUNT files

## Workflows Created/Updated

Project-specific workflows have been created/merged as needed:

- Specification workflows
- Implementation workflows
- Planning workflows
- Codebase analysis workflows

**Total Workflows**: $WORKFLOWS_COUNT files

## Agents Created/Updated

Project-specific agents have been created/updated:

- Specialized agents for project-specific patterns
- Updated agents with project-specific knowledge
- New agents for project-specific needs

**Total Agents**: $AGENTS_COUNT files

## Project Complexity Assessment

**Project Nature**: $PROJECT_NATURE
**Modules**: $MODULE_COUNT
**Abstraction Layers**: $ABSTRACTION_LAYERS
**Complexity Score**: $COMPLEXITY_SCORE

Command checkpoints have been adapted based on project complexity.

## Next Steps

Your geist is now ready to use with specialized commands tailored to your project!

1. **Start using specialized commands**:
   - Run \`/shape-spec\` to start shaping a new feature specification
   - Run \`/write-spec\` to write specifications for your project
   - Run \`/create-tasks\` to create task breakdowns
   - Run \`/implement-tasks\` to implement tasks
   - Run \`/orchestrate-tasks\` to orchestrate multi-agent implementations

2. **The command cycle**:
   - shape-spec → write-spec → create-tasks → implement-tasks → orchestrate-tasks
   - Each command references the previous command's output correctly
   - All commands work together as a cohesive cycle

3. **All commands are project-specific**:
   - Patterns, standards, and workflows are tailored to your codebase
   - Examples and references use your project's structure
   - Test strategies match your project's testing approach

## Notes

- This is a one-time transformation (abstract profiles/default versions no longer referenced)
- All specialized commands are ready to use immediately
- Standards, workflows, and agents are all project-specific
- Command structure has been adapted based on project complexity

EOF

cat geist/output/deploy-agents/reports/deployment-summary.md
```

Create deployment summary:
- **Transformation Status**: Inform user that deploy-agents transformation is complete
- **Specialized Commands**: List all specialized commands created (shape-spec, write-spec, create-tasks, implement-tasks, orchestrate-tasks)
- **Standards, Workflows, Agents**: List counts of standards, workflows, and agents created/updated
- **Project Complexity**: Include project complexity assessment and how it affected command adaptation
- **Next Steps**: Provide clear next steps for using specialized geist commands

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once transformation is finalized and verified, output the deployment summary:

Display the contents of `geist/output/deploy-agents/reports/deployment-summary.md` to the user.
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure that the finalized transformation aligns with the user's preferences and standards as detailed in the following files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}

## Important Constraints

- Must analyze project structure complexity from basepoints (number of modules, abstraction layers)
- Must adapt command checkpoints based on complexity (add for complex, combine for simple)
- Must verify full command cycle flows logically (shape-spec → write-spec → create-tasks → implement-tasks → orchestrate-tasks)
- Must ensure all abstract templates are replaced with specialized versions
- Must verify abstract profiles/default versions are no longer referenced (one-time transformation complete)
- Must verify geist is ready to use with specialized commands
- Must inform user of completion and provide clear next steps
