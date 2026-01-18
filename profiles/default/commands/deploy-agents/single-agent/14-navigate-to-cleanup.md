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
| Commands | `geist/commands/` | shape-spec, write-spec, create-tasks, implement-tasks, orchestrate-tasks |
| Standards | `geist/standards/` | Validation commands, coding standards |
| Workflows | `geist/workflows/` | Project-specific workflows |
| Agents | `geist/agents/` | Agent configurations |

### Reports Generated

Reports are available in `geist/output/deploy-agents/`:
- Complexity assessment
- Specialization report
- Deployment summary

## Next Steps

Now that your Geist is specialized, the recommended next step is to validate the deployment and clean up any remaining issues.

### Recommended Command

Run the **cleanup-geist** command to:
- Verify all placeholders are properly replaced
- Check for broken file references
- Ensure knowledge completeness
- Generate a cleanup report

## Output

Display the following completion message:

```
═══════════════════════════════════════════════════════
  DEPLOY-AGENTS COMPLETE
═══════════════════════════════════════════════════════

🎉 Your Geist commands have been specialized!

**Files Specialized:**
├── geist/commands/      (project-specific commands)
│   ├── shape-spec/
│   ├── write-spec/
│   ├── create-tasks/
│   ├── implement-tasks/
│   └── orchestrate-tasks/
├── geist/standards/     (project-specific standards)
├── geist/workflows/     (project-specific workflows)
└── geist/agents/        (project-specific agents)

**Reports Generated:**
└── geist/output/deploy-agents/
    ├── complexity-assessment.json
    ├── specialization-report.md
    └── deployment-summary.md

═══════════════════════════════════════════════════════
  NEXT STEP
═══════════════════════════════════════════════════════

👉 Run `/cleanup-geist` to validate your deployment.

This will:
• Verify all placeholders are properly replaced
• Check for broken file references
• Ensure knowledge completeness
• Generate a cleanup report

After cleanup, your Geist is ready to use!
```

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

Ensure the navigation guidance follows the user's standards and preferences as documented in these files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
