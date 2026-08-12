# LittleSteps — Design System (Draft)

## 1. Design Philosophy
The application should feel warm, personal, and organized. It is a journal for memories, not a generic utility app. The UI should fade into the background, allowing the user's photos and stories to be the primary focus.

## 2. Typography
- **Primary Font**: Use system defaults (San Francisco on iOS, Roboto on Android) or a clean, modern sans-serif like Inter to ensure excellent readability.
- **Hierarchy**: 
  - **Header 1**: Journey Title (Large, bold)
  - **Header 2**: Milestone Title (Medium, semi-bold)
  - **Body**: Memory text (Regular weight, high readability)
  - **Caption**: Dates, metadata, helper text (Small, muted color)

## 3. Colors
- **Background**: Off-white or a very light warm gray to reduce eye strain and provide a soft canvas.
- **Primary Accent**: A warm, calm color (e.g., soft terracotta, sage green, or muted blue) used for primary buttons and active states.
- **Text**: Dark gray (avoid pure black) for better contrast and softer reading experience.
- **Danger**: Muted red for destructive actions (e.g., Delete Milestone).

## 4. Key Components
- **Timeline Node**: The visual representation of a milestone on the timeline axis, connecting events chronologically.
- **Media Card**: A container for photos/videos featuring slight rounded corners to look modern, safe, and friendly.
- **Action Sheets/Bottom Sheets**: Used for contextual actions (Edit, Delete) to keep the main timeline UI uncluttered.

## 5. Accessibility
- **Touch Targets**: Minimum touch target size of 44x44 points for all interactive elements.
- **Contrast**: Ensure sufficient color contrast for all text (minimum 4.5:1 ratio per WCAG standards).
