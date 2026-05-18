# Garmin Connect (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Garmin Connect's data-cockpit visual language into paste-ready SwiftUI: `Color` extensions, `Font` extensions (Roboto + Roboto Condensed), `ViewModifier`s, and the signature activity-detail components.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — primary)
    static let gcCanvas    = Color.black                                       // #000000
    static let gcSurface1  = Color(red: 0.071, green: 0.078, blue: 0.090)      // #121417
    static let gcSurface2  = Color(red: 0.106, green: 0.118, blue: 0.133)      // #1B1E22
    static let gcSurface3  = Color(red: 0.137, green: 0.153, blue: 0.173)      // #23272C
    static let gcDivider   = Color(red: 0.165, green: 0.180, blue: 0.200)      // #2A2E33

    // MARK: - Canvas & Surfaces (Light — optional)
    static let gcLightCanvas  = Color.white                                    // #FFFFFF
    static let gcLightSurface = Color(red: 0.957, green: 0.961, blue: 0.969)   // #F4F5F7
    static let gcLightDivider = Color(red: 0.886, green: 0.898, blue: 0.914)   // #E2E5E9

    // MARK: - Text
    static let gcTextPrimary   = Color.white                                   // #FFFFFF
    static let gcTextSecondary = Color(red: 0.651, green: 0.675, blue: 0.702)  // #A6ACB3
    static let gcTextTertiary  = Color(red: 0.420, green: 0.443, blue: 0.471)  // #6B7178
    static let gcLightTextPrimary = Color(red: 0.102, green: 0.114, blue: 0.129) // #1A1D21

    // MARK: - Brand (single accent)
    static let gcBlue         = Color(red: 0.0,   green: 0.486, blue: 0.765)   // #007CC3
    static let gcBlueOnDark   = Color(red: 0.165, green: 0.624, blue: 0.839)   // #2A9FD6
    static let gcBluePressed  = Color(red: 0.0,   green: 0.408, blue: 0.624)   // #00689F

    // MARK: - Functional / Data-Viz (fixed meaning, theme-invariant)
    static let gcBodyBattery  = Color(red: 0.180, green: 0.659, blue: 0.878)   // #2EA8E0
    static let gcBodyBatteryLo = Color(red: 0.118, green: 0.435, blue: 0.659)  // #1E6FA8
    static let gcIntensity    = Color(red: 0.0,   green: 0.659, blue: 0.659)   // #00A8A8
    static let gcSteps        = Color(red: 0.784, green: 0.710, blue: 0.376)   // #C8B560
    static let gcZone1        = Color(red: 0.604, green: 0.627, blue: 0.651)   // #9AA0A6
    static let gcZone2        = Color(red: 0.298, green: 0.686, blue: 0.314)   // #4CAF50
    static let gcZone3        = Color(red: 0.784, green: 0.710, blue: 0.376)   // #C8B560
    static let gcZone4        = Color(red: 0.941, green: 0.627, blue: 0.188)   // #F0A030
    static let gcZone5        = Color(red: 0.898, green: 0.251, blue: 0.165)   // #E5402A

    // MARK: - Semantic
    static let gcSuccess = Color(red: 0.298, green: 0.686, blue: 0.314)        // #4CAF50
    static let gcWarning = Color(red: 0.941, green: 0.627, blue: 0.188)        // #F0A030
    static let gcError   = Color(red: 0.898, green: 0.251, blue: 0.165)        // #E5402A
    static let gcTrack   = Color(red: 0.137, green: 0.153, blue: 0.173)        // #23272C
}
```

## 2. Typography

Garmin pairs **Roboto** (UI/body) with **Roboto Condensed** (every meaningful number, tabular figures). Bundle both via `Info.plist` / `UIAppFonts` — both are Apache 2.0 licensed.

```swift
extension Font {
    // Roboto — UI / body / labels
    static let gcScreenTitle  = Font.custom("Roboto-Black",    size: 32)             // weight 900
    static let gcSection      = Font.custom("Roboto-Bold",     size: 20)
    static let gcBody         = Font.custom("Roboto-Medium",   size: 16)
    static let gcBodyRegular  = Font.custom("Roboto-Regular",  size: 16)
    static let gcMeta         = Font.custom("Roboto-Regular",  size: 14)
    static let gcEyebrow      = Font.custom("Roboto-Bold",     size: 12)             // UPPERCASE +0.5 tracking
    static let gcUnit         = Font.custom("Roboto-Medium",   size: 11)
    static let gcCaption      = Font.custom("Roboto-Medium",   size: 11)
    static let gcButton       = Font.custom("Roboto-Bold",     size: 15)             // UPPERCASE +0.5
    static let gcTab          = Font.custom("Roboto-Medium",   size: 10)
    static let gcAxis         = Font.custom("Roboto-Medium",   size: 10)

    // Roboto Condensed — metrics (always pair with .monospacedDigit())
    static let gcHeroMetric   = Font.custom("RobotoCondensed-Bold", size: 40)
    static let gcActivityName = Font.custom("RobotoCondensed-Bold", size: 26)
    static let gcStatValue    = Font.custom("RobotoCondensed-Bold", size: 22)
    static let gcStatValueLg  = Font.custom("RobotoCondensed-Bold", size: 28)
}

// Apply tabular figures to every numeric value
extension View {
    func gcTabular() -> some View { self.monospacedDigit() }
    func gcEyebrowStyle() -> some View {
        self.font(.gcEyebrow).textCase(.uppercase).tracking(0.5)
            .foregroundStyle(Color.gcTextSecondary)
    }
}
```

## 3. Signature Components

### Activity Detail Header

```swift
struct ActivityHeader: View {
    let type: String       // "RUNNING"
    let name: String       // "Morning Run"
    let when: String       // "Today at 6:42 AM · Cedar Loop Trail"

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            Text(type.uppercased())
                .font(.gcEyebrow).tracking(0.5)
                .foregroundStyle(Color.gcBlueOnDark)
            Text(name)
                .font(.gcActivityName)
                .foregroundStyle(Color.gcTextPrimary)
            Text(when)
                .font(.gcMeta)
                .foregroundStyle(Color.gcTextSecondary)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(.horizontal, 16)
        .padding(.bottom, 16)
    }
}
```

### GPS Route Map

```swift
struct RouteMap: View {
    /// Normalised 0...1 points of the GPS track.
    let points: [CGPoint]
    @State private var draw: CGFloat = 0

    var body: some View {
        GeometryReader { geo in
            ZStack {
                LinearGradient(colors: [Color(hex: 0x1A2329), Color(hex: 0x0C1216)],
                               startPoint: .topLeading, endPoint: .bottomTrailing)

                RoutePath(points: points)
                    .trim(from: 0, to: draw)
                    .stroke(Color.gcBlue,
                            style: StrokeStyle(lineWidth: 4, lineCap: .round, lineJoin: .round))
                    .shadow(color: Color.gcBlue.opacity(0.6), radius: 4)
                    .frame(width: geo.size.width, height: geo.size.height)

                if let s = points.first {
                    pin(.gcSuccess).position(x: s.x * geo.size.width, y: s.y * geo.size.height)
                }
                if let e = points.last {
                    pin(.gcError).position(x: e.x * geo.size.width, y: e.y * geo.size.height)
                }
            }
        }
        .frame(height: 168)
        .clipShape(RoundedRectangle(cornerRadius: 12))
        .padding(.horizontal, 16)
        .onAppear { withAnimation(.easeOut(duration: 0.7)) { draw = 1 } }
    }

    private func pin(_ c: Color) -> some View {
        Circle().fill(c).frame(width: 14, height: 14)
            .overlay(Circle().strokeBorder(.white, lineWidth: 3))
    }
}

struct RoutePath: Shape {
    let points: [CGPoint]
    func path(in rect: CGRect) -> Path {
        var p = Path()
        guard let first = points.first else { return p }
        p.move(to: CGPoint(x: first.x * rect.width, y: first.y * rect.height))
        for pt in points.dropFirst() {
            p.addLine(to: CGPoint(x: pt.x * rect.width, y: pt.y * rect.height))
        }
        return p
    }
}

extension Color {
    init(hex: UInt) {
        self.init(red: Double((hex >> 16) & 0xFF) / 255,
                  green: Double((hex >> 8) & 0xFF) / 255,
                  blue: Double(hex & 0xFF) / 255)
    }
}
```

### Stat Grid

```swift
struct Stat: Identifiable { let id = UUID(); let label, value, unit: String }

struct StatGrid: View {
    let stats: [Stat]
    private let cols = Array(repeating: GridItem(.flexible(), spacing: 1), count: 3)

    var body: some View {
        LazyVGrid(columns: cols, spacing: 1) {
            ForEach(stats) { s in
                VStack(alignment: .leading, spacing: 4) {
                    Text(s.label.uppercased())
                        .font(.system(size: 10, weight: .bold))
                        .tracking(0.5)
                        .foregroundStyle(Color.gcTextSecondary)
                    HStack(alignment: .firstTextBaseline, spacing: 3) {
                        Text(s.value)
                            .font(.gcStatValue).gcTabular()
                            .foregroundStyle(Color.gcTextPrimary)
                        Text(s.unit)
                            .font(.gcUnit)
                            .foregroundStyle(Color.gcTextTertiary)
                    }
                }
                .frame(maxWidth: .infinity, minHeight: 64, alignment: .leading)
                .padding(.vertical, 14).padding(.horizontal, 12)
                .background(Color.gcSurface1)
            }
        }
        .background(Color.gcDivider)
        .clipShape(RoundedRectangle(cornerRadius: 12))
        .padding(.horizontal, 16)
    }
}
```

### Training Status Card (Ring)

```swift
struct TrainingStatusCard: View {
    let progress: Double     // 0...1
    let status: String       // "Productive"
    let detail: String       // "VO₂ Max 52 · Load 388"
    @State private var fill: Double = 0

    var body: some View {
        HStack(spacing: 14) {
            ZStack {
                Circle().stroke(Color.gcTrack, lineWidth: 6)
                Circle()
                    .trim(from: 0, to: fill)
                    .stroke(Color.gcBlue, style: StrokeStyle(lineWidth: 6, lineCap: .round))
                    .rotationEffect(.degrees(-90))
            }
            .frame(width: 52, height: 52)

            VStack(alignment: .leading, spacing: 2) {
                Text("TRAINING STATUS")
                    .font(.system(size: 10, weight: .bold)).tracking(0.5)
                    .foregroundStyle(Color.gcTextSecondary)
                Text(status).font(.gcBody.weight(.bold)).foregroundStyle(Color.gcBlueOnDark)
                Text(detail).font(.gcUnit).foregroundStyle(Color.gcTextSecondary)
            }
            Spacer()
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.gcSurface1))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.gcDivider, lineWidth: 1))
        .padding(.horizontal, 16)
        .onAppear { withAnimation(.spring(response: 0.6, dampingFraction: 0.85)) { fill = progress } }
    }
}
```

### Body Battery Gauge

```swift
struct BodyBatteryCard: View {
    let value: Int           // 0...100
    @State private var w: CGFloat = 0

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack(alignment: .firstTextBaseline) {
                Text("Body Battery").font(.system(size: 13, weight: .semibold))
                    .foregroundStyle(Color.gcTextPrimary)
                Spacer()
                Text("\(value)").font(.gcStatValue).gcTabular()
                    .foregroundStyle(Color.gcBodyBattery)
            }
            GeometryReader { g in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.gcTrack)
                    Capsule()
                        .fill(LinearGradient(colors: [Color.gcBodyBatteryLo, Color.gcBodyBattery],
                                             startPoint: .leading, endPoint: .trailing))
                        .frame(width: g.size.width * w)
                }
            }
            .frame(height: 8)
            HStack {
                Text("0"); Spacer(); Text("Charged"); Spacer(); Text("100")
            }
            .font(.system(size: 10, weight: .medium))
            .foregroundStyle(Color.gcTextTertiary)
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.gcSurface1))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.gcDivider, lineWidth: 1))
        .padding(.horizontal, 16)
        .onAppear { withAnimation(.easeOut(duration: 0.6)) { w = CGFloat(value) / 100 } }
    }
}
```

### Heart Rate Zones

```swift
struct HRZone: Identifiable { let id = UUID(); let name: String; let frac: CGFloat; let dur: String; let color: Color }

struct HRZonesCard: View {
    let zones: [HRZone] = [
        .init(name: "Z2", frac: 0.26, dur: "9:48",  color: .gcZone2),
        .init(name: "Z3", frac: 0.54, dur: "22:30", color: .gcZone3),
        .init(name: "Z4", frac: 0.38, dur: "8:12",  color: .gcZone4),
        .init(name: "Z5", frac: 0.10, dur: "1:48",  color: .gcZone5),
    ]

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("Heart Rate Zones").font(.system(size: 13, weight: .semibold))
                .foregroundStyle(Color.gcTextPrimary).padding(.bottom, 4)
            ForEach(zones) { z in
                HStack(spacing: 10) {
                    Text(z.name).font(.gcCaption).foregroundStyle(Color.gcTextSecondary)
                        .frame(width: 30, alignment: .leading)
                    GeometryReader { g in
                        ZStack(alignment: .leading) {
                            RoundedRectangle(cornerRadius: 3).fill(Color.gcTrack)
                            RoundedRectangle(cornerRadius: 3).fill(z.color)
                                .frame(width: g.size.width * z.frac)
                        }
                    }
                    .frame(height: 10)
                    Text(z.dur).font(.gcCaption).gcTabular()
                        .foregroundStyle(Color.gcTextSecondary)
                        .frame(width: 40, alignment: .trailing)
                }
            }
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.gcSurface1))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.gcDivider, lineWidth: 1))
        .padding(.horizontal, 16)
    }
}
```

### Primary Button

```swift
struct GCPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title.uppercased())
                .font(.gcButton).tracking(0.5)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 14)
        }
        .background(Color.gcBlue)
        .clipShape(RoundedRectangle(cornerRadius: 6))
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct GCTabView: View {
    var body: some View {
        TabView {
            MyDayView().tabItem { Label("My Day", systemImage: "circle.circle.fill") }
            ChallengesView().tabItem { Label("Challenges", systemImage: "chart.line.uptrend.xyaxis") }
            CalendarView().tabItem { Label("Calendar", systemImage: "calendar") }
            MoreView().tabItem { Label("More", systemImage: "person.crop.circle") }
        }
        .tint(.gcBlueOnDark)            // active glyph/label is brightened Garmin Blue, no pill
        .toolbarBackground(Color.gcSurface1, for: .tabBar)
        .toolbarColorScheme(.dark, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Route line draws on
.onAppear { withAnimation(.easeOut(duration: 0.7)) { draw = 1 } }

// Ring / gauge fills from 0 with slight overshoot
withAnimation(.spring(response: 0.6, dampingFraction: 0.85)) { fill = progress }

// Bar fill
withAnimation(.easeOut(duration: 0.6)) { w = CGFloat(value) / 100 }

// Stat cells stagger-rise on first appearance
ForEach(Array(stats.enumerated()), id: \.offset) { i, _ in
    cell.opacity(shown ? 1 : 0).offset(y: shown ? 0 : 6)
        .animation(.easeOut(duration: 0.2).delay(Double(i) * 0.03), value: shown)
}

// Card expand (Splits / HR Zones)
withAnimation(.easeOut(duration: 0.22)) { expanded.toggle() }   // chevron .rotationEffect(.degrees(expanded ? 90 : 0))

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: selectedTab)
.sensoryFeedback(.success, trigger: didSync)
.sensoryFeedback(.selection, trigger: rangeSegment)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| My Day (tab) | `circle.circle` / `circle.circle.fill` | 22pt |
| Challenges (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Calendar (tab) | `calendar` | 22pt |
| More (tab) | `person.crop.circle` | 22pt |
| Back | `chevron.left` | 18pt |
| Settings | `gearshape` | 19pt |
| Share | `square.and.arrow.up` / `point.3.connected.trianglepath.dotted` | 19pt |
| Expand card | `chevron.right` → `chevron.down` | 16pt |
| Heart rate | `heart.fill` | 16pt |
| Pace / run | `figure.run` | 18pt |
| Cycling | `figure.outdoor.cycle` | 18pt |
| Elevation | `mountain.2.fill` | 16pt |
| Body Battery | `bolt.fill` | 16pt |
| Steps | `shoeprints.fill` | 16pt |
| Map / route | `map` | 18pt |
| Sync | `arrow.triangle.2.circlepath` | 18pt |
| Goal met | `checkmark.circle.fill` | 18pt |

(Garmin uses custom activity glyphs; SF Symbols above are the closest first-party mappings.)

## 7. Dark Mode

```swift
struct GCTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.gcCanvas : Color.gcLightCanvas)
            .foregroundStyle(scheme == .dark ? Color.gcTextPrimary : Color.gcLightTextPrimary)
            .tint(.gcBlueOnDark)
    }
}
extension View { func gcTheme() -> some View { modifier(GCTheme()) } }
```

Garmin is **dark-first**: ship the dark palette as the default. The light theme is optional. **Functional colors (HR zones, Body Battery `#2EA8E0`, Intensity `#00A8A8`, Steps `#C8B560`) never change between modes** — their color *is* the datum. Only chrome (canvas, surfaces, dividers, text) inverts. On true black, brighten brand-blue text/active to `#2A9FD6` for contrast.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Roboto + Roboto Condensed TTFs via `Info.plist` `UIAppFonts` — both Apache 2.0, free to redistribute
- Always apply `.monospacedDigit()` to metric values so split tables and stat columns align and live values don't shift layout
- Dynamic Type: scale screen titles, section headings, body, meta; keep stat values, units, tab labels, chart axis text, and eyebrow labels at FIXED sizes (they live in tight cells)
- VoiceOver: combine label + value + unit into one announcement, e.g. "Average pace, 5 minutes 2 seconds per kilometre"; announce HR-zone rows as "Zone 4, threshold, 8 minutes 12 seconds"; the route map should expose a summary "Route, 8.42 kilometres, Cedar Loop Trail"
- Color is never the only signal: HR zones carry a Z2…Z5 text label beside the colored bar; goal-met state pairs `#4CAF50` with a checkmark glyph
- Contrast: `#FFFFFF` on `#000000` is maximal; `#2A9FD6` on `#000000` passes AA — never use raw `#007CC3` for small text on black
- Reduce Motion: skip the route draw-on (show full line), skip ring/bar fill-from-0 (set final value with a 120ms crossfade), skip stat-cell stagger-rise
- Reduce Transparency: replace the blurred tab-bar background with solid `#121417`
- Dark mode: surfaces step `#000000 → #121417 → #1B1E22`; floating layers (sheets, menus) get `#1B1E22` + `rgba(0,0,0,0.6) 0 8px 24px`
