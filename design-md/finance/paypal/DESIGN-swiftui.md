# PayPal (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates PayPal's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the signature balance card, Activity row, and Send Money flow.

Targets iOS 17+. The `Color` palette includes both light and dark mode tokens; `Font` extensions assume PayPal Sans Big and PayPal Sans Small are bundled via `Info.plist`.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let payPalBlue        = Color(red: 0.00, green: 0.188, blue: 0.529)  // #003087
    static let payPalSky         = Color(red: 0.00, green: 0.439, blue: 0.729)  // #0070BA
    static let payPalCobalt      = Color(red: 0.00, green: 0.110, blue: 0.392)  // #001C64
    static let payPalBlueDark    = Color(red: 0.231, green: 0.510, blue: 0.965) // #3B82F6 (dark-mode shifted)

    // MARK: - Canvas & Surfaces (light)
    static let ppCanvas       = Color(red: 1.00, green: 1.00, blue: 1.00)    // #FFFFFF
    static let ppSurfaceGray  = Color(red: 0.961, green: 0.969, blue: 0.980) // #F5F7FA
    static let ppSurfaceGray2 = Color(red: 0.933, green: 0.945, blue: 0.957) // #EEF1F4
    static let ppDivider      = Color(red: 0.898, green: 0.910, blue: 0.929) // #E5E8ED

    // MARK: - Text (light)
    static let ppTextPrimary   = Color(red: 0.00, green: 0.078, blue: 0.208)  // #001435
    static let ppTextSecondary = Color(red: 0.173, green: 0.180, blue: 0.184) // #2C2E2F
    static let ppTextMuted     = Color(red: 0.408, green: 0.443, blue: 0.451) // #687173
    static let ppTextTertiary  = Color(red: 0.616, green: 0.639, blue: 0.651) // #9DA3A6

    // MARK: - Semantic
    static let ppSuccess       = Color(red: 0.110, green: 0.545, blue: 0.263) // #1C8B43
    static let ppSuccessBg     = Color(red: 0.894, green: 0.961, blue: 0.918) // #E4F5EA
    static let ppError         = Color(red: 0.824, green: 0.00, blue: 0.129)  // #D20021
    static let ppErrorBg       = Color(red: 0.988, green: 0.898, blue: 0.910) // #FCE5E8
    static let ppWarning       = Color(red: 1.00, green: 0.722, blue: 0.110)  // #FFB81C
    static let ppWarningBg     = Color(red: 1.00, green: 0.965, blue: 0.878)  // #FFF6E0

    // MARK: - Activity Icon Colors
    static let ppIconSent      = Color.payPalBlue       // #003087
    static let ppIconReceived  = Color.ppSuccess        // #1C8B43
    static let ppIconCard      = Color.payPalSky        // #0070BA
    static let ppIconReward    = Color.ppWarning        // #FFB81C

    // MARK: - Dark mode
    static let ppDarkCanvas    = Color(red: 0.039, green: 0.055, blue: 0.102) // #0A0E1A
    static let ppDarkSurface1  = Color(red: 0.078, green: 0.102, blue: 0.165) // #141A2A
    static let ppDarkSurface2  = Color(red: 0.122, green: 0.153, blue: 0.251) // #1F2740
    static let ppDarkDivider   = Color(red: 0.165, green: 0.192, blue: 0.259) // #2A3142
    static let ppDarkTextPri   = Color.white
    static let ppDarkTextSec   = Color(red: 0.659, green: 0.682, blue: 0.769) // #A8AEC4
}
```

## 2. Typography

PayPal Sans Big and PayPal Sans Small are proprietary (Monotype, ~2018). Bundle TTFs via `Info.plist` (`UIAppFonts`); fall back to SF Pro Text. Apply `.monospacedDigit()` on prices and percentages.

```swift
extension Font {
    // Hero amounts
    static let ppBalanceHero    = Font.custom("PayPalSansBig-Bold",    size: 36)
    static let ppSendAmountHero = Font.custom("PayPalSansBig-Bold",    size: 56)

    // Titles
    static let ppScreenTitle    = Font.custom("PayPalSansBig-Bold",    size: 24)
    static let ppSectionHeader  = Font.custom("PayPalSansBig-Bold",    size: 18)
    static let ppCardTitle      = Font.custom("PayPalSansBig-Medium",  size: 16)
    static let ppActivityAmount = Font.custom("PayPalSansBig-Medium",  size: 16)

    // Body (Small variant)
    static let ppBody           = Font.custom("PayPalSansSmall-Regular", size: 16)
    static let ppBodySmall      = Font.custom("PayPalSansSmall-Regular", size: 14)
    static let ppActivityTitle  = Font.custom("PayPalSansSmall-Medium",  size: 15)
    static let ppActivitySub    = Font.custom("PayPalSansSmall-Regular", size: 13)
    static let ppMeta           = Font.custom("PayPalSansSmall-Regular", size: 13)
    static let ppLink           = Font.custom("PayPalSansSmall-Medium",  size: 15)
    static let ppTab            = Font.custom("PayPalSansSmall-Medium",  size: 11)
    static let ppChip           = Font.custom("PayPalSansSmall-Medium",  size: 13)

    // Buttons
    static let ppButton         = Font.custom("PayPalSansBig-Bold",     size: 17)
    static let ppButtonSmall    = Font.custom("PayPalSansSmall-Medium", size: 15)
}

// Fallback if PayPal Sans isn't bundled — SF Pro:
extension Font {
    static func pp(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Balance Card

```swift
struct BalanceCard: View {
    let balance: Double

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            Text("PayPal balance")
                .font(.ppActivitySub)
                .foregroundStyle(Color.ppTextMuted)

            Text(balance, format: .currency(code: "USD"))
                .font(.ppBalanceHero)
                .foregroundStyle(Color.ppTextPrimary)
                .monospacedDigit()
                .contentTransition(.numericText())
                .animation(.snappy, value: balance)

            HStack(spacing: 12) {
                Button(action: {}) {
                    Text("Add Money")
                        .font(.ppButton).foregroundStyle(.white)
                        .frame(maxWidth: .infinity).frame(height: 44)
                        .background(Capsule().fill(Color.payPalBlue))
                }
                Button(action: {}) {
                    Text("Transfer")
                        .font(.ppButtonSmall).foregroundStyle(Color.payPalBlue)
                        .frame(maxWidth: .infinity).frame(height: 44)
                        .overlay(Capsule().strokeBorder(Color.payPalBlue, lineWidth: 1.5))
                }
            }
        }
        .padding(20)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(Color.ppCanvas)
                .shadow(color: .black.opacity(0.06), radius: 8, y: 2)
        )
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.ppDivider, lineWidth: 1))
        .padding(.horizontal, 16)
    }
}
```

### Activity Row

```swift
struct ActivityRow: View {
    let name: String
    let subtitle: String
    let amount: Double
    let direction: Direction
    let icon: ActivityIcon

    enum Direction { case sent, received }
    enum ActivityIcon { case sent, received, card, reward }

    private var iconColor: Color {
        switch icon {
        case .sent:     return .ppIconSent
        case .received: return .ppIconReceived
        case .card:     return .ppIconCard
        case .reward:   return .ppIconReward
        }
    }
    private var iconSystemName: String {
        switch icon {
        case .sent:     return "arrow.up.right"
        case .received: return "arrow.down.left"
        case .card:     return "creditcard.fill"
        case .reward:   return "gift.fill"
        }
    }
    private var amountColor: Color { direction == .received ? .ppSuccess : .ppTextPrimary }
    private var sign: String { direction == .received ? "+" : "" }

    var body: some View {
        HStack(spacing: 12) {
            // Icon circle
            Circle()
                .fill(iconColor)
                .frame(width: 40, height: 40)
                .overlay(
                    Image(systemName: iconSystemName)
                        .font(.system(size: 18, weight: .semibold))
                        .foregroundStyle(.white)
                )

            VStack(alignment: .leading, spacing: 2) {
                Text(name)
                    .font(.ppActivityTitle)
                    .foregroundStyle(Color.ppTextPrimary)
                Text(subtitle)
                    .font(.ppActivitySub)
                    .foregroundStyle(Color.ppTextMuted)
            }

            Spacer()

            Text("\(sign)\(amount, format: .currency(code: "USD"))")
                .font(.ppActivityAmount)
                .foregroundStyle(amountColor)
                .monospacedDigit()
        }
        .padding(.horizontal, 16)
        .frame(minHeight: 68)
        .background(Color.ppCanvas)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.ppDivider).frame(height: 0.5)
        }
    }
}
```

### Primary CTA (Send Button)

```swift
struct PayPalPrimaryButton: View {
    let label: String
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.ppButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 48)
                .background(Capsule().fill(Color.payPalBlue))
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
    }
}

struct PayPalSecondaryButton: View {
    let label: String
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.ppButtonSmall)
                .foregroundStyle(Color.payPalBlue)
                .frame(maxWidth: .infinity)
                .frame(height: 48)
                .overlay(Capsule().strokeBorder(Color.payPalBlue, lineWidth: 1.5))
        }
    }
}
```

### Send Money Amount Screen

```swift
struct SendMoneyScreen: View {
    @State private var amount: String = "0"

    private var sendLabel: String {
        let value = Double(amount) ?? 0
        return "Send \(value.formatted(.currency(code: "USD")))"
    }

    var body: some View {
        VStack(spacing: 24) {
            Spacer()

            VStack(spacing: 8) {
                Text("USD")
                    .font(.ppMeta)
                    .foregroundStyle(Color.ppTextMuted)
                HStack(alignment: .firstTextBaseline, spacing: 2) {
                    Text("$")
                        .font(.custom("PayPalSansBig-Bold", size: 40))
                        .foregroundStyle(Color.ppTextMuted)
                    Text(amount)
                        .font(.ppSendAmountHero)
                        .foregroundStyle(Color.ppTextPrimary)
                        .monospacedDigit()
                }
            }

            Spacer()

            // Numeric keypad (use NumericKeypad from the Cash App example or iOS-native via TextField)

            PayPalPrimaryButton(label: sendLabel) { /* send */ }
                .padding(.horizontal, 16)
                .padding(.bottom, 16)
        }
        .background(Color.ppCanvas)
    }
}
```

### P-P Wordmark (dual-blue logomark)

```swift
struct PayPalWordmark: View {
    var size: CGFloat = 32

    var body: some View {
        ZStack {
            Text("P")
                .font(.custom("PayPalSansBig-Bold", size: size))
                .italic()
                .foregroundStyle(Color.payPalSky)
                .offset(x: -size * 0.18)
            Text("P")
                .font(.custom("PayPalSansBig-Bold", size: size))
                .italic()
                .foregroundStyle(Color.payPalBlue)
                .offset(x: size * 0.10)
        }
        .frame(width: size * 1.4, height: size * 1.2)
    }
}
```

### Activity Filter Chip

```swift
struct ActivityFilterChip: View {
    let label: String
    let isSelected: Bool
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.ppChip)
                .foregroundStyle(isSelected ? .white : Color.ppTextMuted)
                .padding(.horizontal, 16)
                .padding(.vertical, 8)
                .background(
                    Capsule().fill(isSelected ? Color.payPalBlue : Color.ppSurfaceGray2)
                )
        }
    }
}
```

### Status Pill

```swift
struct StatusPill: View {
    enum Status { case completed, pending, failed, refunded }
    let status: Status

    private var bg: Color {
        switch status {
        case .completed: return .ppSuccessBg
        case .pending:   return .ppWarningBg
        case .failed:    return .ppErrorBg
        case .refunded:  return .ppSurfaceGray2
        }
    }
    private var fg: Color {
        switch status {
        case .completed: return .ppSuccess
        case .pending:   return Color(red: 0.628, green: 0.420, blue: 0.0) // #A06B00
        case .failed:    return .ppError
        case .refunded:  return .ppTextMuted
        }
    }
    private var label: String {
        switch status {
        case .completed: return "Completed"
        case .pending:   return "Pending"
        case .failed:    return "Failed"
        case .refunded:  return "Refunded"
        }
    }

    var body: some View {
        Text(label)
            .font(.system(size: 12, weight: .medium))
            .foregroundStyle(fg)
            .padding(.horizontal, 10)
            .padding(.vertical, 4)
            .background(RoundedRectangle(cornerRadius: 4).fill(bg))
    }
}
```

## 4. Tab Bar

```swift
struct PayPalRootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(red: 0.898, green: 0.910, blue: 0.929, alpha: 1)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeView()     .tabItem { Label("Home",     systemImage: "house") }
            SendView()     .tabItem { Label("Send",     systemImage: "paperplane") }
            WalletView()   .tabItem { Label("Wallet",   systemImage: "wallet.pass") }
            ActivityView() .tabItem { Label("Activity", systemImage: "clock") }
            FinancesView() .tabItem { Label("Finances", systemImage: "chart.line.uptrend.xyaxis") }
        }
        .tint(Color.payPalBlue)
    }
}
```

## 5. Motion & Haptics

```swift
// Send button tap
.sensoryFeedback(.impact(weight: .medium), trigger: sendPressed)

// Send success
.sensoryFeedback(.success, trigger: sendConfirmed)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// Filter chip tap
.sensoryFeedback(.selection, trigger: activeFilter)

// Balance update after send
.contentTransition(.numericText())
.animation(.snappy(duration: 0.4), value: balance)

// Success checkmark scale-in
.scaleEffect(showCheck ? 1.0 : 0.5)
.opacity(showCheck ? 1 : 0)
.animation(.spring(response: 0.35, dampingFraction: 0.7).delay(0.1), value: showCheck)

// Modal sheet drag-to-dismiss — use .presentationDetents([.medium, .large]) with .presentationDragIndicator(.visible)
```

## 6. SF Symbols (PayPal's icon equivalents)

| Component | Symbol | Size |
|-----------|--------|------|
| Home tab | `house` / `house.fill` | 24pt |
| Send tab | `paperplane` / `paperplane.fill` | 24pt |
| Wallet tab | `wallet.pass` / `wallet.pass.fill` | 24pt |
| Activity tab | `clock` / `clock.fill` | 24pt |
| Finances tab | `chart.line.uptrend.xyaxis` | 24pt |
| Activity icon: Sent | `arrow.up.right` (inside colored circle) | 18pt |
| Activity icon: Received | `arrow.down.left` (inside colored circle) | 18pt |
| Activity icon: Card | `creditcard.fill` (inside colored circle) | 18pt |
| Activity icon: Reward | `gift.fill` (inside colored circle) | 18pt |
| Notifications | `bell` / `bell.fill` | 20pt |
| Help | `questionmark.circle` | 20pt |
| Search | `magnifyingglass` | 16pt |
| Back | `chevron.left` | 18pt |
| Success | `checkmark.circle.fill` | 32-80pt |
| Failed | `xmark.circle.fill` | 24pt |
| Pending | `clock.fill` | 16pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`, `.contentTransition(.numericText())`); on iOS 16 use `UIImpactFeedbackGenerator` and discrete font updates
- Tabular numerals: apply `.monospacedDigit()` to every `Text` rendering a balance, transaction amount, or percentage so the Activity feed aligns
- VoiceOver: Activity row label should be `"Sent to Sarah Kim, $24.50, yesterday at 3:42 PM, completed"`; balance card: `"PayPal balance, $1,247 and 92 cents"`
- Contrast: `#001435` on `#FFFFFF` exceeds WCAG AA at all sizes; `#687173` on white meets AA at 14pt+; PayPal Blue `#003087` on white meets AAA at all sizes
- Color-blind users: the activity-row icon shape carries directional meaning (arrow-up-right for sent, arrow-down-left for received) — never rely on color alone
- Reduce Motion: skip the modal sheet bounce and the checkmark scale-in animation; use crossfade instead
- Dark mode: switch to `#0A0E1A` canvas (slight blue tint, NOT pure black), `#3B82F6` PayPal Blue for the brighter dark-mode variant; the warmth of the brand is preserved
- Dynamic Type: balance hero scales to 48pt max; send amount scales to 72pt; tab labels and filter chips are fixed
- Floating label input: use SwiftUI's `TextField` with a custom overlay; the lifted-label transition should be 200ms duration
