# OpenTable iOS Inspired Design System

Design system docs inspired by the [OpenTable iOS app](https://apps.apple.com/us/app/opentable-restaurant-deals/id296581815). Not the official system. Brand colors, font names, and component patterns are cross-referenced with OpenTable's public product UI, marketing site, and the OpenTable Dining Rewards / Points program; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, slot-grid booking spine + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Bright white canvas** (`#FFFFFF`) light / clean neutral charcoal (`#121212`) dark — minimal chrome
- **A single disciplined brand color** — OpenTable Red `#DA3743`, brightening to `#F2545B` on dark/emphasis, for Reserve, selected slot, active tab
- **Reservation time-slot grid** — rounded 10pt chips (`6:45` / `7:00` / `7:15`) with the recommended slot filled solid red — the product's heartbeat
- **Restaurant detail page** — hero photo + heavy name + gold stars + social-proof pill + slot grid + sticky Reserve bar
- **Gold review star** `#E8A33D` reserved for ratings only
- **Teal Points accent** `#1F8A8A` reserved for OpenTable Dining Rewards; 1,000-point off-peak slots flagged "+1,000"
- **Diner green** `#2FA86A` for availability + "Booked N times today" social proof
- **Sticky red "Reserve for {time}" bar** pinned to the bottom of the detail page
- **One grotesque sans** (OpenTable Sans / Inter) with heavy `#1A1A1C` restaurant names and CTAs
- **No decorative color** — every hue carries a function (book / rate / earn / available)
- **Fully-rounded pills** (999pt) for buttons, filters, search bar, points/party/date chips
- **5-tab bottom bar** — Discover / Saved / Reservations / Rewards / Profile, active in brand red, no Material pill

## Brand Sources

- [OpenTable](https://www.opentable.com/)
- [OpenTable Dining Rewards](https://www.opentable.com/loyalty) — points / loyalty program
- OpenTable Sans (OpenTable's custom grotesque sans, brand face)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL, closest free analog and recommended substitute
- Public brand palette: OpenTable Red `#DA3743` / `#F2545B`, gold review star `#E8A33D`, teal Points `#1F8A8A`
