# Workflow Reference Audit Report

## Summary

| Metric | Count |
|--------|-------|
| **Total commands audited** | 13 |
| **Total workflow references found** | 130 |
| **Broken references** | 0 |
| **Orphaned workflows** | 5 (intentional) |
| **Phase numbering issues** | 0 |

---

## Commands Audited

### 1. adapt-to-product (8 phases)

**Directory**: `profiles/default/commands/adapt-to-product/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-setup-and-information-gathering.md | 5 references | ✅ All valid |
| 2 | 2-analyze-codebase.md | 1 reference | ✅ Valid |
| 3 | 3-create-mission.md | 1 reference | ✅ Valid |
| 4 | 4-create-roadmap.md | 1 reference | ✅ Valid |
| 5 | 5-create-tech-stack.md | 1 reference | ✅ Valid |
| 6 | 6-review-and-combine-knowledge.md | 0 references | ✅ N/A |
| 7 | 7-product-focused-cleanup.md | 1 reference | ✅ Valid |
| 8 | 8-navigate-to-next-command.md | 0 references | ✅ N/A |

**Workflow references verified:**
- `workflows/detection/detect-project-profile` ✅
- `workflows/research/research-orchestrator` ✅
- `workflows/detection/present-and-confirm` ✅
- `workflows/detection/question-templates` ✅
- `workflows/planning/gather-product-info-from-codebase` ✅
- `workflows/planning/analyze-codebase-for-product` ✅
- `workflows/planning/create-product-mission` ✅
- `workflows/planning/create-product-roadmap` ✅
- `workflows/planning/create-product-tech-stack` ✅
- `workflows/cleanup/product-focused-cleanup` ✅

---

### 2. plan-product (5 phases)

**Directory**: `profiles/default/commands/plan-product/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-product-concept.md | 1 reference | ✅ Valid |
| 2 | 2-create-mission.md | 1 reference | ✅ Valid |
| 3 | 3-create-roadmap.md | 1 reference | ✅ Valid |
| 4 | 4-create-tech-stack.md | 1 reference | ✅ Valid |
| 5 | 5-product-focused-cleanup.md | 1 reference | ✅ Valid |

**Workflow references verified:**
- `workflows/planning/gather-product-info` ✅
- `workflows/planning/create-product-mission` ✅
- `workflows/planning/create-product-roadmap` ✅
- `workflows/planning/create-product-tech-stack` ✅
- `workflows/cleanup/product-focused-cleanup` ✅

---

### 3. create-basepoints (8 phases)

**Directory**: `profiles/default/commands/create-basepoints/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-validate-prerequisites.md | 4 references | ✅ All valid |
| 2 | 2-detect-abstraction-layers.md | 1 reference | ✅ Valid |
| 3 | 3-mirror-project-structure.md | 1 reference | ✅ Valid |
| 4 | 4-analyze-codebase.md | 1 reference | ✅ Valid |
| 5 | 5-generate-module-basepoints.md | 1 reference | ✅ Valid |
| 6 | 6-generate-parent-basepoints.md | 1 reference | ✅ Valid |
| 7 | 7-generate-headquarter.md | 1 reference | ✅ Valid |
| 8 | 8-generate-library-basepoints.md | 1 reference | ✅ Valid |

**Workflow references verified:**
- `workflows/detection/detect-project-profile` ✅
- `workflows/research/research-stack-patterns` ✅
- `workflows/research/research-orchestrator` ✅
- `workflows/codebase-analysis/validate-prerequisites` ✅
- `workflows/codebase-analysis/detect-abstraction-layers` ✅
- `workflows/codebase-analysis/mirror-project-structure` ✅
- `workflows/codebase-analysis/analyze-codebase` ✅
- `workflows/codebase-analysis/generate-module-basepoints` ✅
- `workflows/codebase-analysis/generate-parent-basepoints` ✅
- `workflows/codebase-analysis/generate-headquarter` ✅
- `workflows/codebase-analysis/generate-library-basepoints` ✅

---

### 4. deploy-agents (14 phases)

**Directory**: `profiles/default/commands/deploy-agents/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-validate-prerequisites.md | 2 references | ✅ All valid |
| 2 | 2-extract-basepoints-knowledge.md | 1 reference | ✅ Valid |
| 3 | 3-extract-product-knowledge.md | 0 references | ✅ N/A |
| 4 | 4-merge-knowledge-and-resolve-conflicts.md | 0 references | ✅ N/A |
| 5 | 5-specialize-shape-spec-and-write-spec.md | Multiple (sed replacements) | ✅ Valid |
| 6 | 6-specialize-task-commands.md | Multiple (sed replacements) | ✅ Valid |
| 7 | 7-update-supporting-structures.md | 0 direct references | ✅ N/A |
| 8 | 8-specialize-standards.md | 0 references | ✅ N/A |
| 9 | 9-specialize-agents.md | 2 references | ✅ Valid |
| 10 | 10-specialize-workflows.md | 0 references | ✅ N/A |
| 11 | 11-adapt-structure-and-finalize.md | 1 reference | ✅ Valid |
| 12 | 12-optimize-prompts.md | 0 references | ✅ N/A |
| 13 | 13-apply-prompt-optimizations.md | 0 references | ✅ N/A |
| 14 | 14-navigate-to-cleanup.md | 0 references | ✅ N/A |

**Workflow references verified:**
- `workflows/detection/detect-project-profile` ✅
- `workflows/detection/question-templates` ✅
- `workflows/basepoints/extract-basepoints-knowledge-automatic` ✅
- `workflows/implementation/implement-tasks` ✅
- `workflows/validation/orchestrate-validation` ✅

---

### 5. shape-spec (2 phases)

**Directory**: `profiles/default/commands/shape-spec/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-initialize-spec.md | 1 reference | ✅ Valid |
| 2 | 2-shape-spec.md | 7 references | ✅ All valid |

**Workflow references verified:**
- `workflows/specification/initialize-spec` ✅
- `workflows/common/extract-basepoints-with-scope-detection` ✅
- `workflows/specification/research-spec` ✅
- `workflows/validation/validate-output-exists` ✅
- `workflows/validation/validate-knowledge-integration` ✅
- `workflows/validation/generate-validation-report` ✅
- `workflows/basepoints/organize-and-cache-basepoints-knowledge` ✅
- `workflows/common/accumulate-knowledge` ✅
- `workflows/prompting/save-handoff` ✅

---

### 6. write-spec (1 phase)

**Directory**: `profiles/default/commands/write-spec/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | write-spec.md | 10 references | ✅ All valid |

**Workflow references verified:**
- `workflows/common/extract-basepoints-with-scope-detection` ✅
- `workflows/specification/write-spec` ✅
- `workflows/human-review/review-trade-offs` ✅
- `workflows/validation/validate-output-exists` ✅
- `workflows/validation/validate-knowledge-integration` ✅
- `workflows/validation/validate-references` ✅
- `workflows/validation/generate-validation-report` ✅
- `workflows/basepoints/organize-and-cache-basepoints-knowledge` ✅
- `workflows/common/accumulate-knowledge` ✅
- `workflows/prompting/save-handoff` ✅

---

### 7. create-tasks (2 phases)

**Directory**: `profiles/default/commands/create-tasks/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-get-spec-requirements.md | 0 references | ✅ N/A |
| 2 | 2-create-tasks-list.md | 10 references | ✅ All valid |

**Workflow references verified:**
- `workflows/common/extract-basepoints-with-scope-detection` ✅
- `workflows/human-review/review-trade-offs` ✅
- `workflows/implementation/create-tasks-list` ✅
- `workflows/validation/validate-output-exists` ✅
- `workflows/validation/validate-knowledge-integration` ✅
- `workflows/validation/generate-validation-report` ✅
- `workflows/basepoints/organize-and-cache-basepoints-knowledge` ✅
- `workflows/common/accumulate-knowledge` ✅
- `workflows/prompting/save-handoff` ✅

---

### 8. implement-tasks (3 phases)

**Directory**: `profiles/default/commands/implement-tasks/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-determine-tasks.md | 0 references | ✅ N/A |
| 2 | 2-implement-tasks.md | 11 references | ✅ All valid |
| 3 | 3-verify-implementation.md | 5 references | ✅ All valid |

**Workflow references verified:**
- `workflows/common/extract-basepoints-with-scope-detection` ✅
- `workflows/human-review/review-trade-offs` ✅
- `workflows/human-review/create-checkpoint` ✅
- `workflows/implementation/implement-tasks` ✅
- `workflows/validation/validate-output-exists` ✅
- `workflows/validation/validate-knowledge-integration` ✅
- `workflows/validation/validate-references` ✅
- `workflows/validation/generate-validation-report` ✅
- `workflows/basepoints/organize-and-cache-basepoints-knowledge` ✅
- `workflows/prompting/save-handoff` ✅
- `workflows/implementation/verification/verify-tasks` ✅
- `workflows/implementation/verification/update-roadmap` ✅
- `workflows/implementation/verification/run-all-tests` ✅
- `workflows/implementation/verification/create-verification-report` ✅
- `workflows/learning/capture-session-feedback` ✅

---

### 9. orchestrate-tasks (1 phase)

**Directory**: `profiles/default/commands/orchestrate-tasks/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | orchestrate-tasks.md | 12 references | ✅ All valid |

**Workflow references verified:**
- `workflows/common/extract-basepoints-with-scope-detection` ✅
- `workflows/implementation/compile-implementation-standards` ✅
- `workflows/implementation/implement-tasks` ✅
- `workflows/validation/validate-implementation` ✅
- `workflows/human-review/review-trade-offs` ✅
- `workflows/scope-detection/detect-scope-keyword-matching` ✅
- `workflows/validation/validate-output-exists` ✅
- `workflows/validation/validate-knowledge-integration` ✅
- `workflows/validation/generate-validation-report` ✅
- `workflows/basepoints/organize-and-cache-basepoints-knowledge` ✅
- `workflows/prompting/save-handoff` ✅

---

### 10. fix-bug (6 phases)

**Directory**: `profiles/default/commands/fix-bug/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-analyze-issue.md | 0 references | ✅ N/A |
| 2 | 2-research-libraries.md | 0 references | ✅ N/A |
| 3 | 3-integrate-basepoints.md | 1 reference | ✅ Valid |
| 4 | 4-analyze-code.md | 0 references | ✅ N/A |
| 5 | 5-synthesize-knowledge.md | 0 references | ✅ N/A |
| 6 | 6-implement-fix.md | 1 reference | ✅ Valid |

**Workflow references verified:**
- `workflows/common/extract-basepoints-with-scope-detection` ✅
- `workflows/validation/validate-implementation` ✅

---

### 11. cleanup-agent-os (6 phases)

**Directory**: `profiles/default/commands/cleanup-agent-os/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-validate-prerequisites-and-run-validation.md | 1 reference | ✅ Valid |
| 2 | 2-clean-placeholders.md | 0 references | ✅ N/A |
| 3 | 3-remove-unnecessary-logic.md | 0 references | ✅ N/A |
| 4 | 4-fix-broken-references.md | 0 references | ✅ N/A |
| 5 | 5-verify-knowledge-completeness.md | 0 references | ✅ N/A |
| 6 | 6-generate-cleanup-report.md | 0 references | ✅ N/A |

**Workflow references verified:**
- `workflows/validation/orchestrate-validation` ✅

---

### 12. update-basepoints-and-redeploy (8 phases)

**Directory**: `profiles/default/commands/update-basepoints-and-redeploy/single-agent/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | 1-detect-changes.md | 1 reference | ✅ Valid |
| 2 | 2-identify-affected-basepoints.md | 0 references | ✅ N/A |
| 3 | 3-update-basepoints.md | 4 references | ✅ All valid |
| 4 | 4-re-extract-knowledge.md | 0 references | ✅ N/A |
| 5 | 5-selective-respecialize.md | 0 references | ✅ N/A |
| 6 | 6-validate-and-report.md | 1 reference | ✅ Valid |
| 7 | 7-review-session-learnings.md | 3 references | ✅ All valid |
| 8 | 8-adapt-commands.md | 1 reference | ✅ Valid |

**Workflow references verified:**
- `workflows/codebase-analysis/detect-codebase-changes` ✅
- `workflows/codebase-analysis/incremental-basepoint-update` ✅
- `workflows/codebase-analysis/analyze-codebase` ✅
- `workflows/codebase-analysis/generate-module-basepoints` ✅
- `workflows/codebase-analysis/generate-headquarter` ✅
- `workflows/validation/validate-incremental-update` ✅
- `workflows/learning/extract-session-patterns` ✅
- `workflows/prompting/analyze-prompt-effectiveness` ✅
- `workflows/learning/present-learnings-for-review` ✅
- `workflows/learning/apply-command-adaptations` ✅

---

### 13. improve-skills (1 phase)

**Directory**: `profiles/default/commands/improve-skills/`

| Phase | File | Workflow References | Status |
|-------|------|---------------------|--------|
| 1 | improve-skills.md | 0 references | ✅ N/A |

---

## Broken References

**None found.** All 130 workflow references resolve to existing files.

---

## Orphaned Workflows

The following workflows exist but are not directly referenced by any command. However, they are intentionally kept for specific purposes:

| Workflow Path | Reason Kept |
|---------------|-------------|
| `workflows/deep-reading/detect-reusable-code.md` | Used by other workflows internally |
| `workflows/deep-reading/read-implementation-deep.md` | Used by other workflows internally |
| `workflows/human-review/detect-contradictions.md` | Available for human review scenarios |
| `workflows/human-review/detect-trade-offs.md` | Available for human review scenarios |
| `workflows/human-review/present-human-decision.md` | Available for human review scenarios |

**Recommendation**: Keep these workflows as they serve specific purposes and may be called by other workflows or used in future commands.

---

## Phase Numbering Issues

**None found.** All commands have sequential phase numbering:

| Command | Phase Range | Status |
|---------|-------------|--------|
| adapt-to-product | 1-8 | ✅ Sequential |
| plan-product | 1-5 | ✅ Sequential |
| create-basepoints | 1-8 | ✅ Sequential |
| deploy-agents | 1-14 | ✅ Sequential |
| shape-spec | 1-2 | ✅ Sequential |
| write-spec | 1 (single file) | ✅ N/A |
| create-tasks | 1-2 | ✅ Sequential |
| implement-tasks | 1-3 | ✅ Sequential |
| orchestrate-tasks | 1 (single file) | ✅ N/A |
| fix-bug | 1-6 | ✅ Sequential |
| cleanup-agent-os | 1-6 | ✅ Sequential |
| update-basepoints-and-redeploy | 1-8 | ✅ Sequential |
| improve-skills | 1 (single file) | ✅ N/A |

---

## Recommendations

1. **No immediate action required** - All workflow references are valid and phase numbering is correct.

2. **Consider documenting orphaned workflows** - Add comments in the orphaned workflow files explaining their purpose and when they might be used.

3. **Maintain this audit** - Re-run this audit after significant changes to commands or workflows.

---

## Audit Metadata

| Attribute | Value |
|-----------|-------|
| **Audit Date** | 2026-01-18 |
| **Commands Audited** | 13 |
| **Workflows Verified** | 100+ |
| **Total References Checked** | 130 |
| **Audit Status** | ✅ PASSED |

---

*Generated by Geist Workflow Reference Audit*
