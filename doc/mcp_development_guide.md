# MCP Development Guide (n8n-MCP)

## Table of Contents
- [Purpose](#purpose)
- [Project Orientation](#project-orientation)
- [Architecture Overview](#architecture-overview)
  - [Data Flow (High Level)](#data-flow-high-level)
  - [Runtime Components](#runtime-components)
  - [Request Lifecycle](#request-lifecycle)
  - [Storage & Indexing](#storage--indexing)
  - [Telemetry & Observability](#telemetry--observability)
- [Repository Structure](#repository-structure)
  - [Root-Level Map](#root-level-map)
  - [Key Subdirectories](#key-subdirectories)
- [Learning Plan](#learning-plan)
  - [Phase 1: Orientation & Goals](#phase-1-orientation--goals)
  - [Phase 2: Transport & Startup](#phase-2-transport--startup)
  - [Phase 3: MCP Server Core](#phase-3-mcp-server-core)
  - [Phase 4: Protocol Negotiation](#phase-4-protocol-negotiation)
  - [Phase 5: Tooling System](#phase-5-tooling-system)
  - [Phase 6: Tool Execution Pipeline](#phase-6-tool-execution-pipeline)
  - [Phase 7: Validation & Errors](#phase-7-validation--errors)
  - [Phase 8: Data Layer](#phase-8-data-layer)
  - [Phase 9: Advanced & Production Features](#phase-9-advanced--production-features)
- [Concepts & Code Snippets](#concepts--code-snippets)
  - [1) Stdio Transport and Clean Output](#1-stdio-transport-and-clean-output)
  - [2) MCP Server Initialization](#2-mcp-server-initialization)
  - [3) Protocol Version Negotiation](#3-protocol-version-negotiation)
  - [4) MCP Initialize Handshake](#4-mcp-initialize-handshake)
  - [5) Tool Definitions and Schemas](#5-tool-definitions-and-schemas)
  - [6) Tool Discovery via Meta-Docs](#6-tool-discovery-via-meta-docs)
  - [7) Tool Listing & Conditional Availability](#7-tool-listing--conditional-availability)
  - [8) Tool Execution & Response Formatting](#8-tool-execution--response-formatting)
  - [9) Input Validation Utilities](#9-input-validation-utilities)
  - [10) Error Handling Strategy](#10-error-handling-strategy)
  - [11) Database Adapter Abstraction](#11-database-adapter-abstraction)
  - [12) Payload Optimization with Essentials Tools](#12-payload-optimization-with-essentials-tools)
  - [13) Session Persistence for Multi-Tenant Deployments](#13-session-persistence-for-multi-tenant-deployments)
- [Suggested Next Exercises](#suggested-next-exercises)

## Purpose
This guide consolidates how MCP development works in the n8n-MCP codebase. It includes a learning plan, architectural notes, and code snippets for every core concept implemented here.

## Project Orientation
n8n-MCP is a Model Context Protocol (MCP) server that exposes n8n node documentation, schemas, and management capabilities as MCP tools. It is designed to run in stdio mode for desktop MCP clients and can be configured with an n8n API connection for management tools.

## Architecture Overview

### Data Flow (High Level)
1. MCP client launches the server in stdio or HTTP mode (stdio is recommended for desktop tools).
2. The server initializes the nodes database and registers tool handlers.
3. The client performs MCP initialization; the server negotiates protocol version compatibility.
4. The client lists tools and invokes tool calls to fetch documentation, validate configs, or manage workflows.
5. The server queries the node database, applies validation, and returns JSON-RPC results.

### Runtime Components
- **Transport wrapper**: Ensures clean JSON-RPC output for stdio mode and manages shutdown signals.
- **MCP server**: Registers Initialize/ListTools/CallTool handlers and implements tool execution.
- **Database layer**: Loads `nodes.db` and provides FTS-backed search for node docs and properties.
- **Services**: Validation, dependency analysis, templates, and property filtering.
- **Telemetry**: Tracks session lifecycle, tool usage, and error events.

### Request Lifecycle
1. **Initialize**: Protocol negotiation and capability declaration.
2. **ListTools**: Filters tools (optionally by env config) and returns schemas.
3. **CallTool**: Normalizes arguments, runs tool logic, formats output (structured content if required).

### Storage & Indexing
- SQLite database with FTS5 index for fast search across node metadata and documentation.
- Fallback to `sql.js` when native `better-sqlite3` is unavailable.

### Telemetry & Observability
- Tracks session start, tool usage, tool sequences, and error categories for operational insight.
- Logging is suppressed in stdio mode to prevent JSON-RPC corruption.

## Repository Structure

### Root-Level Map
- `src/`: Core server implementation, tools, services, and utilities.
- `docs/`: Public documentation and setup guides.
- `doc/`: Internal developer documentation (this guide).
- `data/`: Prebuilt node database and assets.
- `tests/`: Test suites for tools, protocol compliance, and integration.
- `dist/`: Compiled output for runtime builds.
- `examples/`: Example scripts and usage patterns.
- `scripts/`: Build and data maintenance scripts.
- `docker/`, `Dockerfile*`: Containerization and deployment artifacts.

### Key Subdirectories
- `src/mcp/`: MCP server, tool definitions, and tool documentation system.
- `src/utils/`: Shared helpers for logging, validation, protocol negotiation, and error handling.
- `src/database/`: SQLite adapters and schema tooling.
- `src/services/`: Domain services (validation, dependency resolution, config analysis).
- `src/templates/`: Template extraction and workflow examples.
- `src/telemetry/`: Event tracking and startup checkpoints.
- `docs/`: User-facing documentation, setup guides, and feature reference.

## Learning Plan

### Phase 1: Orientation & Goals
- Understand what the MCP server provides and which MCP clients it targets.
- Review the core promise: exposing n8n node documentation and operations as MCP tools.

### Phase 2: Transport & Startup
- Study the stdio wrapper and the strict JSON-only output requirement.
- Understand environment flags that control MCP transport and logging.

### Phase 3: MCP Server Core
- Follow the lifecycle: server construction, database initialization, and shutdown.
- Understand the caching and database health checks.

### Phase 4: Protocol Negotiation
- Learn how MCP protocol versions are negotiated for compatibility.
- Understand why n8n clients are pinned to a specific protocol version.

### Phase 5: Tooling System
- Learn how tools are defined with schemas and annotations.
- Discover the tool documentation system for discoverability.

### Phase 6: Tool Execution Pipeline
- Trace a tool call from request handling to response formatting.
- Learn how structured content is returned for schema-based tools.

### Phase 7: Validation & Errors
- Understand how input validation works for tool parameters.
- Learn the standardized error types and how errors are mapped.

### Phase 8: Data Layer
- Understand the database adapter and SQLite fallback strategy.
- Learn how the server loads the prebuilt node database.

### Phase 9: Advanced & Production Features
- Learn the essentials tools strategy for payload reduction.
- Study the session persistence API for multi-tenant deployments.

## Concepts & Code Snippets

### 1) Stdio Transport and Clean Output
The stdio wrapper ensures the server writes only JSON-RPC to stdout and suppresses all other console output to keep MCP communication clean.

```ts
process.env.MCP_MODE = 'stdio';
process.env.DISABLE_CONSOLE_OUTPUT = 'true';
process.env.LOG_LEVEL = 'error';

console.log = () => {};
console.error = () => {};
console.warn = () => {};
```

### 2) MCP Server Initialization
The server loads the node database, initializes services, and logs readiness.

```ts
this.db = await createDatabaseAdapter(dbPath);
this.repository = new NodeRepository(this.db);
this.templateService = new TemplateService(this.db);
EnhancedConfigValidator.initializeSimilarityServices(this.repository);
```

### 3) Protocol Version Negotiation
The server negotiates protocol versions and special-cases n8n-compatible clients.

```ts
export const STANDARD_PROTOCOL_VERSION = '2025-03-26';
export const N8N_PROTOCOL_VERSION = '2024-11-05';

if (isN8n) {
  return {
    version: N8N_PROTOCOL_VERSION,
    isN8nClient: true,
    reasoning: 'n8n client detected, using n8n-compatible protocol version'
  };
}
```

### 4) MCP Initialize Handshake
Initialization uses the negotiated protocol version and declares server capabilities.

```ts
const negotiationResult = negotiateProtocolVersion(clientVersion, clientInfo);
const response = {
  protocolVersion: negotiationResult.version,
  capabilities: { tools: {} },
  serverInfo: { name: 'n8n-documentation-mcp', version: PROJECT_VERSION },
};
```

### 5) Tool Definitions and Schemas
Tools are defined with JSON schemas and annotations to declare idempotency and read-only behavior.

```ts
{
  name: 'search_nodes',
  description: 'Search n8n nodes by keyword... ',
  inputSchema: {
    type: 'object',
    properties: {
      query: { type: 'string' },
      limit: { type: 'number', default: 20 }
    },
    required: ['query'],
  },
  annotations: {
    title: 'Search Nodes',
    readOnlyHint: true,
    idempotentHint: true,
  },
}
```

### 6) Tool Discovery via Meta-Docs
The `tools_documentation` tool provides self-referential documentation for all MCP tools.

```ts
description: 'The meta-documentation tool. Returns documentation for any MCP tool, including itself.'
examples: ['tools_documentation()', 'tools_documentation({topic: "search_nodes"})']
```

### 7) Tool Listing & Conditional Availability
The server dynamically includes management tools only when API credentials are configured.

```ts
const enabledDocTools = n8nDocumentationToolsFinal.filter(
  tool => !disabledTools.has(tool.name)
);

const shouldIncludeManagementTools = hasEnvConfig || hasInstanceConfig || isMultiTenantEnabled;
```

### 8) Tool Execution & Response Formatting
Tool execution wraps results into MCP responses and emits structured content when needed.

```ts
const result = await this.executeTool(name, processedArgs);

const mcpResponse: any = {
  content: [{ type: 'text', text: responseText }],
};

if (name.startsWith('validate_') && structuredContent !== null) {
  mcpResponse.structuredContent = structuredContent;
}
```

### 9) Input Validation Utilities
Validation utilities check types and required fields for tool parameters.

```ts
static validateString(value: any, fieldName: string, required: boolean = true): ValidationResult {
  if (required && (value === undefined || value === null)) {
    errors.push({ field: fieldName, message: `${fieldName} is required`, value });
  }
}
```

### 10) Error Handling Strategy
Errors are normalized into MCP error types with codes and HTTP-like status codes.

```ts
export class AuthenticationError extends MCPError {
  constructor(message: string = 'Authentication failed') {
    super(message, 'AUTH_ERROR', 401);
  }
}
```

### 11) Database Adapter Abstraction
The database adapter chooses `better-sqlite3` first and falls back to `sql.js` if needed.

```ts
try {
  const adapter = await createBetterSQLiteAdapter(dbPath);
  return adapter;
} catch {
  const adapter = await createSQLJSAdapter(dbPath);
  return adapter;
}
```

### 12) Payload Optimization with Essentials Tools
The `get_node_essentials` tool reduces payload size by focusing on the most important properties.

```json
{
  "name": "get_node_essentials",
  "arguments": {
    "nodeType": "nodes-base.httpRequest"
  }
}
```

### 13) Session Persistence for Multi-Tenant Deployments
Session state can be exported and restored to enable zero-downtime deployments.

```ts
const sessions = engine.exportSessionState();
const count = engine.restoreSessionState(sessions);
```

## Suggested Next Exercises
1. Trace a specific tool end-to-end (definition → handler → response) using `search_nodes`.
2. Add a small custom tool with a simple schema and verify it appears in tool listings.
3. Experiment with protocol negotiation by mocking a client requesting different versions.
4. Explore the session persistence guide and implement encrypted session storage in a test app.
