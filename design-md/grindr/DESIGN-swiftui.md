# Grindr (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Grindr's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-only)
    static let grAndCanvas   = Color.black                                   // #000000
    static let grAndSurface1 = Color(red: 0.102, green: 0.102, blue: 0.102)  // #1A1A1A
    static let grAndSurface2 = Color(red: 0.133, green: 0.133, blue: 0.133)  // #222222
    static let grAndSurface3 = Color(red: 0.173, green: 0.173, blue: 0.173)  // #2C2C2C
    static let grAndDivider  = Color(red: 0.173, green: 0.173, blue: 0.173)  // #2C2C2C

    // MARK: - Text
    static let grAndTextPrimary   = Color.white                              // #FFFFFF
    static let grAndTextSecondary = Color(red: 0.6, green: 0.6, blue: 0.6)   // #999999
    static let grAndTextTertiary  = Color(red: 0.361, green: 0.361, blue: 0.361) // #5C5C5C

    // MARK: - Brand
    static let grAndYellow        = Color(red: 1.0, green: 0.871, blue: 0.0) // #FFDE00
    static let grAndYellowPressed = Color(red: 0.902, green: 0.780, blue: 0.0) // #E6C700
    static let grAndYellowTint    = Color(red: 1.0, green: 0.949, blue: 0.659) // #FFF2A8

    // MARK: - Semantic
    static let grAndOnline = Color(red: 0.298, green: 0.851, blue: 0.392)    // #4CD964
    static let grAndError  = Color(red: 1.0, green: 0.231, blue: 0.188)      // #FF3B30
    static let grAndInfo   = Color(red: 0.239, green: 0.545, blue: 1.0)      // #3D8BFF
}
```

## 2. Typography

Grindr ships a custom product grotesque. Bundle the TTFs via `Info.plist` (`UIAppFonts`), or use **Inter** (closest free grotesque). Fall back to `.system(design: .default)` if neither is registered.

```swift
extension Font {
    static let grAndProfileName = Font.custom("Inter-Bold",    size: 24).weight(.bold)
    static let grAndTitle       = Font.custom("Inter-Bold",    size: 22).weight(.bold)
    static let grAndSection     = Font.custom("Inter-Bold",    size: 18).weight(.bold)
    static let grAndTileName    = Font.custom("Inter-Bold",    size: 15).weight(.bold)
    static let grAndRowTitle    = Font.custom("Inter-Bold",    size: 16).weight(.bold)
    static let grAndBody        = Font.custom("Inter-Regular", size: 15).weight(.regular)
    static let grAndMessage     = Font.custom("Inter-Regular", size: 16).weight(.regular)
    static let grAndMeta        = Font.custom("Inter-Regular", size: 13).weight(.regular)
    static let grAndDistance    = Font.custom("Inter-Bold",    size: 12).weight(.bold)
    static let grAndLabelUpper  = Font.custom("Inter-Bold",    size: 11).weight(.bold)
    static let grAndButton      = Font.custom("Inter-Bold",    size: 16).weight(.bold)
    static let grAndTab         = Font.custom("Inter-Bold",    size: 10).weight(.bold)
}

extension Font {
    static func grAnd(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Cascade Grid Tile

```swift
struct GrandTile: View {
    let name: String
    let distance: String
    let photo: Image
    let isOnline: Bool

    var body: some View {
        GeometryReader { geo in
            ZStack(alignment: .bottomLeading) {
                photo
                    .resizable()
                    .aspectRatio(contentMode: .fill)
                    .frame(width: geo.size.width, height: geo.size.width)
                    .clipped()

                // Bottom scrim for overlay legibility
                LinearGradient(
                    colors: [.clear, Color.black.opacity(0.65)],
                    startPoint: .center, endPoint: .bottom
                )

                VStack(alignment: .leading, spacing: 2) {
                    Text(name)
                        .font(.grAndTileName)
                        .foregroundStyle(.white)
                        .lineLimit(1)
                    Text(distance)
                        .font(.grAndDistance)
                        .foregroundStyle(.white)
                        .tracking(0.2)
                }
                .padding(8)

                if isOnline {
                    Circle()
                        .fill(Color.grAndOnline)
                        .frame(width: 10, height: 10)
                        .overlay(Circle().strokeBorder(.black, lineWidth: 2))
                        .padding(8)
                        .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topTrailing)
                }
            }
        }
        .aspectRatio(1, contentMode: .fit)
        .contentShape(Rectangle())
    }
}
```

### Yellow Tap Button

```swift
struct GrandTapButton: View {
    @Binding var tapped: Bool
    var size: CGFloat = 56
    let action: () -> Void

    var body: some View {
        Button {
            action()
            tapped = true
        } label: {
            Group {
                if tapped {
                    Image(systemName: "checkmark")
                        .font(.system(size: size * 0.42, weight: .bold))
                        .foregroundStyle(.white)
                        .frame(width: size, height: size)
                        .overlay(Circle().strokeBorder(Color.grAndTextSecondary, lineWidth: 1))
                } else {
                    Image(systemName: "flame.fill")
                        .font(.system(size: size * 0.42, weight: .bold))
                        .foregroundStyle(.black) // intentional: black on yellow
                        .frame(width: size, height: size)
                        .background(Circle().fill(Color.grAndYellow))
                }
            }
        }
        .sensoryFeedback(.success, trigger: tapped)
        .buttonStyle(GrandPressableStyle(pressedScale: 0.92))
    }
}

struct GrandPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Primary Pill (Send / Chat)

```swift
struct GrandPillButton: View {
    let title: String
    var style: Style = .filled
    let action: () -> Void
    enum Style { case filled, outline }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.grAndButton)
                .tracking(0.2)
                .foregroundStyle(style == .filled ? .black : .white)
                .padding(.vertical, 12)
                .padding(.horizontal, style == .filled ? 28 : 24)
                .background(Capsule().fill(style == .filled ? Color.grAndYellow : .clear))
                .overlay(Capsule().strokeBorder(style == .outline ? Color.grAndTextSecondary : .clear, lineWidth: 1))
        }
        .buttonStyle(GrandPressableStyle())
    }
}
```

### Chat Bubble

```swift
struct GrandBubble: View {
    let text: String
    let outgoing: Bool

    var body: some View {
        Text(text)
            .font(.grAndMessage)
            .foregroundStyle(outgoing ? .black : .white)
            .padding(.vertical, 10)
            .padding(.horizontal, 14)
            .background(
                UnevenRoundedRectangle(
                    topLeadingRadius: 18,
                    bottomLeadingRadius: outgoing ? 18 : 4,
                    bottomTrailingRadius: outgoing ? 4 : 18,
                    topTrailingRadius: 18
                )
                .fill(outgoing ? Color.grAndYellow : Color.grAndSurface2)
            )
            .frame(maxWidth: 280, alignment: outgoing ? .trailing : .leading)
            .frame(maxWidth: .infinity, alignment: outgoing ? .trailing : .leading)
    }
}
```

### Message List Row

```swift
struct GrandMessageRow: View {
    let name: String
    let preview: String
    let time: String
    let avatar: Image
    let isOnline: Bool
    let unread: Bool

    var body: some View {
        HStack(spacing: 12) {
            ZStack(alignment: .topTrailing) {
                avatar.resizable().aspectRatio(contentMode: .fill)
                    .frame(width: 52, height: 52).clipShape(Circle())
                if isOnline {
                    Circle().fill(Color.grAndOnline)
                        .frame(width: 12, height: 12)
                        .overlay(Circle().strokeBorder(.black, lineWidth: 2))
                }
            }
            VStack(alignment: .leading, spacing: 3) {
                Text(name).font(.grAndRowTitle).foregroundStyle(.white).lineLimit(1)
                Text(preview).font(.grAndMeta)
                    .foregroundStyle(unread ? Color.white.opacity(0.85) : .grAndTextSecondary)
                    .lineLimit(1)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 6) {
                Text(time).font(.grAnd(12)).foregroundStyle(.grAndTextSecondary)
                if unread { Circle().fill(Color.grAndYellow).frame(width: 8, height: 8) }
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 72)
        .contentShape(Rectangle())
    }
}
```

## 4. Cascade Grid (lazy-load fade)

```swift
struct GrandCascade: View {
    let profiles: [Profile]
    private let cols = Array(repeating: GridItem(.flexible(), spacing: 2), count: 3)

    var body: some View {
        ScrollView {
            LazyVGrid(columns: cols, spacing: 2) {
                ForEach(profiles) { p in
                    GrandTile(name: p.name, distance: p.distance, photo: p.photo, isOnline: p.online)
                        .modifier(FadeInOnAppear())
                }
            }
        }
        .background(Color.grAndCanvas)
    }
}

struct FadeInOnAppear: ViewModifier {
    @State private var shown = false
    func body(content: Content) -> some View {
        content
            .opacity(shown ? 1 : 0)
            .scaleEffect(shown ? 1 : 1.02)
            .onAppear {
                withAnimation(.easeOut(duration: 0.18)) { shown = true }
            }
    }
}
```

## 5. Tab Bar

```swift
struct GrandRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor.black.withAlphaComponent(0.94)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            BrowseView().tabItem   { Label("Browse",   systemImage: "square.grid.3x3.fill") }
            TapsView().tabItem     { Label("Taps",     systemImage: "flame.fill") }
            MessagesView().tabItem { Label("Messages", systemImage: "bubble.left.fill") }
            ProfileView().tabItem  { Label("Profile",  systemImage: "person.fill") }
        }
        .tint(.grAndYellow) // active = Grindr Yellow
    }
}
```

## 6. Motion

```swift
// Tap send — success haptic + scale spring (see GrandTapButton)
.sensoryFeedback(.success, trigger: tapped)

// Cascade lazy-load — FadeInOnAppear modifier (0.18s opacity + 1.02→1.0 scale)

// Profile sheet present — matchedGeometryEffect on the tile photo → hero photo
@Namespace var ns
// tile:   .matchedGeometryEffect(id: p.id, in: ns)
// hero:   .matchedGeometryEffect(id: p.id, in: ns)
// present with .spring(response: 0.3, dampingFraction: 0.8)

// Message send — outgoing bubble scale-in
.transition(.scale(scale: 0.9).combined(with: .opacity))
.animation(.spring(response: 0.2), value: messages.count)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Tap action | `flame.fill` | 24pt |
| Tapped (sent) | `checkmark` | 24pt |
| Favorite | `star` / `star.fill` | 24pt |
| Block | `nosign` | 22pt |
| More | `ellipsis` | 22pt |
| Send arrow | `arrow.up.circle.fill` | 28pt |
| Photo / camera | `camera.fill` | 22pt |
| Filter | `slider.horizontal.3` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Settings | `gearshape.fill` | 22pt |
| Browse (tab) | `square.grid.3x3.fill` | 26pt |
| Taps (tab) | `flame.fill` | 26pt |
| Messages (tab) | `bubble.left.fill` | 26pt |
| Profile (tab) | `person.fill` | 26pt |
| Online dot | filled `Circle` (not an SF Symbol) | 10pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Support Dynamic Type on chat text, message-list names, and profile body — pin grid overlay labels (name/distance) and tab labels (layout-sensitive over photos)
- VoiceOver: each cascade tile is one element — label it ("Alex, 220 feet away, online"); the Tap button announces "Tap Alex" and its sent state
- Contrast: overlay text is white weight 700 over a `rgba(0,0,0,0.65)` scrim — verify it passes WCAG AA over the brightest photos; bump scrim to 0.7 if needed
- Black on yellow (`#000000` on `#FFDE00`) passes AAA — keep it; never use white text on the yellow button
- Respect Reduce Motion: skip the lazy-load fade/scale and present the profile without the shared-element spring (cross-fade instead)
