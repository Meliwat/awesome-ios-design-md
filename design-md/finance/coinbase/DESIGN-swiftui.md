# Coinbase (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Coinbase's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the signature portfolio chart, asset row, and 4-up action quad.

Targets iOS 17+. The chart uses Swift Charts; the asset row sparkline uses inline `Path`; haptics use `.sensoryFeedback`.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let cbBlue        = Color(red: 0.00, green: 0.322, blue: 1.00)  // #0052FF
    static let cbBluePressed = Color(red: 0.00, green: 0.251, blue: 0.800) // #0040CC
    static let cbBlueTint    = Color(red: 0.898, green: 0.929, blue: 1.00) // #E5EDFF
    static let cbBlueDark    = Color(red: 0.231, green: 0.424, blue: 1.00) // #3B6CFF (dark-mode)
    static let cbBlack       = Color(red: 0.039, green: 0.043, blue: 0.051) // #0A0B0D
    static let cbCharcoal    = Color(red: 0.102, green: 0.110, blue: 0.122) // #1A1C1F

    // MARK: - Crypto Asset Colors
    static let cbBitcoin     = Color(red: 0.969, green: 0.576, blue: 0.102) // #F7931A
    static let cbEthereum    = Color(red: 0.384, green: 0.494, blue: 0.918) // #627EEA
    static let cbUSDC        = Color(red: 0.153, green: 0.459, blue: 0.792) // #2775CA
    static let cbSolana      = Color(red: 0.600, green: 0.271, blue: 1.00)  // #9945FF
    static let cbCardano     = Color(red: 0.00, green: 0.200, blue: 0.678)  // #0033AD
    static let cbTether      = Color(red: 0.149, green: 0.631, blue: 0.482) // #26A17B

    // MARK: - Canvas & Surfaces (light)
    static let cbCanvas      = Color(red: 1.00, green: 1.00, blue: 1.00)    // #FFFFFF
    static let cbSurfaceGray = Color(red: 0.969, green: 0.973, blue: 0.980) // #F7F8FA
    static let cbSurfaceGray2 = Color(red: 0.933, green: 0.941, blue: 0.953) // #EEF0F3
    static let cbDivider     = Color(red: 0.882, green: 0.894, blue: 0.910) // #E1E4E8

    // MARK: - Text (light)
    static let cbTextPrimary   = Color.cbBlack
    static let cbTextSecondary = Color(red: 0.357, green: 0.380, blue: 0.431) // #5B616E
    static let cbTextTertiary  = Color(red: 0.502, green: 0.525, blue: 0.561) // #80868F
    static let cbTextMuted     = Color(red: 0.627, green: 0.643, blue: 0.667) // #A0A4AA

    // MARK: - Semantic
    static let cbSuccess       = Color(red: 0.020, green: 0.694, blue: 0.412) // #05B169
    static let cbSuccessTint   = Color(red: 0.902, green: 0.969, blue: 0.937) // #E6F7EF
    static let cbLoss          = Color(red: 0.812, green: 0.125, blue: 0.184) // #CF202F
    static let cbLossTint      = Color(red: 0.988, green: 0.906, blue: 0.914) // #FCE7E9
    static let cbWarning       = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623

    // MARK: - Dark mode
    static let cbDarkCanvas    = Color.cbBlack
    static let cbDarkSurface1  = Color(red: 0.075, green: 0.082, blue: 0.102) // #13151A
    static let cbDarkSurface2  = Color(red: 0.118, green: 0.125, blue: 0.149) // #1E2026
    static let cbDarkDivider   = Color(red: 0.165, green: 0.180, blue: 0.212) // #2A2E36
    static let cbDarkTextPri   = Color.white
    static let cbDarkTextSec   = Color(red: 0.627, green: 0.643, blue: 0.667) // #A0A4AA
}
```

## 2. Typography

Coinbase Sans / Display / Mono are proprietary (Frere-Jones Type, ~2021). Bundle TTFs via `Info.plist` (`UIAppFonts`); fall back to SF Pro Text / SF Pro Display / SF Mono. Apply `.monospacedDigit()` on every price, percentage, and quantity.

```swift
extension Font {
    // Hero amounts (Display variant)
    static let cbPortfolioHero = Font.custom("CoinbaseDisplay-Bold", size: 40)
    static let cbBuyAmount     = Font.custom("CoinbaseDisplay-Bold", size: 56)
    static let cbScreenTitle   = Font.custom("CoinbaseDisplay-Bold", size: 28)

    // Sections & titles (Sans)
    static let cbSectionHeader = Font.custom("CoinbaseSans-Bold",   size: 20)
    static let cbAssetTitle    = Font.custom("CoinbaseSans-Semibold", size: 16)

    // Prices & deltas
    static let cbAssetPrice    = Font.custom("CoinbaseSans-Medium", size: 16)
    static let cbAssetChange   = Font.custom("CoinbaseSans-Medium", size: 13)

    // Body
    static let cbBody          = Font.custom("CoinbaseSans-Regular", size: 15)
    static let cbBodySmall     = Font.custom("CoinbaseSans-Regular", size: 13)

    // Micro
    static let cbTicker        = Font.custom("CoinbaseSans-Medium", size: 13) // letter-spaced +0.3pt
    static let cbTab           = Font.custom("CoinbaseSans-Semibold", size: 10)
    static let cbAllCaps       = Font.custom("CoinbaseSans-Bold",   size: 11) // letter-spaced +0.6pt
    static let cbRangeChip     = Font.custom("CoinbaseSans-Semibold", size: 13)

    // Buttons
    static let cbButton        = Font.custom("CoinbaseSans-Semibold", size: 16)
    static let cbButtonSmall   = Font.custom("CoinbaseSans-Medium", size: 13)

    // Mono (technical readouts)
    static let cbWalletAddr    = Font.custom("CoinbaseMono-Medium", size: 13)
    static let cbTxHash        = Font.custom("CoinbaseMono-Regular", size: 12)
}

// Fallback if Coinbase fonts aren't bundled — SF Pro / SF Mono:
extension Font {
    static func cb(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
    static func cbMono(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .monospaced)
    }
}
```

## 3. Signature Components

### Portfolio Hero

```swift
struct PortfolioHero: View {
    let value: Double
    let dayChange: Double
    let dayChangePct: Double

    private var isUp: Bool { dayChange >= 0 }
    private var sign: String { isUp ? "+" : "−" }
    private var color: Color { isUp ? .cbSuccess : .cbLoss }

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("PORTFOLIO BALANCE")
                .font(.cbAllCaps)
                .tracking(0.6)
                .foregroundStyle(Color.cbTextSecondary)

            Text(value, format: .currency(code: "USD"))
                .font(.cbPortfolioHero)
                .foregroundStyle(Color.cbTextPrimary)
                .monospacedDigit()
                .contentTransition(.numericText())
                .animation(.snappy, value: value)

            HStack(spacing: 4) {
                Text("\(sign)\(abs(dayChange), format: .currency(code: "USD"))")
                Text("(\(sign)\(abs(dayChangePct * 100), specifier: "%.2f")%)")
            }
            .font(.cbAssetPrice)
            .foregroundStyle(color)
            .monospacedDigit()
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(.horizontal, 16)
        .padding(.top, 16)
    }
}
```

### Asset Row with Mini Sparkline

```swift
struct AssetRow: View {
    let assetName: String       // "Bitcoin"
    let ticker: String          // "BTC"
    let holdings: String        // "0.1842 BTC"
    let price: Double           // 67234.18
    let dayChange: Double
    let dayChangePct: Double
    let iconColor: Color        // .cbBitcoin, .cbEthereum, etc.
    let glyph: String           // "₿", "Ξ", "$" etc.
    let sparklinePoints: [(Double, Double)]

    private var isUp: Bool { dayChange >= 0 }
    private var color: Color { isUp ? .cbSuccess : .cbLoss }
    private var sign: String { isUp ? "+" : "−" }

    var body: some View {
        HStack(spacing: 12) {
            // Icon
            ZStack {
                Circle().fill(iconColor)
                Text(glyph)
                    .font(.system(size: 18, weight: .bold))
                    .foregroundStyle(.white)
            }
            .frame(width: 40, height: 40)

            VStack(alignment: .leading, spacing: 2) {
                Text(assetName)
                    .font(.cbAssetTitle)
                    .foregroundStyle(Color.cbTextPrimary)
                HStack(spacing: 4) {
                    Text(ticker).font(.cbTicker).tracking(0.3)
                    Text("· \(holdings)").font(.cbBodySmall)
                }
                .foregroundStyle(Color.cbTextSecondary)
            }

            Spacer()

            // Mini sparkline
            MiniSparkline(points: sparklinePoints, color: color)
                .frame(width: 56, height: 20)

            VStack(alignment: .trailing, spacing: 2) {
                Text(price, format: .currency(code: "USD"))
                    .font(.cbAssetPrice)
                    .foregroundStyle(Color.cbTextPrimary)
                    .monospacedDigit()
                Text("\(sign)\(abs(dayChangePct * 100), specifier: "%.2f")%")
                    .font(.cbAssetChange)
                    .foregroundStyle(color)
                    .monospacedDigit()
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 64)
        .background(Color.cbCanvas)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.cbDivider).frame(height: 0.5)
        }
    }
}

struct MiniSparkline: View {
    let points: [(Double, Double)]  // (timestamp, price)
    let color: Color

    var body: some View {
        GeometryReader { geo in
            Path { path in
                guard !points.isEmpty else { return }
                let xs = points.map(\.0)
                let ys = points.map(\.1)
                guard let xMin = xs.min(), let xMax = xs.max(),
                      let yMin = ys.min(), let yMax = ys.max(),
                      xMax > xMin, yMax > yMin else { return }
                let xRange = xMax - xMin
                let yRange = yMax - yMin
                for (i, p) in points.enumerated() {
                    let x = CGFloat((p.0 - xMin) / xRange) * geo.size.width
                    let y = geo.size.height - CGFloat((p.1 - yMin) / yRange) * geo.size.height
                    if i == 0 { path.move(to: CGPoint(x: x, y: y)) }
                    else { path.addLine(to: CGPoint(x: x, y: y)) }
                }
            }
            .stroke(color, style: StrokeStyle(lineWidth: 1.5, lineCap: .round, lineJoin: .round))
        }
    }
}
```

### 4-Up Action Row (Buy / Sell / Send / Receive)

```swift
struct AssetActionRow: View {
    var onBuy: () -> Void
    var onSell: () -> Void
    var onSend: () -> Void
    var onReceive: () -> Void

    var body: some View {
        HStack(spacing: 8) {
            ActionButton(icon: "arrow.up", label: "Buy",     action: onBuy)
            ActionButton(icon: "arrow.down", label: "Sell",   action: onSell)
            ActionButton(icon: "paperplane", label: "Send",  action: onSend)
            ActionButton(icon: "qrcode", label: "Receive",   action: onReceive)
        }
        .padding(.horizontal, 16)
    }
}

private struct ActionButton: View {
    let icon: String
    let label: String
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            VStack(spacing: 8) {
                Image(systemName: icon)
                    .font(.system(size: 24, weight: .semibold))
                    .foregroundStyle(Color.cbBlue)
                Text(label)
                    .font(.cbButtonSmall)
                    .foregroundStyle(Color.cbTextPrimary)
            }
            .frame(maxWidth: .infinity)
            .padding(.vertical, 16)
            .background(RoundedRectangle(cornerRadius: 12).fill(Color.cbSurfaceGray))
        }
        .sensoryFeedback(.impact(weight: .light), trigger: UUID())
    }
}
```

### Primary CTA (Buy Button)

```swift
struct CBPrimaryButton: View {
    let label: String
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.cbButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 48)
                .background(RoundedRectangle(cornerRadius: 12).fill(Color.cbBlue))
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
    }
}

struct CBSecondaryButton: View {
    let label: String
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.cbButton)
                .foregroundStyle(Color.cbTextPrimary)
                .frame(maxWidth: .infinity)
                .frame(height: 48)
                .background(RoundedRectangle(cornerRadius: 12).fill(Color.cbSurfaceGray2))
        }
    }
}
```

### Coinbase C-Mark Logomark

```swift
struct CoinbaseCMark: View {
    var size: CGFloat = 28
    var color: Color = .cbBlue

    var body: some View {
        ZStack {
            Circle()
                .stroke(color, lineWidth: size * 0.18)
            Rectangle()
                .fill(color)
                .frame(width: size * 0.42, height: size * 0.16)
        }
        .frame(width: size, height: size)
    }
}

// Animated loading variant
struct CoinbaseCMarkLoading: View {
    @State private var rotate = 0.0

    var body: some View {
        CoinbaseCMark()
            .rotationEffect(.degrees(rotate))
            .onAppear {
                withAnimation(.linear(duration: 1.5).repeatForever(autoreverses: false)) {
                    rotate = 360
                }
            }
    }
}
```

### Wallet Address Display

```swift
struct WalletAddressView: View {
    let address: String  // full address
    @State private var copied = false

    private var truncated: String {
        guard address.count > 14 else { return address }
        let start = address.prefix(10)
        let end = address.suffix(8)
        return "\(start)...\(end)"
    }

    var body: some View {
        Button(action: {
            UIPasteboard.general.string = address
            withAnimation { copied = true }
            DispatchQueue.main.asyncAfter(deadline: .now() + 1.5) { copied = false }
        }) {
            HStack {
                Text(truncated)
                    .font(.cbWalletAddr)
                    .foregroundStyle(Color.cbTextPrimary)
                Spacer()
                Image(systemName: copied ? "checkmark" : "doc.on.doc")
                    .foregroundStyle(copied ? Color.cbSuccess : Color.cbTextSecondary)
            }
            .padding(12)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.cbSurfaceGray))
        }
        .sensoryFeedback(.success, trigger: copied)
    }
}
```

## 4. Tab Bar

```swift
struct CoinbaseRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(red: 0.882, green: 0.894, blue: 0.910, alpha: 1)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeView()    .tabItem { Label("Home",    systemImage: "house") }
            TradeView()   .tabItem { Label("Trade",   systemImage: "arrow.left.arrow.right") }
            CardsView()   .tabItem { Label("Cards",   systemImage: "creditcard") }
            EarnView()    .tabItem { Label("Earn",    systemImage: "percent") }
            WalletView()  .tabItem { Label("Wallet",  systemImage: "wallet.pass") }
        }
        .tint(Color.cbBlue)
    }
}
```

## 5. Motion & Haptics

```swift
// Buy/Sell tap
.sensoryFeedback(.impact(weight: .medium), trigger: tradePressed)

// Order placed
.sensoryFeedback(.success, trigger: orderConfirmed)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// 4-up action button tap (light haptic since they're previews)
.sensoryFeedback(.impact(weight: .light), trigger: actionPressed)

// Wallet address copy
.sensoryFeedback(.success, trigger: addressCopied)

// Portfolio update — numeric text morph during scrubbing
.contentTransition(.numericText())
.animation(.snappy(duration: 0.15), value: portfolioValue)

// C-mark loading rotation
withAnimation(.linear(duration: 1.5).repeatForever(autoreverses: false)) { rotation = 360 }

// Chart scrubber — instant gesture response, no spring
.gesture(DragGesture(minimumDistance: 0).onChanged { ... })

// Order confirmation checkmark scale-in
.scaleEffect(showCheck ? 1.0 : 0.5)
.animation(.spring(response: 0.35, dampingFraction: 0.7).delay(0.1), value: showCheck)
```

## 6. SF Symbols (Coinbase's icon equivalents)

| Component | Symbol | Size |
|-----------|--------|------|
| Home tab | `house` / `house.fill` | 24pt |
| Trade tab | `arrow.left.arrow.right` | 24pt |
| Cards tab | `creditcard` / `creditcard.fill` | 24pt |
| Earn tab | `percent` | 24pt |
| Wallet tab | `wallet.pass` / `wallet.pass.fill` | 24pt |
| 4-up Buy | `arrow.up` | 24pt |
| 4-up Sell | `arrow.down` | 24pt |
| 4-up Send | `paperplane` | 24pt |
| 4-up Receive | `qrcode` | 24pt |
| Search | `magnifyingglass` | 16pt |
| Notifications | `bell` / `bell.fill` | 22pt |
| Watchlist heart | `star` / `star.fill` | 18pt |
| Settings | `gearshape` | 22pt |
| Back | `chevron.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Success | `checkmark.circle.fill` | 32-80pt |
| Failed | `xmark.circle.fill` | 24pt |
| Pending | `clock.fill` | 16pt |
| Copy | `doc.on.doc` | 16pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`, `.contentTransition(.numericText())`, Swift Charts); on iOS 16 use `UIImpactFeedbackGenerator` and a manual `Path` chart
- Tabular numerals: apply `.monospacedDigit()` to every Text rendering a price, percentage, or quantity so the asset list aligns
- Coinbase Mono required for wallet addresses and transaction hashes — use `.system(.body, design: .monospaced)` as fallback; never render an address in proportional Sans
- VoiceOver: asset row `accessibilityLabel("Bitcoin, B T C, you own 0.1842, current price $67,234.18, up 1.92 percent today")`; portfolio hero `accessibilityLabel("Portfolio balance $12,847, up $847 today, up 6.59 percent")`
- Contrast: `#0A0B0D` on `#FFFFFF` exceeds WCAG AAA; `#5B616E` on white meets AA at 14pt+; Coinbase Blue `#0052FF` on white meets AAA at all sizes
- Color-blind users: green/red is the primary signal — pair with directional symbols ("+"/"−") and ensure sparkline shape carries information regardless of color. Optionally add `arrow.up` / `arrow.down` SF Symbol prefixes on the asset change text when `accessibilityDifferentiateWithoutColor` is true
- Reduce Motion: skip the numeric-text morph during chart scrubbing and the C-mark rotation; use crossfade for the rotation alternative
- Dark mode: switch entire palette via `@Environment(\.colorScheme)`; Coinbase Blue shifts to `#3B6CFF` on dark for AA contrast
- Dynamic Type: portfolio hero (40pt) scales to 56pt max; buy/sell entry (56pt) scales to 72pt; tab labels, range chips, and Mono addresses are fixed
- Wallet address copy: when announcing via VoiceOver, expand the truncated address to the full string for clarity
