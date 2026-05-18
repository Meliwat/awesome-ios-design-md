# Kik iOS Inspired Design System

Design system docs inspired by the [Kik iOS app](https://apps.apple.com/us/app/kik/id357218860). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Kik's public product UI and brand assets (including the modern Blue rebrand and the legacy Green heritage); exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, bubble + S/D/R receipt + Kik Code, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, bubble + S/D/R receipt + Kik Code + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Username-first identity** — `@handles`, no phone number ever surfaced; find via username, group code, or Kik Code
- **Modern Kik Blue is the brand** — `#00B0F0` on the outgoing bubble, buttons, tab selection, Kik Code plate
- **Heritage Kik Green** — the legacy `#82BC23` lime survives only as a heritage/legacy accent, never the default CTA
- **Dark-cyan ink on Blue** — outgoing bubble/button text is `#002A36` (WCAG AA on bright `#00B0F0`), never white
- **Neutral near-black dark mode** — canvas `#121316` is a true neutral, NOT color-cast, so Blue stays vivid
- **Gray/blue bubble pair** — outgoing Kik Blue, incoming gray `#E9EAEC` (light) / `#25282D` (dark), ~16pt radius with a ~5pt tail
- **S / D / R receipt** — a single letter: S (sent, gray), D (delivered, gray), R (read, Kik Blue) — swapped instantly, never animated
- **Bots are first-class** — rounded-square avatar + "BOT" tag break the circular-avatar rhythm; a Discover/Bot tab
- **Kik Codes** — flat high-contrast Blue rounded-square plates with a white dotted ring, for add-by-scan
- **No call/video chrome** — Kik is text + bot + Discover-first; the People tab is a username directory
- **Text "Send" label** — the input uses a Kik-Blue text "Send" affordance, not a circular icon button
- **Clean youthful type** — Kik brand sans (Inter / Open Sans substitute), heavy 700/800 titles & username headlines

## Brand Sources

- [Kik (Kik Interactive / MediaLab)](https://www.kik.com/)
- [Kik on the App Store](https://apps.apple.com/us/app/kik/id357218860)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (free Kik-brand substitute); [Open Sans](https://fonts.google.com/specimen/Open+Sans) — SIL OFL alternative
- Public brand palette: modern Kik Blue `#00B0F0`, deep `#0093C8`, cyan `#4FCBF7`, heritage Kik Green `#82BC23`
