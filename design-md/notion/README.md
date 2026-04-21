# Notion iOS Inspired Design System

Design system docs inspired by the [Notion iOS app](https://apps.apple.com/us/app/notion-notes-docs-tasks/id1232780281). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Notion's public product UI and the company's design posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure white canvas** (`#FFFFFF`) light / charcoal (`#191919`) dark — content is the interface
- **Block-based editor** — every paragraph, heading, toggle, image, callout is a draggable "block"
- **Block handles** (⋮⋮⋮ + `+`) appear only on long-press or hover — invisible until needed
- **`/` command palette** — floating 280pt panel anchored at cursor; the primary creation gesture
- **Emoji page icon + full-bleed cover image** — user-chosen page hero, not designer-set
- **Nine muted pastel page backgrounds** — gray / brown / orange / yellow / green / blue / purple / pink / red
- **User-switchable typography** — Inter (default) / Lora (serif) / IBM Plex Mono (mono)
- **Warm dark text** `#37352F` — NOT pure black; document rhythm, not UI rhythm
- **Toggle blocks** with ▸/▾ chevron affordance for collapsible sections
- **Mentions as inline chips** with avatar + 10% blue background + blue text
- **No brand accent** — primary CTAs are Notion Black (`#000000`); no "Spotify Green" equivalent
- **Database views** — Table / Board / Timeline / Calendar / Gallery / List — same data, different renderings

## Brand Sources

- [Notion](https://www.notion.so/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- [Lora (Google Fonts)](https://fonts.google.com/specimen/Lora) — SIL OFL
- [IBM Plex Mono](https://www.ibm.com/plex/) — SIL OFL
- Public brand palette: warm text `#37352F`, link blue `#2E75CC`, 9 pastel page backgrounds
