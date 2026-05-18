# DeepSeek iOS Inspired Design System

Design system docs inspired by the [DeepSeek iOS app](https://apps.apple.com/us/app/deepseek-ai-assistant/id6737597349). Not the official system. Brand colors, font names, and component patterns are cross-referenced with DeepSeek's public product UI and the company's app; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, reasoning trace + toggle composer + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Near-black canvas** (`#0E0E10`) dark / clean white (`#FFFFFF`) light — calm, research-tool quiet
- **One accent only** — DeepSeek Blue `#4D6BFE` on the whale mark, user bubble, send button, active toggles; never body text
- **Reasoning trace** — collapsible recessed panel (`#16171A`, *darker* than canvas) with a 2pt blue left-bar
- **Chain-of-thought is dimmed italic** `#8A8B90` 13pt — the italic IS the "internal monologue" signal
- **Answer is upright & dominant** `#ECECEC` 15pt — visually primary over the subordinate trace
- **"Thought for N seconds · tap to collapse"** header — 200ms height collapse
- **DeepThink (R1) toggle** — switches V3 (fast) → R1 (reasoning, shows the trace)
- **Search toggle** — grounds answers in web results with `[n]` citation chips
- **Soft-blue active toggle** — `#1E2240` fill + `#4D6BFE` border + blue text/icon
- **User bubble** — soft-blue `#1E2240` fill, asymmetric `18/18/4/18` corner; AI replies have no bubble
- **Whale logomark** — the brand glyph, always DeepSeek Blue `#4D6BFE`
- **Blue-tinted code tokens** `#C5CDFF` on `#232428` monospace chips

## Brand Sources

- [DeepSeek](https://www.deepseek.com/)
- [DeepSeek iOS App](https://apps.apple.com/us/app/deepseek-ai-assistant/id6737597349)
- System SF Pro Text/Display (iOS) · [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (open fallback)
- SF Mono (system monospace, iOS) — code blocks
- Public brand palette: DeepSeek Blue `#4D6BFE`, near-black canvas `#0E0E10`, recessed reasoning panel `#16171A`
