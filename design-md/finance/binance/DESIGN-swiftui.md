# Binance (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Binance's dark trading-terminal language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — default)
    static let bnCanvas    = Color(red: 0.043, green: 0.055, blue: 0.067) // #0B0E11
    static let bnSurface1  = Color(red: 0.094, green: 0.102, blue: 0.125) // #181A20
    static let bnSurface2  = Color(red: 0.118, green: 0.125, blue: 0.149) // #1E2026
    static let bnSurface3  = Color(red: 0.169, green: 0.192, blue: 0.224) // #2B3139
    static let bnDivider   = Color(red: 0.169, green: 0.192, blue: 0.224) // #2B3139

    // MARK: - Canvas & Surfaces (Light — secondary)
    static let bnCanvasLight   = Color.white                                  // #FFFFFF
    static let bnSurface1Light = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let bnDividerLight  = Color(red: 0.918, green: 0.925, blue: 0.937) // #EAECEF

    // MARK: - Brand & Market Semantics
    static let bnYellow        = Color(red: 0.941, green: 0.725, blue: 0.043) // #F0B90B
    static let bnYellowPressed  = Color(red: 0.788, green: 0.580, blue: 0.0)  // #C99400
    static let bnUp            = Color(red: 0.055, green: 0.796, blue: 0.506) // #0ECB81
    static let bnDown          = Color(red: 0.965, green: 0.275, blue: 0.365) // #F6465D
    static let bnUpPressed     = Color(red: 0.043, green: 0.647, blue: 0.447) // #0BA572
    static let bnDownPressed   = Color(red: 0.851, green: 0.220, blue: 0.286) // #D93849

    // MARK: - Text
    static let bnTextPrimary    = Color(red: 0.918, green: 0.925, blue: 0.937) // #EAECEF
    static let bnTextSecondary  = Color(red: 0.518, green: 0.557, blue: 0.612) // #848E9C
    static let bnTextTertiary   = Color(red: 0.369, green: 0.400, blue: 0.451) // #5E6673
    static let bnTextPrimaryLt  = Color(red: 0.118, green: 0.137, blue: 0.161) // #1E2329

    // MARK: - Semantic / Info
    static let bnInfo  = Color(red: 0.200, green: 0.459, blue: 0.733) // #3375BB
}

// Tints
extension Color {
    static let bnYellowTint = Color.bnYellow.opacity(0.12)
    static let bnAskFill    = Color.bnDown.opacity(0.14)
    static let bnBidFill     = Color.bnUp.opacity(0.14)
}
```

## 2. Typography

Binance ships a custom `BinancePlex` family (derived from IBM Plex Sans) plus `BinancePlex Mono`. Bundle **IBM Plex Sans** + **IBM Plex Mono** as the open fallback. The cardinal rule: **words use the sans, all numbers use the mono with tabular figures**.

```swift
extension Font {
    // UI Sans (IBM Plex Sans)
    static let bnScreenTitle = Font.custom("IBMPlexSans-Bold",      size: 32)
    static let bnSection     = Font.custom("IBMPlexSans-Bold",      size: 22)
    static let bnRowTitle    = Font.custom("IBMPlexSans-SemiBold",  size: 18)
    static let bnBody        = Font.custom("IBMPlexSans-Regular",   size: 16)
    static let bnListLabel   = Font.custom("IBMPlexSans-SemiBold",  size: 14)
    static let bnMeta        = Font.custom("IBMPlexSans-Regular",   size: 14)
    static let bnPill        = Font.custom("IBMPlexSans-SemiBold",  size: 13)
    static let bnCaption     = Font.custom("IBMPlexSans-Medium",    size: 12)
    static let bnTab         = Font.custom("IBMPlexSans-Medium",    size: 10)
    static let bnButton      = Font.custom("IBMPlexSans-SemiBold",  size: 15)

    // Numeric Mono (IBM Plex Mono) — always tabular
    static let bnBalance     = Font.custom("IBMPlexMono-SemiBold",  size: 28)
    static let bnPrice       = Font.custom("IBMPlexMono-SemiBold",  size: 15)
    static let bnNumber      = Font.custom("IBMPlexMono-SemiBold",  size: 14)
    static let bnMonoCaption = Font.custom("IBMPlexMono-Medium",    size: 12)
    static let bnMonoSmall   = Font.custom("IBMPlexMono-Medium",    size: 10)
}

// Apply tabular figures to every numeric Text
extension View {
    func bnTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Markets List Row

```swift
struct MarketRow: View {
    let symbol: String          // "BTC"
    let quote: String           // "USDT"
    let volume: String          // "1.42B"
    let price: String           // "67,284.10"
    let usd: String             // "$67,284.10"
    let changePct: Double       // 2.34

    private var up: Bool { changePct >= 0 }

    var body: some View {
        HStack(spacing: 10) {
            Circle()
                .fill(Color(red: 0.969, green: 0.576, blue: 0.102)) // BTC #F7931A
                .frame(width: 30, height: 30)
                .overlay(Text("B").font(.system(size: 13, weight: .bold)).foregroundStyle(.black))

            VStack(alignment: .leading, spacing: 2) {
                HStack(spacing: 2) {
                    Text(symbol).font(.bnListLabel).foregroundStyle(Color.bnTextPrimary)
                    Text("/\(quote)").font(.custom("IBMPlexSans-Medium", size: 11)).foregroundStyle(Color.bnTextTertiary)
                }
                Text("Vol \(volume)").font(.custom("IBMPlexSans-Regular", size: 11)).foregroundStyle(Color.bnTextSecondary)
            }

            Spacer()

            VStack(alignment: .trailing, spacing: 2) {
                Text(price).font(.bnNumber).bnTabular().foregroundStyle(Color.bnTextPrimary)
                Text(usd).font(.bnMonoSmall).bnTabular().foregroundStyle(Color.bnTextSecondary)
            }

            Text("\(up ? "+" : "")\(changePct, specifier: "%.2f")%")
                .font(.bnPill).bnTabular()
                .foregroundStyle(.white)
                .frame(minWidth: 64)
                .padding(.vertical, 6)
                .background(RoundedRectangle(cornerRadius: 4).fill(up ? Color.bnUp : Color.bnDown))
                .padding(.leading, 8)
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 12)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.bnDivider).frame(height: 1) }
    }
}
```

### Order Book (depth-shaded)

```swift
struct OrderBookRow: View {
    let price: String
    let qty: String
    let depthRatio: CGFloat   // 0...1 cumulative size / max
    let isAsk: Bool

    var body: some View {
        ZStack(alignment: .trailing) {
            GeometryReader { geo in
                Rectangle()
                    .fill(isAsk ? Color.bnAskFill : Color.bnBidFill)
                    .frame(width: geo.size.width * depthRatio)
                    .frame(maxWidth: .infinity, alignment: .trailing)
            }
            HStack {
                Text(price).font(.bnMonoCaption).bnTabular()
                    .foregroundStyle(isAsk ? Color.bnDown : Color.bnUp)
                Spacer()
                Text(qty).font(.bnMonoCaption).bnTabular()
                    .foregroundStyle(Color.bnTextPrimary)
            }
            .padding(.horizontal, 8)
        }
        .frame(height: 22)
    }
}

struct SpreadRow: View {
    let last: String
    let up: Bool
    var body: some View {
        HStack(spacing: 6) {
            Text(last).font(.custom("IBMPlexMono-SemiBold", size: 16)).bnTabular()
                .foregroundStyle(up ? Color.bnUp : Color.bnDown)
            Image(systemName: up ? "arrow.up" : "arrow.down")
                .font(.system(size: 12, weight: .bold))
                .foregroundStyle(up ? Color.bnUp : Color.bnDown)
            Spacer()
        }
        .padding(.horizontal, 8).padding(.vertical, 6)
    }
}
```

### Balance Hero

```swift
struct BalanceHero: View {
    let value: String      // "12,840.57"
    let currency: String   // "USDT"
    let pnl: String        // "+$214.86 (+1.70%)"
    let gain: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            HStack(spacing: 6) {
                Text("Est. Total Value").font(.bnCaption).foregroundStyle(Color.bnTextSecondary)
                Image(systemName: "eye").font(.system(size: 12)).foregroundStyle(Color.bnTextSecondary)
            }
            HStack(alignment: .firstTextBaseline, spacing: 4) {
                Text(value).font(.bnBalance).bnTabular().foregroundStyle(Color.bnTextPrimary)
                Text(currency).font(.custom("IBMPlexSans-Medium", size: 14)).foregroundStyle(Color.bnTextSecondary)
            }
            Text("\(pnl) Today").font(.bnMonoCaption).bnTabular()
                .foregroundStyle(gain ? Color.bnUp : Color.bnDown)
        }
        .padding(.horizontal, 16)
    }
}
```

### Buy / Sell Trade Ticket

```swift
struct TradeTicket: View {
    @State private var isBuy = true
    @State private var pct: Int = 0

    var body: some View {
        VStack(spacing: 12) {
            // Buy/Sell toggle
            HStack(spacing: 0) {
                ForEach([true, false], id: \.self) { buy in
                    Text(buy ? "Buy" : "Sell")
                        .font(.bnButton)
                        .foregroundStyle(isBuy == buy ? .white : Color.bnTextSecondary)
                        .frame(maxWidth: .infinity).frame(height: 36)
                        .background(isBuy == buy ? (buy ? Color.bnUp : Color.bnDown) : Color.bnSurface3)
                        .onTapGesture { isBuy = buy }
                }
            }
            .clipShape(RoundedRectangle(cornerRadius: 8))

            // Percentage slider chips
            HStack(spacing: 8) {
                ForEach([25, 50, 75, 100], id: \.self) { p in
                    Text("\(p)%")
                        .font(.custom("IBMPlexSans-SemiBold", size: 13))
                        .foregroundStyle(pct == p ? Color.bnYellow : Color.bnTextSecondary)
                        .padding(.vertical, 7).frame(maxWidth: .infinity)
                        .background(RoundedRectangle(cornerRadius: 500)
                            .fill(pct == p ? Color.bnYellowTint : Color.bnSurface3))
                        .onTapGesture { pct = p }
                }
            }

            Button { } label: {
                Text(isBuy ? "Buy BTC" : "Sell BTC")
                    .font(.bnButton).foregroundStyle(.white)
                    .frame(maxWidth: .infinity).frame(height: 48)
                    .background(RoundedRectangle(cornerRadius: 8).fill(isBuy ? Color.bnUp : Color.bnDown))
            }
        }
        .padding(16)
        .background(Color.bnSurface1)
    }
}
```

### Primary (Yellow) Button

```swift
struct BinancePrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.bnButton)
                .foregroundStyle(Color.bnCanvas)        // black text on yellow
                .frame(maxWidth: .infinity).frame(height: 48)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.bnYellow))
        }
        .buttonStyle(.plain)
    }
}
```

### Convert Card

```swift
struct ConvertCard: View {
    var body: some View {
        ZStack {
            VStack(spacing: 8) {
                ConvertTile(role: "From", coin: "BTC", avail: "0.482")
                ConvertTile(role: "To",   coin: "USDT", avail: "12,840.57")
            }
            Circle().fill(Color.bnYellow).frame(width: 36, height: 36)
                .overlay(Image(systemName: "arrow.up.arrow.down")
                    .font(.system(size: 14, weight: .bold)).foregroundStyle(Color.bnCanvas))
        }
    }
}

struct ConvertTile: View {
    let role: String; let coin: String; let avail: String
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack {
                HStack(spacing: 6) {
                    Circle().fill(Color.bnYellow).frame(width: 24, height: 24)
                    Text(coin).font(.bnListLabel).foregroundStyle(Color.bnTextPrimary)
                    Image(systemName: "chevron.down").font(.system(size: 11)).foregroundStyle(Color.bnTextSecondary)
                }
                Spacer()
                Text("0.00").font(.custom("IBMPlexMono-SemiBold", size: 18)).bnTabular()
                    .foregroundStyle(Color.bnTextPrimary)
            }
            Text("\(role) · Available \(avail) \(coin)")
                .font(.bnMonoCaption).foregroundStyle(Color.bnTextSecondary)
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.bnSurface2))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct BinanceTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            MarketsView().tabItem { Label("Markets", systemImage: "chart.bar.fill") }
            TradeView().tabItem { Label("Trade", systemImage: "arrow.left.arrow.right") }
            FuturesView().tabItem { Label("Futures", systemImage: "chart.xyaxis.line") }
            WalletsView().tabItem { Label("Wallets", systemImage: "creditcard.fill") }
        }
        .tint(.bnYellow)
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.bnCanvas)
            a.shadowColor = UIColor(Color.bnDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Price tick flash (call when a price updates)
@State private var flash: Color? = nil
func tick(up: Bool) {
    flash = (up ? Color.bnUp : Color.bnDown).opacity(0.1)
    withAnimation(.easeOut(duration: 0.15)) { flash = nil }
}
// .background(flash ?? .clear)

// Markets tab underline slide
withAnimation(.easeOut(duration: 0.2)) { selectedTab = tab }

// Buy/Sell toggle color-morph
withAnimation(.easeOut(duration: 0.18)) { isBuy.toggle() }

// Order book reflow — no spring, data must feel stable
withAnimation(.easeOut(duration: 0.12)) { rows = newRows }

// Convert swap (vertical flip + haptic)
withAnimation(.easeInOut(duration: 0.25)) { swapped.toggle() }

// Haptics
.sensoryFeedback(.success, trigger: orderFilled)
.sensoryFeedback(.selection, trigger: pct)         // percentage chip snap
.sensoryFeedback(.impact(weight: .light), trigger: orderSubmitted)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Markets (tab) | `chart.bar` / `chart.bar.fill` | 22pt |
| Trade (tab) | `arrow.left.arrow.right` | 22pt |
| Futures (tab) | `chart.xyaxis.line` | 22pt |
| Wallets (tab) | `creditcard` / `creditcard.fill` | 22pt |
| Search | `magnifyingglass` | 14pt |
| Notifications | `bell` | 18pt |
| Hide balance | `eye` / `eye.slash` | 12pt |
| Favorite | `star` / `star.fill` | 18pt |
| Back | `chevron.left` | 22pt |
| Convert swap | `arrow.up.arrow.down` | 14pt |
| Up tick | `arrow.up` | 12pt |
| Down tick | `arrow.down` | 12pt |
| Deposit | `arrow.down.to.line` | 16pt |
| Withdraw | `arrow.up.to.line` | 16pt |
| Share | `square.and.arrow.up` | 18pt |
| More | `ellipsis` | 18pt |
| Chart type | `chart.bar.xaxis` | 16pt |
| Settings | `gearshape` | 18pt |

## 7. Dark Mode

```swift
struct BinanceTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.bnCanvas : Color.bnCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.bnTextPrimary : Color.bnTextPrimaryLt)
    }
}
extension View { func binanceTheme() -> some View { modifier(BinanceTheme()) } }
```

Binance is dark-first; the light theme only swaps canvas (`#0B0E11` → `#FFFFFF`) and primary text (`#EAECEF` → `#1E2329`). **Brand yellow and market green/red are theme-invariant** — `#F0B90B`, `#0ECB81`, `#F6465D` are identical in both modes. Force dark by default if you only ship one theme.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` needs iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle IBM Plex Sans + IBM Plex Mono TTFs via `Info.plist` `UIAppFonts` — both SIL OFL, free to distribute
- **Tabular figures are mandatory** on every numeric `Text` — use `.monospacedDigit()`; without it, list prices and the order book jitter
- Dynamic Type: scale screen titles, body, labels, captions; keep numeric mono columns, % pills, order-book rows, and tab labels FIXED (column alignment is layout-critical)
- Color is not the only signal: pair green/red with a `+`/`−` sign and an `arrow.up`/`arrow.down` glyph so colorblind users can read direction (deuteranopia confuses `#0ECB81`/`#F6465D`)
- VoiceOver: announce a market row as "BTC USDT, 67,284 dollars, up 2.34 percent"; announce order-book rows with side, price, quantity; the Buy/Sell submit button should read "Buy Bitcoin" / "Sell Bitcoin"
- Contrast: `#EAECEF` on `#0B0E11` and `#0B0E11` on `#F0B90B` both pass WCAG AA; never put white text on the yellow button
- Reduce Motion: disable the price-tick flash background and Convert flip; keep instant data updates (no fade)
- Live data accessibility: post a VoiceOver announcement only on significant balance changes, not on every tick, to avoid speech spam
- Dark mode: this is the primary mode — verify the secondary surfaces (`#181A20`/`#1E2026`/`#2B3139`) maintain ≥3:1 separation; floating sheets get a 1pt `#2B3139` border since shadows barely register on `#0B0E11`
