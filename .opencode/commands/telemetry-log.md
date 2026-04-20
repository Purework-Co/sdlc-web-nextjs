---
description: Add telemetry logging
agent: backend-engineer
---

Add Redis telemetry logging: create src/lib/telemetry.ts, log API calls, auth events, errors, performance. Use TTL for data expiry (7-30 days), include tenant context, create dashboard query utilities.