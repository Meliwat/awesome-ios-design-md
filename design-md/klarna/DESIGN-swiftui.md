# Klarna (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Klarna's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let klCanvas        = Color.white                                  // #FFFFFF
    static let klSurfacePink   = Color(red: 1.000, green: 0.949, blue: 0.961) // #FFF2F5
    static let klSurfaceNeutral = Color(red: 0.965, green: 0.957, blue: 0.973) // #F6F4F8
    static let klDivider       = Color(red: 0.941, green: 0.867, blue: 0.890) // #F0DDE3
    static let klBorder        = Color(red: 0.886, green: 0.824, blue: 0.847) // #E2D2D8

    // MARK: - Text
    static let klTextPrimary   = Color(red: 0.043, green: 0.020, blue: 0.114) // #0B051D
    static let klTextSecondary = Color(red: 0.431, green: 0.408, blue: 0.471) // #6E6878
    static let klTextTertiary  = Color(red: 0.608, green: 0.588, blue: 0.639) // #9B96A3

    // MARK: - Brand
    static let klPink        = Color(red: 1.000, green: 0.702, blue: 0.780) // #FFB3C7
    static let klPinkPressed = Color(red: 0.957, green: 0.612, blue: 0.706) // #F49CB4
    static let klPinkTint    = Color(red: 1.000, green: 0.949, blue: 0.961) // #FFF2F5
    static let klBlack       = Color(red: 0.043, green: 0.020, blue: 0.114) // #0B051D
    static let klBlackHover  = Color(red: 0.102, green: 0.075, blue: 0.188) // #1A1330

    // MARK: - Semantic
    static let klSuccess  = Color(red: 0.055, green: 0.541, blue: 0.310) // #0E8A4F
    static let klUpcoming = Color(red: 0.663, green: 0.439, blue: 0.055) // #A9700E
    static let klError    = Color(red: 0.784, green: 0.063, blue: 0.180) // #C8102E
}
```

## 2. Typography

Klarna Text/Display are proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or fall back to `.system` — Inter is the closest free substitute.

```swift
extension Font {
    static let klAmountHero  = Font.custom("KlarnaDisplay-Bold", size: 36).weight(.bold)
    static let klTitleLarge  = Font.custom("KlarnaText-Bold",    size: 28).weight(.bold)
    static let klSection     = Font.custom("KlarnaText-Bold",    size: 22).weight(.bold)
    static let klPlanAmount  = Font.custom("KlarnaText-Bold",    size: 22).weight(.bold)
    static let klSubsection  = Font.custom("KlarnaText-Medium",  size: 18).weight(.semibold)
    static let klAmount      = Font.custom("KlarnaText-Medium",  size: 16).weight(.semibold)
    static let klTitle       = Font.custom("KlarnaText-Medium",  size: 16).weight(.semibold)
    static let klBody        = Font.custom("KlarnaText-Regular", size: 15).weight(.regular)
    static let klButton      = Font.custom("KlarnaText-Bold",    size: 16).weight(.bold)
    static let klMeta        = Font.custom("KlarnaText-Regular", size: 13).weight(.regular)
    static let klLabelUpper  = Font.custom("KlarnaText-Bold",    size: 11).weight(.bold)
    static let klTab         = Font.custom("KlarnaText-Medium",  size: 11).weight(.semibold)
    static let klCaption     = Font.custom("KlarnaText-Regular", size: 11).weight(.regular)
}

extension Font {
    static func kl(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .rounded)
    }
}
```

Tabular figures on every money label:

```swift
Text("£128.40").font(.klPlanAmount).monospacedDigit()
```

## 3. Signature Components

### Primary CTA (Klarna Pink)

```swift
struct KlarnaPrimaryButton: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.klButton)
                .foregroundStyle(Color.klBlack) // near-black on pink, never white
                .frame(maxWidth: .infinity, minHeight: 56)
                .background(Color.klPink, in: Capsule()) // very soft full pill
        }
        .sensoryFeedback(.impact(weight: .light), trigger: UUID())
        .buttonStyle(KlarnaPressableStyle(pressedFill: .klPinkPressed))
    }
}

struct KlarnaBlackButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.klButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 56)
                .background(Color.klBlack, in: Capsule())
        }
        .buttonStyle(KlarnaPressableStyle(pressedFill: .klBlackHover))
    }
}

struct KlarnaPressableStyle: ButtonStyle {
    var pressedFill: Color
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.easeInOut(duration: 0.16), value: configuration.isPressed)
    }
}
```

### Pay-in-4 Schedule Card (signature)

```swift
struct PayInFourCard: View {
    struct Installment: Identifiable {
        let id = UUID(); let date: String; let amount: String
        enum State { case paid, next, upcoming }
        let state: State
    }
    let installments: [Installment]

    var body: some View {
        VStack(spacing: 16) {
            HStack(spacing: 0) {
                ForEach(Array(installments.enumerated()), id: \.element.id) { idx, item in
                    VStack(spacing: 8) {
                        dot(for: item.state)
                        Text(item.date).font(.klMeta).foregroundStyle(.klTextSecondary)
                        Text(item.amount).font(.klMeta.weight(.bold)).monospacedDigit().foregroundStyle(.klTextPrimary)
                    }
                    .frame(maxWidth: .infinity)
                    if idx < installments.count - 1 {
                        Rectangle()
                            .fill(item.state == .paid ? Color.klPink : Color.klDivider)
                            .frame(height: 2)
                            .offset(y: -28) // align with dots
                    }
                }
            }
        }
        .padding(20)
        .background(
            RoundedRectangle(cornerRadius: 20)
                .fill(Color.klCanvas)
                .overlay(RoundedRectangle(cornerRadius: 20).strokeBorder(Color.klDivider, lineWidth: 1))
        )
    }

    @ViewBuilder private func dot(for state: Installment.State) -> some View {
        switch state {
        case .paid:
            ZStack {
                Circle().fill(Color.klPink).frame(width: 28, height: 28)
                Image(systemName: "checkmark").font(.system(size: 12, weight: .bold)).foregroundStyle(Color.klBlack)
            }
        case .next:
            Circle().strokeBorder(Color.klBlack, lineWidth: 2).frame(width: 28, height: 28)
        case .upcoming:
            Circle().strokeBorder(Color.klDivider, lineWidth: 2).frame(width: 28, height: 28)
        }
    }
}
```

### Black Payment-Summary Hero

```swift
struct KlarnaBlackHero: View {
    let total: String
    let nextDate: String

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            Text("TOTAL DUE")
                .font(.klLabelUpper)
                .foregroundStyle(Color.klPink)
            Text(total)
                .font(.klAmountHero)
                .monospacedDigit()
                .foregroundStyle(.white)
            Text("Next payment \(nextDate)")
                .font(.klMeta)
                .foregroundStyle(.klTextTertiary)
            HStack(spacing: 8) {
                Image(systemName: "creditcard.fill").font(.system(size: 13))
                Text("Pay now").font(.klTitle)
            }
            .foregroundStyle(Color.klPink)
            .padding(.top, 4)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(24)
        .background(RoundedRectangle(cornerRadius: 24).fill(Color.klBlack))
        .shadow(color: Color.klBlack.opacity(0.18), radius: 16, y: 12)
    }
}
```

### Order Row

```swift
struct OrderRow: View {
    let merchant: String
    let meta: String
    let amount: String
    let status: String
    let statusColor: Color
    let logo: Image

    var body: some View {
        HStack(spacing: 12) {
            logo
                .resizable().aspectRatio(contentMode: .fill)
                .frame(width: 44, height: 44)
                .background(Color.klSurfacePink)
                .clipShape(RoundedRectangle(cornerRadius: 12))
            VStack(alignment: .leading, spacing: 2) {
                Text(merchant).font(.klTitle).foregroundStyle(.klTextPrimary)
                Text(meta).font(.klMeta).foregroundStyle(.klTextSecondary)
            }
            Spacer()
            VStack(alignment: .trailing, spacing: 4) {
                Text(amount).font(.klAmount).monospacedDigit().foregroundStyle(.klTextPrimary)
                Text(status)
                    .font(.klCaption.weight(.bold))
                    .foregroundStyle(statusColor)
            }
        }
        .padding(.horizontal, 20)
        .frame(height: 72)
        .contentShape(Rectangle())
        .overlay(Divider().background(Color.klDivider), alignment: .bottom)
    }
}
```

### In-App Shopping Browser Footer (signature)

```swift
struct PayWithKlarnaFooter: View {
    let action: () -> Void
    @State private var shown = false

    var body: some View {
        Button(action: action) {
            HStack {
                Spacer()
                Text("Pay with Klarna").font(.klButton).foregroundStyle(Color.klBlack)
                Spacer()
            }
            .frame(height: 56)
            .background(Color.klPink)
        }
        .buttonStyle(KlarnaPressableStyle(pressedFill: .klPinkPressed))
        .offset(y: shown ? 0 : 80)
        .onAppear {
            withAnimation(.easeOut(duration: 0.32)) { shown = true }
        }
    }
}
```

## 4. Smooth Transition Helper

```swift
// Klarna's signature glide: slightly slower easing for screen / sheet transitions.
extension AnyTransition {
    static var klarnaSlide: AnyTransition {
        .asymmetric(
            insertion: .move(edge: .trailing).combined(with: .opacity),
            removal: .move(edge: .leading).combined(with: .opacity)
        )
    }
}

struct KlarnaGlide: ViewModifier {
    func body(content: Content) -> some View {
        content.transition(.klarnaSlide)
            .animation(.easeInOut(duration: 0.34), value: UUID())
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(Color.klDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            ShopView().tabItem { Label("Shop", systemImage: "bag.fill") }
            PaymentsView().tabItem { Label("Payments", systemImage: "creditcard.fill") }
            RewardsView().tabItem { Label("Rewards", systemImage: "gift.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle.fill") }
        }
        .tint(.klBlack) // active = near-black; pair with a soft pink active marker if desired
    }
}
```

## 6. Motion

Use SwiftUI easing (slightly unhurried). Haptics via `.sensoryFeedback` (iOS 17+).

```swift
// Slide transitions — KlarnaGlide: easeInOut 0.34s, move + opacity

// CTA press — KlarnaPressableStyle: scale 0.98, easeInOut 0.16s

// Schedule fill: animate a dot from outlined → pink + check with .easeInOut(0.26)
// and fill the preceding track segment pink

// Footer reveal — PayWithKlarnaFooter: offset 80 → 0, easeOut 0.32s

// Amount roll after payment
.contentTransition(.numericText())
.animation(.easeInOut(duration: 0.3), value: totalDue)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Shop (tab) | `bag` / `bag.fill` | 24pt |
| Payments (tab) | `creditcard` / `creditcard.fill` | 24pt |
| Rewards (tab) | `gift` / `gift.fill` | 24pt |
| Profile (tab) | `person.crop.circle` / `.fill` | 24pt |
| Pay now | `creditcard.fill` | 22pt |
| Browse | `magnifyingglass` | 18pt |
| Schedule paid | `checkmark` | 12pt |
| Order delivered | `shippingbox.fill` | 18pt |
| Search | `magnifyingglass` | 18pt |
| Notifications | `bell` / `bell.fill` | 22pt |
| Help | `questionmark.circle` | 20pt |
| Chevron | `chevron.right` | 14pt |
| Card | `creditcard` | 22pt |
| Due reminder | `clock` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` and `.contentTransition(.numericText())` you need iOS 17; fall back to `UIImpactFeedbackGenerator` and a crossfade)
- Support Dynamic Type on titles, body, amounts — keep tab labels, uppercase labels, and schedule date/amount sizes fixed for alignment under the dots
- VoiceOver: announce the total with currency; describe the schedule as "Payment 1 of 4, paid, £32.10 on 1 June; Payment 2 of 4, next, due 14 June" so the plan is fully audible; mark the hero "TOTAL DUE" with `.accessibilityAddTraits(.isHeader)`
- Contrast: near-black `#0B051D` on Klarna Pink `#FFB3C7` passes WCAG AAA — never white on pink. `#6E6878` secondary on white passes AA at 14pt+; validate 11pt labels
- Reduce Motion: when `accessibilityReduceMotion` is on, swap the slide glide and footer slide-up for crossfades; set the schedule dot/track instantly
- Touch targets: pink CTA 56pt, quick-action circles 56pt, 24pt tab glyphs get a 44pt hit area, schedule dots get a 44pt hit area
