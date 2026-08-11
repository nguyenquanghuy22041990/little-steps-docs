# LittleSteps Documentation Repository

This repository is the shared source of truth for the LittleSteps product, domain, architecture, design, testing, and delivery workflow.

The sibling repositories have separate responsibilities:

```text
little-steps-docs     Shared product and engineering knowledge
little-steps-backend  Backend implementation
little-steps-mobile   Mobile implementation
```

## Working rules

Before changing a document or implementing a feature, read the relevant documents in this order:

1. The task, if one exists in `tasks/`.
2. The AI development workflow in `ai/ai_development_workflow.md` when using an AI coding tool.
3. The AI coding guidelines in `ai/ai_coding_guidelines.md` when implementing or reviewing code.
4. Product requirements in `product/`.
5. Domain rules and models in `domain/`.
6. Architecture decisions in `architecture/`.
7. The repository-specific `AGENTS.md` in the implementation repository.

Keep documentation concise, factual, and internally consistent. Update related documents when an intentional decision changes.

Do not invent requirements, API behavior, domain entities, or future journey types. Surface decisions that are not documented instead of silently making them in code.

## Current V1 boundary

LittleSteps V1 supports a Child Journey: child profile, milestones, memories, photos, videos, and a chronological timeline.

V1 implementation targets local development first: local NestJS API, local or local-Docker PostgreSQL, local MinIO object storage, and a mobile development base URL. Production deployment, production domains, CDN, and cloud-provider choices are deferred.

The generic core is:

```text
Journey -> Milestone -> Memory -> Media
```

`Child` is the V1-specific profile attached to a `Journey`. Future journey types are not part of V1 unless explicitly documented.

`Milestone Definition` is a catalog entry that provides milestone suggestions for a Journey type. A `Milestone` is the actual event recorded by the user and may refer to a definition or be custom.

## Documentation conventions

- Use `snake_case.md` file names, except Architecture Decision Records, which use `ADR-###-short-title.md`.
- Keep domain meaning separate from database implementation details.
- Record significant, durable architecture choices under `architecture/decisions/` before or alongside implementation.
- Move completed implementation tasks from `tasks/in-progress/` to `tasks/completed/`; do not use task files as a substitute for product or architecture documentation.
- Do not store secrets, credentials, production URLs, or personal media in this repository.
