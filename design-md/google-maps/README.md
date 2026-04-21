# Google Maps iOS Inspired Design System

Design system docs inspired by the [Google Maps iOS app](https://apps.apple.com/us/app/google-maps/id585027354). Not the official system. Brand colors, font names, map palette, and component patterns are cross-referenced with Google's public Material Design documentation and Google Brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, MapKit renderer, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `react-native-maps`, Reanimated + Haptics |
| `preview.md` | Link to the interactive Spectr gallery |

## Signature Moves

- **Map is the hero** — 60-85% of every primary screen is full-bleed map
- **Google Blue** (`#4285F4`) as operating accent — route polyline, location dot, Directions FAB
- **Four-color pin system** — Red default, Green saved, Blue home/work, Orange category
- **Floating pill search bar** — translucent top search with mic and profile avatar
- **Bottom sheet drawer** with 3 detents (collapsed / medium / expanded) and a visible grabber
- **Category chip row** beneath search bar (Restaurants / Coffee / Gas / Hotels)
- **Turn-by-turn blue turn card** — 36pt Google Sans bold white on `#4285F4`
- **Accuracy halo** — `rgba(66,133,244,0.18)` circle around the location dot — the trust signal

## Brand Sources

- [Google Brand Center](https://about.google/brand-resource-center/)
- [Material Design — Maps color system](https://m3.material.io/)
- [Google Sans + Roboto typography](https://fonts.google.com/specimen/Roboto)
- Public logo palette: Blue `#4285F4`, Red `#EA4335`, Yellow `#FBBC04`, Green `#34A853`
