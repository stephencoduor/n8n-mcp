# Tooling System Deep Dive

## Table of Contents
- [Tool Definitions](#tool-definitions)
- [Tool Documentation](#tool-documentation)
- [Tool Listing](#tool-listing)
- [Tool Execution](#tool-execution)
- [Validation Tool Output](#validation-tool-output)

## Tool Definitions
Tools are defined with JSON schemas, descriptions, and annotations.

```ts
export const n8nDocumentationToolsFinal: ToolDefinition[] = [
  {
    name: 'search_nodes',
    description: 'Search n8n nodes by keyword with optional real-world examples.',
    inputSchema: {
      type: 'object',
      properties: {
        query: { type: 'string' },
        limit: { type: 'number', default: 20 },
      },
      required: ['query'],
    },
    annotations: {
      title: 'Search Nodes',
      readOnlyHint: true,
      idempotentHint: true,
    },
  },
];
```

## Tool Documentation
The `tools_documentation` meta-tool returns documentation about tools, including itself.

```ts
export const toolsDocumentationDoc: ToolDocumentation = {
  name: 'tools_documentation',
  essentials: {
    description: 'The meta-documentation tool. Returns documentation for any MCP tool, including itself.',
    keyParameters: ['topic', 'depth'],
    example: 'tools_documentation({topic: "search_nodes"})',
  },
};
```

## Tool Listing
Tools are filtered based on `DISABLED_TOOLS` and the availability of n8n API configuration.

```ts
const enabledDocTools = n8nDocumentationToolsFinal.filter(
  tool => !disabledTools.has(tool.name)
);

const shouldIncludeManagementTools = hasEnvConfig || hasInstanceConfig || isMultiTenantEnabled;
```

## Tool Execution
Tool calls are handled by the MCP server and return JSON-RPC responses.

```ts
this.server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const { name, arguments: args } = request.params;
  const result = await this.executeTool(name, args);
  return {
    content: [
      { type: 'text', text: typeof result === 'string' ? result : JSON.stringify(result, null, 2) }
    ]
  };
});
```

## Validation Tool Output
Validation tools return structured output to comply with MCP output schemas.

```ts
if (name.startsWith('validate_') && structuredContent !== null) {
  mcpResponse.structuredContent = structuredContent;
}
```
