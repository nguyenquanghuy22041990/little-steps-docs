# Feature: Mobile UI & Navigation Architecture

## Overview
Built a responsive, cross-platform mobile user interface using React Native and Expo, with a structured navigation flow using React Navigation.

## Technical Decisions & Rationale

1. **Why React Navigation (Native Stack)?**
   - Used `@react-navigation/native-stack` because it utilizes the native navigation primitives (UIViewController on iOS, Fragment on Android), ensuring 60fps animations and native swipe-back gestures.

2. **Conditional Navigation Flow**
   - Separated the navigation into `AuthNavigator` (Login, Register) and `MainNavigator` (Home, Profile, etc.).
   - This architectural pattern guarantees that an unauthenticated user cannot accidentally access protected screens via deep links or back-button manipulation.

3. **Global Theme System**
   - Abstracted colors, spacing, and typography into a `theme.ts` file and created reusable components (`Typography`, `Button`, `TextField`).
   - This ensures UI consistency across the app and makes it trivial to implement Dark Mode or rebrand the app in the future.

4. **Handling Localhost on Android Emulators**
   - **Challenge**: Android emulators route `localhost` to the emulator device itself, not the development machine.
   - **Solution**: Used `Platform.OS === 'android' ? '10.0.2.2' : 'localhost'` dynamically in the API client to ensure seamless API and MinIO image fetching across both iOS Simulators and Android Emulators.

## Key Functions & Components
- **`AppNavigator.tsx`**: The root navigation router that conditionally renders stacks based on the `AuthContext` state.
- **`AuthContext.tsx`**: Provides the state that drives the navigation conditional rendering.
