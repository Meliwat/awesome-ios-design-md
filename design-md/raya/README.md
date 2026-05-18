# Raya iOS Inspired Design System

Design system docs inspired by the [Raya iOS app](https://apps.apple.com/us/app/raya/id961673617). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Raya's public product UI and press coverage; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, slideshow profile + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure black canvas** `#000000` — true black, never warmed; a gallery frame for the member's images
- **Zero brand color** — the ONLY accent is pure white `#FFFFFF`; every UI element is black, white, or a gray between
- **The member's media is the only color** — full-bleed photo/video; the UI must never tint, filter, or colored-frame it
- **Slideshow profile** — full-screen photos/videos auto-advancing like a story, scored to the member's chosen song
- **Segmented story bars** (2.5pt, `#FFFFFF` active / `rgba(255,255,255,0.3)` inactive) — tap left/right thirds to scrub
- **Music ticker** — a white note glyph + "Artist — Track" + an animated 4-bar equalizer over the photo
- **Editorial caption** — name 28pt + UPPERCASE letter-spaced role line + bio + location over a `→ rgba(0,0,0,0.92)` scrim
- **No shadows** — depth is the faint surface lift (`#0D0D0D` vs `#000000`) + a 1px `#2E2E2E` hairline
- **White fill rationed** — one primary CTA per screen; the Heart is a 60pt white **outline** circle, never a fill
- **One neo-grotesque family** — Inter (substitute) does everything; the typographic mirror of the one-color discipline
- **Permanently dark** — there is no light mode, by design
- **Restraint as exclusivity** — the absence of decoration IS the brand statement

## Brand Sources

- [Raya](https://www.rayatheapp.com/)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (closest free substitute for Raya's custom neo-grotesque)
- Public brand palette: pure black `#000000`, pure white `#FFFFFF`, hairline `#2E2E2E`, reluctant error `#E5484D`
- Signature: the music-scored slideshow profile, story bars, the white music ticker + equalizer, and the editorial caption
