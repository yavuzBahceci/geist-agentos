# Geist

### Agent OS, for any project.

---

## Built On Agent OS

**Geist is built on [Agent OS](https://buildermethods.com/agent-os) by Brian Casel @ Builder Methods.**

Agent OS introduced spec-driven agentic development—a structured way to work with AI that actually works. The core concepts (commands, workflows, standards, knowledge extraction) all come from Brian's work.

**Why Geist exists**: I tried Agent OS on a mobile project. The ideas were perfect, but the implementation assumed full-stack web development. Adapting it took more time than building. Geist makes Agent OS work for any project type—mobile, CLI, embedded, whatever you're building.

If you're building full-stack web apps, check out the [original Agent OS](https://buildermethods.com/agent-os) first. If you need something more flexible, that's what Geist is for.

---

## What Geist Does

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│   ANY PROJECT          →    GEIST           →    SPECIALIZED            │
│                                                  AGENT OS               │
│   • Mobile (iOS/Android)     Detects your                               │
│   • Web (any framework)      tech stack         Commands that           │
│   • CLI tools                                   understand YOUR         │
│   • Backend services         Learns your        patterns, not           │
│   • Embedded systems         patterns           assumed patterns        │
│   • Anything else                                                       │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

**Automatically detects** your project—tech stack, build commands, architecture  
**Documents your patterns** into basepoints (the "memory" AI doesn't have)  
**Specializes workflows** so they match how you actually build  
**Validates with your tools**—your build, your tests, your linter

---

## Quick Start

```bash
# 1. Clone Geist
git clone <repository-url> ~/geist

# 2. Install in your project
cd /path/to/your/project
~/geist/scripts/project-install.sh --profile default

# 3. Specialize for your project
/adapt-to-product     # Detects everything, asks 2-3 questions max
/create-basepoints    # Documents your codebase
/deploy-agents        # Configures for your specific project

# 4. Build
/shape-spec "Add payment processing"
# → AI now knows YOUR patterns
```

---

## How It Works

### The Problem It Solves

Every AI conversation starts from zero. The AI doesn't know your codebase, your patterns, your past decisions. You re-explain everything, every time.

### The Solution

```
              YOUR PROJECT
                   │
                   ▼
         ┌─────────────────┐
         │   DETECTION     │  ← Scans config files, analyzes structure
         └────────┬────────┘
                  │
                  ▼
         ┌─────────────────┐
         │   BASEPOINTS    │  ← Documents patterns, decisions, flows
         └────────┬────────┘
                  │
                  ▼
         ┌─────────────────┐
         │ SPECIALIZATION  │  ← Configures commands for YOUR project
         └────────┬────────┘
                  │
                  ▼
         ┌─────────────────┐
         │    AI AGENT     │  ← Now informed, not guessing
         └─────────────────┘
```

Basepoints are the "collective memory" your AI doesn't have. When you run commands like `/shape-spec` or `/implement-tasks`, Geist extracts relevant knowledge and gives it to the AI as context.

---

## Commands

### Setup (run once)

| Command | What it does |
|---------|--------------|
| `/adapt-to-product` | Detects your project, creates product docs |
| `/create-basepoints` | Documents your codebase patterns |
| `/deploy-agents` | Specializes everything for your project |

### Development (ongoing)

| Command | What it does |
|---------|--------------|
| `/shape-spec` | Research and define a feature |
| `/write-spec` | Write detailed specification |
| `/create-tasks` | Break spec into tasks |
| `/implement-tasks` | Implement the tasks |
| `/orchestrate-tasks` | Multi-agent coordination |

### Maintenance

| Command | What it does |
|---------|--------------|
| `/update-basepoints-and-redeploy` | Sync after codebase changes |
| `/cleanup-agent-os` | Verify and clean installation |

---

## Key Features

### Smart Detection

Geist scans your project and figures out:
- Tech stack (from package.json, Cargo.toml, go.mod, etc.)
- Build/test/lint commands
- Project architecture
- Security indicators

It only asks questions it can't answer from your code.

### Basepoints

Documentation that AI can actually use:
- **Patterns**: How you do things
- **Standards**: Your conventions
- **Flows**: How data moves
- **Decisions**: Why you made choices

### Project-Agnostic

Works with any project type, any language, any framework. The same commands work whether you're building iOS apps or Kubernetes operators.

---

## Project Structure

```
geist/
├── profiles/default/          # Project-agnostic templates
│   ├── commands/              # Abstract commands
│   ├── workflows/             # Detection, validation, etc.
│   └── standards/             # Global standards
└── scripts/                   # Installation scripts

your-project/agent-os/         # After installation
├── basepoints/                # Your codebase knowledge
├── product/                   # Your product docs
├── commands/                  # Specialized for YOU
└── config/                    # Detected settings
```

---

## Documentation

| Doc | What it covers |
|-----|----------------|
| [MANIFEST.md](MANIFEST.md) | Why Geist exists, the philosophy |
| [profiles/default/README.md](profiles/default/README.md) | Comprehensive usage guide |
| [profiles/default/docs/COMMAND-FLOWS.md](profiles/default/docs/COMMAND-FLOWS.md) | Detailed command documentation |
| [profiles/default/docs/INSTALLATION-GUIDE.md](profiles/default/docs/INSTALLATION-GUIDE.md) | Step-by-step setup |
| [CHANGELOG.md](CHANGELOG.md) | Version history |

---

## Credits

**Geist is built on [Agent OS](https://buildermethods.com/agent-os) by Brian Casel @ Builder Methods.**

The spec-driven workflow, the command structure, the concept of extracting codebase knowledge—these ideas come from Brian's work. Geist extends them to work for any project type.

### What Geist Adds

- **Project-agnostic architecture**: Works with mobile, CLI, embedded, anything
- **Automatic detection**: Figures out your stack without asking
- **Specialization system**: Transforms abstract templates to match your project
- **Knowledge enrichment**: Web research on your detected libraries

---

## License

MIT

---

*Agent OS made universal.*

**January 2026**
