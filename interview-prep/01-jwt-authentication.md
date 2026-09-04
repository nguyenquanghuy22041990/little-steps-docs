# Feature: JWT Authentication (Login & Register)

## Overview
Implemented a robust authentication system using JSON Web Tokens (JWT) to secure backend endpoints and manage user sessions on the frontend. The system allows users to register, log in, and securely access their own data.

## Technical Decisions & Rationale

1. **Why JWT (JSON Web Token)?**
   - **Statelessness**: JWTs are self-contained and do not require server-side session storage. This makes the backend easily scalable, especially when hosted on serverless or containerized environments (like Render).
   - **Performance**: Validating a JWT is faster than performing a database lookup for a session token on every request.
   - **Mobile Friendly**: React Native handles token storage well using `AsyncStorage`.

2. **Why `bcrypt` for Password Hashing?**
   - Storing plaintext passwords is a severe security vulnerability.
   - We used `bcrypt` to hash passwords with an auto-generated salt. `bcrypt` is intentionally computationally expensive, mitigating brute-force and rainbow table attacks.

3. **NestJS `Passport` and `JwtAuthGuard`**
   - Leveraged NestJS's `PassportModule` and `@nestjs/jwt` for standardizing the authentication flow.
   - Implemented a custom `@CurrentUser()` decorator to cleanly extract the `userId` from the validated JWT payload injected by the `JwtStrategy`. This avoids manually parsing headers in every controller.

## Key Functions & Components

### Backend
- **`AuthService.register(email, password)`**: Checks if the email is unique, generates a salt, hashes the password using `bcrypt`, saves the user to PostgreSQL, and returns a JWT.
- **`AuthService.login(email, password)`**: Finds the user, compares the password hash using `bcrypt.compare`, and returns a JWT if valid.
- **`JwtStrategy`**: Validates the incoming Bearer token from the `Authorization` header and extracts the `sub` (userId).
- **`JwtAuthGuard`**: Applied to controllers (e.g., `@UseGuards(JwtAuthGuard)`). Automatically denies requests with missing or invalid tokens (returns 401 Unauthorized).

### Frontend (React Native)
- **`AuthContext.tsx`**: A React Context provider that manages global authentication state (`user`, `isLoading`). It automatically checks `AsyncStorage` on app launch to persist user sessions.
- **Axios Interceptor (`client.ts`)**: Automatically intercepts every outgoing HTTP request, reads the token from `AsyncStorage`, and attaches it to the `Authorization: Bearer <token>` header.
- **Conditional Routing (`AppNavigator.tsx`)**: Renders `AuthNavigator` (Login/Register) if the user is unauthenticated, and `MainNavigator` (Home, Profile) if authenticated. This prevents unauthorized access to protected screens.
