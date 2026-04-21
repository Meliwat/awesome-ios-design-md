# Slack (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Slack's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Sidebar (Default Aubergine theme — workspace-customizable)
    static let slackAubergine        = Color(red: 0.290, green: 0.082, blue: 0.294) // #4A154B
    static let slackAubergineDark    = Color(red: 0.247, green: 0.055, blue: 0.251) // #3F0E40
    static let slackAubergineActive  = Color(red: 0.067, green: 0.392, blue: 0.639) // #1164A3
    static let slackSidebarText      = Color(red: 0.812, green: 0.765, blue: 0.812) // #CFC3CF
    static let slackSidebarActiveText = Color.white                                  // #FFFFFF

    // MARK: - Brand Palette (Logo + Accents)
    static let slackLogoYellow  = Color(red: 0.925, green: 0.698, blue: 0.180) // #ECB22E
    static let slackLogoPink    = Color(red: 0.878, green: 0.118, blue: 0.353) // #E01E5A
    static let slackLogoGreen   = Color(red: 0.180, green: 0.714, blue: 0.490) // #2EB67D
    static let slackLogoBlue    = Color(red: 0.212, green: 0.773, blue: 0.941) // #36C5F0

    // MARK: - Canvas (Light)
    static let slackCanvas      = Color.white                                     // #FFFFFF
    static let slackSurface     = Color(red: 0.973, green: 0.973, blue: 0.973)   // #F8F8F8
    static let slackPressed     = Color(red: 0.902, green: 0.902, blue: 0.902)   // #E6E6E6
    static let slackDivider     = Color(red: 0.867, green: 0.867, blue: 0.867)   // #DDDDDD

    // MARK: - Canvas (Dark)
    static let slackDarkCanvas    = Color(red: 0.102, green: 0.114, blue: 0.129) // #1A1D21
    static let slackDarkSurface1  = Color(red: 0.133, green: 0.145, blue: 0.161) // #222529
    static let slackDarkSurface2  = Color(red: 0.173, green: 0.176, blue: 0.188) // #2C2D30
    static let slackDarkDivider   = Color(red: 0.208, green: 0.216, blue: 0.231) // #35373B

    // MARK: - Text
    static let slackTextPrimary   = Color(red: 0.114, green: 0.110, blue: 0.114) // #1D1C1D
    static let slackTextSecondary = Color(red: 0.380, green: 0.376, blue: 0.380) // #616061
    static let slackTextTertiary  = Color(red: 0.525, green: 0.525, blue: 0.525) // #868686
    static let slackDarkTextPrimary = Color(red: 0.820, green: 0.824, blue: 0.827) // #D1D2D3
    static let slackDarkTextSecondary = Color(red: 0.671, green: 0.671, blue: 0.678) // #ABABAD

    // MARK: - Semantic
    static let slackOnlineGreen  = Color(red: 0.000, green: 0.478, blue: 0.353) // #007A5A
    static let slackMentionRed   = Color(red: 0.878, green: 0.118, blue: 0.353) // #E01E5A
    static let slackTypingBlue   = Color(red: 0.071, green: 0.392, blue: 0.639) // #1264A3
    static let slackLinkBlue     = Color(red: 0.071, green: 0.392, blue: 0.639) // #1264A3

    // MARK: - Mention pill backgrounds
    static let slackMentionChannelBg = Color(red: 0.961, green: 0.890, blue: 0.745) // #F5E3BE
    static let slackMentionHereBg    = Color(red: 0.996, green: 0.976, blue: 0.906) // #FEF9E7
    static let slackMentionUserBg    = Color(red: 0.976, green: 0.835, blue: 0.859) // #F9D5DB
    static let slackReactionSelfBg   = Color(red: 0.890, green: 0.949, blue: 0.988) // #E3F2FC
}
```

## 2. Typography

Slack Lato is proprietary — bundle the TTFs via `Info.plist` (`UIAppFonts`). Fall back to the free `Lato` from Google Fonts, which is visually very close. On iOS, falling back to SF Pro renders crisp but lacks Lato's warmth.

```swift
extension Font {
    // Workspace / Channel
    static let slackWorkspaceName  = Font.custom("SlackLato-Bold",    size: 18).weight(.bold)
    static let slackChannelActive  = Font.custom("SlackLato-Bold",    size: 15).weight(.bold)
    static let slackChannelDefault = Font.custom("SlackLato-Regular", size: 15).weight(.regular)
    static let slackChannelHeader  = Font.custom("SlackLato-Bold",    size: 17).weight(.bold)
    static let slackChannelTopic   = Font.custom("SlackLato-Regular", size: 13).weight(.regular)

    // Messages
    static let slackUsername       = Font.custom("SlackLato-Bold",    size: 15).weight(.bold)
    static let slackMessageBody    = Font.custom("SlackLato-Regular", size: 15).weight(.regular)
    static let slackTimestamp      = Font.custom("SlackLato-Regular", size: 12).weight(.regular)

    // Threads & Reactions
    static let slackThreadCount    = Font.custom("SlackLato-Semibold", size: 13).weight(.semibold)
    static let slackReactionCount  = Font.custom("SlackLato-Semibold", size: 12).weight(.semibold)

    // Buttons / Tabs
    static let slackButton         = Font.custom("SlackLato-Bold",    size: 15).weight(.bold)
    static let slackButtonSecondary = Font.custom("SlackLato-Semibold", size: 14).weight(.semibold)
    static let slackTab            = Font.custom("SlackLato-Semibold", size: 11).weight(.semibold)
    static let slackSectionHeader  = Font.custom("SlackLato-Bold",    size: 11).weight(.bold)
    static let slackCodeInline     = Font.custom("Menlo-Regular",     size: 13)
}
```

## 3. Signature Components

### Slack Sidebar (Aubergine)

```swift
struct SlackSidebar: View {
    let workspaceName: String
    let sidebarColor: Color  // pass .slackAubergine or workspace-customized
    let sections: [SidebarSection]

    struct SidebarSection: Identifiable {
        let id = UUID()
        let title: String
        let channels: [ChannelRow]
    }

    struct ChannelRow: Identifiable {
        let id = UUID()
        let type: ChannelType
        let name: String
        let isUnread: Bool
        let mentionCount: Int
    }

    enum ChannelType {
        case public_, private_, directMessage(avatarUrl: String)
    }

    var body: some View {
        VStack(spacing: 0) {
            // Workspace header
            HStack(spacing: 12) {
                RoundedRectangle(cornerRadius: 4)
                    .fill(Color.slackLogoBlue)
                    .frame(width: 32, height: 32)
                    .overlay(Text("A").foregroundStyle(.white).font(.system(size: 16, weight: .bold)))

                VStack(alignment: .leading, spacing: 2) {
                    HStack(spacing: 4) {
                        Text(workspaceName).font(.slackWorkspaceName).foregroundStyle(.white)
                        Image(systemName: "chevron.down").font(.system(size: 10)).foregroundStyle(.white.opacity(0.7))
                    }
                    HStack(spacing: 4) {
                        Circle().fill(Color.slackOnlineGreen).frame(width: 8, height: 8)
                        Text("You").font(.slackChannelTopic).foregroundStyle(Color.slackSidebarText)
                    }
                }

                Spacer()

                Image(systemName: "square.and.pencil").font(.system(size: 18)).foregroundStyle(.white.opacity(0.7))
            }
            .padding(.horizontal, 16)
            .frame(height: 56)

            // Sections
            ScrollView {
                ForEach(sections) { section in
                    VStack(alignment: .leading, spacing: 2) {
                        Text(section.title.uppercased())
                            .font(.slackSectionHeader)
                            .foregroundStyle(Color.slackSidebarText.opacity(0.5))
                            .padding(.horizontal, 16)
                            .padding(.top, 16)
                            .padding(.bottom, 4)

                        ForEach(section.channels) { channel in
                            SidebarChannelRow(channel: channel, isActive: false)
                        }
                    }
                }
                Spacer(minLength: 40)
            }
        }
        .frame(maxHeight: .infinity)
        .background(sidebarColor)
    }
}

struct SidebarChannelRow: View {
    let channel: SlackSidebar.ChannelRow
    let isActive: Bool

    var body: some View {
        HStack(spacing: 12) {
            // Leading icon
            switch channel.type {
            case .public_:
                Text("#")
                    .font(.system(size: 16, weight: .regular))
                    .foregroundStyle(isActive ? Color.white : Color.slackSidebarText)
                    .frame(width: 20)
            case .private_:
                Image(systemName: "lock.fill")
                    .font(.system(size: 14))
                    .foregroundStyle(isActive ? Color.white : Color.slackSidebarText)
                    .frame(width: 20)
            case .directMessage(let url):
                AsyncImage(url: URL(string: url)) { img in img.resizable() } placeholder: {
                    RoundedRectangle(cornerRadius: 3).fill(Color.slackSidebarText.opacity(0.3))
                }
                .frame(width: 20, height: 20)
                .clipShape(RoundedRectangle(cornerRadius: 3))
            }

            // Channel name
            Text(channel.name)
                .font(isActive ? .slackChannelActive : (channel.isUnread ? .slackChannelActive : .slackChannelDefault))
                .foregroundStyle(
                    isActive ? Color.white :
                        (channel.isUnread ? Color.white : Color.slackSidebarText)
                )

            Spacer()

            // Mention badge
            if channel.mentionCount > 0 {
                Text("\(channel.mentionCount)")
                    .font(.slackReactionCount)
                    .foregroundStyle(.white)
                    .padding(.horizontal, 6)
                    .padding(.vertical, 2)
                    .background(Capsule().fill(Color.slackMentionRed))
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 32)
        .background(isActive ? Color.slackAubergineActive : Color.clear)
    }
}
```

### Message Row

```swift
struct SlackMessageRow: View {
    let username: String
    let avatarUrl: String?
    let statusEmoji: String?
    let status: String?
    let isOnline: Bool
    let timestamp: String
    let body: String
    let reactions: [Reaction]
    let threadReplyCount: Int?
    let threadLastReply: String?
    let isNewSender: Bool  // true = show avatar + username; false = collapsed

    struct Reaction: Identifiable {
        let id = UUID()
        let emoji: String
        let count: Int
        let youReacted: Bool
    }

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            if isNewSender {
                ZStack(alignment: .bottomTrailing) {
                    RoundedRectangle(cornerRadius: 4)
                        .fill(Color.slackLogoGreen)
                        .frame(width: 36, height: 36)
                    if isOnline {
                        Circle().fill(Color.slackOnlineGreen)
                            .frame(width: 10, height: 10)
                            .overlay(Circle().stroke(Color.slackCanvas, lineWidth: 2))
                            .offset(x: 2, y: 2)
                    }
                }
            } else {
                Color.clear.frame(width: 36, height: 0)
            }

            VStack(alignment: .leading, spacing: 4) {
                if isNewSender {
                    HStack(spacing: 6) {
                        Text(username).font(.slackUsername).foregroundStyle(Color.slackTextPrimary)
                        if let statusEmoji { Text(statusEmoji) }
                        if let status { Text(status).font(.slackChannelTopic).foregroundStyle(Color.slackTextSecondary).lineLimit(1) }
                        Text(timestamp).font(.slackTimestamp).foregroundStyle(Color.slackTextSecondary)
                    }
                }

                Text(body)
                    .font(.slackMessageBody)
                    .foregroundStyle(Color.slackTextPrimary)
                    .fixedSize(horizontal: false, vertical: true)

                if !reactions.isEmpty {
                    HStack(spacing: 4) {
                        ForEach(reactions) { r in ReactionChip(emoji: r.emoji, count: r.count, youReacted: r.youReacted) }
                        AddReactionChip()
                    }
                }

                if let count = threadReplyCount, count > 0 {
                    ThreadIndicator(replyCount: count, lastReply: threadLastReply ?? "")
                }
            }

            Spacer()
        }
        .padding(.horizontal, 16)
        .padding(.vertical, isNewSender ? 8 : 2)
    }
}
```

### Reaction Chip

```swift
struct ReactionChip: View {
    let emoji: String
    let count: Int
    let youReacted: Bool

    var body: some View {
        HStack(spacing: 4) {
            Text(emoji).font(.system(size: 14))
            Text("\(count)").font(.slackReactionCount).foregroundStyle(Color.slackTextPrimary)
        }
        .padding(.horizontal, 8)
        .padding(.vertical, 4)
        .background(
            Capsule().fill(youReacted ? Color.slackReactionSelfBg : Color.slackSurface)
        )
        .overlay(
            Capsule().strokeBorder(youReacted ? Color.slackTypingBlue : .clear, lineWidth: 1)
        )
        .sensoryFeedback(.impact(flexibility: .soft), trigger: youReacted)
    }
}

struct AddReactionChip: View {
    var body: some View {
        Button { /* open picker */ } label: {
            Image(systemName: "face.smiling")
                .font(.system(size: 14))
                .foregroundStyle(Color.slackTextSecondary)
                .padding(.horizontal, 8)
                .padding(.vertical, 4)
                .background(Capsule().fill(Color.slackSurface))
        }
    }
}
```

### Thread Indicator

```swift
struct ThreadIndicator: View {
    let replyCount: Int
    let lastReply: String

    var body: some View {
        Button { /* open thread pane */ } label: {
            HStack(spacing: 6) {
                HStack(spacing: -6) {
                    ForEach(0..<min(3, replyCount), id: \.self) { i in
                        RoundedRectangle(cornerRadius: 3)
                            .fill([Color.slackLogoYellow, Color.slackLogoPink, Color.slackLogoGreen][i % 3])
                            .frame(width: 16, height: 16)
                            .overlay(RoundedRectangle(cornerRadius: 3).stroke(Color.slackCanvas, lineWidth: 1))
                    }
                }
                Text("\(replyCount) \(replyCount == 1 ? "reply" : "replies")")
                    .font(.slackThreadCount)
                    .foregroundStyle(Color.slackLinkBlue)
                Text("• \(lastReply)")
                    .font(.slackTimestamp)
                    .foregroundStyle(Color.slackTextSecondary)
                Spacer()
                Image(systemName: "chevron.right").font(.system(size: 12)).foregroundStyle(Color.slackTextSecondary)
            }
        }
    }
}
```

### Mention Pill (inline)

```swift
struct MentionPill: View {
    enum MentionType { case channel, here, user(name: String) }
    let type: MentionType

    var body: some View {
        Text(text)
            .font(.slackUsername)
            .foregroundStyle(textColor)
            .padding(.horizontal, 4)
            .padding(.vertical, 1)
            .background(RoundedRectangle(cornerRadius: 4).fill(bgColor))
    }

    var text: String {
        switch type {
        case .channel: return "@channel"
        case .here:    return "@here"
        case .user(let name): return "@\(name)"
        }
    }

    var bgColor: Color {
        switch type {
        case .channel: return Color.slackLogoGreen.opacity(0.2)
        case .here:    return Color.slackLogoYellow.opacity(0.2)
        case .user:    return Color.slackMentionUserBg
        }
    }

    var textColor: Color {
        switch type {
        case .channel: return Color(red: 0.059, green: 0.482, blue: 0.424)  // darker green
        case .here:    return Color(red: 0.400, green: 0.322, blue: 0.031)  // darker yellow
        case .user:    return Color(red: 0.353, green: 0.153, blue: 0.192)  // dark red
        }
    }
}
```

### Huddles Banner

```swift
struct HuddlesBanner: View {
    let participantAvatars: [String]
    let onJoin: () -> Void

    var body: some View {
        HStack(spacing: 12) {
            HStack(spacing: 2) {
                Rectangle().fill(Color.slackLogoYellow).frame(width: 8, height: 8)
                Rectangle().fill(Color.slackLogoPink).frame(width: 8, height: 8)
            }
            VStack(alignment: .leading, spacing: 0) {
                HStack(spacing: 2) {
                    Rectangle().fill(Color.slackLogoGreen).frame(width: 8, height: 8)
                    Rectangle().fill(Color.slackLogoBlue).frame(width: 8, height: 8)
                }
            }
            Text("Huddle in progress").font(.slackButton).foregroundStyle(.white)
            Spacer()
            Button(action: onJoin) {
                Text("Join")
                    .font(.slackButtonSecondary)
                    .foregroundStyle(Color.slackTypingBlue)
                    .padding(.horizontal, 16)
                    .padding(.vertical, 8)
                    .background(RoundedRectangle(cornerRadius: 4).fill(.white))
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 56)
        .background(Color.slackTypingBlue)
        .sensoryFeedback(.success, trigger: UUID())
    }
}
```

### Message Composer

```swift
struct MessageComposer: View {
    @State private var text: String = ""

    var body: some View {
        HStack(alignment: .bottom, spacing: 8) {
            Button { /* attach */ } label: {
                Image(systemName: "plus").font(.system(size: 20)).foregroundStyle(Color.slackTextSecondary)
                    .frame(width: 40, height: 40)
            }

            TextField("Message #design", text: $text, axis: .vertical)
                .font(.slackMessageBody)
                .foregroundStyle(Color.slackTextPrimary)
                .lineLimit(1...6)
                .padding(.horizontal, 12)
                .padding(.vertical, 10)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.slackSurface))

            Button { /* send */ } label: {
                Image(systemName: "paperplane.fill")
                    .font(.system(size: 16))
                    .foregroundStyle(.white)
                    .frame(width: 32, height: 32)
                    .background(RoundedRectangle(cornerRadius: 4).fill(text.isEmpty ? Color.slackTextTertiary : Color.slackOnlineGreen))
            }
            .disabled(text.isEmpty)
            .sensoryFeedback(.impact(flexibility: .soft), trigger: text)
        }
        .padding(.horizontal, 8)
        .padding(.vertical, 8)
        .background(Color.slackCanvas)
        .overlay(Rectangle().fill(Color.slackDivider).frame(height: 1), alignment: .top)
    }
}
```

## 4. Tab Bar

```swift
struct SlackTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house") }
            DMsView().tabItem { Label("DMs", systemImage: "bubble.left.and.bubble.right") }
            ActivityView().tabItem { Label("Activity", systemImage: "bell") }
                .badge(3)
            LaterView().tabItem { Label("Later", systemImage: "bookmark") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
        }
        .tint(.slackTextPrimary)
    }
}
```

## 5. Motion

```swift
// Sidebar swipe open/close
// DragGesture with 1:1 tracking; on release, spring(response: 0.25, dampingFraction: 0.8)

// Message send
.sensoryFeedback(.impact(flexibility: .soft), trigger: messageSent)
// Send button scale: 1.0 → 0.9 → 1.0 spring 200ms
// Message flies in from bottom: scale 0.96 → 1.0 + opacity 0 → 1 over 250ms

// Reaction add
// Chip scale 0 → 1.15 → 1.0 spring 300ms + .sensoryFeedback(.impact(flexibility: .soft))

// Thread pane
// Slide from right: .transition(.move(edge: .trailing)) with .animation(.easeOut(duration: 0.25))

// Huddle join
.sensoryFeedback(.success, trigger: huddleJoined)

// Typing indicator
// Text opacity pulses 1.0 ↔ 0.6 over 1.2s with .repeatForever(autoreverses: true)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 24pt |
| DMs (tab) | `bubble.left.and.bubble.right` | 24pt |
| Activity (tab) | `bell` / `bell.fill` | 24pt |
| Later (tab) | `bookmark` / `bookmark.fill` | 24pt |
| Search (tab) | `magnifyingglass` | 24pt |
| Channel (public) | `number` (or text "#") | 16pt |
| Channel (private) | `lock.fill` | 14pt |
| Mute | `bell.slash` | 14pt |
| Huddle start | `headphones` | 22pt |
| Channel info | `info.circle` | 22pt |
| Send | `paperplane.fill` | 16pt |
| Attach | `plus` | 20pt |
| Emoji picker | `face.smiling` | 18pt |
| Mention | `at` | 18pt |
| Voice note | `mic` | 18pt |
| Formatting | `textformat` | 18pt |
| Thread chevron | `chevron.right` | 12pt |
| Bookmark (Later) | `bookmark.fill` | 18pt |
| Star | `star.fill` | 16pt |

## 7. Theme Switcher (Workspace-Customizable Sidebar)

```swift
struct WorkspaceTheme: Equatable {
    var sidebarColor: Color
    var activeChannelColor: Color
}

extension WorkspaceTheme {
    static let aubergine = WorkspaceTheme(sidebarColor: .slackAubergine, activeChannelColor: .slackAubergineActive)
    static let monument  = WorkspaceTheme(sidebarColor: Color(red: 0.173, green: 0.176, blue: 0.188), activeChannelColor: Color(red: 0.282, green: 0.075, blue: 0.286))
    static let tangerine = WorkspaceTheme(sidebarColor: Color(red: 0.792, green: 0.267, blue: 0.000), activeChannelColor: Color(red: 0.420, green: 0.078, blue: 0.047))

    static func custom(hex: String, active: String) -> WorkspaceTheme {
        WorkspaceTheme(sidebarColor: .init(hex: hex), activeChannelColor: .init(hex: active))
    }
}

extension Color {
    init(hex: String) {
        let scanner = Scanner(string: hex.replacingOccurrences(of: "#", with: ""))
        var rgb: UInt64 = 0
        scanner.scanHexInt64(&rgb)
        self.init(.sRGB,
                  red: Double((rgb >> 16) & 0xFF) / 255.0,
                  green: Double((rgb >> 8)  & 0xFF) / 255.0,
                  blue:  Double(rgb         & 0xFF) / 255.0,
                  opacity: 1)
    }
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Bundle Slack Lato TTFs via `Info.plist` / `UIAppFonts`; if unavailable, fall back to Lato (Apache 2.0) or SF Pro
- Dynamic Type: support on message body, username, channel name, thread indicator; keep timestamps, reaction counts, tab labels, section headers at FIXED sizes
- VoiceOver: group message row into one accessible element with combined label: "Sarah Chen, online, status: on vacation, 10:42 AM. 'This is the message content.' 3 reactions. 5 replies in thread."
- Color contrast: `#1D1C1D` on `#FFFFFF` passes WCAG AAA for body text; `#CFC3CF` on `#4A154B` passes AA for sidebar channel text at 15pt; mention pills have carefully-paired BG/FG contrast
- Reduce Motion: skip sidebar spring; use instant open/close; skip chip scale animation on reaction add
- Workspace sidebar theming: colors must work for any user-picked hex — validate contrast ratio and adjust text color (light/dark) based on luminance
- Right-to-left languages: sidebar flips to right side; thread pane flips to left
- Channel `#` symbol: render as text character (not icon) because it's a universal glyph; use icon only for private/muted states
