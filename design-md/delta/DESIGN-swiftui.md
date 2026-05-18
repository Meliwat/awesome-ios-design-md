# Delta (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Delta's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and the signature boarding pass + flight-status timeline.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let deltaBlue        = Color(red: 0.000, green: 0.196, blue: 0.408) // #003268
    static let deltaBlueBright  = Color(red: 0.059, green: 0.298, blue: 0.604) // #0F4C9A
    static let deltaRed         = Color(red: 0.784, green: 0.063, blue: 0.180) // #C8102E
    static let deltaRedPressed  = Color(red: 0.639, green: 0.051, blue: 0.149) // #A30D26
    static let deltaWidgetMaroon = Color(red: 0.525, green: 0.149, blue: 0.200) // #862633
    static let skyMilesGold     = Color(red: 0.788, green: 0.592, blue: 0.000) // #C99700

    // MARK: - Canvas & Surfaces (Light)
    static let deltaCanvas        = Color.white                                 // #FFFFFF
    static let deltaSurfaceGray   = Color(red: 0.957, green: 0.965, blue: 0.976) // #F4F6F9
    static let deltaSurfacePressed = Color(red: 0.910, green: 0.925, blue: 0.953) // #E8ECF3
    static let deltaDividerLight  = Color(red: 0.839, green: 0.863, blue: 0.906) // #D6DCE7

    // MARK: - Canvas & Surfaces (Dark)
    static let deltaDarkCanvas    = Color(red: 0.043, green: 0.075, blue: 0.125) // #0B1320
    static let deltaDarkSurface1  = Color(red: 0.075, green: 0.110, blue: 0.169) // #131C2B
    static let deltaDarkSurface2  = Color(red: 0.110, green: 0.153, blue: 0.220) // #1C2738
    static let deltaDarkDivider   = Color(red: 0.145, green: 0.192, blue: 0.290) // #25314A

    // MARK: - Text
    static let deltaTextPrimary    = Color(red: 0.063, green: 0.141, blue: 0.247) // #10243F
    static let deltaTextSecondary  = Color(red: 0.353, green: 0.416, blue: 0.510) // #5A6A82
    static let deltaTextTertiary   = Color(red: 0.541, green: 0.596, blue: 0.682) // #8A98AE
    static let deltaDarkTextPrimary = Color(red: 0.910, green: 0.925, blue: 0.953) // #E8ECF3
    static let deltaDarkTextSecondary = Color(red: 0.604, green: 0.651, blue: 0.737) // #9AA6BC

    // MARK: - Flight Status (functional, constant)
    static let deltaOnTime        = Color(red: 0.118, green: 0.557, blue: 0.353) // #1E8E5A
    static let deltaOnTimeDark    = Color(red: 0.310, green: 0.820, blue: 0.549) // #4FD18C
    static let deltaBoarding      = Color(red: 0.059, green: 0.298, blue: 0.604) // #0F4C9A
    static let deltaBoardingDark  = Color(red: 0.435, green: 0.690, blue: 0.918) // #6FB0EA
    static let deltaDelayed       = Color(red: 0.788, green: 0.592, blue: 0.000) // #C99700
    static let deltaDelayedDark   = Color(red: 0.878, green: 0.718, blue: 0.263) // #E0B743
    static let deltaCanceled      = Color(red: 0.784, green: 0.063, blue: 0.180) // #C8102E
    static let deltaCanceledDark  = Color(red: 0.878, green: 0.255, blue: 0.306) // #E0414E
}
```

## 2. Typography

Delta uses a Whitney-class humanist sans; **Open Sans** (Apache 2.0) is the closest free analog. Bundle the TTFs via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static func delta(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .bold, .heavy, .black: return "OpenSans-Bold"
            case .semibold:             return "OpenSans-SemiBold"
            case .medium:               return "OpenSans-Medium"
            default:                    return "OpenSans-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let deltaRouteIATA  = Font.custom("OpenSans-ExtraBold", size: 34)
    static let deltaScreenTitle = Font.custom("OpenSans-ExtraBold", size: 26)
    static let deltaSection    = Font.custom("OpenSans-Bold", size: 22)
    static let deltaPaxName    = Font.custom("OpenSans-Bold", size: 18)
    static let deltaDataLarge  = Font.custom("OpenSans-ExtraBold", size: 22)
    static let deltaBody       = Font.custom("OpenSans-Regular", size: 16)
    static let deltaDataInline = Font.custom("OpenSans-SemiBold", size: 15)
    static let deltaMeta       = Font.custom("OpenSans-Regular", size: 14)
    static let deltaEyebrow    = Font.custom("OpenSans-Bold", size: 12)
    static let deltaCellLabel  = Font.custom("OpenSans-Bold", size: 10)
    static let deltaButton     = Font.custom("OpenSans-Bold", size: 16)
    static let deltaTab        = Font.custom("OpenSans-SemiBold", size: 10)
    static let deltaStatusPill = Font.custom("OpenSans-Bold", size: 12)
}
```

## 3. Signature Components

### Digital Boarding Pass

```swift
struct BoardingPass: View {
    let paxName: String
    let gate: String
    let seat: String
    let group: String
    let boardsAt: String
    let zone: String

    var body: some View {
        VStack(spacing: 0) {
            LinearGradient(colors: [.deltaBlue, .deltaRed],
                           startPoint: .leading, endPoint: .trailing)
                .frame(height: 6)

            VStack(alignment: .leading, spacing: 0) {
                Text("PASSENGER")
                    .font(.deltaCellLabel).tracking(0.6)
                    .foregroundStyle(Color.deltaDarkTextSecondary)
                Text(paxName.uppercased())
                    .font(.deltaPaxName)
                    .foregroundStyle(Color.deltaDarkTextPrimary)
                    .padding(.top, 2)

                HStack(spacing: 18) {
                    passCell("GATE", gate, color: .deltaDarkTextPrimary)
                    passCell("SEAT", seat, color: .deltaDarkTextPrimary)
                    passCell("GROUP", group, color: .skyMilesGold)
                }
                .padding(.top, 16)

                HStack {
                    Text("Boards \(boardsAt) · Zone")
                        .font(.delta(11, weight: .semibold))
                        .foregroundStyle(Color.deltaDarkTextSecondary)
                    Spacer()
                    Text(zone)
                        .font(.delta(14, weight: .bold))
                        .foregroundStyle(Color.deltaDarkTextPrimary)
                }
                .padding(14)
                .background(RoundedRectangle(cornerRadius: 10).fill(Color.deltaDarkSurface2))
                .padding(.top, 16)

                BarcodeStripe()
                    .frame(height: 56)
                    .clipShape(RoundedRectangle(cornerRadius: 8))
                    .padding(.top, 16)
            }
            .padding(18)
        }
        .background(RoundedRectangle(cornerRadius: 18).fill(Color.deltaDarkSurface1))
        .overlay(RoundedRectangle(cornerRadius: 18).strokeBorder(Color.deltaDarkDivider, lineWidth: 1))
        .shadow(color: .black.opacity(0.55), radius: 28, y: 10)
    }

    private func passCell(_ label: String, _ value: String, color: Color) -> some View {
        VStack(alignment: .leading, spacing: 3) {
            Text(label).font(.deltaCellLabel).tracking(0.6)
                .foregroundStyle(Color.deltaTextTertiary)
            Text(value).font(.deltaDataLarge).foregroundStyle(color)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
    }
}

/// High-contrast bar pattern; inverts to light bars in dark mode so a gate scanner reads a dimmed screen.
struct BarcodeStripe: View {
    @Environment(\.colorScheme) private var scheme
    var body: some View {
        GeometryReader { geo in
            let bar = scheme == .dark ? Color.deltaDarkTextPrimary : Color.deltaTextPrimary
            HStack(spacing: 2) {
                ForEach(0..<Int(geo.size.width / 4), id: \.self) { i in
                    Rectangle().fill(bar).frame(width: i % 3 == 0 ? 3 : 1)
                }
            }
        }
    }
}
```

### Flight Status Timeline

```swift
struct FlightStatusTimeline: View {
    struct Step: Identifiable {
        let id = UUID()
        let title: String
        let meta: String
        let done: Bool
    }
    let steps: [Step]

    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            Text("Flight Status")
                .font(.delta(13, weight: .bold))
                .foregroundStyle(Color.deltaDarkTextPrimary)

            HStack(alignment: .top, spacing: 14) {
                VStack(spacing: 0) {
                    ForEach(Array(steps.enumerated()), id: \.element.id) { idx, step in
                        Circle()
                            .fill(step.done ? Color.deltaOnTimeDark : Color.deltaDarkTextSecondary)
                            .frame(width: 12, height: 12)
                            .overlay(Circle().strokeBorder(
                                step.done ? Color.deltaOnTimeDark : Color.deltaDarkTextSecondary, lineWidth: 2))
                        if idx < steps.count - 1 {
                            Rectangle().fill(Color.deltaDarkDivider)
                                .frame(width: 2).frame(maxHeight: .infinity)
                        }
                    }
                }
                .frame(width: 12)

                VStack(alignment: .leading, spacing: 18) {
                    ForEach(steps) { step in
                        VStack(alignment: .leading, spacing: 2) {
                            Text(step.title).font(.delta(13, weight: .bold))
                                .foregroundStyle(Color.deltaDarkTextPrimary)
                            Text(step.meta).font(.delta(12))
                                .foregroundStyle(Color.deltaDarkTextSecondary)
                        }
                    }
                }
            }
        }
        .padding(18)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.deltaDarkSurface1))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.deltaDarkDivider, lineWidth: 1))
    }
}
```

### Trip Header (Today hero)

```swift
struct TripHeader: View {
    let flightNo: String
    let origin: String
    let dest: String
    let status: String

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                Text("TODAY · \(flightNo)")
                    .font(.delta(11, weight: .bold)).tracking(1)
                    .foregroundStyle(Color(red: 0.561, green: 0.706, blue: 0.867))
                Spacer()
                Text(status)
                    .font(.deltaStatusPill)
                    .foregroundStyle(Color.deltaOnTimeDark)
                    .padding(.horizontal, 10).padding(.vertical, 4)
                    .background(Capsule().fill(Color.deltaOnTime.opacity(0.22)))
            }
            HStack(alignment: .bottom, spacing: 14) {
                Text(origin).font(.deltaRouteIATA).foregroundStyle(.white)
                HStack(spacing: 6) {
                    Rectangle().fill(.white.opacity(0.35)).frame(height: 2)
                    Image(systemName: "airplane").foregroundStyle(.white)
                    Rectangle().fill(.white.opacity(0.35)).frame(height: 2)
                }
                .padding(.bottom, 8)
                Text(dest).font(.deltaRouteIATA).foregroundStyle(.white)
            }
        }
        .padding(.horizontal, 18).padding(.top, 8).padding(.bottom, 16)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(LinearGradient(colors: [.deltaBlue, Color(red: 0.024, green: 0.125, blue: 0.247)],
                                   startPoint: .top, endPoint: .bottom))
    }
}
```

### Buttons

```swift
struct DeltaPrimaryButton: View {
    let title: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.deltaButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 14)
                .background(RoundedRectangle(cornerRadius: 6)
                    .fill(pressed ? Color.deltaRedPressed : Color.deltaRed))
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .onChanged { _ in pressed = true }
            .onEnded { _ in pressed = false })
    }
}

struct DeltaStatusChip: View {
    enum Kind { case onTime, boarding, delayed, canceled }
    let kind: Kind
    let label: String

    var body: some View {
        let (fg, bg): (Color, Color) = {
            switch kind {
            case .onTime:   return (.deltaOnTimeDark, Color.deltaOnTime.opacity(0.18))
            case .boarding: return (.deltaBoardingDark, Color.deltaBoarding.opacity(0.24))
            case .delayed:  return (.deltaDelayedDark, Color.deltaDelayed.opacity(0.18))
            case .canceled: return (.deltaCanceledDark, Color.deltaCanceled.opacity(0.18))
            }
        }()
        Text(label)
            .font(.deltaStatusPill)
            .foregroundStyle(fg)
            .padding(.horizontal, 14).padding(.vertical, 7)
            .background(Capsule().fill(bg))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct DeltaTabView: View {
    var body: some View {
        TabView {
            TodayView().tabItem { Label("Today", systemImage: "house.fill") }
            BookView().tabItem { Label("Book", systemImage: "airplane") }
            TripsView().tabItem { Label("Trips", systemImage: "rectangle.stack") }
            SkyMilesView().tabItem { Label("SkyMiles", systemImage: "person.crop.circle") }
            MoreView().tabItem { Label("More", systemImage: "ellipsis.circle") }
        }
        .tint(.deltaBlueBright) // active = brand blue, no Material pill
    }
}
```

## 5. Motion

```swift
// Plane glide along the route line (header appear)
withAnimation(.easeInOut(duration: 1.2)) { planeProgress = 1.0 }

// Status change — cross-fade pill color + soft haptic
withAnimation(.easeInOut(duration: 0.25)) { statusKind = newKind }
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Active timeline dot pulse
withAnimation(.easeInOut(duration: 1.6).repeatForever(autoreverses: true)) {
    activeDotScale = 1.15
}

// Boarding pass open — spring scale + brightness boost
withAnimation(.spring(response: 0.35, dampingFraction: 0.8)) { passExpanded = true }
UIScreen.main.brightness = 1.0   // restore prior value on dismiss

// Check-in success
UINotificationFeedbackGenerator().notificationOccurred(.success)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Today (tab) | `house` / `house.fill` | 22pt |
| Book (tab) | `airplane` | 22pt |
| Trips (tab) | `rectangle.stack` / `.fill` | 22pt |
| SkyMiles (tab) | `person.crop.circle` / `.fill` | 22pt |
| More (tab) | `ellipsis.circle` / `.fill` | 22pt |
| In-flight plane | `airplane` | 18pt |
| Departed | `airplane.departure` | 16pt |
| Arrived | `airplane.arrival` | 16pt |
| Gate change | `exclamationmark.triangle.fill` | 16pt |
| Bag tracker | `suitcase.fill` | 18pt |
| Add to Wallet | `wallet.pass` | 18pt |
| Boarding pass | `qrcode` | 20pt |
| Seat | `chair.lounge.fill` | 16pt |
| Back | `chevron.left` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Refresh status | `arrow.clockwise` | 18pt |
| Medallion tier | `star.circle.fill` | 18pt |

## 7. Dark Mode

```swift
struct DeltaTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.deltaDarkCanvas : Color.deltaCanvas)
            .foregroundStyle(scheme == .dark ? Color.deltaDarkTextPrimary : Color.deltaTextPrimary)
    }
}
extension View { func deltaTheme() -> some View { modifier(DeltaTheme()) } }
```

Dark canvas is `#0B1320` — a deep navy-charcoal, **never** neutral black, so the boarding-pass navy gradient still reads as Delta. The barcode inverts to a light bar pattern in dark mode (see `BarcodeStripe`) so gate scanners still read a dimmed screen. Shadows nearly vanish on the dark canvas — a 1pt `#25314A` border doubles as the elevation cue on the pass and timeline cards. Status colors swap to their lighter variants (`#4FD18C`, `#E0B743`, `#6FB0EA`, `#E0414E`) on the same translucent fills.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Open Sans TTFs via `Info.plist` `UIAppFonts` — Apache 2.0, free to distribute. (Production Delta uses its licensed Whitney-class corporate face.)
- Dynamic Type: scale route IATA, screen title, section, body, meta; keep the barcode, 10pt cell labels, 10pt tab labels, and status-pill text FIXED (scan/layout-sensitive). Data values scale one step then clamp to preserve the 3-cell grid
- VoiceOver: label the pass as "Boarding pass, {name}, gate {gate}, seat {seat}, group {group}, boards {time}"; label each timeline step "{title}, {meta}, {completed/upcoming}"; status pill announces the full status word
- Color contrast: `#E8ECF3` on `#0B1320` and `#10243F` on `#FFFFFF` pass WCAG AA; status colors are paired against translucent fills validated for AA
- Reduce Motion: disable the plane glide and active-dot pulse; keep the status cross-fade as an instant color swap
- Brightness: when the full-screen pass opens, raise `UIScreen.main.brightness` to max for gate scanning; restore the previous value on dismiss
- Dark mode: invert via the `deltaDark*` palette; the barcode must remain machine-scannable — never let it drop below the high-contrast bar pattern
