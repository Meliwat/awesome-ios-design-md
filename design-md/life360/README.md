# Life360 iOS Inspired Design System

Design system docs inspired by the [Life360 iOS app](https://apps.apple.com/us/app/life360-find-family-friends/id384830320). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Life360's public product UI and brand presence; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, family map + member sheet |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Map-first home screen** — full-bleed dark-styled map, no feed or list before it
- **Member identity color** — every person has a FIXED hue (amber `#F2A33C`, teal `#2DD4BF`, pink `#F472B6`, blue `#60A5FA`) on pin + avatar + trail + reports, everywhere
- **Life360 Purple** `#582C83` / `#7E57C2` — the brand spine (Circle UI, Places, CTAs); never a member color
- **Member pins** — circular photo in a 3pt white ring with a downward pointer + soft shadow
- **Circle selector pill** — top-floating "The Carters ▾"; families are organized into Circles
- **Draggable member sheet** — name + place + "X min ago" + battery per member
- **Battery on every row** — green `#34C759` healthy, red `#FF6B6B` low (a core safety signal)
- **Dark-styled map** — base `#1A2138`, muted roads/parks/water so colored pins stay salient
- **Places & geofences** — translucent purple circles with place-emoji tags; arrive/leave alerts
- **Safety colors rationed** — Safe green `#34C759`, SOS/Alert red `#FF6B6B`, red only for real events

## Brand Sources

- [Life360](https://www.life360.com/)
- [Life360 Press & Brand](https://www.life360.com/press/) — purple brand presence, the location-pin mark
- [Plus Jakarta Sans](https://fonts.google.com/specimen/Plus+Jakarta+Sans) — SIL OFL (friendly rounded-humanist analog)
- Public brand palette: Life360 Purple `#582C83` / `#7E57C2`; per-member identity colors; Safe green `#34C759`; SOS red `#FF6B6B`
