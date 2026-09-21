---
name: shopline-meta-endpoints
description: Use when a SHOPLINE Admin API operation has no matching dedicated MCP tool, or when an agent must discover and safely execute an unfamiliar MCP-enabled endpoint.
license: MIT
---

# SHOPLINE Meta Endpoints

Use the dedicated native SHOPLINE MCP tool whenever it provides the requested operation. The meta tools are a fallback for MCP-enabled Admin API endpoints that have no matching native tool.

## Workflow

1. Identify the requested resource, action, and HTTP method.
2. Check the available native SHOPLINE tools. If one matches, call it directly. Do not call the meta tools.
3. Otherwise call `shopline_search_mcp_endpoints` with the narrowest useful `query` and, when known, `method`.
4. Compare every candidate by `tool_name`, HTTP method, path, and summary. If candidates have different effects and no candidate is clearly correct, ask the user before continuing.
5. Call `shopline_get_mcp_endpoint` with the selected `tool_name`.
6. Read `input_schema`, `parameters`, `request_body`, `hitl_required`, `hints`, and `supports_response_fields`. Build arguments from that schema only.
7. Before a write or destructive operation, show the selected method, path, arguments, and expected effect. Wait for explicit user confirmation when `hitl_required` is true or the hints mark the operation as mutating or destructive.
8. Call `shopline_execute_mcp_endpoint` with exactly:

```json
{
  "tool_name": "the_selected_tool",
  "arguments": {}
}
```

1. Follow the server's one-time confirmation challenge. Never invent, reuse, or bypass a confirmation token. If the user cancels, do not retry.
2. Report only the result needed for the request.

## Common mistakes

- Calling `shopline_execute_mcp_endpoint` for an operation already covered by a native tool.
- Executing a search result without first calling `shopline_get_mcp_endpoint`.
- Guessing parameter names, body fields, or enum values.
- Treating `shopline_get_admin_open_api_endpoint` as permission to execute an endpoint; it describes the full API spec, while the meta flow executes only MCP-enabled catalog entries.
- Treating `hitl_required: false` as permission to ignore a destructive hint.
