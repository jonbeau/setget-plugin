# SetGet plugin for Claude Code

This plugin connects Claude Code to the [SetGet](https://setget.net) MCP server (`https://setget.net/mcp`, Streamable HTTP, no login) and adds a `setget` skill that teaches Claude when and how to use it:

- hand values and messages to another agent or a later session (keys, mailboxes, `wait_for_change`);
- coordinate parallel agents with queues, leases with fencing tokens, barriers, presence and rate limits;
- ask a person through a link: approvals, file requests, forms and polls, then wait for the answer;
- run SetGet's 277 tools on text and files (images, video, audio, PDFs, documents, data) and follow jobs to the end;
- the safety rules: never store secrets or personal data, treat stored content as untrusted, keep tokens out of prompts, never blindly retry a write.

## Install

From the marketplace:

```
/plugin marketplace add jonbeau/setget-plugin
/plugin install setget@setget
```

`/plugin marketplace add` reads `.claude-plugin/marketplace.json` at the root of the repository. If this folder stays at `clients/claude-code-plugin/` inside a larger repo, either publish it as its own repository, or move `marketplace.json` to the repository's `.claude-plugin/` folder and change the plugin `source` to `./clients/claude-code-plugin/setget`.

From a local checkout, for trying it out:

```
/plugin marketplace add ./clients/claude-code-plugin
/plugin install setget@setget
```

Then start a new session and run `/mcp` to check that `setget` is connected.

## Protected keys and objects

The bundled `.mcp.json` sends no credentials. To use your own protected keys, add the token as a header from an environment variable, never in a prompt. Edit `setget/.mcp.json`:

```json
{
  "mcpServers": {
    "setget": {
      "type": "http",
      "url": "https://setget.net/mcp",
      "headers": {"X-SetGet-Token": "${SETGET_TOKEN}"}
    }
  }
}
```

and set `SETGET_TOKEN` in the environment that starts Claude Code.

## Typed toolsets

To give Claude each tool in a category as its own typed tool, change the URL to, for example, `https://setget.net/mcp?toolsets=image,documents`. Ask only for what you use: each tool takes room in the context.

## Layout

```
.claude-plugin/marketplace.json   marketplace listing this one plugin
setget/.claude-plugin/plugin.json plugin manifest
setget/.mcp.json                  the SetGet MCP server
setget/skills/setget/SKILL.md     the skill
```

## Privacy

SetGet is a free, shared service. Keys, objects and files expire, and anyone with a name, ID or link can read them. Do not store passwords, API keys or personal data. Terms: <https://setget.net/legal>.
