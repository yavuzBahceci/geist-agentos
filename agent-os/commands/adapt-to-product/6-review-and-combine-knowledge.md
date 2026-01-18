Now that all product documents have been created, review and combine the knowledge to ensure consistency and completeness.

## Step 1: Load All Product Documents

Read and analyze all created product documents:

```bash
# Load all product files
MISSION=$(cat agent-os/product/mission.md 2>/dev/null)
ROADMAP=$(cat agent-os/product/roadmap.md 2>/dev/null)
TECH_STACK=$(cat agent-os/product/tech-stack.md 2>/dev/null)

# Verify all files exist
if [ -z "$MISSION" ] || [ -z "$ROADMAP" ] || [ -z "$TECH_STACK" ]; then
    echo "⚠️  Warning: Some product files are missing"
fi
```

## Step 2: Verify Consistency Between Documents

Analyze the documents together to verify consistency:

1. **Mission-Roadmap Alignment**: Verify roadmap features support the mission goals
2. **Roadmap-Tech Stack Alignment**: Verify tech stack supports roadmap features
3. **Mission-Tech Stack Alignment**: Verify tech choices align with product vision

Check for:
- Contradictions between documents
- Missing connections between mission goals and roadmap items
- Tech stack gaps that would block roadmap features
- Terminology consistency across all documents

## Step 3: Identify Gaps or Issues

Document any issues found:
- Missing information that should be added
- Inconsistencies that need resolution
- Questions that need user clarification

## Step 4: Create Product Summary Report

Create a summary document that combines insights from all product files:

```bash
mkdir -p agent-os/output/adapt-to-product/reports

cat > agent-os/output/adapt-to-product/reports/product-summary.md << 'EOF'
# Product Documentation Summary

## Documents Created
- `agent-os/product/mission.md` - Product mission and vision
- `agent-os/product/roadmap.md` - Development roadmap and phases
- `agent-os/product/tech-stack.md` - Technical stack documentation

## Consistency Analysis

### Mission-Roadmap Alignment
[Summary of how roadmap features support mission goals]

### Tech Stack Coverage
[Summary of how tech stack supports all planned features]

### Cross-Document Consistency
[Summary of terminology and concept consistency]

## Identified Gaps (if any)
[List any gaps or missing information]

## Recommendations
[Suggestions for improvements or additions]

## Next Steps
Ready to proceed with codebase analysis and basepoints generation.
EOF
```

## Display confirmation and next step

Once you've reviewed and combined the knowledge, output the following message:

```
✅ I have reviewed and combined all product knowledge.

**Consistency Check Results:**
- Mission-Roadmap alignment: [status]
- Tech stack coverage: [status]
- Cross-document consistency: [status]

**Summary report created:** `agent-os/output/adapt-to-product/reports/product-summary.md`

NEXT STEP 👉 Run the command, `7-navigate-to-next-command.md`
```

## User Standards & Preferences Compliance

Ensure the review process follows the user's standards and preferences as documented in these files:

@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md
