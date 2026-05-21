# happn (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates happn's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views — centered on the Crossings timeline and the Charm gesture.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark)
    static let happnCanvas   = Color(red: 0.055, green: 0.055, blue: 0.071) // #0E0E12
    static let happnSurface1 = Color(red: 0.094, green: 0.094, blue: 0.122) // #18181F
    static let happnSurface2 = Color(red: 0.129, green: 0.129, blue: 0.169) // #21212B
    static let happnSurface3 = Color(red: 0.173, green: 0.173, blue: 0.220) // #2C2C38
    static let happnDivider  = Color(red: 0.165, green: 0.165, blue: 0.200) // #2A2A33  (also timeline spine)

    // MARK: - Canvas & Surfaces (Light)
    static let happnCanvasLight  = Color.white                                   // #FFFFFF
    static let happnSurface1Light = Color(red: 0.965, green: 0.965, blue: 0.973) // #F6F6F8

    // MARK: - Brand
    static let happnPink      = Color(red: 1.000, green: 0.282, blue: 0.396) // #FF4865
    static let happnPinkPress = Color(red: 0.898, green: 0.208, blue: 0.310) // #E5354F
    static let happnMagenta   = Color(red: 0.914, green: 0.118, blue: 0.388) // #E91E63
    static let happnRose      = Color(red: 1.000, green: 0.482, blue: 0.576) // #FF7B93
    static let happnGold      = Color(red: 1.000, green: 0.761, blue: 0.294) // #FFC24B  (Crush/premium only)

    // MARK: - Text
    static let happnTextPrimary   = Color(red: 0.957, green: 0.957, blue: 0.965) // #F4F4F6
    static let happnTextSecondary = Color(red: 0.627, green: 0.627, blue: 0.682) // #A0A0AE
    static let happnTextTertiary  = Color(red: 0.424, green: 0.424, blue: 0.478) // #6C6C7A
    static let happnOnPink        = Color.white                                  // #FFFFFF
    static let happnOnGold        = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A

    // MARK: - Semantic
    static let happnSuccess = Color(red: 0.306, green: 0.851, blue: 0.643) // #4ED9A4
    static let happnError   = Color(red: 1.000, green: 0.361, blue: 0.361) // #FF5C5C
}

// Hero gradient — primary "Say hi" / Crush star / splash
extension LinearGradient {
    static let happnHero = LinearGradient(
        colors: [.happnPink, .happnMagenta],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
}
```

## 2. Typography

happn pairs **Poppins** (warmth: wordmark, names, headings, buttons, crossing count) with **Inter** (legibility: body, location strings, timestamps, chat). Bundle both via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    // Poppins — warmth/identity
    static let happnDisplay  = Font.custom("Poppins-Bold",     size: 32)  // -0.5 tracking
    static let happnName     = Font.custom("Poppins-Bold",     size: 26)
    static let happnSection  = Font.custom("Poppins-SemiBold", size: 22)
    static let happnSubsection = Font.custom("Poppins-SemiBold", size: 18)
    static let happnCardName = Font.custom("Poppins-SemiBold", size: 15)
    static let happnButton   = Font.custom("Poppins-SemiBold", size: 16)
    static let happnCount    = Font.custom("Poppins-SemiBold", size: 12)
    static let happnTab      = Font.custom("Poppins-SemiBold", size: 10)

    // Inter — legibility
    static let happnBody     = Font.custom("Inter-Regular",  size: 16)
    static let happnBodyMeta = Font.custom("Inter-Regular",  size: 14)
    static let happnLocation = Font.custom("Inter-Regular",  size: 12)
    static let happnCaption  = Font.custom("Inter-Regular",  size: 12)
}
```

## 3. Signature Components

### Crossings Timeline

```swift
struct Crossing: Identifiable {
    let id = UUID()
    let avatar: LinearGradient
    let name: String          // "Camille, 27"
    let place: String         // "Le Marais"
    let timeAgo: String       // "11 min ago"
    let crossCount: Int       // 3
    var charmed: Bool
}

struct CrossingsTimeline: View {
    @State var crossings: [Crossing]

    var body: some View {
        ScrollView {
            LazyVStack(spacing: 14) {
                ForEach($crossings) { $c in
                    CrossingRow(crossing: $c)
                }
            }
            .padding(.horizontal, 16)
            .padding(.top, 4)
        }
        .background(Color.happnCanvas)
    }
}

struct CrossingRow: View {
    @Binding var crossing: Crossing

    var body: some View {
        HStack(alignment: .top, spacing: 0) {
            // Connector spine + dot
            ZStack(alignment: .top) {
                Rectangle()
                    .fill(Color.happnDivider)
                    .frame(width: 2)
                    .padding(.top, 6)
                Circle()
                    .fill(Color.happnPink)
                    .frame(width: 12, height: 12)
                    .overlay(Circle().stroke(Color.happnPink.opacity(0.18), lineWidth: 4))
                    .padding(.top, 6)
            }
            .frame(width: 18)

            // Card
            HStack(spacing: 12) {
                RoundedRectangle(cornerRadius: 16).fill(crossing.avatar)
                    .frame(width: 56, height: 56)

                VStack(alignment: .leading, spacing: 3) {
                    Text(crossing.name)
                        .font(.happnCardName).foregroundStyle(Color.happnTextPrimary)
                    HStack(spacing: 5) {
                        Image(systemName: "mappin.and.ellipse")
                            .font(.system(size: 11)).foregroundStyle(Color.happnTextTertiary)
                        Text("\(crossing.place) · \(crossing.timeAgo)")
                            .font(.happnLocation).foregroundStyle(Color.happnTextSecondary)
                    }
                    Text("You crossed paths \(crossing.crossCount == 1 ? "once" : "\(crossing.crossCount) times")")
                        .font(.happnCount).foregroundStyle(Color.happnPink)
                }
                Spacer(minLength: 8)

                CharmButton(charmed: $crossing.charmed, size: 40)
            }
            .padding(12)
            .background(
                RoundedRectangle(cornerRadius: 18)
                    .fill(Color.happnSurface1)
                    .overlay(RoundedRectangle(cornerRadius: 18).strokeBorder(Color.happnDivider, lineWidth: 1))
            )
            .padding(.leading, 8)   // 26pt total left clearance with the 18pt spine column
        }
    }
}
```

### Charm Button (heart)

```swift
struct CharmButton: View {
    @Binding var charmed: Bool
    var size: CGFloat = 40
    @State private var scale: CGFloat = 1.0

    var body: some View {
        Button {
            charmed = true
            scale = 1.25
            withAnimation(.spring(response: 0.28, dampingFraction: 0.55)) { scale = 1.0 }
            UINotificationFeedbackGenerator().notificationOccurred(.success)
        } label: {
            Image(systemName: charmed ? "heart.fill" : "heart")
                .font(.system(size: size * 0.45, weight: .semibold))
                .foregroundStyle(charmed ? Color.happnOnPink : Color.happnTextSecondary)
                .frame(width: size, height: size)
                .background(
                    Circle().fill(charmed ? Color.happnPink : Color.happnSurface2)
                )
                .overlay(
                    Circle().strokeBorder(charmed ? Color.clear : Color.happnDivider, lineWidth: 1)
                )
                .shadow(color: charmed ? Color.happnPink.opacity(0.5) : .clear, radius: 8, x: 0, y: 6)
                .scaleEffect(scale)
        }
        .buttonStyle(.plain)
    }
}
```

### Crush Celebration

```swift
struct CrushCelebration: View {
    let leftAvatar: LinearGradient
    let rightAvatar: LinearGradient
    let onStartChat: () -> Void
    @State private var together = false

    var body: some View {
        ZStack {
            Color.black.opacity(0.92).ignoresSafeArea()
            VStack(spacing: 28) {
                ZStack {
                    Circle().fill(leftAvatar).frame(width: 96, height: 96)
                        .offset(x: together ? -28 : -160)
                    Circle().fill(rightAvatar).frame(width: 96, height: 96)
                        .offset(x: together ? 28 : 160)
                }
                Text("It's a Crush!")
                    .font(.happnDisplay).foregroundStyle(Color.happnTextPrimary)
                Button(action: onStartChat) {
                    Text("Start chatting")
                        .font(.happnButton).foregroundStyle(Color.happnOnPink)
                        .padding(.vertical, 15).padding(.horizontal, 30)
                        .background(Capsule().fill(LinearGradient.happnHero))
                }
            }
            // Gold sparkle layer — the only place gold appears (besides premium)
            SparkleBurst(color: .happnGold)
        }
        .onAppear {
            withAnimation(.easeOut(duration: 0.36)) { together = true }
            UINotificationFeedbackGenerator().notificationOccurred(.success)
        }
    }
}
```

### Map Crossing Pin (teardrop)

```swift
struct CrossingPin: View {
    enum Kind { case standard, mutual, ghost }
    let kind: Kind

    var body: some View {
        ZStack {
            RoundedRectangle(cornerRadius: 22)
                .fill(fill)
                .frame(width: 44, height: 44)
                .clipShape(TeardropShape())
            Circle()
                .fill(kind == .ghost ? Color.happnTextTertiary : Color.white)
                .frame(width: 30, height: 30)
        }
        .overlay(kind == .ghost ? AnyView(TeardropShape().stroke(Color.happnDivider, lineWidth: 1)) : AnyView(EmptyView()))
    }

    private var fill: AnyShapeStyle {
        switch kind {
        case .standard: return AnyShapeStyle(Color.happnPink)
        case .mutual:   return AnyShapeStyle(LinearGradient.happnHero)
        case .ghost:    return AnyShapeStyle(Color.happnSurface2)
        }
    }
}

struct TeardropShape: Shape {
    func path(in r: CGRect) -> Path {
        // 50% 50% 50% 6px rotated 45° — classic map teardrop
        var p = Path(roundedRect: r, cornerRadii: .init(topLeading: r.width/2, bottomLeading: 6, bottomTrailing: r.width/2, topTrailing: r.width/2))
        p = p.applying(CGAffineTransform(rotationAngle: .pi/4).concatenating(.init(translationX: r.midX, y: r.midY)))
        return p
    }
}
```

### Buttons

```swift
struct HappnPrimaryButton: View {
    let title: String; let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.happnButton).foregroundStyle(Color.happnOnPink)
                .padding(.vertical, 15).padding(.horizontal, 30).frame(maxWidth: .infinity)
                .background(Capsule().fill(Color.happnPink))
        }
        .buttonStyle(PressScale())
    }
}

struct HappnGradientButton: View {
    let title: String; let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.happnButton).foregroundStyle(Color.happnOnPink)
                .padding(.vertical, 15).padding(.horizontal, 30).frame(maxWidth: .infinity)
                .background(Capsule().fill(LinearGradient.happnHero))
        }
        .buttonStyle(PressScale())
    }
}

struct PressScale: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label.scaleEffect(configuration.isPressed ? 0.98 : 1.0)
            .animation(.easeOut(duration: 0.08), value: configuration.isPressed)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct HappnTabView: View {
    var body: some View {
        TabView {
            TimelineScreen().tabItem { Label("Timeline", systemImage: "heart.text.square") }
            MapScreen().tabItem { Label("Map", systemImage: "map") }
            ChatsScreen().tabItem { Label("Chats", systemImage: "bubble.left.and.bubble.right") }
            ProfileScreen().tabItem { Label("Profile", systemImage: "person") }
        }
        .tint(.happnPink)                 // active = pink icon + pink label; no tint pill
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithTransparentBackground()
            a.backgroundColor = UIColor(Color.happnCanvas).withAlphaComponent(0.94)
            a.shadowColor = UIColor(Color.happnDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Charm tap — heart 1.0 → 1.25 → 1.0, spring damping 0.55 + success haptic (see CharmButton)

// Crush reveal — avatars slide from edges over 360ms, gold burst (see CrushCelebration)
withAnimation(.easeOut(duration: 0.36)) { together = true }

// Timeline new-crossing entrance — fade + slide from bottom
.transition(.move(edge: .bottom).combined(with: .opacity))
withAnimation(.easeOut(duration: 0.24)) { /* insert crossing */ }

// Dot pulse for a fresh crossing — halo scale 1.0 → 1.4 → 1.0 once
withAnimation(.easeInOut(duration: 0.6)) { haloScale = 1.4 }

// Map pin tap — lift -6pt + bounce, then profile sheet up
withAnimation(.spring(response: 0.22, dampingFraction: 0.6)) { pinLift = -6 }

// Haptics
UINotificationFeedbackGenerator().notificationOccurred(.success)  // Charm sent, Crush
UISelectionFeedbackGenerator().selectionChanged()                 // tab switch, pin tap
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Timeline (tab) | `heart.text.square` / `.fill` | 23pt |
| Map (tab) | `map` / `map.fill` | 23pt |
| Chats (tab) | `bubble.left.and.bubble.right` / `.fill` | 23pt |
| Profile (tab) | `person` / `person.fill` | 23pt |
| Charm (heart) | `heart` / `heart.fill` | 18pt (card) / 24pt (large) |
| Crush (star) | `star.fill` | 24pt |
| Location stamp | `mappin.and.ellipse` | 11pt |
| Search | `magnifyingglass` | 17pt |
| Back | `chevron.left` | 20pt |
| Send message | `arrow.up.circle.fill` | 28pt |
| FlashNote | `text.bubble` | 14pt |
| Block / report | `exclamationmark.shield` | 18pt |
| Settings | `gearshape` | 17pt |
| Like-you / interest received | `heart.circle.fill` | 20pt |

## 7. Dark Mode

happn ships both themes; the brand is most recognizable on dark. Drive palette by `colorScheme`. The brand pink, magenta, gradient, and Crush gold are **identical** across modes.

```swift
struct HappnTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.happnCanvas : Color.happnCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.happnTextPrimary : Color(red: 0.082, green: 0.082, blue: 0.106))
            .tint(.happnPink)
    }
}

extension View { func happnTheme() -> some View { modifier(HappnTheme()) } }
```

Only canvas/surface/text tokens swap. happn pink `#FF4865`, magenta `#E91E63`, the hero gradient, and Crush gold `#FFC24B` never change between light and dark — they are the brand constants.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.spring(response:dampingFraction:)`, `LazyVStack`, MapKit `Map` view modern API)
- Bundle Poppins + Inter TTFs via `Info.plist` `UIAppFonts` — both are SIL OFL, free to distribute
- Dynamic Type: scale display, name, section, body; pin the crossing count, tab labels, and location/time meta at fixed sizes (the timeline row layout is sensitive — a wrapping count breaks the spine alignment)
- Card name: `.lineLimit(1)` + `.truncationMode(.tail)` so a long name never pushes the card to 2 lines and misaligns the dot
- VoiceOver: label each crossing row "Crossed paths with {name}, near {place}, {timeAgo}, {n} times" and the Charm as "Send a Charm to {name}" / "Charm sent"; the Crush screen announces "It's a Crush with {name}"
- The crossing count is meaning, not decoration — ensure VoiceOver reads it; don't hide it as a visual-only flourish
- Color contrast: `#F4F4F6` on `#0E0E12` is strong; `#FFFFFF` on `#FF4865` passes AA for the Charm; validate pink `#FF4865` text on dark for the crossing count (passes for 12pt semibold — keep the weight)
- Reduce Motion: disable the dot pulse, the Charm 1.25 overshoot (use a simple fill cross-fade), and the Crush avatar slide; keep the success haptic
- Reduce Transparency: replace the blurred tab bar with solid `Color.happnCanvas`
- Gold is reserved for Crush/premium — never use it as a status or accent color elsewhere, including in accessibility highlight states
