# Marriott Bonvoy iOS Inspired Design System

Design system docs inspired by the [Marriott Bonvoy iOS app](https://apps.apple.com/us/app/marriott-bonvoy/id455004730). Not the official system. Brand colors, font conventions, and component patterns are cross-referenced with Marriott Bonvoy's public product UI and App Store screenshots; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, points panel, rate cards, hero, Mobile Key, tab bar |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, points panel, rate cards, hero, Mobile Key, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, points panel, rate cards, hero, bottom bar + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Deep Bonvoy navy base** (`#16264A`) / near-black (`#1C1C1C`) — premium, restrained, concierge-like; navy is constant across themes
- **Gold reserved for member value** (`#B3852A`, brightened to `#D2A23E` on dark) — points balances, elite tiers, redemption, the "Book" CTA; never decoration
- **Bonvoy points panel** — navy card with elite tier + points balance in 800-weight numerals + gold progress-to-free-night bar
- **Points-aware rate select** — Member / Points Redemption / Flexible / Prepaid cards; selected gets a gold border + 10% gold tint
- **Big hotel hero photography** with a gold letter-spaced small-caps sub-brand eyebrow (THE RITZ-CARLTON, ST. REGIS)
- **Mobile Key** — navy + gold card, "skip the front desk · tap to unlock · Room 1408"
- **Elite tier badges** — Silver / Gold / Platinum / Titanium / Ambassador as small-caps pills
- **Sticky booking bar** — nightly price + "Earn 2,180 Bonvoy points" + gold "Book" button
- **System font, no custom typeface** — SF Pro + Dynamic Type; eyebrows are uppercase, tightly tracked
- **Calm premium chrome** — navy panels use a hairline border (not a shadow), tight 8pt corners, photography-forward
- **Points count-up motion** — the points numeral animates up over 600ms; the loudest, most intentional moment

## Brand Sources

- [Marriott Bonvoy](https://www.marriott.com/loyalty.mi)
- Apple [SF Pro](https://developer.apple.com/fonts/) (system font) — Marriott Bonvoy ships no custom typeface
- Public brand palette: Bonvoy navy `#16264A`, near-black `#1C1C1C`, Bonvoy gold `#B3852A`
- Loyalty model: Silver / Gold / Platinum / Titanium / Ambassador Elite tiers; points + Mobile Key
