# Headspace iOS Inspired Design System

Design system docs inspired by the [Headspace iOS app](https://apps.apple.com/us/app/headspace-sleep-meditation/id493145008). Not the official system. Brand colors (Marigold Orange, Butter Yellow, Sage Moss), Apercu typography, and the breathing-sphere meditation hero are cross-referenced with Headspace's public brand identity work, their 2017 rebrand by Wieden+Kennedy / Headspace Studios, and visual inspection of the shipping app; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, breathing sphere, Aurora gradient, streak ring, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated breath cycle + streak ring |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Butter Cream canvas** (`#FFF7E7`) — warm, off-white, never pure; the visual equivalent of morning light
- **Marigold Orange** (`#FF6B35`) — primary action color: Start, Continue, the meditation sphere highlight
- **Aurora gradient** (`#FFB89E` → `#FFD25C` → `#FF6B35`) — the signature dawn wash on Today hero and meditation full-bleed cards
- **Breathing meditation sphere** — radial-gradient circle, 60% of screen, scales 1.0 → 1.04 → 0.96 on a 12-second 4-2-4-2 breath cycle
- **Illustration companions** — round-headed characters in pastel hoodies on every empty state and milestone; never photography
- **Apercu typography** (Nunito Google fallback) at weights 400/500/600/700 — title case only, never UPPERCASE
- **24pt soft corner radius** on meditation cards — Headspace's signature comfortable corner
- **Ink Brown text** (`#2E1A47`) — warm purple-brown body text, never harsh black
- **Sage Moss Sleep tab** (`#7E9F4B`) — the only tab with its own tint; Sleep is its own world
- **Dusk canvas mode** (`#1A1430`) — warm purple-blue dark mode for Sleep, never neutral dark gray
- **Soft haptics only** — `.impactOccurred(.soft)` and `.selectionChanged()`, never heavy

## Brand Sources

- [Headspace Press Kit](https://www.headspace.com/press) — public brand assets
- [Colophon Foundry — Apercu](https://www.colophon-foundry.org/custom-fonts/apercu) — typography license
- [Headspace Studios — Design Stories](https://www.headspace.com/headspace-studios) — illustration style notes
- Wieden+Kennedy 2017 rebrand work — Headspace's color and illustration system
- Public brand palette: Marigold `#FF6B35`, Butter `#FFD25C`, Sage Moss `#7E9F4B`, Ink Brown `#2E1A47`, Butter Cream canvas `#FFF7E7`
