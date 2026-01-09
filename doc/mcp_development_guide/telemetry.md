# Telemetry & Observability

## Table of Contents
- [Session Tracking](#session-tracking)
- [Tool Usage Tracking](#tool-usage-tracking)
- [Operational Notes](#operational-notes)

## Session Tracking
Session start events are recorded when MCP Initialize is received.

```ts
telemetry.trackSessionStart();
```

## Tool Usage Tracking
Tool calls record success/failure and latency metrics.

```ts
const startTime = Date.now();
const result = await this.executeTool(name, processedArgs);
const duration = Date.now() - startTime;

telemetry.trackToolUsage(name, true, duration);
```

## Operational Notes
- Tool sequence tracking helps correlate usage patterns.
- Logging is suppressed in stdio mode to avoid JSON-RPC corruption.
