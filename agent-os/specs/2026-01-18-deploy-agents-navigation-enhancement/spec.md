# Specification: Deploy-Agents Navigation Enhancement

## Overview

Add a navigation phase to the `/deploy-agents` command that guides users to run `/cleanup-agent-os` after deployment completes.

## Goal

Improve the user experience by providing clear guidance on the next step in the setup chain after `/deploy-agents` completes.

## Background

### Current State
- `/deploy-agents` has 13 phases ending with "Apply Prompt Optimizations"
- No summary or navigation guidance is provided
- Users must know to run `/cleanup-agent-os` next

### Desired State
- `/deploy-agents` has 14 phases ending with "Navigate to Cleanup"
- Clear summary of what was accomplished
- Explicit guidance to run `/cleanup-agent-os`

## User Stories

### US-1: Developer Completing Setup
**As a** developer setting up Agent OS  
**I want** clear guidance on what to do after deploy-agents completes  
**So that** I know to run cleanup-agent-os to validate my deployment

**Acceptance Criteria:**
- [ ] Summary of specialized files is displayed
- [ ] Next command (`/cleanup-agent-os`) is clearly indicated
- [ ] Purpose of cleanup-agent-os is briefly explained

## Detailed Requirements

### DR-1: Create Navigation Phase File

**File**: `profiles/default/commands/deploy-agents/single-agent/14-navigate-to-cleanup.md`

**Content Structure**:
1. Summary header
2. List of accomplishments (phases completed)
3. Files created/modified summary
4. Next steps section
5. Command recommendation

### DR-2: Update Main Command File

**File**: `profiles/default/commands/deploy-agents/single-agent/deploy-agents.md`

**Change**: Add Phase 14 reference at the end:
```markdown
{{PHASE 14: @agent-os/commands/deploy-agents/14-navigate-to-cleanup.md}}
```

### DR-3: Navigation Content

The navigation phase should display:

```
═══════════════════════════════════════════════════════
  DEPLOY-AGENTS COMPLETE
═══════════════════════════════════════════════════════

🎉 Your Agent OS commands have been specialized!

**Phases Completed:**
1. ✅ Validate Prerequisites
2. ✅ Extract Basepoints Knowledge
3. ✅ Extract Product Knowledge
4. ✅ Merge Knowledge and Resolve Conflicts
5. ✅ Specialize shape-spec and write-spec
6. ✅ Specialize Task Commands
7. ✅ Update Supporting Structures
8. ✅ Specialize Standards
9. ✅ Specialize Agents
10. ✅ Specialize Workflows
11. ✅ Adapt Structure and Finalize
12. ✅ Optimize Prompts
13. ✅ Apply Prompt Optimizations

**Files Specialized:**
├── agent-os/commands/     (specialized for your project)
├── agent-os/standards/    (specialized for your project)
├── agent-os/workflows/    (specialized for your project)
└── agent-os/agents/       (specialized for your project)

**Reports Generated:**
└── agent-os/output/deploy-agents/

═══════════════════════════════════════════════════════
  NEXT STEP
═══════════════════════════════════════════════════════

👉 Run `/cleanup-agent-os` to validate your deployment.

This will:
• Verify all placeholders are properly replaced
• Check for broken file references
• Ensure knowledge completeness
• Generate a cleanup report

After cleanup, your Agent OS is ready to use!
```

## Technical Design

### Implementation Approach

1. Create new file `14-navigate-to-cleanup.md` following the pattern of `adapt-to-product/8-navigate-to-next-command.md`
2. Add phase reference to main `deploy-agents.md`
3. No changes to existing phases (1-13)

### File Changes

| File | Change Type | Description |
|------|-------------|-------------|
| `profiles/default/commands/deploy-agents/single-agent/deploy-agents.md` | Modify | Add Phase 14 reference |
| `profiles/default/commands/deploy-agents/single-agent/14-navigate-to-cleanup.md` | Create | New navigation phase |

## Testing

### Manual Verification
1. Run `/deploy-agents` on a test project
2. Verify Phase 14 displays after Phase 13
3. Verify summary content is accurate
4. Verify next step guidance is clear

## Rollout

- Changes apply to `profiles/default/`
- Will take effect on next installation or update
- No migration needed for existing installations

## Dependencies

- None (standalone enhancement)

## Risks

- **Low**: Simple addition, no changes to existing logic
- No breaking changes to existing functionality
