# LittleSteps — Domain Model

## 1. Purpose

This document defines the business concepts and relationships that LittleSteps V1 represents. It explains what each concept means; table names, column types, indexes, and storage implementation belong in [database_design.md](database_design.md).

LittleSteps is a chronological storytelling product, not merely a media album. Its value is preserving meaningful moments in the context of a journey.

## 2. V1 scope

V1 supports only a **Child Journey**:

- a child profile;
- milestones in the child's life;
- one or more memories for each milestone;
- photos and videos attached to a memory; and
- a timeline ordered by when events happened.

Authentication, sharing, collaboration, reactions, and non-child journey types are outside this scope.

## 3. Core concepts

```text
User
 └── Journey
      ├── Child profile
      └── Milestone
           └── Memory
                └── Media

Milestone Definition
  └── provides a suggested kind of milestone for a Journey type
```

The reusable core is:

```text
Journey -> Milestone -> Memory -> Media
```

`Child` is a profile specific to a Child Journey. It is not the root of the generic model.

`Milestone Definition` is a reusable catalog entry, not an event in a user's timeline. A Milestone can refer to a definition or be entirely custom.

### User

The person who owns one or more journeys. Ownership exists in the model even though V1 may defer sign-in and authentication UX.

### Journey

The container for a coherent, long-running story. In V1, every journey is a Child Journey and has exactly one child profile.

The journey owns its milestones and provides the timeline's overall context.

### Child profile

The information that identifies the subject of a Child Journey: name, date of birth, and optional avatar. A birthday belongs to the child, not to a milestone.

### Milestone

An important event or achievement in a journey, such as a first smile, first crawl, first step, first word, or a custom event.

A milestone answers:

> What important event happened?

It records the event's title, optional summary, and real-world occurrence time. A milestone can optionally refer to a Milestone Definition, but its title remains an editable record of what the user wants to call this particular event. A milestone can contain several memories because one event may have several perspectives or moments worth retaining.

### Milestone Definition

A reusable catalog entry used to suggest relevant milestones for a particular Journey type. For a Child Journey, examples may include First Smile, First Crawl, First Step, and First Word.

A definition has a stable code and a default display title. It is a suggestion, not a requirement: users can create a custom milestone without choosing one. Definitions make it possible to offer guided milestone suggestions now and to add separate catalogs for future Journey types such as Study or Travel without changing the core timeline model.

### Memory

The detailed story or personal context associated with a milestone. A memory can have its own title, description, and occurrence time.

A memory answers:

> What do I want to remember about this event?

Descriptions describing the shared moment belong here rather than on an individual media item.

### Media

A photo or video attached to a memory. Media represents the asset and its metadata, not the narrative. Its file is stored in object storage; the product persists a stable storage reference and exposes usable URLs through the backend.

## 4. Relationship rules

| Parent | Relationship | Child | V1 rule |
| --- | --- | --- | --- |
| User | owns | Journey | A user can own many journeys. |
| Journey | has | Child profile | Exactly one for a V1 Child Journey. |
| Journey | contains | Milestone | A journey can contain many milestones. |
| Milestone Definition | can describe | Milestone | A milestone optionally refers to one definition appropriate for its journey type. |
| Milestone | contains | Memory | A milestone can contain many memories. |
| Memory | contains | Media | A memory can contain many photos and videos. |

A child profile cannot exist without its journey. A milestone cannot be reassigned outside its journey through a memory or media relationship.

## 5. Time and ordering

Three kinds of time must remain distinct:

| Concept | Meaning | Used for |
| --- | --- | --- |
| `occurred_at` | When the event happened in real life. | The default chronological timeline. |
| `created_at` | When the record was added to LittleSteps. | Audit and record history, never business ordering. |
| `updated_at` | When the record was last changed. | Audit and synchronization, never business ordering. |

The default timeline sorts milestones by `occurred_at` ascending. An explicitly managed `sort_order` may control presentation where the product intentionally supports manual placement; it does not change the underlying event date.

For a child, the product may calculate age at an event from the child's birthday and the event's `occurred_at`.

## 6. Media and storage boundary

Media bytes do not belong in PostgreSQL. The backend owns the storage integration and uses a stable `storage_key` rather than treating a provider URL as permanent data.

The intended lifecycle is:

```text
Mobile requests upload authorization
  -> Mobile uploads directly to object storage
  -> Mobile confirms completion with the backend
  -> Backend records usable media metadata
```

V1 implementation targets local development first. MinIO is the local-development storage provider; an S3-compatible production provider may replace it later without changing the domain model.

## 7. V1 invariants

- Every V1 journey has type `CHILD` and exactly one child profile.
- A milestone belongs to exactly one journey.
- A milestone may reference one Milestone Definition or no definition when it is custom.
- A referenced Milestone Definition must apply to the milestone's Journey type.
- A memory belongs to exactly one milestone.
- Media belongs to exactly one memory.
- Image and video are the only media types in V1.
- A timeline reflects real-world event time, not upload or record-creation time.
- Deleting a parent removes dependent database records; associated object-storage files require explicit application cleanup.

## 8. Out of scope and extension point

The generic Journey–Milestone–Memory–Media chain deliberately leaves room for later journey types, but V1 must not add their profiles, UI, tables, APIs, or assumptions prematurely.

Any change to the concepts or invariants in this document must also be reviewed against [database_design.md](database_design.md). Record a significant durable architectural choice as an ADR before implementation.
