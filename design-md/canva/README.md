# Canva iOS Inspired Design System

Design system docs inspired by the [Canva iOS app](https://apps.apple.com/us/app/canva-design-photo-video/id897446215). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Canva's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `LinearGradient` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, gradient components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, tab bar + center create FAB + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Bright workspace** — near-white (`#FFFFFF`/`#F7F8FA`) light, warm-neutral (`#18191B`) dark; friendly, never harsh
- **Cyan→purple brand gradient** (`#00C4CC → #7D2AE8`) — the logo, create FAB, primary CTA and template hero rails
- **Centered, lifted gradient create FAB** — a 46pt rounded-square `+` in the tab bar, the one shadowed element (`rgba(125,42,232,0.45)`)
- **Home = greeting + big rounded search + horizontal design-type tile rail + stacked template galleries**
- **Design-type tiles** — gradient-filled rounded-square icons (Instagram `#00C4CC→#3B5CFF`, Presentation `#7D2AE8→#C13AE0`, Doc `#FF7A59→#FFC24B`)
- **Full-color template thumbnails** with gold **PRO** badges — content is loud, chrome is quiet
- **Rounded everything** — 12–16pt radii on cards/tiles/sheets, 999pt pills on every button
- **Pro is gold** (`#FFC24B`) and only gold — never the brand gradient
- **Slide-up rounded bottom sheets** — every tool, design-type chooser and share flow
- **Heavy display weights** — Plus Jakarta Sans 800 with tight negative tracking for greetings/titles
- **Content stays full-color in dark mode** — only the chrome dims
- **Soft diffuse shadows** — reserved for raised content cards and the FAB; chrome is flat

## Brand Sources

- [Canva](https://www.canva.com/)
- [Plus Jakarta Sans (Google Fonts)](https://fonts.google.com/specimen/Plus+Jakarta+Sans) — SIL OFL (Canva Sans stand-in)
- Public brand palette: cyan→purple gradient `#00C4CC → #7D2AE8`, Canva Blue `#3B5CFF`, Pro gold `#FFC24B`
