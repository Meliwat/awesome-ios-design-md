# Bear iOS Inspired Design System

Design system docs inspired by the [Bear iOS app](https://apps.apple.com/us/app/bear-markdown-notes/id1016366447). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Bear's public product UI and Shiny Frog's design posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, live-Markdown renderer, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, live-Markdown renderer, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, live-Markdown renderer + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Calm canvas** — pure white (`#FFFFFF`) light / warm blue-charcoal (`#21252B`) dark; the prose is the interface
- **Live Markdown rendering** — syntax markers (`#`, `**`, `- [ ]`) dim to `#6B7280` in-place; never a preview pane
- **#hashtags as the filing system** — inline red (`#E0566F`) tappable tokens; no folders, nested tags build the sidebar
- **One brand accent** — the red→orange gradient `#E0566F → #FF8A65` is the icon, compose FAB, checkbox fill and selection
- **Every interactive glyph is Bear Orange** `#FF8A65` — nav, info-bar, list affordances; no second tint
- **Three-pane responsive layout** — tag sidebar → note list → editor (pushed nav on iPhone, side-by-side on iPad)
- **User-switchable typography** — Inter (default sans) / Lora (serif) / JetBrains Mono (mono + all code)
- **Quote = orange left rule + Lora italic** `#FF8A65` 3pt rule, no background
- **Code = filled `#282C34` surface** + JetBrains Mono, 8pt radius, no borders
- **Reading ergonomics** — 22pt side insets, 1.55 body line height, 16pt paragraph gaps
- **Theme engine** — multiple shipped themes (Charcoal, Red Graphite, Dark Graphite, Solarized…); the brand gradient is constant
- **One elevated element** — the compose FAB with a tinted red glow `rgba(224,86,111,0.35)`; content blocks are flat

## Brand Sources

- [Bear](https://bear.app/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- [Lora (Google Fonts)](https://fonts.google.com/specimen/Lora) — SIL OFL
- [JetBrains Mono](https://www.jetbrains.com/lp/mono/) — SIL OFL
- Public brand palette: red→orange gradient `#E0566F → #FF8A65`, charcoal canvas `#21252B`, link blue `#5AAFEF`
