Now that all module and parent basepoints are generated, proceed with generating the headquarter.md file.

# Headquarter File Generation

## Core Responsibilities

1. **Load Product Files**: Read product files (mission.md, roadmap.md, tech-stack.md)
2. **Load Detected Layers**: Retrieve detected abstraction layers from phase 2
3. **Generate Headquarter**: Create headquarter.md at root of basepoints folder
4. **Bridge Abstractions**: Connect product-level abstraction with software project-level abstraction
5. **Document Architecture**: Document overall architecture, abstraction layers, and module relationships

## Workflow

### Step 1: Load Product Files

Load the required product files:

```bash
if [ ! -f "agent-os/product/mission.md" ] || [ ! -f "agent-os/product/roadmap.md" ] || [ ! -f "agent-os/product/tech-stack.md" ]; then
    echo "❌ Product files not found. Cannot generate headquarter."
    exit 1
fi

MISSION_CONTENT=$(cat agent-os/product/mission.md)
ROADMAP_CONTENT=$(cat agent-os/product/roadmap.md)
TECH_STACK_CONTENT=$(cat agent-os/product/tech-stack.md)
```

### Step 2: Load Detected Layers

Load the detected abstraction layers from cache.

### Step 3: Analyze Basepoint Structure

Analyze the generated basepoint structure to understand module relationships:

```bash
TOP_LEVEL_BASEPOINTS=$(find agent-os/basepoints -mindepth 1 -maxdepth 1 -name "agent-base-*.md" | sort)
MODULE_COUNT=$(find agent-os/basepoints -name "agent-base-*.md" | wc -l)
```

### Step 4: Generate Headquarter Content

Create the headquarter.md file with comprehensive content including:
- Product-Level Abstraction (Mission, Roadmap, Tech Stack)
- Software Project-Level Abstraction (Architecture Overview, Detected Layers, Module Structure, Module Relationships)
- Abstraction Bridge (Product → Software Project Mapping, Technology Decisions, Feature → Module Mapping)
- Architecture Patterns
- Standards and Conventions
- Development Workflow
- Testing Strategy
- Key Insights
- Navigation (By Abstraction Layer, By Module)
- References

### Step 5: Populate Headquarter Content

Fill in the headquarter file with actual extracted content from product files and basepoint analysis.

### Step 6: Verify Headquarter File

Verify the headquarter file was generated correctly and contains all required sections.

## Important Constraints

- Must use product files (mission.md, roadmap.md, tech-stack.md) as source
- Must bridge product-level abstraction with software project-level abstraction
- Must include detected abstraction layers
- Must document overall architecture and module relationships
- Must provide navigation to all basepoint files
- Must be placed at root of basepoints folder (`agent-os/basepoints/headquarter.md`)


## Display confirmation and next step

Once headquarter is generated, output:

```
✅ Basepoints generation complete!

**Headquarter file**: agent-os/basepoints/headquarter.md
**Total basepoints**: [number] files (modules + parents)
**Structure**: Complete hierarchy documented
**Abstraction layers**: [number] layers identified

All basepoint documentation is now complete and ready for use!

**Navigation**:
- Start here: agent-os/basepoints/headquarter.md
- Browse by module: agent-os/basepoints/
- Browse by layer: [links organized by abstraction layer]
```

## User Standards & Preferences Compliance

## User Standards & Preferences Compliance

IMPORTANT: Ensure that your headquarter generation aligns with the user's preferences and standards as detailed in the following files:

@agent-os/standards/global/codebase-analysis.md
@agent-os/standards/global/coding-style.md
@agent-os/standards/global/commenting.md
@agent-os/standards/global/conventions.md
@agent-os/standards/global/enriched-knowledge-templates.md
@agent-os/standards/global/error-handling.md
@agent-os/standards/global/project-profile-schema.md
@agent-os/standards/global/tech-stack.md
@agent-os/standards/global/validation-commands.md
@agent-os/standards/global/validation.md
