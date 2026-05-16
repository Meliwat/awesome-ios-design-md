<div align="center">

# Awesome iOS DESIGN.md

**Production-grade design systems for the 100 best apps.**
**Framework-neutral plus SwiftUI, Expo, and Jetpack Compose. Hand one to your AI agent — ship pixel-matched UI.**

<br/>

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Apps](https://img.shields.io/badge/apps-100-10b981?style=flat-square)](#collection)
[![Flavors](https://img.shields.io/badge/flavors-neutral%20%C2%B7%20SwiftUI%20%C2%B7%20Expo%20%C2%B7%20Compose-6E56CF?style=flat-square)](#four-flavors-per-app)
[![Live gallery](https://img.shields.io/badge/live%20gallery-spectr.to-0A84FF?style=flat-square)](https://www.spectr.to/gallery)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-22C55E?style=flat-square)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)

<br/>

[**Browse the live gallery**](https://www.spectr.to/gallery) &nbsp;·&nbsp; [**Quick start**](#quick-start) &nbsp;·&nbsp; [**The 100 apps**](#collection) &nbsp;·&nbsp; [**Request an app**](https://github.com/Meliwat/awesome-ios-design-md/issues/new/choose)

</div>

---

Hand your AI coding agent a `DESIGN.md` and it stops guessing. Each file is a complete, reverse-engineered design system for a real app — **exact hex values, full type ramps, component states, motion curves, and haptics** — in plain markdown that Claude, Cursor, Codex, and Stitch read natively. No Figma exports. No JSON. No tooling. No setup.

> **Want an app that's not here?** [Request it in one click](https://github.com/Meliwat/awesome-ios-design-md/issues/new/choose) — or generate your own from a screen recording at **[www.spectr.to](https://www.spectr.to)**.

> Inspired by [awesome-design-md](https://github.com/VoltAgent/awesome-design-md), reimagined for native mobile. All trademarks, logos, and visual identities remain the property of their respective owners — these documents exist so agents can generate UI that feels native to each platform.

## Quick start

1. **Pick a pack** — the framework-neutral `DESIGN.md`, or the companion that matches your stack:

   ```bash
   curl -O https://raw.githubusercontent.com/Meliwat/awesome-ios-design-md/main/design-md/spotify/DESIGN-swiftui.md
   ```

2. **Drop it in** your project root, next to `AGENTS.md` / `CLAUDE.md`.

3. **Prompt your agent:**

   > *"Build the Now Playing screen using `DESIGN-swiftui.md` for all styling — match the palette, type ramp, spacing, and motion exactly."*

Your agent now has the `#121212` canvas, the Spotify-green play button, the 4/8pt grid, the spring curves, and the haptics — not a vibe, the actual spec.

## What is DESIGN.md?

[DESIGN.md](https://stitch.withgoogle.com/docs/design-md/overview/) is a plain-text design system document that AI agents read to generate consistent UI. Just a markdown file. No Figma exports, no JSON schemas, no special tooling. Drop it into your project root and any AI coding agent (Claude, Cursor, Codex, Stitch) instantly understands how your screens should look.

| File | Who reads it | What it defines |
|------|-------------|-----------------|
| `AGENTS.md` | Coding agents | How to build the project |
| `DESIGN.md` | Design agents | How the app should look and feel |

**This repo provides ready-to-use DESIGN.md files for iOS apps** — colors, SF Pro type ramps, component specs, SF Symbol usage, Dynamic Type behavior, HIG-correct layouts, haptics and motion.

## Four flavors per app

Each app ships four docs so you can pick the one that matches your stack:

| File | For |
|------|-----|
| `DESIGN.md` | Framework-neutral — any agent, any framework (Flutter, SwiftUI, UIKit, React Native, Kotlin Compose) |
| `DESIGN-swiftui.md` | SwiftUI-specific — `Color` / `Font` extensions, `ViewModifier`s, sample views, SF Symbols, `.sensoryFeedback` haptics |
| `DESIGN-expo.md` | Expo / React Native — design token module, StyleSheet, Reanimated + `expo-haptics`, `expo-router`, `@expo/vector-icons` |
| `DESIGN-android.md` | Jetpack Compose — `Color` token object, Material 3 `Typography`, `@Composable`s, `NavigationBar`, AndroidX Palette, `LocalHapticFeedback` |

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
| 9 | Agent Prompt Guide | Quick color reference + ready-to-use SwiftUI / Expo / Compose prompts |

Each app folder includes:

| File | Purpose |
|------|---------|
| `DESIGN.md` | Framework-neutral design system (what agents read) |
| `DESIGN-swiftui.md` | SwiftUI implementation code |
| `DESIGN-expo.md` | Expo / React Native implementation code |
| `DESIGN-android.md` | Jetpack Compose (Material 3, Kotlin) implementation code |
| `README.md` | Pack overview, signature moves, brand sources |
| `preview.md` | Link to the interactive preview at [www.spectr.to/gallery](https://www.spectr.to/gallery) |

## Collection

**100 packs across 11 categories.** Every entry links to its folder; tap any phone in the **[live gallery](https://www.spectr.to/gallery)** to see the tokens rendered.

| Category | | Category | |
|---|--:|---|--:|
| [Social & Community](#social--community) | 15 | [Shopping & Finance](#shopping--finance) | 13 |
| [Messaging](#messaging) | 8 | [Productivity](#productivity) | 13 |
| [Music & Audio](#music--audio) | 6 | [Dating](#dating) | 5 |
| [Video & Streaming](#video--streaming) | 7 | [AI](#ai) | 6 |
| [Food & Delivery](#food--delivery) | 6 | [Health & Utility](#health--utility) | 11 |
| [Travel & Mobility](#travel--mobility) | 10 | **Total** | **100** |

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
- [**BeReal**](design-md/bereal/) — Authentic dual-camera social. Stark pure-black `#000000` canvas, no brand accent (white-only), dual front+back composite card, 2-minute countdown, RealMojis, zero vanity metrics
- [**Mastodon**](design-md/mastodon/) — Federated social. Mastodon Purple `#6364FF`, `@user@instance` handles, content-warning spoilers, home/local/federated timelines, boost-with-spin, light + `#191B22` dark
- [**Bluesky**](design-md/bluesky/) — Custom-feeds social. Bluesky Blue `#1185FE`, butterfly mark, switchable pinned custom feeds, reply-control chips, three themes (light / dim `#1E2936` / dark `#0B0F14`)
- [**Nextdoor**](design-md/nextdoor/) — Hyperlocal community. Nextdoor Green `#00B246`, warm cream `#FAF9F6` canvas, neighborhood feed, map-with-pins, verified-neighbor badge, Lato
- [**Tumblr**](design-md/tumblr/) — Creative blogging social. Deep-navy `#001935` canvas, multi-accent trio (blue `#00B8FF` / green `#00CF35` / pink `#FF4930`), reblog chains, tag bar, notes count
- [**Quora**](design-md/quora/) — Q&A knowledge. Quora Red `#B92B27`, serif-question / sans-answer split (Georgia + Inter), blue `#2E69FF` upvote pill, credential bylines, Spaces

### Messaging

- [**WhatsApp**](design-md/whatsapp/) — Encrypted chat. WhatsApp Green `#25D366`, mint-leaf outgoing bubbles `#D9FDD3`, voice-message waveform, blue double-check read receipts
- [**Telegram**](design-md/telegram/) — Fast, themeable chat. Telegram Blue `#0088CC`, 17pt bubbles with 6pt notch tails, silent-send long-press, Lottie animated emoji, floating voice mini-player
- [**Discord**](design-md/discord/) — Community chat. Blurple `#5865F2`, three-gray surface system `#1E1F22`/`#2B2D31`/`#313338`, server rail with squircle-morph on active, role-colored usernames, gg sans
- [**Slack**](design-md/slack/) — Work chat. Aubergine `#4A154B` sidebar (workspace-customizable), rounded-square avatars, Huddles banner, reaction pill chips, Slack Lato
- [**Signal**](design-md/signal/) — Privacy-first messaging. Single Signal Blue `#3A76F0` accent, disappearing-message timer chips, sealed-sender glyphs, zero-vanity minimalism, light + `#1B1B1B` dark
- [**Messenger**](design-md/messenger/) — Rich chat. Multi-stop brand gradient `#0A7CFF → #9D4EDD → #FF5CA0` conversation-anchored bubbles, chat-head avatars, 6-emoji reactions popover, active dots
- [**LINE**](design-md/line/) — Sticker-first messaging. LINE Green `#06C755`, periwinkle `#8CABD9` chat backdrop, oversized bubble-less stickers, official-account badges, super-app tabs
- [**WeChat**](design-md/wechat/) — Super-app messaging. WeChat Green `#07C160`, gray `#EDEDED` system canvas, tailed `#95EC69` bubbles, the Discover grouped-list hub, Moments, red-packet card

### Music & Audio

- [**Spotify**](design-md/spotify/) — Music streaming. Dark `#121212` canvas, Spotify Green `#1DB954` as the single accent, album-art-driven Now Playing gradient via dynamic color extraction
- [**Apple Music**](design-md/apple-music/) — Music + lyrics. Apple Music Red `#FA2D48`, SF Pro native, time-synced word-by-word lyrics, Dolby Atmos badge, `.regularMaterial` tab bar
- [**SoundCloud**](design-md/soundcloud/) — Creator audio. SoundCloud Orange `#FF5500`, commentable waveform scrubber with inline timestamped avatars, repost feed, up-next queue
- [**TIDAL**](design-md/tidal/) — Hi-fi music. Pure-black `#000000` canvas, cyan `#00FFFF` HiFi accents, MASTER/HiFi quality badges, radically flat square-art tonal-elevation layout
- [**Shazam**](design-md/shazam/) — Music recognition. Radial-blue gradient `#0050FF → #0088FF`, giant central pulsing tap-to-listen button with concentric rings, glass result card, no tab bar
- [**Audible**](design-md/audible/) — Audiobooks. Audible Orange `#FF9900` on warm charcoal `#1A1A1A`, Playfair/Inter pairing, progress-ring cover, speed-dial + 30s-skip player, Captions

### Video & Streaming

- [**YouTube**](design-md/youtube/) — Video platform. YouTube Red `#FF0000`, 16:9 thumbnails with duration tag, Subscribe morph, mini-player drag-down, Shorts rail, YouTube Sans + Roboto
- [**Netflix**](design-md/netflix/) — Streaming. Netflix Red `#E50914` on `#141414` canvas, hero trailer auto-play, Top 10 numeral row, Continue Watching progress bar, Netflix Sans
- [**Disney+**](design-md/disney-plus/) — Premium streaming. Deep space-navy `#0A0E2A` starfield, Disney Blue `#0063E5` + glow `#1A75FF`, brand-portal tile row (Disney/Pixar/Marvel/Star Wars/Nat Geo), 16:9 rails
- [**Hulu**](design-md/hulu/) — Content-dense streaming. Electric Hulu Green `#1CE783` Watch CTA on near-black `#0B0C0F`, 16:9 tiles, hub chips, continue-watching progress
- [**Max**](design-md/max/) — Prestige streaming. Gradient wordmark `#0046FF → #7B2FF7` on deep-purple `#12053A`, auto-trailer billboard, prestige rows, profile gate
- [**Twitch**](design-md/twitch/) — Live streaming. Twitch Purple `#9146FF` action + Live Red `#EB0400` liveness-only, LIVE pills, docked chat column, theater overlay, emotes
- [**Prime Video**](design-md/prime-video/) — Video on demand. Prime Blue `#00A8E1` on Amazon blue-slate `#0F171E`, hero billboard, the X-Ray cast slide-up overlay, channel rows

### Food & Delivery

- [**DoorDash**](design-md/doordash/) — On-demand delivery. DoorDash Red `#EB1700` for every checkout, photo-first 16:10 merchant cards, floating pill CTA, TT Norms Pro bold hierarchy
- [**Starbucks**](design-md/starbucks/) — Coffee + rewards. Starbucks Green `#00704A`, gold Stars progress ring, Short/Tall/Grande/Venti/Trenta size selector, QR pay, SoDo Sans
- [**Uber Eats**](design-md/uber-eats/) — Food delivery. Uber Eats Green `#06C167`, clean white canvas, photo-first restaurant cards, category pills, sticky cart bar, live map order tracking
- [**Instacart**](design-md/instacart/) — Grocery delivery. Instacart Green `#0AAD0A` + carrot `#FF7009`, aisle browse, the − n + qty stepper, replacement-preference flow, green cart bar
- [**Chipotle**](design-md/chipotle/) — QSR ordering. Chipotle Red `#A81612` on cream kraft `#FFF5E1`, build-your-burrito ingredient stepper, ALL-CAPS Archivo headers, rewards ring
- [**Domino's**](design-md/dominos/) — Pizza ordering. Domino's Red `#E31837` action + Blue `#006491` info, 5-stage pizza tracker, build-your-pizza topping layout, deal cards

### Travel & Mobility

- [**Airbnb**](design-md/airbnb/) — Stays + Experiences. Rausch / `#FF385C` coral, Airbnb Cereal font, photography-first stay cards, category chip rail, subtle card shadows
- [**Uber**](design-md/uber/) — Ride-hailing + delivery. Stark monochrome (black/white), Uber Move font, map-as-hero layout, Base design system tokens
- [**Google Maps**](design-md/google-maps/) — Navigation. Google Blue `#4285F4` route polyline, map-as-hero with floating bottom sheet drawer, color-coded pins, turn-by-turn immersive mode
- [**Apple Maps**](design-md/apple-maps/) — Apple's native navigation. Cream parchment cartography `#F6F1E6` land + `#B9D9EB` water, Maps Blue `#0A84FF` directions polyline, pulsing current-location puck, `.regularMaterial` blur on the sliding search card (88/50/full detents), SF Pro tabular figures
- [**Waze**](design-md/waze/) — Community navigation. Waze Purple `#7E55BE` + Cyan `#33CCFF` electric duo, speech-bubble hazard reports with triangular tails, cyan arrow puck (not a dot), bright cyan water + mint parks, Boing typography with SF Pro Rounded fallback
- [**Lyft**](design-md/lyft/) — Ride-hailing. Single Lyft Pink `#FF00BF` accent, map-as-hero with no tab bar, very-rounded springy ride-type selector sheet (Wait & Save / Standard / XL / Lux Black)
- [**Booking.com**](design-md/booking/) — Travel booking. Booking navy `#003580` + bright-blue `#0071C2` action + yellow `#FEBB02`, the review-score badge, dense property cards, Genius banner
- [**Tripadvisor**](design-md/tripadvisor/) — Travel reviews. Tripadvisor Green `#34E0A1` + owl black, the 5-circle bubble rating, traveler-photo place cards, Travelers' Choice badge
- [**Hopper**](design-md/hopper/) — Price-prediction travel. Hopper Red `#FA4747` + bunny, the buy/wait price-prediction calendar heatmap, watch-price toggle, fare cards
- [**Flighty**](design-md/flighty/) — Flight tracking. Flighty Blue `#0A84FF` on deep-black `#0B0B0F`, live great-circle flight arc, on-time stats, Live-Activity status bar, delay timeline

### Shopping & Finance

- [**Amazon**](design-md/amazon/) — E-commerce. Amazon Yellow `#FF9900` Add-to-Cart, Deep Navy `#131921` top nav, PDP price block with superscript cents, Prime badge, Amazon Ember
- [**Venmo**](design-md/venmo/) — Social payments. Venmo Blue `#008CFF`, public emoji-laden transaction feed, Pay/Request split pill, animated sent-checkmark
- [**Cash App**](design-md/cash-app/) — Mobile money. Pure-black canvas + single Cash Green `#00D632` accent, Cash Sans / Cash Sans Mono, 96pt $-amount keypad, icon-only tab bar, matte black Cash Card
- [**PayPal**](design-md/paypal/) — Online payments. Dual-blue P-P wordmark (Sky `#0070BA` + Blue `#003087`), PayPal Sans Big/Small, balance card anchor, soft-pill CTAs, circular activity icons color-coded by transaction type
- [**Robinhood**](design-md/robinhood/) — Investing. White canvas + true black text, Capsule Sans tabular-nums, portfolio chart in `#00C805` up / `#FF5000` down with draggable scrubber, black Trade CTA (green/orange reserved for Buy/Sell)
- [**Coinbase**](design-md/coinbase/) — Crypto exchange. Coinbase Blue `#0052FF` + geometric C-mark, Coinbase Sans/Display/Mono (Frere-Jones, 2021), brand-color asset icons (BTC orange, ETH purple), mini sparklines, 4-up Buy/Sell/Send/Receive quad
- [**Apple Wallet**](design-md/apple-wallet/) — Cards + passes. True-black canvas, 80pt-peek vertical card stack with 10pt-radius envelopes, Apple Card titanium gradient `#E8E8EB → #3D3D3F`, SF Pro Display/Text, no tab bar — the stack is the whole app
- [**Etsy**](design-md/etsy/) — Handmade marketplace. Etsy Orange `#F1641E` on warm-cream `#FAF5EF`, handmade product cards, the favorite-heart bounce, shop-front banners, review stars
- [**eBay**](design-md/ebay/) — Auction marketplace. Four-color identity (red `#E53238` / blue `#0064D2` / yellow `#F5AF02` / green `#86B817`), bid-vs-Buy-It-Now badges, watch heart, time-left countdown
- [**Walmart**](design-md/walmart/) — Big-box retail. Walmart Blue `#0071DC` + Spark Yellow `#FFC220`, the blue-on-yellow Rollback price tag, spark logo, category grid, pickup/delivery toggle
- [**Revolut**](design-md/revolut/) — Neobank. Violet→purple gradient `#5B6BFF → #9C6BFF` on cool near-black `#0A0A0F`, metal-card hero with sheen, multi-currency tiles, spend-analytics donut
- [**Wise**](design-md/wise/) — Multi-currency money. Wise Bright Green `#9FE870` + forest `#163300`, multi-currency flag balances, the transparent fee-breakdown card, mid-market rate
- [**Klarna**](design-md/klarna/) — Buy now, pay later. Klarna Pink `#FFB3C7` + near-black text, the pay-in-4 schedule timeline, in-app shopping browser, very-soft 28pt radii

### Productivity

- [**Gmail**](design-md/gmail/) — Email. Google multicolor logo, Gmail Red `#D93025` Compose FAB (16pt squircle), Material You active-indicator pill, swipe archive/delete, Google Sans + Roboto
- [**Notion**](design-md/notion/) — Block-based workspace. Pure white / `#191919` dark, block editor with `/` command palette, 9 muted pastel page backgrounds, user-switchable Inter / Lora / IBM Plex Mono
- [**Figma**](design-md/figma/) — Design + collaboration. Five iconic brand cubes (`#F24E1E` `#FF7262` `#A259FF` `#1ABCFE` `#0ACF83`) for avatars/thumbnails/cursors, Action Blue `#0D99FF` for the real CTA, Inter at 11–16pt density, `#1E1E1E` dark canvas matching the Editor
- [**Apple Notes**](design-md/apple-notes/) — The calmest note-taker. Warm Notes Cream canvas `#FFFBED`, Notes Orange `#F09A38` FAB + folder stroke, yellow folder glyph `#F5D773`, SF Pro 34pt Heavy nav, 17pt 400 body with 1.5 line-height, no tab bar
- [**Todoist**](design-md/todoist/) — Tasks + projects. Todoist Red `#DC4C3E` for FAB + P1 flag + brand, tinted-red FAB shadow, four-tier priority via checkbox stroke (P1 red, P2 `#EB8909`, P3 `#246FE0`, P4 gray), 52pt edge-to-edge task rows, SF Pro system font
- [**Google Calendar**](design-md/google-calendar/) — Scheduling. Material on iOS: Google Blue `#1A73E8` FAB at Material Level 6, Material primaries (Blue/Red/Yellow/Green) + 24-color user palette, Google Sans Display + SF Pro Text hybrid, 36pt Schedule banners with today as filled blue circle
- [**Trello**](design-md/trello/) — Kanban projects. Trello Blue `#0C66E4`, board-background as canvas, horizontal kanban lists, the card drag-lift, label color rows, no bottom tab bar
- [**Linear**](design-md/linear/) — Fast issue tracker. Linear Purple `#5E6AD2` on near-OLED `#08090A`, ultra-dense issue list, the Cmd+K command menu, cycle bars, drawn status iconography
- [**Zoom**](design-md/zoom/) — Video meetings. Zoom Blue `#2D8CFF` in a committed dark `#1A1A1A` theater, the gallery video grid, floating in-call control bar, active-speaker highlight
- [**Microsoft Teams**](design-md/microsoft-teams/) — Work collaboration. Teams Purple `#6264A7`/`#5B5FC7`, the Teams→Channels tree, activity feed, presence dots, meeting join bar, dual light/dark
- [**Things 3**](design-md/things-3/) — Serene tasks. Things Blue `#4F97FF` on pure-white, circular checkbox fill spring, the Magic-Plus, the `#FFD60A` Today star, generous calm whitespace
- [**Obsidian**](design-md/obsidian/) — Knowledge graph. Obsidian Purple `#7C3AED`/`#A78BFA` on charcoal `#1E1E1E`, the physics graph view, backlinks pane, markdown editor, Inter/JetBrains-Mono
- [**Dropbox**](design-md/dropbox/) — Cloud storage. Dropbox Blue `#0061FF` on warm paper-white, colored file-type rows, preview thumbnail grid, the upload FAB, share sheet

### Dating

- [**Tinder**](design-md/tinder/) — Dating swipe cards. Tinder Gradient `#FD267A → #FF6036`, rotated LIKE/NOPE stamps on swipe, 5-button action bar (Rewind / Nope / Super Like / Like / Boost), match-screen pink takeover
- [**Hinge**](design-md/hinge/) — Relationship-intent dating. Cream paper + warm Hinge Black + sacred Rose Gold accent for Standouts/Roses, vertical-scroll profiles with prompt cards in Sailec 24pt Bold, 44pt heart-tap on every reactive surface
- [**Bumble**](design-md/bumble/) — Women-first dating. Bumble Yellow `#FFC629` + pure-black-on-yellow text, hexagon iconography for matches and mode toggles, big confident Brando 500/700/900, 24-hour countdown chip mechanic
- [**Grindr**](design-md/grindr/) — Proximity dating. Grindr Yellow `#FFDE00` on true black, the dense proximity thumbnail cascade, online dots, distance overlays, profile sheet, Taps
- [**OkCupid**](design-md/okcupid/) — Question-based dating. OkCupid Magenta `#E2024F` + indigo `#0500FF`, the match-% badge with count-up, DoubleTake stack, question cards, playful illustration

### AI

- [**ChatGPT**](design-md/chatgpt/) — AI chat. Monochromatic canvas, 32pt black/white circular send button, user-bubble vs inline-assistant asymmetry, full-screen pulsing blue voice-mode sphere, Söhne
- [**Claude**](design-md/claude/) — AI assistant by Anthropic. Warm cream paper canvas + Claude Orange terracotta + Tiempos serif assistant body (the brand's central typographic decision) / Styrene sans for user and chrome, asterisk-star logomark on every message, streaming orange cursor
- [**Perplexity**](design-md/perplexity/) — Answer engine with citations. Dark canvas `#0A0A0A` + Perplexity Teal `#20B8CD` accent, brand-defining inline citation chips `[1][2][3]` paired with a horizontal source-card row above every answer, FK Grotesk + Inter encyclopedic register
- [**Google Gemini**](design-md/gemini/) — AI assistant. Gemini gradient `#4285F4 → #9B72CB → #D96570`, the sparkle mark, document-not-feed conversation (user chip / assistant text), streaming shimmer
- [**Microsoft Copilot**](design-md/copilot/) — AI companion. Copilot gradient `#FF6F61 → #FFB900` + Fluent Blue `#0078D4`, the flourish logo, Fluent acrylic surfaces, tone selector, prompt chips
- [**Grok**](design-md/grok/) — Real-time AI chat. True-black `#000000` monochrome conversation, link-blue `#1D9BF0` as the only chroma, real-time X citation card, regular/fun mode toggle, streaming cursor

### Health & Utility

- [**Cal AI**](design-md/cal-ai/) — AI calorie tracking. Near-black `#0A0A0A` canvas, white-on-black CTAs, macro trio accents (blue/amber/pink), capture FAB with subtle glow
- [**Duolingo**](design-md/duolingo/) — Language learning. Feather Green `#58CC02`, Feather Bold font, mascot-driven gamification, streak + gems + hearts currency
- [**Strava**](design-md/strava/) — Activity tracking + social. Strava Orange `#FC4C02` as the single accent, 4pt route polyline with halo, 3-up DISTANCE/TIME/PACE grid, tabular numerals everywhere, center Record button protruding 8pt with orange glow, coal-black dark mode `#0F0F0F`
- [**Nike Run Club**](design-md/nike-run-club/) — Run coaching. True-black canvas + Volt `#CCFF00` accent on the 280pt progress ring, 88pt Trade Gothic Heavy Condensed elapsed time, hexagonal achievement medals, white-pill primary CTA, heavy haptics on START RUN
- [**Headspace**](design-md/headspace/) — Meditation + sleep. Butter-cream canvas `#FFF7E7`, Marigold `#FF6B35` primary + Aurora gradient (peach → butter → marigold) wash, 234pt breathing meditation sphere on a 12s 4-2-4-2 ease-in-out cycle, Apercu/Nunito title case with 1.5 line-height
- [**MyFitnessPal**](design-md/myfitnesspal/) — Calorie + macro tracking. White canvas + MFP Blue `#005DAA` heritage + Lake Blue `#0072CE` action, 220pt calorie ring that color-flips green → blue → amber → red, locked macro trio (Carbs `#FF9F1C` / Fat `#A463F2` / Protein `#19C37D`)
- [**WHOOP**](design-md/whoop/) — Recovery + strain coaching. Pitch-black canvas `#0A0A0A`, neon Strain Green `#00FF7B`, Recovery ring that interpolates red → yellow → green by score, DIN 2014 ALL CAPS typography, tabular numerals everywhere, neon glows replace shadows
- [**Calm**](design-md/calm/) — Meditation + sleep. Calm Blue `#2A6FD6` night-sky gradient, nature-photo backdrops, the 4-7-8 breathe bubble, Daily Calm card, Lora serif + Inter, sleep-story rows
- [**Oura**](design-md/oura/) — Health ring. Cool-charcoal `#0B0B0F` instrument panel, tri-domain score rings (Readiness teal `#4FD1C5` / Sleep indigo `#7C6FF0` / Activity amber `#F5A623`), contributor bars
- [**Flo**](design-md/flo/) — Cycle tracking. Flo Coral `#FF6B81` + lavender `#C5B3E6`, the rotating cycle-phase wheel, symptom-log chips, reassuring prediction cards, soft rounded surfaces
- [**AllTrails**](design-md/alltrails/) — Hiking + trails. AllTrails Green `#428000`, topo-map base, trail cards with the difficulty color scale (easy/moderate/hard) + length/elevation stats, route-trace draw

## Contributing

Contributions are welcome and genuinely valued — see **[CONTRIBUTING.md](CONTRIBUTING.md)** for the full guide.

| I want to… | Do this |
|---|---|
| **Request an app** | [Open a request](https://github.com/Meliwat/awesome-ios-design-md/issues/new?template=request-an-app.yml) — app name, store link, a few screenshots |
| **Fix a wrong token** | [Report it](https://github.com/Meliwat/awesome-ios-design-md/issues/new?template=token-fix.yml), or open a PR with before/after rationale |
| **Add states or sharpen a spec** | PR against the app's `DESIGN.md` (and its framework companions) |
| **Generate your own** | Drop a screen recording into **[www.spectr.to](https://www.spectr.to)** |

Quality bar: values come from real builds, not marketing shots. Document light **and** dark. Keep HIG/Material correctness (44pt targets, Dynamic Type, safe areas).

## Star history

If these packs save you time, a star helps others find them.

[![Star History Chart](https://api.star-history.com/svg?repos=Meliwat/awesome-ios-design-md&type=Date)](https://star-history.com/#Meliwat/awesome-ios-design-md&Date)

## License

**MIT** — see [LICENSE](LICENSE).

This repository is a curated collection of design-system documents describing publicly available apps. All files are provided "as is" without warranty. Tokens represent values observable in live builds, public brand documentation, and store screenshots. We claim no ownership of any app's visual identity — these documents exist solely to help AI agents generate consistent, platform-native UI.

Brand names, logos, and proprietary typefaces (TT Norms Pro, Uber Move, Airbnb Cereal, Spotify Mix, Feather Bold, Instagram Sans, and others) remain the property of their respective owners and must be licensed separately where required.
