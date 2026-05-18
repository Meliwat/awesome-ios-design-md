# Fitbit (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Fitbit's encouraging health-dashboard language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views (rings, tiles, scores).

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    init(hex: UInt) {
        self.init(red: Double((hex >> 16) & 0xFF) / 255,
                  green: Double((hex >> 8) & 0xFF) / 255,
                  blue: Double(hex & 0xFF) / 255)
    }

    // MARK: - Canvas & Surfaces (Dark — default)
    static let fbCanvas    = Color(hex: 0x001017)
    static let fbSurface1  = Color(hex: 0x002A3A)
    static let fbSurface2  = Color(hex: 0x00384D)
    static let fbSurface3  = Color(hex: 0x0A475E) // ring track
    static let fbDivider   = Color(hex: 0x143E50)

    // MARK: - Canvas & Surfaces (Light — secondary)
    static let fbCanvasLight   = Color.white
    static let fbSurface1Light = Color(hex: 0xF2F7F8)
    static let fbSurface3Light = Color(hex: 0xE3EEF0)
    static let fbDividerLight  = Color(hex: 0xDCE7E9)

    // MARK: - Brand
    static let fbTeal        = Color(hex: 0x00B0B9)
    static let fbTealBright  = Color(hex: 0x21D9CE) // on-dark text/link
    static let fbTealPressed = Color(hex: 0x008A91)

    // MARK: - Per-Metric Hues (fixed across themes)
    static let fbSteps     = Color(hex: 0x00B0B9)
    static let fbHeartRate = Color(hex: 0xFF6B81)
    static let fbSleep     = Color(hex: 0x7C5CFF)
    static let fbReadiness = Color(hex: 0xB8E986)
    static let fbZone      = Color(hex: 0xFF8A3D)
    static let fbCalories  = Color(hex: 0xFFC233)
    static let fbSpo2      = Color(hex: 0x4FC3F7)

    // MARK: - Text
    static let fbTextPrimary    = Color(hex: 0xEAF6F9)
    static let fbTextSecondary  = Color(hex: 0x8AAEB8)
    static let fbTextTertiary   = Color(hex: 0x5C808B)
    static let fbTextPrimaryLt  = Color(hex: 0x0B2A33)

    // MARK: - Semantic
    static let fbSuccess = Color(hex: 0x3FC7A6)
    static let fbWarning = Color(hex: 0xFF8A3D)
    static let fbAlert   = Color(hex: 0xFF6B81)
}

extension Color { static let fbTealTint = Color.fbTeal.opacity(0.15) }
```

## 2. Typography

Fitbit's brand sans → use **DM Sans** (SIL OFL) as the open stand-in. Bundle via `Info.plist`. The number is always the hero.

```swift
extension Font {
    static let fbMetricHero = Font.custom("DMSans-Bold",    size: 44)
    static let fbScreenTitle = Font.custom("DMSans-Bold",   size: 32)
    static let fbTileValue  = Font.custom("DMSans-Bold",    size: 26)
    static let fbGreeting   = Font.custom("DMSans-Bold",    size: 22)
    static let fbCardTitle  = Font.custom("DMSans-Bold",    size: 18)
    static let fbBody       = Font.custom("DMSans-Regular", size: 16)
    static let fbValueInline = Font.custom("DMSans-SemiBold", size: 15)
    static let fbLabel      = Font.custom("DMSans-Regular", size: 14)
    static let fbUnit       = Font.custom("DMSans-SemiBold", size: 13)
    static let fbCaption    = Font.custom("DMSans-SemiBold", size: 12)
    static let fbTab        = Font.custom("DMSans-SemiBold", size: 10)
    static let fbButton     = Font.custom("DMSans-Bold",    size: 15)
}
```

## 3. Signature Components

### Progress Ring (the signature shape)

```swift
struct MetricRing: View {
    let progress: Double        // 0...1 (can exceed 1)
    let color: Color
    var lineWidth: CGFloat = 11
    @State private var animated: Double = 0

    var body: some View {
        ZStack {
            Circle().stroke(Color.fbSurface3, lineWidth: lineWidth)
            Circle()
                .trim(from: 0, to: min(animated, 1))
                .stroke(color, style: StrokeStyle(lineWidth: lineWidth, lineCap: .round))
                .rotationEffect(.degrees(-90))
        }
        .onAppear {
            withAnimation(.easeOut(duration: 0.9)) { animated = progress }
        }
    }
}
```

### Steps Ring Hero

```swift
struct StepsHero: View {
    let steps: Int
    let goal: Int
    let headline: String       // "You're close!"
    let support: String        // "2,158 steps to hit your daily goal."
    let trend: String          // "▲ 12% vs last week"

    var body: some View {
        HStack(spacing: 18) {
            ZStack {
                MetricRing(progress: Double(steps) / Double(goal), color: .fbSteps)
                    .frame(width: 116, height: 116)
                VStack(spacing: 1) {
                    Text("\(steps)").font(.fbTileValue).foregroundStyle(Color.fbTextPrimary)
                    Text("of \(goal) steps").font(.system(size: 11)).foregroundStyle(Color.fbTextSecondary)
                }
            }
            VStack(alignment: .leading, spacing: 3) {
                Text(headline).font(.fbValueInline).foregroundStyle(Color.fbTextPrimary)
                Text(support).font(.system(size: 13)).foregroundStyle(Color.fbTextSecondary)
                Text(trend)
                    .font(.fbCaption).foregroundStyle(Color.fbTealBright)
                    .padding(.horizontal, 10).padding(.vertical, 5)
                    .background(Capsule().fill(Color.fbTealTint))
                    .padding(.top, 6)
            }
            Spacer()
        }
        .padding(22)
        .background(RoundedRectangle(cornerRadius: 24).fill(Color.fbSurface1))
        .padding(.horizontal, 20)
    }
}
```

### Metric Tile

```swift
struct MetricTile: View {
    let metricColor: Color
    let icon: String           // SF Symbol
    let label: String
    let value: String          // "68"
    let unit: String?          // "bpm"
    let sub: String            // "Resting · 52–148 today"

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 7) {
                RoundedRectangle(cornerRadius: 7).fill(metricColor)
                    .frame(width: 24, height: 24)
                    .overlay(Image(systemName: icon).font(.system(size: 13, weight: .bold))
                        .foregroundStyle(Color.fbCanvas))
                Text(label).font(.fbCaption).foregroundStyle(Color.fbTextSecondary)
            }
            HStack(alignment: .firstTextBaseline, spacing: 3) {
                Text(value).font(.fbTileValue).foregroundStyle(Color.fbTextPrimary)
                if let unit { Text(unit).font(.fbUnit).foregroundStyle(Color.fbTextSecondary) }
            }
            .padding(.top, 10)
            Text(sub).font(.fbCaption.weight(.regular)).foregroundStyle(Color.fbTextTertiary)
                .padding(.top, 3)
        }
        .padding(16)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(RoundedRectangle(cornerRadius: 20).fill(Color.fbSurface1))
    }
}
```

### Score Badge (Sleep / Readiness)

```swift
struct ScoreBadge: View {
    let score: Int             // 0...100
    let color: Color
    var size: CGFloat = 60

    var body: some View {
        Circle()
            .stroke(color, lineWidth: 4)
            .frame(width: size, height: size)
            .overlay(
                Text("\(score)")
                    .font(.system(size: size * 0.36, weight: .bold))
                    .foregroundStyle(color)
            )
    }
}
```

### Daily Readiness Card

```swift
struct ReadinessCard: View {
    let score: Int
    let message: String

    var body: some View {
        HStack(spacing: 16) {
            ScoreBadge(score: score, color: .fbReadiness, size: 60)
            VStack(alignment: .leading, spacing: 3) {
                Text("Daily Readiness").font(.fbValueInline).foregroundStyle(Color.fbTextPrimary)
                Text(message).font(.system(size: 12)).foregroundStyle(Color(hex: 0xA9D9C8))
            }
            Spacer()
        }
        .padding(18)
        .background(
            RoundedRectangle(cornerRadius: 20).fill(
                LinearGradient(colors: [Color(hex: 0x0E3A2E), Color(hex: 0x14564A)],
                    startPoint: .topLeading, endPoint: .bottomTrailing))
        )
        .padding(.horizontal, 20)
    }
}
```

### Primary Pill Button

```swift
struct FitbitPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.fbButton).foregroundStyle(Color.fbCanvas) // deep ink on teal
                .frame(maxWidth: .infinity).frame(height: 52)
                .background(Capsule().fill(Color.fbTeal))
        }
        .buttonStyle(PressableScale())
    }
}

struct PressableScale: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .background(configuration.isPressed ? Color.fbTealPressed : .clear)
            .clipShape(Capsule())
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}
```

### Count-Up Number

```swift
struct CountUpText: View {
    let target: Int
    var font: Font = .fbMetricHero
    @State private var value = 0
    var body: some View {
        Text("\(value)").font(font).foregroundStyle(Color.fbTextPrimary)
            .onAppear {
                withAnimation(.easeOut(duration: 0.7)) { value = target }
            }
            .contentTransition(.numericText())
    }
}
```

## 4. Bottom Tab Bar

```swift
struct FitbitTabView: View {
    var body: some View {
        TabView {
            TodayView().tabItem { Label("Today", systemImage: "circle.dashed") }
            CoachView().tabItem { Label("Coach", systemImage: "figure.run") }
            CommunityView().tabItem { Label("Community", systemImage: "person.2.fill") }
            YouView().tabItem { Label("You", systemImage: "person.crop.square") }
        }
        .tint(.fbTealBright)
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.fbCanvas)
            a.shadowColor = UIColor(Color.fbDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Ring fill sweep (on appear) — 900ms ease-out
withAnimation(.easeOut(duration: 0.9)) { animatedProgress = progress }

// Number count-up — 700ms ease-out + numericText transition
withAnimation(.easeOut(duration: 0.7)) { value = target }
// .contentTransition(.numericText())

// Today card stagger-in (index-based delay)
.opacity(appeared ? 1 : 0)
.offset(y: appeared ? 0 : 8)
.onAppear {
    withAnimation(.easeOut(duration: 0.35).delay(Double(index) * 0.04)) { appeared = true }
}

// Tile tap press
.scaleEffect(pressed ? 0.97 : 1)
withAnimation(.easeOut(duration: 0.12)) { pressed.toggle() }

// Goal celebration — ring pulse + confetti + success haptic
withAnimation(.spring(response: 0.4, dampingFraction: 0.5)) { celebrate = true }

// Range switch — chart cross-fade
withAnimation(.easeInOut(duration: 0.4)) { range = newRange }

// Haptics
.sensoryFeedback(.success, trigger: goalReached)
.sensoryFeedback(.selection, trigger: selectedRange)
.sensoryFeedback(.impact(weight: .light), trigger: tilePressed)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Today (tab) | `circle.dashed` | 22pt |
| Coach (tab) | `figure.run` | 22pt |
| Community (tab) | `person.2` / `person.2.fill` | 22pt |
| You (tab) | `person.crop.square` | 22pt |
| Steps | `figure.walk` | 14pt |
| Heart Rate | `heart.fill` | 14pt |
| Sleep | `moon.fill` | 14pt |
| Active Zone | `bolt.fill` | 14pt |
| Calories | `flame.fill` | 14pt |
| Readiness | `checkmark.seal.fill` | 14pt |
| SpO2 | `lungs.fill` | 14pt |
| Distance | `location.fill` | 14pt |
| Trend up | `arrow.up.right` | 12pt |
| Trend down | `arrow.down.right` | 12pt |
| Back | `chevron.left` | 22pt |
| Share | `square.and.arrow.up` | 18pt |
| More | `ellipsis` | 18pt |
| Add / Log | `plus` | 18pt |
| Settings | `gearshape` | 18pt |
| Notifications | `bell` | 20pt |

## 7. Dark Mode

```swift
struct FitbitTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.fbCanvas : Color.fbCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.fbTextPrimary : Color.fbTextPrimaryLt)
    }
}
extension View { func fitbitTheme() -> some View { modifier(FitbitTheme()) } }
```

Fitbit is dark-friendly with a teal cast (`#001017`/`#002A3A`). The light theme swaps canvas → `#FFFFFF` and primary text → `#0B2A33`. **Every per-metric hue is theme-invariant** — steps teal, HR coral, sleep purple, readiness lime, zone orange, calories gold are identical in both modes. On dark, the brand teal renders as the brighter `#21D9CE` for text/links/active tab so it stays legible; on light, use the base `#00B0B9` (or `#008A91` for small text).

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` & `.contentTransition(.numericText())` need iOS 17; fall back to `UINotificationFeedbackGenerator` and a manual count-up timer)
- Bundle DM Sans TTFs (SIL OFL) via `Info.plist` `UIAppFonts` — free to distribute
- Dynamic Type: scale greeting, card titles, body, labels; let hero/tile numbers scale one step then `minimumScaleFactor(0.6)`; keep unit suffixes, tab labels, and trend-chip text FIXED; ring center text scales within fixed ring geometry
- **Color is not the only signal**: per-metric hues are a mnemonic, but always pair them with the metric icon + text label (a colorblind user must still tell sleep from heart rate — never rely on purple-vs-coral alone)
- VoiceOver: rings announce "Steps: 7,842 of 10,000, 78 percent"; tiles announce "Heart Rate, 68 beats per minute, resting"; the readiness card reads the score + the full recommendation sentence
- Health data is sensitive: gate detailed health screens behind biometric where appropriate; never log raw biometric values; respect HealthKit authorization scopes
- Contrast: `#EAF6F9` on `#001017` passes WCAG AA; deep ink `#001017` on `#00B0B9` passes AA for the pill; verify each metric chip's glyph (`#001017`) on its hue passes AA — lime `#B8E986` and gold `#FFC233` need the dark glyph, never white
- Reduce Motion: disable the ring sweep (show final value instantly), the count-up (show the number directly), and confetti; keep the goal-hit haptic
- Reduce Transparency: the 96% tab bar becomes fully opaque
- Dark mode: primary in practice — verify surface steps (`#002A3A`/`#00384D`/`#0A475E`) keep ≥3:1 separation; floating sheets get a 1pt `#143E50` border since shadows are soft on `#001017`
