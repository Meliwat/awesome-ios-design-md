# Perplexity iOS Inspired Design System

Design system docs inspired by the [Perplexity iOS app](https://apps.apple.com/us/app/perplexity-ask-anything/id1668000334). Not the official system. Brand colors, typography (FK Grotesk + Inter), and component patterns are cross-referenced with Perplexity's public brand site and the in-app surface; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, brand mark `Shape`, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, SVG mark, Reanimated streaming |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, inline citation chips + source-card row + Haptics |
| `README.md` | This file — overview, signatures, sources |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Dark-first canvas** (`#0A0A0A`) — Perplexity defaults to near-black; light mode preserved but rarely the daily driver
- **Perplexity Teal** (`#20B8CD`) — a cool cyan-teal as the single signature accent for send buttons, focus rings, citation outlines, brand mark, and active states
- **Inline citation chips** — `[1]`, `[2]`, `[3]` rendered inline within answer prose using FK Grotesk Mono; the citation pattern is the brand
- **Source card row** above every answer — 200pt × 80pt cards in horizontal scroll, each with favicon + domain + 2-line title + number badge matching the inline citation
- **Triple-circle brand mark** — three overlapping teal circles in a triangular formation, evoking an autocomplete dropdown / Venn intersection; on splash, empty-state, and every answer avatar
- **Two-face typography** — FK Grotesk for chrome and brand voice; Inter for answer body; the encyclopedic register is intentional, the opposite of Claude's editorial-essay serif
- **Pro Search "Steps" card** — a collapsible Teal Soft (`#0F3A42`) card above the answer showing the multi-step reasoning trace with completed-check glyphs
- **Teal focus glow** on the search input — `rgba(32, 184, 205, 0.25)` 4pt halo; the only "elevated" visual moment in the app
- **Off-white `#FAFAFA` primary text** — never pure white; softer on dark canvas, more typographic
- **Searching-the-web indicator** — three pulsing teal dots with "Searching the web…" label between submit and source-card arrival

## Brand Sources

- [Perplexity AI](https://perplexity.ai/) — the answer engine
- [Perplexity on the App Store](https://apps.apple.com/us/app/perplexity-ask-anything/id1668000334)
- [FK Grotesk by Florian Karsten Type Studio](https://floriankarsten.com/) — the proprietary geometric humanist sans used for Perplexity's chrome and brand
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — the open geometric humanist sans used for Perplexity's answer body
- Public brand palette: Perplexity Teal `#20B8CD`, Canvas `#0A0A0A`, Off-white text `#FAFAFA`, Teal Soft `#0F3A42` (Pro Steps)
