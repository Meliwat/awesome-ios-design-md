# Instagram iOS Inspired Design System

[DESIGN.md](DESIGN.md) extracted from observation of the public [Instagram iOS app](https://apps.apple.com/us/app/instagram/id389801252). This is not the official design system. Colors, fonts, and spacing may not be 100% accurate — but it's a solid starting point for building something that feels native to Instagram's visual language.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Complete design system documentation (9 sections) |
| `preview.html` | Interactive design token catalog (light mode) |
| `preview-dark.html` | Interactive design token catalog (dark mode) |

Use [DESIGN.md](DESIGN.md) as a reference for AI agents (Claude, Cursor, Codex, Stitch) to generate SwiftUI or UIKit UI that matches the Instagram iOS design language.

## Signature Moves

- **Pure monochrome chrome** — black/white only; the brand gradient is rationed
- **Content is the hero** — photos and videos bleed edge-to-edge with corner radius 0
- **Icon-only tab bar** — no labels, five glyphs, translucent blur
- **Instagram gradient** (`#833AB4` → `#FD1D1D` → `#FCAF45`) reserved for Stories rings and Create
- **True-black dark mode** — `#000000` for OLED power savings
- **Double-tap-to-like** with signature heart spring animation + soft haptic

## Preview

Open `preview.html` or `preview-dark.html` in a browser to see the design tokens rendered in an iPhone frame.
