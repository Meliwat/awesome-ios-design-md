# WhatsApp iOS Inspired Design System

Design system docs inspired by the [WhatsApp iOS app](https://apps.apple.com/us/app/whatsapp-messenger/id310633997). Not the official system. Brand colors, typography conventions, and component patterns are cross-referenced with WhatsApp's public brand resources and Meta's broader design documentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.md` | Link to the interactive Spectr gallery preview |

## Signature Moves

- **WhatsApp Green** (`#25D366`) reserved for send, answer-call, record, status ring, and the read-tick green
- **Mint-leaf outgoing bubble** (`#D9FDD3` light / `#005C4B` dark) — the single most iconic pixel in the app
- **Asymmetric bubble tails** — 12pt radius on all corners except one pointed at the sender
- **Blue double checks** (`#53BDEB`) — universally understood read receipt
- **Voice waveform bubble** with playback-synced fill — the defining interactive component
- **Native SF Pro + SF Symbols** everywhere; no custom fonts
- **Doodle wallpaper** as chat canvas — cream (`#ECE5DD`) light, deep (`#0B141A`) dark
- **Tabbed bottom nav** (Updates / Calls / Communities / Chats / Settings) as of iOS 2024+
- **End-to-end encryption banner** as the first message in every new chat

## Brand Sources

- [WhatsApp Brand Resources](https://faq.whatsapp.com/general/brand-and-company-logos)
- Public brand palette: WhatsApp Green `#25D366`, Teal `#075E54`, outgoing bubble `#D9FDD3`
- [Meta Design](https://design.meta.com) for broader typography and accessibility conventions
