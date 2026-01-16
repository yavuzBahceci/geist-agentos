Now that the project structure is mirrored, proceed with comprehensive codebase analysis to extract patterns, standards, flows, strategies, and testing approaches.

{{workflows/codebase-analysis/analyze-codebase}}

## Display confirmation and next step

Once analysis is complete, output:

```
✅ Codebase analysis complete!

**Files analyzed**: [number] files
**Patterns extracted**: [count] patterns
**Standards identified**: [count] standards
**Flows documented**: [count] flows
**Strategies extracted**: [count] strategies
**Testing approaches**: [count] approaches

Analysis results are ready for basepoint generation.

NEXT STEP 👉 Run the command, `5-generate-module-basepoints.md`
```

## User Standards & Preferences Compliance

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure that your codebase analysis aligns with the user's preferences and standards as detailed in the following files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
