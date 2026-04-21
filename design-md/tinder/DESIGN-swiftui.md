# Tinder (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Tinder's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the signature swipe-card stack, stamp overlays, and the match screen.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas (Light)
    static let tdrCanvas        = Color.white                                    // #FFFFFF
    static let tdrSurfaceMuted  = Color(red: 0.961, green: 0.961, blue: 0.961)  // #F5F5F5
    static let tdrSurfaceTint   = Color(red: 0.980, green: 0.980, blue: 0.980)  // #FAFAFA
    static let tdrDivider       = Color(red: 0.898, green: 0.898, blue: 0.898)  // #E5E5E5

    // MARK: - Text
    static let tdrTextPrimary   = Color(red: 0.259, green: 0.259, blue: 0.259)  // #424242
    static let tdrTextSecondary = Color(red: 0.451, green: 0.451, blue: 0.451)  // #737373
    static let tdrTextTertiary  = Color(red: 0.620, green: 0.620, blue: 0.620)  // #9E9E9E

    // MARK: - Brand
    static let tdrPink          = Color(red: 0.992, green: 0.149, blue: 0.478)  // #FD267A
    static let tdrOrange        = Color(red: 1.000, green: 0.376, blue: 0.212)  // #FF6036

    // MARK: - Action Colors (the 5 verbs)
    static let tdrNopeRed       = Color(red: 1.000, green: 0.267, blue: 0.345)  // #FF4458
    static let tdrSuperLikeBlue = Color(red: 0.365, green: 0.553, blue: 0.945)  // #5D8DF1
    static let tdrBoostPurple   = Color(red: 0.663, green: 0.322, blue: 1.000)  // #A952FF
    static let tdrRewindGold    = Color(red: 1.000, green: 0.741, blue: 0.231)  // #FFBD3B
    static let tdrLikeStampGreen = Color(red: 0.000, green: 0.839, blue: 0.561) // #00D68F

    // MARK: - Semantic
    static let tdrVerifiedBlue  = Color(red: 0.161, green: 0.690, blue: 1.000)  // #29B0FF
    static let tdrMatchGlow     = Color(red: 0.000, green: 0.839, blue: 0.561)  // #00D68F

    // MARK: - Dark
    static let tdrDarkCanvas    = Color(red: 0.071, green: 0.071, blue: 0.071)  // #121212
    static let tdrDarkSurface1  = Color(red: 0.114, green: 0.114, blue: 0.114)  // #1D1D1D
    static let tdrDarkSurface2  = Color(red: 0.165, green: 0.165, blue: 0.165)  // #2A2A2A
}

// Brand Gradient helper
extension LinearGradient {
    static let tdrBrand = LinearGradient(
        colors: [.tdrPink, .tdrOrange],
        startPoint: .leading, endPoint: .trailing
    )
}
```

## 2. Typography

Tinder Sans is proprietary (licensed from the brand team). Bundle or fall back to SF Pro — its humanist geometry is the closest free substitute.

```swift
extension Font {
    // Display (match screen only)
    static let tdrMatchHero     = Font.custom("TinderSans-DisplayBlackItalic", size: 48)

    // Titles
    static let tdrScreenTitle   = Font.custom("TinderSans-Bold",      size: 28).weight(.bold)
    static let tdrProfileName   = Font.custom("TinderSans-Bold",      size: 28).weight(.bold)
    static let tdrProfileAge    = Font.custom("TinderSans-Regular",   size: 24).weight(.regular)
    static let tdrSection       = Font.custom("TinderSans-Bold",      size: 20).weight(.bold)

    // Buttons
    static let tdrButton        = Font.custom("TinderSans-Bold",      size: 16).weight(.bold)
    static let tdrButtonSmall   = Font.custom("TinderSans-Medium",    size: 15).weight(.medium)

    // Body
    static let tdrBody          = Font.custom("TinderSans-Regular",   size: 15).weight(.regular)
    static let tdrCardMeta      = Font.custom("TinderSans-Regular",   size: 14).weight(.regular)
    static let tdrChat          = Font.custom("TinderSans-Regular",   size: 15).weight(.regular)
    static let tdrChatTimestamp = Font.custom("TinderSans-Regular",   size: 11).weight(.regular)

    // Stamps
    static let tdrStamp         = Font.custom("TinderSans-BlackItalic", size: 36)
    static let tdrSuperStamp    = Font.custom("TinderSans-BlackItalic", size: 32)
}
```

## 3. Signature Components

### Swipe Card

```swift
struct TinderSwipeCard: View {
    let name: String
    let age: Int
    let distance: String
    let occupation: String
    let photoURLs: [URL]
    var onSwipe: (SwipeDirection) -> Void = { _ in }

    @State private var offset = CGSize.zero
    @State private var currentPhoto = 0

    var rotation: Double { Double(offset.width) / 12 }  // -15° to 15° range in practice
    var likeOpacity: Double   { min(1.0, max(0, Double(offset.width) / 160)) }
    var nopeOpacity: Double   { min(1.0, max(0, Double(-offset.width) / 160)) }
    var superOpacity: Double  { min(1.0, max(0, Double(-offset.height) / 160)) }

    var body: some View {
        ZStack(alignment: .bottom) {
            // Photo
            AsyncImage(url: photoURLs[currentPhoto]) { img in
                img.resizable().scaledToFill()
            } placeholder: {
                Color.tdrSurfaceMuted
            }

            // Bottom gradient for legibility
            LinearGradient(
                colors: [.clear, Color.black.opacity(0.7)],
                startPoint: UnitPoint(x: 0.5, y: 0.5),
                endPoint: .bottom
            )

            VStack(alignment: .leading, spacing: 6) {
                HStack(alignment: .firstTextBaseline, spacing: 6) {
                    Text(name).font(.tdrProfileName).foregroundStyle(.white)
                    Text(", ").font(.tdrProfileAge).foregroundStyle(.white)
                    Text("\(age)").font(.tdrProfileAge).foregroundStyle(.white)
                }
                Text("\(distance) · \(occupation)")
                    .font(.tdrCardMeta).foregroundStyle(.white.opacity(0.9))
            }
            .frame(maxWidth: .infinity, alignment: .leading)
            .padding(20)

            // Photo paginator
            VStack {
                HStack(spacing: 3) {
                    ForEach(0..<photoURLs.count, id: \.self) { idx in
                        RoundedRectangle(cornerRadius: 1.5)
                            .fill(idx == currentPhoto
                                ? Color.white
                                : Color.white.opacity(0.4))
                            .frame(height: 3)
                    }
                }
                .padding(.horizontal, 16).padding(.top, 8)
                Spacer()
            }

            // Stamps
            TinderStamp(text: "LIKE", color: .tdrLikeStampGreen, rotation: -15)
                .opacity(likeOpacity)
                .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                .padding(32)
            TinderStamp(text: "NOPE", color: .tdrNopeRed, rotation: 15)
                .opacity(nopeOpacity)
                .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topTrailing)
                .padding(32)
            TinderStamp(text: "SUPER LIKE", color: .tdrSuperLikeBlue, rotation: 0)
                .opacity(superOpacity)
                .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .top)
                .padding(.top, 64)
        }
        .aspectRatio(3/4, contentMode: .fit)
        .clipShape(RoundedRectangle(cornerRadius: 16))
        .shadow(color: .black.opacity(0.15), radius: 16, y: 4)
        .rotationEffect(.degrees(rotation))
        .offset(offset)
        .gesture(
            DragGesture()
                .onChanged { offset = $0.translation }
                .onEnded { val in
                    let threshold: CGFloat = 150
                    if val.translation.width > threshold {
                        onSwipe(.right); commitSwipe(x: 600, y: 0)
                    } else if val.translation.width < -threshold {
                        onSwipe(.left); commitSwipe(x: -600, y: 0)
                    } else if val.translation.height < -threshold {
                        onSwipe(.up); commitSwipe(x: 0, y: -800)
                    } else {
                        withAnimation(.spring(response: 0.4, dampingFraction: 0.7)) {
                            offset = .zero
                        }
                    }
                }
        )
        .onTapGesture { location in
            if location.x < 100 { currentPhoto = max(0, currentPhoto - 1) }
            else                 { currentPhoto = min(photoURLs.count - 1, currentPhoto + 1) }
        }
    }

    private func commitSwipe(x: CGFloat, y: CGFloat) {
        withAnimation(.easeOut(duration: 0.4)) { offset = CGSize(width: x, height: y) }
    }
}

enum SwipeDirection { case left, right, up }
```

### Swipe Stamp

```swift
struct TinderStamp: View {
    let text: String
    let color: Color
    let rotation: Double

    var body: some View {
        Text(text)
            .font(.tdrStamp)
            .foregroundStyle(color)
            .padding(.horizontal, 12).padding(.vertical, 6)
            .overlay(
                RoundedRectangle(cornerRadius: 6)
                    .strokeBorder(color, lineWidth: 4)
            )
            .rotationEffect(.degrees(rotation))
    }
}
```

### Five Action Buttons Row

```swift
struct TinderActionBar: View {
    let onRewind: () -> Void
    let onNope:   () -> Void
    let onSuper:  () -> Void
    let onLike:   () -> Void
    let onBoost:  () -> Void

    var body: some View {
        HStack(spacing: 12) {
            TinderActionButton(size: 48, color: .tdrRewindGold,     symbol: "arrow.counterclockwise", action: onRewind)
            TinderActionButton(size: 56, color: .tdrNopeRed,        symbol: "xmark",                  action: onNope)
            TinderActionButton(size: 40, color: .tdrSuperLikeBlue,  symbol: "star.fill",              action: onSuper)
            TinderActionButton(size: 56, color: .tdrLikeStampGreen, symbol: "heart.fill",             action: onLike)
            TinderActionButton(size: 48, color: .tdrBoostPurple,    symbol: "bolt.fill",              action: onBoost)
        }
    }
}

struct TinderActionButton: View {
    let size: CGFloat
    let color: Color
    let symbol: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Image(systemName: symbol)
                .font(.system(size: size * 0.42, weight: .bold))
                .foregroundStyle(color)
                .frame(width: size, height: size)
                .background(
                    Circle().fill(Color.white)
                        .overlay(Circle().strokeBorder(color, lineWidth: 2))
                        .shadow(color: .black.opacity(0.1), radius: 8, y: 2)
                )
        }
        .scaleEffect(pressed ? 0.92 : 1)
        .animation(.spring(response: 0.25, dampingFraction: 0.5), value: pressed)
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in pressed = true }
                .onEnded   { _ in pressed = false }
        )
    }
}
```

### Primary Brand CTA

```swift
struct TinderBrandPillButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.tdrButton)
                .foregroundStyle(.white)
                .tracking(0.2)
                .padding(.vertical, 14)
                .padding(.horizontal, 32)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(Capsule().fill(LinearGradient.tdrBrand))
                .shadow(color: Color.tdrPink.opacity(0.3), radius: 16, y: 4)
        }
        .buttonStyle(.plain)
    }
}
```

### Match Screen

```swift
struct TinderMatchScreen: View {
    let yourPhotoURL: URL
    let theirPhotoURL: URL
    let theirName: String

    var body: some View {
        ZStack {
            LinearGradient.tdrBrand.ignoresSafeArea()

            VStack(spacing: 24) {
                Text("It's a Match!")
                    .font(.tdrMatchHero)
                    .foregroundStyle(.white)

                Text("You and \(theirName) liked each other")
                    .font(.tdrBody)
                    .foregroundStyle(.white.opacity(0.8))

                HStack(spacing: 20) {
                    MatchAvatar(url: yourPhotoURL)
                    MatchAvatar(url: theirPhotoURL)
                }

                VStack(spacing: 12) {
                    Button {
                        // send message
                    } label: {
                        Text("Send Message")
                            .font(.tdrButton)
                            .foregroundStyle(LinearGradient.tdrBrand)
                            .padding(.vertical, 14).padding(.horizontal, 32)
                            .frame(maxWidth: .infinity)
                            .background(Capsule().fill(Color.white))
                    }
                    Button {
                        // keep playing
                    } label: {
                        Text("Keep Playing")
                            .font(.tdrButton)
                            .foregroundStyle(.white)
                            .padding(.vertical, 14).padding(.horizontal, 32)
                            .frame(maxWidth: .infinity)
                            .overlay(Capsule().strokeBorder(.white, lineWidth: 2))
                    }
                }
                .padding(.horizontal, 24)
                .padding(.top, 12)
            }
        }
        .sensoryFeedback(.success, trigger: true)
    }
}

struct MatchAvatar: View {
    let url: URL
    var body: some View {
        AsyncImage(url: url) { img in
            img.resizable().scaledToFill()
        } placeholder: {
            Circle().fill(.white.opacity(0.3))
        }
        .frame(width: 120, height: 120)
        .clipShape(Circle())
        .overlay(Circle().strokeBorder(.white, lineWidth: 3))
    }
}
```

### Chat Bubble

```swift
struct TinderChatBubble: View {
    enum Sender { case me, them }
    let text: String
    let sender: Sender

    var body: some View {
        HStack {
            if sender == .me { Spacer(minLength: 80) }
            Text(text)
                .font(.tdrChat)
                .foregroundStyle(sender == .me ? .tdrTextPrimary : .white)
                .padding(.horizontal, 14).padding(.vertical, 10)
                .background(
                    Group {
                        if sender == .them {
                            LinearGradient.tdrBrand
                        } else {
                            Color.tdrSurfaceMuted
                        }
                    }
                )
                .clipShape(RoundedRectangle(cornerRadius: 20))
            if sender == .them { Spacer(minLength: 80) }
        }
        .padding(.horizontal, 16)
    }
}
```

## 4. Tab Bar (icon-only)

```swift
struct TinderRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(red: 0.898, green: 0.898, blue: 0.898, alpha: 1)
        // Hide labels
        appearance.stackedLayoutAppearance.normal.titlePositionAdjustment = UIOffset(horizontal: 0, vertical: 1000)
        appearance.stackedLayoutAppearance.selected.titlePositionAdjustment = UIOffset(horizontal: 0, vertical: 1000)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            SwipeView().tabItem    { Image(systemName: "flame.fill") }
            TopPicksView().tabItem { Image(systemName: "star.fill") }
            ChatsView().tabItem    { Image(systemName: "bubble.left.fill") }
            ProfileView().tabItem  { Image(systemName: "person.fill") }
        }
        .tint(Color.tdrPink) // Active = pink (gradient fill requires custom tab icons via AnyView)
    }
}
```

## 5. Motion Specs

```swift
// Swipe commit (off-screen)
withAnimation(.easeOut(duration: 0.4)) { offset = CGSize(width: 600, height: 0) }

// Snap back
withAnimation(.spring(response: 0.4, dampingFraction: 0.7)) { offset = .zero }

// Next card rise
withAnimation(.spring(response: 0.25, dampingFraction: 0.8)) { nextScale = 1.0 }

// Match entrance — 300ms gradient fade + confetti burst 800ms
withAnimation(.easeIn(duration: 0.3)) { matchOpacity = 1.0 }

// New match glow on avatar
@State private var pulseScale = 1.0
.onAppear {
    withAnimation(.easeInOut(duration: 1.5).repeatForever(autoreverses: true)) {
        pulseScale = 1.2
    }
}

// Haptic pattern for Boost activation: heavy + medium pulse
.sensoryFeedback(.impact(weight: .heavy), trigger: boostActivated)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Flame (swipe tab) | `flame.fill` | 28pt |
| Star (Top Picks / Super Like) | `star.fill` | 28pt / 20pt |
| Chat bubble (tab) | `bubble.left.fill` | 28pt |
| Profile (tab) | `person.fill` | 28pt |
| Rewind (action) | `arrow.counterclockwise` | 20pt |
| Nope (action) | `xmark` | 24pt |
| Like (action) | `heart.fill` | 24pt |
| Boost (action) | `bolt.fill` | 20pt |
| Verified | `checkmark.seal.fill` | 18pt |
| Info (card ⓘ) | `info.circle.fill` | 24pt |
| Send | `arrow.up` | 18pt |
| GIF picker | `photo.on.rectangle.angled` | 22pt |
| Settings | `gearshape.fill` | 24pt |

## 7. Dynamic Color Tinting

To render the icon tabs and active action buttons with the brand gradient, use a `ShapeStyle` with `AngularGradient` or wrap an SF Symbol with `foregroundStyle(LinearGradient.tdrBrand)`.

```swift
Image(systemName: "flame.fill")
    .font(.system(size: 28))
    .foregroundStyle(LinearGradient.tdrBrand)
```

Keep the icon on the inactive state flat gray (`#9E9E9E`).

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (iOS 17 for `.sensoryFeedback`)
- **VoiceOver**:
  - Swipe card: `accessibilityLabel` = "[Name], [age], [distance], [occupation]. Double-tap for profile. Swipe right to like."
  - Action buttons: clear labels — "Rewind", "Pass", "Super Like", "Like", "Boost"
  - Stamps: announce "Liked" / "Passed" / "Super Liked" on commit
- **Dynamic Type**: name / age / bio / chat scale freely; cap stamp type at 44pt; lock action-button glyph size at 24pt
- **Reduce Motion**: skip confetti particle burst, skip card rotation during drag, replace match-screen entrance with a simple fade
- **Contrast**: text on gradient CTAs uses pure white — always check the lighter end of the gradient (`#FF6036`) for WCAG AA against white; it passes at 14pt+ weight 700
- **Haptics**:
  - `.impact(.medium)` on swipe commit and action button tap
  - `.success` on match reveal
  - `.impact(.heavy)` on Boost activation
  - `.selection` on photo paginator tap
- **Alternate swipe UX**: Provide discrete tap targets for Like / Nope via the action buttons so users who can't swipe still have parity
