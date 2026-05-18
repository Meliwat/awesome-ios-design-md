# Zwift (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Zwift's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, glass `ViewModifier`s, and example views for the rider HUD over a 3D world.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand / Action
    static let zwiftOrange        = Color(red: 0.988, green: 0.404, blue: 0.098) // #FC6719
    static let zwiftOrangePressed = Color(red: 0.851, green: 0.325, blue: 0.055) // #D9530E
    static let zwiftOrangeSoft    = Color(red: 0.227, green: 0.129, blue: 0.063) // #3A2110

    // MARK: - Ride Metric Semantics (FIXED — do not remap)
    static let zwiftPower    = Color(red: 0.988, green: 0.404, blue: 0.098) // #FC6719 (power = orange)
    static let zwiftCadence  = Color(red: 0.910, green: 0.773, blue: 0.278) // #E8C547
    static let zwiftHeart    = Color(red: 0.941, green: 0.255, blue: 0.243) // #F0413E
    static let zwiftWkg      = Color(red: 0.169, green: 0.831, blue: 0.851) // #2BD4D9

    // MARK: - App-Shell Surfaces (solid, off-ride)
    static let zwiftCanvas   = Color(red: 0.086, green: 0.086, blue: 0.086) // #161616
    static let zwiftCard     = Color(red: 0.122, green: 0.122, blue: 0.122) // #1F1F1F
    static let zwiftSurface2 = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let zwiftSurface3 = Color(red: 0.208, green: 0.208, blue: 0.208) // #353535
    static let zwiftDivider  = Color(red: 0.188, green: 0.188, blue: 0.188) // #303030

    // MARK: - Text
    static let zwiftTextPrimary   = Color.white                                   // #FFFFFF
    static let zwiftTextSecondary = Color(red: 0.659, green: 0.667, blue: 0.678) // #A8AAAD
    static let zwiftTextTertiary  = Color(red: 0.424, green: 0.431, blue: 0.447) // #6C6E72

    // MARK: - Semantic
    static let zwiftSuccess = Color(red: 0.341, green: 0.784, blue: 0.302) // #57C84D
}
```

Glass overlay tints (use over `.ultraThinMaterial`):

```swift
extension ShapeStyle where Self == Color {
    static var zwiftGlass: Color      { Color.black.opacity(0.60) }   // HUD tiles / banner
    static var zwiftGlassHeavy: Color { Color(white: 0.07).opacity(0.92) } // action bar
    static var zwiftGlassStroke: Color { Color.white.opacity(0.10) }
}
```

## 2. Typography

Two faces: **Barlow Semi Condensed** (the closest free analog to Zwift's condensed numerics) for numbers/titles/buttons, **Inter** for body/lists. Bundle both via `Info.plist` / `UIAppFonts`. Use `.monospacedDigit()` on every metric.

```swift
extension Font {
    // Display / numeric — Barlow Semi Condensed
    static let zwiftHUD        = Font.custom("BarlowSemiCondensed-ExtraBold", size: 34).monospacedDigit()
    static let zwiftHUDCompact = Font.custom("BarlowSemiCondensed-ExtraBold", size: 28).monospacedDigit()
    static let zwiftScreenTitle = Font.custom("BarlowSemiCondensed-ExtraBold", size: 28)
    static let zwiftSection    = Font.custom("BarlowSemiCondensed-Bold",      size: 22)
    static let zwiftCardTitle  = Font.custom("BarlowSemiCondensed-Bold",      size: 18)
    static let zwiftUnit       = Font.custom("BarlowSemiCondensed-Bold",      size: 11)
    static let zwiftButton     = Font.custom("BarlowSemiCondensed-Bold",      size: 16)
    static let zwiftBadge      = Font.custom("BarlowSemiCondensed-ExtraBold", size: 11)
    // Body / UI — Inter
    static let zwiftBody       = Font.custom("Inter-Regular",  size: 16)
    static let zwiftListRow    = Font.custom("Inter-SemiBold", size: 15)
    static let zwiftCaption    = Font.custom("Inter-Medium",   size: 13)
    static let zwiftTab        = Font.custom("Inter-SemiBold", size: 10)
}
```

## 3. Signature Components

### Glass Modifier

```swift
struct ZwiftGlass: ViewModifier {
    var radius: CGFloat = 12
    var heavy: Bool = false

    func body(content: Content) -> some View {
        content
            .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: radius))
            .background((heavy ? Color.zwiftGlassHeavy : Color.zwiftGlass),
                        in: RoundedRectangle(cornerRadius: radius))
            .overlay(RoundedRectangle(cornerRadius: radius)
                .strokeBorder(Color.zwiftGlassStroke, lineWidth: 1))
    }
}
extension View { func zwiftGlass(_ r: CGFloat = 12, heavy: Bool = false) -> some View { modifier(ZwiftGlass(radius: r, heavy: heavy)) } }
```

### HUD Metric Tile (the core atom)

```swift
enum RideMetric { case power, cadence, heart, wkg
    var color: Color {
        switch self { case .power: .zwiftPower; case .cadence: .zwiftCadence
                       case .heart: .zwiftHeart; case .wkg: .zwiftWkg }
    }
    var unit: String {
        switch self { case .power: "WATTS"; case .cadence: "RPM"; case .heart: "BPM"; case .wkg: "W/KG" }
    }
}

struct HUDTile: View {
    let metric: RideMetric
    let value: Double
    @State private var displayed: Double = 0

    var body: some View {
        VStack(spacing: 4) {
            Text(metric == .wkg ? String(format: "%.1f", displayed) : "\(Int(displayed))")
                .font(.zwiftHUD)
                .foregroundStyle(metric.color)
            Text(metric.unit)
                .font(.zwiftUnit)
                .foregroundStyle(Color.zwiftTextSecondary)
        }
        .padding(.vertical, 10).padding(.horizontal, 8)
        .frame(minWidth: 88)
        .zwiftGlass(12)
        .onChange(of: value) { _, newValue in
            withAnimation(.easeOut(duration: 0.2)) { displayed = newValue } // ticks, never jumps
        }
        .onAppear { displayed = value }
    }
}

struct RiderHUD: View {
    let power: Double, cadence: Double, heart: Double
    var body: some View {
        HStack(spacing: 12) {
            HUDTile(metric: .power, value: power)
            HUDTile(metric: .cadence, value: cadence)
            HUDTile(metric: .heart, value: heart)
        }
        .padding(.horizontal, 14)
    }
}
```

### Route Banner

```swift
struct RouteBanner: View {
    let route: String          // "Watopia · Volcano Flat"
    let sub: String            // "12.4 / 19.3 km · Lap 2 of 3"
    let progress: Double       // 0...1

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack(spacing: 10) {
                Image(systemName: "flag.fill")
                    .font(.system(size: 16, weight: .bold)).foregroundStyle(.white)
                    .frame(width: 30, height: 30)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.zwiftOrange))
                VStack(alignment: .leading, spacing: 1) {
                    Text(route).font(.zwiftCardTitle).foregroundStyle(.white)
                    Text(sub).font(.custom("Inter-Medium", size: 11)).foregroundStyle(Color.zwiftTextSecondary)
                }
                Spacer()
            }
            GeometryReader { g in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.white.opacity(0.18))
                    Capsule().fill(Color.zwiftOrange).frame(width: g.size.width * progress)
                }
            }
            .frame(height: 4)
        }
        .padding(.horizontal, 14).padding(.vertical, 10)
        .zwiftGlass(12)
        .padding(.horizontal, 14)
    }
}
```

### Power-Up Tile

```swift
struct PowerUpTile: View {
    let glyph: String          // SF Symbol for the power-up
    let armed: Bool
    let onDeploy: () -> Void
    @State private var pop = false

    var body: some View {
        Button(action: onDeploy) {
            Image(systemName: glyph)
                .font(.system(size: 24, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 56, height: 56)
                .background(
                    RoundedRectangle(cornerRadius: 10)
                        .fill(armed ? Color.zwiftOrange : Color.clear)
                )
                .background(armed ? nil : AnyView(RoundedRectangle(cornerRadius: 10).fill(.ultraThinMaterial)))
                .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.zwiftGlassStroke, lineWidth: 1))
                .scaleEffect(pop ? 1 : 0.8)
                .shadow(color: armed ? Color.zwiftOrange.opacity(0.6) : .clear, radius: armed ? 12 : 0)
        }
        .disabled(!armed)
        .onChange(of: armed) { _, isArmed in
            if isArmed {
                withAnimation(.spring(response: 0.25, dampingFraction: 0.55)) { pop = true }
            } else { pop = false }
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: armed)
    }
}
```

### Achievement Burst

```swift
struct AchievementBurst: View {
    let label: String          // "ROUTE COMPLETE"
    @State private var shown = false

    var body: some View {
        Text(label)
            .font(.zwiftBadge).textCase(.uppercase)
            .foregroundStyle(.white)
            .padding(.vertical, 8).padding(.horizontal, 18)
            .background(Capsule().fill(Color.zwiftSuccess))
            .shadow(color: Color.zwiftSuccess.opacity(0.5), radius: 14)
            .scaleEffect(shown ? 1 : 0.8)
            .opacity(shown ? 1 : 0)
            .onAppear { withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { shown = true } }
            .sensoryFeedback(.success, trigger: shown)
    }
}
```

### Primary CTA

```swift
struct ZwiftPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title.uppercased())
                .font(.zwiftButton).tracking(0.3)
                .foregroundStyle(.white)
                .padding(.vertical, 14).padding(.horizontal, 30)
                .frame(maxWidth: .infinity)
                .background(RoundedRectangle(cornerRadius: 10).fill(Color.zwiftOrange))
        }
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Action / Tab Bar

In-ride this is a game-control surface; off-ride it's a standard tab bar.

```swift
// Off-ride app-shell tab bar
struct ZwiftTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            WorkoutsView().tabItem { Label("Workouts", systemImage: "figure.indoor.cycle") }
            EventsView().tabItem { Label("Events", systemImage: "calendar") }
            ClubsView().tabItem { Label("Clubs", systemImage: "person.3.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.zwiftOrange) // active is Zwift Orange, no pill
    }
}

// In-ride action bar (game controls — Action / Power-Up / Workout / Riders / Menu)
struct InRideActionBar: View {
    let powerUpArmed: Bool
    var body: some View {
        HStack {
            actionItem("arrow.uturn.left", "Action", false)
            actionItem("bolt.fill", "Power-Up", powerUpArmed)
            actionItem("stopwatch", "Workout", false)
            actionItem("person.2.fill", "Riders", false)
            actionItem("line.3.horizontal", "Menu", false)
        }
        .padding(.horizontal, 8).padding(.top, 12).padding(.bottom, 18)
        .frame(maxWidth: .infinity)
        .zwiftGlass(0, heavy: true)
    }
    private func actionItem(_ sym: String, _ label: String, _ active: Bool) -> some View {
        VStack(spacing: 4) {
            Image(systemName: sym).font(.system(size: 22))
            Text(label).font(.zwiftTab)
        }
        .foregroundStyle(active ? Color.zwiftOrange : Color.zwiftTextSecondary)
        .frame(maxWidth: .infinity)
    }
}
```

## 5. Motion

```swift
// HUD metric tick — interpolate, never jump
.onChange(of: value) { _, newValue in
    withAnimation(.easeOut(duration: 0.2)) { displayed = newValue }
}

// Power-Up collected: scale-pop + glow + medium haptic
withAnimation(.spring(response: 0.25, dampingFraction: 0.55)) { pop = true }
.sensoryFeedback(.impact(weight: .medium), trigger: armed)

// Achievement burst: spring scale-in + success haptic + orange confetti sweep (~800ms)
withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { shown = true }
.sensoryFeedback(.success, trigger: shown)

// Ride-On burst: fade in/out over 600ms + soft haptic
// Overlay show/hide: .transition(.opacity.combined(with: .offset(y: 8))) — 200ms ease-out
// Route progress: capsule width animates linearly with distance
// App-shell nav: standard NavigationStack push (300ms)

// Haptics: .impact(.medium) on power-up, .success on achievement, .impact(.soft) on Ride-On/lap
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Workouts (tab) | `figure.indoor.cycle` | 22pt |
| Events (tab) | `calendar` | 22pt |
| Clubs (tab) | `person.3.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Route flag | `flag.fill` | 16pt |
| Power-Up (generic) | `bolt.fill` | 24pt |
| Power-Up: aero | `figure.fall` / custom | 24pt |
| Power-Up: feather (light) | `feather` | 24pt |
| Action (U-turn) | `arrow.uturn.left` | 22pt |
| Workout | `stopwatch` | 22pt |
| Riders nearby | `person.2.fill` | 22pt |
| Menu | `line.3.horizontal` | 22pt |
| Heart-rate | `heart.fill` | 16pt |
| Power | `bolt.fill` | 16pt |
| Elevation / climb | `mountain.2.fill` | 16pt |
| Ride On (kudos) | `hand.thumbsup.fill` | 18pt |
| Map | `map.fill` | 18pt |
| Camera angle | `camera.fill` | 18pt |
| Achievement | `rosette` | 18pt |
| Search | `magnifyingglass` | 16pt |

## 7. Dark Mode

```swift
// Zwift is effectively always-dark. In-ride: the 3D world is the background; UI is glass.
// Off-ride: a solid #161616 shell.
struct ZwiftShellTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.zwiftCanvas)        // off-ride shell only
            .foregroundStyle(Color.zwiftTextPrimary)
            .preferredColorScheme(.dark)          // always dark — there is no light theme
    }
}
extension View { func zwiftShell() -> some View { modifier(ZwiftShellTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UIImpactFeedbackGenerator`; `.ultraThinMaterial` is iOS 15+; the in-game 3D scene typically runs via the dedicated rendering pipeline, not SwiftUI)
- Bundle Barlow Semi Condensed + Inter TTFs via `Info.plist` `UIAppFonts` — both are SIL OFL licensed for free distribution
- Always apply `.monospacedDigit()` on every metric (watts, rpm, bpm, W/kg, km, time) so the HUD never jumps; interpolate value changes over ~200ms so numbers tick smoothly
- Dynamic Type: scale body, list rows, captions (Inter); keep HUD metrics, unit labels, screen titles, button labels, and tab labels at FIXED sizes — glance-legibility from a trainer and the glass-tile layout depend on it
- VoiceOver: label a HUD tile "Power, 248 watts" / "Heart rate, 156 BPM" (value + metric name); announce achievements assertively ("Route complete"); the Power-Up tile is a button labelled with its type and "armed/not armed"
- Color contrast: white on the glass tiles passes AA because the glass darkens the world beneath; the metric semantic colors are paired with a glyph + unit label so color is never the sole differentiator
- Reduce Motion: disable the power-up scale-pop, achievement spring, and confetti (use a crossfade); keep the smooth metric tick (it conveys live data) and the route-progress fill
- Reduce Transparency: when enabled, swap glass panels to solid `rgba(18,18,18,0.95)` so overlays stay legible without the blur
- Always dark: there is no light theme — `.preferredColorScheme(.dark)`; in-ride the world is the backdrop, off-ride it's `#161616`
- In-ride is landscape-preferred (the trainer layout); ensure the HUD and action bar reflow gracefully to a reduced 3-tile portrait layout
