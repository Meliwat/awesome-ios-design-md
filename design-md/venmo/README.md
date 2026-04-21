# Venmo iOS Inspired Design System

Design system docs inspired by the [Venmo iOS app](https://apps.apple.com/us/app/venmo/id351727428). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Venmo's public brand materials and the PayPal/Venmo press kit; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, feed row, Pay/Request split pill, balance card, animated checkmark |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, Reanimated, SVG checkmark, `expo-haptics` |
| `preview.md` | Link to the interactive Spectr gallery |

## Signature Moves

- **Social transactions feed** — Instagram-style rows with like/comment on every payment
- **Emoji-rich memos** — `🍕🍺 Pizza Friday` as the primary "what this is for" signal
- **Venmo Blue** (`#008CFF`) as the singular brand color — saturated, friendly (not banking-navy)
- **Pay / Request split pill** — blue, 50pt tall, pinned above the tab bar, the defining CTA
- **Mixed-weight feed line** — actor bold, verb regular, recipient bold, timestamp gray
- **Balance Display** — Venmo Display 56pt bold on a blue gradient card on the Me tab
- **Amounts never shown on public feed** — privacy model, only to the two parties
- **Payment-sent checkmark animation** — animated stroke draw + confetti burst + success haptic
- **QR code pay/scan flow** — blue-cornered viewfinder, personal QR with Venmo logo center

## Brand Sources

- [Venmo Newsroom — Brand assets](https://newsroom.venmo.com/media-kits)
- [PayPal Design Language (parent)](https://www.paypal.com/us/webapps/mpp/about/brand-guidelines)
- Public brand palette: Venmo Blue `#008CFF`, Deep Blue `#0078DE`
- Venmo Sans (internal) + Brown Std (legacy) typography
