# ChatGPT iOS Inspired Design System

Design system docs inspired by the [ChatGPT iOS app](https://apps.apple.com/us/app/chatgpt/id6448311069). Not the official system. Brand colors, font names, and component patterns are cross-referenced with OpenAI's public brand materials, openai.com, and observed in-app UI (post-2024 redesign); exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics + Markdown |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Pure white canvas** (`#FFFFFF`) light / charcoal-black (`#212121`) dark — monochromatic, minimum chrome
- **Circular send button** — 32pt black-on-white light / white-on-black dark, single up-arrow glyph
- **User messages in soft-gray bubbles** with asymmetric corners (18/18/18/4); assistant messages get NO bubble
- **Voice mode sphere** — full-screen pulsing blue gradient (`#3B82F6` → `#60A5FA` → `#93C5FD`) — the flagship animation
- **Model selector chip** top-left — pill with "GPT-4o" + chevron + bottom-sheet picker
- **4-icon feedback row** below every assistant response — regenerate / copy / 👍 / 👎
- **Fully rendered markdown** — headings, code blocks with syntax + copy button, tables, LaTeX, lists
- **Sidebar time-grouped** — Today / Yesterday / Previous 7 Days / Previous 30 Days / Month Year
- **Söhne typography** (with Inter / SF Pro fallback) — neo-grotesque, weights 400 / 500 / 600
- **Typing indicator** — 3 dots pulsing in sequence while model streams
- **Compose with icon row** — paperclip + globe + mic when empty; send button when text entered
- **Retired ChatGPT Green** (`#10A37F`) — the 2024 redesign leans monochromatic; blue (`#2A7FFF`) only on links

## Brand Sources

- [OpenAI Brand Resources](https://openai.com/brand/)
- [Söhne by Klim Type Foundry](https://klim.co.nz/retail-fonts/soehne/) — commercial license
- [Inter by Rasmus Andersson](https://rsms.me/inter/) — SIL OFL (fallback)
- Public brand palette: legacy green `#10A37F` (largely retired), link blue `#2A7FFF`, voice sphere blues `#3B82F6` / `#60A5FA` / `#93C5FD`
