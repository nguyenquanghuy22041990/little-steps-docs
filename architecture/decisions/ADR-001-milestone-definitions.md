# ADR-001: Use Milestone Definitions for Suggested Milestones

## Status

Accepted — 2026-08-10

## Context

LittleSteps needs to suggest milestones such as First Smile, First Step, and First Word to users of a Child Journey. The product must also support milestones that users define themselves.

The core Journey–Milestone–Memory–Media model is intended to support future Journey types, including Study and Travel. A single fixed milestone-type enum on `milestones` would couple the schema to a Child-only taxonomy and require a database migration whenever a suggested milestone changes.

## Decision

Introduce a `Milestone Definition` catalog.

Each definition:

- applies to a Journey type;
- has a stable `code` and a default display title; and
- represents a suggested kind of milestone, not an event in a user's timeline.

Each Milestone:

- belongs to one Journey;
- has a required, editable `title`; and
- optionally references one Milestone Definition.

A milestone with no definition is custom. V1 does not impose a uniqueness rule on definition references. Any rule such as “only one First Step” requires a later product decision.

Definition codes are stored as strings, not a PostgreSQL enum.

## Consequences

- The product can show Child Journey suggestions without restricting users to them.
- A future Study or Travel Journey can have its own definition catalog without changing the core milestone, memory, or media relationships.
- Existing milestones retain their own editable title if a catalog definition changes.
- The backend must validate that a referenced definition applies to the milestone's Journey type.
- The implementation adds a catalog table and lookup behavior, which is more complex than a single fixed `type` column but avoids a less flexible core model.

## Related documents

- [Product requirements](../../product/product_requirements.md)
- [Domain model](../../domain/domain_model.md)
- [Business rules](../../domain/business_rules.md)
- [Database design](../../domain/database_design.md)
