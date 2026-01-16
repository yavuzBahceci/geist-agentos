# Process for Orchestrating a Spec's Implementation

Now that we have a spec and tasks list ready for implementation, we will proceed with orchestrating implementation of each task group by a dedicated agent using the following MULTI-PHASE process.

Follow each of these phases and their individual workflows IN SEQUENCE:

## Multi-Phase Process

### FIRST: Extract Basepoints Knowledge for Orchestration

Before orchestrating tasks, extract basepoints knowledge to inform sub-agent context:

```bash
# Check if basepoints exist
if [ -d "agent-os/basepoints" ] && [ -f "agent-os/basepoints/headquarter.md" ]; then
    # Determine spec path
    SPEC_PATH="agent-os/specs/[current-spec]"
    
    # Extract basepoints knowledge for orchestration context
    {{workflows/basepoints/extract-basepoints-knowledge-automatic}}
    {{workflows/scope-detection/detect-abstraction-layer}}
    {{workflows/scope-detection/detect-scope-semantic-analysis}}
    {{workflows/scope-detection/detect-scope-keyword-matching}}
    
    # Load extracted knowledge for sub-agent context
    if [ -f "$SPEC_PATH/implementation/cache/basepoints-knowledge.md" ]; then
        EXTRACTED_KNOWLEDGE=$(cat "$SPEC_PATH/implementation/cache/basepoints-knowledge.md")
    fi
    
    # Load detected layer for module matching
    if [ -f "$SPEC_PATH/implementation/cache/detected-layer.txt" ]; then
        DETECTED_LAYER=$(cat "$SPEC_PATH/implementation/cache/detected-layer.txt")
    fi
fi
```

If basepoints exist, the extracted knowledge will be used to:
- Match tasks to relevant module basepoints
- Inject basepoint knowledge into sub-agent prompts
- Provide coding patterns and standards context per task group
- Enable sub-agents to leverage project-specific patterns

### NEXT: Get tasks.md for this spec

IF you already know which spec we're working on and IF that spec folder has a `tasks.md` file, then use that and skip to the NEXT phase.

IF you don't already know which spec we're working on and IF that spec folder doesn't yet have a `tasks.md` THEN output the following request to the user:

```
Please point me to a spec's `tasks.md` that you want to orchestrate implementation for.

If you don't have one yet, then run any of these commands first:
/shape-spec
/write-spec
/create-tasks
```

### NEXT: Create orchestration.yml to serve as a roadmap for orchestration of task groups

In this spec's folder, create this file: `agent-os/specs/[this-spec]/orchestration.yml`.

Populate this file with with the names of each task group found in this spec's `tasks.md` and use this EXACT structure for the content of `orchestration.yml`:

```yaml
task_groups:
  - name: [task-group-name]
  - name: [task-group-name]
  - name: [task-group-name]
  # Repeat for each task group found in tasks.md
```

{{IF use_claude_code_subagents}}
### NEXT: Ask user to assign subagents to each task group

Next we must determine which subagents should be assigned to which task groups.  Ask the user to provide this info using the following request to user and WAIT for user's response:

```
Please specify the name of each subagent to be assigned to each task group:

1. [task-group-name]
2. [task-group-name]
3. [task-group-name]
[repeat for each task-group you've added to orchestration.yml]

Simply respond with the subagent names and corresponding task group number and I'll update orchestration.yml accordingly.
```

Using the user's responses, update `orchestration.yml` to specify those subagent names.  `orchestration.yml` should end up looking like this:

```yaml
task_groups:
  - name: [task-group-name]
    claude_code_subagent: [subagent-name]
  - name: [task-group-name]
    claude_code_subagent: [subagent-name]
  - name: [task-group-name]
    claude_code_subagent: [subagent-name]
  # Repeat for each task group found in tasks.md
```

For example, after this step, the `orchestration.yml` file might look like this (exact names will vary):

```yaml
task_groups:
  - name: core-functionality
    claude_code_subagent: core-specialist
  - name: user-interface
    claude_code_subagent: interface-specialist
  - name: interface-implementation
    claude_code_subagent: interface-specialist
```
{{ENDIF use_claude_code_subagents}}

{{UNLESS standards_as_claude_code_skills}}
### NEXT: Ask user to assign standards to each task group

Next we must determine which standards should guide the implementation of each task group.  Ask the user to provide this info using the following request to user and WAIT for user's response:

```
Please specify the standard(s) that should be used to guide the implementation of each task group:

1. [task-group-name]
2. [task-group-name]
3. [task-group-name]
[repeat for each task-group you've added to orchestration.yml]

For each task group number, you can specify any combination of the following:

"all" to include all of your standards
"global/*" to include all of the files inside of standards/global
"global/coding-style.md" to include the coding-style.md standard file
"none" to include no standards for this task group.
```

Using the user's responses, update `orchestration.yml` to specify those standards for each task group.  `orchestration.yml` should end up having AT LEAST the following information added to it:

```yaml
task_groups:
  - name: [task-group-name]
    standards:
      - [users' 1st response for this task group]
      - [users' 2nd response for this task group]
      - [users' 3rd response for this task group]
      # Repeat for all standards that the user specified for this task group
  - name: [task-group-name]
    standards:
      - [users' 1st response for this task group]
      - [users' 2nd response for this task group]
      # Repeat for all standards that the user specified for this task group
  # Repeat for each task group found in tasks.md
```

For example, after this step, the `orchestration.yml` file might look like this (exact names will vary):

```yaml
task_groups:
  - name: core-functionality
    standards:
      - all
  - name: user-interface
    standards:
      - global/*
      - quality/assurance.md
  - name: task-group-with-no-standards
  - name: interface-implementation
    standards:
      - global/*
      - global/error-handling.md
```

Note: If the `use_claude_code_subagents` flag is enabled, the final `orchestration.yml` would include BOTH `claude_code_subagent` assignments AND `standards` for each task group.
{{ENDUNLESS standards_as_claude_code_skills}}

{{IF use_claude_code_subagents}}
### NEXT: Delegate task groups implementations to assigned subagents

Loop through each task group in `agent-os/specs/[this-spec]/tasks.md` and delegate its implementation to the assigned subagent specified in `orchestration.yml`.

For each delegation, provide the subagent with:
- The task group (including the parent task and all sub-tasks)
- The spec file: `agent-os/specs/[this-spec]/spec.md`
- Instruct subagent to:
  - Perform their implementation
  - Check off the task and sub-task(s) in `agent-os/specs/[this-spec]/tasks.md`
{{UNLESS standards_as_claude_code_skills}}

In addition to the above items, also instruct the subagent to closely adhere to the user's standards & preferences as specified in the following files.  To build the list of file references to give to the subagent, follow these instructions:

{{workflows/implementation/compile-implementation-standards}}

Provide all of the above to the subagent when delegating tasks for it to implement.
{{ENDUNLESS standards_as_claude_code_skills}}
{{ENDIF use_claude_code_subagents}}

{{UNLESS use_claude_code_subagents}}
### NEXT: Generate prompts

Now we must generate an ordered series of prompt texts, which will be used to direct the implementation of each task group listed in `orchestration.yml`.

Follow these steps to generate this spec's ordered series of prompts texts, each in its own .md file located in `agent-os/specs/[this-spec]/implementation/prompts/`.

LOOP through EACH task group in `agent-os/specs/[this-spec]/tasks.md` and for each, use the following workflow to generate a markdown file with prompt text for each task group:

#### Step 1. Create the prompt markdown file

Create the prompt markdown file using this naming convention:
`agent-os/specs/[this-spec]/implementation/prompts/[task-group-number]-[task-group-title].md`.

For example, if the 3rd task group in tasks.md is named "Comment System" then create `3-comment-system.md`.

#### Step 2. Populate the prompt file

Populate the prompt markdown file using the following Prompt file content template.

##### Bracket content replacements

In the content template below, replace "[spec-title]" and "[this-spec]" with the current spec's title, and "[task-group-number]" with the current task group's number.

{{UNLESS standards_as_claude_code_skills}}
To replace "[orchestrated-standards]", use the following workflow:

{{workflows/implementation/compile-implementation-standards}}
{{ENDUNLESS standards_as_claude_code_skills}}

#### Prompt file content template:

```markdown
# Task Group [task-group-number]: [task-group-name]

**Spec:** [spec-title]
**Scope:** This prompt covers ONLY Task Group [task-group-number]. Do NOT implement other task groups.

---

## Task to Implement

[paste entire task group including parent task, all of its' sub-tasks, and sub-bullet points]

---

## Context

Read these files to understand the context:
- @agent-os/specs/[this-spec]/spec.md
- @agent-os/specs/[this-spec]/planning/requirements.md
- @agent-os/specs/[this-spec]/planning/visuals (if exists)

## Basepoints Knowledge Context

[IF basepoints-knowledge.md exists for this spec, include relevant patterns and standards for this task group:]

The following patterns and standards from basepoints are relevant to this task group:

[Extract and include relevant sections from $SPEC_PATH/implementation/cache/basepoints-knowledge.md that match this task group's domain]

Use these patterns to guide your implementation and ensure consistency with existing codebase conventions.

---

## Implementation Instructions

1. Implement ONLY the tasks listed above (Task Group [task-group-number])
2. Mark each completed task with `[x]` in `tasks.md`
3. Follow the workflow: {{workflows/implementation/implement-tasks}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure that your implementation work is ALIGNED and DOES NOT CONFLICT with the user's preferences and standards as detailed in:

[orchestrated-standards]
{{ENDUNLESS standards_as_claude_code_skills}}

---

## ✅ Completion and Validation

When you have completed ALL tasks in Task Group [task-group-number]:

### Step 1: Run Implementation Validation

Before marking tasks complete, run deterministic validation:

```bash
SPEC_PATH="agent-os/specs/[this-spec]"
{{workflows/validation/validate-implementation}}
```

This runs project-specific validators (build, test, lint, type-check) that were configured during deployment.

**IF validation FAILED:**
- Present failures to user
- Wait for fixes or user override
- Do NOT proceed until resolved

**IF validation PASSED or WARNINGS:**
- Proceed to Step 2

### Step 2: Mark Completion
1. ✅ Mark all tasks as complete `[x]` in `tasks.md`
2. ✅ Add ✅ marker to this task group header in `tasks.md`
3. ✅ Provide a brief summary of what was implemented

### Step 4: Check If Human Review Needed

Run the review check workflow:

```bash
SPEC_PATH="agent-os/specs/[this-spec]"
{{workflows/human-review/review-trade-offs}}
```

This will automatically:
- Detect any trade-offs or contradictions in your implementation
- If issues found → Present them and WAIT for user decision
- If no issues → Output "✅ No human review needed. Proceeding automatically."

### Step 5: Continue Based on Review Result

**IF review detected issues (REVIEW_TRIGGERED=true):**
- Present the trade-offs/contradictions to user
- Wait for user decision before proceeding
- Once user approves, continue to Step 6

**IF no review needed (REVIEW_TRIGGERED=false):**
- Proceed directly to Step 6

### Step 6: Proceed to Next Prompt

👉 Read and execute the next prompt: `[next-prompt-number]-[next-prompt-name].md`
   (Located in: `agent-os/specs/[this-spec]/implementation/prompts/`)

**Output format:**

```
✅ Task Group [task-group-number] Complete: [task-group-name]

Implemented: [brief list]
Files modified: [list files]

🔍 Review check: [No issues found / X trade-offs, Y contradictions detected]
[IF review needed: "⏳ Waiting for user decision..." ELSE: "✅ Auto-proceeding"]

➡️ Proceeding to Task Group [next-number]...
```

⚠️ **IMPORTANT**: Do NOT implement the next task group directly. You MUST read the next prompt file first and follow its instructions.

[IF this is the final task group, output instead:]
```
🎉 ALL TASK GROUPS COMPLETE!

The implementation is finished. See validation report at:
`agent-os/specs/[this-spec]/implementation/cache/validation-report.md`
```
```

### Step 3: Match Tasks to Relevant Module Basepoints

For each task group, analyze the task description and match it to the most relevant module basepoints:

```bash
# For each task group in orchestration.yml
for task_group in $(yq e '.task_groups[].name' "$SPEC_PATH/orchestration.yml"); do
    # Find relevant basepoints based on task keywords
    # This enables targeted knowledge injection per task group
    {{workflows/scope-detection/detect-scope-keyword-matching}}
done
```

When generating prompts, include the relevant basepoints knowledge for that specific task group.

### Step 4: Output the list of created prompt files

Output to user the following:

```
Ready to begin implementation of [spec-title]!

✅ Basepoints knowledge extracted: [Yes / No basepoints found]
✅ Detected layer: [LAYER or unknown]
✅ Task groups matched to basepoints: [X of Y]

## Implementation Prompts

[list prompt files in order, numbered]

## How to Execute

Start by running prompt 1. Each prompt will:
1. Implement its assigned task group
2. Mark tasks complete in `tasks.md`
3. Automatically proceed to the next prompt

The agent will chain through all prompts until complete (or stop if human review is needed).

Progress is tracked in: `agent-os/specs/[this-spec]/tasks.md`

👉 Start with: "Run prompt 1" or paste the contents of the first prompt file.
```

### Step 5: Run Validation

After orchestration setup is complete, run validation:

```bash
SPEC_PATH="agent-os/specs/[current-spec]"
COMMAND="orchestrate-tasks"
{{workflows/validation/validate-output-exists}}
{{workflows/validation/validate-knowledge-integration}}
{{workflows/validation/generate-validation-report}}
```

### Step 6: Generate Resource Checklist

Generate a checklist of all resources consulted:

```bash
{{workflows/basepoints/organize-and-cache-basepoints-knowledge}}
```

{{ENDUNLESS use_claude_code_subagents}}
