# LittleSteps - AI Coding Guidelines

## 1. Purpose

This document defines coding guidelines for AI agents working on LittleSteps implementation repositories.

It complements [AI development workflow](ai_development_workflow.md). The workflow explains how to approach a task; this document explains how code should be changed once implementation is allowed.

## 2. Core principles

AI agents must:

- follow existing repository patterns before introducing new ones;
- keep changes narrowly scoped to the requested task;
- implement documented V1 behavior, not speculative future features;
- preserve product, domain, API, and architecture boundaries from `little-steps-docs`;
- update tests in proportion to risk; and
- avoid unrelated refactors.

When code and docs disagree, stop and surface the conflict. Do not silently make the docs or code "win" without user verification.

## 3. Scope control

Before writing code, identify the smallest coherent change that satisfies the task.

Do not add:

- future Journey types;
- sharing, collaboration, comments, reactions, or social feeds;
- AI media analysis or generated captions;
- background jobs, queues, or media processing;
- GraphQL;
- production deployment pipeline, production domains, CDN, or cloud-provider setup;
- payment logic;
- generic plugin systems; or
- broad abstractions that are not needed by the current task.

Extensibility is allowed only when it is already part of the documented model, such as `Journey -> Milestone -> Memory -> Media` or Milestone Definitions. Extensibility must not turn into hidden V2 implementation.

## 4. Backend guidelines

The backend is the product, domain, and security boundary.

AI agents working in `little-steps-backend` should:

- target local development first unless a task explicitly says otherwise;
- organize code by domain responsibility when no stronger local pattern exists;
- keep validation and ownership checks on the backend;
- derive `user_id` from authenticated or documented development-only identity, never from client request bodies;
- expose API fields as `camelCase`;
- map database fields such as `occurred_at` and `storage_key` deliberately rather than leaking persistence naming into the API;
- keep object-storage credentials on the backend only;
- generate storage keys and signed URLs server-side;
- return only confirmed media from read endpoints; and
- avoid proxying large media uploads through NestJS as the normal V1 flow.

Backend modules should respect the responsibilities described in [system_architecture.md](../architecture/system_architecture.md).

## 5. Mobile guidelines

The mobile app owns user experience, not domain authority.

AI agents working in `little-steps-mobile` should:

- connect to the local API through a development base URL during V1 implementation;
- use the REST API contract as the source of truth for network shapes;
- keep client models aligned with API response fields;
- avoid storing permanent object-storage provider URLs as canonical state;
- upload media directly using signed upload URLs returned by the backend;
- confirm uploads through the backend before showing media as available;
- treat `occurredAt` as real-world event time;
- avoid using `createdAt`, `updatedAt`, or IDs for business ordering; and
- present validation errors in a way the user can act on.

The mobile app may format and organize data for UI rendering, but it must not duplicate backend-only ownership or storage rules as the final authority.

## 6. API and DTO guidelines

API-related code must follow [api_contract.md](../architecture/api_contract.md).

Rules:

- REST is the V1 API style.
- Request and response fields use `camelCase`.
- Client requests must not include `userId` for owned resources.
- Optional absent values should be represented as `null` where the API contract expects nullability.
- IDs are technical identifiers only.
- Error responses should follow the documented error shape.
- Timeline reads must sort milestones by `occurredAt ASC`.
- Media reads must include temporary read URLs only when needed by the client.

If implementation needs a field not documented in the API contract, update the API contract first or ask the user to verify the change.

## 7. Persistence guidelines

Database code must follow [database_design.md](../domain/database_design.md).

Rules:

- PostgreSQL stores structured product data and relationships.
- Object storage stores media bytes.
- Persist stable `storage_key`, not permanent provider URLs.
- Use `occurred_at` for real-world event time.
- Do not use IDs, `created_at`, or `updated_at` for business ordering.
- Preserve parent-child ownership boundaries.
- Deleting parent records must also handle dependent object-storage cleanup where media is involved.

Schema changes that alter documented entities, relationships, or constraints require documentation updates.

## 8. Validation guidelines

Validation must be enforced by the backend, even when the mobile app also validates for user experience.

At minimum:

- child name is required;
- child birthday is required and must not be in the future;
- milestone title and occurred time are required;
- milestone occurred time must not be in the future;
- selected Milestone Definition must apply to the Journey type;
- memory title and occurred time are required;
- media type must be `IMAGE` or `VIDEO`; and
- media reorder operations must stay within one memory.

Validation errors should be specific enough for the mobile app to show useful feedback.

## 9. Testing guidelines

AI agents should add or update tests when changing behavior.

Backend tests should focus on:

- validation rules;
- ownership boundaries;
- API request and response shapes;
- timeline ordering;
- media upload confirmation behavior; and
- deletion cleanup behavior.

Mobile tests should focus on:

- user-visible flows;
- API state mapping;
- validation feedback;
- timeline rendering order; and
- media upload state transitions.

For documentation-only changes, a consistency review is usually enough.

## 10. Refactoring guidelines

Refactoring is allowed only when it directly supports the requested task or removes clear duplication in touched code.

Do not:

- rename broad modules during a feature task;
- rewrite working code into a new architecture without an ADR;
- introduce a new framework, state manager, ORM, or storage library without approval;
- mix formatting-only churn with behavior changes; or
- change public API shapes without updating the API contract.

If a refactor seems valuable but is not required, mention it as a follow-up instead of doing it inside the current task.

## 11. Final response checklist

When finishing implementation work, the AI agent should report:

- what changed;
- which files were touched;
- what verification ran;
- whether docs were updated; and
- any decision still needed from the user.

Do not claim tests passed unless they were actually run.
