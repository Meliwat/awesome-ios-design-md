<div align="center">
    <strong>Curated collection of DESIGN.md files inspired by the best iOS apps.</strong>
    <br />
    <br />

</div>

<div align="center">

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
![DESIGN.md Count](https://img.shields.io/badge/apps-8-10b981?style=classic)
![Platform](https://img.shields.io/badge/platform-iOS-000000?style=classic&logo=apple)
![Frameworks](https://img.shields.io/badge/frameworks-SwiftUI%20%2B%20Expo-informational?style=classic)
![License](https://img.shields.io/badge/license-MIT-blue?style=classic)

</div>

# Awesome iOS DESIGN.md

> **Want a screen that's not here?** Visit **[www.spectr.to](https://www.spectr.to)** to request or generate your own.

> Inspired by the original [awesome-design-md](https://github.com/VoltAgent/awesome-design-md) concept, reimagined for native iOS. All trademarks, logos, and visual identities referenced here remain the property of their respective owners. These documents exist so AI agents can generate iOS UI that feels at home on the platform.

Copy a DESIGN.md (or one of the framework-specific companions) into your Xcode or Expo project, tell your AI agent "build me a screen that looks like this" and get SwiftUI / React Native code that actually matches the reference app — fonts, spacing, motion, haptics, and all.

## What is DESIGN.md?

[DESIGN.md](https://stitch.withgoogle.com/docs/design-md/overview/) is a plain-text design system document that AI agents read to generate consistent UI. Just a markdown file. No Figma exports, no JSON schemas, no special tooling. Drop it into your project root and any AI coding agent (Claude, Cursor, Codex, Stitch) instantly understands how your screens should look.

| File | Who reads it | What it defines |
|------|-------------|-----------------|
| `AGENTS.md` | Coding agents | How to build the project |
| `DESIGN.md` | Design agents | How the app should look and feel |

**This repo provides ready-to-use DESIGN.md files for iOS apps** — colors, SF Pro type ramps, component specs, SF Symbol usage, Dynamic Type behavior, HIG-correct layouts, haptics and motion.

## Three flavors per app

Each app ships three docs so you can pick the one that matches your stack:

| File | For |
|------|-----|
| `DESIGN.md` | Framework-neutral — any agent, any framework (Flutter, SwiftUI, UIKit, React Native, Kotlin Compose) |
| `DESIGN-swiftui.md` | SwiftUI-specific — `Color` / `Font` extensions, `ViewModifier`s, sample views, SF Symbols, `.sensoryFeedback` haptics |
| `DESIGN-expo.md` | Expo / React Native — design token module, StyleSheet, Reanimated + `expo-haptics`, `expo-router`, `@expo/vector-icons` |

## What's Inside Each DESIGN.md

Every file follows the [Stitch DESIGN.md format](https://stitch.withgoogle.com/docs/design-md/format/), adapted for iOS:

| # | Section | What it captures (iOS flavor) |
|---|---------|-----------------|
| 1 | Visual Theme & Atmosphere | Mood, density, platform personality |
| 2 | Color Palette & Roles | Semantic name + hex + system color mapping (light + dark) |
| 3 | Typography Rules | SF Pro / custom faces, full Dynamic Type hierarchy |
| 4 | Component Stylings | Buttons, cards, inputs, tab bars, nav bars, sheets — with states |
| 5 | Layout Principles | 4/8pt grid, safe areas, insets, whitespace philosophy |
| 6 | Depth & Elevation | Shadows, materials (`.regularMaterial`, `.thin`), blurs, surface hierarchy |
| 7 | Do's and Don'ts | Design guardrails aligned with Apple's HIG |
| 8 | Responsive Behavior | Device sizes, Dynamic Type, landscape, iPad adaptation, touch targets |
| 9 | Agent Prompt Guide | Quick color reference, ready-to-use SwiftUI / Expo prompts |

Each app folder includes:

| File | Purpose |
|------|---------|
| `DESIGN.md` | Framework-neutral design system (what agents read) |
| `DESIGN-swiftui.md` | SwiftUI implementation code |
| `DESIGN-expo.md` | Expo / React Native implementation code |
| `README.md` | Pack overview, signature moves, brand sources |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

### How to Use

1. Copy an app's `DESIGN.md` (or the framework-specific companion) into your project root, next to `AGENTS.md` or `CLAUDE.md`
2. Tell your AI agent to use it: *"Build the feed screen using DESIGN-swiftui.md for styling."*
3. Ship pixel-matched UI.

## Collection

### Social & Media

- [**Instagram**](design-md/instagram/) — Photo & video social. Monochrome chrome, 10-stop brand gradient reserved for Stories + Create, icon-only tab bar, true-black OLED dark mode
- [**TikTok**](design-md/tiktok/) — Short-form video. Full-bleed vertical FYP, chromatic-aberration logo (cyan + red), right-side action rail, signature glitch aesthetic

### Music & Audio

- [**Spotify**](design-md/spotify/) — Music streaming. Dark `#121212` canvas, Spotify Green `#1DB954` as the single accent, album-art-driven Now Playing gradient via dynamic color extraction

### Food & Delivery

- [**DoorDash**](design-md/doordash/) — On-demand delivery. DoorDash Red `#EB1700` for every checkout, photo-first 16:10 merchant cards, floating pill CTA, TT Norms Pro bold hierarchy

### Travel & Marketplace

- [**Airbnb**](design-md/airbnb/) — Stays + Experiences. Rausch / `#FF385C` coral, Airbnb Cereal font, photography-first stay cards, category chip rail, subtle card shadows

### Mobility

- [**Uber**](design-md/uber/) — Ride-hailing + delivery. Stark monochrome (black/white), Uber Move font, map-as-hero layout, Base design system tokens

### Health & Utility

- [**Cal AI**](design-md/cal-ai/) — AI calorie tracking. Near-black `#0A0A0A` canvas, white-on-black CTAs, macro trio accents (blue/amber/pink), capture FAB with subtle glow
- [**Duolingo**](design-md/duolingo/) — Language learning. Feather Green `#58CC02`, Feather Bold font, mascot-driven gamification, streak + gems + hearts currency

## Request an App

[Open a GitHub issue](https://github.com/Meliwat/awesome-ios-design-md/issues/new) with the App Store link and a few reference screenshots.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

- **Improve existing files**: Fix wrong tokens, add missing states, sharpen descriptions, update token values against current app builds
- **Request an app**: Open an issue with screenshots
- **Report issues**: If something looks off vs. the real app, let us know

## License

MIT License — see [LICENSE](LICENSE)

This repository is a curated collection of design system documents extracted from publicly available iOS apps. All DESIGN.md files are provided "as is" without warranty. The extracted tokens represent values observable in live builds, public brand documentation, and App Store screenshots. We do not claim ownership of any app's visual identity. These documents exist to help AI agents generate consistent iOS UI.

Brand names, logos, proprietary typefaces (TT Norms Pro, Uber Move, Airbnb Cereal, Spotify Mix, Feather Bold, Instagram Sans) remain the property of their respective owners and must be licensed separately where required.
