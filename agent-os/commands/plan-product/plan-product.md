You are helping to plan and document the mission, roadmap and tech stack for the current product.  This will include:

- **Gathering Information**: The user's product vision, user personas, problems and key features
- **Mission Document**: Take what you've gathered and create a concise mission document
- **Roadmap**: Create a phased development plan with prioritized features
- **Tech stack**: Establish the technical stack used for all aspects of this product's codebase
- **Product-Focused Cleanup**: Simplify and enhance agent-os files based on detected product scope

Carefully read and execute the instructions in the following files IN SEQUENCE, following their numbered file names.  Only proceed to the next numbered instruction file once the previous numbered instruction has been executed.

Instructions to follow in sequence:

# PHASE 1: Product Concept

This begins a multi-step process for planning and documenting the mission and roadmap for the current product.

The FIRST STEP is to confirm the product details by following these instructions:

Collect comprehensive product information from the user:

```bash
# Check if product folder already exists
if [ -d "agent-os/product" ]; then
    echo "Product documentation already exists. Review existing files or start fresh?"
    # List existing product files
    ls -la agent-os/product/
fi
```

Gather from user the following required information:
- **Product Idea**: Core concept and purpose (required)
- **Key Features**: Minimum 3 features with descriptions
- **Target Users**: At least 1 user segment with use cases
- **Tech stack**: Confirmation or info regarding the product's tech stack choices

If any required information is missing, prompt user:
```
Please provide the following to create your product plan:
1. Main idea for the product
2. List of key features (minimum 3)
3. Target users and use cases (minimum 1)
4. Will this product use your usual tech stack choices or deviate in any way?
```


Then WAIT for me to give you specific instructions on how to use the information you've gathered to create the mission and roadmap.


## User Standards & Preferences Compliance

When planning the product's tech stack, mission statement and roadmap, use the user's standards and preferences for context and baseline assumptions, as documented in these files:

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

# PHASE 2: Create Mission

Now that you've gathered information about this product, use that info to create the mission document in `agent-os/product/mission.md` by following these instructions:

Create `agent-os/product/mission.md` with comprehensive product definition following this structure for its' content:

#### Mission Structure:
```markdown
# Product Mission

## Pitch
[PRODUCT_NAME] is a [PRODUCT_TYPE] that helps [TARGET_USERS] [SOLVE_PROBLEM]
by providing [KEY_VALUE_PROPOSITION].

## Users

### Primary Customers
- [CUSTOMER_SEGMENT_1]: [DESCRIPTION]
- [CUSTOMER_SEGMENT_2]: [DESCRIPTION]

### User Personas
**[USER_TYPE]** ([AGE_RANGE])
- **Role:** [JOB_TITLE/CONTEXT]
- **Context:** [BUSINESS/PERSONAL_CONTEXT]
- **Pain Points:** [SPECIFIC_PROBLEMS]
- **Goals:** [DESIRED_OUTCOMES]

## The Problem

### [PROBLEM_TITLE]
[PROBLEM_DESCRIPTION]. [QUANTIFIABLE_IMPACT].

**Our Solution:** [SOLUTION_APPROACH]

## Differentiators

### [DIFFERENTIATOR_TITLE]
Unlike [COMPETITOR/ALTERNATIVE], we provide [SPECIFIC_ADVANTAGE].
This results in [MEASURABLE_BENEFIT].

## Key Features

### Core Features
- **[FEATURE_NAME]:** [USER_BENEFIT_DESCRIPTION]

### Collaboration Features
- **[FEATURE_NAME]:** [USER_BENEFIT_DESCRIPTION]

### Advanced Features
- **[FEATURE_NAME]:** [USER_BENEFIT_DESCRIPTION]
```

#### Important Constraints

- **Focus on user benefits** in feature descriptions, not technical details
- **Keep it concise** and easy for users to scan and get the more important concepts quickly



## User Standards & Preferences Compliance

IMPORTANT: Ensure the product mission is ALIGNED and DOES NOT CONFLICT with the user's preferences and standards as detailed in the following files:

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

# PHASE 3: Create Roadmap

Now that you've created this product's mission.md, use that to guide your creation of the roadmap in `agent-os/product/roadmap.md` by following these instructions:

Generate `agent-os/product/roadmap.md` with an ordered feature checklist:

Do not include any tasks for initializing a new codebase or bootstrapping a new application. Assume the user is already inside the project's codebase and has a bare-bones application initialized.

#### Creating the Roadmap:

1. **Review the Mission** - Read `agent-os/product/mission.md` to understand the product's goals, target users, and success criteria.

2. **Identify Features** - Based on the mission, determine the list of concrete features needed to achieve the product vision.

3. **Strategic Ordering** - Order features based on:
   - Technical dependencies (foundational features first)
   - Most direct path to achieving the mission
   - Building incrementally from MVP to full product

4. **Create the Roadmap** - Use the structure below as your template. Replace all bracketed placeholders (e.g., `[FEATURE_NAME]`, `[DESCRIPTION]`, `[EFFORT]`) with real content that you create based on the mission.

#### Roadmap Structure:
```markdown
# Product Roadmap

1. [ ] [FEATURE_NAME] — [1-2 SENTENCE DESCRIPTION OF COMPLETE, TESTABLE FEATURE] `[EFFORT]`
2. [ ] [FEATURE_NAME] — [1-2 SENTENCE DESCRIPTION OF COMPLETE, TESTABLE FEATURE] `[EFFORT]`
3. [ ] [FEATURE_NAME] — [1-2 SENTENCE DESCRIPTION OF COMPLETE, TESTABLE FEATURE] `[EFFORT]`
4. [ ] [FEATURE_NAME] — [1-2 SENTENCE DESCRIPTION OF COMPLETE, TESTABLE FEATURE] `[EFFORT]`
5. [ ] [FEATURE_NAME] — [1-2 SENTENCE DESCRIPTION OF COMPLETE, TESTABLE FEATURE] `[EFFORT]`
6. [ ] [FEATURE_NAME] — [1-2 SENTENCE DESCRIPTION OF COMPLETE, TESTABLE FEATURE] `[EFFORT]`
7. [ ] [FEATURE_NAME] — [1-2 SENTENCE DESCRIPTION OF COMPLETE, TESTABLE FEATURE] `[EFFORT]`
8. [ ] [FEATURE_NAME] — [1-2 SENTENCE DESCRIPTION OF COMPLETE, TESTABLE FEATURE] `[EFFORT]`

> Notes
> - Order items by technical dependencies and product architecture
> - Each item should represent an end-to-end functional and testable feature
```

Effort scale:
- `XS`: 1 day
- `S`: 2-3 days
- `M`: 1 week
- `L`: 2 weeks
- `XL`: 3+ weeks

#### Important Constraints

- **Make roadmap actionable** - include effort estimates and dependencies
- **Priorities guided by mission** - When deciding on order, aim for the most direct path to achieving the mission as documented in mission.md
- **Ensure phases are achievable** - start with MVP, build incrementally



## User Standards & Preferences Compliance

IMPORTANT: Ensure the product roadmap is ALIGNED and DOES NOT CONFLICT with the user's preferences and standards as detailed in the following files:

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

# PHASE 4: Create Tech Stack

The final part of our product planning process is to document this product's tech stack in `agent-os/product/tech-stack.md`.  Follow these instructions to do so:

Create `agent-os/product/tech-stack.md` with a list of all tech stack choices that cover all aspects of this product's codebase.

### Creating the Tech Stack document

#### Step 1: Note User's Input Regarding Tech Stack

IF the user has provided specific information in the current conversation in regards to tech stack choices, these notes ALWAYS take precidence.  These must be reflected in your final `tech-stack.md` document that you will create.

#### Step 2: Gather User's Default Tech Stack Information

Reconcile and fill in the remaining gaps in the tech stack list by finding, reading and analyzing information regarding the tech stack.  Find this information in the following sources, in this order:

1. If user has provided their default tech stack under "User Standards & Preferences Compliance", READ and analyze this document.
2. If the current project has any of these files, read them to find information regarding tech stack choices for this codebase:
  - `claude.md`
  - `agents.md`

#### Step 3: Create the Tech Stack Document

Create `agent-os/product/tech-stack.md` and populate it with the final list of all technical stack choices, reconciled between the information the user has provided to you and the information found in provided sources.


## Display confirmation and next step

Once you've created tech-stack.md, output the following message:

```
✅ I have documented the product's tech stack at `agent-os/product/tech-stack.md`.

Review it to ensure all of the tech stack details are correct for this product.

You're ready to start planning a feature spec! You can do so by running `shape-spec.md` or `write-spec.md`.
```

## User Standards & Preferences Compliance

The user may provide information regarding their tech stack, which should take precidence when documenting the product's tech stack.  To fill in any gaps, find the user's usual tech stack information as documented in any of these files:

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

# PHASE 5: Product Focused Cleanup

Now that product files (mission, tech-stack, roadmap) have been created, run the product-focused cleanup workflow to simplify and enhance agent-os files based on the detected product scope.

## Core Responsibilities

1. **Run Cleanup Workflow**: Execute the product-focused cleanup workflow to process agent-os files
2. **Review Results**: Present the cleanup report to the user for review
3. **Complete Plan-Product**: Finalize the plan-product command

## Workflow

Execute the product-focused cleanup workflow:

# Product-Focused Cleanup Workflow

After product files (mission, tech-stack, roadmap) are created, this workflow cleans irrelevant content from agent-os files and enhances remaining content with product-specific knowledge.

## Core Responsibilities

1. **Detect Product Scope**: Load and parse product files to understand language, framework, project type, and architecture
2. **Simplify Files**: Remove irrelevant technology examples, workflows, and patterns not matching the product scope
3. **Expand Knowledge**: Enhance remaining content with product-specific examples and web-validated best practices
4. **Generate Report**: Create a transparent report of all changes made

## Workflow

### Step 1: Load Product Scope Detection Sources

Load the product definition files to understand the project scope:

```bash
echo "📋 Loading product scope detection sources..."

# Initialize scope variables
DETECTED_LANGUAGE=""
DETECTED_FRAMEWORKS=""
DETECTED_PROJECT_TYPE=""
DETECTED_ARCHITECTURE=""

# Load tech-stack.md for language and framework detection
if [ -f "agent-os/product/tech-stack.md" ]; then
    TECH_STACK_CONTENT=$(cat agent-os/product/tech-stack.md)
    echo "✅ Loaded tech-stack.md"
else
    echo "⚠️ tech-stack.md not found - scope detection limited"
fi

# Load mission.md for project type indicators
if [ -f "agent-os/product/mission.md" ]; then
    MISSION_CONTENT=$(cat agent-os/product/mission.md)
    echo "✅ Loaded mission.md"
else
    echo "⚠️ mission.md not found - project type detection limited"
fi

# Load roadmap.md for feature scope
if [ -f "agent-os/product/roadmap.md" ]; then
    ROADMAP_CONTENT=$(cat agent-os/product/roadmap.md)
    echo "✅ Loaded roadmap.md"
else
    echo "⚠️ roadmap.md not found - feature scope detection limited"
fi
```

### Step 2: Parse and Categorize Detected Scope

Analyze product files to categorize the project scope:

```bash
echo "🔍 Parsing and categorizing product scope..."

# Language Detection
detect_language() {
    local tech_stack="$1"
    local tech_lower=$(echo "$tech_stack" | tr '[:upper:]' '[:lower:]')
    
    # Check for common languages
    if echo "$tech_lower" | grep -qE "typescript|\.ts\b"; then
        echo "typescript"
    elif echo "$tech_lower" | grep -qE "javascript|\.js\b|node\.js|nodejs"; then
        echo "javascript"
    elif echo "$tech_lower" | grep -qE "python|\.py\b|django|flask|fastapi"; then
        echo "python"
    elif echo "$tech_lower" | grep -qE "rust|cargo|\.rs\b"; then
        echo "rust"
    elif echo "$tech_lower" | grep -qE "golang|go\b|\.go\b"; then
        echo "go"
    elif echo "$tech_lower" | grep -qE "ruby|rails|\.rb\b"; then
        echo "ruby"
    elif echo "$tech_lower" | grep -qE "java\b|spring|\.java\b"; then
        echo "java"
    elif echo "$tech_lower" | grep -qE "c#|csharp|\.net|dotnet"; then
        echo "csharp"
    elif echo "$tech_lower" | grep -qE "php|laravel|symfony"; then
        echo "php"
    elif echo "$tech_lower" | grep -qE "swift|ios|swiftui"; then
        echo "swift"
    elif echo "$tech_lower" | grep -qE "kotlin|android"; then
        echo "kotlin"
    else
        echo "unknown"
    fi
}

# Framework Detection
detect_frameworks() {
    local tech_stack="$1"
    local tech_lower=$(echo "$tech_stack" | tr '[:upper:]' '[:lower:]')
    local frameworks=""
    
    # Frontend frameworks
    echo "$tech_lower" | grep -qE "react|reactjs" && frameworks="$frameworks react"
    echo "$tech_lower" | grep -qE "vue|vuejs" && frameworks="$frameworks vue"
    echo "$tech_lower" | grep -qE "angular" && frameworks="$frameworks angular"
    echo "$tech_lower" | grep -qE "svelte" && frameworks="$frameworks svelte"
    echo "$tech_lower" | grep -qE "next\.js|nextjs" && frameworks="$frameworks nextjs"
    echo "$tech_lower" | grep -qE "nuxt" && frameworks="$frameworks nuxt"
    
    # Backend frameworks
    echo "$tech_lower" | grep -qE "express|expressjs" && frameworks="$frameworks express"
    echo "$tech_lower" | grep -qE "fastify" && frameworks="$frameworks fastify"
    echo "$tech_lower" | grep -qE "nest\.js|nestjs" && frameworks="$frameworks nestjs"
    echo "$tech_lower" | grep -qE "django" && frameworks="$frameworks django"
    echo "$tech_lower" | grep -qE "flask" && frameworks="$frameworks flask"
    echo "$tech_lower" | grep -qE "fastapi" && frameworks="$frameworks fastapi"
    echo "$tech_lower" | grep -qE "rails" && frameworks="$frameworks rails"
    echo "$tech_lower" | grep -qE "spring" && frameworks="$frameworks spring"
    echo "$tech_lower" | grep -qE "laravel" && frameworks="$frameworks laravel"
    
    # Mobile frameworks
    echo "$tech_lower" | grep -qE "react.native" && frameworks="$frameworks react-native"
    echo "$tech_lower" | grep -qE "flutter" && frameworks="$frameworks flutter"
    echo "$tech_lower" | grep -qE "expo" && frameworks="$frameworks expo"
    
    echo "$frameworks" | xargs
}

# Project Type Detection
detect_project_type() {
    local mission="$1"
    local roadmap="$2"
    local combined=$(echo "$mission $roadmap" | tr '[:upper:]' '[:lower:]')
    
    # Check for project type indicators
    if echo "$combined" | grep -qE "cli|command.line|terminal|console.app"; then
        echo "cli"
    elif echo "$combined" | grep -qE "api|rest|graphql|backend|server|microservice"; then
        echo "api"
    elif echo "$combined" | grep -qE "web.app|webapp|frontend|dashboard|portal|spa"; then
        echo "webapp"
    elif echo "$combined" | grep -qE "mobile|ios|android|app.store|play.store"; then
        echo "mobile"
    elif echo "$combined" | grep -qE "library|package|sdk|npm|pip|crate"; then
        echo "library"
    elif echo "$combined" | grep -qE "ai|ml|machine.learning|llm|agent|model"; then
        echo "ai-service"
    elif echo "$combined" | grep -qE "full.stack|fullstack"; then
        echo "fullstack"
    else
        echo "unknown"
    fi
}

# Architecture Detection
detect_architecture() {
    local tech_stack="$1"
    local roadmap="$2"
    local combined=$(echo "$tech_stack $roadmap" | tr '[:upper:]' '[:lower:]')
    
    if echo "$combined" | grep -qE "microservice|micro.service|distributed|kubernetes|k8s"; then
        echo "microservices"
    elif echo "$combined" | grep -qE "serverless|lambda|cloud.function|faas"; then
        echo "serverless"
    elif echo "$combined" | grep -qE "monolith|monolithic|single.app"; then
        echo "monolith"
    elif echo "$combined" | grep -qE "event.driven|event.sourcing|cqrs"; then
        echo "event-driven"
    else
        echo "monolith"  # Default to monolith
    fi
}

# Run detection
DETECTED_LANGUAGE=$(detect_language "$TECH_STACK_CONTENT")
DETECTED_FRAMEWORKS=$(detect_frameworks "$TECH_STACK_CONTENT")
DETECTED_PROJECT_TYPE=$(detect_project_type "$MISSION_CONTENT" "$ROADMAP_CONTENT")
DETECTED_ARCHITECTURE=$(detect_architecture "$TECH_STACK_CONTENT" "$ROADMAP_CONTENT")

echo ""
echo "📊 Detected Product Scope:"
echo "   Language: $DETECTED_LANGUAGE"
echo "   Frameworks: $DETECTED_FRAMEWORKS"
echo "   Project Type: $DETECTED_PROJECT_TYPE"
echo "   Architecture: $DETECTED_ARCHITECTURE"

# Save scope detection results
mkdir -p agent-os/output/product-cleanup
cat > agent-os/output/product-cleanup/detected-scope.yml << EOF
# Product Scope Detection Results
# Generated by product-focused-cleanup workflow

language: $DETECTED_LANGUAGE
frameworks: [$DETECTED_FRAMEWORKS]
project_type: $DETECTED_PROJECT_TYPE
architecture: $DETECTED_ARCHITECTURE

detection_sources:
  tech_stack: agent-os/product/tech-stack.md
  mission: agent-os/product/mission.md
  roadmap: agent-os/product/roadmap.md
EOF

echo "✅ Scope detection complete"
```

### Step 3: Phase A - Simplify (Remove Irrelevant Content)

Remove content that doesn't match the detected product scope:

```bash
echo ""
echo "🧹 Phase A: Simplifying agent-os files..."

# Initialize tracking
REMOVED_FILES=()
MODIFIED_FILES=()
REMOVED_CONTENT=()

# Define what to remove based on detected scope
define_removal_rules() {
    local lang="$1"
    local proj_type="$2"
    local arch="$3"
    
    # Language-based removal patterns
    case "$lang" in
        "typescript"|"javascript")
            REMOVE_LANG_PATTERNS="python ruby rust go java csharp php swift kotlin"
            ;;
        "python")
            REMOVE_LANG_PATTERNS="typescript javascript ruby rust go java csharp php swift kotlin"
            ;;
        "rust")
            REMOVE_LANG_PATTERNS="typescript javascript python ruby go java csharp php swift kotlin"
            ;;
        "go")
            REMOVE_LANG_PATTERNS="typescript javascript python ruby rust java csharp php swift kotlin"
            ;;
        *)
            REMOVE_LANG_PATTERNS=""
            ;;
    esac
    
    # Project type-based removal patterns
    case "$proj_type" in
        "cli")
            REMOVE_TYPE_PATTERNS="ui frontend screen design mockup component widget button form modal"
            ;;
        "api")
            REMOVE_TYPE_PATTERNS="ui frontend screen design mockup component widget button form modal mobile ios android"
            ;;
        "webapp")
            REMOVE_TYPE_PATTERNS="mobile ios android cli terminal console"
            ;;
        "mobile")
            REMOVE_TYPE_PATTERNS="cli terminal console"
            ;;
        "library")
            REMOVE_TYPE_PATTERNS="ui frontend screen design mockup mobile ios android"
            ;;
        "ai-service")
            REMOVE_TYPE_PATTERNS="crud traditional-web mobile ios android"
            ;;
        *)
            REMOVE_TYPE_PATTERNS=""
            ;;
    esac
    
    # Architecture-based removal patterns
    case "$arch" in
        "monolith")
            REMOVE_ARCH_PATTERNS="microservice kubernetes k8s service-mesh distributed"
            ;;
        "microservices")
            REMOVE_ARCH_PATTERNS="monolith single-app"
            ;;
        "serverless")
            REMOVE_ARCH_PATTERNS="monolith traditional-server"
            ;;
        *)
            REMOVE_ARCH_PATTERNS=""
            ;;
    esac
}

# Run removal rules definition
define_removal_rules "$DETECTED_LANGUAGE" "$DETECTED_PROJECT_TYPE" "$DETECTED_ARCHITECTURE"

# Process each agent-os directory
process_directory_for_removal() {
    local dir="$1"
    local dir_name=$(basename "$dir")
    
    echo "   Processing $dir_name/..."
    
    # Find all markdown files
    for file in $(find "$dir" -name "*.md" -type f 2>/dev/null); do
        local file_name=$(basename "$file")
        local file_content=$(cat "$file")
        local file_lower=$(echo "$file_content" | tr '[:upper:]' '[:lower:]')
        local should_remove=false
        local removal_reason=""
        
        # Check language patterns
        for pattern in $REMOVE_LANG_PATTERNS; do
            if echo "$file_lower" | grep -qiE "\b${pattern}\b.*example|\b${pattern}\b.*pattern"; then
                # Check if this is a significant match (not just a mention)
                match_count=$(echo "$file_lower" | grep -oiE "\b${pattern}\b" | wc -l)
                if [ "$match_count" -gt 3 ]; then
                    should_remove=true
                    removal_reason="Language not in scope: $pattern"
                    break
                fi
            fi
        done
        
        # Check project type patterns (for workflow files)
        if [ "$should_remove" = false ] && [[ "$dir" == *"workflows"* ]]; then
            for pattern in $REMOVE_TYPE_PATTERNS; do
                if echo "$file_name $file_lower" | grep -qiE "\b${pattern}\b"; then
                    should_remove=true
                    removal_reason="Project type not in scope: $pattern"
                    break
                fi
            done
        fi
        
        # Instead of removing files, mark sections for removal or simplify
        if [ "$should_remove" = true ]; then
            REMOVED_CONTENT+=("$file: $removal_reason")
            # Add marker comment at top of file
            echo "<!-- PRODUCT-CLEANUP: This file contains content outside product scope ($removal_reason). Consider reviewing or removing. -->" | cat - "$file" > temp && mv temp "$file"
            MODIFIED_FILES+=("$file")
        fi
    done
}

# Process directories
for dir in agent-os/commands agent-os/workflows agent-os/standards agent-os/agents; do
    if [ -d "$dir" ]; then
        process_directory_for_removal "$dir"
    fi
done

echo "✅ Phase A complete: ${#MODIFIED_FILES[@]} files marked for review"
```

### Step 4: Phase B - Expand (Enhance with Product-Specific Knowledge)

Enhance remaining content with product-specific examples and patterns:

```bash
echo ""
echo "📚 Phase B: Expanding with product-specific knowledge..."

# Initialize tracking
ENHANCED_FILES=()
ADDED_CONTENT=()

# Define enhancement rules based on detected scope
define_enhancement_rules() {
    local lang="$1"
    local frameworks="$2"
    local proj_type="$3"
    
    # Language-specific enhancements
    case "$lang" in
        "typescript")
            LANG_PATTERNS="TypeScript type safety, interfaces, generics, strict mode"
            LANG_EXAMPLES="Use TypeScript interfaces for data structures, leverage type inference"
            ;;
        "python")
            LANG_PATTERNS="Python type hints, dataclasses, async/await patterns"
            LANG_EXAMPLES="Use type hints for function signatures, leverage dataclasses for DTOs"
            ;;
        "rust")
            LANG_PATTERNS="Rust ownership, borrowing, lifetimes, Result/Option patterns"
            LANG_EXAMPLES="Use Result for error handling, leverage pattern matching"
            ;;
        "go")
            LANG_PATTERNS="Go interfaces, goroutines, channels, error handling"
            LANG_EXAMPLES="Use interfaces for abstraction, leverage goroutines for concurrency"
            ;;
        *)
            LANG_PATTERNS=""
            LANG_EXAMPLES=""
            ;;
    esac
    
    # Framework-specific enhancements
    FRAMEWORK_PATTERNS=""
    for fw in $frameworks; do
        case "$fw" in
            "react")
                FRAMEWORK_PATTERNS="$FRAMEWORK_PATTERNS React hooks, functional components, state management"
                ;;
            "express")
                FRAMEWORK_PATTERNS="$FRAMEWORK_PATTERNS Express middleware, routing, error handling"
                ;;
            "fastapi")
                FRAMEWORK_PATTERNS="$FRAMEWORK_PATTERNS FastAPI dependency injection, Pydantic models, async endpoints"
                ;;
            "nextjs")
                FRAMEWORK_PATTERNS="$FRAMEWORK_PATTERNS Next.js App Router, Server Components, API routes"
                ;;
        esac
    done
    
    # Project type enhancements
    case "$proj_type" in
        "cli")
            TYPE_PATTERNS="CLI argument parsing, interactive prompts, progress indicators, exit codes"
            ;;
        "api")
            TYPE_PATTERNS="REST conventions, API versioning, authentication, rate limiting, documentation"
            ;;
        "webapp")
            TYPE_PATTERNS="Component architecture, state management, routing, responsive design"
            ;;
        "ai-service")
            TYPE_PATTERNS="Prompt engineering, model integration, context management, token optimization"
            ;;
        *)
            TYPE_PATTERNS=""
            ;;
    esac
}

# Run enhancement rules definition
define_enhancement_rules "$DETECTED_LANGUAGE" "$DETECTED_FRAMEWORKS" "$DETECTED_PROJECT_TYPE"

# Inject product terminology from mission/roadmap
inject_product_terminology() {
    local file="$1"
    local mission="$2"
    local roadmap="$3"
    
    # Extract key terms from mission and roadmap
    # This is a simplified version - in practice, use NLP or keyword extraction
    local key_terms=$(echo "$mission $roadmap" | grep -oE '\b[A-Z][a-z]+[A-Z][a-zA-Z]*\b' | sort -u | head -10)
    
    if [ -n "$key_terms" ]; then
        echo "   Injecting product terminology into $file"
        ENHANCED_FILES+=("$file")
    fi
}

# Create product-specific enhancement section
create_enhancement_section() {
    cat << EOF

## Product-Specific Patterns

Based on detected product scope ($DETECTED_LANGUAGE / $DETECTED_PROJECT_TYPE):

### Language Patterns
$LANG_PATTERNS

### Framework Patterns
$FRAMEWORK_PATTERNS

### Project Type Patterns
$TYPE_PATTERNS

EOF
}

echo "✅ Phase B complete: Enhancement rules defined"
echo "   Language patterns: $LANG_PATTERNS"
echo "   Framework patterns: $FRAMEWORK_PATTERNS"
echo "   Project type patterns: $TYPE_PATTERNS"
```

### Step 5: Web Search for Trusted Information

Search for current best practices and validate with multiple sources:

```bash
echo ""
echo "🌐 Searching for trusted information (2+ source validation)..."

# Define search categories
SEARCH_CATEGORIES=(
    "best practices"
    "common patterns"
    "anti-patterns"
    "performance optimization"
    "security considerations"
    "testing strategies"
)

# Generate search queries based on detected scope
generate_search_queries() {
    local lang="$1"
    local frameworks="$2"
    local proj_type="$3"
    
    local queries=()
    
    # Language-specific queries
    [ -n "$lang" ] && [ "$lang" != "unknown" ] && queries+=("$lang best practices 2024")
    
    # Framework-specific queries
    for fw in $frameworks; do
        queries+=("$fw best practices patterns")
        queries+=("$fw common mistakes anti-patterns")
    done
    
    # Project type queries
    case "$proj_type" in
        "cli")
            queries+=("CLI application best practices")
            queries+=("command line tool UX patterns")
            ;;
        "api")
            queries+=("REST API design best practices")
            queries+=("API security best practices")
            ;;
        "webapp")
            queries+=("web application architecture patterns")
            queries+=("frontend performance optimization")
            ;;
        "ai-service")
            queries+=("LLM application best practices")
            queries+=("AI agent design patterns")
            ;;
    esac
    
    printf '%s\n' "${queries[@]}"
}

# Generate queries
SEARCH_QUERIES=$(generate_search_queries "$DETECTED_LANGUAGE" "$DETECTED_FRAMEWORKS" "$DETECTED_PROJECT_TYPE")

echo "   Generated search queries:"
echo "$SEARCH_QUERIES" | while read query; do
    [ -n "$query" ] && echo "   - $query"
done

# Note: Actual web search would be performed here
# The workflow instructs the AI agent to:
# 1. Perform web searches for each query
# 2. Validate information appears in 2+ independent sources
# 3. Prioritize: Official docs > Reputable blogs > Stack Overflow
# 4. Add validated information to enhancement section

echo ""
echo "📝 Web Search Instructions for AI Agent:"
echo "   1. Search for each query listed above"
echo "   2. Validate information from 2+ independent sources"
echo "   3. Prioritize sources: Official docs > Reputable tech blogs > Stack Overflow (high-voted)"
echo "   4. Include version-specific information when available"
echo "   5. Add validated patterns to agent-os files"

# Save search queries for reference
cat > agent-os/output/product-cleanup/search-queries.md << EOF
# Web Search Queries for Product Enhancement

Generated based on detected scope:
- Language: $DETECTED_LANGUAGE
- Frameworks: $DETECTED_FRAMEWORKS
- Project Type: $DETECTED_PROJECT_TYPE

## Queries to Execute

$(echo "$SEARCH_QUERIES" | while read query; do
    [ -n "$query" ] && echo "- [ ] $query"
done)

## Validation Requirements

- Information must appear in 2+ independent sources
- Prioritize: Official documentation > Reputable tech blogs > Stack Overflow
- Include version numbers when applicable
- Note the sources for each piece of information added

## Categories to Cover

$(for cat in "${SEARCH_CATEGORIES[@]}"; do
    echo "- [ ] $cat"
done)
EOF

echo "✅ Search queries saved to agent-os/output/product-cleanup/search-queries.md"
```

### Step 6: Generate Cleanup/Enhancement Report

Create a comprehensive report of all changes:

```bash
echo ""
echo "📊 Generating cleanup/enhancement report..."

# Create report directory
mkdir -p agent-os/output/product-cleanup

# Generate the report
cat > agent-os/output/product-cleanup/cleanup-report.md << EOF
# Product-Focused Cleanup Report

Generated: $(date -u +"%Y-%m-%d %H:%M:%S UTC")

## Detected Product Scope

| Attribute | Detected Value |
|-----------|----------------|
| Language | $DETECTED_LANGUAGE |
| Frameworks | $DETECTED_FRAMEWORKS |
| Project Type | $DETECTED_PROJECT_TYPE |
| Architecture | $DETECTED_ARCHITECTURE |

## Phase A: Simplification Summary

### Files Marked for Review
$(if [ ${#MODIFIED_FILES[@]} -gt 0 ]; then
    for file in "${MODIFIED_FILES[@]}"; do
        echo "- $file"
    done
else
    echo "No files marked for review"
fi)

### Content Flagged as Out-of-Scope
$(if [ ${#REMOVED_CONTENT[@]} -gt 0 ]; then
    for content in "${REMOVED_CONTENT[@]}"; do
        echo "- $content"
    done
else
    echo "No content flagged"
fi)

### Removal Rules Applied

**Language-based removals:** $REMOVE_LANG_PATTERNS
**Project type-based removals:** $REMOVE_TYPE_PATTERNS
**Architecture-based removals:** $REMOVE_ARCH_PATTERNS

## Phase B: Enhancement Summary

### Enhancement Rules Applied

**Language Patterns:** $LANG_PATTERNS
**Framework Patterns:** $FRAMEWORK_PATTERNS
**Project Type Patterns:** $TYPE_PATTERNS

### Files Enhanced
$(if [ ${#ENHANCED_FILES[@]} -gt 0 ]; then
    for file in "${ENHANCED_FILES[@]}"; do
        echo "- $file"
    done
else
    echo "Enhancement pending web search validation"
fi)

## Web Search Status

See \`search-queries.md\` for queries to execute and validate.

## Next Steps

1. Review files marked with PRODUCT-CLEANUP comments
2. Execute web searches and validate information
3. Add validated patterns to relevant agent-os files
4. Re-run this workflow to verify completeness

---

*This report was generated by the product-focused-cleanup workflow.*
*Location: agent-os/output/product-cleanup/cleanup-report.md*
EOF

echo "✅ Report generated: agent-os/output/product-cleanup/cleanup-report.md"
```

## Display Confirmation

Once cleanup is complete, output the following:

```
✅ Product-Focused Cleanup Complete!

📊 Detected Scope:
   Language: [detected language]
   Frameworks: [detected frameworks]
   Project Type: [detected project type]
   Architecture: [detected architecture]

🧹 Phase A (Simplify):
   Files reviewed: [count]
   Content flagged: [count]

📚 Phase B (Expand):
   Enhancement rules defined
   Web search queries generated

📁 Reports Generated:
   - agent-os/output/product-cleanup/detected-scope.yml
   - agent-os/output/product-cleanup/search-queries.md
   - agent-os/output/product-cleanup/cleanup-report.md

NEXT STEP 👉 Review the cleanup report and execute web searches for validated enhancements.
```

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Output the confirmation message above after completing the cleanup workflow.
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

Ensure cleanup follows the user's preferences:

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
{{ENDUNLESS standards_as_claude_code_skills}}


## Display Confirmation and Completion

Once the cleanup workflow is complete, output the following message:

```
🎉 plan-product Complete!

**Product Documentation Created:**
├── agent-os/product/
│   ├── mission.md       - Product vision and goals
│   ├── roadmap.md       - Development roadmap
│   └── tech-stack.md    - Technical stack

**Product-Focused Cleanup Applied:**
├── agent-os/output/product-cleanup/
│   ├── detected-scope.yml    - Detected product scope
│   ├── search-queries.md     - Web search queries for enhancement
│   └── cleanup-report.md     - Cleanup/enhancement report

📊 Detected Scope:
   Language: [detected language]
   Frameworks: [detected frameworks]
   Project Type: [detected project type]
   Architecture: [detected architecture]

**What's Next?**

Your product documentation is ready and agent-os files have been cleaned/enhanced for your specific product scope.

👉 Run `/create-basepoints` to analyze your codebase and generate pattern documentation.

This will create basepoints that document your code patterns, making it easier for AI agents to understand and work with your codebase.
```

## User Standards & Preferences Compliance

Ensure cleanup follows the user's preferences:

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
