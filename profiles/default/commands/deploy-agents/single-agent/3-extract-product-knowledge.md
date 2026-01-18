Now that basepoint knowledge has been extracted, proceed with extracting knowledge from product files by following these instructions:

## Core Responsibilities

1. **Read Product Mission File**: Extract product context, goals, and product-level abstractions
2. **Read Product Roadmap File**: Extract product direction, planned features, priorities, and phases
3. **Read Tech Stack File**: Extract technology choices, constraints, and technology-specific patterns
4. **Extract Product-Level Abstractions**: Understand how product-level abstraction relates to software project-level abstraction
5. **Prepare for Merging**: Organize product knowledge ready to merge with basepoints knowledge

## Workflow

### Step 1: Read Product Mission File

Read and extract knowledge from `geist/product/mission.md`:

```bash
if [ -f "geist/product/mission.md" ]; then
    MISSION_CONTENT=$(cat geist/product/mission.md)
    echo "✅ Reading product mission file"
    
    # Extract product context and goals
    PRODUCT_NAME=$(echo "$MISSION_CONTENT" | grep -i "^#\|^##" | head -1 | sed 's/#* *//' || echo "")
    PITCH=$(echo "$MISSION_CONTENT" | grep -A 5 "^## Pitch" | grep -v "^##" | head -2 || echo "")
    
    # Extract product-level abstractions
    TARGET_USERS=$(echo "$MISSION_CONTENT" | grep -A 50 "^## Users" | grep -B 50 "^## " | head -n -1 || echo "")
    PROBLEM_STATEMENT=$(echo "$MISSION_CONTENT" | grep -A 20 "^## The Problem" | grep -B 20 "^## " | head -n -1 || echo "")
    DIFFERENTIATORS=$(echo "$MISSION_CONTENT" | grep -A 30 "^## Differentiators" | grep -B 30 "^## " | head -n -1 || echo "")
    KEY_FEATURES=$(echo "$MISSION_CONTENT" | grep -A 50 "^## Key Features" | grep -B 50 "^## " | head -n -1 || echo "")
fi
```

Extract from mission.md:
- **Product Context**: Product name, pitch, purpose, value proposition
- **Product Goals**: Problems being solved, desired outcomes, user benefits
- **Product-Level Abstractions**: How product is positioned, who it serves, what problems it solves
- **User Context**: Target users, personas, use cases, pain points
- **Product Vision**: Differentiators, key features, strategic positioning

Understand how product relates to software project structure:
- How product mission translates to architectural decisions
- How user needs influence code organization
- How product features map to software modules/components

### Step 2: Read Product Roadmap File

Read and extract knowledge from `geist/product/roadmap.md`:

```bash
if [ -f "geist/product/roadmap.md" ]; then
    ROADMAP_CONTENT=$(cat geist/product/roadmap.md)
    echo "✅ Reading product roadmap file"
    
    # Extract product direction and planned features
    PRODUCT_DIRECTION=$(echo "$ROADMAP_CONTENT" | grep -A 30 "^## Roadmap\|^## Direction\|^## Vision" | grep -B 30 "^## " | head -n -1 || echo "")
    
    # Extract product priorities and phases
    PRIORITIES=$(echo "$ROADMAP_CONTENT" | grep -A 50 -i "priority\|phase\|milestone\|sprint" || echo "")
    PHASES=$(echo "$ROADMAP_CONTENT" | grep -A 100 -i "^##.*Phase\|^##.*Milestone\|^##.*Release" || echo "")
    
    # Extract planned features
    PLANNED_FEATURES=$(echo "$ROADMAP_CONTENT" | grep -A 100 "^##.*Feature\|^##.*Roadmap" || echo "")
    
    # Extract product evolution context
    CURRENT_STATE=$(echo "$ROADMAP_CONTENT" | grep -A 30 -i "current\|now\|today" || echo "")
    FUTURE_STATE=$(echo "$ROADMAP_CONTENT" | grep -A 30 -i "future\|planned\|upcoming\|next" || echo "")
fi
```

Extract from roadmap.md:
- **Product Direction**: Strategic direction, vision, goals, objectives
- **Planned Features**: Features in development, upcoming features, feature roadmap
- **Product Priorities**: Priority features, critical milestones, important phases
- **Product Phases**: Development phases, release phases, milestone phases
- **Product Evolution Context**: Current state, planned state, evolution trajectory

Understand product evolution context:
- How product is evolving over time
- What features are currently prioritized
- How roadmap influences technical architecture decisions

### Step 3: Read Tech Stack File

Read and extract knowledge from `geist/product/tech-stack.md`:

```bash
if [ -f "geist/product/tech-stack.md" ]; then
    TECH_STACK_CONTENT=$(cat geist/product/tech-stack.md)
    echo "✅ Reading tech stack file"
    
    # Extract technology choices and constraints
    TECHNOLOGIES=$(echo "$TECH_STACK_CONTENT" | grep -A 200 "^##.*Technology\|^##.*Stack\|^##.*Tech" || echo "")
    FRAMEWORKS=$(echo "$TECH_STACK_CONTENT" | grep -A 100 -i "framework\|library" || echo "")
    CONSTRAINTS=$(echo "$TECH_STACK_CONTENT" | grep -A 50 -i "constraint\|limitation\|requirement" || echo "")
    
    # Extract technology-specific patterns and conventions
    PATTERNS=$(echo "$TECH_STACK_CONTENT" | grep -A 100 -i "pattern\|convention\|best.*practice" || echo "")
    STANDARDS=$(echo "$TECH_STACK_CONTENT" | grep -A 100 -i "standard\|guideline\|style" || echo "")
    
    # Extract architecture decisions influenced by tech stack
    ARCHITECTURE=$(echo "$TECH_STACK_CONTENT" | grep -A 100 -i "architecture\|structure\|organization" || echo "")
fi
```

Extract from tech-stack.md:
- **Technology Choices**: Programming languages, frameworks, libraries, tools, databases
- **Technology Constraints**: Platform limitations, performance constraints, compatibility requirements
- **Technology-Specific Patterns**: Patterns specific to chosen technologies, framework conventions
- **Technology Conventions**: Coding conventions for chosen stack, framework best practices
- **Architecture Decisions**: How tech stack influences architecture, structural decisions based on technologies

Understand technology stack influence on project structure:
- How chosen technologies influence folder structure
- How frameworks affect code organization
- How technology constraints influence patterns and standards

### Step 4: Extract Product-Level Abstractions

Extract how product-level abstraction relates to software project-level abstraction:

```bash
# Analyze relationship between product and software project
PRODUCT_ABSTRACTIONS=""

# Extract from mission: Product purpose and user needs
PRODUCT_PURPOSE=$(echo "$MISSION_CONTENT" | grep -A 10 "^## Pitch\|^## Purpose" || echo "")
USER_NEEDS=$(echo "$MISSION_CONTENT" | grep -A 20 "^## Users\|^## The Problem" || echo "")

# Extract from roadmap: Product features and evolution
PRODUCT_FEATURES=$(echo "$ROADMAP_CONTENT" | grep -A 50 "^##.*Feature\|^##.*Capability" || echo "")
PRODUCT_EVOLUTION=$(echo "$ROADMAP_CONTENT" | grep -A 30 -i "evolution\|growth\|expansion" || echo "")

# Extract from tech-stack: Technology constraints and capabilities
TECH_CONSTRAINTS=$(echo "$TECH_STACK_CONTENT" | grep -A 30 -i "constraint\|limitation" || echo "")
TECH_CAPABILITIES=$(echo "$TECH_STACK_CONTENT" | grep -A 30 -i "capability\|strength\|advantage" || echo "")

# Understand how product abstractions relate to software abstractions
# Product-level: Users, features, business value
# Software-level: Modules, components, code structure
RELATIONSHIP_ANALYSIS="
Product-level abstractions (from product files):
- Purpose: [extracted from mission]
- Users: [extracted from mission]
- Features: [extracted from roadmap]
- Technology: [extracted from tech-stack]

Software-level abstractions (from basepoints):
- Modules: [from basepoints analysis]
- Patterns: [from basepoints patterns]
- Structure: [from basepoints structure]

Relationship mapping:
- Product features → Software modules/components
- User needs → Code organization patterns
- Technology choices → Project structure decisions
"
```

Extract product-level abstractions:
- **Product Purpose**: Why the product exists, what problems it solves
- **User Needs**: Who uses the product, what they need, their workflows
- **Product Features**: What the product does, key capabilities, functionality
- **Technology Context**: How technology choices influence product capabilities

Extract how product-level abstraction relates to software project-level abstraction:
- **Feature-to-Module Mapping**: How product features map to software modules
- **User-Need-to-Pattern Mapping**: How user needs influence code patterns and organization
- **Technology-to-Structure Mapping**: How technology choices influence project structure
- **Product-Context-to-Commands**: How product context should influence command specialization

Extract product context that should influence command specialization:
- **Mission Context**: How product mission should influence command patterns
- **Roadmap Context**: How product roadmap should influence command structure
- **Tech-Stack Context**: How tech stack should influence command standards and patterns

### Step 5: Prepare Product Knowledge for Merging

Organize extracted product knowledge into structured format ready for merging with basepoints knowledge:

```bash
# Create structured product knowledge
PRODUCT_KNOWLEDGE="{
  \"mission\": {
    \"product_name\": \"[extracted]\",
    \"pitch\": \"[extracted]\",
    \"target_users\": \"[extracted]\",
    \"problems\": \"[extracted]\",
    \"differentiators\": \"[extracted]\",
    \"key_features\": \"[extracted]\"
  },
  \"roadmap\": {
    \"direction\": \"[extracted]\",
    \"planned_features\": \"[extracted]\",
    \"priorities\": \"[extracted]\",
    \"phases\": \"[extracted]\",
    \"evolution_context\": \"[extracted]\"
  },
  \"tech_stack\": {
    \"technologies\": \"[extracted]\",
    \"frameworks\": \"[extracted]\",
    \"constraints\": \"[extracted]\",
    \"patterns\": \"[extracted]\",
    \"standards\": \"[extracted]\",
    \"architecture_influence\": \"[extracted]\"
  },
  \"product_abstractions\": {
    \"product_purpose\": \"[extracted]\",
    \"user_needs\": \"[extracted]\",
    \"feature_to_module_mapping\": \"[extracted]\",
    \"technology_to_structure_mapping\": \"[extracted]\",
    \"command_specialization_context\": \"[extracted]\"
  }
}"

# Store in cache for merging phase
mkdir -p geist/output/deploy-agents/knowledge
echo "$PRODUCT_KNOWLEDGE" > geist/output/deploy-agents/knowledge/product-knowledge.json

# Create markdown summary for reference
cat > geist/output/deploy-agents/knowledge/product-knowledge-summary.md << 'EOF'
# Extracted Product Knowledge

## Mission Context
[Summary of product mission, purpose, users, problems, features]

## Roadmap Context
[Summary of product direction, planned features, priorities, phases]

## Tech Stack Context
[Summary of technology choices, constraints, patterns, standards]

## Product-Level Abstractions
[Summary of how product abstractions relate to software project abstractions]

## Command Specialization Context
[How product context should influence command specialization]
EOF
```

Organize product knowledge:
- **By Source**: Mission, roadmap, tech-stack organized separately
- **By Category**: Context, direction, constraints, patterns extracted per file
- **For Merging**: Structured format ready to merge with basepoints knowledge
- **For Specialization**: Context organized to influence command specialization

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once product knowledge extraction is complete, output the following message:

```
✅ Product knowledge extraction complete!

- Mission file: Extracted product context, goals, and product-level abstractions
- Roadmap file: Extracted product direction, planned features, priorities, and phases
- Tech stack file: Extracted technology choices, constraints, and patterns
- Product abstractions: Mapped product-level abstractions to software project-level abstractions

Product knowledge organized and ready for merging.

NEXT STEP 👉 Run the command, `4-merge-knowledge-and-resolve-conflicts.md`
```
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure that your product knowledge extraction process aligns with the user's preferences and standards as detailed in the following files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}

## Important Constraints

- Must read all three product files: mission.md, roadmap.md, tech-stack.md
- Must extract product context, direction, and tech stack comprehensively
- Must understand product-level abstractions and their relationship to software project-level abstractions
- Must extract product context that will influence command specialization
- Must organize product knowledge in structured format ready for merging
- Must preserve source information (which product file each piece of knowledge came from) for conflict resolution
