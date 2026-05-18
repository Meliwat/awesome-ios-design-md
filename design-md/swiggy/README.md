# Swiggy iOS Inspired Design System

Design system docs inspired by the [Swiggy iOS app](https://apps.apple.com/in/app/swiggy-food-instamart-dineout/id989540920). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Swiggy's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, restaurant card, dish row + floating ADD, rating chip, cart bar |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Swiggy Orange** (`#FC8019`) — the single hero accent: CTA, active tab, icons, footer; pressed `#E06D0C`
- **Photo-forward restaurant cards** — 16:10 hero image + `rgba(0,0,0,0.78)` bottom scrim + bold white offer text
- **Green rating chip** (`#48C479`) / amber (`#DB7C38`) — the trust signal users scan first, always a star pill left of the ETA
- **Sticky location header** — "Home ⌄" + delivery address, trailing circular avatar
- **Veg / non-veg marks** — green-dot-in-square `#48C479` / red-triangle-in-square `#E84A4A`, mandatory on every dish
- **Floating ADD button** — green-outline, shadowed, bottom-overlapping the square dish image; morphs to a `[ − n + ]` stepper
- **Dashed-divider footers** — free-delivery / coupon line in Swiggy Orange `#FC8019`
- **Filter chip row** — horizontally scrollable, 8pt-radius rounded rectangles, active filled
- **Floating orange cart bar** — pinned above the tab bar once items exist ("N items | ₹XXX  VIEW CART ›")
- **Multi-vertical tab bar** — Swiggy (food) / Instamart (grocery `#982C61`) / Dineout (table booking `#D7385E`) / Cart
- **Near-black dark canvas** `#121212` — food photography stays full-saturation; UI chrome recedes
- **Heavy grotesque type** — screen titles 32pt/800, restaurant names 18pt/800; weight carries hierarchy; uppercase CTAs

## Brand Sources

- [Swiggy](https://www.swiggy.com/)
- ProximaNova-lineage display face (Swiggy marketing & headings) — license required to bundle
- Basis-Grotesque-style UI face (body & metadata) — license required to bundle
- Catalog/preview fallback: [Plus Jakarta Sans (Google Fonts)](https://fonts.google.com/specimen/Plus+Jakarta+Sans) — SIL OFL
- Public brand palette: Swiggy Orange `#FC8019`, rating green `#48C479`, rating amber `#DB7C38`, Instamart `#982C61`, Dineout `#D7385E`
