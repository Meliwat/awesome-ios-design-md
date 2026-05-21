# TikTok (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates TikTok's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Targets iOS 17+.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let tiktokCanvas      = Color(red: 0.004, green: 0.004, blue: 0.004) // #010101
    static let tiktokSurface     = Color(red: 0.086, green: 0.094, blue: 0.137) // #161823 — DMs, sheets
    static let tiktokInputField  = Color(red: 0.184, green: 0.184, blue: 0.184) // #2F2F2F — comment compose

    // MARK: - Brand
    static let tiktokRose        = Color(red: 0.996, green: 0.173, blue: 0.333) // #FE2C55
    static let tiktokCyan        = Color(red: 0.145, green: 0.957, blue: 0.933) // #25F4EE

    // MARK: - Text
    static let tiktokTextPrimary   = Color.white                                  // #FFFFFF
    static let tiktokTextSecondary = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5 — hashtag counts
    static let tiktokTextTertiary  = Color.white.opacity(0.6)                     // placeholder, meta
    static let tiktokTextDisabled  = Color.white.opacity(0.3)

    // MARK: - Overlays
    static let tiktokFollowerGray  = Color.white.opacity(0.15)                    // "Following" pill bg
    static let tiktokScrimLight    = Color.black.opacity(0.25)                    // icon shadow
    static let tiktokScrimMedium   = Color.black.opacity(0.4)                     // text shadow
    static let tiktokScrimHeavy    = Color.black.opacity(0.6)                     // sheet dim
    static let tiktokScrubberTrack = Color.white.opacity(0.3)
}
```

## 2. Typography

Proxima Nova is a licensed third-party typeface. Bundle the TTF/OTF files via `Info.plist` (`UIAppFonts`) or use a runtime registrar. Fall back to the system font — SF Pro's geometric humanist quality is the closest free substitute.

```swift
extension Font {
    // Headlines
    static let tiktokDisplayName = Font.custom("ProximaNova-Bold", size: 24).weight(.bold)
    static let tiktokSheetTitle  = Font.custom("ProximaNova-Semibold", size: 17).weight(.semibold)

    // Feed overlays
    static let tiktokUsername    = Font.custom("ProximaNova-Bold", size: 16).weight(.bold)
    static let tiktokCaption     = Font.custom("ProximaNova-Regular", size: 15).weight(.regular)
    static let tiktokHashtag     = Font.custom("ProximaNova-Bold", size: 15).weight(.bold)
    static let tiktokActionCount = Font.custom("ProximaNova-Bold", size: 13).weight(.bold)
    static let tiktokMusic       = Font.custom("ProximaNova-Medium", size: 13).weight(.medium)

    // Lists / sheets
    static let tiktokUsernameList = Font.custom("ProximaNova-Semibold", size: 15).weight(.semibold)
    static let tiktokBody         = Font.custom("ProximaNova-Regular", size: 14).weight(.regular)
    static let tiktokMeta         = Font.custom("ProximaNova-Regular", size: 12).weight(.regular)

    // Controls
    static let tiktokButtonPrimary   = Font.custom("ProximaNova-Bold", size: 16).weight(.bold)
    static let tiktokButtonSecondary = Font.custom("ProximaNova-Semibold", size: 14).weight(.semibold)
    static let tiktokFollow          = Font.custom("ProximaNova-Bold", size: 14).weight(.bold)
    static let tiktokTab             = Font.custom("ProximaNova-Semibold", size: 10).weight(.semibold)
    static let tiktokChip            = Font.custom("ProximaNova-Semibold", size: 13).weight(.semibold)
}

// Fallback if Proxima Nova isn't bundled:
extension Font {
    static func tiktok(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

### Text-on-Video Shadow Modifier

Every label overlaid on a video needs this treatment:

```swift
struct TikTokTextShadow: ViewModifier {
    func body(content: Content) -> some View {
        content.shadow(color: .tiktokScrimMedium, radius: 4, x: 0, y: 1)
    }
}

extension View {
    func tiktokTextOnVideo() -> some View { modifier(TikTokTextShadow()) }
}
```

## 3. Signature Components

### Chromatic Aberration Modifier

The visual thesis of the brand — used on the logo, Create button, and loading states.

```swift
struct ChromaticAberration: ViewModifier {
    var offset: CGFloat = 3

    func body(content: Content) -> some View {
        ZStack {
            content
                .foregroundStyle(Color.tiktokCyan)
                .offset(x: -offset)
            content
                .foregroundStyle(Color.tiktokRose)
                .offset(x: offset)
            content
                .foregroundStyle(.white)
        }
    }
}

extension View {
    func chromaticAberration(offset: CGFloat = 3) -> some View {
        modifier(ChromaticAberration(offset: offset))
    }
}

// Usage:
// Text("d").font(.system(size: 96, weight: .bold)).chromaticAberration(offset: 6)
```

### Follow Button

```swift
struct TikTokFollowButton: View {
    @Binding var isFollowing: Bool

    var body: some View {
        Button {
            isFollowing.toggle()
        } label: {
            Text(isFollowing ? "Following" : "Follow")
                .font(isFollowing ? .tiktokButtonSecondary : .tiktokFollow)
                .foregroundStyle(.white)
                .padding(.vertical, 6)
                .padding(.horizontal, 16)
                .frame(minHeight: 28)
                .background(
                    RoundedRectangle(cornerRadius: 4)
                        .fill(isFollowing ? Color.tiktokFollowerGray : Color.tiktokRose)
                )
        }
        .sensoryFeedback(.success, trigger: isFollowing) { old, new in !old && new }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isFollowing) { old, new in old && !new }
        .buttonStyle(TikTokPressableStyle(pressedScale: 0.95))
    }
}

struct TikTokPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Create Tab Button (Chromatic)

```swift
struct TikTokCreateButton: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            ZStack {
                RoundedRectangle(cornerRadius: 8)
                    .fill(Color.tiktokCyan)
                    .offset(x: -3)
                RoundedRectangle(cornerRadius: 8)
                    .fill(Color.tiktokRose)
                    .offset(x: 3)
                RoundedRectangle(cornerRadius: 8)
                    .fill(.white)
                Image(systemName: "plus")
                    .font(.system(size: 18, weight: .bold))
                    .foregroundStyle(Color.tiktokCanvas)
            }
            .frame(width: 44, height: 30)
            .compositingGroup()
            .clipShape(RoundedRectangle(cornerRadius: 8))
        }
        .sensoryFeedback(.impact(weight: .heavy), trigger: UUID())
        .buttonStyle(TikTokPressableStyle(pressedScale: 0.94))
    }
}
```

### Right-Side Action Rail

```swift
struct ActionRail: View {
    let avatarURL: URL?
    @Binding var isFollowed: Bool
    @Binding var likeCount: Int
    @Binding var isLiked: Bool
    let commentCount: Int
    let bookmarkCount: Int
    let shareCount: Int
    let musicArtwork: URL?

    var body: some View {
        VStack(spacing: 24) {
            avatarBadge
            ActionIcon(systemName: isLiked ? "heart.fill" : "heart",
                       count: likeCount,
                       tint: isLiked ? .tiktokRose : .white) {
                isLiked.toggle()
                likeCount += isLiked ? 1 : -1
            }
            ActionIcon(systemName: "ellipsis.bubble.fill", count: commentCount, tint: .white) { }
            ActionIcon(systemName: "bookmark.fill", count: bookmarkCount, tint: .white) { }
            ActionIcon(systemName: "arrowshape.turn.up.right.fill", count: shareCount, tint: .white) { }
            SpinningMusicDisc(artwork: musicArtwork)
        }
        .padding(.trailing, 12)
    }

    private var avatarBadge: some View {
        ZStack(alignment: .bottom) {
            AsyncImage(url: avatarURL) { $0.resizable() } placeholder: { Color.tiktokSurface }
                .frame(width: 48, height: 48)
                .clipShape(Circle())
                .overlay(Circle().stroke(.white, lineWidth: 2))
            if !isFollowed {
                Button { isFollowed = true } label: {
                    Image(systemName: "plus")
                        .font(.system(size: 10, weight: .bold))
                        .foregroundStyle(.white)
                        .frame(width: 18, height: 18)
                        .background(Circle().fill(Color.tiktokRose))
                }
                .offset(y: 9)
                .sensoryFeedback(.success, trigger: isFollowed)
            }
        }
    }
}

struct ActionIcon: View {
    let systemName: String
    let count: Int
    let tint: Color
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            VStack(spacing: 4) {
                Image(systemName: systemName)
                    .font(.system(size: 30, weight: .semibold))
                    .foregroundStyle(tint)
                    .shadow(color: .tiktokScrimLight, radius: 3, x: 0, y: 1)
                Text(TikTokCount.format(count))
                    .font(.tiktokActionCount)
                    .foregroundStyle(.white)
                    .tiktokTextOnVideo()
            }
        }
        .frame(minWidth: 48, minHeight: 48)
    }
}

enum TikTokCount {
    static func format(_ n: Int) -> String {
        switch n {
        case ..<1_000: return "\(n)"
        case ..<1_000_000: return String(format: "%.1fK", Double(n) / 1_000).replacingOccurrences(of: ".0", with: "")
        default: return String(format: "%.1fM", Double(n) / 1_000_000).replacingOccurrences(of: ".0", with: "")
        }
    }
}

struct SpinningMusicDisc: View {
    let artwork: URL?
    @State private var rotation: Double = 0

    var body: some View {
        ZStack {
            Circle().fill(Color.tiktokCanvas)
            AsyncImage(url: artwork) { $0.resizable() } placeholder: { Color.tiktokSurface }
                .frame(width: 28, height: 28)
                .clipShape(Circle())
            Circle().stroke(.white.opacity(0.4), lineWidth: 1)
        }
        .frame(width: 44, height: 44)
        .rotationEffect(.degrees(rotation))
        .onAppear {
            withAnimation(.linear(duration: 6).repeatForever(autoreverses: false)) {
                rotation = 360
            }
        }
    }
}
```

### Feed Caption Overlay (Bold Hashtags Inline)

```swift
struct CaptionOverlay: View {
    let username: String
    let caption: String       // "Check out #fyp #dance at sunset"
    let musicTitle: String

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("@\(username)")
                .font(.tiktokUsername)
                .foregroundStyle(.white)
                .tiktokTextOnVideo()

            captionText
                .font(.tiktokCaption)
                .foregroundStyle(.white)
                .lineLimit(2)
                .tiktokTextOnVideo()

            HStack(spacing: 6) {
                Image(systemName: "music.note")
                    .font(.system(size: 12, weight: .semibold))
                    .foregroundStyle(.white)
                Text(musicTitle)
                    .font(.tiktokMusic)
                    .foregroundStyle(.white)
                    .lineLimit(1)
            }
            .tiktokTextOnVideo()
        }
        .padding(.leading, 16)
        .padding(.trailing, 80) // leave room for the action rail
    }

    // Parse the caption and render hashtags bold inline
    private var captionText: Text {
        caption.split(separator: " ").reduce(Text("")) { acc, token in
            if token.hasPrefix("#") {
                return acc + Text(" \(token)").font(.tiktokHashtag)
            }
            return acc + Text(" \(token)")
        }
    }
}
```

### Double-Tap Like Burst

```swift
struct DoubleTapLike: ViewModifier {
    @State private var hearts: [HeartBurst] = []

    func body(content: Content) -> some View {
        content
            .overlay(
                GeometryReader { proxy in
                    ZStack {
                        ForEach(hearts) { heart in
                            Image(systemName: "heart.fill")
                                .font(.system(size: 120, weight: .bold))
                                .foregroundStyle(Color.tiktokRose)
                                .scaleEffect(heart.scale)
                                .opacity(heart.opacity)
                                .position(heart.position)
                        }
                    }
                }
            )
            .onTapGesture(count: 2) { loc in
                spawnHeart(at: loc)
            }
    }

    private func spawnHeart(at location: CGPoint) {
        let id = UUID()
        hearts.append(HeartBurst(id: id, position: location, scale: 0, opacity: 1))
        withAnimation(.spring(response: 0.4, dampingFraction: 0.55)) {
            if let i = hearts.firstIndex(where: { $0.id == id }) { hearts[i].scale = 1.4 }
        }
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.4) {
            withAnimation(.easeOut(duration: 0.2)) {
                if let i = hearts.firstIndex(where: { $0.id == id }) {
                    hearts[i].scale = 1.0
                    hearts[i].opacity = 0
                }
            }
        }
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.7) {
            hearts.removeAll { $0.id == id }
        }
        UIImpactFeedbackGenerator(style: .heavy).impactOccurred()
    }
}

struct HeartBurst: Identifiable {
    let id: UUID
    var position: CGPoint
    var scale: CGFloat
    var opacity: Double
}
```

### Video Progress Scrubber

```swift
struct VideoScrubber: View {
    let progress: Double     // 0.0 ... 1.0
    @State private var isScrubbing = false

    var body: some View {
        GeometryReader { proxy in
            ZStack(alignment: .leading) {
                Capsule().fill(Color.tiktokScrubberTrack)
                Capsule().fill(Color.tiktokRose).frame(width: proxy.size.width * progress)
            }
        }
        .frame(height: isScrubbing ? 4 : 2)
        .animation(.easeInOut(duration: 0.15), value: isScrubbing)
    }
}
```

### Chromatic Loading Spinner

```swift
struct TikTokLoadingSpinner: View {
    @State private var rotation: Double = 0
    var body: some View {
        ZStack {
            Circle().trim(from: 0, to: 0.75).stroke(Color.tiktokCyan, lineWidth: 3).offset(x: -3)
            Circle().trim(from: 0, to: 0.75).stroke(Color.tiktokRose, lineWidth: 3).offset(x: 3)
            Circle().trim(from: 0, to: 0.75).stroke(.white, lineWidth: 3)
        }
        .frame(width: 32, height: 32)
        .rotationEffect(.degrees(rotation))
        .onAppear {
            withAnimation(.linear(duration: 1).repeatForever(autoreverses: false)) {
                rotation = 360
            }
        }
    }
}
```

## 4. Tab Bar

Use a custom bottom bar (not `TabView`) so you can swap materials on the feed and embed the chromatic Create button.

```swift
struct RootTabBar: View {
    @Binding var selected: Tab
    let onCreateTapped: () -> Void
    var isOnFeed: Bool = true

    enum Tab: Hashable { case home, discover, inbox, profile }

    var body: some View {
        HStack(alignment: .center) {
            tab(.home,     icon: "house.fill",           label: "Home")
            tab(.discover, icon: "magnifyingglass",      label: "Discover")
            Spacer().frame(width: 56)   // space for Create button
            tab(.inbox,    icon: "tray.fill",            label: "Inbox")
            tab(.profile,  icon: "person.fill",          label: "Profile")
        }
        .frame(height: 48)
        .padding(.horizontal, 8)
        .background(
            Group {
                if isOnFeed {
                    Color.clear
                } else {
                    Color.tiktokCanvas.opacity(0.92)
                        .background(.regularMaterial)
                }
            }
        )
        .overlay(alignment: .top) {
            if !isOnFeed { Rectangle().fill(.white.opacity(0.08)).frame(height: 0.5) }
        }
        .overlay {
            TikTokCreateButton(action: onCreateTapped)
        }
        .safeAreaPadding(.bottom)
    }

    private func tab(_ tab: Tab, icon: String, label: String) -> some View {
        Button { selected = tab } label: {
            VStack(spacing: 3) {
                Image(systemName: icon).font(.system(size: 24, weight: .semibold))
                Text(label).font(.tiktokTab)
            }
            .foregroundStyle(selected == tab ? .white : .white.opacity(0.5))
            .frame(maxWidth: .infinity)
            .shadow(color: isOnFeed ? .tiktokScrimLight : .clear, radius: 3, x: 0, y: 1)
        }
    }
}
```

## 5. Feed Screen Composition

```swift
struct FeedScreen: View {
    @State private var isLiked = false
    @State private var likeCount = 24_500
    @State private var isFollowed = false

    var body: some View {
        ZStack(alignment: .bottom) {
            // Video placeholder (replace with AVPlayerLayer via UIViewRepresentable)
            LinearGradient(colors: [.pink, .purple, .blue],
                           startPoint: .topLeading, endPoint: .bottomTrailing)
                .ignoresSafeArea()
                .modifier(DoubleTapLike())

            // Right rail
            HStack {
                Spacer()
                ActionRail(
                    avatarURL: nil,
                    isFollowed: $isFollowed,
                    likeCount: $likeCount,
                    isLiked: $isLiked,
                    commentCount: 1_234, bookmarkCount: 890, shareCount: 456,
                    musicArtwork: nil
                )
            }
            .padding(.bottom, 120)

            // Left info overlay
            HStack {
                CaptionOverlay(
                    username: "novapalmer",
                    caption: "sunset loop at marin headlands #sunset #bayarea #goldenhour",
                    musicTitle: "original sound - novapalmer"
                )
                Spacer()
            }
            .padding(.bottom, 92)

            // Scrubber pinned to the bottom
            VideoScrubber(progress: 0.35)
                .padding(.horizontal, 0)
                .padding(.bottom, 60)
        }
        .statusBarHidden(false)
        .preferredColorScheme(.dark)
    }
}
```

## 6. Motion & Haptics

```swift
// Heart single-tap
.sensoryFeedback(.impact(weight: .heavy), trigger: isLiked) { old, new in !old && new }

// Follow success
.sensoryFeedback(.success, trigger: isFollowed) { old, new in !old && new }

// Share medium
.sensoryFeedback(.impact(weight: .medium), trigger: shareTapped)

// Feed vertical paging
// Use TabView(selection:) with .tabViewStyle(.page) + .rotationEffect(.degrees(90))
// or a custom DragGesture on a VStack with .offset animation (0.3s spring, damping 0.85)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Heart (idle) | `heart` | 30pt |
| Heart (active) | `heart.fill` | 30pt |
| Comment | `ellipsis.bubble.fill` | 30pt |
| Bookmark | `bookmark.fill` | 30pt |
| Share | `arrowshape.turn.up.right.fill` | 30pt |
| Music note | `music.note` | 12pt (marquee), 18pt (audio page) |
| Create plus | `plus` | 18pt |
| Home tab | `house.fill` | 24pt |
| Discover tab | `magnifyingglass` | 24pt |
| Inbox tab | `tray.fill` | 24pt |
| Profile tab | `person.fill` | 24pt |
| Back | `chevron.backward` | 24pt |
| More | `ellipsis` | 20pt |
| Send | `paperplane.fill` | 20pt |
| Live | `dot.radiowaves.left.and.right` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for modern `.sensoryFeedback`); fall back to `UIImpactFeedbackGenerator` for iOS 16
- **VoiceOver**: label the heart as "Like video by @username, 24.5K likes"; Follow button as "Follow @username"
- **Reduce Motion**: disable the double-tap burst spring and the spinning music disc rotation when `UIAccessibility.isReduceMotionEnabled` is true
- **Dynamic Type**: sheet content (comments, inbox) respects full scaling; feed overlays (caption, username, counts) use fixed sizes since the 9:16 layout is tight
- **Contrast**: white text on video is reinforced by the `rgba(0,0,0,0.4)` drop shadow; validate on bright-frame samples
- **Status bar**: always `.lightContent` on feed screens — use `.statusBar(hidden: false)` with `.preferredColorScheme(.dark)` so it stays white over video
- **Landscape**: lock the feed to portrait via `UIInterfaceOrientationMask.portrait` on the feed view controller
