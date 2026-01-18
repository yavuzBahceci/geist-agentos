Now that knowledge has been merged and conflicts resolved, proceed with specializing the shape-spec and write-spec commands by following these instructions:

## Core Responsibilities

1. **Read Abstract Command Templates**: Load shape-spec and write-spec templates from profiles/default
2. **Inject Project-Specific Knowledge**: Inject patterns, standards, flows, strategies, and testing approaches from merged knowledge
3. **Replace Abstract Placeholders**: Replace {{workflows/...}}
⚠️ This workflow file was not found in profiles/default/workflows/....md,  with project-specific content
4. **Replace Generic Examples**: Replace generic examples with project-specific patterns from basepoints
5. **Write Specialized Commands**: Write specialized commands to agent-os/commands/ (replace abstract versions)

## Workflow

### Step 1: Load Merged Knowledge

Load merged knowledge from previous phase:

```bash
# Load merged knowledge from cache
if [ -f "agent-os/output/deploy-agents/knowledge/merged-knowledge.json" ]; then
    MERGED_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/merged-knowledge.json)
    echo "✅ Loaded merged knowledge"
fi

# Load command-specific knowledge
if [ -f "agent-os/output/deploy-agents/knowledge/shape-spec-knowledge.json" ]; then
    SHAPE_SPEC_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/shape-spec-knowledge.json)
fi

if [ -f "agent-os/output/deploy-agents/knowledge/write-spec-knowledge.json" ]; then
    WRITE_SPEC_KNOWLEDGE=$(cat agent-os/output/deploy-agents/knowledge/write-spec-knowledge.json)
fi
```

### Step 2: Read Abstract shape-spec Command Template

Read abstract shape-spec command template and all its phase files:

```bash
# Read main shape-spec command
if [ -f "profiles/default/commands/shape-spec/shape-spec.md" ]; then
    SHAPE_SPEC_MAIN=$(cat profiles/default/commands/shape-spec/shape-spec.md)
fi

# Read single-agent version
if [ -f "profiles/default/commands/shape-spec/single-agent/shape-spec.md" ]; then
    SHAPE_SPEC_SINGLE=$(cat profiles/default/commands/shape-spec/single-agent/shape-spec.md)
fi

# Read all phase files
SHAPE_SPEC_PHASES=""
for phase_file in profiles/default/commands/shape-spec/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_NAME=$(basename "$phase_file")
        PHASE_CONTENT=$(cat "$phase_file")
        SHAPE_SPEC_PHASES="${SHAPE_SPEC_PHASES}\n\n=== $PHASE_NAME ===\n$PHASE_CONTENT"
    fi
done

echo "✅ Loaded abstract shape-spec command template and phase files"
```

### Step 3: Specialize shape-spec Command

Inject project-specific knowledge into shape-spec command:

```bash
# Start with abstract template
SPECIALIZED_SHAPE_SPEC="$SHAPE_SPEC_SINGLE"

# Inject project-specific patterns from basepoints
# Replace abstract patterns with project-specific patterns from merged knowledge
# Example: Replace generic "Repository pattern" with project-specific pattern name from basepoints
SPECIALIZED_SHAPE_SPEC=$(echo "$SPECIALIZED_SHAPE_SPEC" | \
    sed "s|generic pattern|$(extract_pattern_from_merged "$MERGED_KNOWLEDGE" "shape-spec")|g")

# Inject project-specific standards into shape-spec command
# Replace @agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md with project-specific standards references
# Replace with actual project-specific standards from basepoints
SPECIALIZED_SHAPE_SPEC=$(echo "$SPECIALIZED_SHAPE_SPEC" | \
    sed "s|@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md|$(generate_project_standards_refs "$MERGED_KNOWLEDGE")|g")

# Replace abstract placeholders ({{workflows/...}}
⚠️ This workflow file was not found in profiles/default/workflows/....md) with project-specific content
# Example: # Spec Research

## Core Responsibilities

1. **Read Initial Idea**: Load the raw idea from initialization.md
2. **Analyze Context**: Understand the codebase and existing patterns to inform questions
3. **Ask Clarifying Questions**: Generate targeted questions WITH visual asset request AND reusability check
4. **Process Answers**: Analyze responses and any provided visuals
5. **Ask Follow-ups**: Based on answers and visual analysis if needed
6. **Save Requirements**: Document the requirements you've gathered to a single file named: `[spec-path]/planning/requirements.md`

## Workflow

### Step 1: Read Initial Idea

Read the raw idea from `[spec-path]/planning/initialization.md` to understand what the user wants to build.

### Step 2: Analyze Context

Before generating questions, understand the codebase context:

1. **Load Basepoints Knowledge (if available)**: If basepoints exist and were extracted, load the extracted knowledge:
   ```bash
   # Determine spec path
   SPEC_PATH="[spec-path]"
   
   # Load extracted basepoints knowledge if available
   if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.json" ]; then
       EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.json")
       SCOPE_DETECTION=$(cat "$SPEC_PATH/implementation/cache/scope-detection/semantic-analysis.json" 2>/dev/null || echo "{}")
   fi
   ```

2. **Explore Existing Codebase**: Look for existing patterns, similar features, or related functionality that might inform this spec
3. **Identify Reusable Patterns**: Use extracted basepoints knowledge to identify reusable patterns, modules, and components
4. **Understand System Architecture**: Consider how this feature fits into the existing system architecture using basepoints knowledge

This context will help you:
- Ask more relevant and contextual questions informed by basepoints knowledge
- Identify existing features that might be reused or referenced from basepoints
- Ensure the feature aligns with existing patterns documented in basepoints
- Understand technical constraints and capabilities from basepoints
- Reference historical decisions and pros/cons from basepoints when relevant

### Step 3: Check for Trade-offs and Create Checkpoints (if needed)

Before generating questions, check if trade-offs need to be reviewed:

```bash
# Check for multiple valid patterns or conflicts
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

### Step 4: Generate First Round of Questions WITH Visual Request AND Reusability Check

Based on the initial idea, generate 4-8 targeted, NUMBERED questions that explore requirements while suggesting reasonable defaults.

**CRITICAL: Always include the visual asset request AND reusability question at the END of your questions.**

**Question generation guidelines (SDD-aligned):**
- Start each question with a number
- Propose sensible assumptions based on best practices and SDD principles
- Frame questions as "I'm assuming X, is that correct?"
- Make it easy for users to confirm or provide alternatives
- Include specific suggestions they can say yes/no to
- Always end with an open question about exclusions

**SDD-informed question patterns:**
- Ensure questions capture clear user stories in format: "As a [user], I want [action], so that [benefit]"
- Validate that acceptance criteria will be explicitly documented (not implied)
- Check for explicit scope boundaries (what's in-scope vs out-of-scope)
- Avoid questions that lead to premature technical details (SDD: focus on What & Why, not How in spec phase)
- Encourage minimal, intentionally scoped specs (prevent feature bloat)
- Help avoid SDD anti-patterns:
  - Specification theater: Ask questions that ensure specs will be actionable and referenced
  - Premature comprehensiveness: Ask questions that encourage incremental, focused specs
  - Over-engineering: Avoid questions that push toward excessive technical detail too early

**Required output format (SDD-aligned):**
```
Based on your idea for [spec name], I have some clarifying questions:

1. I assume [specific assumption]. Is that correct, or [alternative]?
2. I'm thinking [specific approach]. Should we [alternative]?
3. [Continue with numbered questions...]

**SDD Requirements Check:**
To ensure we create a well-structured specification (following spec-driven development best practices), I want to confirm:
- Will we capture user stories in the format "As a [user], I want [action], so that [benefit]"?
- Will we define clear acceptance criteria for each requirement?
- Should we explicitly define what's in-scope vs out-of-scope for this feature?

[Last numbered question about exclusions]

**Existing Code Reuse:**
Are there existing features in your codebase with similar patterns we should reference? For example:
- Similar interface elements or components to re-use
- Comparable patterns or structures
- Related logic or service objects
- Existing modules or classes with similar functionality

{{#if basepoints_knowledge_available}}
Based on basepoints analysis, I've identified these potentially reusable patterns:
- [Reusable patterns from basepoints]
- [Common modules that might be relevant]
- [Historical decisions that inform this feature]

Please provide file/folder paths or names of these features if they exist, or confirm if the basepoints suggestions are relevant.
{{/if}}

Please provide file/folder paths or names of these features if they exist.

**Visual Assets Request:**
Do you have any design mockups, wireframes, or screenshots that could help guide the development?

If yes, please place them in: `[spec-path]/planning/visuals/`

Use descriptive file names like:
- homepage-mockup.png
- dashboard-wireframe.jpg
- lofi-form-layout.png
- mobile-view.png
- existing-ui-screenshot.png

Please answer the questions above and let me know if you've added any visual files or can point to similar existing features.
```

**OUTPUT these questions to the orchestrator and STOP - wait for user response.**

### Step 5: Process Answers and MANDATORY Visual Check

After receiving user's answers from the orchestrator:

1. Store the user's answers for later documentation

2. **MANDATORY: Check for visual assets regardless of user's response:**

**CRITICAL**: You MUST run the following bash command even if the user says "no visuals" or doesn't mention visuals (Users often add files without mentioning them):

```bash
# List all files in visuals folder - THIS IS MANDATORY
ls -la [spec-path]/planning/visuals/ 2>/dev/null | grep -E '\.(png|jpg|jpeg|gif|svg|pdf)$' || echo "No visual files found"
```

3. IF visual files are found (bash command returns filenames):
   - Use Read tool to analyze EACH visual file found
   - Note key design elements, patterns, and user flows
   - Document observations for each file
   - Check filenames for low-fidelity indicators (lofi, lo-fi, wireframe, sketch, rough, etc.)

4. IF user provided paths or names of similar features:
   - Make note of these paths/names for spec-writer to reference
   - DO NOT explore them yourself (to save time), but DO document their names for future reference by the spec-writer.

### Step 6: Check for Checkpoints Before Big Changes (SDD-aligned)

After processing answers, check if any big changes or abstraction layer transitions are detected:

```bash
# Check for big changes or layer transitions
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

**SDD Checkpoint: Spec Completeness Before Task Decomposition (Conditional)**

As part of SDD best practices, validate spec completeness before proceeding to task decomposition:

```bash
# Conditionally check if spec completeness validation is needed
# Only trigger if it would add value and doesn't create unnecessary friction
SPEC_COMPLETE_CHECK_NEEDED="false"

# Check if spec has clear requirements, acceptance criteria, and scope boundaries
if [ -f "$SPEC_PATH/planning/requirements.md" ]; then
    # Check for user stories format
    HAS_USER_STORIES=$(grep -i "as a.*i want.*so that" "$SPEC_PATH/planning/requirements.md" | wc -l)
    
    # Check for acceptance criteria
    HAS_ACCEPTANCE_CRITERIA=$(grep -i "acceptance criteria\|acceptance criterion" "$SPEC_PATH/planning/requirements.md" | wc -l)
    
    # Check for scope boundaries
    HAS_SCOPE_BOUNDARIES=$(grep -i "in scope\|out of scope\|scope boundary" "$SPEC_PATH/planning/requirements.md" | wc -l)
    
    # Only trigger checkpoint if key SDD elements are missing AND it would be useful
    if [ "$HAS_USER_STORIES" -eq 0 ] || [ "$HAS_ACCEPTANCE_CRITERIA" -eq 0 ] || [ "$HAS_SCOPE_BOUNDARIES" -eq 0 ]; then
        SPEC_COMPLETE_CHECK_NEEDED="true"
    fi
fi

if [ "$SPEC_COMPLETE_CHECK_NEEDED" = "true" ]; then
    echo "🔍 SDD Checkpoint: Spec Completeness Validation"
    echo ""
    echo "Before proceeding to task decomposition (SDD phase order: Specify → Tasks),"
    echo "let's ensure the specification is complete with:"
    echo "  - Clear user stories"
    echo "  - Explicit acceptance criteria"
    echo "  - Defined scope boundaries (in-scope vs out-of-scope)"
    echo ""
    echo "Should we review and enhance the requirements before creating tasks?"
    echo "This ensures the 'Specify' phase is complete before the 'Tasks' phase (SDD best practice)."
    echo ""
    echo "Reply: [Yes/No/Proceed anyway]"
    
    # Wait for user confirmation (implementation will handle this in actual execution)
fi
```

**Note:** This checkpoint is conditional and only triggers when it would add meaningful value. It follows SDD principle: "Specify" phase should be complete before "Tasks" phase, ensuring spec is the source of truth.

### Step 7: Generate Follow-up Questions (if needed)

Determine if follow-up questions are needed based on:

**Visual-triggered follow-ups:**
- If visuals were found but user didn't mention them: "I found [filename(s)] in the visuals folder. Let me analyze these for the specification."
- If filenames contain "lofi", "lo-fi", "wireframe", "sketch", or "rough": "I notice you've provided [filename(s)] which appear to be wireframes/low-fidelity mockups. Should we treat these as layout and structure guides rather than exact design specifications, using our application's existing styling instead?"
- If visuals show features not discussed in answers
- If there are discrepancies between answers and visuals

**Reusability follow-ups:**
   - If user didn't provide similar features but the spec seems common: "This seems like it might share patterns with existing features. Could you point me to any similar functionality or logic in your codebase?"
- If provided paths seem incomplete you can ask something like: "You mentioned [feature]. Are there any related modules or logic we should also reference?"

**User's Answers-triggered follow-ups:**
- Vague requirements need clarification
- Missing technical details
- Unclear scope boundaries

**If follow-ups needed, OUTPUT to orchestrator:**
```
Based on your answers [and the visual files I found], I have a few follow-up questions:

1. [Specific follow-up question]
2. [Another follow-up if needed]

Please provide these additional details.
```

**Then STOP and wait for responses.**

### Step 8: Save Complete Requirements

After all questions are answered, record ALL gathered information to ONE FILE at this location with this name: `[spec-path]/planning/requirements.md`

Use the following structure and do not deviate from this structure when writing your gathered information to `requirements.md`.  Include ONLY the items specified in the following structure:

```markdown
# Spec Requirements: [Spec Name]

## Initial Description
[User's original spec description from initialization.md]

## Requirements Discussion

### First Round Questions

**Q1:** [First question asked]
**Answer:** [User's answer]

**Q2:** [Second question asked]
**Answer:** [User's answer]

[Continue for all questions]

### Existing Code to Reference
[Based on user's response about similar features]

**Similar Features Identified:**
- Feature: [Name] - Path: `[path provided by user]`
- Components or modules to potentially reuse: [user's description]
- Related logic to reference: [user's description]

[If user provided no similar features]
No similar existing features identified for reference.

### Follow-up Questions
[If any were asked]

**Follow-up 1:** [Question]
**Answer:** [User's answer]

## Visual Assets

### Files Provided:
[Based on actual bash check, not user statement]
- `filename.png`: [Description of what it shows from your analysis]
- `filename2.jpg`: [Key elements observed from your analysis]

### Visual Insights:
- [Design patterns identified]
- [User flow implications]
- [UI components shown]
- [Fidelity level: high-fidelity mockup / low-fidelity wireframe]

[If bash check found no files]
No visual assets provided.

## Requirements Summary

### Functional Requirements
- [Core functionality based on answers]
- [User actions enabled]
- [Data to be managed]

### Reusability Opportunities
- [Components or modules that might exist already based on user's input]
- [Code patterns to investigate]
- [Similar features to model after]

### Scope Boundaries
**In Scope:**
- [What will be built]

**Out of Scope:**
- [What won't be built]
- [Future enhancements mentioned]

### Technical Considerations
- [Integration points mentioned]
- [Existing system constraints]
- [Technical preferences or constraints stated]
- [Similar code patterns to follow]
```

### Step 9: Output Completion

Return to orchestrator:

```
Requirements research complete!

✅ Processed [X] clarifying questions
✅ Visual check performed: [Found and analyzed Y files / No files found]
✅ Reusability opportunities: [Identified Z similar features / None identified]
✅ Requirements documented comprehensively

Requirements saved to: `[spec-path]/planning/requirements.md`

Ready for specification creation.
```

## Important Constraints

- **MANDATORY**: Always run bash command to check visuals folder after receiving user answers
- DO NOT write technical specifications for development. Just record your findings from information gathering to this single file: `[spec-path]/planning/requirements.md`.
- Visual check is based on actual file(s) found via bash, NOT user statements
- Check filenames for low-fidelity indicators and clarify design intent if found
- Ask about existing similar features to promote code reuse
- Keep follow-ups minimal (1-3 questions max)
- Save user's exact answers, not interpretations
- Document all visual findings including fidelity level
- Document paths to similar features for spec-writer to reference
- OUTPUT questions and STOP to wait for orchestrator to relay responses

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Principles Integrated:**
- **Specification as Source of Truth**: Questions ensure specs are actionable and will be referenced
- **Minimal, Clear Specs**: Questions encourage intentional scoping and avoid feature bloat
- **SDD Phase Order**: Conditional checkpoint validates "Specify" phase is complete before "Tasks" phase

**SDD-Aware Question Generation:**
- Questions ensure user stories are captured in standard format
- Questions validate acceptance criteria will be explicit
- Questions check for explicit scope boundaries
- Questions avoid leading to premature technical details (What & Why, not How in spec phase)

**SDD Anti-Pattern Prevention:**
- Questions help avoid specification theater (specs that are written but never referenced)
- Questions prevent premature comprehensiveness (trying to spec everything upfront)
- Questions discourage over-engineering (excessive technical detail too early)

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD framework references are abstract (e.g., "task decomposition frameworks" not technology-specific tools)
- No hardcoded technology-specific SDD tool references in default templates
- Questions maintain technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

**Conditional SDD Checkpoints:**
- Spec completeness checkpoint only triggers when it would add value
- Checkpoints follow existing human-review workflow patterns
- Checkpoints don't create unnecessary friction
 -> project-specific research workflow
SPECIALIZED_SHAPE_SPEC=$(echo "$SPECIALIZED_SHAPE_SPEC" | \
    sed "s|# Spec Research

## Core Responsibilities

1. **Read Initial Idea**: Load the raw idea from initialization.md
2. **Analyze Context**: Understand the codebase and existing patterns to inform questions
3. **Ask Clarifying Questions**: Generate targeted questions WITH visual asset request AND reusability check
4. **Process Answers**: Analyze responses and any provided visuals
5. **Ask Follow-ups**: Based on answers and visual analysis if needed
6. **Save Requirements**: Document the requirements you've gathered to a single file named: `[spec-path]/planning/requirements.md`

## Workflow

### Step 1: Read Initial Idea

Read the raw idea from `[spec-path]/planning/initialization.md` to understand what the user wants to build.

### Step 2: Analyze Context

Before generating questions, understand the codebase context:

1. **Load Basepoints Knowledge (if available)**: If basepoints exist and were extracted, load the extracted knowledge:
   ```bash
   # Determine spec path
   SPEC_PATH="[spec-path]"
   
   # Load extracted basepoints knowledge if available
   if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.json" ]; then
       EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.json")
       SCOPE_DETECTION=$(cat "$SPEC_PATH/implementation/cache/scope-detection/semantic-analysis.json" 2>/dev/null || echo "{}")
   fi
   ```

2. **Explore Existing Codebase**: Look for existing patterns, similar features, or related functionality that might inform this spec
3. **Identify Reusable Patterns**: Use extracted basepoints knowledge to identify reusable patterns, modules, and components
4. **Understand System Architecture**: Consider how this feature fits into the existing system architecture using basepoints knowledge

This context will help you:
- Ask more relevant and contextual questions informed by basepoints knowledge
- Identify existing features that might be reused or referenced from basepoints
- Ensure the feature aligns with existing patterns documented in basepoints
- Understand technical constraints and capabilities from basepoints
- Reference historical decisions and pros/cons from basepoints when relevant

### Step 3: Check for Trade-offs and Create Checkpoints (if needed)

Before generating questions, check if trade-offs need to be reviewed:

```bash
# Check for multiple valid patterns or conflicts
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

### Step 4: Generate First Round of Questions WITH Visual Request AND Reusability Check

Based on the initial idea, generate 4-8 targeted, NUMBERED questions that explore requirements while suggesting reasonable defaults.

**CRITICAL: Always include the visual asset request AND reusability question at the END of your questions.**

**Question generation guidelines (SDD-aligned):**
- Start each question with a number
- Propose sensible assumptions based on best practices and SDD principles
- Frame questions as "I'm assuming X, is that correct?"
- Make it easy for users to confirm or provide alternatives
- Include specific suggestions they can say yes/no to
- Always end with an open question about exclusions

**SDD-informed question patterns:**
- Ensure questions capture clear user stories in format: "As a [user], I want [action], so that [benefit]"
- Validate that acceptance criteria will be explicitly documented (not implied)
- Check for explicit scope boundaries (what's in-scope vs out-of-scope)
- Avoid questions that lead to premature technical details (SDD: focus on What & Why, not How in spec phase)
- Encourage minimal, intentionally scoped specs (prevent feature bloat)
- Help avoid SDD anti-patterns:
  - Specification theater: Ask questions that ensure specs will be actionable and referenced
  - Premature comprehensiveness: Ask questions that encourage incremental, focused specs
  - Over-engineering: Avoid questions that push toward excessive technical detail too early

**Required output format (SDD-aligned):**
```
Based on your idea for [spec name], I have some clarifying questions:

1. I assume [specific assumption]. Is that correct, or [alternative]?
2. I'm thinking [specific approach]. Should we [alternative]?
3. [Continue with numbered questions...]

**SDD Requirements Check:**
To ensure we create a well-structured specification (following spec-driven development best practices), I want to confirm:
- Will we capture user stories in the format "As a [user], I want [action], so that [benefit]"?
- Will we define clear acceptance criteria for each requirement?
- Should we explicitly define what's in-scope vs out-of-scope for this feature?

[Last numbered question about exclusions]

**Existing Code Reuse:**
Are there existing features in your codebase with similar patterns we should reference? For example:
- Similar interface elements or components to re-use
- Comparable patterns or structures
- Related logic or service objects
- Existing modules or classes with similar functionality

{{#if basepoints_knowledge_available}}
Based on basepoints analysis, I've identified these potentially reusable patterns:
- [Reusable patterns from basepoints]
- [Common modules that might be relevant]
- [Historical decisions that inform this feature]

Please provide file/folder paths or names of these features if they exist, or confirm if the basepoints suggestions are relevant.
{{/if}}

Please provide file/folder paths or names of these features if they exist.

**Visual Assets Request:**
Do you have any design mockups, wireframes, or screenshots that could help guide the development?

If yes, please place them in: `[spec-path]/planning/visuals/`

Use descriptive file names like:
- homepage-mockup.png
- dashboard-wireframe.jpg
- lofi-form-layout.png
- mobile-view.png
- existing-ui-screenshot.png

Please answer the questions above and let me know if you've added any visual files or can point to similar existing features.
```

**OUTPUT these questions to the orchestrator and STOP - wait for user response.**

### Step 5: Process Answers and MANDATORY Visual Check

After receiving user's answers from the orchestrator:

1. Store the user's answers for later documentation

2. **MANDATORY: Check for visual assets regardless of user's response:**

**CRITICAL**: You MUST run the following bash command even if the user says "no visuals" or doesn't mention visuals (Users often add files without mentioning them):

```bash
# List all files in visuals folder - THIS IS MANDATORY
ls -la [spec-path]/planning/visuals/ 2>/dev/null | grep -E '\.(png|jpg|jpeg|gif|svg|pdf)$' || echo "No visual files found"
```

3. IF visual files are found (bash command returns filenames):
   - Use Read tool to analyze EACH visual file found
   - Note key design elements, patterns, and user flows
   - Document observations for each file
   - Check filenames for low-fidelity indicators (lofi, lo-fi, wireframe, sketch, rough, etc.)

4. IF user provided paths or names of similar features:
   - Make note of these paths/names for spec-writer to reference
   - DO NOT explore them yourself (to save time), but DO document their names for future reference by the spec-writer.

### Step 6: Check for Checkpoints Before Big Changes (SDD-aligned)

After processing answers, check if any big changes or abstraction layer transitions are detected:

```bash
# Check for big changes or layer transitions
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

**SDD Checkpoint: Spec Completeness Before Task Decomposition (Conditional)**

As part of SDD best practices, validate spec completeness before proceeding to task decomposition:

```bash
# Conditionally check if spec completeness validation is needed
# Only trigger if it would add value and doesn't create unnecessary friction
SPEC_COMPLETE_CHECK_NEEDED="false"

# Check if spec has clear requirements, acceptance criteria, and scope boundaries
if [ -f "$SPEC_PATH/planning/requirements.md" ]; then
    # Check for user stories format
    HAS_USER_STORIES=$(grep -i "as a.*i want.*so that" "$SPEC_PATH/planning/requirements.md" | wc -l)
    
    # Check for acceptance criteria
    HAS_ACCEPTANCE_CRITERIA=$(grep -i "acceptance criteria\|acceptance criterion" "$SPEC_PATH/planning/requirements.md" | wc -l)
    
    # Check for scope boundaries
    HAS_SCOPE_BOUNDARIES=$(grep -i "in scope\|out of scope\|scope boundary" "$SPEC_PATH/planning/requirements.md" | wc -l)
    
    # Only trigger checkpoint if key SDD elements are missing AND it would be useful
    if [ "$HAS_USER_STORIES" -eq 0 ] || [ "$HAS_ACCEPTANCE_CRITERIA" -eq 0 ] || [ "$HAS_SCOPE_BOUNDARIES" -eq 0 ]; then
        SPEC_COMPLETE_CHECK_NEEDED="true"
    fi
fi

if [ "$SPEC_COMPLETE_CHECK_NEEDED" = "true" ]; then
    echo "🔍 SDD Checkpoint: Spec Completeness Validation"
    echo ""
    echo "Before proceeding to task decomposition (SDD phase order: Specify → Tasks),"
    echo "let's ensure the specification is complete with:"
    echo "  - Clear user stories"
    echo "  - Explicit acceptance criteria"
    echo "  - Defined scope boundaries (in-scope vs out-of-scope)"
    echo ""
    echo "Should we review and enhance the requirements before creating tasks?"
    echo "This ensures the 'Specify' phase is complete before the 'Tasks' phase (SDD best practice)."
    echo ""
    echo "Reply: [Yes/No/Proceed anyway]"
    
    # Wait for user confirmation (implementation will handle this in actual execution)
fi
```

**Note:** This checkpoint is conditional and only triggers when it would add meaningful value. It follows SDD principle: "Specify" phase should be complete before "Tasks" phase, ensuring spec is the source of truth.

### Step 7: Generate Follow-up Questions (if needed)

Determine if follow-up questions are needed based on:

**Visual-triggered follow-ups:**
- If visuals were found but user didn't mention them: "I found [filename(s)] in the visuals folder. Let me analyze these for the specification."
- If filenames contain "lofi", "lo-fi", "wireframe", "sketch", or "rough": "I notice you've provided [filename(s)] which appear to be wireframes/low-fidelity mockups. Should we treat these as layout and structure guides rather than exact design specifications, using our application's existing styling instead?"
- If visuals show features not discussed in answers
- If there are discrepancies between answers and visuals

**Reusability follow-ups:**
   - If user didn't provide similar features but the spec seems common: "This seems like it might share patterns with existing features. Could you point me to any similar functionality or logic in your codebase?"
- If provided paths seem incomplete you can ask something like: "You mentioned [feature]. Are there any related modules or logic we should also reference?"

**User's Answers-triggered follow-ups:**
- Vague requirements need clarification
- Missing technical details
- Unclear scope boundaries

**If follow-ups needed, OUTPUT to orchestrator:**
```
Based on your answers [and the visual files I found], I have a few follow-up questions:

1. [Specific follow-up question]
2. [Another follow-up if needed]

Please provide these additional details.
```

**Then STOP and wait for responses.**

### Step 8: Save Complete Requirements

After all questions are answered, record ALL gathered information to ONE FILE at this location with this name: `[spec-path]/planning/requirements.md`

Use the following structure and do not deviate from this structure when writing your gathered information to `requirements.md`.  Include ONLY the items specified in the following structure:

```markdown
# Spec Requirements: [Spec Name]

## Initial Description
[User's original spec description from initialization.md]

## Requirements Discussion

### First Round Questions

**Q1:** [First question asked]
**Answer:** [User's answer]

**Q2:** [Second question asked]
**Answer:** [User's answer]

[Continue for all questions]

### Existing Code to Reference
[Based on user's response about similar features]

**Similar Features Identified:**
- Feature: [Name] - Path: `[path provided by user]`
- Components or modules to potentially reuse: [user's description]
- Related logic to reference: [user's description]

[If user provided no similar features]
No similar existing features identified for reference.

### Follow-up Questions
[If any were asked]

**Follow-up 1:** [Question]
**Answer:** [User's answer]

## Visual Assets

### Files Provided:
[Based on actual bash check, not user statement]
- `filename.png`: [Description of what it shows from your analysis]
- `filename2.jpg`: [Key elements observed from your analysis]

### Visual Insights:
- [Design patterns identified]
- [User flow implications]
- [UI components shown]
- [Fidelity level: high-fidelity mockup / low-fidelity wireframe]

[If bash check found no files]
No visual assets provided.

## Requirements Summary

### Functional Requirements
- [Core functionality based on answers]
- [User actions enabled]
- [Data to be managed]

### Reusability Opportunities
- [Components or modules that might exist already based on user's input]
- [Code patterns to investigate]
- [Similar features to model after]

### Scope Boundaries
**In Scope:**
- [What will be built]

**Out of Scope:**
- [What won't be built]
- [Future enhancements mentioned]

### Technical Considerations
- [Integration points mentioned]
- [Existing system constraints]
- [Technical preferences or constraints stated]
- [Similar code patterns to follow]
```

### Step 9: Output Completion

Return to orchestrator:

```
Requirements research complete!

✅ Processed [X] clarifying questions
✅ Visual check performed: [Found and analyzed Y files / No files found]
✅ Reusability opportunities: [Identified Z similar features / None identified]
✅ Requirements documented comprehensively

Requirements saved to: `[spec-path]/planning/requirements.md`

Ready for specification creation.
```

## Important Constraints

- **MANDATORY**: Always run bash command to check visuals folder after receiving user answers
- DO NOT write technical specifications for development. Just record your findings from information gathering to this single file: `[spec-path]/planning/requirements.md`.
- Visual check is based on actual file(s) found via bash, NOT user statements
- Check filenames for low-fidelity indicators and clarify design intent if found
- Ask about existing similar features to promote code reuse
- Keep follow-ups minimal (1-3 questions max)
- Save user's exact answers, not interpretations
- Document all visual findings including fidelity level
- Document paths to similar features for spec-writer to reference
- OUTPUT questions and STOP to wait for orchestrator to relay responses

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Principles Integrated:**
- **Specification as Source of Truth**: Questions ensure specs are actionable and will be referenced
- **Minimal, Clear Specs**: Questions encourage intentional scoping and avoid feature bloat
- **SDD Phase Order**: Conditional checkpoint validates "Specify" phase is complete before "Tasks" phase

**SDD-Aware Question Generation:**
- Questions ensure user stories are captured in standard format
- Questions validate acceptance criteria will be explicit
- Questions check for explicit scope boundaries
- Questions avoid leading to premature technical details (What & Why, not How in spec phase)

**SDD Anti-Pattern Prevention:**
- Questions help avoid specification theater (specs that are written but never referenced)
- Questions prevent premature comprehensiveness (trying to spec everything upfront)
- Questions discourage over-engineering (excessive technical detail too early)

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD framework references are abstract (e.g., "task decomposition frameworks" not technology-specific tools)
- No hardcoded technology-specific SDD tool references in default templates
- Questions maintain technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

**Conditional SDD Checkpoints:**
- Spec completeness checkpoint only triggers when it would add value
- Checkpoints follow existing human-review workflow patterns
- Checkpoints don't create unnecessary friction
|$(generate_project_workflow_ref "$MERGED_KNOWLEDGE" "research-spec")|g")

# Replace generic examples with project-specific patterns from basepoints
# Example: Replace "Example: User model" with actual project model names from basepoints
SPECIALIZED_SHAPE_SPEC=$(echo "$SPECIALIZED_SHAPE_SPEC" | \
    sed "s|Example: [A-Z][a-z]+ model|Example: $(extract_example_from_basepoints "$MERGED_KNOWLEDGE")|g")

# Inject project-specific patterns into shape-spec phases
for phase_file in profiles/default/commands/shape-spec/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_CONTENT=$(cat "$phase_file")
        
        # Inject project-specific patterns
        PHASE_CONTENT=$(inject_patterns_into_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Inject project-specific standards
        PHASE_CONTENT=$(inject_standards_into_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Replace abstract placeholders
        PHASE_CONTENT=$(replace_placeholders_in_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Replace generic examples
        PHASE_CONTENT=$(replace_examples_in_phase "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Specialize basepoints knowledge extraction workflows
        PHASE_CONTENT=$(specialize_basepoints_extraction_workflows "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Specialize scope detection workflows
        PHASE_CONTENT=$(specialize_scope_detection_workflows "$PHASE_CONTENT" "$MERGED_KNOWLEDGE")
        
        # Store specialized phase content
        SPECIALIZED_SHAPE_SPEC_PHASES="${SPECIALIZED_SHAPE_SPEC_PHASES}\n\n=== $(basename "$phase_file") ===\n$PHASE_CONTENT"
    fi
done
```

Specialize shape-spec command:
- **Inject Project-Specific Patterns**: Replace abstract patterns with project-specific patterns from basepoints
  - Extract patterns relevant to specification shaping from merged knowledge
  - Inject patterns into shape-spec phases (1-initialize-spec.md, 2-shape-spec.md)
  - Use project-specific pattern names and structures from basepoints

- **Inject Project-Specific Standards**: Replace abstract standards references with project-specific standards
  - Replace `@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md` with actual project-specific standards from basepoints
  - Reference project-specific naming conventions, coding styles, structure standards
  - Include project-specific standards in phase files

- **Replace Abstract Placeholders**: Replace workflow and standards placeholders with project-specific content
  - Replace `# Spec Research

## Core Responsibilities

1. **Read Initial Idea**: Load the raw idea from initialization.md
2. **Analyze Context**: Understand the codebase and existing patterns to inform questions
3. **Ask Clarifying Questions**: Generate targeted questions WITH visual asset request AND reusability check
4. **Process Answers**: Analyze responses and any provided visuals
5. **Ask Follow-ups**: Based on answers and visual analysis if needed
6. **Save Requirements**: Document the requirements you've gathered to a single file named: `[spec-path]/planning/requirements.md`

## Workflow

### Step 1: Read Initial Idea

Read the raw idea from `[spec-path]/planning/initialization.md` to understand what the user wants to build.

### Step 2: Analyze Context

Before generating questions, understand the codebase context:

1. **Load Basepoints Knowledge (if available)**: If basepoints exist and were extracted, load the extracted knowledge:
   ```bash
   # Determine spec path
   SPEC_PATH="[spec-path]"
   
   # Load extracted basepoints knowledge if available
   if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.json" ]; then
       EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.json")
       SCOPE_DETECTION=$(cat "$SPEC_PATH/implementation/cache/scope-detection/semantic-analysis.json" 2>/dev/null || echo "{}")
   fi
   ```

2. **Explore Existing Codebase**: Look for existing patterns, similar features, or related functionality that might inform this spec
3. **Identify Reusable Patterns**: Use extracted basepoints knowledge to identify reusable patterns, modules, and components
4. **Understand System Architecture**: Consider how this feature fits into the existing system architecture using basepoints knowledge

This context will help you:
- Ask more relevant and contextual questions informed by basepoints knowledge
- Identify existing features that might be reused or referenced from basepoints
- Ensure the feature aligns with existing patterns documented in basepoints
- Understand technical constraints and capabilities from basepoints
- Reference historical decisions and pros/cons from basepoints when relevant

### Step 3: Check for Trade-offs and Create Checkpoints (if needed)

Before generating questions, check if trade-offs need to be reviewed:

```bash
# Check for multiple valid patterns or conflicts
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

### Step 4: Generate First Round of Questions WITH Visual Request AND Reusability Check

Based on the initial idea, generate 4-8 targeted, NUMBERED questions that explore requirements while suggesting reasonable defaults.

**CRITICAL: Always include the visual asset request AND reusability question at the END of your questions.**

**Question generation guidelines (SDD-aligned):**
- Start each question with a number
- Propose sensible assumptions based on best practices and SDD principles
- Frame questions as "I'm assuming X, is that correct?"
- Make it easy for users to confirm or provide alternatives
- Include specific suggestions they can say yes/no to
- Always end with an open question about exclusions

**SDD-informed question patterns:**
- Ensure questions capture clear user stories in format: "As a [user], I want [action], so that [benefit]"
- Validate that acceptance criteria will be explicitly documented (not implied)
- Check for explicit scope boundaries (what's in-scope vs out-of-scope)
- Avoid questions that lead to premature technical details (SDD: focus on What & Why, not How in spec phase)
- Encourage minimal, intentionally scoped specs (prevent feature bloat)
- Help avoid SDD anti-patterns:
  - Specification theater: Ask questions that ensure specs will be actionable and referenced
  - Premature comprehensiveness: Ask questions that encourage incremental, focused specs
  - Over-engineering: Avoid questions that push toward excessive technical detail too early

**Required output format (SDD-aligned):**
```
Based on your idea for [spec name], I have some clarifying questions:

1. I assume [specific assumption]. Is that correct, or [alternative]?
2. I'm thinking [specific approach]. Should we [alternative]?
3. [Continue with numbered questions...]

**SDD Requirements Check:**
To ensure we create a well-structured specification (following spec-driven development best practices), I want to confirm:
- Will we capture user stories in the format "As a [user], I want [action], so that [benefit]"?
- Will we define clear acceptance criteria for each requirement?
- Should we explicitly define what's in-scope vs out-of-scope for this feature?

[Last numbered question about exclusions]

**Existing Code Reuse:**
Are there existing features in your codebase with similar patterns we should reference? For example:
- Similar interface elements or components to re-use
- Comparable patterns or structures
- Related logic or service objects
- Existing modules or classes with similar functionality

{{#if basepoints_knowledge_available}}
Based on basepoints analysis, I've identified these potentially reusable patterns:
- [Reusable patterns from basepoints]
- [Common modules that might be relevant]
- [Historical decisions that inform this feature]

Please provide file/folder paths or names of these features if they exist, or confirm if the basepoints suggestions are relevant.
{{/if}}

Please provide file/folder paths or names of these features if they exist.

**Visual Assets Request:**
Do you have any design mockups, wireframes, or screenshots that could help guide the development?

If yes, please place them in: `[spec-path]/planning/visuals/`

Use descriptive file names like:
- homepage-mockup.png
- dashboard-wireframe.jpg
- lofi-form-layout.png
- mobile-view.png
- existing-ui-screenshot.png

Please answer the questions above and let me know if you've added any visual files or can point to similar existing features.
```

**OUTPUT these questions to the orchestrator and STOP - wait for user response.**

### Step 5: Process Answers and MANDATORY Visual Check

After receiving user's answers from the orchestrator:

1. Store the user's answers for later documentation

2. **MANDATORY: Check for visual assets regardless of user's response:**

**CRITICAL**: You MUST run the following bash command even if the user says "no visuals" or doesn't mention visuals (Users often add files without mentioning them):

```bash
# List all files in visuals folder - THIS IS MANDATORY
ls -la [spec-path]/planning/visuals/ 2>/dev/null | grep -E '\.(png|jpg|jpeg|gif|svg|pdf)$' || echo "No visual files found"
```

3. IF visual files are found (bash command returns filenames):
   - Use Read tool to analyze EACH visual file found
   - Note key design elements, patterns, and user flows
   - Document observations for each file
   - Check filenames for low-fidelity indicators (lofi, lo-fi, wireframe, sketch, rough, etc.)

4. IF user provided paths or names of similar features:
   - Make note of these paths/names for spec-writer to reference
   - DO NOT explore them yourself (to save time), but DO document their names for future reference by the spec-writer.

### Step 6: Check for Checkpoints Before Big Changes (SDD-aligned)

After processing answers, check if any big changes or abstraction layer transitions are detected:

```bash
# Check for big changes or layer transitions
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

**SDD Checkpoint: Spec Completeness Before Task Decomposition (Conditional)**

As part of SDD best practices, validate spec completeness before proceeding to task decomposition:

```bash
# Conditionally check if spec completeness validation is needed
# Only trigger if it would add value and doesn't create unnecessary friction
SPEC_COMPLETE_CHECK_NEEDED="false"

# Check if spec has clear requirements, acceptance criteria, and scope boundaries
if [ -f "$SPEC_PATH/planning/requirements.md" ]; then
    # Check for user stories format
    HAS_USER_STORIES=$(grep -i "as a.*i want.*so that" "$SPEC_PATH/planning/requirements.md" | wc -l)
    
    # Check for acceptance criteria
    HAS_ACCEPTANCE_CRITERIA=$(grep -i "acceptance criteria\|acceptance criterion" "$SPEC_PATH/planning/requirements.md" | wc -l)
    
    # Check for scope boundaries
    HAS_SCOPE_BOUNDARIES=$(grep -i "in scope\|out of scope\|scope boundary" "$SPEC_PATH/planning/requirements.md" | wc -l)
    
    # Only trigger checkpoint if key SDD elements are missing AND it would be useful
    if [ "$HAS_USER_STORIES" -eq 0 ] || [ "$HAS_ACCEPTANCE_CRITERIA" -eq 0 ] || [ "$HAS_SCOPE_BOUNDARIES" -eq 0 ]; then
        SPEC_COMPLETE_CHECK_NEEDED="true"
    fi
fi

if [ "$SPEC_COMPLETE_CHECK_NEEDED" = "true" ]; then
    echo "🔍 SDD Checkpoint: Spec Completeness Validation"
    echo ""
    echo "Before proceeding to task decomposition (SDD phase order: Specify → Tasks),"
    echo "let's ensure the specification is complete with:"
    echo "  - Clear user stories"
    echo "  - Explicit acceptance criteria"
    echo "  - Defined scope boundaries (in-scope vs out-of-scope)"
    echo ""
    echo "Should we review and enhance the requirements before creating tasks?"
    echo "This ensures the 'Specify' phase is complete before the 'Tasks' phase (SDD best practice)."
    echo ""
    echo "Reply: [Yes/No/Proceed anyway]"
    
    # Wait for user confirmation (implementation will handle this in actual execution)
fi
```

**Note:** This checkpoint is conditional and only triggers when it would add meaningful value. It follows SDD principle: "Specify" phase should be complete before "Tasks" phase, ensuring spec is the source of truth.

### Step 7: Generate Follow-up Questions (if needed)

Determine if follow-up questions are needed based on:

**Visual-triggered follow-ups:**
- If visuals were found but user didn't mention them: "I found [filename(s)] in the visuals folder. Let me analyze these for the specification."
- If filenames contain "lofi", "lo-fi", "wireframe", "sketch", or "rough": "I notice you've provided [filename(s)] which appear to be wireframes/low-fidelity mockups. Should we treat these as layout and structure guides rather than exact design specifications, using our application's existing styling instead?"
- If visuals show features not discussed in answers
- If there are discrepancies between answers and visuals

**Reusability follow-ups:**
   - If user didn't provide similar features but the spec seems common: "This seems like it might share patterns with existing features. Could you point me to any similar functionality or logic in your codebase?"
- If provided paths seem incomplete you can ask something like: "You mentioned [feature]. Are there any related modules or logic we should also reference?"

**User's Answers-triggered follow-ups:**
- Vague requirements need clarification
- Missing technical details
- Unclear scope boundaries

**If follow-ups needed, OUTPUT to orchestrator:**
```
Based on your answers [and the visual files I found], I have a few follow-up questions:

1. [Specific follow-up question]
2. [Another follow-up if needed]

Please provide these additional details.
```

**Then STOP and wait for responses.**

### Step 8: Save Complete Requirements

After all questions are answered, record ALL gathered information to ONE FILE at this location with this name: `[spec-path]/planning/requirements.md`

Use the following structure and do not deviate from this structure when writing your gathered information to `requirements.md`.  Include ONLY the items specified in the following structure:

```markdown
# Spec Requirements: [Spec Name]

## Initial Description
[User's original spec description from initialization.md]

## Requirements Discussion

### First Round Questions

**Q1:** [First question asked]
**Answer:** [User's answer]

**Q2:** [Second question asked]
**Answer:** [User's answer]

[Continue for all questions]

### Existing Code to Reference
[Based on user's response about similar features]

**Similar Features Identified:**
- Feature: [Name] - Path: `[path provided by user]`
- Components or modules to potentially reuse: [user's description]
- Related logic to reference: [user's description]

[If user provided no similar features]
No similar existing features identified for reference.

### Follow-up Questions
[If any were asked]

**Follow-up 1:** [Question]
**Answer:** [User's answer]

## Visual Assets

### Files Provided:
[Based on actual bash check, not user statement]
- `filename.png`: [Description of what it shows from your analysis]
- `filename2.jpg`: [Key elements observed from your analysis]

### Visual Insights:
- [Design patterns identified]
- [User flow implications]
- [UI components shown]
- [Fidelity level: high-fidelity mockup / low-fidelity wireframe]

[If bash check found no files]
No visual assets provided.

## Requirements Summary

### Functional Requirements
- [Core functionality based on answers]
- [User actions enabled]
- [Data to be managed]

### Reusability Opportunities
- [Components or modules that might exist already based on user's input]
- [Code patterns to investigate]
- [Similar features to model after]

### Scope Boundaries
**In Scope:**
- [What will be built]

**Out of Scope:**
- [What won't be built]
- [Future enhancements mentioned]

### Technical Considerations
- [Integration points mentioned]
- [Existing system constraints]
- [Technical preferences or constraints stated]
- [Similar code patterns to follow]
```

### Step 9: Output Completion

Return to orchestrator:

```
Requirements research complete!

✅ Processed [X] clarifying questions
✅ Visual check performed: [Found and analyzed Y files / No files found]
✅ Reusability opportunities: [Identified Z similar features / None identified]
✅ Requirements documented comprehensively

Requirements saved to: `[spec-path]/planning/requirements.md`

Ready for specification creation.
```

## Important Constraints

- **MANDATORY**: Always run bash command to check visuals folder after receiving user answers
- DO NOT write technical specifications for development. Just record your findings from information gathering to this single file: `[spec-path]/planning/requirements.md`.
- Visual check is based on actual file(s) found via bash, NOT user statements
- Check filenames for low-fidelity indicators and clarify design intent if found
- Ask about existing similar features to promote code reuse
- Keep follow-ups minimal (1-3 questions max)
- Save user's exact answers, not interpretations
- Document all visual findings including fidelity level
- Document paths to similar features for spec-writer to reference
- OUTPUT questions and STOP to wait for orchestrator to relay responses

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Principles Integrated:**
- **Specification as Source of Truth**: Questions ensure specs are actionable and will be referenced
- **Minimal, Clear Specs**: Questions encourage intentional scoping and avoid feature bloat
- **SDD Phase Order**: Conditional checkpoint validates "Specify" phase is complete before "Tasks" phase

**SDD-Aware Question Generation:**
- Questions ensure user stories are captured in standard format
- Questions validate acceptance criteria will be explicit
- Questions check for explicit scope boundaries
- Questions avoid leading to premature technical details (What & Why, not How in spec phase)

**SDD Anti-Pattern Prevention:**
- Questions help avoid specification theater (specs that are written but never referenced)
- Questions prevent premature comprehensiveness (trying to spec everything upfront)
- Questions discourage over-engineering (excessive technical detail too early)

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD framework references are abstract (e.g., "task decomposition frameworks" not technology-specific tools)
- No hardcoded technology-specific SDD tool references in default templates
- Questions maintain technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

**Conditional SDD Checkpoints:**
- Spec completeness checkpoint only triggers when it would add value
- Checkpoints follow existing human-review workflow patterns
- Checkpoints don't create unnecessary friction
` with project-specific research workflow references
  - Replace `# Spec Initialization

## Core Responsibilities

1. **Get the description of the feature:** Receive it from the user
2. **Initialize Spec Structure**: Create the spec folder with date prefix
3. **Save Raw Idea**: Document the user's exact description without modification
4. **Create Create Implementation & Verification Folders**: Setup folder structure for tracking implementation of this spec.
5. **Prepare for Requirements**: Set up structure for next phase

## Workflow

### Step 1: Get the description of the feature

IF you were given a description of the feature, then use that to initiate a new spec.

OTHERWISE OUTPUT the following to user and WAIT for user's response:

```
Please provide a description of the feature you'd like to initiate a spec for.
```

**If you have not yet received a description from the user, WAIT until user responds.**

### Step 2: Initialize Spec Structure

Determine a kebab-case spec name from the user's description, then create the spec folder:

```bash
# Get today's date in YYYY-MM-DD format
TODAY=$(date +%Y-%m-%d)

# Determine kebab-case spec name from user's description
SPEC_NAME="[kebab-case-name]"

# Create dated folder name
DATED_SPEC_NAME="${TODAY}-${SPEC_NAME}"

# Store this path for output
SPEC_PATH="agent-os/specs/$DATED_SPEC_NAME"

# Create folder structure following architecture
mkdir -p $SPEC_PATH/planning
mkdir -p $SPEC_PATH/planning/visuals

echo "Created spec folder: $SPEC_PATH"
```

### Step 3: Create Implementation Folder

Create 2 folders:
- `$SPEC_PATH/implementation/`

Leave this folder empty, for now. Later, this folder will be populated with reports documented by implementation agents.

### Step 4: Output Confirmation

Return or output the following:

```
Spec folder initialized: `[spec-path]`

Structure created:
- planning/ - For requirements and specifications
- planning/visuals/ - For mockups and screenshots
- implementation/ - For implementation documentation

Ready for requirements research phase.
```

## Important Constraints

- Always use dated folder names (YYYY-MM-DD-spec-name)
- Pass the exact spec path back to the orchestrator
- Follow folder structure exactly
- Implementation folder should be empty, for now
` with project-specific initialization workflow references
  - Replace `` with actual project-specific standards content

- **Replace Generic Examples**: Replace generic examples with project-specific patterns from basepoints
  - Replace generic model names with actual project model names from basepoints
  - Replace generic component names with actual project component names
  - Use project-specific patterns and structures as examples

### Step 4: Read Abstract write-spec Command Template

Read abstract write-spec command template and referenced workflows:

```bash
# Read single-agent version
if [ -f "profiles/default/commands/write-spec/single-agent/write-spec.md" ]; then
    WRITE_SPEC_SINGLE=$(cat profiles/default/commands/write-spec/single-agent/write-spec.md)
fi

# Read workflows referenced by write-spec
WRITE_SPEC_WORKFLOWS=""
if [ -f "profiles/default/workflows/specification/write-spec.md" ]; then
    WRITE_SPEC_WORKFLOW_CONTENT=$(cat profiles/default/workflows/specification/write-spec.md)
    WRITE_SPEC_WORKFLOWS="${WRITE_SPEC_WORKFLOWS}\n\n=== write-spec workflow ===\n$WRITE_SPEC_WORKFLOW_CONTENT"
fi

echo "✅ Loaded abstract write-spec command template and workflows"
```

### Step 5: Specialize write-spec Command

Inject project-specific knowledge into write-spec command:

```bash
# Start with abstract template
SPECIALIZED_WRITE_SPEC="$WRITE_SPEC_SINGLE"

# Inject project-specific structure patterns from basepoints
# Replace abstract structure patterns with project-specific patterns from merged knowledge
# Example: Replace generic "component structure" with project-specific component structure from basepoints
SPECIALIZED_WRITE_SPEC=$(echo "$SPECIALIZED_WRITE_SPEC" | \
    sed "s|generic structure|$(extract_structure_from_merged "$MERGED_KNOWLEDGE" "write-spec")|g")

# Inject project-specific testing approaches from basepoints
# Replace abstract testing approaches with project-specific testing approaches
# Example: Replace generic "unit tests" with project-specific test patterns from basepoints
SPECIALIZED_WRITE_SPEC=$(echo "$SPECIALIZED_WRITE_SPEC" | \
    sed "s|generic testing|$(extract_testing_from_merged "$MERGED_KNOWLEDGE" "write-spec")|g")

# Replace abstract placeholders with project-specific content
# Example: # Spec Writing

## Core Responsibilities

1. **Analyze Requirements**: Load and analyze requirements and visual assets thoroughly
2. **Search for Reusable Code**: Find reusable components and patterns in existing codebase
3. **Create Specification**: Write comprehensive specification document

## Workflow

### Step 1: Analyze Requirements and Context

Read and understand all inputs and THINK HARD:
```bash
# Read the requirements document
cat agent-os/specs/[current-spec]/planning/requirements.md

# Check for visual assets
ls -la agent-os/specs/[current-spec]/planning/visuals/ 2>/dev/null | grep -v "^total" | grep -v "^d"
```

Parse and analyze:
- User's feature description and goals
- Requirements gathered by spec-shaper
- Visual mockups or screenshots (if present)
- Any constraints or out-of-scope items mentioned

### Step 2: Load Basepoints Knowledge and Search for Reusable Code

Before creating specifications, load basepoints knowledge and search the codebase for existing patterns and components that can be reused.

```bash
# Determine spec path
SPEC_PATH="agent-os/specs/[current-spec]"

# Load extracted basepoints knowledge if available
if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.json" ]; then
    EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.json")
    SCOPE_DETECTION=$(cat "$SPEC_PATH/implementation/cache/scope-detection/semantic-analysis.json" 2>/dev/null || echo "{}")
    KEYWORD_MATCHING=$(cat "$SPEC_PATH/implementation/cache/scope-detection/keyword-matching.json" 2>/dev/null || echo "{}")
fi
```

Based on the feature requirements and basepoints knowledge, identify relevant patterns:

**From Basepoints Knowledge (if available):**
- Reusable patterns extracted from basepoints
- Common modules that match your needs
- Related modules and dependencies from basepoints
- Standards, flows, and strategies relevant to this spec
- Testing approaches and strategies from basepoints
- Historical decisions and pros/cons that inform this feature

**From Codebase Search:**
- Similar features or functionality
- Existing components or modules that match your needs
- Related logic, services, or classes with similar functionality
- Interface patterns that could be extended
- Data structures or schemas that could be reused

Use appropriate search tools and commands for the project's technology stack to find:
- Components or modules that can be reused or extended
- Patterns to follow from similar features
- Naming conventions used in the codebase (from basepoints standards)
- Architecture patterns already established (from basepoints)

Document your findings for use in the specification, prioritizing basepoints knowledge when available.

### Step 3: Check if Deep Reading is Needed

Before creating the specification, check if deep implementation reading is needed:

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
- Inform specification writing with actual implementation patterns
- Suggest reusable code and modules from actual implementation
- Reference similar logic found in implementation
- Include refactoring opportunities in spec

### Step 4: Validate SDD Spec Completeness (SDD-aligned)

Before creating the specification, validate that requirements meet SDD completeness criteria:

```bash
# SDD Spec Completeness Validation
SPEC_PATH="agent-os/specs/[current-spec]"
REQUIREMENTS_FILE="$SPEC_PATH/planning/requirements.md"

if [ -f "$REQUIREMENTS_FILE" ]; then
    # Check for user stories format: "As a [user], I want [action], so that [benefit]"
    HAS_USER_STORIES=$(grep -iE "as a .*i want .*so that|as a .*i want .*so that|user story" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for acceptance criteria
    HAS_ACCEPTANCE_CRITERIA=$(grep -iE "acceptance criteria|acceptance criterion" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for scope boundaries (in-scope vs out-of-scope)
    HAS_SCOPE_BOUNDARIES=$(grep -iE "in scope|out of scope|scope boundary|scope:" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for clear requirements
    HAS_REQUIREMENTS=$(grep -iE "requirement|functional requirement|requirement:" "$REQUIREMENTS_FILE" | wc -l)
    
    # SDD Validation: Check completeness
    SDD_COMPLETE="true"
    MISSING_ELEMENTS=""
    
    if [ "$HAS_USER_STORIES" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}user stories format, "
    fi
    
    if [ "$HAS_ACCEPTANCE_CRITERIA" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}acceptance criteria, "
    fi
    
    if [ "$HAS_SCOPE_BOUNDARIES" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}scope boundaries, "
    fi
    
    if [ "$HAS_REQUIREMENTS" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}clear requirements, "
    fi
    
    if [ "$SDD_COMPLETE" = "false" ]; then
        echo "⚠️ SDD Spec Completeness Check: Missing elements detected"
        echo "Missing: ${MISSING_ELEMENTS%??}"
        echo "Please ensure the specification includes:"
        echo "  - User stories in format 'As a [user], I want [action], so that [benefit]'"
        echo "  - Explicit acceptance criteria"
        echo "  - Clear scope boundaries (in-scope vs out-of-scope)"
        echo "  - Clear requirements"
        echo ""
        echo "Should we proceed anyway or enhance requirements first?"
        # In actual execution, wait for user decision
    else
        echo "✅ SDD Spec Completeness Check: All required elements present"
    fi
fi
```

### Step 5: Check for SDD Anti-Patterns (SDD-aligned)

Before creating the specification, check for SDD anti-patterns:

```bash
# SDD Anti-Pattern Detection
if [ -f "$REQUIREMENTS_FILE" ]; then
    # Check for premature technical details (violates SDD "What & Why, not How" principle)
    PREMATURE_TECH=$(grep -iE "implementation details|code structure|database schema|api endpoints|class hierarchy|architecture diagram|tech stack|framework|library|npm package|import|require" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for over-specification (too many details)
    LINE_COUNT=$(wc -l < "$REQUIREMENTS_FILE")
    OVER_SPECIFIED="false"
    if [ "$LINE_COUNT" -gt 500 ]; then
        OVER_SPECIFIED="true"
    fi
    
    # Check for specification theater (requirements that are too vague)
    VAGUE_REQUIREMENTS=$(grep -iE "should|might|could|maybe|possibly|perhaps|kind of|sort of" "$REQUIREMENTS_FILE" | wc -l)
    
    # Detect anti-patterns
    ANTI_PATTERNS_FOUND="false"
    WARNINGS=""
    
    if [ "$PREMATURE_TECH" -gt 5 ]; then
        ANTI_PATTERNS_FOUND="true"
        WARNINGS="${WARNINGS}⚠️ Premature technical details detected (SDD: focus on What & Why, not How in spec phase). "
    fi
    
    if [ "$OVER_SPECIFIED" = "true" ]; then
        ANTI_PATTERNS_FOUND="true"
        WARNINGS="${WARNINGS}⚠️ Over-specification detected (SDD: minimal, intentionally scoped specs preferred). "
    fi
    
    if [ "$VAGUE_REQUIREMENTS" -gt 10 ]; then
        ANTI_PATTERNS_FOUND="true"
        WARNINGS="${WARNINGS}⚠️ Vague requirements detected (may indicate specification theater). "
    fi
    
    if [ "$ANTI_PATTERNS_FOUND" = "true" ]; then
        echo "🔍 SDD Anti-Pattern Detection: Issues found"
        echo "$WARNINGS"
        echo "Consider reviewing requirements to align with SDD best practices."
        # In actual execution, present to user for review
    fi
fi
```

### Step 6: Check for Trade-offs and Create Checkpoints (if needed)

Before creating the specification, check if trade-offs need to be reviewed:

```bash
# Check for multiple valid patterns or conflicts
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

Also check for big changes or abstraction layer transitions:

```bash
# Check for big changes or layer transitions
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

### Step 7: Create Core Specification (SDD-aligned)

Write the main specification to `agent-os/specs/[current-spec]/spec.md`.

DO NOT write actual code in the spec.md document. Just describe the requirements clearly and concisely.

**SDD Best Practices for Spec Content:**
- Focus on **What & Why**, not **How** (implementation details belong in task creation phase)
- Ensure user stories follow format: "As a [user], I want [action], so that [benefit]"
- Include explicit acceptance criteria for each requirement
- Define clear scope boundaries (in-scope vs out-of-scope)
- Keep specs minimal and intentionally scoped (avoid feature bloat)
- Avoid premature technical details (no implementation specifics, database schemas, API endpoints, etc.)

Keep it short and include only essential information for each section.

Follow this structure exactly when creating the content of `spec.md`:

```markdown
# Specification: [Feature Name]

## Goal
[1-2 sentences describing the core objective]

## User Stories
- As a [user type], I want to [action] so that [benefit]
- [repeat for up to 2 max additional user stories]

## Specific Requirements

**Specific requirement name**
- [Up to 8 CONCISE sub-bullet points to clarify specific sub-requirements, design or architectual decisions that go into this requirement, or the technical approach to take when implementing this requirement]

[repeat for up to a max of 10 specific requirements]

## Visual Design
[If mockups provided]

**`planning/visuals/[filename]`**
- [up to 8 CONCISE bullets describing specific UI elements found in this visual to address when building]

[repeat for each file in the `planning/visuals` folder]

## Existing Code to Leverage

**Code, component, or existing logic found**
- [up to 5 bullets that describe what this existing code does and how it should be re-used or replicated when building this spec]

[repeat for up to 5 existing code areas]

**Basepoints Knowledge to Leverage (if available)**
- [Reusable patterns from basepoints that should be used]
- [Common modules from basepoints that can be referenced]
- [Standards and flows from basepoints that apply to this spec]
- [Testing approaches from basepoints that should be followed]
- [Historical decisions from basepoints that inform this feature]

## Out of Scope
- [up to 10 concise descriptions of specific features that are out of scope and MUST NOT be built in this spec]
```

## Important Constraints

1. **Always search for reusable code** before specifying new components
2. **Reference visual assets** when available
3. **Do NOT write actual code** in the spec
4. **Keep each section short**, with clear, direct, skimmable specifications
5. **Do NOT deviate from the template above** and do not add additional sections

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Principles Integrated:**
- **Specification as Source of Truth**: Spec completeness validation ensures specs are actionable
- **SDD Phase Order**: Spec validation occurs before task creation (Specify → Tasks → Implement)
- **What & Why, not How**: Spec content focuses on requirements, not implementation details

**SDD Validation Methods:**
- **Spec Completeness Checks**: Validates user stories format, acceptance criteria, scope boundaries, clear requirements
- **SDD Structure Validation**: Ensures specs follow SDD best practices (What & Why, minimal scope)
- **Anti-Pattern Detection**: Detects and warns about:
  - Specification theater (vague requirements that are written but never referenced)
  - Premature comprehensiveness (over-specification, trying to spec everything upfront)
  - Over-engineering (premature technical details in spec phase)

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD validation checks are structure-based, not technology-specific in default templates
- No hardcoded technology-specific SDD tool references in default templates
- Validation maintains technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

**Workflow Steps Enhanced:**
- Step 4: Added SDD spec completeness validation
- Step 5: Added SDD anti-pattern detection
- Step 7: Enhanced spec creation guidance with SDD best practices (What & Why focus)
 -> project-specific write-spec workflow
SPECIALIZED_WRITE_SPEC=$(echo "$SPECIALIZED_WRITE_SPEC" | \
    sed "s|# Spec Writing

## Core Responsibilities

1. **Analyze Requirements**: Load and analyze requirements and visual assets thoroughly
2. **Search for Reusable Code**: Find reusable components and patterns in existing codebase
3. **Create Specification**: Write comprehensive specification document

## Workflow

### Step 1: Analyze Requirements and Context

Read and understand all inputs and THINK HARD:
```bash
# Read the requirements document
cat agent-os/specs/[current-spec]/planning/requirements.md

# Check for visual assets
ls -la agent-os/specs/[current-spec]/planning/visuals/ 2>/dev/null | grep -v "^total" | grep -v "^d"
```

Parse and analyze:
- User's feature description and goals
- Requirements gathered by spec-shaper
- Visual mockups or screenshots (if present)
- Any constraints or out-of-scope items mentioned

### Step 2: Load Basepoints Knowledge and Search for Reusable Code

Before creating specifications, load basepoints knowledge and search the codebase for existing patterns and components that can be reused.

```bash
# Determine spec path
SPEC_PATH="agent-os/specs/[current-spec]"

# Load extracted basepoints knowledge if available
if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.json" ]; then
    EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.json")
    SCOPE_DETECTION=$(cat "$SPEC_PATH/implementation/cache/scope-detection/semantic-analysis.json" 2>/dev/null || echo "{}")
    KEYWORD_MATCHING=$(cat "$SPEC_PATH/implementation/cache/scope-detection/keyword-matching.json" 2>/dev/null || echo "{}")
fi
```

Based on the feature requirements and basepoints knowledge, identify relevant patterns:

**From Basepoints Knowledge (if available):**
- Reusable patterns extracted from basepoints
- Common modules that match your needs
- Related modules and dependencies from basepoints
- Standards, flows, and strategies relevant to this spec
- Testing approaches and strategies from basepoints
- Historical decisions and pros/cons that inform this feature

**From Codebase Search:**
- Similar features or functionality
- Existing components or modules that match your needs
- Related logic, services, or classes with similar functionality
- Interface patterns that could be extended
- Data structures or schemas that could be reused

Use appropriate search tools and commands for the project's technology stack to find:
- Components or modules that can be reused or extended
- Patterns to follow from similar features
- Naming conventions used in the codebase (from basepoints standards)
- Architecture patterns already established (from basepoints)

Document your findings for use in the specification, prioritizing basepoints knowledge when available.

### Step 3: Check if Deep Reading is Needed

Before creating the specification, check if deep implementation reading is needed:

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
- Inform specification writing with actual implementation patterns
- Suggest reusable code and modules from actual implementation
- Reference similar logic found in implementation
- Include refactoring opportunities in spec

### Step 4: Validate SDD Spec Completeness (SDD-aligned)

Before creating the specification, validate that requirements meet SDD completeness criteria:

```bash
# SDD Spec Completeness Validation
SPEC_PATH="agent-os/specs/[current-spec]"
REQUIREMENTS_FILE="$SPEC_PATH/planning/requirements.md"

if [ -f "$REQUIREMENTS_FILE" ]; then
    # Check for user stories format: "As a [user], I want [action], so that [benefit]"
    HAS_USER_STORIES=$(grep -iE "as a .*i want .*so that|as a .*i want .*so that|user story" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for acceptance criteria
    HAS_ACCEPTANCE_CRITERIA=$(grep -iE "acceptance criteria|acceptance criterion" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for scope boundaries (in-scope vs out-of-scope)
    HAS_SCOPE_BOUNDARIES=$(grep -iE "in scope|out of scope|scope boundary|scope:" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for clear requirements
    HAS_REQUIREMENTS=$(grep -iE "requirement|functional requirement|requirement:" "$REQUIREMENTS_FILE" | wc -l)
    
    # SDD Validation: Check completeness
    SDD_COMPLETE="true"
    MISSING_ELEMENTS=""
    
    if [ "$HAS_USER_STORIES" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}user stories format, "
    fi
    
    if [ "$HAS_ACCEPTANCE_CRITERIA" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}acceptance criteria, "
    fi
    
    if [ "$HAS_SCOPE_BOUNDARIES" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}scope boundaries, "
    fi
    
    if [ "$HAS_REQUIREMENTS" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}clear requirements, "
    fi
    
    if [ "$SDD_COMPLETE" = "false" ]; then
        echo "⚠️ SDD Spec Completeness Check: Missing elements detected"
        echo "Missing: ${MISSING_ELEMENTS%??}"
        echo "Please ensure the specification includes:"
        echo "  - User stories in format 'As a [user], I want [action], so that [benefit]'"
        echo "  - Explicit acceptance criteria"
        echo "  - Clear scope boundaries (in-scope vs out-of-scope)"
        echo "  - Clear requirements"
        echo ""
        echo "Should we proceed anyway or enhance requirements first?"
        # In actual execution, wait for user decision
    else
        echo "✅ SDD Spec Completeness Check: All required elements present"
    fi
fi
```

### Step 5: Check for SDD Anti-Patterns (SDD-aligned)

Before creating the specification, check for SDD anti-patterns:

```bash
# SDD Anti-Pattern Detection
if [ -f "$REQUIREMENTS_FILE" ]; then
    # Check for premature technical details (violates SDD "What & Why, not How" principle)
    PREMATURE_TECH=$(grep -iE "implementation details|code structure|database schema|api endpoints|class hierarchy|architecture diagram|tech stack|framework|library|npm package|import|require" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for over-specification (too many details)
    LINE_COUNT=$(wc -l < "$REQUIREMENTS_FILE")
    OVER_SPECIFIED="false"
    if [ "$LINE_COUNT" -gt 500 ]; then
        OVER_SPECIFIED="true"
    fi
    
    # Check for specification theater (requirements that are too vague)
    VAGUE_REQUIREMENTS=$(grep -iE "should|might|could|maybe|possibly|perhaps|kind of|sort of" "$REQUIREMENTS_FILE" | wc -l)
    
    # Detect anti-patterns
    ANTI_PATTERNS_FOUND="false"
    WARNINGS=""
    
    if [ "$PREMATURE_TECH" -gt 5 ]; then
        ANTI_PATTERNS_FOUND="true"
        WARNINGS="${WARNINGS}⚠️ Premature technical details detected (SDD: focus on What & Why, not How in spec phase). "
    fi
    
    if [ "$OVER_SPECIFIED" = "true" ]; then
        ANTI_PATTERNS_FOUND="true"
        WARNINGS="${WARNINGS}⚠️ Over-specification detected (SDD: minimal, intentionally scoped specs preferred). "
    fi
    
    if [ "$VAGUE_REQUIREMENTS" -gt 10 ]; then
        ANTI_PATTERNS_FOUND="true"
        WARNINGS="${WARNINGS}⚠️ Vague requirements detected (may indicate specification theater). "
    fi
    
    if [ "$ANTI_PATTERNS_FOUND" = "true" ]; then
        echo "🔍 SDD Anti-Pattern Detection: Issues found"
        echo "$WARNINGS"
        echo "Consider reviewing requirements to align with SDD best practices."
        # In actual execution, present to user for review
    fi
fi
```

### Step 6: Check for Trade-offs and Create Checkpoints (if needed)

Before creating the specification, check if trade-offs need to be reviewed:

```bash
# Check for multiple valid patterns or conflicts
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

Also check for big changes or abstraction layer transitions:

```bash
# Check for big changes or layer transitions
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

### Step 7: Create Core Specification (SDD-aligned)

Write the main specification to `agent-os/specs/[current-spec]/spec.md`.

DO NOT write actual code in the spec.md document. Just describe the requirements clearly and concisely.

**SDD Best Practices for Spec Content:**
- Focus on **What & Why**, not **How** (implementation details belong in task creation phase)
- Ensure user stories follow format: "As a [user], I want [action], so that [benefit]"
- Include explicit acceptance criteria for each requirement
- Define clear scope boundaries (in-scope vs out-of-scope)
- Keep specs minimal and intentionally scoped (avoid feature bloat)
- Avoid premature technical details (no implementation specifics, database schemas, API endpoints, etc.)

Keep it short and include only essential information for each section.

Follow this structure exactly when creating the content of `spec.md`:

```markdown
# Specification: [Feature Name]

## Goal
[1-2 sentences describing the core objective]

## User Stories
- As a [user type], I want to [action] so that [benefit]
- [repeat for up to 2 max additional user stories]

## Specific Requirements

**Specific requirement name**
- [Up to 8 CONCISE sub-bullet points to clarify specific sub-requirements, design or architectual decisions that go into this requirement, or the technical approach to take when implementing this requirement]

[repeat for up to a max of 10 specific requirements]

## Visual Design
[If mockups provided]

**`planning/visuals/[filename]`**
- [up to 8 CONCISE bullets describing specific UI elements found in this visual to address when building]

[repeat for each file in the `planning/visuals` folder]

## Existing Code to Leverage

**Code, component, or existing logic found**
- [up to 5 bullets that describe what this existing code does and how it should be re-used or replicated when building this spec]

[repeat for up to 5 existing code areas]

**Basepoints Knowledge to Leverage (if available)**
- [Reusable patterns from basepoints that should be used]
- [Common modules from basepoints that can be referenced]
- [Standards and flows from basepoints that apply to this spec]
- [Testing approaches from basepoints that should be followed]
- [Historical decisions from basepoints that inform this feature]

## Out of Scope
- [up to 10 concise descriptions of specific features that are out of scope and MUST NOT be built in this spec]
```

## Important Constraints

1. **Always search for reusable code** before specifying new components
2. **Reference visual assets** when available
3. **Do NOT write actual code** in the spec
4. **Keep each section short**, with clear, direct, skimmable specifications
5. **Do NOT deviate from the template above** and do not add additional sections

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Principles Integrated:**
- **Specification as Source of Truth**: Spec completeness validation ensures specs are actionable
- **SDD Phase Order**: Spec validation occurs before task creation (Specify → Tasks → Implement)
- **What & Why, not How**: Spec content focuses on requirements, not implementation details

**SDD Validation Methods:**
- **Spec Completeness Checks**: Validates user stories format, acceptance criteria, scope boundaries, clear requirements
- **SDD Structure Validation**: Ensures specs follow SDD best practices (What & Why, minimal scope)
- **Anti-Pattern Detection**: Detects and warns about:
  - Specification theater (vague requirements that are written but never referenced)
  - Premature comprehensiveness (over-specification, trying to spec everything upfront)
  - Over-engineering (premature technical details in spec phase)

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD validation checks are structure-based, not technology-specific in default templates
- No hardcoded technology-specific SDD tool references in default templates
- Validation maintains technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

**Workflow Steps Enhanced:**
- Step 4: Added SDD spec completeness validation
- Step 5: Added SDD anti-pattern detection
- Step 7: Enhanced spec creation guidance with SDD best practices (What & Why focus)
|$(generate_project_workflow_content "$MERGED_KNOWLEDGE" "write-spec")|g")

# Replace  with project-specific standards
SPECIALIZED_WRITE_SPEC=$(echo "$SPECIALIZED_WRITE_SPEC" | \
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

# Reference project-specific test strategies from basepoints
# Add project-specific test strategy references based on basepoints knowledge
TEST_STRATEGIES=$(extract_test_strategies_from_basepoints "$MERGED_KNOWLEDGE")
SPECIALIZED_WRITE_SPEC="${SPECIALIZED_WRITE_SPEC}\n\n## Project-Specific Test Strategies\n${TEST_STRATEGIES}"

# Specialize the write-spec workflow itself
SPECIALIZED_WRITE_SPEC_WORKFLOW="$WRITE_SPEC_WORKFLOW_CONTENT"

# Inject project-specific structure patterns into workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(inject_structure_into_workflow "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Inject project-specific testing approaches into workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(inject_testing_into_workflow "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Replace abstract placeholders in workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(replace_placeholders_in_workflow "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize basepoints knowledge extraction workflows in write-spec workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(specialize_basepoints_extraction_workflows "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize scope detection workflows in write-spec workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(specialize_scope_detection_workflows "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

# Specialize deep reading workflows in write-spec workflow
SPECIALIZED_WRITE_SPEC_WORKFLOW=$(specialize_deep_reading_workflows "$SPECIALIZED_WRITE_SPEC_WORKFLOW" "$MERGED_KNOWLEDGE")

Specialize write-spec command:
- **Inject Project-Specific Structure Patterns**: Replace abstract structure patterns with project-specific patterns from basepoints
  - Extract structure patterns relevant to specification writing from merged knowledge
  - Inject project-specific folder structures, component structures, module structures
  - Use project-specific architecture patterns from basepoints

- **Inject Project-Specific Testing Approaches**: Replace abstract testing approaches with project-specific testing approaches
  - Extract test patterns, strategies, and organization from merged knowledge
  - Inject project-specific test file structures, test naming conventions
  - Reference project-specific testing tools and frameworks from product tech-stack

- **Replace Abstract Placeholders**: Replace workflow and standards placeholders with project-specific content
  - Replace `# Spec Writing

## Core Responsibilities

1. **Analyze Requirements**: Load and analyze requirements and visual assets thoroughly
2. **Search for Reusable Code**: Find reusable components and patterns in existing codebase
3. **Create Specification**: Write comprehensive specification document

## Workflow

### Step 1: Analyze Requirements and Context

Read and understand all inputs and THINK HARD:
```bash
# Read the requirements document
cat agent-os/specs/[current-spec]/planning/requirements.md

# Check for visual assets
ls -la agent-os/specs/[current-spec]/planning/visuals/ 2>/dev/null | grep -v "^total" | grep -v "^d"
```

Parse and analyze:
- User's feature description and goals
- Requirements gathered by spec-shaper
- Visual mockups or screenshots (if present)
- Any constraints or out-of-scope items mentioned

### Step 2: Load Basepoints Knowledge and Search for Reusable Code

Before creating specifications, load basepoints knowledge and search the codebase for existing patterns and components that can be reused.

```bash
# Determine spec path
SPEC_PATH="agent-os/specs/[current-spec]"

# Load extracted basepoints knowledge if available
if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.json" ]; then
    EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.json")
    SCOPE_DETECTION=$(cat "$SPEC_PATH/implementation/cache/scope-detection/semantic-analysis.json" 2>/dev/null || echo "{}")
    KEYWORD_MATCHING=$(cat "$SPEC_PATH/implementation/cache/scope-detection/keyword-matching.json" 2>/dev/null || echo "{}")
fi
```

Based on the feature requirements and basepoints knowledge, identify relevant patterns:

**From Basepoints Knowledge (if available):**
- Reusable patterns extracted from basepoints
- Common modules that match your needs
- Related modules and dependencies from basepoints
- Standards, flows, and strategies relevant to this spec
- Testing approaches and strategies from basepoints
- Historical decisions and pros/cons that inform this feature

**From Codebase Search:**
- Similar features or functionality
- Existing components or modules that match your needs
- Related logic, services, or classes with similar functionality
- Interface patterns that could be extended
- Data structures or schemas that could be reused

Use appropriate search tools and commands for the project's technology stack to find:
- Components or modules that can be reused or extended
- Patterns to follow from similar features
- Naming conventions used in the codebase (from basepoints standards)
- Architecture patterns already established (from basepoints)

Document your findings for use in the specification, prioritizing basepoints knowledge when available.

### Step 3: Check if Deep Reading is Needed

Before creating the specification, check if deep implementation reading is needed:

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
- Inform specification writing with actual implementation patterns
- Suggest reusable code and modules from actual implementation
- Reference similar logic found in implementation
- Include refactoring opportunities in spec

### Step 4: Validate SDD Spec Completeness (SDD-aligned)

Before creating the specification, validate that requirements meet SDD completeness criteria:

```bash
# SDD Spec Completeness Validation
SPEC_PATH="agent-os/specs/[current-spec]"
REQUIREMENTS_FILE="$SPEC_PATH/planning/requirements.md"

if [ -f "$REQUIREMENTS_FILE" ]; then
    # Check for user stories format: "As a [user], I want [action], so that [benefit]"
    HAS_USER_STORIES=$(grep -iE "as a .*i want .*so that|as a .*i want .*so that|user story" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for acceptance criteria
    HAS_ACCEPTANCE_CRITERIA=$(grep -iE "acceptance criteria|acceptance criterion" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for scope boundaries (in-scope vs out-of-scope)
    HAS_SCOPE_BOUNDARIES=$(grep -iE "in scope|out of scope|scope boundary|scope:" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for clear requirements
    HAS_REQUIREMENTS=$(grep -iE "requirement|functional requirement|requirement:" "$REQUIREMENTS_FILE" | wc -l)
    
    # SDD Validation: Check completeness
    SDD_COMPLETE="true"
    MISSING_ELEMENTS=""
    
    if [ "$HAS_USER_STORIES" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}user stories format, "
    fi
    
    if [ "$HAS_ACCEPTANCE_CRITERIA" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}acceptance criteria, "
    fi
    
    if [ "$HAS_SCOPE_BOUNDARIES" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}scope boundaries, "
    fi
    
    if [ "$HAS_REQUIREMENTS" -eq 0 ]; then
        SDD_COMPLETE="false"
        MISSING_ELEMENTS="${MISSING_ELEMENTS}clear requirements, "
    fi
    
    if [ "$SDD_COMPLETE" = "false" ]; then
        echo "⚠️ SDD Spec Completeness Check: Missing elements detected"
        echo "Missing: ${MISSING_ELEMENTS%??}"
        echo "Please ensure the specification includes:"
        echo "  - User stories in format 'As a [user], I want [action], so that [benefit]'"
        echo "  - Explicit acceptance criteria"
        echo "  - Clear scope boundaries (in-scope vs out-of-scope)"
        echo "  - Clear requirements"
        echo ""
        echo "Should we proceed anyway or enhance requirements first?"
        # In actual execution, wait for user decision
    else
        echo "✅ SDD Spec Completeness Check: All required elements present"
    fi
fi
```

### Step 5: Check for SDD Anti-Patterns (SDD-aligned)

Before creating the specification, check for SDD anti-patterns:

```bash
# SDD Anti-Pattern Detection
if [ -f "$REQUIREMENTS_FILE" ]; then
    # Check for premature technical details (violates SDD "What & Why, not How" principle)
    PREMATURE_TECH=$(grep -iE "implementation details|code structure|database schema|api endpoints|class hierarchy|architecture diagram|tech stack|framework|library|npm package|import|require" "$REQUIREMENTS_FILE" | wc -l)
    
    # Check for over-specification (too many details)
    LINE_COUNT=$(wc -l < "$REQUIREMENTS_FILE")
    OVER_SPECIFIED="false"
    if [ "$LINE_COUNT" -gt 500 ]; then
        OVER_SPECIFIED="true"
    fi
    
    # Check for specification theater (requirements that are too vague)
    VAGUE_REQUIREMENTS=$(grep -iE "should|might|could|maybe|possibly|perhaps|kind of|sort of" "$REQUIREMENTS_FILE" | wc -l)
    
    # Detect anti-patterns
    ANTI_PATTERNS_FOUND="false"
    WARNINGS=""
    
    if [ "$PREMATURE_TECH" -gt 5 ]; then
        ANTI_PATTERNS_FOUND="true"
        WARNINGS="${WARNINGS}⚠️ Premature technical details detected (SDD: focus on What & Why, not How in spec phase). "
    fi
    
    if [ "$OVER_SPECIFIED" = "true" ]; then
        ANTI_PATTERNS_FOUND="true"
        WARNINGS="${WARNINGS}⚠️ Over-specification detected (SDD: minimal, intentionally scoped specs preferred). "
    fi
    
    if [ "$VAGUE_REQUIREMENTS" -gt 10 ]; then
        ANTI_PATTERNS_FOUND="true"
        WARNINGS="${WARNINGS}⚠️ Vague requirements detected (may indicate specification theater). "
    fi
    
    if [ "$ANTI_PATTERNS_FOUND" = "true" ]; then
        echo "🔍 SDD Anti-Pattern Detection: Issues found"
        echo "$WARNINGS"
        echo "Consider reviewing requirements to align with SDD best practices."
        # In actual execution, present to user for review
    fi
fi
```

### Step 6: Check for Trade-offs and Create Checkpoints (if needed)

Before creating the specification, check if trade-offs need to be reviewed:

```bash
# Check for multiple valid patterns or conflicts
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

Also check for big changes or abstraction layer transitions:

```bash
# Check for big changes or layer transitions
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

### Step 7: Create Core Specification (SDD-aligned)

Write the main specification to `agent-os/specs/[current-spec]/spec.md`.

DO NOT write actual code in the spec.md document. Just describe the requirements clearly and concisely.

**SDD Best Practices for Spec Content:**
- Focus on **What & Why**, not **How** (implementation details belong in task creation phase)
- Ensure user stories follow format: "As a [user], I want [action], so that [benefit]"
- Include explicit acceptance criteria for each requirement
- Define clear scope boundaries (in-scope vs out-of-scope)
- Keep specs minimal and intentionally scoped (avoid feature bloat)
- Avoid premature technical details (no implementation specifics, database schemas, API endpoints, etc.)

Keep it short and include only essential information for each section.

Follow this structure exactly when creating the content of `spec.md`:

```markdown
# Specification: [Feature Name]

## Goal
[1-2 sentences describing the core objective]

## User Stories
- As a [user type], I want to [action] so that [benefit]
- [repeat for up to 2 max additional user stories]

## Specific Requirements

**Specific requirement name**
- [Up to 8 CONCISE sub-bullet points to clarify specific sub-requirements, design or architectual decisions that go into this requirement, or the technical approach to take when implementing this requirement]

[repeat for up to a max of 10 specific requirements]

## Visual Design
[If mockups provided]

**`planning/visuals/[filename]`**
- [up to 8 CONCISE bullets describing specific UI elements found in this visual to address when building]

[repeat for each file in the `planning/visuals` folder]

## Existing Code to Leverage

**Code, component, or existing logic found**
- [up to 5 bullets that describe what this existing code does and how it should be re-used or replicated when building this spec]

[repeat for up to 5 existing code areas]

**Basepoints Knowledge to Leverage (if available)**
- [Reusable patterns from basepoints that should be used]
- [Common modules from basepoints that can be referenced]
- [Standards and flows from basepoints that apply to this spec]
- [Testing approaches from basepoints that should be followed]
- [Historical decisions from basepoints that inform this feature]

## Out of Scope
- [up to 10 concise descriptions of specific features that are out of scope and MUST NOT be built in this spec]
```

## Important Constraints

1. **Always search for reusable code** before specifying new components
2. **Reference visual assets** when available
3. **Do NOT write actual code** in the spec
4. **Keep each section short**, with clear, direct, skimmable specifications
5. **Do NOT deviate from the template above** and do not add additional sections

## SDD Integration Notes

This workflow has been enhanced with Spec-Driven Development (SDD) best practices:

**SDD Principles Integrated:**
- **Specification as Source of Truth**: Spec completeness validation ensures specs are actionable
- **SDD Phase Order**: Spec validation occurs before task creation (Specify → Tasks → Implement)
- **What & Why, not How**: Spec content focuses on requirements, not implementation details

**SDD Validation Methods:**
- **Spec Completeness Checks**: Validates user stories format, acceptance criteria, scope boundaries, clear requirements
- **SDD Structure Validation**: Ensures specs follow SDD best practices (What & Why, minimal scope)
- **Anti-Pattern Detection**: Detects and warns about:
  - Specification theater (vague requirements that are written but never referenced)
  - Premature comprehensiveness (over-specification, trying to spec everything upfront)
  - Over-engineering (premature technical details in spec phase)

**Technology-Agnostic Approach (Default Profile Templates Only):**
- All SDD validation checks are structure-based, not technology-specific in default templates
- No hardcoded technology-specific SDD tool references in default templates
- Validation maintains technology-agnostic state throughout **in default profile templates**
- **After Specialization:** When templates are compiled to `agent-os/workflows/`, workflows can and should become technology-specific based on the project's actual stack
- **Command Outputs:** Specs, tasks, and implementations should reflect the project's actual technology stack

**Workflow Steps Enhanced:**
- Step 4: Added SDD spec completeness validation
- Step 5: Added SDD anti-pattern detection
- Step 7: Enhanced spec creation guidance with SDD best practices (What & Why focus)
` with specialized workflow content
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
  - Include project-specific examples and patterns

- **Reference Project-Specific Test Strategies**: Add references to project-specific test strategies from basepoints
  - Include test patterns extracted from basepoints
  - Reference test organization structures from basepoints
  - Use project-specific test strategies as guidance in specification writing

### Step 6: Write Specialized Commands to agent-os/commands/

Write specialized commands to agent-os/commands/, replacing abstract versions:

```bash
# Ensure agent-os/commands/ directories exist
mkdir -p agent-os/commands/shape-spec/single-agent
mkdir -p agent-os/commands/write-spec/single-agent

# Write specialized shape-spec command
echo "$SPECIALIZED_SHAPE_SPEC" > agent-os/commands/shape-spec/shape-spec.md
echo "$SPECIALIZED_SHAPE_SPEC" > agent-os/commands/shape-spec/single-agent/shape-spec.md

# Write specialized shape-spec phase files
for phase_file in profiles/default/commands/shape-spec/single-agent/*.md; do
    if [ -f "$phase_file" ] && [[ "$(basename "$phase_file")" =~ ^[0-9]+- ]]; then
        PHASE_NAME=$(basename "$phase_file")
        
        # Extract specialized phase content
        PHASE_CONTENT=$(extract_phase_from_specialized "$SPECIALIZED_SHAPE_SPEC_PHASES" "$PHASE_NAME")
        
        # Write specialized phase file
        echo "$PHASE_CONTENT" > "agent-os/commands/shape-spec/single-agent/$PHASE_NAME"
    fi
done

# Write specialized write-spec command
echo "$SPECIALIZED_WRITE_SPEC" > agent-os/commands/write-spec/write-spec.md
echo "$SPECIALIZED_WRITE_SPEC" > agent-os/commands/write-spec/single-agent/write-spec.md

# Write specialized write-spec workflow (if workflows directory exists)
mkdir -p agent-os/workflows/specification
echo "$SPECIALIZED_WRITE_SPEC_WORKFLOW" > agent-os/workflows/specification/write-spec.md

echo "✅ Specialized shape-spec and write-spec commands written to agent-os/commands/"
```

Write specialized commands:
- **shape-spec.md**: Write specialized shape-spec command to `agent-os/commands/shape-spec/shape-spec.md` (replace abstract version)
- **single-agent/shape-spec.md**: Write specialized single-agent version to `agent-os/commands/shape-spec/single-agent/shape-spec.md`
- **shape-spec phase files**: Write specialized phase files (1-initialize-spec.md, 2-shape-spec.md) to `agent-os/commands/shape-spec/single-agent/`
- **write-spec.md**: Write specialized write-spec command to `agent-os/commands/write-spec/write-spec.md` (replace abstract version)
- **single-agent/write-spec.md**: Write specialized single-agent version to `agent-os/commands/write-spec/single-agent/write-spec.md`
- **write-spec workflow**: Write specialized workflow to `agent-os/workflows/specification/write-spec.md` (if needed)

Ensure specialized commands:
- Are ready to use immediately (no further processing needed)
- Reference project-specific patterns, standards, workflows
- Contain project-specific examples and patterns
- Are tailored for the specific codebase (not abstract/generic)

## Display confirmation and next step

Once shape-spec and write-spec commands are specialized and written, output the following message:

```
✅ shape-spec and write-spec commands specialized!

- shape-spec command: Specialized with project-specific patterns, standards, and examples
- write-spec command: Specialized with project-specific structure patterns, testing approaches, and test strategies
- Abstract placeholders: Replaced with project-specific content
- Commands written to: agent-os/commands/shape-spec/ and agent-os/commands/write-spec/

Specialized commands are ready to use immediately.

NEXT STEP 👉 Run the command, `6-specialize-task-commands.md`
```

## User Standards & Preferences Compliance

IMPORTANT: Ensure that the specialized commands align with the user's preferences and standards as detailed in the following files:

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
    # Example: {{EXTRACT_PATTERNS_SECTION}} -> project-specific pattern extraction
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

## Important Constraints

- Must inject project-specific patterns, standards, flows, strategies from merged knowledge
- Must replace all abstract placeholders ({{workflows/...}}
⚠️ This workflow file was not found in profiles/default/workflows/....md, ) with project-specific content
- Must replace generic examples with project-specific patterns from basepoints
- **Must specialize basepoints knowledge extraction workflows**: Replace {{BASEPOINTS_PATH}}, {{BASEPOINT_FILE_PATTERN}}, and extraction placeholders with project-specific patterns
- **Must specialize scope detection workflows**: Replace keyword extraction, semantic analysis, and layer detection placeholders with project-specific patterns
- **Must specialize deep reading workflows**: Replace code file patterns, module path detection, and pattern extraction placeholders with project-specific patterns
- Must write specialized commands to agent-os/commands/ (replace abstract versions)
- Specialized commands must be ready to use immediately (no further processing needed)
- Must preserve command structure and phase organization while injecting project-specific knowledge
