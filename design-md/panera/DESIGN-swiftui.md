# Panera (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Panera's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let panCanvas        = Color.white                                   // #FFFFFF
    static let panCream         = Color(red: 0.957, green: 0.937, blue: 0.882) // #F4EFE1
    static let panOat           = Color(red: 0.980, green: 0.965, blue: 0.925) // #FAF6EC
    static let panSurfacePressed = Color(red: 0.929, green: 0.902, blue: 0.831) // #EDE6D4
    static let panDivider       = Color(red: 0.906, green: 0.878, blue: 0.812) // #E7E0CF

    // MARK: - Canvas & Surfaces (Dark)
    static let panDarkCanvas    = Color(red: 0.078, green: 0.078, blue: 0.059) // #14140F
    static let panDarkSurface1  = Color(red: 0.118, green: 0.118, blue: 0.094) // #1E1E18
    static let panDarkSurface2  = Color(red: 0.157, green: 0.157, blue: 0.122) // #28281F
    static let panDarkDivider   = Color(red: 0.200, green: 0.200, blue: 0.165) // #33332A

    // MARK: - Text
    static let panTextPrimary    = Color(red: 0.165, green: 0.165, blue: 0.122) // #2A2A1F
    static let panTextSecondary  = Color(red: 0.424, green: 0.400, blue: 0.333) // #6C6655
    static let panTextTertiary   = Color(red: 0.612, green: 0.588, blue: 0.518) // #9C9684
    static let panDarkTextPrimary = Color(red: 0.953, green: 0.937, blue: 0.894) // #F3EFE4
    static let panDarkTextSecondary = Color(red: 0.706, green: 0.682, blue: 0.612) // #B4AE9C

    // MARK: - Brand
    static let panGreen          = Color(red: 0.298, green: 0.545, blue: 0.169) // #4C8B2B
    static let panGreenBright    = Color(red: 0.420, green: 0.745, blue: 0.271) // #6BBE45
    static let panGreenPressed   = Color(red: 0.235, green: 0.431, blue: 0.133) // #3C6E22
    static let panGreenTint      = Color(red: 0.918, green: 0.953, blue: 0.890) // #EAF3E3
    static let panGreenTintDark  = Color(red: 0.118, green: 0.165, blue: 0.094) // #1E2A18

    // MARK: - Photographic Accents
    static let panBreadTan       = Color(red: 0.847, green: 0.698, blue: 0.443) // #D8B271
    static let panSoupOrange     = Color(red: 0.878, green: 0.478, blue: 0.184) // #E07A2F
    static let panBerry          = Color(red: 0.690, green: 0.196, blue: 0.294) // #B0324B
    static let panGoldStar       = Color(red: 0.949, green: 0.718, blue: 0.020) // #F2B705
    static let panSipCoffee      = Color(red: 0.353, green: 0.227, blue: 0.133) // #5A3A22

    // MARK: - Semantic
    static let panError          = Color(red: 0.839, green: 0.271, blue: 0.184) // #D6452F
    static let panErrorDark      = Color(red: 0.878, green: 0.349, blue: 0.294) // #E0594B
}
```

## 2. Typography

Panera pairs a warm geometric display sans (Poppins, the closest free analog to its marketing face) with a neutral body sans (Inter). Bundle both via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    // Display / Brand — Poppins
    static let panScreenTitle = Font.custom("Poppins-ExtraBold", size: 32)
    static let panHero        = Font.custom("Poppins-Bold",      size: 26)
    static let panSection     = Font.custom("Poppins-Bold",      size: 22)
    static let panCardTitle   = Font.custom("Poppins-SemiBold",  size: 18)
    static let panItemName    = Font.custom("Poppins-SemiBold",  size: 15)
    static let panPrice       = Font.custom("Poppins-Bold",      size: 14)
    static let panRewardTag   = Font.custom("Poppins-Bold",      size: 12)
    static let panButton      = Font.custom("Poppins-Bold",      size: 16)

    // Body / UI — Inter
    static let panBody        = Font.custom("Inter-Regular",  size: 16)
    static let panMeta        = Font.custom("Inter-Regular",  size: 14)
    static let panPill        = Font.custom("Inter-SemiBold", size: 13)
    static let panTab         = Font.custom("Inter-SemiBold", size: 10)
    static let panCaption     = Font.custom("Inter-Medium",   size: 12)
}
```

## 3. Signature Components

### MyPanera Rewards Card

```swift
struct MyPaneraCard: View {
    let headline: String
    let subtext: String
    let progress: Double   // 0...1

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack {
                Text("MYPANERA REWARDS")
                    .font(.custom("Poppins-Bold", size: 11))
                    .tracking(0.6)
                    .foregroundStyle(.white.opacity(0.92))
                Spacer()
                Image(systemName: "chevron.right")
                    .font(.system(size: 13, weight: .semibold))
                    .foregroundStyle(.white.opacity(0.9))
            }
            Text(headline)
                .font(.custom("Poppins-Bold", size: 19))
                .foregroundStyle(.white)
            Text(subtext)
                .font(.panCaption)
                .foregroundStyle(.white.opacity(0.92))
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.white.opacity(0.28))
                    Capsule().fill(Color.white)
                        .frame(width: geo.size.width * progress)
                }
            }
            .frame(height: 6)
            .padding(.top, 4)
        }
        .padding(16)
        .background(
            RoundedRectangle(cornerRadius: 18)
                .fill(LinearGradient(colors: [.panGreen, .panGreenBright],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
        )
        .shadow(color: Color(red: 0.235, green: 0.157, blue: 0.039).opacity(0.06), radius: 8, y: 2)
    }
}
```

### Menu Item Row

```swift
struct MenuItemRow: View {
    let imageName: String
    let name: String
    let desc: String
    let price: String
    let rewardTag: String?
    let onAdd: () -> Void

    var body: some View {
        HStack(alignment: .top, spacing: 14) {
            Image(imageName)
                .resizable().aspectRatio(contentMode: .fill)
                .frame(width: 92, height: 92)
                .clipShape(RoundedRectangle(cornerRadius: 14))

            VStack(alignment: .leading, spacing: 3) {
                Text(name).font(.panItemName).foregroundStyle(Color.panTextPrimary)
                Text(desc).font(.custom("Inter-Regular", size: 12))
                    .foregroundStyle(Color.panTextSecondary)
                    .lineLimit(2)
                if let rewardTag {
                    Text("★ \(rewardTag)")
                        .font(.panRewardTag).tracking(0.4)
                        .foregroundStyle(Color.panGreen)
                        .padding(.top, 4)
                }
                HStack {
                    Text(price).font(.panPrice).foregroundStyle(Color.panTextPrimary)
                    Spacer()
                    Button(action: onAdd) {
                        Image(systemName: "plus")
                            .font(.system(size: 15, weight: .bold))
                            .foregroundStyle(.white)
                            .frame(width: 30, height: 30)
                            .background(Circle().fill(Color.panGreen))
                    }
                }
                .padding(.top, 8)
            }
        }
        .padding(.vertical, 14)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.panDivider).frame(height: 0.5)
        }
    }
}
```

### Quantity Stepper

```swift
struct QuantityStepper: View {
    @Binding var count: Int

    var body: some View {
        HStack(spacing: 16) {
            Button { if count > 1 { count -= 1 } } label: {
                Text("−").font(.custom("Poppins-Bold", size: 16)).foregroundStyle(Color.panGreen)
                    .frame(width: 44, height: 44)
            }
            Text("\(count)").font(.custom("Poppins-Bold", size: 15))
                .foregroundStyle(Color.panTextPrimary).frame(minWidth: 16)
            Button { count += 1 } label: {
                Text("+").font(.custom("Poppins-Bold", size: 16)).foregroundStyle(Color.panGreen)
                    .frame(width: 44, height: 44)
            }
        }
        .padding(.horizontal, 8)
        .background(Capsule().fill(Color.panOat))
        .overlay(Capsule().strokeBorder(Color.panDivider, lineWidth: 1))
        .sensoryFeedback(.impact(flexibility: .soft), trigger: count)
    }
}
```

### Sticky "Add to Order" Bar

```swift
struct AddToOrderBar: View {
    let priceLabel: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text("Add to Order · \(priceLabel)")
                .font(.panButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 16)
                .background(Capsule().fill(Color.panGreen))
        }
        .buttonStyle(.plain)
        .padding(.horizontal, 16)
        .padding(.vertical, 10)
        .background(.ultraThinMaterial)
        .shadow(color: Color(red: 0.235, green: 0.157, blue: 0.039).opacity(0.10), radius: 16, y: 4)
        .sensoryFeedback(.impact(flexibility: .soft), trigger: priceLabel)
    }
}
```

### Pickup / Delivery Toggle

```swift
struct PickupDeliveryToggle: View {
    @Binding var isPickup: Bool

    var body: some View {
        HStack(spacing: 0) {
            segment("Pickup", selected: isPickup) { isPickup = true }
            segment("Delivery", selected: !isPickup) { isPickup = false }
        }
        .padding(4)
        .background(Capsule().fill(Color.panOat))
        .overlay(Capsule().strokeBorder(Color.panDivider, lineWidth: 1))
    }

    @ViewBuilder private func segment(_ title: String, selected: Bool, _ tap: @escaping () -> Void) -> some View {
        Text(title)
            .font(.panPill)
            .foregroundStyle(selected ? .white : Color.panTextSecondary)
            .padding(.vertical, 10).frame(maxWidth: .infinity)
            .background(Capsule().fill(selected ? Color.panGreen : .clear))
            .contentShape(Rectangle())
            .onTapGesture(perform: tap)
    }
}
```

### Reward / Loyalty Chip

```swift
struct RewardChip: View {
    let text: String
    var body: some View {
        HStack(spacing: 8) {
            Circle()
                .fill(LinearGradient(colors: [.panGreenBright, .panGreen],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
                .frame(width: 22, height: 22)
            Text(text).font(.custom("Poppins-Bold", size: 13)).foregroundStyle(Color.panTextPrimary)
        }
        .padding(.vertical, 8).padding(.leading, 10).padding(.trailing, 16)
        .background(Capsule().fill(Color.panOat))
        .overlay(Capsule().strokeBorder(Color.panDivider, lineWidth: 1))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct PaneraTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            OrderView().tabItem { Label("Order", systemImage: "bag.fill") }
            RewardsView().tabItem { Label("Rewards", systemImage: "star.circle.fill") }
            FavoritesView().tabItem { Label("Favorites", systemImage: "bookmark.fill") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle") }
        }
        .tint(.panGreen)   // active green; bright variant in dark mode via asset/scheme
    }
}
```

## 5. Motion

```swift
// Add to Order — scale-bounce + cart badge increment
withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { addedPulse.toggle() }
.sensoryFeedback(.impact(flexibility: .soft), trigger: cartCount)

// Rewards progress fill (earned reward)
withAnimation(.easeOut(duration: 0.6)) { progress = newValue }

// Category underline slide
withAnimation(.easeOut(duration: 0.2)) { selectedCategory = idx }

// Item detail open — iOS push (matchedGeometryEffect for shared photo)
@Namespace private var heroNS
// .matchedGeometryEffect(id: item.id, in: heroNS)

// Cart sheet
.sheet(isPresented: $showCart) { CartSheet().presentationDetents([.medium, .large]) }

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: stepperCount)
.sensoryFeedback(.success, trigger: rewardUnlocked)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Order (tab) | `bag` / `bag.fill` | 22pt |
| Rewards (tab) | `star.circle` / `star.circle.fill` | 22pt |
| Favorites (tab) | `bookmark` / `bookmark.fill` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Add to order | `plus` | 15pt |
| Stepper minus/plus | `minus` / `plus` | 16pt |
| Back | `chevron.left` | 18pt |
| Favorite (heart) | `heart` / `heart.fill` | 20pt |
| Share | `square.and.arrow.up` | 20pt |
| Reward star | `star.fill` | 14pt |
| Sip Club | `cup.and.saucer.fill` | 18pt |
| Pickup / location | `mappin.and.ellipse` | 18pt |
| Delivery | `bicycle` | 18pt |
| Calories / info | `flame.fill` | 14pt |
| Search | `magnifyingglass` | 16pt |
| Disclosure | `chevron.right` | 13pt |

## 7. Dark Mode

```swift
struct PaneraTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.panDarkCanvas : Color.panCanvas)
            .foregroundStyle(scheme == .dark ? Color.panDarkTextPrimary : Color.panTextPrimary)
    }
}

extension View { func paneraTheme() -> some View { modifier(PaneraTheme()) } }

// Use Color.panGreenBright for CTAs/active tabs/progress fill in dark mode —
// Color.panGreen is too dark on #14140F. Define an asset-catalog Color
// "PanAction" = #4C8B2B (light) / #6BBE45 (dark) and use it for all actions.
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- Bundle Poppins and Inter TTFs via `Info.plist` `UIAppFonts` — both are SIL OFL licensed for free distribution
- Dynamic Type: support on screen titles, item names, descriptions, prices, calorie counts; keep tab labels, reward tags, filter chips, stepper glyphs at FIXED sizes
- VoiceOver: label menu rows as "{name}, {price}, {calories}, add to order"; label rewards card as "MyPanera: {headline}, {progress} percent to next reward"; the green add-button needs an explicit `.accessibilityLabel("Add \(name) to order")`
- Color contrast: `#2A2A1F` on `#FFFFFF` and on `#F4EFE1` passes WCAG AA for body text; `#4C8B2B` on white passes AA for large text — use white text on the green button (passes AA). On dark, use `#6BBE45` for actions so contrast holds on `#14140F`
- Reduce Motion: disable the Add-to-Order scale-bounce and the rewards-progress animation (snap to value); keep the cart badge state change
- Dark mode: invert per the `panDark*` palette; warm near-black `#14140F`, NOT pure black or cold gray; food photography stays full-color and is the brightest element on screen
