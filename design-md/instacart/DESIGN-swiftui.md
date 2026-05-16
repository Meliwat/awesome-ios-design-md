# Instacart (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Instacart's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let icCanvas   = Color.white                                   // #FFFFFF
    static let icSurface  = Color(red: 0.965, green: 0.969, blue: 0.973)  // #F6F7F8
    static let icDivider  = Color(red: 0.910, green: 0.914, blue: 0.922)  // #E8E9EB

    // MARK: - Text
    static let icTextPrimary   = Color(red: 0.141, green: 0.145, blue: 0.161) // #242529
    static let icTextSecondary = Color(red: 0.447, green: 0.459, blue: 0.494) // #72757E
    static let icTextTertiary  = Color(red: 0.651, green: 0.659, blue: 0.682) // #A6A8AE

    // MARK: - Brand
    static let icGreen        = Color(red: 0.039, green: 0.678, blue: 0.039) // #0AAD0A
    static let icGreenPressed = Color(red: 0.035, green: 0.561, blue: 0.035) // #098F09
    static let icGreenTint    = Color(red: 0.902, green: 0.965, blue: 0.902) // #E6F6E6

    // MARK: - Deal / Semantic
    static let icCarrot     = Color(red: 1.0,   green: 0.439, blue: 0.035) // #FF7009
    static let icCarrotTint = Color(red: 1.0,   green: 0.941, blue: 0.902) // #FFF0E6
    static let icError      = Color(red: 0.839, green: 0.125, blue: 0.227) // #D6203A
}
```

## 2. Typography

Instacart uses Inter. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to `.system(design: .default)`.

```swift
extension Font {
    static let icScreenTitle  = Font.custom("Inter-Bold",     size: 26)
    static let icSection      = Font.custom("Inter-Bold",     size: 20)
    static let icStoreName    = Font.custom("Inter-Bold",     size: 17)
    static let icItemTitle    = Font.custom("Inter-Regular",  size: 15)
    static let icPrice        = Font.custom("Inter-Bold",     size: 15)
    static let icBody         = Font.custom("Inter-Regular",  size: 15)
    static let icButton       = Font.custom("Inter-Bold",     size: 16)
    static let icStepperValue = Font.custom("Inter-Bold",     size: 16)
    static let icMeta         = Font.custom("Inter-Regular",  size: 13)
    static let icBadge        = Font.custom("Inter-Bold",     size: 12)
    static let icTab          = Font.custom("Inter-SemiBold", size: 11)
    static let icCaption      = Font.custom("Inter-Regular",  size: 11)
}

// System fallback if Inter is unavailable:
extension Font {
    static func ic(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

Prices and quantities use tabular figures — apply `.monospacedDigit()` on any numeric `Text`.

## 3. Signature Components

### Quantity Stepper (− n +)

```swift
struct QuantityStepper: View {
    @Binding var quantity: Int
    var onChange: (Int) -> Void = { _ in }

    var body: some View {
        HStack(spacing: 0) {
            stepButton(symbol: "minus") {
                if quantity > 0 { quantity -= 1; onChange(quantity) }
            }
            Text("\(quantity)")
                .font(.icStepperValue)
                .monospacedDigit()
                .foregroundStyle(.white)
                .frame(minWidth: 28)
            stepButton(symbol: "plus") {
                quantity += 1; onChange(quantity)
            }
        }
        .frame(height: 32)
        .background(Capsule().fill(Color.icGreen))
        .sensoryFeedback(.impact(weight: .light), trigger: quantity)
    }

    private func stepButton(symbol: String, action: @escaping () -> Void) -> some View {
        Button(action: action) {
            Image(systemName: symbol)
                .font(.system(size: 13, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: 36, height: 32) // 44pt hit via contentShape
                .contentShape(Rectangle())
        }
        .buttonStyle(.plain)
    }
}
```

### Add Button → Stepper Morph

```swift
struct AddOrStepper: View {
    @State private var quantity = 0

    var body: some View {
        ZStack {
            if quantity == 0 {
                Button {
                    withAnimation(.spring(response: 0.25, dampingFraction: 0.85)) { quantity = 1 }
                } label: {
                    Text("Add")
                        .font(.custom("Inter-Bold", size: 14))
                        .foregroundStyle(Color.icGreen)
                        .padding(.horizontal, 20)
                        .frame(height: 32)
                        .background(Capsule().fill(Color.icCanvas))
                        .overlay(Capsule().strokeBorder(Color.icGreen, lineWidth: 1.5))
                }
                .buttonStyle(.plain)
                .transition(.scale.combined(with: .opacity))
            } else {
                QuantityStepper(quantity: $quantity)
                    .transition(.scale.combined(with: .opacity))
            }
        }
        .animation(.spring(response: 0.25, dampingFraction: 0.85), value: quantity == 0)
    }
}
```

### Persistent Green Cart Bar

```swift
struct CartBar: View {
    let itemCount: Int
    let subtotal: Decimal
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack {
                Text("\(itemCount) items")
                    .font(.custom("Inter-Bold", size: 13))
                    .monospacedDigit()
                    .foregroundStyle(Color.icGreen)
                    .padding(.horizontal, 12).padding(.vertical, 6)
                    .background(Capsule().fill(.white))

                Spacer()

                Text("View cart  \(subtotal, format: .currency(code: "USD"))")
                    .font(.icButton)
                    .monospacedDigit()
                    .foregroundStyle(.white)
            }
            .padding(.horizontal, 16)
            .frame(height: 56)
            .background(RoundedRectangle(cornerRadius: 12).fill(Color.icGreen))
        }
        .buttonStyle(ICPressableStyle(pressedScale: 0.99))
        .padding(.horizontal, 16)
        .transition(.move(edge: .bottom).combined(with: .opacity))
    }
}

struct ICPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### Store Card Row

```swift
struct StoreRow: View {
    let logo: Image
    let name: String
    let eta: String
    let deliveryFee: String

    var body: some View {
        HStack(spacing: 12) {
            logo
                .resizable().scaledToFill()
                .frame(width: 56, height: 56)
                .clipShape(RoundedRectangle(cornerRadius: 12))
            VStack(alignment: .leading, spacing: 4) {
                Text(name).font(.icStoreName).foregroundStyle(Color.icTextPrimary)
                Text("\(eta) · \(deliveryFee) delivery")
                    .font(.icMeta).foregroundStyle(Color.icTextSecondary)
            }
            Spacer()
            Image(systemName: "chevron.right")
                .font(.system(size: 14, weight: .semibold))
                .foregroundStyle(Color.icTextTertiary)
        }
        .padding(.vertical, 16).padding(.horizontal, 16)
        .contentShape(Rectangle())
    }
}
```

### Product Card

```swift
struct ProductCard: View {
    let photo: Image
    let price: String
    let unitPrice: String
    let title: String
    let dealFlag: String?

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            ZStack(alignment: .topLeading) {
                photo
                    .resizable().scaledToFill()
                    .aspectRatio(1, contentMode: .fill)
                    .clipShape(RoundedRectangle(cornerRadius: 12))
                if let dealFlag {
                    Text(dealFlag)
                        .font(.icBadge)
                        .foregroundStyle(.white)
                        .padding(.horizontal, 8).padding(.vertical, 4)
                        .background(Capsule().fill(Color.icCarrot))
                        .padding(8)
                }
            }
            Text(price).font(.icPrice).monospacedDigit().foregroundStyle(Color.icTextPrimary)
            Text(unitPrice).font(.icMeta).foregroundStyle(Color.icTextSecondary)
            Text(title)
                .font(.icItemTitle).foregroundStyle(Color.icTextPrimary)
                .lineLimit(2)
        }
    }
}
```

### Replacement-Preference Radio Row

```swift
struct ReplacementOption: View {
    let label: String
    let isSelected: Bool
    let tap: () -> Void

    var body: some View {
        Button(action: tap) {
            HStack {
                Text(label).font(.icBody).foregroundStyle(Color.icTextPrimary)
                Spacer()
                ZStack {
                    Circle().strokeBorder(isSelected ? Color.icGreen : Color.icDivider, lineWidth: 1.5)
                        .frame(width: 22, height: 22)
                    if isSelected {
                        Circle().fill(Color.icGreen).frame(width: 12, height: 12)
                            .transition(.scale)
                    }
                }
            }
            .padding(16)
            .background(
                RoundedRectangle(cornerRadius: 12)
                    .strokeBorder(isSelected ? Color.icGreen : Color.icDivider, lineWidth: 1.5)
            )
        }
        .buttonStyle(.plain)
        .animation(.spring(response: 0.2, dampingFraction: 0.7), value: isSelected)
    }
}
```

## 4. Add-to-Cart Fly-Up

```swift
// Drive a ghost thumbnail along an arced path toward the cart bar, then bounce the badge.
struct FlyToCart: ViewModifier {
    @Binding var trigger: Bool
    let endPoint: CGPoint   // cart-bar anchor in global space

    func body(content: Content) -> some View {
        content.overlay(alignment: .topTrailing) {
            if trigger {
                Circle().fill(Color.icGreen.opacity(0.0)) // placeholder; swap for product image
                    .frame(width: 36, height: 36)
                    .modifier(ArcMove(to: endPoint))
            }
        }
    }
}

// Use a keyframe/path animation (iOS 17 KeyframeAnimator) to arc the thumbnail,
// then animate the cart badge scale 1.0 → 1.2 → 1.0 with .spring on completion.
```

## 5. Tab Bar

Use `TabView` with a white `UITabBarAppearance` and a green active tint.

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(Color.icDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            StoresView().tabItem { Label("Stores", systemImage: "storefront") }
            BuyAgainView().tabItem { Label("Buy it again", systemImage: "arrow.clockwise") }
            ListsView().tabItem { Label("Lists", systemImage: "list.bullet") }
            AccountView().tabItem { Label("Account", systemImage: "person") }
            CartView().tabItem { Label("Cart", systemImage: "cart") }
                .badge(8)
        }
        .tint(.icGreen) // active = Instacart Green
    }
}
```

## 6. Motion

```swift
// Stepper increment / decrement
.sensoryFeedback(.impact(weight: .light), trigger: quantity)

// Add pill → stepper morph
withAnimation(.spring(response: 0.25, dampingFraction: 0.85)) { quantity = 1 }

// Cart bar entrance (first add)
.transition(.move(edge: .bottom).combined(with: .opacity))
// with .spring(response: 0.35, dampingFraction: 0.8)

// Replacement radio select
.animation(.spring(response: 0.2, dampingFraction: 0.7), value: isSelected)

// Add-to-cart fly-up: KeyframeAnimator arcing a ghost thumbnail (~450ms ease-out),
// then cart badge scale 1.0 → 1.2 → 1.0
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Stepper minus | `minus` | 13pt |
| Stepper plus | `plus` | 13pt |
| Stores (tab) | `storefront` | 24pt |
| Buy it again (tab) | `arrow.clockwise` | 24pt |
| Lists (tab) | `list.bullet` | 24pt |
| Account (tab) | `person` / `person.fill` | 24pt |
| Cart (tab) | `cart` / `cart.fill` | 24pt |
| Search | `magnifyingglass` | 18pt |
| Store row chevron | `chevron.right` | 14pt |
| Save / heart | `heart` / `heart.fill` | 22pt |
| Filter | `slider.horizontal.3` | 20pt |
| Deal / tag | `tag.fill` | 14pt |
| Replacement / swap | `arrow.triangle.2.circlepath` | 16pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator(style: .light)`)
- Support Dynamic Type on product titles, store names, body — keep prices tabular and right-aligned; pin tab labels and deal badges
- VoiceOver: the stepper should announce "Quantity, 3, stepper"; expose the "+"/"−" as adjustable actions or an `accessibilityAdjustableAction`
- The "Add" pill needs `accessibilityLabel("Add \(productName) to cart")`; the cart bar announces "View cart, 8 items, $64.20"
- Contrast: `#72757E` secondary on `#FFFFFF` passes WCAG AA at 13pt+; carrot orange `#FF7009` text on white is decorative — keep sale prices also bold and pair with a "Sale" label for non-color cues
- Replacement radio rows: mark the selected option with `.accessibilityAddTraits(.isSelected)`
