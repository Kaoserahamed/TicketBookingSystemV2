# Day 3 CI/CD: Build / Test / Deploy Pipeline

## Repository
`TicketBookingSystemV2` — Ticket Booking System (Node.js + Express, React + TypeScript)

## Pipeline Overview
GitHub Actions CI/CD for Node.js + React monorepo (Makefile-driven, hermetic tests).

### Trigger
- Push/Pull Request to `main`
- Manual workflow dispatch

### Stages

#### 1. Lint & Format
- ESLint + Prettier (all packages)
- Code style enforcement

#### 2. Typecheck
- `tsc --noEmit` (TypeScript strict mode, all packages)

#### 3. Test
- `npm test` (hermetic suites: backend node:test units + frontend vitest run)
- `npm run test:unit` (backend unit tests only, no services required)
- `npm run test:web` (frontend vitest run only)
- `npm run test:coverage` (coverage gates)
- No MySQL/Redis/Docker required for unit suites

#### 4. Build
- `npm run setup` (`npm ci` at root + both stacks, lockfile-exact)
- Backend: Docker image (`Dockerfile` in backend/)
- Frontend: Docker image (`Dockerfile` in frontend/)
- Multi-arch Docker images

#### 5. Security Scan
- `npm audit` (all packages)
- Secret detection (no database credentials, no JWT secrets)
- Dependency vulnerability check
- Docker image scanning

#### 6. Deploy (on merge to `main`)
- Build and push Docker images
- Deploy with `docker-compose up -d` (MySQL + Redis + backend + frontend)
- Health check: `GET /api/health`
- Integration tests against throwaway stack:
  - `npm run stack:test:up` (MySQL 8 on 3307 + Redis on 6380)
  - `npm run test:integration`
  - `npm run stack:test:down`

### Quality Gates
- `npm run verify` (lint + format + typecheck + verify:repo + npm test) must pass
- Coverage floors enforced
- TypeScript strict mode clean
- No secrets committed
- Integration tests pass against throwaway stack

### Artifacts
- Docker images (backend + frontend)
- Test reports
- Coverage reports
- Integration test results
- Deployment URL

## Day 3 Outcome
CI/CD pipeline documented. No code changes. 0 PRs. Ready for Day 4 (project restructuring).
<!-- Feature branch commit for PR -->
