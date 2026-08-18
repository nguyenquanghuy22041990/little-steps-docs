# Component Guidelines

## 1. ICONOGRAPHY
- **Style:** Minimal, outline icons (e.g., Feather, Lucide, or Heroicons Outline). 
- **Sizes:** 
  - Small: `16px`
  - Medium (Default): `24px`
  - Large: `32px`
- **Colors:**
  - Default: `color.textPrimary`
  - Active: `color.primary`
  - Disabled: `color.textDisabled`
- **Touch Target:** Minimum `44x44px` on iOS, `48x48px` on Android. Add padding to icons to meet this requirement.

## 2. COMPONENT STANDARDS

### 2.1. Button
- **Variants:**
  - `Primary`: Background `color.primary`, Text `white`.
  - `Secondary`: Background `color.surface`, Border `color.border`, Text `color.textPrimary`.
  - `Ghost`: Transparent background, Text `color.primary`.
  - `Destructive`: Background `color.error`, Text `white`.
- **States:**
  - `Default`: Opacity 1.0.
  - `Pressed`: Opacity 0.8 or scale down slightly (0.97).
  - `Disabled`: Background `color.warmGray200`, Text `color.textDisabled`, disabled pointer events.
  - `Loading`: Show a spinner matching text color, hide text.
- **Metrics:** Height `48px`, Radius `radius.md`, Padding `0 spacing.lg`.

### 2.2. Text Input
- **States:**
  - `Default`: Border `color.border`, Background `color.surface`.
  - `Focused`: Border `color.primary`, Border width `2px`.
  - `Error`: Border `color.error`.
  - `Disabled`: Background `color.warmGray100`, Text `color.textDisabled`.
- **Metrics:** Height `48px`, Radius `radius.md`, Padding `0 spacing.md`. Font `typography.body`.

### 2.3. Card
- **Metrics:** Padding `spacing.lg`, Radius `radius.lg`, Background `color.cardBackground`, Elevation `elevation.sm`.
- **Hierarchy:** Typically uses `Heading 3` for title, `Body` or `Body Small` for content.

### 2.4. Avatar
- **Sizes:** `sm` (32px), `md` (48px), `lg` (64px).
- **Fallback:** Display the first letter of the name (uppercase) with a background of `coral100` and text `coral700`.
- **Treatment:** `radius.full`, `overflow: hidden`.

### 2.5. Icon Button
- **Metrics:** Minimum bounding box `44x44px` or `48x48px` for accessibility, even if the visual background is smaller (e.g., 32x32).
- **States:** `Pressed` state reduces opacity to 0.7.

### 2.6. Modal / Bottom Sheet
- **Radius:** `radius.xl` on the top corners (for Bottom Sheet) or all corners (for Modal).
- **Spacing:** Inner padding `spacing.xl`.
- **Overlay:** Background `color.overlay`.
- **Typography:** Title is `Heading 2`.

## 3. DESIGN STATES

### 3.1. Empty State
- **Visual:** A soft, desaturated illustration or a large, friendly icon (`size: 64px`, color: `color.textDisabled`).
- **Typography:** Title `Heading 2`, Description `Body Small` (`color.textSecondary`).
- **Action:** A Primary or Ghost CTA button directly below.

### 3.2. Loading State
- **Principles:** Prefer Skeleton screens over spinners for full-page loads to avoid layout shifts.
- **Skeletons:** Background `color.warmGray200`, animated pulse opacity.
- **Spinners:** Use `color.primary`.

### 3.3. Error State
- **Visual:** Centered warning icon (`color.error`).
- **Hierarchy:** Title `Heading 3` (e.g., "Something went wrong"), Description `Body Small`.
- **Action:** A "Retry" button (Primary or Secondary variant).
