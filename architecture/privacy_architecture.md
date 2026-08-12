# LittleSteps — Privacy Architecture

## 1. Core Principle
LittleSteps handles highly sensitive family data, including children's photos, videos, birthdays, and personal family memories. Privacy and security must be treated as first-class architectural concerns. The system should minimize the amount of sensitive data it exposes and never make private media publicly accessible.

## 2. Storage Privacy
- **No Public Buckets**: The object storage (MinIO for local, S3/R2 for production) must be configured to block all public access.
- **Signed URLs**: Media is only accessible via short-lived signed read URLs generated dynamically by the backend API. The mobile app must never receive permanent storage credentials or static public URLs.
- **Database Separation**: PostgreSQL stores only metadata and the `storage_key`. It must NOT store binary files.

## 3. Data Ownership & Access Control
- **Strict Authorization**: Every API request must validate that the authenticated user owns the Journey before allowing read, write, or signed URL generation for any Milestone, Memory, or Media within that Journey.
- **No Cross-Journey Leakage**: Database queries must always be scoped by `user_id` (typically by joining through the `journeys` table) to enforce ownership. Direct ID lookups without ownership validation are prohibited.

## 4. Deletion
- **Hard Deletes (V1)**: When a user deletes a milestone or memory, the dependent database records should be hard-deleted (using cascading deletes or application-level transactions).
- **Storage Cleanup**: Deleting media metadata from the database must trigger the actual object removal from MinIO/S3. This prevents orphaned files and respects the user's intent to destroy sensitive data.

## 5. Observability & Logging
- **No Sensitive Data in Logs**: Application logs must NEVER contain Personally Identifiable Information (PII) such as child names, user emails, memory descriptions, or media URLs.
- **Safe Logging**: Log only entity IDs, action types, error codes, and HTTP status codes to facilitate debugging without compromising privacy.
