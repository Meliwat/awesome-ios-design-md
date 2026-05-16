# Revolut (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Revolut's visual language into paste-ready SwiftUI code: `Color` extensions, the brand gradient, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let revCanvas   = Color(red: 0.039, green: 0.039, blue: 0.059) // #0A0A0F
    static let revSurface1 = Color(red: 0.086, green: 0.086, blue: 0.122) // #16161F
    static let revSurface2 = Color(red: 0.118, green: 0.118, blue: 0.165) // #1E1E2A
    static let revSurface3 = Color(red: 0.157, green: 0.157, blue: 0.227) // #28283A
    static let revDivider  = Color(red: 0.165, green: 0.165, blue: 0.220) // #2A2A38
    static let revBorder   = Color(red: 0.200, green: 0.200, blue: 0.290) // #33334A

    // MARK: - Text
    static let revTextPrimary   = Color.white                                  // #FFFFFF
    static let revTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.667) // #9A9AAA
    static let revTextTertiary  = Color(red: 0.416, green: 0.416, blue: 0.494) // #6A6A7E

    // MARK: - Brand
    static let revGradStart = Color(red: 0.357, green: 0.420, blue: 1.000) // #5B6BFF
    static let revGradEnd   = Color(red: 0.612, green: 0.420, blue: 1.000) // #9C6BFF
    static let revBrand     = Color(red: 0.420, green: 0.357, blue: 1.000) // #6B5BFF
    static let revBrandPressed = Color(red: 0.337, green: 0.282, blue: 0.839) // #5648D6
    static let revBrandTint = Color(red: 0.110, green: 0.106, blue: 0.200) // #1C1B33

    // MARK: - Semantic
    static let revIncome = Color(red: 0.122, green: 0.820, blue: 0.482) // #1FD17B
    static let revSpend  = Color(red: 1.000, green: 0.353, blue: 0.416) // #FF5A6A
    static let revWarn   = Color(red: 1.000, green: 0.698, blue: 0.247) // #FFB23F
    static let revCrypto = Color(red: 0.969, green: 0.788, blue: 0.282) // #F7C948
}

extension LinearGradient {
    static let revBrand = LinearGradient(
        colors: [.revGradStart, .revGradEnd],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
}
```

## 2. Typography

Aeonik is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or fall back to `.system` — Inter is the closest free substitute.

```swift
extension Font {
    static let revBalance     = Font.custom("Aeonik-Bold",   size: 40).weight(.bold)
    static let revTitleLarge  = Font.custom("Aeonik-Bold",   size: 28).weight(.bold)
    static let revSection     = Font.custom("Aeonik-Bold",   size: 22).weight(.bold)
    static let revTileBalance = Font.custom("Aeonik-Bold",   size: 22).weight(.bold)
    static let revSubsection  = Font.custom("Aeonik-Medium", size: 18).weight(.semibold)
    static let revAmount      = Font.custom("Aeonik-Medium", size: 16).weight(.semibold)
    static let revMerchant    = Font.custom("Aeonik-Medium", size: 16).weight(.semibold)
    static let revBody        = Font.custom("Aeonik-Regular", size: 15).weight(.regular)
    static let revButton      = Font.custom("Aeonik-Medium", size: 16).weight(.semibold)
    static let revMeta        = Font.custom("Aeonik-Regular", size: 13).weight(.regular)
    static let revLabelUpper  = Font.custom("Aeonik-Bold",   size: 11).weight(.bold)
    static let revTab         = Font.custom("Aeonik-Medium", size: 10).weight(.semibold)
    static let revCaption     = Font.custom("Aeonik-Regular", size: 11).weight(.regular)
}

extension Font {
    static func rev(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

Tabular figures on every money label:

```swift
Text("$4,820.50").font(.revTileBalance).monospacedDigit()
```

## 3. Signature Components

### Primary Gradient CTA

```swift
struct RevPrimaryButton: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.revButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(LinearGradient.revBrand, in: RoundedRectangle(cornerRadius: 16))
                .shadow(color: Color.revBrand.opacity(0.30), radius: 14, y: 8)
        }
        .sensoryFeedback(.impact(weight: .light), trigger: UUID())
        .buttonStyle(RevPressableStyle())
    }
}

struct RevPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .opacity(configuration.isPressed ? 0.85 : 1)
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.easeOut(duration: 0.15), value: configuration.isPressed)
    }
}

struct RevSecondaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.revButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(Color.revSurface2, in: RoundedRectangle(cornerRadius: 16))
        }
        .buttonStyle(RevPressableStyle())
    }
}
```

### Currency Balance Tile

```swift
struct CurrencyTile: View {
    let flag: String      // emoji or asset
    let code: String
    let name: String
    let balance: String

    var body: some View {
        HStack(spacing: 12) {
            Text(flag)
                .font(.system(size: 20))
                .frame(width: 28, height: 28)
                .background(Circle().fill(Color.revSurface2))
            VStack(alignment: .leading, spacing: 2) {
                Text(code).font(.revMerchant).foregroundStyle(.white)
                Text(name).font(.revMeta).foregroundStyle(.revTextSecondary)
            }
            Spacer()
            Text(balance)
                .font(.revTileBalance)
                .monospacedDigit()
                .foregroundStyle(.white)
        }
        .padding(.horizontal, 16)
        .frame(height: 72)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(Color.revSurface1)
                .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.revDivider, lineWidth: 1))
        )
    }
}
```

### Transaction Row

```swift
struct TransactionRow: View {
    let merchant: String
    let meta: String
    let amount: String
    let incoming: Bool
    let logo: Image

    var body: some View {
        HStack(spacing: 12) {
            logo
                .resizable().aspectRatio(contentMode: .fill)
                .frame(width: 40, height: 40)
                .clipShape(Circle())
            VStack(alignment: .leading, spacing: 2) {
                Text(merchant).font(.revMerchant).foregroundStyle(.white).lineLimit(1)
                Text(meta).font(.revMeta).foregroundStyle(.revTextSecondary).lineLimit(1)
            }
            Spacer()
            Text(amount)
                .font(.revAmount)
                .monospacedDigit()
                .foregroundStyle(incoming ? Color.revIncome : .white)
        }
        .padding(.horizontal, 16)
        .frame(height: 64)
        .contentShape(Rectangle())
    }
}
```

### Spend Analytics Donut

```swift
struct SpendDonut: View {
    let total: String
    let progress: Double // 0...1
    @State private var animated: Double = 0

    var body: some View {
        VStack(spacing: 16) {
            ZStack {
                Circle().stroke(Color.revSurface3, lineWidth: 14)
                Circle()
                    .trim(from: 0, to: animated)
                    .stroke(
                        AngularGradient(colors: [.revGradStart, .revGradEnd], center: .center),
                        style: StrokeStyle(lineWidth: 14, lineCap: .round)
                    )
                    .rotationEffect(.degrees(-90))
                VStack(spacing: 2) {
                    Text(total).font(.revSection).monospacedDigit().foregroundStyle(.white)
                    Text("this month").font(.revMeta).foregroundStyle(.revTextSecondary)
                }
            }
            .frame(width: 180, height: 180)
        }
        .padding(20)
        .background(RoundedRectangle(cornerRadius: 20).fill(Color.revSurface1))
        .onAppear {
            withAnimation(.easeOut(duration: 0.7)) { animated = progress }
        }
    }
}
```

### Metal Card Hero (flip + sheen)

```swift
struct MetalCardHero: View {
    @State private var flipped = false
    @State private var sheen: CGFloat = -1

    var body: some View {
        ZStack {
            RoundedRectangle(cornerRadius: 16)
                .fill(
                    LinearGradient(colors: [Color(white: 0.18), Color(white: 0.07)],
                                   startPoint: .topLeading, endPoint: .bottomTrailing)
                )
            // diagonal sheen band
            RoundedRectangle(cornerRadius: 16)
                .fill(
                    LinearGradient(colors: [.clear, Color.white.opacity(0.18), .clear],
                                   startPoint: .topLeading, endPoint: .bottomTrailing)
                )
                .offset(x: sheen * 260)
                .mask(RoundedRectangle(cornerRadius: 16))

            VStack(alignment: .leading) {
                Text("Revolut").font(.revSubsection).foregroundStyle(.white.opacity(0.9))
                Spacer()
                Text(flipped ? "CVV 042" : "•••• 4821")
                    .font(.revAmount).monospacedDigit().foregroundStyle(.white)
            }
            .padding(20)
            .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .leading)
        }
        .aspectRatio(1.586, contentMode: .fit)
        .rotation3DEffect(.degrees(flipped ? 180 : 0), axis: (x: 0, y: 1, z: 0))
        .shadow(color: .black.opacity(0.5), radius: 24, y: 8)
        .onTapGesture {
            withAnimation(.easeInOut(duration: 0.45)) { flipped.toggle() }
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: flipped)
        .onAppear {
            withAnimation(.easeInOut(duration: 1.2).delay(0.2)) { sheen = 1 }
        }
    }
}
```

## 4. Brand Gradient Helpers

```swift
extension View {
    /// Apply the brand gradient as a foreground (e.g., active icon tint).
    func revGradientForeground() -> some View {
        self.overlay(LinearGradient.revBrand).mask(self)
    }
}

// Glow modifier for active / primary elements
struct RevGlow: ViewModifier {
    func body(content: Content) -> some View {
        content.shadow(color: Color.revBrand.opacity(0.30), radius: 14, y: 8)
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor(Color.revCanvas).withAlphaComponent(0.92)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            InvestView().tabItem { Label("Invest", systemImage: "chart.line.uptrend.xyaxis") }
            CryptoView().tabItem { Label("Crypto", systemImage: "bitcoinsign.circle.fill") }
            LifestyleView().tabItem { Label("Lifestyle", systemImage: "sparkles") }
            CardsView().tabItem { Label("Cards", systemImage: "creditcard.fill") }
        }
        .tint(.revBrand) // gradient applied per-icon where possible; brand solid as TabView tint
    }
}
```

## 6. Motion

Use SwiftUI spring/easing. Haptics via `.sensoryFeedback` (iOS 17+).

```swift
// Card flip — see MetalCardHero: rotation3DEffect + .impact(weight: .medium)

// Gradient CTA press — RevPressableStyle: opacity 0.85 + scale 0.98, 150ms

// Donut draw — Circle().trim animated 0→progress over 0.7s ease-out on appear

// Balance hide/reveal
struct BalanceReveal: ViewModifier {
    let hidden: Bool
    func body(content: Content) -> some View {
        content.blur(radius: hidden ? 12 : 0)
            .animation(.easeInOut(duration: 0.25), value: hidden)
    }
}

// Segmented thumb — matchedGeometryEffect on a gradient capsule, 0.22s ease
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 24pt |
| Invest (tab) | `chart.line.uptrend.xyaxis` | 24pt |
| Crypto (tab) | `bitcoinsign.circle` / `.fill` | 24pt |
| Lifestyle (tab) | `sparkles` | 24pt |
| Cards (tab) | `creditcard` / `creditcard.fill` | 24pt |
| Add money | `plus` | 22pt |
| Exchange | `arrow.left.arrow.right` | 22pt |
| Freeze card | `snowflake` | 22pt |
| Send / pay | `paperplane.fill` | 22pt |
| Contactless | `wave.3.right` | 18pt |
| Search | `magnifyingglass` | 18pt |
| Notifications | `bell` / `bell.fill` | 22pt |
| Statement | `doc.text` | 20pt |
| Income arrow | `arrow.down.left` | 14pt |
| Outgoing arrow | `arrow.up.right` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on titles, body, merchant names, amounts — keep tab labels, uppercase labels, and currency-tile figures at a fixed size for column alignment
- VoiceOver: announce balances with currency ("Balance, four thousand eight hundred twenty dollars and fifty cents"); on a transaction row, group merchant + meta + amount and state direction ("Sent 9 pounds 99 to Spotify")
- Contrast: `#9A9AAA` secondary on `#0A0A0F` passes WCAG AA at 14pt+; validate 11pt labels and bump toward `#B0B0C0` if targeting strict compliance. The gradient CTA's white text passes against both stops
- Reduce Motion: when `accessibilityReduceMotion` is on, replace the card flip with a crossfade and skip the sheen sweep; draw the donut instantly
- Reduce Transparency: swap the tab bar blur for a solid `rgba(10,10,15,0.98)` fill
- Touch targets: gradient CTA 52pt, quick-action circles 56pt, 24pt tab glyphs get a 44pt hit area
