# Vrbo (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Vrbo's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the signature photo gallery, the sticky booking bar, listing cards, map price pins, and the bottom tab bar.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let vbCanvas        = Color.white                                   // #FFFFFF
    static let vbSurfaceGray   = Color(red: 0.953, green: 0.961, blue: 0.973) // #F3F5F8
    static let vbSurfacePressed = Color(red: 0.906, green: 0.922, blue: 0.945) // #E7EBF1
    static let vbDivider       = Color(red: 0.882, green: 0.898, blue: 0.922) // #E1E5EB

    // MARK: - Canvas & Surfaces (Dark)
    static let vbDarkCanvas    = Color(red: 0.063, green: 0.075, blue: 0.090) // #101317
    static let vbDarkSurface1  = Color(red: 0.094, green: 0.110, blue: 0.133) // #181C22
    static let vbDarkSurface2  = Color(red: 0.129, green: 0.149, blue: 0.180) // #21262E
    static let vbDarkDivider   = Color(red: 0.165, green: 0.188, blue: 0.220) // #2A3038

    // MARK: - Text
    static let vbTextPrimary   = Color(red: 0.082, green: 0.094, blue: 0.114) // #15181D
    static let vbTextSecondary = Color(red: 0.353, green: 0.392, blue: 0.439) // #5A6470
    static let vbTextTertiary  = Color(red: 0.541, green: 0.576, blue: 0.627) // #8A93A0
    static let vbDarkTextPrimary   = Color(red: 0.929, green: 0.937, blue: 0.949) // #EDEFF2
    static let vbDarkTextSecondary = Color(red: 0.639, green: 0.667, blue: 0.706) // #A3AAB4

    // MARK: - Brand
    static let vbBlue        = Color(red: 0.141, green: 0.353, blue: 0.737) // #245ABC
    static let vbBluePressed = Color(red: 0.106, green: 0.278, blue: 0.580) // #1B4794
    static let vbBlueBright  = Color(red: 0.310, green: 0.545, blue: 0.941) // #4F8BF0
    static let vbSkyBlue     = Color(red: 0.114, green: 0.435, blue: 0.722) // #1D6FB8

    // MARK: - Accent & Semantic
    static let vbGoldStar = Color(red: 0.949, green: 0.690, blue: 0.118) // #F2B01E — the only warm color
    static let vbSuccess  = Color(red: 0.122, green: 0.616, blue: 0.341) // #1F9D57
    static let vbError    = Color(red: 0.878, green: 0.267, blue: 0.267) // #E04444
    static let vbWarning  = Color(red: 0.910, green: 0.573, blue: 0.047) // #E8920C
}
```

## 2. Typography

Vrbo ships **no custom typeface** — it uses SF Pro with Dynamic Type. Price is one of the heaviest styles on every screen.

```swift
extension Font {
    static let vbLargeTitle  = Font.system(size: 32, weight: .heavy,    design: .default)
    static let vbPriceHero   = Font.system(size: 26, weight: .heavy,    design: .default)
    static let vbListingTitle = Font.system(size: 21, weight: .bold,    design: .default)
    static let vbSection     = Font.system(size: 18, weight: .bold,     design: .default)
    static let vbBody        = Font.system(size: 16, weight: .regular,  design: .default)
    static let vbCardTitle   = Font.system(size: 15, weight: .semibold, design: .default)
    static let vbFactValue   = Font.system(size: 16, weight: .bold,     design: .default)
    static let vbMeta        = Font.system(size: 14, weight: .regular,  design: .default)
    static let vbBadge       = Font.system(size: 12, weight: .bold,     design: .default)
    static let vbButton      = Font.system(size: 16, weight: .bold,     design: .default)
    static let vbTab         = Font.system(size: 10, weight: .medium,   design: .default)
    static let vbCaption     = Font.system(size: 12, weight: .medium,   design: .default)
}
```

## 3. Signature Components

### Photo Gallery (the core component)

```swift
struct PhotoGallery: View {
    let photos: [String]            // image URLs
    @State private var index = 0
    let onBack: () -> Void
    let onShare: () -> Void
    @Binding var saved: Bool

    var body: some View {
        ZStack(alignment: .bottom) {
            TabView(selection: $index) {
                ForEach(photos.indices, id: \.self) { i in
                    AsyncImage(url: URL(string: photos[i])) { img in
                        img.resizable().aspectRatio(contentMode: .fill)
                    } placeholder: { Color.vbDarkSurface2 }
                    .tag(i)
                    .clipped()
                }
            }
            .tabViewStyle(.page(indexDisplayMode: .never))
            .frame(height: 290)

            // bottom scrim for legible overlays
            LinearGradient(colors: [.clear, Color.vbDarkCanvas.opacity(0.55)],
                           startPoint: .center, endPoint: .bottom)
                .frame(height: 145).allowsHitTesting(false)

            HStack(spacing: 5) {
                ForEach(photos.indices, id: \.self) { i in
                    Capsule()
                        .fill(i == index ? Color.white : Color.white.opacity(0.45))
                        .frame(width: i == index ? 18 : 6, height: 6)
                        .animation(.easeOut(duration: 0.2), value: index)
                }
            }
            .padding(.bottom, 14)

            HStack {
                Spacer()
                HStack(spacing: 6) {
                    Image(systemName: "photo.on.rectangle").font(.system(size: 12))
                    Text("\(index + 1) / \(photos.count)").font(.vbCaption)
                }
                .foregroundStyle(.white)
                .padding(.horizontal, 11).padding(.vertical, 5)
                .background(Capsule().fill(Color.vbDarkCanvas.opacity(0.7)))
                .padding(.trailing, 16).padding(.bottom, 14)
            }
        }
        .overlay(alignment: .top) {
            HStack {
                GalleryButton(system: "chevron.left", action: onBack)
                Spacer()
                HStack(spacing: 10) {
                    GalleryButton(system: "square.and.arrow.up", action: onShare)
                    GalleryButton(system: saved ? "heart.fill" : "heart",
                                  tint: saved ? .vbError : .white) { saved.toggle() }
                }
            }
            .padding(.horizontal, 16).padding(.top, 54)
        }
    }
}

struct GalleryButton: View {
    let system: String
    var tint: Color = .white
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Image(systemName: system)
                .font(.system(size: 18, weight: .semibold))
                .foregroundStyle(tint)
                .frame(width: 36, height: 36)
                .background(.ultraThinMaterial, in: Circle())
        }
    }
}
```

### Sticky Booking Bar

```swift
struct BookingBar: View {
    let price: Int
    let dateRange: String          // "Jun 14 – 21 · 7 nights"
    let onDates: () -> Void
    let onBook: () -> Void

    var body: some View {
        HStack {
            VStack(alignment: .leading, spacing: 2) {
                HStack(alignment: .firstTextBaseline, spacing: 4) {
                    Text("$\(price)").font(.vbPriceHero)
                        .foregroundStyle(Color.vbDarkTextPrimary)
                        .contentTransition(.numericText())
                    Text("/ night").font(.system(size: 13))
                        .foregroundStyle(Color.vbDarkTextSecondary)
                }
                Button(action: onDates) {
                    Text(dateRange)
                        .font(.system(size: 12))
                        .underline()
                        .foregroundStyle(Color.vbDarkTextSecondary)
                }
            }
            Spacer()
            Button(action: onBook) {
                Text("Book now")
                    .font(.vbButton)
                    .foregroundStyle(.white)
                    .padding(.horizontal, 26).padding(.vertical, 13)
                    .background(RoundedRectangle(cornerRadius: 10).fill(Color.vbBlue))
            }
            .sensoryFeedback(.impact(flexibility: .soft), trigger: price)
        }
        .padding(.horizontal, 18)
        .frame(height: 76)
        .background(.ultraThinMaterial)
        .overlay(Rectangle().fill(Color.vbDarkDivider).frame(height: 1), alignment: .top)
    }
}
```

### Listing Card (results)

```swift
struct ListingCard: View {
    let imageURL: String
    let title: String
    let location: String
    let rating: Double
    let reviews: Int
    let price: Int
    @Binding var saved: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            ZStack(alignment: .topTrailing) {
                AsyncImage(url: URL(string: imageURL)) { img in
                    img.resizable().aspectRatio(4/3, contentMode: .fill)
                } placeholder: { Color.vbDarkSurface2.aspectRatio(4/3, contentMode: .fill) }
                .clipShape(RoundedRectangle(cornerRadius: 14))

                Button { saved.toggle() } label: {
                    Image(systemName: saved ? "heart.fill" : "heart")
                        .font(.system(size: 17, weight: .semibold))
                        .foregroundStyle(saved ? Color.vbError : .white)
                        .frame(width: 36, height: 36)
                        .background(.ultraThinMaterial, in: Circle())
                        .padding(10)
                }
            }
            Text("Entire home").font(.vbCaption).foregroundStyle(Color.vbDarkTextSecondary)
            Text(title).font(.vbCardTitle).foregroundStyle(Color.vbDarkTextPrimary)
            Text(location).font(.vbMeta).foregroundStyle(Color.vbDarkTextSecondary)
            HStack(spacing: 6) {
                Image(systemName: "star.fill").font(.system(size: 12)).foregroundStyle(Color.vbGoldStar)
                Text("\(rating, specifier: "%.1f")").font(.system(size: 14, weight: .bold))
                    .foregroundStyle(Color.vbDarkTextPrimary)
                Text("· \(reviews)").font(.vbMeta).foregroundStyle(Color.vbDarkTextSecondary)
            }
            (Text("$\(price)").font(.system(size: 16, weight: .bold)) + Text(" night").font(.system(size: 14)))
                .foregroundStyle(Color.vbDarkTextPrimary)
        }
    }
}
```

### Map Price Pin

```swift
struct MapPricePin: View {
    enum State { case `default`, viewed, selected }
    let price: Int
    let state: State

    var body: some View {
        Text("$\(price)")
            .font(.system(size: 14, weight: .bold))
            .foregroundStyle(fg)
            .padding(.horizontal, 12).padding(.vertical, 7)
            .background(Capsule().fill(bg))
            .overlay(state == .viewed ? Capsule().strokeBorder(Color.vbDarkDivider, lineWidth: 1) : nil)
            .scaleEffect(state == .selected ? 1.12 : 1)
            .shadow(color: .black.opacity(state == .selected ? 0.35 : 0.2), radius: 8, y: 3)
            .animation(.easeOut(duration: 0.18), value: state)
    }
    private var bg: Color {
        switch state { case .default: return .vbBlue
            case .viewed: return .vbDarkSurface2; case .selected: return .vbGoldStar }
    }
    private var fg: Color {
        switch state { case .default: return .white
            case .viewed: return .vbDarkTextSecondary
            case .selected: return Color(red: 0.102, green: 0.071, blue: 0.024) } // #1A1206
    }
}
```

### Trip Board Card + Trust Badge

```swift
struct TripBoardCard: View {
    let thumb: String
    let name: String
    let facts: String              // "Outer Banks, NC · 4 BR · Sleeps 8"
    let rating: Double
    let tripName: String

    var body: some View {
        HStack(spacing: 12) {
            AsyncImage(url: URL(string: thumb)) { $0.resizable().scaledToFill() }
                placeholder: { Color.vbDarkSurface2 }
                .frame(width: 64, height: 64)
                .clipShape(RoundedRectangle(cornerRadius: 8))
            VStack(alignment: .leading, spacing: 3) {
                Text(name).font(.system(size: 14, weight: .bold)).foregroundStyle(Color.vbDarkTextPrimary)
                Text(facts).font(.vbCaption).foregroundStyle(Color.vbDarkTextSecondary)
                Text("★ \(rating, specifier: "%.1f") · Saved to \"\(tripName)\"")
                    .font(.system(size: 11, weight: .bold)).foregroundStyle(Color.vbGoldStar)
                    .padding(.top, 5)
            }
            Spacer()
        }
        .padding(12)
        .background(RoundedRectangle(cornerRadius: 12).fill(Color.vbDarkSurface2))
        .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.vbDarkDivider, lineWidth: 1))
    }
}

struct TrustBadge: View {
    enum Kind { case premier, instant }
    let kind: Kind
    var body: some View {
        Text(kind == .premier ? "Premier Host" : "Instant Book")
            .font(.vbBadge)
            .foregroundStyle(kind == .premier ? Color.vbBlueBright : Color.vbSuccess)
            .padding(.horizontal, 9).padding(.vertical, 4)
            .background(RoundedRectangle(cornerRadius: 6)
                .fill((kind == .premier ? Color.vbBlue : Color.vbSuccess).opacity(0.16)))
            .overlay(RoundedRectangle(cornerRadius: 6)
                .strokeBorder((kind == .premier ? Color.vbBlueBright : Color.vbSuccess).opacity(0.35), lineWidth: 1))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct VrboTabView: View {
    var body: some View {
        TabView {
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            TripsView().tabItem { Label("Trips", systemImage: "suitcase") }
            InboxView().tabItem { Label("Inbox", systemImage: "bubble.left") }.badge("")
            NotificationsView().tabItem { Label("Notifications", systemImage: "bell") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle") }
        }
        .tint(.vbBlueBright)   // active tab = brand blue (bright on dark)
    }
}
```

## 5. Motion

```swift
// Gallery progress dot — width animates on page change
.animation(.easeOut(duration: 0.2), value: index)

// Booking-bar price update — numeric content transition
Text("$\(price)").contentTransition(.numericText())
withAnimation(.easeInOut(duration: 0.25)) { price = newPrice }

// Save heart pop
withAnimation(.spring(response: 0.28, dampingFraction: 0.5)) { saved.toggle() }
// pair with: .sensoryFeedback(.impact(flexibility: .soft), trigger: saved)

// Map pin select — scale + recolor + peek card
.animation(.easeOut(duration: 0.18), value: state)
.sheet(isPresented: $showPeek) { PinPeekCard() }  // 250ms slide-up

// Results card stagger
.transition(.opacity.combined(with: .move(edge: .bottom)))

// Haptics
.sensoryFeedback(.impact(flexibility: .soft), trigger: bookTapped)  // Book now
.sensoryFeedback(.selection, trigger: selectedDateEndpoint)         // date range
.sensoryFeedback(.selection, trigger: selectedTab)                  // tab change
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search (tab) | `magnifyingglass` | 22pt |
| Trips (tab) | `suitcase` / `suitcase.fill` | 22pt |
| Inbox (tab) | `bubble.left` / `bubble.left.fill` | 22pt |
| Notifications (tab) | `bell` / `bell.fill` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Back (gallery) | `chevron.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Save | `heart` / `heart.fill` | 17–18pt |
| Photo grid / counter | `photo.on.rectangle` | 12pt |
| Rating star | `star.fill` | 12–15pt |
| Guests | `person.2` | 16pt |
| Bedrooms | `bed.double` | 16pt |
| Bathrooms | `shower` | 16pt |
| Whole home | `house` / `house.fill` | 16pt |
| Filters | `slider.horizontal.3` | 16pt |
| Map toggle | `map` | 16pt |
| Calendar / dates | `calendar` | 16pt |
| Location | `mappin.and.ellipse` | 14pt |
| Instant Book | `bolt.fill` | 12pt |

## 7. Dark Mode

```swift
struct VrboTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.vbDarkCanvas : Color.vbCanvas)
            .foregroundStyle(scheme == .dark ? Color.vbDarkTextPrimary : Color.vbTextPrimary)
            .tint(scheme == .dark ? Color.vbBlueBright : Color.vbBlue)
    }
}

extension View {
    func vrboTheme() -> some View { modifier(VrboTheme()) }
}

// Links / pins / outline buttons use the bright blue on dark.
// The primary "Book now" fill stays the deep brand blue (#245ABC) on both.
extension Color {
    static func vbAccent(_ scheme: ColorScheme) -> Color {
        scheme == .dark ? .vbBlueBright : .vbBlue
    }
}
// The gold review star NEVER shifts between schemes — it is constant.
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` and `.contentTransition(.numericText())` require iOS 17; fall back to `UIImpactFeedbackGenerator` and a plain crossfade for the price)
- No custom fonts to bundle — Vrbo uses SF Pro (system); enable Dynamic Type everywhere
- Dynamic Type: scale large titles, price hero, listing title, section headers, body; keep tab labels, trust badges, photo counter, and progress-dot legends at FIXED sizes (layout-sensitive)
- VoiceOver: announce the gallery as "Photo {n} of {total}, swipe to browse"; label the booking bar "Price {amount} per night, {dates}, double-tap Book now"; cards as "{title}, entire home, {rating} stars, {reviews} reviews, {price} per night"
- Color contrast: `#15181D` on `#FFFFFF` and `#EDEFF2` on `#101317` pass WCAG AA; the gold star is paired with a numeric rating so color is never the sole channel; trust badges include text labels, not color alone
- Reduce Motion: disable the save-heart spring and pin scale; cross-dissolve the price instead of counting; keep gallery paging but remove the dot width animation
- Dark mode: invert via the `vbDark*` palette — `#101317`, NOT pure black; links/pins/outline brighten to `#4F8BF0`, the "Book now" fill stays `#245ABC`, the gold star is constant; shadows nearly vanish so floating panels add a 1pt `vbDarkDivider` border
- Photography: gallery images are full-bleed and never carry a shadow; preserve aspect ratio and provide descriptive alt text per photo for VoiceOver
