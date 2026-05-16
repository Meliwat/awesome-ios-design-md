# Figma iOS Inspired Design System

Design system docs inspired by the [Figma iOS app](https://apps.apple.com/us/app/figma/id1152747299). Not the official system. Brand colors (the five Figma cubes, Action Blue, Blurple, Macaron Pink), typography (Inter), and component patterns (file card, cube avatar, comment pin) are cross-referenced with Figma's public brand pages and the Figma Editor's exact surface tokens; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, file card, cube avatar, comment pin, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated spring transitions |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, brand-cube avatars + trailing cursors + Haptics |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery/figma](https://www.spectr.to/gallery/figma) |

## Signature Moves

- **The Five Cubes** — Red `#F24E1E`, Orange `#FF7262`, Purple `#A259FF`, Blue `#1ABCFE`, Green `#0ACF83` — assigned per user, persisted forever, appearing on avatars, file thumbnails, cursors. Brand accents only, never primary CTAs.
- **Action Blue** `#0D99FF` is the actual CTA color, distinct from the avatar Blue cube
- **Blurple** `#5551FF` for plan upgrades, FigJam, and Slides accents
- **File cards** — 16:10 aspect ratio, 6pt corner radius, thumbnail + name (14pt 500) + metadata (12pt 400) below; 2-col grid on iPhone, 4-col on iPad
- **Cube avatar pattern** — circular, single uppercase initial in white Inter SemiBold, randomly assigned from the 5 cubes and persisted via user ID hash
- **Comment pins** — 28pt circle, commenter's cube color, white number, 2pt border for legibility over varied frame backgrounds; tap opens a slide-up thread sheet
- **Dark canvas** `#1E1E1E` matches the Figma Editor's exact background — first-class dark mode
- **Inter typography** — at small, dense 11–16pt sizes; tabular numerals on every number; SF Mono for hex codes (uppercase, slashed zero)
- **4-tab bottom bar** — Recents, Drafts, Notifications, Profile; active tint is a weight/fill swap (not a brand color)
- **6pt corner radius** is the Figma signature on buttons, thumbnails, and inputs

## Brand Sources

- [Figma — Brand](https://www.figma.com/brand/)
- [App Store — Figma](https://apps.apple.com/us/app/figma/id1152747299)
- [Inter — Rasmus Andersson](https://rsms.me/inter/)
- [Figma Color in the Editor](https://help.figma.com/) — the dark canvas `#1E1E1E` and surface palette mirror the Editor
- Public brand palette: Red `#F24E1E`, Orange `#FF7262`, Purple `#A259FF`, Blue `#1ABCFE`, Green `#0ACF83`, Action Blue `#0D99FF`, Blurple `#5551FF`, Macaron Pink `#FFC8C8`
