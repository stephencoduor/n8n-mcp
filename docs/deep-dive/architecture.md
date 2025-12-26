# Architecture & Runtime Flows

> This document explains how n8n‑MCP is structured, how requests flow through the system, and how data is retrieved and validated.

---

## Table of Contents

1. [System Layers](#1-system-layers)
2. [Core Runtime Components](#2-core-runtime-components)
3. [Initialization Flow](#3-initialization-flow)
4. [Tool Call Flow](#4-tool-call-flow)
5. [Validation & Error Handling](#5-validation--error-handling)
6. [Caching & Performance](#6-caching--performance)
7. [Optional n8n API Integration](#7-optional-n8n-api-integration)

---

## 1. System Layers

n8n‑MCP follows a layered design:

1. **Protocol Layer**: MCP server and transports (stdio/HTTP).
2. **Tool Layer**: tool registry + tool handlers.
3. **Service Layer**: domain services (validation, similarity, diffing).
4. **Persistence Layer**: SQLite DB + template storage.
5. **Telemetry/Safety Layer**: sanitization, rate limiting, and logging.

This separation reduces coupling and makes it easier to update components independently.

---

## 2. Core Runtime Components

### 2.1 MCP Server

- **Class:** `N8NDocumentationMCPServer`
- **Role:** accepts MCP requests, lists tools, dispatches tool calls.
- **Transports:** stdio for local client, HTTP for network services.

### 2.2 Database Adapter

- Initializes and manages SQLite connection.
- Provides query helpers used by repository classes.

### 2.3 Repository Layer

- **NodeRepository** is the main query entry for node metadata, docs, operations, and versions.

### 2.4 Service Layer

Services encapsulate complex operations like validation, diffing, or similarity scoring. They are designed for reuse by multiple tools.

---

## 3. Initialization Flow

Initialization typically follows this sequence:

1. **Locate database** (environment override or default path).
2. **Initialize SQLite adapter** and repository classes.
3. **Load template service** (if templates are enabled).
4. **Register tool set** based on API configuration.
5. **Initialize telemetry checkpoints** for startup diagnostics.

This ensures the server is ready before responding to client tool requests.

---

## 4. Tool Call Flow

A typical tool call flow is:

1. MCP client sends `callTool` request.
2. Tool dispatcher identifies the handler and validates inputs.
3. Service layer performs necessary computation.
4. Repository layer queries DB or templates.
5. Output is structured and returned to client.

Key principles:

- **Determinism:** Tool output is based on precomputed data.
- **Structured results:** Avoid ambiguous responses by returning JSON schemas.
- **Validation-first:** Invalid inputs are rejected early.

---

## 5. Validation & Error Handling

Validation occurs at several layers:

- **Schema validation:** Ensure tool arguments match expected structure.
- **Workflow validation:** Check node types, properties, and expressions.
- **Sanitization:** Remove sensitive or malformed data before returning.

Errors are logged and returned in structured format to allow client recovery and retries.

---

## 6. Caching & Performance

n8n‑MCP uses a **simple in‑memory cache** to store:

- Frequently requested node details.
- Common query results (e.g., lists of nodes).

This reduces repeated DB access and improves responsiveness, especially for chat-based MCP clients.

---

## 7. Optional n8n API Integration

When the n8n API is configured:

- Tool list expands to include management operations.
- Handlers call out to live n8n APIs.
- Validation ensures safe request formats.

This mode is optional and must be explicitly enabled with environment variables.

---

**Next:** [Data Model & Database](./data-model.md)
