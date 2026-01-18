# Gather Product Information from Existing Codebase

Collect comprehensive product information from multiple sources for adapting an existing codebase into product documentation.

## Step 1: Create Inheritance Folder

Create the inheritance folder for user-provided documents:

```bash
# Create inheritance folder
mkdir -p geist/product/inheritance

# Check if inheritance folder already exists
if [ -d "geist/product/inheritance" ]; then
    if [ "$(ls -A geist/product/inheritance 2>/dev/null)" ]; then
        echo "Inheritance folder already contains files. Using existing files or add new ones?"
        # List existing files in inheritance folder
        ls -la geist/product/inheritance/
    else
        echo "Inheritance folder created. Please place any product-related documents here."
    fi
fi
```

## Step 2: Check Product Folder

Check if product folder already exists and handle accordingly:

```bash
# Check if product folder already exists
if [ -d "geist/product" ]; then
    echo "Product documentation already exists. Review existing files or start fresh?"
    # List existing product files
    ls -la geist/product/
fi
```

## Step 3: Gather Information from Multiple Sources

Collect comprehensive product information from the following sources:

### A. User Input - Interactive User Prompts

Prompt user interactively for product information following the same patterns as plan-product (reference: `geist/commands/plan-product/1-product-concept.md`):

1. **Initial Information Gathering:**
   - Start with product name and core concept (if not already known from codebase)
   - Ask about product purpose and main value proposition
   - Gather key features (minimum 3 if not inferrable from codebase)
   - Ask about target users and use cases (at least 1 user segment)

2. **Extended Information Gathering:**
   - Prompt for web pages: "Do you have any product web pages or documentation URLs to include?"
   - Prompt for public resources: "Any public resources, documentation sites, or references?"
   - Prompt for private documents: "Do you have private documents or files describing the product?"
   - Prompt for links: "Please provide any relevant links (documentation, marketing pages, technical specs, etc.)"
   - Prompt for @command syntax: "You can use @command syntax to describe specific aspects. For example: @command + 'describe the product mission' or @command + 'outline the product roadmap'"

3. **Follow-up Questions:**
   - Ask whatever questions are needed to create the most accurate product files
   - If information is missing or unclear, prompt for clarification
   - Follow similar user interaction patterns from plan-product but adapted for existing codebase context

If any critical information is missing, prompt user:
```
Please provide the following to create your product plan:
1. Product name and core concept (if not clear from codebase)
2. Any web pages, documents, or links related to the product
3. Additional information using @command syntax if needed (e.g., @command + "describe the product mission")
4. Any specific details about target users, features, or differentiators
```

### B. Inheritance Folder Document Processing

Read and process files from `geist/product/inheritance/`:

```bash
# Check for files in inheritance folder
if [ -d "geist/product/inheritance" ] && [ "$(ls -A geist/product/inheritance 2>/dev/null)" ]; then
    echo "Processing files from inheritance folder..."
    # List all files
    find geist/product/inheritance -type f | while read file; do
        echo "Processing: $file"
        # Extract content based on file type
    done
fi
```

Process files from inheritance folder:

1. **File Type Detection and Reading:**
   - **Markdown files**: .md, .markdown - Read and extract text content, preserve structure
   - **Text files**: .txt, .text - Read plain text content
   - **JSON files**: .json - Parse and extract structured data, look for product-related fields
   - **YAML files**: .yml, .yaml - Parse and extract structured data
   - **HTML files**: .html, .htm - Extract text content, strip HTML tags
   - **PDF files**: .pdf - Extract text content if possible (may require external tools)
   - **Other formats**: Attempt to read as text if supported

2. **Information Extraction:**
   - Extract product description and purpose
   - Identify features, capabilities, and use cases
   - Extract user information and personas
   - Identify goals, objectives, and differentiators
   - Extract tech stack mentions (if present)
   - Extract roadmap or feature plans (if present)

3. **Merge into Product Knowledge:**
   - Combine extracted information with other sources
   - Tag information with source (inheritance folder)
   - Preserve file references for traceability
   - Handle duplicate information gracefully

### C. Automatic Link/Web Page Scraping

For any links provided by the user, automatically scrape and analyze web page content:

```bash
# For each link provided by user:
# Use web scraping capabilities to fetch and parse content
# Note: Actual implementation may use tools like curl, wget, or web scraping libraries
```

Process links provided by user:

1. **Link Validation:**
   - Verify URL format is valid
   - Check if link is accessible (HTTP/HTTPS)
   - Handle different URL formats (with/without protocol, fragments, etc.)

2. **Automatic Content Scraping:**
   - Fetch web page content automatically (do not ask user, just scrape)
   - Extract main content from HTML pages
   - Strip HTML tags and formatting
   - Preserve important structure (headings, lists, etc.) where possible
   - Extract text content for analysis

3. **Information Extraction:**
   - Extract product description and purpose from scraped content
   - Identify features and capabilities mentioned
   - Extract user information and target audience
   - Identify product goals and value propositions
   - Extract technical information if present

4. **Error Handling:**
   - Handle unreachable links gracefully (log error, continue with other sources)
   - Handle parsing failures (invalid HTML, encoding issues, etc.)
   - Handle timeouts and network errors
   - Continue processing other links even if one fails
   - Inform user if critical links cannot be accessed (but do not block the process)

### D. @command Syntax Support

If user provides `@command + "prompt"` syntax, parse and execute:

```bash
# Example: @command + "describe the product mission"
# Parse the command reference and prompt text
# Execute the referenced command with the prompt
# Capture output for product knowledge
```

Implement @command syntax support:

1. **Parsing @command References:**
   - Detect `@command` or `@<command-name>` patterns in user input
   - Extract command name (default to generic command if not specified)
   - Extract prompt text after `+` or following quotation marks
   - Handle variations: `@command + "prompt"`, `@mycommand "prompt"`, `@command prompt`

2. **Command Execution:**
   - Execute the referenced command with the provided prompt
   - Pass the prompt as input to the command
   - Capture all command output (stdout and stderr)
   - Handle command execution errors gracefully

3. **Output Capture:**
   - Capture command output text
   - Parse structured output if applicable (JSON, YAML, etc.)
   - Extract product-relevant information from command output

4. **Supported Use Cases:**
   - `@command + "describe the product mission"` - Generate mission description
   - `@command + "outline the product roadmap"` - Generate roadmap outline
   - `@command + "list the tech stack"` - Generate tech stack information
   - `@command + "describe key features"` - Generate feature descriptions
   - Support any command that can provide product-related information

5. **Integration:**
   - Merge command output into product knowledge
   - Tag information with source (command output)
   - Treat command output as high-priority information source

### E. Optional Web Search Integration

If user explicitly requests web search:

1. **Web Search Request Handling:**
   - Check if user explicitly requested web search (do NOT execute automatically)
   - Prompt user for search terms or topics if not provided
   - Confirm search query before executing
   - Example: "Would you like me to search the web for additional product information? If yes, what should I search for?"

2. **Perform Web Search from Public Sources:**
   - Use available web search capabilities to search for product-related information
   - Search public sources (documentation sites, forums, articles, etc.)
   - Focus search on product information, features, competitors, market research
   - Execute search only when explicitly requested by user

3. **Process Search Results:**
   - Extract relevant information from search results
   - Identify product-relevant content from search results
   - Extract features, descriptions, use cases, or other relevant information
   - Filter out irrelevant or low-quality results

4. **Integrate Search Results:**
   - Add web search results to unified product knowledge base
   - Tag information with source (web search)
   - Integrate findings into product knowledge synthesis
   - Treat web search results as lower priority than other sources

5. **Error Handling:**
   - Handle web search errors gracefully (network failures, API errors, etc.)
   - Handle cases where no relevant results are found
   - Continue with other information sources even if web search fails
   - Inform user if web search fails but do not block the process

## Step 4: Merge All Information Sources

After gathering information from all sources, merge everything into a unified product knowledge base:

### Information Merging Logic

1. **Collect Information from All Sources:**
   - User input (interactive prompts)
   - Inheritance folder documents (from `geist/product/inheritance/`)
   - Scraped web content (from links provided)
   - Command outputs (from @command syntax)
   - Web search results (if user requested web search)

2. **Resolve Conflicts Between Information Sources:**
   - When same information appears from multiple sources with different values:
     * Prioritize user-provided information (highest priority)
     * Use inheritance folder documents if user input is unclear
     * Use scraped content if no user/inheritance information
     * Use codebase findings (from Phase 2) as supporting information
     * Use web search results as additional context (lowest priority)
   - Merge complementary information (combine details from different sources)
   - Identify gaps where information is missing from all sources

3. **Prioritization Order (Highest to Lowest):**
   1. User-provided information (explicit user input)
   2. @command outputs (user-requested command execution)
   3. Inheritance folder documents (user-provided files)
   4. Scraped web content (user-provided links)
   5. Codebase analysis findings (Phase 2)
   6. Web search results (if requested)

4. **Create Unified Product Knowledge Base:**
   - Combine all information into structured knowledge base
   - Organize by categories: mission, users, features, tech stack, etc.
   - Tag each piece of information with its source for traceability
   - Preserve important details from all sources
   - Remove duplicates while keeping the highest priority version
   - Prepare unified knowledge for use in subsequent phases (mission, roadmap, tech-stack generation)
