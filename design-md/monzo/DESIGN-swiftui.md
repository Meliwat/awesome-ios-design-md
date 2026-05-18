# Monzo (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Monzo's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let monzoCoral        = Color(red: 1.000, green: 0.204, blue: 0.392) // #FF3464
    static let monzoCoralPressed = Color(red: 0.878, green: 0.157, blue: 0.333) // #E02855
    static let monzoNavy         = Color(red: 0.078, green: 0.137, blue: 0.235) // #14233C
    static let monzoNavyDeep     = Color(red: 0.055, green: 0.086, blue: 0.125) // #0E1620

    // MARK: - Canvas & Surfaces (Light)
    static let monzoCanvas         = Color(red: 0.965, green: 0.969, blue: 0.976) // #F6F7F9
    static let monzoSurface        = Color.white                                  // #FFFFFF
    static let monzoSurfacePressed = Color(red: 0.933, green: 0.941, blue: 0.953) // #EEF0F3
    static let monzoDivider        = Color(red: 0.894, green: 0.906, blue: 0.922) // #E4E7EB

    // MARK: - Canvas & Surfaces (Dark)
    static let monzoDarkCanvas   = Color(red: 0.055, green: 0.086, blue: 0.125) // #0E1620
    static let monzoDarkSurface1 = Color(red: 0.078, green: 0.137, blue: 0.235) // #14233C
    static let monzoDarkSurface2 = Color(red: 0.106, green: 0.184, blue: 0.302) // #1B2F4D
    static let monzoDarkDivider  = Color(red: 0.141, green: 0.227, blue: 0.341) // #243A57

    // MARK: - Text
    static let monzoTextPrimary     = Color(red: 0.078, green: 0.137, blue: 0.235) // #14233C
    static let monzoTextSecondary   = Color(red: 0.361, green: 0.420, blue: 0.510) // #5C6B82
    static let monzoTextTertiary    = Color(red: 0.580, green: 0.631, blue: 0.710) // #94A1B5
    static let monzoDarkTextPrimary = Color(red: 0.957, green: 0.965, blue: 0.976) // #F4F6F9
    static let monzoDarkTextSecondary = Color(red: 0.624, green: 0.690, blue: 0.776) // #9FB0C6

    // MARK: - Pot / Category Accents (dark-vivid; use *Light for light mode)
    static let monzoMint   = Color(red: 0.361, green: 0.878, blue: 0.769) // #5CE0C4
    static let monzoSky    = Color(red: 0.353, green: 0.663, blue: 0.941) // #5AA9F0
    static let monzoGold   = Color(red: 1.000, green: 0.780, blue: 0.373) // #FFC75F
    static let monzoViolet = Color(red: 0.663, green: 0.541, blue: 0.902) // #A98AE6
    static let monzoTeal   = Color(red: 0.275, green: 0.780, blue: 0.718) // #46C7B7

    // MARK: - Semantic
    static let monzoIncome  = Color(red: 0.184, green: 0.796, blue: 0.561) // #2FCB8F (dark)
    static let monzoError   = Color(red: 1.000, green: 0.353, blue: 0.431) // #FF5A6E (dark)
    static let monzoWarning = Color(red: 1.000, green: 0.702, blue: 0.278) // #FFB347 (dark)
}
```

## 2. Typography

Bundle Inter via `Info.plist` / `UIAppFonts`. Enable tabular figures on every money value.

```swift
extension Font {
    static let monzoScreenTitle = Font.custom("Inter-ExtraBold", size: 32) // w800
    static let monzoBalance     = Font.custom("Inter-ExtraBold", size: 30)
    static let monzoSection     = Font.custom("Inter-Bold",      size: 22)
    static let monzoSubsection  = Font.custom("Inter-Bold",      size: 18)
    static let monzoBody        = Font.custom("Inter-Regular",   size: 16)
    static let monzoMerchant    = Font.custom("Inter-SemiBold",  size: 15)
    static let monzoAmount      = Font.custom("Inter-Bold",      size: 15)
    static let monzoMeta        = Font.custom("Inter-Medium",    size: 13)
    static let monzoLabel       = Font.custom("Inter-Medium",    size: 12)
    static let monzoButton      = Font.custom("Inter-Bold",      size: 16)
    static let monzoTab         = Font.custom("Inter-Medium",    size: 10)
    static let monzoCardNumber  = Font.custom("Inter-SemiBold",  size: 13)
}

// Tabular figures modifier — apply to all money text
struct MonzoTabular: ViewModifier {
    func body(content: Content) -> some View {
        content.monospacedDigit() // or .font(...).fontFeature("tnum")
    }
}
extension View { func monzoTabular() -> some View { modifier(MonzoTabular()) } }
```

## 3. Signature Components

### Hot Coral Card Hero

```swift
struct MonzoCardHero: View {
    let balance: String          // "£2,418.60"
    let maskedNumber: String     // "•••• •••• •••• 9042"
    @State private var flipped = false

    var body: some View {
        ZStack(alignment: .topTrailing) {
            LinearGradient(
                colors: [Color(hex: 0xFF3464), Color(hex: 0xFF5A7F), Color(hex: 0xE02855)],
                startPoint: .topLeading, endPoint: .bottomTrailing
            )
            VStack(alignment: .leading, spacing: 4) {
                Spacer()
                Text(balance)
                    .font(.monzoBalance).monzoTabular()
                    .foregroundStyle(.white)
                Text("Available to spend")
                    .font(.monzoLabel)
                    .foregroundStyle(.white.opacity(0.85))
                Spacer()
                Text(maskedNumber)
                    .font(.monzoCardNumber)
                    .tracking(2)
                    .foregroundStyle(.white.opacity(0.9))
            }
            .padding(20)
            .frame(maxWidth: .infinity, alignment: .leading)

            Text("MONZO")
                .font(.system(size: 11, weight: .bold))
                .tracking(1)
                .foregroundStyle(.white.opacity(0.9))
                .padding(20)
        }
        .frame(height: 132)
        .clipShape(RoundedRectangle(cornerRadius: 18))
        .shadow(color: Color(hex: 0xFF3464).opacity(0.4), radius: 30, x: 0, y: 14)
        .rotation3DEffect(.degrees(flipped ? 180 : 0), axis: (x: 0, y: 1, z: 0))
        .padding(.horizontal, 20)
        .onTapGesture {
            withAnimation(.easeInOut(duration: 0.35)) { flipped.toggle() }
        }
    }
}

extension Color {
    init(hex: UInt32) {
        self.init(.sRGB,
                  red:   Double((hex >> 16) & 0xFF) / 255,
                  green: Double((hex >> 8) & 0xFF) / 255,
                  blue:  Double(hex & 0xFF) / 255)
    }
}
```

### Transaction Row

```swift
struct TransactionRow: View {
    let emoji: String
    let tint: Color
    let merchant: String
    let category: String
    let time: String
    let amount: String
    let isIncome: Bool
    let isPending: Bool

    var body: some View {
        HStack(spacing: 14) {
            Text(emoji)
                .font(.system(size: 20))
                .frame(width: 42, height: 42)
                .background(Circle().fill(tint.opacity(0.18)))

            VStack(alignment: .leading, spacing: 2) {
                Text(merchant)
                    .font(.monzoMerchant)
                    .foregroundStyle(Color.monzoDarkTextPrimary)
                Text("\(category) · \(time)")
                    .font(.monzoMeta)
                    .foregroundStyle(Color.monzoDarkTextSecondary)
            }
            Spacer()
            Text(amount)
                .font(.monzoAmount).monzoTabular()
                .foregroundStyle(isIncome ? Color.monzoIncome : Color.monzoDarkTextPrimary)
        }
        .padding(.horizontal, 20)
        .padding(.vertical, 10)
        .opacity(isPending ? 0.6 : 1.0)
        .contentShape(Rectangle())
    }
}
```

### Pot Coin

```swift
struct PotCoin: View {
    let emoji: String
    let name: String
    let amount: String
    let gradient: [Color]

    var body: some View {
        VStack(spacing: 8) {
            Text(emoji)
                .font(.system(size: 30))
                .frame(width: 92, height: 92)
                .background(
                    RoundedRectangle(cornerRadius: 16)
                        .fill(LinearGradient(colors: gradient,
                                             startPoint: .topLeading,
                                             endPoint: .bottomTrailing))
                )
                .shadow(color: .black.opacity(0.2), radius: 16, x: 0, y: 8)
            Text(name).font(.monzoLabel).foregroundStyle(Color.monzoDarkTextPrimary)
            Text(amount).font(.system(size: 11, weight: .medium))
                .foregroundStyle(Color.monzoDarkTextSecondary)
        }
        .frame(width: 92)
    }
}
```

### Pot Card (detail / list)

```swift
struct PotCard: View {
    let emoji: String
    let name: String
    let saved: String       // "£640"
    let goal: String        // "£1,000"
    let progress: Double    // 0...1

    var body: some View {
        HStack(spacing: 14) {
            Text(emoji)
                .font(.system(size: 22))
                .frame(width: 48, height: 48)
                .background(RoundedRectangle(cornerRadius: 12)
                    .fill(LinearGradient(colors: [.monzoMint, Color(hex: 0x2FB59A)],
                                         startPoint: .topLeading, endPoint: .bottomTrailing)))

            VStack(alignment: .leading, spacing: 4) {
                Text(name).font(.monzoMerchant).foregroundStyle(Color.monzoDarkTextPrimary)
                Text("\(saved) of \(goal) goal")
                    .font(.monzoLabel).foregroundStyle(Color.monzoDarkTextSecondary)
                GeometryReader { geo in
                    ZStack(alignment: .leading) {
                        Capsule().fill(Color.monzoDarkDivider).frame(height: 6)
                        Capsule().fill(Color.monzoMint)
                            .frame(width: geo.size.width * progress, height: 6)
                    }
                }
                .frame(height: 6)
            }
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.monzoDarkSurface2))
    }
}
```

### Primary / Secondary / Pill Buttons

```swift
struct MonzoPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.monzoButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 15)
        }
        .background(RoundedRectangle(cornerRadius: 14).fill(Color.monzoCoral))
        .buttonStyle(.plain)
    }
}

struct MonzoPillButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.custom("Inter-SemiBold", size: 14))
                .foregroundStyle(Color.monzoCoral)
                .padding(.vertical, 10).padding(.horizontal, 20)
        }
        .overlay(Capsule().strokeBorder(Color.monzoCoral, lineWidth: 1.5))
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct MonzoTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            PaymentsView().tabItem { Label("Payments", systemImage: "creditcard.fill") }
            PotsView().tabItem { Label("Pots", systemImage: "circle.grid.2x2.fill") }
            TrendsView().tabItem { Label("Trends", systemImage: "chart.line.uptrend.xyaxis") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle.fill") }
        }
        .tint(.monzoCoral)  // active = Hot Coral, no tint pill
    }
}
```

## 5. Motion

```swift
// Card 3D flip to controls
withAnimation(.easeInOut(duration: 0.35)) { flipped.toggle() }
.rotation3DEffect(.degrees(flipped ? 180 : 0), axis: (x: 0, y: 1, z: 0))

// Pot money move — coin springs into pot + balance ticks
withAnimation(.spring(response: 0.4, dampingFraction: 0.7)) { /* move coin */ }
.contentTransition(.numericText())  // balance digit ticker on change

// Feed insert — slide in from top
.transition(.move(edge: .top).combined(with: .opacity))
// new-row highlight wash fades over 0.8s

// Tab change — instant color crossfade (system handles), 120ms feel

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: cardFlipped)
.sensoryFeedback(.impact(flexibility: .soft), trigger: potMoved)
.sensoryFeedback(.success, trigger: paymentCompleted)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Payments (tab) | `creditcard` / `creditcard.fill` | 22pt |
| Pots (tab) | `circle.grid.2x2` / `.fill` | 22pt |
| Trends (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Account (tab) | `person.crop.circle` / `.fill` | 22pt |
| Search | `magnifyingglass` | 16pt |
| Add money | `plus` | 18pt |
| Freeze card | `snowflake` | 20pt |
| Split bill | `person.2` | 20pt |
| Map pin (detail) | `mappin.circle.fill` | 22pt |
| Receipt | `doc.text` | 18pt |
| Notes | `square.and.pencil` | 18pt |
| Back | `chevron.left` | 20pt |
| Settings | `gearshape` | 20pt |
| Income arrow | `arrow.down.left` | 14pt |
| Pending | `clock` | 14pt |

## 7. Dark Mode

```swift
struct MonzoTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.monzoDarkCanvas : Color.monzoCanvas)
            .foregroundStyle(scheme == .dark ? Color.monzoDarkTextPrimary : Color.monzoTextPrimary)
    }
}
extension View { func monzoTheme() -> some View { modifier(MonzoTheme()) } }
```

Dark mode is deep navy `#0E1620`, NOT pure black; the brand navy `#14233C` becomes the card/row surface. Hot Coral `#FF3464` is unchanged. Pot coins and category emoji tints stay fully saturated — only chrome (dividers, secondary text) recedes. Income stays green `#2FCB8F`.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` require iOS 17 — fall back to `UIImpactFeedbackGenerator` and a manual count animation)
- Bundle Inter TTFs via `Info.plist` (`UIAppFonts`); Inter is SIL OFL licensed for free distribution
- Always enable `.monospacedDigit()` (tabular figures) on balances, amounts, and Pot totals so money columns align
- Dynamic Type: support on screen title, balance, section title, body, merchant, meta; keep tab labels, card number, and Pot-coin captions at FIXED sizes (layout-sensitive)
- VoiceOver: read transaction rows as "{merchant}, {category}, {amount}, {time}{, pending}"; group day headers as headings; the card hero announces "Current account, balance {amount}, double-tap to show card details"
- Color contrast: `#14233C` on `#F6F7F9` and `#F4F6F9` on `#0E1620` pass WCAG AA for body text; verify category-tint chips, especially gold, with a contrast checker
- Reduce Motion: disable the card 3D flip (crossfade instead), the Pot coin spring (instant move), and the balance digit ticker (set value directly)
- Reduce Transparency: replace the tab-bar blur with a solid `#FFFFFF` / `#0E1620` fill
- Dark mode: colors invert per the `monzoDark*` palette; deep navy not black; add a 1pt `monzoDarkDivider` border to sheets as an elevation cue where shadows read weakly
