# Claude iOS Inspired Design System

Design system docs inspired by the [Claude iOS app](https://apps.apple.com/us/app/claude-by-anthropic/id6473753684) by Anthropic. Not the official system. Brand colors, typography (Tiempos + Styrene), and component patterns are cross-referenced with Anthropic's public brand site, Anthropic's product surfaces, and the in-app experience; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, asterisk-star `Shape`, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, SVG mark, Reanimated streaming |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, streaming orange cursor + Tiempos serif body + Haptics |
| `README.md` | This file — overview, signatures, sources |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Cream paper canvas** (`#F8F4ED`) — the Claude paper, a warm sun-touched off-white that defines the brand surface; explicitly NOT cold white
- **Claude Orange** (`#D97757`) — a desaturated terracotta, not a vivid tech orange; reserved for the send button, active model chip, asterisk-star logomark, streaming cursor, and links inside responses
- **Tiempos serif for assistant body** — the central typographic choice; Claude replies in Tiempos Text 16pt at 1.55 line-height while the user types in Styrene/Inter sans, a role-asymmetry no other AI app reproduces
- **Asterisk-star logomark** — the 6-point asterisk-meets-star symbol in Claude Orange, on every Claude message avatar; never any other color, never replaced with a generic AI sparkle
- **Streaming response cursor** — a small orange caret (`8pt × 18pt`, `#D97757`) that blinks at the trailing edge of Claude's text while generating; word-by-word streaming, never character-by-character
- **Warm dark code blocks** — `#1F1B16` warm dark canvas (slightly orange-undertoned), syntax highlighting in the brand palette: orange keywords, sage strings, gold numbers, periwinkle functions
- **No chat bubbles with tails** — user message sits in a soft pill on `#F0EAE0`; assistant message is a flat full-width type flow with a 16pt asterisk avatar leading; the conversation reads as a document, not a chat
- **Artifact card pattern** — when Claude generates code, a doc, or a chart, it surfaces as a card inline (iPhone) or in a side-pane (iPad) that opens to full-screen on tap
- **Warm-tinted shadows everywhere** — `rgba(40, 30, 20, x)` not `rgba(0, 0, 0, x)`, so elevation harmonizes with the cream surface
- **Ink `#2D2520`, never `#000000`** — warm near-black for primary text; the warmth is what makes the surface feel like print

## Brand Sources

- [Anthropic — Brand and design](https://www.anthropic.com/)
- [Klim Type Foundry — Tiempos](https://klim.co.nz/retail-fonts/tiempos-text/) — the proprietary serif used for Claude's assistant body
- [Klim Type Foundry — Styrene](https://klim.co.nz/retail-fonts/styrene-a/) — the proprietary geometric sans used for chrome and user messages
- [Claude on the App Store](https://apps.apple.com/us/app/claude-by-anthropic/id6473753684) — launched May 2024 (iOS)
- Public brand palette: Claude Orange `#D97757`, Cream Paper `#F8F4ED`, Ink `#2D2520`, warm dark canvas `#1F1B16`
