# Waze (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Waze's playful, cartoon-forward visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, themed components for the hazard speech bubble, the next-turn card, the report FAB with tinted purple shadow, and the speed-limit tile.

Targets iOS 17+ for `.sensoryFeedback`, `matchedGeometryEffect`, and SF Pro Rounded via the `.rounded` font design parameter.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let wazePurple       = Color(red: 0.494, green: 0.333, blue: 0.745)  // #7E55BE
    static let wazePurpleDeep   = Color(red: 0.357, green: 0.235, blue: 0.604)  // #5B3C9A
    static let wazePurpleTint   = Color(red: 0.910, green: 0.871, blue: 0.961)  // #E8DEF5
    static let wazeCyan         = Color(red: 0.200, green: 0.800, blue: 1.000)  // #33CCFF
    static let wazeCyanDeep     = Color(red: 0.000, green: 0.600, blue: 0.898)  // #0099E5

    // MARK: - Hazard Colors
    static let wazePoliceRed    = Color(red: 0.937, green: 0.416, blue: 0.396)  // #EF6A65
    static let wazeTrafficOrng  = Color(red: 0.965, green: 0.596, blue: 0.200)  // #F69833
    static let wazeClosureYel   = Color(red: 0.976, green: 0.769, blue: 0.180)  // #F9C42E
    static let wazeClearedGrn   = Color(red: 0.459, green: 0.780, blue: 0.243)  // #75C73E
    static let wazeHazardBrown  = Color(red: 0.545, green: 0.435, blue: 0.278)  // #8B6F47
    static let wazeCameraGray   = Color(red: 0.420, green: 0.420, blue: 0.420)  // #6B6B6B

    // MARK: - Map Cartography
    static let wazeMapCream     = Color(red: 1.000, green: 0.988, blue: 0.949)  // #FFFCF2
    static let wazeMapWater     = Color(red: 0.608, green: 0.871, blue: 0.937)  // #9BDEEF
    static let wazeMapPark      = Color(red: 0.773, green: 0.910, blue: 0.608)  // #C5E89B
    static let wazeMapRoadMajor = Color(red: 1.000, green: 1.000, blue: 1.000)  // #FFFFFF
    static let wazeMapRoadMinor = Color(red: 0.961, green: 0.941, blue: 0.898)  // #F5F0E5
    static let wazeMapHighway   = Color(red: 1.000, green: 0.851, blue: 0.439)  // #FFD970
    static let wazeMapBuilding  = Color(red: 0.910, green: 0.886, blue: 0.820)  // #E8E2D1

    // MARK: - UI Chrome
    static let wazeCardCanvas   = Color(red: 1.000, green: 1.000, blue: 1.000)  // #FFFFFF
    static let wazeSurfaceGray  = Color(red: 0.961, green: 0.961, blue: 0.969)  // #F5F5F7
    static let wazeSurfaceGray2 = Color(red: 0.918, green: 0.918, blue: 0.925)  // #EAEAEC
    static let wazeDivider      = Color(red: 0.839, green: 0.839, blue: 0.851)  // #D6D6D9

    // MARK: - Text
    static let wazeInk          = Color(red: 0.102, green: 0.102, blue: 0.102)  // #1A1A1A
    static let wazeSecondary    = Color(red: 0.420, green: 0.420, blue: 0.420)  // #6B6B6B
    static let wazeTertiary     = Color(red: 0.627, green: 0.627, blue: 0.627)  // #A0A0A0

    // MARK: - Semantic
    static let wazeSuccess      = Color(red: 0.204, green: 0.780, blue: 0.349)  // #34C759
    static let wazeWarning      = Color(red: 0.976, green: 0.769, blue: 0.180)  // #F9C42E
    static let wazeError        = Color(red: 0.937, green: 0.416, blue: 0.396)  // #EF6A65

    // MARK: - Dark Mode (Night driving)
    static let wazeDarkMapLand  = Color(red: 0.118, green: 0.125, blue: 0.149)  // #1E2026
    static let wazeDarkMapWater = Color(red: 0.059, green: 0.239, blue: 0.369)  // #0F3D5E
    static let wazeDarkMapPark  = Color(red: 0.122, green: 0.227, blue: 0.122)  // #1F3A1F
    static let wazeDarkCardSurf = Color(red: 0.149, green: 0.161, blue: 0.196)  // #262932
    static let wazeDarkSurface2 = Color(red: 0.227, green: 0.239, blue: 0.278)  // #3A3D47
    static let wazeDarkDivider  = Color(red: 0.290, green: 0.302, blue: 0.345)  // #4A4D58
    static let wazePurpleDark   = Color(red: 0.624, green: 0.463, blue: 0.855)  // #9F76DA
    static let wazeCyanDark     = Color(red: 0.365, green: 0.851, blue: 1.000)  // #5DD9FF
}
```

## 2. Typography

Waze ships with Boing, a proprietary rounded sans. Bundle the TTFs via `Info.plist` `UIAppFonts`. The correct iOS fallback is `SF Pro Rounded` — accessible via `.system(..., design: .rounded)`.

```swift
extension Font {
    // Helper for Boing with rounded SF Pro fallback
    static func boing(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        // If Boing is bundled:
        // .custom("Boing-\(weight.boingName)", size: size)
        // Otherwise:
        return .system(size: size, weight: weight, design: .rounded)
    }

    static func boingMono(_ size: CGFloat, weight: Font.Weight = .bold) -> Font {
        .system(size: size, weight: weight, design: .rounded).monospacedDigit()
    }

    // Hero next-turn
    static let wazeHeroStreet     = Font.system(size: 32, weight: .black,   design: .rounded)
    static let wazeNextTurnDist   = Font.system(size: 28, weight: .bold,    design: .rounded).monospacedDigit()
    static let wazeStepTitle      = Font.system(size: 22, weight: .bold,    design: .rounded)
    static let wazeStepSubtitle   = Font.system(size: 17, weight: .regular, design: .rounded)

    // ETA
    static let wazeETATime        = Font.system(size: 24, weight: .bold,    design: .rounded).monospacedDigit()
    static let wazeETADistance    = Font.system(size: 17, weight: .medium,  design: .rounded).monospacedDigit()

    // Place card
    static let wazePlaceTitle     = Font.system(size: 26, weight: .bold,    design: .rounded)
    static let wazePlaceSubtitle  = Font.system(size: 15, weight: .regular, design: .rounded)

    // Search
    static let wazeSearchPlaceholder = Font.system(size: 17, weight: .regular, design: .rounded)
    static let wazeSection        = Font.system(size: 13, weight: .bold,    design: .rounded)

    // Lists
    static let wazeListTitle      = Font.system(size: 17, weight: .medium,  design: .rounded)
    static let wazeListSubtitle   = Font.system(size: 13, weight: .regular, design: .rounded)

    // Speed
    static let wazeSpeedLimitNum  = Font.system(size: 22, weight: .black,   design: .rounded).monospacedDigit()
    static let wazeCurrentSpeed   = Font.system(size: 32, weight: .bold,    design: .rounded).monospacedDigit()
    static let wazeSpeedLimitLbl  = Font.system(size: 9,  weight: .bold,    design: .rounded)
    static let wazeMphLabel       = Font.system(size: 11, weight: .regular, design: .rounded)

    // Hazard speech bubble
    static let wazeHazardTitle    = Font.system(size: 14, weight: .bold,    design: .rounded)
    static let wazeHazardTime     = Font.system(size: 11, weight: .regular, design: .rounded)

    // Buttons
    static let wazeButton         = Font.system(size: 17, weight: .bold,    design: .rounded)

    // Misc
    static let wazeCaption        = Font.system(size: 12, weight: .regular, design: .rounded)
    static let wazeTab            = Font.system(size: 10, weight: .medium,  design: .rounded)
}
```

## 3. Signature Components

### Hazard Speech Bubble (The Signature)

```swift
enum HazardType {
    case police, traffic, closure, cleared, pothole, camera

    var color: Color {
        switch self {
        case .police:   return .wazePoliceRed
        case .traffic:  return .wazeTrafficOrng
        case .closure:  return .wazeClosureYel
        case .cleared:  return .wazeClearedGrn
        case .pothole:  return .wazeHazardBrown
        case .camera:   return .wazeCameraGray
        }
    }

    var icon: String {
        switch self {
        case .police:   return "shield.fill"
        case .traffic:  return "cone.fill"
        case .closure:  return "xmark.octagon.fill"
        case .cleared:  return "checkmark.circle.fill"
        case .pothole:  return "exclamationmark.triangle.fill"
        case .camera:   return "camera.fill"
        }
    }

    var title: String {
        switch self {
        case .police:   return "Police"
        case .traffic:  return "Traffic"
        case .closure:  return "Closure"
        case .cleared:  return "Cleared"
        case .pothole:  return "Pothole"
        case .camera:   return "Camera"
        }
    }
}

struct HazardSpeechBubble: View {
    let type: HazardType
    let timeAgo: String?
    @State private var appearScale: CGFloat = 0

    var body: some View {
        VStack(spacing: 0) {
            HStack(spacing: 8) {
                Image(systemName: type.icon)
                    .font(.system(size: 20, weight: .bold))
                    .foregroundStyle(.white)
                VStack(alignment: .leading, spacing: 2) {
                    Text(type.title)
                        .font(.wazeHazardTitle)
                        .foregroundStyle(.white)
                    if let timeAgo = timeAgo {
                        Text(timeAgo)
                            .font(.wazeHazardTime)
                            .foregroundStyle(.white.opacity(0.8))
                    }
                }
            }
            .padding(.horizontal, 12)
            .padding(.vertical, 8)
            .background(
                RoundedRectangle(cornerRadius: 14, style: .continuous).fill(type.color)
            )

            // Tail (downward triangle)
            HazardTail()
                .fill(type.color)
                .frame(width: 12, height: 8)
        }
        .shadow(color: .black.opacity(0.20), radius: 12, y: 4)
        .scaleEffect(appearScale)
        .onAppear {
            withAnimation(.spring(response: 0.4, dampingFraction: 0.55)) {
                appearScale = 1
            }
        }
    }
}

struct HazardTail: Shape {
    func path(in rect: CGRect) -> Path {
        var p = Path()
        p.move(to: CGPoint(x: rect.minX, y: rect.minY))
        p.addLine(to: CGPoint(x: rect.maxX, y: rect.minY))
        p.addLine(to: CGPoint(x: rect.midX, y: rect.maxY))
        p.closeSubpath()
        return p
    }
}
```

### Floating Action Button (Tinted Purple Shadow)

```swift
struct WazeFAB: View {
    var action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button {
            action()
        } label: {
            Image(systemName: "exclamationmark.bubble.fill")
                .font(.system(size: 24, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(
                    Circle().fill(pressed ? Color.wazePurpleDeep : Color.wazePurple)
                )
                .shadow(color: Color.wazePurple.opacity(0.40), radius: 16, y: 6)
                .scaleEffect(pressed ? 0.94 : 1)
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
        .pressEvents(onPress: { pressed = true }, onRelease: { pressed = false })
    }
}

extension View {
    func pressEvents(onPress: @escaping () -> Void, onRelease: @escaping () -> Void) -> some View {
        simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in onPress() }
                .onEnded   { _ in onRelease() }
        )
    }
}
```

### Current-Location Puck (Cyan Arrow with Pulse)

```swift
struct WazeLocationPuck: View {
    let heading: Double
    @State private var pulse = false

    var body: some View {
        ZStack {
            // Outer pulse
            Circle()
                .fill(Color.wazeCyan.opacity(pulse ? 0 : 0.15))
                .frame(width: 60, height: 60)
                .scaleEffect(pulse ? 1.5 : 1.0)
                .animation(.easeOut(duration: 2.0).repeatForever(autoreverses: false), value: pulse)

            // Arrow body
            WazeArrowShape()
                .fill(Color.wazeCyan)
                .frame(width: 32, height: 36)
                .overlay(
                    WazeArrowShape().stroke(Color.white, lineWidth: 3)
                )
                .shadow(color: .black.opacity(0.30), radius: 8, y: 3)
                .rotationEffect(.degrees(heading))
        }
        .onAppear { pulse = true }
    }
}

struct WazeArrowShape: Shape {
    func path(in rect: CGRect) -> Path {
        var p = Path()
        let w = rect.width
        let h = rect.height
        // A chunky arrow shape: tip at top, base wider at bottom
        p.move(to: CGPoint(x: w * 0.5, y: 0))               // tip
        p.addLine(to: CGPoint(x: w, y: h * 0.85))           // bottom right
        p.addLine(to: CGPoint(x: w * 0.5, y: h * 0.70))     // V indent
        p.addLine(to: CGPoint(x: 0, y: h * 0.85))           // bottom left
        p.closeSubpath()
        return p
    }
}
```

### Next-Turn Card (Top of Screen During Navigation)

```swift
struct NextTurnCard: View {
    let arrowSymbol: String          // e.g., "arrow.turn.up.right"
    let arrowRotation: Double
    let distance: String             // "0.4 mi"
    let streetName: String           // "Market Street"
    let subInstruction: String?      // "then turn left in 0.6 mi"

    var body: some View {
        HStack(alignment: .center, spacing: 16) {
            // Direction arrow
            Image(systemName: arrowSymbol)
                .font(.system(size: 44, weight: .heavy))
                .foregroundStyle(.white)
                .rotationEffect(.degrees(arrowRotation))
                .frame(width: 64, height: 64)

            // Content
            VStack(alignment: .leading, spacing: 4) {
                Text(distance)
                    .font(.wazeNextTurnDist)
                    .foregroundStyle(.white)
                Text(streetName)
                    .font(.wazeStepTitle)
                    .foregroundStyle(.white)
                    .lineLimit(2)
                if let sub = subInstruction {
                    Text(sub)
                        .font(.system(size: 14, weight: .regular, design: .rounded))
                        .foregroundStyle(.white.opacity(0.80))
                }
            }

            Spacer()
        }
        .padding(.horizontal, 20)
        .padding(.vertical, 16)
        .frame(maxWidth: .infinity)
        .background(
            UnevenRoundedRectangle(
                cornerRadii: .init(topLeading: 0, bottomLeading: 16, bottomTrailing: 16, topTrailing: 0)
            )
            .fill(Color.wazePurple)
        )
    }
}
```

### ETA Bottom Bar

```swift
struct WazeETABar: View {
    let duration: String       // "12 min"
    let distance: String       // "5.2 mi"
    let arrival: String        // "6:14 PM"
    let alternativeRouteSaves: String?  // "Save 3 min"
    var onEnd: () -> Void

    var body: some View {
        HStack(alignment: .center, spacing: 16) {
            VStack(alignment: .leading, spacing: 4) {
                Text(duration)
                    .font(.wazeETATime)
                    .foregroundStyle(Color.wazeInk)
                Text("\(distance) · \(arrival)")
                    .font(.wazeETADistance)
                    .foregroundStyle(Color.wazeSecondary)
            }

            Spacer()

            if let saves = alternativeRouteSaves {
                Text(saves)
                    .font(.system(size: 14, weight: .bold, design: .rounded))
                    .foregroundStyle(.white)
                    .padding(.vertical, 8).padding(.horizontal, 14)
                    .background(Capsule().fill(Color.wazeClearedGrn))
            }

            Button(action: onEnd) {
                Text("End")
                    .font(.wazeButton)
                    .foregroundStyle(Color.wazeError)
                    .padding(.vertical, 12).padding(.horizontal, 20)
                    .background(
                        Capsule().fill(Color.wazeCardCanvas)
                            .overlay(Capsule().strokeBorder(Color.wazeError, lineWidth: 1.5))
                    )
            }
            .buttonStyle(.plain)
        }
        .padding(.horizontal, 20)
        .frame(height: 80)
        .background(Color.wazeCardCanvas)
    }
}
```

### Speed Limit + Current Speed Tile

```swift
struct SpeedTile: View {
    let limit: Int             // 35
    let current: Int           // 42
    var isSpeeding: Bool { current > limit }

    var body: some View {
        VStack(spacing: 6) {
            // Speed limit section
            VStack(spacing: 2) {
                Text("SPEED LIMIT")
                    .font(.wazeSpeedLimitLbl)
                    .foregroundStyle(Color.wazeInk)
                ZStack {
                    Circle()
                        .strokeBorder(Color.wazeInk, lineWidth: 2)
                        .background(Circle().fill(Color.white))
                        .frame(width: 40, height: 40)
                    Text("\(limit)")
                        .font(.wazeSpeedLimitNum)
                        .foregroundStyle(Color.wazeInk)
                }
            }

            Divider().padding(.horizontal, 8)

            // Current speed section
            HStack(alignment: .firstTextBaseline, spacing: 4) {
                Text("\(current)")
                    .font(.wazeCurrentSpeed)
                    .foregroundStyle(isSpeeding ? Color.wazeError : Color.wazeInk)
                Text("mph")
                    .font(.wazeMphLabel)
                    .foregroundStyle(Color.wazeSecondary)
            }
        }
        .padding(.vertical, 8).padding(.horizontal, 8)
        .frame(width: 80)
        .background(
            RoundedRectangle(cornerRadius: 12).fill(Color.wazeCardCanvas)
        )
        .shadow(color: .black.opacity(0.10), radius: 8, y: 4)
    }
}
```

### "Go" Button

```swift
struct WazeGoButton: View {
    var action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button {
            action()
        } label: {
            Text("Go")
                .font(.wazeButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 56)
                .background(
                    Capsule().fill(pressed ? Color.wazePurpleDeep : Color.wazePurple)
                )
                .shadow(color: Color.wazePurple.opacity(0.30), radius: 12, y: 4)
                .scaleEffect(pressed ? 0.97 : 1)
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .heavy), trigger: pressed)
        .pressEvents(onPress: { pressed = true }, onRelease: { pressed = false })
    }
}
```

### Route Polyline (Animated Ant Trail)

```swift
struct AnimatedRoute: Shape {
    var phase: CGFloat = 0
    var animatableData: CGFloat {
        get { phase }
        set { phase = newValue }
    }

    func path(in rect: CGRect) -> Path {
        // Provide the actual route path here
        Path()
    }
}

extension View {
    func wazeRouteStyle(phase: CGFloat) -> some View {
        self
            .stroke(Color.wazePurple, style: StrokeStyle(
                lineWidth: 6,
                lineCap: .round,
                lineJoin: .round,
                dash: [16, 8],
                dashPhase: phase
            ))
    }
}
```

## 4. Wazer Avatar (Mood Picker)

```swift
struct WazerAvatar: View {
    let emoji: String   // 🙂 / 😴 / 🤩 etc.
    @State private var pop: CGFloat = 1

    var body: some View {
        Text(emoji)
            .font(.system(size: 20))
            .frame(width: 24, height: 24)
            .background(Circle().fill(Color.white))
            .overlay(Circle().strokeBorder(Color.white, lineWidth: 2))
            .scaleEffect(pop)
            .shadow(color: .black.opacity(0.2), radius: 4, y: 2)
            .onTapGesture {
                withAnimation(.spring(response: 0.4, dampingFraction: 0.5)) {
                    pop = 1.2
                }
                withAnimation(.spring(response: 0.4).delay(0.15)) {
                    pop = 1.0
                }
            }
    }
}
```

## 5. Motion & Haptics

```swift
// FAB tap
.sensoryFeedback(.impact(weight: .medium), trigger: fabPressed)

// "Go" tap (start navigation)
.sensoryFeedback(.impact(weight: .heavy), trigger: navStart)

// Hazard report submitted
.sensoryFeedback(.success, trigger: hazardSubmitted)

// Reach destination
.sensoryFeedback(.success, trigger: arrived)

// Speeding warning
.sensoryFeedback(.warning, trigger: speeding)

// Hazard bubble "pop" animation
withAnimation(.spring(response: 0.4, dampingFraction: 0.55)) {
    appearScale = 1
}

// Polyline ant trail — drive a SharedValue
withAnimation(.linear(duration: 2.0).repeatForever(autoreverses: false)) {
    dashPhase = -24
}
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| FAB report icon | `exclamationmark.bubble.fill` | 24pt |
| Police hazard | `shield.fill` (or custom hat) | 20pt |
| Traffic hazard | `cone.fill` | 20pt |
| Closure | `xmark.octagon.fill` | 20pt |
| Cleared | `checkmark.circle.fill` | 20pt |
| Pothole | `exclamationmark.triangle.fill` | 20pt |
| Camera | `camera.fill` | 20pt |
| Direction right | `arrow.turn.up.right` | 44pt |
| Direction left | `arrow.turn.up.left` | 44pt |
| Search | `magnifyingglass` | 17pt |
| Speed warning | `exclamationmark.octagon.fill` | 18pt |
| Beacon share | `bell.badge.fill` | 18pt |
| Gas | `fuelpump.fill` | 16pt |
| Home | `house.fill` | 18pt |
| Work | `briefcase.fill` | 18pt |
| Favorite | `star.fill` | 18pt |
| Recent | `clock` | 18pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`, `UnevenRoundedRectangle`, `matchedGeometryEffect`); fall back to `UIImpactFeedbackGenerator` and manual corner masking on iOS 16
- SF Pro Rounded requires iOS 13+ via `design: .rounded` — this is the correct Boing fallback
- Support Dynamic Type on place card title, body, list rows, search; FIX next-turn card text, ETA numbers, speed-limit numbers, hazard speech bubble text (all glance-critical at driving speed)
- VoiceOver: hazard speech bubble reads as `"<type>, reported <timeAgo>, ahead on <street>"`; the current-location puck announces as `"Your current position, heading <direction>"`
- Contrast: Ink `#1A1A1A` on white meets WCAG AAA; white text on Waze Purple `#7E55BE` meets AAA at all sizes
- Reduce Motion: skip the hazard bubble "pop" animation when `accessibilityReduceMotion` is true; the polyline ant trail also becomes static
- Dark mode: implement via `@Environment(\.colorScheme)` — swap map cartography, card surfaces, but KEEP both Waze Purple and Cyan at brand saturation (slightly brightened to `#9F76DA` and `#5DD9FF`)
- Speeding warning: when current speed > limit, the speed tile flips colors and triggers `.sensoryFeedback(.warning)` once
- The cartoon speech-bubble tail is implemented via a custom `Shape` — there's no SF Symbol or SwiftUI primitive that matches Waze's signature triangular tail
- The `WazeArrowShape` is the chunky cyan arrow puck — distinct from Apple Maps' circular blue dot; implement as a custom Path
