# Twitch (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Twitch's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let twitchCanvas    = Color(red: 0.055, green: 0.055, blue: 0.063) // #0E0E10
    static let twitchDeepBlack = Color.black                                  // #000000
    static let twitchSurface1  = Color(red: 0.094, green: 0.094, blue: 0.106) // #18181B
    static let twitchSurface2  = Color(red: 0.122, green: 0.122, blue: 0.137) // #1F1F23
    static let twitchSurface3  = Color(red: 0.165, green: 0.165, blue: 0.176) // #2A2A2D
    static let twitchDivider   = Color(red: 0.165, green: 0.165, blue: 0.176) // #2A2A2D

    // MARK: - Text
    static let twitchTextPrimary   = Color(red: 0.937, green: 0.937, blue: 0.945) // #EFEFF1
    static let twitchTextSecondary = Color(red: 0.678, green: 0.678, blue: 0.722) // #ADADB8
    static let twitchTextTertiary  = Color(red: 0.435, green: 0.435, blue: 0.482) // #6F6F7B

    // MARK: - Brand & Liveness
    static let twitchPurple        = Color(red: 0.569, green: 0.275, blue: 1.000) // #9146FF
    static let twitchPurplePressed = Color(red: 0.467, green: 0.173, blue: 0.910) // #772CE8
    static let twitchLiveRed       = Color(red: 0.922, green: 0.016, blue: 0.000) // #EB0400
    static let twitchLiveRedPressed = Color(red: 0.761, green: 0.012, blue: 0.000) // #C20300
    static let twitchOnlineGreen   = Color(red: 0.000, green: 0.757, blue: 0.431) // #00C16E
}
```

## 2. Typography

Roobert is a licensed brand typeface. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to Inter (or `.system`) — a rounded grotesque is the closest free substitute.

```swift
extension Font {
    static let twitchTitleLarge   = Font.custom("Roobert-Bold",     size: 28).weight(.bold)
    static let twitchChannelName  = Font.custom("Roobert-Bold",     size: 22).weight(.bold)
    static let twitchSectionHeader = Font.custom("Roobert-Bold",    size: 20).weight(.bold)
    static let twitchStreamTitle  = Font.custom("Roobert-Semibold", size: 16).weight(.semibold)
    static let twitchChannelLabel = Font.custom("Roobert-Bold",     size: 15).weight(.bold)
    static let twitchBody         = Font.custom("Roobert-Regular",  size: 15).weight(.regular)
    static let twitchChatMessage  = Font.custom("Roobert-Regular",  size: 14).weight(.regular)
    static let twitchChatUsername = Font.custom("Roobert-Bold",     size: 14).weight(.bold)
    static let twitchMeta         = Font.custom("Roobert-Regular",  size: 13).weight(.regular)
    static let twitchCardSubtitle = Font.custom("Roobert-Regular",  size: 12).weight(.regular)
    static let twitchLabelUpper   = Font.custom("Roobert-Bold",     size: 11).weight(.bold)
    static let twitchButton       = Font.custom("Roobert-Bold",     size: 15).weight(.bold)
    static let twitchButtonSecondary = Font.custom("Roobert-Semibold", size: 14).weight(.semibold)
    static let twitchTab          = Font.custom("Roobert-Semibold", size: 10).weight(.semibold)
    static let twitchBadge        = Font.custom("Roobert-Bold",     size: 11).weight(.bold)
}

extension Font {
    static func twitch(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Primary Purple Follow / Subscribe Button

```swift
struct TwitchFollowButton: View {
    @Binding var isFollowing: Bool
    var subscribe: Bool = false   // true = Subscribe styling

    var body: some View {
        Button {
            isFollowing.toggle()
        } label: {
            HStack(spacing: 6) {
                Image(systemName: isFollowing ? "checkmark" : (subscribe ? "star.fill" : "heart.fill"))
                    .font(.system(size: 14, weight: .bold))
                Text(isFollowing ? (subscribe ? "Subscribed" : "Following")
                                  : (subscribe ? "Subscribe" : "Follow"))
                    .font(isFollowing ? .twitchButtonSecondary : .twitchButton)
                    .tracking(isFollowing ? 0 : 0.2)
            }
            .foregroundStyle(isFollowing ? Color.twitchTextPrimary : .white)
            .padding(.horizontal, 16)
            .frame(height: 40)
            .background(
                RoundedRectangle(cornerRadius: 6)
                    .fill(isFollowing ? Color.twitchSurface2 : Color.twitchPurple)
            )
            .shadow(color: isFollowing ? .clear : Color.twitchPurple.opacity(0.35), radius: 18, y: 6)
        }
        .sensoryFeedback(.impact(weight: .light), trigger: isFollowing)
        .buttonStyle(TwitchPressableStyle(pressedScale: 0.97))
    }
}

struct TwitchPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### LIVE Pill (pulsing) + Viewer-Count Pill

```swift
struct TwitchLivePill: View {
    @State private var pulse = false
    var body: some View {
        HStack(spacing: 5) {
            Circle().fill(.white).frame(width: 6, height: 6)
                .scaleEffect(pulse ? 0.6 : 1)
                .opacity(pulse ? 0.5 : 1)
                .animation(.easeInOut(duration: 1.6).repeatForever(autoreverses: true), value: pulse)
            Text("LIVE").font(.twitchBadge).tracking(0.4)
        }
        .foregroundStyle(.white)
        .padding(.horizontal, 8)
        .frame(height: 22)
        .background(RoundedRectangle(cornerRadius: 4).fill(Color.twitchLiveRed))
        .onAppear { pulse = true }
    }
}

struct TwitchViewerPill: View {
    let count: String   // e.g. "12.4K"
    var body: some View {
        HStack(spacing: 4) {
            Image(systemName: "person.fill").font(.system(size: 9, weight: .bold))
            Text(count).font(.twitchBadge)
        }
        .foregroundStyle(.white)
        .padding(.horizontal, 8)
        .frame(height: 22)
        .background(RoundedRectangle(cornerRadius: 4).fill(.black.opacity(0.6)))
    }
}
```

### Live Thumbnail Card

```swift
struct TwitchLiveCard: View {
    let title: String
    let channel: String
    let game: String
    let viewers: String
    let thumbnail: Image
    let avatar: Image
    let width: CGFloat

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            ZStack(alignment: .topLeading) {
                thumbnail
                    .resizable()
                    .aspectRatio(16/9, contentMode: .fill)
                    .frame(width: width, height: width * 9/16)
                    .clipShape(RoundedRectangle(cornerRadius: 6))
                TwitchLivePill().padding(8)
                VStack { Spacer()
                    HStack { TwitchViewerPill(count: viewers); Spacer() }
                }.padding(8)
            }
            HStack(alignment: .top, spacing: 8) {
                avatar.resizable().frame(width: 32, height: 32).clipShape(Circle())
                VStack(alignment: .leading, spacing: 2) {
                    Text(title).font(.twitchStreamTitle).foregroundStyle(.twitchTextPrimary).lineLimit(2)
                    Text(channel).font(.twitchMeta).foregroundStyle(.twitchTextSecondary)
                    Text(game).font(.twitchCardSubtitle).foregroundStyle(.twitchTextSecondary)
                }
            }
        }
        .frame(width: width)
    }
}
```

### Chat Message Row + Channel Live Ring

```swift
struct TwitchChatRow: View {
    let username: String
    let userColor: Color
    let message: String
    var mentioned: Bool = false

    var body: some View {
        HStack(alignment: .top, spacing: 0) {
            (Text(username + " ").font(.twitchChatUsername).foregroundColor(userColor)
             + Text(message).font(.twitchChatMessage).foregroundColor(.twitchTextPrimary))
            .fixedSize(horizontal: false, vertical: true)
            Spacer(minLength: 0)
        }
        .padding(.vertical, 4)
        .padding(.horizontal, 12)
        .background(
            mentioned
            ? Color.twitchPurple.opacity(0.20)
                .overlay(Rectangle().fill(Color.twitchPurple).frame(width: 2), alignment: .leading)
            : nil
        )
    }
}

struct TwitchAvatarRing: View {
    let avatar: Image
    let isLive: Bool
    var size: CGFloat = 44
    var body: some View {
        avatar
            .resizable()
            .frame(width: size, height: size)
            .clipShape(Circle())
            .overlay(Circle().strokeBorder(isLive ? Color.twitchLiveRed : Color.twitchPurple, lineWidth: 2))
            .padding(2)
    }
}
```

## 4. Theater-Mode Chat Overlay

```swift
struct TwitchTheaterChatOverlay: View {
    let messages: [ChatLine]
    @Binding var chatHidden: Bool

    var body: some View {
        if !chatHidden {
            VStack(spacing: 0) {
                ScrollView { LazyVStack(spacing: 0) {
                    ForEach(messages) { m in
                        TwitchChatRow(username: m.user, userColor: m.color,
                                      message: m.text, mentioned: m.mentionsMe)
                    }
                }}
                TextField("Send a message", text: .constant(""))
                    .padding(12)
                    .background(Color.twitchSurface2)
            }
            .frame(width: 320)
            .background(.ultraThinMaterial)
            .background(Color.twitchCanvas.opacity(0.72))
            .transition(.move(edge: .trailing).combined(with: .opacity))
        }
    }
}
```

## 5. Tab Bar

Use `TabView` with a custom `UITabBarAppearance` for the 94%-opaque canvas + `.regularMaterial` blur. **Active tint is Twitch Purple** — purple is the indicator.

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor(Color.twitchCanvas).withAlphaComponent(0.94)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            FollowingView().tabItem     { Label("Following",     systemImage: "heart.fill") }
            BrowseView().tabItem        { Label("Browse",        systemImage: "play.square.stack.fill") }
            SearchView().tabItem        { Label("Search",        systemImage: "magnifyingglass") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell.fill") }
            ProfileView().tabItem       { Label("Profile",       systemImage: "person.crop.circle.fill") }
        }
        .tint(.twitchPurple) // active = purple, purple is the indicator
    }
}
```

## 6. Motion

Use `SwiftUI` spring defaults. Haptics via the modern `.sensoryFeedback` API (iOS 17+).

```swift
// LIVE pill pulse — see §3 (1.6s scale+opacity, .repeatForever(autoreverses: true))

// Card press scale-up
.scaleEffect(isPressed ? 1.03 : 1.0)
.animation(.easeOut(duration: 0.18), value: isPressed)

// Follow tap
.sensoryFeedback(.impact(weight: .light), trigger: isFollowing)

// Chat autoscroll: insert with .transition(.move(edge: .bottom).combined(with: .opacity))
// over .animation(.easeOut(duration: 0.12))

// Theater toggle: withAnimation(.easeInOut(duration: 0.25)) { theater.toggle() }
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Follow | `heart` / `heart.fill` | 14pt |
| Subscribe | `star` / `star.fill` | 14pt |
| Followed check | `checkmark` | 14pt |
| Viewers | `person.fill` | 9-12pt |
| Play / Pause | `play.fill` / `pause.fill` | 24pt |
| Emote picker | `face.smiling` | 20pt |
| Send | `paperplane.fill` | 20pt |
| Share | `square.and.arrow.up` | 22pt |
| More | `ellipsis` | 20pt |
| Search | `magnifyingglass` | 18pt |
| Following (tab) | `heart.fill` | 24pt |
| Browse (tab) | `play.square.stack.fill` | 24pt |
| Notifications (tab) | `bell.fill` | 24pt |
| Profile (tab) | `person.crop.circle.fill` | 24pt |
| Quality / settings | `gearshape.fill` | 22pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on stream titles, channel names, chat messages — pin tab labels, LIVE pill, and viewer pills; cap chat growth to keep throughput readable
- VoiceOver: announce live cards as "Live: Ranked grind to Masters by Channel, Just Chatting, 12.4 thousand viewers"; expose new chat messages via an `accessibilityLiveRegion` so screen-reader users hear chat activity (allow muting it)
- Contrast: `#ADADB8` secondary on `#0E0E10` passes WCAG AA at 13pt+; `#EFEFF1` primary is intentionally off-white and still exceeds AAA on the canvas
- Color split must survive color-blindness: never rely on purple-vs-red alone for state — the LIVE pill always carries the word "LIVE" and the dot
- Reduce Motion: stop the LIVE pulse (static red dot), disable card scale-up, and cross-fade the theater toggle instead of expanding
