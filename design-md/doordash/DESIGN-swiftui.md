# DoorDash (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md). Paste-ready SwiftUI code: color tokens, typography, and the signature components (merchant card, floating checkout CTA, category chips).

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // Brand
    static let ddRed         = Color(red: 0.922, green: 0.090, blue: 0.000) // #EB1700
    static let ddRedPressed  = Color(red: 0.765, green: 0.082, blue: 0.000) // #C31500
    static let ddRedTint     = Color(red: 1.0,   green: 0.922, blue: 0.910) // #FFEBE8

    // Canvas
    static let ddCanvas      = Color.white
    static let ddCanvasDark  = Color(red: 0.098, green: 0.098, blue: 0.098) // #191919

    // Text
    static let ddCharcoal    = Color(red: 0.098, green: 0.098, blue: 0.098) // #191919
    static let ddTextSecondary = Color(red: 0.459, green: 0.459, blue: 0.459) // #757575
    static let ddTextTertiary  = Color(red: 0.686, green: 0.686, blue: 0.686) // #AFAFAF
    static let ddDivider     = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5
    static let ddSurfaceMuted = Color(red: 0.969, green: 0.969, blue: 0.969) // #F7F7F7
    static let ddSurfaceTint = Color(red: 0.980, green: 0.980, blue: 0.980) // #FAFAFA

    // Semantic
    static let ddRatingOrange = Color(red: 1.0,   green: 0.502, blue: 0.0)  // #FF8000
    static let ddFeeGreen     = Color(red: 0.0,   green: 0.545, blue: 0.290) // #008B4A
    static let ddWarning      = Color(red: 0.961, green: 0.722, blue: 0.0)  // #F5B800
    static let ddError        = Color(red: 0.820, green: 0.208, blue: 0.059) // #D1350F
    static let ddInfoBlue     = Color(red: 0.0,   green: 0.400, blue: 0.890) // #0066E3
    static let ddDashPass     = Color(red: 0.0,   green: 0.420, blue: 0.510) // #006B82

    // Dark mode
    static let ddSurfaceDark1 = Color(red: 0.149, green: 0.149, blue: 0.149) // #262626
    static let ddSurfaceDark2 = Color(red: 0.188, green: 0.188, blue: 0.188) // #303030
    static let ddDividerDark  = Color(red: 0.227, green: 0.227, blue: 0.227) // #3A3A3A
}
```

## 2. Typography

DoorDash uses TT Norms Pro. Fall back to `SF Pro Text` / system default since TT Norms Pro is a licensed proprietary face.

```swift
extension Font {
    static let ddHeroTitle    = Font.custom("TTNormsPro-Bold", size: 28).weight(.bold)
    static let ddSection      = Font.custom("TTNormsPro-Bold", size: 22).weight(.bold)
    static let ddMerchant     = Font.custom("TTNormsPro-Bold", size: 18).weight(.bold)
    static let ddMenuItem     = Font.custom("TTNormsPro-Regular", size: 17).weight(.semibold)
    static let ddBody         = Font.custom("TTNormsPro-Regular", size: 15).weight(.regular)
    static let ddPriceLarge   = Font.custom("TTNormsPro-Bold", size: 20).weight(.bold).monospacedDigit()
    static let ddPriceBody    = Font.custom("TTNormsPro-Regular", size: 15).weight(.semibold).monospacedDigit()
    static let ddMeta         = Font.custom("TTNormsPro-Regular", size: 13).weight(.regular)
    static let ddChip         = Font.custom("TTNormsPro-Regular", size: 14).weight(.semibold)
    static let ddButtonCTA    = Font.custom("TTNormsPro-Bold", size: 16).weight(.bold)
    static let ddButtonSmall  = Font.custom("TTNormsPro-Regular", size: 14).weight(.semibold)
    static let ddTab          = Font.custom("TTNormsPro-Regular", size: 11).weight(.semibold)
    static let ddPromoBadge   = Font.custom("TTNormsPro-Bold", size: 11).weight(.bold)

    // System fallback
    static let ddHeroTitleSys = Font.system(size: 28, weight: .bold)
}
```

## 3. Signature Components

### Floating Checkout CTA

```swift
struct FloatingCheckoutCTA: View {
    let itemCount: Int
    let total: Decimal
    let onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            HStack(spacing: 0) {
                HStack(spacing: 8) {
                    Text("\(itemCount)")
                        .font(.system(size: 11, weight: .bold))
                        .foregroundStyle(Color.ddRed)
                        .frame(minWidth: 22, minHeight: 22)
                        .padding(.horizontal, 4)
                        .background(Capsule().fill(Color.white))
                    Text("Checkout").font(.ddButtonCTA).foregroundStyle(.white)
                }
                Spacer()
                Text(total.formatted(.currency(code: "USD")))
                    .font(.ddButtonCTA).foregroundStyle(.white)
            }
            .padding(.horizontal, 16)
            .frame(height: 52)
            .background(Capsule().fill(Color.ddRed))
            .shadow(color: Color.ddRed.opacity(0.35), radius: 24, y: 8)
        }
        .buttonStyle(DDPressableStyle(pressedScale: 0.98))
        .padding(.horizontal, 16)
    }
}

struct DDPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Merchant Card

```swift
struct MerchantCard: View {
    let name: String
    let rating: Double
    let deliveryMinutes: Int
    let deliveryFee: String
    let hasFreeDelivery: Bool
    let promoBadge: String?
    let photo: Image
    let isSaved: Bool
    let onToggleSave: () -> Void

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .topLeading) {
                photo
                    .resizable()
                    .aspectRatio(16/10, contentMode: .fill)
                    .clipped()

                if let promo = promoBadge {
                    Text(promo)
                        .font(.ddPromoBadge)
                        .tracking(0.3)
                        .textCase(.uppercase)
                        .foregroundStyle(Color.ddRed)
                        .padding(.vertical, 3).padding(.horizontal, 7)
                        .background(RoundedRectangle(cornerRadius: 4).fill(Color.ddRedTint))
                        .padding(8)
                }

                Button(action: onToggleSave) {
                    Image(systemName: isSaved ? "heart.fill" : "heart")
                        .font(.system(size: 14, weight: .bold))
                        .foregroundStyle(isSaved ? Color.ddRed : .black)
                        .frame(width: 32, height: 32)
                        .background(Circle().fill(.white.opacity(0.85)))
                }
                .frame(maxWidth: .infinity, alignment: .topTrailing)
                .padding(8)
            }
            .clipShape(RoundedRectangle(cornerRadius: 12, style: .continuous))

            VStack(alignment: .leading, spacing: 4) {
                Text(name).font(.ddMerchant)
                HStack(spacing: 4) {
                    Text("★").foregroundStyle(Color.ddRatingOrange).font(.system(size: 12))
                    Text(String(format: "%.1f", rating)).font(.ddMeta)
                    Text("·").font(.ddMeta).foregroundStyle(Color.ddTextSecondary)
                    Text("\(deliveryMinutes) min").font(.ddMeta).foregroundStyle(Color.ddTextSecondary)
                    Text("·").font(.ddMeta).foregroundStyle(Color.ddTextSecondary)
                    if hasFreeDelivery {
                        Text("$0 Delivery").font(.ddMeta).fontWeight(.semibold).foregroundStyle(Color.ddFeeGreen)
                    } else {
                        Text(deliveryFee).font(.ddMeta).foregroundStyle(Color.ddTextSecondary)
                    }
                }
            }
            .padding(12)
        }
        .frame(width: 260)
        .background(Color.ddCanvas)
        .shadow(color: .black.opacity(0.06), radius: 12, y: 4)
    }
}
```

### Category Chip

```swift
struct CategoryChip: View {
    let emoji: String
    let label: String
    let isActive: Bool
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                Text(emoji)
                Text(label).font(.ddChip)
            }
            .foregroundStyle(isActive ? .white : Color.ddCharcoal)
            .padding(.vertical, 8).padding(.horizontal, 14)
            .background(Capsule().fill(isActive ? Color.ddCharcoal : Color.ddSurfaceMuted))
        }
    }
}
```

### Promo Badge

```swift
struct PromoBadge: View {
    let text: String
    var body: some View {
        Text(text)
            .font(.ddPromoBadge)
            .tracking(0.3)
            .textCase(.uppercase)
            .foregroundStyle(Color.ddRed)
            .padding(.vertical, 4).padding(.horizontal, 8)
            .background(RoundedRectangle(cornerRadius: 4).fill(Color.ddRedTint))
    }
}
```

### Quantity Stepper

```swift
struct QuantityStepper: View {
    @Binding var count: Int
    var min: Int = 0
    var max: Int = 99

    var body: some View {
        HStack(spacing: 12) {
            Button { if count > min { count -= 1 } } label: {
                Image(systemName: "minus").font(.system(size: 14, weight: .bold))
                    .frame(width: 28, height: 28)
                    .background(Circle().fill(Color.ddSurfaceMuted))
                    .foregroundStyle(count > min ? Color.ddCharcoal : Color.ddTextTertiary)
            }
            Text("\(count)").font(.system(size: 16, weight: .bold)).frame(minWidth: 20)
            Button { if count < max { count += 1 } } label: {
                Image(systemName: "plus").font(.system(size: 14, weight: .bold))
                    .frame(width: 28, height: 28)
                    .background(Circle().fill(Color.ddSurfaceMuted))
                    .foregroundStyle(Color.ddCharcoal)
            }
        }
    }
}
```

## 4. Tab Bar

```swift
struct DDRootTabView: View {
    var body: some View {
        TabView {
            HomeView()    .tabItem { Label("Home",    systemImage: "house.fill") }
            GroceryView() .tabItem { Label("Grocery", systemImage: "bag.fill") }
            OffersView()  .tabItem { Label("Offers",  systemImage: "percent") }
            OrdersView()  .tabItem { Label("Orders",  systemImage: "bag") }
            AccountView() .tabItem { Label("Account", systemImage: "person.fill") }
        }
        .tint(Color.ddRed)
    }
}
```

## 5. Motion & Haptics

```swift
// Add to cart
.sensoryFeedback(.success, trigger: itemsCount)
// Animate stepper bump
.scaleEffect(bumpScale).animation(.spring(response: 0.3, dampingFraction: 0.6), value: bumpScale)

// Place order confirmation
.sensoryFeedback(.success, trigger: orderPlaced)

// Order placed full-screen
// Use a confetti SwiftUI library or custom Canvas with red/orange particles
```

## 6. SF Symbols

| Purpose | Symbol |
|---------|--------|
| Search | `magnifyingglass` |
| Heart (save) | `heart` / `heart.fill` |
| Star (rating) | `star.fill` (filled in `#FF8000`) |
| Cart | `bag` / `bag.fill` |
| Location | `location.fill` / `mappin.and.ellipse` |
| More | `ellipsis` |
| Delivery | `bicycle` / `car.fill` |
| DashPass | `sparkles` |

## 7. Accessibility

- iOS target: 17+
- Dynamic Type: body/title/meta scale; price numerals cap at 140%
- VoiceOver: checkout CTA reads "Checkout, 2 items, total $23.40"
- Contrast: `#757575` secondary on white meets AA; validate at 11pt tab labels
