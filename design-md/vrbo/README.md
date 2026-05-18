# Vrbo iOS Inspired Design System

Design system docs inspired by the [Vrbo iOS app](https://apps.apple.com/us/app/vrbo-vacation-rentals/id336643851). Not the official system. Brand colors, font conventions, and component patterns are cross-referenced with Vrbo's public product UI and App Store screenshots; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, photo gallery, booking bar, listing cards, map pins, tab bar |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, photo gallery, booking bar, map pins, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, gallery pager, booking bar, map pins, bottom bar + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Full-bleed swipeable photo gallery** leads every listing (≈290pt) — photo counter "1 / 42" + widening progress dots
- **Sticky booking bar** — heavy price (`#15181D`, 800 weight) + dates + always-visible blue "Book now" CTA
- **Vrbo Blue** (`#245ABC`) primary action, brightened to `#4F8BF0` on dark for links/pins/outline; Sky Blue (`#1D6FB8`) support
- **One gold review star** (`#F2B01E`) — the only warm color, also the selected map pin; constant across themes
- **Whole-home framing** — "Entire home" / "Whole home" on every card and detail; never a shared room
- **Map price pins** — blue default, gray viewed, gold selected — with a slide-up peek card
- **Trip boards** — named saved-property collections as horizontal cards with a gold "Saved to" tag
- **Trust badges** — Premier Host (`#245ABC`) + Instant Book (`#1F9D57`) chips above the listing title
- **Bright white canvas** (`#FFFFFF`) light / cool blue-charcoal (`#101317`) dark — clean, trustworthy
- **System font, no custom typeface** — SF Pro + Dynamic Type; price is the loudest type on every screen
- **Calm, flat chrome** — translucent bars, hairline dividers, soft card lifts; photos never carry a shadow

## Brand Sources

- [Vrbo](https://www.vrbo.com/)
- Apple [SF Pro](https://developer.apple.com/fonts/) (system font) — Vrbo ships no custom typeface
- Public brand palette: Vrbo Blue `#245ABC`, Sky Blue `#1D6FB8`, gold review star `#F2B01E`
- Whole-home positioning: "Book the entire place — no shared rooms" (Vrbo brand promise)
