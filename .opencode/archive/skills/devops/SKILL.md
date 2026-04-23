---
name: devops
description: Podman/Docker containerization and deployment
---

## Commands

### /podman-setup
Setup Podman containers.

Create Podman configuration:
1. Containerfile for Next.js app
2. docker-compose.yml for full stack
3. Include: app, postgres, redis
4. Health checks
5. Volume mounts for dev

### /docker-compose
Create docker-compose.

Create docker-compose.yml:
- Next.js app service
- PostgreSQL with init scripts
- Redis service
- Network configuration
- Environment variables