# Feature: Relational Database & Prisma ORM

## Overview
Designed and implemented a relational database schema using PostgreSQL and managed it using Prisma ORM. The schema supports the core entities of the app: Users, Journeys, Children, Milestones, Memories, and Media.

## Technical Decisions & Rationale

1. **Why PostgreSQL?**
   - **Relational Integrity**: The app's data is highly relational (User -> Journey -> Child -> Milestone -> Memory -> Media). PostgreSQL ensures data consistency through Foreign Keys and cascading deletes.
   - **Production Readiness**: Deployed seamlessly on Render.

2. **Why Prisma ORM?**
   - **Type Safety**: Prisma generates a fully type-safe client based on the `schema.prisma` file, catching database errors at compile-time rather than runtime.
   - **Developer Experience**: Migrations (`prisma db push` / `prisma migrate`) and schema declarations are highly readable and maintainable compared to writing raw SQL or using older ORMs like TypeORM.
   - **Connection Pooling**: Used `pg.Pool` with `@prisma/adapter-pg` to properly handle SSL configurations dynamically between local development (no SSL) and Render production (requires SSL).

3. **Cascading Deletes (`onDelete: Cascade`)**
   - Configured `onDelete: Cascade` on relations (e.g., deleting a Journey automatically deletes its Milestones and Memories). This prevents orphaned records and simplifies cleanup logic in the backend.

## Key Functions & Components

### Backend
- **`schema.prisma`**: The source of truth for the database schema. Defines models, types, relations, and index mappings.
- **`PrismaService`**: A global NestJS service extending `PrismaClient`. Manages the database connection lifecycle (`onModuleInit`).
- **Data Access Layer (Services)**: 
  - `this.prisma.user.findUnique()`
  - `this.prisma.journey.create()`
  - Uses `include` to eagerly load relations (e.g., fetching a Journey and `include: { child: true }`).
