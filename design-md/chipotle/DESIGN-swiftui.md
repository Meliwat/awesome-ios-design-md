# Chipotle (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Chipotle's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let cmgCream     = Color(red: 1.0,   green: 0.961, blue: 0.882) // #FFF5E1
    static let cmgSurface   = Color.white                                  // #FFFFFF
    static let cmgCreamDeep = Color(red: 0.957, green: 0.910, blue: 0.816) // #F4E8D0
    static let cmgDivider   = Color(red: 0.910, green: 0.863, blue: 0.769) // #E8DCC4

    // MARK: - Text (brown, never black)
    static let cmgTextPrimary   = Color(red: 0.271, green: 0.078, blue: 0.0)   // #451400
    static let cmgTextSecondary = Color(red: 0.541, green: 0.420, blue: 0.310) // #8A6B4F
    static let cmgTextTertiary  = Color(red: 0.706, green: 0.604, blue: 0.510) // #B49A82

    // MARK: - Brand
    static let cmgRed        = Color(red: 0.659, green: 0.086, blue: 0.071) // #A81612
    static let cmgRedPressed = Color(red: 0.549, green: 0.071, blue: 0.063) // #8C1210
    static let cmgRedTint    = Color(red: 0.957, green: 0.886, blue: 0.882) // #F4E2E1

    // MARK: - Support / Semantic
    static let cmgTan     = Color(red: 0.675, green: 0.549, blue: 0.357) // #AC8C5B
    static let cmgSuccess = Color(red: 0.235, green: 0.478, blue: 0.235) // #3C7A3C
    static let cmgSpice   = Color(red: 0.878, green: 0.482, blue: 0.149) // #E07B26
}
```

## 2. Typography

Headers use Archivo (ALL CAPS); body uses Avenir Next (sentence case). Bundle Archivo via `Info.plist` (`UIAppFonts`); Avenir Next ships with iOS.

```swift
extension Font {
    // Display / Headers — Archivo, ALL CAPS at call site
    static let cmgHero        = Font.custom("Archivo-ExtraBold", size: 32) // use Archivo Narrow if bundled
    static let cmgScreenTitle = Font.custom("Archivo-ExtraBold", size: 26)
    static let cmgSection     = Font.custom("Archivo-Bold",      size: 18)
    static let cmgButton      = Font.custom("Archivo-ExtraBold", size: 16)
    static let cmgPoints      = Font.custom("Archivo-ExtraBold", size: 34)
    static let cmgBadge       = Font.custom("Archivo-Bold",      size: 11)

    // Body / UI — Avenir Next
    static let cmgItemName = Font.custom("AvenirNext-Bold",    size: 17)
    static let cmgPrice    = Font.custom("AvenirNext-Bold",    size: 16)
    static let cmgBody     = Font.custom("AvenirNext-Regular", size: 15)
    static let cmgMeta     = Font.custom("AvenirNext-Regular", size: 13)
    static let cmgTab      = Font.custom("AvenirNext-Bold",    size: 11)
    static let cmgCaption  = Font.custom("AvenirNext-Regular", size: 11)
}

// Helper to apply ALL CAPS + tracking on header text
extension Text {
    func cmgCaps(_ tracking: CGFloat) -> some View {
        self.textCase(.uppercase).tracking(tracking)
    }
}
```

Prices, calories, and points use tabular figures — apply `.monospacedDigit()`.

## 3. Signature Components

### Section Header (ALL CAPS)

```swift
struct SectionHeader: View {
    let title: String
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(title)
                .font(.cmgSection)
                .cmgCaps(0.6)
                .foregroundStyle(Color.cmgTextPrimary)
            Rectangle().fill(Color.cmgDivider).frame(height: 1)
        }
        .padding(.top, 24)
    }
}
```

### Build-Your-Burrito Ingredient Row

```swift
struct IngredientRow: View {
    let name: String
    let meta: String?            // "210 Cal" or "Add for $2.00"
    let isSelected: Bool
    var multiSelect: Bool = false
    let tap: () -> Void

    var body: some View {
        Button(action: tap) {
            HStack(spacing: 12) {
                VStack(alignment: .leading, spacing: 2) {
                    Text(name).font(.cmgItemName).foregroundStyle(Color.cmgTextPrimary)
                    if let meta {
                        Text(meta).font(.cmgMeta).foregroundStyle(Color.cmgTextSecondary)
                    }
                }
                Spacer()
                selector
            }
            .padding(.horizontal, 16)
            .frame(minHeight: 60)
            .background(isSelected ? Color.cmgRedTint : Color.cmgSurface)
            .overlay(alignment: .leading) {
                if isSelected {
                    Rectangle().fill(Color.cmgRed).frame(width: 1.5)
                }
            }
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.impact(weight: .light), trigger: isSelected)
        .animation(.spring(response: 0.2, dampingFraction: 0.7), value: isSelected)
    }

    @ViewBuilder private var selector: some View {
        ZStack {
            if multiSelect {
                RoundedRectangle(cornerRadius: 6)
                    .strokeBorder(isSelected ? Color.cmgRed : Color.cmgDivider, lineWidth: 2)
                    .background(isSelected ? RoundedRectangle(cornerRadius: 6).fill(Color.cmgRed) : nil)
                    .frame(width: 26, height: 26)
            } else {
                Circle()
                    .strokeBorder(isSelected ? Color.cmgRed : Color.cmgDivider, lineWidth: 2)
                    .background(isSelected ? Circle().fill(Color.cmgRed) : nil)
                    .frame(width: 28, height: 28)
            }
            if isSelected {
                Image(systemName: "checkmark")
                    .font(.system(size: 14, weight: .bold))
                    .foregroundStyle(.white)
                    .transition(.scale)
            }
        }
    }
}
```

### Primary CTA (ALL CAPS)

```swift
struct ChipotleCTA: View {
    let label: String     // "ADD TO BAG · $9.65"
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.cmgButton)
                .cmgCaps(0.8)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 54)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.cmgRed))
        }
        .buttonStyle(CMGPressableStyle())
    }
}

struct CMGPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .background(configuration.isPressed ?
                RoundedRectangle(cornerRadius: 8).fill(Color.cmgRedPressed) : nil)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### Rewards Points Ring

```swift
struct RewardsRing: View {
    let points: Int
    let goal: Int

    var body: some View {
        ZStack {
            Circle().stroke(Color.cmgDivider, lineWidth: 10)
            Circle()
                .trim(from: 0, to: min(1, Double(points) / Double(goal)))
                .stroke(Color.cmgRed, style: StrokeStyle(lineWidth: 10, lineCap: .round))
                .rotationEffect(.degrees(-90))
                .animation(.easeOut(duration: 0.7), value: points)
            VStack(spacing: 2) {
                Text("\(points)")
                    .font(.cmgPoints).monospacedDigit()
                    .foregroundStyle(Color.cmgRed)
                Text("POINTS").font(.cmgBadge).cmgCaps(0.6)
                    .foregroundStyle(Color.cmgTextSecondary)
            }
        }
        .frame(width: 120, height: 120)
    }
}
```

### Step Progress Bar

```swift
struct StepProgress: View {
    let total: Int
    let current: Int    // 0-based active step

    var body: some View {
        HStack(spacing: 2) {
            ForEach(0..<total, id: \.self) { i in
                Capsule()
                    .fill(i <= current ? Color.cmgRed : Color.cmgDivider)
                    .frame(height: 4)
            }
        }
        .padding(.horizontal, 16)
        .animation(.easeOut(duration: 0.25), value: current)
    }
}
```

### Foil Hero

```swift
struct FoilHero: View {
    let title: String
    let subtitle: String
    let cta: String

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            LinearGradient(
                colors: [Color(white: 0.85), Color(white: 0.66)],
                startPoint: .topLeading, endPoint: .bottomTrailing
            )
            // faint diagonal streaks → crinkled-foil feel
            LinearGradient(
                colors: [.white.opacity(0.18), .clear, .white.opacity(0.10), .clear],
                startPoint: .leading, endPoint: .trailing
            )
            VStack(alignment: .leading, spacing: 8) {
                Text(title).font(.cmgHero).cmgCaps(0.4).foregroundStyle(Color.cmgTextPrimary)
                Text(subtitle).font(.cmgBody).foregroundStyle(Color.cmgTextSecondary)
                ChipotleCTA(label: cta) {}
                    .fixedSize()
            }
            .padding(20)
        }
        .frame(height: 200)
        .clipShape(RoundedRectangle(cornerRadius: 12))
    }
}
```

## 4. Build-Flow State

```swift
// Single-select sections replace the choice; multi-select toggle into a Set.
struct OrderBuild {
    var rice: String? = nil          // single-select
    var beans: String? = nil         // single-select
    var protein: String? = nil       // single-select
    var toppings: Set<String> = []   // multi-select
    var salsas: Set<String> = []     // multi-select
    var extras: Set<String> = []     // adds +$ tags

    var runningTotal: Decimal { /* base + protein delta + extras */ 9.65 }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(Color.cmgDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            OrderView().tabItem    { Label("Order",    systemImage: "fork.knife") }
            ScanView().tabItem     { Label("Scan",     systemImage: "qrcode") }
            DeliveryView().tabItem { Label("Delivery", systemImage: "bicycle") }
            MoreView().tabItem     { Label("More",     systemImage: "ellipsis") }
        }
        .tint(.cmgRed) // active = Chipotle Red
    }
}
```

## 6. Motion

```swift
// Ingredient select
.sensoryFeedback(.impact(weight: .light), trigger: isSelected)
.animation(.spring(response: 0.2, dampingFraction: 0.7), value: isSelected)

// Step progress slide
.animation(.easeOut(duration: 0.25), value: currentStep)

// Rewards ring fill (0 → value over 700ms) + counting number
.animation(.easeOut(duration: 0.7), value: points)

// Add to Bag success
.sensoryFeedback(.success, trigger: didAddToBag)
// then bump the bag-count badge scale 1.0 → 1.2 → 1.0 with .spring

// Sheet present: .presentationDetents + default slide; warm scrim via .presentationBackground
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Selected check | `checkmark` | 14pt |
| Order (tab) | `fork.knife` | 24pt |
| Scan (tab) | `qrcode` | 24pt |
| Delivery (tab) | `bicycle` | 24pt |
| More (tab) | `ellipsis` | 24pt |
| Bag | `bag` / `bag.fill` | 24pt |
| Search | `magnifyingglass` | 18pt |
| Location chevron | `chevron.down` | 14pt |
| Quantity minus / plus | `minus` / `plus` | 14pt |
| Rewards / star | `star.fill` | 18pt |
| Spicy | `flame.fill` | 13pt |
| Back | `chevron.left` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator(style: .light)` and `UINotificationFeedbackGenerator`)
- Support Dynamic Type on item names, body, descriptions; ALL-CAPS headers should scale to a max and never wrap (condensed Archivo absorbs growth) — pin tab labels and step segments
- VoiceOver: ingredient rows announce "White Rice, 210 calories, selected" — add `.accessibilityAddTraits(.isSelected)` and treat multi-select as a toggle; the CTA reads "Add to bag, total $9.65"
- The rewards ring should expose a single value: `.accessibilityValue("1,250 of 1,500 points")` and hide the decorative arc
- Contrast: espresso `#451400` on cream `#FFF5E1` is very high contrast (good); secondary `#8A6B4F` on cream passes WCAG AA at 13pt+ — validate 11pt captions and darken toward `#6E5238` if needed
- Keep selected ingredients distinguished by more than color (the check glyph + left rule), not the `#F4E2E1` wash alone
