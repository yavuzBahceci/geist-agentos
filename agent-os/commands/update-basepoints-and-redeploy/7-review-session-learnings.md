# Phase 7: Review Session Learnings

Present session learnings and prompt effectiveness analysis to user for review.

## Core Responsibilities

1. **Load Session Feedback**: Read current session data
2. **Extract Patterns**: Use extraction workflow to identify successful/failed patterns
3. **Analyze Prompts**: Use analysis workflow to determine prompt effectiveness
4. **Generate Adaptations**: Create specific suggestions based on learnings
5. **Present for Review**: Human approval required - NEVER auto-apply

## Prerequisites

- Session feedback exists from previous `/implement-tasks` runs
- Patterns have been extracted (use extraction workflow)
- Prompt effectiveness has been analyzed (use analysis workflow)

## Workflow

### Step 1: Load Session Feedback

```bash
SESSION_FILE="agent-os/output/session-feedback/current-session.md"

if [ ! -f "$SESSION_FILE" ]; then
    echo "ℹ️ No session feedback found. Skipping learning phase."
    echo "   Run /implement-tasks to generate session feedback."
    exit 0
fi

echo "📋 Loading session feedback..."
```

### Step 2: Extract Patterns from Session

Extract successful and failed patterns from session data:

```bash
# Extract Session Patterns

Analyze session data to identify successful and failed patterns.

## Purpose

Extract patterns from session implementation data to identify:
- Successful patterns (100% success rate, 3+ uses)
- Failed patterns/anti-patterns (any failure)
- Pattern usage statistics

## Inputs

- Session file: `agent-os/output/session-feedback/current-session.md`
- Raw patterns file: `agent-os/output/session-feedback/patterns/raw-patterns.txt`

## Process

### Step 1: Read All Implementations from Session

```bash
SESSION_FILE="agent-os/output/session-feedback/current-session.md"
PATTERNS_DIR="agent-os/output/session-feedback/patterns"

# Read session file
if [ ! -f "$SESSION_FILE" ]; then
    echo "⚠️ No session file found. Run /implement-tasks first."
    exit 0
fi

# Extract implementations table
IMPLEMENTATIONS=$(grep -A 100 "| # | Spec | Tasks | Outcome | Duration | Notes |" "$SESSION_FILE" | grep "^| [0-9]" || echo "")
```

### Step 2: Group by Patterns Used

```bash
# Parse patterns from raw patterns file
RAW_PATTERNS_FILE="$PATTERNS_DIR/raw-patterns.txt"

if [ -f "$RAW_PATTERNS_FILE" ]; then
    # Extract patterns mentioned in implementations
    # Pattern format: "Pattern: [name] (used in implementation #N)"
    
    # Group patterns by name
    declare -A PATTERN_COUNTS
    declare -A PATTERN_SUCCESS_COUNTS
    declare -A PATTERN_FAILURE_COUNTS
    
    while read -r line; do
        if [[ "$line" =~ "Pattern:" ]]; then
            PATTERN_NAME=$(echo "$line" | sed -n 's/.*Pattern: \([^ ]*\).*/\1/p')
            IMPL_NUM=$(echo "$line" | sed -n 's/.*implementation #\([0-9]*\).*/\1/p')
            
            # Check if this implementation was successful
            OUTCOME=$(echo "$IMPLEMENTATIONS" | grep "^| $IMPL_NUM " | awk -F'|' '{print $5}' | tr -d ' ')
            
            if [ -n "$PATTERN_NAME" ]; then
                PATTERN_COUNTS["$PATTERN_NAME"]=$((${PATTERN_COUNTS["$PATTERN_NAME"]:-0} + 1))
                
                if [[ "$OUTCOME" =~ "✅" ]]; then
                    PATTERN_SUCCESS_COUNTS["$PATTERN_NAME"]=$((${PATTERN_SUCCESS_COUNTS["$PATTERN_NAME"]:-0} + 1))
                else
                    PATTERN_FAILURE_COUNTS["$PATTERN_NAME"]=$((${PATTERN_FAILURE_COUNTS["$PATTERN_NAME"]:-0} + 1))
                fi
            fi
        fi
    done < "$RAW_PATTERNS_FILE"
fi
```

### Step 3: Calculate Success Rate per Pattern

```bash
# Calculate success rates
declare -A PATTERN_SUCCESS_RATES

for pattern in "${!PATTERN_COUNTS[@]}"; do
    total=${PATTERN_COUNTS["$pattern"]}
    successes=${PATTERN_SUCCESS_COUNTS["$pattern"]:-0}
    failures=${PATTERN_FAILURE_COUNTS["$pattern"]:-0}
    
    if [ $total -gt 0 ]; then
        success_rate=$(awk "BEGIN {printf \"%.2f\", ($successes / $total)}")
        PATTERN_SUCCESS_RATES["$pattern"]=$success_rate
    fi
done
```

### Step 4: Identify Successful Patterns

```bash
# Thresholds from config
MIN_USES_FOR_RECOMMENDATION=3
SUCCESS_RATE_THRESHOLD=1.0  # 100%

SUCCESSFUL_PATTERNS_FILE="$PATTERNS_DIR/successful.md"

cat > "$SUCCESSFUL_PATTERNS_FILE" << EOF
# Successful Patterns

EOF

for pattern in "${!PATTERN_COUNTS[@]}"; do
    total=${PATTERN_COUNTS["$pattern"]}
    success_rate=${PATTERN_SUCCESS_RATES["$pattern"]}
    
    # Pattern is successful if:
    # - Used 3+ times
    # - 100% success rate
    if [ $total -ge $MIN_USES_FOR_RECOMMENDATION ] && [ "$(echo "$success_rate >= $SUCCESS_RATE_THRESHOLD" | bc -l)" = "1" ]; then
        cat >> "$SUCCESSFUL_PATTERNS_FILE" << EOF
## Pattern: $pattern

**Description**: Pattern extracted from successful implementations

**First Observed**: $(date +%Y-%m-%d)
**Times Used**: $total
**Success Rate**: $(awk "BEGIN {printf \"%.0f\", $success_rate * 100}")%

**Applicable To**: [Context where this pattern applies]

---

EOF
    fi
done

echo "✅ Successful patterns written to: $SUCCESSFUL_PATTERNS_FILE"
```

### Step 5: Identify Failed Patterns (Anti-patterns)

```bash
FAILED_PATTERNS_FILE="$PATTERNS_DIR/failed.md"

cat > "$FAILED_PATTERNS_FILE" << EOF
# Failed Patterns (Anti-patterns)

EOF

for pattern in "${!PATTERN_COUNTS[@]}"; do
    failures=${PATTERN_FAILURE_COUNTS["$pattern"]:-0}
    
    # Pattern is an anti-pattern if it caused any failures
    if [ $failures -gt 0 ]; then
        total=${PATTERN_COUNTS["$pattern"]}
        failure_rate=$(awk "BEGIN {printf \"%.2f\", ($failures / $total)}")
        
        cat >> "$FAILED_PATTERNS_FILE" << EOF
## Pattern: $pattern

**Description**: This pattern caused implementation failures

**First Observed**: $(date +%Y-%m-%d)
**Times Used**: $total
**Failure Rate**: $(awk "BEGIN {printf \"%.0f\", $failure_rate * 100}")%

**Error Details**:
- Implementations affected: $failures of $total
- Common errors: [Extract from session data]

**Recommended Alternative**: [Suggest alternative approach]

---

EOF
    fi
done

echo "✅ Failed patterns written to: $FAILED_PATTERNS_FILE"
```

### Step 6: Write Pattern Summary to Session File

```bash
# Update session file with pattern summary
SUCCESSFUL_COUNT=$(grep -c "^## Pattern:" "$SUCCESSFUL_PATTERNS_FILE" 2>/dev/null || echo "0")
FAILED_COUNT=$(grep -c "^## Pattern:" "$FAILED_PATTERNS_FILE" 2>/dev/null || echo "0")

# Update session file patterns section
sed -i '' "/## Patterns Observed/,/## Prompt Effectiveness/c\\
## Patterns Observed\\
\\
### Successful\\
$SUCCESSFUL_COUNT pattern(s) identified with 100% success rate and 3+ uses\\
\\
### Failed\\
$FAILED_COUNT anti-pattern(s) identified\\
\\
## Prompt Effectiveness" "$SESSION_FILE"
```

## Output

- Successful patterns: `agent-os/output/session-feedback/patterns/successful.md`
- Failed patterns: `agent-os/output/session-feedback/patterns/failed.md`
- Updated session file with pattern summary

## Thresholds

From `workflow-config.yml`:
- `min_uses_for_recommendation: 3` - Minimum uses before recommending
- `success_rate_threshold: 1.0` - 100% success rate required

## Usage

Called by `/update-basepoints-and-redeploy` Phase 7 to extract patterns before human review.

Can also be called manually to analyze current session data.

## Notes

- Patterns with 100% success and 3+ uses are marked as successful
- Patterns with any failures are marked as anti-patterns
- Pattern extraction improves over time as more data is collected

```

This workflow will:
- Read all implementations from session
- Group by patterns used
- Calculate success rate per pattern
- Write patterns to `patterns/successful.md` and `patterns/failed.md`

### Step 3: Analyze Prompt Effectiveness

Analyze which prompts led to good/bad outcomes:

```bash
# Analyze Prompt Effectiveness

Determine which prompts led to good/bad outcomes.

## Purpose

Analyze prompt effectiveness from session data to identify:
- Prompts that worked well (effective)
- Prompts that needed clarification (ineffective)
- Improvement opportunities for prompt construction

## Inputs

- Session file: `agent-os/output/session-feedback/current-session.md`
- Raw prompt data: `agent-os/output/session-feedback/prompts/effective-raw.txt`
- Raw prompt issues: `agent-os/output/session-feedback/prompts/needs-improvement-raw.txt`

## Indicators of Effective Prompts

Prompts that lead to:
- ✅ Implementation completed without retries
- ✅ No clarification questions needed
- ✅ Validation passed first time
- ✅ Correct files created/modified
- ✅ Clear understanding of requirements

## Indicators of Ineffective Prompts

Prompts that lead to:
- ❌ Multiple retries needed
- ❌ User had to clarify requirements
- ❌ Validation failed due to misunderstanding
- ❌ Wrong files created/modified
- ❌ Missing context or constraints

## Process

### Step 1: Load Session Data

```bash
SESSION_FILE="agent-os/output/session-feedback/current-session.md"
PROMPTS_DIR="agent-os/output/session-feedback/prompts"

# Create prompts directory if needed
mkdir -p "$PROMPTS_DIR"

# Read session file
if [ ! -f "$SESSION_FILE" ]; then
    echo "⚠️ No session file found. Run /implement-tasks first."
    exit 0
fi

# Extract implementations
IMPLEMENTATIONS=$(grep -A 100 "| # | Spec | Tasks | Outcome | Duration | Notes |" "$SESSION_FILE" | grep "^| [0-9]" || echo "")
```

### Step 2: Analyze Effective Prompts

```bash
EFFECTIVE_FILE="$PROMPTS_DIR/effective.md"
EFFECTIVE_RAW="$PROMPTS_DIR/effective-raw.txt"

cat > "$EFFECTIVE_FILE" << EOF
# Effective Prompts

Prompts that led to successful implementations without issues.

EOF

# Count effective prompts
EFFECTIVE_COUNT=0

# Read raw effective data
if [ -f "$EFFECTIVE_RAW" ]; then
    while read -r line; do
        if [[ "$line" =~ "Implementation #([0-9]+): Passed without issues" ]]; then
            IMPL_NUM="${BASH_REMATCH[1]}"
            EFFECTIVE_COUNT=$((EFFECTIVE_COUNT + 1))
            
            # Extract implementation details
            IMPL_DETAILS=$(echo "$IMPLEMENTATIONS" | grep "^| $IMPL_NUM " || echo "")
            
            if [ -n "$IMPL_DETAILS" ]; then
                SPEC=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $3}' | tr -d ' ')
                TASKS=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $4}' | tr -d ' ')
                
                cat >> "$EFFECTIVE_FILE" << EOF
## Implementation #$IMPL_NUM: $SPEC

**Spec**: $SPEC
**Tasks**: $TASKS
**Outcome**: ✅ Passed without issues

**What Worked Well**:
- No retries needed
- Validation passed first time
- Clear understanding of requirements

**Prompt Characteristics**:
- Sufficient context provided
- Clear constraints defined
- Output format specified

---

EOF
            fi
        fi
    done < "$EFFECTIVE_RAW"
fi

echo "✅ Effective prompts: $EFFECTIVE_COUNT identified"
```

### Step 3: Analyze Ineffective Prompts

```bash
INEFFECTIVE_FILE="$PROMPTS_DIR/needs-improvement.md"
INEFFECTIVE_RAW="$PROMPTS_DIR/needs-improvement-raw.txt"

cat > "$INEFFECTIVE_FILE" << EOF
# Prompts Needing Improvement

Prompts that led to issues or required clarification.

EOF

# Count ineffective prompts
INEFFECTIVE_COUNT=0

# Read raw ineffective data
if [ -f "$INEFFECTIVE_RAW" ]; then
    while read -r line; do
        if [[ "$line" =~ "Implementation #([0-9]+): (.+)" ]]; then
            IMPL_NUM="${BASH_REMATCH[1]}"
            ISSUE="${BASH_REMATCH[2]}"
            INEFFECTIVE_COUNT=$((INEFFECTIVE_COUNT + 1))
            
            # Extract implementation details
            IMPL_DETAILS=$(echo "$IMPLEMENTATIONS" | grep "^| $IMPL_NUM " || echo "")
            
            if [ -n "$IMPL_DETAILS" ]; then
                SPEC=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $3}' | tr -d ' ')
                TASKS=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $4}' | tr -d ' ')
                OUTCOME=$(echo "$IMPL_DETAILS" | awk -F'|' '{print $5}' | tr -d ' ')
                
                cat >> "$INEFFECTIVE_FILE" << EOF
## Implementation #$IMPL_NUM: $SPEC

**Spec**: $SPEC
**Tasks**: $TASKS
**Outcome**: $OUTCOME
**Issue**: $ISSUE

**What Went Wrong**:
- [Issue details]

**Prompt Issues**:
- Missing context: [specific context needed]
- Unclear instructions: [what was unclear]
- Missing constraints: [what constraints were missing]

**Recommended Improvements**:
- Add: [specific improvement]
- Clarify: [what to clarify]
- Include: [missing information]

---

EOF
            fi
        fi
    done < "$INEFFECTIVE_RAW"
fi

echo "✅ Prompts needing improvement: $INEFFECTIVE_COUNT identified"
```

### Step 4: Generate Prompt Effectiveness Summary

```bash
# Update session file with prompt effectiveness summary
sed -i '' "/## Prompt Effectiveness/,/^#/c\\
## Prompt Effectiveness\\
\\
### Worked Well\\
$EFFECTIVE_COUNT prompt(s) led to successful implementations without issues\\
\\
### Needed Clarification\\
$INEFFECTIVE_COUNT prompt(s) required clarification or led to issues\\
" "$SESSION_FILE"
```

### Step 5: Generate Improvement Suggestions

```bash
SUGGESTIONS_FILE="$PROMPTS_DIR/improvement-suggestions.md"

cat > "$SUGGESTIONS_FILE" << EOF
# Prompt Improvement Suggestions

Based on analysis of $INEFFECTIVE_COUNT problematic implementations.

## Common Issues

EOF

# Extract common issues from ineffective prompts
if [ -f "$INEFFECTIVE_FILE" ]; then
    # Count common issues
    MISSING_CONTEXT=$(grep -c "Missing context:" "$INEFFECTIVE_FILE" || echo "0")
    UNCLEAR_INSTRUCTIONS=$(grep -c "Unclear instructions:" "$INEFFECTIVE_FILE" || echo "0")
    MISSING_CONSTRAINTS=$(grep -c "Missing constraints:" "$INEFFECTIVE_FILE" || echo "0")
    
    cat >> "$SUGGESTIONS_FILE" << EOF
1. **Missing Context**: $MISSING_CONTEXT occurrences
   - Add project patterns from basepoints
   - Include relevant tech stack info
   - Reference similar previous implementations

2. **Unclear Instructions**: $UNCLEAR_INSTRUCTIONS occurrences
   - Use numbered steps
   - Specify expected outputs
   - Define clear boundaries

3. **Missing Constraints**: $MISSING_CONSTRAINTS occurrences
   - Add "DO NOT" sections
   - Include anti-patterns to avoid
   - Specify validation requirements

## Recommendations

- Always include basepoints context
- Use clear, numbered steps
- Explicitly state constraints
- Specify output format
- Include validation criteria

EOF
fi

echo "✅ Improvement suggestions: $SUGGESTIONS_FILE"
```

## Output

- Effective prompts: `agent-os/output/session-feedback/prompts/effective.md`
- Prompts needing improvement: `agent-os/output/session-feedback/prompts/needs-improvement.md`
- Improvement suggestions: `agent-os/output/session-feedback/prompts/improvement-suggestions.md`
- Updated session file with prompt effectiveness summary

## Usage

Called by `/update-basepoints-and-redeploy` Phase 7 to analyze prompt effectiveness before human review.

Can also be called manually to analyze current session data.

## Notes

- Effectiveness is measured by implementation success without retries
- Patterns in ineffective prompts are used to improve prompt construction
- Analysis improves over time as more data is collected

```

This workflow will:
- Identify effective prompts (no retries, passed first time)
- Identify ineffective prompts (needed clarification, failed)
- Generate improvement suggestions

### Step 4: Generate Adaptation Suggestions

Based on analysis, generate specific adaptation suggestions:

**For Successful Patterns** (used 3+ times with 100% success):
- Add to `/shape-spec` context section
- Add to `/implement-tasks` guidance section
- Include in relevant command patterns

**For Failed Patterns** (caused issues):
- Add warning to `/implement-tasks` constraints
- Add "DO NOT" section to relevant commands
- Include in anti-patterns documentation

**For Prompt Issues** (needed clarification):
- Add missing context to command prompts
- Clarify ambiguous instructions
- Add explicit constraints and boundaries

### Step 5: Present Human Review Checkpoint

Present learnings to user for approval:

```bash
# Present Learnings for Review

Workflow to present session learnings to user for review and approval.

## Purpose

Display session learnings summary with clear options for user approval:
- Successful patterns discovered
- Anti-patterns identified
- Prompt effectiveness analysis
- Proposed adaptations

## Inputs

- Session file: `agent-os/output/session-feedback/current-session.md`
- Successful patterns: `agent-os/output/session-feedback/patterns/successful.md`
- Failed patterns: `agent-os/output/session-feedback/patterns/failed.md`
- Prompt analysis: `agent-os/output/session-feedback/prompts/effective.md` and `needs-improvement.md`

## Process

### Step 1: Load Session Learnings

```bash
SESSION_FILE="agent-os/output/session-feedback/current-session.md"
PATTERNS_SUCCESS="agent-os/output/session-feedback/patterns/successful.md"
PATTERNS_FAILED="agent-os/output/session-feedback/patterns/failed.md"
PROMPTS_EFFECTIVE="agent-os/output/session-feedback/prompts/effective.md"
PROMPTS_IMPROVEMENT="agent-os/output/session-feedback/prompts/needs-improvement.md"

if [ ! -f "$SESSION_FILE" ]; then
    echo "ℹ️ No session feedback found. Skipping learning phase."
    echo "   Run /implement-tasks to generate session feedback."
    exit 0
fi

echo "📋 Loading session learnings..."
```

### Step 2: Extract Session Summary

```bash
# Extract metadata from session
TOTAL_IMPL=$(grep "Implementations:" "$SESSION_FILE" | sed 's/.*: \([0-9]*\).*/\1/' | head -1)
SUCCESS_RATE=$(grep "Success Rate:" "$SESSION_FILE" | sed 's/.*: \([0-9]*\)%.*/\1/' | head -1)

# Count successful implementations
SUCCESSFUL=$(grep -c "✅ Pass" "$SESSION_FILE" || echo "0")
FAILED=$(grep -c "❌ Fail" "$SESSION_FILE" || echo "0")

# Count patterns
SUCCESSFUL_PATTERNS_COUNT=$(grep -c "^## Pattern:" "$PATTERNS_SUCCESS" 2>/dev/null || echo "0")
FAILED_PATTERNS_COUNT=$(grep -c "^## Pattern:" "$PATTERNS_FAILED" 2>/dev/null || echo "0")
```

### Step 3: Extract Pattern Details

```bash
# Extract successful patterns
SUCCESSFUL_PATTERNS=""
if [ -f "$PATTERNS_SUCCESS" ]; then
    while IFS= read -r line; do
        if [[ "$line" =~ "^## Pattern: (.*)" ]]; then
            PATTERN_NAME="${BASH_REMATCH[1]}"
            # Get times used
            TIMES_USED=$(grep -A 5 "^## Pattern: $PATTERN_NAME" "$PATTERNS_SUCCESS" | grep "Times Used:" | sed 's/.*: \([0-9]*\).*/\1/')
            SUCCESSFUL_PATTERNS="${SUCCESSFUL_PATTERNS}  - $PATTERN_NAME (used $TIMES_USED times)\n"
        fi
    done < "$PATTERNS_SUCCESS"
fi

# Extract failed patterns
FAILED_PATTERNS=""
if [ -f "$PATTERNS_FAILED" ]; then
    while IFS= read -r line; do
        if [[ "$line" =~ "^## Pattern: (.*)" ]]; then
            PATTERN_NAME="${BASH_REMATCH[1]}"
            # Get error details
            ERROR=$(grep -A 10 "^## Pattern: $PATTERN_NAME" "$PATTERNS_FAILED" | grep "Error Details:" -A 3 | head -1)
            FAILED_PATTERNS="${FAILED_PATTERNS}  - $PATTERN_NAME: $ERROR\n"
        fi
    done < "$PATTERNS_FAILED"
fi
```

### Step 4: Extract Prompt Effectiveness

```bash
# Count effective prompts
EFFECTIVE_COUNT=$(grep -c "^## Implementation #" "$PROMPTS_EFFECTIVE" 2>/dev/null || echo "0")
IMPROVEMENT_COUNT=$(grep -c "^## Implementation #" "$PROMPTS_IMPROVEMENT" 2>/dev/null || echo "0")
```

### Step 5: Generate Adaptation Suggestions

```bash
# Generate suggestions based on patterns
SUGGESTIONS=""

# For successful patterns - suggest adding to commands
if [ $SUCCESSFUL_PATTERNS_COUNT -gt 0 ]; then
    SUGGESTIONS="${SUGGESTIONS}✅ Add successful patterns to command context\n"
fi

# For failed patterns - suggest adding warnings
if [ $FAILED_PATTERNS_COUNT -gt 0 ]; then
    SUGGESTIONS="${SUGGESTIONS}⚠️ Add anti-pattern warnings to commands\n"
fi

# For prompt improvements
if [ $IMPROVEMENT_COUNT -gt 0 ]; then
    SUGGESTIONS="${SUGGESTIONS}📝 Improve prompts based on effectiveness analysis\n"
fi
```

### Step 6: Present Review UI

Display the review UI:

```
┌─────────────────────────────────────────────────────────────────┐
│  🔍 SESSION LEARNINGS REVIEW                                    │
│                                                                 │
│  📊 Session Summary:                                            │
│  • Implementations: $TOTAL_IMPL total ($SUCCESSFUL successful, $FAILED failed) │
│  • Success Rate: $SUCCESS_RATE%                                   │
│  • New Patterns: $SUCCESSFUL_PATTERNS_COUNT                                       │
│  • Anti-patterns: $FAILED_PATTERNS_COUNT                                       │
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  ✅ SUCCESSFUL PATTERNS                                         │
│                                                                 │
EOF

# Display successful patterns
if [ $SUCCESSFUL_PATTERNS_COUNT -gt 0 ]; then
    echo "$SUCCESSFUL_PATTERNS" | while read -r pattern; do
        if [ -n "$pattern" ]; then
            echo "│  $pattern"
        fi
    done
else
    echo "│  (No successful patterns identified yet)"
fi

cat << EOF
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  ❌ ANTI-PATTERNS (caused failures)                             │
│                                                                 │
EOF

# Display failed patterns
if [ $FAILED_PATTERNS_COUNT -gt 0 ]; then
    echo "$FAILED_PATTERNS" | while read -r pattern; do
        if [ -n "$pattern" ]; then
            echo "│  $pattern"
        fi
    done
else
    echo "│  (No anti-patterns identified)"
fi

cat << EOF
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  📝 PROMPT EFFECTIVENESS                                        │
│                                                                 │
│  ✅ Effective: $EFFECTIVE_COUNT prompt(s)                          │
│  ⚠️ Needs Improvement: $IMPROVEMENT_COUNT prompt(s)                            │
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  💡 PROPOSED ADAPTATIONS                                        │
│                                                                 │
EOF

echo "$SUGGESTIONS" | while read -r suggestion; do
    if [ -n "$suggestion" ]; then
        echo "│  $suggestion"
    fi
done

cat << EOF
│                                                                 │
│  ═══════════════════════════════════════════════════════════    │
│                                                                 │
│  Options:                                                       │
│  [a] Apply all adaptations                                      │
│  [s] Select which to apply                                      │
│  [n] Skip (save learnings for later)                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Step 7: Wait for User Response

```bash
echo ""
read -p "Your choice [a/s/n]: " USER_CHOICE

case "$USER_CHOICE" in
    "a"|"A"|"all")
        APPROVAL_TYPE="all"
        ;;
    "s"|"S"|"select")
        APPROVAL_TYPE="select"
        ;;
    "n"|"N"|"skip")
        APPROVAL_TYPE="skip"
        ;;
    *)
        echo "⚠️ Invalid choice. Saving for later review."
        APPROVAL_TYPE="skip"
        ;;
esac

echo "User choice: $APPROVAL_TYPE"
```

### Step 8: Save Adaptations to Pending

```bash
ADAPTATIONS_DIR="agent-os/output/session-feedback/adaptations"
PENDING_FILE="$ADAPTATIONS_DIR/pending.md"

mkdir -p "$ADAPTATIONS_DIR"

cat > "$PENDING_FILE" << EOF
# Pending Adaptations

**Created**: $(date -Iseconds)
**Approval Status**: $APPROVAL_TYPE

## Session Summary
- Total Implementations: $TOTAL_IMPL
- Success Rate: $SUCCESS_RATE%
- Successful Patterns: $SUCCESSFUL_PATTERNS_COUNT
- Anti-patterns: $FAILED_PATTERNS_COUNT

## Proposed Adaptations

EOF

# Add adaptations based on approval type
if [ "$APPROVAL_TYPE" = "all" ] || [ "$APPROVAL_TYPE" = "select" ]; then
    cat >> "$PENDING_FILE" << EOF
### Successful Patterns Adaptations
[Adaptations for successful patterns]

### Anti-patterns Adaptations
[Adaptations for anti-patterns]

### Prompt Improvements
[Adaptations for prompt improvements]
EOF
fi

echo "✅ Adaptations saved to: $PENDING_FILE"
```

## Output

- User approval choice (all/select/skip)
- Pending adaptations file: `agent-os/output/session-feedback/adaptations/pending.md`

## Critical Safety

- ALWAYS asks user before applying any changes
- NEVER auto-applies adaptations
- Clear presentation of what will change
- Option to reject and save for later

## Usage

Called by Phase 7 of `/update-basepoints-and-redeploy` to present learnings for human review.

```

This workflow will:
- Display session summary
- Show successful patterns discovered
- Show anti-patterns identified
- Show prompt effectiveness analysis
- Present clear options (Approve all / Select / Skip)
- **WAIT for user response**

### Step 6: Handle User Response

Based on user choice:

```bash
# User choice is saved by present-learnings-for-review workflow
PENDING_FILE="agent-os/output/session-feedback/adaptations/pending.md"

if [ -f "$PENDING_FILE" ]; then
    APPROVAL_STATUS=$(grep "Approval Status:" "$PENDING_FILE" | sed 's/.*: \([^ ]*\).*/\1/')
    
    case "$APPROVAL_STATUS" in
        "all")
            echo "✅ All adaptations approved. Proceed to Phase 8: Adapt Commands."
            ;;
        "select")
            echo "✅ Selected adaptations approved. Proceed to Phase 8: Adapt Commands."
            ;;
        "skip")
            echo "ℹ️ Adaptations saved for later review. Skipping Phase 8."
            echo "   Adaptations saved to: $PENDING_FILE"
            exit 0
            ;;
    esac
else
    echo "⚠️ No adaptations file found. Skipping Phase 8."
    exit 0
fi
```

## Output

- Adaptations file: `agent-os/output/session-feedback/adaptations/pending.md`
- User approval status (all/select/skip)

## Critical Safety Requirements

- ⚠️ **ALWAYS ask user** before applying any changes
- ⚠️ **NEVER auto-apply** adaptations
- ⚠️ **Clear presentation** of what will change
- ⚠️ **Option to reject** and save for later

## Next Step

If user approved adaptations (all or select):
- Proceed to Phase 8: Adapt Commands

If user skipped:
- Exit (adaptations saved for later)
- Can review later by running Phase 7 again

## Notes

- This phase is CRITICAL for safety - human review is mandatory
- All adaptations require explicit user approval
- Rejected learnings are saved to history for future reference
- Session data is preserved for later analysis
