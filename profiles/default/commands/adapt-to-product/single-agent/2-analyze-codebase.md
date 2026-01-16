Now that you've gathered information from external sources, proceed to analyze the existing codebase to infer product information automatically.

{{workflows/planning/analyze-codebase-for-product}}

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once you've completed codebase analysis and merged all information sources, output the following message:

```
I have analyzed the codebase and merged findings with other information sources.

NEXT STEP 👉 Run the command, `3-create-mission.md`
```
{{ENDUNLESS compiled_single_command}}

## User Standards & Preferences Compliance

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

When analyzing the codebase, use tech-stack agnostic descriptions and follow the user's standards and preferences, as documented in these files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
