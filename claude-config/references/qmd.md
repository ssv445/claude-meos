# QMD - Local Knowledge Base Search

QMD is a local semantic search engine for your notes, projects, and documentation. Runs entirely local — no cloud, no API keys. Use it to find relevant context before answering questions or making decisions.

GitHub: https://github.com/tobi/qmd

## Installation

```bash
npm install -g @tobilu/qmd
# or
bun install -g @tobilu/qmd
```

## Setup Collections

```bash
# Index your workspace directories
qmd collection add ~/workspace/notes --name notes
qmd collection add ~/workspace/projects --name projects

# Add context descriptions (helps search understand your content)
qmd context add qmd://notes "Daily notes, captures, and personal thoughts"
qmd context add qmd://projects "Project planning, research, meeting notes, decisions"

# Build vector embeddings for semantic search
qmd embed
```

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

## MCP Tools (when MCP server is configured)
- `qmd_search` - Fast BM25 keyword search
- `qmd_vsearch` - Semantic vector search
- `qmd_query` - Hybrid search with reranking (best quality)
- `qmd_get` - Retrieve document by path
- `qmd_multi_get` - Retrieve multiple documents by glob pattern

## MCP Configuration

Add to `~/.claude/settings.json`:
```json
{
  "mcpServers": {
    "qmd": {
      "command": "qmd",
      "args": ["mcp"]
    }
  }
}
```

## Re-indexing

After adding new notes or projects:
```bash
qmd embed        # Re-index and rebuild embeddings
qmd embed -f     # Force full re-embed
```

## Usage Guidelines
- Use `query` for best results when exploring unfamiliar topics
- Use `search` for quick lookups of specific terms
- Always check relevant project CLAUDE.md files for current context
- Combine QMD results with direct file reads for full picture
