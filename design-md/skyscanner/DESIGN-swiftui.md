# Skyscanner (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Skyscanner's visual language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, `ViewModifier`s, and the signature price-calendar / flight-row / sort-tab components.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let skyCanvas        = Color.white                                    // #FFFFFF
    static let skySurface1Light = Color(red: 0.961, green: 0.969, blue: 0.980)  // #F5F7FA
    static let skySurface2Light = Color(red: 0.914, green: 0.929, blue: 0.949)  // #E9EDF2
    static let skyDividerLight  = Color(red: 0.886, green: 0.906, blue: 0.925)  // #E2E7EC

    // MARK: - Canvas & Surfaces (Dark)
    static let skyDarkCanvas    = Color(red: 0.043, green: 0.059, blue: 0.078)  // #0B0F14
    static let skyDarkSurface1  = Color(red: 0.078, green: 0.102, blue: 0.133)  // #141A22
    static let skyDarkSurface2  = Color(red: 0.110, green: 0.141, blue: 0.180)  // #1C242E
    static let skyDarkDivider   = Color(red: 0.145, green: 0.188, blue: 0.235)  // #25303C

    // MARK: - Text
    static let skyTextPrimary    = Color(red: 0.055, green: 0.106, blue: 0.173) // #0E1B2C
    static let skyTextSecondary  = Color(red: 0.361, green: 0.420, blue: 0.478) // #5C6B7A
    static let skyTextTertiary   = Color(red: 0.545, green: 0.592, blue: 0.639) // #8B97A3
    static let skyDarkTextPrim   = Color(red: 0.910, green: 0.929, blue: 0.949) // #E8EDF2
    static let skyDarkTextSec    = Color(red: 0.604, green: 0.655, blue: 0.706) // #9AA7B4

    // MARK: - Brand
    static let skyBlue        = Color(red: 0.027, green: 0.439, blue: 0.890)    // #0770E3
    static let skyBluePressed = Color(red: 0.039, green: 0.357, blue: 0.729)    // #0A5BBA
    static let skyBright      = Color(red: 0.020, green: 0.659, blue: 0.980)    // #05A8FA
    static let skyDeepNavy    = Color(red: 0.020, green: 0.125, blue: 0.235)    // #05203C

    // MARK: - Price Traffic-Light (theme-invariant)
    static let skyPriceLow  = Color(red: 0.000, green: 0.651, blue: 0.596)      // #00A698
    static let skyPriceAvg  = Color(red: 1.000, green: 0.722, blue: 0.110)      // #FFB81C
    static let skyPriceHigh = Color(red: 0.898, green: 0.224, blue: 0.180)      // #E5392E
}
```

## 2. Typography

Skyscanner ships **Skyscanner Relative** (Dalton Maag). Bundle the TTFs via `Info.plist` / `UIAppFonts`; fall back to SF Pro. All money/time styles use `.monospacedDigit()`.

```swift
extension Font {
    static func sky(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        Font.custom("SkyscannerRelative", size: size).weight(weight)
        // Fallback to SF Pro is automatic if the face is absent.
    }

    static let skyDisplay     = sky(32, weight: .heavy)
    static let skyScreenTitle = sky(26, weight: .bold)
    static let skySection     = sky(22, weight: .bold)
    static let skyCardTitle   = sky(18, weight: .bold)
    static let skyFareLarge   = sky(19, weight: .heavy)
    static let skyBody        = sky(16, weight: .regular)
    static let skyPriceInline = sky(15, weight: .semibold)
    static let skyTime        = sky(17, weight: .bold)
    static let skyMeta        = sky(14, weight: .regular)
    static let skyAirport     = sky(12, weight: .semibold)
    static let skyFieldLabel  = sky(12, weight: .semibold)
    static let skyButton      = sky(16, weight: .bold)
    static let skyChip        = sky(13, weight: .bold)
    static let skyTab         = sky(10, weight: .semibold)
    static let skyCalDay      = sky(11, weight: .bold)
    static let skyCalPrice    = sky(8,  weight: .semibold)
}
```

## 3. Signature Components

### Stacked Search Card (From / To / Dates + Swap)

```swift
struct SearchCard: View {
    @Binding var from: String
    @Binding var to: String
    @Binding var dates: String
    let onSwap: () -> Void

    var body: some View {
        VStack(spacing: 0) {
            field(icon: "mappin.and.ellipse", label: "FROM", value: from)
            Divider().background(Color.skyDividerLight)
            field(icon: "mappin.and.ellipse", label: "TO", value: to)
            Divider().background(Color.skyDividerLight)
            field(icon: "calendar", label: "DEPART · RETURN", value: dates)
        }
        .background(Color.skyCanvas)
        .clipShape(RoundedRectangle(cornerRadius: 16))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.skyDividerLight, lineWidth: 1))
        .overlay(alignment: .topTrailing) {
            Button(action: onSwap) {
                Image(systemName: "arrow.up.arrow.down")
                    .font(.system(size: 14, weight: .semibold))
                    .foregroundStyle(Color.skyTextSecondary)
                    .frame(width: 30, height: 30)
                    .background(Circle().fill(Color.skyCanvas))
                    .overlay(Circle().strokeBorder(Color.skyDividerLight, lineWidth: 1))
            }
            .offset(x: -16, y: 56) // centered on the From–To divider
        }
        .padding(.horizontal, 18)
    }

    private func field(icon: String, label: String, value: String) -> some View {
        HStack(spacing: 12) {
            Image(systemName: icon)
                .font(.system(size: 18, weight: .semibold))
                .foregroundStyle(Color.skyBlue)
            VStack(alignment: .leading, spacing: 2) {
                Text(label).font(.skyFieldLabel).foregroundStyle(Color.skyTextTertiary)
                    .textCase(.uppercase).tracking(0.2)
                Text(value).font(.skyPriceInline).foregroundStyle(Color.skyTextPrimary)
            }
            Spacer()
        }
        .padding(.vertical, 14)
        .padding(.horizontal, 16)
    }
}
```

### Month Price Grid (the signature surface)

```swift
struct PriceCalendar: View {
    struct Day: Identifiable {
        let id = UUID()
        let number: Int
        let fare: Int?       // nil = out of month
        enum Band { case low, avg, high }
        let band: Band?
        var inMonth: Bool { fare != nil }
    }
    let days: [Day]
    @Binding var selectedIndex: Int?
    let columns = Array(repeating: GridItem(.flexible(), spacing: 5), count: 7)

    func bandColor(_ b: Day.Band?) -> Color {
        switch b {
        case .low:  return .skyPriceLow
        case .avg:  return .skyPriceAvg
        case .high: return .skyPriceHigh
        case nil:   return .skyTextTertiary
        }
    }

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            Text("Cheapest month — October").font(.skySection).foregroundStyle(Color.skyTextPrimary)
            Text("Green is a good deal · prices per person")
                .font(.system(size: 11)).foregroundStyle(Color.skyTextSecondary)
            LazyVGrid(columns: columns, spacing: 5) {
                ForEach(Array(days.enumerated()), id: \.element.id) { i, day in
                    let selected = selectedIndex == i
                    VStack(spacing: 1) {
                        Text("\(day.number)")
                            .font(.skyCalDay).monospacedDigit()
                            .foregroundStyle(selected ? .white : Color.skyTextPrimary)
                        if let fare = day.fare {
                            Text("£\(fare)")
                                .font(.skyCalPrice).monospacedDigit()
                                .foregroundStyle(selected ? .white : bandColor(day.band))
                        }
                    }
                    .frame(maxWidth: .infinity)
                    .aspectRatio(1, contentMode: .fit)
                    .background(
                        RoundedRectangle(cornerRadius: 7)
                            .fill(selected ? Color.skyBlue : Color.skySurface1Light)
                    )
                    .overlay(
                        RoundedRectangle(cornerRadius: 7)
                            .strokeBorder(Color.skyDividerLight, lineWidth: selected ? 0 : 1)
                    )
                    .opacity(day.inMonth ? 1 : 0.35)
                    .onTapGesture {
                        if day.inMonth {
                            withAnimation(.easeOut(duration: 0.15)) { selectedIndex = i }
                        }
                    }
                }
            }
        }
        .padding(.horizontal, 18)
    }
}
```

### Flight Result Row

```swift
struct FlightRow: View {
    let depTime, depCode, arrTime, arrCode, duration, stops, fare: String
    var stopColor: Color = .skyPriceLow   // green for "Direct"

    var body: some View {
        HStack(alignment: .center, spacing: 12) {
            VStack(alignment: .leading, spacing: 2) {
                Text(depTime).font(.skyTime).monospacedDigit().foregroundStyle(Color.skyTextPrimary)
                Text(depCode).font(.skyAirport).foregroundStyle(Color.skyTextSecondary)
            }
            VStack(spacing: 6) {
                Text(duration).font(.system(size: 11)).foregroundStyle(Color.skyTextTertiary)
                ZStack(alignment: .trailing) {
                    Rectangle().fill(Color.skyDividerLight).frame(height: 1)
                    Circle().fill(Color.skyBlue).frame(width: 5, height: 5)
                }
                Text(stops).font(.system(size: 10, weight: .semibold)).foregroundStyle(stopColor)
            }
            .frame(maxWidth: .infinity)
            VStack(alignment: .trailing, spacing: 2) {
                Text(arrTime).font(.skyTime).monospacedDigit().foregroundStyle(Color.skyTextPrimary)
                Text(arrCode).font(.skyAirport).foregroundStyle(Color.skyTextSecondary)
            }
            VStack(alignment: .trailing, spacing: 2) {
                Text(fare).font(.skyFareLarge).monospacedDigit().foregroundStyle(Color.skyTextPrimary)
                Text("return").font(.skyAirport).foregroundStyle(Color.skyTextSecondary)
            }
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 14).fill(Color.skySurface1Light))
        .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(Color.skyDividerLight, lineWidth: 1))
    }
}
```

### Best / Cheapest / Fastest Sort Tabs

```swift
struct SortTabs: View {
    enum Sort: String, CaseIterable { case best = "Best", cheapest = "Cheapest", fastest = "Fastest" }
    @Binding var selection: Sort
    let fares: [Sort: String]   // e.g. [.cheapest: "£42"]
    @Namespace private var ns

    var body: some View {
        HStack(spacing: 0) {
            ForEach(Sort.allCases, id: \.self) { s in
                VStack(spacing: 4) {
                    Text(s.rawValue)
                        .font(.skyChip)
                        .foregroundStyle(selection == s ? Color.skyTextPrimary : Color.skyTextSecondary)
                    Text(fares[s] ?? "—")
                        .font(.skyPriceInline).monospacedDigit()
                        .foregroundStyle(selection == s ? Color.skyTextPrimary : Color.skyTextSecondary)
                    ZStack {
                        if selection == s {
                            Capsule().fill(Color.skyBlue).frame(height: 2)
                                .matchedGeometryEffect(id: "underline", in: ns)
                        } else {
                            Color.clear.frame(height: 2)
                        }
                    }
                }
                .frame(maxWidth: .infinity)
                .contentShape(Rectangle())
                .onTapGesture { withAnimation(.easeOut(duration: 0.2)) { selection = s } }
            }
        }
    }
}
```

### Price Hint Chip & Everywhere Pill

```swift
struct PriceChip: View {
    let fare: String
    let label: String
    let band: PriceCalendar.Day.Band

    var color: Color { band == .low ? .skyPriceLow : band == .avg ? .skyPriceAvg : .skyPriceHigh }

    var body: some View {
        Text("\(fare) · \(label)")
            .font(.skyChip).foregroundStyle(color)
            .padding(.horizontal, 14).padding(.vertical, 7)
            .background(RoundedRectangle(cornerRadius: 8).fill(color.opacity(0.16)))
    }
}

struct EverywherePill: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: "globe").font(.system(size: 14, weight: .bold))
                Text("Explore Everywhere — find the cheapest destination")
                    .font(.system(size: 12, weight: .semibold))
            }
            .foregroundStyle(Color.skyBright)
        }
        .padding(.horizontal, 18)
    }
}
```

### Primary Search Button

```swift
struct SearchButton: View {
    let title: String
    let action: () -> Void
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.skyButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).frame(height: 50)
                .background(RoundedRectangle(cornerRadius: 12).fill(Color.skyBlue))
                .shadow(color: scheme == .dark ? .clear : Color.skyBlue.opacity(0.45),
                        radius: 18, x: 0, y: 8)
        }
        .buttonStyle(.plain)
        .padding(.horizontal, 18)
        .sensoryFeedback(.impact(weight: .light), trigger: title)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct SkyTabView: View {
    var body: some View {
        TabView {
            ExploreView().tabItem { Label("Explore", systemImage: "paperplane.fill") }
            TripsView().tabItem   { Label("Trips",   systemImage: "list.bullet") }
            SavedView().tabItem   { Label("Saved",   systemImage: "heart") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.skyBlue) // active = Sky Blue, no pill indicator
    }
}
```

## 5. Motion

```swift
// Calendar day select — 150ms ease-out background fill
withAnimation(.easeOut(duration: 0.15)) { selectedIndex = i }

// Sort tab underline — sliding matchedGeometryEffect
withAnimation(.easeOut(duration: 0.2)) { selection = s }

// Swap From/To — rotate 180° + cross-fade values
withAnimation(.easeInOut(duration: 0.25)) { swapped.toggle() }
// .rotationEffect(.degrees(swapped ? 180 : 0)) on the swap glyph

// Live price streaming — staggered row reveal
.transition(.opacity.combined(with: .move(edge: .bottom)))
// per-row: .animation(.easeOut(duration: 0.2).delay(Double(index) * 0.04))

// Price-alert bell bounce
.scaleEffect(alertOn ? 1.0 : 1.0)
.sensoryFeedback(.success, trigger: alertOn)

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: selectedIndex) // calendar/tab
.sensoryFeedback(.success, trigger: priceAlertCreated)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Explore (tab) | `paperplane` / `paperplane.fill` | 22pt |
| Trips (tab) | `list.bullet` | 22pt |
| Saved (tab) | `heart` / `heart.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| From / To field | `mappin.and.ellipse` | 18pt |
| Dates field | `calendar` | 18pt |
| Swap | `arrow.up.arrow.down` | 14pt |
| Everywhere | `globe` | 14pt |
| Price alert | `bell` / `bell.fill` | 18pt |
| Back | `chevron.left` | 20pt |
| Filters | `slider.horizontal.3` | 18pt |
| Sort | `arrow.up.arrow.down.circle` | 18pt |
| Direct flag | `arrow.right` | 12pt |
| Stop indicator | `circle.fill` | 5pt |
| Share trip | `square.and.arrow.up` | 20pt |
| Refresh fares | `arrow.clockwise` | 18pt |

## 7. Dark Mode

```swift
struct SkyTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.skyDarkCanvas : Color.skyCanvas)
            .foregroundStyle(scheme == .dark ? Color.skyDarkTextPrim : Color.skyTextPrimary)
    }
}
extension View { func skyTheme() -> some View { modifier(SkyTheme()) } }

// Surface helpers — pick by scheme
func skySurface1(_ s: ColorScheme) -> Color { s == .dark ? .skyDarkSurface1 : .skySurface1Light }
func skyDivider(_ s: ColorScheme)  -> Color { s == .dark ? .skyDarkDivider  : .skyDividerLight }
// NOTE: skyPriceLow / skyPriceAvg / skyPriceHigh are NEVER swapped — price meaning is theme-invariant.
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Skyscanner Relative TTFs via `Info.plist`; SF Pro fallback is acceptable for the catalog/clone
- **Tabular digits**: apply `.monospacedDigit()` to every fare, time, and calendar price so columns align under Dynamic Type
- Dynamic Type: scale display, titles, body, fares; keep tab labels, airport codes, field labels, and calendar day/price text FIXED (the 7-col grid is layout-sensitive)
- VoiceOver: label calendar cells "October 12, £42, cheap price" (combine date + fare + band so the traffic-light meaning is spoken, not just seen); label flight rows "London Heathrow 07:25 to Barcelona 10:40, 2 hours 15 minutes, direct, £42 return"
- Color is never the only signal: the calendar speaks "cheap/average/expensive" via VoiceOver, and price chips include the word ("Cheap"/"Average"/"Expensive") next to the colored fare for color-blind users
- Contrast: `#0E1B2C` on `#FFFFFF` and `#E8EDF2` on `#0B0F14` pass WCAG AA; amber `#FFB81C` price text is paired with a label and sits on a tint, never as thin text on white alone
- Reduce Motion: disable the swap rotation and the staggered price-stream reveal — use a single crossfade; keep the calendar fill (it conveys selection state)
- Dark mode: invert via `skyDark*`; the Search button's blue glow is dropped on dark (separation comes from the `#141A22` → `#1C242E` surface ramp). The traffic-light trio is **not** inverted.
