# Hypotize plugin

Turn your AI agent into a disciplined growth hacker. This plugin connects Claude, Codex, ChatGPT, Cursor, Grok Bot and other Agent Plugins clients to [Hypotize](https://hypotize.ai), the agent-native Growth OS.

Your agent reads the team's experiment backlog before proposing, writes hypothesis-first ideas, scores them with ICE, PIE, RICE or a custom framework, moves experiments through an enforced lifecycle and records measured outcomes and learnings.

## What is inside

- **MCP server**: `https://api.hypotize.ai/mcp`, 21 tools, OAuth 2.1. You sign in to Hypotize the first time the agent uses it.
- **Skills**:
  - `hypotize-growth-experiments`: the method. Read before proposing, vote with evidence, follow the lifecycle, close with a measured result.
  - `hypotize-weekly-review`: a read-only pipeline review that ends with what needs a decision.
  - `hypotize-backlog-import`: move a spreadsheet or Notion backlog into Hypotize, in atomic batches of 50.

Hypotize does not run A/B tests or assign traffic. It keeps the record before and after an experiment runs.

## Install

- **Claude Code**: `/plugin marketplace add hypotizeai/hypotize-plugin`, then `/plugin install hypotize`.
- **Codex**: `codex plugin marketplace add hypotizeai/hypotize-plugin`.
- **Cursor**: install Hypotize from the Cursor Marketplace, or add `https://api.hypotize.ai/mcp` in Settings → MCP.
- **Grok Bot**: open Connect apps and add Hypotize. If it is not listed yet, add a custom MCP server with `https://api.hypotize.ai/mcp`.
- **Any MCP client**: add `https://api.hypotize.ai/mcp` as a remote MCP server. Setup guides: https://hypotize.ai/mcp

## Links

- Website: https://hypotize.ai
- Privacy policy: https://hypotize.ai/privacy
- Terms of service: https://hypotize.ai/terms
- Support: hello@hypotize.ai
