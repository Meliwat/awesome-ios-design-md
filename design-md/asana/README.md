# Asana iOS Inspired Design System

Design system docs inspired by the [Asana iOS app](https://apps.apple.com/us/app/asana-work-in-one-place/id489969512). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Asana's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, task list + completion circle + status card |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **The task list IS the product** — every view is a rendering of the same checkbox + name + metadata-pill rows
- **Completion circle** — 20pt hollow circle fills green (`#62D26F`) with a celebratory checkmark + haptic on tap
- **Single brand accent** — Asana Coral (`#F06A6A`) only; FAB, active tab, primary CTA — identical light & dark
- **Coral FAB** — 56pt circular `#F06A6A` create-task button with a soft coral-tinted shadow `rgba(240,106,106,0.45)`
- **Multicolor object palette** — plum (`#4573D2`) / aqua (`#4ECBC4`) / green (`#62D26F`) / yellow (`#F8DF72`) / orange (`#F1BD6C`) / magenta (`#F26FD3`)
- **Color as ~16% tint** — pills are low-opacity fills with saturated text, never solid slabs
- **Four-state status updates** — On track (`#4ECBC4`) / At risk (`#F8DF72`) / Off track (`#E8384F`) / Complete (`#62D26F`)
- **Charcoal dark canvas** `#1E1F21` — NOT pure black; text inverts to warm off-white `#F5F4F2`
- **Sectioned list** — collapsible caret headers (Today / Upcoming / custom) with task counts
- **View switcher tabs** — List / Board / Calendar / Timeline / Files over the same task data, active gets a 2pt coral underline
- **Inter typography** — quiet, legible; task rows 15pt/500, screen titles 32pt/800, tabular numerals for dates
- **Celebration creatures** — a unicorn/narwhal flies across on full-section completion

## Brand Sources

- [Asana](https://asana.com/)
- [Asana Brand Guidelines](https://asana.com/brand)
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL
- Public brand palette: Asana Coral `#F06A6A`, charcoal canvas `#1E1F21`, multicolor object palette (plum/aqua/green/yellow/orange/magenta/indigo)
