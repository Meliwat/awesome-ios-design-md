# Instagram (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md). Paste-ready SwiftUI code: color tokens, typography, reusable components, and motion patterns.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // Canvas & surfaces
    static let igCanvasLight   = Color.white
    static let igCanvasDark    = Color.black  // true #000000 for OLED
    static let igElevatedDark  = Color(red: 0.071, green: 0.071, blue: 0.071)  // #121212
    static let igSurfaceInputL = Color(red: 0.937, green: 0.937, blue: 0.937)  // #EFEFEF
    static let igSurfaceInputD = Color(red: 0.149, green: 0.149, blue: 0.149)  // #262626
    static let igDividerLight  = Color(red: 0.859, green: 0.859, blue: 0.859)  // #DBDBDB
    static let igDividerDark   = Color(red: 0.149, green: 0.149, blue: 0.149)  // #262626

    // Text
    static let igTextSecondaryL = Color(red: 0.557, green: 0.557, blue: 0.557) // #8E8E8E
    static let igTextSecondaryD = Color(red: 0.659, green: 0.659, blue: 0.659) // #A8A8A8

    // Brand
    static let igActionBlue    = Color(red: 0.000, green: 0.584, blue: 0.965) // #0095F6
    static let igActionPressed = Color(red: 0.094, green: 0.467, blue: 0.949) // #1877F2
    static let igDestructive   = Color(red: 0.929, green: 0.286, blue: 0.337) // #ED4956
    static let igLinkLight     = Color(red: 0.000, green: 0.216, blue: 0.420) // #00376B
    static let igLinkDark      = Color(red: 0.878, green: 0.945, blue: 1.0)   // #E0F1FF

    // Gradient stops (10 total — from official brand)
    static let igGradBlue       = Color(red: 0.251, green: 0.365, blue: 0.902) // #405DE6
    static let igGradPurpleBlue = Color(red: 0.345, green: 0.318, blue: 0.859) // #5851DB
    static let igGradPurple     = Color(red: 0.514, green: 0.227, blue: 0.706) // #833AB4
    static let igGradPurpleRed  = Color(red: 0.757, green: 0.208, blue: 0.518) // #C13584
    static let igGradRose       = Color(red: 0.882, green: 0.188, blue: 0.424) // #E1306C
    static let igGradRed        = Color(red: 0.992, green: 0.114, blue: 0.114) // #FD1D1D
    static let igGradRedOrange  = Color(red: 0.961, green: 0.376, blue: 0.251) // #F56040
    static let igGradOrange     = Color(red: 0.969, green: 0.467, blue: 0.216) // #F77737
    static let igGradOrangeYellow = Color(red: 0.988, green: 0.686, blue: 0.271) // #FCAF45
    static let igGradYellow     = Color(red: 1.0,   green: 0.863, blue: 0.502) // #FFDC80

    // Online / Close Friends
    static let igOnlineGreen      = Color(red: 0.471, green: 0.871, blue: 0.271) // #78DE45
    static let igCloseFriends     = Color(red: 0.184, green: 0.722, blue: 0.145) // #2FB825
}

extension LinearGradient {
    /// The official Instagram story-ring gradient (10-stop sweep)
    static let instagramBrand = LinearGradient(
        gradient: Gradient(colors: [
            .igGradYellow, .igGradOrangeYellow, .igGradOrange, .igGradRedOrange,
            .igGradRed, .igGradRose, .igGradPurpleRed, .igGradPurple,
            .igGradPurpleBlue, .igGradBlue,
        ]),
        startPoint: .bottomLeading,
        endPoint: .topTrailing
    )

    /// Short 3-stop version often used in marketing
    static let instagramBrandShort = LinearGradient(
        colors: [.igGradPurple, .igGradRed, .igGradOrangeYellow],
        startPoint: .bottomLeading,
        endPoint: .topTrailing
    )
}
```

## 2. Typography

Instagram uses SF Pro (system font) for UI. The logotype "Instagram" uses the proprietary Instagram Sans script — fall back to `Billabong` or a similar condensed script when unavailable.

```swift
extension Font {
    // SF Pro UI
    static let igScreenTitle    = Font.system(size: 16, weight: .semibold, design: .default)
    static let igUsernameLarge  = Font.system(size: 20, weight: .bold,     design: .default)
    static let igUsername       = Font.system(size: 14, weight: .semibold, design: .default)
    static let igBio            = Font.system(size: 14, weight: .regular,  design: .default)
    static let igCaption        = Font.system(size: 14, weight: .regular,  design: .default)
    static let igComment        = Font.system(size: 14, weight: .regular,  design: .default)
    static let igSecondaryMeta  = Font.system(size: 12, weight: .regular,  design: .default)
    static let igButtonPrimary  = Font.system(size: 14, weight: .semibold, design: .default)
    static let igButtonSmall    = Font.system(size: 12, weight: .semibold, design: .default)
    static let igCounterLarge   = Font.system(size: 16, weight: .bold,     design: .default).monospacedDigit()
    static let igDMBubble       = Font.system(size: 16, weight: .regular,  design: .default)
    static let igBadge          = Font.system(size: 11, weight: .bold,     design: .default).monospacedDigit()
    static let igTimestamp      = Font.system(size: 11, weight: .regular,  design: .default)

    /// Logotype — register Billabong or similar script font
    static let igLogotype = Font.custom("Billabong", size: 28)
}
```

## 3. Signature Components

### Story Ring

```swift
struct StoryRing: View {
    let avatar: Image
    let isUnread: Bool
    var size: CGFloat = 66

    var body: some View {
        ZStack {
            if isUnread {
                Circle()
                    .strokeBorder(
                        AngularGradient(
                            gradient: Gradient(colors: [
                                .igGradYellow, .igGradOrangeYellow, .igGradOrange,
                                .igGradRed, .igGradRose, .igGradPurple, .igGradYellow,
                            ]),
                            center: .center
                        ),
                        lineWidth: 2.5
                    )
            } else {
                Circle()
                    .strokeBorder(Color.igDividerLight, lineWidth: 1)
            }
            avatar
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(width: size - 8, height: size - 8)
                .clipShape(Circle())
                .overlay(Circle().stroke(Color.igCanvasLight, lineWidth: 2))
        }
        .frame(width: size, height: size)
    }
}
```

### Primary Button (Follow / Log In)

```swift
struct IGPrimaryButton: View {
    let title: String
    var variant: Variant = .primary
    let action: () -> Void

    enum Variant { case primary, secondary, destructive }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.igButtonPrimary)
                .foregroundStyle(fg)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 7)
                .background(RoundedRectangle(cornerRadius: 8).fill(bg))
        }
        .buttonStyle(IGPressableStyle())
    }

    private var bg: Color {
        switch variant {
        case .primary:     return .igActionBlue
        case .secondary:   return .igSurfaceInputL
        case .destructive: return .clear
        }
    }
    private var fg: Color {
        switch variant {
        case .primary:     return .white
        case .secondary:   return .black
        case .destructive: return .igDestructive
        }
    }
}

struct IGPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.97 : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Feed Post

```swift
struct FeedPost: View {
    let username: String
    let avatar: Image
    let photo: Image
    let likes: Int
    let caption: String
    let timestamp: String
    @State private var isLiked = false
    @State private var showHeart = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            // Header
            HStack(spacing: 10) {
                StoryRing(avatar: avatar, isUnread: false, size: 32)
                Text(username).font(.igUsername)
                Spacer()
                Image(systemName: "ellipsis")
            }
            .frame(height: 48)
            .padding(.horizontal, 14)

            // Photo (double-tap to like)
            ZStack {
                photo
                    .resizable()
                    .aspectRatio(1, contentMode: .fill)
                    .onTapGesture(count: 2) {
                        if !isLiked { isLiked = true }
                        showHeart = true
                        Task { try? await Task.sleep(for: .milliseconds(600)); showHeart = false }
                    }

                if showHeart {
                    Image(systemName: "heart.fill")
                        .font(.system(size: 120))
                        .foregroundStyle(.white)
                        .shadow(color: .black.opacity(0.3), radius: 8)
                        .transition(.scale.combined(with: .opacity))
                }
            }
            .sensoryFeedback(.impact(weight: .soft), trigger: showHeart)

            // Action bar
            HStack(spacing: 16) {
                Button { isLiked.toggle() } label: {
                    Image(systemName: isLiked ? "heart.fill" : "heart")
                        .font(.system(size: 24))
                        .foregroundStyle(isLiked ? .igDestructive : .primary)
                }
                Image(systemName: "message")
                Image(systemName: "paperplane")
                Spacer()
                Image(systemName: "bookmark")
            }
            .font(.system(size: 24))
            .padding(.horizontal, 14)
            .frame(height: 48)

            // Likes + caption
            Text("\(likes) likes")
                .font(.system(size: 13, weight: .semibold))
                .padding(.horizontal, 14)

            (Text(username).fontWeight(.semibold) + Text(" ") + Text(caption))
                .font(.igCaption)
                .lineLimit(2)
                .padding(.horizontal, 14)
                .padding(.top, 4)

            Text(timestamp.uppercased())
                .font(.igTimestamp)
                .foregroundStyle(.igTextSecondaryL)
                .tracking(0.5)
                .padding(.horizontal, 14)
                .padding(.vertical, 4)
        }
    }
}
```

### Tab Bar (icon-only, 5 tabs)

```swift
struct IGRootTabView: View {
    @State private var selection = 0

    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterial)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView(selection: $selection) {
            HomeFeed()    .tabItem { Image(systemName: selection == 0 ? "house.fill" : "house") }.tag(0)
            ExploreView() .tabItem { Image(systemName: "magnifyingglass") }.tag(1)
            ReelsView()   .tabItem { Image(systemName: "play.rectangle") }.tag(2)
            CreateView()  .tabItem { Image(systemName: "plus.app") }.tag(3)
            ProfileView() .tabItem { Image(systemName: "person.circle") }.tag(4)
        }
        .tint(.primary)
    }
}
```

## 4. Motion & Haptics

```swift
// Double-tap-to-like heart animation
@State private var heartScale: CGFloat = 0
.scaleEffect(heartScale)
.animation(.spring(response: 0.4, dampingFraction: 0.6), value: heartScale)
.onChange(of: showHeart) { _, new in
    if new {
        heartScale = 1.4
        Task { try? await Task.sleep(for: .milliseconds(300)); heartScale = 0 }
    }
}

// Sensory feedback
.sensoryFeedback(.impact(weight: .soft), trigger: likeTap)   // like
.sensoryFeedback(.selection, trigger: tabSwitch)              // tab tap
.sensoryFeedback(.impact(weight: .medium), trigger: longPress) // tab long-press
```

## 5. SF Symbols Used

| Purpose | Symbol |
|---------|--------|
| Home | `house` / `house.fill` |
| Explore | `magnifyingglass` |
| Reels | `play.rectangle` |
| Create | `plus.app` / `plus.app.fill` |
| Heart | `heart` / `heart.fill` |
| Comment | `message` / `message.fill` |
| Share / DM | `paperplane` / `paperplane.fill` |
| Save | `bookmark` / `bookmark.fill` |
| More | `ellipsis` |
| Back | `chevron.backward` |

## 6. Accessibility

- Support Dynamic Type on username, caption, comments, DM
- Fix size on logotype (28pt), tab icons (24pt), timestamp (11pt)
- VoiceOver: `accessibilityLabel` for each action icon (e.g., "Like post by username")
- True-black canvas in dark mode — add `.preferredColorScheme(.dark)` at app root or expose a toggle
