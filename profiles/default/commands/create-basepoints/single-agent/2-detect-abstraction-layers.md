Now that prerequisites are validated, proceed with detecting abstraction layers in the codebase.

{{workflows/codebase-analysis/detect-abstraction-layers}}

{{UNLESS compiled_single_command}}
## Display confirmation and next step
{{ENDUNLESS compiled_single_command}}

Once abstraction layers are detected and confirmed, output:

```
✅ Abstraction layers detected and confirmed!

**Architecture**: [type]
**Layers identified**: [number] layers
- [Layer 1 name]
- [Layer 2 name]
[Continue listing]

NEXT STEP 👉 Run the command, `3-mirror-project-structure.md`
```

## User Standards & Preferences Compliance

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure that your layer detection aligns with the user's preferences and standards as detailed in the following files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
