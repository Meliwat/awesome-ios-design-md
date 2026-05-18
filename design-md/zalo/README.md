# Zalo iOS Inspired Design System

Design system docs inspired by the [Zalo iOS app](https://apps.apple.com/us/app/zalo/id579523206). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Zalo's public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, asymmetric bubble `Shape`, chat thread, launcher grid |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, per-corner bubble radii, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, bubble shape + launcher grid + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Single anchor color** — Zalo Blue (`#0068FF`) on every header, primary CTA, active tab, send button, and outgoing-bubble tint
- **White content canvas** (`#FFFFFF`) + calm blue-grey chat backdrop (`#E8ECF1`)
- **Asymmetric chat bubbles** — white incoming with a clipped bottom-left tail, blue-tinted (`#DBEBFF`) outgoing with a clipped bottom-right tail (16pt corners / 5pt tail)
- **Mini-app / service launcher grid** — 4-column rounded-square saturated icon tiles; the super-app hub
- **Solid Zalo Blue header bar** — circular avatar + presence + call/video actions, persists across navigation pushes
- **"Đã xem / Seen" read receipts** + centered day-separator pills
- **Be Vietnam Pro typeface** — designed for Vietnamese diacritics; generous line heights so tone marks never clip
- **Bottom tab bar** — Tin nhắn / Danh bạ / Khám phá / Nhật ký / Cá nhân with red unread badges
- **Notification red** (`#F5325B`) — unread counts & timeline likes only
- **Dense, utilitarian list rows** (72pt chat, 56pt settings) — super-app function over flourish
- **Social timeline (Nhật ký)** — a Facebook-like feed inside the messenger
- **One soft shadow** — only on incoming white bubbles; everything else uses hairline dividers + the blue header for structure

## Brand Sources

- [Zalo](https://zalo.me/)
- [Zalo brand: Zalo Blue `#0068FF`](https://zalo.me/)
- [Be Vietnam Pro (Google Fonts)](https://fonts.google.com/specimen/Be+Vietnam+Pro) — SIL OFL, designed for Vietnamese
- Public brand palette: Zalo Blue `#0068FF`, notification red `#F5325B`, white canvas, blue-grey chat backdrop
