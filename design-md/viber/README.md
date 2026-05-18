# Viber iOS Inspired Design System

Design system docs inspired by the [Viber iOS app](https://apps.apple.com/us/app/viber-messenger-chats-calls/id382617920). Not the official system. Brand colors, font names, and component patterns are cross-referenced with Viber's (Rakuten) public product UI and brand assets; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, bubble + receipt + banner, sample views |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, themed components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, bubble + 3-state receipt + free-call banner + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Viber Purple is the entire brand** — `#7360F2` on the outgoing bubble, every CTA, tab selection, send button, and call screen; no second accent
- **Aubergine-tinted dark mode** — canvas `#121118` carries a purple cast, NOT neutral gray
- **Gray/purple bubble pair** — outgoing purple `#7360F2`, incoming gray `#EDEBF5` (light) / `#26232F` (dark), ~16pt radius with a ~5pt tail
- **Three-state check receipt** — one gray check = sent, two gray = delivered, two violet `#8F7DF7` = seen (the recolor is the signature moment)
- **Free Viber Call banner** — purple→deep-violet `#7360F2 → #59267C` gradient; call-first DNA
- **Sticker market** — a full storefront in the keyboard; stickers render as ~96pt borderless in-thread artwork
- **Call-first chrome** — phone + video icons in every chat header, a dedicated Calls tab
- **Green presence** `#46C26A` online dot / accept-call; **red** `#F0506E` decline + unread badges
- **Reactions strip** — thumbs-up-led emoji row on long-press, docked as a chip
- **Deep-violet `#59267C` gradient reserved** — only the call screen and free-call banner
- **Friendly heavy type** — rounded humanist sans (Manrope substitute), 700/800 titles & buttons
- **Bottom tabs** — Chats / Calls / Explore / More with purple active tint and no Material pill

## Brand Sources

- [Viber (Rakuten)](https://www.viber.com/)
- [Viber on the App Store](https://apps.apple.com/us/app/viber-messenger-chats-calls/id382617920)
- [Manrope by Mikhail Sharanda](https://fonts.google.com/specimen/Manrope) — SIL OFL (free Viber-brand substitute)
- Public brand palette: Viber Purple `#7360F2`, deep `#665CAC`, violet `#8F7DF7`, banner `#59267C`, green `#46C26A`, red `#F0506E`
