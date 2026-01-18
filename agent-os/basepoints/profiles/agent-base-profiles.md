# Basepoint: profiles/

## Overview

The `profiles/` folder contains template profiles that can be installed into target projects. Currently, only the `default` profile exists, but the system supports multiple profiles for different use cases.

## Purpose

Provides a profile system for:
- Different project types (web, mobile, CLI, etc.)
- Different team preferences
- Different AI tool integrations
- Custom workflow variations

## Structure

```
profiles/
└── default/              # The default (and currently only) profile
    ├── commands/         # Command templates
    ├── workflows/        # Reusable workflows
    ├── standards/        # Coding standards
    ├── agents/           # Agent definitions
    ├── docs/             # Documentation
    └── README.md         # Profile documentation
```

## Profile Concept

A profile is a complete set of templates that define:
- **Commands**: What AI commands are available
- **Workflows**: How commands execute their logic
- **Standards**: What guidelines AI follows
- **Agents**: What specialists are available (multi-agent)
- **Docs**: How to use the profile

## Current Profiles

| Profile | Status | Purpose |
|---------|--------|---------|
| `default` | ✅ Active | Universal, technology-agnostic profile |

## Future Profile Ideas

| Profile | Purpose |
|---------|---------|
| `web-fullstack` | Optimized for web development |
| `mobile` | Optimized for mobile apps |
| `cli` | Optimized for CLI tools |
| `minimal` | Stripped-down essentials |
| `enterprise` | Additional compliance workflows |

## Profile Selection

Profiles are selected during installation:
```bash
./scripts/project-install.sh --profile default
```

Or configured in `config.yml`:
```yaml
profile: default
```

## Profile Inheritance (Future)

Profiles could inherit from others:
```yaml
# profiles/web-fullstack/profile.yml
inherits: default
overrides:
  - commands/implement-tasks
additions:
  - workflows/frontend-testing
```

## Subfolders

| Folder | Basepoint |
|--------|-----------|
| `default/` | [agent-base-default.md](default/agent-base-default.md) |

## File Statistics

| Profile | Files |
|---------|-------|
| `default/` | ~174 |
| **Total** | **~174** |
