# Match (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Match's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let matchCanvas        = Color.white                                   // #FFFFFF
    static let matchSurfaceGray   = Color(red: 0.965, green: 0.957, blue: 0.957) // #F6F4F4
    static let matchSurfacePressed = Color(red: 0.925, green: 0.910, blue: 0.910) // #ECE8E8
    static let matchDivider       = Color(red: 0.906, green: 0.886, blue: 0.886) // #E7E2E2

    // MARK: - Canvas & Surfaces (Dark)
    static let matchDarkCanvas    = Color(red: 0.078, green: 0.078, blue: 0.078) // #141414
    static let matchDarkSurface1  = Color(red: 0.118, green: 0.118, blue: 0.118) // #1E1E1E
    static let matchDarkSurface2  = Color(red: 0.165, green: 0.141, blue: 0.141) // #2A2424
    static let matchDarkDivider   = Color(red: 0.196, green: 0.173, blue: 0.173) // #322C2C

    // MARK: - Text
    static let matchTextPrimary   = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let matchTextSecondary = Color(red: 0.431, green: 0.400, blue: 0.400) // #6E6666
    static let matchTextTertiary  = Color(red: 0.639, green: 0.608, blue: 0.608) // #A39B9B
    static let matchDarkTextPrimary   = Color(red: 0.949, green: 0.929, blue: 0.929) // #F2EDED
    static let matchDarkTextSecondary = Color(red: 0.659, green: 0.620, blue: 0.620) // #A89E9E

    // MARK: - Brand
    static let matchRed        = Color(red: 0.914, green: 0.141, blue: 0.204) // #E92434
    static let matchRedBright  = Color(red: 0.941, green: 0.125, blue: 0.243) // #F0203E
    static let matchRedPressed = Color(red: 0.784, green: 0.106, blue: 0.173) // #C81B2C
    static let matchInk        = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A

    // MARK: - Functional Accents
    static let matchActiveGreen = Color(red: 0.180, green: 0.800, blue: 0.443) // #2ECC71
    static let matchSuperBlue   = Color(red: 0.122, green: 0.714, blue: 1.000) // #1FB6FF
    static let matchGold        = Color(red: 0.839, green: 0.655, blue: 0.357) // #D6A75B
    static let matchBoost       = Color(red: 0.702, green: 0.420, blue: 0.847) // #B36BD8
    static let matchError       = Color(red: 0.949, green: 0.329, blue: 0.357) // #F2545B
}

// Bottom-of-photo scrim
extension LinearGradient {
    static let matchPhotoScrim = LinearGradient(
        colors: [.clear, Color(red: 0.078, green: 0.078, blue: 0.078).opacity(0.95)],
        startPoint: .center, endPoint: .bottom)
    static let matchCelebration = LinearGradient(
        colors: [Color(red: 0.165, green: 0.078, blue: 0.094), Color(red: 0.102, green: 0.102, blue: 0.102)],
        startPoint: .top, endPoint: .bottom)
    static let matchPremium = LinearGradient(
        colors: [Color(red: 0.941, green: 0.125, blue: 0.243), Color(red: 0.722, green: 0.114, blue: 0.690)],
        startPoint: .leading, endPoint: .trailing)
}
```

## 2. Typography

Match uses a custom geometric grotesque (Proxima/Poppins family). Bundle `Poppins` (SIL OFL) via `Info.plist` / `UIAppFonts` as the closest free substitute.

```swift
extension Font {
    static func match(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .bold, .heavy, .black: return "Poppins-Bold"
            case .semibold:             return "Poppins-SemiBold"
            case .medium:               return "Poppins-Medium"
            default:                    return "Poppins-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let matchWordmark    = Font.custom("Poppins-ExtraBold", size: 28)
    static let matchProfileName = Font.custom("Poppins-Bold",      size: 26)
    static let matchProfileAge  = Font.custom("Poppins-Medium",    size: 22)
    static let matchMatchTitle  = Font.custom("Poppins-ExtraBold", size: 26)
    static let matchScreenTitle = Font.custom("Poppins-Bold",      size: 22)
    static let matchSection     = Font.custom("Poppins-SemiBold",  size: 18)
    static let matchBody        = Font.custom("Poppins-Regular",   size: 16)
    static let matchListTitle   = Font.custom("Poppins-SemiBold",  size: 15)
    static let matchButton      = Font.custom("Poppins-Bold",      size: 16)
    static let matchMeta        = Font.custom("Poppins-Regular",   size: 14)
    static let matchSubtitle    = Font.custom("Poppins-Medium",    size: 14)
    static let matchChip        = Font.custom("Poppins-SemiBold",  size: 12)
    static let matchTab         = Font.custom("Poppins-SemiBold",  size: 10)
    static let matchTimestamp   = Font.custom("Poppins-Regular",   size: 12)
}
```

## 3. Signature Components

### Full-Bleed Profile Card

```swift
struct ProfileCard: View {
    let imageName: String
    let name: String
    let age: Int
    let job: String
    let distance: String
    let interests: [String]
    let photoCount: Int
    let activeIndex: Int
    let isVerified: Bool

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            Image(imageName)
                .resizable()
                .aspectRatio(contentMode: .fill)
                .clipShape(RoundedRectangle(cornerRadius: 20, style: .continuous))

            LinearGradient.matchPhotoScrim

            // Story progress bars
            VStack {
                HStack(spacing: 4) {
                    ForEach(0..<photoCount, id: \.self) { i in
                        Capsule()
                            .fill(i == activeIndex ? Color.white : Color.white.opacity(0.35))
                            .frame(height: 3)
                    }
                }
                .padding(.horizontal, 12)
                .padding(.top, 12)
                Spacer()
            }

            VStack(alignment: .leading, spacing: 6) {
                HStack(alignment: .firstTextBaseline, spacing: 8) {
                    Text(name).font(.matchProfileName).foregroundStyle(.white)
                    Text("\(age)").font(.matchProfileAge).foregroundStyle(.white)
                    if isVerified {
                        Image(systemName: "checkmark.seal.fill")
                            .font(.system(size: 17)).foregroundStyle(Color.matchSuperBlue)
                    }
                }
                Text(job).font(.matchSubtitle).foregroundStyle(.white.opacity(0.9))
                HStack(spacing: 5) {
                    Circle().fill(Color.matchActiveGreen).frame(width: 7, height: 7)
                    Text(distance).font(.matchMeta).foregroundStyle(.white.opacity(0.75))
                }
                ChipFlow(interests)
                    .padding(.top, 4)
            }
            .padding(.horizontal, 18)
            .padding(.bottom, 92) // clears the action dock
        }
        .shadow(color: .black.opacity(0.55), radius: 28, y: 12)
    }
}

struct ChipFlow: View {
    let items: [String]
    init(_ items: [String]) { self.items = items }
    var body: some View {
        HStack(spacing: 6) {
            ForEach(items.prefix(4), id: \.self) { tag in
                Text(tag)
                    .font(.matchChip)
                    .foregroundStyle(.white)
                    .padding(.horizontal, 11).padding(.vertical, 5)
                    .background(.white.opacity(0.16), in: Capsule())
                    .overlay(Capsule().strokeBorder(.white.opacity(0.28), lineWidth: 1))
            }
        }
    }
}
```

### Action Dock

```swift
struct ActionDock: View {
    var onRewind: () -> Void = {}
    var onPass:   () -> Void = {}
    var onSuper:  () -> Void = {}
    var onLike:   () -> Void = {}
    var onBoost:  () -> Void = {}

    var body: some View {
        HStack(spacing: 16) {
            DockButton(systemName: "arrow.uturn.backward", size: 44, tint: .matchGold, action: onRewind)
            DockButton(systemName: "xmark", size: 56, tint: .matchDarkTextSecondary, action: onPass)
            DockButton(systemName: "star.fill", size: 48, tint: .matchSuperBlue, action: onSuper)
            DockButton(systemName: "heart.fill", size: 56, tint: .white,
                       fill: .matchRedBright, bordered: false, action: onLike)
            DockButton(systemName: "bolt.fill", size: 44, tint: .matchBoost, action: onBoost)
        }
    }
}

struct DockButton: View {
    let systemName: String
    let size: CGFloat
    let tint: Color
    var fill: Color = .matchDarkSurface1
    var bordered: Bool = true
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button {
            UIImpactFeedbackGenerator(style: .soft).impactOccurred()
            action()
        } label: {
            Image(systemName: systemName)
                .font(.system(size: size * 0.42, weight: .bold))
                .foregroundStyle(tint)
                .frame(width: size, height: size)
                .background(Circle().fill(fill))
                .overlay(bordered ? Circle().strokeBorder(Color.matchDarkDivider, lineWidth: 1) : nil)
                .shadow(color: .black.opacity(0.5), radius: 16, y: 6)
        }
        .scaleEffect(pressed ? 0.92 : 1)
        .animation(.spring(response: 0.25, dampingFraction: 0.6), value: pressed)
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .onChanged { _ in pressed = true }
            .onEnded { _ in pressed = false })
    }
}
```

### "It's a Match!" Celebration

```swift
struct MatchCelebration: View {
    let myAvatar: String
    let theirAvatar: String
    let theirName: String
    var onMessage: () -> Void = {}
    var onKeepSwiping: () -> Void = {}

    var body: some View {
        ZStack {
            LinearGradient.matchCelebration.ignoresSafeArea()
            VStack(spacing: 16) {
                Text("It's a Match!")
                    .font(.matchMatchTitle)
                    .foregroundStyle(Color.matchRedBright)
                    .shadow(color: Color.matchRedBright.opacity(0.5), radius: 24)
                Text("You and \(theirName) liked each other")
                    .font(.match(13)).foregroundStyle(Color.matchDarkTextSecondary)

                HStack(spacing: -14) {
                    Image(myAvatar).resizable().scaledToFill()
                        .frame(width: 56, height: 56).clipShape(Circle())
                        .overlay(Circle().strokeBorder(Color.matchInk, lineWidth: 3))
                    Image(theirAvatar).resizable().scaledToFill()
                        .frame(width: 56, height: 56).clipShape(Circle())
                        .overlay(Circle().strokeBorder(Color.matchInk, lineWidth: 3))
                }
                .padding(.vertical, 8)

                Button(action: onMessage) {
                    Text("Send a Message")
                        .font(.matchButton).foregroundStyle(.white)
                        .frame(maxWidth: .infinity).padding(.vertical, 15)
                        .background(Color.matchRedBright, in: Capsule())
                }
                Button(action: onKeepSwiping) {
                    Text("Keep Swiping")
                        .font(.match(15, weight: .semibold))
                        .foregroundStyle(Color.matchDarkTextPrimary)
                        .frame(maxWidth: .infinity).padding(.vertical, 13)
                        .overlay(Capsule().strokeBorder(Color.matchDarkTextSecondary, lineWidth: 1.5))
                }
            }
            .padding(.horizontal, 40)
        }
        .onAppear { UINotificationFeedbackGenerator().notificationOccurred(.success) }
    }
}
```

### Likes You Tile

```swift
struct LikesYouTile: View {
    let imageName: String
    let unlocked: Bool

    var body: some View {
        ZStack {
            Image(imageName).resizable().scaledToFill()
                .frame(width: 96, height: 128)
                .blur(radius: unlocked ? 0 : 7)
                .scaleEffect(unlocked ? 1 : 1.2)
                .clipped()
            if unlocked {
                VStack { HStack {
                    Spacer()
                    Image(systemName: "heart.fill")
                        .font(.system(size: 12)).foregroundStyle(.white)
                        .frame(width: 22, height: 22)
                        .background(Color.matchRed, in: Circle())
                }; Spacer() }.padding(8)
            } else {
                VStack { Spacer()
                    Text("Liked you").font(.matchTimestamp).foregroundStyle(.white)
                        .padding(.horizontal, 8).padding(.vertical, 4)
                        .background(.black.opacity(0.5), in: Capsule())
                }.padding(.bottom, 10)
            }
        }
        .frame(width: 96, height: 128)
        .clipShape(RoundedRectangle(cornerRadius: 14, style: .continuous))
    }
}
```

### Primary Button

```swift
struct MatchPrimaryButton: View {
    let title: String
    var premium = false
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title).font(.matchButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).padding(.vertical, 15)
                .background {
                    if premium { LinearGradient.matchPremium }
                    else { Color.matchRedBright }
                }
                .clipShape(Capsule())
        }
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct MatchTabView: View {
    var body: some View {
        TabView {
            DiscoverView().tabItem { Label("Discover", systemImage: "square.stack.fill") }
            LikesView().tabItem { Label("Likes", systemImage: "heart.fill") }
            MessagesView().tabItem { Label("Messages", systemImage: "bubble.left.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.fill") }
        }
        .tint(.matchRedBright) // active = bright Match Red
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithDefaultBackground()
            a.backgroundColor = UIColor(white: 0.078, alpha: 0.94)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Swipe card — rotate + translate with drag, spring on commit
@State private var offset: CGSize = .zero
private var rotation: Angle { .degrees(Double(offset.width / 12)) }

cardView
  .offset(offset)
  .rotationEffect(rotation, anchor: .bottom)
  .gesture(DragGesture()
    .onChanged { offset = $0.translation }
    .onEnded { v in
        if abs(v.translation.width) > 110 {
            UIImpactFeedbackGenerator(style: .soft).impactOccurred()
            withAnimation(.spring(response: 0.3, dampingFraction: 0.78)) {
                offset.width = v.translation.width > 0 ? 700 : -700
            }
        } else {
            withAnimation(.spring(response: 0.35, dampingFraction: 0.7)) { offset = .zero }
        }
    })

// Like tap — heart pulse
withAnimation(.easeOut(duration: 0.28)) { heartScale = 1.25 }
// then back to 1.0

// It's a Match! — avatars spring from opposite edges
.transition(.asymmetric(
    insertion: .move(edge: .leading).combined(with: .opacity),
    removal: .opacity))
withAnimation(.spring(response: 0.4, dampingFraction: 0.7)) { showMatch = true }

// Boost glow — pulsing radial
.scaleEffect(boosting ? 1.04 : 1)
.animation(.easeInOut(duration: 1.6).repeatForever(autoreverses: true), value: boosting)

// Haptics
UIImpactFeedbackGenerator(style: .soft).impactOccurred()       // swipe commit, dock tap
UINotificationFeedbackGenerator().notificationOccurred(.success) // a match
UISelectionFeedbackGenerator().selectionChanged()              // story-bar navigation
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Discover (tab) | `square.stack` / `square.stack.fill` | 23pt |
| Likes (tab) | `heart` / `heart.fill` | 23pt |
| Messages (tab) | `bubble.left` / `bubble.left.fill` | 23pt |
| Profile (tab) | `person` / `person.fill` | 23pt |
| Like (dock) | `heart.fill` | 25pt |
| Pass (dock) | `xmark` | 24pt |
| Super Like (dock) | `star.fill` | 21pt |
| Rewind (dock) | `arrow.uturn.backward` | 19pt |
| Boost (dock) | `bolt.fill` | 19pt |
| Verified badge | `checkmark.seal.fill` | 17pt |
| Filter / preferences | `slider.horizontal.3` | 24pt |
| Send message | `paperplane.fill` | 18pt |
| Unmatch / report | `flag` / `xmark.circle` | 20pt |
| Premium | `crown.fill` | 18pt |

## 7. Dark Mode

```swift
struct MatchTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.matchDarkCanvas : Color.matchCanvas)
            .foregroundStyle(scheme == .dark ? Color.matchDarkTextPrimary : Color.matchTextPrimary)
            .tint(scheme == .dark ? Color.matchRedBright : Color.matchRed)
    }
}

extension View { func matchTheme() -> some View { modifier(MatchTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` is iOS 17 — examples use `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator` for iOS 16 support)
- Bundle Poppins TTFs via `Info.plist` `UIAppFonts` — SIL OFL, free to redistribute (substitute for Match's custom face)
- Dynamic Type: scale profile name, body, section headers, message body; keep tab labels, interest chips, story-bar captions, timestamps, the distance line at FIXED sizes (layout-sensitive over photos)
- VoiceOver: announce the profile card as "{name}, {age}, {job}, {distance}, photo {i} of {n}"; the dock buttons get explicit labels ("Like", "Pass", "Super Like", "Rewind", "Boost"); expose left/right photo navigation as adjustable actions; the "It's a Match!" overlay announces "It's a match with {name}" on appear
- Locked Likes-You tiles: label "Someone liked you — subscribe to see" so the paywall is screen-reader clear
- Color contrast: on-photo text always sits on the gradient scrim and uses white with a subtle shadow; `#E92434` on white passes WCAG AA for the CTA at 16pt bold; the green presence dot is paired with the "Active now" text label (never color-only)
- Reduce Motion: replace the swipe rotation with a straight fade; the "It's a Match!" avatar spring becomes a cross-fade; disable the Boost pulse — keep the success haptic
- Hit targets: Like / Pass circles 56pt; Super Like / Rewind / Boost ≥ 44pt; story-bar tap zones span the full left/right thirds of the card
- Dark mode: warm near-black `#141414` (NOT pure black); Match Red brightens to `#F0203E` for the Like button and active tab so it holds vibrance over dark photography; dock circles add a 1pt `#322C2C` border as the dark-mode elevation cue
