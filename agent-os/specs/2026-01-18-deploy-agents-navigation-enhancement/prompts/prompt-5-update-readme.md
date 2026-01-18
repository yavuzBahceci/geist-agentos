# Prompt 5: Update Root README.md

## Task
Update the README.md to reflect that deploy-agents now has 14 phases and navigates to cleanup-agent-os.

## File to Modify
`README.md` (root)

## Changes Required

### 1. Update "What It Does" Section (around line 155-163)

Current:
```markdown
/adapt-to-product    # Scans YOUR project, asks 2-3 questions max
                     # + Cleans irrelevant tech, expands relevant patterns
/create-basepoints   # Documents YOUR patterns and architecture
/deploy-agents       # Configures for YOUR specific project

# Then just build
/shape-spec "Add payment processing"
```

Update to:
```markdown
/adapt-to-product    # Scans YOUR project, asks 2-3 questions max
                     # + Cleans irrelevant tech, expands relevant patterns
/create-basepoints   # Documents YOUR patterns and architecture
/deploy-agents       # Configures for YOUR specific project
                     # → Navigates to /cleanup-agent-os when done
/cleanup-agent-os    # Validates deployment, ensures completeness

# Then just build
/shape-spec "Add payment processing"
```

### 2. Update "Getting Started" Section (around line 201-213)

Current:
```markdown
```bash
git clone <repo> ~/geist
cd your-project
~/geist/scripts/project-install.sh --profile default

/adapt-to-product
/create-basepoints  
/deploy-agents

# You're ready
```
```

Update to:
```markdown
```bash
git clone <repo> ~/geist
cd your-project
~/geist/scripts/project-install.sh --profile default

/adapt-to-product
/create-basepoints  
/deploy-agents       # → guides you to cleanup
/cleanup-agent-os    # validates deployment

# You're ready
```
```

## Acceptance Criteria
- [ ] deploy-agents description mentions navigation to cleanup
- [ ] cleanup-agent-os added to command list
- [ ] Getting Started section shows full command chain
