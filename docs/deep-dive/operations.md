# Operational Guide & Deployment

> This document focuses on running n8n‑MCP in production or development environments, including configuration, deployment options, and maintenance workflows.

---

## Table of Contents

1. [Supported Deployment Modes](#1-supported-deployment-modes)
2. [Local npx Execution](#2-local-npx-execution)
3. [Docker Deployment](#3-docker-deployment)
4. [HTTP Server Mode](#4-http-server-mode)
5. [Configuration & Environment Variables](#5-configuration--environment-variables)
6. [Database Updates & Rebuilds](#6-database-updates--rebuilds)
7. [Operational Safety](#7-operational-safety)
8. [Troubleshooting](#8-troubleshooting)

---

## 1. Supported Deployment Modes

n8n‑MCP supports multiple deployment styles:

- **Hosted service** (fastest, managed)
- **NPX local execution** (quick start)
- **Docker** (production-ready)
- **HTTP server** (integration with HTTP-based MCP clients)

---

## 2. Local npx Execution

Use `npx n8n-mcp` for fast local startup.

**Pros:**
- Fast setup
- Minimal dependencies

**Considerations:**
- Uses local filesystem for DB.
- Requires Node.js runtime.

---

## 3. Docker Deployment

Docker provides a consistent, isolated runtime.

**Pros:**
- Reproducible builds
- Easy upgrades via image tags

**Considerations:**
- Ensure database is included in the image.
- Configure environment variables at runtime.

---

## 4. HTTP Server Mode

HTTP mode supports:

- Multi-session connections
- Stateless integration with external clients
- Deployment behind reverse proxies

This mode can be used for hosted services or multi‑tenant contexts.

---

## 5. Configuration & Environment Variables

Common environment variables:

- `MCP_MODE`: `stdio` or `http` (depending on transport).
- `LOG_LEVEL`: logging verbosity.
- `DISABLE_CONSOLE_OUTPUT`: reduce stdout noise in MCP clients.
- `N8N_API_URL`: enable management tools by pointing to an n8n instance.
- `N8N_API_KEY`: authenticate API calls.

---

## 6. Database Updates & Rebuilds

When n8n updates, you may need to rebuild the database:

- Run rebuild scripts to extract new nodes and documentation.
- Re-generate template metadata.
- Validate node property schemas.

This process ensures tool outputs remain aligned with current n8n versions.

---

## 7. Operational Safety

n8n‑MCP is designed to be safe by default. Recommended operational policies:

- Avoid editing production workflows with AI directly.
- Use validation tooling before deployment.
- Keep backups of workflows and templates.

---

## 8. Troubleshooting

Common issues and remediation steps:

- **Database not found:** ensure `nodes.db` is in `data/` or set `NODE_DB_PATH`.
- **Tool list missing management tools:** verify `N8N_API_URL` and `N8N_API_KEY`.
- **Unexpected logs in MCP output:** set `DISABLE_CONSOLE_OUTPUT=true`.

---

**Next:** [Tooling, Validation, and Safety](./tooling-and-safety.md)
