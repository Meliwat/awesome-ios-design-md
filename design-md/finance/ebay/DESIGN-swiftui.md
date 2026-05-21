# eBay (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates eBay's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let ebayCanvas        = Color.white                                   // #FFFFFF
    static let ebaySurface       = Color(red: 0.969, green: 0.969, blue: 0.969)  // #F7F7F7
    static let ebayDivider       = Color(red: 0.898, green: 0.898, blue: 0.898)  // #E5E5E5
    static let ebaySurfacePressed = Color(red: 0.937, green: 0.937, blue: 0.937) // #EFEFEF

    // MARK: - Text
    static let ebayTextPrimary   = Color(red: 0.098, green: 0.098, blue: 0.098)  // #191919
    static let ebayTextSecondary = Color(red: 0.439, green: 0.439, blue: 0.439)  // #707070
    static let ebayTextTertiary  = Color(red: 0.608, green: 0.608, blue: 0.608)  // #9B9B9B

    // MARK: - Brand (four-color)
    static let ebayBlue        = Color(red: 0.000, green: 0.392, blue: 0.824)    // #0064D2
    static let ebayBluePressed = Color(red: 0.000, green: 0.314, blue: 0.659)    // #0050A8
    static let ebayRed         = Color(red: 0.898, green: 0.196, blue: 0.220)    // #E53238
    static let ebayYellow      = Color(red: 0.961, green: 0.686, blue: 0.008)    // #F5AF02
    static let ebayGreen       = Color(red: 0.525, green: 0.722, blue: 0.090)    // #86B817

    // MARK: - Semantic
    static let ebayFreeShip = Color(red: 0.145, green: 0.525, blue: 0.208)       // #258635
    static let ebayErrorRed = Color(red: 0.780, green: 0.000, blue: 0.043)       // #C7000B
}
```

## 2. Typography

Market Sans is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or substitute Inter / fall back to `.system` (SF Pro). Use monospaced digits for the "Time left" countdown.

```swift
extension Font {
    static let ebayTitleLarge = Font.custom("Inter-Bold",     size: 28).weight(.bold)
    static let ebayListing    = Font.custom("Inter-Bold",     size: 22).weight(.bold)
    static let ebaySection    = Font.custom("Inter-Bold",     size: 20).weight(.bold)
    static let ebayPrice      = Font.custom("Inter-Bold",     size: 24).weight(.bold)
    static let ebayCardPrice  = Font.custom("Inter-Bold",     size: 17).weight(.bold)
    static let ebayCardTitle  = Font.custom("Inter-Regular",  size: 15).weight(.regular)
    static let ebayBody       = Font.custom("Inter-Regular",  size: 15).weight(.regular)
    static let ebayButton     = Font.custom("Inter-Bold",     size: 16).weight(.bold)
    static let ebayBadge      = Font.custom("Inter-Bold",     size: 12).weight(.bold)
    static let ebayMeta       = Font.custom("Inter-Regular",  size: 13).weight(.regular)
    static let ebayTimeLeft   = Font.custom("Inter-Bold",     size: 13).weight(.bold)
    static let ebayTab        = Font.custom("Inter-SemiBold", size: 11).weight(.semibold)
    static let ebayCondition  = Font.custom("Inter-SemiBold", size: 11).weight(.semibold)
}

extension Font {
    static func ebay(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Bid vs Buy-It-Now Badge (the signature)

```swift
struct BuyMechanicBadge: View {
    enum Mechanic {
        case buyItNow
        case auction(bids: Int)
    }
    let mechanic: Mechanic
    var bestOffer: Bool = false

    var body: some View {
        HStack(spacing: 6) {
            switch mechanic {
            case .buyItNow:
                Text("BUY IT NOW")
                    .font(.ebayBadge)
                    .foregroundStyle(Color.ebayBlue)
                    .padding(.vertical, 4).padding(.horizontal, 8)
                    .background(RoundedRectangle(cornerRadius: 4).fill(Color.ebayBlue.opacity(0.10)))
            case .auction(let bids):
                Text("\(bids) BID\(bids == 1 ? "" : "S")")
                    .font(.ebayBadge)
                    .foregroundStyle(Color.ebayTextPrimary)
                    .padding(.vertical, 4).padding(.horizontal, 8)
                    .background(RoundedRectangle(cornerRadius: 4).fill(Color.ebaySurface))
            }
            if bestOffer {
                Text("or Best Offer")
                    .font(.ebayMeta)
                    .foregroundStyle(Color.ebayGreen)
            }
        }
    }
}
```

### "Time Left" Countdown

```swift
struct TimeLeft: View {
    let secondsRemaining: Int

    private var urgent: Bool { secondsRemaining < 86_400 }   // < 24h
    private var sub1h: Bool  { secondsRemaining < 3_600 }

    private var label: String {
        let d = secondsRemaining / 86_400
        let h = (secondsRemaining % 86_400) / 3_600
        let m = (secondsRemaining % 3_600) / 60
        let s = secondsRemaining % 60
        if d > 0 { return "\(d)d \(h)h left" }
        if h > 0 { return "\(h)h \(m)m left" }
        return "\(m)m \(s)s left"
    }

    var body: some View {
        Text(label)
            .font(.ebayTimeLeft)
            .monospacedDigit()   // tabular — never jitters
            .foregroundStyle(urgent ? Color.ebayRed : Color.ebayTextSecondary)
    }
}
```

### Watch Heart

```swift
struct WatchHeart: View {
    @Binding var watching: Bool
    var overPhoto: Bool = true

    var body: some View {
        Button {
            watching.toggle()
        } label: {
            Image(systemName: watching ? "heart.fill" : "heart")
                .font(.system(size: 20, weight: .semibold))
                .foregroundStyle(watching ? Color.ebayRed : (overPhoto ? .white : .ebayTextPrimary))
                .padding(overPhoto ? 8 : 0)
                .background(overPhoto ? Circle().fill(.black.opacity(0.40)) : nil)
                .scaleEffect(watching ? 1.0 : 1.0)
        }
        .sensoryFeedback(.success, trigger: watching)
        .animation(.spring(response: 0.26, dampingFraction: 0.55), value: watching)
        .accessibilityLabel(watching ? "Watching this item" : "Add to watchlist")
    }
}
```

### Listing Card

```swift
struct ListingCard: View {
    let title: String
    let photo: Image
    let price: String
    let original: String?
    let mechanic: BuyMechanicBadge.Mechanic
    let bestOffer: Bool
    let freeShipping: Bool
    let secondsLeft: Int?
    @State private var watching = false

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            ZStack(alignment: .topTrailing) {
                photo
                    .resizable()
                    .aspectRatio(1, contentMode: .fill)
                    .frame(maxWidth: .infinity)
                    .clipShape(RoundedRectangle(cornerRadius: 8))
                WatchHeart(watching: $watching).padding(8)
            }
            Text(title).font(.ebayCardTitle).foregroundStyle(.ebayTextPrimary).lineLimit(2)
            HStack(alignment: .firstTextBaseline, spacing: 6) {
                Text(price).font(.ebayCardPrice).foregroundStyle(.ebayTextPrimary)
                if let original {
                    Text(original)
                        .font(.ebayMeta)
                        .strikethrough()
                        .foregroundStyle(.ebayTextTertiary)
                }
            }
            BuyMechanicBadge(mechanic: mechanic, bestOffer: bestOffer)
            HStack(spacing: 8) {
                if freeShipping {
                    Text("Free shipping").font(.ebayMeta).foregroundStyle(Color.ebayFreeShip)
                }
                if let s = secondsLeft { TimeLeft(secondsRemaining: s) }
            }
        }
        .padding(8)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.ebayCanvas))
        .shadow(color: .black.opacity(0.06), radius: 4, y: 1)
        .contentShape(Rectangle())
    }
}
```

### Primary / Bid Buttons

```swift
struct EbayPrimaryButton: View {
    let title: String
    var style: Style = .filled
    let action: () -> Void
    enum Style { case filled, bidOutline }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.ebayButton)
                .foregroundStyle(style == .filled ? .white : Color.ebayBlue)
                .frame(maxWidth: .infinity)
                .padding(.vertical, style == .filled ? 15 : 13)
                .background(
                    Capsule().fill(style == .filled ? Color.ebayBlue : .clear)
                )
                .overlay(
                    Capsule().strokeBorder(style == .bidOutline ? Color.ebayBlue : .clear, lineWidth: 1.5)
                )
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: title)
        .buttonStyle(EbayPressableStyle())
    }
}

struct EbayPressableStyle: ButtonStyle {
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
        appearance.backgroundColor = UIColor.white.withAlphaComponent(0.97)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            SavedView().tabItem { Label("Saved", systemImage: "heart.fill") }
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell.fill") }
            MyEbayView().tabItem { Label("My eBay", systemImage: "person.fill") }
            SellingView().tabItem { Label("Selling", systemImage: "tag.fill") }
        }
        .tint(.ebayBlue) // active = eBay Blue
    }
}
```

## 5. Motion

```swift
// Watch-heart toggle — see WatchHeart (spring dampingFraction 0.55 → ~1.2 overshoot, success haptic)
.sensoryFeedback(.success, trigger: watching)

// Bid placement — present a confirm sheet; on success tick the bid number + increment count
.sensoryFeedback(.impact(weight: .medium), trigger: bidPlaced)

// "Time left" tick — drive from a TimelineView(.periodic(from:.now, by:1)); .monospacedDigit() prevents jitter

// Card tap — scale 0.98 spring (see EbayPressableStyle)

// Photo carousel — light haptic per page
.sensoryFeedback(.selection, trigger: page)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Watch | `heart` / `heart.fill` | 24pt |
| Search | `magnifyingglass` | 18pt |
| Visual search | `camera` | 18pt |
| Star (feedback) | `star.fill` | 12pt |
| Share | `square.and.arrow.up` | 22pt |
| Back | `chevron.left` | 20pt |
| Cart | `cart` / `cart.fill` | 22pt |
| Filters | `slider.horizontal.3` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Saved (tab) | `heart.fill` | 24pt |
| Notifications (tab) | `bell.fill` | 24pt |
| My eBay (tab) | `person.fill` | 24pt |
| Selling (tab) | `tag.fill` | 24pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on listing title, price, body, card title (2-line clamp) — pin buy-mechanic badge, condition tag, "Time left", and tab labels (layout-sensitive)
- VoiceOver: never rely on color alone for the buy mechanic — set `.accessibilityLabel("Buy It Now")` vs `"Auction, 12 bids"` on the badge; announce the watch-heart as a toggle; read "Time left" as "1 day 4 hours remaining"
- Tabular figures: use `.monospacedDigit()` on the countdown so the seconds don't reflow and VoiceOver re-reads stay stable
- Contrast: `#707070` on `#FFFFFF` passes WCAG AA at 13pt+. Validate eBay Yellow `#F5AF02` — it fails as text on white at small sizes, so use it for star fills and accents, not body text
- The four brand colors are equity-locked — do not adjust their hues for theming; only swap surrounding neutrals for dark mode
