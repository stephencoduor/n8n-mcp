# Architecture Deep Dive

## Table of Contents
- [Server Bootstrap](#server-bootstrap)
- [Protocol Negotiation](#protocol-negotiation)
- [Tool Routing & Execution](#tool-routing--execution)
- [Response Formatting & Limits](#response-formatting--limits)
- [Shutdown & Cleanup](#shutdown--cleanup)

## Server Bootstrap
The server boot process wires up the MCP server instance, initializes the database, and prepares tool handlers.

```ts
this.server = new Server(
  {
    name: 'n8n-documentation-mcp',
    version: PROJECT_VERSION,
    icons: [
      { src: 'https://www.n8n-mcp.com/logo.png', mimeType: 'image/png', sizes: ['192x192'] }
    ],
    websiteUrl: 'https://n8n-mcp.com'
  },
  { capabilities: { tools: {} } }
);
```

```ts
this.db = await createDatabaseAdapter(dbPath);
this.repository = new NodeRepository(this.db);
this.templateService = new TemplateService(this.db);
EnhancedConfigValidator.initializeSimilarityServices(this.repository);
```

## Protocol Negotiation
Protocol versions are negotiated for compatibility, with explicit support for n8n clients.

```ts
export const STANDARD_PROTOCOL_VERSION = '2025-03-26';
export const N8N_PROTOCOL_VERSION = '2024-11-05';

export function negotiateProtocolVersion(
  clientRequestedVersion?: string,
  clientInfo?: ClientInfo,
  userAgent?: string,
  headers?: Record<string, string | string[] | undefined>
): ProtocolNegotiationResult {
  const isN8n = isN8nClient(clientInfo, userAgent, headers);

  if (isN8n) {
    return {
      version: N8N_PROTOCOL_VERSION,
      isN8nClient: true,
      reasoning: 'n8n client detected, using n8n-compatible protocol version'
    };
  }

  if (clientRequestedVersion && SUPPORTED_VERSIONS.includes(clientRequestedVersion)) {
    return {
      version: clientRequestedVersion,
      isN8nClient: false,
      reasoning: `Using client-requested version: ${clientRequestedVersion}`
    };
  }

  return {
    version: STANDARD_PROTOCOL_VERSION,
    isN8nClient: false,
    reasoning: 'No specific client detected, using standard protocol version'
  };
}
```

## Tool Routing & Execution
Tool requests are routed through the MCP server handlers and normalized before execution.

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

## Response Formatting & Limits
Tool responses are serialized, and large responses are truncated to avoid client limits.

```ts
if (responseText.length > 1000000) {
  responseText = responseText.substring(0, 999000) + '\n\n[Response truncated due to size limits]';
  structuredContent = null;
}
```

## Shutdown & Cleanup
The server releases resources in a defined order to prevent leaks and corrupt state.

```ts
await this.server.close();
this.cache.destroy();
this.db?.close();
this.db = null;
this.repository = null;
this.templateService = null;
```
