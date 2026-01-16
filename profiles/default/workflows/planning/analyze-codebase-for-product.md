# Analyze Codebase for Product Information

Analyze existing codebase to infer product information automatically for adapting an existing codebase into product documentation.

## Step 1: Software-Agnostic Codebase Traversal

Traverse the existing codebase to find relevant files using software-agnostic approach:

```bash
# Traverse codebase to identify project structure
# This should work for any project type (Node.js, Python, Ruby, Go, Java, etc.)
# Look for common files and directory patterns

# Find top-level directories
find . -maxdepth 1 -type d ! -name "." ! -name ".git" ! -name "node_modules" ! -name "vendor" | sort

# Find common root-level files that indicate project type
find . -maxdepth 1 -type f -name "package.json" -o -name "requirements.txt" -o -name "Gemfile" -o -name "go.mod" -o -name "pom.xml" -o -name "build.gradle" -o -name "README*" | sort

# Identify main source directories (common patterns across languages)
find . -maxdepth 2 -type d \( -name "src" -o -name "lib" -o -name "app" -o -name "src/main" \) | sort
```

Analyze code structure, directories, and file organization:

1. **Detect project type without hardcoding specific technologies:**
   - Look for configuration files (package.json, requirements.txt, Gemfile, go.mod, etc.)
   - Identify file extensions patterns (.js, .py, .rb, .go, .java, etc.)
   - Analyze directory naming conventions (src/, lib/, app/, etc.)
   - Use tech-stack agnostic descriptions in analysis (avoid using specific technology names where possible)

2. **Identify main directories, modules, and components:**
   - Traverse directory structure recursively
   - Identify source code directories vs configuration vs documentation
   - Map directory organization to logical modules/components
   - Identify patterns in file naming and organization

3. **Use tech-stack agnostic descriptions in analysis:**
   - Describe patterns in generic terms (e.g., "dependency management file" instead of "package.json")
   - Focus on structural patterns rather than specific technologies

## Step 2: Configuration File Analysis

Detect and parse dependency/configuration files across different project types:

```bash
# Check for common dependency/configuration files
# Node.js
if [ -f "package.json" ]; then
    echo "Found Node.js project"
    cat package.json
fi

# Python
if [ -f "requirements.txt" ]; then
    echo "Found Python requirements"
    cat requirements.txt
fi
if [ -f "setup.py" ] || [ -f "pyproject.toml" ] || [ -f "Pipfile" ]; then
    echo "Found Python project configuration"
fi

# Ruby
if [ -f "Gemfile" ]; then
    echo "Found Ruby project"
    cat Gemfile
fi

# Go
if [ -f "go.mod" ]; then
    echo "Found Go project"
    cat go.mod
fi

# Java
if [ -f "pom.xml" ] || [ -f "build.gradle" ]; then
    echo "Found Java project"
fi

# Other common files
find . -maxdepth 1 -type f \( -name "*.toml" -o -name "*.yaml" -o -name "*.yml" -o -name "*.json" -o -name "Cargo.toml" -o -name "composer.json" \) | head -10
```

Detect and parse dependency/configuration files for these project types:
- **Node.js**: package.json, package-lock.json, yarn.lock, pnpm-lock.yaml
- **Python**: requirements.txt, setup.py, pyproject.toml, Pipfile, Pipfile.lock
- **Ruby**: Gemfile, Gemfile.lock
- **Go**: go.mod, go.sum
- **Java**: pom.xml, build.gradle, build.gradle.kts
- **Rust**: Cargo.toml, Cargo.lock
- **PHP**: composer.json, composer.lock
- **Other**: Look for common dependency and build configuration files

Extract from configuration files:
- **Tech stack information**: Frameworks, libraries, tools, runtime versions
- **Build and deployment configuration**: Build scripts, deployment settings, environment configurations
- **Project metadata**: Name, version, description, author, license
- **Dependencies**: Production and development dependencies, their versions and purposes

Handle multiple configuration file formats gracefully:
- Parse JSON, YAML, TOML, and text-based formats
- Handle missing or malformed configuration files
- Extract relevant information even if file structure varies

## Step 3: Documentation Extraction

Read and analyze documentation files:

```bash
# Find README files
find . -maxdepth 2 -type f -iname "README*" | head -5

# Find documentation directories
find . -maxdepth 2 -type d \( -name "docs" -o -name "documentation" -o -name "wiki" -o -name "doc" \) | head -5

# Find common documentation files
find . -maxdepth 2 -type f \( -iname "CHANGELOG*" -o -iname "CONTRIBUTING*" -o -iname "LICENSE*" -o -iname "HISTORY*" \) | head -10
```

Read and analyze documentation files:
- **README files**: README.md, README.txt, README.rst, README.markdown, etc.
- **Documentation directories**: docs/, documentation/, wiki/, doc/, guides/
- **Configuration docs**: CHANGELOG.md, CONTRIBUTING.md, LICENSE, HISTORY.md, etc.
- **Project-specific docs**: Any files in documentation directories

Extract from documentation:
- **Product description and purpose**: What the product does, why it exists
- **Feature lists and capabilities**: Key features, functionality, capabilities
- **User guides and use cases**: How users interact with the product, common use cases
- **Goals and objectives**: Product goals, mission, objectives
- **Architecture information**: System architecture, design decisions
- **Installation and setup**: How to set up and run the product

Process documentation in various formats:
- **Markdown**: .md, .markdown files
- **Text**: .txt files
- **ReStructuredText**: .rst files
- **HTML**: .html documentation files
- Extract text content from all formats, preserving structure where possible

## Step 4: Code Structure Analysis

Analyze directory structure for project architecture:

```bash
# Analyze directory structure
# Get top-level structure
tree -L 2 -d -I 'node_modules|vendor|.git' . 2>/dev/null || find . -maxdepth 2 -type d ! -path "*/node_modules/*" ! -path "*/.git/*" ! -path "*/vendor/*" | sort

# Identify common architectural patterns
# Look for MVC, module-based, feature-based, or layered architectures
# Common patterns: src/, lib/, app/, components/, modules/, features/, services/, controllers/, models/, views/
```

Analyze directory structure for project architecture:

1. **Identify main modules, components, and features from code organization:**
   - Map directory structure to logical modules
   - Identify feature-based vs module-based organization
   - Recognize patterns: MVC, layered architecture, microservices, monolith
   - Extract component boundaries from directory structure

2. **Infer product purpose from codebase structure:**
   - Analyze naming conventions in directories and files
   - Identify domain concepts from structure (e.g., user/, product/, order/ directories)
   - Infer functionality from organizational patterns
   - Map code organization to business features

3. **Map code organization to feature sets:**
   - Extract feature names from directory/folder names
   - Identify related code groupings
   - Understand feature boundaries and relationships
   - Build feature inventory from structure

4. **Extract architectural patterns:**
   - Identify architectural style (MVC, REST API, CLI, library, etc.)
   - Recognize framework patterns (if framework usage is evident)
   - Understand separation of concerns from structure
   - Note design patterns evident in organization

Use software-agnostic analysis that works with any project type:
- Focus on structural patterns rather than language-specific conventions
- Describe patterns in generic terms
- Avoid assumptions about specific technologies

## Step 5: Merge Codebase Findings with Other Sources

Integrate codebase analysis results into product knowledge:
- Combine codebase findings with user input, documents, and scraped content
- Resolve conflicts and prioritize information sources appropriately
- Create comprehensive product understanding from all sources

Prioritization guidance:
1. User-provided information (highest priority)
2. Inheritance folder documents
3. Scraped web content and link analysis
4. Codebase analysis findings
5. Web search results (if requested)
