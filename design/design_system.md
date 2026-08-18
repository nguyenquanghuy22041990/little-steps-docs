# LittleSteps - Mobile Design System

## 1. BRAND PERSONALITY
LittleSteps is a warm, modern mobile application for parents to preserve and organize their child's milestones and memories into a visual timeline.
- **Warm & Emotional:** The design should evoke positive emotions without being overly childish or cartoonish.
- **Calm & Trustworthy:** Parents are entrusting their most precious memories to this app. It must feel reliable and secure.
- **Premium but Approachable:** Clean aesthetics, balanced whitespace, and high-quality typography.
- **Family-Oriented:** The focus is always on the child's journey.

## 2. DESIGN PRINCIPLES
1. **Warm but not childish:** Use the Coral/Warm Gray palette to create a welcoming environment. Avoid primary toy colors (pure reds/yellows/blues).
2. **Content is the focus:** The UI should frame the user's photos and text. Do not distract with heavy decorations.
3. **Calm visual hierarchy:** Use typography and spacing—not just color—to establish importance. 
4. **Consistent spacing:** Rely strictly on the predefined 4px base spacing scale.
5. **Strong readability:** Native system fonts sized appropriately. High contrast for text.
6. **Accessibility over decoration:** Usability must never be compromised for visual flair.

## 3. DESIGN SYSTEM vs SCREEN SPECIFICATION
- **Design System:** Defines the visual language, tokens (colors, spacing, fonts), components, standard states, and absolute rules. It is the source of truth for *how* things look.
- **Screen Specification:** Defines screen layout, information hierarchy, navigation, content, and component composition. 
- **Rule:** Do not put screen-specific decisions (e.g., "The profile screen has 3 buttons") into the global Design System unless they represent a genuinely reusable pattern.

## 4. AI IMPLEMENTATION RULES
This is the most critical section for future AI coding agents working on the LittleSteps React Native application.

1. **Read the Design System:** You must read the documentation in `little-steps-docs/design/` before implementing UI.
2. **Search Before Creating:** Always search for existing design-system components before creating a new one.
3. **Reuse Tokens:** You MUST reuse the semantic and component tokens defined in `design_tokens.md`.
4. **No Hard-coded Colors:** NEVER hard-code colors (e.g., `#FF7F50`) directly in feature screens.
5. **No Arbitrary Spacing:** DO NOT hard-code arbitrary margins or padding (e.g., `padding: 17`, `marginTop: 13`). Use the spacing scale (`spacing.md`, `spacing.lg`).
6. **No Invented Typography:** DO NOT invent font sizes or weights.
7. **No Arbitrary Radii:** DO NOT invent arbitrary corner radii.
8. **Semantic Over Primitive:** Prefer semantic tokens (`color.primary`) over primitive tokens (`color.coral500`).
9. **Component Abstraction:** Create a new token or reusable component ONLY when the existing system cannot represent the required design. Do not create duplicate components.
10. **Minimal Screen Styling:** Keep screen-specific styling minimal.
11. **Platform Consistency:** Maintain consistency between iOS and Android.
12. **No Silent Changes:** Do not change the Design System silently.
13. **Raise Conflicts:** If a feature requirement conflicts with this Design System, stop and surface the conflict to the human user.
