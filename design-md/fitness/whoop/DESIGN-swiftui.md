# WHOOP (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates WHOOP's instrument-panel aesthetic into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the Recovery ring with red→yellow→green interpolation, the strain bar, the sleep stage chart, and haptics.

Targets iOS 17+. `.sensoryFeedback`, `Path`-based ring drawing, and `matchedGeometryEffect` all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-mode-only)
    static let whoopCanvas    = Color(red: 0.039, green: 0.039, blue: 0.039) // #0A0A0A
    static let whoopSurface1  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let whoopSurface2  = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let whoopSurface3  = Color(red: 0.227, green: 0.227, blue: 0.227) // #3A3A3A
    static let whoopDivider   = Color(red: 0.145, green: 0.145, blue: 0.145) // #252525

    // MARK: - Strain (primary brand)
    static let whoopStrain        = Color(red: 0.000, green: 1.000, blue: 0.482) // #00FF7B
    static let whoopStrainBright  = Color(red: 0.404, green: 0.886, blue: 0.420) // #67E26B
    static let whoopStrainDim     = Color(red: 0.000, green: 0.373, blue: 0.184) // #005F2F

    // MARK: - Recovery spectrum
    static let whoopRecoveryRed    = Color(red: 1.000, green: 0.000, blue: 0.149) // #FF0026
    static let whoopRecoveryYellow = Color(red: 1.000, green: 0.871, blue: 0.000) // #FFDE00
    static let whoopRecoveryGreen  = Color(red: 0.086, green: 0.925, blue: 0.024) // #16EC06

    // MARK: - Sleep
    static let whoopSleepBlue     = Color(red: 0.000, green: 0.576, blue: 0.906) // #0093E7
    static let whoopSleepBlueBri  = Color(red: 0.310, green: 0.722, blue: 1.000) // #4FB8FF
    static let whoopREMPurple     = Color(red: 0.612, green: 0.302, blue: 1.000) // #9C4DFF
    static let whoopDeepIndigo    = Color(red: 0.239, green: 0.239, blue: 1.000) // #3D3DFF
    static let whoopLightCyan     = Color(red: 0.239, green: 0.851, blue: 1.000) // #3DD9FF

    // MARK: - Text
    static let whoopBrightWhite = Color.white                                       // #FFFFFF
    static let whoopSoftWhite   = Color(red: 0.961, green: 0.961, blue: 0.969)      // #F5F5F7
    static let whoopGray400     = Color(red: 0.631, green: 0.631, blue: 0.667)      // #A1A1AA
    static let whoopGray600     = Color(red: 0.373, green: 0.373, blue: 0.396)      // #5F5F65
    static let whoopGray700     = Color(red: 0.247, green: 0.247, blue: 0.271)      // #3F3F45

    // MARK: - Semantic
    static let whoopAlertRed   = Color(red: 1.000, green: 0.271, blue: 0.227) // #FF453A
    static let whoopWarning    = Color(red: 1.000, green: 0.667, blue: 0.000) // #FFAA00
    static let whoopInfoBlue   = Color(red: 0.039, green: 0.518, blue: 1.000) // #0A84FF
    static let whoopSuccess    = Color(red: 0.188, green: 0.820, blue: 0.345) // #30D158
    static let whoopGold       = Color(red: 0.831, green: 0.686, blue: 0.216) // #D4AF37
}

// MARK: - Recovery ramp interpolation

extension Color {
    /// Returns the recovery ring color for a given percentage (0–100).
    static func whoopRecoveryColor(_ percent: Double) -> Color {
        let p = max(0, min(100, percent)) / 100
        if p < 0.33 {
            return whoopRecoveryRed
        } else if p < 0.67 {
            // lerp red → yellow
            let t = (p - 0.33) / 0.34
            return Color(
                red: 1.0,
                green: t * 0.871,
                blue: 0.149 * (1 - t)
            )
        } else {
            // lerp yellow → green
            let t = (p - 0.67) / 0.33
            return Color(
                red: 1.0 * (1 - t) + 0.086 * t,
                green: 0.871 + (0.925 - 0.871) * t,
                blue: 0.024 * t
            )
        }
    }
}
```

## 2. Typography

WHOOP licenses DIN 2014. Bundle the TTFs via `Info.plist` (`UIAppFonts`). Fall back to Inter or system if not available.

```swift
extension Font {
    // Hero metrics
    static let whoopRecoveryHero = Font.custom("DIN2014-ExtraBold", size: 76)
    static let whoopStrainHero   = Font.custom("DIN2014-ExtraBold", size: 56)
    static let whoopSleepHero    = Font.custom("DIN2014-ExtraBold", size: 48)

    // Titles
    static let whoopLargeNav     = Font.custom("DIN2014-Bold",      size: 24)
    static let whoopSubhead      = Font.custom("DIN2014-Bold",      size: 18)
    static let whoopCardTitle    = Font.custom("DIN2014-Demi",      size: 15)

    // Body
    static let whoopBody         = Font.custom("DIN2014-Regular",   size: 14)
    static let whoopBodySmall    = Font.custom("DIN2014-Regular",   size: 13)

    // Headers (uppercase, wide tracking)
    static let whoopSectionCaps  = Font.custom("DIN2014-Bold",      size: 14)
    static let whoopMetaCaps     = Font.custom("DIN2014-Bold",      size: 11)
    static let whoopMetricLabel  = Font.custom("DIN2014-Demi",      size: 11)
    static let whoopChartAxis    = Font.custom("DIN2014-Demi",      size: 10)

    // Metric values
    static let whoopMetricValue  = Font.custom("DIN2014-Bold",      size: 18).monospacedDigit()

    // Buttons / tabs
    static let whoopButton       = Font.custom("DIN2014-Bold",      size: 14)
    static let whoopTab          = Font.custom("DIN2014-Bold",      size: 10)
}

// Fallback when DIN 2014 isn't bundled — Inter ships with similar geometric/tabular figures
extension Font {
    static func whoop(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default).monospacedDigit()
    }
}
```

## 3. Signature Components

### Recovery Ring

```swift
struct RecoveryRing: View {
    let percent: Double          // 0–100
    let diameter: CGFloat = 240
    let stroke: CGFloat = 10

    @State private var animatedPercent: Double = 0

    var body: some View {
        ZStack {
            // Background ring — open at the top with a 2pt gap
            Circle()
                .trim(from: 0.005, to: 0.995)
                .stroke(Color.whoopSurface2, style: StrokeStyle(lineWidth: stroke, lineCap: .round))
                .rotationEffect(.degrees(90))

            // Filled ring
            Circle()
                .trim(from: 0.005, to: 0.005 + (0.99 * (animatedPercent / 100)))
                .stroke(Color.whoopRecoveryColor(percent), style: StrokeStyle(lineWidth: stroke, lineCap: .round))
                .rotationEffect(.degrees(90))
                .animation(.easeOut(duration: 1.2), value: animatedPercent)

            // Center stack
            VStack(spacing: 4) {
                Text("RECOVERY")
                    .font(.whoopSectionCaps)
                    .tracking(1.6)
                    .foregroundStyle(Color.whoopGray400)

                HStack(alignment: .firstTextBaseline, spacing: 4) {
                    Text("\(Int(percent))")
                        .font(.whoopRecoveryHero)
                        .tracking(-1.6)
                        .foregroundStyle(Color.whoopBrightWhite)
                    Text("%")
                        .font(.custom("DIN2014-Bold", size: 24))
                        .foregroundStyle(Color.whoopBrightWhite)
                }
            }
        }
        .frame(width: diameter, height: diameter)
        .onAppear { animatedPercent = percent }
        .onChange(of: percent) { _, newValue in animatedPercent = newValue }
    }
}
```

### Strain Bar

```swift
struct StrainBar: View {
    let currentStrain: Double      // 0–21
    let height: CGFloat = 12

    var body: some View {
        VStack(spacing: 6) {
            // Tick labels
            ZStack(alignment: .topLeading) {
                Rectangle().fill(Color.clear).frame(height: 14)
                ForEach([(0.4, "LOW"), (0.5, "MOD"), (0.7, "HIGH"), (0.9, "ALL-OUT")], id: \.1) { pos, label in
                    GeometryReader { geo in
                        Text(label)
                            .font(.whoopChartAxis)
                            .tracking(0.8)
                            .foregroundStyle(Color.whoopGray400)
                            .position(x: geo.size.width * pos, y: 7)
                    }
                }
            }

            // Track + fill + marker
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    RoundedRectangle(cornerRadius: 6)
                        .fill(Color.whoopSurface2)
                        .frame(height: height)

                    RoundedRectangle(cornerRadius: 6)
                        .fill(LinearGradient(
                            colors: [Color.whoopStrainDim, Color.whoopStrain, Color.whoopStrainBright],
                            startPoint: .leading, endPoint: .trailing
                        ))
                        .frame(width: geo.size.width * (currentStrain / 21), height: height)
                        .animation(.spring(response: 0.5, dampingFraction: 0.7), value: currentStrain)

                    Circle()
                        .fill(Color.whoopBrightWhite)
                        .frame(width: 16, height: 16)
                        .position(x: max(8, geo.size.width * (currentStrain / 21)), y: height / 2)
                        .animation(.spring(response: 0.5, dampingFraction: 0.7), value: currentStrain)
                }
            }
            .frame(height: height)
        }
    }
}
```

### Sleep Stage Chart

```swift
struct SleepStageChart: View {
    struct Segment: Identifiable {
        let id = UUID()
        let stage: Stage
        let durationMin: Int
    }
    enum Stage {
        case awake, light, deep, rem
        var color: Color {
            switch self {
            case .awake: return Color.whoopGray600
            case .light: return Color.whoopLightCyan
            case .deep:  return Color.whoopDeepIndigo
            case .rem:   return Color.whoopREMPurple
            }
        }
    }

    let segments: [Segment]
    let startHour: String      // e.g. "11 PM"
    let endHour: String        // e.g. "7 AM"

    var totalMin: Int { segments.reduce(0) { $0 + $1.durationMin } }

    var body: some View {
        VStack(spacing: 6) {
            GeometryReader { geo in
                HStack(spacing: 1) {
                    ForEach(segments) { seg in
                        Rectangle()
                            .fill(seg.stage.color)
                            .frame(width: geo.size.width * CGFloat(seg.durationMin) / CGFloat(totalMin))
                    }
                }
            }
            .frame(height: 32)
            .clipShape(RoundedRectangle(cornerRadius: 4))

            HStack {
                Text(startHour)
                    .font(.whoopChartAxis)
                    .tracking(0.4)
                    .foregroundStyle(Color.whoopGray400)
                Spacer()
                Text(endHour)
                    .font(.whoopChartAxis)
                    .tracking(0.4)
                    .foregroundStyle(Color.whoopGray400)
            }
        }
    }
}
```

### Primary CTA (instrument-panel button)

```swift
struct WHOOPPrimaryButton: View {
    let label: String          // already uppercase
    var action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.whoopButton)
                .tracking(1.0)
                .foregroundStyle(Color.whoopCanvas)
                .frame(maxWidth: .infinity, minHeight: 48)
                .background(
                    RoundedRectangle(cornerRadius: 4)
                        .fill(pressed ? Color.whoopStrainBright : Color.whoopStrain)
                )
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .onLongPressGesture(minimumDuration: 0, perform: {}, onPressingChanged: { pressed = $0 })
        .sensoryFeedback(.impact(weight: .medium), trigger: pressed)
    }
}
```

### Outline Button

```swift
struct WHOOPOutlineButton: View {
    let label: String
    var action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.whoopButton)
                .tracking(1.0)
                .foregroundStyle(Color.whoopStrain)
                .frame(maxWidth: .infinity, minHeight: 48)
                .background(
                    RoundedRectangle(cornerRadius: 4)
                        .strokeBorder(Color.whoopStrain, lineWidth: 1.5)
                )
        }
        .buttonStyle(.plain)
    }
}
```

### Activity Row

```swift
struct ActivityRow: View {
    let icon: String       // SF Symbol name (we ship custom DIN-icons in production)
    let name: String
    let strain: Double
    let duration: String
    let startTime: String

    var body: some View {
        HStack(spacing: 12) {
            ZStack {
                Circle().strokeBorder(Color.whoopStrain, lineWidth: 1.5).frame(width: 40, height: 40)
                Image(systemName: icon).font(.system(size: 18)).foregroundStyle(Color.whoopStrain)
            }

            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.whoopCardTitle).foregroundStyle(Color.whoopBrightWhite)
                Text(String(format: "%.1f", strain))
                    .font(.whoopMetricValue)
                    .foregroundStyle(Color.whoopStrain)
            }

            Spacer()

            VStack(alignment: .trailing, spacing: 2) {
                Text(duration).font(.whoopBodySmall).foregroundStyle(Color.whoopSoftWhite)
                Text(startTime).font(.whoopChartAxis).foregroundStyle(Color.whoopGray400)
            }

            Image(systemName: "chevron.right").font(.system(size: 12)).foregroundStyle(Color.whoopGray600)
        }
        .padding(.horizontal, 16)
        .frame(height: 72)
        .background(Color.whoopCanvas)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.whoopDivider).frame(height: 0.5)
        }
    }
}
```

### Metric Card

```swift
struct MetricCard<Content: View>: View {
    let label: String
    @ViewBuilder var content: () -> Content

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            Text(label)
                .font(.whoopMetricLabel)
                .tracking(0.6)
                .foregroundStyle(Color.whoopGray400)
            content()
        }
        .padding(20)
        .background(
            RoundedRectangle(cornerRadius: 12)
                .fill(Color.whoopSurface1)
                .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.whoopDivider, lineWidth: 1))
        )
    }
}
```

### Live Workout Banner

```swift
struct LiveWorkoutBanner: View {
    let elapsed: String       // "32:14"
    let currentStrain: Double // 11.8

    var body: some View {
        HStack(spacing: 8) {
            Circle().fill(Color.whoopCanvas).frame(width: 8, height: 8)
                .opacity(pulsing ? 1.0 : 0.4)
                .animation(.easeInOut(duration: 0.6).repeatForever(autoreverses: true), value: pulsing)
            Text("LIVE — \(elapsed) · STRAIN \(String(format: "%.1f", currentStrain))")
                .font(.whoopMetaCaps)
                .tracking(0.6)
                .foregroundStyle(Color.whoopCanvas)
            Spacer()
        }
        .padding(.horizontal, 16)
        .frame(height: 28)
        .background(Color.whoopStrain)
        .shadow(color: Color.whoopStrain.opacity(0.32), radius: 24, y: 0)
        .onAppear { pulsing = true }
    }
    @State private var pulsing = false
}
```

### Day Switcher

```swift
struct DaySwitcher: View {
    let days: [(label: String, isToday: Bool)]
    @Binding var selectedIndex: Int

    var body: some View {
        HStack(spacing: 10) {
            ForEach(Array(days.enumerated()), id: \.offset) { idx, day in
                Button {
                    withAnimation(.spring(response: 0.2, dampingFraction: 0.8)) { selectedIndex = idx }
                } label: {
                    Text(day.label)
                        .font(.whoopChartAxis)
                        .tracking(0.4)
                        .foregroundStyle(idx == selectedIndex ? Color.whoopCanvas : Color.whoopGray400)
                        .frame(width: 36, height: 36)
                        .background(
                            Circle()
                                .fill(idx == selectedIndex ? Color.whoopStrain : Color.clear)
                                .overlay(Circle().strokeBorder(idx == selectedIndex ? .clear : Color.whoopGray600, lineWidth: 1))
                        )
                        .scaleEffect(idx == selectedIndex ? 1.08 : 1)
                }
                .sensoryFeedback(.selection, trigger: selectedIndex)
            }
        }
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor(Color.whoopSurface1)
        appearance.shadowColor = UIColor(Color.whoopDivider)

        // Active tint glow handled separately
        appearance.stackedLayoutAppearance.normal.iconColor = UIColor(Color.whoopGray600)
        appearance.stackedLayoutAppearance.normal.titleTextAttributes = [
            .font: UIFont(name: "DIN2014-Bold", size: 10) ?? .systemFont(ofSize: 10, weight: .bold),
            .kern: 0.8,
            .foregroundColor: UIColor(Color.whoopGray600),
        ]
        appearance.stackedLayoutAppearance.selected.iconColor = UIColor(Color.whoopStrain)
        appearance.stackedLayoutAppearance.selected.titleTextAttributes = [
            .font: UIFont(name: "DIN2014-Bold", size: 10) ?? .systemFont(ofSize: 10, weight: .bold),
            .kern: 0.8,
            .foregroundColor: UIColor(Color.whoopStrain),
        ]

        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            OverviewView() .tabItem { Label("OVERVIEW",  systemImage: "circle.dashed") }
            CoachingView() .tabItem { Label("COACHING",  systemImage: "person.crop.square") }
            CommunityView().tabItem { Label("COMMUNITY", systemImage: "person.3") }
            StatsView()    .tabItem { Label("STATS",     systemImage: "chart.bar") }
        }
        .tint(Color.whoopStrain)
    }
}
```

## 5. Motion & Haptics

```swift
// Recovery ring fill
.animation(.easeOut(duration: 1.2), value: animatedPercent)

// Strain marker slide
.animation(.spring(response: 0.5, dampingFraction: 0.7), value: currentStrain)

// CTA tap
.sensoryFeedback(.impact(weight: .medium), trigger: ctaPressed)

// Day switcher tap
.sensoryFeedback(.selection, trigger: selectedIndex)

// Sleep stage tap
.sensoryFeedback(.impact(weight: .light), trigger: tappedSegment)

// Sync complete
.sensoryFeedback(.success, trigger: syncComplete)

// Live banner pulse — see LiveWorkoutBanner above
```

## 6. SF Symbols Used (substitute when DIN-icons aren't available)

| Component | Symbol | Size |
|-----------|--------|------|
| Overview tab | `circle.dashed` / `.fill` | 22pt |
| Coaching tab | `person.crop.square` | 22pt |
| Community tab | `person.3` / `.fill` | 22pt |
| Stats tab | `chart.bar` / `.fill` | 22pt |
| Activity icon (run) | `figure.run` | 18pt |
| Activity icon (cycling) | `figure.outdoor.cycle` | 18pt |
| Activity icon (yoga) | `figure.yoga` | 18pt |
| Activity icon (strength) | `dumbbell.fill` | 18pt |
| Heart-rate live | `heart.fill` | 16pt |
| Battery | `battery.75` | 14pt |
| Sync | `arrow.triangle.2.circlepath` | 14pt |
| Search | `magnifyingglass` | 16pt |
| Row chevron | `chevron.right` | 12pt |
| Live banner dot | `circle.fill` | 8pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`). On iOS 16, fall back to `UIImpactFeedbackGenerator`.
- Support Dynamic Type on body, card titles, coaching headlines, large nav title. **Fix** all hero metrics (76pt Recovery, 56pt Strain, 48pt Sleep), tab labels, chart axes, metric labels — they are layout-sensitive.
- VoiceOver: read the Recovery ring as `"Recovery 73 percent, high recovery"`; the strain bar as `"Current strain 11.8 out of 21, high range"`; sleep chart as `"Total sleep 6 hours 12 minutes, 18 percent REM, 22 percent deep, 47 percent light, 13 percent awake"`.
- Contrast on dark: Bright White on `#0A0A0A` exceeds AAA. Strain Green `#00FF7B` on `#0A0A0A` meets AA at 18pt+ (use it only for short caps labels, not body text). Gray 400 `#A1A1AA` is for secondary text only.
- Reduce Motion: skip the Recovery ring fill (set instantly), the heart-rate beat pulse, and the live-banner dot pulse when `accessibilityReduceMotion` is true. Day switcher tap should not scale.
- No light mode: there is no `Color.whoopLightCanvas` — WHOOP is dark-canvas-only. Override `.preferredColorScheme(.dark)` at the root if needed.
- Tabular numerals: applied via `.monospacedDigit()` on every numeric value — non-negotiable for the cockpit aesthetic.
