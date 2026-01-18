# Prompt 1: Create Navigation Phase File

## Task
Create the navigation phase file for `/deploy-agents` that guides users to run `/cleanup-agent-os` after deployment completes.

## File to Create
`profiles/default/commands/deploy-agents/single-agent/14-navigate-to-cleanup.md`

## Reference Pattern
Follow the structure of `profiles/default/commands/adapt-to-product/single-agent/8-navigate-to-next-command.md`:

```markdown
The adapt-to-product process is now complete. This final phase provides a summary of what was accomplished and guidance on next steps.

## Summary of Accomplishments

Display a comprehensive summary of the adapt-to-product command results:

### Files Created
...

### Process Completed
1. ✅ Phase name
2. ✅ Phase name
...

## Next Steps
...

## Output

Display the following completion message:
```

## Content Requirements

Create the file with this structure:

```markdown
The deploy-agents process is now complete. This final phase provides a summary of what was accomplished and guidance on next steps.

## Summary of Accomplishments

Display a comprehensive summary of the deploy-agents command results:

### Phases Completed

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

### Files Specialized

| Category | Location | Description |
|----------|----------|-------------|
| Commands | `agent-os/commands/` | shape-spec, write-spec, create-tasks, implement-tasks, orchestrate-tasks |
| Standards | `agent-os/standards/` | Validation commands, coding standards |
| Workflows | `agent-os/workflows/` | Project-specific workflows |
| Agents | `agent-os/agents/` | Agent configurations |

### Reports Generated

Reports are available in `agent-os/output/deploy-agents/`:
- Complexity assessment
- Specialization report
- Deployment summary

## Next Steps

Now that your Agent OS is specialized, the recommended next step is to validate the deployment and clean up any remaining issues.

### Recommended Command

Run the **cleanup-agent-os** command to:
- Verify all placeholders are properly replaced
- Check for broken file references
- Ensure knowledge completeness
- Generate a cleanup report

## Output

Display the following completion message:

(Include ASCII art banner similar to adapt-to-product showing:
- DEPLOY-AGENTS COMPLETE header
- Tree structure of specialized directories
- What's Next section pointing to /cleanup-agent-os
- Brief explanation of what cleanup does)

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

Ensure the navigation guidance follows the user's standards and preferences as documented in these files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
```

## Acceptance Criteria
- [ ] File created at correct path
- [ ] All 13 phases listed with checkmarks
- [ ] Specialized directories listed in table format
- [ ] Reports location mentioned
- [ ] Next command (`/cleanup-agent-os`) clearly indicated
- [ ] Cleanup purpose explained (4 bullet points)
- [ ] Follows same pattern as adapt-to-product navigation phase
- [ ] Includes standards compliance section at end
