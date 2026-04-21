# X (Twitter) (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates X's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark / Default)
    static let xCanvas           = Color.black                                   // #000000
    static let xSurface1          = Color(red: 0.086, green: 0.094, blue: 0.110) // #16181C
    static let xSurface2          = Color(red: 0.118, green: 0.125, blue: 0.141) // #1E2024
    static let xDivider           = Color(red: 0.184, green: 0.200, blue: 0.212) // #2F3336
    static let xDimCanvas         = Color(red: 0.082, green: 0.125, blue: 0.173) // #15202B
    static let xDimSurface1       = Color(red: 0.098, green: 0.153, blue: 0.204) // #192734
    static let xDimDivider        = Color(red: 0.220, green: 0.267, blue: 0.302) // #38444D

    // MARK: - Canvas & Surfaces (Light)
    static let xLightCanvas       = Color.white                                  // #FFFFFF
    static let xLightSurface1     = Color(red: 0.969, green: 0.976, blue: 0.976) // #F7F9F9
    static let xLightSurface2     = Color(red: 0.937, green: 0.953, blue: 0.957) // #EFF3F4

    // MARK: - Text
    static let xTextPrimaryDark   = Color(red: 0.906, green: 0.914, blue: 0.918) // #E7E9EA
    static let xTextPrimaryLight  = Color(red: 0.059, green: 0.078, blue: 0.098) // #0F1419
    static let xTextSecondaryDark = Color(red: 0.443, green: 0.463, blue: 0.482) // #71767B
    static let xTextSecondaryLight = Color(red: 0.325, green: 0.392, blue: 0.443) // #536471

    // MARK: - Brand / Action
    static let xBlue              = Color(red: 0.114, green: 0.608, blue: 0.941) // #1D9BF0
    static let xBluePressed       = Color(red: 0.102, green: 0.549, blue: 0.847) // #1A8CD8
    static let xRepostGreen       = Color(red: 0.000, green: 0.729, blue: 0.486) // #00BA7C
    static let xLikePink          = Color(red: 0.976, green: 0.094, blue: 0.502) // #F91880
    static let xVerifiedGold      = Color(red: 0.918, green: 0.702, blue: 0.031) // #EAB308
    static let xVerifiedGray      = Color(red: 0.510, green: 0.604, blue: 0.671) // #829AAB
    static let xErrorRed          = Color(red: 0.957, green: 0.129, blue: 0.180) // #F4212E
}
```

## 2. Typography

Chirp is proprietary to X. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to SF Pro — its grotesque-ish quality is the closest free substitute.

```swift
extension Font {
    // Chirp Display (use at 17pt+)
    static let xScreenTitle   = Font.custom("Chirp-Bold",     size: 20).weight(.bold)
    static let xSectionHeader = Font.custom("Chirp-Bold",     size: 17).weight(.bold)

    // Chirp UI (use below 17pt)
    static let xDisplayName   = Font.custom("Chirp-Bold",     size: 15).weight(.bold)
    static let xPostBody      = Font.custom("Chirp-Regular",  size: 15).weight(.regular)
    static let xQuotedBody    = Font.custom("Chirp-Regular",  size: 14).weight(.regular)
    static let xHandle        = Font.custom("Chirp-Regular",  size: 15).weight(.regular)
    static let xActionCount   = Font.custom("Chirp-Regular",  size: 13).weight(.regular)
    static let xTrendingTopic = Font.custom("Chirp-Bold",     size: 15).weight(.bold)
    static let xTrendingMeta  = Font.custom("Chirp-Regular",  size: 13).weight(.regular)
    static let xButton        = Font.custom("Chirp-Bold",     size: 15).weight(.bold)
    static let xDMBody        = Font.custom("Chirp-Regular",  size: 15).weight(.regular)
    static let xDMTimestamp   = Font.custom("Chirp-Regular",  size: 11).weight(.regular)
}

// System fallback if Chirp isn't bundled:
extension Font {
    static func xFallback(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Post Row

```swift
struct XPostRow: View {
    let displayName: String
    let handle: String
    let timestamp: String
    let isVerified: Bool
    let avatar: Image
    let body: String
    var replyCount: Int = 0
    var repostCount: Int = 0
    var likeCount: Int = 0
    var viewCount: Int = 0
    @State private var isLiked = false
    @State private var isReposted = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(alignment: .top, spacing: 12) {
                avatar
                    .resizable()
                    .frame(width: 32, height: 32)
                    .clipShape(Circle())

                VStack(alignment: .leading, spacing: 4) {
                    // Header row: name + verified + handle + time + overflow
                    HStack(spacing: 4) {
                        Text(displayName)
                            .font(.xDisplayName)
                            .foregroundStyle(.xTextPrimaryDark)
                            .lineLimit(1)
                        if isVerified {
                            Image(systemName: "checkmark.seal.fill")
                                .font(.system(size: 16))
                                .foregroundStyle(.xBlue)
                        }
                        Text("@\(handle)")
                            .font(.xHandle)
                            .foregroundStyle(.xTextSecondaryDark)
                            .lineLimit(1)
                        Text("·")
                            .foregroundStyle(.xTextSecondaryDark)
                        Text(timestamp)
                            .font(.xHandle)
                            .foregroundStyle(.xTextSecondaryDark)
                        Spacer()
                        Button { /* overflow */ } label: {
                            Image(systemName: "ellipsis")
                                .font(.system(size: 18))
                                .foregroundStyle(.xTextSecondaryDark)
                        }
                    }

                    // Body
                    Text(body)
                        .font(.xPostBody)
                        .foregroundStyle(.xTextPrimaryDark)
                        .lineSpacing(4)
                        .fixedSize(horizontal: false, vertical: true)

                    // Action row
                    HStack(spacing: 0) {
                        XActionIcon(systemName: "bubble.left",        count: replyCount,  color: .xTextSecondaryDark, active: false)
                        Spacer()
                        XActionIcon(systemName: isReposted ? "arrow.2.squarepath" : "arrow.2.squarepath",
                                    count: repostCount, color: isReposted ? .xRepostGreen : .xTextSecondaryDark, active: isReposted)
                            .onTapGesture { withAnimation(.spring(response: 0.35)) { isReposted.toggle() } }
                        Spacer()
                        XActionIcon(systemName: isLiked ? "heart.fill" : "heart",
                                    count: likeCount, color: isLiked ? .xLikePink : .xTextSecondaryDark, active: isLiked)
                            .onTapGesture { withAnimation(.spring(response: 0.35)) { isLiked.toggle() } }
                        Spacer()
                        XActionIcon(systemName: "chart.bar",          count: viewCount,   color: .xTextSecondaryDark, active: false)
                        Spacer()
                        Image(systemName: "square.and.arrow.up")
                            .font(.system(size: 18))
                            .foregroundStyle(.xTextSecondaryDark)
                    }
                    .padding(.top, 8)
                }
            }
            .padding(.horizontal, 16)
            .padding(.vertical, 12)

            Divider().background(Color.xDivider)
        }
        .background(Color.xCanvas)
    }
}

struct XActionIcon: View {
    let systemName: String
    let count: Int
    let color: Color
    let active: Bool

    var body: some View {
        HStack(spacing: 4) {
            Image(systemName: systemName)
                .font(.system(size: 18.75, weight: active ? .semibold : .regular))
                .foregroundStyle(color)
                .scaleEffect(active ? 1.0 : 1.0)
            if count > 0 {
                Text(formatted(count))
                    .font(.xActionCount)
                    .foregroundStyle(color)
            }
        }
        .frame(minWidth: 44, minHeight: 44, alignment: .leading)
    }

    private func formatted(_ n: Int) -> String {
        switch n {
        case 1_000_000...: return String(format: "%.1fM", Double(n)/1_000_000)
        case 1_000...:     return String(format: "%.1fK", Double(n)/1_000)
        default:           return "\(n)"
        }
    }
}
```

### Floating Post Button (FAB)

```swift
struct XPostFAB: View {
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: "pencil.and.outline")
                .font(.system(size: 24, weight: .semibold))
                .foregroundStyle(.black)
                .frame(width: 56, height: 56)
                .background(Circle().fill(Color.white))
                .shadow(color: .black.opacity(0.4), radius: 12, y: 4)
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: UUID())
        .buttonStyle(XPressableStyle(pressedScale: 0.95))
        .padding(.trailing, 16)
        .padding(.bottom, 16)
    }
}

struct XPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Follow Pill

```swift
struct XFollowPill: View {
    @Binding var isFollowing: Bool
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(isFollowing ? "Following" : "Follow")
                .font(.xButton)
                .foregroundStyle(isFollowing ? .xTextPrimaryDark : .black)
                .padding(.vertical, 8)
                .padding(.horizontal, 16)
                .frame(minWidth: 80)
                .background(
                    Capsule().fill(isFollowing ? Color.clear : Color.xTextPrimaryDark)
                )
                .overlay(
                    Capsule().strokeBorder(isFollowing ? Color.xTextSecondaryDark : .clear, lineWidth: 1)
                )
        }
        .buttonStyle(XPressableStyle())
    }
}
```

### Feed Filter Tabs ("For You" / "Following")

```swift
struct XFeedFilter: View {
    @Binding var selection: Int // 0 = For you, 1 = Following
    private let titles = ["For you", "Following"]
    @Namespace private var indicator

    var body: some View {
        HStack(spacing: 0) {
            ForEach(0..<2) { i in
                VStack(spacing: 12) {
                    Text(titles[i])
                        .font(.xButton)
                        .foregroundStyle(selection == i ? .xTextPrimaryDark : .xTextSecondaryDark)
                    if selection == i {
                        Capsule()
                            .fill(Color.xBlue)
                            .frame(width: 40, height: 4)
                            .matchedGeometryEffect(id: "indicator", in: indicator)
                    } else {
                        Color.clear.frame(height: 4)
                    }
                }
                .frame(maxWidth: .infinity)
                .contentShape(Rectangle())
                .onTapGesture {
                    withAnimation(.spring(response: 0.3, dampingFraction: 0.8)) {
                        selection = i
                    }
                }
            }
        }
        .frame(height: 48)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.xDivider).frame(height: 1)
        }
    }
}
```

## 4. X-Specific Feature: Timeline Row with Like Burst

```swift
struct LikeBurstModifier: ViewModifier {
    @Binding var isLiked: Bool
    @State private var particles: [CGPoint] = []

    func body(content: Content) -> some View {
        content
            .scaleEffect(isLiked ? 1.0 : 1.0)
            .animation(.spring(response: 0.35, dampingFraction: 0.55), value: isLiked)
            .overlay {
                ForEach(Array(particles.enumerated()), id: \.offset) { _, p in
                    Circle()
                        .fill(Color.xLikePink)
                        .frame(width: 4, height: 4)
                        .offset(x: p.x, y: p.y)
                        .opacity(0)
                        .animation(.easeOut(duration: 0.4), value: particles.count)
                }
            }
            .onChange(of: isLiked) { _, newValue in
                guard newValue else { return }
                // Hexagonal burst of 6 particles
                let radius: CGFloat = 18
                particles = (0..<6).map { i in
                    let angle = Double(i) * .pi / 3
                    return CGPoint(x: cos(angle) * radius, y: sin(angle) * radius)
                }
            }
    }
}

extension View {
    func xLikeBurst(isLiked: Binding<Bool>) -> some View {
        modifier(LikeBurstModifier(isLiked: isLiked))
    }
}
```

## 5. Tab Bar

X's tab bar is icon-only (no labels) — the signature pattern.

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor.black.withAlphaComponent(0.85)
        // Hide tab labels — X is icon-only
        let iconAppearance = UITabBarItemAppearance()
        iconAppearance.normal.titleTextAttributes = [.foregroundColor: UIColor.clear]
        iconAppearance.selected.titleTextAttributes = [.foregroundColor: UIColor.clear]
        appearance.stackedLayoutAppearance = iconAppearance
        appearance.inlineLayoutAppearance = iconAppearance
        appearance.compactInlineLayoutAppearance = iconAppearance
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeView()          .tabItem { Image(systemName: "house.fill") }
            SearchView()        .tabItem { Image(systemName: "magnifyingglass") }
            CommunitiesView()   .tabItem { Image(systemName: "person.3.fill") }
            NotificationsView().tabItem { Image(systemName: "bell.fill") }
            MessagesView()      .tabItem { Image(systemName: "envelope.fill") }
        }
        .tint(.xTextPrimaryDark)
    }
}
```

## 6. Motion

Use SwiftUI spring defaults. Haptics via the modern `.sensoryFeedback` API (iOS 17+).

```swift
// Like tap
.sensoryFeedback(.impact(flexibility: .soft), trigger: isLiked)

// Repost tap — icon spins 360deg
@State private var repostRotation: Double = 0
Image(systemName: "arrow.2.squarepath")
    .rotationEffect(.degrees(repostRotation))
    .onTapGesture {
        withAnimation(.easeOut(duration: 0.4)) { repostRotation += 360 }
    }

// Pull-to-refresh — X-logo expand
// Use UIRefreshControl via introspection, or a custom `ScrollView` overlay that tracks content offset.

// Compose modal open
.sheet(isPresented: $showCompose) {
    ComposeView()
        .presentationDetents([.large])
        .presentationDragIndicator(.hidden)
}
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Reply | `bubble.left` | 18.75pt |
| Repost | `arrow.2.squarepath` | 18.75pt |
| Like (outline) | `heart` | 18.75pt |
| Like (filled) | `heart.fill` | 18.75pt |
| Views | `chart.bar` | 18.75pt |
| Bookmark (outline) | `bookmark` | 18.75pt |
| Bookmark (filled) | `bookmark.fill` | 18.75pt |
| Share | `square.and.arrow.up` | 18pt |
| Verified | `checkmark.seal.fill` | 16pt |
| Overflow | `ellipsis` | 18-20pt |
| Compose (FAB) | `pencil.and.outline` | 24pt |
| Home tab | `house.fill` | 26pt |
| Search tab | `magnifyingglass` | 26pt |
| Communities tab | `person.3.fill` | 26pt |
| Notifications tab | `bell.fill` | 26pt |
| Messages tab | `envelope.fill` | 26pt |
| Grok | `sparkles` | 28pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on post body, quoted body, DM body — fix display name/timestamp/action counts
- VoiceOver: label action icons semantically ("Like post, 1.2 thousand likes"), group each post as a single element with a container shape
- Contrast: off-white `#E7E9EA` on black meets WCAG AA; secondary `#71767B` on black is borderline at small sizes — keep at 15pt+ for timestamps/handles
- Reduce Motion: respect `@Environment(\.accessibilityReduceMotion)` — skip the like-burst particles and the repost icon spin, still apply color changes and haptics
- Reduce Transparency: drop `.regularMaterial` blur on top nav in favor of solid canvas when enabled
- Large avatar on profile screen uses `.resizable()` + `.aspectRatio(contentMode: .fill)` to maintain circle crop at Dynamic Type XXL
