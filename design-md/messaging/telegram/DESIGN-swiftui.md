# Telegram (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Telegram's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

Telegram's entire color system is themeable — accent colors flow through as a single variable. Model this as a `ThemeToken` type backed by `@Environment` so views can re-tint when the user picks a different accent.

```swift
import SwiftUI

extension Color {
    // MARK: - Default Accent (user-themeable)
    static let tgAccent        = Color(red: 0.000, green: 0.533, blue: 0.800) // #0088CC
    static let tgAccentLight   = Color(red: 0.251, green: 0.655, blue: 0.890) // #40A7E3
    static let tgAccentPressed = Color(red: 0.000, green: 0.443, blue: 0.690) // #0071B0

    // MARK: - Bubble (default blue theme)
    static let tgBubbleOutgoing     = Color(red: 0.169, green: 0.525, blue: 0.992) // #2B86FD
    static let tgBubbleOutgoingTop  = Color(red: 0.169, green: 0.525, blue: 0.992) // #2B86FD (gradient top)
    static let tgBubbleOutgoingBot  = Color(red: 0.380, green: 0.702, blue: 1.000) // #61B3FF (gradient bottom)
    static let tgBubbleIncomingLight = Color.white
    static let tgBubbleIncomingDark  = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A

    // MARK: - Canvas
    static let tgCanvasLight   = Color.white                                    // #FFFFFF
    static let tgCanvasDark    = Color(red: 0.129, green: 0.129, blue: 0.129)   // #212121
    static let tgCanvasOLED    = Color.black                                     // #000000
    static let tgChatBGBlue    = Color(red: 0.859, green: 0.906, blue: 0.957)   // #DBE7F4 (default blue wallpaper)
    static let tgSurface1Light = Color(red: 0.969, green: 0.969, blue: 0.969)   // #F7F7F7
    static let tgSurface1Dark  = Color(red: 0.110, green: 0.110, blue: 0.110)   // #1C1C1C
    static let tgSurface2Dark  = Color(red: 0.173, green: 0.173, blue: 0.180)   // #2C2C2E
    static let tgDividerLight  = Color(red: 0.780, green: 0.780, blue: 0.800)   // #C7C7CC
    static let tgDividerDark   = Color(red: 0.220, green: 0.220, blue: 0.220)   // #383838

    // MARK: - Text
    static let tgTextPrimary       = Color.black
    static let tgTextSecondary     = Color(red: 0.439, green: 0.459, blue: 0.475) // #707579
    static let tgTextTertiary      = Color(red: 0.627, green: 0.651, blue: 0.678) // #A0A6AD
    static let tgTextPrimaryDark   = Color.white
    static let tgTextSecondaryDark = Color(red: 0.553, green: 0.557, blue: 0.576) // #8D8E93

    // MARK: - Semantic
    static let tgOnlineGreen   = Color(red: 0.302, green: 0.827, blue: 0.392) // #4DD364
    static let tgErrorRed      = Color(red: 0.898, green: 0.224, blue: 0.208) // #E53935
    static let tgDestructive   = Color(red: 0.890, green: 0.333, blue: 0.380) // #E35561
    static let tgPremiumA      = Color(red: 0.682, green: 0.435, blue: 0.992) // #AE6FFD
    static let tgPremiumB      = Color(red: 0.808, green: 0.420, blue: 1.000) // #CE6BFF
}

// Sender color palette (for group chats)
enum TgSenderColor: Int, CaseIterable {
    case red, orange, purple, green, teal, blue, pink
    var color: Color {
        switch self {
        case .red:    return Color(red: 0.988, green: 0.361, blue: 0.318)
        case .orange: return Color(red: 0.980, green: 0.475, blue: 0.059)
        case .purple: return Color(red: 0.537, green: 0.365, blue: 0.835)
        case .green:  return Color(red: 0.059, green: 0.698, blue: 0.592)
        case .teal:   return Color(red: 0.000, green: 0.757, blue: 0.651)
        case .blue:   return Color(red: 0.235, green: 0.647, blue: 0.925)
        case .pink:   return Color(red: 1.000, green: 0.322, blue: 0.455)
        }
    }
    static func forSender(_ id: Int) -> Color {
        return allCases[abs(id) % allCases.count].color
    }
}
```

### Theming hook

```swift
struct TelegramTheme {
    var accent: Color = .tgAccent
    var outgoingBubble: Color = .tgBubbleOutgoing
    var useGradientBubbles: Bool = false
    var isDark: Bool = false
    var isOLED: Bool = false
}

private struct ThemeKey: EnvironmentKey {
    static let defaultValue = TelegramTheme()
}

extension EnvironmentValues {
    var telegramTheme: TelegramTheme {
        get { self[ThemeKey.self] }
        set { self[ThemeKey.self] = newValue }
    }
}
```

## 2. Typography

Telegram uses SF Pro exclusively on iOS.

```swift
extension Font {
    static let tgLargeTitle     = Font.system(size: 34, weight: .bold)
    static let tgNavTitle       = Font.system(size: 17, weight: .semibold)
    static let tgContactName    = Font.system(size: 17, weight: .semibold)
    static let tgBubbleBody     = Font.system(size: 17, weight: .regular)
    static let tgGroupSender    = Font.system(size: 14, weight: .semibold)
    static let tgMessagePreview = Font.system(size: 15, weight: .regular)
    static let tgTimestampList  = Font.system(size: 13, weight: .regular)
    static let tgTimestampBubble = Font.system(size: 11, weight: .regular)
    static let tgCaption        = Font.system(size: 12, weight: .regular)
    static let tgTabLabel       = Font.system(size: 10, weight: .medium)
    static let tgButton         = Font.system(size: 17, weight: .semibold)
    static let tgCode           = Font.system(size: 15, weight: .regular, design: .monospaced)
}
```

## 3. Signature Components

### Compose Bar (with morphing send)

```swift
struct TgComposeBar: View {
    @State private var text: String = ""
    @State private var showSilentMenu = false
    @Environment(\.telegramTheme) private var theme

    var body: some View {
        HStack(spacing: 16) {
            Button { /* attach */ } label: {
                Image(systemName: "paperclip")
                    .font(.system(size: 22))
                    .foregroundStyle(.tgTextSecondary)
                    .rotationEffect(.degrees(45))
            }

            TextField("Message", text: $text, axis: .vertical)
                .font(.system(size: 16))
                .lineLimit(1...5)

            Button { /* emoji */ } label: {
                Image(systemName: "face.smiling")
                    .font(.system(size: 22))
                    .foregroundStyle(.tgTextSecondary)
            }

            if text.isEmpty {
                Button { } label: {
                    Image(systemName: "mic.fill")
                        .font(.system(size: 22))
                        .foregroundStyle(theme.accent)
                }
            } else {
                Button {
                    sendMessage()
                } label: {
                    Image(systemName: "paperplane.fill")
                        .font(.system(size: 22))
                        .foregroundStyle(theme.accent)
                }
                .sensoryFeedback(.impact(flexibility: .soft), trigger: text)
                .contextMenu {
                    Button("Send Without Sound", systemImage: "speaker.slash") { sendSilent() }
                    Button("Schedule Message", systemImage: "calendar") { scheduleSend() }
                    Button("Send When Online", systemImage: "person.wave.2") { sendWhenOnline() }
                }
            }
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 10)
        .background(Color.tgSurface1Light)
        .overlay(Divider(), alignment: .top)
    }

    private func sendMessage() { text = "" }
    private func sendSilent() { text = "" }
    private func scheduleSend() { }
    private func sendWhenOnline() { }
}
```

### Outgoing Bubble (Default Blue + Gradient Variant)

```swift
struct TgOutgoingBubble: View {
    let text: String
    let timestamp: String
    let isRead: Bool
    @Environment(\.telegramTheme) private var theme

    var body: some View {
        HStack {
            Spacer(minLength: UIScreen.main.bounds.width * 0.2)
            VStack(alignment: .trailing, spacing: 4) {
                Text(text)
                    .font(.tgBubbleBody)
                    .foregroundStyle(.white)
                HStack(spacing: 2) {
                    Text(timestamp)
                        .font(.tgTimestampBubble)
                        .foregroundStyle(.white.opacity(0.8))
                    Image(systemName: isRead ? "checkmark.circle.fill" : "checkmark")
                        .font(.system(size: 12, weight: .semibold))
                        .foregroundStyle(.white.opacity(0.9))
                }
            }
            .padding(.vertical, 8)
            .padding(.horizontal, 12)
            .background(bubbleBackground)
        }
        .padding(.trailing, 8)
    }

    @ViewBuilder
    private var bubbleBackground: some View {
        let shape = UnevenRoundedRectangle(
            topLeadingRadius: 17,
            bottomLeadingRadius: 17,
            bottomTrailingRadius: 6, // the "notch" tail corner
            topTrailingRadius: 17
        )
        if theme.useGradientBubbles {
            shape.fill(LinearGradient(colors: [.tgBubbleOutgoingTop, .tgBubbleOutgoingBot], startPoint: .top, endPoint: .bottom))
        } else {
            shape.fill(theme.outgoingBubble)
        }
    }
}
```

### Incoming Bubble

```swift
struct TgIncomingBubble: View {
    let text: String
    let senderName: String?
    let senderId: Int?

    var body: some View {
        HStack {
            VStack(alignment: .leading, spacing: 2) {
                if let senderName, let senderId {
                    Text(senderName)
                        .font(.tgGroupSender)
                        .foregroundStyle(TgSenderColor.forSender(senderId))
                }
                Text(text).font(.tgBubbleBody).foregroundStyle(.tgTextPrimary)
            }
            .padding(.vertical, 8)
            .padding(.horizontal, 12)
            .background(
                UnevenRoundedRectangle(
                    topLeadingRadius: 17,
                    bottomLeadingRadius: 6, // tail notch
                    bottomTrailingRadius: 17,
                    topTrailingRadius: 17
                )
                .fill(Color.white)
            )
            Spacer(minLength: UIScreen.main.bounds.width * 0.2)
        }
        .padding(.leading, 8)
    }
}
```

### Floating Voice Mini-Player

```swift
struct TgVoiceMiniPlayer: View {
    let title: String
    let progress: Double
    var onClose: () -> Void = {}

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: "pause.fill")
                .font(.system(size: 14, weight: .bold))
                .foregroundStyle(.tgAccent)
            VStack(alignment: .leading, spacing: 2) {
                Text(title)
                    .font(.system(size: 13, weight: .semibold))
                    .foregroundStyle(.tgTextPrimary)
                GeometryReader { geo in
                    ZStack(alignment: .leading) {
                        Capsule().fill(Color.tgDividerLight).frame(height: 2)
                        Capsule().fill(Color.tgAccent).frame(width: geo.size.width * progress, height: 2)
                    }
                }
                .frame(height: 2)
            }
            Button(action: onClose) {
                Image(systemName: "xmark").font(.system(size: 13, weight: .bold)).foregroundStyle(.tgTextSecondary)
            }
        }
        .padding(.horizontal, 14)
        .padding(.vertical, 8)
        .frame(height: 40)
        .background(
            Capsule()
                .fill(Color.white)
                .shadow(color: .black.opacity(0.16), radius: 16, y: 4)
        )
        .padding(.horizontal, 16)
    }
}
```

### Swipe-to-Reply Gesture

```swift
struct TgSwipeToReply<Content: View>: View {
    @ViewBuilder let content: Content
    @State private var offset: CGFloat = 0
    @State private var hasTicked = false
    let threshold: CGFloat = 60

    var body: some View {
        content
            .offset(x: offset)
            .overlay(alignment: .leading) {
                Image(systemName: "arrowshape.turn.up.left.fill")
                    .foregroundStyle(.tgAccent)
                    .opacity(min(1, offset / threshold))
                    .padding(.leading, -32 + offset * 0.3)
            }
            .gesture(
                DragGesture()
                    .onChanged { g in
                        offset = max(0, min(g.translation.width, threshold + 20))
                        if offset >= threshold && !hasTicked {
                            hasTicked = true
                            UIImpactFeedbackGenerator(style: .soft).impactOccurred()
                        }
                    }
                    .onEnded { _ in
                        if offset >= threshold { /* trigger reply */ }
                        withAnimation(.spring(response: 0.25, dampingFraction: 0.8)) { offset = 0 }
                        hasTicked = false
                    }
            )
    }
}
```

### Animated Emoji (Lottie)

```swift
// Use lottie-ios SPM package. Render large emoji as Lottie animations.
import Lottie

struct TgAnimatedEmoji: View {
    let animationName: String
    @State private var play = false

    var body: some View {
        LottieView(animation: .named(animationName))
            .playing(loopMode: .playOnce)
            .frame(width: 72, height: 72)
            .onTapGesture { play.toggle() }
    }
}
```

## 4. Chat List Row

```swift
struct TgChatListRow: View {
    let avatar: Image
    let name: String
    let preview: String
    let timestamp: String
    let unreadCount: Int
    let isPinned: Bool
    let isMuted: Bool

    var body: some View {
        HStack(spacing: 12) {
            avatar
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(width: 54, height: 54)
                .clipShape(Circle())

            VStack(alignment: .leading, spacing: 4) {
                HStack(spacing: 4) {
                    Text(name)
                        .font(.tgContactName)
                        .foregroundStyle(.tgTextPrimary)
                        .lineLimit(1)
                    if isMuted {
                        Image(systemName: "speaker.slash.fill")
                            .font(.system(size: 11))
                            .foregroundStyle(.tgTextSecondary)
                    }
                }
                Text(preview)
                    .font(.tgMessagePreview)
                    .foregroundStyle(.tgTextSecondary)
                    .lineLimit(1)
            }

            Spacer()

            VStack(alignment: .trailing, spacing: 4) {
                Text(timestamp)
                    .font(.tgTimestampList)
                    .foregroundStyle(.tgTextSecondary)
                HStack(spacing: 4) {
                    if isPinned {
                        Image(systemName: "pin.fill")
                            .font(.system(size: 11))
                            .foregroundStyle(.tgTextSecondary)
                    }
                    if unreadCount > 0 {
                        Text("\(unreadCount)")
                            .font(.system(size: 13, weight: .semibold))
                            .foregroundStyle(.white)
                            .padding(.horizontal, 7)
                            .padding(.vertical, 2)
                            .background(RoundedRectangle(cornerRadius: 10).fill(isMuted ? Color.tgTextTertiary : Color.tgAccent))
                    }
                }
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 76)
        .background(isPinned ? Color.tgSurface1Light : Color.clear)
        .contentShape(Rectangle())
    }
}
```

## 5. Tab Bar

```swift
struct TgRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterial)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            ContactsView().tabItem { Label("Contacts", systemImage: "person.2.fill") }
            CallsView().tabItem { Label("Calls", systemImage: "phone.fill") }
            ChatsView().tabItem { Label("Chats", systemImage: "bubble.left.and.bubble.right.fill") }
                .badge(12)
            SettingsView().tabItem { Label("Settings", systemImage: "gearshape.fill") }
        }
        .tint(.tgAccent)
    }
}
```

## 6. Motion

```swift
// Silent-send popover uses the standard contextMenu
.contextMenu { /* menu items */ }

// Swipe-to-reply haptic tick
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Bubble arrival spring
withAnimation(.spring(response: 0.4, dampingFraction: 0.6)) { messages.append(new) }

// Reaction palette appearance (scale from center)
.transition(.scale.combined(with: .opacity))

// Voice mini-player slide in from top
.transition(.move(edge: .top).combined(with: .opacity))

// Context menu open (bubble lifts)
.scaleEffect(menuOpen ? 1.05 : 1.0)

// Interactive emoji full-screen (heart/rocket)
// Trigger a ZStack overlay Lottie at 1024x1024 and auto-dismiss on complete
```

## 7. SF Symbols + Telegram Custom Glyphs

Telegram uses a mix of SF Symbols and a proprietary outlined icon set. Use SF Symbols where semantically equivalent; for brand-specific icons (chat bubble, channel broadcast, sticker, premium star) use custom Lottie or SVG assets.

| Component | Symbol |
|-----------|--------|
| Send | `paperplane.fill` (or custom Telegram plane) |
| Mic | `mic.fill` |
| Attach | `paperclip` (rotated 45°) |
| Emoji | `face.smiling` |
| Sticker | Custom outlined sticker glyph |
| Read ticks | `checkmark` (single) / `checkmark.circle.fill` (double) |
| Phone | `phone.fill` |
| Video | `video.fill` |
| Lock (secret chat) | `lock.fill` |
| Pinned | `pin.fill` |
| Muted | `speaker.slash.fill` |
| Chats tab | `bubble.left.and.bubble.right.fill` |
| Contacts tab | `person.2.fill` |
| Calls tab | `phone.fill` |
| Settings tab | `gearshape.fill` |
| Channel broadcast | Custom megaphone |
| Premium star | Custom Telegram gradient star |
| Search | `magnifyingglass` |
| Ellipsis | `ellipsis` |
| Schedule | `calendar` |
| Silent | `speaker.slash` |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`UnevenRoundedRectangle` + `.sensoryFeedback` iOS 17+; fall back to `UIImpactFeedbackGenerator`)
- Dynamic Type: supported on bubble body, contact names, previews; fixed on inline timestamps
- VoiceOver: bubble text + timestamp + read state as a single combined accessibility element
- RTL: supported natively; bubble tail notch auto-mirrors
- Themeable accent: inject `TelegramTheme` via `.environment(\.telegramTheme, ...)` at the app root; change accent and every consuming view re-tints live
- OLED mode: detect via `@Environment(\.colorScheme)` plus a stored user preference; swap canvas to pure `#000000` when enabled
- Lottie playback: respect `UIAccessibility.isReduceMotionEnabled` — fall back to a static PNG when reduce-motion is on
- Inline Markdown: parse `**bold**`, `__italic__`, `||spoiler||`, `` `code` `` into `AttributedString` for rendering; spoiler uses a blurred overlay that taps to reveal
