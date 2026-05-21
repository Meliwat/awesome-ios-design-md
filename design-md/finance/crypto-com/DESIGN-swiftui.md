# Crypto.com (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Crypto.com's premium navy financial language into paste-ready SwiftUI: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views (including the metallic Visa card).

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — default)
    static let ccCanvas    = Color(red: 0.012, green: 0.024, blue: 0.059) // #03060F
    static let ccSurface1  = Color(red: 0.043, green: 0.078, blue: 0.149) // #0B1426
    static let ccSurface2  = Color(red: 0.063, green: 0.247, blue: 0.408) // #103F68 (Navy 2)
    static let ccSurface3  = Color(red: 0.082, green: 0.149, blue: 0.247) // #15263F
    static let ccDivider   = Color(red: 0.110, green: 0.200, blue: 0.329) // #1C3354

    // MARK: - Canvas & Surfaces (Light — secondary)
    static let ccCanvasLight   = Color.white                                  // #FFFFFF
    static let ccSurface1Light = Color(red: 0.957, green: 0.969, blue: 0.984) // #F4F7FB
    static let ccDividerLight  = Color(red: 0.890, green: 0.914, blue: 0.949) // #E3E9F2

    // MARK: - Brand (decorative)
    static let ccNavy   = Color(red: 0.0,   green: 0.176, blue: 0.455) // #002D74
    static let ccNavy2  = Color(red: 0.063, green: 0.247, blue: 0.408) // #103F68

    // MARK: - Accent (interactive)
    static let ccAccent        = Color(red: 0.067, green: 0.600, blue: 0.980) // #1199FA
    static let ccAccentPressed = Color(red: 0.043, green: 0.478, blue: 0.820) // #0B7AD1

    // MARK: - Market Semantics (never invert)
    static let ccUp   = Color(red: 0.0,   green: 0.753, blue: 0.545) // #00C08B
    static let ccDown = Color(red: 0.965, green: 0.282, blue: 0.365) // #F6485D

    // MARK: - Card Metals
    static let ccGold = Color(red: 0.784, green: 0.635, blue: 0.290) // #C8A24A

    // MARK: - Text
    static let ccTextPrimary    = Color(red: 0.957, green: 0.969, blue: 0.984) // #F4F7FB
    static let ccTextSecondary  = Color(red: 0.553, green: 0.627, blue: 0.741) // #8DA0BD
    static let ccTextTertiary   = Color(red: 0.357, green: 0.431, blue: 0.549) // #5B6E8C
    static let ccTextPrimaryLt  = Color(red: 0.039, green: 0.122, blue: 0.267) // #0A1F44

    // MARK: - Semantic
    static let ccWarning = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623
}

extension Color { static let ccAccentTint = Color.ccAccent.opacity(0.12) }

// Brand & card gradients
extension LinearGradient {
    static let ccBrandNavy = LinearGradient(colors: [.ccNavy, .ccNavy2],
        startPoint: .topLeading, endPoint: .bottomTrailing)

    static let ccCardMidnight = LinearGradient(colors: [Color(hex: 0x1A1A1E), Color(hex: 0x2A2A30)],
        startPoint: .topLeading, endPoint: .bottomTrailing)
    static let ccCardRuby = LinearGradient(colors: [Color(hex: 0x5A1020), Color(hex: 0x8A1830)],
        startPoint: .topLeading, endPoint: .bottomTrailing)
    static let ccCardJade = LinearGradient(colors: [Color(hex: 0x0E3A30), Color(hex: 0x14564A)],
        startPoint: .topLeading, endPoint: .bottomTrailing)
    static let ccCardObsidian = LinearGradient(colors: [Color(hex: 0x0A0A0C), Color(hex: 0x1A1A1E), Color(hex: 0x26262C)],
        startPoint: .topLeading, endPoint: .bottomTrailing)
}

extension Color {
    init(hex: UInt) {
        self.init(red: Double((hex >> 16) & 0xFF) / 255,
                  green: Double((hex >> 8) & 0xFF) / 255,
                  blue: Double(hex & 0xFF) / 255)
    }
}
```

## 2. Typography

Crypto.com's UI face is a custom grotesque — use **Manrope** as the open stand-in; all money/PAN/APR uses **Roboto Mono** with tabular figures. Bundle both via `Info.plist`.

```swift
extension Font {
    // UI Sans (Manrope)
    static let ccScreenTitle = Font.custom("Manrope-ExtraBold", size: 32)
    static let ccSection     = Font.custom("Manrope-ExtraBold", size: 22)
    static let ccRowTitle    = Font.custom("Manrope-Bold",      size: 18)
    static let ccBody        = Font.custom("Manrope-Regular",   size: 16)
    static let ccListLabel   = Font.custom("Manrope-Bold",      size: 15)
    static let ccCardTier    = Font.custom("Manrope-Bold",      size: 13)
    static let ccMeta        = Font.custom("Manrope-Regular",   size: 14)
    static let ccChip        = Font.custom("Manrope-SemiBold",  size: 13)
    static let ccCaption     = Font.custom("Manrope-SemiBold",  size: 12)
    static let ccTab         = Font.custom("Manrope-SemiBold",  size: 10)
    static let ccButton      = Font.custom("Manrope-Bold",      size: 15)

    // Numeric Mono (Roboto Mono) — always tabular
    static let ccBalance = Font.custom("RobotoMono-SemiBold", size: 30)
    static let ccPrice   = Font.custom("RobotoMono-SemiBold", size: 15)
    static let ccPAN     = Font.custom("RobotoMono-Medium",   size: 15)
    static let ccPctSm   = Font.custom("RobotoMono-SemiBold", size: 12)
}

extension View { func ccTabular() -> some View { self.monospacedDigit() } }
```

## 3. Signature Components

### Metallic Visa Card

```swift
struct VisaCard: View {
    enum Tier { case midnight, ruby, jade, obsidian
        var name: String { ["midnight":"Midnight Blue","ruby":"Ruby Steel","jade":"Jade Green","obsidian":"Obsidian"]["\(self)"] ?? "" }
        var gradient: LinearGradient {
            switch self {
            case .midnight: return .ccCardMidnight
            case .ruby:     return .ccCardRuby
            case .jade:     return .ccCardJade
            case .obsidian: return .ccCardObsidian
            }
        }
    }
    let tier: Tier
    let pan: String        // "5482 •••• •••• 7310"
    let holder: String     // "ALEX RIVERA"

    var body: some View {
        ZStack {
            RoundedRectangle(cornerRadius: 18).fill(tier.gradient)
            // gloss sheen
            RoundedRectangle(cornerRadius: 18)
                .fill(LinearGradient(colors: [.white.opacity(0.08), .clear],
                    startPoint: .topLeading, endPoint: .center))

            VStack(alignment: .leading, spacing: 0) {
                HStack(alignment: .top) {
                    Text(tier.name.uppercased())
                        .font(.ccCardTier).tracking(1.5)
                        .foregroundStyle(Color.ccGold)
                    Spacer()
                    Image(systemName: "diamond.fill") // placeholder for Crypto.com mark
                        .font(.system(size: 20)).foregroundStyle(.white)
                }
                RoundedRectangle(cornerRadius: 5)
                    .fill(LinearGradient(colors: [Color(hex: 0xD9C079), Color(hex: 0xA88D45)],
                        startPoint: .topLeading, endPoint: .bottomTrailing))
                    .frame(width: 36, height: 26)
                    .padding(.top, 18)
                Spacer()
                Text(pan).font(.ccPAN).tracking(2).ccTabular()
                    .foregroundStyle(.white.opacity(0.92))
                HStack {
                    Text(holder.uppercased())
                        .font(.system(size: 12, weight: .semibold)).tracking(1)
                        .foregroundStyle(.white.opacity(0.75))
                    Spacer()
                    Text("VISA").font(.system(size: 18, weight: .heavy)).italic()
                        .foregroundStyle(.white)
                }
            }
            .padding(20)
        }
        .aspectRatio(1.586, contentMode: .fit)
        .shadow(color: .black.opacity(0.8), radius: 30, x: 0, y: 14)
    }
}
```

### Total Balance Hero

```swift
struct BalanceHero: View {
    let value: String      // "$ 18,402.65"
    let pnl: String        // "+$320.18 (+1.77%)"
    let gain: Bool
    @State private var hidden = false

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            Text("Total Balance (USD)").font(.ccCaption).foregroundStyle(Color.ccTextSecondary)
            HStack(spacing: 10) {
                Text(hidden ? "••••••" : value)
                    .font(.ccBalance).ccTabular().foregroundStyle(Color.ccTextPrimary)
                    .contentTransition(.opacity)
                Button { withAnimation(.easeInOut(duration: 0.2)) { hidden.toggle() } } label: {
                    Image(systemName: hidden ? "eye.slash" : "eye")
                        .font(.system(size: 14)).foregroundStyle(Color.ccTextSecondary)
                }
            }
            Text("\(pnl) Today").font(.ccPctSm).ccTabular()
                .foregroundStyle(gain ? Color.ccUp : Color.ccDown)
        }
        .padding(.horizontal, 20)
    }
}
```

### Watchlist Row

```swift
struct WatchRow: View {
    let symbol: String, name: String, price: String, pct: Double
    var body: some View {
        HStack(spacing: 12) {
            Circle().fill(Color(hex: 0xF7931A)).frame(width: 34, height: 34)
                .overlay(Text(symbol.prefix(1)).font(.system(size: 14, weight: .bold)).foregroundStyle(.white))
            VStack(alignment: .leading, spacing: 1) {
                Text(symbol).font(.ccListLabel).foregroundStyle(Color.ccTextPrimary)
                Text(name).font(.system(size: 12, weight: .regular)).foregroundStyle(Color.ccTextSecondary)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 2) {
                Text(price).font(.ccPrice).ccTabular().foregroundStyle(Color.ccTextPrimary)
                Text("\(pct >= 0 ? "+" : "")\(pct, specifier: "%.2f")%")
                    .font(.ccPctSm).ccTabular()
                    .foregroundStyle(pct >= 0 ? Color.ccUp : Color.ccDown)
            }
        }
        .padding(.horizontal, 20).padding(.vertical, 11)
    }
}
```

### Primary Pill Button

```swift
struct CryptoPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.ccButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).frame(height: 52)
                .background(Capsule().fill(Color.ccAccent))
        }
        .buttonStyle(PressableScale())
    }
}

struct PressableScale: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .opacity(configuration.isPressed ? 0.92 : 1)
            .animation(.easeOut(duration: 0.15), value: configuration.isPressed)
    }
}
```

### Quick-Action Circle Row

```swift
struct QuickActions: View {
    let items = [("plus","Deposit"), ("arrow.right","Send"),
                 ("arrow.left.arrow.right","Trade"), ("percent","Earn")]
    var body: some View {
        HStack {
            ForEach(items, id: \.1) { icon, label in
                VStack(spacing: 6) {
                    Circle().fill(Color.ccSurface1).frame(width: 46, height: 46)
                        .overlay(Image(systemName: icon).font(.system(size: 20))
                            .foregroundStyle(Color.ccAccent))
                    Text(label).font(.system(size: 11, weight: .semibold))
                        .foregroundStyle(Color.ccTextSecondary)
                }
                .frame(maxWidth: .infinity)
            }
        }
        .padding(.horizontal, 20)
    }
}
```

### Earn Row

```swift
struct EarnRow: View {
    let name: String, term: String, apr: String
    var body: some View {
        HStack {
            HStack(spacing: 10) {
                Circle().fill(Color.ccAccent).frame(width: 30, height: 30)
                VStack(alignment: .leading, spacing: 1) {
                    Text(name).font(.system(size: 14, weight: .bold)).foregroundStyle(Color.ccTextPrimary)
                    Text(term).font(.system(size: 11)).foregroundStyle(Color.ccTextSecondary)
                }
            }
            Spacer()
            Text(apr).font(.ccPrice).ccTabular().foregroundStyle(Color.ccUp)
        }
        .padding(.vertical, 12)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.ccDivider).frame(height: 1) }
    }
}
```

## 4. Bottom Tab Bar

Crypto.com's signature is a 5-tab bar with a **floating accent circle** for the center Trade action.

```swift
struct CryptoTabBar: View {
    @Binding var selected: Int
    var body: some View {
        ZStack(alignment: .bottom) {
            HStack {
                tab(0, "house.fill", "Home")
                tab(1, "chart.bar.fill", "Prices")
                Spacer().frame(width: 56)            // gap for center button
                tab(3, "percent", "Earn")
                tab(4, "creditcard.fill", "Card")
            }
            .padding(.horizontal, 8).padding(.top, 8).padding(.bottom, 14)
            .background(Color.ccCanvas.opacity(0.96))
            .overlay(alignment: .top) { Rectangle().fill(Color.ccDivider).frame(height: 1) }

            Button { selected = 2 } label: {
                Circle().fill(Color.ccAccent).frame(width: 44, height: 44)
                    .overlay(Image(systemName: "arrow.left.arrow.right")
                        .font(.system(size: 18, weight: .bold)).foregroundStyle(.white))
                    .shadow(color: Color.ccAccent.opacity(0.4), radius: 16, x: 0, y: 6)
            }
            .offset(y: -22)
        }
    }
    @ViewBuilder func tab(_ i: Int, _ icon: String, _ label: String) -> some View {
        Button { selected = i } label: {
            VStack(spacing: 4) {
                Image(systemName: icon).font(.system(size: 20))
                Text(label).font(.ccTab)
            }
            .foregroundStyle(selected == i ? Color.ccAccent : Color.ccTextTertiary)
            .frame(maxWidth: .infinity)
        }
    }
}
```

## 5. Motion

```swift
// Card flip (reveal CVV)
@State private var flipped = false
// .rotation3DEffect(.degrees(flipped ? 180 : 0), axis: (x: 0, y: 1, z: 0))
withAnimation(.easeInOut(duration: 0.4)) { flipped.toggle() }

// Balance reveal cross-dissolve
.contentTransition(.opacity)
withAnimation(.easeInOut(duration: 0.2)) { hidden.toggle() }

// Floating Trade press-spring
.scaleEffect(pressed ? 0.92 : 1)
withAnimation(.spring(response: 0.25, dampingFraction: 0.55)) { pressed.toggle() }

// Buy/Sell toggle color-morph
withAnimation(.easeOut(duration: 0.2)) { isBuy.toggle() }

// Price tick color pulse (~150ms, no full background flash)
withAnimation(.easeOut(duration: 0.15)) { priceColor = up ? .ccUp : .ccDown }

// Card carousel focus scale
// .scaleEffect(isFocused ? 1.0 : 0.94)

// Haptics
.sensoryFeedback(.impact(weight: .soft), trigger: flipped)       // card flip
.sensoryFeedback(.selection, trigger: selectedTerm)              // term/segment
.sensoryFeedback(.success, trigger: transactionComplete)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 20pt |
| Prices (tab) | `chart.bar` / `chart.bar.fill` | 20pt |
| Trade (center) | `arrow.left.arrow.right` | 18pt |
| Earn (tab) | `percent` | 20pt |
| Card (tab) | `creditcard` / `creditcard.fill` | 20pt |
| Notifications | `bell` | 20pt |
| Menu | `line.3.horizontal` | 20pt |
| Hide balance | `eye` / `eye.slash` | 14pt |
| Deposit | `plus` | 20pt |
| Send | `arrow.up.right` | 20pt |
| Receive | `arrow.down.left` | 20pt |
| Search | `magnifyingglass` | 16pt |
| Back | `chevron.left` | 22pt |
| Share | `square.and.arrow.up` | 18pt |
| More | `ellipsis` | 18pt |
| Up tick | `arrow.up` | 12pt |
| Down tick | `arrow.down` | 12pt |
| Stake / reward | `star.circle` | 18pt |
| Settings | `gearshape` | 18pt |
| Lock card | `lock` / `lock.fill` | 18pt |

## 7. Dark Mode

```swift
struct CryptoTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.ccCanvas : Color.ccCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.ccTextPrimary : Color.ccTextPrimaryLt)
    }
}
extension View { func cryptoTheme() -> some View { modifier(CryptoTheme()) } }
```

Crypto.com is dark-first with a navy cast (`#03060F`/`#0B1426`). The light theme swaps canvas → `#FFFFFF` and primary text → `#0A1F44`. **Accent `#1199FA`, Brand Navy `#002D74`/`#103F68`, market green/red, and all card-metal gradients are theme-invariant** — the Visa card looks identical in both modes. Default to dark.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` needs iOS 17; fall back to `UIImpactFeedbackGenerator`/`UINotificationFeedbackGenerator`); `.contentTransition(.opacity)` is iOS 16+
- Bundle Manrope (SIL OFL) + Roboto Mono (Apache 2.0) TTFs via `Info.plist` `UIAppFonts` — both free to distribute
- **Tabular figures mandatory** on every numeric `Text` (`.monospacedDigit()`) — without it the watchlist prices and the card PAN don't align
- Dynamic Type: scale screen titles, section heads, body, labels; keep card text (tier/PAN), numeric mono columns, and tab labels FIXED — scaling them breaks the card layout and tabular alignment
- Color is not the only signal: pair green/red with a `+`/`−` sign and an up/down arrow glyph for colorblind users
- VoiceOver: the Visa card is one element — label it "Obsidian Visa card, ending 7310, 5 percent cashback"; watchlist rows announce "Bitcoin, 67,284 dollars, up 2.34 percent"; the floating Trade button reads "Trade"
- Contrast: `#F4F7FB` on `#03060F` passes WCAG AA; white on `#1199FA` passes AA for the pill button; the gold tier text on dark card faces is decorative — provide the tier in the accessibility label, don't rely on the gold for contrast
- Reduce Motion: replace the 3D card flip with a cross-dissolve; disable the floating-button spring and price pulse; keep instant data updates
- Card security: never log the full PAN; the reveal action should require biometric (`LocalAuthentication`) before showing CVV/full number
- Dark mode: primary mode — verify navy surface steps (`#0B1426`/`#15263F`/`#1C3354`) keep ≥3:1 separation; floating sheets get a 1pt `#1C3354` border since shadows are subtle on `#03060F`
