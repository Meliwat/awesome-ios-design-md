# Apple Notes iOS Inspired Design System

Design system docs inspired by the [Apple Notes iOS app](https://apps.apple.com/us/app/notes/id1110145109). Not the official system. Brand colors (Notes Orange, Folder Yellow, the cream paper canvas), typography (SF Pro Display Heavy on large nav titles, SF Pro Text 17/1.5 on body), and component patterns (yellow folder glyph, three-line note row, orange FAB) are cross-referenced with Apple's iOS Notes app and the Human Interface Guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, folder glyph as `Path`, note row, FAB, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, SVG folder glyph, Reanimated checklist |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, Canvas yellow-folder glyph + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery/apple-notes](https://www.spectr.to/gallery/apple-notes) |

## Signature Moves

- **Notes Cream canvas** `#FFFBED` in light, **Dark Paper** `#1A1A1A` in dark — both warmer than typical iOS apps, evoking real paper
- **Notes Orange** `#F09A38` as the primary action color — folder glyph stroke, New Note FAB, link color, cursor color, selection accent
- **Folder Yellow** `#F5D773` — the iconic yellow used on every folder glyph, identical to macOS Finder and iCloud Drive
- **The Yellow Folder glyph** — a 3D tab-folder shape with `#F5D773` fill, `#F09A38` stroke, warm highlight, and subtle 2-degree forward tilt
- **SF Pro Display 34pt Heavy (800)** on the large nav title — one of the few iOS apps to use the rare Heavy weight
- **Body type at 17pt 400 with 1.5 line-height** — the calmest body in any iOS app, optimized for reading
- **Three-line note row** — title (Semibold 16pt) + preview (Regular 14pt Slate) + date (Regular 12pt Slate)
- **Highlight Yellow** `#FFEB78` reserved for text-selection highlights inside the note body
- **Orange-tinted FAB shadow** — `rgba(240,154,56,0.30) 0 4px 12px` makes the New Note FAB feel like it's glowing
- **No bottom tab bar** — hierarchical navigation (folders → notes → editor) with standard iOS push/pop transitions

## Brand Sources

- [Apple Notes — App Store](https://apps.apple.com/us/app/notes/id1110145109)
- [Apple Human Interface Guidelines — Typography](https://developer.apple.com/design/human-interface-guidelines/typography)
- [SF Pro — Apple Fonts](https://developer.apple.com/fonts/)
- macOS Finder folder glyph: the same `#F5D773` yellow + `#F09A38` orange treatment
- Public Apple system palette: `systemOrange` `#F09A38`, `systemYellow` `#FFCC00`, `secondaryLabel` `#8E8E93`, `systemBackground` `#FFFFFF` (Notes uses a warmer variant `#FFFBED`)
