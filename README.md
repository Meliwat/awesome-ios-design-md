<div align="center">

# Awesome iOS DESIGN.md

**Production-grade design systems for the 200 best apps.**
**Framework-neutral plus SwiftUI, Expo, and Jetpack Compose. Hand one to your AI agent — ship pixel-matched UI.**

<br/>

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![Apps](https://img.shields.io/badge/apps-200-10b981?style=flat-square)](#collection)
[![Flavors](https://img.shields.io/badge/flavors-neutral%20%C2%B7%20SwiftUI%20%C2%B7%20Expo%20%C2%B7%20Compose-6E56CF?style=flat-square)](#four-flavors-per-app)
[![Live gallery](https://img.shields.io/badge/live%20gallery-spectr.to-0A84FF?style=flat-square)](https://www.spectr.to/gallery)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-22C55E?style=flat-square)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)

<br/>

[**Browse the live gallery**](https://www.spectr.to/gallery) &nbsp;·&nbsp; [**Quick start**](#quick-start) &nbsp;·&nbsp; [**The 200 apps**](#collection) &nbsp;·&nbsp; [**Request an app**](https://github.com/Meliwat/awesome-ios-design-md/issues/new/choose)

</div>

---

Hand your AI coding agent a `DESIGN.md` and it stops guessing. Each file is a complete, reverse-engineered design system for a real app — **exact hex values, full type ramps, component states, motion curves, and haptics** — in plain markdown that Claude, Cursor, Codex, and Stitch read natively. No Figma exports. No JSON. No tooling. No setup.

> **Want an app that's not here?** [Request it in one click](https://github.com/Meliwat/awesome-ios-design-md/issues/new/choose) — or generate your own from a screen recording at **[www.spectr.to](https://www.spectr.to)**.

> Inspired by [awesome-design-md](https://github.com/VoltAgent/awesome-design-md), reimagined for native mobile. All trademarks, logos, and visual identities remain the property of their respective owners — these documents exist so agents can generate UI that feels native to each platform.

## Quick start

1. **Browse the [live gallery](https://www.spectr.to/gallery)** — all 200 apps, every screen, rendered free in your browser.

2. **Open the app you want** — e.g. [spectr.to/gallery/spotify](https://www.spectr.to/gallery/spotify).

3. **Get the full pack** and drop it next to your `CLAUDE.md` / `AGENTS.md`, then prompt your agent:

   > *"Build the Now Playing screen using `DESIGN-swiftui.md` for all styling — match the palette, type ramp, spacing, and motion exactly."*

Your agent now has the `#121212` canvas, the Spotify-green play button, the 4/8pt grid, the spring curves, and the haptics — not a vibe, the actual spec.

## What is DESIGN.md?

[DESIGN.md](https://stitch.withgoogle.com/docs/design-md/overview/) is a plain-text design system document that AI agents read to generate consistent UI. Just a markdown file. No Figma exports, no JSON schemas, no special tooling. Drop it into your project root and any AI coding agent (Claude, Cursor, Codex, Stitch) instantly understands how your screens should look.

| File | Who reads it | What it defines |
|------|-------------|-----------------|
| `AGENTS.md` | Coding agents | How to build the project |
| `DESIGN.md` | Design agents | How the app should look and feel |

**The full DESIGN.md packs — colors, SF Pro type ramps, component specs, SF Symbol usage, Dynamic Type behavior, HIG-correct layouts, haptics and motion — are delivered through the [Spectr gallery](https://www.spectr.to/gallery).** This repo is the index and free screen preview for all 200.

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

**200 packs across 11 categories**, organized exactly like the [live gallery](https://www.spectr.to/gallery). Every entry links to its folder; tap any phone in the gallery to see the tokens rendered.

| Category | | Category | |
|---|--:|---|--:|
| [Social](#social) | 23 | [Finance](#finance) | 22 |
| [Messaging](#messaging) | 16 | [Fitness](#fitness) | 19 |
| [Travel](#travel) | 20 | [Productivity](#productivity) | 22 |
| [Music](#music) | 14 | [Dating](#dating) | 12 |
| [Video](#video) | 16 | [Misc](#misc) | 20 |
| [Food](#food) | 16 | **Total** | **200** |


### Social

- [**Instagram**](design-md/social/instagram/) — Photo & video social. Monochrome chrome, 10-stop brand gradient reserved for Stories + Create, icon-only tab bar, true-black OLED dark mode
- [**TikTok**](design-md/social/tiktok/) — Short-form video. Full-bleed vertical FYP, chromatic-aberration logo (cyan + red), right-side action rail, signature glitch aesthetic
- [**X (Twitter)**](design-md/social/x-twitter/) — Real-time text-first social. Pure black OLED canvas, X Blue `#1D9BF0` reserved for links, Chirp font, column-centered feed, per-action color micro-animations
- [**Threads**](design-md/social/threads/) — Text-forward Meta social. Pure black canvas, Instagram-coral like heart, thread-line indentation, no hashtags/trending, Instagram Sans
- [**Facebook**](design-md/social/facebook/) — Social network. Facebook Blue `#1877F2`, `#F0F2F5` feed background, 7-emoji Reactions popover with drag-select, profile cover hero
- [**Snapchat**](design-md/social/snapchat/) — Camera-first social. Snap Yellow `#FFFC00`, 5-screen swipe nav, 82pt yellow capture button, color-coded snap types, Avenir Next
- [**Reddit**](design-md/social/reddit/) — Communities & discussion. Reddit Orange-Red `#FF4500`, upvote-orange `#FF8717` / downvote `#7193FF` vote column, flat post cards, 10pt-per-level comment indentation
- [**Pinterest**](design-md/social/pinterest/) — Visual discovery. Pinterest Red `#E60023`, masonry 2-column grid with variable heights, floating Save-button morph, Pinterest Sans
- [**LinkedIn**](design-md/social/linkedin/) — Professional network. Cream canvas `#F3F2EF`, LinkedIn Blue `#0A66C2`, 6-reaction picker, Premium gold avatar frame, Open-to-Work green ring
- [**BeReal**](design-md/social/bereal/) — Authentic dual-camera social. Stark pure-black `#000000` canvas, no brand accent (white-only), dual front+back composite card, 2-minute countdown, RealMojis, zero vanity metrics
- [**Mastodon**](design-md/social/mastodon/) — Federated social. Mastodon Purple `#6364FF`, `@user@instance` handles, content-warning spoilers, home/local/federated timelines, boost-with-spin, light + `#191B22` dark
- [**Bluesky**](design-md/social/bluesky/) — Custom-feeds social. Bluesky Blue `#1185FE`, butterfly mark, switchable pinned custom feeds, reply-control chips, three themes (light / dim `#1E2936` / dark `#0B0F14`)
- [**Nextdoor**](design-md/social/nextdoor/) — Hyperlocal community. Nextdoor Green `#00B246`, warm cream `#FAF9F6` canvas, neighborhood feed, map-with-pins, verified-neighbor badge, Lato
- [**Tumblr**](design-md/social/tumblr/) — Creative blogging social. Deep-navy `#001935` canvas, multi-accent trio (blue `#00B8FF` / green `#00CF35` / pink `#FF4930`), reblog chains, tag bar, notes count
- [**Quora**](design-md/social/quora/) — Q&A knowledge. Quora Red `#B92B27`, serif-question / sans-answer split (Georgia + Inter), blue `#2E69FF` upvote pill, credential bylines, Spaces
- [**VSCO**](design-md/social/vsco/) — film-grade photo & video editor. Pure-black darkroom canvas (`#000000`), monochrome chrome with hairline bipolar sliders on a `#3A3A3A` track, a white-ringed film-preset carousel, and UPPERCASE wide-tracked Inter labels — zero brand accent, the photo is the only color.
- [**Clubhouse**](design-md/social/clubhouse/) — live-audio social rooms. Warm cream "hallway" canvas (`#F2EFE4`) with DM Serif room titles, a grid of circular speaker avatars, and an emerald speaking-pulse ring (`#38B569` + `rgba(56,181,105,0.18)` halo) — all-pill controls, warm `#1A1A1A` dark mode, no blue anywhere.
- [**Lemon8**](design-md/social/lemon8/) — Lifestyle & social discovery. Bright editorial white canvas (`#FFFFFF`), two-column staggered masonry feed of magazine-cover cards, sparing Lemon8 Yellow (`#FFE600`) paired only with ink `#1A1A1A`, and a pastel topic-tag system (mint `#E4F5EC`, blush `#FCE8EE`, sky `#E6F0FB`).
- [**Flickr**](design-md/social/flickr/) — Photo sharing & community. Near-black gallery canvas (`#0C0D0E`), justified photo mosaic with 3pt gutters, twin brand dots (Pink `#FF0084` + Blue `#0063DC`), and a monospace EXIF camera-detail table.
- [**Vimeo**](design-md/social/vimeo/) — cinematic creator video platform. Near-black screening-room canvas (#0D0E12) with a single Vimeo Blue accent (#00ADEF), the iconic Staff Pick gold star (#FFD24C), and a 14pt-corner auto-fading player.
- [**Vero**](design-md/social/vero/) — calm, no-algorithm chronological social. Pure true-black canvas (#000000) with a single teal-to-blue brand gradient (#00D1C1 to #0079D3), flat teal accents (#00C2B8), and a 7 first-class post-type selector.
- [**Gas**](design-md/social/gas/) — joyful anonymous compliments for schools. Full-bleed indigo-to-purple gradient (#6C5CE7 to #A06CFF) behind a big white poll card, four fixed festive choice colors, and the flame #FF7A45 currency loop.
- [**Locket**](design-md/social/locket/) — warm, widget-first photos from your closest friends. Cream-gold world (#FFF7EC / #FFB02E) with a square full-bleed friend photo, a big white capture button with a gold ring, and a 4-up photo-history grid.

### Messaging

- [**WhatsApp**](design-md/messaging/whatsapp/) — Encrypted chat. WhatsApp Green `#25D366`, mint-leaf outgoing bubbles `#D9FDD3`, voice-message waveform, blue double-check read receipts
- [**Telegram**](design-md/messaging/telegram/) — Fast, themeable chat. Telegram Blue `#0088CC`, 17pt bubbles with 6pt notch tails, silent-send long-press, Lottie animated emoji, floating voice mini-player
- [**Discord**](design-md/messaging/discord/) — Community chat. Blurple `#5865F2`, three-gray surface system `#1E1F22`/`#2B2D31`/`#313338`, server rail with squircle-morph on active, role-colored usernames, gg sans
- [**Slack**](design-md/messaging/slack/) — Work chat. Aubergine `#4A154B` sidebar (workspace-customizable), rounded-square avatars, Huddles banner, reaction pill chips, Slack Lato
- [**Signal**](design-md/messaging/signal/) — Privacy-first messaging. Single Signal Blue `#3A76F0` accent, disappearing-message timer chips, sealed-sender glyphs, zero-vanity minimalism, light + `#1B1B1B` dark
- [**Messenger**](design-md/messaging/messenger/) — Rich chat. Multi-stop brand gradient `#0A7CFF → #9D4EDD → #FF5CA0` conversation-anchored bubbles, chat-head avatars, 6-emoji reactions popover, active dots
- [**LINE**](design-md/messaging/line/) — Sticker-first messaging. LINE Green `#06C755`, periwinkle `#8CABD9` chat backdrop, oversized bubble-less stickers, official-account badges, super-app tabs
- [**WeChat**](design-md/messaging/wechat/) — Super-app messaging. WeChat Green `#07C160`, gray `#EDEDED` system canvas, tailed `#95EC69` bubbles, the Discover grouped-list hub, Moments, red-packet card
- [**iMessage**](design-md/messaging/imessage/) — Apple's system messaging surface. Blue/gray bubble pair with outgoing iMessage Blue (#007AFF), SMS Green (#34C759) fallback, incoming gray (#E9E9EB / #26262A), the six-glyph tapback strip, pulsing typing dots, and true-black (#000000) OLED dark mode.
- [**Viber**](design-md/messaging/viber/) — Rakuten's call-first messenger. Single-brand Viber Purple (#7360F2) outgoing bubbles, gray incoming (#EDEBF5 / #26232F), the three-state check receipt (gray → gray → violet #8F7DF7 seen), a purple→deep-violet (#59267C) free-call banner, and an aubergine-tinted (#121118) dark canvas.
- [**Skype**](design-md/messaging/skype/) — Microsoft's call-first messenger. Cloud-blue brand (bright #00AFF0 buttons/FABs, deep #0078D4 outgoing bubble), gray incoming (#EBEBEF / #2A2A30), inline call cards, a gradient video-call grid with circular controls, presence dots (green #2DC26B / away #FFC400), and a neutral near-black (#16161A) dark canvas.
- [**Kik**](design-md/messaging/kik/) — Username-first anonymous messenger. Modern Kik Blue (#00B0F0) outgoing bubbles with dark-cyan ink (#002A36), gray incoming (#E9EAEC / #25282D), the single-letter S/D/R receipt (Read turns #00B0F0), first-class bots, circular Kik Code plates, and a neutral near-black (#121316) dark canvas (heritage Kik Green #82BC23 noted).
- [**GroupMe**](design-md/messaging/groupme/) — Friendly group-messaging design system. Solid GroupMe-Blue chat nav bar (`#00AFF0`), blue outbound bubbles with neutral inbound (`#F0F0F0` / `#2A2A2C`), a docked heart-like count pill (`#FF3B5C`), circular gradient avatars, and per-group color themes.
- [**KakaoTalk**](design-md/messaging/kakaotalk/) — Korea’s super-app messenger design system. Kakao Yellow outbound bubbles (`#FEE500`) on Kakao Brown text (`#3C1E1E`), the iconic side-docked yellow unread "N", rounded-square avatars, and a blue-gray chat backdrop (`#B2C7DA` / `#1E2A33`).
- [**Zalo**](design-md/messaging/zalo/) — Messaging super-app. Single Zalo Blue anchor (`#0068FF`) on every header, CTA, and active tab, white canvas over a calm blue-grey chat backdrop (`#E8ECF1`), asymmetric clipped-tail bubbles (incoming `#FFFFFF`, outgoing `#DBEBFF`), and a mini-app service launcher grid.
- [**Threema**](design-md/messaging/threema/) — Secure messaging. Single Threema Green `#088A29` accent (brightened to `#1FA53C` on dark) + the signature red/orange/green trust-level three-dot indicator `#E5453A` / `#EF8B2C` / `#15A33A`, no phone number, monospace Threema ID, QR-first in-person verification, soft-green outgoing bubbles `#D6F0DC` / `#0C5E22`

### Travel

- [**Airbnb**](design-md/travel/airbnb/) — Stays + Experiences. Rausch / `#FF385C` coral, Airbnb Cereal font, photography-first stay cards, category chip rail, subtle card shadows
- [**Uber**](design-md/travel/uber/) — Ride-hailing + delivery. Stark monochrome (black/white), Uber Move font, map-as-hero layout, Base design system tokens
- [**Google Maps**](design-md/travel/google-maps/) — Navigation. Google Blue `#4285F4` route polyline, map-as-hero with floating bottom sheet drawer, color-coded pins, turn-by-turn immersive mode
- [**Apple Maps**](design-md/travel/apple-maps/) — Apple's native navigation. Cream parchment cartography `#F6F1E6` land + `#B9D9EB` water, Maps Blue `#0A84FF` directions polyline, pulsing current-location puck, `.regularMaterial` blur on the sliding search card (88/50/full detents), SF Pro tabular figures
- [**Waze**](design-md/travel/waze/) — Community navigation. Waze Purple `#7E55BE` + Cyan `#33CCFF` electric duo, speech-bubble hazard reports with triangular tails, cyan arrow puck (not a dot), bright cyan water + mint parks, Boing typography with SF Pro Rounded fallback
- [**Lyft**](design-md/travel/lyft/) — Ride-hailing. Single Lyft Pink `#FF00BF` accent, map-as-hero with no tab bar, very-rounded springy ride-type selector sheet (Wait & Save / Standard / XL / Lux Black)
- [**Booking.com**](design-md/travel/booking/) — Travel booking. Booking navy `#003580` + bright-blue `#0071C2` action + yellow `#FEBB02`, the review-score badge, dense property cards, Genius banner
- [**Tripadvisor**](design-md/travel/tripadvisor/) — Travel reviews. Tripadvisor Green `#34E0A1` + owl black, the 5-circle bubble rating, traveler-photo place cards, Travelers' Choice badge
- [**Hopper**](design-md/travel/hopper/) — Price-prediction travel. Hopper Red `#FA4747` + bunny, the buy/wait price-prediction calendar heatmap, watch-price toggle, fare cards
- [**Flighty**](design-md/travel/flighty/) — Flight tracking. Flighty Blue `#0A84FF` on deep-black `#0B0B0F`, live great-circle flight arc, on-time stats, Live-Activity status bar, delay timeline
- [**Expedia**](design-md/travel/expedia/) — Travel booking. Expedia Yellow `#FFC94D` savings accent + Action Blue `#1668E3` + navy `#00355F` review-score badge, the search-and-results loop, Bundle + Save, One Key gold `#F5C518` earn lines
- [**KAYAK**](design-md/travel/kayak/) — Travel metasearch. KAYAK Orange `#FF690F` as the single scarce accent over a near-neutral canvas, the color-coded price calendar (green `#1E9E5A` / red `#E5484D`), forecast banner, fare-compare matrix, and Hacker Fares
- [**Vrbo**](design-md/travel/vrbo/) — Vacation rentals. Vrbo Blue `#245ABC` primary action (brightened to `#4F8BF0` on dark for links/pins/outline) + Sky Blue `#1D6FB8` support + one gold review star `#F2B01E`, full-bleed swipeable photo galleries with a "1 / 42" counter, a sticky heavy-price Book now bar, whole-home framing, and blue/gray/gold map price pins on a cool blue-charcoal `#101317` dark canvas
- [**Marriott Bonvoy**](design-md/travel/marriott-bonvoy/) — Hotel loyalty. Deep Bonvoy navy `#16264A` / near-black `#1C1C1C` base with disciplined gold `#B3852A` (brightened to `#D2A23E` on dark) reserved for member value only — points balances, elite tiers, redemption, the gold Book CTA — plus big hotel heros with gold small-caps sub-brand eyebrows, points-aware rate cards, and the navy+gold Mobile Key on a `#121214` dark canvas
- [**Delta**](design-md/travel/delta/) — Airline travel concierge, boarding-pass-first. Navy→red boarding pass (Delta Blue `#003268` → widget red `#C8102E`), on-time green `#1E8E5A` status timeline, SkyMiles gold `#C99700`.
- [**United**](design-md/travel/united-airlines/) — Airline travel app, flight-detail-first. Two-blue brand (United Blue `#002244` + Rhapsody Blue `#1414FF`), perforated boarding pass, interactive seat map, Premier gold `#C2A14D`.
- [**Southwest**](design-md/travel/southwest/) — Open-seating airline. The Heart tri-color (Southwest Blue `#304CB2`, Bold Red `#E51D23`, Warm Yellow `#F9B612`) over a deep navy canvas `#0E1726`, with the giant A/B/C boarding position as the hero, a 24-hour check-in countdown ring, and the Wanna Get Away fare ladder
- [**Turo**](design-md/travel/turo/) — Peer-to-peer car sharing. The vehicle photo is the product on a near-black canvas `#0F0F12`, with Turo Purple `#593BFB` as the single booking action, Turo Teal `#5CE0B8` as the trust/value accent (All-Star Host, instant-book), a teal-iconed specs strip, and a sticky price + Book bar
- [**Skyscanner**](design-md/travel/skyscanner/) — Flight search and price comparison. Single Sky Blue action `#0770E3`, traffic-light price grid (cheap `#00A698`, average `#FFB81C`, expensive `#E5392E`), Everywhere destination, on a cool navy-black `#0B0F14` dark canvas.
- [**Citymapper**](design-md/travel/citymapper/) — Multimodal transit and live navigation. Theme-invariant mode colors (walk `#00B894`, bus `#E8453C`, tube `#2B5BFF`), the colored leg strip, and the shape-locked green GO pill `#00C281`, on a deep blue-black `#0C0E14` dark canvas.

### Music

- [**Spotify**](design-md/music/spotify/) — Music streaming. Dark `#121212` canvas, Spotify Green `#1DB954` as the single accent, album-art-driven Now Playing gradient via dynamic color extraction
- [**Apple Music**](design-md/music/apple-music/) — Music + lyrics. Apple Music Red `#FA2D48`, SF Pro native, time-synced word-by-word lyrics, Dolby Atmos badge, `.regularMaterial` tab bar
- [**SoundCloud**](design-md/music/soundcloud/) — Creator audio. SoundCloud Orange `#FF5500`, commentable waveform scrubber with inline timestamped avatars, repost feed, up-next queue
- [**TIDAL**](design-md/music/tidal/) — Hi-fi music. Pure-black `#000000` canvas, cyan `#00FFFF` HiFi accents, MASTER/HiFi quality badges, radically flat square-art tonal-elevation layout
- [**Shazam**](design-md/music/shazam/) — Music recognition. Radial-blue gradient `#0050FF → #0088FF`, giant central pulsing tap-to-listen button with concentric rings, glass result card, no tab bar
- [**Audible**](design-md/music/audible/) — Audiobooks. Audible Orange `#FF9900` on warm charcoal `#1A1A1A`, Playfair/Inter pairing, progress-ring cover, speed-dial + 30s-skip player, Captions
- [**Pandora**](design-md/music/pandora/) — Music Genome radio. The thumb up/down is the product on a deep blue-navy canvas `#0B0F1C` from Pandora Blue `#224099`, with bright blue `#3668FF` as the constant accent, an asymmetric solid-up / hollow-down thumb, an album-art gradient Now Playing with no card frame, and the genome station list as home
- [**Deezer**](design-md/music/deezer/) — Music streaming, gradient dark. Violet near-black #0F0D13 canvas with a scarce purple-to-pink gradient (#A238FF to #FF0092) lighting only the 68pt circular play button, the scrubber fill, and the Flow badge; the signature is the living-gradient Flow artwork with an embedded white equalizer and pink now-playing rows.
- [**YouTube Music**](design-md/music/youtube-music/) — Music streaming, immersive dark. Near-black `#030303` canvas with a blurred album-art glow behind the player, YT red `#FF0000` chrome, and a white `#FFFFFF` play button — the signature Song/Video toggle sits under the art.
- [**Amazon Music**](design-md/music/amazon-music/) — Music streaming, deep teal dark. A teal-navy `#0C1B22` canvas with the signature X-Ray synced-lyrics panel (current line in bright cyan `#25D1DA`) and a cyan `#00A8E1` glow play button on a top-down player gradient.
- [**iHeartRadio**](design-md/music/iheartradio/) — Live radio & podcasts, warm dark. Maroon-black #120A0E canvas with a scarce red-to-magenta system (#C6002B to #E40A5D, coral #F23A2F) lighting the 68pt circular play button and a pulsing LIVE badge; the signature is the live-station player with a heart-logomark tile and an indeterminate scanning bar instead of a scrubber (live radio cannot seek).
- [**Bandcamp**](design-md/music/bandcamp/) — Music marketplace, paper editorial. Light #FFFFFF and #F4F4F4 canvas where big square album art is the hero and a scarce Bandcamp Teal #1DA0C3 marks artist links, the buy price, and the collection; the signature is the album page with a name-your-price buy-and-support card and a social fan-collection feed.
- [**Pocket Casts**](design-md/music/pocket-casts/) — premium podcast player with per-podcast theming. Pocket Casts Red `#F43E37` on near-black `#1A1A1A`, cover-art theme tint `#E0533C`, circular transport, and a reorderable Up Next queue.
- [**Overcast**](design-md/music/overcast/) — audio-first podcast player with Smart Speed. Overcast Orange `#FC7E0F` on warm paper-cream `#FBFAF6` (or true dark `#121212`), an outlined orange-ring play button, and first-class Smart Speed / Voice Boost toggles.

### Video

- [**YouTube**](design-md/video/youtube/) — Video platform. YouTube Red `#FF0000`, 16:9 thumbnails with duration tag, Subscribe morph, mini-player drag-down, Shorts rail, YouTube Sans + Roboto
- [**Netflix**](design-md/video/netflix/) — Streaming. Netflix Red `#E50914` on `#141414` canvas, hero trailer auto-play, Top 10 numeral row, Continue Watching progress bar, Netflix Sans
- [**Disney+**](design-md/video/disney-plus/) — Premium streaming. Deep space-navy `#0A0E2A` starfield, Disney Blue `#0063E5` + glow `#1A75FF`, brand-portal tile row (Disney/Pixar/Marvel/Star Wars/Nat Geo), 16:9 rails
- [**Hulu**](design-md/video/hulu/) — Content-dense streaming. Electric Hulu Green `#1CE783` Watch CTA on near-black `#0B0C0F`, 16:9 tiles, hub chips, continue-watching progress
- [**Max**](design-md/video/max/) — Prestige streaming. Gradient wordmark `#0046FF → #7B2FF7` on deep-purple `#12053A`, auto-trailer billboard, prestige rows, profile gate
- [**Twitch**](design-md/video/twitch/) — Live streaming. Twitch Purple `#9146FF` action + Live Red `#EB0400` liveness-only, LIVE pills, docked chat column, theater overlay, emotes
- [**Prime Video**](design-md/video/prime-video/) — Video on demand. Prime Blue `#00A8E1` on Amazon blue-slate `#0F171E`, hero billboard, the X-Ray cast slide-up overlay, channel rows
- [**Peacock**](design-md/video/peacock/) — Video streaming, indigo-black dark. A permanently-dark `#0A0A14` canvas warming to violet `#140E26` behind a full-bleed hero billboard, with the five-color peacock-feather swoosh (`#FACC15 → #F97316 → #EC4899 → #8B5CF6 → #2563EB`) as the only accent, a white `#FFFFFF` Play CTA, and NBC live red `#E5142B` badges.
- [**Paramount+**](design-md/video/paramount-plus/) — Video streaming, midnight-navy dark. A dark-only `#0A0E2D` navy canvas lifting to `#0E1438` under a full-bleed hero billboard, with a network brand-hubs row, one electric Paramount+ Blue `#0064FF` action color, a sky-blue `#4D9DFF` active-tab tint, and broadcast live red `#FF2D46` badges.
- [**Apple TV**](design-md/video/apple-tv/) — Video streaming, true-black minimal. A pure `#000000` OLED canvas with an inset rounded floating hero, the Up Next rail of 16:9 thumbnails with white resume bars, one system-blue `#0A84FF` accent for links and the active tab, a white Play CTA, and MLS Season Pass hot-pink `#ED1A6F`.
- [**Crunchyroll**](design-md/video/crunchyroll/) — cinematic anime streaming platform. True-black OLED canvas (#000000) with a single Crunchyroll Orange accent (#F47521), full-bleed key-art heroes with a #000-fading scrim, and a resume-aware episode list with orange progress bars and Sub | Dub badges.
- [**Plex**](design-md/video/plex/) — calm personal-media server platform. Cool charcoal canvas (#1F2326, never true black) with a single Plex Yellow accent (#E5A00D) and dark #1A1304 button text, an On Deck resume rail with yellow progress bars, and a multi-server status picker.
- [**ESPN**](design-md/video/espn/) — fast live-first sports scoreboard platform. Near-black canvas (#0E0F11, never pure black) with a single ESPN Red accent (#D50A0A), a pinned horizontal scores ticker, pulsing #FF1A1A LIVE badges, and the winning team's score in green (#1FAA59) with tabular numerals.
- [**Tubi**](design-md/video/tubi/) — Free ad-supported streaming. Indigo-black canvas (#0A0A2A) with the signature purple-to-magenta gradient (#7408FF -> #FF00FF), white #FFFFFF Play button, dense 2:3 poster rows, and a yellow #FFD400 "FREE" tag.
- [**Pluto TV**](design-md/video/pluto-tv/) — Free ad-supported live TV. Navy canvas (#0B0F1F) built around a frozen-frame EPG channel guide where the "On Now" cell lifts to #1C2440 with a 3pt #FFE100 yellow left border; Pluto Blue #0048FF CTAs and a #FF3B5C live indicator.
- [**Kick**](design-md/video/kick/) — Creator-first live streaming. Near-black canvas (#0E0E10) with one screaming electric-green brand color (#53FC18) on every CTA; signature video-plus-streamer-bar-plus-chat watch page with role-colored usernames (mod #3EA6FF, sub #FFC700, VIP #FF4FD8) and a red #FF1F44 LIVE pill.

### Food

- [**DoorDash**](design-md/food/doordash/) — On-demand delivery. DoorDash Red `#EB1700` for every checkout, photo-first 16:10 merchant cards, floating pill CTA, TT Norms Pro bold hierarchy
- [**Starbucks**](design-md/food/starbucks/) — Coffee + rewards. Starbucks Green `#00704A`, gold Stars progress ring, Short/Tall/Grande/Venti/Trenta size selector, QR pay, SoDo Sans
- [**Uber Eats**](design-md/food/uber-eats/) — Food delivery. Uber Eats Green `#06C167`, clean white canvas, photo-first restaurant cards, category pills, sticky cart bar, live map order tracking
- [**Instacart**](design-md/food/instacart/) — Grocery delivery. Instacart Green `#0AAD0A` + carrot `#FF7009`, aisle browse, the − n + qty stepper, replacement-preference flow, green cart bar
- [**Chipotle**](design-md/food/chipotle/) — QSR ordering. Chipotle Red `#A81612` on cream kraft `#FFF5E1`, build-your-burrito ingredient stepper, ALL-CAPS Archivo headers, rewards ring
- [**Domino's**](design-md/food/dominos/) — Pizza ordering. Domino's Red `#E31837` action + Blue `#006491` info, 5-stage pizza tracker, build-your-pizza topping layout, deal cards
- [**Grubhub**](design-md/food/grubhub/) — Food delivery. Grubhub Red `#F63440` primary action (pressed `#D2202B`) + Grubhub Orange `#FF8000` for deals and savings + a gold `#FFB81C` Grubhub+ Perks badge, wide 16:9 restaurant photography in 14pt rounded cards, a green rating star `#18A957`, and a live order-tracking timeline on a calm Track Blue `#00A0DF` accent over a warm `#121212` dark canvas
- [**Deliveroo**](design-md/food/deliveroo/) — Food delivery. One brand color — Deliveroo Teal `#00CCBC` (pressed `#00A99C`) — for the kangaroo logo, every button, ratings, Plus and the active tab, with deep Teal Ink `#003733` labels on teal fills, wide 5:3 restaurant photo cards, a signature delivery-fee pill, a floating teal `+` on menu thumbnails, and promo gold `#FFC100` for deals only, over a warm `#121212` dark canvas
- [**Zomato**](design-md/food/zomato/) — Food delivery and dining. Zomato Red `#E23744` for brand, primary action and "ADD" (pressed `#C42531`), a semantic color-coded rating pill — green `#267E3E` great, amber `#DB7C38` average, red `#E23744` poor — the India-mandated veg/non-veg mark (green dot vs maroon `#B91C1C` triangle), a bordered red ADD button that morphs into a stepper, and a Delivery / Dining Out toggle re-theming a hero-plus-pull-up-card detail screen over a warm `#121212` dark canvas
- [**Swiggy**](design-md/food/swiggy/) — appetite-driving food delivery and quick-commerce platform. A single Swiggy Orange accent (#FC8019) over a near-black canvas (#121212), photo-forward restaurant cards with a #000-fading bottom scrim and bold offer text, a green rating chip (#48C479), and a floating ADD button that morphs into a stepper.
- [**McDonald's**](design-md/food/mcdonalds/) — bold, deal-driven QSR loyalty and mobile-order platform. A two-color Golden Arches Yellow (#FFC72C, always with #1A1A1A text) and McDonald's Red (#DA291C) system over a near-black canvas (#121212), with a points-hero rewards card, a product-photo deals grid, and an elevated yellow Order FAB.
- [**Taco Bell**](design-md/food/taco-bell/) — neon, dark-first build-your-box QSR ordering platform. A purple-to-magenta brand gradient (#702082 → #C72BC8) over a violet-tinted near-black canvas (#0E0A14), with a Hot Sauce Yellow value highlight (#FFC700), a gradient box-preview hero, numbered step pills, and magenta single-select controls.
- [**Panera**](design-md/food/panera/) — Food and beverage ordering. Panera Green `#4C8B2B` as the single action color (brightened to `#6BBE45` on dark for CTAs/active tabs/rewards arc) on a bakery-warm cream canvas `#F4EFE1` (warm near-black `#14140F` dark), full-color soup/sandwich/salad photo cards at 14pt radius, a green-gradient MyPanera rewards card with a progress bar + Unlimited Sip Club status, a gold reward star `#F2B705`, fully-rounded pill buttons, a quantity stepper, a pickup/delivery toggle, and a sticky green checkout bar
- [**OpenTable**](design-md/food/opentable/) — Restaurant reservations. A single disciplined OpenTable Red `#DA3743` (brightened to `#F2545B` on dark for the Reserve CTA/active tab/selected slot) on a bright white canvas (clean charcoal `#121212` dark), a tappable reservation time-slot grid (10pt chips, recommended slot filled red), a gold review star `#E8A33D`, a teal Dining Points accent `#1F8A8A` annotating 1,000-point off-peak slots, a diner-green `#2FA86A` Booked-N-times-today social-proof pill, fully-rounded pill controls, and a sticky red Reserve-for-time bar
- [**Resy**](design-md/food/resy/) — Restaurant reservations. A black-first editorial system on a pure-black canvas `#000000` where Playfair-style display-serif restaurant names contrast with clean Inter UI, a single disciplined Resy Red `#C73E3A` (brightened to `#E2504B` for the active tab on black) drives booking, available reservation slots are red-OUTLINED while the primary slot is SOLID red, sold-out times become dashed amber `#D99A2B` Notify (waitlist) chips, a muted gold `#C9A24B` carries the /10 rating + Hit List, and hairline `#262626` borders replace shadows
- [**Yelp**](design-md/food/yelp/) — Local business reviews. One rationed Yelp Red `#FF1A1A` (logo `#D32323`) for the primary CTA + active tab, a warm orange-red five-star rating `#F25C05` (identical light and dark, half-star increments) as the brand asset, a full-bleed business photo header with a "1 / 248 photos" counter, Recommended Reviews cards with Useful/Funny/Cool votes, 8px gray `#F0F0F0` / dark `#0C0C0C` section bands instead of cards, and green/red `#2DA44E`/`#E03E3E` open-closed status on a near-black `#161616` dark canvas

### Finance

- [**Venmo**](design-md/finance/venmo/) — Social payments. Venmo Blue `#008CFF`, public emoji-laden transaction feed, Pay/Request split pill, animated sent-checkmark
- [**Cash App**](design-md/finance/cash-app/) — Mobile money. Pure-black canvas + single Cash Green `#00D632` accent, Cash Sans / Cash Sans Mono, 96pt $-amount keypad, icon-only tab bar, matte black Cash Card
- [**PayPal**](design-md/finance/paypal/) — Online payments. Dual-blue P-P wordmark (Sky `#0070BA` + Blue `#003087`), PayPal Sans Big/Small, balance card anchor, soft-pill CTAs, circular activity icons color-coded by transaction type
- [**Robinhood**](design-md/finance/robinhood/) — Investing. White canvas + true black text, Capsule Sans tabular-nums, portfolio chart in `#00C805` up / `#FF5000` down with draggable scrubber, black Trade CTA (green/orange reserved for Buy/Sell)
- [**Coinbase**](design-md/finance/coinbase/) — Crypto exchange. Coinbase Blue `#0052FF` + geometric C-mark, Coinbase Sans/Display/Mono (Frere-Jones, 2021), brand-color asset icons (BTC orange, ETH purple), mini sparklines, 4-up Buy/Sell/Send/Receive quad
- [**Apple Wallet**](design-md/finance/apple-wallet/) — Cards + passes. True-black canvas, 80pt-peek vertical card stack with 10pt-radius envelopes, Apple Card titanium gradient `#E8E8EB → #3D3D3F`, SF Pro Display/Text, no tab bar — the stack is the whole app
- [**Etsy**](design-md/finance/etsy/) — Handmade marketplace. Etsy Orange `#F1641E` on warm-cream `#FAF5EF`, handmade product cards, the favorite-heart bounce, shop-front banners, review stars
- [**eBay**](design-md/finance/ebay/) — Auction marketplace. Four-color identity (red `#E53238` / blue `#0064D2` / yellow `#F5AF02` / green `#86B817`), bid-vs-Buy-It-Now badges, watch heart, time-left countdown
- [**Walmart**](design-md/finance/walmart/) — Big-box retail. Walmart Blue `#0071DC` + Spark Yellow `#FFC220`, the blue-on-yellow Rollback price tag, spark logo, category grid, pickup/delivery toggle
- [**Revolut**](design-md/finance/revolut/) — Neobank. Violet→purple gradient `#5B6BFF → #9C6BFF` on cool near-black `#0A0A0F`, metal-card hero with sheen, multi-currency tiles, spend-analytics donut
- [**Wise**](design-md/finance/wise/) — Multi-currency money. Wise Bright Green `#9FE870` + forest `#163300`, multi-currency flag balances, the transparent fee-breakdown card, mid-market rate
- [**Klarna**](design-md/finance/klarna/) — Buy now, pay later. Klarna Pink `#FFB3C7` + near-black text, the pay-in-4 schedule timeline, in-app shopping browser, very-soft 28pt radii
- [**Chime**](design-md/finance/chime/) — Mobile banking. Chime Mint `#1EC677` / `#00D67E` as the entire brand personality, a full `#1EC677 → #12A862` gradient balance hero with the amount at 44pt 700 in near-black mint ink `#062014` plus a mint-glow shadow, a SpotMe fee-free-overdraft banner, slide-down instant transaction alerts with a solid-mint check icon, spending kept neutral ink (red `#FF6B6B` only for declined/negative), Posted/Pending/SpotMe/Declined status chips, and heavily rounded geometry on a deep green-charcoal `#0E1411` dark canvas
- [**SoFi**](design-md/finance/sofi/) — All-in-one fintech. A dark-native brand: electric SoFi Blue `#00A0DF` (brightened to `#29C2FF` for accents/links/active tabs) on deep navy `#0A0E27`, a signature `#00A0DF → #1B53C4 → #0A0E27` member-hero gradient with net worth in white at 40pt 800 plus a green month-change pill, a consolidated 2-column account/product tile grid with colored sub-stats, cross-sell product cards, color-coded performance (gain-green `#2FD08A`, loss-coral `#FF6B6B`, rewards-gold `#F2C14E`), and fully pill-shaped buttons over lifted navy surfaces `#121736` / `#1B2147`
- [**Monzo**](design-md/finance/monzo/) — Neobank with the iconic Hot Coral card. Coral-and-navy palette (`#FF3464` card hero on `#14233C` navy), round Pot coins with savings goals, and a day-grouped emoji transaction feed with income in green `#2FCB8F`.
- [**Nubank**](design-md/finance/nubank/) — Latin America's purple neobank. Full-bleed Nu Purple hero (`#820AD1`) over a calm tile stack on deep aubergine `#15101C`, the Roxinho credit card as a `#9B2BE0` gradient, pill buttons, and Pix-first quick actions with positive green `#2ED47A`.
- [**Acorns**](design-md/finance/acorns/) — Micro-investing with spare change. Dual brand Acorns Oak `#5A2B82` + Acorn Green `#6FBF4E`, a sweeping multi-segment allocation donut, the purple Round-Ups card (`#5A2B82 → #7B43A8`), and a Found Money cashback feed on deep aubergine `#14121A`.
- [**Fidelity**](design-md/finance/fidelity/) — Institutional brokerage and portfolio tracking. Fidelity Green `#368727` + Heritage Green `#00754A` on a calm evergreen dark canvas `#0E1411`, a 34pt tabular account-value hero with sparkline and range tabs, flat hairline holding rows, and sacred gain `#15B374` / loss `#E5544B` numerals with a sticky green Trade bar.
- [**Charles Schwab**](design-md/finance/charles-schwab/) — Confident full-service brokerage and trading. Schwab Blue `#009DDC` (with dark-navy `#002233` on-color text) on a maritime-navy canvas `#0A1622`, a navy-gradient `#003B5C → #002A42` Total Value hero, flat hairline account rows, the segmented Buy/Sell trade ticket centerpiece, and sacred gain `#18B07B` / loss `#E2564E` tabular numerals.
- [**Webull**](design-md/finance/webull/) — Dark-native retail trading terminal. A near-black canvas `#0B0E11` with the Webull blue→cyan gradient `#1B9EFB → #20D5C4`, a full-bleed candlestick chart, a depth-bar order-book ladder, an options chain, and pervasive saturated up `#00C076` / down `#FA5252` color on a split Buy/Sell docked pair.
- [**Binance**](design-md/finance/binance/) — Dark-native crypto exchange terminal. Single Binance Yellow `#F0B90B` accent on a near-black `#0B0E11` canvas, absolute market green `#0ECB81` / red `#F6465D`, a dense markets list with colored percentage pills, and a depth-shaded order book over IBM Plex Mono tabular figures.
- [**Crypto.com**](design-md/finance/crypto-com/) — Premium navy crypto super-app. A two-blue system (brand navy `#002D74` / `#103F68` for hero, electric `#1199FA` for interaction) on a navy-tinted `#03060F` canvas, the brushed-metal tiered Visa card with gold `#C8A24A` embossing, and a floating center Trade tab over Roboto Mono money.

### Fitness

- [**Strava**](design-md/fitness/strava/) — Activity tracking + social. Strava Orange `#FC4C02` as the single accent, 4pt route polyline with halo, 3-up DISTANCE/TIME/PACE grid, tabular numerals everywhere, center Record button protruding 8pt with orange glow, coal-black dark mode `#0F0F0F`
- [**Nike Run Club**](design-md/fitness/nike-run-club/) — Run coaching. True-black canvas + Volt `#CCFF00` accent on the 280pt progress ring, 88pt Trade Gothic Heavy Condensed elapsed time, hexagonal achievement medals, white-pill primary CTA, heavy haptics on START RUN
- [**Headspace**](design-md/fitness/headspace/) — Meditation + sleep. Butter-cream canvas `#FFF7E7`, Marigold `#FF6B35` primary + Aurora gradient (peach → butter → marigold) wash, 234pt breathing meditation sphere on a 12s 4-2-4-2 ease-in-out cycle, Apercu/Nunito title case with 1.5 line-height
- [**MyFitnessPal**](design-md/fitness/myfitnesspal/) — Calorie + macro tracking. White canvas + MFP Blue `#005DAA` heritage + Lake Blue `#0072CE` action, 220pt calorie ring that color-flips green → blue → amber → red, locked macro trio (Carbs `#FF9F1C` / Fat `#A463F2` / Protein `#19C37D`)
- [**WHOOP**](design-md/fitness/whoop/) — Recovery + strain coaching. Pitch-black canvas `#0A0A0A`, neon Strain Green `#00FF7B`, Recovery ring that interpolates red → yellow → green by score, DIN 2014 ALL CAPS typography, tabular numerals everywhere, neon glows replace shadows
- [**Calm**](design-md/fitness/calm/) — Meditation + sleep. Calm Blue `#2A6FD6` night-sky gradient, nature-photo backdrops, the 4-7-8 breathe bubble, Daily Calm card, Lora serif + Inter, sleep-story rows
- [**Oura**](design-md/fitness/oura/) — Health ring. Cool-charcoal `#0B0B0F` instrument panel, tri-domain score rings (Readiness teal `#4FD1C5` / Sleep indigo `#7C6FF0` / Activity amber `#F5A623`), contributor bars
- [**Flo**](design-md/fitness/flo/) — Cycle tracking. Flo Coral `#FF6B81` + lavender `#C5B3E6`, the rotating cycle-phase wheel, symptom-log chips, reassuring prediction cards, soft rounded surfaces
- [**AllTrails**](design-md/fitness/alltrails/) — Hiking + trails. AllTrails Green `#428000`, topo-map base, trail cards with the difficulty color scale (easy/moderate/hard) + length/elevation stats, route-trace draw
- [**Fitbit**](design-md/fitness/fitbit/) — Supportive health dashboard. Fitbit Teal `#00B0B9` (brightening to `#21D9CE` on dark) on a teal-black `#001017` canvas, a per-metric color system (heart-rate coral `#FF6B81`, sleep purple `#7C5CFF`, readiness lime `#B8E986`), and the steps progress-ring hero with big-number-first DM Sans tiles.
- [**Garmin Connect**](design-md/fitness/garmin-connect/) — Fitness tracking and activity analysis. True-black canvas `#000000` with a single Garmin Blue accent `#007CC3` (brightened to `#2A9FD6` on black), a glowing GPS route line, a condensed tabular stat grid, Training Status ring, Body Battery gauge `#2EA8E0`, and the green-to-red HR-zone ramp (`#4CAF50 → #E5402A`).
- [**Peloton**](design-md/fitness/peloton/) — Boutique fitness streaming and live classes. Pure-black studio canvas `#000000` with a single Peloton Red accent `#DF1E2E` (brightened to `#FF4B57` on black), cinematic class cards, a pulsing red LIVE badge, the in-class output ring, fixed metric colors (Cadence `#E5402A`, Resistance `#F0A030`, Strive/HR `#3DB8E0`), and a real-time leaderboard with your row washed in red.
- [**Apple Fitness**](design-md/fitness/apple-fitness/) — Activity tracking and Fitness+ streaming. True-black canvas `#000000` with the three Activity rings as the entire brand (Move `#FA114F`, Exercise `#92E82A`, Stand `#1EE4E1`) on 22%-opacity tracks, grouped system cards `#1C1C1E`, Apple’s label-opacity text ramp, a Move-pink chrome accent `#FF375F`, and a cinematic Fitness+ shelf with frosted-glass overlays.
- [**Hevy**](design-md/fitness/hevy/) — Workout logger built around the live set table. Single-accent Hevy Blue `#1E6FFF` on a near-black training canvas `#0E1116`, completed sets washing green `#2FBF71`, an auto-starting rest-timer pill, and a gold `#F5B83D` PR badge that fires when you beat a record.
- [**Strong**](design-md/fitness/strong/) — Minimalist workout logger built around the set-log table. Single-accent Strong Blue `#2F80ED` on a flat neutral-grey canvas `#1A1A1A`, logged sets filling green `#27AE60`, a slim auto-running rest-timer bar, flat borderless cards, and an amber `#F2C94C` PR flag on records.
- [**Zwift**](design-md/fitness/zwift/) — Virtual cycling built around a full-bleed 3D world with a glassmorphic ride HUD. Zwift Orange `#FC6719` brand action over `rgba(12,12,12,0.6)` glass tiles, a fixed metric color mapping (power orange, cadence yellow `#E8C547`, HR red `#F0413E`, W/kg cyan `#2BD4D9`), tall condensed Barlow numerals, and an orange route-progress banner.
- [**Sleep Cycle**](design-md/fitness/sleep-cycle/) — Sleep tracking with a smart alarm. Indigo→night gradient `#2A2D5A → #3B4371` over a deep `#14152E` canvas, the signature glowing aqua hypnogram wave (`#7FE3F0` over `#6C7BFF` fill), a score-banded sleep-quality ring, and color-coded stages (Deep `#3D4ABF`, REM `#4FD1E6`).
- [**Noom**](design-md/fitness/noom/) — Psychology-led weight loss and food logging. Noom Blue `#2A5DF6` + Noom Teal `#1FC29B`, the bold blue→teal daily psychology lesson card, the sacred green/yellow/red food-color system (`#34C759` / `#FFC531` / `#FF5A52`), and a Coach Purple `#7B61FF` chat surface.
- [**Runna**](design-md/fitness/runna/) — Structured running training plans. Two-color brand of Runna Indigo `#4F46E5` (structure) + electric Lime `#C2F94E` (go), a color-coded plan week strip, the Indigo guided run-session card (`#4F46E5 → #3A33B8`), and a warm-up→cooldown structure bar on an Indigo-tinted `#0E0E16` canvas.

### Productivity

- [**Notion**](design-md/productivity/notion/) — Block-based workspace. Pure white / `#191919` dark, block editor with `/` command palette, 9 muted pastel page backgrounds, user-switchable Inter / Lora / IBM Plex Mono
- [**Figma**](design-md/productivity/figma/) — Design + collaboration. Five iconic brand cubes (`#F24E1E` `#FF7262` `#A259FF` `#1ABCFE` `#0ACF83`) for avatars/thumbnails/cursors, Action Blue `#0D99FF` for the real CTA, Inter at 11–16pt density, `#1E1E1E` dark canvas matching the Editor
- [**Apple Notes**](design-md/productivity/apple-notes/) — The calmest note-taker. Warm Notes Cream canvas `#FFFBED`, Notes Orange `#F09A38` FAB + folder stroke, yellow folder glyph `#F5D773`, SF Pro 34pt Heavy nav, 17pt 400 body with 1.5 line-height, no tab bar
- [**Todoist**](design-md/productivity/todoist/) — Tasks + projects. Todoist Red `#DC4C3E` for FAB + P1 flag + brand, tinted-red FAB shadow, four-tier priority via checkbox stroke (P1 red, P2 `#EB8909`, P3 `#246FE0`, P4 gray), 52pt edge-to-edge task rows, SF Pro system font
- [**Google Calendar**](design-md/productivity/google-calendar/) — Scheduling. Material on iOS: Google Blue `#1A73E8` FAB at Material Level 6, Material primaries (Blue/Red/Yellow/Green) + 24-color user palette, Google Sans Display + SF Pro Text hybrid, 36pt Schedule banners with today as filled blue circle
- [**Trello**](design-md/productivity/trello/) — Kanban projects. Trello Blue `#0C66E4`, board-background as canvas, horizontal kanban lists, the card drag-lift, label color rows, no bottom tab bar
- [**Linear**](design-md/productivity/linear/) — Fast issue tracker. Linear Purple `#5E6AD2` on near-OLED `#08090A`, ultra-dense issue list, the Cmd+K command menu, cycle bars, drawn status iconography
- [**Zoom**](design-md/productivity/zoom/) — Video meetings. Zoom Blue `#2D8CFF` in a committed dark `#1A1A1A` theater, the gallery video grid, floating in-call control bar, active-speaker highlight
- [**Microsoft Teams**](design-md/productivity/microsoft-teams/) — Work collaboration. Teams Purple `#6264A7`/`#5B5FC7`, the Teams→Channels tree, activity feed, presence dots, meeting join bar, dual light/dark
- [**Things 3**](design-md/productivity/things-3/) — Serene tasks. Things Blue `#4F97FF` on pure-white, circular checkbox fill spring, the Magic-Plus, the `#FFD60A` Today star, generous calm whitespace
- [**Obsidian**](design-md/productivity/obsidian/) — Knowledge graph. Obsidian Purple `#7C3AED`/`#A78BFA` on charcoal `#1E1E1E`, the physics graph view, backlinks pane, markdown editor, Inter/JetBrains-Mono
- [**Dropbox**](design-md/productivity/dropbox/) — Cloud storage. Dropbox Blue `#0061FF` on warm paper-white, colored file-type rows, preview thumbnail grid, the upload FAB, share sheet
- [**Asana**](design-md/productivity/asana/) — project & task management. The task list IS the product: 20pt completion circles that fill green (#62D26F) on tap, a single coral accent (#F06A6A) for the FAB and active tab, multicolor object pills as ~16% tints (plum #4573D2, aqua #4ECBC4), and a charcoal dark canvas (#1E1F21).
- [**monday.com**](design-md/productivity/monday/) — work management & project OS. The colorful board IS the product: full-bleed saturated status cells (Done #00C875, Working on it #FDAB3D, Stuck #E2445C), a 6pt leading group-color stripe on every row, blue (#0073EA) as the only action color, and a deep-indigo night canvas (#181B34).
- [**ClickUp**](design-md/productivity/clickup/) — all-in-one productivity & project management. The 3-stop brand gradient (purple #7B68EE → pink #FD71AF → blue #49CCF9) is reserved for the squircle FAB, primary CTA, and AI surfaces; the task list groups by solid custom-status pills, priority is a colored flag triangle, all over a deep blue-violet canvas (#1B1B2E).
- [**Jira**](design-md/productivity/jira/) — project & issue tracking. Atlassian-neutral canvas (light `#FFFFFF`/`#F7F8F9`, dark `#1D2125`) with the single brand Jira Blue `#0052CC` for actions, Navy `#172B4D` ink, semantic status lozenges, and the drag-to-transition sprint board.
- [**Evernote**](design-md/productivity/evernote/) — note-taking & knowledge capture. Calm neutral canvas (near-white light, true near-black `#1C1C1E` dark) with the single brand Evernote Green `#00A82D` for FAB/checkboxes/links, warm ink `#1C2B33`, green tag pills, and a 1.6 reading rhythm.
- [**Bear**](design-md/productivity/bear/) — Markdown notes and writing app. Calm charcoal canvas (#21252B) with live in-place Markdown, inline #hashtag tokens and a single red-to-orange brand gradient (#E0566F to #FF8A65); every interactive glyph is Bear Orange (#FF8A65).
- [**Craft**](design-md/productivity/craft/) — docs & notes editor. The blue→purple gradient (#2F5BEA → #6E56CF) carries every primary action and the rounded-square FAB; structural blocks render as soft-shadowed cards with tinted icon chips on a warm canvas (#FCFCFD light / #1A1A1E dark), titles in Inter 28pt extrabold, plus a first-class Daily Note card.
- [**Miro**](design-md/productivity/miro/) — collaboration whiteboard. An infinite pinch-zoom canvas with a zoom-scaled dotted grid; Miro Yellow (#FFD02F) on Miro Ink (#050038) is the brand, while Miro Blue (#4262FF) is reserved for selection; tight 4pt-radius pastel sticky notes (default #FEF3B6) lift off the board with soft shadows, and a floating toolbar fills the active tool yellow.
- [**Canva**](design-md/productivity/canva/) — Design and creativity tool. Bright workspace (#FFFFFF / dark #18191B) with the cyan-to-purple brand gradient (#00C4CC to #7D2AE8) on the logo, primary CTA and a centered lifted create FAB; full-color template galleries with gold Pro badges (#FFC24B).
- [**Grammarly**](design-md/productivity/grammarly/) — writing assistant & grammar checker. One brand accent — Grammarly Green (#15C39A → #11A683) — carries every CTA, the animated document score ring, and the assistant orb; suggestion underlines are color-coded by category (Correctness red #E5484D, Clarity blue #3B82F6, Engagement green #16A34A, Delivery purple #8B5CF6); the suggestion card slides up from the bottom over a warm charcoal canvas (#121212).

### Dating

- [**Tinder**](design-md/dating/tinder/) — Dating swipe cards. Tinder Gradient `#FD267A → #FF6036`, rotated LIKE/NOPE stamps on swipe, 5-button action bar (Rewind / Nope / Super Like / Like / Boost), match-screen pink takeover
- [**Hinge**](design-md/dating/hinge/) — Relationship-intent dating. Cream paper + warm Hinge Black + sacred Rose Gold accent for Standouts/Roses, vertical-scroll profiles with prompt cards in Sailec 24pt Bold, 44pt heart-tap on every reactive surface
- [**Bumble**](design-md/dating/bumble/) — Women-first dating. Bumble Yellow `#FFC629` + pure-black-on-yellow text, hexagon iconography for matches and mode toggles, big confident Brando 500/700/900, 24-hour countdown chip mechanic
- [**Grindr**](design-md/dating/grindr/) — Proximity dating. Grindr Yellow `#FFDE00` on true black, the dense proximity thumbnail cascade, online dots, distance overlays, profile sheet, Taps
- [**OkCupid**](design-md/dating/okcupid/) — Question-based dating. OkCupid Magenta `#E2024F` + indigo `#0500FF`, the match-% badge with count-up, DoubleTake stack, question cards, playful illustration
- [**Coffee Meets Bagel**](design-md/dating/coffee-meets-bagel/) — curated dating & relationships. A cozy coffee-house palette — brew-brown (#A0522D) + bagel-orange (#F4623A) + cream (#F3E4CF) over a roasted brown-black canvas (#14100D); one curated Today's Bagel card at a time (24pt radius, full-bleed photo) with a noticeably oversized bagel-orange Like, a roasted-brown Pass, and a brew-brown Send a Bagel; cream replaces pure white everywhere and a match fires an orange→brown takeover with bagel confetti.
- [**Plenty of Fish**](design-md/dating/plenty-of-fish/) — Dating and social app. POF Blue (#0098DB to #00A6E2) on the logo, primary CTA, active tab and like button over a cool near-black canvas (#121417); a 2-column photo match grid, the Meet Me card, teal online dots (#00C9B7) and pink unread badges (#FF4F8B).
- [**Match**](design-md/dating/match/) — Dating and social. A full-bleed profile-photo system on a warm near-black canvas `#141414` where the entire screen is one person and a bottom `rgba(20,20,20,0.95)` gradient scrim carries the name/age/chips, a single disciplined Match Red `#E92434` (energized to `#F0203E` for the Like button and active tab on dark) drives every action, the solid-red circular Like is deliberately the loudest object in a five-button floating action dock, functional accents stay strict (green `#2ECC71` presence, blue `#1FB6FF` Super Like/verified, gold `#D6A75B` Premium, purple `#B36BD8` Boost), the profile name is bold 700 with the age a lighter 500 beside it, interest chips are frosted translucent pills, the "It's a Match!" moment is a full-screen maroon-to-ink `#2A1418 → #1A1A1A` celebration, and a heavy blur is the Likes You paywall
- [**The League**](design-md/dating/the-league/) — Dating and social. A black-tie members-club system on a near-true-black canvas `#0A0A0A` (dark-native, no light mode in spirit) where a single rationed gold `#C8A35A` (bright `#DBBA71`, deep `#A8863F`) does ALL accent work — crest, hairlines, one CTA, credential marks — and depth comes entirely from 1px gold-tinted hairline borders `#2A2620` with zero drop shadows, the prospect card reads like an embossed calling card with a Cormorant-style display-serif name over a clean Jost-style sans credential stack (the serif/sans split IS the hierarchy), the concierge is a recurring character speaking in italic serif behind a 2px gold left rule, scarcity is made visual via a finite daily "Today's Batch · 7 Prospects" banner, corner radii stay sharp (2-6pt) with no pills, labels are quiet letter-spaced uppercase, the Heart action is a gold OUTLINE not a fill, and a match is a drawn champagne `#E8DCC0` wax seal, not confetti
- [**Raya**](design-md/dating/raya/) — Members-only dating, uncompromisingly monochrome. Pure black canvas `#000000` + pure white `#FFFFFF` as the only accent, a music-scored slideshow profile with segmented `#FFFFFF` story bars, a white music ticker + equalizer, and an editorial caption over a `→ rgba(0,0,0,0.92)` scrim; depth is a 1px `#2E2E2E` hairline, never a shadow.
- [**Feeld**](design-md/dating/feeld/) — Open-minded dating, dark-native. True-black canvas `#000000` with one rationed acid-yellow `#E8FF63` accent, desire pink `#FF5C8A`, a full-bleed 28pt Discover card with couple-aware names and a heavy `rgba(0,0,0,0.72)→0.92` gradient, plus the three-state Desire chip system.
- [**happn**](design-md/dating/happn/) — Crossed-paths dating, timeline-first. happn pink `#FF4865` fading to magenta `#E91E63` as connective tissue across a connector-line Crossings feed, location+time stamps, the one-tap Charm heart (shadow `rgba(255,72,101,0.5)`), teardrop map pins, and a reserved Crush gold `#FFC24B` celebration on a graphite `#0E0E12` canvas.

### Misc

- [**ChatGPT**](design-md/misc/chatgpt/) — AI chat. Monochromatic canvas, 32pt black/white circular send button, user-bubble vs inline-assistant asymmetry, full-screen pulsing blue voice-mode sphere, Söhne
- [**Claude**](design-md/misc/claude/) — AI assistant by Anthropic. Warm cream paper canvas + Claude Orange terracotta + Tiempos serif assistant body (the brand's central typographic decision) / Styrene sans for user and chrome, asterisk-star logomark on every message, streaming orange cursor
- [**Perplexity**](design-md/misc/perplexity/) — Answer engine with citations. Dark canvas `#0A0A0A` + Perplexity Teal `#20B8CD` accent, brand-defining inline citation chips `[1][2][3]` paired with a horizontal source-card row above every answer, FK Grotesk + Inter encyclopedic register
- [**Gmail**](design-md/misc/gmail/) — Email. Google multicolor logo, Gmail Red `#D93025` Compose FAB (16pt squircle), Material You active-indicator pill, swipe archive/delete, Google Sans + Roboto
- [**Amazon**](design-md/misc/amazon/) — E-commerce. Amazon Yellow `#FF9900` Add-to-Cart, Deep Navy `#131921` top nav, PDP price block with superscript cents, Prime badge, Amazon Ember
- [**Cal AI**](design-md/misc/cal-ai/) — AI calorie tracking. Near-black `#0A0A0A` canvas, white-on-black CTAs, macro trio accents (blue/amber/pink), capture FAB with subtle glow
- [**Duolingo**](design-md/misc/duolingo/) — Language learning. Feather Green `#58CC02`, Feather Bold font, mascot-driven gamification, streak + gems + hearts currency
- [**Google Gemini**](design-md/misc/gemini/) — AI assistant. Gemini gradient `#4285F4 → #9B72CB → #D96570`, the sparkle mark, document-not-feed conversation (user chip / assistant text), streaming shimmer
- [**Microsoft Copilot**](design-md/misc/copilot/) — AI companion. Copilot gradient `#FF6F61 → #FFB900` + Fluent Blue `#0078D4`, the flourish logo, Fluent acrylic surfaces, tone selector, prompt chips
- [**Grok**](design-md/misc/grok/) — Real-time AI chat. True-black `#000000` monochrome conversation, link-blue `#1D9BF0` as the only chroma, real-time X citation card, regular/fun mode toggle, streaming cursor
- [**Character.AI**](design-md/misc/character-ai/) — Immersive conversation with an AI persona. Near-black canvas `#0F0F10`, asymmetric bubbles set apart by a 4pt tucked corner (AI `#1C1C1F` / user `#26262A`) rather than color, italic `*roleplay actions*` in muted `#9A9AA2`, and a single rationed accent blue `#3A7BFD` on the send button and CTAs.
- [**Midjourney**](design-md/misc/midjourney/) — AI image generation, gallery-first. A true-black OLED canvas (#000000) with no brand accent — white (#FFFFFF) is the only primary; the 2x2 image grid carries 100% of the color while chrome stays a grey ramp (#A1A1A1 / #6E6E6E); prompts render in monospace and the Discord-heritage U1-U4 (white) / V1-V4 (dark #1A1A1A) chip grammar drives every refinement.
- [**DeepSeek**](design-md/misc/deepseek/) — AI reasoning chat assistant. A near-black canvas (#0E0E10) with exactly one accent — DeepSeek Blue (#4D6BFE) on the whale mark, user bubble, and active DeepThink/Search toggles; the signature is the reasoning trace: a recessed #16171A panel (darker than the canvas) with a 2pt blue left-bar holding the R1 chain-of-thought in dimmed italic #8A8B90, subordinate to an upright #ECECEC answer.
- [**GitHub**](design-md/misc/github/) — Developer tooling, the Primer system on mobile. Dark default canvas `#0D1117` with one accent blue `#2F81F7` and a reserved green `#238636` for Code/Merge only, the iconic contributions heatmap (`#161B22 → #39D353`), semantic state pills (open `#3FB950` / merged `#A371F7` / closed `#F85149`), and a monospace code browser + diff viewer.
- [**Vercel**](design-md/misc/vercel/) — developer deployment platform. The deployments list IS the product: a true-black #000000 canvas (not charcoal), a status traffic-light (Ready #0CCE6B, Building #F5A623, Error #E5484D) as the only systemic color, Geist Mono for every URL and commit hash, hairline #2E2E2E borders instead of shadows, and a white #EDEDED fill button with no brand accent.
- [**Postman**](design-md/misc/postman/) — API client and testing console. The request builder IS the product: a neutral-grey #1A1A1A canvas, the HTTP-method color system (GET #6BDD9A, POST #FFE47A, PUT #74AEF6, DELETE #F79090) read before text everywhere a method appears, a single Postman Orange #FF6C37 reserved for the Send button and active tab, and a syntax-colored JSON viewer that sinks onto a darker #161616 surface.
- [**Life360**](design-md/misc/life360/) — family safety and location sharing. The family map IS the product: a deep-violet night canvas (#161325) behind a dark-styled map (#1A2138) so colored member pins pop, a fixed per-member identity color (amber #F2A33C, teal #2DD4BF, pink #F472B6) used on every pin, avatar and trail, Life360 Purple #7E57C2 as the brand spine for Circles and Places, and SOS red #FF6B6B rationed strictly for real safety events.
- [**Goodreads**](design-md/misc/goodreads/) — books, reviews & reading tracking. Warm tan paper canvas (`#F4F1EA` light, ink-brown `#161310` dark) with literary brown `#382110` chrome, full-color cover grids, the load-bearing amber five-star rating `#E9A100`, a green Want-to-Read CTA `#409D69`, teal links `#00635D`, and a serif 1.6 reading rhythm.
- [**Kindle**](design-md/misc/kindle/) — e-reading & digital library. A chrome-free reading page (default Sepia paper `#FBF0D9` / warm ink `#5F4B32`, five user themes incl. dark `#2A2A2A` / black `#000000`) with the single Amazon Orange `#FF9900` accent for CTAs and progress, a cover-grid library with orange progress bars, the Aa panel, and a justified 1.72 reading rhythm.
- [**Substack**](design-md/misc/substack/) — newsletters & long-form publishing. A clean white serif reading page (`#FFFFFF` light, soft charcoal `#121212` dark) with reading ink `#1F1F1F`, the single rationed Substack Orange `#FF6719` for the Subscribe CTA / active tab / unread dot / liked heart, an orange-tint paywall card, a chronological subscription inbox, and a 1.65 editorial reading rhythm.

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
