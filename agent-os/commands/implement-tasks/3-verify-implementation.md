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
