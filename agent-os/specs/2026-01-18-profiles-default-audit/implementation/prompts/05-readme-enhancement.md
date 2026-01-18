# Task Group 5: README Enhancement

## Overview

**Goal**: Make the library look professional on GitHub so developers can easily understand the value and decide to use it immediately.

## Context

The README.md is the first thing developers see. It needs to:
1. Communicate value in seconds
2. Show, not just tell
3. Make getting started trivial
4. Look professional and maintained

## Standards to Follow

- @agent-os/standards/documentation/standards.md
- @agent-os/standards/global/conventions.md

## Tasks

### Task 5.1: Enhance hero section

**File**: `README.md`

**Current state**: Has badges and a comparison diagram

**Improvements needed**:
1. Make value proposition clearer in first 3 lines
2. Add a more compelling tagline
3. Ensure badges show real metrics

**Proposed hero section**:
```markdown
# Geist

<p align="center">
  <strong>Your AI assistant that actually knows your codebase.</strong>
</p>

<p align="center">
  Stop explaining your patterns every prompt. Geist documents them once, injects them automatically.
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT"></a>
  <a href="#quick-start"><img src="https://img.shields.io/badge/setup-~20min-green.svg" alt="Setup: ~20min"></a>
  <a href="profiles/default/docs/COMMAND-FLOWS.md"><img src="https://img.shields.io/badge/commands-13-orange.svg" alt="Commands: 13"></a>
</p>
```

### Task 5.2: Add architecture diagram

**Location**: After "The Core Concept" section

**Diagram to add**:
```markdown
## How It Works

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           YOUR PROJECT                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐      │
│  │   Your Code      │    │   agent-os/      │    │   AI Assistant   │      │
│  │                  │    │                  │    │                  │      │
│  │  src/            │◄───│  basepoints/     │───►│  Knows your:     │      │
│  │  components/     │    │  • patterns      │    │  • Architecture  │      │
│  │  services/       │    │  • standards     │    │  • Patterns      │      │
│  │  ...             │    │  • conventions   │    │  • Conventions   │      │
│  └──────────────────┘    │                  │    │  • Tech stack    │      │
│                          │  commands/       │    │                  │      │
│                          │  • shape-spec    │    │  Generates code  │      │
│                          │  • write-spec    │    │  that fits YOUR  │      │
│                          │  • create-tasks  │    │  codebase        │      │
│                          │  • implement     │    │                  │      │
│                          └──────────────────┘    └──────────────────┘      │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```
```

### Task 5.3: Add feature comparison table

**Location**: After the architecture diagram

**Table to add**:
```markdown
## Why Geist?

| Without Geist | With Geist |
|---------------|------------|
| Explain your patterns every prompt | Patterns documented once, used automatically |
| AI generates generic code | AI generates code matching YOUR style |
| Manual validation | Automatic validation with YOUR commands |
| Context lost between sessions | Context persists in basepoints |
| "Use our auth pattern in src/auth..." | Just run `/shape-spec "Add auth"` |
```

### Task 5.4: Enhance quick start section

**Location**: Make more prominent, possibly move higher

**Improvements**:
1. Reduce to absolute minimum steps
2. Add expected output for each step
3. Verify commands are copy-pasteable

**Proposed quick start**:
```markdown
## Quick Start (5 minutes)

```bash
# 1. Clone Geist
git clone https://github.com/[org]/geist.git ~/geist

# 2. Install in your project
cd /path/to/your/project
~/geist/scripts/project-install.sh --profile default

# 3. Specialize for your project (run these in your AI chat)
/adapt-to-product     # Detects your tech stack (~5 min)
/create-basepoints    # Documents your patterns (~10 min)
/deploy-agents        # Specializes commands (~2 min)

# 4. Build a feature
/shape-spec "Add user authentication"
/write-spec
/create-tasks
/implement-tasks

# That's it! The AI now knows your codebase.
```

**Expected output after `/adapt-to-product`**:
```
✅ Detected: TypeScript, React, Node.js
✅ Found: npm run build, npm test, npm run lint
✅ Created: agent-os/product/tech-stack.md
```
```

### Task 5.5: Add before/after examples

**Location**: After quick start

**Example to add**:
```markdown
## Before vs After

### Before Geist
```
You: "Add a new API endpoint for user profiles. We use Express with 
TypeScript, our routes are in src/routes/, we use Zod for validation,
our error handling pattern is in src/middleware/errorHandler.ts,
we follow REST conventions, our response format is..."

AI: *generates generic code that doesn't match your patterns*

You: "No, we use our custom response wrapper, and the validation 
should use our existing schemas in src/schemas/..."
```

### After Geist
```
You: /shape-spec "Add user profile endpoint"

AI: "I see from your basepoints that you:
- Use Express with TypeScript
- Have existing user schemas in src/schemas/user.ts
- Follow your REST patterns from src/routes/
- Use your errorHandler middleware

Here's the specification..."

You: /implement-tasks

AI: *generates code that matches your existing patterns*
```
```

### Task 5.6: Review and polish overall README

**Checklist**:
- [ ] Check for typos
- [ ] Ensure consistent formatting
- [ ] Verify all internal links work
- [ ] Remove any outdated information
- [ ] Update "Last Updated" date
- [ ] Ensure ASCII diagrams render correctly on GitHub

## Acceptance Criteria

- [ ] Hero section clearly communicates value in first 3 lines
- [ ] Architecture diagram shows how Geist works
- [ ] Feature comparison table highlights benefits
- [ ] Quick start is prominent and takes < 5 minutes to read
- [ ] Before/after example demonstrates value
- [ ] All links work
- [ ] No typos
- [ ] Last Updated date is current

## Files to Modify

1. `README.md`

## Validation

After completing this task group:
1. View README on GitHub (or preview)
2. Time how long it takes to understand what Geist does (target: < 30 seconds)
3. Verify quick start commands work
4. Check all ASCII diagrams render correctly
