# Walmart (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Walmart's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let wmtCanvas      = Color.white                                  // #FFFFFF
    static let wmtSurfaceTint = Color(red: 0.949, green: 0.973, blue: 0.992) // #F2F8FD
    static let wmtSurfaceGray = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let wmtDivider     = Color(red: 0.886, green: 0.910, blue: 0.941) // #E2E8F0
    static let wmtBorder      = Color(red: 0.788, green: 0.824, blue: 0.871) // #C9D2DE

    // MARK: - Text
    static let wmtTextPrimary   = Color(red: 0.180, green: 0.184, blue: 0.196) // #2E2F32
    static let wmtTextSecondary = Color(red: 0.455, green: 0.463, blue: 0.486) // #74767C
    static let wmtTextTertiary  = Color(red: 0.608, green: 0.627, blue: 0.659) // #9BA0A8

    // MARK: - Brand
    static let wmtBlue        = Color(red: 0.000, green: 0.443, blue: 0.863) // #0071DC
    static let wmtBluePressed = Color(red: 0.000, green: 0.310, blue: 0.604) // #004F9A
    static let wmtBlueTint    = Color(red: 0.902, green: 0.945, blue: 0.988) // #E6F1FC
    static let wmtSpark       = Color(red: 1.000, green: 0.761, blue: 0.125) // #FFC220
    static let wmtSparkPressed = Color(red: 0.898, green: 0.659, blue: 0.000) // #E5A800

    // MARK: - Semantic
    static let wmtSuccess  = Color(red: 0.102, green: 0.498, blue: 0.216) // #1A7F37
    static let wmtSavings  = Color(red: 0.165, green: 0.529, blue: 0.012) // #2A8703
    static let wmtWarning  = Color(red: 0.698, green: 0.369, blue: 0.000) // #B25E00
    static let wmtError    = Color(red: 0.816, green: 0.227, blue: 0.176) // #D03A2D
    static let wmtStarGold = Color(red: 1.000, green: 0.722, blue: 0.110) // #FFB81C
}
```

## 2. Typography

Bogle is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or fall back to `.system` — Inter is the closest free substitute if you ship a webfont; SF Pro is the safe native fallback.

```swift
extension Font {
    static let wmtTitleLarge  = Font.custom("Bogle-Bold",     size: 28).weight(.bold)
    static let wmtSection     = Font.custom("Bogle-Bold",     size: 22).weight(.bold)
    static let wmtPriceLarge  = Font.custom("Bogle-Bold",     size: 22).weight(.bold)
    static let wmtSubsection  = Font.custom("Bogle-Semibold", size: 18).weight(.semibold)
    static let wmtProductTitle = Font.custom("Bogle-Semibold", size: 16).weight(.semibold)
    static let wmtBody        = Font.custom("Bogle-Regular",  size: 15).weight(.regular)
    static let wmtButton      = Font.custom("Bogle-Semibold", size: 16).weight(.semibold)
    static let wmtPriceInline = Font.custom("Bogle-Bold",     size: 15).weight(.bold)
    static let wmtStruck      = Font.custom("Bogle-Regular",  size: 13).weight(.regular)
    static let wmtMeta        = Font.custom("Bogle-Regular",  size: 13).weight(.regular)
    static let wmtTag         = Font.custom("Bogle-Bold",     size: 12).weight(.bold)
    static let wmtTab         = Font.custom("Bogle-Semibold", size: 11).weight(.semibold)
    static let wmtCaption     = Font.custom("Bogle-Regular",  size: 11).weight(.regular)
}

// System fallback if Bogle is unavailable:
extension Font {
    static func wmt(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

Use tabular figures on price/quantity labels:

```swift
Text("$24.97").font(.wmtPriceLarge).monospacedDigit()
```

## 3. Signature Components

### Primary CTA — Add to Cart

```swift
struct WalmartCTAButton: View {
    let title: String
    var style: Style = .primary
    let action: () -> Void

    enum Style { case primary, secondary, deal }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.wmtButton)
                .foregroundStyle(fg)
                .frame(maxWidth: .infinity, minHeight: 48)
                .background(Capsule().fill(bg))
                .overlay(Capsule().strokeBorder(border, lineWidth: 1.5))
        }
        .sensoryFeedback(.impact(weight: .light), trigger: UUID())
        .buttonStyle(WalmartPressableStyle())
    }

    private var bg: Color {
        switch style { case .primary: .wmtBlue; case .secondary: .clear; case .deal: .wmtSpark }
    }
    private var fg: Color {
        switch style { case .primary: .white; case .secondary: .wmtBlue; case .deal: .wmtTextPrimary }
    }
    private var border: Color { style == .secondary ? .wmtBlue : .clear }
}

struct WalmartPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.easeOut(duration: 0.15), value: configuration.isPressed)
    }
}
```

### Rollback Price Tag (signature)

```swift
struct RollbackTag: View {
    enum Kind { case rollback, clearance, bestSeller }
    var kind: Kind = .rollback

    var body: some View {
        Text(label)
            .font(.wmtTag)
            .textCase(.uppercase)
            .kerning(0.2)
            .foregroundStyle(fg)
            .padding(.horizontal, 8)
            .padding(.vertical, 3)
            .background(RoundedRectangle(cornerRadius: 4).fill(bg))
    }

    private var label: String {
        switch kind { case .rollback: "Rollback"; case .clearance: "Clearance"; case .bestSeller: "Best seller" }
    }
    private var bg: Color {
        switch kind { case .rollback: .wmtSpark; case .clearance: .wmtError; case .bestSeller: .wmtBlueTint }
    }
    private var fg: Color {
        switch kind { case .rollback: .wmtTextPrimary; case .clearance: .white; case .bestSeller: .wmtBlue }
    }
}
```

### Price Block

```swift
struct PriceBlock: View {
    let price: String
    var wasPrice: String? = nil
    var savings: String? = nil
    var rollback: Bool = false

    var body: some View {
        VStack(alignment: .leading, spacing: 4) {
            if rollback { RollbackTag(kind: .rollback) }
            HStack(alignment: .firstTextBaseline, spacing: 8) {
                Text(price)
                    .font(.wmtPriceLarge)
                    .monospacedDigit()
                    .foregroundStyle(.wmtTextPrimary)
                if let was = wasPrice {
                    Text(was)
                        .font(.wmtStruck)
                        .strikethrough()
                        .foregroundStyle(.wmtTextSecondary)
                }
            }
            if let s = savings {
                Text(s)
                    .font(.wmtMeta.weight(.semibold))
                    .foregroundStyle(.wmtSavings)
            }
        }
    }
}
```

### Product Card

```swift
struct ProductCard: View {
    let title: String
    let price: String
    var wasPrice: String? = nil
    var rollback: Bool = false
    let rating: Int
    let onAdd: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            RoundedRectangle(cornerRadius: 8)
                .fill(Color.wmtSurfaceGray)
                .aspectRatio(1, contentMode: .fit)
            PriceBlock(price: price, wasPrice: wasPrice, rollback: rollback)
            Text(title)
                .font(.wmtProductTitle)
                .foregroundStyle(.wmtTextPrimary)
                .lineLimit(2)
            HStack(spacing: 2) {
                ForEach(0..<5) { i in
                    Image(systemName: i < rating ? "star.fill" : "star")
                        .font(.system(size: 11))
                        .foregroundStyle(.wmtStarGold)
                }
            }
            WalmartCTAButton(title: "Add to cart", action: onAdd)
                .frame(height: 40)
        }
        .padding(12)
        .background(
            RoundedRectangle(cornerRadius: 12)
                .fill(Color.wmtCanvas)
                .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.wmtDivider, lineWidth: 1))
        )
        .shadow(color: .black.opacity(0.06), radius: 3, y: 1)
    }
}
```

### Pickup / Delivery Toggle (signature)

```swift
struct FulfillmentToggle: View {
    @Binding var isDelivery: Bool
    @Namespace private var ns

    var body: some View {
        HStack(spacing: 0) {
            segment("Pickup",   selected: !isDelivery) { isDelivery = false }
            segment("Delivery", selected:  isDelivery) { isDelivery = true }
        }
        .padding(4)
        .background(Capsule().fill(Color.wmtSurfaceTint))
    }

    @ViewBuilder
    private func segment(_ label: String, selected: Bool, tap: @escaping () -> Void) -> some View {
        Button(action: { withAnimation(.easeInOut(duration: 0.22)) { tap() } }) {
            Text(label)
                .font(.wmtButton)
                .foregroundStyle(selected ? Color.wmtBlue : Color.wmtTextSecondary)
                .frame(maxWidth: .infinity, minHeight: 32)
                .background(
                    ZStack {
                        if selected {
                            Capsule().fill(Color.white)
                                .shadow(color: .black.opacity(0.10), radius: 6, y: 2)
                                .matchedGeometryEffect(id: "thumb", in: ns)
                        }
                    }
                )
        }
    }
}
```

### Quantity Stepper

```swift
struct QuantityStepper: View {
    @Binding var qty: Int

    var body: some View {
        HStack(spacing: 16) {
            Button { qty = max(0, qty - 1) } label: {
                Image(systemName: qty <= 1 ? "trash" : "minus")
                    .font(.system(size: 16, weight: .semibold))
                    .foregroundStyle(.wmtBlue)
            }
            Text("\(qty)").font(.wmtButton).monospacedDigit().foregroundStyle(.wmtTextPrimary)
            Button { qty += 1 } label: {
                Image(systemName: "plus")
                    .font(.system(size: 16, weight: .semibold))
                    .foregroundStyle(.wmtBlue)
            }
        }
        .padding(.horizontal, 14)
        .frame(height: 36)
        .overlay(Capsule().strokeBorder(Color.wmtBorder, lineWidth: 1))
    }
}
```

## 4. Spark Logo Mark

```swift
struct SparkMark: View {
    var size: CGFloat = 28
    var body: some View {
        ZStack {
            ForEach(0..<6) { i in
                Capsule()
                    .fill(Color.wmtSpark)
                    .frame(width: size * 0.16, height: size * 0.42)
                    .offset(y: -size * 0.30)
                    .rotationEffect(.degrees(Double(i) * 60))
            }
        }
        .frame(width: size, height: size)
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
        appearance.shadowColor = UIColor(Color.wmtDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            ShopView().tabItem { Label("Shop", systemImage: "house.fill") }
            ServicesView().tabItem { Label("Services", systemImage: "wrench.and.screwdriver.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            CartView().tabItem { Label("Cart", systemImage: "cart.fill") }
                .badge(3)
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle.fill") }
        }
        .tint(.wmtBlue) // active = Walmart Blue
    }
}
```

## 6. Motion

Use SwiftUI spring/easing defaults. Haptics via `.sensoryFeedback` (iOS 17+).

```swift
// Add-to-cart "bump"
struct CartBump: ViewModifier {
    @State private var s: CGFloat = 1
    let trigger: Int
    func body(content: Content) -> some View {
        content
            .scaleEffect(s)
            .onChange(of: trigger) { _, _ in
                withAnimation(.easeOut(duration: 0.1)) { s = 0.96 }
                withAnimation(.easeOut(duration: 0.08).delay(0.1)) { s = 1.04 }
                withAnimation(.spring(response: 0.2, dampingFraction: 0.6).delay(0.18)) { s = 1 }
            }
    }
}

// Add-to-cart soft haptic
.sensoryFeedback(.impact(weight: .light), trigger: cartCount)

// Subtotal roll on quantity change
.contentTransition(.numericText(value: Double(subtotal)))
.animation(.easeInOut(duration: 0.2), value: subtotal)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Add to cart / Cart tab | `cart` / `cart.fill` | 24pt |
| Shop (tab) | `house` / `house.fill` | 24pt |
| Services (tab) | `wrench.and.screwdriver.fill` | 24pt |
| Search | `magnifyingglass` | 18–24pt |
| Account (tab) | `person.crop.circle` / `.fill` | 24pt |
| Barcode scan | `barcode.viewfinder` | 22pt |
| Heart / list | `heart` / `heart.fill` | 24pt |
| Rating star | `star` / `star.fill` | 11–14pt |
| Quantity minus | `minus` | 16pt |
| Quantity remove | `trash` | 16pt |
| Quantity plus | `plus` | 16pt |
| Location chevron | `chevron.down` | 12pt |
| Fulfillment ETA | `clock` | 14pt |
| Success | `checkmark.circle.fill` | 18pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator(style: .light)`)
- Support Dynamic Type on product titles, prices, body — keep tab labels, Rollback tags, and stepper digits fixed
- VoiceOver: label the Add to cart button with the product ("Add Great Value Whole Milk to cart"); announce price and savings together; group the card's text and mark the CTA as a distinct button
- Contrast: dark charcoal `#2E2F32` on Spark Yellow `#FFC220` passes WCAG AA — never put white on yellow. `#74767C` secondary on white passes AA at 14pt+; validate 11pt captions
- Touch targets: keep quantity stepper buttons at 44pt effective hit area; card image and CTA are independently tappable
- Reduce Motion: replace the add-to-cart bump with a simple opacity confirmation when `accessibilityReduceMotion` is on
