# Bluesky (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Bluesky's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Light
    static let bskyCanvas    = Color.white                                  // #FFFFFF
    static let bskySurface1  = Color(red: 0.945, green: 0.953, blue: 0.961) // #F1F3F5
    static let bskySurface2  = Color(red: 0.886, green: 0.910, blue: 0.941) // #E2E8F0
    static let bskyDivider   = Color(red: 0.886, green: 0.910, blue: 0.941) // #E2E8F0
    static let bskyText      = Color(red: 0.043, green: 0.059, blue: 0.078) // #0B0F14
    static let bskyTextSec   = Color(red: 0.412, green: 0.467, blue: 0.529) // #697787

    // MARK: - Dim
    static let bskyDimCanvas   = Color(red: 0.118, green: 0.161, blue: 0.212) // #1E2936
    static let bskyDimSurface1 = Color(red: 0.153, green: 0.192, blue: 0.243) // #27313E
    static let bskyDimDivider  = Color(red: 0.180, green: 0.251, blue: 0.322) // #2E4052

    // MARK: - Dark
    static let bskyDarkCanvas   = Color(red: 0.043, green: 0.059, blue: 0.078) // #0B0F14
    static let bskyDarkSurface1 = Color(red: 0.086, green: 0.118, blue: 0.153) // #161E27
    static let bskyDarkDivider  = Color(red: 0.118, green: 0.161, blue: 0.212) // #1E2936
    static let bskyDarkTextSec  = Color(red: 0.545, green: 0.596, blue: 0.647) // #8B98A5

    // MARK: - Brand
    static let bskyBlue        = Color(red: 0.067, green: 0.522, blue: 0.996) // #1185FE
    static let bskyBluePressed = Color(red: 0.059, green: 0.435, blue: 0.839) // #0F6FD6
    static let bskyLike        = Color(red: 0.925, green: 0.282, blue: 0.600) // #EC4899
    static let bskyRepost      = Color(red: 0.176, green: 0.745, blue: 0.522) // #2DBE85

    // MARK: - Semantic
    static let bskyError   = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
    static let bskyWarning = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623
}
```

## 2. Typography

Bluesky uses the system font (SF Pro) at reading sizes; the web client renders Inter. Use `.system` directly — bundle Inter via `UIAppFonts` only for pixel parity with web.

```swift
extension Font {
    static let bskyTitleLarge  = Font.system(size: 28, weight: .bold)
    static let bskySection     = Font.system(size: 21, weight: .bold)
    static let bskyDisplayName = Font.system(size: 15, weight: .semibold)
    static let bskyBody        = Font.system(size: 15, weight: .regular)   // 1.4 line height via lineSpacing
    static let bskyHandle      = Font.system(size: 14, weight: .regular)
    static let bskyFeedTab     = Font.system(size: 15, weight: .semibold)
    static let bskyCount       = Font.system(size: 13, weight: .medium)
    static let bskyButton      = Font.system(size: 15, weight: .semibold)
    static let bskyButtonSec   = Font.system(size: 14, weight: .semibold)
    static let bskyTab         = Font.system(size: 10, weight: .semibold)
    static let bskyReplyCtx    = Font.system(size: 13, weight: .regular)
}

// 15pt body at 1.4 → lineSpacing ≈ 15 * 0.4 ≈ 6pt
extension View {
    func bskyBodyText() -> some View { self.font(.bskyBody).lineSpacing(6) }
}
```

## 3. Signature Components

### Skeet Card (the core unit)

```swift
struct SkeetCard: View {
    let displayName: String
    let handle: String          // "@pfrazee.com"
    let timestamp: String
    let body: String
    let avatar: Image
    var replyContext: String? = nil

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            if let ctx = replyContext {
                Label(ctx, systemImage: "arrow.turn.up.left")
                    .font(.bskyReplyCtx).foregroundStyle(Color.bskyTextSec)
            }
            HStack(alignment: .top, spacing: 12) {
                avatar.resizable().frame(width: 42, height: 42).clipShape(Circle())
                VStack(alignment: .leading, spacing: 2) {
                    HStack(spacing: 4) {
                        Text(displayName).font(.bskyDisplayName).foregroundStyle(Color.bskyText)
                        Text("@\(handle) · \(timestamp)").font(.bskyHandle).foregroundStyle(Color.bskyTextSec)
                    }
                }
                Spacer()
                Image(systemName: "ellipsis").foregroundStyle(Color.bskyTextSec)
            }
            Text(body).bskyBodyText().foregroundStyle(Color.bskyText)

            HStack {
                ActionItem(symbol: "bubble.left", count: 8, tint: .bskyTextSec)
                Spacer()
                RepostButton(count: 14)
                Spacer()
                LikeButton(count: 142)
                Spacer()
                ActionItem(symbol: "square.and.arrow.up", count: 0, tint: .bskyTextSec)
            }
            .padding(.top, 4)
            .padding(.trailing, 16)
        }
        .padding(.vertical, 12)
        .padding(.horizontal, 16)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.bskyDivider).frame(height: 1) }
    }
}

struct ActionItem: View {
    let symbol: String; let count: Int; let tint: Color
    var body: some View {
        HStack(spacing: 6) {
            Image(systemName: symbol).font(.system(size: 18))
            if count > 0 { Text("\(count)").font(.bskyCount) }
        }
        .foregroundStyle(tint)
        .frame(minWidth: 44, minHeight: 44, alignment: .leading)
    }
}
```

### Like Button (the pop-scale signature)

```swift
struct LikeButton: View {
    let count: Int
    @State private var liked = false
    @State private var scale: CGFloat = 1

    var body: some View {
        Button {
            liked.toggle()
            withAnimation(.spring(response: 0.3, dampingFraction: 0.45)) { scale = 1.25 }
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.15) {
                withAnimation(.spring(response: 0.3, dampingFraction: 0.7)) { scale = 1 }
            }
        } label: {
            HStack(spacing: 6) {
                Image(systemName: liked ? "heart.fill" : "heart")
                    .font(.system(size: 18)).scaleEffect(scale)
                Text("\(liked ? count + 1 : count)").font(.bskyCount)
            }
            .foregroundStyle(liked ? Color.bskyLike : Color.bskyTextSec)
        }
        .frame(minWidth: 44, minHeight: 44)
        .sensoryFeedback(.impact(weight: .light), trigger: liked)
    }
}
```

### Repost Button

```swift
struct RepostButton: View {
    let count: Int
    @State private var reposted = false
    @State private var scale: CGFloat = 1

    var body: some View {
        Button {
            reposted.toggle()
            withAnimation(.spring(response: 0.25, dampingFraction: 0.5)) { scale = 1.1 }
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.12) {
                withAnimation(.spring()) { scale = 1 }
            }
        } label: {
            HStack(spacing: 6) {
                Image(systemName: "arrow.2.squarepath")
                    .font(.system(size: 18)).scaleEffect(scale)
                Text("\(reposted ? count + 1 : count)").font(.bskyCount)
            }
            .foregroundStyle(reposted ? Color.bskyRepost : Color.bskyTextSec)
        }
        .frame(minWidth: 44, minHeight: 44)
        .sensoryFeedback(.success, trigger: reposted)
    }
}
```

### Primary Button & Compose FAB

```swift
struct BskyPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.bskyButton).foregroundStyle(.white)
                .padding(.vertical, 10).padding(.horizontal, 20)
                .background(Capsule().fill(Color.bskyBlue))
        }
        .buttonStyle(BskyPressable())
    }
}

struct ComposeFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "square.and.pencil")
                .font(.system(size: 24, weight: .medium))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(Circle().fill(Color.bskyBlue))
                .shadow(color: Color.bskyBlue.opacity(0.35), radius: 20, y: 6)
        }
        .buttonStyle(BskyPressable(pressedScale: 0.94))
        .sensoryFeedback(.impact(weight: .soft), trigger: UUID())
    }
}

struct BskyPressable: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

## 4. Distinctive System — Pinned Custom-Feed Selector

```swift
struct FeedSelector: View {
    let feeds: [String]          // ["Following", "Discover", "Science", …]
    @Binding var selected: String
    @Namespace private var ns

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 20) {
                ForEach(feeds, id: \.self) { feed in
                    VStack(spacing: 8) {
                        Text(feed)
                            .font(.bskyFeedTab)
                            .foregroundStyle(selected == feed ? Color.bskyBlue : Color.bskyTextSec)
                        ZStack {
                            if selected == feed {
                                Capsule().fill(Color.bskyBlue).frame(height: 3)
                                    .matchedGeometryEffect(id: "feedUnderline", in: ns)
                            } else { Color.clear.frame(height: 3) }
                        }
                    }
                    .contentShape(Rectangle())
                    .onTapGesture { withAnimation(.easeOut(duration: 0.25)) { selected = feed } }
                }
            }
            .padding(.horizontal, 16)
        }
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterial)
        a.backgroundColor = UIColor(Color.bskyCanvas).withAlphaComponent(0.94)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            HomeView().tabItem          { Label("Home", systemImage: "house.fill") }
            SearchView().tabItem        { Label("Search", systemImage: "magnifyingglass") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell.fill") }
            ChatView().tabItem          { Label("Chat", systemImage: "message.fill") }
            ProfileView().tabItem       { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.bskyBlue)
    }
}
```

## 6. Motion

```swift
// Like: heart fill #EC4899 + pop-scale 1.0 → 1.25 → 1.0 ~300ms spring + light haptic (see LikeButton)
// Repost: tint #2DBE85 + 1.0 → 1.1 → 1.0 bounce + success haptic (see RepostButton)

// Feed switch: matchedGeometryEffect underline + 0.25s ease-out cross-fade
withAnimation(.easeOut(duration: 0.25)) { selected = feed }

// Compose FAB: scale 0.94 on press + soft haptic; sheet via .sheet with spring presentation

// Theme switch: 0.2s color cross-fade
withAnimation(.easeInOut(duration: 0.2)) { theme = newTheme }
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Reply | `bubble.left` | 18pt |
| Repost | `arrow.2.squarepath` | 18pt |
| Like | `heart` / `heart.fill` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| More | `ellipsis` | 18pt |
| Reply context | `arrow.turn.up.left` | 13pt |
| Compose (FAB) | `square.and.pencil` | 24pt |
| Home (tab) | `house.fill` | 26pt |
| Search (tab) | `magnifyingglass` | 26pt |
| Notifications (tab) | `bell.fill` | 26pt |
| Chat (tab) | `message.fill` | 26pt |
| Profile (tab) | `person.crop.circle` | 26pt |
| Feed settings | `slider.horizontal.3` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` and `matchedGeometryEffect` polish prefer iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type generously on skeet body, names, and bios — Bluesky is conversational; only tab labels stay fixed
- Provide all three themes (Light/Dim/Dark) via an app-level `@AppStorage` theme enum; swap the token sets and cross-fade surfaces over 0.2s
- VoiceOver: announce a skeet as "<name>, @<handle>, <relative time>, <body>"; expose like/repost state ("Liked"/"Reposted"); the feed selector should read as a tab list ("Following, tab 1 of 4")
- Contrast: `#697787` on `#FFFFFF` passes WCAG AA; in Dark `#8B98A5` on `#0B0F14` passes AA at 14pt+; validate the Dim mode `#9CA6B5` on `#1E2936`
- Respect Reduce Motion: replace the like pop-scale with a simple fill cross-fade and the feed switch with an instant change
