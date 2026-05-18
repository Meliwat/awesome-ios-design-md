# Threema iOS Inspired Design System

Design system docs inspired by the [Threema iOS app](https://apps.apple.com/us/app/threema-secure-messenger/id578665578). Not the official system. Brand colors, font conventions, and component patterns are cross-referenced with Threema's public product UI, App Store screenshots, and the company's security documentation; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, trust-level indicator, QR/verify, bubbles, tab bar |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, trust dots, QR scanner, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, trust dots, QR/verify, bottom bar + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Single brand color** — Threema Green (`#088A29`) for send, CTA, links, switches, active tab; brightened to `#1FA53C` on dark
- **Trust-level dots** — three dots colored by verification: red `#E5453A` (unknown) / orange `#EF8B2C` (server-matched) / green `#15A33A` (verified), on every contact
- **Anonymous Threema ID** — an 8-character random identity (e.g. `ECHOX9P2`) with **no phone number or email**, shown in monospace
- **QR verify ritual** — scan a contact's QR code in person to reach Level 3 (three green dots) — the product's emotional center
- **Pure white canvas** (`#FFFFFF`) light / charcoal (`#121212`) dark — calm, utilitarian, Swiss-engineered
- **Soft-green outgoing bubbles** (`#D6F0DC` light / `#0C5E22` dark) vs neutral-gray incoming (`#EBEBED` / `#262628`)
- **System font, no custom typeface** — SF Pro + Dynamic Type; monospace (SF Mono) reserved for identity strings
- **Red means destructive *and* untrusted** — `#E5453A` is shared by delete/block and Trust Level 1
- **Trust never color-only** — dots always paired with a text label and VoiceOver string
- **Flat, quiet UI** — translucent bars, hairline dividers, no decorative shadows; motion 120–300ms ease-out
- **Encrypted-everywhere affordances** — lock glyphs, "Verified" badges, key-fingerprint strings surfaced where trust matters

## Brand Sources

- [Threema](https://threema.ch/)
- [Threema Security & Trust Levels](https://threema.ch/en/faq/levels) — red / orange / green verification model
- Apple [SF Pro](https://developer.apple.com/fonts/) (system font) — Threema ships no custom typeface
- Public brand palette: Threema Green `#088A29`, trust red `#E5453A` / orange `#EF8B2C` / green `#15A33A`
