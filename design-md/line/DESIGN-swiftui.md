# LINE (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates LINE's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the periwinkle backdrop, text bubbles, and the signature oversized bubble-less sticker.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Chat backdrop & canvas
    static let lineBackdrop    = Color(red: 0.549, green: 0.671, blue: 0.851) // #8CABD9 (periwinkle)
    static let lineCanvas      = Color.white                                  // #FFFFFF
    static let lineSurface     = Color(red: 0.969, green: 0.969, blue: 0.969) // #F7F7F7
    static let lineDivider     = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5
    static let lineBackdropDk  = Color(red: 0.102, green: 0.133, blue: 0.188) // #1A2230

    // MARK: - Text
    static let lineTextPrimary   = Color.black                                // #000000
    static let lineTextSecondary = Color(red: 0.549, green: 0.549, blue: 0.549) // #8C8C8C
    static let lineTextTertiary  = Color(red: 0.702, green: 0.702, blue: 0.702) // #B3B3B3

    // MARK: - Brand
    static let lineGreen        = Color(red: 0.024, green: 0.780, blue: 0.333) // #06C755
    static let lineGreenPressed = Color(red: 0.020, green: 0.651, blue: 0.278) // #05A647
    static let lineGreenWash    = Color(red: 0.945, green: 0.984, blue: 0.953) // #F1FBF3

    // MARK: - Message
    static let lineIncoming    = Color.white                                  // #FFFFFF
    static let lineOutgoing    = Color(red: 0.773, green: 0.941, blue: 0.694) // #C5F0B1 (pale chat-green)

    // MARK: - Semantic
    static let lineNotif = Color(red: 1.000, green: 0.200, blue: 0.294)        // #FF334B
    static let lineLink  = Color(red: 0.122, green: 0.561, blue: 1.000)        // #1F8FFF
}
```

## 2. Typography

LINE uses **Noto Sans** at weights 400/700 (no semibold tier). Bundle the TTFs via `Info.plist` (`UIAppFonts`); fall back to the system font for CJK coverage.

```swift
extension Font {
    static let lineLargeTitle  = Font.custom("NotoSans-Bold",    size: 24)
    static let lineFriendName  = Font.custom("NotoSans-Regular", size: 16)
    static let lineThreadTitle = Font.custom("NotoSans-Bold",    size: 17)
    static let lineMessageBody = Font.custom("NotoSans-Regular", size: 16)
    static let lineStatus      = Font.custom("NotoSans-Regular", size: 13)
    static let linePreview     = Font.custom("NotoSans-Regular", size: 14)
    static let lineSection     = Font.custom("NotoSans-Bold",    size: 13)
    static let lineTimestamp   = Font.custom("NotoSans-Regular", size: 12)
    static let lineReadLabel   = Font.custom("NotoSans-Regular", size: 11)
    static let lineButton      = Font.custom("NotoSans-Bold",    size: 16)
    static let lineButtonText  = Font.custom("NotoSans-Bold",    size: 15)
    static let lineTab         = Font.custom("NotoSans-Regular", size: 10)
    static let lineBadge       = Font.custom("NotoSans-Bold",    size: 11)
    static let lineStickerPx   = Font.custom("NotoSans-Bold",    size: 13)
}

extension Font {
    static func line(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Oversized Bubble-less Sticker (Signature)

```swift
struct StickerMessage: View {
    let image: Image
    let time: String
    let isOutgoing: Bool
    let read: Bool
    @State private var landed = false

    var body: some View {
        HStack(alignment: .bottom, spacing: 6) {
            if isOutgoing {
                Spacer(minLength: 0)
                metaView
            }
            image
                .resizable()
                .scaledToFit()
                .frame(maxWidth: 140, maxHeight: 140)   // bubble-less, oversized
                .scaleEffect(landed ? 1 : 0.6)
                .onAppear {
                    withAnimation(.spring(response: 0.4, dampingFraction: 0.55)) { landed = true }
                }
            if !isOutgoing {
                metaView
                Spacer(minLength: 0)
            }
        }
        .padding(.horizontal, 12)
        .padding(.vertical, 10) // stickers need air
    }

    private var metaView: some View {
        VStack(alignment: isOutgoing ? .trailing : .leading, spacing: 1) {
            if isOutgoing && read { Text("Read").font(.lineReadLabel).foregroundStyle(Color.lineTextSecondary) }
            Text(time).font(.lineTimestamp).foregroundStyle(Color.lineTextSecondary)
        }
    }
}
```

### Text Bubble (on the periwinkle backdrop)

```swift
struct TextBubble: View {
    let text: String
    let time: String
    let isOutgoing: Bool
    let read: Bool

    var body: some View {
        HStack(alignment: .bottom, spacing: 6) {
            if isOutgoing { Spacer(minLength: 40); metaView }
            Text(text)
                .font(.lineMessageBody)
                .foregroundStyle(Color.lineTextPrimary)
                .padding(.vertical, 9)
                .padding(.horizontal, 12)
                .background(
                    BubbleTail(isOutgoing: isOutgoing)
                        .fill(isOutgoing ? Color.lineOutgoing : Color.lineIncoming)
                        .shadow(color: .black.opacity(0.06), radius: 1, y: 1) // lift off periwinkle
                )
                .frame(maxWidth: 260, alignment: isOutgoing ? .trailing : .leading)
            if !isOutgoing { metaView; Spacer(minLength: 40) }
        }
        .padding(.horizontal, 8)
        .padding(.vertical, 2)
    }

    private var metaView: some View {
        VStack(alignment: isOutgoing ? .trailing : .leading, spacing: 1) {
            if isOutgoing && read { Text("Read").font(.lineReadLabel).foregroundStyle(Color.lineTextSecondary) }
            Text(time).font(.lineTimestamp).foregroundStyle(Color.lineTextSecondary)
        }
    }
}

// 16pt radius with a 4pt notch on the avatar-side top corner
struct BubbleTail: Shape {
    let isOutgoing: Bool
    func path(in rect: CGRect) -> Path {
        let notchCorner: UIRectCorner = isOutgoing ? .topRight : .topLeft
        let p = UIBezierPath(roundedRect: rect,
                             byRoundingCorners: [.topLeft, .topRight, .bottomLeft, .bottomRight].subtracting(notchCorner),
                             cornerRadii: CGSize(width: 16, height: 16))
        let tp = UIBezierPath(roundedRect: rect, byRoundingCorners: notchCorner,
                              cornerRadii: CGSize(width: 4, height: 4))
        p.append(tp)
        return Path(p.cgPath)
    }
}
```

### Friend List Row (with official-account badge)

```swift
struct FriendRow: View {
    let name: String
    let status: String
    let time: String
    let official: Bool

    var body: some View {
        HStack(spacing: 12) {
            Circle().fill(Color.lineSurface).frame(width: 52, height: 52)
            VStack(alignment: .leading, spacing: 3) {
                HStack(spacing: 5) {
                    Text(name).font(.lineFriendName).foregroundStyle(Color.lineTextPrimary)
                    if official { OfficialBadge() }
                }
                Text(status).font(.lineStatus)
                    .foregroundStyle(Color.lineTextSecondary).lineLimit(1)
            }
            Spacer()
            Text(time).font(.lineTimestamp).foregroundStyle(Color.lineTextSecondary)
        }
        .padding(.vertical, 8).padding(.horizontal, 16)
        .frame(height: 68)
        .background(Color.lineCanvas)
        .contentShape(Rectangle())
    }
}

// LINE's verified/business trust primitive
struct OfficialBadge: View {
    var body: some View {
        Image(systemName: "checkmark.seal.fill")
            .font(.system(size: 13))
            .foregroundStyle(Color.lineGreen)
    }
}
```

### Sticker-Shop Tile

```swift
struct StickerShopTile: View {
    let preview: Image
    let title: String
    let author: String
    let price: String   // "120" or "Free"

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            preview.resizable().scaledToFit()
                .frame(height: 96)
                .frame(maxWidth: .infinity)
                .background(Color.white)
                .clipShape(RoundedRectangle(cornerRadius: 8))
            Text(title).font(.lineSection).foregroundStyle(Color.lineTextPrimary)
            Text(author).font(.lineTimestamp).foregroundStyle(Color.lineTextSecondary)
            HStack {
                Spacer()
                Text(price == "Free" ? "Free" : "🪙 \(price)")
                    .font(.lineStickerPx).foregroundStyle(Color.lineGreen)
                    .padding(.vertical, 6).padding(.horizontal, 14)
                    .overlay(Capsule().strokeBorder(Color.lineGreen, lineWidth: 1))
            }
        }
        .padding(12)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.lineSurface))
    }
}
```

### Composer (sticker keyboard is primary)

```swift
struct ComposerBar: View {
    @State private var text = ""
    private var hasText: Bool { !text.trimmingCharacters(in: .whitespaces).isEmpty }

    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: "plus").font(.system(size: 24)).foregroundStyle(.white)
            HStack(spacing: 8) {
                TextField("Aa", text: $text, axis: .vertical)
                    .font(.lineMessageBody).lineLimit(1...5)
                Image(systemName: "face.smiling").font(.system(size: 22)) // sticker keyboard (primary)
                    .foregroundStyle(Color.lineTextSecondary)
                Image(systemName: "camera").font(.system(size: 20))
                    .foregroundStyle(Color.lineTextSecondary)
            }
            .padding(.horizontal, 12).frame(minHeight: 36)
            .background(Capsule().fill(Color.white))
            Button { text = "" } label: {
                if hasText {
                    Image(systemName: "arrow.up").font(.system(size: 16, weight: .bold))
                        .foregroundStyle(.white).frame(width: 30, height: 30)
                        .background(Circle().fill(Color.lineGreen))
                } else {
                    Image(systemName: "mic.fill").font(.system(size: 22)).foregroundStyle(.white)
                }
            }
            .sensoryFeedback(.impact(weight: .light), trigger: hasText)
        }
        .padding(.horizontal, 8).padding(.vertical, 8)
        .background(Color.lineBackdrop)
    }
}
```

## 4. Chat Screen Backdrop

```swift
struct ChatScreen<Content: View>: View {
    @ViewBuilder var content: Content
    var body: some View {
        ScrollView {
            LazyVStack(spacing: 0) { content }
                .padding(.vertical, 12)
        }
        .background(Color.lineBackdrop) // periwinkle, themeable — never white
    }
}
```

## 5. Tab Bar (Super-App)

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithOpaqueBackground()
        a.backgroundColor = UIColor.white
        a.shadowColor = UIColor(Color.lineDivider)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            HomeView().tabItem  { Label("Home",  systemImage: "house.fill") }
            TalkView().tabItem  { Label("Talk",  systemImage: "bubble.left.fill") }
            VOOMView().tabItem  { Label("VOOM",  systemImage: "play.rectangle.fill") }
            NewsView().tabItem  { Label("News",  systemImage: "newspaper.fill") }
            WalletView().tabItem{ Label("Wallet",systemImage: "wallet.pass.fill") }
        }
        .tint(.lineGreen)
    }
}
```

## 6. Motion

```swift
// Sticker bounce-in (signature) — see StickerMessage
withAnimation(.spring(response: 0.4, dampingFraction: 0.55)) { landed = true }

// Animated sticker replay on tap — re-trigger the bounce
.onTapGesture { landed = false; DispatchQueue.main.async { landed = true } }

// Text bubble enter — fade + 6pt rise
.transition(.opacity.combined(with: .offset(y: 6)))
.animation(.easeOut(duration: 0.22), value: messages.count)

// Send circle press
.sensoryFeedback(.impact(weight: .light), trigger: hasText)

// Sticker keyboard present — slide up
.transition(.move(edge: .bottom)).animation(.easeOut(duration: 0.28), value: showStickerKeyboard)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Send (text present) | `arrow.up` | 16pt |
| Voice (empty composer) | `mic.fill` | 22pt |
| Sticker keyboard | `face.smiling` | 22pt |
| Plus / more | `plus` | 24pt |
| Camera | `camera` | 20pt |
| Official-account badge | `checkmark.seal.fill` | 13pt |
| Audio call | `phone` | 22pt |
| Video call | `video` | 22pt |
| Menu (chat) | `line.3.horizontal` | 22pt |
| Search | `magnifyingglass` | 16pt |
| Home (tab) | `house.fill` | 24pt |
| Talk (tab) | `bubble.left.fill` | 24pt |
| VOOM (tab) | `play.rectangle.fill` | 24pt |
| News (tab) | `newspaper.fill` | 24pt |
| Wallet (tab) | `wallet.pass.fill` | 24pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`TextField(axis: .vertical)` needs iOS 16; `.sensoryFeedback` needs iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on friend names, status messages, and message bodies — cap body at ~22pt; pin tab labels, badge counts, sticker prices; stickers are image assets and do not scale with type
- VoiceOver: a sticker must carry a meaningful label (the sticker's name/emotion, e.g. "Sticker: Brown waving") since there is no text fallback — never leave it unlabeled
- Announce the official-account badge as "Official account, verified" after the name; it is the trust signal
- Contrast: `#000000` text on `#C5F0B1` and on `#FFFFFF` passes WCAG AAA; `#8C8C8C` meta floats on the `#8CABD9` backdrop — validate and consider a subtle plate behind meta if a user picks a low-contrast theme
- Honor Reduce Motion: replace the sticker bounce with a quick fade-scale to 1.0
- The periwinkle backdrop is user-themeable — if you support custom backgrounds, re-check bubble/meta contrast per theme
