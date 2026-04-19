---
name: testing
description: Unit, integration, and E2E testing
---

## Commands

### /test-unit
Create unit tests.

Create unit tests for:
- Service functions
- Utility functions
- API handlers (mocked)

Use Vitest/Jest with >80% coverage target.

### /test-integration
Create integration tests.

Create integration tests:
- API endpoint tests with test DB
- Service layer tests
- Auth flow tests

Use testcontainers or mock Redis/DB.

### /test-e2e
Create E2E tests.

Create Playwright E2E tests:
- User flows
- Auth scenarios
- Multi-tenant isolation

Store in tests/e2e/