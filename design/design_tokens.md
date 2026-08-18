# Design Tokens

## 1. LAYERED TOKEN ARCHITECTURE
We use a 3-tier token architecture.
- **Primitive Tokens:** Raw values (e.g., `#FF7F50`, `16px`). DO NOT use these directly in UI components.
- **Semantic Tokens:** Meaningful names tied to intent (e.g., `color.primary`, `spacing.md`). Components SHOULD use these.
- **Component Tokens:** Specific to a component (e.g., `color.buttonPrimaryBackground`). Use these within the specific component implementation.

## 2. COLOR SYSTEM

### 2.1. Primitive Colors
- `coral100`: `#FFDBCF`
- `coral300`: `#FFA580`
- `coral500`: `#FF7F50` (Core Warm Coral)
- `coral700`: `#CC5326`
- `warmGray50`: `#FAFAF9`
- `warmGray100`: `#F5F5F4`
- `warmGray200`: `#E7E5E4`
- `warmGray300`: `#D6D3D1`
- `warmGray400`: `#A8A29E`
- `warmGray500`: `#78716C`
- `warmGray700`: `#44403C`
- `warmGray900`: `#1C1917`
- `red500`: `#EF4444`
- `green500`: `#22C55E`
- `blue500`: `#3B82F6`
- `white`: `#FFFFFF`
- `black`: `#000000`

### 2.2. Semantic Colors
- `color.primary`: `coral500`
- `color.primaryPressed`: `coral700`
- `color.background`: `warmGray50` (Light Mode) / `warmGray900` (Dark Mode)
- `color.surface`: `white` (Light) / `warmGray700` (Dark)
- `color.surfaceElevated`: `white` (Light) / `warmGray500` (Dark)
- `color.textPrimary`: `warmGray900` (Light) / `warmGray50` (Dark)
- `color.textSecondary`: `warmGray500` (Light) / `warmGray300` (Dark)
- `color.textDisabled`: `warmGray300` (Light) / `warmGray500` (Dark)
- `color.border`: `warmGray200` (Light) / `warmGray700` (Dark)
- `color.divider`: `warmGray100` (Light) / `warmGray700` (Dark)
- `color.error`: `red500`
- `color.success`: `green500`
- `color.info`: `blue500`
- `color.overlay`: `rgba(28, 25, 23, 0.5)` (Black with 50% opacity)

### 2.3. Component Colors (Examples)
- `color.buttonPrimaryBackground`: `color.primary`
- `color.buttonPrimaryText`: `white`
- `color.cardBackground`: `color.surface`
- `color.timelineConnector`: `color.border`

## 3. TYPOGRAPHY
**Font Family:** System Native (`San Francisco` on iOS, `Roboto` on Android).

| Role | Font Size | Font Weight | Line Height | Intended Usage |
|---|---|---|---|---|
| Display | 32px | Bold (700) | 40px | Empty states, big hero numbers |
| Heading 1 | 24px | Bold (700) | 32px | Screen titles |
| Heading 2 | 20px | SemiBold (600) | 28px | Section titles, Timeline dates |
| Heading 3 | 18px | SemiBold (600) | 24px | Card titles, Milestone names |
| Body Large | 18px | Regular (400) | 28px | Long-form reading |
| Body | 16px | Regular (400) | 24px | Standard paragraphs, inputs |
| Body Small | 14px | Regular (400) | 20px | Secondary descriptions |
| Caption | 12px | Regular (400) | 16px | Timestamps, metadata |
| Label | 14px | Medium (500) | 20px | Input labels |
| Button | 16px | SemiBold (600) | 24px | CTA text |

## 4. SPACING
Base unit: `4px`.
DO NOT use arbitrary spacing. Only use these tokens.
- `spacing.xs`: `4px`
- `spacing.sm`: `8px`
- `spacing.md`: `12px`
- `spacing.lg`: `16px`
- `spacing.xl`: `24px`
- `spacing.xxl`: `32px`
- `spacing.3xl`: `48px`

## 5. BORDER RADIUS
- `radius.sm`: `4px` (Checkboxes, small tags)
- `radius.md`: `8px` (Inputs, standard buttons)
- `radius.lg`: `12px` (Cards, image containers)
- `radius.xl`: `16px` (Bottom sheets, large modal cards)
- `radius.full`: `9999px` (Avatars, circular icon buttons)

## 6. ELEVATION / SHADOWS
Keep shadows minimal for a calm, modern feel.
- `elevation.none`: No shadow.
- `elevation.sm`: Soft drop shadow for cards (`0px 2px 4px rgba(0,0,0,0.05)`).
- `elevation.md`: Moderate shadow for sticky headers/bottom bars (`0px 4px 8px rgba(0,0,0,0.08)`).
- `elevation.lg`: Deep shadow for modals/bottom sheets (`0px 8px 16px rgba(0,0,0,0.12)`).
