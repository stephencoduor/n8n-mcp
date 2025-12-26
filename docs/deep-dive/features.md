# Feature Deep Dive

> This document provides a **comprehensive, feature‑level explanation** of the n8n‑MCP system. It is designed to be read independently or after the overview.

---

## Table of Contents

1. [Documentation & Node Metadata Tools](#1-documentation--node-metadata-tools)
2. [Property & Operation Coverage](#2-property--operation-coverage)
3. [Template & Example Systems](#3-template--example-systems)
4. [Workflow Validation](#4-workflow-validation)
5. [Workflow Diffing & Versioning](#5-workflow-diffing--versioning)
6. [Similarity, Discovery, and Suggestions](#6-similarity-discovery-and-suggestions)
7. [AI Tooling and Tool Variants](#7-ai-tooling-and-tool-variants)
8. [Optional n8n Management Tools](#8-optional-n8n-management-tools)
9. [Caching, Performance, and Resilience](#9-caching-performance-and-resilience)

---

## 1. Documentation & Node Metadata Tools

**Objective:** Provide MCP tools that can answer questions like:

- “What does the HTTP Request node do?”
- “Which nodes are in the AI category?”
- “Which node versions exist and what changed?”

**Core capabilities:**

- **Node discovery:** list nodes by category, package, or keyword.
- **Metadata access:** retrieve display name, description, category, and node type.
- **Documentation access:** expose curated node docs as structured content.

These tools are designed to be deterministic and consistent. They rely on database‑backed retrieval rather than ad-hoc scraping at runtime, ensuring stable outputs across calls.

### Typical Output Structure

- **Node identifier** (e.g., `n8n-nodes-base.httpRequest`)
- **Display name** and **category**
- **Description** (high‑level summary)
- **Documentation** sections (overview, properties, examples)

---

## 2. Property & Operation Coverage

n8n-MCP standardizes node property data into structured schemas. This supports:

- **Required vs optional** properties
- **Property dependencies** (e.g., conditional fields)
- **Common property groups** for faster UI or LLM summaries

**Operations** represent the “actions” or “events” a node can perform. Coverage includes:

- Operation names and descriptions
- Required parameters per operation
- Operation‑level examples

This structure allows AI assistants to generate valid JSON configurations or guide users toward correct setups.

---

## 3. Template & Example Systems

Templates are essential for real-world usage. n8n‑MCP incorporates:

- **Workflow templates** with metadata for search and categorization
- **Example configurations** tied to specific nodes or workflows
- **Canonical AI tool examples** for high‑quality, deterministic guidance

### Example Use Cases

- “Show me a template that uses a webhook trigger and sends a Slack message.”
- “Find a template that uses an AI agent node with a memory component.”

Templates are pre‑processed and sanitized before inclusion to ensure consistent outputs.

---

## 4. Workflow Validation

**Workflow validation** ensures that:

- Node types are valid and known.
- Required properties are present.
- Expressions are syntactically valid.
- Property values meet constraints or type expectations.

Validation is critical for AI‑generated workflows, reducing invalid or fragile outputs. Results are typically returned with:

- **Error messages** categorized by severity.
- **Validation metadata** for confidence scoring.
- **Suggested fixes** where possible.

---

## 5. Workflow Diffing & Versioning

The diff engine supports:

- **Comparing two workflows** at a node and property level.
- **Detecting breaking changes** between node versions.
- **Guidance for migration** when nodes update.

This allows a tool to answer:

- “What changed between these two versions of my workflow?”
- “Is this node update compatible with my current workflow?”

Diffs are structured as lists of changes, enabling downstream tools to act programmatically.

---

## 6. Similarity, Discovery, and Suggestions

To improve node discovery:

- Similarity engines compare node properties and operations.
- The system can suggest **alternative nodes** when a requested node is not found.
- Operation similarity can suggest **related actions**.

This is especially useful for AI assistants that interpret vague user intent (e.g., “send a webhook” or “notify in chat”).

---

## 7. AI Tooling and Tool Variants

n8n includes AI‑specific nodes and tool variants. n8n-MCP provides:

- **AI tool detection:** flags nodes that are AI‑compatible.
- **Tool variant mapping:** links between standard nodes and tool versions.
- **Guidance strings:** help explain when to use tool variants.

This helps prevent confusion when similar node types exist in multiple contexts.

---

## 8. Optional n8n Management Tools

When configured with `N8N_API_URL` and `N8N_API_KEY`, additional tools become available:

- List workflows and their metadata.
- Retrieve and update workflow definitions.
- Validate or diff workflows directly in a live instance.

These features are **disabled by default** to preserve safety in untrusted environments.

---

## 9. Caching, Performance, and Resilience

To enable quick responses:

- Queries are cached in memory for repeat requests.
- Heavy computations are isolated into services.
- Input validation prevents expensive operations on malformed data.

The system is built to be stable for long‑running MCP sessions, including multi‑tenant use cases.

---

**Next:** [Architecture & Runtime Flows](./architecture.md)
