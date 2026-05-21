# Cash App (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Cash App's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the signature $-amount keypad, Cash Card, and Pay flow.

Targets iOS 17+. Cash Sans is proprietary — bundle TTFs via `Info.plist` (`UIAppFonts`) or fall back to SF Pro Text / SF Mono.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let cashCanvas      = Color(red: 0.00, green: 0.00, blue: 0.00) // #000000
    static let cashSurface1    = Color(red: 0.059, green: 0.059, blue: 0.059) // #0F0F0F
    static let cashSurface2    = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let cashDivider     = Color(red: 0.122, green: 0.122, blue: 0.122) // #1F1F1F
    static let cashHairline    = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A

    // MARK: - Text
    static let cashTextPrimary    = Color(red: 1.00, green: 1.00, blue: 1.00)   // #FFFFFF
    static let cashTextSecondary  = Color(red: 0.620, green: 0.620, blue: 0.620) // #9E9E9E
    static let cashTextTertiary   = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E

    // MARK: - Brand
    static let cashGreen         = Color(red: 0.00, green: 0.839, blue: 0.196) // #00D632
    static let cashGreenPressed  = Color(red: 0.00, green: 0.722, blue: 0.161) // #00B829
    static let cashGreenDim      = Color(red: 0.00, green: 0.549, blue: 0.125) // #008C20
    static let bitcoinOrange     = Color(red: 0.969, green: 0.576, blue: 0.102) // #F7931A
    static let boostRed          = Color(red: 1.00, green: 0.271, blue: 0.247) // #FF453A

    // MARK: - Semantic
    static let cashSuccess       = Color(red: 0.00, green: 0.839, blue: 0.196) // #00D632
    static let cashError         = Color(red: 1.00, green: 0.271, blue: 0.247) // #FF453A
    static let cashWarning       = Color(red: 1.00, green: 0.639, blue: 0.00)  // #FFA300

    // Random saturated avatar colors (no-photo fallback)
    static let avatarColors: [Color] = [
        Color(red: 1.00, green: 0.420, blue: 0.616),  // pink   #FF6B9D
        Color(red: 1.00, green: 0.580, blue: 0.235),  // orange #FF953C
        Color(red: 0.380, green: 0.604, blue: 1.00),  // blue   #619AFF
        Color(red: 0.357, green: 0.831, blue: 0.482), // green  #5BD47B
        Color(red: 0.667, green: 0.467, blue: 1.00),  // purple #AA77FF
        Color(red: 1.00, green: 0.792, blue: 0.298),  // yellow #FFCA4C
    ]
}
```

## 2. Typography

Cash Sans and Cash Sans Mono are proprietary. Bundle the TTFs via `Info.plist` or register at runtime. Fall back to SF Pro Text (sans) and SF Mono (mono) — both are warm system substitutes.

```swift
extension Font {
    // Hero amounts (Cash Sans Mono — tabular numerals)
    static let cashAmountHero    = Font.custom("CashSansMono-Bold",   size: 96)
    static let cashAmountReceive = Font.custom("CashSansMono-Bold",   size: 64)
    static let cashBalance       = Font.custom("CashSansMono-Bold",   size: 48)
    static let cashCashtagLarge  = Font.custom("CashSansMono-Medium", size: 22)
    static let cashAmountRow     = Font.custom("CashSansMono-Medium", size: 17)
    static let cashCashtagInline = Font.custom("CashSansMono-Medium", size: 15)
    static let cashKeypadDigit   = Font.custom("CashSansMono-Regular", size: 32)

    // Sans titles & body
    static let cashScreenTitle   = Font.custom("CashSans-Black",   size: 28)
    static let cashSectionHeader = Font.custom("CashSans-Bold",    size: 22)
    static let cashCardLabel     = Font.custom("CashSans-Medium",  size: 16)
    static let cashBody          = Font.custom("CashSans-Regular", size: 15)
    static let cashMeta          = Font.custom("CashSans-Regular", size: 13)

    // Buttons & micro
    static let cashButton        = Font.custom("CashSans-Bold",    size: 17)
    static let cashButtonSmall   = Font.custom("CashSans-Medium",  size: 15)
    static let cashAllCaps       = Font.custom("CashSans-Bold",    size: 11) // letter-spacing 1.5pt
    static let cashAvatarInit    = Font.custom("CashSans-Bold",    size: 22)
}

// Fallback if Cash Sans isn't bundled — SF Pro / SF Mono
extension Font {
    static func cashSans(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
    static func cashMono(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .monospaced)
    }
}
```

## 3. Signature Components

### The $-Amount Keypad (Pay screen — the hero)

```swift
struct AmountKeypad: View {
    @Binding var amount: String   // raw string, e.g. "12.50"
    var onPay: () -> Void
    var onRequest: () -> Void

    private var displayAmount: String {
        amount.isEmpty ? "0" : amount
    }

    // Auto-shrink as more digits appear
    private var amountFontSize: CGFloat {
        switch displayAmount.count {
        case 0...3: return 96
        case 4:     return 80
        case 5:     return 64
        default:    return 52
        }
    }

    var body: some View {
        VStack(spacing: 0) {
            // Top: giant amount
            HStack(alignment: .center, spacing: 4) {
                Text("$")
                    .font(.custom("CashSansMono-Bold", size: amountFontSize * 0.67))
                    .foregroundStyle(Color.cashTextSecondary)
                Text(displayAmount)
                    .font(.custom("CashSansMono-Bold", size: amountFontSize))
                    .foregroundStyle(Color.cashTextPrimary)
                    .shadow(color: amount.isEmpty ? .clear : .cashGreen.opacity(0.5), radius: 8)
                    .contentTransition(.numericText())
                    .animation(.spring(response: 0.3, dampingFraction: 0.7), value: amount)
            }
            .frame(maxWidth: .infinity)
            .padding(.top, 64)

            Spacer()

            // Bottom: keypad
            VStack(spacing: 4) {
                HStack(spacing: 4) {
                    KeypadButton("1") { append("1") }
                    KeypadButton("2") { append("2") }
                    KeypadButton("3") { append("3") }
                }
                HStack(spacing: 4) {
                    KeypadButton("4") { append("4") }
                    KeypadButton("5") { append("5") }
                    KeypadButton("6") { append("6") }
                }
                HStack(spacing: 4) {
                    KeypadButton("7") { append("7") }
                    KeypadButton("8") { append("8") }
                    KeypadButton("9") { append("9") }
                }
                HStack(spacing: 4) {
                    KeypadButton(".") { append(".") }
                    KeypadButton("0") { append("0") }
                    KeypadButton(systemImage: "delete.left") { backspace() }
                }
            }
            .padding(.horizontal, 24)

            // Pay / Request buttons
            HStack(spacing: 12) {
                Button(action: onRequest) {
                    Text("Request")
                        .font(.cashButton)
                        .foregroundStyle(Color.cashTextPrimary)
                        .frame(maxWidth: .infinity)
                        .frame(height: 60)
                        .background(RoundedRectangle(cornerRadius: 30).fill(Color.cashSurface2))
                }
                Button(action: onPay) {
                    Text("Pay")
                        .font(.cashButton)
                        .foregroundStyle(.black)
                        .frame(maxWidth: .infinity)
                        .frame(height: 60)
                        .background(
                            RoundedRectangle(cornerRadius: 30)
                                .fill(amount.isEmpty ? Color.cashSurface2 : Color.cashGreen)
                        )
                }
                .disabled(amount.isEmpty)
                .sensoryFeedback(.success, trigger: amount)
            }
            .padding(.horizontal, 24)
            .padding(.top, 16)
            .padding(.bottom, 24)
        }
        .background(Color.cashCanvas)
    }

    private func append(_ s: String) {
        amount += s
    }
    private func backspace() {
        if !amount.isEmpty { amount.removeLast() }
    }
}

struct KeypadButton: View {
    let label: String?
    let systemImage: String?
    let action: () -> Void
    @State private var pressed = false

    init(_ label: String, action: @escaping () -> Void) {
        self.label = label; self.systemImage = nil; self.action = action
    }
    init(systemImage: String, action: @escaping () -> Void) {
        self.label = nil; self.systemImage = systemImage; self.action = action
    }

    var body: some View {
        Button(action: action) {
            Group {
                if let label { Text(label).font(.cashKeypadDigit) }
                else if let systemImage { Image(systemName: systemImage).font(.system(size: 28, weight: .regular)) }
            }
            .foregroundStyle(Color.cashTextPrimary)
            .frame(maxWidth: .infinity)
            .frame(height: 64)
            .background(
                RoundedRectangle(cornerRadius: 12)
                    .fill(pressed ? Color.cashSurface2 : Color.clear)
            )
            .scaleEffect(pressed ? 0.96 : 1)
        }
        .sensoryFeedback(.impact(weight: .light), trigger: pressed)
        ._onButtonGesture(pressing: { pressed = $0 }, perform: {})
    }
}
```

### Cash Card Render

```swift
struct CashCardView: View {
    let cashtag: String       // "$johndoe"
    let accentColor: Color    // user-chosen color (default .cashGreen)

    var body: some View {
        ZStack(alignment: .bottom) {
            RoundedRectangle(cornerRadius: 12)
                .fill(Color.cashCanvas)
                .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.cashDivider, lineWidth: 1))
                .aspectRatio(1.586, contentMode: .fit)

            HStack(alignment: .bottom) {
                Text(cashtag)
                    .font(.custom("CashSansMono-Bold", size: 24))
                    .foregroundStyle(accentColor)
                Spacer()
                Text("$")
                    .font(.custom("CashSans-Black", size: 32))
                    .foregroundStyle(accentColor)
            }
            .padding(24)
        }
    }
}
```

### Activity Row

```swift
struct ActivityRow: View {
    let name: String
    let direction: Direction          // .sent or .received
    let subtitle: String              // "Sent to John" / "From Sarah"
    let amount: String                // "20.00"
    let avatarColor: Color = Color.avatarColors.randomElement() ?? .cashGreen

    enum Direction { case sent, received }

    private var amountString: String {
        (direction == .received ? "+" : "-") + "$" + amount
    }
    private var amountColor: Color {
        direction == .received ? .cashGreen : .cashTextPrimary
    }

    var body: some View {
        HStack(spacing: 12) {
            // Avatar
            Circle()
                .fill(avatarColor)
                .frame(width: 40, height: 40)
                .overlay(
                    Text(String(name.prefix(1)))
                        .font(.cashAvatarInit)
                        .foregroundStyle(.white)
                )

            // Name + subtitle
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.cashCardLabel).foregroundStyle(Color.cashTextPrimary)
                Text(subtitle).font(.cashMeta).foregroundStyle(Color.cashTextSecondary)
            }

            Spacer()

            // Amount
            Text(amountString)
                .font(.cashAmountRow)
                .foregroundStyle(amountColor)
        }
        .padding(.horizontal, 24)
        .frame(height: 64)
        .background(Color.cashCanvas)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.cashDivider).frame(height: 0.5)
        }
    }
}
```

### Balance Tile (Money tab top)

```swift
struct BalanceTile: View {
    let dollars: Int      // 1247
    let cents: Int        // 92

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("CASH BALANCE")
                .font(.cashAllCaps)
                .foregroundStyle(Color.cashTextSecondary)
                .tracking(1.5)

            HStack(alignment: .firstTextBaseline, spacing: 0) {
                Text("$\(dollars.formatted())")
                    .font(.cashBalance)
                    .foregroundStyle(Color.cashTextPrimary)
                Text(String(format: ".%02d", cents))
                    .font(.custom("CashSansMono-Bold", size: 24))
                    .foregroundStyle(Color.cashTextPrimary)
            }

            HStack(spacing: 8) {
                PillButton(label: "Add Cash") {}
                PillButton(label: "Cash Out") {}
            }
            .padding(.top, 12)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(.horizontal, 24)
        .padding(.vertical, 24)
        .background(Color.cashCanvas)
    }
}

struct PillButton: View {
    let label: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.cashButtonSmall)
                .foregroundStyle(Color.cashTextPrimary)
                .padding(.horizontal, 18).padding(.vertical, 10)
                .background(Capsule().fill(Color.cashSurface2))
        }
    }
}
```

### Screen Title (used at top of Money / Card / Activity)

```swift
struct CashScreenTitle: View {
    let title: String     // "Money", "Card", "Activity"
    let avatarUrl: URL?

    var body: some View {
        HStack {
            Text(title)
                .font(.cashScreenTitle)
                .foregroundStyle(Color.cashTextPrimary)
            Spacer()
            Circle()
                .fill(Color.cashSurface2)
                .frame(width: 36, height: 36)
                .overlay(Image(systemName: "person.fill").foregroundStyle(Color.cashTextPrimary))
        }
        .padding(.horizontal, 24)
        .padding(.top, 16)
    }
}
```

## 4. Tab Bar

```swift
struct CashRootTabView: View {
    @State private var selectedTab = 2  // Pay default

    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.black
        appearance.shadowColor = UIColor(red: 0.122, green: 0.122, blue: 0.122, alpha: 1)
        // NO TITLE TEXT — clear the label color
        let itemAppearance = UITabBarItemAppearance()
        itemAppearance.normal.titleTextAttributes = [.foregroundColor: UIColor.clear]
        itemAppearance.selected.titleTextAttributes = [.foregroundColor: UIColor.clear]
        appearance.stackedLayoutAppearance = itemAppearance
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView(selection: $selectedTab) {
            MoneyView()    .tabItem { Image(systemName: "dollarsign.circle") }.tag(0)
            CardView()     .tabItem { Image(systemName: "creditcard") }.tag(1)
            PayView()      .tabItem { Image(systemName: "dollarsign") }.tag(2)
            ActivityView() .tabItem { Image(systemName: "clock") }.tag(3)
            BitcoinView()  .tabItem { Image(systemName: "bitcoinsign.circle") }.tag(4)
        }
        .tint(Color.cashGreen)
    }
}
```

## 5. Motion & Haptics

```swift
// Keypad digit tap
.sensoryFeedback(.impact(weight: .light), trigger: amount)

// Pay success
.sensoryFeedback(.success, trigger: payConfirmed)

// Cash Card flip
withAnimation(.spring(response: 0.6, dampingFraction: 0.65)) { rotation = 180 }

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// Amount text shrink on digit count change (numericText transition)
.contentTransition(.numericText())
.animation(.spring(response: 0.3, dampingFraction: 0.7), value: amount)
```

## 6. SF Symbols (Cash App's icon equivalents)

| Component | Symbol | Size |
|-----------|--------|------|
| Money tab | `dollarsign.circle` / `.fill` | 28pt |
| Card tab | `creditcard` / `.fill` | 28pt |
| Pay tab (center, larger) | `dollarsign` | 32pt |
| Activity tab | `clock` / `.fill` | 28pt |
| Bitcoin tab | `bitcoinsign.circle` / `.fill` | 28pt |
| Backspace (keypad) | `delete.left` | 28pt |
| Search | `magnifyingglass` | 20pt |
| Boost (Cash Card) | use commissioned custom art, NOT SF Symbols |
| Avatar fallback | `person.fill` | 22pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback`, `.contentTransition(.numericText())`); fall back to `UIImpactFeedbackGenerator` and discrete font-size states on iOS 16
- VoiceOver: announce the keypad amount as "Sending $12 and 50 cents to John Doe"; group the keypad as a number input with `accessibilityLabel("Amount entry")`; Pay button label should reflect amount ("Pay $12.50")
- Contrast: white on `#000000` exceeds WCAG AA at all sizes; `#9E9E9E` on black meets AA at 14pt+ — avoid `#6E6E6E` for body text
- Reduce Motion: disable the amount-glow effect and the digit-shrink animation when `accessibilityReduceMotion` is true — fall back to a static font size that matches the longest expected digit count
- Dynamic Type: $-amounts on the Pay screen do NOT scale (layout is critical); section headers, body, and activity rows scale normally
- Tab labels are hidden — make sure VoiceOver receives meaningful `accessibilityLabel`s on each tab icon ("Money tab", "Pay tab", etc.) since there's no visible text
- Light mode: optional, primarily for accessibility — swap canvas to `#FFFFFF`, text primary to `#000000`, Cash Green to `#00B829` for AA on white; Cash Sans Mono and tabular numerals stay identical
