# Postman iOS Inspired Design System

Design system docs inspired by the [Postman iOS app](https://apps.apple.com/us/app/postman-api-platform/id6478579414). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Postman's public product UI and brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, request builder + response viewer |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Neutral grey canvas** (`#1A1A1A`) dark / `#FFFFFF` light — a developer console, not a branded surface
- **Request builder as home** — method pill + monospace URL + orange Send button
- **HTTP-method color system** — GET `#6BDD9A` / POST `#FFE47A` / PUT `#74AEF6` / PATCH `#C0A8E1` / DELETE `#F79090`, consistent everywhere
- **Single brand orange** `#FF6C37` — reserved for Send / active tab / primary CTAs, never decorative
- **Status-code chips** — 2xx green / 3xx blue / 4xx yellow / 5xx red, monospace pills
- **Sans for chrome, Mono for data** — Inter labels, JetBrains Mono URLs / headers / JSON
- **Syntax-colored JSON** — keys `#74AEF6` / strings `#6BDD9A` / numbers `#C0A8E1` / punctuation `#6E6E6E`
- **Response code block sinks** — JSON sits on a darker `#161616` surface (data sinks, controls float)
- **Environment pill + `{{variables}}`** — active env shown as a pill, interpolated tokens in orange
- **Method hues soften on dark** — pastel-ish dark set vs saturated light set, hue identity preserved

## Brand Sources

- [Postman](https://www.postman.com/)
- [Postman Brand Resources](https://www.postman.com/company/brand-assets/) — orange `#FF6C37`, the roundel logomark
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (UI chrome analog)
- [JetBrains Mono](https://www.jetbrains.com/lp/mono/) — SIL OFL (request-data monospace analog)
- Public brand palette: Postman Orange `#FF6C37`; HTTP-method colors GET green / POST yellow / PUT blue / PATCH purple / DELETE red
