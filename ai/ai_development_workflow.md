# LittleSteps - AI Development Workflow

## 1. Purpose

This document defines how AI coding tools should work on LittleSteps. It helps AI agents use the documentation repository as shared context without inventing product scope, domain rules, or architecture decisions.

Use this workflow when asking an AI tool to plan, implement, review, or update LittleSteps code or documentation.

## 2. Repository context

LittleSteps is organized as sibling repositories:

```text
little-steps-docs     Shared product, domain, architecture, testing, and AI workflow documents
little-steps-backend  NestJS API implementation
little-steps-mobile   React Native mobile implementation
```

AI tools should open the parent `LittleSteps/` workspace when possible so they can read all relevant repositories together.

## 3. Required reading order

Before implementing a feature, an AI agent should read context in this order:

1. The active task file, if one exists in `tasks/`.
2. `little-steps-docs/AGENTS.md`.
3. `little-steps-docs/ai/ai_coding_guidelines.md` when implementing or reviewing code.
4. Product requirements relevant to the request.
5. Domain model and business rules relevant to the request.
6. Database design when persistence is involved.
7. Architecture documents and ADRs when API, storage, ownership, or module boundaries are involved.
8. The target implementation repository's `AGENTS.md`.
9. Existing code near the files being changed.

The AI agent should not read every document by default. It should read the smallest set that can answer the task safely, then pull more context when the task touches another boundary.

## 4. Task intake workflow

When receiving a request, the AI agent should classify it as one of:

| Request type | Expected action |
| --- | --- |
| Question | Answer from docs/code. Do not modify files unless asked. |
| Documentation change | Update the requested document and related docs needed for consistency. |
| Feature planning | Produce or update a task file before implementation. |
| Implementation | Read the task/docs/code, implement, verify, and summarize. |
| Review | Look for bugs, missing tests, scope drift, and doc inconsistencies. |

If the request conflicts with existing documents, the AI agent should stop and surface the conflict rather than silently choosing a new product direction.

## 5. Implementation workflow

For implementation tasks, follow this sequence:

1. Confirm the feature belongs to V1 scope.
2. Identify the source-of-truth docs that govern the change.
3. Inspect existing code patterns in the target repository.
4. Make the smallest coherent change that satisfies the task.
5. Add or update tests in proportion to risk.
6. Run relevant verification commands.
7. Update docs only when behavior, API shape, domain rules, or architecture decisions changed.
8. Summarize what changed, what was verified, and any remaining risk.

Do not implement future features merely because the model is extensible. V1 supports only Child Journey behavior unless the docs explicitly say otherwise.

## 6. Documentation update workflow

When a decision changes, update documents from most conceptual to most concrete:

1. Product requirements if user-visible scope or behavior changes.
2. Domain model or business rules if concepts or invariants change.
3. Database design if persistence shape changes.
4. Architecture documents if API, storage, ownership, or module boundaries change.
5. ADRs for durable architectural decisions.
6. Task files when implementation work is created, moved, or completed.

Keep documents concise and internally consistent. Do not copy entire sections between documents unless that duplication is necessary for AI readability.

## 7. Architecture decision workflow

Create an ADR when a decision is durable, hard to reverse, or affects multiple repositories.

Examples that require an ADR:

- choosing REST vs GraphQL;
- changing the media storage architecture;
- changing ownership or authentication boundaries;
- introducing a new Journey type;
- replacing the Milestone Definition model;
- adding background jobs, queues, or media processing.

Small implementation choices do not require ADRs when they stay within an existing documented architecture.

## 8. API workflow

When implementing or changing API behavior, the AI agent must check:

- [API contract](../architecture/api_contract.md)
- [System architecture](../architecture/system_architecture.md)
- [Business rules](../domain/business_rules.md)
- [Database design](../domain/database_design.md)

Rules:

- V1 implementation targets local development first.
- API fields use `camelCase`.
- Database fields may use `snake_case`.
- Clients must not send `userId` for owned resources.
- The backend derives ownership from authenticated or documented development-only identity.
- Media upload uses signed upload URLs and direct object-storage upload.
- REST remains the V1 API style unless an ADR changes it.
- Production deployment, production domains, CDN, and cloud-provider choices are deferred.

## 9. Media workflow

Media changes require special care because data spans PostgreSQL and object storage.

The intended upload flow is:

```text
Mobile requests upload authorization
  -> Backend validates ownership and returns signed upload URL
  -> Mobile uploads directly to object storage
  -> Mobile confirms upload with backend
  -> Backend persists Media metadata
```

AI agents must not:

- proxy large V1 media uploads through the NestJS API as the normal flow;
- store permanent provider URLs as product data;
- expose object-storage credentials to the mobile app;
- show unconfirmed media in the timeline; or
- delete database records while ignoring required object-storage cleanup.

## 10. Handling unclear requirements

If a requirement is unclear, the AI agent must ask the user to verify before changing product behavior, domain rules, API shape, database design, architecture, or user-facing behavior.

For purely mechanical details that do not affect product or architecture meaning, the AI agent may follow the repository's existing convention and state the assumption in its final response.

Examples of mechanical details:

- following existing file naming conventions;
- placing a DTO, component, or test beside similar existing files;
- using the repository's existing formatter and lint style; and
- matching established import, module, or folder patterns.

Examples that require clarification:

- whether duplicate suggested milestones should be allowed;
- whether manual milestone reordering is part of V1;
- which production authentication provider to use;
- maximum media file size;
- whether to add non-child journey types.

## 11. Done criteria

A task is done when:

- the requested behavior or document change is complete;
- related documents are consistent;
- relevant tests or checks were run, or skipped with a clear reason;
- no unrelated user changes were reverted; and
- the final response explains changed files and any follow-up decision needed.

For documentation-only changes, a diff review and consistency check are usually enough.
