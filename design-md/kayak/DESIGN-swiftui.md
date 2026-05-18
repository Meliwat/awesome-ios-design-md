# KAYAK (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates KAYAK's visual language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views (the fare card, price-calendar strip, forecast banner, fare-compare matrix).

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let kykCanvas        = Color.white                                    // #FFFFFF
    static let kykSurfaceGray   = Color(red: 0.957, green: 0.961, blue: 0.969)  // #F4F5F7
    static let kykSurfacePressed = Color(red: 0.914, green: 0.922, blue: 0.933) // #E9EBEE
    static let kykDivider       = Color(red: 0.886, green: 0.898, blue: 0.914)  // #E2E5E9

    // MARK: - Canvas & Surfaces (Dark) — near-neutral, NO brand tint
    static let kykDarkCanvas    = Color(red: 0.063, green: 0.071, blue: 0.078)  // #101214
    static let kykDarkSurface1  = Color(red: 0.094, green: 0.106, blue: 0.118)  // #181B1E
    static let kykDarkSurface2  = Color(red: 0.129, green: 0.149, blue: 0.169)  // #21262B
    static let kykDarkDivider   = Color(red: 0.173, green: 0.200, blue: 0.220)  // #2C3338

    // MARK: - Text
    static let kykTextPrimary   = Color(red: 0.086, green: 0.098, blue: 0.110)  // #16191C
    static let kykTextSecondary = Color(red: 0.361, green: 0.396, blue: 0.431)  // #5C656E
    static let kykTextTertiary  = Color(red: 0.545, green: 0.580, blue: 0.612)  // #8B949C
    static let kykDarkTextPrimary   = Color(red: 0.914, green: 0.925, blue: 0.937) // #E9ECEF
    static let kykDarkTextSecondary = Color(red: 0.608, green: 0.639, blue: 0.671) // #9BA3AB

    // MARK: - Brand (single accent)
    static let kykOrange        = Color(red: 1.000, green: 0.412, blue: 0.059)  // #FF690F
    static let kykOrangePressed = Color(red: 0.878, green: 0.337, blue: 0.039)  // #E0560A
    static let kykOrangeSoft    = Color(red: 1.000, green: 0.541, blue: 0.259)  // #FF8A42
    static let kykLink          = Color(red: 0.180, green: 0.486, blue: 0.965)  // #2E7CF6

    // MARK: - Functional semantic (NOT brand)
    static let kykPriceLow      = Color(red: 0.118, green: 0.620, blue: 0.353)  // #1E9E5A
    static let kykPriceHigh     = Color(red: 0.898, green: 0.282, blue: 0.302)  // #E5484D
    static let kykWait          = Color(red: 0.910, green: 0.639, blue: 0.090)  // #E8A317
}

enum PriceBand { case low, mid, high }

func kykPriceColor(_ band: PriceBand, dark: Bool = false) -> Color {
    switch band {
    case .low:  return .kykPriceLow
    case .mid:  return dark ? .kykDarkTextPrimary : .kykTextPrimary
    case .high: return .kykPriceHigh
    }
}

enum ForecastAdvice { case buy, wait, rise }

func kykAdviceColor(_ a: ForecastAdvice) -> Color {
    switch a {
    case .buy:  return .kykPriceLow
    case .wait: return .kykWait
    case .rise: return .kykPriceHigh
    }
}
```

## 2. Typography

KAYAK's product face → SF Pro fallback (Inter is the closest free substitute). Prices, times, durations, and matrix cells use **monospaced (tabular) digits**.

```swift
extension Font {
    static func kayak(_ size: CGFloat, _ weight: Font.Weight = .regular) -> Font {
        Font.system(size: size, weight: weight, design: .default)
    }

    static let kykDisplay   = Font.system(size: 32, weight: .heavy)
    static let kykRoute     = Font.system(size: 26, weight: .heavy)
    static let kykSection   = Font.system(size: 22, weight: .bold)
    static let kykBody      = Font.system(size: 16, weight: .regular)
    static let kykLabel     = Font.system(size: 14, weight: .semibold)
    static let kykMeta      = Font.system(size: 13, weight: .regular)
    static let kykCalDay    = Font.system(size: 11, weight: .semibold)
    static let kykTag       = Font.system(size: 11, weight: .bold)
    static let kykButton    = Font.system(size: 16, weight: .bold)
    static let kykTab       = Font.system(size: 10, weight: .semibold)

    // Numeric — always tabular
    static let kykPrice     = Font.system(size: 20, weight: .heavy).monospacedDigit()
    static let kykLegTime   = Font.system(size: 18, weight: .heavy).monospacedDigit()
    static let kykCalPrice  = Font.system(size: 13, weight: .heavy).monospacedDigit()
    static let kykMatrixNum = Font.system(size: 12, weight: .heavy).monospacedDigit()
}
```

## 3. Signature Components

### Fare Card

```swift
struct FareCard: View {
    let airlineCode: String       // "AS"
    let airlineName: String       // "Alaska · Delta"
    let isHackerFare: Bool
    let departTime: String        // "7:05"
    let departCode: String        // "SFO"
    let arriveTime: String        // "3:33"
    let arriveCode: String        // "JFK"
    let duration: String          // "5h 28m"
    let stopsText: String         // "1 stop · SEA" / "Nonstop"
    let nonstop: Bool
    let provider: String          // "2 booking sites"
    let price: Int                // 247

    var body: some View {
        VStack(spacing: 0) {
            HStack {
                HStack(spacing: 8) {
                    Text(airlineCode)
                        .font(.system(size: 10, weight: .heavy))
                        .foregroundStyle(Color.kykTextSecondary)
                        .frame(width: 26, height: 26)
                        .background(RoundedRectangle(cornerRadius: 7).fill(Color.kykSurfaceGray))
                    Text(airlineName).font(.system(size: 12, weight: .semibold))
                        .foregroundStyle(Color.kykTextSecondary)
                }
                Spacer()
                if isHackerFare {
                    Text("HACKER FARE")
                        .font(.kykTag).tracking(0.3).foregroundStyle(.white)
                        .padding(.vertical, 3).padding(.horizontal, 7)
                        .background(RoundedRectangle(cornerRadius: 5).fill(Color.kykOrange))
                }
            }

            HStack(spacing: 12) {
                VStack(spacing: 2) {
                    Text(departTime).font(.kykLegTime).foregroundStyle(Color.kykTextPrimary)
                    Text(departCode).font(.system(size: 11)).foregroundStyle(Color.kykTextSecondary)
                }
                VStack(spacing: 6) {
                    Text(duration).font(.system(size: 11)).foregroundStyle(Color.kykTextSecondary)
                    ZStack(alignment: .trailing) {
                        Rectangle().fill(Color.kykDivider).frame(height: 1)
                        Circle().fill(Color.kykTextTertiary).frame(width: 5, height: 5)
                    }
                    Text(stopsText)
                        .font(.system(size: 11, weight: .bold))
                        .foregroundStyle(nonstop ? Color.kykTextSecondary : Color.kykOrange)
                }
                .frame(maxWidth: .infinity)
                VStack(spacing: 2) {
                    Text(arriveTime).font(.kykLegTime).foregroundStyle(Color.kykTextPrimary)
                    Text(arriveCode).font(.system(size: 11)).foregroundStyle(Color.kykTextSecondary)
                }
            }
            .padding(.top, 12)

            HStack(alignment: .bottom) {
                Text(provider).font(.system(size: 11)).foregroundStyle(Color.kykTextSecondary)
                Spacer()
                VStack(alignment: .trailing, spacing: 0) {
                    Text("$\(price)").font(.kykPrice).foregroundStyle(Color.kykTextPrimary)
                    Text("round-trip").font(.system(size: 11)).foregroundStyle(Color.kykTextSecondary)
                }
            }
            .padding(.top, 12)
            .overlay(alignment: .top) {
                Rectangle().fill(Color.kykDivider).frame(height: 0.5)
            }
            .padding(.top, 2)
        }
        .padding(14)
        .background(RoundedRectangle(cornerRadius: 14).fill(Color.kykCanvas))
        .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(Color.kykDivider, lineWidth: 0.5))
        .shadow(color: .black.opacity(0.06), radius: 8, y: 2)
    }
}
```

### Price-Calendar Strip

```swift
struct PriceCalendarStrip: View {
    struct Day: Identifiable { let id = UUID(); let label: String; let price: Int; let band: PriceBand }
    let days: [Day]
    @Binding var selectedIndex: Int

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 6) {
                ForEach(Array(days.enumerated()), id: \.offset) { i, day in
                    let sel = i == selectedIndex
                    VStack(spacing: 4) {
                        Text(day.label)
                            .font(.kykCalDay)
                            .foregroundStyle(sel ? .white : Color.kykTextSecondary)
                        Text("$\(day.price)")
                            .font(.kykCalPrice)
                            .foregroundStyle(sel ? .white : kykPriceColor(day.band))
                    }
                    .frame(width: 50)
                    .padding(.vertical, 8)
                    .background(
                        RoundedRectangle(cornerRadius: 10)
                            .fill(sel ? Color.kykOrange : Color.kykCanvas)
                    )
                    .overlay(
                        RoundedRectangle(cornerRadius: 10)
                            .strokeBorder(sel ? Color.clear : Color.kykDivider, lineWidth: 0.5)
                    )
                    .onTapGesture {
                        withAnimation(.easeOut(duration: 0.16)) { selectedIndex = i }
                    }
                }
            }
            .padding(.horizontal, 16)
        }
    }
}
```

### Forecast Banner

```swift
struct ForecastBanner: View {
    let advice: ForecastAdvice
    let title: String       // "Prices are likely to rise"
    let subtitle: String    // "We recommend booking soon · 87% confidence"

    private var tint: Color {
        switch advice {
        case .buy:  return Color.kykPriceLow
        case .wait: return Color.kykWait
        case .rise: return Color.kykOrange
        }
    }
    private var glyph: String {
        switch advice {
        case .buy:  return "checkmark.circle"
        case .wait: return "clock"
        case .rise: return "chart.line.uptrend.xyaxis"
        }
    }

    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: glyph)
                .font(.system(size: 18, weight: .bold))
                .foregroundStyle(tint)
            VStack(alignment: .leading, spacing: 1) {
                Text(title).font(.system(size: 12, weight: .bold))
                    .foregroundStyle(Color.kykTextPrimary)
                Text(subtitle).font(.system(size: 11))
                    .foregroundStyle(Color.kykTextSecondary)
            }
            Spacer()
        }
        .padding(.vertical, 11).padding(.horizontal, 13)
        .background(RoundedRectangle(cornerRadius: 10).fill(tint.opacity(0.12)))
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(tint.opacity(0.4), lineWidth: 0.5))
        .padding(.horizontal, 16)
    }
}
```

### Fare-Compare Matrix

```swift
struct FareMatrix: View {
    struct Row: Identifiable { let id = UUID(); let airline: String; let cells: [Int?]; let bestIndex: Int? }
    let columns: [String]   // ["Nonstop", "1 stop", "2+ stops"]
    let rows: [Row]

    var body: some View {
        VStack(spacing: 0) {
            HStack(spacing: 0) {
                Text("Airline").font(.system(size: 12, weight: .semibold))
                    .foregroundStyle(Color.kykTextPrimary)
                    .frame(maxWidth: .infinity, alignment: .leading)
                    .layoutPriority(1.4)
                ForEach(columns, id: \.self) { c in
                    Text(c).font(.system(size: 12)).foregroundStyle(Color.kykTextSecondary)
                        .frame(maxWidth: .infinity)
                }
            }
            .padding(.vertical, 10).padding(.horizontal, 8)
            .background(Color.kykSurfaceGray)

            ForEach(rows) { row in
                HStack(spacing: 0) {
                    Text(row.airline).font(.system(size: 12, weight: .semibold))
                        .foregroundStyle(Color.kykTextPrimary)
                        .frame(maxWidth: .infinity, alignment: .leading)
                        .layoutPriority(1.4)
                    ForEach(Array(row.cells.enumerated()), id: \.offset) { ci, value in
                        let best = row.bestIndex == ci
                        Text(value.map { "$\($0)" } ?? "—")
                            .font(.kykMatrixNum)
                            .foregroundStyle(value == nil ? Color.kykTextTertiary
                                             : best ? Color.kykPriceLow : Color.kykTextPrimary)
                            .frame(maxWidth: .infinity)
                    }
                }
                .padding(.vertical, 10).padding(.horizontal, 8)
                .overlay(alignment: .top) { Rectangle().fill(Color.kykDivider).frame(height: 0.5) }
            }
        }
        .clipShape(RoundedRectangle(cornerRadius: 10))
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.kykDivider, lineWidth: 0.5))
    }
}
```

### Primary Button

```swift
struct ViewDealButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.kykButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).padding(.vertical, 14)
        }
        .background(RoundedRectangle(cornerRadius: 10).fill(Color.kykOrange))
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct KayakTabView: View {
    var body: some View {
        TabView {
            SearchScreen().tabItem { Label("Search", systemImage: "magnifyingglass") }
            TrendsScreen().tabItem { Label("Trends", systemImage: "chart.line.uptrend.xyaxis") }
            TripsScreen().tabItem { Label("Trips", systemImage: "bookmark") }
            AlertsScreen().tabItem { Label("Price Alerts", systemImage: "bell") }
            ProfileScreen().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.kykOrange) // active = KAYAK Orange, no pill indicator
    }
}
```

## 5. Motion

```swift
// Calendar day select — orange fill + fare list cross-dissolve
withAnimation(.easeOut(duration: 0.16)) { selectedIndex = i }
// fare list:
.transition(.opacity) // 220ms; show skeleton shimmer while re-pricing

// Forecast banner reveal
.transition(.move(edge: .top).combined(with: .opacity)) // 240ms ease-out

// Fare card → detail — push 300ms (navigation default); leg row anchors

// Matrix cell tap — flash orange tint
withAnimation(.easeOut(duration: 0.14)) { flashedCell = id }

// Price-drop pulse (Price Alerts)
withAnimation(.easeInOut(duration: 0.18).repeatCount(1, autoreverses: true)) { pulse.toggle() }

// Sort/filter sheet
.presentationDetents([.medium, .large]) // slides up ~300ms

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: selectedIndex)  // calendar select
.sensoryFeedback(.success, trigger: priceDropCaptured)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search (tab) | `magnifyingglass` | 22pt |
| Trends (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Trips (tab) | `bookmark` / `bookmark.fill` | 22pt |
| Price Alerts (tab) | `bell` / `bell.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Route arrow | `arrow.right` | 16pt |
| Forecast rise | `chart.line.uptrend.xyaxis` | 18pt |
| Forecast buy | `checkmark.circle` | 18pt |
| Forecast wait | `clock` | 18pt |
| Swap origin/dest | `arrow.up.arrow.down` | 18pt |
| Filter | `slider.horizontal.3` | 18pt |
| Sort | `arrow.up.arrow.down.circle` | 18pt |
| Flight | `airplane` | 18pt |
| Stay | `bed.double` | 18pt |
| Car | `car` | 18pt |
| Calendar | `calendar` | 18pt |
| Travelers | `person.2` | 18pt |
| Price up | `arrow.up.right` | 14pt |
| Price down | `arrow.down.right` | 14pt |
| Share | `square.and.arrow.up` | 20pt |

## 7. Dark Mode

```swift
struct KayakTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.kykDarkCanvas : Color.kykSurfaceGray)
            .foregroundStyle(scheme == .dark ? Color.kykDarkTextPrimary : Color.kykTextPrimary)
    }
}
extension View { func kayakTheme() -> some View { modifier(KayakTheme()) } }
```

- Dark canvas is `#101214` — near-neutral with NO brand tint so the lone orange accent stays loud; cards lift to `#181B1E`.
- KAYAK Orange `#FF690F` is identical across themes; price-signal green/red and forecast amber are functional, also unchanged.
- For `mid` price-band text, pass `dark: true` to `kykPriceColor` so it resolves to `#E9ECEF`.
- Shadows deepen to 0.4–0.6 opacity on dark; airline logos keep brand color.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` on 16).
- KAYAK's product face is proprietary — SF Pro fallback is the safe default; Inter is the closest free substitute for parity testing.
- Dynamic Type: scale display, route, section, body, prices, leg times; pin calendar day/price, Hacker Fare tag, tab labels, and matrix cells (the strip + grid are layout-sensitive).
- Prices, times, durations, and matrix cells must use `.monospacedDigit()` so the calendar columns and matrix align.
- VoiceOver: label the fare card "{airlineName}, departs {departTime} {departCode}, arrives {arriveTime} {arriveCode}, {stopsText}, {price} dollars round-trip"; the calendar cell "{label}, {price} dollars, {low/typical/high}"; the forecast banner reads title + subtitle; the Hacker Fare tag exposed as "Hacker Fare, two separate one-way tickets".
- **Don't encode price band by color alone**: the calendar/ matrix also convey cheapness via position and the forecast text — keep an accessible label ("lowest price these dates") so colorblind users get the signal.
- Color contrast: white on KAYAK Orange `#FF690F` passes WCAG AA at button sizes; the price-signal green/red on white/dark pass AA for the small bold figures used.
- Reduce Motion: replace the fare-list cross-dissolve with an instant swap; drop the price-drop pulse; keep the calendar fill (conveys selection).
- Dark mode: invert via the `kykDark*` palette; keep orange + functional semantics fixed; add a 0.5pt `kykDarkDivider` border to floating sheets as the elevation cue.
