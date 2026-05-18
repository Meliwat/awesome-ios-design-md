# Zomato (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Zomato's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the semantic rating pill, the veg/non-veg mark, and the restaurant detail with the Delivery/Dining toggle.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (Interactive)
    static let zomatoRed         = Color(red: 0.886, green: 0.216, blue: 0.267) // #E23744
    static let zomatoRedPressed  = Color(red: 0.769, green: 0.145, blue: 0.192) // #C42531

    // MARK: - Rating Scale (semantic)
    static let zRatingGreen = Color(red: 0.149, green: 0.494, blue: 0.243) // #267E3E
    static let zRatingAmber = Color(red: 0.859, green: 0.486, blue: 0.220) // #DB7C38
    static let zRatingRed   = Color(red: 0.886, green: 0.216, blue: 0.267) // #E23744 (= brand)
    static let zRatingGrey  = Color(red: 0.612, green: 0.612, blue: 0.612) // #9C9C9C

    // MARK: - Membership & Dining
    static let zGold       = Color(red: 0.937, green: 0.780, blue: 0.369) // #EFC75E
    static let zDiningBlue = Color(red: 0.145, green: 0.435, blue: 0.937) // #256FEF

    // MARK: - Canvas & Surfaces (Light)
    static let zCanvas        = Color.white                                   // #FFFFFF
    static let zSurface1      = Color(red: 0.957, green: 0.957, blue: 0.961) // #F4F4F5
    static let zSurface2      = Color(red: 0.922, green: 0.922, blue: 0.925) // #EBEBEC
    static let zDivider       = Color(red: 0.910, green: 0.910, blue: 0.910) // #E8E8E8

    // MARK: - Canvas & Surfaces (Dark)
    static let zDarkCanvas    = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let zDarkSurface1  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let zDarkSurface2  = Color(red: 0.149, green: 0.149, blue: 0.161) // #262629
    static let zDarkDivider   = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E

    // MARK: - Text
    static let zTextPrimary    = Color(red: 0.110, green: 0.110, blue: 0.110) // #1C1C1C
    static let zTextSecondary  = Color(red: 0.412, green: 0.412, blue: 0.412) // #696969
    static let zTextTertiary   = Color(red: 0.612, green: 0.612, blue: 0.612) // #9C9C9C
    static let zDarkTextPrimary = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2

    // MARK: - Veg / Non-Veg
    static let zVegGreen   = Color(red: 0.149, green: 0.494, blue: 0.243) // #267E3E
    static let zNonVegMaroon = Color(red: 0.725, green: 0.110, blue: 0.110) // #B91C1C

    // MARK: - Semantic
    static let zError      = Color(red: 0.878, green: 0.227, blue: 0.227) // #E03A3A
}
```

## 2. Typography

Zomato's brand face is a custom Okra-family grotesque; the recommended free substitute is **Inter**. Bundle Inter TTFs via `Info.plist` / `UIAppFonts`. Prices/ratings use tabular figures.

```swift
extension Font {
    static let zScreenTitle    = Font.custom("Inter-Black",     size: 32) // 900
    static let zRestaurantHero = Font.custom("Inter-ExtraBold", size: 24) // 800
    static let zSection        = Font.custom("Inter-ExtraBold", size: 20)
    static let zMenuCategory   = Font.custom("Inter-ExtraBold", size: 17)
    static let zItemName       = Font.custom("Inter-Bold",      size: 15)
    static let zBody           = Font.custom("Inter-Medium",    size: 14)
    static let zPrice          = Font.custom("Inter-Bold",      size: 14)
    static let zMeta           = Font.custom("Inter-Regular",   size: 13)
    static let zRatingPill     = Font.custom("Inter-ExtraBold", size: 14)
    static let zRatingCaption  = Font.custom("Inter-Regular",   size: 11)
    static let zTag            = Font.custom("Inter-ExtraBold", size: 11)
    static let zButton         = Font.custom("Inter-ExtraBold", size: 16)
    static let zAddLabel       = Font.custom("Inter-ExtraBold", size: 14)
    static let zTab            = Font.custom("Inter-SemiBold",  size: 10)
    static let zCaption        = Font.custom("Inter-Medium",    size: 12)
}

extension View {
    func zTabular() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Semantic Rating Pill

```swift
struct RatingPill: View {
    let score: Double
    let count: String?           // e.g. "12.4K Delivery"

    private var fill: Color {
        switch score {
        case 4.0...:    return .zRatingGreen
        case 3.0..<4.0: return .zRatingAmber
        case 0.1..<3.0: return .zRatingRed
        default:        return .zRatingGrey
        }
    }

    var body: some View {
        VStack(spacing: 5) {
            HStack(spacing: 4) {
                Image(systemName: "star.fill").font(.system(size: 11)).foregroundStyle(.white)
                Text(String(format: "%.1f", score)).font(.zRatingPill).zTabular().foregroundStyle(.white)
            }
            .padding(.horizontal, 9).padding(.vertical, 4)
            .background(fill, in: RoundedRectangle(cornerRadius: 7))

            if let count {
                Text(count)
                    .font(.zRatingCaption)
                    .foregroundStyle(Color.zTextSecondary)
                    .underline()
            }
        }
    }
}
```

### Veg / Non-Veg Mark

```swift
struct VegMark: View {
    let isVeg: Bool
    var body: some View {
        RoundedRectangle(cornerRadius: 3)
            .strokeBorder(isVeg ? Color.zVegGreen : Color.zNonVegMaroon, lineWidth: 1.5)
            .frame(width: 16, height: 16)
            .overlay {
                if isVeg {
                    Circle().fill(Color.zVegGreen).frame(width: 7, height: 7)
                } else {
                    Triangle().fill(Color.zNonVegMaroon).frame(width: 9, height: 8)
                }
            }
    }
}

struct Triangle: Shape {
    func path(in r: CGRect) -> Path {
        var p = Path()
        p.move(to: CGPoint(x: r.midX, y: r.minY))
        p.addLine(to: CGPoint(x: r.maxX, y: r.maxY))
        p.addLine(to: CGPoint(x: r.minX, y: r.maxY))
        p.closeSubpath()
        return p
    }
}
```

### Menu Item Row (with bordered ADD)

```swift
struct MenuItemRow: View {
    let isVeg: Bool
    let bestseller: Bool
    let name: String
    let price: String
    let rating: Double
    let ratingCount: Int
    let imageName: String
    @State private var quantity = 0

    var body: some View {
        HStack(alignment: .top, spacing: 14) {
            VStack(alignment: .leading, spacing: 0) {
                VegMark(isVeg: isVeg)
                if bestseller {
                    HStack(spacing: 4) {
                        Image(systemName: "star.fill").font(.system(size: 9))
                        Text("Bestseller").font(.zTag)
                    }
                    .foregroundStyle(Color.zRatingAmber)
                    .padding(.top, 7).padding(.bottom, 3)
                }
                Text(name).font(.zItemName).foregroundStyle(Color.zTextPrimary).padding(.top, bestseller ? 0 : 7)
                Text(price).font(.zPrice).foregroundStyle(Color.zTextPrimary).padding(.top, 5)
                HStack(spacing: 4) {
                    Image(systemName: "star.fill").font(.system(size: 10)).foregroundStyle(Color.zRatingGreen)
                    Text("\(String(format: "%.1f", rating)) (\(ratingCount))")
                        .font(.zCaption).foregroundStyle(Color.zTextSecondary)
                }
                .padding(.top, 6)
            }
            Spacer(minLength: 8)

            VStack(spacing: 0) {
                Image(imageName)
                    .resizable().scaledToFill()
                    .frame(width: 96, height: 90)
                    .clipShape(RoundedRectangle(cornerRadius: 12))
                Group {
                    if quantity == 0 {
                        Button { quantity = 1 } label: {
                            Text("ADD")
                                .font(.zAddLabel).tracking(0.3)
                                .foregroundStyle(Color.zomatoRed)
                                .padding(.horizontal, 22).padding(.vertical, 8)
                        }
                    } else {
                        AddStepper(quantity: $quantity)
                    }
                }
                .background(Color.zCanvas, in: RoundedRectangle(cornerRadius: 10))
                .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.zomatoRed, lineWidth: 1))
                .shadow(color: .black.opacity(0.12), radius: 8, y: 3)
                .offset(y: -16)   // overlaps the thumbnail
            }
        }
        .padding(.vertical, 14)
        .overlay(alignment: .bottom) { Rectangle().fill(Color.zDivider).frame(height: 1) }
    }
}

struct AddStepper: View {
    @Binding var quantity: Int
    var body: some View {
        HStack(spacing: 0) {
            Button { if quantity > 1 { quantity -= 1 } else { quantity = 0 } } label: {
                Image(systemName: "minus").font(.system(size: 14, weight: .heavy))
                    .foregroundStyle(Color.zomatoRed).frame(width: 36, height: 36)
            }
            Text("\(quantity)").font(.zItemName).zTabular()
                .foregroundStyle(Color.zomatoRed).frame(minWidth: 24)
                .contentTransition(.numericText())
            Button { quantity += 1 } label: {
                Image(systemName: "plus").font(.system(size: 14, weight: .heavy))
                    .foregroundStyle(Color.zomatoRed).frame(width: 36, height: 36)
            }
        }
        .sensoryFeedback(.selection, trigger: quantity)
    }
}
```

### Delivery / Dining Out Toggle

```swift
struct DeliveryDiningToggle: View {
    @Binding var isDelivery: Bool
    @Namespace private var ns

    var body: some View {
        HStack(spacing: 0) {
            segment("Delivery", active: isDelivery) { isDelivery = true }
            segment("Dining Out", active: !isDelivery) { isDelivery = false }
        }
        .padding(4)
        .background(Color.zSurface1, in: Capsule())
    }

    private func segment(_ title: String, active: Bool, _ tap: @escaping () -> Void) -> some View {
        Text(title)
            .font(.custom("Inter-Bold", size: 13))
            .foregroundStyle(active ? .white : Color.zTextSecondary)
            .frame(maxWidth: .infinity)
            .padding(.vertical, 9)
            .background {
                if active {
                    Capsule().fill(Color.zomatoRed).matchedGeometryEffect(id: "seg", in: ns)
                }
            }
            .contentShape(Capsule())
            .onTapGesture { withAnimation(.spring(response: 0.22, dampingFraction: 0.85)) { tap() } }
    }
}
```

### Restaurant Detail Header (hero + pull-up card)

```swift
struct RestaurantDetailHeader: View {
    let imageName: String
    let name: String
    let meta: String
    let deliveryRating: Double
    let diningRating: Double

    var body: some View {
        VStack(spacing: 0) {
            ZStack(alignment: .top) {
                Image(imageName)
                    .resizable().scaledToFill()
                    .frame(height: 200).clipped()
                HStack {
                    heroBtn("chevron.left")
                    Spacer()
                    HStack(spacing: 10) { heroBtn("magnifyingglass"); heroBtn("heart") }
                }
                .padding(.horizontal, 16).padding(.top, 14)
            }

            VStack(alignment: .leading, spacing: 0) {
                Text(name).font(.zRestaurantHero).foregroundStyle(Color.zTextPrimary)
                Text(meta).font(.zMeta).foregroundStyle(Color.zTextSecondary).padding(.top, 5)
                HStack(spacing: 18) {
                    RatingPill(score: deliveryRating, count: "12.4K Delivery")
                    RatingPill(score: diningRating, count: "3.1K Dining")
                }
                .padding(.top, 14)
            }
            .frame(maxWidth: .infinity, alignment: .leading)
            .padding(.horizontal, 18).padding(.top, 20)
            .background(
                UnevenRoundedRectangle(topLeadingRadius: 22, topTrailingRadius: 22)
                    .fill(Color.zCanvas)
            )
            .offset(y: -22)
        }
    }

    private func heroBtn(_ symbol: String) -> some View {
        Image(systemName: symbol)
            .font(.system(size: 17, weight: .semibold))
            .foregroundStyle(.white)
            .frame(width: 34, height: 34)
            .background(Color.black.opacity(0.4), in: Circle())
    }
}
```

### Primary Button

```swift
struct ZButton: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.zButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 52)
                .background(Color.zomatoRed, in: RoundedRectangle(cornerRadius: 12))
        }
        .buttonStyle(.plain)
    }
}
// Embeds price: ZButton(title: "Add item · ₹320", action: {})
```

## 4. Bottom Tab Bar

```swift
struct ZomatoTabView: View {
    var body: some View {
        TabView {
            DeliveryView().tabItem { Label("Delivery", systemImage: "house.fill") }
            DiningView().tabItem   { Label("Dining",   systemImage: "fork.knife") }
            LiveView().tabItem     { Label("Live",     systemImage: "dot.radiowaves.left.and.right") }
            ProfileView().tabItem  { Label("Profile",  systemImage: "person.crop.circle") }
        }
        .tint(.zomatoRed)   // active tab is Zomato Red
    }
}
```

## 5. Motion

```swift
// ADD → stepper morph (in place), cart slides up
withAnimation(.easeOut(duration: 0.18)) { quantity = 1 }
withAnimation(.easeOut(duration: 0.28)) { cartVisible = true }

// Quantity stepper
.contentTransition(.numericText())
.sensoryFeedback(.selection, trigger: quantity)

// Delivery/Dining toggle — sliding red fill
withAnimation(.spring(response: 0.22, dampingFraction: 0.85)) { isDelivery.toggle() }
// content cross-fade 200ms

// Hero pull-up parallax: GeometryReader on scroll offset → hero .offset(y: scroll * 0.5)

// Card → detail: matchedGeometryEffect on the hero photo, 320ms push

// Order tracking stage advance
withAnimation(.easeOut(duration: 0.25)) { stageIndex += 1 }
.sensoryFeedback(.impact(weight: .medium), trigger: stageIndex)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Delivery (tab) | `house` / `house.fill` | 22pt |
| Dining (tab) | `fork.knife` | 22pt |
| Live (tab) | `dot.radiowaves.left.and.right` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Rating star | `star.fill` | 11pt |
| Dish rating star | `star.fill` | 10pt |
| Bestseller | `star.fill` | 9pt |
| Hero back | `chevron.left` | 17pt |
| Hero search | `magnifyingglass` | 17pt |
| Hero save | `heart` / `heart.fill` | 17pt |
| ADD minus | `minus` | 14pt |
| ADD plus | `plus` | 14pt |
| Share | `square.and.arrow.up` | 17pt |
| Dining — book | `calendar` | 16pt |
| Offer | `tag.fill` | 12pt |
| Pro / Gold | `crown.fill` | 14pt |
| Rider (tracking) | `bicycle` | 22pt |

## 7. Dark Mode

```swift
struct ZomatoTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.zDarkCanvas : Color.zCanvas)
            .foregroundStyle(scheme == .dark ? Color.zDarkTextPrimary : Color.zTextPrimary)
    }
}

extension View {
    func zomatoTheme() -> some View { modifier(ZomatoTheme()) }
}
```

Zomato Red, the full rating scale (green/amber/red/grey), the veg/non-veg colors, Pro gold and Dining blue are identical across light and dark — so a pill's color always means the same thing. Only canvas, surfaces, dividers and text invert. Food photography is never dimmed. Floating surfaces (ADD button, sticky cart) add a 0.5pt `zDarkDivider` border in dark mode as the elevation cue (shadows fade on dark).

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback`, `.contentTransition(.numericText())` and `UnevenRoundedRectangle` require iOS 17 — fall back to `UIImpactFeedbackGenerator`, a manual cross-fade, and a custom `Shape` on iOS 16)
- Bundle Inter TTFs via `Info.plist` (`UIAppFonts`) — Inter is SIL OFL, free to distribute. Substitute for Zomato's proprietary Okra-family face
- Dynamic Type: support on screen titles, restaurant names, body and prices; keep the rating pill, veg/non-veg mark, Bestseller tag, tab labels and the ADD label at FIXED sizes
- Tabular numerals: apply `.monospacedDigit()` to ratings, prices, review counts and quantity counters
- VoiceOver: the rating pill must announce **both** the score and its semantic meaning ("4.3 stars, rated great") since sighted users read quality from color; the veg/non-veg mark announces "Vegetarian" / "Non-vegetarian" (never rely on the color alone); menu rows combine veg status + name + price + rating
- The ADD button is "Add {dish}"; after tap it becomes a stepper announced as "{dish}, quantity 1, stepper"
- Color contrast: `#1C1C1C` on `#FFFFFF` passes WCAG AAA; white on the rating greens/ambers/reds passes AA for the pill score; the semantic scale must be paired with the star + accessible label so it is not color-only
- Reduce Motion: disable the ADD→stepper expand and the toggle's sliding fill (cross-fade instead); keep the rating pill static (the color is the signal, not motion)
- Dark mode: invert to the `zDark*` palette — warm `#121212`, NOT true black; add a 0.5pt divider border to floating surfaces (ADD button, sticky cart, customize sheet) since shadows are barely visible on dark
