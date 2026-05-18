# Peloton iOS Inspired Design System

Design system docs inspired by the [Peloton iOS app](https://apps.apple.com/us/app/peloton-fitness-workouts/id792750948). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Peloton's public product UI and the company's brand expression; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, class card, LIVE badge, output ring, metric column, real-time leaderboard |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, SVG output ring, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, Canvas rings, live leaderboard + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure-black studio canvas** (`#000000`) — never charcoal; cinematic content bleeds edge-to-edge
- **Single brand color** — Peloton Red `#DF1E2E` (brightened to `#FF4B57` for text/active on black)
- **Cinematic class cards** — 150pt film-poster thumbnail with a baked gradient scrim
- **Pulsing LIVE badge** — `#DF1E2E` fill, white "LIVE", a white dot pulsing on a 1.4s loop (the brand heartbeat)
- **White-chip selected pill** — active filter inverts to solid `#FFFFFF` with `#000000` text
- **In-class output ring** — thick `#DF1E2E` arc on `#262629` track, total kJ in 900-weight numerals
- **Fixed metric colors** — Cadence `#E5402A`, Resistance `#F0A030`, Strive/HR `#3DB8E0`, theme-invariant
- **Real-time leaderboard** — your row washed `rgba(223,30,46,0.12)` with red accents, rows reorder live
- **Inter set heavy** — output up to 44pt/900, tight bold titles, small uppercase red eyebrows
- **Pill-shaped buttons** — red primary "Take Class", white secondary "Start Ride"
- **No tint pill** in the 4-tab bar — active is simply white
- **No light mode** — always studio-black by design

## Brand Sources

- [Peloton](https://www.onepeloton.com/) · [Peloton App](https://www.onepeloton.com/app)
- Peloton brand expression — Peloton Red `#DF1E2E`, pure-black studio canvas
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public product UI: cinematic class cards, the pulsing LIVE badge, the output ring, the real-time leaderboard, fixed in-class metric colors
