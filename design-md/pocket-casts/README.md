# Pocket Casts iOS Inspired Design System

Design system docs inspired by the [Pocket Casts iOS app](https://apps.apple.com/us/app/pocket-casts/id414834813). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Pocket Casts' public product UI and the company's design posts; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, per-podcast tint env, player + episode list |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, theme-tint context, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, Palette tint extraction, composables |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Near-black canvas** (`#1A1A1A`) dark / paper-white (`#FFFFFF`) light — dark is canonical, NOT pure black
- **Pocket Casts Red** (`#F43E37`) — the single brand accent: tab selection, Subscribe, unplayed badge
- **Per-podcast theme tint** sampled from cover art (e.g. `#E0533C`) — re-colors scrubber, play button, active chips
- **Circular geometry** — the play button, transport controls, episode-row play buttons, and logo are all circles
- **Full-screen Now Playing player** — large cover art with a theme-tint glow + thin scrubber + circular transport
- **Up Next queue** — a persistent, reorderable play queue surfaced from the player and mini-player
- **Trim Silence + Volume Boost** — power-user playback effects exposed as first-class toggles (ON track `#F43E37`)
- **Pill buttons** — Subscribe / Play Episode use a full 999pt corner radius
- **Mini-player** — a persistent strip above the tab bar with a theme-tint accent, artwork, title, play/pause
- **Tabular timecodes** — `14:22 / -19:48` use tabular figures so digits don't jitter while scrubbing
- **Episode list rows** — uppercase date eyebrow `#757575`, 52pt artwork thumb, one-line title, circular action
- **No tint pill on tabs** — active tab is brand Red via color + fill change only

## Brand Sources

- [Pocket Casts](https://pocketcasts.com/)
- [Pocket Casts on the App Store](https://apps.apple.com/us/app/pocket-casts/id414834813)
- iOS system face `SF Pro` — Apple system font; `Inter` ([Rasmus Andersson](https://rsms.me/inter/), SIL OFL) as the web/spec fallback
- Public brand palette: Pocket Casts Red `#F43E37`, near-black canvas `#1A1A1A`, per-podcast theme tint sampled from cover art
