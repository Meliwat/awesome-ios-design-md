# Fidelity (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Fidelity's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. The recurring rule: **money is tabular**, and **green/red mean gain/loss only**.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let fidGreen        = Color(red: 0.212, green: 0.529, blue: 0.153) // #368727
    static let fidGreenPressed = Color(red: 0.165, green: 0.420, blue: 0.122) // #2A6B1F
    static let fidHeritage     = Color(red: 0.000, green: 0.459, blue: 0.290) // #00754A
    static let fidLeaf         = Color(red: 0.310, green: 0.698, blue: 0.231) // #4FB23B

    // MARK: - Canvas & Surfaces (Light)
    static let fidCanvas        = Color.white                                    // #FFFFFF
    static let fidSurface       = Color(red: 0.957, green: 0.965, blue: 0.957)  // #F4F6F4
    static let fidSurfacePressed = Color(red: 0.910, green: 0.925, blue: 0.910) // #E8ECE8
    static let fidDivider       = Color(red: 0.882, green: 0.898, blue: 0.882)  // #E1E5E1

    // MARK: - Canvas & Surfaces (Dark)
    static let fidDarkCanvas   = Color(red: 0.055, green: 0.078, blue: 0.067) // #0E1411
    static let fidDarkSurface1 = Color(red: 0.086, green: 0.125, blue: 0.106) // #16201B
    static let fidDarkSurface2 = Color(red: 0.122, green: 0.173, blue: 0.141) // #1F2C24
    static let fidDarkDivider  = Color(red: 0.149, green: 0.204, blue: 0.169) // #26342B

    // MARK: - Text
    static let fidTextPrimary    = Color(red: 0.102, green: 0.122, blue: 0.106) // #1A1F1B
    static let fidTextSecondary  = Color(red: 0.353, green: 0.396, blue: 0.361) // #5A655C
    static let fidTextTertiary   = Color(red: 0.541, green: 0.576, blue: 0.549) // #8A938C
    static let fidDarkTextPrimary   = Color(red: 0.910, green: 0.937, blue: 0.914) // #E8EFE9
    static let fidDarkTextSecondary = Color(red: 0.616, green: 0.690, blue: 0.635) // #9DB0A2
    static let fidDarkTextTertiary  = Color(red: 0.420, green: 0.494, blue: 0.447) // #6B7E72

    // MARK: - Financial Semantics (sacred — gain/loss only)
    static let fidGainLight = Color(red: 0.082, green: 0.514, blue: 0.243) // #15833E
    static let fidGainDark  = Color(red: 0.082, green: 0.702, blue: 0.455) // #15B374
    static let fidLossLight = Color(red: 0.827, green: 0.184, blue: 0.184) // #D32F2F
    static let fidLossDark  = Color(red: 0.898, green: 0.329, blue: 0.294) // #E5544B

    // MARK: - System / Accent
    static let fidWarning = Color(red: 0.784, green: 0.525, blue: 0.169) // #C8862B
    static let fidInfo    = Color(red: 0.118, green: 0.435, blue: 0.722) // #1E6FB8
    static let fidGold    = Color(red: 0.784, green: 0.635, blue: 0.294) // #C8A24B
}

// Scheme-aware semantic helpers
extension Color {
    static func fidGain(_ scheme: ColorScheme) -> Color { scheme == .dark ? .fidGainDark : .fidGainLight }
    static func fidLoss(_ scheme: ColorScheme) -> Color { scheme == .dark ? .fidLossDark : .fidLossLight }
}

/// Resolve a change value to the correct semantic color (flat -> secondary text).
func fidChangeColor(_ change: Double, scheme: ColorScheme) -> Color {
    if change > 0 { return .fidGain(scheme) }
    if change < 0 { return .fidLoss(scheme) }
    return scheme == .dark ? .fidDarkTextSecondary : .fidTextSecondary
}
```

## 2. Typography

Fidelity's brand face is "Average Sans"; on iOS, fall back to SF Pro or bundle **Inter** (SIL OFL) as the closest free match. The defining rule: every monetary figure uses `.monospacedDigit()`.

```swift
extension Font {
    static let fidBalanceHero = Font.custom("Inter-ExtraBold", size: 34).weight(.heavy)
    static let fidScreenTitle = Font.custom("Inter-Bold",      size: 26).weight(.bold)
    static let fidSection     = Font.custom("Inter-Bold",      size: 22).weight(.bold)
    static let fidCardTitle   = Font.custom("Inter-Bold",      size: 18).weight(.bold)
    static let fidBody        = Font.custom("Inter-Regular",   size: 16).weight(.regular)
    static let fidRowValue    = Font.custom("Inter-Bold",      size: 16).weight(.bold)
    static let fidRowSymbol   = Font.custom("Inter-SemiBold",  size: 15).weight(.semibold)
    static let fidMeta        = Font.custom("Inter-Regular",   size: 14).weight(.regular)
    static let fidNumericMono = Font.custom("Inter-SemiBold",  size: 13).weight(.semibold)
    static let fidCaption     = Font.custom("Inter-Medium",    size: 12).weight(.medium)
    static let fidButton      = Font.custom("Inter-Bold",      size: 16).weight(.bold)
    static let fidTab         = Font.custom("Inter-SemiBold",  size: 10).weight(.semibold)
    static let fidRangeTab    = Font.custom("Inter-SemiBold",  size: 11).weight(.semibold)
}

/// Money modifier — ALWAYS apply to currency / price / % / quantity.
struct Money: ViewModifier {
    func body(content: Content) -> some View {
        content.monospacedDigit().kerning(0)
    }
}
extension View { func money() -> some View { modifier(Money()) } }
```

## 3. Signature Components

### Balance Header (the screen hero)

```swift
struct BalanceHeader: View {
    @Environment(\.colorScheme) var scheme
    let greeting: String
    let title: String
    let value: String          // pre-formatted "$428,164.92"
    let changeAmount: String   // "+$3,841.20"
    let changePercent: String  // "+0.90%"
    let change: Double

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Text(greeting).font(.fidCaption).foregroundStyle(secondary)
            Text(title).font(.fidScreenTitle).foregroundStyle(primary).padding(.top, 2)
            Text("Total account value").font(.fidCaption).foregroundStyle(secondary).padding(.top, 18)
            Text(value).font(.fidBalanceHero).foregroundStyle(primary).money().padding(.top, 4)
            HStack(spacing: 6) {
                Image(systemName: change >= 0 ? "triangle.fill" : "triangle.fill")
                    .rotationEffect(.degrees(change >= 0 ? 0 : 180))
                    .font(.system(size: 9))
                Text("\(changeAmount) ").font(.fidNumericMono).money()
                + Text("(\(changePercent)) today").font(.fidNumericMono).foregroundColor(secondary)
            }
            .foregroundStyle(fidChangeColor(change, scheme: scheme))
            .padding(.top, 8)
        }
        .padding(.horizontal, 20)
        .padding(.bottom, 16)
    }

    private var primary: Color { scheme == .dark ? .fidDarkTextPrimary : .fidTextPrimary }
    private var secondary: Color { scheme == .dark ? .fidDarkTextSecondary : .fidTextSecondary }
}
```

### Holding / Watchlist Row

```swift
struct HoldingRow: View {
    @Environment(\.colorScheme) var scheme
    let symbol: String
    let name: String
    let value: String      // "$229.87"
    let changePct: String  // "+1.42%"
    let change: Double
    var isBrandFund: Bool = false

    var body: some View {
        HStack(spacing: 12) {
            Text(symbol)
                .font(.system(size: 12, weight: .heavy))
                .foregroundStyle(isBrandFund ? Color.fidLeaf : primary)
                .frame(width: 38, height: 38)
                .background(RoundedRectangle(cornerRadius: 10)
                    .fill(isBrandFund ? Color.fidGreen.opacity(0.16) : surface2))

            VStack(alignment: .leading, spacing: 2) {
                Text(symbol).font(.fidRowSymbol).foregroundStyle(primary)
                Text(name).font(.fidCaption).foregroundStyle(secondary).lineLimit(1)
            }
            Spacer(minLength: 8)
            VStack(alignment: .trailing, spacing: 2) {
                Text(value).font(.fidRowValue).foregroundStyle(primary).money()
                Text(changePct).font(.fidNumericMono)
                    .foregroundStyle(fidChangeColor(change, scheme: scheme)).money()
            }
        }
        .padding(.horizontal, 20)
        .frame(height: 64)
        .overlay(Rectangle().fill(divider).frame(height: 0.5), alignment: .bottom)
    }

    private var primary: Color { scheme == .dark ? .fidDarkTextPrimary : .fidTextPrimary }
    private var secondary: Color { scheme == .dark ? .fidDarkTextSecondary : .fidTextSecondary }
    private var surface2: Color { scheme == .dark ? .fidDarkSurface2 : .fidSurface }
    private var divider: Color { scheme == .dark ? .fidDarkDivider : .fidDivider }
}
```

### Sparkline + Range Tabs

```swift
struct Sparkline: View {
    let points: [CGFloat]   // normalized 0...1
    let isUp: Bool

    var body: some View {
        GeometryReader { geo in
            let w = geo.size.width, h = geo.size.height
            let path = Path { p in
                for (i, v) in points.enumerated() {
                    let x = w * CGFloat(i) / CGFloat(max(points.count - 1, 1))
                    let y = h - v * h
                    i == 0 ? p.move(to: .init(x: x, y: y)) : p.addLine(to: .init(x: x, y: y))
                }
            }
            let strokeColor = isUp ? Color.fidLeaf : Color.fidLossDark
            ZStack {
                path.strokedPath(.init(lineWidth: 2, lineJoin: .round)).foregroundStyle(strokeColor)
                path.copy(closingTo: h)
                    .fill(LinearGradient(colors: [strokeColor.opacity(0.32), strokeColor.opacity(0)],
                                         startPoint: .top, endPoint: .bottom))
            }
        }
        .animation(.easeOut(duration: 0.28), value: points)
    }
}

private extension Path {
    func copy(closingTo bottom: CGFloat) -> Path {
        var p = self
        if let last = currentPoint { p.addLine(to: .init(x: last.x, y: bottom)) }
        p.addLine(to: .init(x: 0, y: bottom)); p.closeSubpath(); return p
    }
}

struct RangeTabs: View {
    @State private var sel = 0
    let ranges = ["1D", "1W", "1M", "1Y", "5Y", "All"]
    var body: some View {
        HStack(spacing: 6) {
            ForEach(ranges.indices, id: \.self) { i in
                Text(ranges[i]).font(.fidRangeTab)
                    .padding(.horizontal, 9).padding(.vertical, 3)
                    .foregroundStyle(i == sel ? Color.white : Color.fidDarkTextSecondary)
                    .background(Capsule().fill(i == sel ? Color.fidGreen : .clear))
                    .onTapGesture {
                        sel = i
                        UIImpactFeedbackGenerator(style: .light).impactOccurred()
                    }
            }
        }
    }
}
```

### Sticky Trade Button

```swift
struct StickyTradeBar: View {
    let action: () -> Void
    var body: some View {
        VStack(spacing: 0) {
            Divider().background(Color.fidDarkDivider)
            Button(action: {
                UIImpactFeedbackGenerator(style: .medium).impactOccurred()
                action()
            }) {
                Text("Trade").font(.fidButton).foregroundStyle(.white)
                    .frame(maxWidth: .infinity).frame(height: 52)
                    .background(RoundedRectangle(cornerRadius: 8).fill(Color.fidGreen))
            }
            .buttonStyle(PressScaleStyle())
            .padding(.horizontal, 16).padding(.top, 12)
        }
        .background(.ultraThinMaterial)
    }
}

struct PressScaleStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .brightness(configuration.isPressed ? -0.04 : 0)
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}
```

### Performance Pill & Quote Card

```swift
struct PerformancePill: View {
    enum Kind { case gain, loss, flat }
    let kind: Kind
    let text: String
    var body: some View {
        Text(prefix + text).font(.fidNumericMono).money()
            .padding(.horizontal, 14).padding(.vertical, 8)
            .foregroundStyle(fg)
            .background(RoundedRectangle(cornerRadius: 8).fill(bg))
    }
    private var prefix: String { kind == .gain ? "▲ " : kind == .loss ? "▼ " : "— " }
    private var fg: Color { kind == .gain ? .fidGainDark : kind == .loss ? .fidLossDark : .fidDarkTextSecondary }
    private var bg: Color {
        switch kind {
        case .gain: return Color.fidGainDark.opacity(0.16)
        case .loss: return Color.fidLossDark.opacity(0.16)
        case .flat: return Color.fidDarkSurface2
        }
    }
}

struct QuoteCard: View {
    @Environment(\.colorScheme) var scheme
    let symbol: String, name: String, price: String, pct: String, change: Double
    var body: some View {
        HStack {
            VStack(alignment: .leading, spacing: 2) {
                Text(symbol).font(.system(size: 16, weight: .heavy))
                Text(name).font(.fidCaption).foregroundStyle(.secondary)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 2) {
                Text(price).font(.fidRowValue).money()
                Text(pct).font(.fidCaption.weight(.bold))
                    .foregroundStyle(fidChangeColor(change, scheme: scheme)).money()
            }
        }
        .padding(.vertical, 14).padding(.horizontal, 16)
        .background(RoundedRectangle(cornerRadius: 12)
            .fill(scheme == .dark ? Color.fidDarkSurface2 : Color.fidSurface))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct FidelityTabView: View {
    var body: some View {
        TabView {
            SummaryView().tabItem  { Label("Summary",  systemImage: "house.fill") }
            PlanningView().tabItem { Label("Planning", systemImage: "chart.bar.fill") }
            InvestView().tabItem   { Label("Invest",   systemImage: "chart.line.uptrend.xyaxis") }
            NewsView().tabItem     { Label("News",     systemImage: "newspaper.fill") }
            ProfileView().tabItem  { Label("Profile",  systemImage: "person.crop.circle") }
        }
        .tint(.fidLeaf) // active green; inactive is system tertiary
    }
}
```

## 5. Motion

```swift
// Price tick — brief color cross-fade ONLY (digits do not roll)
withAnimation(.easeOut(duration: 0.6)) { tickColor = fidChangeColor(delta, scheme: scheme) }
// settle back to primary text color after 600ms

// Chart range morph
.animation(.easeOut(duration: 0.28), value: points)

// Push navigation: standard NavigationStack push (300ms iOS)

// Sticky Trade button: always visible; top Divider appears as content scrolls beneath (.ultraThinMaterial)

// Haptics
UIImpactFeedbackGenerator(style: .light).impactOccurred()   // tab / range change
UIImpactFeedbackGenerator(style: .medium).impactOccurred()  // tap Trade / Place order
UINotificationFeedbackGenerator().notificationOccurred(.success) // order filled
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Summary (tab) | `house` / `house.fill` | 22pt |
| Planning (tab) | `chart.bar` / `chart.bar.fill` | 22pt |
| Invest (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| News (tab) | `newspaper` / `newspaper.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Day change up | `triangle.fill` | 9pt |
| Day change down | `triangle.fill` (rotated 180°) / `arrowtriangle.down.fill` | 9pt |
| Watchlist toggle | `star` / `star.fill` | 20pt |
| Search | `magnifyingglass` | 16pt |
| Back | `chevron.left` | 22pt |
| Overflow | `ellipsis` | 20pt |
| Trade / Buy | `arrow.left.arrow.right` | 18pt |
| Transfer | `arrow.down.circle` | 18pt |
| Alerts | `bell` / `bell.fill` | 20pt |
| Account detail | `chevron.right` | 14pt |
| Allocation ring | `chart.pie.fill` | 18pt |
| Documents | `doc.text` | 18pt |

## 7. Dark Mode

```swift
struct FidelityTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.fidDarkCanvas : Color.fidCanvas)
            .foregroundStyle(scheme == .dark ? Color.fidDarkTextPrimary : Color.fidTextPrimary)
            .tint(scheme == .dark ? Color.fidLeaf : Color.fidGreen)
    }
}
extension View { func fidelityTheme() -> some View { modifier(FidelityTheme()) } }
```

Dark canvas is the evergreen `#0E1411`, never pure black. Brand action stays `#368727`; links/selected tints brighten to `#4FB23B` for contrast on dark. Gain/loss shift to the brighter dark-mode pair (`#15B374` / `#E5544B`) but their meaning never changes. Cards get a 1pt `#26342B` border on dark since shadows are nearly invisible against the canvas.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`NavigationStack`, `.ultraThinMaterial`). `UIImpactFeedbackGenerator` works back to iOS 13.
- Bundle Inter (SIL OFL) as the free brand substitute; register all weights via `Info.plist` `UIAppFonts`. If using SF Pro, apply `.monospacedDigit()` for all money.
- Dynamic Type: scale balance hero, titles, body, meta; pin tab labels, range tabs, chart-axis labels, and table headers at fixed sizes for layout integrity. Tabular figures must remain tabular at every size.
- Do NOT rely on color alone for gain/loss: pair the green/red with a sign (`+`/`−`) and a ▲/▼ glyph so colorblind users get the direction. The provided components always include the sign and arrow.
- Contrast: `#15B374`/`#E5544B` on `#0E1411` pass WCAG AA for the bold numeric weights used; `#368727` on white passes AA for button text at 16pt bold. Validate any custom pairing.
- VoiceOver: announce a holding row as "Apple, A A P L, 229 dollars 87 cents, up 1.42 percent" — combine symbol, value, and signed change in one label; never expose color as the only state.
- Reduce Motion: disable the chart path morph (cross-fade instead) and the price-tick color animation (set final color instantly); keep the static sign/arrow.
- Dark mode: invert via the `fidDark*` palette — `#0E1411`, NOT true black; add the 1pt `#26342B` border to cards and sheets as the elevation cue.
