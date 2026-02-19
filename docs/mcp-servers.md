# MCP Server Setup

MCP (Model Context Protocol) servers extend Claude Code with additional tools. Configure in `.mcp.json` (workspace-local) or `~/.claude/settings.json` (global).

## QMD - Local Knowledge Base

Indexes your files for semantic search. Claude can search your notes and projects entirely locally — no cloud, no API keys.

GitHub: [tobi/qmd](https://github.com/tobi/qmd)

### Install

```bash
npm install -g @tobilu/qmd
# or
bun install -g @tobilu/qmd
```

### Set Up Collections

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

### Add to Workspace Config

Add to `.mcp.json` in your workspace root (`/meos init` does this automatically):

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

### Usage

```bash
qmd query "topic"              # Best quality (hybrid + reranking)
qmd search "term" -c notes     # Fast keyword search
qmd vsearch "concept"          # Semantic vector search
qmd get "path/to/file" --full  # Get full document
```

### Re-indexing

After adding significant new content:
```bash
qmd embed        # Re-index and rebuild embeddings
qmd embed -f     # Force full re-embed
```

Once configured in `.mcp.json`, Claude will automatically have access to QMD tools in your workspace.

## Filesystem MCP

Extended file system access through MCP tools.

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "mcp-filesystem",
      "args": ["/path/to/allowed/directory"]
    }
  }
}
```

## Playwright - Browser Automation

Lets Claude control a web browser for testing and automation.

```json
{
  "mcpServers": {
    "playwright": {
      "command": "mcp-playwright",
      "args": []
    }
  }
}
```

## Finding More Servers

- [modelcontextprotocol.io](https://modelcontextprotocol.io) - Official directory
- [github.com/modelcontextprotocol](https://github.com/modelcontextprotocol) - Source repos

All servers follow the same configuration pattern — add to `.mcp.json` (workspace) or `~/.claude/settings.json` (global).
