# Wise (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Wise's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let wiseCanvas       = Color.white                                  // #FFFFFF
    static let wiseSurface      = Color(red: 0.969, green: 0.969, blue: 0.969) // #F7F7F7
    static let wiseSurfaceSunken = Color(red: 0.937, green: 0.937, blue: 0.937) // #EFEFEF
    static let wiseDivider      = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5
    static let wiseBorder       = Color(red: 0.824, green: 0.824, blue: 0.824) // #D2D2D2

    // MARK: - Text
    static let wiseTextPrimary   = Color(red: 0.055, green: 0.059, blue: 0.047) // #0E0F0C
    static let wiseTextSecondary = Color(red: 0.420, green: 0.435, blue: 0.400) // #6B6F66
    static let wiseTextTertiary  = Color(red: 0.604, green: 0.616, blue: 0.584) // #9A9D95

    // MARK: - Brand
    static let wiseBright        = Color(red: 0.624, green: 0.910, blue: 0.439) // #9FE870
    static let wiseBrightPressed = Color(red: 0.541, green: 0.831, blue: 0.361) // #8AD45C
    static let wiseBrightTint    = Color(red: 0.918, green: 0.976, blue: 0.863) // #EAF9DC
    static let wiseForest        = Color(red: 0.086, green: 0.200, blue: 0.000) // #163300
    static let wiseForestHover   = Color(red: 0.055, green: 0.133, blue: 0.000) // #0E2200

    // MARK: - Semantic
    static let wiseSuccess = Color(red: 0.184, green: 0.561, blue: 0.306) // #2F8F4E
    static let wisePending = Color(red: 0.710, green: 0.471, blue: 0.118) // #B5781E
    static let wiseError   = Color(red: 0.831, green: 0.200, blue: 0.169) // #D4332B
}
```

## 2. Typography

Wise Sans is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or fall back to `.system` — Inter is the closest free substitute.

```swift
extension Font {
    static let wiseBalance     = Font.custom("WiseSans-Extrabold", size: 40).weight(.heavy)
    static let wiseTitleLarge  = Font.custom("WiseSans-Extrabold", size: 32).weight(.heavy)
    static let wiseSection     = Font.custom("WiseSans-Extrabold", size: 22).weight(.heavy)
    static let wiseCurrency    = Font.custom("WiseSans-Extrabold", size: 22).weight(.heavy)
    static let wiseSubsection  = Font.custom("WiseSans-Semibold",  size: 18).weight(.semibold)
    static let wiseAmount      = Font.custom("WiseSans-Semibold",  size: 16).weight(.semibold)
    static let wiseTitle       = Font.custom("WiseSans-Semibold",  size: 16).weight(.semibold)
    static let wiseBody        = Font.custom("WiseSans-Regular",   size: 15).weight(.regular)
    static let wiseButton      = Font.custom("WiseSans-Extrabold", size: 16).weight(.heavy)
    static let wiseMeta        = Font.custom("WiseSans-Regular",   size: 13).weight(.regular)
    static let wiseLabelUpper  = Font.custom("WiseSans-Extrabold", size: 11).weight(.heavy)
    static let wiseTab         = Font.custom("WiseSans-Semibold",  size: 11).weight(.semibold)
    static let wiseCaption     = Font.custom("WiseSans-Regular",   size: 11).weight(.regular)
}

extension Font {
    static func wise(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .rounded)
    }
}
```

Tabular figures on every money label:

```swift
Text("€3,180.55").font(.wiseCurrency).monospacedDigit()
```

## 3. Signature Components

### Primary CTA (Bright Green)

```swift
struct WisePrimaryButton: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.wiseButton)
                .foregroundStyle(Color.wiseForest) // forest on bright green, never white
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(Color.wiseBright, in: RoundedRectangle(cornerRadius: 16))
        }
        .sensoryFeedback(.impact(weight: .light), trigger: UUID())
        .buttonStyle(WisePressableStyle(pressedFill: .wiseBrightPressed))
    }
}

struct WiseForestButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.wiseButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(Color.wiseForest, in: RoundedRectangle(cornerRadius: 16))
        }
        .buttonStyle(WisePressableStyle(pressedFill: .wiseForestHover))
    }
}

struct WisePressableStyle: ButtonStyle {
    var pressedFill: Color
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .brightness(configuration.isPressed ? -0.03 : 0)
            .animation(.easeOut(duration: 0.15), value: configuration.isPressed)
    }
}
```

### Forest Account Hero (with number roll-up)

```swift
struct ForestAccountHero: View {
    let total: String

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            Text("TOTAL BALANCE")
                .font(.wiseLabelUpper)
                .foregroundStyle(Color.wiseBright)
            Text(total)
                .font(.wiseBalance)
                .monospacedDigit()
                .foregroundStyle(.white)
                .contentTransition(.numericText())   // rolls digits into place
            HStack(spacing: 8) {
                Image(systemName: "plus")
                    .font(.system(size: 14, weight: .bold))
                Text("Add money").font(.wiseTitle)
            }
            .foregroundStyle(Color.wiseBright)
            .padding(.top, 4)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
        .padding(24)
        .background(RoundedRectangle(cornerRadius: 20).fill(Color.wiseForestHover))
        .shadow(color: Color.wiseForestHover.opacity(0.25), radius: 16, y: 12)
    }
}
```

### Currency Balance Row

```swift
struct CurrencyRow: View {
    let flag: String
    let code: String
    let name: String
    let balance: String

    var body: some View {
        HStack(spacing: 12) {
            Text(flag)
                .font(.system(size: 22))
                .frame(width: 32, height: 32)
                .background(Circle().fill(Color.wiseSurface))
            VStack(alignment: .leading, spacing: 2) {
                Text(code).font(.wiseTitle).foregroundStyle(.wiseTextPrimary)
                Text(name).font(.wiseMeta).foregroundStyle(.wiseTextSecondary)
            }
            Spacer()
            Text(balance)
                .font(.wiseCurrency)
                .monospacedDigit()
                .foregroundStyle(.wiseTextPrimary)
        }
        .padding(.horizontal, 16)
        .frame(height: 64)
        .contentShape(Rectangle())
        .overlay(Divider().background(Color.wiseDivider), alignment: .bottom)
    }
}
```

### Fee-Transparency Card (signature)

```swift
struct FeeBreakdownCard: View {
    struct Line: Identifiable { let id = UUID(); let label: String; let value: String; var emphasized = false }
    let lines: [Line]

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ForEach(Array(lines.enumerated()), id: \.element.id) { idx, line in
                HStack {
                    Text(line.label)
                        .font(line.emphasized ? .wiseTitle : .wiseBody)
                        .foregroundStyle(line.emphasized ? Color.wiseForest : .wiseTextSecondary)
                    Spacer()
                    Text(line.value)
                        .font(line.emphasized ? .wiseSubsection : .wiseAmount)
                        .monospacedDigit()
                        .foregroundStyle(line.emphasized ? Color.wiseForest : .wiseTextPrimary)
                }
                .padding(.vertical, 12)
                .padding(.horizontal, line.emphasized ? 12 : 0)
                .background(
                    line.emphasized
                        ? RoundedRectangle(cornerRadius: 10).fill(Color.wiseBrightTint)
                        : nil
                )
                if idx < lines.count - 1 && !line.emphasized {
                    Divider().background(Color.wiseDivider)
                }
            }
        }
        .padding(20)
        .background(
            RoundedRectangle(cornerRadius: 16)
                .fill(Color.wiseCanvas)
                .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Color.wiseDivider, lineWidth: 1))
        )
    }
}
```

### Send-Money Stepper (signature)

```swift
struct SendStepper: View {
    let steps: [String]      // ["Recipient","Amount","Review","Pay"]
    let current: Int

    var body: some View {
        HStack(spacing: 0) {
            ForEach(steps.indices, id: \.self) { i in
                HStack(spacing: 0) {
                    ZStack {
                        Circle()
                            .fill(i < current ? Color.wiseBright : (i == current ? Color.wiseForest : Color.wiseDivider))
                            .frame(width: 28, height: 28)
                        if i < current {
                            Image(systemName: "checkmark")
                                .font(.system(size: 12, weight: .bold))
                                .foregroundStyle(Color.wiseForest)
                        } else {
                            Text("\(i + 1)")
                                .font(.system(size: 13, weight: .bold))
                                .foregroundStyle(i == current ? .white : .wiseTextSecondary)
                        }
                    }
                    if i < steps.count - 1 {
                        Rectangle()
                            .fill(i < current ? Color.wiseForest : Color.wiseDivider)
                            .frame(height: 2)
                            .frame(maxWidth: .infinity)
                    }
                }
            }
        }
    }
}
```

## 4. Number Roll-Up Helper

```swift
// Use SwiftUI's contentTransition(.numericText()) and animate the value change:
struct RollingBalance: View {
    let value: Double
    var body: some View {
        Text(value, format: .currency(code: "GBP"))
            .font(.wiseBalance)
            .monospacedDigit()
            .contentTransition(.numericText(value: value))
            .animation(.easeOut(duration: 0.5), value: value)
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
        appearance.shadowColor = UIColor(Color.wiseDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            CardView().tabItem { Label("Card", systemImage: "creditcard.fill") }
            RecipientsView().tabItem { Label("Recipients", systemImage: "person.2.fill") }
            PaymentsView().tabItem { Label("Payments", systemImage: "arrow.left.arrow.right") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle.fill") }
        }
        .tint(.wiseForest) // active = forest; pair with a bright-green active marker if desired
    }
}
```

## 6. Motion

Use SwiftUI easing. Haptics via `.sensoryFeedback` (iOS 17+).

```swift
// Number roll-up — contentTransition(.numericText()) + .animation(.easeOut(0.5))

// CTA press — WisePressableStyle: scale 0.98 + brightness -0.03, 150ms

// Stepper advance: animate the circle fill + rail with .easeInOut(0.2) on `current` change

// Rate ticker pulse
struct LiveDot: View {
    @State private var on = false
    var body: some View {
        Circle().fill(Color.wiseSuccess).frame(width: 6, height: 6)
            .opacity(on ? 1 : 0.3)
            .onAppear { withAnimation(.easeInOut(duration: 1).repeatForever()) { on = true } }
    }
}

// Fee card reveal: stagger lines with .transition(.opacity) and per-row delay
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 24pt |
| Card (tab) | `creditcard` / `creditcard.fill` | 24pt |
| Recipients (tab) | `person.2` / `person.2.fill` | 24pt |
| Payments (tab) | `arrow.left.arrow.right` | 24pt |
| Account (tab) | `person.crop.circle` / `.fill` | 24pt |
| Send | `paperplane.fill` | 22pt |
| Add money | `plus` | 22pt |
| Request | `arrow.down.left` | 22pt |
| Convert | `arrow.left.arrow.right.circle` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Notifications | `bell` / `bell.fill` | 22pt |
| Statement | `doc.text` | 20pt |
| Step complete | `checkmark` | 12pt |
| Money in | `arrow.down.left` | 14pt |
| Rate up / down | `arrow.up` / `arrow.down` | 12pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` and `.contentTransition(.numericText())` you need iOS 17; fall back to `UIImpactFeedbackGenerator` and a simple crossfade)
- Support Dynamic Type on titles, body, names, amounts — keep tab labels, uppercase labels, and currency-row figures at a fixed size for column alignment
- VoiceOver: announce the total with currency; read each fee-breakdown line as "label, value" and mark the "Recipient gets" line with `.accessibilityAddTraits(.isHeader)`; on the stepper, announce "Step 2 of 4, Amount"
- Contrast: forest `#163300` on bright green `#9FE870` passes WCAG AAA — never white on bright green. `#6B6F66` secondary on white passes AA at 14pt+; validate 11pt labels
- Reduce Motion: when `accessibilityReduceMotion` is on, swap the number roll-up for an instant set and the stepper fill for a crossfade
- Touch targets: bright-green CTA 52pt, quick-action circles 52pt, 24pt tab glyphs get a 44pt hit area, stepper dots get a 44pt hit area
