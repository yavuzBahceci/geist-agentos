# Geist - Product Roadmap

## Current State (v2.1.1)

### Completed Features

#### Core Infrastructure
- [x] Profile-based template system (`profiles/default/`)
- [x] Installation scripts (`project-install.sh`, `project-update.sh`)
- [x] Template compilation with conditional logic
- [x] Workflow injection system

#### Commands (Specialization Chain)
- [x] `/adapt-to-product` - Extract product info from codebase (8 phases)
- [x] `/plan-product` - Plan new product from scratch (5 phases)
- [x] `/create-basepoints` - Document codebase patterns
- [x] `/deploy-agents` - Specialize commands for project
- [x] `/cleanup-agent-os` - Validate and clean deployment
- [x] `/update-basepoints-and-redeploy` - Sync after changes

#### Commands (Development Chain)
- [x] `/shape-spec` - Research and gather requirements
- [x] `/write-spec` - Write detailed specification
- [x] `/create-tasks` - Break spec into tasks
- [x] `/implement-tasks` - Single agent implementation
- [x] `/orchestrate-tasks` - Multi-agent orchestration

#### Recent Additions
- [x] Product-Focused Cleanup workflow
- [x] SDD (Spec-Driven Development) integration
- [x] Web search for trusted information (2+ source validation)
- [x] Scope detection and filtering

### Known Limitations
- No package.json/Cargo.toml (shell-based project)
- Limited automated testing
- Documentation could be more comprehensive

---

## Phase 1: Foundation Improvements

### 1.1 Hierarchical Basepoints (Planned)
- [ ] Folder-based basepoint generation (not module-based)
- [ ] Parent folder basepoints describing sub-structure
- [ ] Abstraction layers from root to leaf
- [ ] Configurable depth control

### 1.2 Detection Enhancements
- [ ] Improve confidence scoring
- [ ] Add more tech stack detectors
- [ ] Better architecture pattern recognition
- [ ] Monorepo support

### 1.3 Validation Improvements
- [ ] Add validation tests for templates
- [ ] Technology-agnostic validation
- [ ] Reference integrity checking

---

## Phase 2: Developer Experience

### 2.1 Installation Experience
- [ ] Interactive installation wizard
- [ ] Profile selection guidance
- [ ] Post-install validation

### 2.2 Command Experience
- [ ] Progress indicators for long operations
- [ ] Better error messages
- [ ] Recovery from partial failures

### 2.3 Documentation
- [ ] Video tutorials
- [ ] Example projects per tech stack
- [ ] Troubleshooting guide

---

## Phase 3: Ecosystem

### 3.1 Profile Library
- [ ] Community-contributed profiles
- [ ] Tech-stack specific profiles (React, Rust, Python)
- [ ] Industry-specific profiles (fintech, healthcare)

### 3.2 Integration
- [ ] VS Code extension
- [ ] GitHub Action for CI validation
- [ ] Claude Code deep integration

### 3.3 Analytics
- [ ] Usage tracking (opt-in)
- [ ] Pattern effectiveness metrics
- [ ] Community benchmarks

---

## Technical Debt

| Item | Priority | Effort |
|------|----------|--------|
| Consolidate duplicate workflows | High | Medium |
| Add unit tests for shell scripts | Medium | High |
| Improve error handling in install scripts | Medium | Low |
| Document all placeholder variables | Low | Medium |

---

## Version History

| Version | Date | Highlights |
|---------|------|------------|
| 2.1.1 | 2026-01 | Product-Focused Cleanup, SDD integration |
| 2.1.0 | 2026-01 | Initial Geist release |
| 2.0.x | 2025 | Original Agent OS (Brian Casel) |
