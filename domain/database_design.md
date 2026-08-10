# LittleSteps — Database Design

## 1. Purpose

This document defines the database model and persistence rules for LittleSteps V1.

LittleSteps V1 focuses exclusively on the **Child Journey** use case.

The core domain model is intentionally designed around a generic journey structure so that future versions can support other journey types without changing the core milestone and memory model.

### Core hierarchy

```text
User
 └── Journey
      ├── Child
      └── Milestone
            └── Memory
                  └── Media

Milestone Definition
  └── provides suggestions for a Journey type
```

The core timeline structure is:

```text
Journey → Milestone → Memory → Media
```

---

## 2. Design Principles

### 2.1 V1 scope

V1 supports only:

* Child Journey
* Child Profile
* Milestones
* Memories
* Photos
* Videos
* Chronological Timeline

Do not introduce additional journey types such as Learning, Fitness, Travel, or Career into V1.

The database should remain extensible, but should not implement speculative future features.

### 2.2 Separation of concerns

Each entity has a specific responsibility:

| Entity       | Responsibility                                |
| ------------ | --------------------------------------------- |
| `users`      | Ownership of journeys                         |
| `journeys`   | Represents a journey                          |
| `children`   | Child-specific profile information            |
| `milestone_definitions` | Suggested milestone catalog for journey types |
| `milestones` | Important events or achievements in a journey |
| `memories`   | Rich memories associated with a milestone     |
| `media`      | Photos and videos associated with a memory    |

Do not merge these entities unless there is a documented architectural reason.

### 2.3 Identity vs ordering

An entity's `id` identifies the entity only.

Never use an ID to represent ordering.

Use:

* `occurred_at` for real-world chronological time.
* `sort_order` for explicit presentation ordering where required.

---

# 3. Entities

## 3.1 User

Represents the owner of one or more journeys.

### Table: `users`

| Field        | Type      | Required | Description            |
| ------------ | --------- | -------: | ---------------------- |
| `id`         | UUID      |      Yes | Unique user identifier |
| `created_at` | timestamp |      Yes | Record creation time   |
| `updated_at` | timestamp |      Yes | Last modification time |

### Relationships

```text
User 1 ─── N Journey
```

A user may own multiple journeys.

Authentication is outside the scope of the initial database implementation, but the ownership relationship should exist in the data model.

---

## 3.2 Journey

Represents a journey being documented.

For V1, every journey has type `CHILD`.

### Table: `journeys`

| Field         | Type        | Required | Description                  |
| ------------- | ----------- | -------: | ---------------------------- |
| `id`          | UUID        |      Yes | Unique journey identifier    |
| `user_id`     | UUID        |      Yes | Owner of the journey         |
| `type`        | string/enum |      Yes | V1 value: `CHILD`            |
| `title`       | varchar     |      Yes | Journey title                |
| `description` | text        |       No | Optional journey description |
| `start_date`  | date        |       No | Journey start date           |
| `end_date`    | date        |       No | Journey end date             |
| `created_at`  | timestamp   |      Yes | Record creation time         |
| `updated_at`  | timestamp   |      Yes | Last modification time       |

### Relationships

```text
User 1 ─── N Journey
Journey 1 ─── 1 Child       // V1
Journey 1 ─── N Milestone
```

### V1 constraint

`type` must be `CHILD`.

Do not add future journey types merely to make the database appear more generic.

---

## 3.3 Child

Contains child-specific information for a Child Journey.

### Table: `children`

| Field                | Type      | Required | Description                               |
| -------------------- | --------- | -------: | ----------------------------------------- |
| `id`                 | UUID      |      Yes | Unique child identifier                   |
| `journey_id`         | UUID      |      Yes | Associated child journey                  |
| `name`               | varchar   |      Yes | Child's name                              |
| `birthday`           | date      |      Yes | Child's date of birth                     |
| `avatar_storage_key` | varchar   |       No | Object-storage key for the child's avatar |
| `created_at`         | timestamp |      Yes | Record creation time                      |
| `updated_at`         | timestamp |      Yes | Last modification time                    |

### Relationships

```text
Journey 1 ─── 1 Child
```

### Constraints

* A Child belongs to exactly one Journey.
* A V1 Child Journey has exactly one Child.
* `birthday` represents the child's actual date of birth.
* Avatar files are stored in object storage, not PostgreSQL.

---

# 4. Milestone Definition

A Milestone Definition is a catalog entry that provides a suggested kind of milestone for a Journey type. It is not an event in a user's timeline.

For V1, definitions apply to `CHILD` journeys. Initial examples may include First Smile, First Rollover, First Crawl, First Tooth, First Step, First Word, and First Birthday.

### Table: `milestone_definitions`

| Field | Type | Required | Description |
| --- | --- | ---: | --- |
| `id` | UUID | Yes | Unique definition identifier |
| `journey_type` | string | Yes | Journey type this definition applies to; V1 value: `CHILD` |
| `code` | varchar | Yes | Stable semantic code, for example `FIRST_STEP` |
| `default_title` | varchar | Yes | Suggested initial title for a milestone |
| `sort_order` | integer | Yes | Presentation order in the suggestion catalog |
| `is_active` | boolean | Yes | Whether the definition is currently offered as a suggestion |
| `created_at` | timestamp | Yes | Record creation time |
| `updated_at` | timestamp | Yes | Last modification time |

### Constraints

* `code` must be unique within a `journey_type`.
* A definition may be added or deactivated without changing existing milestones.
* A definition is a suggestion; users may create milestones without one.
* Do not use a PostgreSQL enum for definition codes. New definitions must be addable without a schema migration.

### Relationships

```text
Journey type 1 ─── N Milestone Definition
Milestone Definition 1 ─── N Milestone (optional on Milestone)
```

# 5. Milestone

A milestone represents an important event or achievement in the child's journey. It is an instance in a user's story, not a catalog definition.

### Table: `milestones`

| Field         | Type        | Required | Description                          |
| ------------- | ----------- | -------: | ------------------------------------ |
| `id`          | UUID        |      Yes | Unique milestone identifier          |
| `journey_id`  | UUID        |      Yes | Journey containing the milestone     |
| `milestone_definition_id` | UUID | No | Optional suggested definition used for this milestone |
| `title`       | varchar     |      Yes | Milestone title                      |
| `description` | text        |       No | Description of what happened         |
| `occurred_at` | timestamp   |      Yes | When the milestone actually happened |
| `sort_order`  | integer     |      Yes | Presentation order                   |
| `created_at`  | timestamp   |      Yes | Record creation time                 |
| `updated_at`  | timestamp   |      Yes | Last modification time               |

### Relationships

```text
Journey 1 ─── N Milestone
Milestone Definition 1 ─── N Milestone (optional)
```

### Definition rules

A milestone may reference a definition appropriate to its journey type, or no definition when it is custom.

The milestone `title` is required even when a definition is selected. It is a user-editable snapshot of the display title and must not be inferred dynamically from the definition.

V1 does not impose a uniqueness rule on a definition reference. Multiple milestones may use the same definition unless a later product decision introduces a specific rule.

### Ordering rules

`occurred_at` represents **real-world time**.

`sort_order` represents **presentation order**.

Do not use the milestone `id` to determine ordering.

The default timeline should be chronological:

```sql
ORDER BY occurred_at ASC
```

`sort_order` exists to support future manual ordering/reordering without changing entity identity.

### Example

```text
First Smile
occurred_at = 2025-03-10
sort_order  = 10

First Crawl
occurred_at = 2025-08-20
sort_order  = 20

First Step
occurred_at = 2025-11-02
sort_order  = 30
```

### Important distinction

`occurred_at` answers:

> When did this actually happen?

`sort_order` answers:

> Where should this appear in the presentation?

Do not use `created_at` for timeline ordering.

A milestone may be created days or months after the actual event occurred.

---

# 6. Memory

A Memory represents the detailed story/context associated with a milestone.

A milestone may contain multiple memories.

### Table: `memories`

| Field          | Type      | Required | Description                 |
| -------------- | --------- | -------: | --------------------------- |
| `id`           | UUID      |      Yes | Unique memory identifier    |
| `milestone_id` | UUID      |      Yes | Associated milestone        |
| `title`        | varchar   |      Yes | Memory title                |
| `description`  | text      |       No | Detailed memory description |
| `occurred_at`  | timestamp |      Yes | When this memory occurred   |
| `created_at`   | timestamp |      Yes | Record creation time        |
| `updated_at`   | timestamp |      Yes | Last modification time      |

### Relationships

```text
Milestone 1 ─── N Memory
```

### Example

```text
Milestone:
First Step

Memory 1:
Title:
"He walked toward Grandma"

Description:
"Daddy was recording when he suddenly stood up
and walked toward Grandma."

Memory 2:
Title:
"Everyone celebrated"

Description:
"Everyone in the room started cheering."
```

### Responsibility

A Milestone answers:

> What important event happened?

A Memory answers:

> What do I want to remember about this event?

Do not store media descriptions directly on the Media entity unless the description specifically describes the media asset itself.

---

# 7. Media

Media represents a photo or video attached to a Memory.

Actual binary files must NOT be stored in PostgreSQL.

### Table: `media`

| Field         | Type        | Required | Description                          |
| ------------- | ----------- | -------: | ------------------------------------ |
| `id`          | UUID        |      Yes | Unique media identifier              |
| `memory_id`   | UUID        |      Yes | Associated memory                    |
| `type`        | string/enum |      Yes | `IMAGE` or `VIDEO`                   |
| `storage_key` | varchar     |      Yes | Object-storage key                   |
| `mime_type`   | varchar     |      Yes | MIME type                            |
| `file_name`   | varchar     |      Yes | Original file name                   |
| `file_size`   | bigint      |      Yes | File size in bytes                   |
| `width`       | integer     |       No | Media width in pixels                |
| `height`      | integer     |       No | Media height in pixels               |
| `duration`    | decimal     |       No | Video duration in seconds            |
| `sort_order`  | integer     |      Yes | Presentation order within the memory |
| `created_at`  | timestamp   |      Yes | Record creation time                 |
| `updated_at`  | timestamp   |      Yes | Last modification time               |

### Relationships

```text
Memory 1 ─── N Media
```

### Media type rules

For `IMAGE`:

```text
width  = optional image width
height = optional image height
duration = null
```

For `VIDEO`:

```text
width    = optional video width
height   = optional video height
duration = optional video duration
```

---

# 8. Object Storage

PostgreSQL stores metadata and references.

Actual files are stored in an S3-compatible object storage system.

### Local development

Use:

```text
MinIO
```

### Future production

The storage implementation may be replaced with:

* Amazon S3
* Cloudflare R2
* another S3-compatible provider

The database should store only the stable `storage_key`.

Example:

```text
memories/
  memory_123/
    media_001.jpg
    media_002.jpg
    media_003.mp4
```

Do not store provider-specific URLs as the permanent source of truth.

The backend should generate an appropriate URL when returning media to the client.

---

# 9. Media Upload Flow

The backend should avoid proxying large image/video files through the application server whenever possible.

Preferred flow:

```text
React Native
     │
     │ 1. Request upload authorization
     ▼
NestJS
     │
     │ 2. Generate signed upload URL
     ▼
React Native
     │
     │ 3. Upload directly
     ▼
MinIO / S3-compatible storage
     │
     │ 4. Confirm upload
     ▼
NestJS
     │
     │ 5. Persist/complete media metadata
     ▼
PostgreSQL
```

This keeps large file transfers away from the NestJS application server.

---

# 10. CRUD and Modification Rules

The database must support independent modification of each level.

## Add Milestone

Create a new row in `milestones`.

Existing milestones must remain unchanged.

If chronological ordering is used:

```sql
ORDER BY occurred_at ASC
```

automatically places the new milestone according to its real-world date.

---

## Update Milestone

Update only the target milestone.

Changing:

* title
* description
* milestone_definition_id
* occurred_at
* sort_order

must not modify unrelated memories or media.

---

## Delete Milestone

Deleting a milestone should delete its dependent memories and media metadata.

Expected logical cascade:

```text
Milestone
   ↓
Memories
   ↓
Media metadata
```

Actual media objects must also be removed from object storage through application/service logic.

PostgreSQL foreign-key cascading cannot delete objects from MinIO/S3 directly.

---

## Add Memory

Insert a new Memory associated with a Milestone.

Existing memories must remain unchanged.

---

## Update Memory

Update only the target Memory.

Changing Memory metadata must not modify the associated Milestone or unrelated Media.

---

## Delete Memory

Delete:

```text
Memory
   ↓
Media metadata
   ↓
Actual media objects
```

Object-storage cleanup must be handled by application/service logic.

---

## Add Media

Insert a new Media record with a `memory_id` and `sort_order`.

The actual file must exist in object storage before the media is considered fully available.

---

## Update Media

Possible updates include:

* file replacement
* metadata correction
* `sort_order`

The Media `id` should remain stable when replacing the underlying file unless there is a specific reason to create a new Media entity.

---

## Delete Media

Delete:

1. The object from object storage.
2. The Media database record.

The operation should be designed to handle partial failures safely.

---

# 11. Ordering Rules

## Milestones

Primary timeline ordering:

```text
occurred_at ASC
```

`sort_order` is available for explicit presentation ordering.

Do not use:

```text
id
created_at
updated_at
```

as the business ordering field.

## Media

Media has no independent real-world event time requirement in V1.

Therefore:

```text
sort_order ASC
```

is the primary presentation order within a Memory.

Example:

```text
Photo A → sort_order 10
Photo B → sort_order 20
Video C → sort_order 30
```

If a new Media item is inserted between A and B, the application may assign an intermediate order or renumber the collection.

The implementation strategy should remain encapsulated in the service layer.

---

# 12. Referential Integrity

Foreign keys must enforce ownership relationships.

Expected relationships:

```text
users.id
   ↓
journeys.user_id

journeys.id
   ↓
children.journey_id

journeys.id
   ↓
milestones.journey_id

milestone_definitions.id
   ↓
milestones.milestone_definition_id

milestones.id
   ↓
memories.milestone_id

memories.id
   ↓
media.memory_id
```

A child record must not reference a journey that does not exist.

A milestone must not reference a journey that does not exist.

A milestone definition reference must be null or reference a definition that exists and applies to the milestone's journey type.

A memory must not reference a milestone that does not exist.

A media record must not reference a memory that does not exist.

---

# 13. Delete Strategy

For V1, dependent records should follow the parent lifecycle.

Conceptually:

```text
Journey
 ├── Child
 └── Milestone
      └── Memory
           └── Media
```

Deleting a parent must not leave orphaned database records.

However, object-storage deletion is an external side effect and must be handled separately by application logic.

Do not rely solely on database `ON DELETE CASCADE` for complete media cleanup.

---

# 14. Timestamps

Every mutable entity should have:

```text
created_at
updated_at
```

These represent database record lifecycle events.

They must not be confused with real-world event time.

For example:

```text
Child's first step:
occurred_at = 2025-11-02

Memory created:
created_at = 2025-11-03
```

The Timeline should use `occurred_at`, not `created_at`.

---

# 15. IDs

Use UUIDs for entity identifiers.

IDs must:

* uniquely identify records
* have no business meaning
* not encode ordering
* not encode entity type
* remain stable during normal updates

Do not rely on sequential IDs for timeline ordering.

---

# 16. Future Extensibility

The current core model intentionally supports the following future structure:

```text
Journey
 ├── Child Journey
 ├── Learning Journey
 ├── Fitness Journey
 ├── Travel Journey
 └── Custom Journey
```

However, V1 implements only:

```text
Journey
 └── Child
```

The generic core remains:

```text
Journey
   ↓
Milestone
   ↓
Memory
   ↓
Media
```

Do not add future domain-specific tables until the corresponding product feature is actually required.

---

# 17. AI Implementation Rules

AI coding agents must follow this document when implementing database-related changes.

### Rule 1 — Do not change the domain model implicitly

If an implementation appears to require a new entity or relationship, stop and surface the architectural decision instead of silently changing the schema.

### Rule 2 — Do not remove fields without justification

Existing fields may only be removed or repurposed after reviewing their usage and documenting the decision.

### Rule 3 — Preserve relationship integrity

New foreign-key relationships must follow the ownership model defined in this document.

### Rule 4 — Do not store binary media in PostgreSQL

Images and videos belong in object storage.

PostgreSQL stores media metadata and `storage_key`.

### Rule 5 — Do not use IDs for ordering

Use the explicitly defined ordering fields.

### Rule 6 — Do not introduce speculative abstractions

Do not create generic tables or relationships solely for hypothetical future features.

### Rule 7 — Validate cascade behavior

Any migration or delete operation affecting parent entities must verify that dependent database records and object-storage files cannot be unintentionally orphaned.

### Rule 8 — Update this document when the architecture changes

If the database design changes intentionally, update this document and create an Architecture Decision Record (ADR) when the decision has meaningful architectural impact.

---

# 18. Current V1 Schema Summary

```text
users
 └── journeys
      ├── children
      │
      └── milestones
            └── memories
                  └── media

milestone_definitions
  └── optional reference from milestones
```

Core tables:

```text
users
journeys
children
milestone_definitions
milestones
memories
media
```

Core relationships:

```text
User      1 ─── N Journey
Journey   1 ─── 1 Child
Journey   1 ─── N Milestone
Milestone Definition 1 ─── N Milestone (optional)
Milestone 1 ─── N Memory
Memory    1 ─── N Media
```

Core ordering:

```text
Milestone → occurred_at
Milestone → sort_order (manual presentation override)
Media     → sort_order
```

Storage:

```text
PostgreSQL
  └── structured data + metadata

MinIO / S3-compatible storage
  └── actual image/video files
```

This schema is the source of truth for the LittleSteps V1 persistence layer.
