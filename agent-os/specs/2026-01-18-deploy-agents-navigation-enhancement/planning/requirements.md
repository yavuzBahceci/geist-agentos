# Requirements: Deploy-Agents Navigation Enhancement

## Feature Request

> "I want two things: first I want to make sure deploy-agents are updating and specializing installed agent-os structure in installed path of user, and when it's done it should navigate to cleanup-agent-os command"

---

## Analysis Findings

### Current Specialization Behavior (Verified ✅)

After reviewing the deploy-agents implementation, the specialization **IS correctly targeting** the installed `agent-os/` directory:

**Evidence from Phase 5 (`5-specialize-shape-spec-and-write-spec.md`):**
```bash
# Writes to agent-os/commands/ (correct)
echo "$SPECIALIZED_SHAPE_SPEC" > agent-os/commands/shape-spec/single-agent/shape-spec.md
```

**Evidence from Phase 11 (`11-adapt-structure-and-finalize.md`):**
```bash
# Operates on agent-os/commands/ (correct)
CMD_DIR="agent-os/commands/${cmd}"
```

**Conclusion**: The specialization is working correctly. No bug fix needed.

### Missing Navigation Phase (Issue Found ❌)

The current `/deploy-agents` command has **13 phases** but **no navigation phase** at the end. Compare to `/adapt-to-product` which has Phase 8 (`8-navigate-to-next-command.md`) that guides users to the next command.

---

## Requirements

### FR-1: Add Navigation Phase to deploy-agents

Add a new **Phase 14** to `/deploy-agents` that:
1. Displays a summary of what was accomplished
2. Lists files that were specialized
3. Guides user to run `/cleanup-agent-os` next

### FR-2: Navigation Content

The navigation phase should display:
```
🎉 deploy-agents Complete!

**Specialization Summary:**
├── Commands specialized: X files
├── Standards specialized: X files  
├── Workflows specialized: X files
├── Agents specialized: X files
└── Reports generated in: agent-os/output/deploy-agents/

**What's Next?**

Your commands are now specialized for your project. To verify the deployment:

👉 Run `/cleanup-agent-os` to validate and clean up the deployment.

This will:
- Verify all placeholders are replaced
- Check for broken references
- Ensure knowledge completeness
- Generate a cleanup report
```

---

## Out of Scope

- Modifying specialization logic (already working correctly)
- Auto-running cleanup-agent-os (user requested Option A: display message only)
- Changes to other commands

---

## Files to Modify

| File | Action |
|------|--------|
| `profiles/default/commands/deploy-agents/single-agent/deploy-agents.md` | Add Phase 14 reference |
| `profiles/default/commands/deploy-agents/single-agent/14-navigate-to-cleanup.md` | Create new file |
