# Threads (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Threads' visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark / Default)
    static let threadsCanvas          = Color.black                                   // #000000
    static let threadsSurface1        = Color(red: 0.063, green: 0.063, blue: 0.063) // #101010
    static let threadsSurface2        = Color(red: 0.094, green: 0.094, blue: 0.094) // #181818
    static let threadsDivider         = Color(red: 0.133, green: 0.133, blue: 0.133) // #222222
    static let threadsLine            = Color(red: 0.200, green: 0.200, blue: 0.200) // #333333

    // MARK: - Canvas & Surfaces (Light)
    static let threadsLightCanvas     = Color.white                                   // #FFFFFF
    static let threadsLightSurface1   = Color(red: 0.980, green: 0.980, blue: 0.980) // #FAFAFA
    static let threadsLightSurface2   = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let threadsLightDivider    = Color(red: 0.859, green: 0.859, blue: 0.859) // #DBDBDB
    static let threadsLightLine       = Color(red: 0.851, green: 0.851, blue: 0.851) // #D9D9D9

    // MARK: - Text
    static let threadsTextPrimaryDark  = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let threadsTextPrimaryLight = Color.black                                   // #000000
    static let threadsTextSecondary    = Color(red: 0.467, green: 0.467, blue: 0.467) // #777777
    static let threadsTextTertiaryDark = Color(red: 0.302, green: 0.302, blue: 0.302) // #4D4D4D
    static let threadsTextTertiaryLight = Color(red: 0.600, green: 0.600, blue: 0.600) // #999999

    // MARK: - Brand / Action
    static let threadsLinkBlue        = Color(red: 0.176, green: 0.498, blue: 0.976) // #2D7FF9
    static let threadsLikeCoral       = Color(red: 0.996, green: 0.173, blue: 0.333) // #FE2C55
    static let threadsErrorRed        = Color(red: 0.929, green: 0.286, blue: 0.337) // #ED4956
    static let threadsSuccessGreen    = Color(red: 0.345, green: 0.765, blue: 0.133) // #58C322
    static let threadsIGVerified      = Color(red: 0.000, green: 0.584, blue: 0.965) // #0095F6
}
```

## 2. Typography

Instagram Sans is proprietary to Meta. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to SF Pro — its tall-x-height quality is the closest free substitute.

```swift
extension Font {
    static let threadsScreenTitle    = Font.custom("InstagramSans-Bold",     size: 17).weight(.bold)
    static let threadsDisplayName    = Font.custom("InstagramSans-Semibold", size: 15).weight(.semibold)
    static let threadsPostBody       = Font.custom("InstagramSans-Regular",  size: 15).weight(.regular)
    static let threadsQuotedBody     = Font.custom("InstagramSans-Regular",  size: 14).weight(.regular)
    static let threadsHandle         = Font.custom("InstagramSans-Regular",  size: 14).weight(.regular)
    static let threadsActionCount    = Font.custom("InstagramSans-Regular",  size: 13).weight(.regular)
    static let threadsProfileBio     = Font.custom("InstagramSans-Regular",  size: 15).weight(.regular)
    static let threadsButton         = Font.custom("InstagramSans-Semibold", size: 15).weight(.semibold)
    static let threadsSecondaryBtn   = Font.custom("InstagramSans-Semibold", size: 14).weight(.semibold)
    static let threadsComposePH      = Font.custom("InstagramSans-Regular",  size: 17).weight(.regular)
    static let threadsDMBody         = Font.custom("InstagramSans-Regular",  size: 15).weight(.regular)
    static let threadsFilterChip     = Font.custom("InstagramSans-Semibold", size: 14).weight(.semibold)
}

// System fallback if Instagram Sans isn't bundled:
extension Font {
    static func threadsFallback(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Thread Post Row (with thread line)

```swift
struct ThreadPostRow: View {
    let displayName: String
    let handle: String
    let timestamp: String
    let avatar: Image
    let body: String
    let hasReplies: Bool  // if true, render the thread line
    var likeCount: Int = 0
    var commentCount: Int = 0
    @State private var isLiked = false
    @State private var isReposted = false

    var body: some View {
        VStack(spacing: 0) {
            HStack(alignment: .top, spacing: 12) {
                // Avatar + thread line column
                VStack(spacing: 0) {
                    avatar
                        .resizable()
                        .frame(width: 36, height: 36)
                        .clipShape(Circle())
                    if hasReplies {
                        Rectangle()
                            .fill(Color.threadsLine)
                            .frame(width: 1)
                            .padding(.top, 4)
                    }
                }

                VStack(alignment: .leading, spacing: 6) {
                    // Header: name + handle + time + overflow
                    HStack(spacing: 4) {
                        Text(displayName)
                            .font(.threadsDisplayName)
                            .foregroundStyle(.threadsTextPrimaryDark)
                            .lineLimit(1)
                        Text("@\(handle)")
                            .font(.threadsHandle)
                            .foregroundStyle(.threadsTextSecondary)
                            .lineLimit(1)
                        Text("·")
                            .foregroundStyle(.threadsTextSecondary)
                        Text(timestamp)
                            .font(.threadsHandle)
                            .foregroundStyle(.threadsTextSecondary)
                        Spacer()
                        Button { /* overflow */ } label: {
                            Image(systemName: "ellipsis")
                                .font(.system(size: 18))
                                .foregroundStyle(.threadsTextSecondary)
                        }
                    }

                    // Body
                    Text(body)
                        .font(.threadsPostBody)
                        .foregroundStyle(.threadsTextPrimaryDark)
                        .lineSpacing(6)  // approximates 1.4 line-height at 15pt
                        .fixedSize(horizontal: false, vertical: true)

                    // Action row: heart, comment, repost, share
                    HStack(spacing: 20) {
                        ActionBtn(icon: isLiked ? "heart.fill" : "heart",
                                  count: likeCount,
                                  tint: isLiked ? .threadsLikeCoral : .threadsTextSecondary) {
                            withAnimation(.spring(response: 0.3)) { isLiked.toggle() }
                        }
                        ActionBtn(icon: "bubble.left", count: commentCount, tint: .threadsTextSecondary) {}
                        ActionBtn(icon: isReposted ? "arrow.2.squarepath.circle.fill" : "arrow.2.squarepath",
                                  count: nil,
                                  tint: .threadsTextSecondary) {
                            isReposted.toggle()
                        }
                        ActionBtn(icon: "paperplane", count: nil, tint: .threadsTextSecondary) {}
                        Spacer()
                    }
                    .padding(.top, 4)
                }
            }
            .padding(.horizontal, 16)
            .padding(.top, 16)
            .padding(.bottom, 12)

            Divider().background(Color.threadsDivider)
        }
        .background(Color.threadsCanvas)
    }
}

struct ActionBtn: View {
    let icon: String
    let count: Int?
    let tint: Color
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: icon)
                    .font(.system(size: 22, weight: .regular))
                    .foregroundStyle(tint)
                if let c = count, c > 0 {
                    Text("\(c)")
                        .font(.threadsActionCount)
                        .foregroundStyle(tint)
                }
            }
            .frame(minWidth: 44, minHeight: 44, alignment: .leading)
        }
    }
}
```

### Primary Post Pill Button

```swift
struct ThreadsPostPill: View {
    let title: String
    let isEnabled: Bool
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.threadsButton)
                .foregroundStyle(.black)
                .padding(.vertical, 8)
                .padding(.horizontal, 20)
                .background(Capsule().fill(Color.threadsTextPrimaryDark))
        }
        .disabled(!isEnabled)
        .opacity(isEnabled ? 1 : 0.3)
        .buttonStyle(ThreadsPressableStyle())
    }
}

struct ThreadsPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .opacity(configuration.isPressed ? 0.8 : 1)
            .animation(.easeInOut(duration: 0.15), value: configuration.isPressed)
    }
}
```

### Follow Pill

```swift
struct ThreadsFollowPill: View {
    @Binding var isFollowing: Bool
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(isFollowing ? "Following" : "Follow")
                .font(.threadsSecondaryBtn)
                .foregroundStyle(isFollowing ? .threadsTextPrimaryDark : .black)
                .padding(.vertical, 8)
                .padding(.horizontal, 20)
                .frame(minWidth: 92)
                .background(
                    Capsule().fill(isFollowing ? Color.clear : Color.threadsTextPrimaryDark)
                )
                .overlay(
                    Capsule().strokeBorder(isFollowing ? Color.threadsTextSecondary : .clear, lineWidth: 1)
                )
        }
        .buttonStyle(ThreadsPressableStyle())
    }
}
```

### Activity Filter Chip Row

```swift
struct ActivityFilterRow: View {
    @Binding var selected: String
    private let chips = ["All", "Follows", "Replies", "Mentions", "Quotes", "Reposts", "Verified"]

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                ForEach(chips, id: \.self) { label in
                    let isOn = selected == label
                    Button {
                        withAnimation(.easeInOut(duration: 0.2)) { selected = label }
                    } label: {
                        Text(label)
                            .font(.threadsFilterChip)
                            .foregroundStyle(isOn ? .black : .threadsTextPrimaryDark)
                            .padding(.vertical, 8)
                            .padding(.horizontal, 16)
                            .frame(minHeight: 36)
                            .background(
                                Capsule().fill(isOn ? Color.threadsTextPrimaryDark : .clear)
                            )
                            .overlay(
                                Capsule().strokeBorder(isOn ? Color.clear : Color.threadsLine, lineWidth: 1)
                            )
                    }
                }
            }
            .padding(.horizontal, 16)
        }
    }
}
```

## 4. Threads-Specific Feature: Post Composer with Multi-Post Thread

```swift
struct ThreadsComposer: View {
    @State private var drafts: [String] = [""]
    @State private var currentIndex: Int = 0
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            ScrollView {
                VStack(alignment: .leading, spacing: 0) {
                    ForEach(drafts.indices, id: \.self) { i in
                        HStack(alignment: .top, spacing: 12) {
                            VStack(spacing: 0) {
                                Circle().fill(Color.threadsSurface2).frame(width: 36, height: 36)
                                if i < drafts.count - 1 || drafts[i].isEmpty == false {
                                    Rectangle()
                                        .fill(Color.threadsLine)
                                        .frame(width: 1)
                                        .frame(maxHeight: .infinity)
                                        .padding(.top, 4)
                                }
                            }

                            VStack(alignment: .leading, spacing: 8) {
                                Text("you").font(.threadsDisplayName).foregroundStyle(.threadsTextPrimaryDark)
                                TextField("Start a thread...", text: $drafts[i], axis: .vertical)
                                    .font(.threadsComposePH)
                                    .foregroundStyle(.threadsTextPrimaryDark)
                                    .tint(.threadsTextPrimaryDark)
                                    .lineLimit(1...20)
                            }
                        }
                        .padding(.horizontal, 16)
                        .padding(.vertical, 12)
                    }

                    // Add-to-thread dot
                    HStack(alignment: .center, spacing: 12) {
                        Circle()
                            .strokeBorder(Color.threadsLine, lineWidth: 1)
                            .frame(width: 20, height: 20)
                            .overlay(
                                Image(systemName: "plus")
                                    .font(.system(size: 10, weight: .semibold))
                                    .foregroundStyle(.threadsTextSecondary)
                            )
                            .padding(.leading, 24)
                        Button("Add to thread") { drafts.append("") }
                            .font(.threadsHandle)
                            .foregroundStyle(.threadsTextSecondary)
                    }
                    .padding(.top, 8)
                }
            }
            .background(Color.threadsCanvas)
            .toolbar {
                ToolbarItem(placement: .cancellationAction) {
                    Button("Cancel") { dismiss() }
                        .foregroundStyle(.threadsTextPrimaryDark)
                }
                ToolbarItem(placement: .confirmationAction) {
                    ThreadsPostPill(title: "Post",
                                    isEnabled: drafts.contains(where: { !$0.isEmpty }),
                                    action: { /* submit */ dismiss() })
                }
            }
            .toolbarBackground(Color.threadsCanvas, for: .navigationBar)
        }
        .preferredColorScheme(.dark)
    }
}
```

## 5. Tab Bar

Threads uses an icon-only tab bar — no labels. Compose is one of the tabs (no floating FAB).

```swift
struct RootTabView: View {
    @State private var userAvatar = Image("placeholder-avatar")

    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor.black.withAlphaComponent(0.9)
        // Hide tab labels
        let iconAppearance = UITabBarItemAppearance()
        iconAppearance.normal.titleTextAttributes = [.foregroundColor: UIColor.clear]
        iconAppearance.selected.titleTextAttributes = [.foregroundColor: UIColor.clear]
        appearance.stackedLayoutAppearance = iconAppearance
        appearance.inlineLayoutAppearance = iconAppearance
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeView()     .tabItem { Image(systemName: "house.fill") }
            SearchView()   .tabItem { Image(systemName: "magnifyingglass") }
            ComposeView()  .tabItem { Image(systemName: "plus.square") }
            ActivityView() .tabItem { Image(systemName: "heart.fill") }
            ProfileView()  .tabItem { Image(systemName: "person.circle.fill") }
        }
        .tint(.threadsTextPrimaryDark)
    }
}
```

## 6. Motion

Use SwiftUI spring defaults. Haptics via the modern `.sensoryFeedback` API (iOS 17+).

```swift
// Like tap
.sensoryFeedback(.impact(flexibility: .soft), trigger: isLiked)

// Repost tap — outline to filled cross-fade (no color change)
.animation(.easeInOut(duration: 0.2), value: isReposted)

// Compose open — full-screen sheet slide-up
.sheet(isPresented: $showCompose) {
    ThreadsComposer()
        .presentationDetents([.large])
        .presentationDragIndicator(.hidden)
}

// Pull-to-refresh — @-logo pulse
// Use ScrollView geometry + a custom overlay that scales the "@" based on offset.

// Profile tab strip underline
@Namespace private var tabIndicator
if selectedTab == tabName {
    Rectangle()
        .fill(Color.threadsTextPrimaryDark)
        .frame(height: 2)
        .matchedGeometryEffect(id: "indicator", in: tabIndicator)
}
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Like (outline) | `heart` | 22pt |
| Like (filled) | `heart.fill` | 22pt |
| Comment | `bubble.left` | 22pt |
| Repost (outline) | `arrow.2.squarepath` | 22pt |
| Repost (filled) | `arrow.2.squarepath.circle.fill` | 22pt |
| Share | `paperplane` | 22pt |
| Overflow | `ellipsis` | 18pt |
| Home tab | `house.fill` / `house` | 26pt |
| Search tab | `magnifyingglass` | 26pt |
| Compose tab | `plus.square` / `plus.square.fill` | 26pt |
| Activity tab | `heart` / `heart.fill` | 26pt |
| Profile tab | `person.circle.fill` or user avatar | 26pt |
| Verified (IG-inherited) | `checkmark.seal.fill` in `#0095F6` | 14pt |
| Add-to-thread plus | `plus` inside a 20pt circle border | 10pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on post body, compose placeholder, bio, DM body — fix display name/handle/action counts
- VoiceOver: group the post row as a single element with a container shape; expose the thread line as an informational role ("Thread, reply count {n}")
- Contrast: off-white `#F5F5F5` on true-black meets WCAG AAA; secondary `#777777` on black is borderline at 14pt — keep handles/timestamps at 14pt+ only
- Reduce Motion: respect `@Environment(\.accessibilityReduceMotion)` — skip the `@`-logo pull-to-refresh animation; still fire haptics and color changes on action taps
- Reduce Transparency: drop `.regularMaterial` blur on top nav + tab bar in favor of solid canvas when enabled
- Dynamic Type also affects compose placeholder (17pt) — it scales up with user preferences so the "Start a thread..." prompt stays legible
- The thread line is purely visual — it's NOT interactive and should be hidden from accessibility (`.accessibilityHidden(true)`)
