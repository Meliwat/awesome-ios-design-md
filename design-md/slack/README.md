# Slack iOS Inspired Design System

Design system docs inspired by the [Slack iOS app](https://apps.apple.com/us/app/slack/id618783545). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Slack's public brand guidelines and observed UI; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, `ViewModifier`s, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, StyleSheet, Reanimated + Haptics + Drawer |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Slack Aubergine sidebar** (`#4A154B`) — the unmistakable wine-purple default, workspace-customizable
- **Two-zone layout** — saturated workspace sidebar + clean white/charcoal conversation canvas
- **Rounded-square avatars** (4pt corner radius) — Slack's signature deviation from the chat-app circle
- **Slack 4-color logo palette** — Yellow `#ECB22E`, Pink `#E01E5A`, Green `#2EB67D`, Blue `#36C5F0`
- **Emoji reaction pill chips** — blue outline (`#1264A3`) when you've reacted, count next to emoji
- **Threads as first-class** — "5 replies" link opens a slide-in pane (full-screen on iPhone, side on iPad)
- **Mention pills inline** — green @channel, yellow @here, red @user — colored backgrounds, bold text
- **Huddles banner** (`#1264A3`) across top of channel when audio huddle is active
- **Online presence** — 8pt green (`#007A5A`) dot on avatar bottom-right
- **Slack Lato** (proprietary) at weights 400 / 600 / 700 — warm geometric sans, friendly voice
- **Bottom tab bar** — Home, DMs, Activity, Later, Search
- **Status + emoji** next to every user name — 30-char custom status is part of the identity

## Brand Sources

- [Slack Brand Guidelines](https://slack.com/media-kit)
- [Slack 2019 rebrand announcement](https://slack.com/blog/news/say-hello-to-the-new-slack-logo)
- Public brand palette: Aubergine `#4A154B`, Yellow `#ECB22E`, Pink `#E01E5A`, Green `#2EB67D`, Blue `#36C5F0`
