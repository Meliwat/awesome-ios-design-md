# Duolingo iOS Inspired Design System

Design system docs inspired by the [Duolingo iOS app](https://apps.apple.com/us/app/duolingo-language-lessons/id570060128). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Duolingo's public brand materials (duolingo.design, press kit) and observed UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics, `expo-router` Tabs |
| `preview.html` | Interactive design token catalog (light) |
| `preview-dark.html` | Interactive design token catalog (dark, blue-tinted navy) |

## Signature Moves

- **Snow white canvas** (`#FFFFFF`) with saturated branded accents — the opposite of muted
- **Feather Green** (`#58CC02`) as the primary verb — CTAs, correct answers, completed skill nodes
- **The 3D "slab" button** — every primary interactive sits on a 4pt darker-tone ledge that collapses on press
- **Duo the owl** as the emotional anchor — empty states, celebrations, streak saves, passive-aggressive nudges
- **The curved learning path** — vertically scrolling wave of circular skill nodes offset ±60pt left-right
- **Gamified HUD** — streak flame (`#FF9600`), gems (`#1CB0F6`), hearts (`#FF4B4B`) always visible on Home
- **Celebration takeovers** — full-bleed green screen, Duo cheering, confetti particles, haptic + cheer sound
- **Feather Bold + DIN Next Rounded Pro** — chunky headlines for numbers and emotion, rounded sans for UI
- **Uppercase tracked button labels** — "CONTINUE", "CHECK", "GOT IT" — always 800 weight, 0.4pt tracking
- **Blue-tinted dark mode** (`#131F24`) — not true black; accent colors stay bright against the navy

## Brand Sources

- [Duolingo Design](https://www.duolingo.design/)
- [Duolingo Press Kit](https://blog.duolingo.com/press/)
- [Feather Bold by Johnson Banks](https://www.johnsonbanks.co.uk/thoughts/duolingo-feather-bold) — typeface credit, 2019
- Public brand palette: Feather Green `#58CC02`, Cardinal Red `#FF4B4B`, Fox Orange `#FF9600`, Bee Yellow `#FFC800`, Macaw Blue `#1CB0F6`, Beetle Purple `#CE82FF`
