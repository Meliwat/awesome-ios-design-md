# Obsidian iOS Inspired Design System

Design system docs inspired by the [Obsidian iOS app](https://apps.apple.com/us/app/obsidian-connected-notes/id1557175442). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Obsidian's public product UI and documentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, graph + backlinks + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Deep charcoal canvas** (`#1E1E1E`) — a dark workshop for thought, not a glossy consumer surface
- **Obsidian Purple** (`#7C3AED` action / `#A78BFA` links & nodes) as the single connective accent
- **Graph view** — notes as draggable nodes, links as edges, with gentle continuous physics drift
- **Backlinks pane** — "Linked mentions" listing every note that references the current one, with context
- **Markdown source editor** — monospace lines with dimmed-but-visible syntax characters
- **Dual type system** — Inter for chrome/reading, JetBrains Mono for source/commands (the split is the identity)
- **Tag pills as connections** — `#tag` chips in tinted purple that filter the graph when tapped
- **Command palette** — a monospace, keyboard-first fuzzy action sheet; no bottom tab bar (ribbon + panes)

## Brand Sources

- [Obsidian](https://obsidian.md/)
- [Obsidian Help — Documentation](https://help.obsidian.md/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- [JetBrains Mono](https://www.jetbrains.com/lp/mono/) — SIL OFL
- Public brand palette: Obsidian Purple `#7C3AED`, link purple `#A78BFA`, charcoal canvas `#1E1E1E`
