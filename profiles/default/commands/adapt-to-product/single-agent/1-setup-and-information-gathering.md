This begins a multi-step process for adapting an existing codebase into product documentation.

## Step 0: Automatic Project Detection & Research (NEW)

Before gathering product information, automatically detect project characteristics and enrich with web research:

```bash
echo "═══════════════════════════════════════════════════════"
echo "  ADAPTIVE PROJECT DETECTION"
echo "═══════════════════════════════════════════════════════"
echo ""
```

### 0.1: Run Project Detection

Automatically analyze the codebase to detect tech stack, commands, architecture, and security level:

```bash
{{workflows/detection/detect-project-profile}}
```

This will:
- Detect language, framework, and database from config files
- Extract build/test/lint commands from package.json, Makefile, etc.
- Analyze directory structure for project type and architecture
- Check for security indicators (auth, secrets management)
- Calculate overall detection confidence

### 0.2: Enrich with Web Research

Research best practices and patterns for the detected tech stack:

```bash
# Set research depth (minimal for quick setup, standard for most projects)
RESEARCH_DEPTH="${RESEARCH_DEPTH:-standard}"

{{workflows/research/research-orchestrator}}
```

This will:
- Research best practices for detected libraries
- Gather architecture patterns for your tech stack
- Check for security vulnerabilities and updates
- Store findings in `geist/config/enriched-knowledge/`

### 0.3: Present Findings and Confirm

Display detected configuration and ask for confirmation:

```bash
{{workflows/detection/present-and-confirm}}
```

### 0.4: Ask Minimal Questions

Only ask questions that cannot be detected from the codebase:

```bash
{{workflows/detection/question-templates}}
```

Questions asked (maximum 2-3):
1. **Compliance requirements** - GDPR, HIPAA, SOC 2, etc. (can't detect from code)
2. **Human review preference** - How much AI oversight do you want?

The profile is saved to `geist/config/project-profile.yml` for use by subsequent commands.

---

## Step 1: Gather Product Information

The FIRST STEP is to set up folders and gather comprehensive product information from multiple sources by following these instructions:

{{workflows/planning/gather-product-info-from-codebase}}

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once you've completed setup and information gathering, output the following message:

```
I have gathered product information from all available sources.

📊 Project Profile: Saved to geist/config/project-profile.yml
📚 Enriched Knowledge: Saved to geist/config/enriched-knowledge/

NEXT STEP 👉 Run the command, `2-analyze-codebase.md`
```
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

When gathering product information, use the user's standards and preferences for context and baseline assumptions, as documented in these files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
