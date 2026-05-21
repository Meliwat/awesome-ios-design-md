# Tripadvisor (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Tripadvisor's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let taCanvas        = Color.white                                    // #FFFFFF
    static let taSurface       = Color(red: 0.949, green: 0.949, blue: 0.949)   // #F2F2F2
    static let taDivider       = Color(red: 0.878, green: 0.878, blue: 0.878)   // #E0E0E0
    static let taSurfacePressed = Color(red: 0.910, green: 0.910, blue: 0.910)  // #E8E8E8

    // MARK: - Text
    static let taTextPrimary   = Color.black                                    // #000000
    static let taTextSecondary = Color(red: 0.420, green: 0.420, blue: 0.420)   // #6B6B6B
    static let taTextTertiary  = Color(red: 0.608, green: 0.608, blue: 0.608)   // #9B9B9B

    // MARK: - Brand
    static let taGreen         = Color(red: 0.204, green: 0.878, blue: 0.631)   // #34E0A1
    static let taGreenPressed  = Color(red: 0.129, green: 0.773, blue: 0.537)   // #21C589
    static let taOwlBlack      = Color.black                                    // #000000

    // MARK: - Semantic
    static let taEmptyBubble   = Color(red: 0.851, green: 0.851, blue: 0.851)   // #D9D9D9
    static let taErrorRed      = Color(red: 0.839, green: 0.071, blue: 0.180)   // #D6122E
}
```

## 2. Typography

Trip Sans is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or substitute Inter / fall back to `.system` — SF Pro's humanist quality is the closest free match.

```swift
extension Font {
    static let taTitleLarge  = Font.custom("Inter-Bold",     size: 28).weight(.bold)
    static let taPlaceHero   = Font.custom("Inter-Bold",     size: 24).weight(.bold)
    static let taSection     = Font.custom("Inter-Bold",     size: 22).weight(.bold)
    static let taPlaceName   = Font.custom("Inter-Bold",     size: 17).weight(.bold)
    static let taCardTitle   = Font.custom("Inter-SemiBold", size: 16).weight(.semibold)
    static let taReviewBody  = Font.custom("Inter-Regular",  size: 15).weight(.regular)
    static let taButton      = Font.custom("Inter-Bold",     size: 16).weight(.bold)
    static let taButtonSec   = Font.custom("Inter-SemiBold", size: 15).weight(.semibold)
    static let taMeta        = Font.custom("Inter-Regular",  size: 13).weight(.regular)
    static let taTab         = Font.custom("Inter-SemiBold", size: 11).weight(.semibold)
    static let taBadge       = Font.custom("Inter-Bold",     size: 11).weight(.bold)
    static let taCaption     = Font.custom("Inter-Regular",  size: 12).weight(.regular)
}

extension Font {
    static func ta(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Five-Circle Bubble Rating

```swift
struct BubbleRating: View {
    let value: Double          // 0.0 ... 5.0
    var size: CGFloat = 16
    var reviewCount: Int? = nil

    var body: some View {
        HStack(spacing: 6) {
            HStack(spacing: 2) {
                ForEach(0..<5) { i in
                    bubble(at: i)
                }
            }
            if let count = reviewCount {
                Text("\(String(format: "%.1f", value)) (\(count.formatted()))")
                    .font(.taMeta)
                    .foregroundStyle(.taTextSecondary)
            }
        }
    }

    @ViewBuilder
    private func bubble(at index: Int) -> some View {
        let fill = min(max(value - Double(index), 0), 1)  // 0, 0.5, or 1
        ZStack {
            Circle().fill(Color.taEmptyBubble)
            Circle()
                .fill(Color.taGreen)
                .mask(
                    Rectangle()
                        .frame(width: size * fill)
                        .frame(width: size, alignment: .leading)
                )
        }
        .frame(width: size, height: size)
    }
}
```

### Interactive Bubble Rating (Review Write)

```swift
struct BubbleRatingPicker: View {
    @Binding var rating: Int   // 1 ... 5
    var size: CGFloat = 32

    var body: some View {
        HStack(spacing: 8) {
            ForEach(1...5, id: \.self) { i in
                Circle()
                    .fill(i <= rating ? Color.taGreen : Color.taEmptyBubble)
                    .frame(width: size, height: size)
                    .scaleEffect(i == rating ? 1.0 : 1.0)
                    .onTapGesture { rating = i }
                    .sensoryFeedback(.impact(weight: .light), trigger: rating)
                    .animation(.spring(response: 0.2, dampingFraction: 0.6), value: rating)
            }
        }
    }
}
```

### Primary CTA Pill

```swift
struct TAPillButton: View {
    let title: String
    var style: Style = .filled
    let action: () -> Void

    enum Style { case filled, outline }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(style == .filled ? .taButton : .taButtonSec)
                .foregroundStyle(.black)
                .padding(.vertical, style == .filled ? 14 : 12)
                .padding(.horizontal, style == .filled ? 28 : 24)
                .frame(maxWidth: .infinity)
                .background(
                    Capsule().fill(style == .filled ? Color.taGreen : .clear)
                )
                .overlay(
                    Capsule().strokeBorder(style == .outline ? Color.black : .clear, lineWidth: 1)
                )
        }
        .buttonStyle(TAPressableStyle())
    }
}

struct TAPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### Place Card

```swift
struct PlaceCard: View {
    let name: String
    let photo: Image
    let rating: Double
    let reviews: Int
    let category: String
    let priceTier: String
    let distance: String
    let isAwarded: Bool
    @State private var saved = false

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            ZStack(alignment: .topTrailing) {
                photo
                    .resizable()
                    .aspectRatio(3/2, contentMode: .fill)
                    .frame(maxWidth: .infinity)
                    .clipShape(RoundedRectangle(cornerRadius: 16))

                Button {
                    saved.toggle()
                } label: {
                    Image(systemName: saved ? "heart.fill" : "heart")
                        .font(.system(size: 18, weight: .semibold))
                        .foregroundStyle(saved ? Color.taGreen : .white)
                        .padding(8)
                        .background(Circle().fill(.black.opacity(0.30)))
                }
                .sensoryFeedback(.success, trigger: saved)
                .padding(12)
            }

            if isAwarded {
                Text("TRAVELERS' CHOICE")
                    .font(.taBadge)
                    .foregroundStyle(.black)
                    .padding(.vertical, 4)
                    .padding(.horizontal, 10)
                    .background(Capsule().fill(Color.taGreen))
            }

            Text(name).font(.taPlaceName).foregroundStyle(.taTextPrimary)
            BubbleRating(value: rating, size: 16, reviewCount: reviews)
            Text("\(category) · \(priceTier) · \(distance)")
                .font(.taMeta)
                .foregroundStyle(.taTextSecondary)
        }
        .contentShape(Rectangle())
    }
}
```

### Place Detail Hero

```swift
struct PlaceHero: View {
    let name: String
    let photo: Image
    let rating: Double
    let reviews: Int

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            photo
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(height: 280)
                .clipped()

            LinearGradient(
                colors: [.clear, .black.opacity(0.55)],
                startPoint: .center, endPoint: .bottom
            )

            VStack(alignment: .leading, spacing: 8) {
                Text(name).font(.taPlaceHero).foregroundStyle(.white)
                HStack(spacing: 6) {
                    BubbleRating(value: rating, size: 18)
                    Text("\(reviews.formatted()) reviews")
                        .font(.taMeta).foregroundStyle(.white)
                }
            }
            .padding(20)
        }
        .frame(height: 280)
        .clipped()
    }
}
```

## 4. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground()
        appearance.backgroundColor = UIColor.white.withAlphaComponent(0.96)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            ExploreView().tabItem { Label("Explore", systemImage: "safari.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            TripsView().tabItem { Label("Trips", systemImage: "suitcase.fill") }
            ReviewView().tabItem { Label("Review", systemImage: "square.and.pencil") }
            MoreView().tabItem { Label("More", systemImage: "ellipsis") }
        }
        .tint(.taGreen) // active = Tripadvisor Green
    }
}
```

## 5. Motion

```swift
// Save-heart bounce
.sensoryFeedback(.success, trigger: saved)
.scaleEffect(saved ? 1.0 : 1.0)
.animation(.spring(response: 0.28, dampingFraction: 0.55), value: saved)

// Bubble selection in review form
.sensoryFeedback(.impact(weight: .light), trigger: rating)

// Card tap
.scaleEffect(pressed ? 0.98 : 1.0)
.animation(.spring(response: 0.25, dampingFraction: 0.8), value: pressed)

// Hero parallax: read scroll offset via GeometryReader, apply 0.5× translation + scale to the photo
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search | `magnifyingglass` | 18pt |
| Save heart | `heart` / `heart.fill` | 24pt |
| Share | `square.and.arrow.up` | 22pt |
| Back | `chevron.left` | 20pt |
| Filters | `line.3.horizontal.decrease` | 18pt |
| Location pin | `mappin.and.ellipse` | 18pt |
| Directions | `arrow.triangle.turn.up.right.diamond` | 20pt |
| Helpful vote | `hand.thumbsup` / `hand.thumbsup.fill` | 18pt |
| Explore (tab) | `safari.fill` | 24pt |
| Trips (tab) | `suitcase.fill` | 24pt |
| Review (tab) | `square.and.pencil` | 24pt |
| More (tab) | `ellipsis` | 24pt |
| Award laurel | `laurel.leading` / `laurel.trailing` | 14pt |

## 7. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on place names, review body, section headers — fix bubble rating size (it is a geometric unit), tab labels, badge text
- VoiceOver: announce the bubble rating as a single value, e.g. `.accessibilityLabel("Rated 4.5 out of 5, 1,284 reviews")`; make the review-form picker an `.accessibilityAdjustableAction`
- Save heart: `.accessibilityLabel(saved ? "Saved to trip" : "Save to trip")` with `.isButton` trait
- Contrast: `#6B6B6B` on `#FFFFFF` passes WCAG AA at 13pt+. Black-on-`#34E0A1` is intentional and high-contrast; do not switch to white text
- Floating photo-overlay controls: ensure 44pt hit area and the `rgba(0,0,0,0.35)` backing for contrast over bright traveler photos
