Now that you've gathered information from all sources and analyzed the codebase, use that unified product knowledge to create the mission document in `agent-os/product/mission.md` by following these instructions:

{{workflows/planning/create-product-mission}}

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once you've created mission.md, output the following message:

```
✅ I have documented the product mission at `agent-os/product/mission.md`.

Review it to ensure it matches your vision and strategic goals for this product.

NEXT STEP 👉 Run the command, `4-create-roadmap.md`
```
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure the product mission is ALIGNED and DOES NOT CONFLICT with the user's preferences and standards as detailed in the following files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
