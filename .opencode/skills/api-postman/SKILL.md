---
name: api-postman
description: REST API and Postman collection management
---

## Commands

### /postman-collection
Generate Postman collection.

Generate Postman collection:
1. Read API routes from src/app/api/v1/
2. Create collection.json with:
   - Auth (Bearer token)
   - Environment variables
   - Request examples
3. Save to docs/api/[module]-collection.json

Follow .opencode/docs/06-api-guidance.md format.

### /api-docs
Document API endpoint.

Create API documentation:
1. Document endpoint in docs/api/
2. Include: URL, method, params, body, response, errors
3. Add Postman request example
4. List required permissions