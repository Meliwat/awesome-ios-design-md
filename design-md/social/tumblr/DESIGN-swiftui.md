# Tumblr (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Tumblr's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (deep navy — the default)
    static let tmblrCanvas   = Color(red: 0.0,   green: 0.098, blue: 0.208) // #001935
    static let tmblrSurface1 = Color(red: 0.0,   green: 0.063, blue: 0.125) // #001020
    static let tmblrSurface2 = Color(red: 0.043, green: 0.165, blue: 0.271) // #0B2A45
    static let tmblrSurface3 = Color(red: 0.075, green: 0.224, blue: 0.357) // #13395B
    static let tmblrDivider  = Color(red: 0.212, green: 0.275, blue: 0.365) // #36465D

    // MARK: - Text
    static let tmblrTextPrimary   = Color.white                                 // #FFFFFF
    static let tmblrTextSecondary = Color(red: 0.541, green: 0.604, blue: 0.682) // #8A9AAE
    static let tmblrTextTertiary  = Color(red: 0.361, green: 0.420, blue: 0.502) // #5C6B80

    // MARK: - Brand trio
    static let tmblrBlue        = Color(red: 0.0, green: 0.722, blue: 1.0)   // #00B8FF
    static let tmblrBluePressed = Color(red: 0.0, green: 0.565, blue: 0.800) // #0090CC
    static let tmblrGreen       = Color(red: 0.0, green: 0.812, blue: 0.208) // #00CF35
    static let tmblrPinkRed     = Color(red: 1.0, green: 0.286, blue: 0.188) // #FF4930

    // MARK: - Semantic
    static let tmblrWarning = Color(red: 1.0, green: 0.702, blue: 0.0)       // #FFB300
}
```

## 2. Typography

Tumblr uses the system font (SF Pro); the web client renders Inter/Helvetica. Use `.system` directly; use a serif for quote-type posts.

```swift
extension Font {
    static let tmblrTitleLarge = Font.system(size: 28, weight: .bold)
    static let tmblrQuote      = Font.system(size: 26, weight: .bold, design: .serif) // editorial quote posts
    static let tmblrSection    = Font.system(size: 20, weight: .bold)
    static let tmblrBlogName   = Font.system(size: 16, weight: .bold)
    static let tmblrBody       = Font.system(size: 16, weight: .regular)   // 1.5 line height via lineSpacing
    static let tmblrReblog     = Font.system(size: 15, weight: .regular)
    static let tmblrBodySettings = Font.system(size: 15, weight: .regular)
    static let tmblrTag        = Font.system(size: 14, weight: .regular)
    static let tmblrNotes      = Font.system(size: 13, weight: .semibold)
    static let tmblrButton     = Font.system(size: 16, weight: .bold)
    static let tmblrTab        = Font.system(size: 10, weight: .semibold)
    static let tmblrReblogSrc  = Font.system(size: 14, weight: .bold)
}

// 16pt body at 1.5 → lineSpacing ≈ 16 * 0.5 ≈ 8pt
extension View {
    func tmblrBodyText() -> some View { self.font(.tmblrBody).lineSpacing(8) }
}
```

## 3. Signature Components

### Dashboard Post Card (the core unit)

```swift
struct PostCard<Media: View>: View {
    let blogName: String
    let body: String
    let tags: [String]
    let notes: Int
    @ViewBuilder var media: () -> Media

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            // Header
            HStack(spacing: 10) {
                RoundedRectangle(cornerRadius: 6).fill(Color.tmblrSurface3).frame(width: 36, height: 36)
                Text(blogName).font(.tmblrBlogName).foregroundStyle(.white)
                Spacer()
                FollowButton()
                Image(systemName: "ellipsis").foregroundStyle(Color.tmblrTextSecondary)
            }
            .padding(12)

            media()   // full-bleed inside the card, no letterboxing

            if !body.isEmpty {
                Text(body).tmblrBodyText().foregroundStyle(.white).padding(12)
            }

            // Tag bar
            TagBar(tags: tags).padding(.horizontal, 12).padding(.bottom, 8)

            // Footer
            HStack {
                Text("\(notes.formatted()) notes")
                    .font(.tmblrNotes).foregroundStyle(Color.tmblrTextSecondary)
                Spacer()
                PostAction(symbol: "bubble.left", tint: .tmblrTextSecondary)
                ReblogButton()
                LikeButton(notes: notes)
                PostAction(symbol: "square.and.arrow.up", tint: .tmblrTextSecondary)
            }
            .padding(.horizontal, 12).padding(.bottom, 12)
        }
        .background(Color.tmblrSurface2)
        .clipShape(RoundedRectangle(cornerRadius: 8))
        .shadow(color: .black.opacity(0.3), radius: 4, y: 1)
        .padding(.horizontal, 8)
        .padding(.bottom, 8)
    }
}

struct PostAction: View {
    let symbol: String; let tint: Color
    var body: some View {
        Image(systemName: symbol).font(.system(size: 22)).foregroundStyle(tint)
            .frame(minWidth: 44, minHeight: 44)
    }
}
```

### Like Button with Heart-Burst (the signature)

```swift
struct LikeButton: View {
    let notes: Int
    @State private var liked = false
    @State private var burst = false

    var body: some View {
        ZStack {
            // Confetti hearts
            if burst {
                ForEach(0..<8, id: \.self) { i in
                    Image(systemName: "heart.fill")
                        .font(.system(size: 10))
                        .foregroundStyle(Color.tmblrPinkRed)
                        .offset(burstOffset(i))
                        .opacity(burst ? 0 : 1)
                        .scaleEffect(burst ? 0.3 : 1)
                }
            }
            Button {
                liked.toggle()
                if liked {
                    burst = false
                    withAnimation(.easeOut(duration: 0.6)) { burst = true }
                    DispatchQueue.main.asyncAfter(deadline: .now() + 0.6) { burst = false }
                }
            } label: {
                Image(systemName: liked ? "heart.fill" : "heart")
                    .font(.system(size: 22))
                    .foregroundStyle(liked ? Color.tmblrPinkRed : Color.tmblrTextSecondary)
            }
            .frame(minWidth: 44, minHeight: 44)
            .sensoryFeedback(.impact(weight: .light), trigger: liked)
        }
    }

    private func burstOffset(_ i: Int) -> CGSize {
        let angle = Double(i) / 8 * 2 * .pi
        let r: CGFloat = burst ? 26 : 0
        return CGSize(width: cos(angle) * r, height: sin(angle) * r - (burst ? 8 : 0))
    }
}
```

### Reblog Button

```swift
struct ReblogButton: View {
    @State private var reblogged = false
    @State private var scale: CGFloat = 1
    var body: some View {
        Button {
            reblogged.toggle()
            withAnimation(.spring(response: 0.25, dampingFraction: 0.5)) { scale = 1.15 }
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.12) {
                withAnimation(.spring()) { scale = 1 }
            }
        } label: {
            Image(systemName: "arrow.2.squarepath")
                .font(.system(size: 22)).scaleEffect(scale)
                .foregroundStyle(reblogged ? Color.tmblrGreen : Color.tmblrTextSecondary)
        }
        .frame(minWidth: 44, minHeight: 44)
        .sensoryFeedback(.success, trigger: reblogged)
    }
}
```

### Reblog Chain (the signature structure)

```swift
struct ReblogSegment: Identifiable {
    let id = UUID()
    let sourceBlog: String
    let comment: String
    let depth: Int          // 0 = original (deepest indent), higher = newer
}

struct ReblogChain: View {
    let segments: [ReblogSegment]   // ordered original → newest
    let maxDepth: Int

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            ForEach(segments) { seg in
                let indent = CGFloat(maxDepth - seg.depth) * 12
                HStack(alignment: .top, spacing: 0) {
                    if indent > 0 {
                        Rectangle().fill(Color.tmblrDivider).frame(width: 2)
                        Spacer().frame(width: 12)
                    }
                    VStack(alignment: .leading, spacing: 4) {
                        Text(seg.sourceBlog)
                            .font(.tmblrReblogSrc).foregroundStyle(Color.tmblrBlue)
                        Text(seg.comment)
                            .font(.tmblrReblog).foregroundStyle(.white)
                            .lineSpacing(6)
                    }
                }
                .padding(.leading, indent)
            }
        }
        .padding(12)
    }
}
```

### Buttons (Follow / Primary / FAB)

```swift
struct FollowButton: View {
    @State private var following = false
    var body: some View {
        Button { following.toggle() } label: {
            Text(following ? "Following" : "Follow")
                .font(.tmblrButton)
                .foregroundStyle(following ? Color.tmblrCanvas : Color.tmblrGreen)
                .padding(.vertical, 8).padding(.horizontal, 18)
                .background(RoundedRectangle(cornerRadius: 6).fill(following ? Color.tmblrGreen : .clear))
                .overlay(RoundedRectangle(cornerRadius: 6).strokeBorder(Color.tmblrGreen, lineWidth: following ? 0 : 1.5))
        }
    }
}

struct TmblrPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.tmblrButton).foregroundStyle(Color.tmblrCanvas)
                .padding(.vertical, 12).padding(.horizontal, 24)
                .background(RoundedRectangle(cornerRadius: 6).fill(Color.tmblrBlue))
        }
    }
}

struct NewPostFAB: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: "plus")
                .font(.system(size: 24, weight: .bold))
                .foregroundStyle(Color.tmblrCanvas)
                .frame(width: 52, height: 52)
                .background(Circle().fill(Color.tmblrBlue))
                .shadow(color: Color.tmblrBlue.opacity(0.35), radius: 18, y: 6)
        }
        .sensoryFeedback(.impact(weight: .soft), trigger: UUID())
    }
}
```

### Tag Bar

```swift
struct TagBar: View {
    let tags: [String]
    var body: some View {
        // Simple wrap; use a Flow layout (iOS 16+) or a custom wrapping HStack
        FlowLayout(spacing: 8) {
            ForEach(tags, id: \.self) { tag in
                Text("#\(tag)")
                    .font(.tmblrTag)
                    .foregroundStyle(Color.tmblrTextSecondary)
            }
        }
    }
}
```

## 4. Distinctive System — Heart-Burst & Reblog Chain

The two souls of Tumblr are coded above: `LikeButton` scatters 8 `heart.fill` particles on randomized radial vectors over 0.6s (double-tap on media should call the same burst centered on the image), and `ReblogChain` renders progressively-indented attributed segments with a 2pt `#36465D` left rule, blue source names, and the newest comment unindented at the bottom.

## 5. Tab Bar

Tumblr's tab bar is deep-navy opaque with a raised blue center New-post button — a plain `TabView` can't host a center FAB, so overlay a custom bar.

```swift
struct RootView: View {
    @State private var tab = 0
    var body: some View {
        ZStack(alignment: .bottom) {
            Group {
                switch tab {
                case 0: DashboardView()
                case 1: SearchView()
                case 3: ActivityView()
                default: AccountView()
                }
            }
            HStack {
                tabButton(0, "house.fill", "Home")
                tabButton(1, "magnifyingglass", "Search")
                NewPostFAB { /* post-type picker */ }.offset(y: -10)
                tabButton(3, "bell.fill", "Activity")
                tabButton(4, "person.crop.square", "Account")
            }
            .padding(.horizontal, 12).padding(.top, 8)
            .background(Color.tmblrSurface1)
            .overlay(Rectangle().fill(Color.tmblrDivider).frame(height: 0.5), alignment: .top)
        }
    }
    func tabButton(_ i: Int, _ sys: String, _ label: String) -> some View {
        Button { tab = i } label: {
            VStack(spacing: 3) {
                Image(systemName: sys).font(.system(size: 24))
                Text(label).font(.tmblrTab)
            }
            .foregroundStyle(tab == i ? Color.tmblrBlue : Color.tmblrTextSecondary)
            .frame(maxWidth: .infinity)
        }
    }
}
```

## 6. Motion

```swift
// Heart-burst: 8 hearts scatter on radial vectors, fade+shrink over 0.6s + light haptic (see LikeButton)
// Reblog: tint #00CF35 + 1.0 → 1.15 → 1.0 bounce + success haptic (see ReblogButton)

// Double-tap media: same burst centered on the image
.onTapGesture(count: 2) { triggerCenteredBurst() }

// FAB press: scale 0.94, #0090CC, soft haptic; post-type picker via .sheet rising 0.3s

// Tag press: flash to #00B8FF then push the tag feed
withAnimation(.easeOut(duration: 0.2)) { tagHighlighted = true }
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Reply | `bubble.left` | 22pt |
| Reblog | `arrow.2.squarepath` | 22pt |
| Like | `heart` / `heart.fill` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| More | `ellipsis` | 22pt |
| New post (center FAB) | `plus` | 24pt |
| Home (tab) | `house.fill` | 26pt |
| Search (tab) | `magnifyingglass` | 26pt |
| Activity (tab) | `bell.fill` | 26pt |
| Account (tab) | `person.crop.square` | 26pt |
| Messaging | `paperplane.fill` | 22pt |
| Mature label | `exclamationmark.triangle.fill` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` prefer iOS 17; fall back to `UIImpactFeedbackGenerator`). A `Flow` layout for the tag bar needs a custom `Layout` (iOS 16+) or a wrapping `HStack` fallback.
- Support Dynamic Type generously on post body, blog names, and quote posts — Tumblr text posts are long-form; pin only tab labels and reblog-source minimums
- VoiceOver: announce a post as "<blog name>, <body or 'photo/GIF post'>, <N> notes"; expose the like state ("Liked") and **do not** read the burst particles; for the reblog chain announce each segment as "<source blog> reblogged: <comment>" with depth context
- The heart-burst is decorative — mark particles `.accessibilityHidden(true)`
- Contrast: `#8A9AAE` secondary on `#001935` passes WCAG AA at 13pt+; the bright accents (blue/green/pink) on navy are high-contrast — keep dark navy text on the bright-blue primary button
- Respect Reduce Motion: replace the heart-burst with a simple fill cross-fade, and the reblog bounce with an instant tint change
- A light theme exists — gate it behind a user setting; the default and brand-defining experience is the deep navy
