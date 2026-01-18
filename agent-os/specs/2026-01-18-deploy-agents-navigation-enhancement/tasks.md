# Tasks: Deploy-Agents Navigation Enhancement

## Overview

Implementation tasks for adding a navigation phase to `/deploy-agents` that guides users to run `/cleanup-agent-os`.

**Spec**: [spec.md](./spec.md)  
**Complexity**: Low (2 files, simple addition)

---

## Tasks

### Task 1: Create Navigation Phase File

**File**: `profiles/default/commands/deploy-agents/single-agent/14-navigate-to-cleanup.md`

**Action**: Create new file

**Content Requirements**:
- Summary of accomplishments (13 phases completed)
- List of specialized directories
- Reports location
- Next step: `/cleanup-agent-os`
- Brief explanation of what cleanup does

**Implementation Prompt**:
```
Create the file `profiles/default/commands/deploy-agents/single-agent/14-navigate-to-cleanup.md` with the following structure:

1. Header explaining this is the final phase
2. Summary section listing all 13 completed phases with checkmarks
3. Files specialized section showing:
   - agent-os/commands/
   - agent-os/standards/
   - agent-os/workflows/
   - agent-os/agents/
4. Reports generated section pointing to agent-os/output/deploy-agents/
5. Next step section recommending `/cleanup-agent-os`
6. Brief explanation of what cleanup-agent-os does:
   - Verifies placeholders are replaced
   - Checks for broken references
   - Ensures knowledge completeness
   - Generates cleanup report

Follow the pattern established in `profiles/default/commands/adapt-to-product/single-agent/8-navigate-to-next-command.md` for structure and tone.
```

**Acceptance Criteria**:
- [ ] File created at correct path
- [ ] All 13 phases listed
- [ ] Specialized directories listed
- [ ] Next command clearly indicated
- [ ] Cleanup purpose explained

---

### Task 2: Update Main Command File

**File**: `profiles/default/commands/deploy-agents/single-agent/deploy-agents.md`

**Action**: Modify existing file

**Change**: Add Phase 14 reference after Phase 13

**Implementation Prompt**:
```
Modify `profiles/default/commands/deploy-agents/single-agent/deploy-agents.md` to add Phase 14 at the end.

Add the following line after the Phase 13 reference:

{{PHASE 14: @agent-os/commands/deploy-agents/14-navigate-to-cleanup.md}}

This should be added after line 39 (the Phase 13 reference).
```

**Acceptance Criteria**:
- [ ] Phase 14 reference added
- [ ] Reference follows existing pattern
- [ ] File path is correct

---

### Task 3: Update Command Reference Documentation

**File**: `profiles/default/docs/command-references/deploy-agents.md`

**Action**: Modify existing file (or create if missing)

**Implementation Prompt**:
```
Update the deploy-agents command reference to include Phase 14 (Navigate to Cleanup).

Add to the phase list:
- Phase 14: Navigate to Cleanup - Summary and guidance to run /cleanup-agent-os

Update the visual flow diagram to show the navigation to cleanup-agent-os at the end.

Update outputs section to mention the navigation guidance.
```

**Acceptance Criteria**:
- [ ] Phase 14 documented in phase list
- [ ] Visual flow updated
- [ ] Next command clearly indicated

---

### Task 4: Update COMMAND-FLOWS.md

**File**: `profiles/default/docs/COMMAND-FLOWS.md`

**Action**: Modify existing file

**Implementation Prompt**:
```
Update the deploy-agents section in COMMAND-FLOWS.md to:
1. Add Phase 14 to the phase list
2. Update the flow diagram to show navigation to cleanup-agent-os
3. Update the "What's Next" section
```

**Acceptance Criteria**:
- [ ] Phase 14 listed
- [ ] Flow diagram updated
- [ ] Navigation to cleanup-agent-os shown

---

### Task 5: Update Root README.md

**File**: `README.md`

**Action**: Modify existing file

**Implementation Prompt**:
```
Update the README.md deploy-agents section to mention:
1. The command now has 14 phases (was 13)
2. It navigates to cleanup-agent-os upon completion
```

**Acceptance Criteria**:
- [ ] Phase count updated
- [ ] Navigation mentioned

---

### Task 6: Update MANIFEST.md

**File**: `MANIFEST.md`

**Action**: Modify existing file

**Implementation Prompt**:
```
Update the deploy-agents entry in MANIFEST.md to mention it now includes navigation to cleanup-agent-os.
```

**Acceptance Criteria**:
- [ ] Description updated to mention navigation

---

## Task Summary

| # | Task | File | Action | Complexity |
|---|------|------|--------|------------|
| 1 | Create Navigation Phase | `14-navigate-to-cleanup.md` | Create | Simple |
| 2 | Update Main Command | `deploy-agents.md` | Modify | Trivial |
| 3 | Update Command Reference | `command-references/deploy-agents.md` | Modify/Create | Simple |
| 4 | Update COMMAND-FLOWS | `COMMAND-FLOWS.md` | Modify | Simple |
| 5 | Update Root README | `README.md` | Modify | Trivial |
| 6 | Update MANIFEST | `MANIFEST.md` | Modify | Trivial |

**Total Estimated Changes**: 6 files

---

## Verification

After implementation, verify by:
1. Check file exists: `profiles/default/commands/deploy-agents/single-agent/14-navigate-to-cleanup.md`
2. Check `deploy-agents.md` contains Phase 14 reference
3. Check documentation files mention 14 phases and navigation to cleanup
4. Optionally reinstall and run `/deploy-agents` to see the navigation phase
