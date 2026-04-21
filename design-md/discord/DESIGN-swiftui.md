# Discord (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Discord's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let dcBlurple        = Color(red: 0.345, green: 0.396, blue: 0.949) // #5865F2
    static let dcBlurplePressed = Color(red: 0.278, green: 0.322, blue: 0.769) // #4752C4
    static let dcBlurpleLegacy  = Color(red: 0.447, green: 0.537, blue: 0.855) // #7289DA

    // MARK: - Three-Gray Surface System (Dark)
    static let dcServerRail   = Color(red: 0.118, green: 0.122, blue: 0.133) // #1E1F22
    static let dcChannelList  = Color(red: 0.169, green: 0.176, blue: 0.192) // #2B2D31
    static let dcChatCanvas   = Color(red: 0.192, green: 0.200, blue: 0.220) // #313338
    static let dcSurface2     = Color(red: 0.220, green: 0.227, blue: 0.251) // #383A40
    static let dcDivider      = Color(red: 0.247, green: 0.255, blue: 0.278) // #3F4147
    static let dcRowHover     = Color(red: 0.180, green: 0.188, blue: 0.208) // #2E3035
    static let dcActiveChannelBg = Color(red: 0.251, green: 0.259, blue: 0.286) // #404249

    // MARK: - Text
    static let dcTextPrimary    = Color(red: 0.949, green: 0.953, blue: 0.961) // #F2F3F5
    static let dcTextSecondary  = Color(red: 0.710, green: 0.729, blue: 0.757) // #B5BAC1
    static let dcTextMuted      = Color(red: 0.580, green: 0.608, blue: 0.643) // #949BA4
    static let dcTextLink       = Color(red: 0.000, green: 0.659, blue: 0.988) // #00A8FC
    static let dcTextDisabled   = Color(red: 0.365, green: 0.376, blue: 0.412) // #5D6069

    // MARK: - Status
    static let dcOnlineGreen    = Color(red: 0.137, green: 0.647, blue: 0.353) // #23A55A
    static let dcIdleYellow     = Color(red: 0.941, green: 0.698, blue: 0.196) // #F0B232
    static let dcDNDRed         = Color(red: 0.949, green: 0.247, blue: 0.263) // #F23F43
    static let dcOfflineGray    = Color(red: 0.502, green: 0.518, blue: 0.557) // #80848E
    static let dcStreamingPurple = Color(red: 0.349, green: 0.212, blue: 0.584) // #593695

    // MARK: - Destructive / Brand Variants
    static let dcDestructiveRed = Color(red: 0.855, green: 0.216, blue: 0.235) // #DA373C
    static let dcBoostPink      = Color(red: 0.922, green: 0.271, blue: 0.620) // #EB459E
}

// Nitro gradient
extension LinearGradient {
    static let dcNitroGradient = LinearGradient(
        colors: [.dcBlurple, .dcBoostPink],
        startPoint: .topLeading,
        endPoint: .bottomTrailing
    )
}
```

## 2. Typography

gg sans is proprietary. Register via `Info.plist` + `UIAppFonts` or fall back to `.system()` which renders SF Pro on iOS.

```swift
extension Font {
    // Use gg sans if bundled; fall back to .system
    private static func gg(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        if let _ = UIFont(name: "ggSans-Regular", size: size) {
            let suffix: String = {
                switch weight {
                case .bold:     return "Bold"
                case .semibold: return "Semibold"
                case .medium:   return "Medium"
                default:        return "Regular"
                }
            }()
            return .custom("ggSans-\(suffix)", size: size)
        }
        return .system(size: size, weight: weight)
    }

    static let dcScreenTitle    = gg(20, weight: .bold)
    static let dcChannelActive  = gg(16, weight: .semibold)
    static let dcChannelInactive = gg(16, weight: .medium)
    static let dcUsername       = gg(16, weight: .semibold)
    static let dcMessageBody    = gg(16, weight: .regular)
    static let dcTimestamp      = gg(12, weight: .medium)
    static let dcReplyContext   = gg(14, weight: .regular)
    static let dcSystemMessage  = gg(14, weight: .medium)
    static let dcSectionLabel   = gg(12, weight: .bold)
    static let dcMemberName     = gg(14, weight: .medium)
    static let dcButton         = gg(14, weight: .medium)
    static let dcTabLabel       = gg(10, weight: .semibold)
    static let dcCode           = Font.system(size: 14, weight: .regular, design: .monospaced)
}
```

## 3. Signature Components

### Server Rail (The Defining Discord Component)

```swift
struct DCServerRail: View {
    let servers: [Server]
    @Binding var activeServerId: String?

    var body: some View {
        ScrollView {
            VStack(spacing: 8) {
                DCHomeButton()
                Rectangle()
                    .fill(Color.dcDivider)
                    .frame(width: 32, height: 2)
                    .padding(.vertical, 4)

                ForEach(servers) { server in
                    DCServerIcon(
                        server: server,
                        isActive: server.id == activeServerId
                    ) {
                        activeServerId = server.id
                    }
                }

                Spacer(minLength: 16)
                DCAddServerButton()
                DCExploreButton()
            }
            .padding(.vertical, 8)
        }
        .frame(width: 72)
        .background(Color.dcServerRail)
    }
}

struct Server: Identifiable {
    let id: String
    let name: String
    let imageUri: String?
    let initials: String
    let unreadCount: Int
    let mentionCount: Int
}

struct DCServerIcon: View {
    let server: Server
    let isActive: Bool
    let action: () -> Void

    var body: some View {
        HStack(spacing: 0) {
            // Leading active indicator
            Rectangle()
                .fill(Color.dcTextPrimary)
                .frame(width: 4, height: isActive ? 40 : (server.unreadCount > 0 ? 8 : 0))
                .animation(.spring(response: 0.25, dampingFraction: 0.7), value: isActive)

            Button(action: action) {
                ZStack(alignment: .bottomTrailing) {
                    Group {
                        if let uri = server.imageUri, let url = URL(string: uri) {
                            AsyncImage(url: url) { $0.resizable() } placeholder: {
                                Text(server.initials)
                                    .font(.dcButton)
                                    .foregroundStyle(.white)
                                    .frame(maxWidth: .infinity, maxHeight: .infinity)
                                    .background(Color.dcBlurple)
                            }
                        } else {
                            Text(server.initials)
                                .font(.dcButton)
                                .foregroundStyle(.white)
                                .frame(maxWidth: .infinity, maxHeight: .infinity)
                                .background(Color.dcBlurple)
                        }
                    }
                    .frame(width: 48, height: 48)
                    .clipShape(RoundedRectangle(cornerRadius: isActive ? 12 : 16))
                    .animation(.spring(response: 0.3, dampingFraction: 0.7), value: isActive)

                    if server.mentionCount > 0 {
                        Text("\(min(server.mentionCount, 99))")
                            .font(.system(size: 11, weight: .bold))
                            .foregroundStyle(.white)
                            .padding(.horizontal, 5)
                            .frame(minWidth: 18, minHeight: 18)
                            .background(Circle().fill(Color.dcDNDRed))
                            .overlay(Circle().stroke(Color.dcServerRail, lineWidth: 3))
                            .offset(x: 6, y: 6)
                    }
                }
            }
            .buttonStyle(.plain)
            .padding(.leading, 8)
            .padding(.trailing, 12)
        }
    }
}

struct DCHomeButton: View {
    var body: some View {
        RoundedRectangle(cornerRadius: 16)
            .fill(LinearGradient.dcNitroGradient)
            .frame(width: 48, height: 48)
            .overlay(
                Image(systemName: "house.fill")
                    .font(.system(size: 22, weight: .bold))
                    .foregroundStyle(.white)
            )
    }
}

struct DCAddServerButton: View {
    var body: some View {
        Circle()
            .fill(Color.dcChannelList)
            .frame(width: 48, height: 48)
            .overlay(
                Image(systemName: "plus")
                    .font(.system(size: 22, weight: .bold))
                    .foregroundStyle(.dcOnlineGreen)
            )
    }
}

struct DCExploreButton: View {
    var body: some View {
        Circle()
            .fill(Color.dcChannelList)
            .frame(width: 48, height: 48)
            .overlay(
                Image(systemName: "safari.fill")
                    .font(.system(size: 22))
                    .foregroundStyle(.dcOnlineGreen)
            )
    }
}
```

### Message Row (with Role Color + Presence Dot)

```swift
struct DCMessageRow: View {
    let avatar: Image
    let username: String
    let roleColor: Color
    let timestamp: String
    let message: String
    let presenceStatus: PresenceStatus
    let isGroupedWithPrevious: Bool

    enum PresenceStatus { case online, idle, dnd, offline, streaming }

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            if isGroupedWithPrevious {
                Text(timestamp)
                    .font(.system(size: 11, weight: .medium))
                    .foregroundStyle(.dcTextMuted)
                    .frame(width: 40, alignment: .center)
                    .opacity(0) // reserve space; shows on hover in desktop
            } else {
                ZStack(alignment: .bottomTrailing) {
                    avatar
                        .resizable()
                        .aspectRatio(contentMode: .fill)
                        .frame(width: 40, height: 40)
                        .clipShape(Circle())
                    presenceDot
                }
            }

            VStack(alignment: .leading, spacing: 2) {
                if !isGroupedWithPrevious {
                    HStack(spacing: 6) {
                        Text(username)
                            .font(.dcUsername)
                            .foregroundStyle(roleColor)
                        Text(timestamp)
                            .font(.dcTimestamp)
                            .foregroundStyle(.dcTextMuted)
                    }
                }
                Text(message)
                    .font(.dcMessageBody)
                    .foregroundStyle(.dcTextPrimary)
                    .lineSpacing(4)
                    .textSelection(.enabled)
            }

            Spacer()
        }
        .padding(.horizontal, 16)
        .padding(.vertical, isGroupedWithPrevious ? 2 : 8)
    }

    @ViewBuilder
    private var presenceDot: some View {
        let color: Color = {
            switch presenceStatus {
            case .online:    return .dcOnlineGreen
            case .idle:      return .dcIdleYellow
            case .dnd:       return .dcDNDRed
            case .offline:   return .dcOfflineGray
            case .streaming: return .dcStreamingPurple
            }
        }()
        Circle()
            .fill(color)
            .frame(width: 12, height: 12)
            .overlay(Circle().stroke(Color.dcChatCanvas, lineWidth: 2.5))
    }
}
```

### Compose Bar

```swift
struct DCComposeBar: View {
    let channelName: String
    @State private var text: String = ""
    @FocusState private var isFocused: Bool

    var body: some View {
        HStack(spacing: 10) {
            Button { } label: {
                Image(systemName: "plus.circle.fill")
                    .font(.system(size: 22))
                    .foregroundStyle(.dcTextSecondary)
            }

            TextField("Message \(channelName)", text: $text, axis: .vertical)
                .foregroundStyle(.dcTextPrimary)
                .tint(.dcBlurple)
                .focused($isFocused)
                .lineLimit(1...6)
                .padding(.horizontal, 2)

            HStack(spacing: 12) {
                Button { } label: { Image(systemName: "gift") }
                Button { } label: { Image(systemName: "photo.stack") }
                Button { } label: { Image(systemName: "face.smiling") }
            }
            .font(.system(size: 20))
            .foregroundStyle(.dcTextSecondary)

            if !text.isEmpty {
                Button { text = "" } label: {
                    Image(systemName: "paperplane.fill")
                        .font(.system(size: 18, weight: .semibold))
                        .foregroundStyle(.white)
                        .frame(width: 32, height: 32)
                        .background(Circle().fill(Color.dcBlurple))
                }
                .sensoryFeedback(.impact(flexibility: .soft), trigger: text)
            }
        }
        .padding(.horizontal, 12)
        .padding(.vertical, 10)
        .background(
            RoundedRectangle(cornerRadius: 8)
                .fill(Color.dcSurface2)
                .overlay(
                    RoundedRectangle(cornerRadius: 8)
                        .stroke(Color.dcBlurple.opacity(isFocused ? 1 : 0), lineWidth: 2)
                )
        )
        .padding(.horizontal, 12)
        .padding(.bottom, 8)
    }
}
```

### Channel List Row

```swift
struct DCChannelRow: View {
    let name: String
    let type: ChannelType
    let isActive: Bool
    let unreadCount: Int
    let mentionCount: Int

    enum ChannelType { case text, voice, announcement }

    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: iconForType)
                .font(.system(size: 16, weight: .regular))
                .foregroundStyle(.dcTextMuted)
                .frame(width: 20)

            Text(name)
                .font(isActive ? .dcChannelActive : .dcChannelInactive)
                .foregroundStyle(textColor)

            Spacer()

            if mentionCount > 0 {
                Text("\(mentionCount)")
                    .font(.system(size: 12, weight: .bold))
                    .foregroundStyle(.white)
                    .padding(.horizontal, 6)
                    .frame(minWidth: 18, minHeight: 16)
                    .background(Capsule().fill(Color.dcDNDRed))
            }
        }
        .padding(.horizontal, 8)
        .padding(.vertical, 6)
        .frame(height: 36)
        .background(
            RoundedRectangle(cornerRadius: 4)
                .fill(isActive ? Color.dcActiveChannelBg : .clear)
        )
        .overlay(alignment: .leading) {
            if isActive {
                Rectangle()
                    .fill(Color.dcBlurple)
                    .frame(width: 3)
                    .padding(.leading, -4)
            }
        }
    }

    private var iconForType: String {
        switch type {
        case .text:         return "number"
        case .voice:        return "speaker.wave.2.fill"
        case .announcement: return "megaphone.fill"
        }
    }

    private var textColor: Color {
        if isActive || unreadCount > 0 || mentionCount > 0 { return .dcTextPrimary }
        return .dcTextMuted
    }
}
```

### Voice Channel Speaking Ring

```swift
struct DCSpeakingRing<Avatar: View>: View {
    let isActiveSpeaker: Bool
    @ViewBuilder let avatar: Avatar
    @State private var pulseScale: CGFloat = 1.0

    var body: some View {
        avatar
            .overlay(
                Circle()
                    .stroke(Color.dcOnlineGreen, lineWidth: isActiveSpeaker ? 2.5 : 0)
                    .scaleEffect(pulseScale)
                    .opacity(isActiveSpeaker ? 1 : 0)
            )
            .onChange(of: isActiveSpeaker) { _, active in
                if active {
                    withAnimation(.easeInOut(duration: 0.3).repeatForever(autoreverses: true)) {
                        pulseScale = 1.12
                    }
                } else {
                    withAnimation(.easeOut(duration: 0.2)) { pulseScale = 1.0 }
                }
            }
    }
}
```

### Emoji Reaction Chip

```swift
struct DCReactionChip: View {
    let emoji: String
    let count: Int
    let didYouReact: Bool
    let onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            HStack(spacing: 4) {
                Text(emoji).font(.system(size: 16))
                Text("\(count)")
                    .font(.system(size: 13, weight: .medium))
                    .foregroundStyle(.dcTextPrimary)
            }
            .padding(.horizontal, 8)
            .padding(.vertical, 4)
            .background(
                RoundedRectangle(cornerRadius: 8)
                    .fill(didYouReact ? Color.dcBlurple.opacity(0.3) : Color.dcChannelList)
            )
            .overlay(
                RoundedRectangle(cornerRadius: 8)
                    .stroke(didYouReact ? Color.dcBlurple : Color.dcDivider, lineWidth: 1)
            )
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: didYouReact)
    }
}
```

## 4. Tab Bar (Mobile Root)

```swift
struct DCRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor(Color.dcServerRail)
        appearance.shadowColor = UIColor(Color.dcDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            ServersView().tabItem { Label("Servers", systemImage: "square.grid.2x2.fill") }
            MessagesView().tabItem { Label("Messages", systemImage: "bubble.left.and.bubble.right.fill") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell.fill") }
                .badge(5)
            ProfileView().tabItem { Label("You", systemImage: "person.crop.circle.fill") }
        }
        .tint(.dcTextPrimary)
    }
}
```

## 5. Three-Pane Swipe Navigation (Mobile)

Use `NavigationStack` + a custom gesture-driven drawer container.

```swift
struct DCMobileShell: View {
    @State private var drawerOpen: Bool = true
    @State private var activeServerId: String? = nil

    var body: some View {
        GeometryReader { geo in
            let drawerWidth = geo.size.width * 0.85

            ZStack(alignment: .leading) {
                DCChatView()
                    .frame(width: geo.size.width, height: geo.size.height)
                    .offset(x: drawerOpen ? drawerWidth : 0)
                    .animation(.spring(response: 0.3, dampingFraction: 0.85), value: drawerOpen)
                    .disabled(drawerOpen)

                HStack(spacing: 0) {
                    DCServerRail(servers: sampleServers, activeServerId: $activeServerId)
                    DCChannelListPane(activeServerId: activeServerId)
                }
                .frame(width: drawerWidth, height: geo.size.height)
                .offset(x: drawerOpen ? 0 : -drawerWidth)
                .animation(.spring(response: 0.3, dampingFraction: 0.85), value: drawerOpen)
            }
            .gesture(
                DragGesture()
                    .onEnded { g in
                        if g.translation.width > 60 { drawerOpen = true }
                        else if g.translation.width < -60 { drawerOpen = false }
                    }
            )
        }
    }
}
```

## 6. Motion

```swift
// Server icon active squircle morph
.clipShape(RoundedRectangle(cornerRadius: isActive ? 12 : 16))
.animation(.spring(response: 0.3, dampingFraction: 0.7), value: isActive)

// Active indicator bar slide
.frame(height: isActive ? 40 : (hasUnread ? 8 : 0))
.animation(.spring(response: 0.25, dampingFraction: 0.7), value: isActive)

// Speaking ring pulse
withAnimation(.easeInOut(duration: 0.3).repeatForever(autoreverses: true)) {
    pulseScale = 1.12
}

// Reaction added
.sensoryFeedback(.impact(flexibility: .soft), trigger: reactionCount)

// Message arrival
.transition(.move(edge: .bottom).combined(with: .opacity))

// Typing indicator (three dots)
// Use TimelineView with three Circle().scaleEffect staggered by 0.2s offset

// Drawer open/close
.offset(x: drawerOpen ? 0 : -drawerWidth)
.animation(.spring(response: 0.3, dampingFraction: 0.85), value: drawerOpen)
```

## 7. SF Symbols + Custom Discord Glyphs

Discord uses many proprietary glyphs (Discord logo, Nitro star, Wumpus mascot, channel type icons). Use SF Symbols for the closest semantic match; bundle SVG assets for brand glyphs.

| Component | Symbol | Notes |
|-----------|--------|-------|
| Text channel | `number` | `#` |
| Voice channel | `speaker.wave.2.fill` | `🔊` |
| Announcement channel | `megaphone.fill` | |
| Home / Discover | `house.fill` (or custom logo) | Home server icon |
| Add server | `plus` | Green variant |
| Explore servers | `safari.fill` | |
| Message compose attach | `plus.circle.fill` | |
| Gift | `gift` | |
| GIF picker | `photo.stack` | |
| Emoji | `face.smiling` | |
| Send | `paperplane.fill` | |
| Pin | `pin.fill` | |
| Member list | `person.2.fill` | |
| Search | `magnifyingglass` | |
| Notifications tab | `bell.fill` | |
| Profile tab | `person.crop.circle.fill` | |
| Settings | `gearshape.fill` | |
| Mic | `mic.fill` / `mic.slash.fill` | |
| Headphones | `headphones` | |
| Streaming | Custom TV glyph | Use inside presence dot |
| Nitro | Custom Nitro gradient star | Brand glyph |
| Boost | Custom Boost gem | Brand glyph |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` is iOS 17+; fall back to `UIImpactFeedbackGenerator`)
- Dynamic Type: respect on message body, username, channel names; fix timestamps, tab labels, and presence-dot size
- VoiceOver: combine username + role + message into a single accessibility element; announce presence separately ("Online", "Do Not Disturb")
- High contrast: Discord's three-gray surface system is AA-compliant at 16pt with `#F2F3F5` text; validate at smaller sizes and strengthen where needed
- Reduce motion: disable server-icon squircle morph and speaking-ring pulse when `UIAccessibility.isReduceMotionEnabled`
- Color blind: never rely on presence-dot color alone — back up with shape/label (green dot = "Online" label)
- Role color fallback: if role color fails contrast check against chat canvas, fall back to `#F2F3F5`
- Markdown rendering: use `AttributedString` parsing for `**bold**`, `*italic*`, code backticks; render spoiler (`||text||`) as a `Rectangle` overlay with tap-to-reveal
