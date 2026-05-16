# WeChat (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates WeChat's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the squarer tailed bubbles, rounded-square avatars, and the signature Discover grouped-list hub.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Backdrop & canvas
    static let wcBackdrop   = Color(red: 0.929, green: 0.929, blue: 0.929) // #EDEDED
    static let wcCanvas     = Color.white                                  // #FFFFFF
    static let wcSurface    = Color(red: 0.969, green: 0.969, blue: 0.969) // #F7F7F7
    static let wcDivider    = Color(red: 0.851, green: 0.851, blue: 0.851) // #D9D9D9
    static let wcPressedRow = Color(red: 0.925, green: 0.925, blue: 0.925) // #ECECEC

    // MARK: - Text
    static let wcTextPrimary   = Color(red: 0.094, green: 0.094, blue: 0.094) // #181818
    static let wcTextSecondary = Color(red: 0.533, green: 0.533, blue: 0.533) // #888888
    static let wcTextTertiary  = Color(red: 0.698, green: 0.698, blue: 0.698) // #B2B2B2

    // MARK: - Brand
    static let wcGreen        = Color(red: 0.027, green: 0.757, blue: 0.376) // #07C160
    static let wcGreenPressed = Color(red: 0.024, green: 0.627, blue: 0.314) // #06A050

    // MARK: - Message
    static let wcIncoming = Color.white                                      // #FFFFFF
    static let wcOutgoing = Color(red: 0.584, green: 0.925, blue: 0.412)     // #95EC69 (pale chat-green)

    // MARK: - Semantic
    static let wcRedDot   = Color(red: 0.980, green: 0.318, blue: 0.318)     // #FA5151
    static let wcLinkBlue = Color(red: 0.341, green: 0.420, blue: 0.584)     // #576B95
    static let wcGold     = Color(red: 0.984, green: 0.890, blue: 0.702)     // #FBE3B3

    // MARK: - Discover icon chips (data-driven)
    static let wcMoments  = Color(red: 0.247, green: 0.490, blue: 0.835)     // #3F7DD5
    static let wcScan     = Color(red: 0.180, green: 0.627, blue: 0.973)     // #2EA0F8
    static let wcChannels = Color(red: 0.980, green: 0.616, blue: 0.231)     // #FA9D3B
    static let wcMini     = Color(red: 0.235, green: 0.773, blue: 0.122)     // #3CC51F
    static let wcSearch   = Color(red: 0.361, green: 0.486, blue: 0.980)     // #5C7CFA
    static let wcNearby   = Color(red: 0.969, green: 0.435, blue: 0.204)     // #F76F34
}
```

## 2. Typography

WeChat uses **PingFang SC** (iOS system CJK face) / Noto Sans at weights 400/600. PingFang SC ships with iOS — no bundling needed.

```swift
extension Font {
    static let wcNavTitle    = Font.custom("PingFangSC-Semibold", size: 17)
    static let wcContactName = Font.custom("PingFangSC-Regular",  size: 17)
    static let wcRowTitle    = Font.custom("PingFangSC-Regular",  size: 17)
    static let wcMessageBody = Font.custom("PingFangSC-Regular",  size: 17)
    static let wcSection     = Font.custom("PingFangSC-Regular",  size: 14)
    static let wcPreview     = Font.custom("PingFangSC-Regular",  size: 14)
    static let wcTimestamp   = Font.custom("PingFangSC-Regular",  size: 13)
    static let wcTimeSep     = Font.custom("PingFangSC-Regular",  size: 12)
    static let wcButton      = Font.custom("PingFangSC-Semibold", size: 17)
    static let wcButtonSec   = Font.custom("PingFangSC-Regular",  size: 16)
    static let wcTab         = Font.custom("PingFangSC-Regular",  size: 10)
    static let wcBadge       = Font.custom("PingFangSC-Semibold", size: 11)
    static let wcMomentsName  = Font.custom("PingFangSC-Semibold", size: 15)
    static let wcCaption     = Font.custom("PingFangSC-Regular",  size: 13)
}

extension Font {
    static func wechat(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight) // PingFang SC is the system CJK fallback on iOS
    }
}
```

## 3. Signature Components

### Discover Grouped-List Hub (Signature — the super-app)

```swift
struct DiscoverRow: View {
    let icon: String
    let iconColor: Color
    let title: String
    var redDot: Bool = false

    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 8)
                .fill(iconColor)
                .frame(width: 28, height: 28)
                .overlay(Image(systemName: icon).font(.system(size: 15)).foregroundStyle(.white))
            Text(title).font(.wcRowTitle).foregroundStyle(Color.wcTextPrimary)
            Spacer()
            if redDot {
                Circle().fill(Color.wcRedDot).frame(width: 8, height: 8)
            }
            Image(systemName: "chevron.right")
                .font(.system(size: 14, weight: .semibold))
                .foregroundStyle(Color.wcTextTertiary)
        }
        .padding(.horizontal, 16)
        .frame(height: 56)
        .background(Color.wcCanvas)
    }
}

struct DiscoverScreen: View {
    var body: some View {
        ScrollView {
            VStack(spacing: 0) {
                cluster { DiscoverRow(icon: "photo.on.rectangle", iconColor: .wcMoments, title: "Moments", redDot: true) }
                cluster {
                    DiscoverRow(icon: "play.rectangle.fill", iconColor: .wcChannels, title: "Channels")
                    Divider().padding(.leading, 56).background(Color.wcDivider)
                    DiscoverRow(icon: "qrcode.viewfinder", iconColor: .wcScan, title: "Scan")
                    Divider().padding(.leading, 56).background(Color.wcDivider)
                    DiscoverRow(icon: "doc.text.image", iconColor: Color(red: 0.92, green: 0.44, blue: 0.44), title: "Top Stories")
                }
                cluster {
                    DiscoverRow(icon: "magnifyingglass", iconColor: .wcSearch, title: "Search")
                    Divider().padding(.leading, 56).background(Color.wcDivider)
                    DiscoverRow(icon: "location.fill", iconColor: .wcNearby, title: "People Nearby")
                }
                cluster { DiscoverRow(icon: "square.grid.2x2.fill", iconColor: .wcMini, title: "Mini Programs") }
            }
            .padding(.vertical, 12)
        }
        .background(Color.wcBackdrop)
    }

    @ViewBuilder private func cluster<C: View>(@ViewBuilder _ rows: () -> C) -> some View {
        VStack(spacing: 0) { rows() }
            .clipShape(RoundedRectangle(cornerRadius: 0))
            .padding(.bottom, 12) // gray gutter between clusters
    }
}
```

### Tailed Message Bubble + Rounded-Square Avatar (Signature)

```swift
struct WCBubble: View {
    let text: String
    let isOutgoing: Bool

    var body: some View {
        HStack(alignment: .top, spacing: 8) {
            if isOutgoing { Spacer(minLength: 40) }
            if !isOutgoing { Avatar() }
            BubbleWithTail(isOutgoing: isOutgoing)
                .fill(isOutgoing ? Color.wcOutgoing : Color.wcIncoming)
                .overlay(
                    Text(text)
                        .font(.wcMessageBody)
                        .foregroundStyle(Color.wcTextPrimary)
                        .padding(.vertical, 9)
                        .padding(.horizontal, 12)
                        .padding(isOutgoing ? .trailing : .leading, 6) // room for the tail
                )
                .fixedSize(horizontal: false, vertical: true)
                .frame(maxWidth: 270, alignment: isOutgoing ? .trailing : .leading)
            if isOutgoing { Avatar() }
            if !isOutgoing { Spacer(minLength: 40) }
        }
        .padding(.horizontal, 8)
        .padding(.vertical, 2)
    }
}

// Rounded-SQUARE avatar — WeChat's constant brand tell (never a circle)
struct Avatar: View {
    var body: some View {
        RoundedRectangle(cornerRadius: 8)
            .fill(Color.wcSurface)
            .frame(width: 40, height: 40)
    }
}

// 6pt radius + a small solid triangular tail toward the sender's avatar
struct BubbleWithTail: Shape {
    let isOutgoing: Bool
    func path(in rect: CGRect) -> Path {
        let r: CGFloat = 6, tail: CGFloat = 6
        var p = Path()
        let body = rect.insetBy(dx: 0, dy: 0)
        let bodyRect = isOutgoing
            ? CGRect(x: body.minX, y: body.minY, width: body.width - tail, height: body.height)
            : CGRect(x: body.minX + tail, y: body.minY, width: body.width - tail, height: body.height)
        p.addRoundedRect(in: bodyRect, cornerSize: CGSize(width: r, height: r))
        // triangular tail near the top, pointing toward the avatar
        let ty = body.minY + 14
        if isOutgoing {
            p.move(to: CGPoint(x: bodyRect.maxX, y: ty - 5))
            p.addLine(to: CGPoint(x: body.maxX, y: ty))
            p.addLine(to: CGPoint(x: bodyRect.maxX, y: ty + 5))
        } else {
            p.move(to: CGPoint(x: bodyRect.minX, y: ty - 5))
            p.addLine(to: CGPoint(x: body.minX, y: ty))
            p.addLine(to: CGPoint(x: bodyRect.minX, y: ty + 5))
        }
        return p
    }
}
```

### Chat List Row (rounded-square avatar + red badge)

```swift
struct ChatRow: View {
    let name: String
    let preview: String
    let time: String
    let unread: Int

    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 8).fill(Color.wcSurface).frame(width: 48, height: 48)
            VStack(alignment: .leading, spacing: 4) {
                Text(name).font(.wcContactName).foregroundStyle(Color.wcTextPrimary)
                Text(preview).font(.wcPreview).foregroundStyle(Color.wcTextSecondary).lineLimit(1)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 6) {
                Text(time).font(.wcTimestamp).foregroundStyle(Color.wcTextSecondary)
                if unread > 0 {
                    Text("\(unread)")
                        .font(.wcBadge).foregroundStyle(.white)
                        .frame(minWidth: 18, minHeight: 18)
                        .background(Circle().fill(Color.wcRedDot))
                }
            }
        }
        .padding(.horizontal, 12)
        .frame(height: 64)
        .background(Color.wcCanvas)
        .contentShape(Rectangle())
    }
}
```

### Red Packet (Hóngbāo) Card (Signature)

```swift
struct RedPacketCard: View {
    let sender: String
    @State private var opened = false

    var body: some View {
        VStack(spacing: 10) {
            ZStack {
                Circle().fill(Color.wcGold).frame(width: 44, height: 44)
                Image(systemName: opened ? "envelope.open.fill" : "yensign.circle.fill")
                    .font(.system(size: 22)).foregroundStyle(Color(red: 0.78, green: 0.18, blue: 0.16))
                    .rotation3DEffect(.degrees(opened ? 360 : 0), axis: (x: 0, y: 1, z: 0))
            }
            Text("Best wishes!").font(.wcButton).foregroundStyle(.white)
            Text("From \(sender)").font(.wcCaption).foregroundStyle(.white.opacity(0.85))
        }
        .frame(width: 260)
        .padding(.vertical, 22)
        .background(
            RoundedRectangle(cornerRadius: 8)
                .fill(LinearGradient(colors: [Color.wcRedDot, Color(red: 0.78, green: 0.18, blue: 0.16)],
                                     startPoint: .top, endPoint: .bottom))
                .shadow(color: .black.opacity(0.12), radius: 4, y: 2)
        )
        .onTapGesture {
            withAnimation(.easeInOut(duration: 0.6)) { opened = true }
        }
    }
}
```

### Composer

```swift
struct ComposerBar: View {
    @State private var text = ""
    private var hasText: Bool { !text.trimmingCharacters(in: .whitespaces).isEmpty }

    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: "waveform.circle").font(.system(size: 26)).foregroundStyle(Color.wcTextPrimary)
            TextField("", text: $text, axis: .vertical)
                .font(.wcMessageBody).lineLimit(1...5)
                .padding(.horizontal, 10).frame(minHeight: 36)
                .background(RoundedRectangle(cornerRadius: 6).fill(Color.wcCanvas))
            Image(systemName: "face.smiling").font(.system(size: 26)).foregroundStyle(Color.wcTextPrimary)
            if hasText {
                Button { text = "" } label: {
                    Text("Send").font(.wcButtonSec).foregroundStyle(.white)
                        .padding(.vertical, 8).padding(.horizontal, 14)
                        .background(RoundedRectangle(cornerRadius: 4).fill(Color.wcGreen))
                }
            } else {
                Image(systemName: "plus.circle").font(.system(size: 26)).foregroundStyle(Color.wcTextPrimary)
            }
        }
        .padding(.horizontal, 8).padding(.vertical, 8)
        .background(Color.wcBackdrop)
        .animation(.easeOut(duration: 0.12), value: hasText)
    }
}
```

## 4. Time Separator

```swift
struct TimeSeparator: View {
    let label: String   // "2:14 PM", "Yesterday"
    var body: some View {
        Text(label)
            .font(.wcTimeSep)
            .foregroundStyle(Color.wcTextSecondary)
            .frame(maxWidth: .infinity)
            .padding(.vertical, 8)
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithOpaqueBackground()
        a.backgroundColor = UIColor(Color.wcSurface)
        a.shadowColor = UIColor(Color.wcDivider)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            ChatsView().tabItem    { Label("Chats",    systemImage: "bubble.left.fill") }
            ContactsView().tabItem { Label("Contacts", systemImage: "person.crop.square.fill") }
            DiscoverScreen().tabItem { Label("Discover", systemImage: "safari.fill") }
            MeView().tabItem       { Label("Me",       systemImage: "person.fill") }
        }
        .tint(.wcGreen)
    }
}
```

## 6. Motion

```swift
// Red-packet open (the one flourish) — see RedPacketCard
withAnimation(.easeInOut(duration: 0.6)) { opened = true }  // ~600ms coin-flip

// Send (text) — quick fade + slight scale, no flourish
.transition(.opacity.combined(with: .scale(scale: 0.98)))
.animation(.easeOut(duration: 0.15), value: messages.count)

// Press states — subtle background tint, ~120ms
.animation(.easeOut(duration: 0.12), value: isPressed)

// Attachment grid present — slide up, "+" rotates to "×"
.rotationEffect(.degrees(showGrid ? 45 : 0))
.animation(.easeOut(duration: 0.26), value: showGrid)

// Red dot — NO animation; appears/disappears instantly (authoritative, not playful)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Send | (text button, no glyph) | — |
| Voice | `waveform.circle` | 26pt |
| Sticker | `face.smiling` | 26pt |
| Plus / attachment | `plus.circle` | 26pt |
| Moments (Discover) | `photo.on.rectangle` | 15pt (in chip) |
| Scan (Discover) | `qrcode.viewfinder` | 15pt |
| Channels (Discover) | `play.rectangle.fill` | 15pt |
| Mini Programs (Discover) | `square.grid.2x2.fill` | 15pt |
| Search (Discover) | `magnifyingglass` | 15pt |
| Nearby (Discover) | `location.fill` | 15pt |
| Chevron (rows) | `chevron.right` | 14pt |
| Red packet | `yensign.circle.fill` / `envelope.open.fill` | 22pt |
| Chats (tab) | `bubble.left.fill` | 26pt |
| Contacts (tab) | `person.crop.square.fill` | 26pt |
| Discover (tab) | `safari.fill` | 26pt |
| Me (tab) | `person.fill` | 26pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`TextField(axis: .vertical)` needs iOS 16). PingFang SC is the system CJK face — no font bundling
- Support Dynamic Type on names, list rows, and message bodies (they share the 17pt base) — cap body at ~22pt; pin tab labels, badge counts, time separators
- VoiceOver: read the Discover row as "Moments, has new updates, button" (announce the red dot as "new updates" — it's the attention primitive); each row is a single button
- Announce the red packet as "Red packet from <sender>, double-tap to open" — never leave the gold seal unlabeled
- Contrast: `#181818` text on `#95EC69`, `#FFFFFF`, and `#EDEDED` passes WCAG AAA; `#888888` secondary on white passes AA at 14pt+; validate `#888888` time separators on the `#EDEDED` backdrop
- Honor Reduce Motion: replace the red-packet coin-flip with a quick crossfade to the opened state
- Keep motion minimal for assistive tech too — WeChat's calm is part of the accessibility story; the red dot must not animate
