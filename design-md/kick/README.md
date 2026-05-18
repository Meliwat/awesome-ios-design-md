# Kick iOS Inspired Design System

Design system docs inspired by the [Kick iOS app](https://apps.apple.com/us/app/kick-live-streaming/id6451060459). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Kick's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, watch page + live chat, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, watch page + chat, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, watch page + live chat |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **A single electric lime green** (`#53FC18`) does *everything* — logo, all primary CTAs, active tab, links, online rings
- **Near-black canvas** (`#0E0E10`) — NOT pure black (true black makes the green vibrate); dark-only, no light mode
- **Strictly monochrome** everywhere else (`#161618`/`#1F1F23`/`#2A2A2E`) so the green screams
- **Signature screen** = video player + streamer bar + live chat panel stacked vertically
- **Role-colored chat usernames** — mod `#3EA6FF`, sub `#FFC700`, VIP `#FF4FD8`, OG/regular green-ish
- **Tiny role badge chips** before usernames — MOD / sub-month number / VIP / OG / VERIFIED
- **Custom emote sprites** rendered inline in chat at ~18pt
- **Red `#FF1F44` LIVE pill** + viewer-count chip on every stream and channel card (never green)
- **Green Follow / Subscribe buttons**; 2pt `#53FC18` online ring around live streamers' avatars
- **Live channel grid** — 16:9 thumbnail + LIVE tag + viewer count + avatar + title + category
- **Tight radii** (6pt buttons, 8pt cards) — Kick is bolder/rawer/less rounded than other streamers
- **Bottom tabs** Home / Browse / Following / Clips / Profile — active icon green `#53FC18`, no pill

## Brand Sources

- [Kick](https://kick.com/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (closest free analog to Kick's tight grotesque)
- [Archivo (Google Fonts)](https://fonts.google.com/specimen/Archivo) — SIL OFL (acceptable substitute)
- Public brand palette: Kick Green `#53FC18` on near-black `#0E0E10`, live red `#FF1F44`, role colors (mod `#3EA6FF`, sub `#FFC700`, VIP `#FF4FD8`)
