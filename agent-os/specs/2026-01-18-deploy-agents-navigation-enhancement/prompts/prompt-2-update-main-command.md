# Prompt 2: Update Main Command File

## Task
Add Phase 14 reference to the main deploy-agents command file.

## File to Modify
`profiles/default/commands/deploy-agents/single-agent/deploy-agents.md`

## Current State (Lines 37-40)
```markdown
{{PHASE 12: @agent-os/commands/deploy-agents/12-optimize-prompts.md}}

{{PHASE 13: @agent-os/commands/deploy-agents/13-apply-prompt-optimizations.md}}
```

## Required Change
Add Phase 14 after Phase 13:

```markdown
{{PHASE 12: @agent-os/commands/deploy-agents/12-optimize-prompts.md}}

{{PHASE 13: @agent-os/commands/deploy-agents/13-apply-prompt-optimizations.md}}

{{PHASE 14: @agent-os/commands/deploy-agents/14-navigate-to-cleanup.md}}
```

## Implementation
Use string replacement to add the new phase reference after line 39.

## Acceptance Criteria
- [ ] Phase 14 reference added after Phase 13
- [ ] Reference follows existing pattern (`{{PHASE N: @agent-os/commands/deploy-agents/...}}`)
- [ ] File path is correct (`14-navigate-to-cleanup.md`)
- [ ] Blank line before Phase 14 (consistent with other phases)
