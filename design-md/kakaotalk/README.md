# KakaoTalk iOS Inspired Design System

Design system docs inspired by the [KakaoTalk iOS app](https://apps.apple.com/us/app/kakaotalk/id362057947). Not the official system. Brand colors, font conventions, and component patterns are cross-referenced with KakaoTalk's public product UI and Kakao's brand guidelines; exact private tokens may differ from production.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Framework-neutral design spec (9 sections) |
| `DESIGN-swiftui.md` | SwiftUI implementation — `Color` / `Font` extensions, bubble + side-docked unread mark, friends roster, gifticon, tab bar |
| `DESIGN-expo.md` | Expo / React Native implementation — design tokens, components, Reanimated + Haptics |
| `DESIGN-android.md` | Jetpack Compose implementation — `Color` tokens, Material 3 `Typography`, composables, navigation + Haptics |
| `preview.md` | Link to the interactive design token catalog on Spectr |

## Signature Moves

- **Kakao Yellow** (`#FEE500`) outbound bubbles + primary CTA + speech-bubble logo, with **Kakao Brown** (`#3C1E1E`) text on yellow — never white/black on yellow
- **Side-docked unread mark** — a yellow `#FEE500` "N" + timestamp on the bubble's INNER side (outside the bubble); the most culturally iconic KakaoTalk element in Korea
- **Rounded-square avatars** (≈16pt radius) everywhere — friends, chat, channels — never circles
- **Signature chat backdrop** — blue-gray `#B2C7DA` light / `#1E2A33` dark; not plain white
- **Inbound bubbles** white (light) / `#2E2E2E` (dark), ≈14pt radius with the head corner clipped to ~4pt
- **Kakao Friends emoticons** (Ryan / Apeach / Muzi / Tube) as large bubble-less stickers — first-class content
- **Gifticon cards** — sendable gift vouchers with a product image, brand, name, and barcode "Use" redeem
- **Friends roster** — my-profile banner + "Friends · N" section + rows with status / now-playing badge
- **Status message line** — a mood + emoji under profile/friend names; the social texture of Korean messaging
- **Super-app tab bar** — Friends / Chats / Open / Shopping / More; active = primary text color (filled icon), no yellow tint
- **Heavier titles (weight 900)** for the friendly KakaoBig feel; Hangul in Apple SD Gothic Neo with generous line height
- **Warm dark mode** — canvas `#1A1A1A`, never pure black; yellow bubbles unchanged

## Brand Sources

- [KakaoTalk](https://www.kakaocorp.com/page/service/service/KakaoTalk)
- KakaoTalk on the App Store: <https://apps.apple.com/us/app/kakaotalk/id362057947>
- Public brand palette: Kakao Yellow `#FEE500`, Kakao Brown `#3C1E1E`, signature chat backdrop `#B2C7DA`
- Typeface: Apple system font (SF Pro Text / SF Pro Display) + Apple SD Gothic Neo for Hangul — no custom brand face
