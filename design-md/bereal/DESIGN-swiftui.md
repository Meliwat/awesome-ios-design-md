# BeReal (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates BeReal's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let brealCanvas   = Color.black                                  // #000000
    static let brealSurface1 = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let brealSurface2 = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E
    static let brealDivider  = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E

    // MARK: - Text
    static let brealTextPrimary   = Color.white                                 // #FFFFFF
    static let brealTextSecondary = Color(red: 0.557, green: 0.557, blue: 0.576) // #8E8E93
    static let brealTextTertiary  = Color(red: 0.388, green: 0.388, blue: 0.400) // #636366

    // MARK: - Accent (intentionally monochrome)
    static let brealAccent    = Color.white                                 // #FFFFFF — no brand hue
    static let brealOnAccent  = Color.black                                 // #000000

    // MARK: - Semantic
    static let brealLate    = Color(red: 1.0,   green: 0.839, blue: 0.039)  // #FFD60A
    static let brealError   = Color(red: 1.0,   green: 0.271, blue: 0.227)  // #FF453A
    static let brealOnline  = Color(red: 0.188, green: 0.820, blue: 0.345)  // #30D158
}
```

## 2. Typography

BeReal ships no custom typeface — it uses the system font (SF Pro). Use `.system` directly; enable `.monospacedDigit()` on the countdown.

```swift
extension Font {
    // Display
    static let brealTimer      = Font.system(size: 24, weight: .bold).monospacedDigit()
    static let brealScreenTitle = Font.system(size: 22, weight: .bold)

    // Text
    static let brealUsername   = Font.system(size: 17, weight: .semibold)
    static let brealCaption    = Font.system(size: 16, weight: .regular)
    static let brealBody       = Font.system(size: 15, weight: .regular)
    static let brealCommentAuthor = Font.system(size: 14, weight: .semibold)
    static let brealMeta       = Font.system(size: 13, weight: .regular)
    static let brealLateBadge  = Font.system(size: 12, weight: .bold)
    static let brealButton     = Font.system(size: 17, weight: .semibold)
    static let brealTab        = Font.system(size: 10, weight: .semibold)
    static let brealRetake     = Font.system(size: 11, weight: .semibold)
}
```

## 3. Signature Components

### Dual-Lens Composite Card (the signature)

```swift
struct DualLensCard: View {
    let backPhoto: Image
    let frontSelfie: Image
    @State private var corner: UnitPoint = .topLeading
    @State private var lifted = false

    private let selfieSize = CGSize(width: 100, height: 134)

    var body: some View {
        GeometryReader { geo in
            ZStack(alignment: alignment(for: corner)) {
                backPhoto
                    .resizable()
                    .aspectRatio(3/4, contentMode: .fill)
                    .frame(width: geo.size.width, height: geo.size.width * 4/3)
                    .clipShape(RoundedRectangle(cornerRadius: 18))

                frontSelfie
                    .resizable()
                    .aspectRatio(3/4, contentMode: .fill)
                    .frame(width: selfieSize.width, height: selfieSize.height)
                    .clipShape(RoundedRectangle(cornerRadius: 14))
                    .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(.black, lineWidth: 2))
                    .padding(12)
                    .scaleEffect(lifted ? 1.05 : 1.0)
                    .shadow(color: .black.opacity(lifted ? 0.7 : 0), radius: 18, y: 6)
                    .gesture(
                        LongPressGesture(minimumDuration: 0.2)
                            .onEnded { _ in withAnimation(.spring()) { lifted = true } }
                            .sequenced(before: DragGesture())
                            .onEnded { _ in
                                withAnimation(.spring(response: 0.3, dampingFraction: 0.75)) {
                                    lifted = false
                                    corner = [.topLeading, .topTrailing, .bottomLeading, .bottomTrailing].randomElement()!
                                }
                            }
                    )
            }
        }
        .aspectRatio(3/4, contentMode: .fit)
        .shadow(color: .black.opacity(0.6), radius: 24, y: 8)
    }

    private func alignment(for c: UnitPoint) -> Alignment {
        switch c {
        case .topTrailing: return .topTrailing
        case .bottomLeading: return .bottomLeading
        case .bottomTrailing: return .bottomTrailing
        default: return .topLeading
        }
    }
}
```

### Capture Button (Dual Shutter)

```swift
struct CaptureButton: View {
    let onCapture: () -> Void
    @State private var pressed = false

    var body: some View {
        Button {
            onCapture()
        } label: {
            ZStack {
                Circle().strokeBorder(.white, lineWidth: 4).frame(width: 76, height: 76)
                Circle().fill(.white).frame(width: pressed ? 64 : 0, height: pressed ? 64 : 0)
            }
            .scaleEffect(pressed ? 0.92 : 1)
        }
        .buttonStyle(.plain)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in pressed = true }
                .onEnded { _ in pressed = false }
        )
        // heavy for back camera, then a soft for the front lens ~0.5s later
        .sensoryFeedback(.impact(weight: .heavy), trigger: pressed)
    }
}
```

### Primary / Secondary Button

```swift
struct BeRealButton: View {
    enum Style { case filled, outline }
    let title: String
    var style: Style = .filled
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.brealButton)
                .foregroundStyle(style == .filled ? Color.brealOnAccent : .white)
                .padding(.vertical, style == .filled ? 14 : 12)
                .padding(.horizontal, style == .filled ? 32 : 24)
                .background(Capsule().fill(style == .filled ? Color.brealAccent : .clear))
                .overlay(Capsule().strokeBorder(style == .outline ? Color.white.opacity(0.4) : .clear, lineWidth: 1))
        }
        .buttonStyle(BeRealPressable())
    }
}

struct BeRealPressable: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .opacity(configuration.isPressed ? 0.85 : 1)
            .scaleEffect(configuration.isPressed ? 0.97 : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### Late Badge & Countdown Banner

```swift
struct LateBadge: View {
    let text: String   // "2h late"
    var body: some View {
        Label(text, systemImage: "exclamationmark.triangle.fill")
            .font(.brealLateBadge)
            .foregroundStyle(Color.brealLate)
            .labelStyle(.titleAndIcon)
    }
}

struct CountdownBanner: View {
    let remaining: String   // "1:42"
    var body: some View {
        Text("⏰ \(remaining) left to capture a BeReal")
            .font(.brealTimer)
            .monospacedDigit()
            .foregroundStyle(Color.brealLate)
            .padding(.vertical, 12).padding(.horizontal, 20)
            .frame(maxWidth: .infinity)
            .background(.ultraThinMaterial.opacity(0.6), in: Capsule())
            .overlay(Capsule().strokeBorder(Color.brealLate.opacity(0.4), lineWidth: 1))
    }
}
```

### RealMoji Reaction Cluster

```swift
struct RealMojiCluster: View {
    let selfies: [Image]    // real selfie photos — never emoji
    let onAdd: () -> Void

    var body: some View {
        HStack(spacing: -8) {           // overlap by 8pt
            ForEach(Array(selfies.enumerated()), id: \.offset) { _, img in
                img.resizable().scaledToFill()
                    .frame(width: 30, height: 30)
                    .clipShape(Circle())
                    .overlay(Circle().strokeBorder(.black, lineWidth: 2))
            }
            Button(action: onAdd) {
                Image(systemName: "plus")
                    .font(.system(size: 14, weight: .semibold))
                    .foregroundStyle(.white)
                    .frame(width: 30, height: 30)
                    .overlay(Circle().strokeBorder(.white.opacity(0.6), style: StrokeStyle(lineWidth: 1.5, dash: [3])))
            }
            .padding(.leading, 12)
        }
        // No counts, no like numbers — reactions are faces, not metrics
    }
}
```

## 4. Distinctive System — Selfie Corner-Snap

```swift
// The front selfie inset is the soul of BeReal. Long-press lifts it
// (scale 1.05 + shadow), drag, release snaps to nearest corner with a spring.
// See DualLensCard above for the full gesture chain. Key spring:
//   .spring(response: 0.3, dampingFraction: 0.75)
// Tapping (not dragging) the selfie swaps which camera is full-bleed:

func swapPrimaryCamera() {
    withAnimation(.easeInOut(duration: 0.35)) { /* cross-fade back<->front */ }
}
```

## 5. Tab Bar

BeReal's tab bar is opaque true-black — **no blur**, the canvas simply continues. Active tint is white.

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = .black
        appearance.shadowColor = UIColor.white.withAlphaComponent(0.08)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            FriendsView().tabItem  { Label("Friends",  systemImage: "person.2.fill") }
            OfficialView().tabItem { Label("Official", systemImage: "star.circle.fill") }
            SearchView().tabItem   { Label("Search",   systemImage: "magnifyingglass") }
            ProfileView().tabItem  { Label("Profile",  systemImage: "person.crop.circle") }
        }
        .tint(.white)   // accent is white — BeReal has no brand hue
    }
}
```

## 6. Motion

```swift
// Dual-shutter capture: white flash + heavy haptic, then ~0.5s later a soft haptic
.sensoryFeedback(.impact(weight: .heavy), trigger: didCaptureBack)
.sensoryFeedback(.impact(weight: .soft),  trigger: didCaptureFront)

// Camera flip: 180° 3D rotation of the viewfinder
.rotation3DEffect(.degrees(flipped ? 180 : 0), axis: (x: 0, y: 1, z: 0))
.animation(.easeInOut(duration: 0.35), value: flipped)

// Selfie drag-snap: spring(response: 0.3, dampingFraction: 0.75) — see §3
// RealMoji pop: scale 0 → 1.2 → 1.0 over 280ms
.scaleEffect(appeared ? 1 : 0)
.animation(.spring(response: 0.28, dampingFraction: 0.55), value: appeared)

// Friends' BeReals blurred until you post yours:
.blur(radius: hasPosted ? 0 : 30)
.animation(.easeOut(duration: 0.4), value: hasPosted)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Camera flip | `arrow.triangle.2.circlepath.camera` | 28pt |
| Flash | `bolt.fill` / `bolt.slash.fill` | 22pt |
| Late badge | `exclamationmark.triangle.fill` | 12pt |
| Add RealMoji | `plus` | 14pt |
| Comments | `bubble.left` | 20pt |
| Share | `square.and.arrow.up` | 22pt |
| More | `ellipsis` | 20pt |
| Friends (tab) | `person.2.fill` | 26pt |
| Official (tab) | `star.circle.fill` | 26pt |
| Search (tab) | `magnifyingglass` | 26pt |
| Profile (tab) | `person.crop.circle` | 26pt |
| BTS video | `play.rectangle.on.rectangle` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator` — use `.heavy` then `.soft`)
- Support Dynamic Type on usernames, captions, and comments — pin the countdown timer (tabular, layout-critical), late badge, retake count, and tab labels
- VoiceOver: label the dual-lens card ("jordan's BeReal, 2 hours late"), expose the selfie as an adjustable "drag to reposition" element, and announce RealMojis as "Reaction from <name>" (never read a count — there is none)
- The capture button needs `.accessibilityLabel("Capture BeReal")` and a hint about the dual exposure
- Contrast: `#8E8E93` secondary on `#000000` passes WCAG AA at 13pt+; the amber `#FFD60A` late badge on black is high-contrast — keep it for urgency only
- Reduce Motion: replace the 180° camera-flip rotation and selfie drag-spring with a cross-fade; freeze the countdown roll to an instant text swap
