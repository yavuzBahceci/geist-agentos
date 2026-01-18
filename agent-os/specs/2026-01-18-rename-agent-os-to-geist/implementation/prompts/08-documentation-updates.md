# Prompt: Task Group 8 - Documentation Updates

## Objective

Review and update all documentation to ensure consistent "Geist" branding while preserving Agent OS credits.

## Context

After the bulk replacements in Task Groups 4-7, documentation needs a final review to:
1. Ensure all references are updated
2. Verify credits are preserved
3. Fix any broken links or references
4. Update examples and diagrams

## Files to Review

### Priority 1: Root Documentation
1. `README.md`
2. `MANIFEST.md`
3. `COMMANDS-WORKFLOW-MAP.md`

### Priority 2: Profile Documentation
4. `profiles/default/README.md`
5. `profiles/default/docs/COMMAND-FLOWS.md`

### Priority 3: Command References
6. All files in `profiles/default/docs/command-references/`

### Priority 4: Other Documentation
7. Any remaining `.md` files with documentation

## Tasks

### Task 8.1: Review README.md (root)

Check and update:
- [ ] Installation examples use `geist/` folder
- [ ] Folder structure diagrams show `geist/`
- [ ] Command examples use `/cleanup-geist`
- [ ] PRESERVE credit section with "Agent OS" mentions

### Task 8.2: Review profiles/default/README.md

Check and update:
- [ ] All path references use `geist/`
- [ ] All command references use new names
- [ ] PRESERVE credit section

### Task 8.3: Review MANIFEST.md

Check and update:
- [ ] Folder structure references
- [ ] Any path examples

### Task 8.4: Review COMMANDS-WORKFLOW-MAP.md

Check and update:
- [ ] All command names
- [ ] All path references
- [ ] Workflow diagrams

### Task 8.5: Review COMMAND-FLOWS.md

Check and update:
- [ ] All command references
- [ ] All path references
- [ ] Flow diagrams

### Task 8.6: Review command-references/

For each file in `profiles/default/docs/command-references/`:
- [ ] Verify path references
- [ ] Verify command examples
- [ ] Check for any remaining `agent-os` references

### Task 8.7: Final Documentation Sweep

Search for any remaining issues:
```bash
# Find any remaining agent-os references (except credits)
grep -rn "agent-os" profiles/ | grep -v "Agent OS" | grep -v "buildermethods"

# Find any remaining AGENT_OS references
grep -rn "AGENT_OS" profiles/

# Find any remaining agent_os references
grep -rn "agent_os" profiles/
```

## Credit Section Template

Ensure credit sections follow this format:

```markdown
## Credits

Built on foundational concepts from [Agent OS](https://buildermethods.com/agent-os) by Brian Casel. Geist extends these concepts into a complete cognitive architecture for AI-assisted development.
```

## Verification

After completion:

```bash
# Verify no unwanted agent-os references remain:
grep -rn "agent-os/" profiles/ --include="*.md" | grep -v "buildermethods"

# Verify credits are preserved:
grep -rn "Agent OS" profiles/ --include="*.md"
grep -rn "buildermethods.com/agent-os" profiles/ --include="*.md"
```

## Acceptance Criteria

- [ ] All documentation uses "Geist" and "geist/" consistently
- [ ] All command references updated
- [ ] All path examples updated
- [ ] Credit sections preserved in README files
- [ ] No broken internal references
- [ ] Documentation is accurate and readable

## Notes

- This is a REVIEW task - most replacements should already be done
- Focus on catching anything missed by bulk replacements
- Pay special attention to:
  - Code examples in documentation
  - Folder structure diagrams
  - Installation instructions
  - Credit/attribution sections
