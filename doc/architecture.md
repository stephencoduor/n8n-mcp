# n8n-MCP Architecture

## Table of Contents
- [Purpose](#purpose)
- [System Overview](#system-overview)
- [High-Level Data Flow](#high-level-data-flow)
- [Runtime Components](#runtime-components)
- [Request Lifecycle](#request-lifecycle)
- [Storage & Indexing](#storage--indexing)
- [Telemetry & Observability](#telemetry--observability)
- [Repository Structure](#repository-structure)
  - [Root-Level Map](#root-level-map)
  - [Key Subdirectories](#key-subdirectories)
- [Key Runtime Paths](#key-runtime-paths)

## Purpose
This document provides a detailed architecture view of the n8n-MCP server, with emphasis on runtime flow, data storage, and repository layout.

## System Overview
n8n-MCP is a Model Context Protocol (MCP) server that exposes n8n node documentation, schemas, and management capabilities as MCP tools. It can run in stdio mode (for desktop MCP clients) or HTTP mode (for server deployments). The server relies on a prebuilt SQLite database of n8n node metadata and templates, providing fast search and rich documentation responses.

## High-Level Data Flow
1. The MCP client starts the server (stdio or HTTP mode).
2. The server initializes the SQLite database and supporting services.
3. The client completes the MCP Initialize handshake; the server negotiates protocol versions.
4. The client lists available tools and calls tools for node data, validation, or workflow management.
5. Tool handlers query the database, apply validation/filters, and return JSON-RPC responses.

## Runtime Components
- **Transport wrapper**: Handles stdio setup, suppresses non-JSON output, and traps shutdown signals.
- **MCP server**: Registers handlers for Initialize, ListTools, and CallTool, and executes tool logic.
- **Database layer**: Loads `nodes.db` and exposes FTS-backed search for node docs/properties.
- **Services**: Validation, property filtering, dependency analysis, template support.
- **Telemetry**: Tracks sessions, tool usage, error events, and startup checkpoints.

## Request Lifecycle
1. **Initialize**
   - Client sends protocol version and capabilities.
   - Server negotiates version compatibility and returns server info.
2. **ListTools**
   - Server filters tool availability based on configuration.
   - Returns tool schemas and metadata to the client.
3. **CallTool**
   - Server validates/normalizes arguments.
   - Tool executes and returns JSON response (with structured content for schema tools).

## Storage & Indexing
- **SQLite nodes database**: Prebuilt `nodes.db` with documentation, schemas, and templates.
- **FTS5 index**: Enables fast keyword search across documentation and node properties.
- **Fallback adapter**: If native `better-sqlite3` is unavailable, the server falls back to `sql.js`.

## Telemetry & Observability
- Tracks session start/end, tool usage, tool sequence timing, and error categories.
- Logging is minimized in stdio mode to prevent JSON-RPC output corruption.

## Repository Structure

### Root-Level Map
- `src/`: Core server implementation, tools, services, and utilities.
- `docs/`: Public documentation and setup guides.
- `doc/`: Internal developer documentation (architecture, development guide).
- `data/`: Prebuilt node database and assets.
- `tests/`: Test suites for tools, protocol compliance, and integrations.
- `dist/`: Compiled output for runtime builds.
- `examples/`: Example scripts and usage patterns.
- `scripts/`: Build and data maintenance scripts.
- `docker/`, `Dockerfile*`: Containerization and deployment assets.

### Key Subdirectories
- `src/mcp/`: MCP server, tool definitions, tool docs, and handlers.
- `src/utils/`: Shared helpers (logging, validation, protocol negotiation, errors).
- `src/database/`: SQLite adapters, schema access, and DB tooling.
- `src/services/`: Domain services (validation, dependency analysis, config filters).
- `src/templates/`: Template extraction, examples, and workflow samples.
- `src/telemetry/`: Event tracking, checkpoints, and telemetry helpers.
- `docs/`: User-facing docs (setup guides, essentials, deployment).

## Key Runtime Paths
- **Entry point (stdio)**: `src/mcp/stdio-wrapper.ts`
- **Server core**: `src/mcp/server.ts`
- **Tool definitions**: `src/mcp/tools.ts`
- **Protocol negotiation**: `src/utils/protocol-version.ts`
- **Validation utilities**: `src/utils/validation-schemas.ts`
- **Database adapter**: `src/database/database-adapter.ts`
- **Telemetry**: `src/telemetry/`
