# Nubank (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Nubank's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let nuPurple        = Color(red: 0.510, green: 0.039, blue: 0.820) // #820AD1
    static let nuPurpleBright  = Color(red: 0.608, green: 0.169, blue: 0.878) // #9B2BE0
    static let nuPurplePressed = Color(red: 0.416, green: 0.027, blue: 0.678) // #6A07AD
    static let nuPurpleSoft    = Color(red: 0.788, green: 0.651, blue: 0.910) // #C9A6E8

    // MARK: - Canvas & Surfaces (Light)
    static let nuCanvas         = Color(red: 0.957, green: 0.949, blue: 0.969) // #F4F2F7
    static let nuSurface        = Color.white                                  // #FFFFFF
    static let nuSurfacePressed = Color(red: 0.925, green: 0.910, blue: 0.949) // #ECE8F2
    static let nuDivider        = Color(red: 0.902, green: 0.882, blue: 0.933) // #E6E1EE

    // MARK: - Canvas & Surfaces (Dark)
    static let nuDarkCanvas   = Color(red: 0.082, green: 0.063, blue: 0.110) // #15101C
    static let nuDarkSurface1 = Color(red: 0.122, green: 0.090, blue: 0.161) // #1F1729
    static let nuDarkSurface2 = Color(red: 0.165, green: 0.125, blue: 0.220) // #2A2038
    static let nuDarkDivider  = Color(red: 0.212, green: 0.169, blue: 0.278) // #362B47

    // MARK: - Text
    static let nuTextPrimary       = Color(red: 0.102, green: 0.082, blue: 0.137) // #1A1523
    static let nuTextSecondary     = Color(red: 0.420, green: 0.380, blue: 0.471) // #6B6178
    static let nuTextTertiary      = Color(red: 0.612, green: 0.576, blue: 0.671) // #9C93AB
    static let nuDarkTextPrimary   = Color(red: 0.953, green: 0.933, blue: 0.973) // #F3EEF8
    static let nuDarkTextSecondary = Color(red: 0.714, green: 0.659, blue: 0.784) // #B6A8C8
    static let nuDarkTextTertiary  = Color(red: 0.478, green: 0.420, blue: 0.557) // #7A6B8E

    // MARK: - Product Accents (dark-vivid; use *Light for light mode)
    static let nuGreen = Color(red: 0.180, green: 0.831, blue: 0.478) // #2ED47A
    static let nuGold  = Color(red: 0.961, green: 0.761, blue: 0.294) // #F5C24B
    static let nuPink  = Color(red: 0.910, green: 0.373, blue: 0.690) // #E85FB0
    static let nuInfo  = Color(red: 0.353, green: 0.584, blue: 0.941) // #5A95F0

    // MARK: - Semantic
    static let nuPositive = Color(red: 0.180, green: 0.831, blue: 0.478) // #2ED47A
    static let nuError    = Color(red: 1.000, green: 0.361, blue: 0.424) // #FF5C6C
    static let nuWarning  = Color(red: 1.000, green: 0.702, blue: 0.278) // #FFB347
}
```

## 2. Typography

Nubank ships Graphik / Nu Sans; bundle **Inter** (SIL OFL) as the free substitute via `Info.plist` / `UIAppFonts`. Enable tabular figures on every currency value.

```swift
extension Font {
    static let nuScreenTitle = Font.custom("Inter-ExtraBold", size: 32) // w800
    static let nuBalance     = Font.custom("Inter-ExtraBold", size: 26)
    static let nuGreeting    = Font.custom("Inter-Bold",      size: 22)
    static let nuSubsection  = Font.custom("Inter-Bold",      size: 18)
    static let nuBody        = Font.custom("Inter-Regular",   size: 16)
    static let nuRowTitle    = Font.custom("Inter-SemiBold",  size: 15)
    static let nuAmount      = Font.custom("Inter-Bold",      size: 15)
    static let nuMeta        = Font.custom("Inter-Medium",    size: 13)
    static let nuLabel       = Font.custom("Inter-Medium",    size: 12)
    static let nuButton      = Font.custom("Inter-Bold",      size: 16)
    static let nuTab         = Font.custom("Inter-Medium",    size: 10)
    static let nuQuickAction = Font.custom("Inter-Medium",    size: 11)
}

// Tabular figures — apply to all money text
struct NuTabular: ViewModifier {
    func body(content: Content) -> some View { content.monospacedDigit() }
}
extension View { func nuTabular() -> some View { modifier(NuTabular()) } }
```

## 3. Signature Components

### Purple Hero Header

```swift
struct NuHeroHeader: View {
    let initials: String
    let name: String

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text(initials)
                    .font(.system(size: 15, weight: .bold))
                    .foregroundStyle(.white)
                    .frame(width: 38, height: 38)
                    .background(Circle().fill(.white.opacity(0.18)))
                Spacer()
                HStack(spacing: 18) {
                    Image(systemName: "gearshape")
                    Image(systemName: "bell")
                    Image(systemName: "questionmark.circle")
                }
                .font(.system(size: 21, weight: .semibold))
                .foregroundStyle(.white)
            }
            .padding(.horizontal, 22)
            .padding(.bottom, 16)

            Text("Olá,")
                .font(.nuLabel)
                .foregroundStyle(.white.opacity(0.85))
                .padding(.horizontal, 22)
            Text(name)
                .font(.nuGreeting)
                .foregroundStyle(.white)
                .padding(.horizontal, 22)
        }
        .padding(.top, 8)
        .padding(.bottom, 22)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(
            LinearGradient(colors: [Color(hex: 0x820AD1), Color(hex: 0x9B2BE0), Color(hex: 0x6A07AD)],
                           startPoint: .topLeading, endPoint: .bottomTrailing)
                .ignoresSafeArea(edges: .top)
        )
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

### NuConta Balance Tile

```swift
struct NuAccountTile: View {
    let balance: String          // "R$ 4.812,35"
    @State private var hidden = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 12) {
                Image(systemName: "brazilianrealsign.circle")
                    .font(.system(size: 28, weight: .light))
                    .foregroundStyle(Color.nuPurpleSoft)
                Text("Conta").font(.nuRowTitle).foregroundStyle(Color.nuDarkTextPrimary)
                Spacer()
                Button { hidden.toggle() } label: {
                    Image(systemName: hidden ? "eye.slash" : "eye")
                        .foregroundStyle(Color.nuDarkTextSecondary)
                }
            }
            Text(hidden ? "R$ ••••••" : balance)
                .font(.nuBalance).nuTabular()
                .foregroundStyle(Color.nuDarkTextPrimary)
                .padding(.top, 14)
            Text("Saldo disponível")
                .font(.nuMeta)
                .foregroundStyle(Color.nuDarkTextSecondary)
                .padding(.top, 2)
        }
        .padding(18)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.nuDarkSurface1))
        .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.nuDarkDivider, lineWidth: 1))
        .padding(.horizontal, 18)
    }
}
```

### Credit-Card (Roxinho) Tile

```swift
struct NuCreditTile: View {
    let invoice: String          // "R$ 1.248,90"
    let dueText: String          // "Fatura atual · vence 10 jun"
    let limitText: String        // "Limite disponível R$ 6.751,10"
    let used: Double             // 0...1

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 12) {
                RoundedRectangle(cornerRadius: 5)
                    .fill(LinearGradient(colors: [Color(hex: 0x820AD1), Color(hex: 0x9B2BE0)],
                                         startPoint: .topLeading, endPoint: .bottomTrailing))
                    .frame(width: 40, height: 26)
                Text("Cartão de crédito").font(.nuRowTitle).foregroundStyle(Color.nuDarkTextPrimary)
            }
            Text(invoice).font(.nuSubsection).fontWeight(.heavy).nuTabular()
                .foregroundStyle(Color.nuDarkTextPrimary).padding(.top, 14)
            Text(dueText).font(.nuMeta).foregroundStyle(Color.nuDarkTextSecondary).padding(.top, 2)
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.nuDarkDivider).frame(height: 6)
                    Capsule().fill(Color.nuPurpleBright).frame(width: geo.size.width * used, height: 6)
                }
            }
            .frame(height: 6).padding(.top, 12)
            Text(limitText).font(.nuMeta).foregroundStyle(Color.nuDarkTextSecondary).padding(.top, 8)
        }
        .padding(18)
        .background(RoundedRectangle(cornerRadius: 16).fill(Color.nuDarkSurface1))
        .padding(.horizontal, 18)
    }
}
```

### Quick-Action Shortcut + Strip

```swift
struct NuQuickAction: View {
    let systemName: String
    let label: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            VStack(spacing: 8) {
                Image(systemName: systemName)
                    .font(.system(size: 22, weight: .semibold))
                    .foregroundStyle(Color.nuPurpleSoft)
                    .frame(width: 52, height: 52)
                    .background(Circle().fill(Color.nuDarkSurface2))
                Text(label)
                    .font(.nuQuickAction)
                    .foregroundStyle(Color.nuDarkTextSecondary)
                    .multilineTextAlignment(.center)
            }
            .frame(width: 72)
        }
        .buttonStyle(.plain)
    }
}
```

### Activity Row

```swift
struct NuActivityRow: View {
    let systemName: String
    let title: String
    let subtitle: String
    let amount: String
    let isIncome: Bool
    let isPending: Bool

    var body: some View {
        HStack(spacing: 14) {
            Image(systemName: systemName)
                .font(.system(size: 19, weight: .semibold))
                .foregroundStyle(Color.nuPurpleSoft)
                .frame(width: 38, height: 38)
                .background(Circle().fill(Color.nuDarkSurface2))
            VStack(alignment: .leading, spacing: 2) {
                Text(title).font(.nuRowTitle).foregroundStyle(Color.nuDarkTextPrimary)
                Text(subtitle).font(.nuMeta).foregroundStyle(Color.nuDarkTextSecondary)
            }
            Spacer()
            Text(amount).font(.nuAmount).nuTabular()
                .foregroundStyle(isIncome ? Color.nuPositive : Color.nuDarkTextPrimary)
        }
        .padding(.horizontal, 22)
        .padding(.vertical, 10)
        .opacity(isPending ? 0.6 : 1.0)
        .contentShape(Rectangle())
    }
}
```

### Pill Buttons

```swift
struct NuPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.nuButton).foregroundStyle(.white)
                .frame(maxWidth: .infinity).padding(.vertical, 15)
        }
        .background(Capsule().fill(Color.nuPurple))
        .buttonStyle(.plain)
    }
}

struct NuSecondaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.custom("Inter-SemiBold", size: 15)).foregroundStyle(Color.nuPurpleSoft)
                .padding(.vertical, 13).padding(.horizontal, 24)
        }
        .overlay(Capsule().strokeBorder(Color.nuPurpleSoft, lineWidth: 1.5))
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct NuTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Início", systemImage: "house.fill") }
            CardsView().tabItem { Label("Cartões", systemImage: "creditcard.fill") }
            InvestView().tabItem { Label("Investir", systemImage: "chart.line.uptrend.xyaxis") }
            SearchView().tabItem { Label("Buscar", systemImage: "magnifyingglass") }
            ProfileView().tabItem { Label("Perfil", systemImage: "person.crop.circle.fill") }
        }
        .tint(.nuPurpleSoft)  // dark active = Purple Soft; use .nuPurple on light. No tint pill.
    }
}
```

## 5. Motion

```swift
// Tile press — subtle scale on tap-down
.scaleEffect(pressed ? 0.98 : 1).animation(.easeOut(duration: 0.08), value: pressed)

// Balance reveal — eye toggle wipe
withAnimation(.easeOut(duration: 0.2)) { hidden.toggle() }

// Pix success — green check stroke draw + count-up
.trim(from: 0, to: drawn).stroke(Color.nuPositive, style: .init(lineWidth: 4, lineCap: .round))
withAnimation(.easeOut(duration: 0.4)) { drawn = 1 }

// Number ticker — balances / invoice
.contentTransition(.numericText())

// Tab change — instant color crossfade (system handles), 120ms feel

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: quickActionTapped)
.sensoryFeedback(.success, trigger: pixCompleted)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Início (tab) | `house` / `house.fill` | 22pt |
| Cartões (tab) | `creditcard` / `creditcard.fill` | 22pt |
| Investir (tab) | `chart.line.uptrend.xyaxis` | 22pt |
| Buscar (tab) | `magnifyingglass` | 22pt |
| Perfil (tab) | `person.crop.circle` / `.fill` | 22pt |
| Settings (hero) | `gearshape` | 21pt |
| Notifications (hero) | `bell` | 21pt |
| Help (hero) | `questionmark.circle` | 21pt |
| Pix | `arrow.up` (in circle) | 22pt |
| Pagar | `barcode` | 22pt |
| Cartões (quick) | `creditcard` | 22pt |
| Investir (quick) | `chart.line.uptrend.xyaxis` | 22pt |
| Empréstimo | `person.fill` / `dollarsign.circle` | 22pt |
| Conta value | `brazilianrealsign.circle` | 28pt |
| Pix received | `arrow.down` | 19pt |
| Hide balance | `eye` / `eye.slash` | 18pt |
| Back | `chevron.left` | 20pt |
| Receipt | `doc.text` | 18pt |

## 7. Dark Mode

```swift
struct NuTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.nuDarkCanvas : Color.nuCanvas)
            .foregroundStyle(scheme == .dark ? Color.nuDarkTextPrimary : Color.nuTextPrimary)
    }
}
extension View { func nuTheme() -> some View { modifier(NuTheme()) } }
```

Dark mode is deep aubergine `#15101C`, NOT pure black — tinted from Nu Purple. The full-bleed purple hero is identical across themes. Tiles lift to `#1F1729`. Nu Purple `#820AD1` is unchanged; interactive tint shifts to Purple Soft `#C9A6E8` for contrast; text becomes a purple-tinted off-white `#F3EEF8`. Positive amounts stay green `#2ED47A`. Only chrome recedes.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` require iOS 17 — fall back to `UIImpactFeedbackGenerator` and a manual count animation)
- Bundle Inter TTFs via `Info.plist` (`UIAppFonts`) as the Graphik / Nu Sans substitute; Inter is SIL OFL licensed for free distribution
- Always enable `.monospacedDigit()` (tabular figures) on balances, invoice totals, and amounts so currency columns align
- Dynamic Type: support on screen title, balance, greeting, body, row title, meta; keep tab labels and quick-action labels at FIXED sizes (layout-sensitive)
- VoiceOver: read the NuConta tile as "Conta, saldo disponível {amount}, double-tap to open"; activity rows as "{title}, {amount}, {subtitle}{, pendente}"; the eye-toggle as "Hide balance" / "Show balance"
- Color contrast: `#1A1523` on `#F4F2F7` and `#F3EEF8` on `#15101C` pass WCAG AA for body; white on Nu Purple `#820AD1` passes AA — verify product-accent chips, especially gold, with a contrast checker
- Reduce Motion: disable the Pix check stroke-draw (show final state), the balance ticker (set value directly), and the hero parallax (static header)
- Reduce Transparency: replace the tab-bar blur with a solid `#FFFFFF` / `#15101C` fill
- Dark mode: colors invert per the `nuDark*` palette; deep aubergine not black; add a 1pt `nuDarkDivider` border to tiles/sheets as an elevation cue where shadows read weakly
