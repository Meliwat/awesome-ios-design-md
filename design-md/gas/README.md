# Gas iOS Inspired Design System

Design system docs inspired by the [Gas iOS app](https://apps.apple.com/us/app/gas-positivity-for-friends/id1637364946) (Gas — anonymous compliments for schools). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Gas's public product UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, brand & choice gradients, poll card, flame pill |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, poll card, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, poll loop screen |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Full-bleed indigo→purple gradient world** (`#6C5CE7 → #A06CFF`) — Gas is light-UI by design; no dark mode in spirit
- **Big friendly white poll card** (`#FFFFFF`, 28pt radius, soft purple-tinted shadow) floating centered — one card at a time
- **Anonymous compliment poll** — 50pt emoji + bold centered question + four colorful gradient name buttons in a strict 2×2 grid
- **Positive-only questions** — every poll is a compliment, tagged "be nice"; no negativity, fully anonymous
- **Fixed festive choice palette** — indigo (`#6C5CE7`) / pink (`#FF6CC8`) / yellow (`#FFC93C`) / green (`#2BC48A`), rotating by slot
- **Flame 🔥 currency** (`#FF7A45`) — earned by voting, spent to unlock "who picked you" hints; the dopamine loop
- **Heavy rounded Nunito type** (800–900) — chunky, warm, middle-school-friendly, anti-corporate
- **Purple-plum tinted shadows** (`rgba(40,20,90,…)`) — never neutral gray; depth feels candy-bright
- **Springy, celebratory motion** — cards bounce in/out, flames count up, confetti on milestones
- **Finite & centered** — no feed, no infinite scroll, no public profiles; phone-shaped even on iPad

## Brand Sources

- [Gas — Positivity for friends](https://apps.apple.com/us/app/gas-positivity-for-friends/id1637364946)
- [Nunito (Google Fonts)](https://fonts.google.com/specimen/Nunito) — SIL OFL (closest free face to Gas's rounded, friendly type)
- Public brand palette: indigo→purple gradient `#6C5CE7 → #A06CFF`, flame `#FF7A45`, festive choice colors
