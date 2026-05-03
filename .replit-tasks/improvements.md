# Replit Agent Task Spec — ghidramcp

## Instructions for Replit Agent
Read this file carefully before touching any code.
Commit all changes with prefix "replit: " and push to main when done.

## Stack Rules
- Python 3.10+
- MCP SDK: mcp>=1.5.0 (requirements.txt updated ✅)
- Java/Gradle for the Ghidra plugin side (do not touch Java code)
- Commit and push every change

## Context
ghidraMCP is an MCP server that lets LLMs autonomously reverse engineer binaries via Ghidra.
It bridges Claude/other LLMs to Ghidra's decompiler via an HTTP API.

Architecture:
- `bridge_mcp_ghidra.py` — Python MCP server (FastMCP), talks to Ghidra HTTP server
- `src/` — Java Ghidra plugin that exposes HTTP endpoints on port 8080
- MCP tools: list_methods, list_classes, decompile_function, rename_function,
  rename_data, list_segments, list_imports, list_exports, list_namespaces,
  list_data_items, search_functions_by_name, rename_variable,
  get_function_by_address, get_current_address, get_current_function,
  list_functions, decompile_function_by_address, + more

MCP version: already updated to mcp>=1.5.0 ✅
FastMCP API: already using modern @mcp.tool() decorators ✅

## Tasks for Replit Agent

### Task 1 — Claude Code MCP Config
Create `.claude/mcp.json` (or update if exists) with Claude Code MCP server config:
```json
{
  "mcpServers": {
    "ghidra": {
      "command": "python3",
      "args": ["bridge_mcp_ghidra.py"],
      "env": {}
    }
  }
}
```
Also create `claude-code-setup.md` with instructions for using ghidraMCP with Claude Code.

### Task 2 — Add MCP Resources
In `bridge_mcp_ghidra.py`, add MCP resources alongside tools (mcp 1.5 feature):
```python
@mcp.resource("ghidra://program/info")
def get_program_info() -> str:
    """Get current program metadata (name, architecture, base address)."""
    ...
```
Add resources for: program info, current selection, bookmarks list.

### Task 3 — Improve Tool Descriptions
All @mcp.tool() docstrings are terse. Expand each to include:
- What it does
- Parameter descriptions
- Example return value format
Better docstrings = better LLM tool selection.

### Task 4 — Error Handling
Wrap `safe_get` and `safe_post` calls in all tools with better error messages.
Currently errors return plain strings mixed with results.
Return structured errors: `{"error": true, "message": "...", "tool": "tool_name"}`.

### Task 5 — README Integration Section
Add a "Claude Code Integration" section to README.md:
- How to add ghidraMCP as an MCP server in Claude Code
- Example prompts that work well with the tools
- Known limitations

## Do NOT touch
- Java source in `src/` — Ghidra plugin code
- `pom.xml` / `build.yml` — Java build config
- The `safe_get`/`safe_post` function signatures
