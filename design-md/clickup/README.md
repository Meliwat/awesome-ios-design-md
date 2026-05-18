# ClickUp iOS Inspired Design System

Design system docs inspired by the [ClickUp iOS app](https://apps.apple.com/us/app/clickup-mobile-app/id1492113819). Not the official system. Brand colors, font names, and component patterns are cross-referenced with ClickUp's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, brand `LinearGradient`, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, `expo-linear-gradient`, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, brand `Brush`, Material 3 `Typography`, composables, dense status list + squircle FAB + AI bar |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **3-stop brand gradient** — purple `#7B68EE` → pink `#FD71AF` → blue `#49CCF9`, reserved for the FAB, primary CTA, logo, and all AI surfaces
- **Deep blue-violet dark canvas** `#1B1B2E` — chosen to complement the gradient, NOT neutral black
- **Custom status pills** — every List defines its own ordered colored statuses; the task list groups by them (SOLID, uppercased, 10pt/800)
- **Dense task rows** — status box + name + priority flag + tags + due + assignee packed into one compact line
- **Squircle FAB** — a 56pt rounded-square (18pt radius) gradient button, deliberately NOT a circle
- **Priority flag triangle** — Urgent (`#F44E6E`) / High (`#FF9F1A`) / Normal (`#49CCF9`) / Low (gray)
- **ClickUp AI surfaces** — the gradient marks every AI affordance (AI bar tint + border, AI buttons, AI content backdrop)
- **12+ view types** — List / Board / Calendar / Gantt / Timeline / Doc / Table / Mind Map / Workload / Map over the same tasks
- **Tags as ~22% tinted pills** — saturated text on a translucent fill in any palette hue
- **Hierarchy breadcrumb** — Space ▸ Folder ▸ List path always visible in the top nav
- **Plus Jakarta Sans typography** — confident; task names 14pt/600, titles 800, status pills 10pt/800, tabular numerals
- **Functional palette** — green `#2ECC71` / red `#F44E6E` / orange `#FF9F1A` / blue `#49CCF9` solids; the gradient is the only gradient

## Brand Sources

- [ClickUp](https://clickup.com/)
- [ClickUp Brand](https://clickup.com/brand)
- [Plus Jakarta Sans (Google Fonts)](https://fonts.google.com/specimen/Plus+Jakarta+Sans) — SIL OFL, the closest free match to ClickUp's product typeface
- Public brand palette: 3-stop gradient purple `#7B68EE` → pink `#FD71AF` → blue `#49CCF9`, deep blue-violet canvas `#1B1B2E`
