# Lemon8 (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Lemon8's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the two-column masonry feed, the lifestyle card, the pastel tag system, and the post-detail article.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light — core identity)
    static let l8Canvas    = Color.white                                   // #FFFFFF
    static let l8Surface1  = Color(red: 0.980, green: 0.980, blue: 0.980)  // #FAFAFA
    static let l8Surface2  = Color(red: 0.949, green: 0.949, blue: 0.949)  // #F2F2F2
    static let l8Divider   = Color(red: 0.925, green: 0.925, blue: 0.925)  // #ECECEC

    // MARK: - Canvas & Surfaces (Dark)
    static let l8DarkCanvas   = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let l8DarkSurface1 = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let l8DarkSurface2 = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let l8DarkDivider  = Color(red: 0.180, green: 0.180, blue: 0.180) // #2E2E2E

    // MARK: - Text
    static let l8Ink            = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let l8TextSecondary  = Color(red: 0.420, green: 0.420, blue: 0.420) // #6B6B6B
    static let l8TextTertiary   = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A
    static let l8TextPrimaryDk  = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2

    // MARK: - Brand (used sparingly — always with ink text)
    static let l8Yellow        = Color(red: 1.000, green: 0.902, blue: 0.000) // #FFE600
    static let l8YellowPressed = Color(red: 0.902, green: 0.812, blue: 0.000) // #E6CF00

    // MARK: - Pastel Topic Tags (background + saturated text sibling)
    static let l8TagMint     = Color(red: 0.894, green: 0.961, blue: 0.925) // #E4F5EC
    static let l8TagMintInk  = Color(red: 0.122, green: 0.541, blue: 0.322) // #1F8A52
    static let l8TagBlush    = Color(red: 0.988, green: 0.910, blue: 0.933) // #FCE8EE
    static let l8TagBlushInk = Color(red: 0.757, green: 0.243, blue: 0.408) // #C13E68
    static let l8TagSky      = Color(red: 0.902, green: 0.941, blue: 0.984) // #E6F0FB
    static let l8TagSkyInk   = Color(red: 0.176, green: 0.435, blue: 0.722) // #2D6FB8
    static let l8TagButter   = Color(red: 1.000, green: 0.965, blue: 0.800) // #FFF6CC
    static let l8TagButterInk = Color(red: 0.541, green: 0.451, blue: 0.000) // #8A7300

    // MARK: - Semantic
    static let l8LikeRed  = Color(red: 1.000, green: 0.180, blue: 0.388) // #FF2E63
    static let l8Success  = Color(red: 0.122, green: 0.722, blue: 0.467) // #1FB877
    static let l8Error    = Color(red: 1.000, green: 0.231, blue: 0.361) // #FF3B5C
}
```

## 2. Typography

Bundle **Poppins** (display/titles) and **Inter** (UI/body) via `Info.plist` / `UIAppFonts`. Both SIL OFL.

```swift
extension Font {
    // Display — Poppins (magazine headlines)
    static let l8Display     = Font.custom("Poppins-ExtraBold", size: 32) // -0.5 tracking
    static let l8ScreenTitle = Font.custom("Poppins-Bold",      size: 26)
    static let l8TabHeader   = Font.custom("Poppins-Bold",      size: 22)
    static let l8PostTitle   = Font.custom("Poppins-SemiBold",  size: 18)
    static let l8Subheading  = Font.custom("Poppins-SemiBold",  size: 17)

    // UI / body — Inter
    static let l8Body      = Font.custom("Inter-Regular",  size: 16)
    static let l8CardTitle = Font.custom("Inter-SemiBold", size: 15)
    static let l8Button    = Font.custom("Inter-Bold",     size: 15)
    static let l8Meta      = Font.custom("Inter-Regular",  size: 13)
    static let l8CardByline = Font.custom("Inter-Medium",  size: 11)
    static let l8Tag       = Font.custom("Inter-SemiBold", size: 11)
    static let l8Tab       = Font.custom("Inter-Medium",   size: 10)
    static let l8Caption   = Font.custom("Inter-Regular",  size: 12)
}
```

## 3. Signature Components

### Two-Column Masonry Feed

Places each card into the currently-shorter column for a balanced stagger.

```swift
struct Post: Identifiable {
    let id = UUID()
    let aspect: CGFloat       // cover aspect (w/h): 1.0, 0.75, 0.8
    let gradient: [Color]
    let title: String
    let tag: (text: String, bg: Color, fg: Color)
    let creator: String
    let likes: String
    let photoChip: String?
}

struct MasonryFeed: View {
    let posts: [Post]
    private let columnGap: CGFloat = 10

    private func split() -> ([Post], [Post]) {
        var left: [Post] = [], right: [Post] = []
        var lh: CGFloat = 0, rh: CGFloat = 0
        for p in posts {
            // estimate card height from cover aspect + ~110pt body
            let h = (1 / p.aspect) + 1.4
            if lh <= rh { left.append(p); lh += h } else { right.append(p); rh += h }
        }
        return (left, right)
    }

    var body: some View {
        let (left, right) = split()
        ScrollView {
            HStack(alignment: .top, spacing: columnGap) {
                LazyVStack(spacing: 12) { ForEach(left) { LifestyleCard(post: $0) } }
                LazyVStack(spacing: 12) { ForEach(right) { LifestyleCard(post: $0) } }
            }
            .padding(10)
        }
        .background(Color.l8Canvas)
    }
}
```

### Lifestyle Card

```swift
struct LifestyleCard: View {
    let post: Post

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .bottomLeading) {
                LinearGradient(colors: post.gradient, startPoint: .topLeading, endPoint: .bottomTrailing)
                    .aspectRatio(post.aspect, contentMode: .fill)
                    .frame(maxWidth: .infinity)
                    .clipped()
                if let chip = post.photoChip {
                    Text(chip)
                        .font(.custom("Inter-SemiBold", size: 10))
                        .foregroundStyle(.white)
                        .padding(.vertical, 4).padding(.horizontal, 8)
                        .background(.black.opacity(0.55), in: RoundedRectangle(cornerRadius: 6))
                        .padding(8)
                }
            }
            VStack(alignment: .leading, spacing: 7) {
                Text(post.title)
                    .font(.custom("Inter-SemiBold", size: 13))
                    .foregroundStyle(Color.l8Ink)
                    .lineLimit(2)
                Text(post.tag.text)
                    .font(.custom("Inter-SemiBold", size: 9))
                    .foregroundStyle(post.tag.fg)
                    .padding(.vertical, 3).padding(.horizontal, 7)
                    .background(post.tag.bg, in: RoundedRectangle(cornerRadius: 5))
                HStack(spacing: 6) {
                    Circle()
                        .fill(LinearGradient(colors: [.l8Yellow, .l8LikeRed], startPoint: .topLeading, endPoint: .bottomTrailing))
                        .frame(width: 18, height: 18)
                    Text(post.creator).font(.l8CardByline).foregroundStyle(Color.l8TextSecondary)
                    Spacer()
                    Image(systemName: "heart").font(.system(size: 12)).foregroundStyle(Color.l8TextSecondary)
                    Text(post.likes).font(.l8CardByline).foregroundStyle(Color.l8TextSecondary)
                }
            }
            .padding(11)
        }
        .background(Color.l8Canvas)
        .clipShape(RoundedRectangle(cornerRadius: 14))
        .shadow(color: .black.opacity(0.06), radius: 3, y: 1)
        .shadow(color: .black.opacity(0.04), radius: 12, y: 4)
    }
}
```

### Pastel Topic Tag

```swift
struct TopicTag: View {
    let text: String
    let bg: Color
    let fg: Color

    var body: some View {
        Text(text)
            .font(.l8Tag)
            .foregroundStyle(fg)
            .padding(.vertical, 7).padding(.horizontal, 14)
            .background(bg, in: RoundedRectangle(cornerRadius: 8))
    }
}
// e.g. TopicTag(text: "Recipe", bg: .l8TagMint, fg: .l8TagMintInk)
```

### Top Segmented Tabs

```swift
struct TopTabs: View {
    @Binding var selection: Int
    let titles = ["Following", "For You", "Nearby"]
    @Namespace private var underline

    var body: some View {
        HStack(spacing: 22) {
            ForEach(titles.indices, id: \.self) { i in
                VStack(spacing: 6) {
                    Text(titles[i])
                        .font(.l8TabHeader)
                        .fontWeight(selection == i ? .bold : .semibold)
                        .foregroundStyle(selection == i ? Color.l8Ink : Color.l8TextTertiary)
                    ZStack {
                        if selection == i {
                            Capsule().fill(Color.l8Yellow)
                                .frame(width: 20, height: 3)
                                .matchedGeometryEffect(id: "u", in: underline)
                        } else {
                            Capsule().fill(Color.clear).frame(width: 20, height: 3)
                        }
                    }
                }
                .onTapGesture {
                    withAnimation(.easeOut(duration: 0.22)) { selection = i }
                }
            }
            Spacer()
            Image(systemName: "magnifyingglass").font(.system(size: 22, weight: .semibold)).foregroundStyle(Color.l8Ink)
        }
        .padding(.horizontal, 18).padding(.top, 4).padding(.bottom, 12)
    }
}
```

### Primary / Follow Buttons

```swift
struct L8PrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.l8Button)
                .foregroundStyle(Color.l8Ink)               // ink — never white on yellow
                .padding(.vertical, 13).padding(.horizontal, 28)
                .background(Color.l8Yellow, in: Capsule())
        }
        .buttonStyle(.plain)
    }
}

struct FollowPill: View {
    @State private var following = false
    var body: some View {
        Button { following.toggle() } label: {
            Text(following ? "Following" : "Follow")
                .font(.custom("Inter-Bold", size: 12))
                .foregroundStyle(following ? Color.l8TextSecondary : Color.l8Ink)
                .padding(.vertical, 7).padding(.horizontal, 18)
                .background(
                    Capsule().fill(following ? Color.clear : Color.l8Yellow)
                )
                .overlay(Capsule().strokeBorder(Color(white: 0.85), lineWidth: following ? 1 : 0))
        }
        .buttonStyle(.plain)
    }
}
```

### Post Detail (magazine article)

```swift
struct PostDetailView: View {
    let post: Post
    @State private var page = 0

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 0) {
                TabView(selection: $page) {
                    ForEach(0..<5, id: \.self) { i in
                        LinearGradient(colors: post.gradient, startPoint: .top, endPoint: .bottom).tag(i)
                    }
                }
                .tabViewStyle(.page)
                .frame(height: 380)
                .overlay(alignment: .topTrailing) {
                    Text("\(page + 1)/5")
                        .font(.custom("Inter-SemiBold", size: 11)).foregroundStyle(.white)
                        .padding(.vertical, 4).padding(.horizontal, 8)
                        .background(.black.opacity(0.55), in: Capsule())
                        .padding(12)
                }

                Group {
                    Text("My 6-step evening skincare routine")
                        .font(.l8PostTitle).foregroundStyle(Color.l8Ink)
                        .padding(.top, 16)

                    HStack(spacing: 10) {
                        Circle().fill(LinearGradient(colors: [.l8Yellow, .l8LikeRed], startPoint: .top, endPoint: .bottom))
                            .frame(width: 56, height: 56)
                        VStack(alignment: .leading, spacing: 2) {
                            Text("glow.diary").font(.l8Subheading).foregroundStyle(Color.l8Ink)
                            Text("48.2k followers").font(.l8Meta).foregroundStyle(Color.l8TextSecondary)
                        }
                        Spacer()
                        FollowPill()
                    }
                    .padding(.top, 14)

                    Text("Glass skin starts with double cleansing. Here is the full breakdown of every step and the products I actually repurchase.")
                        .font(.l8Body).foregroundStyle(Color.l8Ink)
                        .padding(.top, 14)

                    Text("Step 1 — Oil cleanse")
                        .font(.l8Subheading).foregroundStyle(Color.l8Ink)
                        .padding(.top, 18)

                    HStack(spacing: 8) {
                        TopicTag(text: "Beauty", bg: .l8TagBlush, fg: .l8TagBlushInk)
                        TopicTag(text: "Skincare", bg: .l8TagSky, fg: .l8TagSkyInk)
                    }
                    .padding(.top, 16)
                }
                .padding(.horizontal, 16)
            }
        }
        .background(Color.l8Canvas)
    }
}
```

## 4. Bottom Tab Bar

The center "Post" slot is a yellow rounded rectangle, not an icon-only tab.

```swift
struct L8TabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house") }
            DiscoverView().tabItem { Label("Discover", systemImage: "magnifyingglass") }
            ComposeView().tabItem { Label("", systemImage: "plus") }   // styled separately
            InboxView().tabItem { Label("Inbox", systemImage: "message") }
            ProfileView().tabItem { Label("Me", systemImage: "person") }
        }
        .tint(.l8Ink)
        .toolbarBackground(.ultraThinMaterial, for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
        // Overlay a custom yellow 42x30 RoundedRectangle (radius 9) with an ink "+"
        // centered over the middle tab via a ZStack + safeAreaInset for pixel-accurate FAB.
    }
}
```

## 5. Motion

```swift
// Masonry card press
.scaleEffect(pressed ? 0.98 : 1.0)
.animation(.easeOut(duration: 0.12), value: pressed)

// Card -> post detail (shared element)
@Namespace private var cardNS
// card cover: .matchedGeometryEffect(id: post.id, in: cardNS)
// detail carousel: .matchedGeometryEffect(id: post.id, in: cardNS)
// transition .animation(.easeOut(duration: 0.30))

// Top tab underline slide
withAnimation(.easeOut(duration: 0.22)) { selection = i }   // matchedGeometryEffect "u"

// Like heart burst
withAnimation(.spring(response: 0.26, dampingFraction: 0.6)) { likeScale = 1.2 }
// ...back to 1.0 after 0.13s; tint -> .l8LikeRed

// New masonry cards
.transition(.opacity.combined(with: .move(edge: .bottom)))
.animation(.easeOut(duration: 0.22), value: posts.count)

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: liked)        // like
.sensoryFeedback(.selection, trigger: selection)                     // tab switch
.sensoryFeedback(.impact(weight: .medium), trigger: followed)        // follow / save
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 23pt |
| Discover (tab) | `magnifyingglass` | 23pt |
| Post (tab, center) | `plus` | 20pt (ink, on yellow rect) |
| Inbox (tab) | `message` / `message.fill` | 23pt |
| Me (tab) | `person` / `person.fill` | 23pt |
| Like (inactive) | `heart` | 24pt |
| Like (active) | `heart.fill` | 24pt (`#FF2E63`) |
| Save / bookmark | `bookmark` / `bookmark.fill` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Comment | `bubble.right` | 22pt |
| Search (top tabs) | `magnifyingglass` | 22pt |
| Back | `chevron.left` | 22pt |
| Overflow | `ellipsis` | 20pt |
| Photo carousel page | `circle.fill` | 6pt (dots) |

## 7. Dark Mode

```swift
struct L8Theme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.l8DarkCanvas : Color.l8Canvas)
            .foregroundStyle(scheme == .dark ? Color.l8TextPrimaryDk : Color.l8Ink)
    }
}
extension View { func l8Theme() -> some View { modifier(L8Theme()) } }
```

Lemon8 is **light-first** — the magazine-white aesthetic is core identity. In dark mode the canvas drops to `#121212`, cards to `#1C1C1E`. The pastel tags shift to muted dark equivalents (mint `#1F3A2C`/`#6FD49E`, blush `#3A2630`/`#F09BB6`, sky `#1F2F42`/`#8FBEE8`, butter `#3A350F`/`#E6CF66`) so they stay legible without glowing. Lemon8 Yellow `#FFE600` is identical in both modes and stays paired with ink `#1A1A1A` — never white-on-yellow. Card cover photography never dims.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `matchedGeometryEffect` shared transitions smoothest on iOS 17; fall back to `UIImpactFeedbackGenerator` on 16)
- Bundle Poppins + Inter TTFs via `Info.plist` — both SIL OFL, free to ship
- Dynamic Type: scale display, screen titles, post title, body; card titles scale then `lineLimit(2)`; keep tags, card bylines, tab labels FIXED (layout-sensitive in the masonry)
- VoiceOver: label cards "Post: {title}, by {creator}, {likes} likes"; the like control as a toggle with selected trait; tags as "Topic: {name}, button"; the Post FAB as "Create post"
- Color contrast: ink `#1A1A1A` on `#FFFFFF` is maximal; each pastel tag is paired with a saturated text sibling that passes WCAG AA (validated for mint/blush/sky/butter); never put white on `#FFE600`
- Reduce Motion: disable the like spring/burst (crossfade fill), disable the card->detail shared zoom (cross-dissolve), keep the tab underline as an instant move; keep selection states
- Reduce Transparency: replace the `.ultraThinMaterial` tab bar with solid `#FFFFFF` (light) / `#121212` (dark)
- Masonry: recompute the column split when Dynamic Type or width changes (rotation / iPad); memoize per width + content size category
