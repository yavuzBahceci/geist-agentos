# Geist v0.0.1 - Initial Release

<p align="center">
  <strong>A Mind for Agentic Development</strong>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT"></a>
  <a href="#quick-start"><img src="https://img.shields.io/badge/setup-~20min-green.svg" alt="Setup: ~20min"></a>
  <a href="profiles/default/docs/COMMAND-FLOWS.md"><img src="https://img.shields.io/badge/commands-13-orange.svg" alt="Commands: 13"></a>
</p>

---

## What is Geist?

Geist is a **cognitive architecture for AI coding assistants**. It transforms stateless AI tools into intelligent agents that understand your codebase, remember context across sessions, and follow structured workflows.

**The Problem**: Standard AI coding tools are stateless. Rules files are static. Context is lost between conversations. You explain your patterns every prompt.

**The Solution**: Geist gives your AI assistant a **mind**—persistent memory (basepoints), structured workflows (commands), and accumulated knowledge that flows between every interaction.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   STANDARD AI TOOLS                        GEIST                            │
│   ──────────────────                       ─────                            │
│                                                                             │
│   Rules files (static)          →          Basepoints (living, auto-gen)   │
│   Context window (limited)      →          Knowledge system (accumulated)  │
│   Ad-hoc prompts               →          Structured workflows             │
│   Results vary wildly          →          Consistent, validated output     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Highlights

### 13 Specialized Commands

| Category | Commands | Purpose |
|----------|----------|---------|
| **Setup** | `adapt-to-product`, `plan-product`, `create-basepoints`, `deploy-agents` | One-time project specialization |
| **Development** | `shape-spec`, `write-spec`, `create-tasks`, `implement-tasks`, `orchestrate-tasks` | Feature development workflow |
| **Maintenance** | `cleanup-agent-os`, `update-basepoints-and-redeploy` | Keep knowledge in sync |
| **Utility** | `fix-bug` | Systematic bug analysis and fixing |

### Intelligent Knowledge System

- **Basepoints**: Living documentation of your codebase patterns, architecture, and decisions—auto-generated, not manually written
- **Library Basepoints**: Dedicated documentation for your tech stack with usage patterns, best practices, and troubleshooting
- **Knowledge Accumulation**: Context flows between commands—each step builds on the previous
- **Context Enrichment**: "Narrow focus + expand knowledge" strategy for precise, informed AI responses

### Auto-Detection

Geist automatically detects from your config files:
- **Tech stack**: Language, framework, database
- **Build commands**: `npm run build`, `cargo build`, etc.
- **Test commands**: `npm test`, `cargo test`, etc.
- **Lint commands**: `npm run lint`, `cargo clippy`, etc.
- **Architecture**: Module boundaries, layer structure

You only answer 2-3 questions (compliance requirements, review preference).

### Command Chaining

Commands chain together by passing knowledge forward:

```
shape-spec → write-spec → create-tasks → implement-tasks
     │            │            │              │
     └── requirements.md      │              │
                  └── spec.md │              │
                        └── tasks.md         │
                              └── Code changes
```

Each command reads context from previous commands and adds to it. Nothing is lost.

---

## What's Included

### Commands (`profiles/default/commands/`)

```
commands/
├── adapt-to-product/     # Extract product info from existing codebase
├── plan-product/         # Plan new product from scratch
├── create-basepoints/    # Document codebase patterns
├── deploy-agents/        # Specialize commands for your project
├── cleanup-agent-os/     # Verify deployment completeness
├── update-basepoints-and-redeploy/  # Sync after codebase changes
├── shape-spec/           # Research and shape feature requirements
├── write-spec/           # Write detailed specification
├── create-tasks/         # Break spec into actionable tasks
├── implement-tasks/      # Single-agent implementation
├── orchestrate-tasks/    # Multi-agent implementation with prompts
└── fix-bug/              # Systematic bug analysis and fixing
```

### Workflows (`profiles/default/workflows/`)

```
workflows/
├── basepoints/           # Knowledge extraction
├── codebase-analysis/    # Codebase analysis & basepoint generation
│   └── generate-library-basepoints.md
├── common/               # Shared workflows
│   ├── extract-basepoints-with-scope-detection.md
│   ├── extract-library-basepoints-knowledge.md
│   └── accumulate-knowledge.md
├── detection/            # Auto-detection
├── research/             # Web research
│   └── research-library-documentation.md
├── validation/           # Validation utilities
├── specification/        # Spec writing
├── implementation/       # Task implementation
├── learning/             # Session learning
└── cleanup/              # Cleanup workflows
    └── product-focused-cleanup.md
```

### Documentation (`profiles/default/docs/`)

```
docs/
├── COMMAND-FLOWS.md          # Detailed command documentation
├── KNOWLEDGE-SYSTEMS.md      # Knowledge integration explained
├── INSTALLATION-GUIDE.md     # Step-by-step setup
├── PATH-REFERENCE-GUIDE.md   # Path conventions
├── TROUBLESHOOTING.md        # Common issues and solutions
├── REFACTORING-GUIDELINES.md # Template maintenance
├── TECHNOLOGY-AGNOSTIC-BEST-PRACTICES.md
└── command-references/       # Per-command visual guides
```

---

## Quick Start

```bash
# 1. Clone Geist
git clone https://github.com/yavuzBahceci/geist.git ~/geist

# 2. Install in your project
cd /path/to/your/project
~/geist/scripts/project-install.sh --profile default

# 3. Specialize (one-time setup, ~20 min)
/adapt-to-product     # Detects everything, asks 2-3 questions
/create-basepoints    # Documents your codebase
/deploy-agents        # Specializes commands
/cleanup-agent-os     # Validates deployment

# 4. Build a feature
/shape-spec "Add user authentication"
/write-spec
/create-tasks
/implement-tasks

# 5. Review code and iterate
```

---

## Key Features in v0.0.1

### fix-bug Command

A new 6-phase command for systematic bug analysis and fixing:

```
Phase 1: Analyze Issue      → Parse error/feedback, extract details
Phase 2: Research Libraries → Deep-dive into affected libraries
Phase 3: Integrate Basepoints → Extract relevant patterns/standards
Phase 4: Analyze Code       → Identify exact locations, trace execution
Phase 5: Synthesize Knowledge → Combine all analysis, prioritize fixes
Phase 6: Implement Fix      → Iterative fixing with validation
```

Features:
- Iterative fix attempts with validation after each
- Automatic stop after 3 worsening results
- Generates `fix-report.md` on success or `guidance-request.md` if stuck

### Library Basepoints

Dedicated documentation for your tech stack libraries:

```
agent-os/basepoints/libraries/
├── data/           # Databases, ORMs, caching
├── domain/         # Business logic frameworks
├── util/           # Utilities, helpers
├── infrastructure/ # Cloud, DevOps, monitoring
└── framework/      # Core frameworks
```

Each library basepoint includes:
- Internal architecture
- Project-specific usage patterns
- Best practices
- Troubleshooting guide
- Security notes (from enriched knowledge)
- Version status (from enriched knowledge)

### Knowledge Accumulation

Context now flows between commands:

```
shape-spec
  └─► Extracts basepoints, caches knowledge
       └─► accumulated-knowledge.md
            │
write-spec  │
  └─► Loads previous + adds own knowledge
       └─► accumulated-knowledge.md (updated)
            │
create-tasks │
  └─► Loads previous + adds own knowledge
       └─► accumulated-knowledge.md (updated)
            │
implement-tasks
  └─► Uses full accumulated context
```

### Context Enrichment Strategy

All spec/implementation commands now use "narrow focus + expand knowledge":

```
┌─────────────────────────────────────────────────────────────┐
│                  CONTEXT ENRICHMENT                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. NARROW FOCUS                                            │
│     └─► Detect relevant modules for this feature            │
│     └─► Extract only applicable basepoints                  │
│                                                             │
│  2. EXPAND KNOWLEDGE                                        │
│     └─► Add library capabilities/constraints                │
│     └─► Add product context                                 │
│     └─► Add accumulated knowledge from previous commands    │
│                                                             │
│  3. ENRICHED CONTEXT                                        │
│     └─► Precise, comprehensive context for AI               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Global Standards Extraction

`deploy-agents` now classifies patterns as project-wide vs module-specific:

- **Project-wide patterns**: Included in global standards
- **Module-specific patterns**: Documented but excluded from global standards

This prevents standards bloat and keeps global standards focused.

---

## Why Geist Over Standard AI Tools?

| Standard Approach | Problem | Geist Solution |
|-------------------|---------|----------------|
| **Rules files** | Static, generic, manually written | Basepoints: auto-generated, living, specific to your code |
| **Context window** | Limited tokens, lost between sessions | Knowledge system: accumulated, persistent, interconnected |
| **Ad-hoc prompts** | Inconsistent results, no structure | Workflows: structured, validated, repeatable |
| **Memory features** | Shallow, conversation-scoped | Deep, project-scoped, flows between commands |

**The key insight**: Standard tools give AI *instructions*. Geist gives AI *understanding*.

---

## Requirements

- **AI Tool**: Cursor, Claude Desktop, or any AI coding assistant
- **Shell**: Bash-compatible shell
- **Git**: For version control integration
- **Project**: Any codebase (web, mobile, CLI, embedded, etc.)

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on:
- Adding new commands
- Creating workflows
- Writing documentation
- Submitting pull requests

---

## License

MIT License - see [LICENSE](LICENSE) for details.

---

## Credits

Geist builds on the foundational concepts from [Agent OS](https://buildermethods.com/agent-os) by Brian Casel @ Builder Methods—the spec-driven workflow, command structure, and knowledge extraction patterns. Geist extends these ideas into a complete cognitive architecture for agentic development, adding auto-detection, basepoints generation, knowledge accumulation, and support for any project type.

---

## Links

- **Repository**: https://github.com/yavuzBahceci/geist
- **Agent OS**: https://buildermethods.com/agent-os
- **Documentation**: [profiles/default/docs/](profiles/default/docs/)

---

**Release Date**: 2026-01-18

**Full Changelog**: Initial release
