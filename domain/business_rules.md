# LittleSteps — Business Rules

## 1. Purpose

This document defines the V1 business rules for a Child Journey. It turns product intent into decisions that implementation must follow consistently.

For concept definitions, see [domain_model.md](domain_model.md). For persistence and storage constraints, see [database_design.md](database_design.md).

## 2. Scope and terminology

V1 supports only a Child Journey. In this document:

- **Journey** means a V1 Child Journey unless stated otherwise.
- **Milestone Definition** means a catalog entry that suggests a kind of milestone for a Journey type, such as First Smile or First Step for `CHILD`.
- **Custom milestone** means a milestone that does not refer to a Milestone Definition.
- **Media** means an image or video attached to a memory.

## 3. Journey and child profile rules

1. A journey belongs to exactly one user in the data model.
2. A V1 journey must have type `CHILD`.
3. A V1 Child Journey has exactly one child profile.
4. A child profile cannot be shared by, or reassigned to, another journey.
5. A child's name and birthday are required to create a child profile.
6. A birthday must not be in the future.
7. An avatar is optional and is not a Memory or Media item.

## 4. Milestone rules

1. Every milestone belongs to exactly one journey.
2. A milestone requires a title and `occurred_at` value.
3. `occurred_at` represents when the event happened in real life. It must not be in the future.
4. The creation or editing time of a record must never replace its real-world `occurred_at` value.
5. A milestone may optionally refer to one Milestone Definition. A milestone without a definition is custom.
6. A referenced definition must apply to the milestone's Journey type.
7. Selecting a definition may prefill a title, but the user can edit the title. The definition alone must not be the milestone's display title.
8. V1 does not impose a uniqueness rule on definitions; multiple milestones may refer to the same definition unless a later product decision says otherwise.
9. A milestone may exist without a memory or media. This supports recording an event before its story or files are available.
10. Updating or deleting a milestone must not modify unrelated milestones.

## 5. Memory rules

1. Every memory belongs to exactly one milestone.
2. A memory requires a title and `occurred_at` value; its description is optional.
3. A memory may exist without media.
4. A memory's `occurred_at` may differ from its milestone's `occurred_at` when the remembered part of the event happened at a different time.
5. A memory must not be reassigned to another milestone.
6. Editing a memory must not change the milestone's title, definition, description, or occurred time.
7. A memory description describes the remembered moment. It must not be used as the sole description of an individual media file.

## 6. Media rules

1. Every media item belongs to exactly one memory.
2. V1 supports only `IMAGE` and `VIDEO` media types.
3. Media does not appear as an independent timeline entry.
4. A media item is available to the product only after its upload has been confirmed and its metadata has been recorded.
5. The actual image or video file is stored in object storage; PostgreSQL stores its metadata and stable storage key.
6. Media may be reordered only within its own memory.
7. Removing a media item must not alter other media items or their parent memory.

## 7. Timeline and ordering rules

1. The default timeline displays milestones in ascending chronological order by `occurred_at`.
2. A newly recorded past milestone must appear at its historical position, not at the end of the timeline.
3. `created_at`, `updated_at`, and record IDs must never determine business ordering.
4. `sort_order` may be used as a stable tie-breaker when milestones have the same `occurred_at`, but it does not override chronological ordering. Manual milestone reordering is not a V1 requirement and must not be exposed or implemented without a documented product decision.
5. Media is presented within a memory by its explicit `sort_order`.
6. If a milestone's `occurred_at` changes, its chronological timeline position must be recalculated.

## 8. Deletion rules

| User action | Product result | Required cleanup |
| --- | --- | --- |
| Delete media | Remove only that media item. | Remove its object-storage file and metadata record. |
| Delete memory | Remove the memory and all its media. | Remove dependent media files and metadata records. |
| Delete milestone | Remove the milestone and all of its memories and media. | Remove dependent records and media files. |
| Delete journey | Remove its child profile, milestones, memories, and media. | Remove dependent records and media files. |

Deletion must not leave orphaned database records or storage files. Because object storage is external to the database, the application must handle partial failures safely and report a failure rather than silently claiming completion.

## 9. Data integrity rules

1. IDs are stable technical identifiers only; they have no user-facing meaning and do not encode order or type.
2. Every parent-child relationship must remain within one journey's ownership boundary.
3. A milestone must not reference a definition for a different Journey type.
4. The product must not expose a permanent provider-specific media URL as the canonical media reference.
5. An implementation must not silently create a new journey, milestone, memory, or media item as a side effect of editing another item.
6. If a requested action conflicts with a rule in this document, the product must reject it with a clear, actionable validation message.

## 10. Change control

These rules are V1 decisions, not implementation suggestions. A change that affects user-visible behavior, data integrity, or the core Journey–Milestone–Memory–Media relationship requires:

1. Updating this document and related product/domain documents.
2. Creating an Architecture Decision Record when the decision has durable architectural impact.
3. Updating acceptance criteria before implementing the change.
