# monday.com iOS Inspired Design System

Design system docs inspired by the [monday.com iOS app](https://apps.apple.com/us/app/monday-com-work-management/id1290128888). Not the official system. Brand colors, font names, and component patterns are cross-referenced with monday.com's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, colorful board + status cells + battery rollup |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **The colorful board IS the product** — grouped item rows × column cells (Status / Person / Priority / Date / Battery)
- **Full-bleed status cells** — solid saturated slabs (green `#00C875` / orange `#FDAB3D` / red `#E2445C`), not tint chips — color carries the meaning
- **Group-color stripe** — a 6pt leading vertical stripe in the group's hue, flush to the edge, full row height
- **Colored group headers** — "This week" rendered in the group's own color, Figtree 22pt extrabold, with an item count
- **Multicolor data palette** — red/orange/green triad + blue (`#0073EA`) / purple (`#A25DDC`) / yellow (`#FFCB00`) / teal / pink, fully remappable
- **Blue is the only action color** — `#0073EA` for the FAB, CTAs, and active view tab; the rest of the palette is data
- **Deep-indigo night canvas** `#181B34` — blue-tinted, NOT neutral black; status colors stay fully saturated in dark
- **Battery rollup** — a segmented bar (green/orange/red proportions) summarizing a group's health
- **View switcher** — Main Table / Kanban / Timeline / Calendar / Chart over the same items, active gets a 2.5pt blue underline
- **Figtree typography** — bold and confident; item names 14pt/500, group headers 22pt/800, status labels 11pt/700
- **Person column** — circular assignee avatars inline in a cell, "+N" chip for overflow

## Brand Sources

- [monday.com](https://monday.com/)
- [monday.com Brand](https://monday.com/brand)
- [Figtree by Erik Kennedy (Google Fonts)](https://fonts.google.com/specimen/Figtree) — SIL OFL, the closest free match to monday.com's Poppins-family brand font
- Public brand palette: red `#E2445C` / orange `#FDAB3D` / green `#00C875` triad, action blue `#0073EA`, deep-indigo night canvas `#181B34`
