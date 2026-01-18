Now that knowledge has been extracted from basepoints and product files, proceed with merging all knowledge sources and resolving conflicts by following these instructions:

## Core Responsibilities

1. **Merge Knowledge from All Sources**: Combine basepoints, product, and abstract command knowledge
2. **Detect Conflicts**: Identify contradictions between knowledge sources
3. **Present Conflicts for Human Review**: Show all conflicts with context from each source
4. **Apply Conflict Resolution**: Use priority rules and human resolutions to resolve conflicts
5. **Store Merged Knowledge**: Organize merged knowledge ready for command transformation

## Workflow

### Step 1: Load Extracted Knowledge

Load knowledge from previous phases:

```bash
# Load basepoints knowledge from cache
if [ -f "geist/output/deploy-agents/knowledge/basepoints-knowledge.json" ]; then
    BASEPOINTS_KNOWLEDGE=$(cat geist/output/deploy-agents/knowledge/basepoints-knowledge.json)
    echo "✅ Loaded basepoints knowledge"
fi

# Load product knowledge from cache
if [ -f "geist/output/deploy-agents/knowledge/product-knowledge.json" ]; then
    PRODUCT_KNOWLEDGE=$(cat geist/output/deploy-agents/knowledge/product-knowledge.json)
    echo "✅ Loaded product knowledge"
fi

# Read abstract command templates to understand abstract command knowledge
ABSTRACT_COMMANDS=""
for cmd in shape-spec write-spec create-tasks implement-tasks orchestrate-tasks; do
    if [ -f "profiles/default/commands/$cmd/single-agent/$cmd.md" ]; then
        ABSTRACT_COMMANDS="${ABSTRACT_COMMANDS}\n\n=== $cmd ===\n$(cat profiles/default/commands/$cmd/single-agent/$cmd.md)"
    fi
done
echo "✅ Loaded abstract command templates"
```

### Step 2: Merge Complementary Knowledge

Combine complementary knowledge from all sources that doesn't conflict:

```bash
# Merge patterns from all sources
MERGED_PATTERNS="{
  \"same_layer\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product tech-stack],
    \"from_abstract\": [generic patterns from abstract commands],
    \"merged\": [combined complementary patterns]
  },
  \"cross_layer\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product mission/roadmap],
    \"from_abstract\": [generic patterns from abstract commands],
    \"merged\": [combined complementary patterns]
  }
}"

# Merge standards from all sources
MERGED_STANDARDS="{
  \"naming\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product tech-stack],
    \"from_abstract\": [generic standards from abstract commands],
    \"merged\": [combined complementary standards]
  },
  \"coding_style\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product tech-stack],
    \"from_abstract\": [generic standards from abstract commands],
    \"merged\": [combined complementary standards]
  },
  \"structure\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product tech-stack],
    \"from_abstract\": [generic standards from abstract commands],
    \"merged\": [combined complementary standards]
  }
}"

# Merge flows from all sources
MERGED_FLOWS="{
  \"data_flows\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product context],
    \"from_abstract\": [generic flows from abstract commands],
    \"merged\": [combined complementary flows]
  },
  \"control_flows\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product context],
    \"from_abstract\": [generic flows from abstract commands],
    \"merged\": [combined complementary flows]
  },
  \"dependency_flows\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product context],
    \"from_abstract\": [generic flows from abstract commands],
    \"merged\": [combined complementary flows]
  }
}"

# Merge strategies from all sources
MERGED_STRATEGIES="{
  \"implementation\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product mission/roadmap],
    \"from_abstract\": [generic strategies from abstract commands],
    \"merged\": [combined complementary strategies]
  },
  \"architectural\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product mission/roadmap],
    \"from_abstract\": [generic strategies from abstract commands],
    \"merged\": [combined complementary strategies]
  }
}"

# Merge testing approaches from all sources
MERGED_TESTING="{
  \"patterns\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product tech-stack],
    \"from_abstract\": [generic testing patterns from abstract commands],
    \"merged\": [combined complementary testing patterns]
  },
  \"strategies\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product context],
    \"from_abstract\": [generic testing strategies from abstract commands],
    \"merged\": [combined complementary testing strategies]
  },
  \"organization\": {
    \"from_basepoints\": [extracted from basepoints],
    \"from_product\": [extracted from product context],
    \"from_abstract\": [generic testing organization from abstract commands],
    \"merged\": [combined complementary testing organization]
  }
}"
```

Merge complementary knowledge:
- **Patterns**: Combine patterns from basepoints (project-specific), product (context-specific), and abstract commands (generic templates)
- **Standards**: Combine standards from all sources, merging naming conventions, coding styles, structure standards
- **Flows**: Combine data flows, control flows, dependency flows from all sources
- **Strategies**: Combine implementation and architectural strategies from all sources
- **Testing Approaches**: Combine test patterns, strategies, and organization from all sources

### Step 3: Detect Conflicts

Identify contradictions between knowledge sources:

```bash
# Detect conflicts between basepoints and product knowledge
CONFLICTS_BASEPOINTS_PRODUCT=""

# Check for conflicting patterns
if [ -f "geist/output/deploy-agents/knowledge/basepoints-knowledge.json" ] && [ -f "geist/output/deploy-agents/knowledge/product-knowledge.json" ]; then
    # Compare patterns from basepoints vs product tech-stack
    # Example: Basepoints says "use Repository pattern" but product tech-stack suggests different pattern
    # Detect contradictions in naming conventions, coding styles, structural patterns
fi

# Detect conflicts between basepoints and abstract command knowledge
CONFLICTS_BASEPOINTS_ABSTRACT=""

# Check for conflicting standards
# Example: Basepoints has project-specific standard "use camelCase" but abstract command says "use kebab-case"
# Detect contradictions between project-specific patterns and generic abstract patterns

# Detect conflicts between product knowledge and abstract command knowledge
CONFLICTS_PRODUCT_ABSTRACT=""

# Check for conflicting approaches
# Example: Product tech-stack suggests "use TypeScript" patterns but abstract command uses generic patterns
# Detect contradictions between product context and generic abstract templates

# Collect all conflicts
ALL_CONFLICTS="${CONFLICTS_BASEPOINTS_PRODUCT}\n${CONFLICTS_BASEPOINTS_ABSTRACT}\n${CONFLICTS_PRODUCT_ABSTRACT}"
```

Detect conflicts in:
- **Patterns**: Conflicting design patterns, architectural patterns, coding patterns
- **Standards**: Conflicting naming conventions, coding styles, structure standards
- **Flows**: Conflicting data flows, control flows, dependency flows
- **Strategies**: Conflicting implementation strategies, architectural strategies
- **Testing Approaches**: Conflicting test patterns, strategies, organization

### Step 4: Present Conflicts for Human Review

IF conflicts are detected, present them to user for review:

```bash
if [ -n "$ALL_CONFLICTS" ]; then
    echo "⚠️  Conflicts detected between knowledge sources. Review required."
    echo ""
    echo "=========================================="
    echo "CONFLICTS REQUIRING HUMAN REVIEW"
    echo "=========================================="
    echo ""
    
    # Present each conflict with context
    CONFLICT_NUM=1
    echo "$ALL_CONFLICTS" | while IFS= read -r conflict; do
        if [ -n "$conflict" ]; then
            echo "Conflict #$CONFLICT_NUM:"
            echo "  Category: [pattern/standard/flow/strategy/testing]"
            echo "  Basepoints says: [value from basepoints with context]"
            echo "  Product says: [value from product with context]"
            echo "  Abstract command says: [value from abstract command with context]"
            echo ""
            echo "  Resolution options:"
            echo "    1. Use basepoints knowledge (highest priority if not resolved)"
            echo "    2. Use product knowledge"
            echo "    3. Use abstract command knowledge"
            echo "    4. Provide custom resolution"
            echo ""
            CONFLICT_NUM=$((CONFLICT_NUM + 1))
        fi
    done
    
    echo "=========================================="
    echo ""
    echo "Please review each conflict above and provide resolutions."
    echo "For each conflict, respond with the number of your choice or provide custom resolution."
fi
```

Present conflicts with:
- **Context from Each Source**: Show what each source says about the conflicting item
- **Source Identification**: Clearly label which knowledge source each conflicting item comes from
- **Resolution Options**: Provide options to choose which knowledge to use or provide custom resolution

WAIT for user to resolve conflicts before proceeding.

### Step 5: Apply Conflict Resolution Priority Rules

Apply resolution rules based on user responses and priority order:

```bash
# Priority order (when user doesn't resolve):
# 1. Human review resolution (highest priority - user choice)
# 2. Basepoints knowledge (takes priority over product)
# 3. Product knowledge (takes priority over abstract)
# 4. Abstract command knowledge (lowest priority)

for conflict in $ALL_CONFLICTS; do
    if [ -n "$conflict" ]; then
        # Check if user provided resolution
        if [ -n "$USER_RESOLUTION_FOR_CONFLICT" ]; then
            # Use user resolution (highest priority)
            RESOLVED_VALUE="$USER_RESOLUTION_FOR_CONFLICT"
        else
            # Apply automatic priority rules
            # Basepoints knowledge takes priority over product knowledge
            if [ -n "$BASEPOINTS_VALUE" ]; then
                RESOLVED_VALUE="$BASEPOINTS_VALUE"
            elif [ -n "$PRODUCT_VALUE" ]; then
                RESOLVED_VALUE="$PRODUCT_VALUE"
            else
                RESOLVED_VALUE="$ABSTRACT_VALUE"
            fi
        fi
    fi
done

# Merge complementary knowledge that doesn't conflict
# Combine details that complement each other from different sources
COMPLEMENTARY_MERGED=$(combine_complementary_knowledge "$BASEPOINTS_KNOWLEDGE" "$PRODUCT_KNOWLEDGE" "$ABSTRACT_KNOWLEDGE")
```

Apply conflict resolution:
- **Human Review Priority**: User resolution takes priority over all automatic resolution
- **Basepoints Priority**: Basepoints knowledge takes priority over product knowledge when user doesn't resolve
- **Product Priority**: Product knowledge takes priority over abstract command knowledge
- **Complementary Merging**: Merge knowledge that doesn't conflict (combine details from different sources)

### Step 6: Store Merged Knowledge for Command Transformation

Organize merged knowledge ready for injection into commands:

```bash
# Create final merged knowledge structure
MERGED_KNOWLEDGE="{
  \"patterns\": {
    \"same_layer\": $MERGED_PATTERNS.same_layer.merged,
    \"cross_layer\": $MERGED_PATTERNS.cross_layer.merged
  },
  \"standards\": {
    \"naming\": $MERGED_STANDARDS.naming.merged,
    \"coding_style\": $MERGED_STANDARDS.coding_style.merged,
    \"structure\": $MERGED_STANDARDS.structure.merged
  },
  \"flows\": {
    \"data\": $MERGED_FLOWS.data_flows.merged,
    \"control\": $MERGED_FLOWS.control_flows.merged,
    \"dependency\": $MERGED_FLOWS.dependency_flows.merged
  },
  \"strategies\": {
    \"implementation\": $MERGED_STRATEGIES.implementation.merged,
    \"architectural\": $MERGED_STRATEGIES.architectural.merged
  },
  \"testing\": {
    \"patterns\": $MERGED_TESTING.patterns.merged,
    \"strategies\": $MERGED_TESTING.strategies.merged,
    \"organization\": $MERGED_TESTING.organization.merged
  },
  \"project_structure\": {
    \"abstraction_layers\": [from basepoints],
    \"module_hierarchy\": [from basepoints],
    \"folder_structure\": [from basepoints]
  },
  \"product_context\": {
    \"mission\": [from product],
    \"roadmap\": [from product],
    \"tech_stack\": [from product]
  },
  \"conflict_resolutions\": {
    \"resolved_conflicts\": [list of conflicts resolved with resolutions],
    \"resolution_source\": [which source was chosen for each conflict]
  }
}"

# Store in cache for command transformation
mkdir -p geist/output/deploy-agents/knowledge
echo "$MERGED_KNOWLEDGE" > geist/output/deploy-agents/knowledge/merged-knowledge.json

# Create markdown summary
cat > geist/output/deploy-agents/knowledge/merged-knowledge-summary.md << 'EOF'
# Merged Knowledge for Command Specialization

## Patterns (Merged)
[Summary of merged patterns from all sources]

## Standards (Merged)
[Summary of merged standards from all sources]

## Flows (Merged)
[Summary of merged flows from all sources]

## Strategies (Merged)
[Summary of merged strategies from all sources]

## Testing Approaches (Merged)
[Summary of merged testing approaches from all sources]

## Project Structure
[Abstraction layers, module hierarchy, folder structure]

## Product Context
[Mission, roadmap, tech stack context]

## Conflict Resolutions
[List of conflicts and how they were resolved]
EOF

# Prepare knowledge for each command
# Organize merged knowledge by command type (shape-spec, write-spec, create-tasks, implement-tasks, orchestrate-tasks)
for cmd in shape-spec write-spec create-tasks implement-tasks orchestrate-tasks; do
    CMD_SPECIFIC_KNOWLEDGE=$(extract_knowledge_for_command "$MERGED_KNOWLEDGE" "$cmd")
    echo "$CMD_SPECIFIC_KNOWLEDGE" > "geist/output/deploy-agents/knowledge/${cmd}-knowledge.json"
done
```

Organize merged knowledge:
- **By Category**: Patterns, standards, flows, strategies, testing organized separately
- **For Command Transformation**: Knowledge structured ready for injection into each of the five commands
- **For Placeholder Replacement**: Knowledge organized to replace abstract placeholders ({{workflows/...}}, {{standards/...}})
- **With Source Tracking**: Preserve which source each piece of knowledge came from (for traceability)
- **With Conflict History**: Document how conflicts were resolved (for understanding decisions)

{{UNLESS compiled_single_command}}
## Display confirmation and next step

Once knowledge merging and conflict resolution is complete, output the following message:

```
✅ Knowledge merging and conflict resolution complete!

- Knowledge sources merged: Basepoints + Product + Abstract Commands
- Complementary knowledge: Combined from all sources
- Conflicts detected: [count] conflicts
- Conflicts resolved: [count] resolved (via [human review / automatic priority])
- Merged knowledge organized: Ready for command specialization

Knowledge prepared for specializing the five core commands.

NEXT STEP 👉 Run the command, `5-specialize-shape-spec-and-write-spec.md`
```
{{ENDUNLESS compiled_single_command}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure that your knowledge merging and conflict resolution process aligns with the user's preferences and standards as detailed in the following files:

{{standards/global/*}}
{{ENDUNLESS standards_as_claude_code_skills}}

## Important Constraints

- Must merge knowledge from all three sources: basepoints, product, abstract commands
- Must detect conflicts between all source pairs (basepoints↔product, basepoints↔abstract, product↔abstract)
- Must present all conflicts to user with context from each source before automatic resolution
- Must allow user to resolve conflicts by choosing source or providing custom resolution
- Human review step takes priority over all automatic resolution
- Basepoints knowledge takes priority over product knowledge when user doesn't resolve
- Must merge complementary knowledge that doesn't conflict (combine details from different sources)
- Must organize merged knowledge ready for injection into commands
- Must prepare knowledge for replacing abstract placeholders
