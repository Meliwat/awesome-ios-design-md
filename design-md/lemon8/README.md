# Lemon8 iOS Inspired Design System

Design system docs inspired by the [Lemon8 iOS app](https://apps.apple.com/us/app/lemon8-lifestyle-community/id1502892699). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Lemon8's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, masonry feed, lifestyle card, pastel tags |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, two-column masonry, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, staggered-grid masonry + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Bright editorial white canvas** (`#FFFFFF` light / `#121212` dark) — a digital lifestyle magazine, light-first
- **Two-column staggered masonry feed** — varied card heights, 10pt column gap, 12pt vertical gap, no single-column
- **Lifestyle cards as mini magazine covers** — tall photo + punchy 2-line title + pastel tags + creator byline + like count
- **Lemon8 Yellow used sparingly** (`#FFE600`) — post FAB, active tab underline, primary CTA — ALWAYS with ink `#1A1A1A` text, never white-on-yellow
- **Pastel topic-tag palette** — mint (`#E4F5EC`/`#1F8A52`), blush (`#FCE8EE`/`#C13E68`), sky (`#E6F0FB`/`#2D6FB8`), butter (`#FFF6CC`/`#8A7300`)
- **Soft two-layer card elevation** — `0 1px 3px` + `0 4px 12px` at very low opacity, cards float gently off the white
- **Post detail as a magazine article** — photo carousel + Poppins subheadings + embedded full-width photos
- **Poppins display headlines** + **Inter UI/body** — never swap them
- **Top segmented tabs** — Following / For You / Nearby with a small yellow underline
- **Yellow rounded-rectangle post FAB** in the center of the bottom tab bar — not a plain icon
- **Photo-overlay chips** ("5 photos", "Guide") in translucent dark, bottom-left of covers
- **Creator-centric** — bylines, Follow pills, and like counts on every card

## Brand Sources

- [Lemon8](https://www.lemon8-app.com/)
- [Lemon8 brand: Lemon8 Yellow `#FFE600` + ink `#1A1A1A`](https://www.lemon8-app.com/)
- [Poppins (Google Fonts)](https://fonts.google.com/specimen/Poppins) — SIL OFL
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: Lemon8 Yellow `#FFE600`, ink `#1A1A1A`, pastel topic tags (mint/blush/sky/butter)
