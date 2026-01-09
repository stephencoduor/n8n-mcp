# Session Persistence

## Table of Contents
- [When to Use](#when-to-use)
- [Export Session State](#export-session-state)
- [Restore Session State](#restore-session-state)
- [Security Considerations](#security-considerations)

## When to Use
Session persistence enables zero-downtime container restarts in multi-tenant deployments by exporting and restoring MCP session state.

## Export Session State

```ts
const sessions = engine.exportSessionState();
```

## Restore Session State

```ts
const count = engine.restoreSessionState(sessions);
```

## Security Considerations
Session state includes plaintext n8n API keys. Encrypt exported sessions before storage and use secure key management.
