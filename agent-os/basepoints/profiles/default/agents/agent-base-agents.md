# Basepoint: profiles/default/agents/

## Overview

The `agents/` folder contains agent definitions for multi-agent workflows. Each agent has a specialized role and set of capabilities for specific tasks.

## Purpose

Provides specialized agents for:
- Codebase analysis
- Specification writing
- Task implementation
- Verification and validation

## Structure

```
agents/
├── abstraction-detector.md    # Detect abstraction layers
├── basepoint-generator.md     # Generate basepoint files
├── codebase-analyzer.md       # Analyze codebase patterns
├── headquarter-writer.md      # Write headquarter.md
├── implementation-verifier.md # Verify implementations
├── implementer.md             # Implement code changes
├── product-planner.md         # Plan product documentation
├── prompting-specialist.md    # Optimize prompts
├── spec-initializer.md        # Initialize specifications
├── spec-shaper.md             # Shape requirements
├── spec-verifier.md           # Verify specifications
├── spec-writer.md             # Write specifications
└── tasks-list-creator.md      # Create task lists
```

## Agent Pattern

Each agent definition follows:
```markdown
# [Agent Name]

## Role
[What this agent specializes in]

## Capabilities
- [Capability 1]
- [Capability 2]

## When to Use
[Scenarios where this agent is appropriate]

## Inputs
- [Required inputs]

## Outputs
- [Expected outputs]

## Standards
{{standards/relevant/*}}
```

## Agent Roles

| Agent | Role | Used By |
|-------|------|---------|
| `codebase-analyzer` | Analyze code patterns | create-basepoints |
| `basepoint-generator` | Generate basepoints | create-basepoints |
| `headquarter-writer` | Write project overview | create-basepoints |
| `spec-initializer` | Initialize specs | shape-spec |
| `spec-shaper` | Gather requirements | shape-spec |
| `spec-writer` | Write detailed specs | write-spec |
| `spec-verifier` | Verify spec quality | write-spec |
| `tasks-list-creator` | Create task lists | create-tasks |
| `implementer` | Implement code | implement-tasks |
| `implementation-verifier` | Verify implementation | implement-tasks |
| `product-planner` | Plan product docs | adapt-to-product |
| `abstraction-detector` | Detect layers | create-basepoints |
| `prompting-specialist` | Optimize prompts | deploy-agents |

## Multi-Agent Coordination

In multi-agent mode, commands delegate to specialized agents:
```markdown
{{IF use_claude_code_subagents}}
  Delegate to: @agent-os/agents/codebase-analyzer
{{ENDIF}}
```

## Agent Communication

Agents communicate through:
- Shared cache files in `agent-os/specs/[spec]/implementation/cache/`
- Handoff documents in `agent-os/output/handoff/`
- Standard output locations

## File Count

| Category | Count |
|----------|-------|
| Analysis agents | 3 |
| Spec agents | 4 |
| Implementation agents | 2 |
| Planning agents | 2 |
| Utility agents | 2 |
| **Total** | **13** |
