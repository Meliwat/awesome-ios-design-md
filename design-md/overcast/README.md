# Overcast iOS Inspired Design System

Design system docs inspired by the [Overcast iOS app](https://apps.apple.com/us/app/overcast-podcast-player/id888422857) by Marco Arment. Not the official system. Brand colors, font names, and component patterns are cross-referenced with Overcast's public product UI and the developer's design posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, orange-ring play button, player + playlist rows |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, paper/dark scheme hook, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, core nav |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Warm paper-cream light theme** (`#FBFAF6` canvas, `#F7F5EF` surfaces) — NOT stark white
- **True dark theme** (`#121212` canvas, `#1C1C1E` surfaces)
- **Single Overcast Orange accent** (`#FC7E0F`) — identical across themes; used only for action
- **Orange-ring play button** — a 2.5pt outlined `#FC7E0F` ring, never a filled disc — the signature control
- **Smart Speed toggle** — dynamically removes silences with no pitch change; first-class on the player (ON track `#FC7E0F`)
- **Voice Boost toggle** — loudness + EQ tuned for spoken-word clarity; first-class on the player
- **"Saved X with Smart Speed" stat** — a deliberate badge of the feature's value, shown in-app
- **Skip buttons with interval labels** — chevron pair with "30" in `#6E6E6E` directly beneath
- **Playlists as the home surface** — smart + manual playlists with colored rounded-square (9pt) icons
- **Recommendation engine** — publicly recommend episodes; a feed of friends' recommendations
- **Tabular timecodes** — `48:12 / -1:22:40` use tabular figures so digits don't jitter
- **Flat, editorial chrome** — one accent, hairline dividers, generous row spacing, no gradients

## Brand Sources

- [Overcast](https://overcast.fm/)
- [Overcast on the App Store](https://apps.apple.com/us/app/overcast-podcast-player/id888422857)
- iOS system face `SF Pro` — Apple system font; `Inter` ([Rasmus Andersson](https://rsms.me/inter/), SIL OFL) as the web/spec fallback
- Public brand palette: Overcast Orange `#FC7E0F`, paper-cream canvas `#FBFAF6`, true dark `#121212`
