# LittleSteps — Acceptance Criteria (V1)

## 1. Journey & Profile
- **AC1.1 (Create Profile)**: Given a user is creating a journey, when they submit valid child data (name, DOB), then a new Child Journey is created in the database and they are navigated to the empty timeline.
- **AC1.2 (Edit Profile)**: Given an existing profile, when the user updates the avatar and saves, then the new avatar is successfully uploaded, the database is updated, and the new avatar is displayed across the app.

## 2. Milestones & Timeline
- **AC2.1 (Timeline Ordering)**: Given a journey with multiple milestones, when the timeline is loaded, then milestones are sorted by `occurred_at` in ascending order (oldest first), regardless of when they were created in the app.
- **AC2.2 (Add Custom Milestone)**: Given the timeline, when a user adds a milestone without selecting a suggested definition, then it is saved as a custom milestone with the provided title and date.
- **AC2.3 (Delete Milestone)**: Given a milestone with memories and media, when the user deletes it, then the milestone, its memories, and its media metadata are removed from the database, and the actual media files are deleted (or queued for deletion) from object storage.

## 3. Memories & Media
- **AC3.1 (Add Memory)**: Given a milestone, when the user adds a memory with text, then the memory appears grouped under that specific milestone.
- **AC3.2 (Upload Media via Signed URL)**: Given a memory, when a user selects a photo, then the app requests a signed URL from the API, uploads the binary directly to object storage, confirms completion with the API, and the API saves the media metadata and storage key.
- **AC3.3 (Reorder Media)**: Given a memory with 3 photos, when the user moves the 3rd photo to the 1st position, then the new `sort_order` is persisted in the database and displayed immediately on the UI.
