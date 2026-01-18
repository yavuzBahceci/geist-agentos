# Basepoints Knowledge: Rename agent-os to geist

## Relevant Patterns

### File Reference Pattern
- Uses `@agent-os/` prefix for internal references
- Paths follow `agent-os/[category]/[subcategory]/file.md` structure

### Variable Naming Pattern
- Environment variables: `AGENT_OS_*` (uppercase with underscores)
- Script variables: `agent_os_*` (lowercase with underscores)
- Path references: `agent-os/` (lowercase with hyphens)

### Command Naming Pattern
- Commands use hyphenated names: `cleanup-agent-os`
- Folder structure mirrors command name

### Documentation Pattern
- Credits section at bottom of README files
- Links to original Agent OS preserved
- Product name "Geist" used throughout except credits

## Affected Modules

1. **Scripts** (`scripts/`)
   - Installation and update scripts
   - Common functions library

2. **Commands** (`profiles/default/commands/`)
   - All command files reference `@agent-os/`
   - `cleanup-agent-os` command needs full rename

3. **Workflows** (`profiles/default/workflows/`)
   - Path references throughout
   - Variable usage in bash snippets

4. **Documentation** (`profiles/default/docs/`)
   - Path examples
   - Command references
   - Installation guides

5. **Standards** (`profiles/default/standards/`)
   - Path references
   - Convention documentation

## Key Files

### Scripts (Critical)
- `scripts/project-install.sh` - Creates target folder
- `scripts/project-update.sh` - Updates existing installation
- `scripts/common-functions.sh` - Shared utilities

### Config
- `config.yml` - Default configuration
- `.gitignore` - Ignore patterns

### Command to Rename
- `profiles/default/commands/cleanup-agent-os/`
- `profiles/default/docs/command-references/cleanup-agent-os.md`
