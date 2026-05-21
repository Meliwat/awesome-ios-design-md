# United (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates United's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and the signature flight detail → boarding pass + interactive seat map.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let uaBlue          = Color(red: 0.000, green: 0.133, blue: 0.267) // #002244
    static let uaBlueMid       = Color(red: 0.106, green: 0.239, blue: 0.431) // #1B3D6E
    static let uaRhapsody      = Color(red: 0.078, green: 0.078, blue: 1.000) // #1414FF
    static let uaRhapsodyPress = Color(red: 0.055, green: 0.055, blue: 0.784) // #0E0EC8
    static let uaRhapsodyBright = Color(red: 0.290, green: 0.290, blue: 1.000) // #4A4AFF
    static let uaPremierGold   = Color(red: 0.761, green: 0.631, blue: 0.302) // #C2A14D
    static let uaPremierGoldBright = Color(red: 0.847, green: 0.722, blue: 0.388) // #D8B863

    // MARK: - Canvas & Surfaces (Light)
    static let uaCanvas        = Color.white                                 // #FFFFFF
    static let uaSurfaceGray   = Color(red: 0.953, green: 0.961, blue: 0.980) // #F3F5FA
    static let uaSurfacePressed = Color(red: 0.898, green: 0.918, blue: 0.953) // #E5EAF3
    static let uaDividerLight  = Color(red: 0.831, green: 0.863, blue: 0.914) // #D4DCE9

    // MARK: - Canvas & Surfaces (Dark)
    static let uaDarkCanvas    = Color(red: 0.039, green: 0.078, blue: 0.141) // #0A1424
    static let uaDarkSurface1  = Color(red: 0.067, green: 0.118, blue: 0.200) // #111E33
    static let uaDarkSurface2  = Color(red: 0.102, green: 0.165, blue: 0.271) // #1A2A45
    static let uaDarkDivider   = Color(red: 0.141, green: 0.212, blue: 0.322) // #243652

    // MARK: - Text
    static let uaTextPrimary    = Color(red: 0.047, green: 0.122, blue: 0.220) // #0C1F38
    static let uaTextSecondary  = Color(red: 0.337, green: 0.408, blue: 0.518) // #566884
    static let uaTextTertiary   = Color(red: 0.518, green: 0.576, blue: 0.675) // #8493AC
    static let uaDarkTextPrimary = Color(red: 0.914, green: 0.933, blue: 0.965) // #E9EEF6
    static let uaDarkTextSecondary = Color(red: 0.592, green: 0.651, blue: 0.745) // #97A6BE
    static let uaDarkTextTertiary = Color(red: 0.369, green: 0.439, blue: 0.565) // #5E7090

    // MARK: - Flight Status (functional, constant)
    static let uaOnTime        = Color(red: 0.118, green: 0.557, blue: 0.353) // #1E8E5A
    static let uaOnTimeDark    = Color(red: 0.310, green: 0.820, blue: 0.549) // #4FD18C
    static let uaBoarding      = Color(red: 0.078, green: 0.078, blue: 1.000) // #1414FF
    static let uaBoardingDark  = Color(red: 0.478, green: 0.478, blue: 1.000) // #7A7AFF
    static let uaDelayed       = Color(red: 0.788, green: 0.541, blue: 0.118) // #C98A1E
    static let uaDelayedDark   = Color(red: 0.878, green: 0.663, blue: 0.263) // #E0A943
    static let uaCanceled      = Color(red: 0.847, green: 0.263, blue: 0.310) // #D8434F
    static let uaCanceledDark  = Color(red: 0.941, green: 0.502, blue: 0.541) // #F0808A
}
```

## 2. Typography

United uses a clean humanist corporate sans; **Open Sans** (Apache 2.0) is the closest free analog. Bundle the TTFs via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static func ua(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .black, .heavy: return "OpenSans-ExtraBold"
            case .bold:          return "OpenSans-Bold"
            case .semibold:      return "OpenSans-SemiBold"
            case .medium:        return "OpenSans-Medium"
            default:             return "OpenSans-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let uaRouteIATA  = Font.custom("OpenSans-ExtraBold", size: 32)
    static let uaScreenTitle = Font.custom("OpenSans-ExtraBold", size: 26)
    static let uaSection    = Font.custom("OpenSans-Bold", size: 22)
    static let uaPaxName    = Font.custom("OpenSans-Bold", size: 18)
    static let uaDataLarge  = Font.custom("OpenSans-ExtraBold", size: 22)
    static let uaBody       = Font.custom("OpenSans-Regular", size: 16)
    static let uaDataInline = Font.custom("OpenSans-SemiBold", size: 15)
    static let uaMeta       = Font.custom("OpenSans-Regular", size: 14)
    static let uaEyebrow    = Font.custom("OpenSans-Bold", size: 12)
    static let uaCellLabel  = Font.custom("OpenSans-Bold", size: 10)
    static let uaButton     = Font.custom("OpenSans-Bold", size: 16)
    static let uaTab        = Font.custom("OpenSans-SemiBold", size: 10)
    static let uaStatusPill = Font.custom("OpenSans-Bold", size: 12)
}
```

## 3. Signature Components

### Boarding Pass (United Blue header + perforation)

```swift
struct UABoardingPass: View {
    let paxName: String
    let gate: String
    let seat: String
    let boards: String
    let premierGroup: String

    var body: some View {
        VStack(spacing: 0) {
            // United Blue header band
            HStack {
                HStack(spacing: 8) {
                    UAGlobe(stroke: .white).frame(width: 22, height: 22)
                    Text("UNITED").font(.ua(13, weight: .black)).tracking(0.5)
                        .foregroundStyle(.white)
                }
                Spacer()
                Text(premierGroup).font(.ua(11, weight: .bold))
                    .foregroundStyle(Color.uaPremierGoldBright)
            }
            .padding(.horizontal, 18).padding(.vertical, 12)
            .frame(maxWidth: .infinity)
            .background(Color.uaBlue)

            VStack(alignment: .leading, spacing: 0) {
                Text("PASSENGER").font(.uaCellLabel).tracking(0.6)
                    .foregroundStyle(Color.uaDarkTextSecondary)
                Text(paxName.uppercased()).font(.uaPaxName)
                    .foregroundStyle(Color.uaDarkTextPrimary).padding(.top, 2)

                HStack(spacing: 16) {
                    passCell("GATE", gate, color: .uaDarkTextPrimary)
                    passCell("SEAT", seat, color: .uaDarkTextPrimary)
                    passCell("BOARDS", boards, color: .uaPremierGoldBright)
                }
                .padding(.top, 16)

                PerforationDivider().padding(.vertical, 16)

                BarcodeStripe().frame(height: 54)
                    .clipShape(RoundedRectangle(cornerRadius: 8))
            }
            .padding(18)
        }
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.uaDarkSurface1))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.uaDarkDivider, lineWidth: 1))
        .clipShape(RoundedRectangle(cornerRadius: 16))
        .shadow(color: .black.opacity(0.55), radius: 28, y: 10)
    }

    private func passCell(_ label: String, _ value: String, color: Color) -> some View {
        VStack(alignment: .leading, spacing: 3) {
            Text(label).font(.uaCellLabel).tracking(0.6)
                .foregroundStyle(Color.uaDarkTextTertiary)
            Text(value).font(.uaDataLarge).foregroundStyle(color)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
    }
}

/// Dashed line with circular notch cut-outs at the edges — the "tear here" affordance.
struct PerforationDivider: View {
    var body: some View {
        ZStack {
            Line().stroke(style: StrokeStyle(lineWidth: 1, dash: [5, 4]))
                .foregroundStyle(Color.uaDarkDivider).frame(height: 1)
            HStack {
                Circle().fill(Color.uaDarkCanvas).frame(width: 20, height: 20).offset(x: -28)
                Spacer()
                Circle().fill(Color.uaDarkCanvas).frame(width: 20, height: 20).offset(x: 28)
            }
        }
    }
    private struct Line: Shape {
        func path(in r: CGRect) -> Path {
            var p = Path(); p.move(to: CGPoint(x: 0, y: r.midY))
            p.addLine(to: CGPoint(x: r.width, y: r.midY)); return p
        }
    }
}

struct BarcodeStripe: View {
    @Environment(\.colorScheme) private var scheme
    var body: some View {
        GeometryReader { geo in
            let bar = scheme == .dark ? Color.uaDarkTextPrimary : Color.uaTextPrimary
            HStack(spacing: 2) {
                ForEach(0..<Int(geo.size.width / 4), id: \.self) { i in
                    Rectangle().fill(bar).frame(width: i % 3 == 0 ? 3 : 1)
                }
            }
        }
    }
}

struct UAGlobe: View {
    var stroke: Color
    var body: some View {
        ZStack {
            Circle().strokeBorder(stroke, lineWidth: 2.4)
            Ellipse().strokeBorder(stroke, lineWidth: 1.8).scaleEffect(x: 0.45, y: 1)
            Rectangle().fill(stroke).frame(height: 1.6)
            Rectangle().fill(stroke).frame(height: 1.6).offset(y: -6)
            Rectangle().fill(stroke).frame(height: 1.6).offset(y: 6)
        }
    }
}
```

### Interactive Seat Map

```swift
struct SeatMap: View {
    enum SeatKind { case standard, econPlus, taken, selected, aisle }
    @State private var selected = "7A"
    let rows: [[SeatKind]]   // each inner array length 7 (3 · aisle · 3)

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text("Choose your seat").font(.ua(13, weight: .bold))
                .foregroundStyle(Color.uaDarkTextPrimary)
            Text("Economy Plus · Row 7–9 · \(selected) selected")
                .font(.ua(12)).foregroundStyle(Color.uaDarkTextSecondary)
                .padding(.top, 4).padding(.bottom, 16)

            let cols = Array(repeating: GridItem(.flexible(), spacing: 6), count: 7)
            LazyVGrid(columns: cols, spacing: 6) {
                ForEach(Array(rows.enumerated()), id: \.offset) { _, row in
                    ForEach(Array(row.enumerated()), id: \.offset) { _, kind in
                        SeatCell(kind: kind)
                    }
                }
            }

            HStack(spacing: 16) {
                legend(Color.uaRhapsody.opacity(0.4), "Econ+")
                legend(Color.uaRhapsody, "Selected")
                legend(Color.uaDarkCanvas, "Taken")
            }
            .font(.ua(11)).foregroundStyle(Color.uaDarkTextSecondary)
            .padding(.top, 16)
        }
        .padding(18)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.uaDarkSurface1))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.uaDarkDivider, lineWidth: 1))
    }

    private func legend(_ c: Color, _ t: String) -> some View {
        HStack(spacing: 6) {
            RoundedRectangle(cornerRadius: 3).fill(c).frame(width: 10, height: 10)
            Text(t)
        }
    }
}

struct SeatCell: View {
    let kind: SeatMap.SeatKind
    @State private var pop = false
    var body: some View {
        Group {
            if kind == .aisle { Color.clear }
            else {
                RoundedRectangle(cornerRadius: 6)
                    .fill(fill)
                    .overlay(RoundedRectangle(cornerRadius: 6).strokeBorder(border, lineWidth: 1))
            }
        }
        .aspectRatio(1, contentMode: .fit)
        .scaleEffect(pop ? 1 : (kind == .selected ? 1 : 1))
        .onTapGesture {
            guard kind == .standard || kind == .econPlus else { return }
            UIImpactFeedbackGenerator(style: .light).impactOccurred()
            withAnimation(.spring(response: 0.2, dampingFraction: 0.6)) { pop.toggle() }
        }
    }
    private var fill: Color {
        switch kind {
        case .selected: return .uaRhapsody
        case .econPlus: return Color.uaRhapsody.opacity(0.18)
        case .taken:    return .uaDarkCanvas
        default:        return .uaDarkSurface2
        }
    }
    private var border: Color {
        switch kind {
        case .selected: return .uaRhapsodyBright
        case .econPlus: return Color.uaRhapsodyBright.opacity(0.4)
        default:        return .uaDarkDivider
        }
    }
}
```

### Flight Detail Header + Buttons + Status Chip

```swift
struct FlightDetailHeader: View {
    let flightNo, origin, originCity, dest, destCity, dur, depRow, arrRow, status: String

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text(flightNo.uppercased()).font(.ua(11, weight: .bold)).tracking(1)
                    .foregroundStyle(Color(red: 0.498, green: 0.627, blue: 0.808))
                Spacer()
                UAStatusChip(kind: .onTime, label: status)
            }
            HStack(alignment: .top) {
                VStack(alignment: .leading, spacing: 2) {
                    Text(origin).font(.uaRouteIATA).foregroundStyle(.white)
                    Text(originCity).font(.ua(12)).foregroundStyle(Color(red: 0.624, green: 0.729, blue: 0.863))
                }
                Spacer()
                VStack(spacing: 4) {
                    Image(systemName: "airplane").foregroundStyle(.white)
                    Text(dur).font(.ua(11)).foregroundStyle(Color(red: 0.624, green: 0.729, blue: 0.863))
                }.padding(.top, 8)
                Spacer()
                VStack(alignment: .trailing, spacing: 2) {
                    Text(dest).font(.uaRouteIATA).foregroundStyle(.white)
                    Text(destCity).font(.ua(12)).foregroundStyle(Color(red: 0.624, green: 0.729, blue: 0.863))
                }
            }
            .padding(.top, 14)
            HStack {
                Text(depRow); Spacer(); Text(arrRow)
            }
            .font(.ua(13, weight: .semibold))
            .foregroundStyle(Color(red: 0.812, green: 0.878, blue: 0.949))
            .padding(.top, 6)
        }
        .padding(.horizontal, 18).padding(.top, 8).padding(.bottom, 18)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(LinearGradient(colors: [.uaBlue, Color(red: 0.016, green: 0.082, blue: 0.173)],
                                   startPoint: .topLeading, endPoint: .bottomTrailing))
    }
}

struct UAPrimaryButton: View {
    let title: String; let action: () -> Void
    @State private var pressed = false
    var body: some View {
        Button(action: action) {
            Text(title).font(.uaButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).padding(.vertical, 14)
                .background(RoundedRectangle(cornerRadius: 4)
                    .fill(pressed ? Color.uaRhapsodyPress : Color.uaRhapsody))
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .onChanged { _ in pressed = true }.onEnded { _ in pressed = false })
    }
}

struct UAStatusChip: View {
    enum Kind { case onTime, boarding, delayed, canceled }
    let kind: Kind; let label: String
    var body: some View {
        let (fg, bg): (Color, Color) = {
            switch kind {
            case .onTime:   return (.uaOnTimeDark,   Color.uaOnTime.opacity(0.18))
            case .boarding: return (.uaBoardingDark, Color.uaBoarding.opacity(0.22))
            case .delayed:  return (.uaDelayedDark,  Color.uaDelayed.opacity(0.18))
            case .canceled: return (.uaCanceledDark, Color.uaCanceled.opacity(0.18))
            }
        }()
        Text(label).font(.uaStatusPill).foregroundStyle(fg)
            .padding(.horizontal, 14).padding(.vertical, 7)
            .background(Capsule().fill(bg))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct UATabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            BookView().tabItem { Label("Book", systemImage: "magnifyingglass") }
            FlightsView().tabItem { Label("Flights", systemImage: "airplane") }
            TripsView().tabItem { Label("Trips", systemImage: "rectangle.stack") }
            MileagePlusView().tabItem { Label("MileagePlus", systemImage: "person.crop.circle") }
        }
        .tint(.uaRhapsody) // active = Rhapsody Blue, no Material pill
    }
}
```

## 5. Motion

```swift
// Aircraft glide on flight-detail appear
withAnimation(.easeInOut(duration: 0.9)) { aircraftProgress = 1.0 }

// Seat select — spring pop + light haptic
UIImpactFeedbackGenerator(style: .light).impactOccurred()
withAnimation(.spring(response: 0.2, dampingFraction: 0.6)) { seatSelected = id }

// Status change — cross-fade + soft haptic
withAnimation(.easeInOut(duration: 0.25)) { statusKind = newKind }
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Boarding pass open — spring scale + brightness boost
withAnimation(.spring(response: 0.35, dampingFraction: 0.8)) { passExpanded = true }
UIScreen.main.brightness = 1.0   // restore prior value on dismiss

// Check-in success
UINotificationFeedbackGenerator().notificationOccurred(.success)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Book (tab) | `magnifyingglass` | 22pt |
| Flights (tab) | `airplane` | 22pt |
| Trips (tab) | `rectangle.stack` / `.fill` | 22pt |
| MileagePlus (tab) | `person.crop.circle` / `.fill` | 22pt |
| Aircraft (header) | `airplane` | 20pt |
| Departed | `airplane.departure` | 16pt |
| Arrived | `airplane.arrival` | 16pt |
| Seat | `chair.lounge.fill` | 16pt |
| Bag tracker | `suitcase.fill` | 18pt |
| Add to Wallet | `wallet.pass` | 18pt |
| Boarding pass | `qrcode` | 20pt |
| Upgrade list | `arrow.up.circle.fill` | 18pt |
| Gate change | `exclamationmark.triangle.fill` | 16pt |
| Premier tier | `star.circle.fill` | 18pt |
| Back | `chevron.left` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Refresh | `arrow.clockwise` | 18pt |

## 7. Dark Mode

```swift
struct UATheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.uaDarkCanvas : Color.uaCanvas)
            .foregroundStyle(scheme == .dark ? Color.uaDarkTextPrimary : Color.uaTextPrimary)
    }
}
extension View { func uaTheme() -> some View { modifier(UATheme()) } }
```

Dark canvas is `#0A1424` — a deep navy, **never** neutral black, so the United Blue boarding-pass header and flight-detail gradient still read as United. Rhapsody Blue `#1414FF` stays identical across themes — it's the electric action accent (button, selected seat, active tab). The barcode inverts to a light bar pattern in dark mode (see `BarcodeStripe`) so gate scanners read a dimmed screen. Shadows nearly vanish on the dark canvas — a 1pt `#243652` border doubles as the elevation cue. Status colors swap to lighter variants on the same translucent fills.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Open Sans TTFs via `Info.plist` `UIAppFonts` — Apache 2.0, free to distribute. (Production United uses its licensed corporate face.)
- Dynamic Type: scale route IATA, screen title, section, body, meta; keep the barcode, seat-map cells, 10pt cell labels, 10pt tab labels, status-pill text FIXED. Data values scale one step then clamp to preserve the 3-cell grid
- VoiceOver: label the pass "Boarding pass, {name}, gate {gate}, seat {seat}, boards {time}, {premierGroup}"; label each seat "Seat {id}, {Economy Plus / standard / taken / selected}, {row}"; the status pill announces the full status word
- Color contrast: `#E9EEF6` on `#0A1424` and `#0C1F38` on `#FFFFFF` pass WCAG AA; status colors are paired against translucent fills validated for AA
- Reduce Motion: disable the aircraft glide and seat-pop spring; keep the status cross-fade as an instant color swap; keep the selected-seat state (it conveys selection)
- Brightness: when the full-screen pass opens, raise `UIScreen.main.brightness`; restore the previous value on dismiss
- Dark mode: invert via the `uaDark*` palette; the barcode must remain machine-scannable — never below the high-contrast bar pattern
