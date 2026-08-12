# LittleSteps — Test Strategy

## 1. Testing Philosophy
The goal is to maintain a high-quality portfolio project that demonstrates production readiness. Tests should focus on critical business logic, data integrity, authorization, and privacy. Avoid overly fragile tests that slow down development without providing significant confidence.

## 2. Backend Testing (NestJS)
- **Unit Tests**:
  - Focus primarily on the Service layer to test domain logic (e.g., Timeline chronological ordering logic, Ownership validation).
  - Use Jest. Mock external dependencies like Prisma (Database) and the Storage adapter (MinIO).
- **Integration Tests**:
  - Focus on API endpoints (Controllers).
  - Spin up a test PostgreSQL database (using Testcontainers or a local Docker instance) to verify actual database interactions.
  - Crucial focus: Verify authorization (ensure a user cannot access or mutate another user's journey or media).

## 3. Mobile Testing (React Native)
- **Component Tests**:
  - Use React Native Testing Library.
  - Test critical interactive components without deep mounting the entire app (e.g., Timeline rendering, Form validation for creating a milestone).
- **Manual / End-to-End (E2E) Testing**:
  - For V1, rely on structured manual testing for complex flows like the direct-to-storage media upload flow. Mocking signed URLs and real device file systems in automated E2E can introduce unnecessary complexity for a portfolio MVP.

## 4. Continuous Integration (CI)
- A CI pipeline (e.g., GitHub Actions) should be established to run on every Pull Request.
- **CI Pipeline Steps**:
  1. Linting and formatting checks (ESLint, Prettier).
  2. TypeScript compilation check (no type errors).
  3. Execution of Backend Unit and Integration tests.
