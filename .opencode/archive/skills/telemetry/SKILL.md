---
name: telemetry
description: Redis-based logging and telemetry
---

## Commands

### /telemetry-log
Add telemetry logging.

Add Redis telemetry:
1. Create src/lib/telemetry.ts
2. Log: API calls, auth events, errors, performance
3. Use TTL for data expiry (7-30 days)
4. Include tenant context
5. Create dashboard query utilities