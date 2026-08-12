# Phase 2 — Project Foundation Backlog

## Overview
This phase focuses on setting up the core infrastructure, backend, and mobile project foundations so that feature development can begin. No business logic is implemented in this phase.

## Task List

### 1. Development Environment & Infrastructure
- [x] **Task 1.1**: Setup `docker-compose.yml` in the root (or backend) directory to run a local PostgreSQL instance.
- [x] **Task 1.2**: Add MinIO to `docker-compose.yml` to simulate S3-compatible object storage locally.
- [x] **Task 1.3**: Write a simple initialization script to create the required default buckets in MinIO (e.g., `littlesteps-media`).

### 2. Backend Foundation (`little-steps-backend`)
- [/] **Task 2.1**: Initialize a new NestJS project with TypeScript.
- [/] **Task 2.2**: Configure ESLint and Prettier for the backend.
- [ ] **Task 2.3**: Install and initialize Prisma ORM.
- [ ] **Task 2.4**: Connect Prisma to the local Docker PostgreSQL database.
- [ ] **Task 2.5**: Define the initial `User` model in `schema.prisma` and run the first database migration to verify the connection.
- [ ] **Task 2.6**: Create `AGENTS.md` in `little-steps-backend` to guide AI on backend conventions (NestJS, Prisma, testing rules).

### 3. Mobile Foundation (`little-steps-mobile`)
- [ ] **Task 3.1**: Initialize the React Native project (using Expo or React Native CLI as decided) with TypeScript.
- [ ] **Task 3.2**: Configure ESLint and Prettier for the mobile app.
- [ ] **Task 3.3**: Setup the foundational folder structure (e.g., `src/screens`, `src/components`, `src/navigation`, `src/services`).
- [ ] **Task 3.4**: Create `AGENTS.md` in `little-steps-mobile` to guide AI on mobile conventions (React Native, state management, styling).

## Completion Criteria
Phase 2 is complete when:
1. Docker containers for Postgres and MinIO are running locally.
2. The NestJS backend can start without errors and successfully connects to the database via Prisma.
3. The React Native mobile app can build and run the default screen on an iOS/Android simulator.
4. `AGENTS.md` files are present in their respective repositories.
