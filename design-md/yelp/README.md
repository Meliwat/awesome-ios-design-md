# Yelp iOS Inspired Design System

Design system docs inspired by the [Yelp iOS app](https://apps.apple.com/us/app/yelp-food-delivery-reviews/id284910350). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Yelp's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, half-star `RatingView`, review card, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, half-star rating, review card, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, Canvas-drawn star bar, review card + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **One rationed brand color** — Yelp Red `#FF1A1A` (logo `#D32323`, pressed `#B81E1E`) for the primary CTA, active tab, and logomark only
- **The 5-star rating is a brand asset** — warm orange-red filled star `#F25C05` (identical in light and dark), gray empty (`#E3E3E0` light / `#3A3A3A` dark), half-star increments
- **Business detail as signature screen** — full-bleed photo header with a "1 / 248 photos" counter, then name + stars + reviews
- **Recommended Reviews cards** — circular avatar, reviewer identity, stars, date, body, and Useful / Funny / Cool votes that turn red when tapped
- **Section bands, not cards** — 8pt gray `#F0F0F0` (light) / `#0C0C0C` (dark) bands chunk the long detail scroll
- **Open / Closed status** in green `#2DA44E` / red `#E03E3E`; the `$ $$ $$$ $$$$` price band is gray metadata, never colored
- **Category filter chips** — pills with a red glyph; the selected chip is a solid red fill
- **Open Sans typography** — heavy headers/numbers (700-800), 400 body, 600 meta — scanning over reading
- **Pure white canvas** `#FFFFFF` light / near-black `#161616` dark — content and photos forward
- **Links** `#0073BB` light → `#4FA3D9` dark; business photos and avatars stay full-color in both modes
- **Always pair a rating with its review count** — "4.3 · 3,812 reviews"; a bare star rating is untrustworthy in Yelp's model
- **Bottom tab bar** — Search, Nearby, Write (center, red), Activity, Me

## Brand Sources

- [Yelp](https://www.yelp.com/)
- [Yelp Brand & Logo](https://brand.yelp.com/)
- [Open Sans (Google Fonts)](https://fonts.google.com/specimen/Open+Sans) — SIL OFL
- Public brand palette: Yelp Red `#FF1A1A` / `#D32323`, warm rating star `#F25C05`, link blue `#0073BB`
