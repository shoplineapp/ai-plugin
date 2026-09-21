---
name: shopline-response-fields
description: Use when a SHOPLINE MCP tool returns more data than the task needs, especially for read-only Admin API results, nested objects, or arrays where token use matters.
license: MIT
---

# SHOPLINE Response Fields

Reduce SHOPLINE responses with the tool's `response_fields` argument. Use it whenever the visible tool schema declares it. The server supports string dot paths and applies them to the upstream response.

## Rules

1. Read the native tool schema, or `supports_response_fields` from `shopline_get_mcp_endpoint`, before adding `response_fields`.
2. When the selected schema supports it, include the smallest complete set of fields needed to answer the user's request. Do not request guessed fields.
3. Pass an array of non-empty strings:

```json
{"response_fields":["id","customer.email"]}
```

1. Use dot notation for nested objects, such as `customer.email`.
2. Follow the selected schema's response shape. A dedicated wrapped response may require root paths such as `items.id` or `items.customer.email`.
3. A generated endpoint whose output is an array uses paths relative to each element. Use `id` or `product_id`, not `items.id`; nested element data uses paths such as `product.id`. Do not apply this convention to every list-shaped response.
4. Do not use array indexes, empty segments, numeric path segments, or bracket syntax such as `items[].id`.
5. Do not include `response_fields` for a write or for a tool whose schema does not support it. Do not assume that a similarly named API endpoint supports it.
6. Treat `response_fields` only as output projection, never as request filtering.
7. If the requested field is not in the visible input or output schema, inspect the endpoint contract or ask the user. Never silently substitute a guessed field.
8. Keep pagination and status metadata when it is needed to interpret the result; request only business fields that the user needs otherwise.

## Examples

- Wrapped order list: `{"response_fields":["items.id","items.customer.email"]}` when those paths are in the tool schema.
- Single order: `{"response_fields":["id","customer.email"]}`.
- Generated product array: `{"response_fields":["id"]}` (not `items.id`) when the schema describes an array root.
- User coupon list: `{"response_fields":["user_coupons.user_id","next_cursor_id"]}` when those paths are present in that tool's contract.

The server validates paths for unsafe or numeric segments and returns a tool error for invalid paths. Do not retry with a guessed path; inspect the schema and correct the request.
