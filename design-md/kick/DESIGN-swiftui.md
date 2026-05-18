# Kick (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Kick's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the live chat panel, the watch page, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-only; NOT pure black)
    static let kickCanvas   = Color(red: 0.055, green: 0.055, blue: 0.063) // #0E0E10
    static let kickSurface1 = Color(red: 0.086, green: 0.086, blue: 0.094) // #161618
    static let kickSurface2 = Color(red: 0.122, green: 0.122, blue: 0.137) // #1F1F23
    static let kickSurface3 = Color(red: 0.165, green: 0.165, blue: 0.180) // #2A2A2E
    static let kickDivider  = Color(red: 0.176, green: 0.176, blue: 0.192) // #2D2D31

    // MARK: - Brand
    static let kickGreen        = Color(red: 0.325, green: 0.988, blue: 0.094) // #53FC18
    static let kickGreenPressed = Color(red: 0.271, green: 0.851, blue: 0.071) // #45D912
    static let kickGreenDeep    = Color(red: 0.000, green: 0.906, blue: 0.004) // #00E701

    // MARK: - Text
    static let kickTextPrimary   = Color.white                                  // #FFFFFF
    static let kickTextSecondary = Color(red: 0.651, green: 0.651, blue: 0.678) // #A6A6AD
    static let kickTextTertiary  = Color(red: 0.431, green: 0.431, blue: 0.463) // #6E6E76
    static let kickChatBody      = Color(red: 0.894, green: 0.894, blue: 0.914) // #E4E4E9
    static let kickOnGreen       = Color(red: 0.055, green: 0.055, blue: 0.063) // #0E0E10

    // MARK: - Chat roles
    static let kickMod  = Color(red: 0.243, green: 0.651, blue: 1.000) // #3EA6FF
    static let kickSub  = Color(red: 1.000, green: 0.780, blue: 0.000) // #FFC700
    static let kickVIP  = Color(red: 1.000, green: 0.310, blue: 0.847) // #FF4FD8

    // MARK: - Semantic
    static let kickLive  = Color(red: 1.000, green: 0.122, blue: 0.267) // #FF1F44
    static let kickError = Color(red: 1.000, green: 0.290, blue: 0.290) // #FF4A4A
}

enum ChatRole { case mod, sub(Int), vip, og, verified, regular(Color) }

extension ChatRole {
    var userColor: Color {
        switch self {
        case .mod: return .kickMod
        case .sub: return .kickSub
        case .vip: return .kickVIP
        case .og:  return .kickGreen
        case .verified: return .white
        case .regular(let c): return c
        }
    }
    var badge: (text: String, fill: Color, fg: Color)? {
        switch self {
        case .mod: return ("MOD", .kickMod, Color(red: 0.024, green: 0.071, blue: 0.122))
        case .sub(let m): return ("\(m)", .kickSub, Color(red: 0.102, green: 0.082, blue: 0.0))
        case .vip: return ("VIP", .kickVIP, Color(red: 0.165, green: 0.0, blue: 0.133))
        case .og:  return ("OG", .kickGreen, .kickOnGreen)
        case .verified: return ("✓", .kickSurface3, .white)
        case .regular: return nil
        }
    }
}
```

## 2. Typography

Kick's brand face is a tight modern grotesque; **Inter** at heavy weights is the closest free analog (SIL OFL). Bundle Inter via `Info.plist` / `UIAppFonts`. Use tabular figures for viewer counts.

```swift
extension Font {
    static let kickDisplay     = Font.custom("Inter-Black",      size: 32) // 900
    static let kickScreenTitle = Font.custom("Inter-ExtraBold",  size: 24) // 800
    static let kickSection     = Font.custom("Inter-ExtraBold",  size: 20) // 800
    static let kickStreamer    = Font.custom("Inter-Bold",       size: 16) // 700
    static let kickBody        = Font.custom("Inter-Regular",    size: 15) // 400
    static let kickCardTitle   = Font.custom("Inter-Bold",       size: 13) // 700
    static let kickChatUser    = Font.custom("Inter-Bold",       size: 13) // 700
    static let kickChatMsg     = Font.custom("Inter-Regular",    size: 13) // 400
    static let kickMeta        = Font.custom("Inter-Regular",    size: 13) // 400
    static let kickButton      = Font.custom("Inter-ExtraBold",  size: 15) // 800
    static let kickViewers     = Font.custom("Inter-Bold",       size: 11) // 700 tabular
    static let kickBadge       = Font.custom("Inter-ExtraBold",  size: 10) // 800 caps
    static let kickTab         = Font.custom("Inter-SemiBold",   size: 10) // 600
}
```

## 3. Signature Components

### Watch Page (video + streamer bar + chat)

```swift
struct KickWatchPage: View {
    let streamer: String
    let category: String
    let viewers: String
    let messages: [ChatMessage]

    var body: some View {
        VStack(spacing: 0) {
            VideoPlayerView()                       // your AVPlayer 16:9
                .aspectRatio(16/9, contentMode: .fit)
                .overlay(alignment: .topLeading) { LivePill().padding(12) }
                .overlay(alignment: .topTrailing) { ViewerChip(viewers).padding(12) }

            StreamerBar(name: streamer, category: category, viewers: viewers)

            ChatPanel(messages: messages)           // fills remaining height
                .frame(maxHeight: .infinity)
        }
        .background(Color.kickCanvas)
    }
}

struct LivePill: View {
    var body: some View {
        HStack(spacing: 6) {
            Circle().fill(.white).frame(width: 6, height: 6)
            Text("LIVE").font(.kickBadge).foregroundStyle(.white)
        }
        .padding(.horizontal, 9).padding(.vertical, 4)
        .background(RoundedRectangle(cornerRadius: 5).fill(Color.kickLive))
    }
}

struct ViewerChip: View {
    let count: String
    init(_ c: String) { count = c }
    var body: some View {
        HStack(spacing: 5) {
            Image(systemName: "eye.fill").font(.system(size: 11)).foregroundStyle(.white)
            Text(count).font(.kickViewers).monospacedDigit().foregroundStyle(.white)
        }
        .padding(.horizontal, 9).padding(.vertical, 4)
        .background(RoundedRectangle(cornerRadius: 5).fill(Color.black.opacity(0.55)))
    }
}

struct StreamerBar: View {
    let name: String; let category: String; let viewers: String
    @State private var following = false

    var body: some View {
        HStack(spacing: 10) {
            Circle()
                .fill(LinearGradient(colors: [.kickGreen, Color(red: 0, green: 0.65, blue: 0.32)],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 40, height: 40)
                .overlay(Circle().strokeBorder(Color.kickGreen, lineWidth: 2))
            VStack(alignment: .leading, spacing: 1) {
                HStack(spacing: 5) {
                    Text(name).font(.kickStreamer).foregroundStyle(.white)
                    Image(systemName: "checkmark.seal.fill").font(.system(size: 13)).foregroundStyle(Color.kickGreen)
                }
                Text("\(category) · \(viewers) watching")
                    .font(.system(size: 12)).foregroundStyle(Color.kickTextSecondary)
                    .lineLimit(1)
            }
            Spacer()
            Button {
                withAnimation(.spring(response: 0.25, dampingFraction: 0.55)) { following.toggle() }
            } label: {
                Text(following ? "Following" : "Follow")
                    .font(.system(size: 12, weight: .heavy))
                    .foregroundStyle(following ? Color.kickTextPrimary : Color.kickOnGreen)
                    .padding(.horizontal, 16).padding(.vertical, 8)
                    .background(
                        RoundedRectangle(cornerRadius: 6)
                            .fill(following ? Color.kickSurface2 : Color.kickGreen)
                    )
                    .overlay(
                        RoundedRectangle(cornerRadius: 6)
                            .strokeBorder(Color.kickSurface3, lineWidth: following ? 1 : 0)
                    )
            }
            .buttonStyle(.plain)
            .sensoryFeedback(.impact(flexibility: .soft), trigger: following)
        }
        .padding(.horizontal, 14).padding(.vertical, 12)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.kickDivider).frame(height: 0.5) }
    }
}
```

### Live Chat Panel + Message

```swift
struct ChatMessage: Identifiable {
    let id = UUID()
    let role: ChatRole
    let username: String
    let segments: [Segment]      // text + emote runs
    let mentionsMe: Bool

    enum Segment { case text(String); case emote(name: String) }
}

struct ChatPanel: View {
    let messages: [ChatMessage]
    @State private var draft = ""

    var body: some View {
        VStack(spacing: 0) {
            HStack {
                Text("STREAM CHAT").font(.system(size: 12, weight: .heavy))
                    .foregroundStyle(Color.kickTextSecondary)
                Spacer()
                Image(systemName: "gearshape").font(.system(size: 16)).foregroundStyle(Color.kickTextSecondary)
            }
            .padding(.horizontal, 14).padding(.top, 10).padding(.bottom, 8)

            ScrollViewReader { proxy in
                ScrollView {
                    LazyVStack(alignment: .leading, spacing: 9) {
                        ForEach(messages) { ChatRow(message: $0) }
                    }
                    .padding(.horizontal, 14).padding(.vertical, 4)
                }
            }

            HStack(spacing: 8) {
                HStack {
                    TextField("", text: $draft, prompt: Text("Send a message").foregroundColor(.kickTextTertiary))
                        .font(.system(size: 13)).foregroundStyle(.white)
                }
                .padding(.horizontal, 12).frame(height: 38)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.kickSurface2))

                Button {} label: {
                    Image(systemName: "paperplane.fill").font(.system(size: 16))
                        .foregroundStyle(draft.isEmpty ? Color.kickTextTertiary : Color.kickOnGreen)
                        .frame(width: 38, height: 38)
                        .background(RoundedRectangle(cornerRadius: 8)
                            .fill(draft.isEmpty ? Color.kickSurface2 : Color.kickGreen))
                }
                .buttonStyle(.plain)
            }
            .padding(.horizontal, 12).padding(.top, 10).padding(.bottom, 12)
            .overlay(alignment: .top) { Rectangle().fill(Color.kickDivider).frame(height: 0.5) }
        }
        .background(Color.kickCanvas)
    }
}

struct ChatRow: View {
    let message: ChatMessage

    var body: some View {
        (
            badgeText
            + Text("\(message.username)").font(.kickChatUser).foregroundColor(message.role.userColor)
            + Text(": ").font(.kickChatUser).foregroundColor(message.role.userColor)
            + bodyText
        )
        .fixedSize(horizontal: false, vertical: true)
        .padding(message.mentionsMe ? 6 : 0)
        .background(
            message.mentionsMe
            ? RoundedRectangle(cornerRadius: 4).fill(Color.kickGreen.opacity(0.12))
            : nil
        )
        .overlay(alignment: .leading) {
            if message.mentionsMe { Rectangle().fill(Color.kickGreen).frame(width: 2) }
        }
    }

    private var badgeText: Text {
        guard let b = message.role.badge else { return Text("") }
        // Approximation: inline pill-like prefix (production: use a Label/HStack run)
        return Text(" \(b.text) ")
            .font(.system(size: 9, weight: .black))
            .foregroundColor(b.fg)
            + Text(" ")
    }

    private var bodyText: Text {
        message.segments.reduce(Text("")) { acc, seg in
            switch seg {
            case .text(let t): return acc + Text(t).font(.kickChatMsg).foregroundColor(.kickChatBody)
            case .emote: return acc + Text("\(Image(systemName: "face.smiling.fill"))").foregroundColor(.kickGreen)
            }
        }
    }
}
```

> For pixel-accurate badge chips and emote sprites, render the row as an `HStack`/`WrappingHStack` of views (chip = rounded `Text`, emote = `AsyncImage` 18×18) rather than a single concatenated `Text`. The `Text` form above is the lightweight path.

### Live Channel Card

```swift
struct LiveCard: View {
    let title: String; let streamer: String; let category: String
    let viewers: String; let thumbURL: String

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            AsyncImage(url: URL(string: thumbURL)) { $0.resizable().aspectRatio(16/9, contentMode: .fill) }
                placeholder: { Color.kickSurface2.aspectRatio(16/9, contentMode: .fill) }
                .clipShape(RoundedRectangle(cornerRadius: 8))
                .overlay(alignment: .topLeading) {
                    Text("LIVE").font(.system(size: 8, weight: .heavy)).foregroundStyle(.white)
                        .padding(.horizontal, 6).padding(.vertical, 2)
                        .background(RoundedRectangle(cornerRadius: 3).fill(Color.kickLive))
                        .padding(6)
                }
                .overlay(alignment: .bottomLeading) {
                    Text(viewers).font(.system(size: 9, weight: .bold)).foregroundStyle(.white)
                        .padding(.horizontal, 6).padding(.vertical, 2)
                        .background(RoundedRectangle(cornerRadius: 3).fill(Color.black.opacity(0.6)))
                        .padding(6)
                }
            HStack(spacing: 8) {
                Circle().fill(Color.kickGreen).frame(width: 30, height: 30)
                    .overlay(Circle().strokeBorder(Color.kickGreen, lineWidth: 2))
                VStack(alignment: .leading, spacing: 2) {
                    Text(title).font(.kickCardTitle).foregroundStyle(.white).lineLimit(1)
                    Text("\(streamer) · \(category)").font(.system(size: 11)).foregroundStyle(Color.kickTextSecondary)
                }
            }
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct KickTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem      { Label("Home",      systemImage: "house.fill") }
            BrowseView().tabItem    { Label("Browse",    systemImage: "magnifyingglass") }
            FollowingView().tabItem { Label("Following", systemImage: "circle.circle.fill") }
            ClipsView().tabItem     { Label("Clips",     systemImage: "film.fill") }
            ProfileView().tabItem   { Label("Profile",   systemImage: "person.crop.circle") }
        }
        .tint(.kickGreen) // active = green, no pill
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.kickCanvas.opacity(0.96))
            a.shadowColor = UIColor(Color.kickDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// New chat message — append with fade + slight slide
.transition(.opacity.combined(with: .move(edge: .bottom)))
withAnimation(.easeOut(duration: 0.12)) { messages.append(newMsg) }
// auto-scroll to bottom unless user scrolled up:
withAnimation(.spring(response: 0.2, dampingFraction: 0.85)) { proxy.scrollTo(messages.last?.id, anchor: .bottom) }

// Follow → Following
withAnimation(.spring(response: 0.25, dampingFraction: 0.55)) { following.toggle() }
.sensoryFeedback(.impact(flexibility: .soft), trigger: following)

// Live pill subtle pulse
.opacity(pulse ? 1.0 : 0.6)
.animation(.easeInOut(duration: 1.5).repeatForever(autoreverses: true), value: pulse)

// Player controls fade
withAnimation(.easeInOut(duration: 0.2)) { controlsVisible.toggle() }   // auto-hide after 3s

// Stream join: cross-fade into watch page
withAnimation(.easeInOut(duration: 0.3)) { showWatch = true }

// Card press
.scaleEffect(pressed ? 0.98 : 1.0)
.animation(.easeOut(duration: 0.12), value: pressed)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Browse (tab) | `magnifyingglass` | 22pt |
| Following (tab) | `circle.circle` / `.fill` | 22pt |
| Clips (tab) | `film` / `film.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Verified check | `checkmark.seal.fill` | 13pt |
| Live (eye) | `eye.fill` | 11pt |
| Play / pause | `play.fill` / `pause.fill` | 18pt |
| Volume | `speaker.wave.2.fill` / `speaker.slash.fill` | 18pt |
| Fullscreen | `arrow.up.left.and.arrow.down.right` | 18pt |
| Chat send | `paperplane.fill` | 16pt |
| Chat settings | `gearshape` | 16pt |
| Emote picker | `face.smiling` | 18pt |
| Share / clip | `square.and.arrow.up` / `scissors` | 18pt |
| Back | `chevron.left` | 24pt |
| Subscribe | `star.fill` / `crown.fill` | 14pt |

## 7. Dark Mode

Kick is **dark-only** — there is no light scheme, and the canvas must stay `#0E0E10` (NOT pure black).

```swift
struct KickTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.kickCanvas)
            .foregroundStyle(Color.kickTextPrimary)
            .preferredColorScheme(.dark)   // force dark
    }
}

extension View { func kickTheme() -> some View { modifier(KickTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Bundle Inter TTFs (Regular/Medium/SemiBold/Bold/ExtraBold/Black) via `Info.plist` — SIL OFL, free to ship
- The canvas is `#0E0E10`, never pure black — `#53FC18` on `#000000` causes visual vibration/strobing for some users; the slight lift is an accessibility choice as much as an aesthetic one
- Dynamic Type: scale screen titles, body, descriptions, streamer name; chat text scales to L then truncates the username; keep LIVE/role badges, viewer counts, tab labels FIXED
- VoiceOver: chat rows labeled "{role} {username} says: {message text}" (e.g. "Moderator StreamGuard says: keep it civil") — the role MUST be spoken because the color/badge is the only visual role cue, and color-only is not accessible; emotes announce their name ("emote: KEKW")
- Role color is never the sole signal — it is always paired with a badge chip and announced in the accessibility label
- @-mention rows: announce "Mentions you:" before the message
- Color contrast: white/`#E4E4E9` on `#0E0E10`/`#161618` passes WCAG AA; `#A6A6AD` on canvas passes AA at 13pt+; `#0E0E10` on `#53FC18` is very high contrast for the green CTA
- Reduce Motion: disable the live-pill pulse and chat slide-in (use a plain crossfade); keep auto-scroll but make it instant
- Reduce Transparency: replace the tab bar's blur with solid `#0E0E10`
- Chat performance: use `LazyVStack` in a `ScrollViewReader`, cap retained messages (e.g. last 200), and recycle — a fast live chat can append many messages per second
