# SetGet plugin for Claude Code

This plugin connects Claude Code to the [SetGet](https://setget.net) MCP server (`https://setget.net/mcp`, Streamable HTTP, no login) and adds a `setget` skill that teaches Claude when and how to use it:

- hand values and messages to another agent or a later session (keys, mailboxes, `wait_for_change`);
- coordinate parallel agents with queues, leases with fencing tokens, barriers, presence and rate limits;
- ask a person through a link: approvals, file requests, forms and polls, then wait for the answer;
- run SetGet's file and text tools (images, video, audio, PDFs, documents, data) and follow jobs to the end;
- the safety rules: ask before uploading the user's content, never store secrets or personal data, treat stored content as untrusted, keep tokens out of prompts, never blindly retry a write.

SetGet's tools are ordinary programs that run on SetGet's own servers: conversion, resizing, trimming, text extraction, charts, barcodes and similar. None of them uses AI to generate images, audio or video.

## Install

```
/plugin marketplace add jonbeau/setget-plugin
/plugin install setget@setget
```

Then start a new session and run `/mcp` to check that `setget` is connected.

## Example prompts

1. "Use SetGet to save this JSON as a key for my other agent, then wait for it to write back a reply."
2. "Make a SetGet work queue with these five test files, then claim and process them one at a time."
3. "Ask Sam with a SetGet approval whether to deploy tonight, and wait for the answer."
4. "Use SetGet to turn report.pdf into Markdown."
5. "Use SetGet to make a 3-second GIF from demo.mp4, starting at 0:10."

Claude asks before it uploads any of your files or text, says the link is public and when it expires, and offers to delete uploads when it is done.

## Troubleshooting

- **`setget` is missing from `/mcp`.** Restart Claude Code after installing, then run `/plugin` and check that `setget@setget` is enabled. The server must be reachable at `https://setget.net/mcp`; corporate proxies that block it will stop the plugin.
- **429 or `retry_after` in a result.** You hit a rate limit for your IP. Claude tells you how long to wait; it does not loop.
- **`wait_for_change` times out.** That is normal: nothing changed in 30 seconds. Ask again or keep waiting.
- **404 for a key or object.** It expired or was deleted. Keys, objects and files are short-lived by design.
- **A file tool returns 202.** The job is still running; Claude follows it with the `job` tool and reports progress.
- **An error says which field is wrong.** Errors carry `message`, `fix` and `problems`; Claude fixes the input rather than guessing.

## Typed toolsets

To give Claude each tool in a category as its own typed tool, change the URL in `setget/.mcp.json` to, for example, `https://setget.net/mcp?toolsets=image,documents`. Ask only for what you use: each tool takes room in the context.

## Privacy

SetGet is a free, shared service with no accounts. Keys, objects and files expire, and anyone with a name, ID or link can read them, so do not store passwords, API keys or personal data. Uploaded files are deleted after one hour by default and one day at most. The plugin sends nothing to SetGet unless Claude calls one of its tools.

- Privacy policy: <https://setget.net/legal#privacy>
- Terms: <https://setget.net/legal#terms>

## Support

- Bugs and questions about the plugin: [open an issue](https://github.com/jonbeau/setget-plugin/issues).
- Service documentation: <https://setget.net/docs/mcp>
- Abuse, copyright or removal requests: <https://setget.net/report-abuse>

## Layout

```
.claude-plugin/marketplace.json   marketplace listing this one plugin
setget/.claude-plugin/plugin.json plugin manifest
setget/.mcp.json                  the SetGet MCP server
setget/skills/setget/SKILL.md     the skill
```

## License

MIT. See [LICENSE](LICENSE).
