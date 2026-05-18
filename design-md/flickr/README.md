# Flickr iOS Inspired Design System

Design system docs inspired by the [Flickr iOS app](https://apps.apple.com/us/app/flickr/id328407587). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Flickr's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, justified mosaic, EXIF table, fave-star animation |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, justified packing, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, custom mosaic layout + EXIF table + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Near-black gallery canvas** (`#0C0D0E` dark / `#FFFFFF` light) — photography reads first, chrome recedes
- **Justified photo mosaic** — row-packed full-bleed grid preserving native aspect ratios with 3pt gutters, no cropping
- **EXIF / camera detail table** — Camera / Lens / Exposure / Aperture / ISO / Focal length key-value rows, values in monospace
- **Twin brand dots** — Flickr Pink (`#FF0084`) for favorites & primary, Flickr Blue (`#0063DC`) for links & secondary
- **Five-point favorite star** — fills `#FF0084` when active (NOT a heart), with fave-count overlay on a `rgba(0,0,0,0.45)` scrim
- **Proza Libre serif** for titles/headers + **Inter sans** for all functional UI + monospace EXIF readouts
- **Photo detail page** — full-width image + title + author row + action bar + EXIF table + tags + comments
- **Camera & lens chips** — tappable, link to "more photos shot with this gear"
- **Pro gold star** (`#FFB200`) — appears only on Pro-account badges, never as a general accent
- **Photos never dim** across themes — only chrome, dividers, and EXIF tables invert
- **Translucent blurred chrome** — nav and tab bars float invisibly over the photography
- **Center camera tab** — emphasized 27pt camera glyph in `#FF0084`, FAB-like, no label

## Brand Sources

- [Flickr](https://www.flickr.com/)
- [Flickr brand: Pink `#FF0084` + Blue `#0063DC`](https://www.flickr.com/)
- [Proza Libre (Google Fonts)](https://fonts.google.com/specimen/Proza+Libre) — SIL OFL
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- [IBM Plex Mono](https://www.ibm.com/plex/) — SIL OFL
- Public brand palette: Flickr Pink `#FF0084`, Flickr Blue `#0063DC`, Pro gold `#FFB200`
