# Mobile UI Guidelines

## 1. LAYOUT
- **Screen Horizontal Padding:** Safe area left/right + `spacing.lg` (16px).
- **Safe Area Handling:** Always respect notch/status bar (Top) and home indicator (Bottom) using `SafeAreaView` or `react-native-safe-area-context`.
- **Header Spacing:** `spacing.md` below navigation headers.
- **Vertical Rhythm:** Gap between sibling sections is `spacing.xl` (24px). Gap between elements within a section is `spacing.md` (12px).
- **Bottom Action Areas:** Fixed to the bottom of the screen with a `spacing.lg` padding, elevated with `elevation.md` to float above scrollable content.
- **Keyboard-Aware:** Input forms must use `KeyboardAvoidingView` to prevent inputs from being hidden.

## 2. MEDIA DESIGN
Photos and videos are the core of LittleSteps. Avoid unnecessary borders or decorations.
- **Image Aspect Ratios:** 
  - Standard: `4:3` or `1:1` for grid thumbnails. 
  - Full screen: `16:9` or unconstrained.
- **Corners:** Use `radius.lg` (12px) for media inside cards.
- **Loading:** Display a skeleton block with the correct aspect ratio while downloading.
- **Full-screen Viewer:** Pitch black background (`#000000`) to maximize contrast. 

## 3. TIMELINE DESIGN (Child Journey)
The timeline must feel chronological, emotional, and scannable.
- **Timeline Connector:** A vertical line (Width: `2px`, Color: `color.timelineConnector`) running down the left side.
- **Milestone Marker:** A dot overlapping the connector (Size: `12px`, Radius: `full`, Color: `color.primary`).
- **Date Typography:** Sits next to the marker. `Heading 2` or `Body Large` depending on hierarchy.
- **Memory Card:** Placed to the right of the connector. Uses the standard Card component.
- **Media Presentation:** If a memory has 1 image, display full width. If 2 images, split 50/50. If 3+, show 2 and a "+X" overlay on the last thumbnail.
- **Empty Timeline:** Display a warm Empty State inviting the user to "Add your child's first memory."

## 4. DESIGN STATES (Application Wide)
Reuse these states consistently:
- **Offline:** A small banner at the top (`color.error` or `color.warning` background, white text).
- **Pull-to-refresh:** Standard native refresh control, tinted with `color.primary`.
- **Uploading:** Show progress bar or circular progress indicator. Disable 'Submit' button to prevent double-uploads.

## 5. DARK MODE
Dark mode must not simply invert colors.
- **Background:** Shift from `warmGray50` to `warmGray900`.
- **Surfaces (Cards):** Shift from `white` to `warmGray700`.
- **Primary Color:** `coral500` works on dark backgrounds. Ensure contrast ratio is acceptable. If not, map `color.primary` to `coral300` in dark mode.
- **Borders:** Shift from `warmGray200` to `warmGray700`.
- **Rule:** Components must consume Semantic tokens (`color.background`) so they switch automatically.

## 6. ACCESSIBILITY
- **Minimum Touch Target:** `44x44px` on iOS, `48x48px` on Android.
- **Text Contrast:** Text must pass WCAG AA standards (4.5:1 for standard text, 3:1 for large text).
- **Dynamic Text:** Text sizes must not be hard-coded with absolute `height` constraints on their containers, allowing them to scale if the user increases system font size.
- **Screen Reader Labels:** All icon-only buttons MUST have an `accessibilityLabel` providing meaningful context (e.g., "Go back", "Add photo").
- **Do not rely on color:** Error states must include text (e.g., "Invalid date") or an icon, not just a red border.
