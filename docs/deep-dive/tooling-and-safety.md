# Tooling, Validation, and Safety

> This document explains how n8n‑MCP ensures correctness, safety, and structured outputs for AI assistants.

---

## Table of Contents

1. [Tool Definitions and Schemas](#1-tool-definitions-and-schemas)
2. [Input Validation](#2-input-validation)
3. [Workflow Validation](#3-workflow-validation)
4. [Expression Validation](#4-expression-validation)
5. [Sanitization and Security](#5-sanitization-and-security)
6. [Telemetry and Observability](#6-telemetry-and-observability)
7. [Error Handling Patterns](#7-error-handling-patterns)

---

## 1. Tool Definitions and Schemas

All MCP tools are defined with structured schemas. This ensures:

- Input arguments are type‑safe.
- Tool responses are predictable.
- Clients can validate responses programmatically.

Tool documentation exists both for developer reference and for client introspection, enabling rich automated help inside MCP clients.

---

## 2. Input Validation

Input validation is applied early in the request flow to avoid downstream errors. Common validation checks include:

- Required argument presence
- Correct data types
- Allowed value constraints
- Data shape enforcement

This prevents malformed requests from triggering expensive operations.

---

## 3. Workflow Validation

Workflows are validated to ensure:

- Node types exist and are supported.
- Required properties are present and correctly typed.
- Property dependencies are satisfied.
- Node configuration values are within expected ranges.

This reduces errors in generated workflows and improves output reliability.

---

## 4. Expression Validation

Expressions used inside n8n workflows are validated for:

- Syntax correctness
- Structural integrity
- Compatibility with node property types

This prevents failures in runtime execution where invalid expressions would break workflows.

---

## 5. Sanitization and Security

Safety features include:

- **SSRF protection** for any network-bound operations.
- **Workflow sanitization** to remove sensitive data before logging.
- **Error sanitization** to avoid leaking secrets.

These safeguards make the system safer for multi-tenant or public deployments.

---

## 6. Telemetry and Observability

Telemetry is designed to be privacy‑preserving:

- All events are sanitized.
- Rate limiting prevents logging storms.
- Early error logging provides startup diagnostics without sensitive content.

This supports operational monitoring without exposing user data.

---

## 7. Error Handling Patterns

n8n‑MCP uses structured error outputs, generally including:

- Error code or category
- Human‑readable message
- Optional metadata for debugging

This consistent error format allows MCP clients to recover gracefully.

---

**Back to:** [Comprehensive Documentation Index](../comprehensive_guide.md)
