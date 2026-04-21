# LinkedIn iOS Inspired Design System

Design system docs inspired by the [LinkedIn iOS app](https://apps.apple.com/us/app/linkedin-network-job-finder/id288429040). Not the official system. Brand colors, font names, and component patterns are cross-referenced with LinkedIn's public brand guidelines and its Artdeco / Mercado design systems; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Cream canvas** (`#F3F2EF`) — NOT white — with white post cards floating above
- **LinkedIn Blue** (`#0A66C2`) reserved for links, Connect/Follow, active states — the verb color
- **Six custom reaction icons** — Like / Celebrate / Support / Love / Insightful / Funny on long-press
- **Avatar status rings** — green (`#057642`) for Open-to-Work, gold gradient frame for Premium
- **Full-bleed feed cards on iPhone** — 0pt radius, 8pt gap, canvas cream separates them
- **SF Pro** at 12-24pt, weights 400 / 600 / 700 — dense text-first hierarchy
- **Connection degree** ("• 1st", "• 2nd", "• 3rd+") inline after every name in the feed
- **Bottom tab bar** — Home, My Network, Post (+), Notifications, Jobs
- **Underlined hyperlinks** on @mentions, #hashtags, inline URLs — old-web professional signal
- **Dark mode charcoal** (`#1B1F23`) — NOT true black; blue shifts to `#70B5F9` for contrast

## Brand Sources

- [LinkedIn Brand Guidelines](https://brand.linkedin.com/)
- [LinkedIn Artdeco Design System](https://www.linkedin.com/design/artdeco)
- [LinkedIn Engineering — Mercado Design System](https://engineering.linkedin.com/)
- Public brand palette: LinkedIn Blue `#0A66C2`, Open-to-Work Green `#057642`, Premium Gold `#915907`
