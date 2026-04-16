# Cal AI iOS Inspired Design System

[DESIGN.md](DESIGN.md) inspired by the [Cal AI iOS app](https://apps.apple.com/us/app/cal-ai-calorie-tracker/id6480417616) — an AI-powered calorie tracker by Viral Development that detects meals from a single photo. This is not the official design system — it's a design-language interpretation based on observed screenshots and marketing material (the app uses a prominent dark theme). Some hex values and font names are best-effort approximations; audit against the live app before shipping production UI.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native — design tokens, StyleSheet, Reanimated + Haptics |
| `preview.html` | Interactive design token catalog (light mode, secondary) |
| `preview-dark.html` | Interactive design token catalog (dark mode, primary) |

## Signature Moves

- **Near-black canvas** (`#0A0A0A`) — darker than most iOS apps, OLED-friendly
- **White-on-black primary CTA** — the bold "Scan a meal" / "Save" pill, NOT a saturated brand color
- **Macro trio accents**: Protein blue (`#4DA8FF`), Carbs amber (`#FFB54C`), Fat pink (`#FF6E87`) — desaturated, calm
- **Oversized numerals** (48-72pt weight 700) with tabular figures for calorie totals
- **Capture FAB** — 68pt white circle in the tab bar with a subtle white glow shadow
- **AI tri-color gradient sweep** (blue → indigo → pink) at 2pt thickness during detection
- **Data-forward type** — Inter / SF Pro at weights 400-700, no rounded display faces

## Sources

- [Cal AI on the App Store](https://apps.apple.com/us/app/cal-ai-calorie-tracker/id6480417616)
- [Cal AI product site](https://www.calai.app/)

## Notes on Accuracy

Cal AI does not publish a public design system. Colors and tokens in this pack are an interpretation based on public screenshots. The brand palette and typography choices represent a plausible match to the app's visible aesthetic (dark canvas + macro color trio + large tabular numerals + white CTA) but should be verified against a live build before being used as authoritative tokens.
