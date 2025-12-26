# Folder‑by‑Folder Codebase Map

> This document provides a detailed walkthrough of the repository structure, explaining responsibilities for each directory and subdirectory.

---

## Table of Contents

1. [Root Directory](#1-root-directory)
2. [`src/` Source](#2-src-source)
3. [`dist/` Build Output](#3-dist-build-output)
4. [`docs/` Documentation](#4-docs-documentation)
5. [`data/` Runtime Data](#5-data-runtime-data)
6. [`tests/` Test Suites](#6-tests-test-suites)
7. [`examples/` Examples](#7-examples)
8. [`scripts/` Utility Scripts](#8-scripts-utility-scripts)
9. [`types/` Shared Types](#9-types-shared-types)
10. [`docker/` and `deploy/`](#10-docker-and-deploy)

---

## 1. Root Directory

Key root files include:

- `README.md`: primary user-facing overview.
- `CHANGELOG.md`: release history.
- `package.json`: build/test scripts and dependencies.
- `Dockerfile`: production container definition.
- `docker-compose*.yml`: deployment compositions.
- `tsconfig*.json`: TypeScript configurations.

---

## 2. `src/` Source

Top-level entry points and systems:

- `index.ts`: library exports and public API surface.
- `mcp-engine.ts`: engine for starting the MCP server.
- `http-server.ts` / `http-server-single-session.ts`: HTTP transport.

### 2.1 `src/mcp/`

MCP server and tool definitions.

- `server.ts`: main MCP server implementation.
- `tools.ts`: documentation tools registry.
- `tools-documentation.ts`: tool documentation helpers.
- `tools-n8n-manager.ts`: optional n8n management tools.
- `handlers-*`: tool handlers for workflow diffing and management.
- `tool-docs/`: tool-specific doc metadata.

### 2.2 `src/database/`

SQLite management and schema.

- `database-adapter.ts`: DB connection and helpers.
- `node-repository.ts`: query logic for nodes and docs.
- `schema.sql`: baseline schema.
- `schema-optimized.sql`: optimized schema.
- `migrations/`: schema migrations.

### 2.3 `src/services/`

Domain services and validation logic.

Includes:

- Validation: `workflow-validator.ts`, `expression-validator.ts`.
- Diffing: `workflow-diff-engine.ts`.
- Similarity: `node-similarity-service.ts`, `operation-similarity-service.ts`.
- Versioning: `node-version-service.ts`, `workflow-versioning-service.ts`.
- Tool variants: `tool-variant-generator.ts`.
- Property helpers: `property-filter.ts`, `property-dependencies.ts`.

### 2.4 `src/utils/`

Shared helpers and infrastructure utilities.

- Logging: `logger.ts`, `console-manager.ts`.
- Validation: `validation-schemas.ts`, `fixed-collection-validator.ts`.
- Network safety: `ssrf-protection.ts`.
- Node utilities: `node-utils.ts`, `node-type-normalizer.ts`.

### 2.5 `src/templates/`

Template ingestion and storage.

- `template-service.ts`: public access to templates.
- `template-repository.ts`: data layer for template queries.
- `template-fetcher.ts`: remote retrieval of templates.
- `metadata-generator.ts`: template metadata extraction.

### 2.6 `src/telemetry/`

Telemetry capture and sanitization.

Includes:

- `telemetry-manager.ts`: central telemetry hub.
- `error-sanitizer.ts`: safe error logging.
- `rate-limiter.ts`: throttle logging.
- `workflow-sanitizer.ts`: scrub workflow data.

### 2.7 `src/triggers/`

Trigger analysis and registry.

- `trigger-detector.ts`: detect trigger-type nodes.
- `trigger-registry.ts`: registry of known triggers.
- `handlers/`: implementations for webhook/form/chat triggers.

### 2.8 `src/parsers/` & `src/loaders/`

Used for parsing node schemas and extracting documentation data.

- `node-parser.ts`: parse node definitions.
- `property-extractor.ts`: extract property metadata.
- `node-loader.ts`: load node definitions into the system.

### 2.9 `src/n8n/`

n8n‑specific definitions and node config wrappers.

### 2.10 `src/config/`

Configuration and environment bindings.

- `n8n-api.ts`: API connection configuration.

### 2.11 `src/types/`

Centralized type definitions used throughout the system.

---

## 3. `dist/` Build Output

Compiled JavaScript output (mirrors `src/`). Used for published builds and container images.

---

## 4. `docs/` Documentation

Repository documentation, guides, integration instructions, and setup docs. This deep‑dive documentation is located under `docs/` as well.

---

## 5. `data/` Runtime Data

Contains `nodes.db` and other data assets used during runtime. This folder is read by the MCP server at startup.

---

## 6. `tests/` Test Suites

Test structure includes:

- `unit/` tests
- `integration/` tests
- `benchmarks/`
- `fixtures/`, `mocks/`, `helpers/`

---

## 7. `examples/`

Example files and configs for users.

---

## 8. `scripts/` Utility Scripts

Automation and dev tooling (build, validate, rebuild DB, fetch templates).

---

## 9. `types/` Shared Types

Shared type definitions for use by integrations or tooling.

---

## 10. `docker/` and `deploy/`

Deployment artifacts and helpers for containerized or hosted environments.

---

**Next:** [Operational Guide & Deployment](./operations.md)
