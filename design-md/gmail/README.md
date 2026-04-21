# Gmail iOS Inspired Design System

Design system docs inspired by the [Gmail iOS app](https://apps.apple.com/us/app/gmail-email-by-google/id422689480). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Google's Material You / Material 3 design system and Google brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics + Swipeable |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Compose FAB** — the red `#D93025` floating squircle (56×56, 16pt radius) bottom-right — Gmail's signature
- **Pure white canvas** (`#FFFFFF`) light / charcoal (`#202124`) dark — content is the color, not the chrome
- **Gmail red reserved** for the FAB and destructive actions only — everything else is calm
- **Google 4-color palette** (`#EA4335` red, `#FBBC04` yellow, `#34A853` green, `#4285F4` blue) for avatar fallbacks, chips, Smart Reply
- **Google Sans + Roboto** — structural type vs. body type, never mixed within a block
- **Unread = bold weight** on sender + subject — color alone is insufficient
- **Left-swipe archive / right-swipe delete** — user-configurable actions
- **Smart Reply chips** — 3 ML-suggested pill chips above the keyboard in thread view
- **Material You tab active indicator** — `#E8F0FE` pill background behind active icon
- **Layered shadows** following Material elevation model — FAB at Level 3, inbox rows flat

## Brand Sources

- [Google Material Design 3](https://m3.material.io/)
- [Google Brand Guidelines](https://about.google/brand-resource-center/)
- [Google Fonts — Google Sans & Roboto](https://fonts.google.com/)
- Public brand palette: Gmail Red `#D93025`, Google Red `#EA4335`, Yellow `#FBBC04`, Green `#34A853`, Blue `#4285F4`
