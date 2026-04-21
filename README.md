<div align="center">
    <strong>Curated collection of DESIGN.md files inspired by the best iOS apps.</strong>
    <br />
    <br />

</div>

<div align="center">

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
![DESIGN.md Count](https://img.shields.io/badge/apps-30-10b981?style=classic)
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

### Social & Community

- [**Instagram**](design-md/instagram/) — Photo & video social. Monochrome chrome, 10-stop brand gradient reserved for Stories + Create, icon-only tab bar, true-black OLED dark mode
- [**TikTok**](design-md/tiktok/) — Short-form video. Full-bleed vertical FYP, chromatic-aberration logo (cyan + red), right-side action rail, signature glitch aesthetic
- [**X (Twitter)**](design-md/x-twitter/) — Real-time text-first social. Pure black OLED canvas, X Blue `#1D9BF0` reserved for links, Chirp font, column-centered feed, per-action color micro-animations
- [**Threads**](design-md/threads/) — Text-forward Meta social. Pure black canvas, Instagram-coral like heart, thread-line indentation, no hashtags/trending, Instagram Sans
- [**Facebook**](design-md/facebook/) — Social network. Facebook Blue `#1877F2`, `#F0F2F5` feed background, 7-emoji Reactions popover with drag-select, profile cover hero
- [**Snapchat**](design-md/snapchat/) — Camera-first social. Snap Yellow `#FFFC00`, 5-screen swipe nav, 82pt yellow capture button, color-coded snap types, Avenir Next
- [**Reddit**](design-md/reddit/) — Communities & discussion. Reddit Orange-Red `#FF4500`, upvote-orange `#FF8717` / downvote `#7193FF` vote column, flat post cards, 10pt-per-level comment indentation
- [**Pinterest**](design-md/pinterest/) — Visual discovery. Pinterest Red `#E60023`, masonry 2-column grid with variable heights, floating Save-button morph, Pinterest Sans
- [**LinkedIn**](design-md/linkedin/) — Professional network. Cream canvas `#F3F2EF`, LinkedIn Blue `#0A66C2`, 6-reaction picker, Premium gold avatar frame, Open-to-Work green ring

### Messaging

- [**WhatsApp**](design-md/whatsapp/) — Encrypted chat. WhatsApp Green `#25D366`, mint-leaf outgoing bubbles `#D9FDD3`, voice-message waveform, blue double-check read receipts
- [**Telegram**](design-md/telegram/) — Fast, themeable chat. Telegram Blue `#0088CC`, 17pt bubbles with 6pt notch tails, silent-send long-press, Lottie animated emoji, floating voice mini-player
- [**Discord**](design-md/discord/) — Community chat. Blurple `#5865F2`, three-gray surface system `#1E1F22`/`#2B2D31`/`#313338`, server rail with squircle-morph on active, role-colored usernames, gg sans
- [**Slack**](design-md/slack/) — Work chat. Aubergine `#4A154B` sidebar (workspace-customizable), rounded-square avatars, Huddles banner, reaction pill chips, Slack Lato

### Music & Audio

- [**Spotify**](design-md/spotify/) — Music streaming. Dark `#121212` canvas, Spotify Green `#1DB954` as the single accent, album-art-driven Now Playing gradient via dynamic color extraction
- [**Apple Music**](design-md/apple-music/) — Music + lyrics. Apple Music Red `#FA2D48`, SF Pro native, time-synced word-by-word lyrics, Dolby Atmos badge, `.regularMaterial` tab bar

### Video & Streaming

- [**YouTube**](design-md/youtube/) — Video platform. YouTube Red `#FF0000`, 16:9 thumbnails with duration tag, Subscribe morph, mini-player drag-down, Shorts rail, YouTube Sans + Roboto
- [**Netflix**](design-md/netflix/) — Streaming. Netflix Red `#E50914` on `#141414` canvas, hero trailer auto-play, Top 10 numeral row, Continue Watching progress bar, Netflix Sans

### Food & Delivery

- [**DoorDash**](design-md/doordash/) — On-demand delivery. DoorDash Red `#EB1700` for every checkout, photo-first 16:10 merchant cards, floating pill CTA, TT Norms Pro bold hierarchy
- [**Starbucks**](design-md/starbucks/) — Coffee + rewards. Starbucks Green `#00704A`, gold Stars progress ring, Short/Tall/Grande/Venti/Trenta size selector, QR pay, SoDo Sans

### Travel & Mobility

- [**Airbnb**](design-md/airbnb/) — Stays + Experiences. Rausch / `#FF385C` coral, Airbnb Cereal font, photography-first stay cards, category chip rail, subtle card shadows
- [**Uber**](design-md/uber/) — Ride-hailing + delivery. Stark monochrome (black/white), Uber Move font, map-as-hero layout, Base design system tokens
- [**Google Maps**](design-md/google-maps/) — Navigation. Google Blue `#4285F4` route polyline, map-as-hero with floating bottom sheet drawer, color-coded pins, turn-by-turn immersive mode

### Shopping & Finance

- [**Amazon**](design-md/amazon/) — E-commerce. Amazon Yellow `#FF9900` Add-to-Cart, Deep Navy `#131921` top nav, PDP price block with superscript cents, Prime badge, Amazon Ember
- [**Venmo**](design-md/venmo/) — Social payments. Venmo Blue `#008CFF`, public emoji-laden transaction feed, Pay/Request split pill, animated sent-checkmark

### Productivity

- [**Gmail**](design-md/gmail/) — Email. Google multicolor logo, Gmail Red `#D93025` Compose FAB (16pt squircle), Material You active-indicator pill, swipe archive/delete, Google Sans + Roboto
- [**Notion**](design-md/notion/) — Block-based workspace. Pure white / `#191919` dark, block editor with `/` command palette, 9 muted pastel page backgrounds, user-switchable Inter / Lora / IBM Plex Mono

### Dating

- [**Tinder**](design-md/tinder/) — Dating swipe cards. Tinder Gradient `#FD267A → #FF6036`, rotated LIKE/NOPE stamps on swipe, 5-button action bar (Rewind / Nope / Super Like / Like / Boost), match-screen pink takeover

### AI

- [**ChatGPT**](design-md/chatgpt/) — AI chat. Monochromatic canvas, 32pt black/white circular send button, user-bubble vs inline-assistant asymmetry, full-screen pulsing blue voice-mode sphere, Söhne

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
