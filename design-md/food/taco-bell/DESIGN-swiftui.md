# Taco Bell (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Taco Bell's visual language into paste-ready SwiftUI code: `Color` extensions, the brand `LinearGradient`, `Font` extensions, `ViewModifier`s, and example views (the box-preview hero, numbered step pills, the customizer option row, the persistent gradient CTA bar).

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let tbPurple       = Color(red: 0.439, green: 0.125, blue: 0.510) // #702082
    static let tbPurpleBright = Color(red: 0.541, green: 0.169, blue: 0.627) // #8A2BA0
    static let tbMagenta      = Color(red: 0.780, green: 0.169, blue: 0.784) // #C72BC8
    static let tbMagentaPressed = Color(red: 0.659, green: 0.137, blue: 0.624) // #A8239F
    static let tbPink         = Color(red: 0.914, green: 0.157, blue: 0.627) // #E928A0

    // MARK: - Energy / Value
    static let tbYellow = Color(red: 1.000, green: 0.780, blue: 0.000) // #FFC700
    static let tbOrange = Color(red: 1.000, green: 0.416, blue: 0.102) // #FF6A1A

    // MARK: - Text on Yellow / Gradient
    static let tbOnYellow = Color(red: 0.165, green: 0.082, blue: 0.188) // #2A1530

    // MARK: - Canvas & Surfaces (Dark — primary)
    static let tbCanvas    = Color(red: 0.055, green: 0.039, blue: 0.078) // #0E0A14
    static let tbSurface1  = Color(red: 0.102, green: 0.075, blue: 0.133) // #1A1322
    static let tbSurface2  = Color(red: 0.149, green: 0.106, blue: 0.196) // #261B32
    static let tbDivider   = Color(red: 0.204, green: 0.153, blue: 0.247) // #34273F

    // MARK: - Canvas & Surfaces (Light — secondary)
    static let tbCanvasLight   = Color.white                                    // #FFFFFF
    static let tbSurfaceLight1 = Color(red: 0.965, green: 0.949, blue: 0.973)  // #F6F2F8
    static let tbSurfaceLight2 = Color(red: 0.929, green: 0.902, blue: 0.945)  // #EDE6F1
    static let tbDividerLight  = Color(red: 0.890, green: 0.851, blue: 0.918)  // #E3D9EA

    // MARK: - Text
    static let tbTextPrimaryD   = Color(red: 0.957, green: 0.937, blue: 0.969) // #F4EFF7
    static let tbTextSecondaryD = Color(red: 0.663, green: 0.608, blue: 0.714) // #A99BB6
    static let tbTextTertiaryD  = Color(red: 0.431, green: 0.376, blue: 0.475) // #6E6079
    static let tbTextPrimaryL   = Color(red: 0.122, green: 0.086, blue: 0.149) // #1F1626

    // MARK: - Semantic
    static let tbSuccess = Color(red: 0.212, green: 0.761, blue: 0.459) // #36C275
    static let tbError   = Color(red: 1.000, green: 0.302, blue: 0.427) // #FF4D6D
}

// The brand gradient — the identity on screen
extension LinearGradient {
    static let tbBrand = LinearGradient(
        colors: [.tbPurple, .tbMagenta],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
    static let tbBoxHero = LinearGradient(
        colors: [.tbPurple, Color(red: 0.290, green: 0.082, blue: 0.349), Color(red: 0.169, green: 0.047, blue: 0.212)],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
}
```

## 2. Typography

Taco Bell uses a clean techy-geometric brand sans. Bundle the licensed TTFs via `Info.plist` / `UIAppFonts`; fall back to SF Pro. Numerals are tabular for prices, totals, steps.

```swift
extension Font {
    static let tbScreenTitle  = Font.custom("TacoBellSans-Bold",     size: 32).weight(.heavy)
    static let tbBuilderTitle = Font.custom("TacoBellSans-Bold",     size: 26).weight(.heavy)
    static let tbSection      = Font.custom("TacoBellSans-Bold",     size: 22).weight(.bold)
    static let tbCardTitle    = Font.custom("TacoBellSans-Bold",     size: 18).weight(.heavy)
    static let tbBody         = Font.custom("TacoBellSans-Regular",  size: 16).weight(.regular)
    static let tbOption       = Font.custom("TacoBellSans-Medium",   size: 15).weight(.semibold)
    static let tbPrice        = Font.custom("TacoBellSans-Bold",     size: 15).weight(.heavy)
    static let tbMeta         = Font.custom("TacoBellSans-Regular",  size: 14).weight(.regular)
    static let tbEyebrow      = Font.custom("TacoBellSans-Bold",     size: 12).weight(.bold)
    static let tbStepPill     = Font.custom("TacoBellSans-Bold",     size: 12).weight(.bold)
    static let tbButton       = Font.custom("TacoBellSans-Bold",     size: 15).weight(.heavy)
    static let tbChip         = Font.custom("TacoBellSans-Bold",     size: 12).weight(.heavy)
    static let tbCaption      = Font.custom("TacoBellSans-Regular",  size: 12).weight(.regular)
    static let tbTab          = Font.custom("TacoBellSans-Bold",     size: 10).weight(.bold)
}

extension View {
    func tbTabularNumbers() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Box-Preview Hero

```swift
struct BoxPreviewHero: View {
    let boxName: String
    let itemSummary: String  // "4 items · customizable"
    let price: String        // "$5.99"

    var body: some View {
        ZStack {
            RoundedRectangle(cornerRadius: 20).fill(LinearGradient.tbBoxHero)

            // glow blooms
            Circle()
                .fill(RadialGradient(colors: [Color.tbMagenta.opacity(0.35), .clear], center: .center, startRadius: 0, endRadius: 70))
                .frame(width: 130, height: 130)
                .offset(x: 120, y: -55)
            Circle()
                .fill(RadialGradient(colors: [Color.tbYellow.opacity(0.22), .clear], center: .center, startRadius: 0, endRadius: 70))
                .frame(width: 120, height: 120)
                .offset(x: -120, y: 60)

            TacoGlyph().frame(width: 96, height: 70)

            VStack { Spacer()
                HStack(alignment: .bottom) {
                    VStack(alignment: .leading, spacing: 2) {
                        Text(boxName).font(.tbPrice).foregroundStyle(.white)
                        Text(itemSummary).font(.tbCaption).foregroundStyle(Color.tbYellow)
                    }
                    Spacer()
                    Text(price).font(.tbBuilderTitle).foregroundStyle(Color.tbYellow).tbTabularNumbers()
                }
                .padding(14)
            }
        }
        .frame(height: 150)
        .overlay(RoundedRectangle(cornerRadius: 20).stroke(Color.tbDivider, lineWidth: 1))
        .clipShape(RoundedRectangle(cornerRadius: 20))
        .padding(.horizontal, 18)
    }
}

// Simple stylized taco mark
struct TacoGlyph: View {
    var body: some View {
        ZStack(alignment: .bottom) {
            Capsule()
                .fill(LinearGradient(colors: [Color(red: 0.56, green: 0.75, blue: 0.29), Color(red: 0.75, green: 0.22, blue: 0.17)],
                                     startPoint: .top, endPoint: .bottom))
                .frame(height: 26).padding(.horizontal, 6).offset(y: -28)
            UnevenRoundedRectangle(bottomLeadingRadius: 30, bottomTrailingRadius: 30)
                .fill(LinearGradient(colors: [Color(red: 0.95, green: 0.71, blue: 0.25), Color(red: 0.85, green: 0.56, blue: 0.16)],
                                     startPoint: .top, endPoint: .bottom))
                .frame(height: 44)
        }
    }
}
```

### Numbered Step Pills

```swift
struct StepPill: View {
    enum State { case done, active, todo }
    let index: Int
    let label: String
    let state: State

    var body: some View {
        HStack(spacing: 6) {
            ZStack {
                Circle().frame(width: 16, height: 16)
                    .foregroundStyle(badgeBg)
                if state == .done {
                    Image(systemName: "checkmark").font(.system(size: 9, weight: .black)).foregroundStyle(Color(red: 0.024, green: 0.129, blue: 0.059))
                } else {
                    Text("\(index)").font(.system(size: 10, weight: .black)).foregroundStyle(badgeFg)
                }
            }
            Text(label).font(.tbStepPill)
        }
        .foregroundStyle(textColor)
        .padding(.horizontal, 12)
        .frame(height: 30)
        .background(bg)
        .overlay(Capsule().stroke(border, lineWidth: 1))
        .clipShape(Capsule())
    }

    private var bg: some ShapeStyle {
        switch state {
        case .active: return AnyShapeStyle(LinearGradient.tbBrand)
        default:      return AnyShapeStyle(Color.tbSurface1)
        }
    }
    private var border: Color {
        switch state { case .done: return .tbSuccess; case .active: return .clear; default: return .tbDivider }
    }
    private var textColor: Color {
        switch state { case .active: return .white; case .done: return .tbTextPrimaryD; default: return .tbTextSecondaryD }
    }
    private var badgeBg: Color {
        switch state { case .active: return .white.opacity(0.25); case .done: return .tbSuccess; default: return .tbSurface2 }
    }
    private var badgeFg: Color { state == .active ? .white : .tbTextSecondaryD }
}

struct StepPillRow: View {
    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 8) {
                StepPill(index: 1, label: "Main", state: .done)
                StepPill(index: 2, label: "Side", state: .active)
                StepPill(index: 3, label: "Drink", state: .todo)
                StepPill(index: 4, label: "Sweet", state: .todo)
            }
            .padding(.horizontal, 18)
        }
    }
}
```

### Customizer Option Row

```swift
struct OptionRow: View {
    let name: String
    let subtitle: String
    let upcharge: String?     // "+$1.00"
    let thumbColor: Color
    enum Control { case radio(Bool), stepper(Int) }
    @State var control: Control

    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 12).fill(thumbColor).frame(width: 46, height: 46)

            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.tbOption).foregroundStyle(Color.tbTextPrimaryD)
                Text(subtitle).font(.tbCaption).foregroundStyle(Color.tbTextSecondaryD)
            }
            Spacer(minLength: 4)

            if let upcharge { Text(upcharge).font(.system(size: 13, weight: .bold)).foregroundStyle(Color.tbTextSecondaryD) }

            switch control {
            case .radio(let on):
                ZStack {
                    Circle().strokeBorder(on ? Color.tbMagenta : Color.tbDivider, lineWidth: 2).frame(width: 24, height: 24)
                    if on { Circle().fill(Color.tbMagenta).frame(width: 14, height: 14) }
                }
                .contentShape(Rectangle())
                .onTapGesture {
                    withAnimation(.easeOut(duration: 0.15)) { control = .radio(true) }
                    UIImpactFeedbackGenerator(style: .light).impactOccurred()
                }
            case .stepper(let qty):
                HStack(spacing: 12) {
                    stepperButton("minus") { control = .stepper(max(0, qty - 1)) }
                    Text("\(qty)").font(.system(size: 14, weight: .black)).foregroundStyle(Color.tbTextPrimaryD).tbTabularNumbers()
                    stepperButton("plus") { control = .stepper(qty + 1) }
                }
            }
        }
        .padding(.vertical, 14)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.tbDivider).frame(height: 1) }
    }

    private func stepperButton(_ symbol: String, _ action: @escaping () -> Void) -> some View {
        Button(action: action) {
            Image(systemName: symbol).font(.system(size: 13, weight: .black)).foregroundStyle(Color.tbMagenta)
                .frame(width: 26, height: 26)
                .overlay(Circle().stroke(Color.tbMagenta, lineWidth: 1.5))
        }
    }
}
```

### Buttons

```swift
struct TBPrimaryButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .font(.tbButton)
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .padding(.vertical, 15)
            .background(LinearGradient.tbBrand, in: RoundedRectangle(cornerRadius: 14))
            .brightness(configuration.isPressed ? -0.05 : 0)
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
    }
}

struct TBValueButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .font(.tbButton)
            .foregroundStyle(Color.tbOnYellow) // never white
            .frame(maxWidth: .infinity)
            .padding(.vertical, 15)
            .background(Color.tbYellow, in: RoundedRectangle(cornerRadius: 14))
            .brightness(configuration.isPressed ? -0.05 : 0)
    }
}
```

### Persistent Bottom CTA Bar

```swift
struct CtaBar: View {
    let total: String
    let onAdd: () -> Void

    var body: some View {
        HStack(spacing: 12) {
            VStack(alignment: .leading, spacing: 1) {
                Text("Box total").font(.system(size: 11, weight: .semibold)).foregroundStyle(Color.tbTextSecondaryD)
                Text(total).font(.system(size: 19, weight: .heavy)).foregroundStyle(Color.tbTextPrimaryD).tbTabularNumbers()
            }
            Button("Add to Order", action: onAdd)
                .buttonStyle(TBPrimaryButtonStyle())
        }
        .padding(.horizontal, 18)
        .padding(.vertical, 12)
        .background(Color.tbCanvas.opacity(0.96))
        .overlay(alignment: .top) { Rectangle().fill(Color.tbDivider).frame(height: 1) }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct TBTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            MenuView().tabItem { Label("Menu", systemImage: "menucard.fill") }
            RewardsView().tabItem { Label("Rewards", systemImage: "clock.fill") }
            BagView().tabItem { Label("Bag", systemImage: "bag.fill") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle") }
        }
        .tint(.tbMagenta) // active = filled glyph in Electric Magenta, no pill
    }
}
```

## 5. Motion

```swift
// Add to Order — gradient CTA flash + total roll
withAnimation(.easeInOut(duration: 0.11).repeatCount(2, autoreverses: true)) { ctaFlash.toggle() } // drives .brightness
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Step advance — pill default → gradient → green
withAnimation(.easeOut(duration: 0.25)) { stepState[i] = .done }

// Radio fill
withAnimation(.easeOut(duration: 0.15)) { control = .radio(true) }
UIImpactFeedbackGenerator(style: .light).impactOccurred()

// Box-preview contents morph on step complete
withAnimation(.easeOut(duration: 0.2)) { boxStage = nextStage }

// Cravings "Add" → customizer (shared element ~300ms)
.matchedGeometryEffect(id: item.id, in: namespace)
withAnimation(.easeOut(duration: 0.3)) { showCustomizer = true }

// Combo upsell / sauce picker sheet
.presentationDetents([.medium])
.presentationCornerRadius(28)

// Reward redeem
withAnimation(.spring(response: 0.3, dampingFraction: 0.5)) { redeemed = true }
UINotificationFeedbackGenerator().notificationOccurred(.success)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Menu (tab) | `menucard` / `menucard.fill` | 22pt |
| Rewards (tab) | `clock` / `clock.fill` | 22pt |
| Bag (tab) | `bag` / `bag.fill` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Step done | `checkmark` | 9pt |
| Stepper minus / plus | `minus` / `plus` | 13pt |
| Search | `magnifyingglass` | 18pt |
| Back | `chevron.left` | 20pt |
| Sauce / heat | `flame.fill` | 14pt |
| Reward | `gift.fill` | 16pt |
| Store / location | `mappin.and.ellipse` | 18pt |
| Combo upsell | `plus.circle.fill` | 18pt |
| Close | `xmark` | 18pt |
| Customize | `slider.horizontal.3` | 16pt |

## 7. Dark Mode

```swift
struct TBTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.tbCanvas : Color.tbCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.tbTextPrimaryD : Color.tbTextPrimaryL)
    }
}

extension View {
    func tbTheme() -> some View { modifier(TBTheme()) }
}
```

Taco Bell is **dark-first**: the default canvas is the purple-tinted near-black `#0E0A14` (not neutral gray). The brand `#702082 → #C72BC8` gradient and Hot Sauce Yellow `#FFC700` are identical across themes; text on yellow is always `#2A1530` and text on the gradient is always white. In the secondary light theme, glow blooms soften and a normal soft shadow replaces them on the box hero and sheets. Food imagery stays vivid in both modes; only neutrals adapt.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`presentationCornerRadius` requires iOS 16.4; `UnevenRoundedRectangle` is iOS 16.4 — substitute a custom `Shape` on 16.0; spring `response:` is iOS 17)
- Bundle the licensed Taco Bell brand sans via `Info.plist`; fall back to SF Pro Display / SF Pro Text
- Dynamic Type: support on screen/builder titles, section headers, body, option copy; keep step pills, eyebrows, chips/badges, tab labels, and the box-hero price FIXED (layout-critical)
- VoiceOver: announce the box hero as "{boxName}, {itemSummary}, {price}"; step pills as "Step {n}, {label}, {completed/current/not started}"; option rows as "{name}, {subtitle}, {selected/quantity n}"; the CTA as "Add to Order, box total {total}"
- Radios/steppers need a ≥ 44pt hit area though the visual is 24–26pt; the whole option row should also toggle the radio
- Color contrast: white on the `#702082 → #C72BC8` gradient passes WCAG AA across its span; `#2A1530` on `#FFC700` passes AA (the reason white-on-yellow is forbidden); `#F4EFF7` on `#0E0A14` passes AA
- Use `monospacedDigit()` on prices, the running total, and step counts so values don't jitter while rolling
- Reduce Motion: skip the gradient-CTA flash and box-hero morph (set final state directly); make the step-pill state change an instant color swap; keep the radio fill as an immediate change
- Dark mode: the dark theme is the primary design; do not invert the gradient or yellow; in light theme replace glows with soft shadows and keep `#2A1530`-on-yellow / white-on-gradient rules intact
