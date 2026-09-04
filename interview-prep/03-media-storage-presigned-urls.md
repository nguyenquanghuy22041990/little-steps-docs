# Feature: Media Storage with MinIO & Presigned URLs

## Overview
Implemented an object storage system using MinIO (S3-compatible) to handle user-uploaded photos and videos, bypassing the backend server to optimize performance.

## Technical Decisions & Rationale

1. **Why MinIO?**
   - It is an open-source, S3-compatible object storage server. Developing locally with MinIO ensures zero vendor lock-in and a seamless transition to Cloudflare R2 or AWS S3 in production.

2. **Why Presigned URLs? (Direct-to-S3 Upload)**
   - **Problem**: If the mobile app sends large video files directly to the NestJS backend, it consumes backend memory, CPU, and bandwidth, creating a massive bottleneck.
   - **Solution**: The app requests a "Presigned URL" from the backend. The backend securely generates a temporary, cryptographically signed URL. The mobile app then uploads the file *directly* to MinIO/S3 using this URL.
   - **Benefit**: The backend only handles lightweight JSON API requests. All heavy data transfer happens directly between the client and the object storage server.

3. **Unique Storage Keys**
   - Storage keys (filenames) are generated using `uuid` and timestamps to prevent filename collisions when multiple users upload files with the same name (e.g., `image.jpg`).

## Key Functions & Components

### Backend
- **`StorageController.getPresignedUrl()`**: Validates the requested file extension and size, then uses the AWS S3 SDK (`@aws-sdk/client-s3`) to generate a `PutObjectCommand` presigned URL.

### Frontend
- **Image Picker (`expo-image-picker`)**: Selects media from the device's camera roll.
- **Fetch API (Blob Upload)**: 
  - Resolves the local file URI into a `Blob`.
  - Executes a `PUT` request directly to the presigned URL with the raw Blob data.
