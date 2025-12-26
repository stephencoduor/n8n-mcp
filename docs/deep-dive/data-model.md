# Data Model & Database

> This document provides a deep dive into the SQLite database structure and the data sources powering n8n‑MCP.

---

## Table of Contents

1. [Database Overview](#1-database-overview)
2. [Schema Sources](#2-schema-sources)
3. [Core Entities](#3-core-entities)
4. [Node Properties and Operations](#4-node-properties-and-operations)
5. [Documentation and Examples](#5-documentation-and-examples)
6. [Versioning and Breaking Changes](#6-versioning-and-breaking-changes)
7. [Templates and Metadata](#7-templates-and-metadata)
8. [Storage and Runtime Access](#8-storage-and-runtime-access)

---

## 1. Database Overview

n8n‑MCP ships with a prebuilt SQLite database (typically `data/nodes.db`) that includes:

- Node metadata (types, categories, packages)
- Node property schemas (inputs, options, constraints)
- Operation definitions
- Documentation snapshots
- Template metadata and workflow examples
- Version histories and breaking change info

This database enables fast, deterministic tool responses without requiring live scraping.

---

## 2. Schema Sources

Schema definitions are maintained in:

- `src/database/schema.sql`
- `src/database/schema-optimized.sql`
- `src/database/migrations/`

The schema is optimized for **read-heavy** query workloads and may include indexes for search and filtering.

---

## 3. Core Entities

While exact table names and fields are detailed in schema files, the core concepts are:

### 3.1 Node Records

Each node entry typically includes:

- Node type identifier
- Package name
- Display name and description
- Category metadata
- Flags: trigger, webhook, AI tool, versioned, tool variant

### 3.2 Node Documentation

Documentation is stored as structured content that can be rendered into tool responses. This may include:

- Overview
- Property descriptions
- Examples
- Notes and warnings

### 3.3 Node Versions

Version information includes:

- Latest version
- Historical versions
- Breaking change metadata
- Migration guidance

---

## 4. Node Properties and Operations

Property and operation data is preprocessed into canonical schemas. Key elements include:

- **Property type** (string, number, boolean, collection, etc.)
- **Required flags**
- **Dependencies** (conditional visibility/requirements)
- **Operation groupings**

This allows validation tools to confirm workflow correctness and generate precise guidance.

---

## 5. Documentation and Examples

Documentation blobs often include:

- Narrative descriptions
- Use case patterns
- Configuration examples

Examples are stored in structured formats, enabling retrieval by node or workflow pattern.

---

## 6. Versioning and Breaking Changes

Version history tables track:

- Node version evolution
- Specific breaking changes
- Migration instructions

These are used by diffing and validation tools to guide safe upgrades.

---

## 7. Templates and Metadata

Workflow templates include:

- Template identifiers
- Category and tags
- Node usage metadata
- Downloaded workflow JSON (sanitized)

Template metadata supports search and recommendation in tool outputs.

---

## 8. Storage and Runtime Access

During runtime:

- `DatabaseAdapter` opens the SQLite file.
- `NodeRepository` executes queries based on tool requests.
- Results are cached and structured into MCP tool outputs.

---

**Next:** [Folder‑by‑Folder Codebase Map](./folder-by-folder.md)
