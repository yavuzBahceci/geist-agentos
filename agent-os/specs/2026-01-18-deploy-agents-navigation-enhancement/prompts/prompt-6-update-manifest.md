# Prompt 6: Update MANIFEST.md

## Task
Update the deploy-agents entry in MANIFEST.md to mention navigation to cleanup-agent-os.

## File to Modify
`MANIFEST.md`

## Context
MANIFEST.md is the "Why Geist Exists" document. It contains a brief mention of deploy-agents in the command list.

## Changes Required

### Find and Update deploy-agents Reference

Look for the deploy-agents mention (around line 157-158):
```markdown
/deploy-agents       # Configures for YOUR specific project
```

Update to:
```markdown
/deploy-agents       # Configures for YOUR specific project
                     # → Navigates to /cleanup-agent-os when done
```

Or if there's a more detailed description, update it to mention that deploy-agents now guides users to run cleanup-agent-os after completion.

## Acceptance Criteria
- [ ] deploy-agents entry updated to mention navigation
- [ ] Consistent with README.md wording
