# GroupMe iOS Inspired Design System

Design system docs inspired by the [GroupMe iOS app](https://apps.apple.com/us/app/groupme/id392796698). Not the official system. Brand colors, font conventions, and component patterns are cross-referenced with GroupMe's public product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, colored nav bar, bubbles, like pill, gallery, tab bar |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **GroupMe Blue chrome** (`#00AFF0`) — the chat nav bar is a solid block of brand blue with white text; the single most recognizable piece of GroupMe UI
- **Blue outbound bubbles** (`#00AFF0`) / neutral inbound (`#F0F0F0` light, `#2A2A2C` dark) — never the per-group accent
- **Heart "like" count pill** — double-tap a bubble; a `#FF3B5C` heart + count docks in a pill (`#FFFFFF` / `#1C1C1E`) below the bubble's trailing-bottom corner
- **Circular generated-gradient avatars** with the member's initial — a wall of friendly faces
- **Colored sender name** above the first bubble in a run, tinted in the group accent (default `#00AFF0`)
- **Per-group color themes** — coral `#FF6B6B`, green `#2ECC71`, purple `#9B59B6`, sunset gradient — recolor avatars + sender names while blue chrome holds
- **Inline image gallery grid** — multi-photo shares render as a 2-col gap grid in a bubble with a `+N` overflow tile
- **Pill composer** with a `+` attachment button and a circular blue send button
- **Bubble run grouping** — consecutive same-sender messages collapse: avatar + name shown once
- **Bubbles** are 18pt rounded with only the tail corner clipped to ~5pt
- **System face (SF Pro)** — no decorative brand typeface; color + avatars carry the brand
- **Warm dark mode** — canvas `#121212`, never pure black; brand blue chrome unchanged

## Brand Sources

- [GroupMe](https://groupme.com/)
- GroupMe on the App Store: <https://apps.apple.com/us/app/groupme/id392796698>
- Public brand palette: GroupMe Blue `#00AFF0`, like heart `#FF3B5C`, neutral inbound bubble `#F0F0F0`
- Typeface: Apple system font (SF Pro Text / SF Pro Display) — no custom brand face
