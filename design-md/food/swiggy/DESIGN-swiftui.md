# Swiggy (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Swiggy's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views (the photo-forward restaurant card, the dish row with the floating ADD button, the rating chip, the floating cart bar).

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let swiggyOrange        = Color(red: 0.988, green: 0.502, blue: 0.098) // #FC8019
    static let swiggyOrangePressed = Color(red: 0.878, green: 0.427, blue: 0.047) // #E06D0C

    // MARK: - Rating & Trust
    static let swiggyRatingGreen = Color(red: 0.282, green: 0.769, blue: 0.475) // #48C479
    static let swiggyRatingAmber = Color(red: 0.859, green: 0.486, blue: 0.220) // #DB7C38
    static let swiggyRatingRed   = Color(red: 0.910, green: 0.290, blue: 0.290) // #E84A4A

    // MARK: - Vertical Accents
    static let swiggyInstamart = Color(red: 0.596, green: 0.173, blue: 0.380) // #982C61
    static let swiggyDineout   = Color(red: 0.843, green: 0.220, blue: 0.369) // #D7385E

    // MARK: - Canvas & Surfaces (Light)
    static let swiggyCanvas        = Color.white                                    // #FFFFFF
    static let swiggySurface1L     = Color(red: 0.957, green: 0.957, blue: 0.965)  // #F4F4F6
    static let swiggySurface2L     = Color(red: 0.925, green: 0.925, blue: 0.933)  // #ECECEE
    static let swiggyDividerL      = Color(red: 0.886, green: 0.886, blue: 0.906)  // #E2E2E7

    // MARK: - Canvas & Surfaces (Dark)
    static let swiggyDarkCanvas    = Color(red: 0.071, green: 0.071, blue: 0.071)  // #121212
    static let swiggyDarkSurface1  = Color(red: 0.110, green: 0.110, blue: 0.118)  // #1C1C1E
    static let swiggyDarkSurface2  = Color(red: 0.165, green: 0.165, blue: 0.165)  // #2A2A2A
    static let swiggyDarkDivider   = Color(red: 0.173, green: 0.173, blue: 0.180)  // #2C2C2E

    // MARK: - Text
    static let swiggyTextPrimaryL   = Color(red: 0.008, green: 0.024, blue: 0.047) // #02060C
    static let swiggyTextSecondaryL = Color(red: 0.408, green: 0.420, blue: 0.471) // #686B78
    static let swiggyTextTertiaryL  = Color(red: 0.576, green: 0.584, blue: 0.624) // #93959F
    static let swiggyTextPrimaryD   = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
    static let swiggyTextSecondaryD = Color(red: 0.627, green: 0.627, blue: 0.627) // #A0A0A0

    // MARK: - Semantic
    static let swiggyError = Color(red: 0.910, green: 0.290, blue: 0.290) // #E84A4A
    static let swiggyInfo  = Color(red: 0.102, green: 0.451, blue: 0.910) // #1A73E8
}
```

## 2. Typography

Swiggy uses a ProximaNova-lineage display face and a Basis-Grotesque-style UI face. Bundle the licensed TTFs via `Info.plist` / `UIAppFonts`; fall back to SF Pro. Numerals are tabular for prices, ETAs, and ratings.

```swift
extension Font {
    // Display (ProximaNova lineage) — headings & actions
    static let swiggyScreenTitle = Font.custom("ProximaNova-Bold",       size: 32).weight(.heavy)
    static let swiggyHero        = Font.custom("ProximaNova-Bold",       size: 26).weight(.heavy)
    static let swiggySection     = Font.custom("ProximaNova-Bold",       size: 22).weight(.bold)
    static let swiggyCardTitle   = Font.custom("ProximaNova-Bold",       size: 18).weight(.heavy)
    static let swiggySubsection  = Font.custom("ProximaNova-Bold",       size: 17).weight(.bold)
    static let swiggyLocation    = Font.custom("ProximaNova-Bold",       size: 17).weight(.heavy)
    static let swiggyButton      = Font.custom("ProximaNova-Bold",       size: 15).weight(.bold)
    static let swiggyAdd         = Font.custom("ProximaNova-Bold",       size: 13).weight(.heavy)
    static let swiggyBadge       = Font.custom("ProximaNova-Bold",       size: 12).weight(.heavy)

    // UI text (Basis Grotesque style)
    static let swiggyBody      = Font.custom("BasisGrotesquePro-Regular", size: 16).weight(.regular)
    static let swiggyDishTitle = Font.custom("BasisGrotesquePro-Medium",  size: 15).weight(.semibold)
    static let swiggyPrice     = Font.custom("BasisGrotesquePro-Medium",  size: 14).weight(.semibold)
    static let swiggyMeta      = Font.custom("BasisGrotesquePro-Regular", size: 14).weight(.regular)
    static let swiggyRating    = Font.custom("BasisGrotesquePro-Bold",    size: 12).weight(.bold)
    static let swiggyCaption   = Font.custom("BasisGrotesquePro-Regular", size: 12).weight(.regular)
    static let swiggyTab       = Font.custom("BasisGrotesquePro-Medium",  size: 10).weight(.semibold)
}

// Tabular numerals for money / time / ratings
extension View {
    func swiggyTabularNumbers() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Rating Chip

```swift
struct RatingChip: View {
    let score: Double

    private var fill: Color {
        switch score {
        case 4.0...: return .swiggyRatingGreen
        case 3.0..<4.0: return .swiggyRatingAmber
        default: return .swiggyRatingRed
        }
    }

    var body: some View {
        HStack(spacing: 3) {
            Image(systemName: "star.fill")
                .font(.system(size: 9))
                .foregroundStyle(.white)
            Text(String(format: "%.1f", score))
                .font(.swiggyRating)
                .foregroundStyle(.white)
                .swiggyTabularNumbers()
        }
        .padding(.vertical, 2)
        .padding(.horizontal, 6)
        .background(RoundedRectangle(cornerRadius: 6).fill(fill))
    }
}
```

### Veg / Non-Veg Mark

```swift
struct DietMark: View {
    let isVeg: Bool
    private var tint: Color { isVeg ? .swiggyRatingGreen : .swiggyError }

    var body: some View {
        ZStack {
            RoundedRectangle(cornerRadius: 3)
                .stroke(tint, lineWidth: 1.5)
                .frame(width: 16, height: 16)
            if isVeg {
                Circle().fill(tint).frame(width: 7, height: 7)
            } else {
                Image(systemName: "triangle.fill")
                    .font(.system(size: 8))
                    .foregroundStyle(tint)
            }
        }
    }
}
```

### Photo-Forward Restaurant Card

```swift
struct RestaurantCard: View {
    let imageURL: String
    let offerTitle: String        // "60% OFF"
    let offerSub: String          // "UPTO ₹120"
    let name: String
    let rating: Double
    let eta: String               // "30–35 mins"
    let cuisines: String          // "Biryani, Andhra, South Indian"
    let locality: String          // "Koramangala · 1.2 km"
    let footer: String            // "Free delivery on orders above ₹199"
    let isAd: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .bottomLeading) {
                AsyncImage(url: URL(string: imageURL)) { $0.resizable().aspectRatio(contentMode: .fill) }
                    placeholder: { Rectangle().fill(Color.swiggyDarkSurface2) }
                    .aspectRatio(16.0/10.0, contentMode: .fill)
                    .clipped()

                LinearGradient(
                    colors: [.clear, .black.opacity(0.78)],
                    startPoint: .init(x: 0.5, y: 0.45), endPoint: .bottom
                )

                VStack(alignment: .leading, spacing: 0) {
                    Text(offerTitle).font(.swiggyCardTitle).foregroundStyle(.white)
                    Text(offerSub).font(.swiggyBadge).foregroundStyle(.white.opacity(0.92))
                }
                .padding(12)

                if isAd {
                    Text("AD")
                        .font(.system(size: 10, weight: .bold))
                        .foregroundStyle(.white)
                        .padding(.vertical, 3).padding(.horizontal, 7)
                        .background(RoundedRectangle(cornerRadius: 5).fill(.black.opacity(0.4)))
                        .padding(12)
                        .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                }

                Image(systemName: "heart")
                    .font(.system(size: 16, weight: .semibold))
                    .foregroundStyle(.white)
                    .frame(width: 32, height: 32)
                    .background(Circle().fill(.black.opacity(0.4)))
                    .padding(12)
                    .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topTrailing)
            }
            .clipShape(RoundedRectangle(cornerRadius: 16))

            Text(name)
                .font(.swiggyCardTitle)
                .foregroundStyle(Color.swiggyTextPrimaryD)
                .padding(.top, 10)

            HStack(spacing: 6) {
                RatingChip(score: rating)
                Text("·").foregroundStyle(Color.swiggyTextTertiaryL)
                Text(eta).font(.swiggyPrice).foregroundStyle(Color.swiggyTextPrimaryD)
            }
            .padding(.top, 6)

            Text(cuisines).font(.swiggyMeta).foregroundStyle(Color.swiggyTextSecondaryD).padding(.top, 4)
            Text(locality).font(.swiggyMeta).foregroundStyle(Color.swiggyTextSecondaryD).padding(.top, 2)

            HStack(spacing: 6) {
                Image(systemName: "clock").font(.system(size: 12, weight: .bold))
                Text(footer).font(.swiggyRating)
            }
            .foregroundStyle(Color.swiggyOrange)
            .padding(.top, 10)
            .overlay(alignment: .top) {
                Line().stroke(style: StrokeStyle(lineWidth: 1, dash: [4, 4]))
                    .frame(height: 1)
                    .foregroundStyle(Color.swiggyDarkDivider)
                    .padding(.top, -5)
            }
        }
        .padding(.bottom, 22)
    }
}

struct Line: Shape {
    func path(in r: CGRect) -> Path {
        var p = Path(); p.move(to: .init(x: r.minX, y: r.midY)); p.addLine(to: .init(x: r.maxX, y: r.midY)); return p
    }
}
```

### Dish Row with Floating ADD Button

```swift
struct DishRow: View {
    let isVeg: Bool
    let name: String
    let price: String       // "₹289"
    let rating: Double
    let ratingCount: String // "1.2K"
    let desc: String
    let imageURL: String
    @State private var qty = 0

    var body: some View {
        HStack(alignment: .top, spacing: 14) {
            VStack(alignment: .leading, spacing: 6) {
                DietMark(isVeg: isVeg)
                Text(name).font(.swiggyDishTitle).foregroundStyle(Color.swiggyTextPrimaryD)
                Text(price).font(.swiggyPrice).foregroundStyle(Color.swiggyTextPrimaryD).swiggyTabularNumbers()
                HStack(spacing: 3) {
                    Image(systemName: "star.fill").font(.system(size: 10))
                    Text("\(String(format: "%.1f", rating)) (\(ratingCount))").font(.swiggyRating)
                }
                .foregroundStyle(Color.swiggyRatingGreen)
                Text(desc).font(.swiggyCaption).foregroundStyle(Color.swiggyTextSecondaryD).lineLimit(2)
            }
            Spacer(minLength: 0)

            ZStack(alignment: .bottom) {
                AsyncImage(url: URL(string: imageURL)) { $0.resizable().aspectRatio(contentMode: .fill) }
                    placeholder: { Rectangle().fill(Color.swiggyDarkSurface2) }
                    .frame(width: 110, height: 110)
                    .clipShape(RoundedRectangle(cornerRadius: 14))

                addControl
                    .offset(y: 12)
            }
            .frame(width: 110)
        }
        .padding(.vertical, 16)
        .overlay(alignment: .bottom) {
            Line().stroke(style: StrokeStyle(lineWidth: 1, dash: [4, 4]))
                .frame(height: 1).foregroundStyle(Color.swiggyDarkDivider)
        }
    }

    @ViewBuilder private var addControl: some View {
        if qty == 0 {
            Button {
                qty = 1
                UIImpactFeedbackGenerator(style: .soft).impactOccurred()
            } label: {
                Text("ADD").font(.swiggyAdd)
            }
            .buttonStyle(AddButtonStyle())
        } else {
            HStack(spacing: 14) {
                Button { qty -= 1 } label: { Text("−").font(.swiggyAdd) }
                Text("\(qty)").font(.swiggyAdd).swiggyTabularNumbers()
                Button { qty += 1 } label: { Text("+").font(.swiggyAdd) }
            }
            .foregroundStyle(Color.swiggyRatingGreen)
            .padding(.vertical, 7).padding(.horizontal, 14)
            .background(addBackground)
        }
    }

    private var addBackground: some View {
        RoundedRectangle(cornerRadius: 10)
            .fill(Color.swiggyDarkSurface2)
            .overlay(RoundedRectangle(cornerRadius: 10).stroke(Color.swiggyRatingGreen, lineWidth: 1))
            .shadow(color: .black.opacity(0.5), radius: 12, y: 4)
    }
}

struct AddButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .foregroundStyle(Color.swiggyRatingGreen)
            .padding(.vertical, 7).padding(.horizontal, 26)
            .background(
                RoundedRectangle(cornerRadius: 10)
                    .fill(Color.swiggyDarkSurface2)
                    .overlay(RoundedRectangle(cornerRadius: 10).stroke(Color.swiggyRatingGreen, lineWidth: 1))
                    .shadow(color: .black.opacity(0.5), radius: 12, y: 4)
            )
            .scaleEffect(configuration.isPressed ? 0.97 : 1)
    }
}
```

### Primary Button

```swift
struct SwiggyPrimaryButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .font(.swiggyButton)
            .textCase(.uppercase)
            .tracking(0.2)
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .padding(.vertical, 14)
            .background(
                RoundedRectangle(cornerRadius: 12)
                    .fill(configuration.isPressed ? Color.swiggyOrangePressed : Color.swiggyOrange)
            )
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
    }
}
```

### Floating Cart Bar

```swift
struct CartBar: View {
    let itemCount: Int
    let total: String  // "₹648"
    let onView: () -> Void

    var body: some View {
        HStack {
            Text("\(itemCount) item\(itemCount == 1 ? "" : "s")  |  \(total)")
                .font(.swiggyCardTitle)
                .foregroundStyle(.white)
                .swiggyTabularNumbers()
            Spacer()
            HStack(spacing: 4) {
                Text("VIEW CART").font(.swiggyAdd)
                Image(systemName: "chevron.right").font(.system(size: 12, weight: .heavy))
            }
            .foregroundStyle(.white)
        }
        .padding(.horizontal, 18)
        .padding(.vertical, 16)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.swiggyOrange))
        .shadow(color: Color.swiggyOrange.opacity(0.35), radius: 16, y: 6)
        .padding(.horizontal, 12)
        .transition(.move(edge: .bottom).combined(with: .opacity))
        .onTapGesture(perform: onView)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct SwiggyTabView: View {
    var body: some View {
        TabView {
            FoodHomeView().tabItem { Label("Swiggy", systemImage: "house.fill") }
            InstamartView().tabItem { Label("Instamart", systemImage: "square.grid.2x2.fill") }
            DineoutView().tabItem { Label("Dineout", systemImage: "paperplane.fill") }
            CartView().tabItem { Label("Cart", systemImage: "cart.fill") }
        }
        .tint(.swiggyOrange) // active = filled glyph in Swiggy Orange, no pill
    }
}
```

## 5. Motion

```swift
// ADD → stepper morph
withAnimation(.easeOut(duration: 0.18)) { qty = 1 }
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Cart bar reveal (first item added)
withAnimation(.spring(response: 0.28, dampingFraction: 0.8)) { cartVisible = true }

// Restaurant card → detail (shared element hero zoom ~320ms)
.matchedGeometryEffect(id: restaurant.id, in: namespace)
withAnimation(.easeOut(duration: 0.32)) { showDetail = true }

// Filter chip select
withAnimation(.easeOut(duration: 0.15)) { selectedFilter = chip }

// Bottom sheet (offers / customise)
.presentationDetents([.medium, .large])
.presentationCornerRadius(20)

// Haptics
UIImpactFeedbackGenerator(style: .soft).impactOccurred()    // ADD tap, chip toggle
UINotificationFeedbackGenerator().notificationOccurred(.success) // Place Order success
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Swiggy / Home (tab) | `house` / `house.fill` | 22pt |
| Instamart (tab) | `square.grid.2x2` / `.fill` | 22pt |
| Dineout (tab) | `paperplane` / `.fill` | 22pt |
| Cart (tab) | `cart` / `cart.fill` | 22pt |
| Location pin | `mappin.circle.fill` | 18pt |
| Location chevron | `chevron.down` | 14pt |
| Search | `magnifyingglass` | 18pt |
| Voice search | `mic.fill` | 18pt |
| Rating star | `star.fill` | 9–11pt |
| Favourite | `heart` / `heart.fill` | 16pt |
| Filter | `slider.horizontal.3` | 12pt |
| Footer (delivery time) | `clock` | 12–13pt |
| Free delivery | `bicycle` | 14pt |
| Back | `chevron.left` | 22pt |
| Share | `square.and.arrow.up` | 20pt |
| Veg/Non-veg triangle | `triangle.fill` | 8pt |
| Cart chevron | `chevron.right` | 12pt |
| Add / qty | `plus` / `minus` | 13pt |

## 7. Dark Mode

```swift
struct SwiggyTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.swiggyDarkCanvas : Color.swiggyCanvas)
            .foregroundStyle(scheme == .dark ? Color.swiggyTextPrimaryD : Color.swiggyTextPrimaryL)
    }
}

extension View {
    func swiggyTheme() -> some View { modifier(SwiggyTheme()) }
}
```

Dark canvas is `#121212` (near-black, never pure black). Cards lift to `#1C1C1E`, the ADD background to `#2A2A2A`. Swiggy Orange `#FC8019` and the rating green `#48C479` are identical across themes. Food photography stays full-saturation — only UI chrome dims. Bottom sheets gain a 1pt `#2C2C2E` top border as the dark elevation cue.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`presentationCornerRadius` requires iOS 16.4; spring `response:` API is iOS 17 — fall back to `.spring()` defaults on 16)
- Bundle the licensed ProximaNova-lineage display face and Basis-Grotesque-style UI face via `Info.plist`; fall back to SF Pro Display / SF Pro Text
- Dynamic Type: support on screen titles, section headers, body, dish descriptions; keep rating chips, offer badges, tab labels, veg marks, and the qty stepper FIXED (layout-critical)
- VoiceOver: announce restaurant cards as "{name}, rated {score}, {eta}, {cuisines}"; the rating chip as "Rated {score} out of 5"; the ADD button as "Add {dish} to cart"; the diet mark as "Vegetarian" / "Non-vegetarian"
- The ADD button and stepper must each be ≥ 44pt hit area even though the visual is ~32pt tall
- Color contrast: white on `#FC8019` and on `#48C479` passes WCAG AA at button sizes; `#F2F2F2` on `#121212` passes AA for body
- Use `monospacedDigit()` on every price, ETA, and rating so columns don't jitter as quantities change
- Reduce Motion: replace the shared-element hero zoom and the cart-bar spring with crossfades; keep the ADD→stepper as an instant swap
- Dark mode: invert per the `swiggyDark*` palette; never dim food imagery; add the 1pt divider border to floating sheets
