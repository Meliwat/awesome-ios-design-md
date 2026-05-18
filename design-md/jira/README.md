# Jira iOS Inspired Design System

Design system docs inspired by the [Jira iOS app](https://apps.apple.com/us/app/jira-cloud-by-atlassian/id1006972087). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Jira's public product UI and Atlassian's published Design System (Atlassian Design Tokens); exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, board + drag + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Single brand color** — Jira Blue (`#0052CC`) is the only saturated color, used for primary actions only; brightens to `#1868DB` for interactive elements on dark
- **Board + issue card** — horizontal status columns of vertical card stacks; the card is the atomic unit
- **Issue card footer pattern** — issue-type icon + key (left), story points + assignee avatar (right), 2-line summary clamp
- **Status lozenges** — uppercase tracked rounded chips with semantic color: To Do `#DCDFE4`/`#44546F`, In Progress `#E9F2FF`/`#0055CC`, Done `#DCFFF1`/`#216E4E`, Blocked `#FFECEB`/`#AE2A19`
- **Issue-type iconography** — green square Story `#1F845A`, red circle Bug `#C9372C`, blue check Task `#1868DB`, purple lightning Epic `#8270DB`
- **Atlassian neutral canvas** — light `#FFFFFF`/`#F7F8F9`, dark `#1D2125` (never true black)
- **Atlassian Navy ink** `#172B4D` — primary text on light; never pure black
- **Uppercase micro-labels** — 11-12pt tracked caps for column headers and field labels
- **Tabular numerals** — points, counts, and issue keys align in columns
- **Drag-to-transition** — long-press a card, drag across columns, drop updates the status lozenge with haptic
- **Hairline structure** — field/list rows separated by 1px navy-tinted dividers, never nested cards
- **Atlassian Sans typography** — humanist product sans (Inter as the closest free analog)

## Brand Sources

- [Jira](https://www.atlassian.com/software/jira)
- [Atlassian Design System](https://atlassian.design/) — color tokens, elevation, lozenge & icon patterns
- [Atlassian Design Tokens](https://atlassian.design/components/tokens/all-tokens) — `#0052CC` brand blue, `#172B4D` navy ink, neutral ramp, semantic palette
- Atlassian Sans / Atlassian Mono (Atlassian, 2023) — proprietary; **Inter** (SIL OFL) is the closest free analog
- Public brand palette: Jira Blue `#0052CC`, Navy ink `#172B4D`, semantic blue/green/red/yellow/purple + neutral gray ramp
