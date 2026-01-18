# Extracted Knowledge for Specialization

## Source: Project Profile

```yaml
project:
  name: Geist
  type: developer-tool
  category: ai-agent-framework

tech_stack:
  primary_language: shell
  secondary_languages: [markdown, yaml]
  framework: none
  runtime: bash
  database: none

characteristics:
  project_type: cli-tool
  architecture: template-based
  ui_type: none
  api_type: none

commands:
  build: "scripts/project-install.sh --profile default"
  test: none
  lint: none

complexity:
  estimated_level: moderate
```

## Source: Product Mission

- **Vision**: Universal Agent OS for any project type
- **Core Problem**: AI assistants lack project context
- **Solution**: Auto-detection, basepoints, command specialization
- **Target Users**: Solo devs, teams, non-web developers, AI-first developers

## Source: Tech Stack

- **Language**: Shell (Bash) + Markdown
- **Architecture**: Template-based compilation
- **No runtime dependencies**: Pure shell + standard Unix tools
- **Platform**: macOS, Linux, WSL

## Source: Basepoints

### Headquarter Summary
- Template system with profiles
- Commands → Workflows → Standards → Agents hierarchy
- Technology-agnostic by design

### Key Modules
| Module | Files | Purpose |
|--------|-------|---------|
| scripts/ | 4 | Installation automation |
| commands/ | ~50 | Command templates |
| workflows/ | ~80 | Reusable workflows |
| standards/ | 14 | Coding guidelines |
| agents/ | 13 | Agent definitions |
| docs/ | 17 | Documentation |

## Specialization Variables

Based on extracted knowledge:

```bash
# Project Identity
PROJECT_NAME="Geist"
PROJECT_TYPE="cli-tool"
PROJECT_DESCRIPTION="Universal Agent OS for any project"

# Build Commands
PROJECT_BUILD_COMMAND="scripts/project-install.sh --profile default"
PROJECT_TEST_COMMAND="echo 'No tests configured'"
PROJECT_LINT_COMMAND="echo 'No linter configured'"
PROJECT_TYPE_CHECK_COMMAND="echo 'No type checker configured'"

# Paths
BASEPOINTS_PATH="agent-os/basepoints"
PRODUCT_PATH="agent-os/product"
CONFIG_PATH="agent-os/config"
OUTPUT_PATH="agent-os/output"

# Characteristics
HAS_UI=false
HAS_API=false
HAS_DATABASE=false
IS_MONOREPO=false
```

## Conflicts Detected

None - Geist is a meta-project (Agent OS for Agent OS), so specialization is minimal.

## Specialization Strategy

Since Geist is a **template system** that must remain **technology-agnostic**:

1. **Minimal specialization** - Keep templates generic
2. **Self-referential** - Geist documents itself
3. **No tech-specific replacements** - Placeholders stay as examples
4. **Focus on structure** - Document the template system itself
