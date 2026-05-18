# Yelp (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Yelp's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, a half-star `RatingView`, the review card, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (Interactive)
    static let yelpRed         = Color(red: 1.000, green: 0.102, blue: 0.102) // #FF1A1A
    static let yelpRedLogo     = Color(red: 0.827, green: 0.137, blue: 0.137) // #D32323
    static let yelpRedPressed  = Color(red: 0.722, green: 0.118, blue: 0.118) // #B81E1E

    // MARK: - Rating
    static let yelpStarFilled  = Color(red: 0.949, green: 0.361, blue: 0.020) // #F25C05
    static let yelpStarEmpty       = Color(red: 0.890, green: 0.890, blue: 0.878) // #E3E3E0
    static let yelpStarEmptyDark   = Color(red: 0.227, green: 0.227, blue: 0.227) // #3A3A3A

    // MARK: - Canvas & Surfaces (Light)
    static let yelpCanvas      = Color.white                                   // #FFFFFF
    static let yelpSurfaceGray = Color(red: 0.961, green: 0.961, blue: 0.961) // #F5F5F5
    static let yelpSectionBand = Color(red: 0.941, green: 0.941, blue: 0.941) // #F0F0F0
    static let yelpDivider     = Color(red: 0.878, green: 0.878, blue: 0.878) // #E0E0E0
    static let yelpPressedRow  = Color(red: 0.929, green: 0.929, blue: 0.929) // #EDEDED

    // MARK: - Canvas & Surfaces (Dark)
    static let yelpDarkCanvas      = Color(red: 0.086, green: 0.086, blue: 0.086) // #161616
    static let yelpDarkSurface1    = Color(red: 0.118, green: 0.118, blue: 0.118) // #1E1E1E
    static let yelpDarkSurface2    = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let yelpDarkDivider     = Color(red: 0.188, green: 0.188, blue: 0.188) // #303030
    static let yelpDarkSectionBand = Color(red: 0.047, green: 0.047, blue: 0.047) // #0C0C0C

    // MARK: - Text
    static let yelpTextPrimary    = Color(red: 0.169, green: 0.169, blue: 0.169) // #2B2B2B
    static let yelpTextSecondary  = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E
    static let yelpTextTertiary   = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A
    static let yelpDarkTextPrimary   = Color(red: 0.902, green: 0.902, blue: 0.902) // #E6E6E6
    static let yelpDarkTextSecondary = Color(red: 0.659, green: 0.659, blue: 0.659) // #A8A8A8

    // MARK: - Semantic
    static let yelpOpen    = Color(red: 0.176, green: 0.643, blue: 0.306) // #2DA44E
    static let yelpClosed  = Color(red: 0.878, green: 0.243, blue: 0.243) // #E03E3E
    static let yelpWarning = Color(red: 0.851, green: 0.451, blue: 0.051) // #D9730D
    static let yelpLink        = Color(red: 0.000, green: 0.451, blue: 0.733) // #0073BB
    static let yelpLinkDark    = Color(red: 0.310, green: 0.639, blue: 0.851) // #4FA3D9
    static let yelpElite       = Color(red: 0.635, green: 0.137, blue: 0.114) // #A2231D
}
```

## 2. Typography

Yelp uses **Open Sans** across body and review copy. Bundle the Open Sans TTFs via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let yelpScreenTitle  = Font.custom("OpenSans-ExtraBold", size: 32)
    static let yelpBizName      = Font.custom("OpenSans-ExtraBold", size: 24)
    static let yelpSection      = Font.custom("OpenSans-Bold",      size: 20)
    static let yelpCardHeader   = Font.custom("OpenSans-ExtraBold", size: 16)
    static let yelpEmphasis     = Font.custom("OpenSans-Bold",      size: 15)
    static let yelpBody         = Font.custom("OpenSans-Regular",   size: 14)
    static let yelpReviewerName = Font.custom("OpenSans-Bold",      size: 14)
    static let yelpMeta         = Font.custom("OpenSans-SemiBold",  size: 13)
    static let yelpCaption      = Font.custom("OpenSans-SemiBold",  size: 12)
    static let yelpButton       = Font.custom("OpenSans-Bold",      size: 15)
    static let yelpTab          = Font.custom("OpenSans-SemiBold",  size: 10)
    static let yelpChip         = Font.custom("OpenSans-SemiBold",  size: 13)
    static let yelpLink         = Font.custom("OpenSans-Bold",      size: 14)
}
```

## 3. Signature Components

### 5-Star Rating (half-star)

```swift
struct YelpRatingView: View {
    let rating: Double          // 0.0 ... 5.0, rounded to nearest 0.5 for display
    var size: CGFloat = 18
    var emptyColor: Color = .yelpStarEmpty

    var body: some View {
        HStack(spacing: 2) {
            ForEach(0..<5, id: \.self) { i in
                star(for: i)
                    .frame(width: size, height: size)
            }
        }
        .accessibilityElement()
        .accessibilityLabel("\(rating, specifier: "%.1f") out of 5 stars")
    }

    @ViewBuilder private func star(for index: Int) -> some View {
        let lower = Double(index)
        let fill = min(max(rating - lower, 0), 1)   // 0, 0.5, or 1
        ZStack {
            Image(systemName: "star.fill").resizable().foregroundStyle(emptyColor)
            Image(systemName: "star.fill").resizable().foregroundStyle(Color.yelpStarFilled)
                .mask(alignment: .leading) {
                    GeometryReader { g in
                        Rectangle().frame(width: g.size.width * fill)
                    }
                }
        }
    }
}

// Interactive input variant — tap or drag for half-stars
struct YelpRatingInput: View {
    @Binding var rating: Double
    var size: CGFloat = 32

    var body: some View {
        HStack(spacing: 6) {
            ForEach(1...5, id: \.self) { i in
                Image(systemName: Double(i) <= rating ? "star.fill"
                      : (Double(i) - 0.5 == rating ? "star.leadinghalf.filled" : "star"))
                    .resizable().frame(width: size, height: size)
                    .foregroundStyle(Double(i) - 0.5 <= rating ? Color.yelpStarFilled : .yelpStarEmpty)
                    .onTapGesture { rating = Double(i) }
            }
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: rating)
    }
}
```

### Business Header

```swift
struct YelpBusinessHeader: View {
    let photoURL: String
    let name: String
    let rating: Double
    let reviewCount: Int
    let priceCategory: String   // "$$ · Bakeries · Cafes"
    let isOpen: Bool
    let hours: String           // "Closes 8:00 PM"

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .bottomTrailing) {
                AsyncImage(url: URL(string: photoURL)) { $0.resizable().aspectRatio(contentMode: .fill) }
                    placeholder: { Color.yelpSurfaceGray }
                    .frame(height: 184).clipped()
                Text("1 / 248 photos")
                    .font(.yelpCaption).foregroundStyle(.white)
                    .padding(.horizontal, 10).padding(.vertical, 4)
                    .background(Color.black.opacity(0.6), in: RoundedRectangle(cornerRadius: 6))
                    .padding(12)
            }

            VStack(alignment: .leading, spacing: 6) {
                Text(name).font(.yelpBizName).foregroundStyle(Color.yelpTextPrimary)
                HStack(spacing: 8) {
                    YelpRatingView(rating: rating)
                    Text(String(format: "%.1f", rating)).font(.yelpEmphasis).foregroundStyle(Color.yelpTextPrimary)
                    Text("\(reviewCount) reviews").font(.yelpMeta).foregroundStyle(Color.yelpLink)
                }
                Text(priceCategory).font(.yelpMeta).foregroundStyle(Color.yelpTextSecondary)
                HStack(spacing: 4) {
                    Text(isOpen ? "Open" : "Closed")
                        .font(.yelpMeta).fontWeight(.bold)
                        .foregroundStyle(isOpen ? Color.yelpOpen : Color.yelpClosed)
                    Text("· \(hours)").font(.yelpMeta).foregroundStyle(Color.yelpTextSecondary)
                }
            }
            .padding(16)

            Rectangle().fill(Color.yelpSectionBand).frame(height: 8) // section band
        }
    }
}
```

### Review Card

```swift
struct YelpReviewCard: View {
    let initials: String
    let name: String
    let location: String
    let reviewerCount: Int
    let rating: Double
    let date: String
    let body: String
    @State private var useful = false

    var view: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 10) {
                Text(initials).font(.yelpEmphasis).foregroundStyle(.white)
                    .frame(width: 40, height: 40)
                    .background(LinearGradient(colors: [.yelpRedLogo, Color(red: 0.541, green: 0.094, blue: 0.094)],
                                               startPoint: .topLeading, endPoint: .bottomTrailing), in: Circle())
                VStack(alignment: .leading, spacing: 1) {
                    Text(name).font(.yelpReviewerName).foregroundStyle(Color.yelpTextPrimary)
                    Text("\(location) · \(reviewerCount) reviews").font(.yelpCaption).foregroundStyle(Color.yelpTextSecondary)
                }
            }
            HStack {
                YelpRatingView(rating: rating, size: 15)
                Spacer()
                Text(date).font(.yelpCaption).foregroundStyle(Color.yelpTextSecondary)
            }
            .padding(.top, 10)

            Text(body).font(.yelpBody).foregroundStyle(Color.yelpTextPrimary)
                .lineSpacing(4).padding(.top, 8)

            HStack(spacing: 18) {
                voteButton("hand.thumbsup", "Useful 24", $useful)
                voteButton("face.smiling", "Funny 6", .constant(false))
                voteButton("heart", "Cool 11", .constant(false))
            }
            .padding(.top, 10)
        }
        .padding(.vertical, 16)
        .overlay(alignment: .bottom) { Divider().background(Color.yelpDivider) }
    }

    private func voteButton(_ icon: String, _ label: String, _ on: Binding<Bool>) -> some View {
        Button { on.wrappedValue.toggle() } label: {
            HStack(spacing: 5) {
                Image(systemName: icon).font(.system(size: 13))
                Text(label).font(.yelpCaption)
            }
            .foregroundStyle(on.wrappedValue ? Color.yelpRed : Color.yelpTextSecondary)
        }
    }
}
```

### Primary Button & Category Chip

```swift
struct YelpPrimaryButton: View {
    let title: String
    var systemImage: String? = nil
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            HStack(spacing: 6) {
                if let s = systemImage { Image(systemName: s).font(.system(size: 16, weight: .bold)) }
                Text(title).font(.yelpButton)
            }
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity, minHeight: 44)
            .background(Color.yelpRed, in: RoundedRectangle(cornerRadius: 8))
        }
        .buttonStyle(.plain)
    }
}

struct YelpCategoryChip: View {
    let title: String
    let systemImage: String
    let selected: Bool
    var body: some View {
        HStack(spacing: 7) {
            Image(systemName: systemImage).font(.system(size: 13, weight: .semibold))
            Text(title).font(.yelpChip)
        }
        .foregroundStyle(selected ? .white : Color.yelpTextPrimary)
        .padding(.horizontal, 16).padding(.vertical, 9)
        .background(selected ? Color.yelpRed : Color.yelpSurfaceGray, in: Capsule())
        .overlay(Capsule().stroke(selected ? Color.clear : Color.yelpDivider, lineWidth: 1))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct YelpTabView: View {
    var body: some View {
        TabView {
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            NearbyView().tabItem { Label("Nearby", systemImage: "house.fill") }
            WriteView().tabItem { Label("Write", systemImage: "square.and.pencil") }
            ActivityView().tabItem { Label("Activity", systemImage: "waveform.path.ecg") }
            MeView().tabItem { Label("Me", systemImage: "person.crop.circle") }
        }
        .tint(.yelpRed)   // active icon = Yelp Red, filled; no pill
    }
}
```

## 5. Motion

```swift
// Photo header parallax + compact title cross-fade
ScrollView { /* content */ }
    .onScrollGeometryChange(for: CGFloat.self) { $0.contentOffset.y } action: { _, y in
        compactTitleOpacity = min(max((y - 120) / 60, 0), 1)
    }

// Rating increment — soft haptic per step
.sensoryFeedback(.impact(flexibility: .soft), trigger: rating)

// Vote tap — count bump spring
withAnimation(.spring(response: 0.15, dampingFraction: 0.6)) { useful.toggle() }

// Filter sheet
.sheet(isPresented: $showFilters) { FilterSheet().presentationDetents([.large]) }
// slides up 300ms ease-out (system); scrim fades to 0.4

// Check-in fill
withAnimation(.easeOut(duration: 0.2)) { checkedIn = true }
.sensoryFeedback(.impact(flexibility: .solid), trigger: checkedIn)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search (tab) | `magnifyingglass` | 22pt |
| Nearby (tab) | `house` / `house.fill` | 22pt |
| Write (tab) | `square.and.pencil` | 22pt |
| Activity (tab) | `waveform.path.ecg` / `bell` | 22pt |
| Me (tab) | `person.crop.circle` | 22pt |
| Star (rating) | `star.fill` / `star` / `star.leadinghalf.filled` | 14–22pt |
| Write a Review | `square.and.pencil` | 16pt |
| Save | `bookmark` / `bookmark.fill` | 16pt |
| Share | `square.and.arrow.up` | 16pt |
| Directions | `arrow.triangle.turn.up.right.diamond` | 16pt |
| Call | `phone.fill` | 16pt |
| Useful | `hand.thumbsup` / `hand.thumbsup.fill` | 13pt |
| Funny | `face.smiling` | 13pt |
| Cool | `heart` / `heart.fill` | 13pt |
| Back | `chevron.left` | 17pt |
| Filter | `slider.horizontal.3` | 18pt |
| Map toggle | `map` | 18pt |
| Add photo | `camera` | 16pt |
| Location pill | `chevron.down` | 12pt |

## 7. Dark Mode

```swift
struct YelpTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.yelpDarkCanvas : Color.yelpCanvas)
            .foregroundStyle(scheme == .dark ? Color.yelpDarkTextPrimary : Color.yelpTextPrimary)
            .tint(.yelpRed)
    }
}
extension View { func yelpTheme() -> some View { modifier(YelpTheme()) } }

// Star fill (#F25C05) is IDENTICAL in both modes — only the empty color swaps:
// .yelpStarEmpty (light) ↔ .yelpStarEmptyDark (dark)
// Section bands: .yelpSectionBand (light) ↔ .yelpDarkSectionBand (#0C0C0C, dark)
// Links: .yelpLink (light) ↔ .yelpLinkDark (dark)
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.onScrollGeometryChange` need iOS 17 / 18 — gate behind `#available`, fall back to `UIImpactFeedbackGenerator` and `GeometryReader`)
- Bundle Open Sans TTFs (Regular, SemiBold, Bold, ExtraBold) via `Info.plist` `UIAppFonts` — Open Sans is SIL OFL via Google Fonts, free to redistribute
- Dynamic Type: scale screen title, business name, section headers, body, meta, review text; keep tab labels, chip text, photo-count chip, vote labels FIXED; star glyphs scale with their adjacent rating number
- VoiceOver: rating views announce "{x.x} out of 5 stars"; review cards announce "Review by {name}, {x} stars, {date}"; vote buttons announce state ("Useful, 24, button" / "Marked useful")
- The 5-star input must support the Adjustable trait — increment/decrement by 0.5 with the rotor
- Color contrast: `#2B2B2B` on `#FFFFFF` passes WCAG AA; the warm star `#F25C05` on white passes for graphical objects (3:1); "Open" green `#2DA44E` paired with the word "Open" (never color alone)
- Never convey open/closed by color alone — always include the text "Open"/"Closed"
- Reduce Motion: disable header parallax (static image) and the vote-count spring (instant); keep rating-fill (it conveys value)
- Dark mode: canvas `#161616` (NOT pure black), cards `#1E1E1E`; the star fill stays `#F25C05`; section bands become `#0C0C0C`; shadows nearly vanish so dividers/bands carry structure
