# Robinhood (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Robinhood's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the signature portfolio chart, position row, and Trade button.

Targets iOS 17+. The chart scrubber uses `DragGesture`, `Charts` framework, and `.contentTransition(.numericText())` for the real-time hero value morph.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (light)
    static let rhCanvas       = Color(red: 1.00, green: 1.00, blue: 1.00) // #FFFFFF
    static let rhSurfaceGray  = Color(red: 0.969, green: 0.969, blue: 0.969) // #F7F7F7
    static let rhSurfaceGray2 = Color(red: 0.937, green: 0.937, blue: 0.937) // #EFEFEF
    static let rhDivider      = Color(red: 0.902, green: 0.902, blue: 0.902) // #E6E6E6

    // MARK: - Text (light)
    static let rhTextPrimary   = Color(red: 0.00, green: 0.00, blue: 0.00)   // #000000
    static let rhTextSecondary = Color(red: 0.361, green: 0.380, blue: 0.400) // #5C6166
    static let rhTextTertiary  = Color(red: 0.608, green: 0.620, blue: 0.639) // #9B9EA3
    static let rhTextMuted     = Color(red: 0.761, green: 0.773, blue: 0.792) // #C2C5CA

    // MARK: - Brand
    static let rhGreen         = Color(red: 0.00, green: 0.784, blue: 0.020) // #00C805
    static let rhGreenPressed  = Color(red: 0.00, green: 0.659, blue: 0.016) // #00A904
    static let rhGreenDim      = Color(red: 0.129, green: 0.808, blue: 0.600) // #21CE99 heritage
    static let rhGreenBg       = Color(red: 0.902, green: 0.976, blue: 0.878) // #E6F9E0
    static let rhRed           = Color(red: 1.00, green: 0.314, blue: 0.00)  // #FF5000 (orange)
    static let rhRedBg         = Color(red: 1.00, green: 0.929, blue: 0.898) // #FFEDE5

    // MARK: - Semantic
    static let rhErrorTrue     = Color(red: 0.902, green: 0.133, blue: 0.196) // #E62232 true red
    static let rhWarning       = Color(red: 1.00, green: 0.722, blue: 0.00)  // #FFB800
    static let rhInfo          = Color(red: 0.114, green: 0.435, blue: 0.949) // #1D6FF2

    // MARK: - Crypto accents
    static let rhBitcoin       = Color(red: 0.969, green: 0.576, blue: 0.102) // #F7931A
    static let rhEthereum      = Color(red: 0.384, green: 0.494, blue: 0.918) // #627EEA

    // MARK: - Dark mode
    static let rhDarkCanvas    = Color(red: 0.00, green: 0.00, blue: 0.00)    // #000000
    static let rhDarkSurface1  = Color(red: 0.094, green: 0.106, blue: 0.122) // #181B1F
    static let rhDarkSurface2  = Color(red: 0.137, green: 0.153, blue: 0.176) // #23272D
    static let rhDarkDivider   = Color(red: 0.176, green: 0.192, blue: 0.220) // #2D3138
    static let rhDarkTextPri   = Color(red: 1.00, green: 1.00, blue: 1.00)    // #FFFFFF
    static let rhDarkTextSec   = Color(red: 0.643, green: 0.659, blue: 0.678) // #A4A8AD
}
```

## 2. Typography

Capsule Sans is proprietary (drawn by XYZ Type for Robinhood). Bundle the TTFs via `Info.plist` or register at runtime; fall back to SF Pro Text / Inter. ALWAYS apply `.monospacedDigit()` modifier on prices, percentages, and share counts.

```swift
extension Font {
    // Hero portfolio values (Display variant)
    static let rhPortfolioHero = Font.custom("CapsuleSansDisplay-Bold", size: 40)
    static let rhOrderEntry    = Font.custom("CapsuleSansDisplay-Bold", size: 48)

    // Titles & body (Text variant)
    static let rhScreenTitle   = Font.custom("CapsuleSansText-Bold",     size: 22)
    static let rhSectionHeader = Font.custom("CapsuleSansText-Semibold", size: 18)
    static let rhPositionTitle = Font.custom("CapsuleSansText-Semibold", size: 16)
    static let rhPositionValue = Font.custom("CapsuleSansText-Medium",   size: 17)
    static let rhDayChange     = Font.custom("CapsuleSansText-Medium",   size: 16)
    static let rhBody          = Font.custom("CapsuleSansText-Regular",  size: 15)
    static let rhBodySmall     = Font.custom("CapsuleSansText-Regular",  size: 13)

    // Micro
    static let rhTicker        = Font.custom("CapsuleSansText-Medium",   size: 13) // letter-spaced +0.3pt
    static let rhPositionChange = Font.custom("CapsuleSansText-Medium",  size: 13)
    static let rhChartAxis     = Font.custom("CapsuleSansText-Medium",   size: 11)
    static let rhRangeChip     = Font.custom("CapsuleSansText-Semibold", size: 13)
    static let rhTab           = Font.custom("CapsuleSansText-Semibold", size: 10)
    static let rhAllCaps       = Font.custom("CapsuleSansText-Bold",     size: 11) // letter-spaced +0.6pt

    static let rhButton        = Font.custom("CapsuleSansText-Semibold", size: 16)
    static let rhButtonSmall   = Font.custom("CapsuleSansText-Medium",   size: 15)
}

// Fallback if Capsule Sans isn't bundled — SF Pro with tabular digit modifier:
extension Font {
    static func rh(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Portfolio Hero (top of Investing tab)

```swift
struct PortfolioHero: View {
    let portfolioValue: Double      // 24847.93
    let dayChange: Double           // 847.93
    let dayChangePct: Double        // 0.0352

    private var changeColor: Color { dayChange >= 0 ? .rhGreen : .rhRed }
    private var changeSign: String { dayChange >= 0 ? "+" : "−" }

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("BUYING POWER")
                .font(.rhAllCaps)
                .foregroundStyle(Color.rhTextSecondary)
                .tracking(0.6)

            Text(portfolioValue, format: .currency(code: "USD"))
                .font(.rhPortfolioHero)
                .foregroundStyle(Color.rhTextPrimary)
                .monospacedDigit()
                .contentTransition(.numericText())
                .animation(.snappy, value: portfolioValue)

            HStack(spacing: 4) {
                Text("\(changeSign)\(abs(dayChange), format: .currency(code: "USD"))")
                Text("(\(changeSign)\(dayChangePct * 100, specifier: "%.2f")%)")
                Text("Today")
            }
            .font(.rhDayChange)
            .foregroundStyle(changeColor)
            .monospacedDigit()
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(.horizontal, 24)
        .padding(.top, 16)
    }
}
```

### Portfolio Chart with Scrubber

```swift
import Charts

struct PortfolioChart: View {
    let points: [(Date, Double)]
    let isUp: Bool

    @State private var dragPoint: (Date, Double)?

    private var chartColor: Color { isUp ? .rhGreen : .rhRed }

    var body: some View {
        Chart {
            ForEach(Array(points.enumerated()), id: \.offset) { _, p in
                LineMark(x: .value("Time", p.0), y: .value("Value", p.1))
                    .foregroundStyle(chartColor)
                    .interpolationMethod(.linear)
            }
            if let dp = dragPoint {
                RuleMark(x: .value("Time", dp.0))
                    .foregroundStyle(Color.rhTextPrimary)
                    .lineStyle(StrokeStyle(lineWidth: 1))
                PointMark(x: .value("Time", dp.0), y: .value("Value", dp.1))
                    .symbol(.circle)
                    .symbolSize(80)
                    .foregroundStyle(Color.rhTextPrimary)
            }
        }
        .chartXAxis {
            AxisMarks(values: .stride(by: .hour, count: 2)) {
                AxisValueLabel(format: .dateTime.hour())
                    .font(.rhChartAxis)
                    .foregroundStyle(Color.rhTextSecondary)
            }
        }
        .chartYAxis(.hidden)
        .chartPlotStyle { plot in
            plot
                .background(Color.clear)
        }
        .chartOverlay { proxy in
            GeometryReader { geo in
                Rectangle()
                    .fill(.clear)
                    .contentShape(Rectangle())
                    .gesture(DragGesture(minimumDistance: 0)
                        .onChanged { value in
                            guard let x = proxy.value(atX: value.location.x, as: Date.self) else { return }
                            if let p = points.min(by: { abs($0.0.timeIntervalSince(x)) < abs($1.0.timeIntervalSince(x)) }) {
                                dragPoint = p
                            }
                        }
                        .onEnded { _ in dragPoint = nil }
                    )
            }
        }
        .frame(height: 220)
        .chartLegend(.hidden)
    }
}
```

### Chart Range Chips

```swift
struct ChartRangeChips: View {
    @Binding var selected: Range
    let isUp: Bool

    enum Range: String, CaseIterable { case d="1D", w="1W", m="1M", m3="3M", ytd="YTD", y="1Y", y5="5Y", all="ALL" }

    private var activeColor: Color { isUp ? .rhGreen : .rhRed }

    var body: some View {
        HStack(spacing: 8) {
            ForEach(Range.allCases, id: \.self) { r in
                Button(action: { withAnimation(.spring(response: 0.2)) { selected = r } }) {
                    VStack(spacing: 4) {
                        Text(r.rawValue)
                            .font(.rhRangeChip)
                            .tracking(0.3)
                            .foregroundStyle(selected == r ? activeColor : Color.rhTextSecondary)
                        Rectangle()
                            .fill(selected == r ? activeColor : Color.clear)
                            .frame(height: 2)
                            .frame(maxWidth: .infinity)
                    }
                    .frame(minWidth: 44, minHeight: 32)
                }
                .sensoryFeedback(.selection, trigger: selected)
            }
        }
        .padding(.horizontal, 16)
    }
}
```

### Position Row

```swift
struct PositionRow: View {
    let ticker: String           // "AAPL"
    let company: String          // "Apple Inc."
    let shares: Double
    let marketValue: Double
    let dayChange: Double
    let dayChangePct: Double
    let logo: Image?

    private var changeColor: Color { dayChange >= 0 ? .rhGreen : .rhRed }
    private var changeSign: String { dayChange >= 0 ? "+" : "−" }

    var body: some View {
        HStack(spacing: 12) {
            // Ticker icon (32pt rounded square)
            Group {
                if let logo { logo.resizable() }
                else {
                    RoundedRectangle(cornerRadius: 8)
                        .fill(Color.rhSurfaceGray)
                        .overlay(Text(String(ticker.prefix(1)))
                            .font(.custom("CapsuleSansText-Bold", size: 14))
                            .foregroundStyle(Color.rhTextPrimary))
                }
            }
            .frame(width: 32, height: 32)
            .clipShape(RoundedRectangle(cornerRadius: 8))

            // Company + ticker · shares
            VStack(alignment: .leading, spacing: 2) {
                Text(company)
                    .font(.rhPositionTitle)
                    .foregroundStyle(Color.rhTextPrimary)
                HStack(spacing: 4) {
                    Text(ticker)
                        .font(.rhTicker)
                        .tracking(0.3)
                    Text("· \(shares.formatted(.number.precision(.fractionLength(0...4)))) shares")
                        .font(.rhBodySmall)
                }
                .foregroundStyle(Color.rhTextSecondary)
            }

            Spacer()

            // Market value + day change
            VStack(alignment: .trailing, spacing: 2) {
                Text(marketValue, format: .currency(code: "USD"))
                    .font(.rhPositionValue)
                    .foregroundStyle(Color.rhTextPrimary)
                    .monospacedDigit()
                HStack(spacing: 4) {
                    Text("\(changeSign)\(abs(dayChange), format: .currency(code: "USD"))")
                    Text("(\(changeSign)\(dayChangePct * 100, specifier: "%.2f")%)")
                }
                .font(.rhPositionChange)
                .foregroundStyle(changeColor)
                .monospacedDigit()
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 56)
        .background(Color.rhCanvas)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.rhDivider).frame(height: 0.5)
        }
    }
}
```

### Trade Button (Sticky Footer)

```swift
struct TradeButton: View {
    let label: String
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.rhButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 48)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.rhTextPrimary))
        }
        .padding(.horizontal, 16)
        .padding(.bottom, 16)
        .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
    }
}
```

### Buy / Sell Pair (within Trade modal)

```swift
struct BuySellButtons: View {
    var onBuy: () -> Void
    var onSell: () -> Void

    var body: some View {
        HStack(spacing: 12) {
            Button(action: onSell) {
                Text("Sell")
                    .font(.rhButton).foregroundStyle(.white)
                    .frame(maxWidth: .infinity).frame(height: 48)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.rhRed))
            }
            Button(action: onBuy) {
                Text("Buy")
                    .font(.rhButton).foregroundStyle(.white)
                    .frame(maxWidth: .infinity).frame(height: 48)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.rhGreen))
            }
        }
        .padding(.horizontal, 16)
    }
}
```

## 4. Tab Bar

```swift
struct RobinhoodTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(red: 0.902, green: 0.902, blue: 0.902, alpha: 1)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            InvestingView() .tabItem { Label("Investing", systemImage: "chart.line.uptrend.xyaxis") }
            SearchView()    .tabItem { Label("Search",    systemImage: "magnifyingglass") }
            CryptoView()    .tabItem { Label("Crypto",    systemImage: "bitcoinsign.circle") }
            CashView()      .tabItem { Label("Cash",      systemImage: "wallet.pass") }
            AccountView()   .tabItem { Label("Account",   systemImage: "person.crop.circle") }
        }
        .tint(Color.rhTextPrimary)
    }
}
```

## 5. Motion & Haptics

```swift
// Chart scrubber drag — instant response, no smoothing
.gesture(DragGesture(minimumDistance: 0).onChanged { ... })

// Portfolio value update during scrub
.contentTransition(.numericText())
.animation(.snappy(duration: 0.1), value: portfolioValue)

// Buy / Sell tap
.sensoryFeedback(.impact(weight: .medium), trigger: tradePressed)

// Order placed
.sensoryFeedback(.success, trigger: orderConfirmed)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// Range chip switch
withAnimation(.spring(response: 0.2, dampingFraction: 0.8)) { selected = newRange }
```

## 6. SF Symbols (Robinhood's icon equivalents)

| Component | Symbol | Size |
|-----------|--------|------|
| Investing tab | `chart.line.uptrend.xyaxis` | 24pt |
| Search tab | `magnifyingglass` | 24pt |
| Crypto tab | `bitcoinsign.circle` / `.fill` | 24pt |
| Cash tab | `wallet.pass` / `.fill` | 24pt |
| Account tab | `person.crop.circle` / `.fill` | 24pt |
| Search icon (in field) | `magnifyingglass` | 16pt |
| Set alert | `bell` / `bell.fill` | 18pt |
| Add to list | `plus.circle` | 18pt |
| Back | `chevron.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Order placed (success) | `checkmark.circle.fill` | 32pt |
| Order failed | `xmark.circle.fill` | 32pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`, `.contentTransition(.numericText())`, Swift Charts framework); on iOS 16, replace `Charts` with a hand-built `Path` line graph
- Tabular numerals are CRITICAL: every `Text` rendering a price, percentage, or share count must use `.monospacedDigit()` — Capsule Sans's default tabular feature is the brand-correct rendering, but `.monospacedDigit()` on the SF Pro fallback guarantees alignment
- VoiceOver: portfolio hero should announce "Buying power $24,847.93, up $847.93, up 3.52 percent today"; position rows should announce "Apple Inc., A A P L, 4 shares, market value $1,247.92, up $24.13, up 1.97 percent today"
- Color-blind users: green/orange is the primary signal — pair with directional symbols ("+"/"−") and ensure the day-change pill is never color-only. Add an `up.arrow` / `down.arrow` SF Symbol prefix on the position row's change text when `accessibilityDifferentiateWithoutColor` is true
- Reduce Motion: skip the numeric-text morph animation during chart scrubbing — use instant text updates instead
- Contrast: `#000000` on `#FFFFFF` is maximum; `#5C6166` on white meets AA at 14pt+; `#9B9EA3` (inactive tab labels) meets AA only at 18pt+ — keep 10pt tab labels at `#000000` once tab is selected
- Dark mode: switch entire palette via `@Environment(\.colorScheme)`; `#00C805` (green) and `#FF5000` (orange) stay identical — they read electric on `#000000`
- Dynamic Type: the portfolio hero (40pt) scales but should clamp at 56pt to preserve layout; tab labels, chart axis labels, and range chips are fixed
