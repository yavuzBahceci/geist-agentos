# Spec Initialization

## Feature Description

Comprehensive audit and improvement of `profiles/default/` to:

1. **Investigate Latest Updates**: Review all workflows and commands for missing pieces, wrong references, missing orders, and overall quality
2. **Validate Installed Agent-OS Behavior**: Ensure the templates produce correct behavior when installed
3. **Standards Compliance**: Verify all files respect standards, are clean, and well-documented
4. **Professional GitHub Presentation**: Improve documentation with visuals to help developers understand value immediately
5. **Knowledge Consolidation**: Investigate duplication between `config/enriched-knowledge/` and `basepoints/libraries/` - determine if both are needed or should be merged

## Key Questions to Address

### Duplication Analysis
- `config/enriched-knowledge/` contains: library-research.md, stack-best-practices.md, security-notes.md, version-analysis.md, domain-knowledge.md
- `basepoints/libraries/` contains: per-library basepoints with patterns, workflows, best practices, troubleshooting

**Are these duplicating?**
- `enriched-knowledge/library-research.md` vs `basepoints/libraries/[category]/[library].md`
- Both contain best practices, known issues, patterns

**Where is each used?**
- `enriched-knowledge/` → Used by `deploy-agents` for specialization hints, security checks
- `basepoints/libraries/` → Used by spec/implementation commands for context enrichment

## Date Created

2026-01-18
