# Clubhouse iOS Inspired Design System

Design system docs inspired by the [Clubhouse iOS app](https://apps.apple.com/us/app/clubhouse-talk-anywhere/id1503133294). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Clubhouse's public product UI and brand materials; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / serif+sans `Font` extensions, `ViewModifier`s, speaking-pulse avatar + room stage + raise-hand bar |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, serif/sans pairing, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, speaking-pulse avatar + room stage + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Cream "hallway" canvas** (`#F2EFE4`) light / warm near-black "room" (`#1A1A1A`) dark — paper, never white or slate
- **Serif display titles + sans UI** — DM Serif Display for every topic/greeting, Inter for every name/role/button
- **Circular illustrated avatars everywhere** — the product is a stage of faces, not a feed of cards
- **Emerald speaking ring** (`#38B569`) — a 3pt ring + a breathing `rgba(56,181,105,0.18)` halo = "who has the floor"
- **Live Room** — a grid of speaker circles grouped Speakers / Followed by speakers / Others
- **"✋ Raise hand" + "✌️ Leave quietly"** — emoji-led pill controls with a calm, human voice
- **Everything is a pill** — buttons, status badges, search fields; Clubhouse has almost no rectangles
- **Host 🎙️ pin + moderator gold (`#E9C46A`) + muted 55% dim** — role state read at a glance
- **Warm gradient avatar palette** — peach `#F4C77B→#E0A24B`, sage `#9FD8B4→#5FB484`, lavender, coral, sky carry the screen's color
- **No blue, no cold gray** — the entire system is warm, including dark mode and the warm scrim `rgba(20,18,16,0.5)`
- **Soft depth** — barely-there card shadows + the emerald glow halo, not heavy drop-shadows
- **The Hallway** — a scrollable list of live & upcoming rooms with stacked overlapping avatar previews

## Brand Sources

- [Clubhouse](https://www.clubhouse.com/)
- [Clubhouse on the App Store](https://apps.apple.com/us/app/clubhouse-talk-anywhere/id1503133294)
- [DM Serif Display (Google Fonts)](https://fonts.google.com/specimen/DM+Serif+Display) — SIL OFL (serif-title substitute)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (UI sans)
- Public brand palette: cream hallway `#F2EFE4`, speaking emerald `#38B569`, moderator gold `#E9C46A`, warm dark room `#1A1A1A`
