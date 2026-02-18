# QMD - Local Knowledge Base Search

QMD is a local semantic search engine for your notes, projects, and documentation. Use it to find relevant context before answering questions or making decisions.

## Available Collections
- `notes` - Daily notes, captures, personal thoughts
- `projects` - Project planning, research, meeting notes, decisions

## When to Use QMD
- **Before starting work on a project** - Search for prior decisions, context, meeting notes
- **When user asks about past work** - Find relevant notes and documentation
- **For cross-project context** - Search across all projects for related information
- **When uncertain about project history** - Check what was previously decided

## Search Commands (via Bash)
```sh
# Fast keyword search
qmd search "newsletter strategy" -c projects

# Semantic search (finds related concepts)
qmd vsearch "how to monetize" -c projects

# Best quality (hybrid + re-ranking)
qmd query "project roadmap decisions"

# Get full document content
qmd get "projects/my-project/CLAUDE.md" --full
```

## MCP Tools (if MCP server available)
- `qmd_search` - Fast BM25 keyword search
- `qmd_vsearch` - Semantic vector search
- `qmd_query` - Hybrid search with reranking (best quality)
- `qmd_get` - Retrieve document by path

## Usage Guidelines
- Use `query` for best results when exploring unfamiliar topics
- Use `search` for quick lookups of specific terms
- Always check relevant project CLAUDE.md files for current context
- Combine QMD results with direct file reads for full picture
