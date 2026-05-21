# Webull (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Webull's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Recurring rules: **dark-native**, **everything numeric is tabular**, **up `#00C076` / down `#FA5252` everywhere**, **blue→cyan gradient = brand/action only**.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let wbBlue        = Color(red: 0.106, green: 0.620, blue: 0.984) // #1B9EFB
    static let wbCyan        = Color(red: 0.125, green: 0.835, blue: 0.769) // #20D5C4
    static let wbBluePressed = Color(red: 0.086, green: 0.518, blue: 0.839) // #1684D6
    static let wbOnBrand     = Color(red: 0.016, green: 0.071, blue: 0.110) // #04121C
    static let wbBrandGradient = LinearGradient(colors: [.wbBlue, .wbCyan],
                                                startPoint: .topLeading, endPoint: .bottomTrailing)

    // MARK: - Canvas & Surfaces (dark — the only mode)
    static let wbCanvas   = Color(red: 0.043, green: 0.055, blue: 0.067) // #0B0E11
    static let wbSurface1 = Color(red: 0.078, green: 0.094, blue: 0.114) // #14181D
    static let wbSurface2 = Color(red: 0.110, green: 0.129, blue: 0.153) // #1C2127
    static let wbDivider  = Color(red: 0.137, green: 0.161, blue: 0.192) // #232931
    static let wbGridLine = Color(red: 0.102, green: 0.122, blue: 0.149) // #1A1F26

    // MARK: - Text
    static let wbTextPrimary   = Color(red: 0.918, green: 0.933, blue: 0.949) // #EAEEF2
    static let wbTextSecondary = Color(red: 0.545, green: 0.584, blue: 0.631) // #8B95A1
    static let wbTextTertiary  = Color(red: 0.353, green: 0.388, blue: 0.431) // #5A636E

    // MARK: - Market semantics (loud & pervasive)
    static let wbUp     = Color(red: 0.000, green: 0.753, blue: 0.463) // #00C076
    static let wbDown   = Color(red: 0.980, green: 0.322, blue: 0.322) // #FA5252
    static let wbOnUp   = Color(red: 0.012, green: 0.082, blue: 0.051) // #03150D
    static let wbOnDown = Color(red: 0.106, green: 0.024, blue: 0.024) // #1B0606
    static let wbUpFill   = Color(red: 0.000, green: 0.753, blue: 0.463).opacity(0.14)
    static let wbDownFill  = Color(red: 0.980, green: 0.322, blue: 0.322).opacity(0.14)

    // MARK: - System / accent
    static let wbAmber = Color(red: 0.969, green: 0.651, blue: 0.000) // #F7A600
}

/// US default: up=green, down=red. Region setting may invert (Asia markets).
struct WBDirection { var upIsGreen = true }

func wbColor(_ change: Double, dir: WBDirection = .init()) -> Color {
    if change == 0 { return .wbTextSecondary }
    let positive = change > 0
    let green = dir.upIsGreen ? positive : !positive
    return green ? .wbUp : .wbDown
}
```

## 2. Typography

Fall back to SF Pro or bundle **Inter** (SIL OFL) with tabular figures. The defining rule: every numeric uses `.monospacedDigit()` so the ladder and option chain align.

```swift
extension Font {
    static let wbLastPrice  = Font.custom("Inter-ExtraBold", size: 32).weight(.heavy)
    static let wbScreenTitle = Font.custom("Inter-ExtraBold", size: 24).weight(.heavy)
    static let wbSection    = Font.custom("Inter-Bold",      size: 20).weight(.bold)
    static let wbSymbol     = Font.custom("Inter-Bold",      size: 17).weight(.bold)
    static let wbBody       = Font.custom("Inter-Regular",   size: 15).weight(.regular)
    static let wbRowSymbol  = Font.custom("Inter-SemiBold",  size: 14).weight(.semibold)
    static let wbRowValue   = Font.custom("Inter-Bold",      size: 15).weight(.bold)
    static let wbMeta       = Font.custom("Inter-Regular",   size: 13).weight(.regular)
    static let wbNumericMono = Font.custom("Inter-Bold",     size: 12).weight(.bold)
    static let wbLadder     = Font.custom("Inter-Bold",      size: 13).weight(.bold)
    static let wbLabel      = Font.custom("Inter-SemiBold",  size: 11).weight(.semibold)
    static let wbButton     = Font.custom("Inter-ExtraBold", size: 15).weight(.heavy)
    static let wbTab        = Font.custom("Inter-SemiBold",  size: 10).weight(.semibold)
    static let wbTimeframe  = Font.custom("Inter-Bold",      size: 11).weight(.bold)
}

/// Tabular modifier — apply to ALL numeric values.
struct WBNum: ViewModifier {
    func body(content: Content) -> some View { content.monospacedDigit() }
}
extension View { func wbNum() -> some View { modifier(WBNum()) } }
```

## 3. Signature Components

### Quote Header

```swift
struct QuoteHeader: View {
    let symbol: String, name: String
    let price: String, delta: String, change: Double
    let isPaper: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(alignment: .top) {
                VStack(alignment: .leading, spacing: 1) {
                    Text(symbol).font(.system(size: 20, weight: .heavy)).foregroundStyle(Color.wbTextPrimary)
                    Text(name).font(.wbMeta).foregroundStyle(Color.wbTextSecondary)
                }
                Spacer()
                if isPaper {
                    Text("PAPER")
                        .font(.system(size: 10, weight: .heavy))
                        .foregroundStyle(Color.wbOnBrand)
                        .padding(.horizontal, 8).padding(.vertical, 3)
                        .background(RoundedRectangle(cornerRadius: 4).fill(Color.wbCyan))
                }
            }
            Text(price).font(.wbLastPrice).foregroundStyle(wbColor(change))
                .wbNum().padding(.top, 12)
            Text(delta + (change >= 0 ? " ▲" : " ▼"))
                .font(.wbRowValue).foregroundStyle(wbColor(change)).wbNum().padding(.top, 4)
        }
        .padding(.horizontal, 18).padding(.top, 4).padding(.bottom, 10)
        .background(Color.wbCanvas)
    }
}
```

### Candlestick Chart + Timeframe Strip

```swift
struct Candle: Identifiable { let id = UUID(); let o, h, l, c: CGFloat }

struct CandleChart: View {
    let candles: [Candle]
    let prevClose: CGFloat   // normalized 0...1
    var range: ClosedRange<CGFloat> = 0...1

    var body: some View {
        GeometryReader { geo in
            let w = geo.size.width, h = geo.size.height
            let step = w / CGFloat(candles.count)
            func y(_ v: CGFloat) -> CGFloat {
                h - (v - range.lowerBound) / (range.upperBound - range.lowerBound) * h
            }
            ZStack {
                // grid lines — nearly invisible
                ForEach(1..<4) { i in
                    Path { p in
                        let yy = h * CGFloat(i) / 4
                        p.move(to: .init(x: 0, y: yy)); p.addLine(to: .init(x: w, y: yy))
                    }.stroke(Color.wbGridLine, lineWidth: 1)
                }
                // prev-close dashed line
                Path { p in
                    p.move(to: .init(x: 0, y: y(prevClose))); p.addLine(to: .init(x: w, y: y(prevClose)))
                }.stroke(Color.wbBlue, style: .init(lineWidth: 1, dash: [3, 3]))
                // candles
                ForEach(Array(candles.enumerated()), id: \.offset) { i, c in
                    let x = step * (CGFloat(i) + 0.5)
                    let up = c.c >= c.o
                    let color = up ? Color.wbUp : Color.wbDown
                    Path { p in p.move(to: .init(x: x, y: y(c.h))); p.addLine(to: .init(x: x, y: y(c.l))) }
                        .stroke(color, lineWidth: 1.5)
                    Rectangle().fill(color)
                        .frame(width: step * 0.6, height: max(abs(y(c.o) - y(c.c)), 1))
                        .position(x: x, y: (y(c.o) + y(c.c)) / 2)
                }
            }
        }
        .animation(.easeOut(duration: 0.25), value: candles.count)
    }
}

struct TimeframeStrip: View {
    @State private var sel = 2
    let tfs = ["1m", "5m", "1D", "5D", "1M", "1Y"]
    var body: some View {
        HStack(spacing: 4) {
            ForEach(tfs.indices, id: \.self) { i in
                Text(tfs[i]).font(.wbTimeframe)
                    .padding(.horizontal, 10).padding(.vertical, 5)
                    .foregroundStyle(i == sel ? Color.wbBlue : Color.wbTextSecondary)
                    .background(RoundedRectangle(cornerRadius: 4)
                        .fill(i == sel ? Color.wbBlue.opacity(0.16) : .clear))
                    .onTapGesture {
                        sel = i; UIImpactFeedbackGenerator(style: .light).impactOccurred()
                    }
            }
        }
        .padding(.horizontal, 14).padding(.top, 10)
    }
}
```

### Order-Book Ladder

```swift
struct LadderLevel: Identifiable { let id = UUID(); let price: String; let size: String; let frac: CGFloat }

struct OrderLadder: View {
    let asks: [LadderLevel]   // displayed top→down (highest first)
    let bids: [LadderLevel]
    let mid: String
    let spread: String

    var body: some View {
        VStack(spacing: 0) {
            ForEach(asks) { LadderRow(level: $0, side: .ask) }
            HStack {
                Spacer()
                Text("\(mid)  ·  Spread \(spread)")
                    .font(.wbLadder).foregroundStyle(Color.wbTextPrimary).wbNum()
                Spacer()
            }
            .padding(.vertical, 6)
            .overlay(Rectangle().fill(Color.wbDivider).frame(height: 0.5), alignment: .top)
            .overlay(Rectangle().fill(Color.wbDivider).frame(height: 0.5), alignment: .bottom)
            .padding(.vertical, 4)
            ForEach(bids) { LadderRow(level: $0, side: .bid) }
        }
        .padding(.horizontal, 18).padding(.top, 12)
    }
}

struct LadderRow: View {
    enum Side { case bid, ask }
    let level: LadderLevel
    let side: Side
    var body: some View {
        ZStack {
            GeometryReader { geo in
                Rectangle()
                    .fill(side == .bid ? Color.wbUpFill : Color.wbDownFill)
                    .frame(width: geo.size.width * level.frac)
                    .frame(maxWidth: .infinity, alignment: .trailing)
            }
            HStack {
                Text(level.price).font(.wbNumericMono).frame(width: 64, alignment: .leading)
                    .foregroundStyle(side == .bid ? Color.wbUp : Color.wbDown)
                Spacer()
                Text(level.size).font(.wbNumericMono).foregroundStyle(Color.wbTextSecondary)
            }
        }
        .frame(height: 22)
    }
}
```

### Docked Buy / Sell Pair + Watchlist Row

```swift
struct BuySellBar: View {
    let onBuy: () -> Void
    let onSell: () -> Void
    var body: some View {
        HStack(spacing: 10) {
            Button(action: { tap(); onBuy() }) {
                Text("Buy").font(.wbButton).foregroundStyle(Color.wbOnUp)
                    .frame(maxWidth: .infinity).frame(height: 48)
                    .background(RoundedRectangle(cornerRadius: 6).fill(Color.wbUp))
            }.buttonStyle(WBPress())
            Button(action: { tap(); onSell() }) {
                Text("Sell").font(.wbButton).foregroundStyle(Color.wbOnDown)
                    .frame(maxWidth: .infinity).frame(height: 48)
                    .background(RoundedRectangle(cornerRadius: 6).fill(Color.wbDown))
            }.buttonStyle(WBPress())
        }
        .padding(.horizontal, 18).padding(.vertical, 12)
        .background(Color.wbCanvas.opacity(0.94))
    }
    private func tap() { UIImpactFeedbackGenerator(style: .medium).impactOccurred() }
}

struct WBPress: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .brightness(configuration.isPressed ? -0.05 : 0)
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}

struct WatchlistRow: View {
    let symbol: String, name: String, price: String, pct: String, change: Double
    let spark: [CGFloat]
    var body: some View {
        HStack(spacing: 12) {
            VStack(alignment: .leading, spacing: 2) {
                Text(symbol).font(.wbRowSymbol).foregroundStyle(Color.wbTextPrimary)
                Text(name).font(.system(size: 11)).foregroundStyle(Color.wbTextSecondary).lineLimit(1)
            }
            Spacer(minLength: 6)
            MiniSpark(points: spark, up: change >= 0).frame(width: 56, height: 28)
            VStack(alignment: .trailing, spacing: 4) {
                Text(price).font(.wbRowValue).foregroundStyle(Color.wbTextPrimary).wbNum()
                Text(pct).font(.system(size: 11, weight: .heavy)).wbNum()
                    .foregroundStyle(change >= 0 ? Color.wbOnUp : Color.wbOnDown)
                    .padding(.horizontal, 7).padding(.vertical, 3)
                    .background(RoundedRectangle(cornerRadius: 4).fill(wbColor(change)))
            }
        }
        .padding(.vertical, 12)
        .overlay(Rectangle().fill(Color.wbDivider).frame(height: 0.5), alignment: .bottom)
    }
}

struct MiniSpark: View {
    let points: [CGFloat]; let up: Bool
    var body: some View {
        GeometryReader { geo in
            Path { p in
                for (i, v) in points.enumerated() {
                    let x = geo.size.width * CGFloat(i) / CGFloat(max(points.count - 1, 1))
                    let y = geo.size.height - v * geo.size.height
                    i == 0 ? p.move(to: .init(x: x, y: y)) : p.addLine(to: .init(x: x, y: y))
                }
            }.stroke(up ? Color.wbUp : Color.wbDown, lineWidth: 2)
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct WebullTabView: View {
    var body: some View {
        TabView {
            MarketsView().tabItem  { Label("Markets",   systemImage: "magnifyingglass") }
            QuotesView().tabItem   { Label("Quotes",    systemImage: "chart.xyaxis.line") }
            TradeView().tabItem    { Label("Trade",     systemImage: "dollarsign.circle") }
            PortfolioView().tabItem{ Label("Portfolio", systemImage: "chart.bar.fill") }
            MenuView().tabItem     { Label("Menu",      systemImage: "line.3.horizontal") }
        }
        .tint(.wbBlue)
        .preferredColorScheme(.dark)
    }
}
```

## 5. Motion

```swift
// Live price tick — flash bright then settle to steady up/down color (NO digit roll)
withAnimation(.easeOut(duration: 0.25)) { flash = true }
// reset flash after 250ms; price text stays wbColor(change)

// Chart timeframe switch
.animation(.easeOut(duration: 0.25), value: candles.count)

// Crosshair scrub — 1:1, no animation
// DragGesture on the chart → vertical+horizontal #8B95A1 lines + OHLC tooltip

// Buy/Sell → ticket sheet
.sheet(isPresented: $showTicket) { OrderTicket() } // slides up ~280ms; confirm inherits green/red

// Haptics
UIImpactFeedbackGenerator(style: .light).impactOccurred()    // timeframe / tab
UISelectionFeedbackGenerator().selectionChanged()            // crosshair crossing key levels
UIImpactFeedbackGenerator(style: .medium).impactOccurred()   // order submit
UINotificationFeedbackGenerator().notificationOccurred(.success) // fill
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Markets (tab) | `magnifyingglass` | 22pt |
| Quotes (tab) | `chart.xyaxis.line` | 22pt |
| Trade (tab) | `dollarsign.circle` | 22pt |
| Portfolio (tab) | `chart.bar` / `.fill` | 22pt |
| Menu (tab) | `line.3.horizontal` | 22pt |
| Watchlist star | `star` / `star.fill` | 18pt |
| Search | `magnifyingglass` | 16pt |
| Back | `chevron.left` | 22pt |
| Overflow | `ellipsis` | 20pt |
| Up tick | `arrowtriangle.up.fill` | 12pt |
| Down tick | `arrowtriangle.down.fill` | 12pt |
| Alerts | `bell` / `bell.fill` | 18pt |
| Indicators | `slider.horizontal.3` | 16pt |
| Chart fullscreen | `arrow.up.left.and.arrow.down.right` | 16pt |
| Order type | `list.bullet` | 16pt |
| Quantity + / − | `plus` / `minus` | 16pt |
| Depth / level 2 | `chart.bar.doc.horizontal` | 16pt |

## 7. Dark Mode

```swift
// Webull is dark-native — pin it.
struct WebullTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .preferredColorScheme(.dark)
            .background(Color.wbCanvas)
            .foregroundStyle(Color.wbTextPrimary)
            .tint(Color.wbBlue)
    }
}
extension View { func webullTheme() -> some View { modifier(WebullTheme()) } }
```

There is no canonical light mode — a trading terminal is dark-native. Pin `.preferredColorScheme(.dark)` app-wide. The near-black `#0B0E11` canvas is deliberate: it maximizes contrast for candlesticks, depth bars, and the blue→cyan gradient. Elevation is communicated with a 1pt `#232931` border + a slight surface lift (`#14181D`/`#1C2127`), never drop shadow — shadows do not register on near-black. Performance color (`#00C076`/`#FA5252`) is the loudest thing on screen and that is intentional.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16. `UIImpactFeedbackGenerator`/`UISelectionFeedbackGenerator` work back to iOS 13.
- Bundle Inter (SIL OFL) with tabular figures, or use SF Pro `.monospacedDigit()`. The ladder and option chain MUST be tabular.
- Pin `.preferredColorScheme(.dark)` — do not offer a primary light theme.
- Dynamic Type: scale last price, titles, body modestly; the ladder, option chain, timeframe chips, tab labels, and column headers are FIXED — they are tabular layout-critical grids.
- Do NOT rely on color alone for up/down: always pair the green/red with `+`/`−` and `▲`/`▼` (components above do). Hard requirement for a trading product, and essential because the up/down→color mapping can invert by region (Asia markets) — never let color be the only signal.
- Contrast: `#00C076`/`#FA5252` on `#0B0E11` pass WCAG AA at the bold weights used; on-button text `#03150D`/`#1B0606` on the solid green/red passes AA at 15pt w800. `#1B9EFB` with `#04121C` text passes AA.
- VoiceOver: announce a watchlist row as "Apple, 229.87, up 1.42 percent"; announce a ladder row as "Ask, 248.92, size 1204"; combine all parts in one label, never expose color alone.
- Reduce Motion: skip the timeframe re-layout animation and the price flash (set final color instantly); keep the static sign/arrow and the crosshair (it conveys data).
- Charts: use `Canvas`/`Path` for 60fps candle rendering; throttle live ticks to ~10Hz for battery; crosshair uses a high-priority `DragGesture`.
