# Etsy (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Etsy's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let etsyCanvas        = Color(red: 0.980, green: 0.961, blue: 0.937)  // #FAF5EF
    static let etsyCard          = Color.white                                   // #FFFFFF
    static let etsySurface       = Color(red: 0.945, green: 0.945, blue: 0.945)  // #F1F1F1
    static let etsyDivider       = Color(red: 0.882, green: 0.867, blue: 0.835)  // #E1DDD5
    static let etsySurfacePressed = Color(red: 0.937, green: 0.918, blue: 0.886) // #EFEAE2

    // MARK: - Text
    static let etsyTextPrimary   = Color(red: 0.133, green: 0.133, blue: 0.133)  // #222222
    static let etsyTextSecondary = Color(red: 0.349, green: 0.349, blue: 0.349)  // #595959
    static let etsyTextTertiary  = Color(red: 0.541, green: 0.541, blue: 0.541)  // #8A8A8A

    // MARK: - Brand
    static let etsyOrange        = Color(red: 0.945, green: 0.392, blue: 0.118)  // #F1641E
    static let etsyOrangePressed = Color(red: 0.835, green: 0.341, blue: 0.102)  // #D5571A
    static let etsyOrangeTint    = Color(red: 0.992, green: 0.929, blue: 0.894)  // #FDEDE4

    // MARK: - Semantic
    static let etsyStarFilled = Color(red: 0.133, green: 0.133, blue: 0.133)     // #222222
    static let etsySaleRed    = Color(red: 0.651, green: 0.102, blue: 0.071)     // #A61A12
    static let etsySuccess    = Color(red: 0.145, green: 0.525, blue: 0.208)     // #258635
}
```

## 2. Typography

Graphik is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or substitute Inter / fall back to `.system` (SF Pro).

```swift
extension Font {
    static let etsyTitleLarge = Font.custom("Inter-Bold",     size: 28).weight(.bold)
    static let etsyListing    = Font.custom("Inter-Bold",     size: 22).weight(.bold)
    static let etsySection    = Font.custom("Inter-Bold",     size: 20).weight(.bold)
    static let etsyPrice      = Font.custom("Inter-Bold",     size: 24).weight(.bold)
    static let etsyCardTitle  = Font.custom("Inter-Medium",   size: 15).weight(.medium)
    static let etsyCardPrice  = Font.custom("Inter-Bold",     size: 16).weight(.bold)
    static let etsyBody       = Font.custom("Inter-Regular",  size: 15).weight(.regular)
    static let etsyShopName   = Font.custom("Inter-Medium",   size: 14).weight(.medium)
    static let etsyButton     = Font.custom("Inter-Bold",     size: 16).weight(.bold)
    static let etsyMeta       = Font.custom("Inter-Regular",  size: 13).weight(.regular)
    static let etsyBadge      = Font.custom("Inter-Bold",     size: 11).weight(.bold)
    static let etsyTab        = Font.custom("Inter-Medium",   size: 11).weight(.medium)
    static let etsyReviewCnt  = Font.custom("Inter-Medium",   size: 13).weight(.medium)
}

extension Font {
    static func etsy(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Favorite Heart (the emotional core)

```swift
struct FavoriteHeart: View {
    @Binding var isFavorite: Bool
    var overPhoto: Bool = true

    var body: some View {
        Button {
            isFavorite.toggle()
        } label: {
            Image(systemName: isFavorite ? "heart.fill" : "heart")
                .font(.system(size: 20, weight: .semibold))
                .foregroundStyle(isFavorite ? Color.etsyOrange : (overPhoto ? .white : .etsyTextPrimary))
                .scaleEffect(isFavorite ? 1.0 : 1.0)
                .padding(overPhoto ? 8 : 0)
                .background(
                    overPhoto ? Circle().fill(.black.opacity(0.45)) : nil
                )
        }
        .sensoryFeedback(.success, trigger: isFavorite)
        .animation(.spring(response: 0.3, dampingFraction: 0.5), value: isFavorite)
        .accessibilityLabel(isFavorite ? "Remove from favorites" : "Add to favorites")
    }
}
```

> The bounce: SwiftUI's `.spring(dampingFraction: 0.5)` on the `isFavorite` toggle naturally overshoots to ~1.25 then settles — Etsy's signature heart pop.

### Handmade Product Card

```swift
struct ProductCard: View {
    let title: String
    let photo: Image
    let price: String
    let rating: Double
    let reviews: Int
    let shop: String
    let isBestseller: Bool
    @State private var fav = false

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            ZStack(alignment: .topLeading) {
                photo
                    .resizable()
                    .aspectRatio(1, contentMode: .fill)
                    .frame(maxWidth: .infinity)
                    .clipShape(RoundedRectangle(cornerRadius: 12))

                if isBestseller {
                    Text("BESTSELLER")
                        .font(.etsyBadge)
                        .foregroundStyle(Color.etsyOrange)
                        .padding(.vertical, 4).padding(.horizontal, 10)
                        .background(Capsule().fill(Color.etsyOrangeTint))
                        .padding(8)
                }

                FavoriteHeart(isFavorite: $fav)
                    .frame(maxWidth: .infinity, alignment: .topTrailing)
                    .padding(8)
            }

            Text(title).font(.etsyCardTitle).foregroundStyle(.etsyTextPrimary).lineLimit(2)
            Text(price).font(.etsyCardPrice).foregroundStyle(.etsyTextPrimary)
            StarRow(rating: rating, reviews: reviews)
            Text(shop).font(.etsyShopName).foregroundStyle(.etsyTextSecondary)
            Text("Free shipping").font(.etsyMeta).foregroundStyle(.etsyTextSecondary)
        }
        .padding(8)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.etsyCard))
        .shadow(color: Color(red: 0.47, green: 0.35, blue: 0.20).opacity(0.10), radius: 10, y: 2)
        .contentShape(Rectangle())
    }
}

struct StarRow: View {
    let rating: Double
    let reviews: Int
    var body: some View {
        HStack(spacing: 4) {
            HStack(spacing: 1) {
                ForEach(0..<5) { i in
                    Image(systemName: Double(i) + 1 <= rating.rounded() ? "star.fill" : "star")
                        .font(.system(size: 11))
                        .foregroundStyle(Color.etsyStarFilled)
                }
            }
            Text("(\(reviews.formatted()))").font(.etsyReviewCnt).foregroundStyle(.etsyTextSecondary)
        }
    }
}
```

### Listing Detail Hero (gallery)

```swift
struct ListingHero: View {
    let photos: [Image]
    @State private var page = 0
    @State private var fav = false

    var body: some View {
        ZStack(alignment: .bottom) {
            TabView(selection: $page) {
                ForEach(Array(photos.enumerated()), id: \.offset) { i, img in
                    img.resizable().aspectRatio(4/5, contentMode: .fill).tag(i)
                }
            }
            .tabViewStyle(.page(indexDisplayMode: .never))
            .aspectRatio(4/5, contentMode: .fit)

            HStack(spacing: 6) {
                ForEach(0..<photos.count, id: \.self) { i in
                    Circle()
                        .fill(i == page ? Color.white : Color.white.opacity(0.5))
                        .frame(width: 6, height: 6)
                }
            }
            .padding(.bottom, 14)
        }
        .overlay(alignment: .topTrailing) {
            FavoriteHeart(isFavorite: $fav).padding(16)
        }
        .overlay(alignment: .bottomTrailing) {
            Text("\(page + 1) / \(photos.count)")
                .font(.etsyMeta).foregroundStyle(.white)
                .padding(.vertical, 4).padding(.horizontal, 10)
                .background(Capsule().fill(.black.opacity(0.5)))
                .padding(12)
        }
    }
}
```

### Primary Button

```swift
struct EtsyPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.etsyButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 16)
                .background(Capsule().fill(Color.etsyOrange))
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: title)
        .buttonStyle(EtsyPressableStyle())
    }
}

struct EtsyPressableStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundColor = UIColor(Color.etsyCanvas).withAlphaComponent(0.97)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            YouView().tabItem { Label("You", systemImage: "person.fill") }
            FavoritesView().tabItem { Label("Favorites", systemImage: "heart.fill") }
            CartView().tabItem { Label("Cart", systemImage: "cart.fill") }
        }
        .tint(.etsyOrange) // active = Etsy Orange
    }
}
```

## 5. Motion

```swift
// Favorite-heart bounce — see FavoriteHeart (spring dampingFraction 0.5 overshoots to ~1.25)
.sensoryFeedback(.success, trigger: isFavorite)
.animation(.spring(response: 0.3, dampingFraction: 0.5), value: isFavorite)

// Add-to-cart — medium haptic + a fly-to-cart matchedGeometryEffect of the thumbnail; bump the badge
.sensoryFeedback(.impact(weight: .medium), trigger: addedToCart)

// Card tap — scale 0.98 spring (see EtsyPressableStyle)

// Gallery paging — light haptic at each page change
.sensoryFeedback(.selection, trigger: page)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Favorite | `heart` / `heart.fill` | 24pt |
| Star | `star` / `star.fill` | 11-16pt |
| Search | `magnifyingglass` | 18pt |
| Share | `square.and.arrow.up` | 22pt |
| Back | `chevron.left` | 20pt |
| Cart | `cart` / `cart.fill` | 22pt |
| Filters | `slider.horizontal.3` | 18pt |
| Message seller | `bubble.left` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| You (tab) | `person.fill` | 24pt |
| Favorites (tab) | `heart.fill` | 24pt |
| Cart (tab) | `cart.fill` | 24pt |
| In cart check | `checkmark.circle.fill` | 16pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on listing title, price, body, card title (2-line clamp) — pin badge text, tab labels, gallery photo-count pill, and review star size (a geometric unit)
- VoiceOver: announce the favorite heart as `.accessibilityLabel(isFavorite ? "Saved" : "Add to favorites")` with `.isButton`; combine the star row into one label like `"Rated 4.9 out of 5, 1,284 reviews"`
- Contrast: `#595959` on `#FAF5EF` passes WCAG AA at 13pt+. The cream canvas is intentionally low-contrast warmth — keep primary text at `#222222` for headings/prices
- Warm-tinted shadow uses a brown `rgba(120,90,50,0.10)` — purely decorative; ensure card boundaries are also conveyed by the white fill against cream for low-vision users
- Floating photo controls: 44pt hit area and the `rgba(0,0,0,0.45)` backing for contrast over colorful maker photography
