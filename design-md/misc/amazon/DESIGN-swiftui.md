# Amazon (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Amazon's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas
    static let amzCanvas        = Color.white                                   // #FFFFFF
    static let amzSurfaceMuted  = Color(red: 0.953, green: 0.953, blue: 0.953) // #F3F3F3
    static let amzSurfaceTint   = Color(red: 0.969, green: 0.973, blue: 0.973) // #F7F8F8
    static let amzDivider       = Color(red: 0.867, green: 0.867, blue: 0.867) // #DDDDDD
    static let amzBorderDefault = Color(red: 0.835, green: 0.851, blue: 0.851) // #D5D9D9

    // MARK: - Text
    static let amzTextPrimary   = Color(red: 0.059, green: 0.067, blue: 0.067) // #0F1111
    static let amzTextSecondary = Color(red: 0.337, green: 0.349, blue: 0.349) // #565959
    static let amzTextTertiary  = Color(red: 0.518, green: 0.541, blue: 0.549) // #848A8C

    // MARK: - Brand
    static let amzYellow        = Color(red: 1.000, green: 0.600, blue: 0.000) // #FF9900
    static let amzYellowPressed = Color(red: 0.902, green: 0.541, blue: 0.000) // #E68A00
    static let amzYellowHighlight = Color(red: 0.988, green: 0.824, blue: 0.000) // #FCD200
    static let amzBuyNowOrange  = Color(red: 0.941, green: 0.533, blue: 0.016) // #F08804
    static let amzDeepNavy      = Color(red: 0.075, green: 0.098, blue: 0.129) // #131921
    static let amzSecondaryNavy = Color(red: 0.137, green: 0.184, blue: 0.243) // #232F3E

    // MARK: - Semantic
    static let amzPriceRed      = Color(red: 0.694, green: 0.153, blue: 0.016) // #B12704
    static let amzAlertRed      = Color(red: 0.800, green: 0.047, blue: 0.224) // #CC0C39
    static let amzSuccessGreen  = Color(red: 0.000, green: 0.463, blue: 0.000) // #007600
    static let amzPrimeTeal     = Color(red: 0.000, green: 0.443, blue: 0.522) // #007185
    static let amzPrimeSky      = Color(red: 0.000, green: 0.659, blue: 0.882) // #00A8E1
    static let amzRatingGold    = Color(red: 1.000, green: 0.643, blue: 0.110) // #FFA41C

    // MARK: - Dark
    static let amzDarkCanvas    = Color(red: 0.059, green: 0.067, blue: 0.067) // #0F1111
    static let amzDarkSurface1  = Color(red: 0.102, green: 0.122, blue: 0.145) // #1A1F25
    static let amzDarkSurface2  = Color(red: 0.137, green: 0.184, blue: 0.243) // #232F3E
}
```

## 2. Typography

Amazon Ember is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) if you have a license, or fall back to SF Pro — the closest free substitute.

```swift
extension Font {
    // Amazon Ember hierarchy
    static let amzNavHero      = Font.custom("AmazonEmber-Bold",    size: 22).weight(.bold)
    static let amzSection      = Font.custom("AmazonEmber-Bold",    size: 20).weight(.bold)
    static let amzProductTitle = Font.custom("AmazonEmber-Regular", size: 16).weight(.regular)
    static let amzPDPTitle     = Font.custom("AmazonEmber-Medium",  size: 20).weight(.medium)
    static let amzPriceHero    = Font.custom("AmazonEmber-Bold",    size: 28).weight(.bold)
    static let amzPriceSuperscript = Font.custom("AmazonEmber-Bold", size: 14).weight(.bold)
    static let amzPriceCard    = Font.custom("AmazonEmber-Bold",    size: 18).weight(.bold)
    static let amzPriceStruck  = Font.custom("AmazonEmber-Regular", size: 14).weight(.regular)
    static let amzBody         = Font.custom("AmazonEmber-Regular", size: 15).weight(.regular)
    static let amzRatingCount  = Font.custom("AmazonEmber-Regular", size: 13).weight(.regular)
    static let amzDelivery     = Font.custom("AmazonEmber-Medium",  size: 13).weight(.medium)
    static let amzMeta         = Font.custom("AmazonEmber-Regular", size: 12).weight(.regular)
    static let amzButton       = Font.custom("AmazonEmber-Medium",  size: 15).weight(.medium)
    static let amzButtonSmall  = Font.custom("AmazonEmber-Regular", size: 14).weight(.regular)
    static let amzPrimeBadge   = Font.custom("AmazonEmber-Bold",    size: 11).weight(.bold)
    static let amzTab          = Font.custom("AmazonEmber-Medium",  size: 10).weight(.medium)
    static let amzSearchPlaceholder = Font.custom("AmazonEmber-Regular", size: 16).weight(.regular)
    static let amzPromoBadge   = Font.custom("AmazonEmber-Bold",    size: 11).weight(.bold)
}
```

## 3. Signature Components

### Top Nav with Search Bar

```swift
struct AmazonTopNav: View {
    @State private var query = ""
    let onSearch: () -> Void
    let onMicOrScan: () -> Void

    var body: some View {
        HStack(spacing: 12) {
            Image(systemName: "cart.fill")
                .resizable().scaledToFit()
                .frame(width: 28, height: 28)
                .foregroundStyle(.white)
            HStack(spacing: 0) {
                HStack(spacing: 8) {
                    Image(systemName: "magnifyingglass")
                        .font(.system(size: 18))
                        .foregroundStyle(.amzTextSecondary)
                    Text("Search Amazon")
                        .font(.amzSearchPlaceholder)
                        .foregroundStyle(.amzTextSecondary)
                    Spacer()
                }
                .padding(.horizontal, 12)
                .frame(height: 44)
                .background(Color.amzCanvas)
                .clipShape(RoundedCorner(radius: 8, corners: [.topLeft, .bottomLeft]))

                Button(action: onMicOrScan) {
                    HStack(spacing: 10) {
                        Image(systemName: "mic.fill").font(.system(size: 18))
                        Image(systemName: "barcode.viewfinder").font(.system(size: 20))
                    }
                    .foregroundStyle(.amzTextPrimary)
                    .padding(.horizontal, 14)
                    .frame(height: 44)
                    .background(Color.amzYellow)
                    .clipShape(RoundedCorner(radius: 8, corners: [.topRight, .bottomRight]))
                }
            }
        }
        .padding(.horizontal, 12)
        .frame(height: 56)
        .background(Color.amzDeepNavy)
        .onTapGesture(perform: onSearch)
    }
}

struct RoundedCorner: Shape {
    var radius: CGFloat
    var corners: UIRectCorner
    func path(in rect: CGRect) -> Path {
        Path(UIBezierPath(roundedRect: rect, byRoundingCorners: corners,
                          cornerRadii: CGSize(width: radius, height: radius)).cgPath)
    }
}
```

### Add to Cart Button (Yellow)

```swift
struct AmazonAddToCartButton: View {
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text("Add to Cart")
                .font(.amzButton)
                .foregroundStyle(.amzTextPrimary)
                .frame(maxWidth: .infinity, minHeight: 44)
                .background(
                    ZStack {
                        RoundedRectangle(cornerRadius: 8)
                            .fill(pressed ? Color.amzYellowPressed : Color.amzYellow)
                        // 1pt top highlight (vestigial 3D cue)
                        VStack(spacing: 0) {
                            Rectangle().fill(Color.amzYellowHighlight).frame(height: 1)
                            Spacer()
                        }
                        .clipShape(RoundedRectangle(cornerRadius: 8))
                    }
                )
                .shadow(color: .black.opacity(0.1), radius: 2, y: 1)
        }
        .buttonStyle(.plain)
        .scaleEffect(pressed ? 0.98 : 1)
        .animation(.spring(response: 0.2, dampingFraction: 0.8), value: pressed)
        .sensoryFeedback(.success, trigger: pressed)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .onChanged { _ in pressed = true }
                .onEnded   { _ in pressed = false }
        )
    }
}
```

### Buy Now Button (Orange)

```swift
struct AmazonBuyNowButton: View {
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text("Buy Now")
                .font(.amzButton)
                .foregroundStyle(.amzTextPrimary)
                .frame(maxWidth: .infinity, minHeight: 48)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.amzBuyNowOrange))
        }
        .buttonStyle(.plain)
    }
}
```

### Product Card (Grid Tile)

```swift
struct AmazonProductCard: View {
    let title: String
    let rating: Double
    let reviewCount: Int
    let price: String
    let originalPrice: String?
    let isPrime: Bool
    let deliveryLine: String
    let imageUrl: URL?

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            AsyncImage(url: imageUrl) { image in
                image.resizable().scaledToFit()
            } placeholder: {
                Color.amzSurfaceMuted
            }
            .aspectRatio(1, contentMode: .fit)
            .clipShape(RoundedRectangle(cornerRadius: 4))

            Text(title)
                .font(.amzProductTitle)
                .foregroundStyle(.amzTextPrimary)
                .lineLimit(2)
                .fixedSize(horizontal: false, vertical: true)

            HStack(spacing: 2) {
                ForEach(0..<5, id: \.self) { i in
                    Image(systemName: Double(i) < rating ? "star.fill" : "star")
                        .font(.system(size: 12))
                        .foregroundStyle(.amzRatingGold)
                }
                Text(" (\(reviewCount.formatted()))")
                    .font(.amzRatingCount)
                    .foregroundStyle(.amzPrimeTeal)
            }

            HStack(alignment: .firstTextBaseline, spacing: 6) {
                Text(price)
                    .font(.amzPriceCard)
                    .foregroundStyle(.amzTextPrimary)
                if let original = originalPrice {
                    Text(original)
                        .font(.amzPriceStruck)
                        .foregroundStyle(.amzTextSecondary)
                        .strikethrough()
                }
            }

            if isPrime {
                HStack(spacing: 4) {
                    Text("prime")
                        .font(.amzPrimeBadge)
                        .foregroundStyle(.amzPrimeSky)
                    Text(deliveryLine)
                        .font(.amzDelivery)
                        .foregroundStyle(.amzSuccessGreen)
                }
            }
        }
        .padding(8)
        .background(Color.amzCanvas)
    }
}
```

### PDP Price Block (Hero Price with Superscript Cents)

```swift
struct AmazonPDPPriceBlock: View {
    let dollars: Int
    let cents: Int
    let originalPrice: String?
    let savingsLine: String?
    let deliveryText: String

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            HStack(alignment: .top, spacing: 2) {
                Text("$")
                    .font(.amzPriceSuperscript)
                    .foregroundStyle(.amzTextPrimary)
                    .baselineOffset(12)
                Text("\(dollars)")
                    .font(.amzPriceHero)
                    .foregroundStyle(.amzTextPrimary)
                Text(String(format: "%02d", cents))
                    .font(.amzPriceSuperscript)
                    .foregroundStyle(.amzTextPrimary)
                    .baselineOffset(12)
            }
            if let original = originalPrice {
                Text(original)
                    .font(.amzPriceStruck)
                    .foregroundStyle(.amzTextSecondary)
                    .strikethrough()
            }
            if let savings = savingsLine {
                Text(savings)
                    .font(.amzButton)
                    .foregroundStyle(.amzPriceRed)
            }
            HStack(spacing: 4) {
                Text("prime")
                    .font(.amzPrimeBadge)
                    .foregroundStyle(.amzPrimeSky)
                Text(deliveryText)
                    .font(.amzButton)
                    .foregroundStyle(.amzSuccessGreen)
            }
        }
    }
}
```

### Lightning Deal Countdown Banner

```swift
struct AmazonLightningDealBanner: View {
    let countdown: String  // "02h 45m 10s"
    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: "bolt.fill")
                .font(.system(size: 18, weight: .bold))
                .foregroundStyle(.white)
            Text("Lightning Deal")
                .font(.amzDelivery)
                .foregroundStyle(.white)
                .fontWeight(.bold)
            Spacer()
            Text("Ends in \(countdown)")
                .font(.amzDelivery)
                .foregroundStyle(.white)
                .monospacedDigit()
        }
        .padding(.horizontal, 12)
        .padding(.vertical, 8)
        .background(
            LinearGradient(colors: [Color.amzPriceRed, Color.amzAlertRed],
                           startPoint: .leading, endPoint: .trailing)
        )
        .clipShape(RoundedRectangle(cornerRadius: 4))
    }
}
```

### Quantity Stepper

```swift
struct AmazonQuantityStepper: View {
    @Binding var count: Int
    var body: some View {
        HStack(spacing: 0) {
            Button { if count > 1 { count -= 1 } } label: {
                Image(systemName: "minus")
                    .frame(width: 44, height: 32)
            }
            Text("\(count)")
                .font(.amzButton)
                .frame(width: 36, height: 32)
                .foregroundStyle(.amzTextPrimary)
            Button { count += 1 } label: {
                Image(systemName: "plus")
                    .frame(width: 44, height: 32)
            }
        }
        .foregroundStyle(.amzTextPrimary)
        .background(
            RoundedRectangle(cornerRadius: 8)
                .fill(Color.amzSurfaceMuted)
                .overlay(RoundedRectangle(cornerRadius: 8).stroke(Color.amzBorderDefault, lineWidth: 1))
        )
    }
}
```

## 4. Tab Bar

```swift
struct AmazonRootTabView: View {
    @State private var cartCount = 2
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithOpaqueBackground()
        appearance.backgroundColor = UIColor.white
        appearance.shadowColor = UIColor(red: 0.867, green: 0.867, blue: 0.867, alpha: 1)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home",   systemImage: "house.fill") }
            MenuView().tabItem { Label("Menu",   systemImage: "line.3.horizontal") }
            CartView().tabItem { Label("Cart",   systemImage: "cart.fill") }
                .badge(cartCount)
            YouView().tabItem  { Label("You",    systemImage: "person.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
        }
        .tint(Color.amzYellow)
    }
}
```

## 5. Motion

```swift
// Add to cart — success haptic on release
.sensoryFeedback(.success, trigger: cartCount)

// Cart badge bump
.scaleEffect(bumping ? 1.3 : 1.0)
.animation(.spring(response: 0.25, dampingFraction: 0.55), value: bumping)

// Add-to-cart toast
struct AddToCartToast: View {
    @Binding var visible: Bool
    var body: some View {
        HStack {
            Image(systemName: "checkmark.circle.fill").foregroundStyle(.amzSuccessGreen)
            Text("Added to your Cart").font(.amzButton)
            Spacer()
        }
        .padding(.horizontal, 16).frame(height: 48)
        .background(Color.amzCanvas)
        .shadow(color: .black.opacity(0.15), radius: 12, y: -4)
        .offset(y: visible ? 0 : 60)
        .animation(.spring(response: 0.3, dampingFraction: 0.8), value: visible)
    }
}
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Cart | `cart.fill` / `cart` | 24pt |
| Search | `magnifyingglass` | 20pt |
| Mic (search) | `mic.fill` | 18pt |
| Barcode scanner | `barcode.viewfinder` | 20pt |
| Lightning deal | `bolt.fill` | 18pt |
| Star (rating) | `star.fill` / `star` | 12pt |
| Checkmark (success) | `checkmark.circle.fill` | 20pt |
| Plus / Minus (stepper) | `plus` / `minus` | 16pt |
| Hamburger menu | `line.3.horizontal` | 24pt |
| Bell (notifications) | `bell.fill` | 22pt |
| Person (You tab) | `person.fill` | 24pt |
| Home (tab) | `house.fill` | 24pt |
| Lock (secure transaction) | `lock.fill` | 14pt |

## 7. Accessibility Notes

- Minimum target: iOS 16 (iOS 17 for `.sensoryFeedback`)
- **VoiceOver**:
  - Add to Cart: `accessibilityLabel("Add Wireless Headphones to cart for $24.99")`
  - Star rating: `accessibilityLabel("4.7 out of 5 stars, 12345 reviews")`
  - Price: `accessibilityLabel("$28.99")` (combine dollars + superscript cents into one utterance)
- **Dynamic Type**: Product titles, body, reviews scale freely; prices cap at 140% to preserve layout; Prime wordmark fixed at 11pt for brand consistency
- **Contrast**: `#565959` secondary text on `#FFFFFF` canvas meets WCAG AA at 13pt+; verify `#848A8C` tertiary only for non-essential labels
- **Dark mode**: Canvas becomes `#0F1111`, nav stays `#131921`, yellow button unchanged (`#FF9900` + black text reads on both)
- **Reduce Motion**: Replace scale-bump on cart badge with a 100ms fade; skip the toast slide animation
- **Haptics**:
  - `.selection` on quantity stepper tap
  - `.success` on Add to Cart
  - `.impact(.medium)` on "Place your order" final checkout
  - `.warning` on "Out of stock" tap
