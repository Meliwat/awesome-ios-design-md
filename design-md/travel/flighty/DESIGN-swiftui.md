# Flighty (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Flighty's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let fltCanvas   = Color(red: 0.043, green: 0.043, blue: 0.059)  // #0B0B0F
    static let fltSurface1 = Color(red: 0.102, green: 0.102, blue: 0.122)  // #1A1A1F
    static let fltSurface2 = Color(red: 0.133, green: 0.133, blue: 0.157)  // #222228
    static let fltSurface3 = Color(red: 0.173, green: 0.173, blue: 0.204)  // #2C2C34
    static let fltDivider  = Color(red: 0.180, green: 0.180, blue: 0.212)  // #2E2E36

    // MARK: - Text
    static let fltTextPrimary   = Color.white                              // #FFFFFF
    static let fltTextSecondary = Color(red: 0.557, green: 0.557, blue: 0.588) // #8E8E96
    static let fltTextTertiary  = Color(red: 0.353, green: 0.353, blue: 0.384) // #5A5A62

    // MARK: - Brand
    static let fltBlue        = Color(red: 0.039, green: 0.518, blue: 1.000) // #0A84FF
    static let fltBluePressed = Color(red: 0.000, green: 0.400, blue: 0.800) // #0066CC

    // MARK: - Status (strict semantics)
    static let fltOnTime    = Color(red: 0.188, green: 0.820, blue: 0.345)  // #30D158
    static let fltDelay     = Color(red: 1.000, green: 0.839, blue: 0.039)  // #FFD60A
    static let fltCancelled = Color(red: 1.000, green: 0.271, blue: 0.227)  // #FF453A

    // MARK: - Map
    static let fltMapLand      = Color(red: 0.086, green: 0.086, blue: 0.106) // #16161B
    static let fltMapGraticule = Color(red: 0.122, green: 0.122, blue: 0.149) // #1F1F26
}

extension Color {
    static let fltBlueGlow = Color.fltBlue.opacity(0.45)
}
```

## 2. Typography

Flighty uses SF Pro — already the system font. Use `.system(design: .default)` with tabular figures for all flight data, or substitute Inter.

```swift
extension Font {
    static let fltTimeHero  = Font.system(size: 32, weight: .bold).monospacedDigit()
    static let fltTitleLarge = Font.system(size: 28, weight: .bold)
    static let fltRouteCode = Font.system(size: 24, weight: .bold)
    static let fltSection   = Font.system(size: 22, weight: .bold)
    static let fltCardTitle = Font.system(size: 17, weight: .semibold)
    static let fltBody      = Font.system(size: 15, weight: .regular)
    static let fltButton    = Font.system(size: 16, weight: .semibold)
    static let fltStatus    = Font.system(size: 13, weight: .bold)
    static let fltGate      = Font.system(size: 15, weight: .semibold).monospaced()
    static let fltMeta      = Font.system(size: 13, weight: .regular)
    static let fltOnTimePct = Font.system(size: 20, weight: .bold).monospacedDigit()
    static let fltTab       = Font.system(size: 11, weight: .semibold)
    static let fltTimestamp = Font.system(size: 11, weight: .semibold).monospaced()
}
```

## 3. Signature Components

### Flight Status Chip

```swift
struct StatusChip: View {
    enum Status { case onTime, delayed(Int), cancelled }
    let status: Status

    private var color: Color {
        switch status {
        case .onTime: .fltOnTime
        case .delayed: .fltDelay
        case .cancelled: .fltCancelled
        }
    }
    private var label: String {
        switch status {
        case .onTime: "ON TIME"
        case .delayed(let m): "DELAYED \(m)m"
        case .cancelled: "CANCELLED"
        }
    }

    var body: some View {
        Text(label)
            .font(.fltStatus)
            .foregroundStyle(color)
            .padding(.vertical, 5)
            .padding(.horizontal, 10)
            .background(Capsule().fill(color.opacity(0.15)))
            .accessibilityLabel(label.replacingOccurrences(of: "m", with: " minutes"))
    }
}
```

### Live Flight Map Arc

```swift
import MapKit

struct FlightArc: Shape {
    let origin: CGPoint        // normalized 0...1 in the view
    let destination: CGPoint
    let progress: CGFloat      // 0...1 flown fraction

    func path(in rect: CGRect) -> Path {
        let o = CGPoint(x: origin.x * rect.width, y: origin.y * rect.height)
        let d = CGPoint(x: destination.x * rect.width, y: destination.y * rect.height)
        let mid = CGPoint(x: (o.x + d.x) / 2, y: min(o.y, d.y) - rect.height * 0.18) // arched control
        var p = Path()
        p.move(to: o)
        p.addQuadCurve(to: d, control: mid)
        return p.trimmedPath(from: 0, to: progress)
    }
}

struct LiveFlightMap: View {
    let origin: CGPoint
    let destination: CGPoint
    @State private var drawn: CGFloat = 0
    let livePosition: CGFloat   // 0...1

    var body: some View {
        ZStack {
            Color.fltCanvas // map host; swap for a styled MKMapView / dark tile layer

            // Remaining (dashed, dim)
            FlightArc(origin: origin, destination: destination, progress: 1)
                .stroke(Color.fltBlue.opacity(0.35),
                        style: StrokeStyle(lineWidth: 2, dash: [4, 5]))

            // Flown (solid, glowing)
            FlightArc(origin: origin, destination: destination, progress: drawn)
                .stroke(Color.fltBlue, style: StrokeStyle(lineWidth: 2.5, lineCap: .round))
                .shadow(color: .fltBlueGlow, radius: 18)

            // Plane marker with glow halo
            GeometryReader { geo in
                let pt = pointOnArc(in: geo.size, at: livePosition)
                Image(systemName: "airplane")
                    .font(.system(size: 18, weight: .black))
                    .foregroundStyle(.white)
                    .shadow(color: .fltBlueGlow, radius: 10)
                    .position(pt)
            }
        }
        .onAppear {
            withAnimation(.easeOut(duration: 0.9)) { drawn = livePosition }
        }
    }

    private func pointOnArc(in size: CGSize, at t: CGFloat) -> CGPoint {
        let o = CGPoint(x: origin.x * size.width, y: origin.y * size.height)
        let d = CGPoint(x: destination.x * size.width, y: destination.y * size.height)
        let c = CGPoint(x: (o.x + d.x) / 2, y: min(o.y, d.y) - size.height * 0.18)
        let mt = 1 - t
        return CGPoint(
            x: mt * mt * o.x + 2 * mt * t * c.x + t * t * d.x,
            y: mt * mt * o.y + 2 * mt * t * c.y + t * t * d.y
        )
    }
}
```

### Flight Card

```swift
struct FlightCard: View {
    let airline: String
    let flightNo: String
    let originCode: String
    let destCode: String
    let depTime: String
    let arrTime: String
    let depGate: String
    let arrGate: String
    let meta: String
    let status: StatusChip.Status

    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            HStack {
                Text("\(airline) · \(flightNo)").font(.fltCardTitle).foregroundStyle(.white)
                Spacer()
                StatusChip(status: status)
            }
            HStack(alignment: .center, spacing: 16) {
                Text(originCode).font(.fltRouteCode).foregroundStyle(.white)
                Image(systemName: "airplane")
                    .font(.system(size: 14))
                    .foregroundStyle(Color.fltBlue)
                    .frame(maxWidth: .infinity)
                Text(destCode).font(.fltRouteCode).foregroundStyle(.white)
            }
            HStack(alignment: .top) {
                VStack(alignment: .leading, spacing: 4) {
                    Text(depTime).font(.fltTimeHero).foregroundStyle(.white)
                    Text(depGate).font(.fltGate).foregroundStyle(.fltTextSecondary)
                }
                Spacer()
                VStack(alignment: .trailing, spacing: 4) {
                    Text(arrTime).font(.fltTimeHero).foregroundStyle(.white)
                    Text(arrGate).font(.fltGate).foregroundStyle(.fltTextSecondary)
                }
            }
            Text(meta).font(.fltMeta).foregroundStyle(.fltTextSecondary)
        }
        .padding(18)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.fltSurface1))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.fltDivider, lineWidth: 1))
        .shadow(color: .black.opacity(0.4), radius: 16, y: 4)
    }
}
```

### On-Time Percentage Ring

```swift
struct OnTimeRing: View {
    let percent: Int   // 0...100
    var body: some View {
        ZStack {
            Circle().stroke(Color.fltDivider, lineWidth: 3)
            Circle()
                .trim(from: 0, to: CGFloat(percent) / 100)
                .stroke(Color.fltOnTime, style: StrokeStyle(lineWidth: 3, lineCap: .round))
                .rotationEffect(.degrees(-90))
            VStack(spacing: 0) {
                Text("\(percent)%").font(.fltOnTimePct).foregroundStyle(.white)
                Text("on time").font(.fltTimestamp).foregroundStyle(.fltTextSecondary)
            }
        }
        .frame(width: 72, height: 72)
    }
}
```

### Primary Button

```swift
struct FltPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.fltButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 15)
                .background(RoundedRectangle(cornerRadius: 14).fill(Color.fltBlue))
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: title)
        .buttonStyle(FltPressableStyle())
    }
}

struct FltPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor(Color.fltCanvas).withAlphaComponent(0.92)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            FlightsView().tabItem { Label("Flights", systemImage: "airplane") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            AirportView().tabItem { Label("Airport", systemImage: "building.2.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.fill") }
        }
        .tint(.fltBlue) // active = Flighty Blue
    }
}
```

## 5. Motion

```swift
// Arc draw-in (see LiveFlightMap)
withAnimation(.easeOut(duration: 0.9)) { drawn = livePosition }

// Plane advance — animate the live position; the marker follows the curve
withAnimation(.linear(duration: 1)) { livePosition = updated }

// Status change — cross-fade chip color + single medium haptic
.animation(.easeInOut(duration: 0.3), value: status)
.sensoryFeedback(.impact(weight: .medium), trigger: statusKey)

// Latest timeline node pulse — repeating scale on a glow ring
// Card press — scale 0.98 spring (see FltPressableStyle)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Plane / arc | `airplane` | 14-18pt |
| Search | `magnifyingglass` | 18pt |
| Refresh | `arrow.clockwise` | 20pt |
| Notify | `bell` / `bell.fill` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Open map | `map` / `map.fill` | 22pt |
| Add flight | `plus` | 20pt |
| Gate / terminal | `door.left.hand.open` | 16pt |
| On-time | `checkmark.circle.fill` | 16pt |
| Delay | `clock.badge.exclamationmark` | 16pt |
| Flights (tab) | `airplane` | 24pt |
| Airport (tab) | `building.2.fill` | 24pt |
| Profile (tab) | `person.fill` | 24pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`). Live Activities require iOS 16.1+ (`ActivityKit`) — mirror the in-app status bar in the widget.
- Support Dynamic Type on flight times, route codes, body — pin status chips, gate/terminal, timeline timestamps, and tab labels (tabular, layout-sensitive)
- VoiceOver: never rely on color alone for status — set `.accessibilityLabel("On time")` / `"Delayed 22 minutes"` / `"Cancelled"` on the chip; describe the arc as `"Flight 62% complete, en route from SFO to JFK"`
- Tabular figures: use `.monospacedDigit()` so live time updates don't shift layout or confuse VoiceOver re-reads
- Contrast: `#8E8E96` on `#0B0B0F` passes WCAG AA at 13pt+; validate amber `#FFD60A` text on its 15%-tint fill and bump opacity if needed for accessibility builds
- The blue arc glow is decorative — ensure the route is also stated textually so the visual is not the only conveyance of progress
