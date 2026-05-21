# Citymapper (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Citymapper's visual language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, the mode-color system, the leg strip, the GO button, GO trip mode, and the departures board.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let cmCanvas        = Color.white                                    // #FFFFFF
    static let cmSurface1Light = Color(red: 0.957, green: 0.961, blue: 0.973)  // #F4F5F8
    static let cmSurface2Light = Color(red: 0.910, green: 0.918, blue: 0.941)  // #E8EAF0
    static let cmDividerLight  = Color(red: 0.890, green: 0.898, blue: 0.925)  // #E3E5EC

    // MARK: - Canvas & Surfaces (Dark) — deep blue-black, NOT pure black
    static let cmDarkCanvas    = Color(red: 0.047, green: 0.055, blue: 0.078)  // #0C0E14
    static let cmDarkSurface1  = Color(red: 0.082, green: 0.090, blue: 0.122)  // #15171F
    static let cmDarkSurface2  = Color(red: 0.118, green: 0.129, blue: 0.169)  // #1E212B
    static let cmDarkDivider   = Color(red: 0.157, green: 0.173, blue: 0.220)  // #282C38

    // MARK: - Text
    static let cmTextPrimary    = Color(red: 0.063, green: 0.075, blue: 0.102) // #10131A
    static let cmTextSecondary  = Color(red: 0.353, green: 0.392, blue: 0.451) // #5A6473
    static let cmTextTertiary   = Color(red: 0.541, green: 0.576, blue: 0.639) // #8A93A3
    static let cmDarkTextPrim   = Color(red: 0.925, green: 0.933, blue: 0.953) // #ECEEF3
    static let cmDarkTextSec    = Color(red: 0.596, green: 0.627, blue: 0.682) // #98A0AE

    // MARK: - Brand
    static let cmBlue        = Color(red: 0.169, green: 0.357, blue: 1.000)     // #2B5BFF
    static let cmBlueBright  = Color(red: 0.302, green: 0.482, blue: 1.000)     // #4D7BFF
    static let cmBluePressed = Color(red: 0.118, green: 0.271, blue: 0.800)     // #1E45CC
    static let cmGoGreen     = Color(red: 0.000, green: 0.761, blue: 0.506)     // #00C281

    // MARK: - Transit Mode Colors (theme-invariant — the brand)
    static let cmModeWalk = Color(red: 0.000, green: 0.722, blue: 0.580)        // #00B894
    static let cmModeBus  = Color(red: 0.910, green: 0.271, blue: 0.235)        // #E8453C
    static let cmModeTube = Color(red: 0.169, green: 0.357, blue: 1.000)        // #2B5BFF
    static let cmModeRail = Color(red: 0.557, green: 0.267, blue: 0.847)        // #8E44D8
    static let cmModeBike = Color(red: 0.000, green: 0.659, blue: 0.773)        // #00A8C5
    static let cmModeCab  = Color(red: 1.000, green: 0.706, blue: 0.000)        // #FFB400
    static let cmModeFerry = Color(red: 0.000, green: 0.580, blue: 0.776)       // #0094C6

    // MARK: - Semantic
    static let cmDisruption = Color(red: 1.000, green: 0.541, blue: 0.000)      // #FF8A00
}

enum TransitMode {
    case walk, bus, tube, rail, bike, cab, ferry
    var color: Color {
        switch self {
        case .walk: return .cmModeWalk
        case .bus:  return .cmModeBus
        case .tube: return .cmModeTube
        case .rail: return .cmModeRail
        case .bike: return .cmModeBike
        case .cab:  return .cmModeCab
        case .ferry: return .cmModeFerry
        }
    }
    var symbol: String {
        switch self {
        case .walk: return "figure.walk"
        case .bus:  return "bus.fill"
        case .tube: return "tram.fill"
        case .rail: return "train.side.front.car"
        case .bike: return "bicycle"
        case .cab:  return "car.fill"
        case .ferry: return "ferry.fill"
        }
    }
}
```

## 2. Typography

Citymapper ships a custom chunky grotesque; fall back to a heavy SF Pro or bundle Inter (400–900). Money/time styles use `.monospacedDigit()`.

```swift
extension Font {
    static func cm(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        Font.custom("Inter", size: size).weight(weight) // swap for the custom face if bundled
    }

    static let cmDisplay   = cm(32, weight: .black)
    static let cmEtaHero   = cm(26, weight: .heavy)
    static let cmSection   = cm(22, weight: .heavy)
    static let cmRowTitle  = cm(18, weight: .bold)
    static let cmBody      = cm(16, weight: .regular)
    static let cmPlace     = cm(15, weight: .semibold)
    static let cmMeta      = cm(14, weight: .regular)
    static let cmModeBadge = cm(12, weight: .heavy)
    static let cmEtaSuffix = cm(13, weight: .semibold)
    static let cmGoLabel   = cm(18, weight: .black)
    static let cmButton    = cm(15, weight: .heavy)
    static let cmTag       = cm(10, weight: .heavy)
    static let cmDepMin    = cm(16, weight: .heavy)
    static let cmTab       = cm(10, weight: .semibold)
}
```

## 3. Signature Components

### Mode Chip & Leg Strip

```swift
struct ModeChip: View {
    let mode: TransitMode
    let label: String   // "4" (walk min) or "Victoria" (line)

    var body: some View {
        HStack(spacing: 5) {
            Image(systemName: mode.symbol).font(.system(size: 13, weight: .bold))
            Text(label).font(.cmModeBadge)
        }
        .foregroundStyle(.white)
        .padding(.horizontal, 9).padding(.vertical, 5)
        .background(RoundedRectangle(cornerRadius: 8).fill(mode.color))
    }
}

struct LegStrip: View {
    struct Leg: Identifiable { let id = UUID(); let mode: TransitMode; let label: String }
    let legs: [Leg]

    var body: some View {
        HStack(spacing: 6) {
            ForEach(Array(legs.enumerated()), id: \.element.id) { i, leg in
                ModeChip(mode: leg.mode, label: leg.label)
                if i < legs.count - 1 {
                    Text("›").font(.system(size: 11, weight: .bold))
                        .foregroundStyle(Color.cmTextTertiary)
                }
            }
        }
    }
}
```

### Route Option Card

```swift
struct RouteCard: View {
    let etaMinutes: Int
    let tag: (text: String, color: Color)?   // ("Fastest", .cmGoGreen)
    let legs: [LegStrip.Leg]
    let isBest: Bool
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                HStack(alignment: .firstTextBaseline, spacing: 4) {
                    Text("\(etaMinutes)").font(.cmSection).monospacedDigit()
                        .foregroundStyle(scheme == .dark ? Color.cmDarkTextPrim : Color.cmTextPrimary)
                    Text("min").font(.cmEtaSuffix)
                        .foregroundStyle(Color.cmTextSecondary)
                }
                Spacer()
                if let tag {
                    Text(tag.text.uppercased())
                        .font(.cmTag).foregroundStyle(tag.color)
                        .padding(.horizontal, 9).padding(.vertical, 4)
                        .background(Capsule().fill(tag.color.opacity(0.18)))
                }
            }
            LegStrip(legs: legs)
        }
        .padding(.horizontal, 16).padding(.vertical, 14)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(scheme == .dark ? Color.cmDarkSurface1 : Color.cmSurface1Light)
        )
        .overlay(
            RoundedRectangle(cornerRadius: 16)
                .strokeBorder(isBest ? Color.cmGoGreen :
                    (scheme == .dark ? Color.cmDarkDivider : Color.cmDividerLight), lineWidth: 1)
        )
    }
}
```

### GO Button (shape/color-locked)

```swift
struct GoButton: View {
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill").font(.system(size: 20, weight: .black))
                Text("GO").font(.cmGoLabel).tracking(0.4)
            }
            .foregroundStyle(Color(red: 0, green: 0.2, blue: 0.13)) // dark-green text
            .frame(maxWidth: .infinity).frame(height: 54)
            .background(RoundedRectangle(cornerRadius: 28).fill(Color.cmGoGreen))
            .shadow(color: Color.cmGoGreen.opacity(0.7), radius: 22, x: 0, y: 10)
        }
        .buttonStyle(.plain)
        .padding(.horizontal, 16)
        .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
        // NOTE: shape (28pt pill) + color (#00C281) are LOCKED — never theme- or context-dependent
    }
}
```

### Origin → Destination Card

```swift
struct ODCard: View {
    let from: String
    let to: String
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        VStack(spacing: 2) {
            row(dot: Color.cmTextSecondary, text: from)
            ZStack {
                HStack { Color.clear.frame(width: 10) }
                Rectangle().fill(scheme == .dark ? Color.cmDarkDivider : Color.cmDividerLight)
                    .frame(width: 2, height: 14)
                    .frame(maxWidth: .infinity, alignment: .leading)
                    .padding(.leading, 18)
            }
            Divider().background(scheme == .dark ? Color.cmDarkDivider : Color.cmDividerLight)
            row(dot: Color.cmBlue, text: to)
        }
        .padding(.horizontal, 14).padding(.vertical, 12)
        .background(
            RoundedRectangle(cornerRadius: 14)
                .fill(scheme == .dark ? Color.cmDarkSurface1 : Color.cmSurface1Light)
        )
        .overlay(
            RoundedRectangle(cornerRadius: 14)
                .strokeBorder(scheme == .dark ? Color.cmDarkDivider : Color.cmDividerLight, lineWidth: 1)
        )
        .padding(.horizontal, 16)
    }

    private func row(dot: Color, text: String) -> some View {
        HStack(spacing: 12) {
            Circle().fill(dot).frame(width: 10, height: 10)
            Text(text).font(.cmPlace)
                .foregroundStyle(scheme == .dark ? Color.cmDarkTextPrim : Color.cmTextPrimary)
            Spacer()
        }
        .frame(height: 34)
    }
}
```

### Departures Board

```swift
struct DeparturesBoard: View {
    struct Departure: Identifiable {
        let id = UUID()
        let badge: String          // "VIC", "63", "NR"
        let mode: TransitMode
        let destination: String
        let minutes: Int
    }
    let rows: [Departure]
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        VStack(spacing: 0) {
            ForEach(Array(rows.enumerated()), id: \.element.id) { i, d in
                HStack(spacing: 12) {
                    Text(d.badge)
                        .font(.cmModeBadge).foregroundStyle(.white)
                        .frame(width: 34, height: 24)
                        .background(RoundedRectangle(cornerRadius: 6).fill(d.mode.color))
                    Text(d.destination).font(.cmPlace)
                        .foregroundStyle(scheme == .dark ? Color.cmDarkTextPrim : Color.cmTextPrimary)
                    Spacer()
                    Text("\(d.minutes) min")
                        .font(.cmDepMin).monospacedDigit()
                        .foregroundStyle(d.minutes <= 3 ? Color.cmDisruption : Color.cmGoGreen)
                }
                .padding(.horizontal, 16).padding(.vertical, 13)
                if i < rows.count - 1 {
                    Divider().background(scheme == .dark ? Color.cmDarkDivider : Color.cmDividerLight)
                }
            }
        }
        .background(
            RoundedRectangle(cornerRadius: 14)
                .fill(scheme == .dark ? Color.cmDarkSurface2 : Color.cmSurface1Light)
        )
        .clipShape(RoundedRectangle(cornerRadius: 14))
    }
}
```

### GO Trip-Mode Header

```swift
struct GoTripHeader: View {
    let instruction: String   // "Get off in 2 stops"
    let sub: String           // "Tate Modern · 8 min"
    let legColor: Color       // current leg's mode color

    var body: some View {
        HStack(alignment: .top, spacing: 14) {
            // vertical progress line
            VStack(spacing: 4) {
                Circle().fill(legColor).frame(width: 12, height: 12)
                Rectangle().fill(legColor).frame(width: 3, height: 60)
                Circle().stroke(Color.cmTextTertiary, lineWidth: 2).frame(width: 12, height: 12)
            }
            VStack(alignment: .leading, spacing: 6) {
                Text(instruction).font(.cmEtaHero)
                    .foregroundStyle(Color.cmTextPrimary)
                Text(sub).font(.cmBody).foregroundStyle(Color.cmTextSecondary)
            }
            Spacer()
        }
        .padding(20)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct CMTabView: View {
    var body: some View {
        TabView {
            GetMeView().tabItem  { Label("Get me", systemImage: "magnifyingglass") }
            NearbyView().tabItem { Label("Nearby", systemImage: "mappin.and.ellipse") }
            SavedView().tabItem  { Label("Saved",  systemImage: "heart") }
            YouView().tabItem    { Label("You",    systemImage: "person.crop.circle") }
        }
        .tint(.cmBlue) // active = Citymapper Blue, no pill indicator
    }
}
```

## 5. Motion

```swift
// Route list ⇄ map drag — sheet snap
// .presentationDetents([.height(120), .medium, .large]) with spring(response: 0.35, dampingFraction: 0.85)

// GO start — cross-dissolve into GO mode + progress line draw-in
withAnimation(.easeInOut(duration: 0.35)) { inGoMode = true }
// progress line: animate a trim from 0→1 over 0.5s

// GO-mode step transition — fill completed leg + slide instruction
withAnimation(.easeOut(duration: 0.25)) { legProgress = 1 }
// instruction text: .transition(.move(edge: .top).combined(with: .opacity)) (200ms)

// Departure tick — number cross-fade
.contentTransition(.numericText())
withAnimation(.easeInOut(duration: 0.15)) { minutes -= 1 }

// Route cards stagger-in
.transition(.opacity.combined(with: .move(edge: .bottom)))
// per-card .animation(.easeOut(duration: 0.25).delay(Double(i) * 0.06))

// Disruption banner — slide down + one border pulse
.transition(.move(edge: .top).combined(with: .opacity))

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: selectedRoute)   // route select / tab
.sensoryFeedback(.impact(weight: .medium), trigger: goStepIndex)    // GO start + each step
.sensoryFeedback(.warning, trigger: activeDisruption)               // disruption on active route
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Get me (tab) | `magnifyingglass` | 22pt |
| Nearby (tab) | `mappin.and.ellipse` | 22pt |
| Saved (tab) | `heart` / `heart.fill` | 22pt |
| You (tab) | `person.crop.circle` | 22pt |
| Walk leg | `figure.walk` | 13pt |
| Bus leg | `bus.fill` | 13pt |
| Tube/Metro leg | `tram.fill` | 13pt |
| Rail leg | `train.side.front.car` | 13pt |
| Bike leg | `bicycle` | 13pt |
| Cab leg | `car.fill` | 13pt |
| Ferry leg | `ferry.fill` | 13pt |
| GO | `play.fill` | 20pt |
| Leg arrow | `chevron.right` | 11pt |
| Disruption | `exclamationmark.triangle.fill` | 14pt |
| Live dot | `circle.fill` | 8pt |
| Back | `chevron.left` | 20pt |
| Rain-safe tag | `cloud.rain.fill` | 11pt |
| Step-free tag | `figure.roll` | 11pt |

## 7. Dark Mode

```swift
struct CMTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.cmDarkCanvas : Color.cmCanvas)
            .foregroundStyle(scheme == .dark ? Color.cmDarkTextPrim : Color.cmTextPrimary)
    }
}
extension View { func cmTheme() -> some View { modifier(CMTheme()) } }

func cmSurface1(_ s: ColorScheme) -> Color { s == .dark ? .cmDarkSurface1 : .cmSurface1Light }
func cmDivider(_ s: ColorScheme)  -> Color { s == .dark ? .cmDarkDivider  : .cmDividerLight }
// NOTE: every TransitMode.color and cmGoGreen are NEVER swapped — a bus is always red, GO is always #00C281.
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` & `.contentTransition(.numericText())` need iOS 17; fall back to `UIImpactFeedbackGenerator` and a plain crossfade)
- Bundle the custom grotesque or Inter (400–900) TTFs via `Info.plist`; SF Pro heavy is an acceptable fallback
- **Tabular digits**: `.monospacedDigit()` on every ETA, departure-min, and GO-mode countdown so they don't jitter
- Dynamic Type: scale display/ETA/body/place; keep mode badges, route tags, GO label, departure-min, and tab labels FIXED (chips are layout-sensitive); GO-mode instruction scales generously
- VoiceOver: announce a leg strip as a single element — "Route, 24 minutes, fastest: walk 4 minutes, Victoria line, walk 6 minutes" (so the *mode* is spoken, not just colored); departures as "Victoria line to Brixton, 2 minutes, leaving soon"
- Color is never the only signal: each mode chip carries a glyph + label and VoiceOver speaks the mode name; the ≤3-min "soon" state is announced as "leaving soon", not only orange
- Contrast: `#10131A` on `#FFFFFF` and `#ECEEF3` on `#0C0E14` pass WCAG AA; white-on-mode-color chips are tuned (bus red, cab amber use sufficiently dark/bright text where needed)
- Reduce Motion: disable the progress-line draw-in and route-card stagger — use a single crossfade; keep the departure number crossfade (it conveys state)
- Dark mode: invert via `cmDark*` — `#0C0E14`, NOT true black; the GO button's green glow stays on dark; card separation uses the `#15171F` → `#1E212B` ramp. The mode-color system and GO green are **never** inverted — they are theme-invariant brand constants. When an operator's official line color is known, override the category mode color with it.
