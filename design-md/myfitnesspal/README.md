# MyFitnessPal iOS Inspired Design System

Design system docs inspired by the [MyFitnessPal iOS app](https://apps.apple.com/us/app/myfitnesspal-calorie-counter/id341232718). Not the official system. Brand colors (MFP Blue, Lake Blue, the macro trio), typography (SF Pro / Proxima Nova), and component patterns (calorie ring, macro donut, diary FAB) are cross-referenced with MyFitnessPal's public brand pages and App Store screenshots; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, calorie ring, macro donut, FAB, haptics |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, `react-native-svg` ring, Reanimated |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery/myfitnesspal](https://www.spectr.to/gallery/myfitnesspal) |

## Signature Moves

- **White canvas, clinical-but-friendly** — content-first, soft `#F5F7FA` surface for diary rows, no aggressive shadows
- **MFP Blue heritage + Lake Blue action** — `#005DAA` lives on the logomark and Premium gradient; `#0072CE` is the current CTA color and ring fill
- **Calorie ring** — 220pt diameter, 18pt stroke, semantic color flip (green under → blue on track → amber approaching → red over), big tabular 56pt number in the center
- **Macro donut trio** — Carbs orange `#FF9F1C`, Fat purple `#A463F2`, Protein green `#19C37D`, clockwise from 12 o'clock, locked order, locked colors
- **Diary FAB** — 56pt Lake Blue circle floating above the tab bar with a blue-tinted shadow, opens the Add-Food sheet (Scan / Search / Quick Add / Recent / My Foods)
- **Barcode scanner** — full-screen camera with a pulsing red 1pt scan line and four white L-bracket corners framing a 280×180 window
- **Meal section pattern** — Breakfast / Lunch / Dinner / Snacks (in that order), each with a calorie total on the right and an inline `+ Add Food` row
- **Tabular numerals on every number** — calories, grams, percentages, weight, water; non-negotiable
- **SF Pro** — system default on iOS; Proxima Nova substituted on marketing/web

## Brand Sources

- [MyFitnessPal — Brand & Press](https://www.myfitnesspal.com/press)
- [App Store — MyFitnessPal: Calorie Counter](https://apps.apple.com/us/app/myfitnesspal-calorie-counter/id341232718)
- [MyFitnessPal 2022 Rebrand — Lake Blue refresh](https://blog.myfitnesspal.com/)
- Public brand palette: MFP Blue `#005DAA` (heritage), Lake Blue `#0072CE` (current action), Carbs Orange `#FF9F1C`, Fat Purple `#A463F2`, Protein Green `#19C37D`, Ink `#1F2937`, Slate `#4B5563`
