# Airbnb (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Airbnb's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views for the signature stay card, search pill, and sticky booking footer.

Targets iOS 17+. The modern `.sensoryFeedback`, `ScrollView`-based category bars, and `matchedGeometryEffect` on the search pill all assume iOS 17.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let airbnbCanvas       = Color(red: 1.00, green: 1.00, blue: 1.00)   // #FFFFFF
    static let airbnbSurfaceGray  = Color(red: 0.969, green: 0.969, blue: 0.969) // #F7F7F7
    static let airbnbSurfaceGray2 = Color(red: 0.922, green: 0.922, blue: 0.922) // #EBEBEB
    static let airbnbDivider      = Color(red: 0.922, green: 0.922, blue: 0.922) // #EBEBEB

    // MARK: - Text
    static let airbnbHof          = Color(red: 0.282, green: 0.282, blue: 0.282) // #484848 primary text
    static let airbnbFoggy        = Color(red: 0.463, green: 0.463, blue: 0.463) // #767676 secondary text
    static let airbnbFoggyLight   = Color(red: 0.690, green: 0.690, blue: 0.690) // #B0B0B0 tertiary
    static let airbnbInk          = Color(red: 0.133, green: 0.133, blue: 0.133) // #222222 hero titles

    // MARK: - Brand
    static let airbnbCoral        = Color(red: 1.00, green: 0.220, blue: 0.361)  // #FF385C primary
    static let airbnbCoralPressed = Color(red: 0.890, green: 0.110, blue: 0.373) // #E31C5F
    static let airbnbRausch       = Color(red: 1.00, green: 0.353, blue: 0.373)  // #FF5A5F heritage
    static let airbnbBabu         = Color(red: 0.00, green: 0.651, blue: 0.600)  // #00A699 Plus / Experiences
    static let airbnbArches       = Color(red: 0.988, green: 0.392, blue: 0.176) // #FC642D Trips
    static let airbnbBeach        = Color(red: 1.00, green: 0.706, blue: 0.00)   // #FFB400 star yellow

    // MARK: - Semantic
    static let airbnbSuccess      = Color(red: 0.00, green: 0.541, blue: 0.020)  // #008A05
    static let airbnbError        = Color(red: 0.757, green: 0.208, blue: 0.082) // #C13515

    // MARK: - Dark mode
    static let airbnbDarkCanvas   = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let airbnbDarkSurface  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let airbnbDarkText     = Color(red: 0.867, green: 0.867, blue: 0.867) // #DDDDDD
    static let airbnbDarkTextSec  = Color(red: 0.627, green: 0.627, blue: 0.627) // #A0A0A0
}
```

## 2. Typography

Airbnb Cereal is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to SF Pro Display/Text — the warmest system face — when Cereal is unavailable.

```swift
extension Font {
    // Large titles / hero
    static let airbnbLargeNav   = Font.custom("AirbnbCereal-ExtraBold", size: 32) // weight 800
    static let airbnbHero       = Font.custom("AirbnbCereal-Bold",      size: 26) // weight 700
    static let airbnbSection    = Font.custom("AirbnbCereal-Bold",      size: 22)
    static let airbnbSubsection = Font.custom("AirbnbCereal-Bold",      size: 18)

    // Cards & body
    static let airbnbCardTitle  = Font.custom("AirbnbCereal-Medium",    size: 15)
    static let airbnbBody       = Font.custom("AirbnbCereal-Book",      size: 16)
    static let airbnbBodySmall  = Font.custom("AirbnbCereal-Book",      size: 14)
    static let airbnbMeta       = Font.custom("AirbnbCereal-Book",      size: 14)
    static let airbnbRatingNum  = Font.custom("AirbnbCereal-Medium",    size: 14)

    // Prices
    static let airbnbPriceInline = Font.custom("AirbnbCereal-Bold",     size: 15)
    static let airbnbPriceHero   = Font.custom("AirbnbCereal-ExtraBold", size: 22)

    // Buttons / labels
    static let airbnbButton     = Font.custom("AirbnbCereal-Bold",      size: 16)
    static let airbnbButtonSm   = Font.custom("AirbnbCereal-Medium",    size: 14)
    static let airbnbTab        = Font.custom("AirbnbCereal-Medium",    size: 10)
    static let airbnbChip       = Font.custom("AirbnbCereal-Medium",    size: 12)
    static let airbnbCaption    = Font.custom("AirbnbCereal-Book",      size: 12)
}

// If Cereal isn't bundled, this fallback keeps the warm system substitute:
extension Font {
    static func airbnb(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default) // SF Pro
    }
}
```

## 3. Signature Components

### Save Heart (top-right of every stay card)

```swift
struct SaveHeart: View {
    @Binding var isSaved: Bool

    var body: some View {
        Button {
            withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) {
                isSaved.toggle()
            }
        } label: {
            Image(systemName: isSaved ? "heart.fill" : "heart")
                .font(.system(size: 20, weight: .semibold))
                .foregroundStyle(isSaved ? Color.airbnbCoral : .white)
                .overlay(
                    Image(systemName: "heart")
                        .font(.system(size: 20, weight: .semibold))
                        .foregroundStyle(isSaved ? .clear : Color.airbnbInk)
                )
                .shadow(color: .black.opacity(0.2), radius: 4, y: 2)
                .frame(width: 32, height: 32)
                .contentShape(Rectangle().inset(by: -12)) // 44pt hit area
                .scaleEffect(isSaved ? 1 : 1)
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isSaved)
    }
}
```

### Rating Row (star + number + review count)

```swift
struct RatingRow: View {
    let rating: Double
    let reviewCount: Int

    var body: some View {
        HStack(spacing: 4) {
            Image(systemName: "star.fill")
                .font(.system(size: 12))
                .foregroundStyle(Color.airbnbHof)
            Text(String(format: "%.2f", rating))
                .font(.airbnbRatingNum)
                .foregroundStyle(Color.airbnbHof)
            Text(" · \(reviewCount.formatted())")
                .font(.airbnbMeta)
                .foregroundStyle(Color.airbnbFoggy)
        }
    }
}
```

### Stay Card (the hero component)

```swift
struct StayCard: View {
    let photos: [Image]
    let title: String
    let host: String
    let dates: String
    let pricePerNight: Int
    let rating: Double
    let reviewCount: Int
    @State private var isSaved = false
    @State private var currentPhoto = 0

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            // Photo carousel w/ save heart overlay
            TabView(selection: $currentPhoto) {
                ForEach(Array(photos.enumerated()), id: \.offset) { idx, photo in
                    photo
                        .resizable()
                        .aspectRatio(4/3, contentMode: .fill)
                        .tag(idx)
                }
            }
            .tabViewStyle(.page(indexDisplayMode: .always))
            .aspectRatio(4/3, contentMode: .fit)
            .clipShape(RoundedRectangle(cornerRadius: 16))
            .overlay(alignment: .topTrailing) {
                SaveHeart(isSaved: $isSaved)
                    .padding(12)
            }

            // Rating row
            RatingRow(rating: rating, reviewCount: reviewCount)
                .padding(.top, 8)

            // Title
            Text(title)
                .font(.airbnbCardTitle)
                .foregroundStyle(Color.airbnbHof)
                .lineLimit(1)
                .padding(.top, 2)

            // Host subtitle
            Text(host)
                .font(.airbnbMeta)
                .foregroundStyle(Color.airbnbFoggy)
                .lineLimit(1)
                .padding(.top, 2)

            // Dates
            Text(dates)
                .font(.airbnbMeta)
                .foregroundStyle(Color.airbnbFoggy)
                .padding(.top, 6)

            // Price
            HStack(spacing: 4) {
                Text("$\(pricePerNight)")
                    .font(.airbnbPriceInline)
                    .foregroundStyle(Color.airbnbHof)
                Text("night")
                    .font(.system(size: 15, weight: .regular))
                    .foregroundStyle(Color.airbnbHof)
            }
            .padding(.top, 4)
        }
        .contentShape(Rectangle())
    }
}
```

### Search Pill (Explore top)

```swift
struct SearchPill: View {
    var action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 12) {
                Image(systemName: "magnifyingglass")
                    .font(.system(size: 16, weight: .semibold))
                    .foregroundStyle(Color.airbnbInk)
                VStack(alignment: .leading, spacing: 2) {
                    Text("Where to?")
                        .font(.custom("AirbnbCereal-Bold", size: 14))
                        .foregroundStyle(Color.airbnbInk)
                    Text("Anywhere · Any week · Add guests")
                        .font(.custom("AirbnbCereal-Book", size: 12))
                        .foregroundStyle(Color.airbnbFoggy)
                }
                Spacer()
                Image(systemName: "slider.horizontal.3")
                    .font(.system(size: 14, weight: .semibold))
                    .foregroundStyle(Color.airbnbInk)
                    .padding(10)
                    .overlay(Circle().strokeBorder(Color.airbnbDivider, lineWidth: 1))
            }
            .padding(.horizontal, 20)
            .frame(height: 56)
            .background(
                Capsule()
                    .fill(Color.airbnbCanvas)
                    .overlay(Capsule().strokeBorder(Color.airbnbDivider, lineWidth: 0.5))
                    .shadow(color: .black.opacity(0.08), radius: 12, y: 4)
                    .shadow(color: .black.opacity(0.04), radius: 2, y: 1)
            )
        }
        .buttonStyle(.plain)
    }
}
```

### Category Bar

```swift
struct CategoryBar: View {
    let categories: [(icon: String, label: String)]
    @Binding var selected: Int
    @Namespace private var underlineNS

    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 32) {
                ForEach(Array(categories.enumerated()), id: \.offset) { idx, cat in
                    VStack(spacing: 8) {
                        Image(systemName: cat.icon)
                            .font(.system(size: 24))
                            .foregroundStyle(selected == idx ? Color.airbnbInk : Color(red: 0.443, green: 0.443, blue: 0.443))
                        Text(cat.label)
                            .font(.airbnbChip)
                            .foregroundStyle(selected == idx ? Color.airbnbInk : Color(red: 0.443, green: 0.443, blue: 0.443))
                        if selected == idx {
                            Rectangle()
                                .fill(Color.airbnbInk)
                                .frame(height: 2)
                                .matchedGeometryEffect(id: "underline", in: underlineNS)
                        } else {
                            Rectangle()
                                .fill(Color.clear)
                                .frame(height: 2)
                        }
                    }
                    .onTapGesture {
                        withAnimation(.spring(response: 0.25, dampingFraction: 0.8)) {
                            selected = idx
                        }
                    }
                }
            }
            .padding(.horizontal, 16)
        }
        .frame(height: 72)
        .background(Color.airbnbCanvas)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.airbnbDivider).frame(height: 0.5)
        }
    }
}
```

### Sticky Booking Footer (Stay Detail bottom)

```swift
struct BookingFooter: View {
    let totalPrice: Int
    let dateRange: String
    var onReserve: () -> Void

    var body: some View {
        HStack(alignment: .center) {
            VStack(alignment: .leading, spacing: 2) {
                HStack(alignment: .firstTextBaseline, spacing: 4) {
                    Text("$\(totalPrice)")
                        .font(.custom("AirbnbCereal-Bold", size: 16))
                        .foregroundStyle(Color.airbnbInk)
                    Text("total")
                        .font(.airbnbBodySmall)
                        .foregroundStyle(Color.airbnbInk)
                }
                Text(dateRange)
                    .font(.airbnbBodySmall)
                    .foregroundStyle(Color.airbnbInk)
                    .underline()
            }
            Spacer()
            Button(action: onReserve) {
                Text("Reserve")
                    .font(.airbnbButton)
                    .foregroundStyle(.white)
                    .padding(.vertical, 14)
                    .padding(.horizontal, 28)
                    .background(
                        RoundedRectangle(cornerRadius: 8)
                            .fill(Color.airbnbCoral)
                    )
            }
            .sensoryFeedback(.impact(weight: .medium), trigger: UUID())
        }
        .padding(.horizontal, 24)
        .frame(height: 80)
        .background(.regularMaterial)
        .overlay(alignment: .top) {
            Rectangle().fill(Color.airbnbDivider).frame(height: 0.5)
        }
    }
}
```

### Map Price Bubble

```swift
struct MapPriceBubble: View {
    let price: Int
    var state: BubbleState = .default

    enum BubbleState { case `default`, visited, selected }

    var bg: Color {
        switch state {
        case .default:  return .white
        case .visited:  return Color.airbnbInk
        case .selected: return Color.airbnbCoral
        }
    }
    var fg: Color {
        state == .default ? Color.airbnbInk : .white
    }

    var body: some View {
        Text("$\(price)")
            .font(.custom("AirbnbCereal-Bold", size: 14))
            .foregroundStyle(fg)
            .padding(.vertical, 6)
            .padding(.horizontal, 10)
            .background(
                RoundedRectangle(cornerRadius: 12).fill(bg)
            )
            .shadow(color: .black.opacity(0.2), radius: 6, y: 2)
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterial)
        appearance.backgroundColor = UIColor.white.withAlphaComponent(0.92)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            ExploreView()   .tabItem { Label("Explore",   systemImage: "magnifyingglass") }
            WishlistsView() .tabItem { Label("Wishlists", systemImage: "heart") }
            TripsView()     .tabItem { Label("Trips",     systemImage: "airplane") }
            InboxView()     .tabItem { Label("Inbox",     systemImage: "message") }
            ProfileView()   .tabItem { Label("Profile",   systemImage: "person.circle") }
        }
        .tint(Color.airbnbCoral) // active = Primary Coral
    }
}
```

## 5. Motion & Haptics

```swift
// Save heart tap
.sensoryFeedback(.impact(flexibility: .soft), trigger: isSaved)

// Reserve CTA tap
.sensoryFeedback(.impact(weight: .medium), trigger: reservePressed)

// Booking confirmed
.sensoryFeedback(.success, trigger: bookingConfirmed)

// Tab switch
.sensoryFeedback(.selection, trigger: selectedTab)

// Save heart spring bounce
withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) { isSaved.toggle() }

// Category bar underline slide
.matchedGeometryEffect(id: "underline", in: underlineNS)

// Search pill → modal shared transition
.matchedGeometryEffect(id: "searchPill", in: searchNS)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Save (unsaved) | `heart` | 20pt |
| Save (saved) | `heart.fill` | 20pt |
| Rating star | `star.fill` | 12pt |
| Search | `magnifyingglass` | 16pt |
| Filters | `slider.horizontal.3` | 14pt |
| Show all photos | `square.grid.2x2.fill` | 14pt |
| Superhost | `checkmark.seal.fill` (Babu) | 14pt |
| Explore tab | `magnifyingglass` | 24pt |
| Wishlists tab | `heart` / `heart.fill` | 24pt |
| Trips tab | `airplane` | 24pt |
| Inbox tab | `message` / `message.fill` | 24pt |
| Profile tab | `person.circle` / `person.circle.fill` | 24pt |
| Wifi amenity | `wifi` | 24pt |
| TV amenity | `tv.fill` | 24pt |
| Parking amenity | `parkingsign.circle` | 24pt |
| Back (detail) | `chevron.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 17 (for `.sensoryFeedback` and `matchedGeometryEffect` on search pill); fall back to `UIImpactFeedbackGenerator` on iOS 16
- Support Dynamic Type on stay card titles, host bylines, dates, body copy, large nav titles — fix rating numbers, tab labels, chip labels, map price bubbles (layout-sensitive)
- VoiceOver: group stay card into a single accessibility element with label `"Private room in Reykjavik, hosted by Sigrún, Superhost, 4.92 stars, 324 reviews, Oct 12 to 17, $214 per night"`; save heart is a separate element: `"Save, button"` / `"Saved, button"`
- Contrast: Hof (`#484848`) on white meets WCAG AA at all sizes; Foggy (`#767676`) on white meets AA only at 18pt+ Bold or 14pt+ Medium — avoid using Foggy for small body text
- Reduce Motion: skip the heart scale bounce and the category underline slide when `accessibilityReduceMotion` is true — fall back to an instant color change
- Dark mode: implement via `@Environment(\.colorScheme)` — swap canvas, text, and surface tokens; Primary Coral stays identical
