# Slide

[Slide](https://www.getslide.pro) is an AI presentation maker. This repository is the public skill and plugin. The presentation is built by the remote MCP server at `https://www.getslide.pro/mcp`. Sign in with a Slide account on the first call.

## Install the skill

```bash
npx skills add arielcohen4/slide
```

That installs the skill for Claude Code, Cursor, Codex, and the other agents the `skills` CLI supports.

## Connect the MCP server

```json
{
  "mcpServers": {
    "Slide": {
      "url": "https://www.getslide.pro/mcp"
    }
  }
}
```

Claude Code:

```bash
claude mcp add --transport http slide https://www.getslide.pro/mcp
```

Cursor can also install this repository as a plugin. The plugin bundles the skill and the same remote server.
