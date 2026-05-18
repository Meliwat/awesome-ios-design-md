# Zalo (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Zalo's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the asymmetric chat bubble (custom `Shape`), the chat thread, the mini-app launcher grid, and the blue header.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (single anchor)
    static let zaloBlue        = Color(red: 0.000, green: 0.408, blue: 1.000) // #0068FF
    static let zaloBluePressed = Color(red: 0.000, green: 0.322, blue: 0.800) // #0052CC
    static let zaloBlueDeep    = Color(red: 0.000, green: 0.278, blue: 0.702) // #0047B3
    static let zaloBubbleOut   = Color(red: 0.859, green: 0.922, blue: 1.000) // #DBEBFF
    static let zaloBubbleOutInk = Color(red: 0.078, green: 0.133, blue: 0.227) // #14223A

    // MARK: - Canvas & Surfaces (Light)
    static let zaloCanvas      = Color.white                                   // #FFFFFF
    static let zaloChatBg      = Color(red: 0.910, green: 0.925, blue: 0.945)  // #E8ECF1
    static let zaloSurface1    = Color(red: 0.957, green: 0.961, blue: 0.969)  // #F4F5F7
    static let zaloSurface2    = Color(red: 0.922, green: 0.929, blue: 0.941)  // #EBEDF0
    static let zaloDivider     = Color(red: 0.894, green: 0.902, blue: 0.922)  // #E4E6EB

    // MARK: - Canvas & Surfaces (Dark)
    static let zaloDarkCanvas   = Color(red: 0.082, green: 0.090, blue: 0.102) // #15171A
    static let zaloDarkChatBg   = Color(red: 0.063, green: 0.078, blue: 0.094) // #101418
    static let zaloDarkBubbleIn = Color(red: 0.122, green: 0.137, blue: 0.161) // #1F2329
    static let zaloDarkBubbleOut = Color(red: 0.039, green: 0.227, blue: 0.478) // #0A3A7A
    static let zaloDarkDivider  = Color(red: 0.149, green: 0.165, blue: 0.188) // #262A30

    // MARK: - Text
    static let zaloInk           = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let zaloTextSecondary = Color(red: 0.420, green: 0.447, blue: 0.502) // #6B7280
    static let zaloTextTertiary  = Color(red: 0.604, green: 0.627, blue: 0.667) // #9AA0AA
    static let zaloTextPrimaryDk = Color(red: 0.925, green: 0.929, blue: 0.937) // #ECEDEF

    // MARK: - Semantic
    static let zaloNotify  = Color(red: 0.961, green: 0.196, blue: 0.357) // #F5325B
    static let zaloSuccess = Color(red: 0.094, green: 0.663, blue: 0.341) // #18A957
    static let zaloWarning = Color(red: 1.000, green: 0.584, blue: 0.000) // #FF9500

    // MARK: - Mini-App Tile Colors
    static let zaloTileGreen  = Color(red: 0.094, green: 0.663, blue: 0.341) // #18A957
    static let zaloTileViolet = Color(red: 0.482, green: 0.361, blue: 1.000) // #7B5CFF
    static let zaloTileCyan   = Color(red: 0.000, green: 0.722, blue: 0.831) // #00B8D4
}
```

## 2. Typography

Bundle **Be Vietnam Pro** via `Info.plist` / `UIAppFonts` (SIL OFL — designed for Vietnamese diacritics). Falls back to SF Pro, which handles Vietnamese acceptably.

```swift
extension Font {
    static let zaloDisplay    = Font.custom("BeVietnamPro-ExtraBold", size: 32) // -0.5 tracking
    static let zaloScreenTitle = Font.custom("BeVietnamPro-Bold",     size: 24)
    static let zaloSection    = Font.custom("BeVietnamPro-Bold",      size: 20)
    static let zaloListTitle  = Font.custom("BeVietnamPro-SemiBold",  size: 17)
    static let zaloBody       = Font.custom("BeVietnamPro-Regular",   size: 15) // body + bubble
    static let zaloCellTitle  = Font.custom("BeVietnamPro-SemiBold",  size: 14)
    static let zaloPreview    = Font.custom("BeVietnamPro-Regular",   size: 13)
    static let zaloButton     = Font.custom("BeVietnamPro-Bold",      size: 15)
    static let zaloTimestamp  = Font.custom("BeVietnamPro-SemiBold",  size: 11)
    static let zaloTab        = Font.custom("BeVietnamPro-Medium",    size: 10)
    static let zaloBadge      = Font.custom("BeVietnamPro-Bold",      size: 9)
    static let zaloMiniLabel  = Font.custom("BeVietnamPro-Medium",    size: 11)
}
```

> Vietnamese diacritics need vertical room — set `lineSpacing` generously on bubble/body text and never use a fixed-height container that clips stacked tone marks.

## 3. Signature Components

### Asymmetric Bubble Shape

The signature: 16pt corners with one tail corner clipped to 5pt (bottom-left for incoming, bottom-right for outgoing).

```swift
struct BubbleShape: Shape {
    let outgoing: Bool
    var big: CGFloat = 16
    var tail: CGFloat = 5

    func path(in rect: CGRect) -> Path {
        let tl = big
        let tr = big
        let bl = outgoing ? big : tail
        let br = outgoing ? tail : big
        var p = Path()
        p.move(to: CGPoint(x: rect.minX + tl, y: rect.minY))
        p.addLine(to: CGPoint(x: rect.maxX - tr, y: rect.minY))
        p.addArc(center: CGPoint(x: rect.maxX - tr, y: rect.minY + tr), radius: tr,
                 startAngle: .degrees(-90), endAngle: .degrees(0), clockwise: false)
        p.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY - br))
        p.addArc(center: CGPoint(x: rect.maxX - br, y: rect.maxY - br), radius: br,
                 startAngle: .degrees(0), endAngle: .degrees(90), clockwise: false)
        p.addLine(to: CGPoint(x: rect.minX + bl, y: rect.maxY))
        p.addArc(center: CGPoint(x: rect.minX + bl, y: rect.maxY - bl), radius: bl,
                 startAngle: .degrees(90), endAngle: .degrees(180), clockwise: false)
        p.addLine(to: CGPoint(x: rect.minX, y: rect.minY + tl))
        p.addArc(center: CGPoint(x: rect.minX + tl, y: rect.minY + tl), radius: tl,
                 startAngle: .degrees(180), endAngle: .degrees(270), clockwise: false)
        p.closeSubpath()
        return p
    }
}
```

### Chat Bubble

```swift
struct Message: Identifiable {
    let id = UUID()
    let text: String
    let outgoing: Bool
    let time: String
    let showAvatar: Bool
}

struct ChatBubble: View {
    let message: Message

    var body: some View {
        HStack(alignment: .bottom, spacing: 8) {
            if !message.outgoing {
                if message.showAvatar {
                    Circle()
                        .fill(LinearGradient(colors: [Color(red: 0.302, green: 0.624, blue: 1), .zaloBlueDeep], startPoint: .topLeading, endPoint: .bottomTrailing))
                        .frame(width: 28, height: 28)
                } else {
                    Color.clear.frame(width: 28, height: 28)
                }
            }
            VStack(alignment: message.outgoing ? .trailing : .leading, spacing: 3) {
                Text(message.text)
                    .font(.zaloBody)
                    .foregroundStyle(message.outgoing ? Color.zaloBubbleOutInk : Color.zaloInk)
                    .padding(.vertical, 9).padding(.horizontal, 13)
                    .background(message.outgoing ? Color.zaloBubbleOut : Color.zaloCanvas, in: BubbleShape(outgoing: message.outgoing))
                    .shadow(color: message.outgoing ? .clear : .black.opacity(0.06), radius: 2, y: 1)
                Text(message.time)
                    .font(.system(size: 10))
                    .foregroundStyle(Color.zaloTextTertiary)
            }
            if message.outgoing { Spacer(minLength: 40) } else { Spacer(minLength: 40) }
        }
        .frame(maxWidth: .infinity, alignment: message.outgoing ? .trailing : .leading)
        .padding(.horizontal, 12)
    }
}
```

### Chat Thread

```swift
struct ChatThread: View {
    let messages: [Message]

    var body: some View {
        ScrollView {
            VStack(spacing: 6) {
                DaySeparator(text: "Hôm nay · Today")
                ForEach(messages) { ChatBubble(message: $0) }
                Text("Đã xem · Seen 09:27")
                    .font(.system(size: 10))
                    .foregroundStyle(Color.zaloTextTertiary)
                    .frame(maxWidth: .infinity, alignment: .trailing)
                    .padding(.trailing, 16).padding(.top, 2)
            }
            .padding(.vertical, 14)
        }
        .background(Color.zaloChatBg)
    }
}

struct DaySeparator: View {
    let text: String
    var body: some View {
        Text(text)
            .font(.zaloTimestamp)
            .foregroundStyle(Color.zaloTextSecondary)
            .padding(.vertical, 3).padding(.horizontal, 12)
            .background(Color.white.opacity(0.7), in: Capsule())
            .padding(.bottom, 4)
    }
}
```

### Chat Header (Zalo Blue bar)

```swift
struct ChatHeader: View {
    let name: String
    let presence: String

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: "chevron.left").font(.system(size: 22, weight: .semibold)).foregroundStyle(.white)
            Circle()
                .fill(LinearGradient(colors: [Color(red: 0.302, green: 0.624, blue: 1), .zaloBlueDeep], startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 38, height: 38)
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.system(size: 15, weight: .semibold)).foregroundStyle(.white)
                Text(presence).font(.system(size: 11)).foregroundStyle(.white.opacity(0.85))
            }
            Spacer()
            Image(systemName: "phone.fill").font(.system(size: 19)).foregroundStyle(.white)
            Image(systemName: "video.fill").font(.system(size: 19)).foregroundStyle(.white)
        }
        .padding(.horizontal, 16).padding(.bottom, 14).padding(.top, 6)
        .background(Color.zaloBlue)
    }
}
```

### Message Composer

```swift
struct Composer: View {
    @State private var text = ""
    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: "face.smiling").font(.system(size: 23)).foregroundStyle(Color.zaloBlue)
            HStack {
                TextField("Tin nhắn", text: $text)
                    .font(.zaloPreview)
            }
            .padding(.horizontal, 16)
            .frame(height: 38)
            .background(Color.zaloSurface1, in: Capsule())

            if text.isEmpty {
                Image(systemName: "camera.fill").font(.system(size: 22)).foregroundStyle(Color.zaloBlue)
            } else {
                Button {
                    text = ""
                } label: {
                    Image(systemName: "paperplane.fill")
                        .font(.system(size: 16))
                        .foregroundStyle(.white)
                        .frame(width: 38, height: 38)
                        .background(Color.zaloBlue, in: Circle())
                }
            }
        }
        .padding(.horizontal, 14).padding(.top, 9).padding(.bottom, 14)
        .background(Color.zaloCanvas)
        .overlay(alignment: .top) { Rectangle().fill(Color.zaloDivider).frame(height: 0.5) }
    }
}
```

### Mini-App Launcher Grid

```swift
struct MiniApp: Identifiable {
    let id = UUID()
    let symbol: String
    let tile: Color
    let label: String
}

struct LauncherGrid: View {
    let apps: [MiniApp]
    private let cols = Array(repeating: GridItem(.flexible(), spacing: 16), count: 4)

    var body: some View {
        LazyVGrid(columns: cols, spacing: 18) {
            ForEach(apps) { app in
                VStack(spacing: 7) {
                    Image(systemName: app.symbol)
                        .font(.system(size: 24))
                        .foregroundStyle(.white)
                        .frame(width: 52, height: 52)
                        .background(app.tile, in: RoundedRectangle(cornerRadius: 15))
                    Text(app.label)
                        .font(.zaloMiniLabel)
                        .foregroundStyle(Color.zaloInk)
                        .lineLimit(2)
                        .multilineTextAlignment(.center)
                }
            }
        }
        .padding(16)
        .background(Color.zaloCanvas)
    }
}
```

### Chat List Row

```swift
struct ChatListRow: View {
    let name: String
    let preview: String
    let time: String
    let unread: Int
    let online: Bool

    var body: some View {
        HStack(spacing: 12) {
            ZStack(alignment: .bottomTrailing) {
                Circle()
                    .fill(LinearGradient(colors: [Color(red: 0.302, green: 0.624, blue: 1), .zaloBlueDeep], startPoint: .topLeading, endPoint: .bottomTrailing))
                    .frame(width: 48, height: 48)
                if online {
                    Circle().fill(Color.zaloSuccess).frame(width: 10, height: 10)
                        .overlay(Circle().strokeBorder(.white, lineWidth: 2))
                }
            }
            VStack(alignment: .leading, spacing: 3) {
                Text(name).font(.zaloListTitle).fontWeight(unread > 0 ? .bold : .semibold).foregroundStyle(Color.zaloInk)
                Text(preview).font(.zaloPreview).foregroundStyle(unread > 0 ? Color.zaloInk : Color.zaloTextSecondary).lineLimit(1)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 6) {
                Text(time).font(.zaloTimestamp).foregroundStyle(Color.zaloTextTertiary)
                if unread > 0 {
                    Text("\(unread)")
                        .font(.zaloBadge).foregroundStyle(.white)
                        .padding(.horizontal, 5).frame(minWidth: 16, minHeight: 16)
                        .background(Color.zaloNotify, in: Capsule())
                }
            }
        }
        .padding(.horizontal, 16).frame(height: 72)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.zaloDivider).frame(height: 0.5).padding(.leading, 76)
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct ZaloTabView: View {
    var body: some View {
        TabView {
            ChatListView().tabItem { Label("Tin nhắn", systemImage: "message.fill") }
                .badge(5)
            ContactsView().tabItem { Label("Danh bạ", systemImage: "person.2.fill") }
            DiscoverView().tabItem { Label("Khám phá", systemImage: "square.grid.2x2.fill") }
            TimelineView().tabItem { Label("Nhật ký", systemImage: "clock.fill") }
            ProfileView().tabItem { Label("Cá nhân", systemImage: "person.crop.circle.fill") }
        }
        .tint(.zaloBlue)
    }
}
```

## 5. Motion

```swift
// Outgoing bubble send
.scaleEffect(appeared ? 1.0 : 0.9)
.opacity(appeared ? 1 : 0)
.animation(.easeOut(duration: 0.2), value: appeared)

// Incoming bubble + typing
.transition(.opacity.combined(with: .move(edge: .bottom)))
// typing indicator: 3 dots bouncing on a repeatForever 0.6s timing

// Read receipt fade-in
.transition(.opacity).animation(.easeOut(duration: 0.15), value: seen)

// Tab change — active icon outline -> filled + tint
.foregroundStyle(selected ? Color.zaloBlue : Color.zaloTextTertiary)
.animation(.easeOut(duration: 0.12), value: selected)

// Launcher tile press
.scaleEffect(pressed ? 0.94 : 1.0)
.animation(.easeOut(duration: 0.10), value: pressed)

// Reaction picker (long-press bubble)
.transition(.scale.combined(with: .opacity)) // 180ms

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: sent)        // send
.sensoryFeedback(.selection, trigger: selectedTab)                  // tab change
.sensoryFeedback(.impact(weight: .medium), trigger: paymentDone)    // Zalo Pay confirm
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Tin nhắn (tab) | `message` / `message.fill` | 23pt |
| Danh bạ (tab) | `person.2` / `person.2.fill` | 23pt |
| Khám phá (tab) | `square.grid.2x2` / `.fill` | 23pt |
| Nhật ký (tab) | `clock` / `clock.fill` | 23pt |
| Cá nhân (tab) | `person.crop.circle` / `.fill` | 23pt |
| Back | `chevron.left` | 22pt |
| Voice call | `phone.fill` | 19-21pt |
| Video call | `video.fill` | 19-21pt |
| Send | `paperplane.fill` | 16pt (on blue circle) |
| Sticker / emoji | `face.smiling` | 23pt |
| Camera | `camera.fill` | 22pt |
| Attachment | `plus` / `paperclip` | 23pt |
| Search | `magnifyingglass` | 16-22pt |
| Like (timeline) | `heart.fill` | 20pt (`#F5325B`) |
| Verified (OA) | `checkmark.seal.fill` | 14pt (`#0068FF`) |
| Settings | `gearshape.fill` | 24pt |

## 7. Dark Mode

```swift
struct ZaloTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.zaloDarkCanvas : Color.zaloCanvas)
            .foregroundStyle(scheme == .dark ? Color.zaloTextPrimaryDk : Color.zaloInk)
    }
}
extension View { func zaloTheme() -> some View { modifier(ZaloTheme()) } }
```

The brand holds in dark mode: headers and the active tab stay Zalo Blue `#0068FF`. The chat backdrop flips `#E8ECF1` → `#101418`; incoming bubbles `#FFFFFF` → `#1F2329` (light text); outgoing bubbles `#DBEBFF` → `#0A3A7A` with white text (the asymmetric clipped tail is preserved). Content surfaces → `#15171A`, dividers → `#262A30`. The bubble shadow is dropped on dark (value separates the bubbles). Notification red `#F5325B` and mini-app tile colors are identical across themes.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.badge(_:)` on `tabItem` need iOS 17 — fall back to `UIImpactFeedbackGenerator` and a manual badge overlay on 16)
- Bundle Be Vietnam Pro TTFs via `Info.plist` — SIL OFL, free to ship; it is the correct face for Vietnamese diacritics
- Dynamic Type: scale display, titles, body, bubbles, previews; keep timestamps, badges, tab labels, mini-app captions FIXED. Crucially, allow chat rows and bubbles to grow vertically with Dynamic Type — never clip stacked Vietnamese tone marks (ậ ệ ọ ữ); set adequate `lineSpacing`
- VoiceOver: label bubbles "{incoming/outgoing} message: {text}, {time}"; chat rows "{name}, {preview}, {unread} unread"; the send button "Send message"; launcher tiles "{label}, opens mini-app"
- Color contrast: white on `#0068FF` passes AA; `#1A1A1A` on `#FFFFFF` and `#14223A` on `#DBEBFF` both pass AA for bubble text; the notification red badge uses white text and passes AA
- Reduce Motion: disable the bubble scale-in (plain fade), disable the launcher tile press scale; keep the typing indicator as a static "typing…" label; keep selection states
- Right-to-left: Zalo is LTR (Vietnamese); the asymmetric bubble tail does NOT mirror by sender side (incoming = bottom-left, outgoing = bottom-right, regardless)
- Accessibility focus order in the thread: top→bottom so screen readers narrate the conversation in sequence; group day separators as headings
