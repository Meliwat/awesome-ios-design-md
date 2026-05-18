# Chime (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Chime's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the balance hero, the transaction row, the SpotMe banner, and the instant alert.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (Mint)
    static let chimeMint        = Color(red: 0.118, green: 0.776, blue: 0.467) // #1EC677
    static let chimeMintBright  = Color(red: 0.000, green: 0.839, blue: 0.494) // #00D67E
    static let chimeMintPressed = Color(red: 0.086, green: 0.659, blue: 0.384) // #16A862
    static let chimeMintSoft       = Color(red: 0.894, green: 0.969, blue: 0.933) // #E4F7EE  (light)
    static let chimeMintSoftDark   = Color(red: 0.071, green: 0.227, blue: 0.165) // #123A2A  (dark)
    static let chimeSpotMe         = Color(red: 0.071, green: 0.725, blue: 0.506) // #12B981  (light)
    static let chimeSpotMeDark     = Color(red: 0.180, green: 0.902, blue: 0.651) // #2EE6A6  (dark)
    static let chimeBalanceInk  = Color(red: 0.024, green: 0.125, blue: 0.078) // #062014

    // MARK: - Canvas & Surfaces (Light)
    static let chimeCanvas    = Color(red: 0.965, green: 0.984, blue: 0.973) // #F6FBF8
    static let chimeSurface1  = Color.white                                   // #FFFFFF
    static let chimeSurface2  = Color(red: 0.941, green: 0.965, blue: 0.949) // #F0F6F2
    static let chimeDivider   = Color(red: 0.894, green: 0.929, blue: 0.910) // #E4EDE8

    // MARK: - Canvas & Surfaces (Dark)
    static let chimeDarkCanvas   = Color(red: 0.055, green: 0.078, blue: 0.067) // #0E1411
    static let chimeDarkSurface1 = Color(red: 0.086, green: 0.125, blue: 0.106) // #16201B
    static let chimeDarkSurface2 = Color(red: 0.122, green: 0.173, blue: 0.145) // #1F2C25
    static let chimeDarkDivider  = Color(red: 0.141, green: 0.188, blue: 0.161) // #243029

    // MARK: - Text
    static let chimeTextPrimary   = Color(red: 0.059, green: 0.102, blue: 0.078) // #0F1A14
    static let chimeTextSecondary = Color(red: 0.361, green: 0.431, blue: 0.388) // #5C6E63
    static let chimeTextTertiary  = Color(red: 0.565, green: 0.639, blue: 0.600) // #90A399
    static let chimeDarkTextPrimary   = Color(red: 0.918, green: 0.949, blue: 0.925) // #EAF2EC
    static let chimeDarkTextSecondary = Color(red: 0.616, green: 0.690, blue: 0.643) // #9DB0A4

    // MARK: - Semantic
    static let chimePositive     = Color(red: 0.059, green: 0.682, blue: 0.388) // #0FAE63 (light)
    static let chimePositiveDark = Color(red: 0.000, green: 0.839, blue: 0.494) // #00D67E (dark)
    static let chimeNegative     = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D (light)
    static let chimeNegativeDark = Color(red: 1.000, green: 0.420, blue: 0.420) // #FF6B6B (dark)
    static let chimeWarning      = Color(red: 0.878, green: 0.525, blue: 0.000) // #E08600 (light)
    static let chimeWarningDark  = Color(red: 1.000, green: 0.698, blue: 0.243) // #FFB23E (dark)
}
```

## 2. Typography

Chime uses a clean geometric sans — **DM Sans** is the faithful free stand-in (SIL OFL). Bundle the TTFs via `Info.plist` / `UIAppFonts`. Use tabular figures for all amounts.

```swift
extension Font {
    static let chimeBalance   = Font.custom("DMSans-Bold",    size: 44)
    static let chimeBalanceCents = Font.custom("DMSans-Bold", size: 26)
    static let chimeScreenTitle  = Font.custom("DMSans-Bold", size: 32)
    static let chimeSection   = Font.custom("DMSans-Bold",    size: 22)
    static let chimeCardTitle = Font.custom("DMSans-Bold",    size: 18)
    static let chimeBody      = Font.custom("DMSans-Regular", size: 16)
    static let chimeRowTitle  = Font.custom("DMSans-SemiBold", size: 15)
    static let chimeAmount    = Font.custom("DMSans-Bold",    size: 15)
    static let chimeMeta      = Font.custom("DMSans-Regular", size: 14)
    static let chimeCaption   = Font.custom("DMSans-SemiBold", size: 12)
    static let chimeButton    = Font.custom("DMSans-Bold",    size: 16)
    static let chimeTab       = Font.custom("DMSans-SemiBold", size: 10)
    static let chimeChip      = Font.custom("DMSans-Bold",    size: 12)
    static let chimeLink      = Font.custom("DMSans-Bold",    size: 13)
}

// Apply tabular figures to any amount Text:
// Text(amount).font(.chimeAmount).monospacedDigit()
```

## 3. Signature Components

### Balance Hero Card

```swift
struct ChimeBalanceHero: View {
    let dollars: String     // "$2,847"
    let cents: String       // "19"
    let onMove: () -> Void
    let onPay: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 6) {
                Image(systemName: "creditcard.fill").font(.system(size: 12, weight: .bold))
                Text("Checking").font(.chimeCaption)
            }
            .foregroundStyle(Color.chimeBalanceInk.opacity(0.7))

            HStack(alignment: .top, spacing: 0) {
                Text(dollars).font(.chimeBalance)
                Text(".\(cents)").font(.chimeBalanceCents).baselineOffset(14)
            }
            .foregroundStyle(Color.chimeBalanceInk)
            .tracking(-1.5)
            .monospacedDigit()
            .padding(.top, 6)

            Text("Available to spend")
                .font(.chimeCaption)
                .foregroundStyle(Color.chimeBalanceInk.opacity(0.65))
                .padding(.top, 4)

            HStack(spacing: 10) {
                heroButton("arrow.left.arrow.right", "Move money", onMove)
                heroButton("paperplane.fill", "Pay anyone", onPay)
            }
            .padding(.top, 20)
        }
        .padding(EdgeInsets(top: 24, leading: 22, bottom: 24, trailing: 22))
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(
            LinearGradient(colors: [.chimeMint, Color(red: 0.071, green: 0.659, blue: 0.384)],
                           startPoint: .topLeading, endPoint: .bottomTrailing),
            in: RoundedRectangle(cornerRadius: 24)
        )
        .shadow(color: Color.chimeMint.opacity(0.45), radius: 16, y: 16)
    }

    private func heroButton(_ icon: String, _ title: String, _ action: @escaping () -> Void) -> some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Image(systemName: icon).font(.system(size: 15, weight: .bold))
                Text(title).font(.chimeLink)
            }
            .foregroundStyle(Color.chimeBalanceInk)
            .frame(maxWidth: .infinity, minHeight: 40)
            .background(Color.chimeBalanceInk.opacity(0.14), in: RoundedRectangle(cornerRadius: 12))
        }
        .buttonStyle(.plain)
    }
}
```

### Transaction Row

```swift
struct ChimeTransactionRow: View {
    let tile: TileStyle
    let name: String
    let meta: String
    let amount: String       // "-$38.42" or "+$60.00"
    let isPositive: Bool

    enum TileStyle { case merchant(String), action(String) }  // initial / SF Symbol

    var body: some View {
        HStack(spacing: 12) {
            tileView
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.chimeRowTitle).foregroundStyle(Color.chimeTextPrimary)
                Text(meta).font(.chimeMeta).foregroundStyle(Color.chimeTextSecondary)
            }
            Spacer()
            Text(amount)
                .font(.chimeAmount).monospacedDigit()
                .foregroundStyle(isPositive ? Color.chimePositive : Color.chimeTextPrimary)
        }
        .padding(.vertical, 12)
        .overlay(alignment: .bottom) { Divider().background(Color.chimeDivider) }
    }

    @ViewBuilder private var tileView: some View {
        switch tile {
        case .merchant(let initial):
            Text(initial).font(.chimeAmount).foregroundStyle(.white)
                .frame(width: 42, height: 42)
                .background(LinearGradient(colors: [Color(red: 0.165, green: 0.227, blue: 0.200),
                                                    Color(red: 0.102, green: 0.149, blue: 0.125)],
                                           startPoint: .topLeading, endPoint: .bottomTrailing),
                            in: RoundedRectangle(cornerRadius: 14))
        case .action(let symbol):
            Image(systemName: symbol).font(.system(size: 19, weight: .bold))
                .foregroundStyle(Color.chimeSpotMe)
                .frame(width: 42, height: 42)
                .background(Color.chimeMintSoft, in: RoundedRectangle(cornerRadius: 14))
        }
    }
}
```

### SpotMe Banner

```swift
struct ChimeSpotMeBanner: View {
    let limit: String        // "$200"
    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: "star.fill").font(.system(size: 19, weight: .bold))
                .foregroundStyle(Color.chimeSpotMe)
                .frame(width: 38, height: 38)
                .background(Color.chimeMintSoft, in: RoundedRectangle(cornerRadius: 12))
            VStack(alignment: .leading, spacing: 2) {
                Text("SpotMe is on").font(.chimeRowTitle.weight(.bold)).foregroundStyle(Color.chimeTextPrimary)
                Text("Overdraft up to your limit, fee-free").font(.chimeCaption.weight(.regular)).foregroundStyle(Color.chimeTextSecondary)
            }
            Spacer()
            Text(limit).font(.chimeAmount).foregroundStyle(Color.chimeSpotMe)
        }
        .padding(EdgeInsets(top: 14, leading: 16, bottom: 14, trailing: 16))
        .background(Color.chimeSurface1, in: RoundedRectangle(cornerRadius: 16))
        .overlay(RoundedRectangle(cornerRadius: 16).stroke(Color.chimeDivider, lineWidth: 1))
    }
}
```

### Instant Transaction Alert

```swift
struct ChimeInstantAlert: View {
    let title: String
    let sub: String
    let amount: String
    var body: some View {
        HStack(spacing: 14) {
            Image(systemName: "checkmark").font(.system(size: 22, weight: .heavy))
                .foregroundStyle(Color.chimeBalanceInk)
                .frame(width: 44, height: 44)
                .background(Color.chimeMint, in: Circle())
            VStack(alignment: .leading, spacing: 2) {
                Text(title).font(.chimeRowTitle.weight(.bold)).foregroundStyle(Color.chimeTextPrimary)
                Text(sub).font(.chimeCaption.weight(.regular)).foregroundStyle(Color.chimeTextSecondary)
            }
            Spacer()
            Text(amount).font(.chimeCardTitle).monospacedDigit().foregroundStyle(Color.chimePositive)
        }
        .padding(EdgeInsets(top: 14, leading: 16, bottom: 14, trailing: 16))
        .background(Color.chimeSurface2, in: RoundedRectangle(cornerRadius: 18))
        .overlay(RoundedRectangle(cornerRadius: 18).stroke(Color.chimeDivider, lineWidth: 1))
        .shadow(color: Color.chimeTextPrimary.opacity(0.16), radius: 14, y: 12)
        .transition(.move(edge: .top).combined(with: .opacity))
    }
}
```

### Primary Button & Status Chip

```swift
struct ChimePrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.chimeButton).foregroundStyle(Color.chimeBalanceInk)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(Color.chimeMint, in: RoundedRectangle(cornerRadius: 14))
        }
        .buttonStyle(ChimePressStyle())
    }
}
struct ChimePressStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .brightness(configuration.isPressed ? -0.05 : 0)
    }
}

enum ChimeStatus { case posted, pending, spotMe, declined }
struct ChimeStatusChip: View {
    let status: ChimeStatus
    var body: some View {
        let (bg, fg, label): (Color, Color, String) = {
            switch status {
            case .posted:   return (.chimeMintSoft, .chimePositive, "Posted")
            case .pending:  return (Color.chimeWarning.opacity(0.15), .chimeWarning, "Pending")
            case .spotMe:   return (Color.chimeSpotMe.opacity(0.14), .chimeSpotMe, "SpotMe covered")
            case .declined: return (Color.chimeNegative.opacity(0.15), .chimeNegative, "Declined")
            }
        }()
        Text(label).font(.chimeChip).foregroundStyle(fg)
            .padding(.horizontal, 14).padding(.vertical, 7)
            .background(bg, in: Capsule())
    }
}
```

## 4. Bottom Tab Bar

```swift
struct ChimeTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            MoveView().tabItem { Label("Move", systemImage: "arrow.left.arrow.right") }
            CardView().tabItem { Label("Card", systemImage: "creditcard.fill") }
            GrowView().tabItem { Label("Grow", systemImage: "chart.line.uptrend.xyaxis") }
            MeView().tabItem   { Label("Me", systemImage: "person.crop.circle") }
        }
        .tint(.chimeMintBright)   // active = bright mint, filled icon; no pill
    }
}
```

## 5. Motion

```swift
// Balance count-up — odometer roll on load / after transfer
@State private var displayed: Double = 0
Text(displayed, format: .currency(code: "USD"))
    .contentTransition(.numericText())
    .onAppear { withAnimation(.easeOut(duration: 0.6)) { displayed = actualBalance } }

// Instant alert toast — slide down + soft success haptic
withAnimation(.spring(response: 0.35, dampingFraction: 0.8)) { showAlert = true }
    .sensoryFeedback(.success, trigger: showAlert)
// auto-dismiss
DispatchQueue.main.asyncAfter(deadline: .now() + 4) {
    withAnimation { showAlert = false }
}

// SpotMe boost — number scale bump + medium haptic
withAnimation(.spring(response: 0.3, dampingFraction: 0.55)) { spotLimitScale = 1.15 }
.sensoryFeedback(.impact(weight: .medium), trigger: spotBoosted)

// Transfer success — full-screen mint check stroke draw (500ms) + success haptic
.sensoryFeedback(.success, trigger: transferDone)

// New transaction insert
.transition(.move(edge: .top).combined(with: .opacity))   // 250ms ease-out
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Move (tab) | `arrow.left.arrow.right` | 22pt |
| Card (tab) | `creditcard` / `creditcard.fill` | 22pt |
| Grow (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Me (tab) | `person.crop.circle` | 22pt |
| Move money | `arrow.left.arrow.right` | 15pt |
| Pay anyone | `paperplane.fill` | 15pt |
| SpotMe | `star.fill` / `shield.lefthalf.filled` | 19pt |
| Deposit / incoming | `arrow.down.circle.fill` | 19pt |
| Alert check | `checkmark` | 22pt |
| Notifications | `bell` | 22pt |
| Add to Spot | `plus` | 15pt |
| Back | `chevron.left` | 17pt |
| Card frozen | `snowflake` | 18pt |
| Settings | `gearshape` | 20pt |
| Direct deposit | `dollarsign.arrow.circlepath` | 19pt |
| Grow / savings | `leaf.fill` | 19pt |

## 7. Dark Mode

```swift
struct ChimeTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.chimeDarkCanvas : Color.chimeCanvas)
            .foregroundStyle(scheme == .dark ? Color.chimeDarkTextPrimary : Color.chimeTextPrimary)
            .tint(.chimeMintBright)
    }
}
extension View { func chimeTheme() -> some View { modifier(ChimeTheme()) } }

// The mint hero gradient + #062014 ink are IDENTICAL in both modes.
// Dark swaps: chimeMintSoft → chimeMintSoftDark (#123A2A),
//             chimeSpotMe → chimeSpotMeDark (#2EE6A6),
//             chimePositive → chimePositiveDark (#00D67E),
//             chimeNegative → chimeNegativeDark (#FF6B6B).
// The mint-glow shadow softens on dark; neutral shadows nearly vanish —
// rounded #16201B surfaces + #243029 dividers carry structure.
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` need iOS 17 — gate behind `#available`, fall back to `UIImpactFeedbackGenerator` and a manual count-up timer)
- Bundle DM Sans TTFs (Regular, Medium, SemiBold, Bold) via `Info.plist` `UIAppFonts` — DM Sans is SIL OFL via Google Fonts, free to redistribute
- **Tabular figures**: apply `.monospacedDigit()` to the balance and every amount so columns align and the count-up doesn't jitter
- Dynamic Type: scale balance, screen titles, section/card headers, body, row titles, meta; keep tab labels, chip text, and the cents-superscript ratio FIXED
- VoiceOver: the balance announces "Available to spend, $2,847.19"; transaction rows announce "{name}, {meta}, {signed amount}"; the instant alert posts an `.announcement` notification so it's read aloud when it appears; SpotMe announces "SpotMe is on, limit $200"
- **Never convey money direction by color alone** — always keep the explicit `+`/`-` sign and the word in alerts ("received")
- Color contrast: `#062014` on the mint hero passes WCAG AA (the near-black ink is chosen for exactly this); `#0F1A14` on `#F6FBF8` passes AA; validate `#0FAE63` positive text against white at small sizes
- Reduce Motion: disable the balance odometer roll (set value instantly), the SpotMe scale bump, and the check-stroke draw (crossfade instead); keep the alert slide as a fade
- Dark mode: canvas `#0E1411` (NOT neutral black); the mint hero gradient and `#062014` ink stay constant; soft fills, SpotMe, positive, and negative all shift to their bright dark variants
