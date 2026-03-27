Help the user set up MCP server connections for Claude Spam Warrior by creating or updating `.mcp.json` in the project root.

## Context

This workspace benefits from two types of MCP integration:

1. **Email provider MCP** — for reading emails directly (instead of manually exporting `.eml` files)
2. **Email filter MCP** — for pushing sender blocks to a server-side filter (e.g., blocking senders in Gmail, Fastmail, or a custom filtering service)

Without MCP, the workspace still works — users export `.eml` files manually and sender blocks are recorded locally only.

## Steps

1. **Check for existing config**: Read `.mcp.json` in the project root if it exists. Also note that user-level MCP servers in `~/.claude/.mcp.json` are always available.

2. **Ask the user** what they want to connect:
   - "Do you want to connect an **email provider** MCP server? (e.g., Gmail, Fastmail, Google Workspace)"
   - "Do you want to connect a **server-side email filter** MCP server? (e.g., for auto-blocking senders)"
   - "Do you have any other MCP servers you'd like to add?"

3. **For each server the user wants**, gather:
   - Server name / identifier
   - Transport type (`stdio` or `streamable-http`)
   - For `stdio`: the command and args to launch it
   - For `streamable-http`: the URL
   - Any required environment variables (API keys, tokens)

4. **Write `.mcp.json`** in the project root with the standard format:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "package-name"],
      "env": {
        "API_KEY": "..."
      }
    }
  }
}
```

Or for HTTP transport:

```json
{
  "mcpServers": {
    "server-name": {
      "type": "streamable-http",
      "url": "https://..."
    }
  }
}
```

5. **Remind the user**:
   - Environment variables with secrets should use references rather than hardcoded values where possible
   - They may need to restart Claude Code for new MCP servers to take effect
   - The `/analyse-email` command will automatically use available MCP tools for sender blocking when configured
