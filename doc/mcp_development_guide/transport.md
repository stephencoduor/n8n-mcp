# Transport & Entry Points

## Table of Contents
- [Stdio Wrapper](#stdio-wrapper)
- [Environment Guards](#environment-guards)
- [Shutdown Handling](#shutdown-handling)

## Stdio Wrapper
The stdio wrapper enforces JSON-only output for MCP stdio transport and prevents logs from corrupting responses.

```ts
process.env.MCP_MODE = 'stdio';
process.env.DISABLE_CONSOLE_OUTPUT = 'true';
process.env.LOG_LEVEL = 'error';

console.log = () => {};
console.error = () => {};
console.warn = () => {};
console.info = () => {};
```

## Environment Guards
The wrapper sets environment variables before any imports so logs do not leak to stdout.

```ts
// CRITICAL: Set environment BEFORE any imports to prevent any initialization logs
process.env.MCP_MODE = 'stdio';
process.env.DISABLE_CONSOLE_OUTPUT = 'true';
process.env.LOG_LEVEL = 'error';
```

## Shutdown Handling
Signal handlers ensure clean shutdown without corrupting the MCP pipe.

```ts
process.on('SIGTERM', () => void shutdown('SIGTERM'));
process.on('SIGINT', () => void shutdown('SIGINT'));
process.on('SIGHUP', () => void shutdown('SIGHUP'));

process.stdin.on('end', () => {
  void shutdown('STDIN_CLOSE');
});
```
