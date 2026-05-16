# Booking.com (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Booking.com's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views — including the signature review-score badge and the dense property card.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let bkCanvas      = Color.white                                  // #FFFFFF
    static let bkSurface     = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
    static let bkSurfaceDeep = Color(red: 0.902, green: 0.902, blue: 0.902) // #E6E6E6
    static let bkDivider     = Color(red: 0.878, green: 0.878, blue: 0.878) // #E0E0E0

    // MARK: - Text
    static let bkTextPrimary   = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let bkTextSecondary = Color(red: 0.420, green: 0.420, blue: 0.420) // #6B6B6B
    static let bkTextTertiary  = Color(red: 0.580, green: 0.580, blue: 0.580) // #949494

    // MARK: - Brand
    static let bkBlue         = Color(red: 0.0,   green: 0.208, blue: 0.502) // #003580
    static let bkCTA          = Color(red: 0.0,   green: 0.443, blue: 0.761) // #0071C2
    static let bkCTAPressed   = Color(red: 0.0,   green: 0.353, blue: 0.612) // #005A9C
    static let bkBlueTint     = Color(red: 0.906, green: 0.941, blue: 0.969) // #E7F0F7
    static let bkYellow       = Color(red: 0.996, green: 0.733, blue: 0.008) // #FEBB02

    // MARK: - Semantic
    static let bkSuccess = Color(red: 0.0,   green: 0.502, blue: 0.035) // #008009
    static let bkDeal    = Color(red: 0.8,   green: 0.0,   blue: 0.0)   // #CC0000
    static let bkWarning = Color(red: 0.961, green: 0.651, blue: 0.137) // #F5A623
}
```

## 2. Typography

Booking.com uses a BookingSans-equivalent grotesque — substitute Inter. Bundle Inter via `Info.plist` (`UIAppFonts`); fall back to `.system(design: .default)`.

```swift
extension Font {
    static let bkScreenTitle = Font.custom("Inter-Bold",     size: 22)
    static let bkSection     = Font.custom("Inter-Bold",     size: 18)
    static let bkPropName    = Font.custom("Inter-Bold",     size: 17)
    static let bkPrice       = Font.custom("Inter-Bold",     size: 17)
    static let bkScoreNum    = Font.custom("Inter-Bold",     size: 15)
    static let bkBody        = Font.custom("Inter-Regular",  size: 15)
    static let bkButton      = Font.custom("Inter-Bold",     size: 16)
    static let bkScoreWord   = Font.custom("Inter-Bold",     size: 14)
    static let bkMeta        = Font.custom("Inter-Regular",  size: 13)
    static let bkTag         = Font.custom("Inter-SemiBold", size: 12)
    static let bkTab         = Font.custom("Inter-SemiBold", size: 11)
    static let bkCaption     = Font.custom("Inter-Regular",  size: 11)
}

extension Font {
    static func bk(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

Prices and review scores use tabular figures — apply `.monospacedDigit()`.

## 3. Signature Components

### Review-Score Badge

```swift
struct ReviewScoreBadge: View {
    let score: Double          // 8.9
    let reviews: Int           // 1284

    private var word: String {
        switch score {
        case 9...:   "Fabulous"
        case 8..<9:  "Very good"
        case 7..<8:  "Good"
        default:     "Pleasant"
        }
    }

    var body: some View {
        HStack(spacing: 6) {
            Text(String(format: "%.1f", score))   // always one decimal
                .font(.bkScoreNum).monospacedDigit()
                .foregroundStyle(.white)
                .padding(.horizontal, 7).padding(.vertical, 4)
                .background(RoundedRectangle(cornerRadius: 5).fill(Color.bkBlue))
            Text(word)
                .font(.bkScoreWord)
                .foregroundStyle(Color.bkTextPrimary)
            Text("· \(reviews.formatted()) reviews")
                .font(.bkTag).fontWeight(.regular)
                .foregroundStyle(Color.bkTextSecondary)
        }
    }
}
```

### Property Card (dense, border-defined)

```swift
struct PropertyCard: View {
    let photo: Image
    let name: String
    let area: String
    let distance: String
    let score: Double
    let reviews: Int
    let originalPrice: String?
    let price: String
    let scarcity: String?

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            photo
                .resizable().scaledToFill()
                .frame(width: 120, height: 120)
                .clipShape(RoundedRectangle(cornerRadius: 8))

            VStack(alignment: .leading, spacing: 4) {
                Text(name).font(.bkPropName).foregroundStyle(Color.bkTextPrimary).lineLimit(2)
                HStack(spacing: 6) {
                    Text(area).font(.bkMeta).foregroundStyle(Color.bkCTA)
                    Text(distance).font(.bkMeta).foregroundStyle(Color.bkTextSecondary)
                }
                Text("Free cancellation")
                    .font(.bkMeta).foregroundStyle(Color.bkSuccess)
                ReviewScoreBadge(score: score, reviews: reviews)

                Spacer(minLength: 4)

                VStack(alignment: .trailing, spacing: 1) {
                    if let originalPrice {
                        Text(originalPrice)
                            .font(.bkMeta).strikethrough()
                            .foregroundStyle(Color.bkTextTertiary)
                    }
                    Text(price).font(.bkPrice).monospacedDigit()
                        .foregroundStyle(Color.bkTextPrimary)
                    Text("Includes taxes and fees")
                        .font(.bkCaption).foregroundStyle(Color.bkTextSecondary)
                    if let scarcity {
                        Text(scarcity)
                            .font(.bkTag).foregroundStyle(Color.bkDeal)
                    }
                }
                .frame(maxWidth: .infinity, alignment: .trailing)
            }
        }
        .padding(12)
        .background(
            RoundedRectangle(cornerRadius: 8).fill(Color.bkCanvas)
                .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.bkDivider, lineWidth: 1))
        )
    }
}
```

### Primary CTA

```swift
struct BookingCTA: View {
    let label: String     // "Search" / "Reserve"
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(label)
                .font(.bkButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 52)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.bkCTA))
        }
        .buttonStyle(BKPressableStyle())
    }
}

struct BKPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .brightness(configuration.isPressed ? -0.04 : 0)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### Search Form Card

```swift
struct SearchFormCard: View {
    var body: some View {
        VStack(spacing: 0) {
            FormRow(system: "mappin.and.ellipse", text: "Where are you going?")
            Divider().overlay(Color.bkDivider)
            FormRow(system: "calendar", text: "Fri 12 Jul — Sun 14 Jul")
            Divider().overlay(Color.bkDivider)
            FormRow(system: "person", text: "2 adults · 0 children · 1 room")
            BookingCTA(label: "Search") {}
                .padding(.top, 12)
        }
        .padding(12)
        .background(
            RoundedRectangle(cornerRadius: 8).fill(Color.bkCanvas)
                .shadow(color: Color.bkTextPrimary.opacity(0.08), radius: 6, y: 2)
        )
    }

    struct FormRow: View {
        let system: String
        let text: String
        var body: some View {
            HStack(spacing: 10) {
                Image(systemName: system)
                    .font(.system(size: 18)).foregroundStyle(Color.bkTextSecondary).frame(width: 22)
                Text(text).font(.bkBody).foregroundStyle(Color.bkTextPrimary)
                Spacer()
            }
            .frame(height: 52)
        }
    }
}
```

### Genius Banner

```swift
struct GeniusBanner: View {
    var body: some View {
        HStack {
            VStack(alignment: .leading, spacing: 2) {
                Text("Genius").font(.bkPropName).foregroundStyle(.white)
                Text("You're a Genius level 1 member — enjoy 10% off")
                    .font(.bkMeta).foregroundStyle(.white.opacity(0.9))
            }
            Spacer()
            Text("10% off")
                .font(.bkTag).foregroundStyle(Color.bkBlue)
                .padding(.horizontal, 8).padding(.vertical, 4)
                .background(Capsule().fill(Color.bkYellow))
        }
        .padding(16)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.bkBlue))
    }
}
```

### Filter Chip

```swift
struct FilterChip: View {
    let label: String
    let isSelected: Bool
    let tap: () -> Void
    var body: some View {
        Button(action: tap) {
            Text(label)
                .font(.bkTag)
                .fontWeight(isSelected ? .bold : .semibold)
                .foregroundStyle(isSelected ? Color.bkCTA : Color.bkTextPrimary)
                .padding(.horizontal, 16).padding(.vertical, 8)
                .background(Capsule().fill(isSelected ? Color.bkBlueTint : Color.bkCanvas))
                .overlay(
                    Capsule().strokeBorder(isSelected ? Color.bkCTA : Color.bkDivider,
                                           lineWidth: isSelected ? 1.5 : 1)
                )
        }
        .buttonStyle(.plain)
        .animation(.easeOut(duration: 0.15), value: isSelected)
    }
}
```

## 4. Map Toggle + Price Pin

```swift
struct PricePin: View {
    let price: String
    let isSelected: Bool
    var body: some View {
        Text(price)
            .font(.bk(13, weight: .bold)).monospacedDigit()
            .foregroundStyle(isSelected ? .white : Color.bkTextPrimary)
            .padding(.horizontal, 10).padding(.vertical, 6)
            .background(Capsule().fill(isSelected ? Color.bkBlue : Color.bkCanvas))
            .shadow(color: Color.bkTextPrimary.opacity(0.16), radius: 6, y: 2)
            .scaleEffect(isSelected ? 1.12 : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: isSelected)
    }
}
// A "Map"/"List" pill toggle floats above the list; selecting a pin slides a single
// PropertyCard up from the bottom (a .sheet at a low detent or a bottom overlay).
```

## 5. Tab Bar

The top app bar is solid Booking Blue. Configure a navy `UINavigationBarAppearance` and a white tab bar with a CTA-blue tint.

```swift
struct RootTabView: View {
    init() {
        let nav = UINavigationBarAppearance()
        nav.configureWithOpaqueBackground()
        nav.backgroundColor = UIColor(Color.bkBlue)
        nav.titleTextAttributes = [.foregroundColor: UIColor.white]
        UINavigationBar.appearance().standardAppearance = nav
        UINavigationBar.appearance().scrollEdgeAppearance = nav

        let tab = UITabBarAppearance()
        tab.configureWithOpaqueBackground()
        tab.backgroundColor = UIColor.white
        tab.shadowColor = UIColor(Color.bkDivider)
        UITabBar.appearance().standardAppearance = tab
        UITabBar.appearance().scrollEdgeAppearance = tab
    }
    var body: some View {
        TabView {
            SearchView().tabItem   { Label("Search",   systemImage: "magnifyingglass") }
            SavedView().tabItem    { Label("Saved",    systemImage: "heart") }
            BookingsView().tabItem { Label("Bookings", systemImage: "suitcase") }
            ProfileView().tabItem  { Label("Profile",  systemImage: "person") }
            HelpView().tabItem     { Label("Help",     systemImage: "questionmark.circle") }
        }
        .tint(.bkCTA) // active = CTA blue
    }
}
```

## 6. Motion

```swift
// Card tap
.scaleEffect(isPressed ? 0.99 : 1)

// Filter chip toggle (~150ms crossfade)
.animation(.easeOut(duration: 0.15), value: isSelected)

// Map pin select (scale 1.0 → 1.12, recolor navy)
.animation(.spring(response: 0.25, dampingFraction: 0.7), value: isSelected)
// then slide the property card up: .transition(.move(edge: .bottom)) + ease-out 0.3s

// CTA press
.scaleEffect(isPressed ? 0.98 : 1)

// Score badge: NO animation — consistency is the brand
// Sheet present (calendar / guests): .presentationDetents + default slide
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search (tab) | `magnifyingglass` | 24pt |
| Saved (tab) | `heart` / `heart.fill` | 24pt |
| Bookings (tab) | `suitcase` / `suitcase.fill` | 24pt |
| Profile (tab) | `person` / `person.fill` | 24pt |
| Help (tab) | `questionmark.circle` | 24pt |
| Destination | `mappin.and.ellipse` | 18pt |
| Dates | `calendar` | 18pt |
| Guests | `person` / `person.2` | 18pt |
| Map toggle | `map` | 18pt |
| Filter | `slider.horizontal.3` | 18pt |
| Star rating | `star.fill` | 12pt |
| Back | `chevron.left` | 18pt |
| Stepper minus / plus | `minus` / `plus` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (the score badge / cards are layout-only). `.presentationDetents` (iOS 16) for calendar/guest sheets
- Support Dynamic Type on property names, body, policy copy; keep prices and the score number tabular and aligned — the badge always shows `X.X`
- VoiceOver: combine the badge + word + count into one label — `.accessibilityElement(children: .combine)` → "Review score 8.9, Fabulous, 1,284 reviews"; the card reads name → score → price → scarcity in reading order
- The CTA reads "Search" / "Reserve"; filter chips use `.accessibilityAddTraits(.isSelected)` when active
- Don't rely on the navy badge color alone — the numeric score and word label carry the meaning for color-blind users
- Contrast: `#6B6B6B` on `#FFFFFF` passes WCAG AA at 13pt+. CTA Blue `#0071C2` on white passes AA for 16pt+ bold; white on Booking Blue `#003580` (badge/banner) is high-contrast. Deal red `#CC0000` on white passes AA — keep scarcity text weight 600
