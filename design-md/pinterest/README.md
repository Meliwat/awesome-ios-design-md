# Pinterest iOS Inspired Design System

Design system docs inspired by the [Pinterest iOS app](https://apps.apple.com/us/app/pinterest/id429047995). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Pinterest's public brand documentation and the Gestalt design system; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, masonry grid, Save button, pin detail |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `MasonryFlashList`, Reanimated + Haptics |
| `preview.md` | Link to the interactive design token catalog on the Spectr gallery |

## Signature Moves

- **Masonry grid** (`#1` signature) — 2-column variable-height layout, pins sit at native aspect ratio
- **Pinterest Red** (`#E60023`) reserved for the Save button and "P" logo — nothing else
- **Save button morph** — red pill transforms to black "Saved" pill on commit, success haptic
- **White canvas** (`#FFFFFF`) in light mode, warm `#121212` dark mode — pin images are the color story
- **16pt image corner radius** — larger than most apps, softens the grid
- **Pinterest Sans** (proprietary, 2022 refresh); weights 400 / 500 / 700
- **Red center Create button** in the bottom tab bar — the only non-outlined tab icon

## Brand Sources

- [Pinterest Newsroom — Pinterest Sans announcement (2022)](https://newsroom.pinterest.com/)
- [Gestalt — Pinterest's open-source design system](https://gestalt.pinterest.systems)
- Public brand palette: Pinterest Red `#E60023`, Canvas `#FFFFFF`, Text `#111111`
