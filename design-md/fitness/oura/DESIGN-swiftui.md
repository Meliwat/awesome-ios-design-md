# Oura (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Oura's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let ouraCanvas   = Color(red: 0.043, green: 0.043, blue: 0.059) // #0B0B0F
    static let ouraSurface1 = Color(red: 0.086, green: 0.086, blue: 0.110) // #16161C
    static let ouraSurface2 = Color(red: 0.118, green: 0.118, blue: 0.149) // #1E1E26
    static let ouraSurface3 = Color(red: 0.153, green: 0.153, blue: 0.184) // #27272F
    static let ouraDivider  = Color(red: 0.165, green: 0.165, blue: 0.200) // #2A2A33

    // MARK: - Text
    static let ouraTextPrimary   = Color.white                                  // #FFFFFF
    static let ouraTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.647) // #9A9AA5
    static let ouraTextTertiary  = Color(red: 0.416, green: 0.416, blue: 0.451) // #6A6A73

    // MARK: - Domain Accents (color = meaning)
    static let ouraReadiness     = Color(red: 0.310, green: 0.820, blue: 0.773) // #4FD1C5
    static let ouraReadinessDeep = Color(red: 0.216, green: 0.702, blue: 0.659) // #37B3A8
    static let ouraSleep         = Color(red: 0.486, green: 0.435, blue: 0.941) // #7C6FF0
    static let ouraSleepDeep     = Color(red: 0.388, green: 0.329, blue: 0.839) // #6354D6
    static let ouraActivity      = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623
    static let ouraActivityDeep  = Color(red: 0.851, green: 0.541, blue: 0.071) // #D98A12

    // MARK: - Semantic
    static let ouraNegativeDelta = Color(red: 0.878, green: 0.455, blue: 0.420) // #E0746B
}

enum OuraDomain {
    case readiness, sleep, activity
    var hue: Color { switch self { case .readiness: .ouraReadiness; case .sleep: .ouraSleep; case .activity: .ouraActivity } }
    var deep: Color { switch self { case .readiness: .ouraReadinessDeep; case .sleep: .ouraSleepDeep; case .activity: .ouraActivityDeep } }
    var label: String { switch self { case .readiness: "READINESS"; case .sleep: "SLEEP"; case .activity: "ACTIVITY" } }
}
```

## 2. Typography

Inter is open-source (SIL OFL). Bundle the TTFs via `Info.plist` (`UIAppFonts`). Enable tabular figures so scores never reflow.

```swift
extension Font {
    static let ouraRingScore  = Font.custom("Inter-Bold",     size: 52)
    static let ouraScreenTitle = Font.custom("Inter-Bold",    size: 26)
    static let ouraSection    = Font.custom("Inter-Bold",     size: 20)
    static let ouraScore2      = Font.custom("Inter-Bold",    size: 34)
    static let ouraCardTitle  = Font.custom("Inter-SemiBold", size: 17)
    static let ouraMetricVal  = Font.custom("Inter-SemiBold", size: 22)
    static let ouraContributor = Font.custom("Inter-SemiBold", size: 15)
    static let ouraBody       = Font.custom("Inter-Regular",  size: 15)
    static let ouraSubtitle   = Font.custom("Inter-Regular",  size: 13)
    static let ouraDelta      = Font.custom("Inter-SemiBold", size: 13)
    static let ouraDomainLabel = Font.custom("Inter-Bold",    size: 13)
    static let ouraButton     = Font.custom("Inter-SemiBold", size: 16)
    static let ouraTab        = Font.custom("Inter-SemiBold", size: 11)
    static let ouraCaption    = Font.custom("Inter-Regular",  size: 11)
}

extension View {
    /// Scores, deltas, metric values must use tabular figures.
    func ouraTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Score Ring (signature)

```swift
struct OuraScoreRing: View {
    let score: Int
    let domain: OuraDomain
    var diameter: CGFloat = 220
    var stroke: CGFloat = 12

    @State private var progress: CGFloat = 0
    @State private var shown: Int = 0

    var body: some View {
        ZStack {
            Circle()
                .stroke(Color.ouraSurface3, lineWidth: stroke)
            Circle()
                .trim(from: 0, to: progress)
                .stroke(domain.hue, style: StrokeStyle(lineWidth: stroke, lineCap: .round))
                .rotationEffect(.degrees(-90)) // start at 12 o'clock
            VStack(spacing: 4) {
                Text("\(shown)")
                    .font(.ouraRingScore).foregroundStyle(.white)
                    .ouraTabular().kerning(-1)
                Text(domain.label)
                    .font(.ouraDomainLabel).tracking(0.8)
                    .foregroundStyle(domain.hue)
            }
        }
        .frame(width: diameter, height: diameter)
        .onAppear {
            withAnimation(.easeOut(duration: 0.9)) { progress = CGFloat(score) / 100 }
            animateCount(to: score)
        }
    }

    private func animateCount(to target: Int) {
        let steps = 30
        for i in 0...steps {
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.9 * Double(i) / Double(steps)) {
                shown = Int(Double(target) * Double(i) / Double(steps))
            }
        }
    }
}
```

### Contributor Bar List (signature)

```swift
struct Contributor: Identifiable {
    let id = UUID()
    let name: String
    let normalized: CGFloat // 0...1
    let value: String
}

struct ContributorBarList: View {
    let contributors: [Contributor]
    let domain: OuraDomain

    var body: some View {
        VStack(spacing: 0) {
            ForEach(Array(contributors.enumerated()), id: \.element.id) { idx, c in
                HStack(spacing: 12) {
                    Text(c.name)
                        .font(.ouraContributor).foregroundStyle(.white)
                        .frame(width: 130, alignment: .leading)
                    GeometryReader { geo in
                        ZStack(alignment: .leading) {
                            Capsule().fill(Color.ouraSurface3).frame(height: 6)
                            Capsule().fill(domain.hue)
                                .frame(width: geo.size.width * c.normalized, height: 6)
                        }
                    }
                    .frame(height: 6)
                    Text(c.value)
                        .font(.ouraDelta).foregroundStyle(.white)
                        .ouraTabular().frame(width: 44, alignment: .trailing)
                }
                .frame(height: 44).padding(.horizontal, 16)
                if idx < contributors.count - 1 {
                    Rectangle().fill(Color.ouraDivider).frame(height: 1).padding(.leading, 16)
                }
            }
        }
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.ouraSurface1))
    }
}
```

### Metric Tile

```swift
struct OuraMetricTile: View {
    let label: String
    let value: String
    let unit: String
    let delta: String
    let domain: OuraDomain

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            Text(label.uppercased())
                .font(.ouraSubtitle).fontWeight(.bold).tracking(0.8)
                .foregroundStyle(Color.ouraTextSecondary)
            HStack(alignment: .firstTextBaseline, spacing: 4) {
                Text(value).font(.ouraMetricVal).foregroundStyle(.white).ouraTabular()
                Text(unit).font(.ouraSubtitle).foregroundStyle(Color.ouraTextSecondary)
            }
            HStack {
                Capsule().fill(domain.hue).frame(width: 40, height: 3) // sparkline stand-in
                Spacer()
                Text(delta).font(.ouraDelta).foregroundStyle(domain.hue).ouraTabular()
            }
        }
        .padding(16)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.ouraSurface1))
    }
}
```

### Insight Card

```swift
struct OuraInsightCard: View {
    let glyph: String
    let title: String
    let body: String
    let domain: OuraDomain

    var body: some View {
        HStack(alignment: .top, spacing: 14) {
            Image(systemName: glyph)
                .font(.system(size: 24)).foregroundStyle(domain.hue)
            VStack(alignment: .leading, spacing: 6) {
                Text(title).font(.ouraCardTitle).foregroundStyle(.white)
                Text(body).font(.ouraBody).foregroundStyle(Color.ouraTextSecondary)
            }
            Spacer()
        }
        .padding(18)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.ouraSurface1))
    }
}
```

### Primary Button

```swift
struct OuraPrimaryButton: View {
    let title: String
    let domain: OuraDomain
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.ouraButton).foregroundStyle(Color.ouraCanvas) // dark text on hue
                .padding(.vertical, 14).padding(.horizontal, 28)
                .frame(maxWidth: .infinity)
                .background(Capsule().fill(domain.hue))
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: title)
        .buttonStyle(OuraPressableStyle())
    }
}

struct OuraPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.97 : 1)
            .animation(.easeOut(duration: 0.2), value: configuration.isPressed)
    }
}
```

## 4. Tab Bar

`TabView` with a custom `UITabBarAppearance`. **Active tint is neutral white** — domain color never touches chrome.

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        a.backgroundColor = UIColor(Color.ouraCanvas).withAlphaComponent(0.82)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            TodayView().tabItem { Label("Today", systemImage: "circle.circle.fill") }
            VitalsView().tabItem { Label("Vitals", systemImage: "waveform.path.ecg") }
            MyHealthView().tabItem { Label("My Health", systemImage: "heart.text.square") }
            ExploreView().tabItem { Label("Explore", systemImage: "square.grid.2x2") }
        }
        .tint(.white) // neutral — domain hue lives only in content
    }
}
```

## 5. Motion

```swift
// Score reveal — arc + count-up land together (see OuraScoreRing.onAppear)
withAnimation(.easeOut(duration: 0.9)) { progress = CGFloat(score) / 100 }

// Contributor bars — stagger ~40ms per row
.transition(.scale(scale: 0, anchor: .leading).animation(.easeOut(duration: 0.5).delay(Double(idx) * 0.04)))

// Range switch (7D/30D/90D)
withAnimation(.easeInOut(duration: 0.35)) { dataset = newRange }

// Card tap
.buttonStyle(OuraPressableStyle())  // 200ms ease-out, scale 0.98

// Sync pull-to-refresh — a ring filling, then:
.sensoryFeedback(.success, trigger: didSync)
```

Haptics via `.sensoryFeedback` (iOS 17+): `.impact(flexibility: .soft)` on primary actions, `.success` on completed sync, `.selection` on range/tag changes.

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Readiness glyph | `bolt.heart` | 24pt |
| Sleep glyph | `moon.zzz` | 24pt |
| Activity glyph | `flame` | 24pt |
| HRV insight | `waveform.path.ecg` | 24pt |
| Heart rate | `heart` | 20pt |
| Temperature | `thermometer.medium` | 20pt |
| Add tag | `plus.circle` | 20pt |
| Share | `square.and.arrow.up` | 20pt |
| Info | `info.circle` | 20pt |
| Date chevron | `chevron.down` | 14pt |
| Ring battery | `circle.bottomhalf.filled` | 16pt |
| Today (tab) | `circle.circle.fill` | 24pt |
| Vitals (tab) | `waveform.path.ecg` | 24pt |
| My Health (tab) | `heart.text.square` | 24pt |
| Explore (tab) | `square.grid.2x2` | 24pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` needs iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Tabular figures: register Inter with the tabular numeral feature, or rely on `.monospacedDigit()` so the count-up animation and contributor columns never shift
- Dynamic Type: scale titles, contributors, body, insights; cap the ring score at ~60pt so it stays inside the ring; pin domain labels (13pt), deltas (13pt), axis captions (11pt), tab labels (11pt)
- VoiceOver: the score ring needs a combined label — "Readiness 82 out of 100"; expose contributor rows as "Resting Heart Rate, good" and the trend graph with an `.accessibilityChartDescriptor`
- Color is information: never rely on hue alone — pair every domain color with its uppercase text label and an icon so color-blind users still parse it
- Reduce Motion: replace the score sweep + count-up with the final values shown immediately; disable the staggered contributor-bar growth
- Contrast: `#9A9AA5` on `#0B0B0F` passes AA at 13pt+; validate the 11pt axis captions and bump toward `#AEAEB8` when "Increase Contrast" is on
- Dark-first: do not provide a light `ColorScheme`; lock the app to `.dark`
