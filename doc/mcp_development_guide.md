# MCP Development Guide (n8n-MCP)

## Table of Contents
- [Purpose](#purpose)
- [Project Orientation](#project-orientation)
- [Architecture Overview](#architecture-overview)
- [Repository Structure](#repository-structure)
- [Learning Plan](#learning-plan)
- [Deep-Dive Guides](#deep-dive-guides)
  - [Architecture Deep Dive](#architecture-deep-dive)
  - [Transport & Entry Points](#transport--entry-points)
  - [Tooling System](#tooling-system)
  - [Data Layer](#data-layer)
  - [Validation & Error Handling](#validation--error-handling)
  - [Telemetry & Observability](#telemetry--observability)
  - [Performance & Payload Optimization](#performance--payload-optimization)
  - [Session Persistence](#session-persistence)
- [Suggested Next Exercises](#suggested-next-exercises)

## Purpose
This guide consolidates how MCP development works in the n8n-MCP codebase. It includes a learning plan, architectural notes, and code snippets for every core concept implemented here.

## Project Orientation
n8n-MCP is a Model Context Protocol (MCP) server that exposes n8n node documentation, schemas, and management capabilities as MCP tools. It is designed to run in stdio mode for desktop MCP clients and can be configured with an n8n API connection for management tools.

## Architecture Overview
The full architectural deep dive is documented in the guide below, including request lifecycle, database strategy, and runtime components:

- [Architecture Deep Dive](./mcp_development_guide/architecture.md)

## Repository Structure
The repository layout and key runtime paths are documented in:

- [Architecture Deep Dive](./mcp_development_guide/architecture.md)

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

## Deep-Dive Guides
Use the following guides for detailed, code-heavy explanations of each concept:

- [Architecture Deep Dive](./mcp_development_guide/architecture.md)
- [Transport & Entry Points](./mcp_development_guide/transport.md)
- [Tooling System](./mcp_development_guide/tools.md)
- [Data Layer](./mcp_development_guide/data_layer.md)
- [Validation & Error Handling](./mcp_development_guide/validation_errors.md)
- [Telemetry & Observability](./mcp_development_guide/telemetry.md)
- [Performance & Payload Optimization](./mcp_development_guide/optimization.md)
- [Session Persistence](./mcp_development_guide/session_persistence.md)

## Suggested Next Exercises
1. Trace a specific tool end-to-end (definition → handler → response) using `search_nodes`.
2. Add a small custom tool with a simple schema and verify it appears in tool listings.
3. Experiment with protocol negotiation by mocking a client requesting different versions.
4. Explore the session persistence guide and implement encrypted session storage in a test app.
