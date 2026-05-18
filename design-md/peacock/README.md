# Peacock iOS Inspired Design System

Design system docs inspired by the [Peacock TV iOS app](https://apps.apple.com/us/app/peacock-tv-stream-tv-movies/id1508186374). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Peacock's public product UI and NBCUniversal's brand identity; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, hero billboard + channels rail + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Permanently dark** — indigo-black canvas `#0A0A14` warming to violet `#140E26` under hero art; no light mode
- **Full-bleed hero billboard** — one featured title, centered logo lockup, colored eyebrow, white Play button
- **Five-color feather swoosh** — yellow `#FACC15` → orange `#F97316` → pink `#EC4899` → purple `#8B5CF6` → blue `#2563EB`
- **White primary CTA** — "Play" is solid `#FFFFFF` with a near-black `#0A0A14` label, never brand-tinted
- **Channels rail** — rounded live-network tiles (NBC, Bravo, USA, E!, SNL) sit high on Home
- **Content rows** — tight free-scroll 2:3 poster strips that bleed off the trailing edge; Top 10 shows rank numerals
- **Live / sports red** `#E5142B` with a pulsing dot — the only saturated non-feather color
- **Greyscale chrome** — white `#FFFFFF` / `#B5B2C2` / `#7A7689` so full-color art is the loudest thing on screen
- **Broadcast-promo typography** — Poppins (Peacock Sans fallback), hero 800, 11pt uppercase tracked eyebrows
- **Feather-gradient upsell** — "Get Premium" is the one place the 5-stop gradient is a button fill
- **No tint pill** — the active tab is just a white icon fill
- **Cinematic, quiet motion** — 120-400ms ease-out; hero cross-fades, live dot pulses on a 1.2s loop

## Brand Sources

- [Peacock TV](https://www.peacocktv.com/)
- [NBCUniversal brand](https://www.nbcuniversal.com/)
- [Poppins (Google Fonts)](https://fonts.google.com/specimen/Poppins) — SIL OFL (closest substitute for proprietary Peacock Sans)
- Public brand palette: feather gradient `#FACC15 / #F97316 / #EC4899 / #8B5CF6 / #2563EB`, indigo canvas `#0A0A14`, NBC live red `#E5142B`
