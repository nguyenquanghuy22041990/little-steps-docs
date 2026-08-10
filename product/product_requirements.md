# LittleSteps — Product Requirements

## 1. Purpose

LittleSteps is a private memory-journal product for turning a child's important moments into a meaningful, chronological story.

It is not a generic photo album. Photos and videos are retained with the milestone and personal memory that give them meaning.

This document defines the product boundary for LittleSteps V1. It is the source of truth for what the V1 product should and should not do.

## 2. Product vision

Parents and caregivers should be able to preserve a child's important milestones with the context they will want to remember later.

The product should make it easy to answer:

> What happened, when did it happen, and what made that moment special?

## 3. Target user

The primary V1 user is a parent or caregiver documenting one child's early-life milestones.

The user may capture a milestone as it happens or add it retrospectively from existing photos and videos. The product must support both cases because the date an event happened can differ from the date it is recorded.

## 4. V1 product scope

V1 supports one Child Journey per journey record, with these capabilities:

1. Create and edit a child profile.
2. Browse suggested milestone definitions and create custom milestones.
3. Create, edit, and delete milestones in the child's journey.
4. Create, edit, and delete one or more memories in a milestone.
5. Add, view, remove, and reorder photos and videos in a memory.
6. View milestones as a chronological timeline.
7. View the story, description, and media attached to a milestone or memory.

### 4.1 Child profile

The user can record:

- the child's name;
- birthday; and
- an optional avatar.

The birthday allows the product to show or calculate the child's age at a milestone in the future. The birthday is not itself a milestone.

### 4.2 Milestones

A milestone represents an important event or achievement. The product offers a catalog of suggested milestone definitions for a Child Journey and also lets the user create a custom milestone.

Examples include:

- First Smile
- First Rollover
- First Crawl
- First Tooth
- First Step
- First Word
- First Birthday

Suggestions make it easier to begin documenting a journey; they do not limit what a user can record. A user can select a suggestion or create a milestone without one.

For every milestone, the user can provide:

- an optional selected milestone definition;
- title;
- optional description of what happened; and
- the date and time it occurred.

The milestone's occurred time, rather than its creation time, determines its default position in the timeline.

### 4.3 Memories

A milestone can contain multiple memories. A memory retains the personal story or context surrounding the event.

For every memory, the user can provide:

- title;
- optional description; and
- date and time it occurred.

For example, a `First Step` milestone might have a memory called `He walked toward Grandma`, with a description of the family's reaction.

### 4.4 Media: photos and videos

In the domain model, **Media** is the collective term for photos and videos. This product document uses “photos and videos” when describing the user-facing experience.

Each memory can include multiple photos and videos. The product must retain their order within the memory.

The product stores media metadata and a durable storage reference. It does not treat a storage-provider URL as permanent product data.

## 5. Primary user flow

```text
Create Child Journey
  -> Add child profile
  -> Add milestone with occurred date
  -> Add a memory describing the moment
  -> Attach photos and/or video
  -> See the milestone in chronological timeline
```

The user can later revise the text, date, and media order of the same story without needing to recreate it.

## 6. Manage existing content

After the initial happy path, the user must be able to maintain a journey without recreating it.

| Content | Supported actions |
| --- | --- |
| Child profile | Edit name, birthday, or avatar. |
| Milestone | Choose or remove a suggested definition; create, view, edit, and delete its title, description, and occurred time. |
| Memory | Create, view, edit, and delete its title, description, and occurred time. |
| Media | Add, view, remove, and reorder photos and videos within a memory. |

Changing a milestone's occurred time must place it in the appropriate chronological position in the timeline. Editing or deleting an item must not alter unrelated milestones, memories, or media.

Deleting a milestone removes the milestone and its associated memories and media from the product experience. Deleting a memory removes only that memory and its media. Deleting media removes only the selected photo or video.

## 7. Timeline behavior

The timeline is the primary way to browse a Child Journey.

By default, it displays milestones chronologically by the time they happened, oldest first. Each milestone should communicate:

- what happened;
- when it happened;
- optional summary/context; and
- that photos or videos are available.

Opening a milestone reveals its memories and associated media. The product should preserve the distinction between the milestone's event summary and a memory's personal narrative.

## 8. Product rules

- A journey represents one coherent story and, in V1, has one child profile.
- A milestone belongs to one journey.
- A memory belongs to one milestone.
- A media item belongs to one memory.
- A milestone can have no memories initially, but a completed rich story may include one or more memories and media items.
- Photos and videos are attachments to a memory, not standalone timeline entries.
- The user can add a past event after it occurred; timeline placement remains based on the event date.
- Deleting a parent item removes its dependent content from the product experience. Storage cleanup is an implementation responsibility.

## 9. Out of scope for V1

The following are intentionally excluded until a later product decision:

- authentication and account-management UI;
- sharing journeys with family members;
- collaboration, comments, reactions, or social feeds;
- AI-generated captions, milestone detection, or media classification;
- reminders and notifications;
- albums, tags, search, or advanced filters;
- web client;
- non-child journeys such as learning, fitness, travel, or custom journeys;
- payments and subscriptions.

The data model may retain ownership and a generic journey core, but these exclusions must not be implemented speculatively.

## 10. MVP success criteria

V1 succeeds when a parent or caregiver can reliably create a child's journey, document meaningful milestones with context and media, and revisit them in a coherent chronological timeline.

Before considering V1 complete, validate that a user can:

1. Create a child profile.
2. Add a past or present milestone.
3. Add a memory to that milestone.
4. Attach at least one photo or video.
5. See the milestone in the correct chronological position.
6. Edit the child profile, a milestone, a memory, and media order without affecting unrelated entries.
7. Delete a media item, memory, or milestone and observe the expected dependent-content behavior.

## 11. Related documents

- [Domain model](../domain/domain_model.md)
- [Database design](../domain/database_design.md)

Changes to V1 scope or these product rules require corresponding review of the linked domain documents. Significant durable decisions should be recorded as an Architecture Decision Record.
