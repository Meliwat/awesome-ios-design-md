# Hinge iOS Inspired Design System

Design system docs inspired by the [Hinge iOS app](https://apps.apple.com/us/app/hinge-dating-app/id599247134). Not the official system. Brand colors, typography (Sailec), and component patterns are cross-referenced with Hinge's public brand site, marketing materials, and the in-app surface; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, components, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, 44pt heart-tap on reactive surfaces + Haptics |
| `README.md` | This file — overview, signatures, sources |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **Cream paper canvas** (`#FDF8F2`) — warm off-white that rejects the cold purity of standard tech apps; reads as printed journal paper
- **Hinge Black** (`#1A1A1A`) — warm near-black for type, the iconic "H" mark, and primary CTAs; never pure `#000000`
- **One sacred gold** (`#E8A04D`) — Rose Gold reserved exclusively for the Standouts star, the Roses currency icon, and the Send-a-Rose premium CTA
- **Prompt cards as hero** — full-width Paper White cards with the user's answer in Sailec 24pt Bold, sized larger than supporting photos; the words are louder than the face
- **Heart-tap on every reactive surface** — a 44pt circle with a filled heart, placed bottom-right with 12pt inset, on every prompt and photo; tapping opens a comment sheet pinned to that specific moment
- **Vertical-scroll profiles, never a card stack** — the architecture is the brand; advancement is read-and-tap, not flick-and-judge
- **Match celebration with Rose Gold confetti** — 1800ms particle fall paired with `notificationOccurred(.success)`, then a soft photo-pulse on the two avatars
- **Sailec for display + prompts; Inter for chrome** — proprietary geometric humanist sans for personal voice, neutral grotesque for tabular UI
- **Warm-tinted shadows** — every elevation uses `rgba(28, 20, 10, x)` not `rgba(0, 0, 0, x)`, so the lift feels like paper on paper

## Brand Sources

- [Hinge — Designed to be deleted](https://hinge.co/)
- [Hinge Brand & Press](https://hinge.co/press)
- [Sailec by Type Dynamic](https://typedynamic.com/) — the proprietary geometric sans used in Hinge marketing and product
- Public brand palette: Hinge Black `#1A1A1A`, Rose Gold `#E8A04D` (Standouts/Roses), Cream `#FDF8F2`, Paper White `#FAF6F0`, Surface Sand `#F2EBE0`
