# Nike Run Club (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates NRC's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, themed components, and the signature run-tracking screen with Volt progress ring.

Targets iOS 17+. `.sensoryFeedback`, `matchedGeometryEffect`, and `Canvas`-based progress rings all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Nike Brand
    static let nikeVolt          = Color(red: 0.800, green: 1.00,  blue: 0.00)   // #CCFF00
    static let nikeVoltPressed   = Color(red: 0.624, green: 0.800, blue: 0.00)   // #9FCC00
    static let nikeVoltDim       = Color(red: 0.541, green: 0.659, blue: 0.00)   // #8AA800 (light-mode accessible)
    static let nikeRed           = Color(red: 0.980, green: 0.329, blue: 0.00)   // #FA5400
    static let nikeBrakeRed      = Color(red: 0.898, green: 0.035, blue: 0.086)  // #E50916

    // MARK: - Canvas & Surfaces
    static let nikeCanvas        = Color(red: 0.00, green: 0.00, blue: 0.00)     // #000000
    static let nikeCharcoal      = Color(red: 0.039, green: 0.039, blue: 0.039)  // #0A0A0A
    static let nikeSurface1      = Color(red: 0.102, green: 0.102, blue: 0.102)  // #1A1A1A
    static let nikeSurface2      = Color(red: 0.169, green: 0.169, blue: 0.169)  // #2B2B2B
    static let nikeDivider       = Color(red: 0.200, green: 0.200, blue: 0.200)  // #333333

    // MARK: - Text
    static let nikeWhite         = Color(red: 1.00, green: 1.00, blue: 1.00)
    static let nikeGray1         = Color(red: 0.702, green: 0.702, blue: 0.702)  // #B3B3B3
    static let nikeGray2         = Color(red: 0.557, green: 0.557, blue: 0.557)  // #8E8E8E
    static let nikeGray3         = Color(red: 0.361, green: 0.361, blue: 0.361)  // #5C5C5C

    // MARK: - Heart Rate Zones
    static let nikeHR1Blue       = Color(red: 0.239, green: 0.529, blue: 0.957)  // #3D87F4
    static let nikeHR2Green      = Color(red: 0.282, green: 0.780, blue: 0.494)  // #48C77E
    static let nikeHR3Yellow     = Color(red: 1.00,  green: 0.839, blue: 0.00)   // #FFD600
    static let nikeHR4Orange     = Color(red: 0.980, green: 0.329, blue: 0.00)   // #FA5400 (same as Red)
    static let nikeHR5Red        = Color(red: 0.898, green: 0.035, blue: 0.086)  // #E50916

    // MARK: - Semantic
    static let nikeWarning       = Color(red: 1.00, green: 0.769, blue: 0.00)    // #FFC400
}
```

## 2. Typography

Trade Gothic is proprietary. Bundle via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to `Oswald` (Google Fonts) or `SF Compressed` as the closest system substitute for the condensed-heavy weight.

```swift
extension Font {
    // Trade Gothic Condensed Heavy — the Nike voice
    static func tradeGothic(_ size: CGFloat) -> Font {
        Font.custom("TradeGothicNextLTW04-Heavy", size: size)
    }
    static func tradeGothicBold(_ size: CGFloat) -> Font {
        Font.custom("TradeGothicNextLTW04-Bold", size: size)
    }
    // System fallback for the rare project without Trade Gothic licensed
    static func nikeDisplay(_ size: CGFloat, weight: Font.Weight = .heavy) -> Font {
        .system(size: size, weight: weight, design: .default).width(.condensed)
    }

    // Hero stats (run tracking)
    static let nrcRunHero      = Font.custom("TradeGothicNextLTW04-Heavy", size: 88).monospacedDigit()
    static let nrcPreHero      = Font.custom("TradeGothicNextLTW04-Heavy", size: 64).monospacedDigit()
    static let nrcHeadline     = Font.custom("TradeGothicNextLTW04-Heavy", size: 48)
    static let nrcScreamHdr    = Font.custom("TradeGothicNextLTW04-Heavy", size: 32)
    static let nrcSubHdr       = Font.custom("TradeGothicNextLTW04-Bold",  size: 22)
    static let nrcStatLabel    = Font.custom("TradeGothicNextLTW04-Heavy", size: 13)
    static let nrcButton       = Font.custom("TradeGothicNextLTW04-Heavy", size: 17)
    static let nrcAchievement  = Font.custom("TradeGothicNextLTW04-Heavy", size: 56).monospacedDigit()

    // SF Pro for body / metadata
    static let nrcCardTitle    = Font.system(size: 17, weight: .bold,     design: .default)
    static let nrcBody         = Font.system(size: 15, weight: .regular,  design: .default)
    static let nrcBodyBold     = Font.system(size: 15, weight: .semibold, design: .default)
    static let nrcMeta         = Font.system(size: 13, weight: .regular,  design: .default)
    static let nrcCaption      = Font.system(size: 11, weight: .medium,   design: .default)
    static let nrcTab          = Font.system(size: 10, weight: .semibold, design: .default)
}
```

## 3. Signature Components

### Progress Ring (the Nike Run heart)

```swift
struct VoltProgressRing: View {
    let progress: Double      // 0...1
    let diameter: CGFloat
    let strokeWidth: CGFloat

    var body: some View {
        ZStack {
            // Track
            Circle()
                .stroke(Color.white.opacity(0.1), style: StrokeStyle(lineWidth: strokeWidth, lineCap: .round))
            // Volt fill
            Circle()
                .trim(from: 0, to: progress)
                .stroke(Color.nikeVolt, style: StrokeStyle(lineWidth: strokeWidth, lineCap: .round))
                .rotationEffect(.degrees(-90))
                .animation(.linear(duration: 1.0), value: progress)
        }
        .frame(width: diameter, height: diameter)
    }
}
```

### Run Tracking Screen (the hero component)

```swift
struct RunTrackingScreen: View {
    let elapsed: String        // "12:34"
    let distance: String       // "1.4"
    let pace: String           // "8:42"
    let hr: String             // "152"
    let progress: Double       // 0...1
    @State private var paused = false

    var body: some View {
        ZStack {
            Color.nikeCanvas.ignoresSafeArea()

            VStack(spacing: 32) {
                // 3-up stats
                HStack(alignment: .top, spacing: 24) {
                    NRCStat(label: "Distance", value: distance)
                    NRCStat(label: "Min/Mi",   value: pace)
                    NRCStat(label: "BPM",      value: hr)
                }
                .padding(.horizontal, 24)

                // Progress ring with elapsed inside
                ZStack {
                    VoltProgressRing(progress: progress, diameter: 280, strokeWidth: 6)
                    VStack(spacing: 4) {
                        Text(elapsed)
                            .font(.nrcRunHero)
                            .foregroundStyle(.white)
                            .tracking(0.5)
                            .lineSpacing(-8)
                        Text("ELAPSED".uppercased())
                            .font(.nrcStatLabel)
                            .tracking(1.0)
                            .foregroundStyle(.white.opacity(0.7))
                    }
                }

                Spacer()

                // Pause / Lock controls
                HStack {
                    Spacer()
                    Button {
                        paused.toggle()
                    } label: {
                        Image(systemName: paused ? "play.fill" : "pause.fill")
                            .font(.system(size: 28, weight: .bold))
                            .foregroundStyle(paused ? .black : .black)
                            .frame(width: 72, height: 72)
                            .background(Circle().fill(paused ? Color.nikeVolt : Color.white))
                    }
                    .sensoryFeedback(.impact(weight: .heavy), trigger: paused)
                    Spacer()
                    Button {} label: {
                        Image(systemName: "lock.fill")
                            .font(.system(size: 18, weight: .bold))
                            .foregroundStyle(.white)
                            .frame(width: 48, height: 48)
                            .background(Circle().fill(Color.nikeSurface2))
                    }
                    .padding(.trailing, 16)
                }
                .padding(.bottom, 48)
            }
            .padding(.top, 64)
        }
    }
}

struct NRCStat: View {
    let label: String
    let value: String
    var body: some View {
        VStack(spacing: 4) {
            Text(label.uppercased())
                .font(.nrcStatLabel)
                .tracking(1.0)
                .foregroundStyle(.white.opacity(0.7))
            Text(value)
                .font(.tradeGothic(64).monospacedDigit())
                .foregroundStyle(.white)
                .tracking(0.4)
                .lineSpacing(-4)
        }
        .frame(maxWidth: .infinity)
    }
}
```

### Primary Action Button ("START RUN" / "LET'S DO THIS")

```swift
struct NikeActionButton: View {
    let title: String
    var style: Style = .white
    var onTap: () -> Void

    enum Style { case white, volt, outline }

    var bg: Color {
        switch style {
        case .white:   return .white
        case .volt:    return .nikeVolt
        case .outline: return .clear
        }
    }
    var fg: Color {
        switch style {
        case .outline: return .white
        default:       return .black
        }
    }

    var body: some View {
        Button(action: onTap) {
            Text(title.uppercased())
                .font(.nrcButton)
                .tracking(0.5)
                .foregroundStyle(fg)
                .frame(maxWidth: .infinity)
                .frame(height: 56)
                .background(
                    RoundedRectangle(cornerRadius: 32)
                        .fill(bg)
                        .overlay(
                            RoundedRectangle(cornerRadius: 32)
                                .strokeBorder(style == .outline ? Color.white : .clear, lineWidth: 2)
                        )
                )
        }
        .sensoryFeedback(.impact(weight: .heavy), trigger: UUID())
        .padding(.horizontal, 16)
    }
}
```

### Run Plan Card (Home Feed)

```swift
struct RunPlanCard: View {
    let photo: Image
    let eyebrow: String        // "GUIDED RUN · 25 MIN"
    let title: String          // "RECOVERY MILE"
    let description: String    // "Coach Bennett · Easy effort"

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            photo
                .resizable()
                .scaledToFill()
                .frame(height: 320)
                .clipped()

            LinearGradient(
                colors: [.clear, .black.opacity(0.6), .black.opacity(0.85)],
                startPoint: .top,
                endPoint: .bottom
            )

            VStack(alignment: .leading, spacing: 6) {
                Text(eyebrow.uppercased())
                    .font(.nrcCaption)
                    .tracking(0.5)
                    .foregroundStyle(.white.opacity(0.8))
                Text(title.uppercased())
                    .font(.nrcScreamHdr)
                    .tracking(0.3)
                    .foregroundStyle(.white)
                Text(description)
                    .font(.nrcMeta)
                    .foregroundStyle(.white.opacity(0.85))
            }
            .padding(16)
        }
        .frame(height: 320)
        .clipShape(RoundedRectangle(cornerRadius: 16))
    }
}
```

### Achievement Medal Reveal

```swift
struct AchievementMedal: View {
    let milestone: String       // "100"
    let label: String           // "100 MILES"
    let subtitle: String        // "Lifetime distance"

    @State private var revealed = false

    var body: some View {
        VStack(spacing: 16) {
            ZStack {
                // Hexagonal silhouette
                HexagonShape()
                    .fill(LinearGradient(colors: [.nikeVolt, .nikeRed], startPoint: .topLeading, endPoint: .bottomTrailing))
                    .frame(width: 200, height: 200)
                Text(milestone)
                    .font(.nrcAchievement)
                    .foregroundStyle(.black)
            }
            .scaleEffect(revealed ? 1.0 : 0.5)
            .opacity(revealed ? 1 : 0)
            .animation(.spring(response: 0.4, dampingFraction: 0.6), value: revealed)

            Text(label.uppercased())
                .font(.nrcSubHdr)
                .tracking(0.3)
                .foregroundStyle(.white)
            Text(subtitle)
                .font(.nrcBody)
                .foregroundStyle(.nikeGray1)
        }
        .padding(24)
        .onAppear {
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.1) { revealed = true }
        }
        .sensoryFeedback(.success, trigger: revealed)
    }
}

struct HexagonShape: Shape {
    func path(in rect: CGRect) -> Path {
        var path = Path()
        let w = rect.width, h = rect.height
        path.move(to: CGPoint(x: w * 0.5, y: 0))
        path.addLine(to: CGPoint(x: w, y: h * 0.25))
        path.addLine(to: CGPoint(x: w, y: h * 0.75))
        path.addLine(to: CGPoint(x: w * 0.5, y: h))
        path.addLine(to: CGPoint(x: 0, y: h * 0.75))
        path.addLine(to: CGPoint(x: 0, y: h * 0.25))
        path.closeSubpath()
        return path
    }
}
```

### Heart Rate Zone Bar

```swift
struct HRZoneBar: View {
    let times: [Double]   // 5 values, time spent in each zone (will normalize)
    let zones: [(Color, String)] = [
        (.nikeHR1Blue,   "Z1 Easy"),
        (.nikeHR2Green,  "Z2 Steady"),
        (.nikeHR3Yellow, "Z3 Mod"),
        (.nikeHR4Orange, "Z4 Hard"),
        (.nikeHR5Red,    "Z5 Max"),
    ]

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            // Stacked bar
            GeometryReader { geo in
                HStack(spacing: 0) {
                    ForEach(Array(times.enumerated()), id: \.offset) { i, t in
                        Rectangle()
                            .fill(zones[i].0)
                            .frame(width: geo.size.width * CGFloat(t / max(times.reduce(0, +), 1)))
                    }
                }
            }
            .frame(height: 8)
            .clipShape(RoundedRectangle(cornerRadius: 4))

            // Zone labels
            HStack(spacing: 16) {
                ForEach(Array(zones.enumerated()), id: \.offset) { i, z in
                    HStack(spacing: 4) {
                        Circle().fill(z.0).frame(width: 6, height: 6)
                        Text(z.1.uppercased())
                            .font(.nrcCaption)
                            .tracking(0.3)
                            .foregroundStyle(.white)
                    }
                }
            }
        }
    }
}
```

## 4. Tab Bar (with Volt Active Indicator)

```swift
struct RootTabView: View {
    @State private var tab = 0
    @Namespace private var voltNS

    var body: some View {
        ZStack(alignment: .bottom) {
            TabView(selection: $tab) {
                HomeView()     .tabItem { Label("Home",     systemImage: "house") }.tag(0)
                ActivityView() .tabItem { Label("Activity", systemImage: "chart.bar") }.tag(1)
                CoachView()    .tabItem { Label("Coach",    systemImage: "person.crop.square") }.tag(2)
                GoalView()     .tabItem { Label("Goal",     systemImage: "target") }.tag(3)
                ProfileView()  .tabItem { Label("Profile",  systemImage: "person.circle") }.tag(4)
            }
            .tint(.white)
            .sensoryFeedback(.selection, trigger: tab)
        }
        .preferredColorScheme(.dark)
        .background(Color.nikeCanvas)
    }
}
```

## 5. Motion & Haptics

```swift
// START RUN tap — the heaviest haptic
.sensoryFeedback(.impact(weight: .heavy), trigger: startedRun)

// PAUSE / RESUME
.sensoryFeedback(.impact(weight: .heavy), trigger: paused)

// Tab switch
.sensoryFeedback(.selection, trigger: tab)

// Achievement reveal
.sensoryFeedback(.success, trigger: achievementSeen)
withAnimation(.spring(response: 0.4, dampingFraction: 0.6)) { revealed = true }

// Progress ring linear tick
.animation(.linear(duration: 1.0), value: progress)

// Confetti burst on PR
.sensoryFeedback(.impact(weight: .heavy), trigger: prCelebration)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Pause | `pause.fill` | 28pt |
| Play / Resume | `play.fill` | 28pt |
| Stop | `stop.fill` | 28pt |
| Lock | `lock.fill` | 18pt |
| GPS strength | `location.fill` | 16pt |
| Coach Bennett | `person.crop.circle.fill` | 22pt |
| Home tab | `house` / `house.fill` | 24pt |
| Activity tab | `chart.bar` / `chart.bar.fill` | 24pt |
| Coach tab | `person.crop.square` / `person.crop.square.fill` | 24pt |
| Goal tab | `target` | 24pt |
| Profile tab | `person.circle` / `person.circle.fill` | 24pt |
| Heart rate | `heart.fill` | 16pt |
| Share | `square.and.arrow.up` | 18pt |
| Achievement medal | (custom hexagon) | 200pt |
| Settings | `gearshape.fill` | 22pt |
| Music | `music.note` | 18pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 for `.sensoryFeedback` and modern `tracking()` text modifier; iOS 16 fallback uses `UIImpactFeedbackGenerator` and `kerning()`
- Trade Gothic Next LT W04 is licensed via Monotype/Linotype — check your license; if you can't bundle it, use the `Oswald` Google Font fallback or `SF Compressed` system face
- Dynamic Type: scale body, metadata, and plan card title; FIX hero numbers, button labels, and stat labels (the visual identity depends on them)
- VoiceOver: Run tracking screen reads as `"Run in progress, 12 minutes 34 seconds elapsed, 1.4 miles, 8 minute 42 second per mile pace, heart rate 152 BPM. Pause button."`. Each stat is a separate element so a user can spot-check one stat at a time.
- Contrast: Volt `#CCFF00` on black passes WCAG AAA at all sizes; Volt on white fails — use `nikeVoltDim` `#8AA800` on light surfaces if you must
- Reduce Motion: skip the medal scale spring and the confetti burst — fall back to a fade in over 300ms with no scale change
- Tabular numerals: `.monospacedDigit()` everywhere there's a stat — pace columns, splits, elapsed, BPM
- Dark mode: NRC is dark-only by brand design; respect `colorScheme: .dark` regardless of system setting
- The progress ring should be drawn via SwiftUI `Circle().trim(from:to:).stroke(...)` — not a `Canvas` — so it animates smoothly with the `.linear(duration: 1.0)` modifier
