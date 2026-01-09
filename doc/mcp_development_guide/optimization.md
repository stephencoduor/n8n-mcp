# Performance & Payload Optimization

## Table of Contents
- [Essentials Tools](#essentials-tools)
- [Property Search](#property-search)
- [Response Size Limits](#response-size-limits)

## Essentials Tools
The `get_node_essentials` tool reduces payload size by returning only the most important fields.

```json
{
  "name": "get_node_essentials",
  "arguments": {
    "nodeType": "nodes-base.httpRequest"
  }
}
```

## Property Search
Search tools allow targeted discovery of properties without retrieving full schemas.

```json
{
  "name": "search_node_properties",
  "arguments": {
    "nodeType": "nodes-base.httpRequest",
    "query": "auth"
  }
}
```

## Response Size Limits
Large responses are truncated to avoid client limits and prevent MCP failures.

```ts
if (responseText.length > 1000000) {
  responseText = responseText.substring(0, 999000) + '\n\n[Response truncated due to size limits]';
  structuredContent = null;
}
```
