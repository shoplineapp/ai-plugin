# SHOPLINE Agent Plugin

A portable Agent Plugins 1.0.0 package for SHOPLINE Admin APIs. It provides two Agent Skills and one public SHOPLINE MCP server.

## Package tree

```text
.
├── .agents/plugins/marketplace.json
├── .claude-plugin/marketplace.json
├── .claude-plugin/plugin.json
├── .mcp.json
├── LICENSE
├── README.md
├── marketplace.json
├── mcp.json
├── plugin.json
└── skills/
    ├── shopline-meta-endpoints/SKILL.md
    └── shopline-response-fields/SKILL.md
```

## Operation

- Use a dedicated native SHOPLINE tool first.
- Use `shopline_search_mcp_endpoints` → `shopline_get_mcp_endpoint` → `shopline_execute_mcp_endpoint` only when no dedicated tool matches.
- Read the inspected schema before a meta call. Confirm writes or destructive calls when HITL requires it, and never bypass a confirmation challenge.
- When the selected schema supports `response_fields`, send the smallest schema-defined field set. Use dot notation and follow that schema's response shape: wrapped responses may use `items.id`, while generated array-root responses use element-relative `id`. Omit it when unsupported.

## Install

### Claude Code

```bash
claude plugin marketplace add shoplineapp/ai-plugin
claude plugin install shopline-plugin@SHOPLINE
```

### Claude Desktop

Open **Customize → Plugins → Personal plugins → + → Add marketplace from GitHub**, enter `shoplineapp/ai-plugin`, select **Sync**, then install `shopline-plugin`.

### Codex 0.146.0+

```bash
codex plugin marketplace add shoplineapp/ai-plugin
```

Install `shopline-plugin` from `/plugins` or the Codex app **Plugins** sidebar.

### ZCode

Open **Settings → Plugins → Create → Add marketplace**, enter `shoplineapp/ai-plugin`, then install `shopline-plugin`.

### OpenCode

OpenCode does not use this marketplace format. Add the SHOPLINE remote MCP server through OpenCode's supported MCP settings and expose these two skills through its supported explicit skill path. This repository does not claim one-command OpenCode marketplace installation.

Hermes is not a supported installation path for this package because its package contract has not been verified here.

## OAuth and credentials

The on-demand MCP connection allows initialization and tool discovery without credentials. The first protected tool call starts client-managed MCP OAuth discovery. Complete the browser flow in the client. This repository stores no access token, client secret, or authorization header; credentials remain in client-managed storage.

## Update and removal

Each client has its own installation. An installation in one client does not appear in another.

- **Claude Code:** `claude plugin update shopline-plugin`; remove with `claude plugin uninstall shopline-plugin`.
- **Claude Desktop:** use **Sync** for updates and **Uninstall** in its Plugins UI.
- **Codex:** run `codex plugin marketplace upgrade SHOPLINE`, then reinstall `shopline-plugin`; remove with `codex plugin remove shopline-plugin`.
- **ZCode:** refresh the marketplace in **Settings → Plugins**, then update or uninstall the plugin there.

## Verification

From this repository:

1. Validate both skills with `skills-ref validate skills/<skill-name>`.
2. Validate the Claude package with `claude plugin validate .`.
3. Parse all JSON manifests with `python3 -m json.tool`.
4. Confirm each marketplace exposes exactly one `shopline-plugin` entry.
5. Install in each available client, confirm the two namespaced skills and the SHOPLINE MCP tools, then complete OAuth.
6. Test one native read, the meta search → inspect → execute fallback, and minimal `response_fields` including a nested or array-relative path.
7. Test an unsupported projection without `response_fields`.
8. Start a write or destructive meta call, confirm that HITL stops execution, and cancel without changing data.

Live authenticated checks require a client to complete OAuth and a suitable SHOPLINE merchant account.
