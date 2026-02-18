# MCP Server Setup

MCP (Model Context Protocol) servers extend Claude Code with additional tools. Configure in `~/.claude/settings.json`.

## QMD - Local Knowledge Base

Indexes your files for semantic search. Claude can search your notes and projects.

### Install

```bash
npm install -g qmd
```

### Configure Collections

Create `~/.config/qmd/index.yml`:

```yaml
collections:
  notes:
    path: ~/workspace/notes
    patterns: ["**/*.md"]
  projects:
    path: ~/workspace/projects
    patterns: ["**/*.md"]
```

### Index Content

```bash
qmd index
```

Run after adding significant new content.

### Add to Claude Settings

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
```

Add a QMD section to `~/.claude/CLAUDE.md` so Claude knows to use it.

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

All servers follow the same `settings.json` configuration pattern.
