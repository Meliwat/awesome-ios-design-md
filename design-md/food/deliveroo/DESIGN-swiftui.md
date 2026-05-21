# Deliveroo (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Deliveroo's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the restaurant feed and menu.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (the ONE color)
    static let rooTeal        = Color(red: 0.000, green: 0.800, blue: 0.737) // #00CCBC
    static let rooTealPressed = Color(red: 0.000, green: 0.663, blue: 0.612) // #00A99C
    static let rooTealInk     = Color(red: 0.000, green: 0.216, blue: 0.200) // #003733  (on-teal content)

    // MARK: - Membership & Promo
    static let rooPlusMint    = Color(red: 0.769, green: 0.957, blue: 0.937) // #C4F4EF
    static let rooPromoGold   = Color(red: 1.000, green: 0.757, blue: 0.000) // #FFC100

    // MARK: - Canvas & Surfaces (Light)
    static let rooCanvas        = Color.white                                   // #FFFFFF
    static let rooSurface1      = Color(red: 0.957, green: 0.957, blue: 0.949) // #F4F4F2
    static let rooSurface2      = Color(red: 0.918, green: 0.918, blue: 0.910) // #EAEAE8
    static let rooDivider       = Color(red: 0.910, green: 0.910, blue: 0.902) // #E8E8E6

    // MARK: - Canvas & Surfaces (Dark)
    static let rooDarkCanvas    = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let rooDarkSurface1  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let rooDarkSurface2  = Color(red: 0.149, green: 0.149, blue: 0.161) // #262629
    static let rooDarkDivider   = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E

    // MARK: - Text
    static let rooTextPrimary    = Color(red: 0.114, green: 0.114, blue: 0.106) // #1D1D1B
    static let rooTextSecondary  = Color(red: 0.420, green: 0.420, blue: 0.420) // #6B6B6B
    static let rooTextTertiary   = Color(red: 0.627, green: 0.627, blue: 0.627) // #A0A0A0
    static let rooDarkTextPrimary = Color(red: 0.957, green: 0.957, blue: 0.949) // #F4F4F2

    // MARK: - Semantic
    static let rooError       = Color(red: 0.886, green: 0.282, blue: 0.239) // #E2483D
}
```

## 2. Typography

Deliveroo's brand face is a custom heavy grotesque; the recommended free substitute is **Inter**. Bundle Inter TTFs via `Info.plist` / `UIAppFonts`. Prices/fees use tabular figures.

```swift
extension Font {
    static let rooScreenTitle    = Font.custom("Inter-Black",     size: 32) // 900
    static let rooRestaurantHero = Font.custom("Inter-ExtraBold", size: 26) // 800
    static let rooSection        = Font.custom("Inter-ExtraBold", size: 22)
    static let rooSubsection     = Font.custom("Inter-ExtraBold", size: 20)
    static let rooRestaurantName = Font.custom("Inter-ExtraBold", size: 16)
    static let rooMenuName       = Font.custom("Inter-Bold",      size: 15)
    static let rooBody           = Font.custom("Inter-Medium",    size: 15)
    static let rooPrice          = Font.custom("Inter-ExtraBold", size: 14)
    static let rooMeta           = Font.custom("Inter-Regular",   size: 14)
    static let rooFeePill        = Font.custom("Inter-Bold",      size: 13)
    static let rooBadge          = Font.custom("Inter-ExtraBold", size: 12)
    static let rooButton         = Font.custom("Inter-ExtraBold", size: 16)
    static let rooTab            = Font.custom("Inter-Bold",      size: 10)
    static let rooCaption        = Font.custom("Inter-SemiBold",  size: 12)
}

extension View {
    func rooTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Restaurant Card

```swift
struct RestaurantCard: View {
    let name: String
    let meta: String
    let rating: Double
    let fee: String          // "£1.49 delivery · Min £12"
    let badge: String?       // "PLUS · Free delivery" or promo
    let badgeIsPromo: Bool
    let imageName: String
    @State private var saved = false

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .topLeading) {
                Image(imageName)
                    .resizable()
                    .aspectRatio(5/3, contentMode: .fill)
                    .clipped()
                    .clipShape(RoundedRectangle(cornerRadius: 14))

                if let badge {
                    Text(badge)
                        .font(.rooBadge)
                        .foregroundStyle(badgeIsPromo ? Color(red: 0.102, green: 0.071, blue: 0.024) : Color.rooTealInk)
                        .padding(.horizontal, 9).padding(.vertical, 4)
                        .background(badgeIsPromo ? Color.rooPromoGold : Color.rooTeal,
                                    in: RoundedRectangle(cornerRadius: 6))
                        .padding(10)
                }

                Button { saved.toggle() } label: {
                    Image(systemName: saved ? "heart.fill" : "heart")
                        .font(.system(size: 16, weight: .semibold))
                        .foregroundStyle(saved ? Color.rooTeal : .white)
                        .frame(width: 32, height: 32)
                        .background(Color.black.opacity(0.4), in: Circle())
                }
                .frame(maxWidth: .infinity, alignment: .trailing)
                .padding(10)
            }

            HStack {
                Text(name).font(.rooRestaurantName).foregroundStyle(Color.rooTextPrimary)
                Spacer()
                HStack(spacing: 4) {
                    Image(systemName: "star.fill").font(.system(size: 13)).foregroundStyle(Color.rooTeal)
                    Text(String(format: "%.1f", rating)).font(.rooFeePill).foregroundStyle(Color.rooTextPrimary)
                }
            }
            .padding(.top, 10)

            Text(meta).font(.rooMeta).foregroundStyle(Color.rooTextSecondary).padding(.top, 4)

            FeePill(text: fee).padding(.top, 8)
        }
        .padding(.bottom, 22)
    }
}

struct FeePill: View {
    let text: String
    var body: some View {
        HStack(spacing: 5) {
            Image(systemName: "bicycle").font(.system(size: 11, weight: .bold)).foregroundStyle(Color.rooTeal)
            Text(text).font(.rooFeePill).foregroundStyle(Color.rooTextPrimary)
        }
        .padding(.horizontal, 10).padding(.vertical, 5)
        .background(Color.rooSurface1, in: Capsule())
    }
}
```

### Menu Item Row (with floating teal +)

```swift
struct MenuItemRow: View {
    let name: String
    let desc: String
    let price: String
    let imageName: String
    let onAdd: () -> Void

    var body: some View {
        HStack(alignment: .top, spacing: 14) {
            VStack(alignment: .leading, spacing: 4) {
                Text(name).font(.rooMenuName).foregroundStyle(Color.rooTextPrimary)
                Text(desc)
                    .font(.rooBody.weight(.regular))
                    .foregroundStyle(Color.rooTextSecondary)
                    .lineLimit(2)
                Text(price).font(.rooPrice).foregroundStyle(Color.rooTextPrimary).padding(.top, 4)
            }
            Spacer(minLength: 8)
            ZStack(alignment: .bottomTrailing) {
                Image(imageName)
                    .resizable().scaledToFill()
                    .frame(width: 84, height: 84)
                    .clipShape(RoundedRectangle(cornerRadius: 12))
                Button(action: onAdd) {
                    Image(systemName: "plus")
                        .font(.system(size: 16, weight: .bold))
                        .foregroundStyle(Color.rooTealInk)
                        .frame(width: 28, height: 28)
                        .background(Color.rooTeal, in: Circle())
                        .shadow(color: .black.opacity(0.18), radius: 3, y: 2)
                }
                .offset(x: 8, y: 10)   // hangs off the corner
            }
        }
        .padding(.vertical, 14)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.rooDivider).frame(height: 1) }
    }
}
```

### Quantity Stepper

```swift
struct QuantityStepper: View {
    @Binding var quantity: Int

    var body: some View {
        HStack(spacing: 14) {
            control("minus") { if quantity > 1 { quantity -= 1 } }
                .opacity(quantity > 1 ? 1 : 0.35)
            Text("\(quantity)").font(.rooButton).rooTabular()
                .foregroundStyle(Color.rooTextPrimary)
                .frame(minWidth: 18)
                .contentTransition(.numericText())
            control("plus") { quantity += 1 }
        }
        .sensoryFeedback(.selection, trigger: quantity)
    }

    private func control(_ symbol: String, _ action: @escaping () -> Void) -> some View {
        Button(action: action) {
            Image(systemName: symbol)
                .font(.system(size: 16, weight: .bold))
                .foregroundStyle(Color.rooTealInk)
                .frame(width: 32, height: 32)
                .background(Color.rooTeal, in: Circle())
        }
    }
}
```

### Sticky Basket Bar

```swift
struct BasketBar: View {
    let itemCount: Int
    let total: String
    let onTap: () -> Void
    @State private var pulse = false

    var body: some View {
        Button(action: onTap) {
            Text("View basket · \(itemCount) items · \(total)")
                .font(.rooButton)
                .foregroundStyle(Color.rooTealInk)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(Color.rooTeal, in: Capsule())
        }
        .buttonStyle(.plain)
        .scaleEffect(pulse ? 1.03 : 1.0)
        .padding(.horizontal, 16)
        .onChange(of: itemCount) { _, _ in
            withAnimation(.easeOut(duration: 0.1)) { pulse = true }
            withAnimation(.easeOut(duration: 0.1).delay(0.1)) { pulse = false }
        }
    }
}
```

### Category Icon Row

```swift
struct CategoryRow: View {
    struct Cat: Identifiable { let id = UUID(); let symbol: String; let label: String }
    let cats: [Cat]
    @State private var selected = 0

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 18) {
                ForEach(Array(cats.enumerated()), id: \.element.id) { i, c in
                    VStack(spacing: 7) {
                        Image(systemName: c.symbol)
                            .font(.system(size: 24, weight: .medium))
                            .foregroundStyle(i == selected ? Color.rooTealInk : Color.rooTeal)
                            .frame(width: 54, height: 54)
                            .background(i == selected ? Color.rooTeal : Color.rooSurface1,
                                        in: RoundedRectangle(cornerRadius: 16))
                        Text(c.label)
                            .font(.rooCaption.weight(.semibold))
                            .foregroundStyle(i == selected ? Color.rooTextPrimary : Color.rooTextSecondary)
                    }
                    .onTapGesture { withAnimation(.easeOut(duration: 0.15)) { selected = i } }
                }
            }
            .padding(.horizontal, 16)
        }
    }
}
```

### Primary Button

```swift
struct RooButton: View {
    let title: String
    let action: () -> Void
    var style: Style = .teal
    enum Style { case teal, dark }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(style == .teal ? .rooButton : .rooButton)
                .foregroundStyle(style == .teal ? Color.rooTealInk : .white)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(style == .teal ? Color.rooTeal : Color.rooTextPrimary, in: Capsule())
        }
        .buttonStyle(.plain)
    }
}
// Checkout: RooButton(title: "Checkout · £24.50", action: {}, style: .dark)
```

## 4. Bottom Tab Bar

```swift
struct DeliverooTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem      { Label("Home",       systemImage: "house.fill") }
            SearchView().tabItem    { Label("Search",     systemImage: "magnifyingglass") }
            OrdersView().tabItem    { Label("Orders",     systemImage: "list.bullet.rectangle") }
            FavouritesView().tabItem{ Label("Favourites", systemImage: "heart") }
            AccountView().tabItem   { Label("Account",    systemImage: "person.crop.circle") }
        }
        .tint(.rooTeal)   // active tab is Deliveroo Teal
    }
}
```

## 5. Motion

```swift
// Floating + add → morph to stepper, basket slides up
withAnimation(.easeOut(duration: 0.18)) { plusScale = 0.9 }   // then back to 1.0
withAnimation(.easeOut(duration: 0.28)) { basketVisible = true }

// Quantity stepper
.contentTransition(.numericText())
.sensoryFeedback(.selection, trigger: quantity)

// Basket bar pulse on add
withAnimation(.easeOut(duration: 0.1)) { pulse = true }   // 1 → 1.03 → 1

// Category tile select
withAnimation(.easeOut(duration: 0.15)) { selected = i }

// Card → menu: matchedGeometryEffect on the hero photo, 320ms push

// Order tracking stage advance
withAnimation(.easeOut(duration: 0.25)) { stageIndex += 1 }
.sensoryFeedback(.impact(weight: .medium), trigger: stageIndex)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Orders (tab) | `list.bullet.rectangle` | 22pt |
| Favourites (tab) | `heart` / `heart.fill` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Saved heart (card) | `heart` / `heart.fill` | 16pt |
| Rating star | `star.fill` | 13pt |
| Address chevron | `chevron.down` | 12pt |
| Basket | `bag` / `bag.fill` | 16pt |
| Fee pill (moped) | `bicycle` / `scooter` | 11pt |
| Floating add | `plus` | 16pt |
| Stepper minus | `minus` | 16pt |
| Category — Restaurants | `fork.knife` | 24pt |
| Category — Grocery | `cart` | 24pt |
| Category — Fast | `clock` | 24pt |
| Category — Treats | `birthday.cake` | 24pt |
| Back | `chevron.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Rider (tracking) | `scooter` | 22pt |

## 7. Dark Mode

```swift
struct DeliverooTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.rooDarkCanvas : Color.rooCanvas)
            .foregroundStyle(scheme == .dark ? Color.rooDarkTextPrimary : Color.rooTextPrimary)
    }
}

extension View {
    func deliverooTheme() -> some View { modifier(DeliverooTheme()) }
}
```

Deliveroo Teal and Teal Ink are identical across light and dark — only canvas, surfaces, dividers and text invert. The "on-teal" content stays `#003733` in both modes. Food photography is never dimmed. Floating surfaces add a 0.5pt `rooDarkDivider` border in dark mode as the elevation cue (shadows fade on dark).

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` require iOS 17 — fall back to `UIImpactFeedbackGenerator` and a manual cross-fade on iOS 16)
- Bundle Inter TTFs via `Info.plist` (`UIAppFonts`) — Inter is SIL OFL, free to distribute. Substitute for Deliveroo's proprietary brand face
- Dynamic Type: support on screen titles, restaurant names, body and prices; keep Plus/offer badges, the fee pill, tab labels and category labels at FIXED sizes
- Tabular numerals: apply `.monospacedDigit()` to all prices, fees, minimums and quantity counters
- VoiceOver: label restaurant cards as "{name}, {rating} stars, {meta}, {fee}"; the saved heart is a toggle; the floating `+` is "Add {item name}" and announces "Added, quantity 1" after tap
- The sticky basket bar should post a VoiceOver announcement on change ("Basket, 2 items, £24.50")
- Color contrast: `#1D1D1B` on `#FFFFFF` passes WCAG AAA; **Teal Ink `#003733` on `#00CCBC` passes AA** for button labels (white on teal would NOT — this is exactly why Deliveroo uses the dark ink)
- Reduce Motion: disable the `+` morph bounce and the basket-bar pulse (cross-fade instead); keep stage advance instant
- Dark mode: invert to the `rooDark*` palette — warm `#121212`, NOT true black; add a 0.5pt divider border to floating surfaces (basket bar, customize sheet, floating `+` container) since shadows are barely visible on dark
