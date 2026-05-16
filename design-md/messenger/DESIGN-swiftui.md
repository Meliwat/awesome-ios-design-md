# Messenger (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Messenger's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the conversation-anchored gradient bubble, and the bouncy reactions popover.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Gradient stops (outgoing bubble only)
    static let msgGradBlue   = Color(red: 0.039, green: 0.486, blue: 1.000) // #0A7CFF
    static let msgGradViolet = Color(red: 0.616, green: 0.306, blue: 0.867) // #9D4EDD
    static let msgGradPink   = Color(red: 1.000, green: 0.361, blue: 0.627) // #FF5CA0

    // MARK: - UI blue
    static let msgBlue        = Color(red: 0.039, green: 0.486, blue: 1.000) // #0A7CFF
    static let msgBluePressed = Color(red: 0.031, green: 0.400, blue: 0.839) // #0866D6

    // MARK: - Canvas & Surfaces
    static let msgCanvas       = Color(red: 1, green: 1, blue: 1)             // #FFFFFF
    static let msgCanvasDark   = Color.black                                  // #000000 (true black)
    static let msgSurface      = Color(red: 0.945, green: 0.945, blue: 0.949) // #F1F1F2
    static let msgSurfaceDark  = Color(red: 0.110, green: 0.110, blue: 0.114) // #1C1C1D
    static let msgIncoming     = Color(red: 0.945, green: 0.945, blue: 0.949) // #F1F1F2
    static let msgIncomingDark = Color(red: 0.188, green: 0.188, blue: 0.188) // #303030
    static let msgDivider      = Color(red: 0.894, green: 0.902, blue: 0.922) // #E4E6EB
    static let msgDividerDark  = Color(red: 0.227, green: 0.231, blue: 0.235) // #3A3B3C

    // MARK: - Text
    static let msgTextPrimary   = Color(red: 0.020, green: 0.020, blue: 0.020) // #050505
    static let msgTextPrimaryD  = Color(red: 0.894, green: 0.902, blue: 0.922) // #E4E6EB
    static let msgTextSecondary = Color(red: 0.396, green: 0.404, blue: 0.420) // #65676B
    static let msgTextTertiary  = Color(red: 0.541, green: 0.553, blue: 0.569) // #8A8D91

    // MARK: - Semantic
    static let msgActiveGreen = Color(red: 0.192, green: 0.820, blue: 0.345)  // #31D158
    static let msgError       = Color(red: 0.980, green: 0.220, blue: 0.243)  // #FA383E
    static let msgSuccess     = Color(red: 0.192, green: 0.635, blue: 0.298)  // #31A24C
}

extension LinearGradient {
    // The signature outgoing-bubble ribbon (≈135°)
    static let msgBubble = LinearGradient(
        colors: [.msgGradBlue, .msgGradViolet, .msgGradPink],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
}
```

## 2. Typography

Messenger uses **Inter / SF** at weights 400/600/700. Bundle Inter via `Info.plist` (`UIAppFonts`) or fall back to SF Pro. Reactions are system color emoji.

```swift
extension Font {
    static let msgLargeTitle   = Font.custom("Inter-Bold",     size: 28)
    static let msgConvoName    = Font.custom("Inter-SemiBold", size: 17)
    static let msgConvoUnread  = Font.custom("Inter-Bold",     size: 17)
    static let msgThreadTitle  = Font.custom("Inter-SemiBold", size: 16)
    static let msgMessageBody  = Font.custom("Inter-Regular",  size: 16)
    static let msgPreview      = Font.custom("Inter-Regular",  size: 15)
    static let msgSection      = Font.custom("Inter-Bold",     size: 13)
    static let msgTimestamp    = Font.custom("Inter-Regular",  size: 13)
    static let msgBubbleMeta   = Font.custom("Inter-Regular",  size: 12)
    static let msgReactCount   = Font.custom("Inter-SemiBold", size: 12)
    static let msgButton       = Font.custom("Inter-SemiBold", size: 16)
    static let msgTab          = Font.custom("Inter-SemiBold", size: 10)
    static let msgActiveNow    = Font.custom("Inter-Regular",  size: 12)
    static let msgSystem       = Font.custom("Inter-Regular",  size: 13)
}

extension Font {
    static func messenger(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Conversation-Anchored Gradient Bubble

Render the gradient at the size of the whole message stack and mask it to the union of outgoing bubble shapes. The simple per-bubble approach below uses `.background(LinearGradient...)` with a shared frame so consecutive bubbles continue the ribbon.

```swift
struct OutgoingBubble: View {
    let text: String
    let isLastInRun: Bool
    let threadHeight: CGFloat   // total visible thread height
    let bubbleOriginY: CGFloat  // this bubble's y within the thread

    var body: some View {
        HStack {
            Spacer(minLength: 0)
            Text(text)
                .font(.msgMessageBody)
                .foregroundStyle(.white)
                .padding(.vertical, 9)
                .padding(.horizontal, 14)
                .background(
                    // Gradient anchored to the conversation, not the bubble:
                    LinearGradient(colors: [.msgGradBlue, .msgGradViolet, .msgGradPink],
                                   startPoint: .topLeading, endPoint: .bottomTrailing)
                        .frame(height: threadHeight)
                        .offset(y: -bubbleOriginY)
                        .frame(height: 0, alignment: .top) // clip to bubble via mask below
                )
                .clipShape(BubbleShape(radius: 18,
                                       tightCorner: .bottomRight,
                                       tight: isLastInRun ? 6 : 18))
                .frame(maxWidth: 270, alignment: .trailing)
        }
        .padding(.horizontal, 10)
    }
}

struct BubbleShape: Shape {
    var radius: CGFloat
    var tightCorner: UIRectCorner
    var tight: CGFloat
    func path(in rect: CGRect) -> Path {
        let normal: UIRectCorner = [.topLeft, .topRight, .bottomLeft, .bottomRight]
        let p = UIBezierPath(roundedRect: rect, byRoundingCorners: normal.subtracting(tightCorner),
                             cornerRadii: CGSize(width: radius, height: radius))
        let tp = UIBezierPath(roundedRect: rect, byRoundingCorners: tightCorner,
                              cornerRadii: CGSize(width: tight, height: tight))
        p.append(tp)
        return Path(p.cgPath)
    }
}
```

> Production approach: place a single `LinearGradient` in a `ZStack` behind the `LazyVStack` of messages and use `.mask` with a shape that is the union of every outgoing bubble's frame (collect frames via `GeometryReader` + a preference key). That guarantees a perfectly continuous ribbon regardless of bubble heights.

### Reactions Popover (Signature — bouncy)

```swift
struct ReactionsPopover: View {
    let onPick: (String) -> Void
    @State private var shown = false
    private let emoji = ["👍", "❤️", "😆", "😮", "😢", "😡"]

    var body: some View {
        HStack(spacing: 12) {
            ForEach(Array(emoji.enumerated()), id: \.offset) { i, e in
                Text(e)
                    .font(.system(size: 30))
                    .scaleEffect(shown ? 1 : 0.4)
                    .animation(.spring(response: 0.3, dampingFraction: 0.55)
                        .delay(Double(i) * 0.02), value: shown)
                    .onTapGesture { onPick(e) }
            }
            Image(systemName: "plus")
                .font(.system(size: 18, weight: .semibold))
                .foregroundStyle(Color.msgTextSecondary)
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 10)
        .background(
            Capsule().fill(Color.msgCanvas)
                .shadow(color: .black.opacity(0.18), radius: 14, y: 8)
        )
        .scaleEffect(shown ? 1 : 0.7)
        .animation(.spring(response: 0.32, dampingFraction: 0.6), value: shown)
        .onAppear { shown = true }
    }
}

// Corner reaction badge that lands with a bounce
struct ReactionBadge: View {
    let emoji: String
    let count: Int
    @State private var landed = false
    var body: some View {
        HStack(spacing: 3) {
            Text(emoji).font(.system(size: 14))
            if count > 1 { Text("\(count)").font(.msgReactCount).foregroundStyle(Color.msgTextSecondary) }
        }
        .padding(.horizontal, 6).padding(.vertical, 3)
        .background(Capsule().fill(Color.msgCanvas)
            .overlay(Capsule().strokeBorder(Color.msgDivider, lineWidth: 1)))
        .scaleEffect(landed ? 1 : 1.25)
        .animation(.spring(response: 0.25, dampingFraction: 0.5), value: landed)
        .onAppear { landed = true }
    }
}
```

### Big-Thumb Send / One-Tap Like

```swift
struct ComposerBar: View {
    @State private var text = ""
    private var hasText: Bool { !text.trimmingCharacters(in: .whitespaces).isEmpty }

    var body: some View {
        HStack(spacing: 10) {
            if !hasText {
                HStack(spacing: 16) {
                    ForEach(["camera", "photo", "mic", "face.smiling"], id: \.self) {
                        Image(systemName: $0).font(.system(size: 22))
                            .foregroundStyle(Color.msgBlue)
                    }
                }
                .transition(.opacity.combined(with: .scale))
            }
            HStack {
                TextField("Aa", text: $text, axis: .vertical)
                    .font(.msgMessageBody).lineLimit(1...5)
                Image(systemName: "face.smiling").font(.system(size: 20))
                    .foregroundStyle(Color.msgTextSecondary)
            }
            .padding(.horizontal, 12).frame(minHeight: 36)
            .background(Capsule().fill(Color.msgSurface))

            // Big-thumb: 👍 when empty (one-tap like), filled send when text exists
            Button { text = "" } label: {
                if hasText {
                    Image(systemName: "paperplane.fill")
                        .font(.system(size: 16, weight: .bold)).foregroundStyle(.white)
                        .frame(width: 32, height: 32)
                        .background(Circle().fill(Color.msgBlue))
                } else {
                    Image(systemName: "hand.thumbsup.fill")
                        .font(.system(size: 22)).foregroundStyle(Color.msgBlue)
                        .frame(width: 32, height: 32)
                }
            }
            .sensoryFeedback(.impact(weight: .light), trigger: hasText)
        }
        .padding(.horizontal, 10).padding(.vertical, 8)
        .animation(.spring(response: 0.18, dampingFraction: 0.7), value: hasText)
    }
}
```

### Conversation Row (active-now dot)

```swift
struct ConversationRow: View {
    let name: String
    let preview: String
    let time: String
    let unread: Bool
    let activeNow: Bool

    var body: some View {
        HStack(spacing: 12) {
            ZStack(alignment: .bottomTrailing) {
                Circle().fill(Color.msgSurface).frame(width: 56, height: 56)
                if activeNow {
                    Circle().fill(Color.msgActiveGreen)
                        .frame(width: 14, height: 14)
                        .overlay(Circle().strokeBorder(Color.msgCanvas, lineWidth: 2))
                }
            }
            VStack(alignment: .leading, spacing: 3) {
                Text(name)
                    .font(unread ? .msgConvoUnread : .msgConvoName)
                    .foregroundStyle(Color.msgTextPrimary)
                Text(preview).font(.msgPreview)
                    .foregroundStyle(Color.msgTextSecondary).lineLimit(1)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 6) {
                Text(time).font(.msgTimestamp).foregroundStyle(Color.msgTextSecondary)
                if unread { Circle().fill(Color.msgBlue).frame(width: 8, height: 8) }
            }
        }
        .padding(.vertical, 12).padding(.horizontal, 16)
        .frame(height: 72).contentShape(Rectangle())
    }
}
```

## 4. Typing Indicator

```swift
struct TypingBubble: View {
    @State private var phase = 0.0
    var body: some View {
        HStack(spacing: 4) {
            ForEach(0..<3) { i in
                Circle().fill(Color.msgTextSecondary).frame(width: 7, height: 7)
                    .offset(y: sin(phase + Double(i) * 0.6) * 3)
            }
        }
        .padding(.vertical, 12).padding(.horizontal, 14)
        .background(Capsule().fill(Color.msgIncoming))
        .onAppear {
            withAnimation(.linear(duration: 0.9).repeatForever(autoreverses: false)) {
                phase = .pi * 2
            }
        }
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithOpaqueBackground()
        a.backgroundColor = UIColor.systemBackground
        a.shadowColor = UIColor(Color.msgDivider)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            ChatsView().tabItem { Label("Chats", systemImage: "message.fill") }
            MarketplaceView().tabItem { Label("Marketplace", systemImage: "storefront.fill") }
            StoriesView().tabItem { Label("Stories", systemImage: "play.circle.fill") }
        }
        .tint(.msgBlue)
    }
}
```

## 6. Motion

```swift
// Reactions popover enter — capsule + staggered emoji (see ReactionsPopover)
.animation(.spring(response: 0.32, dampingFraction: 0.6), value: shown)

// Reaction badge land bounce
.animation(.spring(response: 0.25, dampingFraction: 0.5), value: landed)

// Sent bubble pop
.scaleEffect(entered ? 1 : 0.85)
.animation(.spring(response: 0.3, dampingFraction: 0.65), value: entered)

// Big-thumb morph
.animation(.spring(response: 0.18, dampingFraction: 0.7), value: hasText)

// Typing dots — continuous sine offset (see TypingBubble)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Send (text present) | `paperplane.fill` | 16pt |
| Like / thumbs-up (empty composer) | `hand.thumbsup.fill` | 22pt |
| Camera | `camera` | 22pt |
| Photo | `photo` | 22pt |
| Mic | `mic` | 22pt |
| Emoji / sticker | `face.smiling` | 20-22pt |
| More reactions | `plus` | 18pt |
| Audio call | `phone` | 22pt |
| Video call | `video` | 22pt |
| Compose | `square.and.pencil` | 22pt |
| Search | `magnifyingglass` | 16pt |
| Chats (tab) | `message.fill` | 26pt |
| Marketplace (tab) | `storefront.fill` | 26pt |
| Stories (tab) | `play.circle.fill` | 26pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`TextField(axis: .vertical)` needs iOS 16; `.sensoryFeedback` needs iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on conversation names and message bodies — cap body at ~22pt; pin bubble meta, reaction counts, tab labels
- VoiceOver: announce the gradient bubble as a normal outgoing message ("You said…"); the gradient is decorative and must not add noise. Announce reactions as "Reacted ❤️ by 3"
- The reactions popover must be operable with VoiceOver: expose each emoji as a button with a label ("React with heart"); the "+" opens the full picker
- The big-thumb send must announce its current action: "Send like" when empty, "Send" when text exists
- Contrast: `#65676B` on `#FFFFFF` passes WCAG AA at 13pt+; white text on the gradient passes AA across all stops; verify the lightest pink region and add a subtle text shadow if needed
- Honor Reduce Motion: replace the popover spring/stagger and bubble pop with a quick fade
