# Apple Wallet iOS Inspired Design System

Design system docs inspired by the [Apple Wallet iOS app](https://apps.apple.com/us/app/wallet/id1160481993). Not the official system. Color values for the Apple Card titanium gradient, card-frame radii, and stack physics are reverse-engineered from public Apple HIG documentation, WWDC sessions on PassKit and Apple Pay, and visual inspection of the shipping app; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, card stack, Apple Card face, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated card stack |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, card-stack physics + Apple Pay handoff + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Signature Moves

- **True black canvas** (`#000000`) — not system dark gray — so the cards float
- **Card stack with 80pt peek** — vertical column of cards aligned on a vanishing-point spine, each card overlapping the one beneath
- **10pt card radius, every card type** — the universal Wallet card frame; never larger, never smaller
- **Apple Card titanium gradient** — `#E8E8EB` → `#A8A8AD` → `#3D3D3F` with gold chip and red Daily Cash chip — the signature object
- **Heavy shadows** (`rgba(0,0,0,0.5)` at 24pt blur) — required to lift cards off true black
- **No tab bar** — the stack is the entire navigation; only the "Wallet" title and a 32pt "+" button live at the top
- **PassKit boundary** — Wallet owns the frame, radius, shadow, and stack physics; each issuer owns the card face artwork
- **SF Pro Display ≥20pt / SF Pro Text ≤17pt** — Apple's documented switchover, applied rigidly
- **Card expand physics** — `.spring(response: 0.5, dampingFraction: 0.8)`: bouncy but contained

## Brand Sources

- [Apple Human Interface Guidelines — Wallet](https://developer.apple.com/design/human-interface-guidelines/wallet)
- [PassKit framework documentation](https://developer.apple.com/documentation/passkit)
- [WWDC 2019 — Designing Great Apple Pay Experiences](https://developer.apple.com/videos/play/wwdc2019/704/)
- Public brand palette: True Black `#000000` (canvas), Titanium gradient `#E8E8EB` → `#3D3D3F` (Apple Card), Chip Gold `#C7AC73`, Daily Cash Red `#FF3B30`, System Blue `#0A84FF` (HIG dark mode tint)
