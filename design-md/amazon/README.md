# Amazon iOS Inspired Design System

Design system docs inspired by the [Amazon iOS app](https://apps.apple.com/us/app/amazon-shopping/id297606951). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Amazon's public brand documentation and the Amazon Style Guide (brand.amazon.com); exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, top nav, CTA buttons, PDP price block, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, top nav, CTA buttons, Reanimated + Haptics |
| `preview.md` | Link to the interactive Spectr gallery |

## Signature Moves

- **Amazon Yellow** (`#FF9900`) on every primary CTA — Add to Cart, Buy Now, Search button
- **Black text on yellow buttons** (`#0F1111` on `#FF9900`) — the iconic Amazon contrast
- **Deep Navy** (`#131921`) top nav bar with bright search bar and yellow Search button
- **Amazon Ember font** at weights 400/500/700 — engineered for e-commerce density
- **Superscript cents** on PDP hero prices — `$28` with `99` as 14pt superscript
- **Prime wordmark** in teal-blue (`#00A8E1`) inline with product delivery lines
- **Gold star ratings** (`#FFA41C`) with teal-blue review-count links
- **Price Red** (`#B12704`) for strike-throughs and lightning-deal countdown banners
- **"FREE Delivery by [date]"** in green on every eligible product — the delivery promise as UI

## Brand Sources

- [Amazon Style Guide — brand.amazon.com](https://brand.amazon.com/)
- [Amazon Ember typography documentation](https://developer.amazon.com/docs/styleguide/typography)
- Public brand palette: Amazon Yellow `#FF9900`, Deep Navy `#131921`, Prime Sky `#00A8E1`
