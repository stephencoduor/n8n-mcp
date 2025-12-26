# n8n-MCP Comprehensive Documentation

> **Audience:** Engineers, technical writers, platform operators, and AI-assistant integrators.
> 
> **Goal:** Provide a complete overview and deep dive of n8n-MCP, covering features, architecture, data model, operational behavior, and a folder‑by‑folder codebase walkthrough.

---

## Table of Contents

### Overview (This Document)
1. [Executive Summary](#1-executive-summary)
2. [Core Capabilities](#2-core-capabilities)
3. [Architecture at a Glance](#3-architecture-at-a-glance)
4. [How to Read the Deep Dive](#4-how-to-read-the-deep-dive)

### Deep‑Dive Pages (Linked)
- [Feature Deep Dive](./deep-dive/features.md)
- [Architecture & Runtime Flows](./deep-dive/architecture.md)
- [Data Model & Database](./deep-dive/data-model.md)
- [Folder‑by‑Folder Codebase Map](./deep-dive/folder-by-folder.md)
- [Operational Guide & Deployment](./deep-dive/operations.md)
- [Tooling, Validation, and Safety](./deep-dive/tooling-and-safety.md)

### Section Links in This Page
- [Capabilities Index](#2-core-capabilities)
- [Architecture Blocks](#3-architecture-at-a-glance)

---

## 1. Executive Summary

**n8n-MCP** is a Model Context Protocol (MCP) server that exposes a curated, structured, and validated knowledge base about **n8n workflow automation nodes**. It enables AI assistants to interact with n8n documentation, schemas, examples, and workflow tooling in a safe, searchable, and machine‑readable format.

Key highlights:

- Ships with a **prebuilt SQLite database** of node metadata, properties, operations, documentation, and templates.
- Exposes **MCP tools** for node discovery, documentation lookup, property/operation analysis, and workflow validation.
- Can optionally connect to a live **n8n API** to provide management capabilities.
- Includes **safety, validation, telemetry, and sanitization** layers for reliable AI-assisted workflow operations.

---

## 2. Core Capabilities

This section is a high‑level index of capabilities. Full technical details are in the linked deep‑dive pages.

### 2.1 Documentation & Metadata Access
- Lookup node documentation, display names, categories, and descriptions.
- Retrieve property schemas and operation lists.
- Provide AI‑friendly guidance and examples.

### 2.2 Template and Example Retrieval
- Access pre-extracted workflow templates.
- Provide structured example configurations.
- Support use cases like “show me a webhook trigger workflow.”

### 2.3 Workflow Validation & Diffing
- Validate workflow JSON structures.
- Detect breaking changes and version mismatches.
- Compute workflow diffs and node-level changes.

### 2.4 Similarity, Discovery, and AI Tooling
- Similarity scoring to find nodes by intent.
- AI-tool detection and guidance for tool variants.
- Auto-suggestion engine for alternative nodes/operations.

### 2.5 Optional Live Management Tools
- With n8n API configuration, tools can read or update live workflows.
- Instance-aware operations with safe guards and validation.

---

## 3. Architecture at a Glance

n8n-MCP is organized into distinct layers:

```
[ MCP Client ]
    │
    ▼
[ MCP Server ] → [ Tools & Handlers ] → [ Services & Validation ]
    │                               │
    ▼                               ▼
[ SQLite DB + Templates ]        [ Telemetry & Safety ]
    │
    ▼
[ Optional n8n API ]
```

**Key architectural principles:**

- **Separation of concerns:** MCP protocol handling is isolated from business logic and from persistence.
- **Data-driven responses:** Node details are resolved from the SQLite database and template repositories.
- **Validation-first design:** Most tool responses are filtered through structured schemas to reduce ambiguity and errors.
- **Safety by default:** Sanitization and validation occur before returning data or performing actions.

For detailed request flows and module responsibilities, see [Architecture & Runtime Flows](./deep-dive/architecture.md).

---

## 4. How to Read the Deep Dive

This documentation set is intentionally **layered**:

1. **Feature Deep Dive** (what the system does and how tools are designed)
2. **Architecture & Runtime Flows** (how requests move across layers)
3. **Data Model & Database** (schema and data sources)
4. **Folder‑by‑Folder Codebase Map** (where to find everything)
5. **Operational Guide & Deployment** (how to run, update, and deploy)
6. **Tooling, Validation, and Safety** (how correctness and safety are enforced)

This structure enables multiple reading paths:

- **New engineer:** start with Feature Deep Dive → Folder Map.
- **Platform operator:** start with Operations → Architecture.
- **Contributor:** start with Folder Map → Tooling and Safety.

---

## Appendix: Version & Scope Notes

- This documentation describes the repository at a **point-in-time snapshot**.
- Always consult the latest `README.md` and `CHANGELOG.md` for updates.
- Exact node counts and coverage metrics may change as new data is generated.

---

**Next:** [Feature Deep Dive](./deep-dive/features.md)
