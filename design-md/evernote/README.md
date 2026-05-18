# Evernote iOS Inspired Design System

Design system docs inspired by the [Evernote iOS app](https://apps.apple.com/us/app/evernote-notes-organizer/id281796108). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Evernote's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, editor + checklist + FAB + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Single brand color** — Evernote Green (`#00A82D`) for logo, FAB, checkboxes, links, primary actions; brightens to `#2DBE60` for interactive elements on dark
- **Note editor + note list** — near chrome-free document ⇄ scannable list; the loop the app orbits
- **Green circular FAB** — raised in the center of the bottom bar for new note / capture, shadow `0 6px 16px rgba(0,168,45,0.40)`
- **Tags as green-tinted pill chips** — `#E6F4EA`/`#1F7A33` light, `#1C3A24`/`#5FD68A` dark
- **Notebooks + stacks** — colored notebook-spine icons with note counts; stacks are expandable groups
- **True near-black dark mode** — canvas `#1C1C1E` for reading comfort, body text `#E4E4E6` (not pure white)
- **Warm Evernote ink** `#1C2B33` — primary text on light; never pure black
- **Roomy 1.6 body line-height** — built for long-form reading, 22pt editor side margins
- **Green-fill checkboxes** — completed items get strikethrough + dim, kept visible (never removed)
- **Format toolbar docked above the keyboard** — Bold / Italic / List / Checklist / Attach / Link, active control green
- **First-class Search** — full-text, OCR, handwriting; a bottom-tab destination
- **Calm humanist typography** — system SF Pro on iOS (Inter as the closest free analog)

## Brand Sources

- [Evernote](https://evernote.com/)
- [Evernote Brand & Press](https://evernote.com/press) — elephant logomark, brand green
- Public brand palette: Evernote Green `#00A82D` (current) / `#108A00` (legacy deep green), warm ink `#1C2B33`
- iOS system font (SF Pro) — [Inter by Rasmus Andersson](https://rsms.me/inter/) (SIL OFL) is the closest free analog
