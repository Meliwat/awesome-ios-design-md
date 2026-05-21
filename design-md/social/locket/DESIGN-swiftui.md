# Locket (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Locket's warm cream-gold visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the capture button, square viewfinder, friends pill, and history grid — plus a WidgetKit note (the widget IS the product).

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let locketGold   = Color(red: 1.0,   green: 0.690, blue: 0.180) // #FFB02E
    static let locketAmber  = Color(red: 1.0,   green: 0.620, blue: 0.173) // #FF9E2C
    static let locketDeep   = Color(red: 0.941, green: 0.541, blue: 0.114) // #F08A1D
    static let locketGoldPressed = Color(red: 0.863, green: 0.478, blue: 0.071) // #DC7A12
    static let locketOnGold = Color(red: 0.227, green: 0.141, blue: 0.0)   // #3A2400

    // MARK: - Surfaces
    static let locketCream    = Color(red: 1.0,   green: 0.969, blue: 0.925) // #FFF7EC
    static let locketBgTop    = Color(red: 1.0,   green: 0.914, blue: 0.800) // #FFE9CC
    static let locketBgBottom = Color(red: 1.0,   green: 0.851, blue: 0.659) // #FFD9A8
    static let locketWhite    = Color.white                                   // #FFFFFF
    static let locketSurface  = Color(red: 1.0,   green: 0.945, blue: 0.867) // #FFF1DD
    static let locketDivider  = Color(red: 0.949, green: 0.886, blue: 0.788) // #F2E2C9

    // MARK: - Text
    static let locketTextPrimary   = Color(red: 0.173, green: 0.125, blue: 0.078) // #2C2014
    static let locketTextSecondary = Color(red: 0.541, green: 0.478, blue: 0.388) // #8A7A63
    static let locketTextTertiary  = Color(red: 0.722, green: 0.659, blue: 0.557) // #B8A88E

    // MARK: - Accent
    static let locketCoral = Color(red: 1.0, green: 0.478, blue: 0.349) // #FF7A59

    // MARK: - Semantic
    static let locketSuccess = Color(red: 0.220, green: 0.757, blue: 0.447) // #38C172
    static let locketError   = Color(red: 1.0,   green: 0.353, blue: 0.353) // #FF5A5A
}

extension LinearGradient {
    static let locketWorld = LinearGradient(colors: [.locketBgTop, .locketBgBottom],
                                            startPoint: .top, endPoint: .bottom)
}

// Warm honey-tinted shadow base — NEVER neutral gray
let locketHoney = Color(red: 0.706, green: 0.471, blue: 0.118)  // rgba(180,120,30)
let locketHoneyDeep = Color(red: 0.627, green: 0.392, blue: 0.078) // rgba(160,100,20)
```

## 2. Typography

Locket's UI face is **Poppins** (SIL OFL — free to bundle), warm 600–800. Register the TTFs via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static func locket(_ size: CGFloat, weight: Font.Weight = .medium) -> Font {
        let face: String = {
            switch weight {
            case .heavy, .black: return "Poppins-ExtraBold"
            case .bold:          return "Poppins-Bold"
            case .semibold:      return "Poppins-SemiBold"
            case .medium:        return "Poppins-Medium"
            default:             return "Poppins-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let locketDisplay   = Font.custom("Poppins-ExtraBold", size: 32)
    static let locketScreen    = Font.custom("Poppins-Bold",      size: 26)
    static let locketSection   = Font.custom("Poppins-Bold",      size: 22)
    static let locketSender    = Font.custom("Poppins-Bold",      size: 18)
    static let locketCaption   = Font.custom("Poppins-SemiBold",  size: 16)
    static let locketBody      = Font.custom("Poppins-Medium",    size: 15)
    static let locketBigNum    = Font.custom("Poppins-ExtraBold", size: 28)
    static let locketMeta      = Font.custom("Poppins-Regular",   size: 13)
    static let locketLabel     = Font.custom("Poppins-Bold",      size: 12)
    static let locketButton    = Font.custom("Poppins-Bold",      size: 16)
    static let locketPill      = Font.custom("Poppins-Bold",      size: 14)
    static let locketTab       = Font.custom("Poppins-Bold",      size: 10)
}
```

## 3. Signature Components

### Capture Button (the signature interaction)

```swift
struct CaptureButton: View {
    let onCapture: () -> Void
    @State private var pressed = false
    @State private var pulse = false

    var body: some View {
        ZStack {
            // gold ring pulse
            Circle()
                .strokeBorder(Color.locketGold, lineWidth: 4)
                .frame(width: 84, height: 84)
                .scaleEffect(pulse ? 1.6 : 1.0)
                .opacity(pulse ? 0 : 0)

            Circle()
                .fill(Color.locketWhite)
                .frame(width: 84, height: 84)
                .overlay(Circle().strokeBorder(.white.opacity(0.55), lineWidth: 5))
                .overlay(Circle().strokeBorder(Color.locketGold, lineWidth: 4).frame(width: 66, height: 66))
                .shadow(color: locketHoney.opacity(0.32), radius: 26, y: 10)
                .scaleEffect(pressed ? 0.92 : 1.0)
        }
        .contentShape(Circle())
        .gesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in withAnimation(.spring(response: 0.2, dampingFraction: 0.6)) { pressed = true } }
                .onEnded { _ in
                    withAnimation(.spring(response: 0.3, dampingFraction: 0.5)) { pressed = false }
                    withAnimation(.easeOut(duration: 0.25)) { pulse = true }
                    pulse = false
                    onCapture()
                }
        )
        .sensoryFeedback(.impact(weight: .heavy), trigger: pressed)
    }
}
```

### Square Viewfinder / Friend Photo

```swift
struct FriendPhoto: View {
    let sender: String
    let caption: String?

    var body: some View {
        ZStack {
            // photo placeholder (replace with the friend's image)
            LinearGradient(colors: [Color(red: 0.96, green: 0.73, blue: 0.42),
                                    Color(red: 0.77, green: 0.42, blue: 0.13)],
                           startPoint: .topLeading, endPoint: .bottomTrailing)
                .overlay(
                    RadialGradient(colors: [.white.opacity(0.4), .clear],
                                   center: .init(x: 0.5, y: 0.38), startRadius: 0, endRadius: 220)
                )

            VStack {
                HStack {
                    HStack(spacing: 8) {
                        Circle()
                            .fill(LinearGradient(colors: [.locketBgBottom, .locketAmber],
                                                 startPoint: .topLeading, endPoint: .bottomTrailing))
                            .frame(width: 24, height: 24)
                        Text("from \(sender)").font(.locket(13, weight: .bold)).foregroundStyle(.white)
                    }
                    .padding(.leading, 6).padding(.trailing, 14).padding(.vertical, 6)
                    .background(Capsule().fill(Color(red: 0.173, green: 0.125, blue: 0.078).opacity(0.32)))
                    .background(.ultraThinMaterial, in: Capsule())
                    Spacer()
                }
                Spacer()
                if let caption {
                    Text(caption).font(.locketCaption).foregroundStyle(.white)
                        .padding(.horizontal, 16).padding(.vertical, 8)
                        .background(RoundedRectangle(cornerRadius: 16).fill(Color(red: 0.173, green: 0.125, blue: 0.078).opacity(0.36)))
                        .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 16))
                }
            }
            .padding(16)
        }
        .aspectRatio(1, contentMode: .fit)
        .clipShape(RoundedRectangle(cornerRadius: 40))
        .shadow(color: locketHoneyDeep.opacity(0.55), radius: 24, y: 24)
    }
}
```

### Friends Photo-History Grid

```swift
struct HistoryGrid: View {
    let photos: [HistoryItem]
    private let cols = Array(repeating: GridItem(.flexible(), spacing: 8), count: 4)

    struct HistoryItem: Identifiable { let id = UUID(); let tint: Color }

    var body: some View {
        LazyVGrid(columns: cols, spacing: 8) {
            ForEach(photos) { item in
                RoundedRectangle(cornerRadius: 14)
                    .fill(item.tint)
                    .aspectRatio(1, contentMode: .fit)
                    .overlay(alignment: .bottomLeading) {
                        Circle().fill(Color.locketTextPrimary.opacity(0.4))
                            .frame(width: 14, height: 14)
                            .overlay(Circle().strokeBorder(.white, lineWidth: 1.5))
                            .padding(4)
                    }
            }
        }
    }
}
```

### Friends Pill + Primary Button + World

```swift
struct FriendsPill: View {
    let count: Int
    var body: some View {
        HStack(spacing: 8) {
            HStack(spacing: -8) {
                ForEach(0..<3, id: \.self) { i in
                    Circle()
                        .fill(LinearGradient(colors: [.locketGold, .locketCoral],
                                             startPoint: .topLeading, endPoint: .bottomTrailing))
                        .frame(width: 22, height: 22)
                        .overlay(Circle().strokeBorder(.white, lineWidth: 2))
                }
            }
            Text("\(count) friends").font(.locket(13, weight: .bold)).foregroundStyle(Color.locketTextPrimary)
        }
        .padding(.leading, 10).padding(.trailing, 16).padding(.vertical, 8)
        .background(Capsule().fill(Color.locketWhite).shadow(color: locketHoney.opacity(0.18), radius: 16, y: 6))
    }
}

struct LocketPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.locketButton).foregroundStyle(Color.locketOnGold)
                .padding(.vertical, 14).padding(.horizontal, 28)
                .background(Capsule().fill(Color.locketGold))
                .shadow(color: locketHoney.opacity(0.30), radius: 20, y: 8)
        }
        .buttonStyle(.plain)
    }
}

struct LocketWorld: View {
    var body: some View { LinearGradient.locketWorld.ignoresSafeArea() }
}
```

## 4. Bottom Tab Bar

```swift
struct LocketTabView: View {
    var body: some View {
        TabView {
            CameraView().tabItem { Label("Camera", systemImage: "camera.fill") }
            HistoryView().tabItem { Label("History", systemImage: "square.grid.2x2.fill") }
            YouView().tabItem { Label("You", systemImage: "person.fill") }
        }
        .tint(.locketDeep) // active = deep amber on the warm world; no tint pill
        // Keep the tab bar transparent over the gradient (no frosted bar — Locket stays minimal)
    }
}
```

## 5. WidgetKit — The Real Product Surface

The Home Screen widget IS Locket. Implement a `Widget` whose timeline reloads when a friend sends a photo (push → `WidgetCenter.shared.reloadTimelines`).

```swift
import WidgetKit
import SwiftUI

struct LocketWidgetEntry: TimelineEntry {
    let date: Date
    let sender: String
    let image: Image
}

struct LocketWidgetView: View {
    let entry: LocketWidgetEntry
    var body: some View {
        entry.image
            .resizable().scaledToFill()
            .overlay(alignment: .bottomLeading) {
                Text("from \(entry.sender)")
                    .font(.system(size: 11, weight: .bold)).foregroundStyle(.white)
                    .padding(.horizontal, 8).padding(.vertical, 3)
                    .background(Capsule().fill(.black.opacity(0.4)))
                    .padding(8)
            }
            .containerBackground(for: .widget) { Color.locketCream }
    }
}

struct LocketWidget: Widget {
    var body: some WidgetConfiguration {
        StaticConfiguration(kind: "LocketWidget", provider: Provider()) { entry in
            LocketWidgetView(entry: entry)
        }
        .configurationDisplayName("Locket")
        .description("A photo from your friends on your Home Screen.")
        .supportedFamilies([.systemSmall, .systemMedium])
    }
}
```

## 6. Motion

```swift
// Capture press → spring back + gold ring pulse
withAnimation(.spring(response: 0.2, dampingFraction: 0.6)) { pressed = true }
withAnimation(.spring(response: 0.3, dampingFraction: 0.5)) { pressed = false }
withAnimation(.easeOut(duration: 0.25)) { ringPulse = true }

// Send: photo lifts + fades up
withAnimation(.easeIn(duration: 0.32)) { sent = true }

// Incoming photo: cross-dissolve + gentle scale
.transition(.opacity)               // 280ms
.scaleEffect(arrived ? 1.0 : 1.03)  // settle

// History open: scale into full screen
withAnimation(.spring(response: 0.3, dampingFraction: 0.7)) { expanded = true }

// Reaction: emoji bounce + float up
withAnimation(.spring(response: 0.4, dampingFraction: 0.5)) { reactFloat = true }

// Haptics
.sensoryFeedback(.impact(weight: .heavy), trigger: captured)  // shutter
.sensoryFeedback(.impact(flexibility: .soft), trigger: reacted)
.sensoryFeedback(.success, trigger: sent)
.sensoryFeedback(.selection, trigger: selectedTab)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Camera (tab) | `camera` / `camera.fill` | 24pt |
| History (tab) | `square.grid.2x2` / `.fill` | 24pt |
| You (tab) | `person` / `person.fill` | 24pt |
| Flip camera | `arrow.triangle.2.circlepath.camera` | 22pt |
| Flash / effects | `bolt` / `sparkles` | 22pt |
| Add friend | `person.badge.plus` | 19pt |
| Messages / chat | `bubble.left.fill` | 19pt |
| Send | `paperplane.fill` | 18pt |
| Reaction heart | `heart.fill` (tint `#FF7A59`) | 20pt |
| See all | `chevron.right` | 13pt |
| Add widget | `plus.square.on.square` | 20pt |
| Settings | `gearshape.fill` | 22pt |
| Close | `xmark` | 20pt |
| Online dot | `circle.fill` (tint `#38C172`) | 8pt |

## 8. Light Mode (Locket is warm-by-design)

```swift
struct LocketTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(LocketWorld())
            .foregroundStyle(Color.locketTextPrimary)
            .preferredColorScheme(.light) // Locket has no dark theme — warmth IS the brand
    }
}
extension View { func locketTheme() -> some View { modifier(LocketTheme()) } }
```

> Locket intentionally has **no dark mode**. The identity is the warm cream-gold world. Force `.light`; do not invert to a dark canvas (the widget on the Home Screen must stay warm even in system dark mode).

## 9. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`). WidgetKit `containerBackground` requires iOS 17 — gate older OS with a plain background.
- Bundle Poppins TTFs (Regular/Medium/SemiBold/Bold/ExtraBold) via `Info.plist` — SIL OFL, free to distribute
- Locket is warm-by-design: force `.preferredColorScheme(.light)`; never ship a dark inversion — the cozy gold identity must hold, including in the widget
- Dynamic Type: support on sender names, captions, body, onboarding; keep tab labels, uppercase labels, the capture button, history-cell chrome FIXED
- VoiceOver: label the viewfinder "Photo from {sender}{, caption}"; the capture button "Take a photo, sends to your {n} friends"; history cells "Photo from {sender}, {time}"; the widget exposes its photo + sender as an accessibility label
- Color contrast: `#2C2014` on `#FFF7EC` passes WCAG AA; `#3A2400` on `#FFB02E` passes; white on the dark frosted chips passes — use `#F08A1D` (not `#FFB02E`) for small accent text on cream (gold lacks contrast on cream)
- Reduce Motion: replace the capture ring pulse + send lift with a quick crossfade; keep the experience warm and gentle
- Camera privacy: request camera access only at first capture; never auto-capture; respect the tiny-circle privacy model — photos go only to confirmed friends
- WidgetKit: reload the timeline on push when a friend posts; keep widget rendering lightweight; the widget background stays `#FFF7EC` even in system dark mode
