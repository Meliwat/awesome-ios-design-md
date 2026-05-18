# Grammarly iOS Inspired Design System

Design system docs inspired by the [Grammarly iOS app](https://apps.apple.com/us/app/grammarly-keyboard-editor/id1158877342). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Grammarly's public product UI, marketing site, and brand posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, suggestion card + score ring + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Calm writing canvas** — pure white (`#FFFFFF`) light / warm charcoal (`#121212`) dark; the document is the interface
- **One brand accent** — Grammarly Green (`#15C39A` → `#11A683`); no secondary brand color
- **Color-coded category underlines** — Correctness red (`#E5484D`), Clarity blue (`#3B82F6`), Engagement green (`#16A34A`), Delivery purple (`#8B5CF6`)
- **Suggestion card** — slides up from the bottom: category dot + strikethrough → green swap + explanation + green Accept pill
- **Document score ring** — thin animated 0–100 gauge, fills green ≥90, gold 60–89, red <60
- **Assistant bar** — pinned bottom strip: green orb + running suggestion count + Review CTA
- **Tone detector** — emoji + tone pills ("Confident", "Formal") summarizing how the writing reads
- **Pill buttons everywhere** — fully rounded (999pt) primary / secondary / premium
- **Accept-flash** — a faint 12% green wash (`#15C39A1F`) over the corrected span, fading over 600ms
- **Premium gold** (`#E0A82E`) — upsell only; never functional, never primary
- **No tint pill on tabs** — active tab goes green on icon + label, no Material-style pill
- **Calm motion** — 200–600ms ease-out, soft haptics, an odometer count roll

## Brand Sources

- [Grammarly](https://www.grammarly.com/)
- [Grammarly Brand](https://www.grammarly.com/brand) — Grammarly Green, the rotated-G logomark
- `Apercu` by Colophon Foundry (Grammarly's licensed brand grotesque) — substitute with [Inter by Rasmus Andersson](https://rsms.me/inter/) (SIL OFL) for free distribution
- Public brand palette: Grammarly Green `#15C39A` / `#11A683`, four category hues (`#E5484D` / `#3B82F6` / `#16A34A` / `#8B5CF6`), premium gold `#E0A82E`
