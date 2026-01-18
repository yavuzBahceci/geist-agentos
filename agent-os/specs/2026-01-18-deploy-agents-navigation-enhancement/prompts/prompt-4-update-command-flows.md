# Prompt 4: Update COMMAND-FLOWS.md

## Task
Update the deploy-agents section in COMMAND-FLOWS.md to include Phase 12-14.

## File to Modify
`profiles/default/docs/COMMAND-FLOWS.md`

## Changes Required

### 1. Update Phase List (Lines 293-354)

Current phases end at Phase 11. Add Phases 12-14:

After:
```
Phase 11: Adapt Structure & Finalize
├── Run: comprehensive validation
└── Generate: deployment report
```

Add:
```
Phase 12: Optimize Prompts
└── Generate: context sections for commands

Phase 13: Apply Prompt Optimizations
└── Inject: approved context sections into templates

Phase 14: Navigate to Cleanup
├── Display: summary of accomplishments
├── List: specialized directories
└── Recommend: /cleanup-agent-os
```

### 2. Update Outputs Section (Lines 287-292)

Current:
```markdown
**Outputs**:
- Specialized commands in `agent-os/commands/`
- Specialized workflows in `agent-os/workflows/`
- Specialized standards in `agent-os/standards/`
- Configured validation commands
```

Update to:
```markdown
**Outputs**:
- Specialized commands in `agent-os/commands/`
- Specialized workflows in `agent-os/workflows/`
- Specialized standards in `agent-os/standards/`
- Configured validation commands
- Deployment report in `agent-os/output/deploy-agents/`
- Navigation guidance to `/cleanup-agent-os`
```

### 3. Add "What's Next" Note After Phase List

After the phase list, add:
```markdown
#### What's Next

After `/deploy-agents` completes, run `/cleanup-agent-os` to:
- Verify all placeholders are properly replaced
- Check for broken file references
- Ensure knowledge completeness
- Generate a cleanup report
```

## Acceptance Criteria
- [ ] Phases 12-14 added to phase list
- [ ] Outputs section updated
- [ ] "What's Next" section added
- [ ] Navigation to cleanup-agent-os clearly indicated
