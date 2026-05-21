# Goodreads (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Goodreads' visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand & Interactive
    static let grBrown       = Color(red: 0.220, green: 0.129, blue: 0.063) // #382110
    static let grBrownDeep   = Color(red: 0.345, green: 0.216, blue: 0.122) // #58371F
    static let grTan         = Color(red: 0.957, green: 0.945, blue: 0.918) // #F4F1EA
    static let grShelfGreen  = Color(red: 0.251, green: 0.616, blue: 0.412) // #409D69
    static let grShelfGreenPressed = Color(red: 0.204, green: 0.514, blue: 0.333) // #348355
    static let grLinkTeal    = Color(red: 0.000, green: 0.388, blue: 0.365) // #00635D
    static let grLinkTealDark = Color(red: 0.435, green: 0.702, blue: 0.678) // #6FB3AD

    // MARK: - Rating
    static let grAmber       = Color(red: 0.914, green: 0.631, blue: 0.000) // #E9A100
    static let grStarRail    = Color(red: 0.847, green: 0.824, blue: 0.769) // #D8D2C4
    static let grStarRailDark = Color(red: 0.290, green: 0.247, blue: 0.173) // #4A3F2C

    // MARK: - Canvas & Surfaces (Light)
    static let grCanvas      = Color(red: 0.957, green: 0.945, blue: 0.918) // #F4F1EA
    static let grCardWhite   = Color.white                                  // #FFFFFF
    static let grSurfaceSubtle = Color(red: 0.922, green: 0.902, blue: 0.855) // #EBE6DA
    static let grDivider     = Color(red: 0.867, green: 0.839, blue: 0.780) // #DDD6C7

    // MARK: - Canvas & Surfaces (Dark)
    static let grDarkCanvas   = Color(red: 0.086, green: 0.075, blue: 0.063) // #161310
    static let grDarkSurface1 = Color(red: 0.129, green: 0.110, blue: 0.086) // #211C16
    static let grDarkSurface2 = Color(red: 0.173, green: 0.149, blue: 0.114) // #2C261D
    static let grDarkDivider  = Color(red: 0.227, green: 0.196, blue: 0.149) // #3A3226
    static let grBrownOnDark  = Color(red: 0.788, green: 0.533, blue: 0.239) // #C9883D

    // MARK: - Text
    static let grTextPrimary    = Color(red: 0.220, green: 0.129, blue: 0.063) // #382110
    static let grTextSecondary  = Color(red: 0.420, green: 0.369, blue: 0.278) // #6B5E47
    static let grTextTertiary   = Color(red: 0.596, green: 0.545, blue: 0.435) // #988B6F
    static let grDarkTextPrimary = Color(red: 0.929, green: 0.902, blue: 0.847) // #EDE6D8
    static let grDarkTextSecondary = Color(red: 0.702, green: 0.659, blue: 0.561) // #B3A88F

    // MARK: - Semantic
    static let grError = Color(red: 0.851, green: 0.325, blue: 0.310) // #D9534F
}
```

## 2. Typography

Goodreads uses **Merriweather** for editorial content (titles, synopses, reviews) and **Lato** for UI chrome (buttons, labels, tabs). Bundle both via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    // Editorial — Merriweather (serif)
    static let grScreenTitle = Font.custom("Merriweather-Black",   size: 30)
    static let grBookTitle   = Font.custom("Merriweather-Black",   size: 23)
    static let grSection     = Font.custom("Merriweather-Bold",    size: 19)
    static let grSubsection  = Font.custom("Merriweather-Bold",    size: 17)
    static let grBody        = Font.custom("Merriweather-Regular", size: 15)   // line spacing → 1.6
    static let grReview      = Font.custom("Merriweather-Regular", size: 13)

    // UI — Lato (sans)
    static let grButton      = Font.custom("Lato-Black",     size: 17)
    static let grLabel       = Font.custom("Lato-Bold",      size: 14)
    static let grMeta        = Font.custom("Lato-Regular",   size: 13)
    static let grLink        = Font.custom("Lato-Bold",      size: 13)
    static let grCaption     = Font.custom("Lato-Regular",   size: 12)
    static let grStarCaption = Font.custom("Lato-Bold",      size: 12)
    static let grOverline    = Font.custom("Lato-Bold",      size: 11)
    static let grTab         = Font.custom("Lato-Bold",      size: 10)
}

// Merriweather body needs explicit line spacing for the 1.6 reading rhythm:
// Text(synopsis).font(.grBody).lineSpacing(15 * 0.6)
```

## 3. Signature Components

### Book Cover

```swift
struct BookCover: View {
    let imageUrl: String?
    var width: CGFloat = 104
    @Environment(\.colorScheme) private var scheme

    private var height: CGFloat { width * 1.52 }

    var body: some View {
        ZStack(alignment: .leading) {
            AsyncImage(url: URL(string: imageUrl ?? "")) { img in
                img.resizable().aspectRatio(contentMode: .fill)
            } placeholder: {
                LinearGradient(colors: [.grBrownDeep, .grBrown],
                               startPoint: .topLeading, endPoint: .bottomTrailing)
            }
            .frame(width: width, height: height)
            .clipped()

            // spine hint
            Rectangle()
                .fill(Color.black.opacity(0.35))
                .frame(width: 6)
        }
        .frame(width: width, height: height)
        .clipShape(RoundedRectangle(cornerRadius: 3))
        .shadow(color: .black.opacity(scheme == .dark ? 0.55 : 0.18),
                radius: scheme == .dark ? 8 : 5, y: scheme == .dark ? 6 : 4)
    }
}
```

### Star Rating (display)

```swift
struct StarRating: View {
    let value: Double          // 0...5, supports fractional
    var size: CGFloat = 15
    @Environment(\.colorScheme) private var scheme

    private var rail: Color { scheme == .dark ? .grStarRailDark : .grStarRail }

    var body: some View {
        HStack(spacing: 1) {
            ForEach(0..<5, id: \.self) { i in
                let fill = min(max(value - Double(i), 0), 1)
                ZStack(alignment: .leading) {
                    star.foregroundStyle(rail)
                    star.foregroundStyle(Color.grAmber)
                        .mask(alignment: .leading) {
                            Rectangle().frame(width: size * fill)
                        }
                }
                .frame(width: size, height: size)
            }
        }
    }
    private var star: some View {
        Image(systemName: "star.fill").resizable().scaledToFit()
    }
}
```

### Interactive "Rate this Book"

```swift
struct RateThisBook: View {
    @Binding var rating: Int   // 0...5
    @Environment(\.colorScheme) private var scheme

    var body: some View {
        VStack(spacing: 10) {
            Text("RATE THIS BOOK")
                .font(.grOverline).kerning(0.6)
                .foregroundStyle(scheme == .dark ? Color.grDarkTextSecondary : .grTextSecondary)
            HStack(spacing: 6) {
                ForEach(1...5, id: \.self) { i in
                    Image(systemName: "star.fill")
                        .resizable().scaledToFit().frame(width: 28, height: 28)
                        .foregroundStyle(i <= rating ? Color.grAmber
                                         : (scheme == .dark ? Color.grStarRailDark : .grStarRail))
                        .scaleEffect(i == rating ? 1.0 : 1.0)
                        .onTapGesture {
                            withAnimation(.spring(response: 0.2, dampingFraction: 0.55)) {
                                rating = i
                            }
                            UIImpactFeedbackGenerator(style: .medium).impactOccurred()
                        }
                }
            }
        }
        .padding(14)
        .frame(maxWidth: .infinity)
        .background(RoundedRectangle(cornerRadius: 6)
            .fill(scheme == .dark ? Color.grDarkSurface1 : .grCardWhite))
    }
}
```

### Want-to-Read CTA Block

```swift
struct ShelfCTA: View {
    @Binding var shelf: String?      // nil, "Want to Read", "Currently Reading", "Read"

    var body: some View {
        VStack(spacing: 8) {
            Button {
                withAnimation(.spring(response: 0.18, dampingFraction: 0.6)) {
                    shelf = shelf == nil ? "Want to Read" : shelf
                }
                UINotificationFeedbackGenerator().notificationOccurred(.success)
            } label: {
                HStack(spacing: 8) {
                    if shelf != nil { Image(systemName: "checkmark").font(.system(size: 16, weight: .heavy)) }
                    Text(shelf ?? "Want to Read").font(.grButton)
                }
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 44)
                .background(RoundedRectangle(cornerRadius: 4).fill(Color.grShelfGreen))
            }
            .buttonStyle(.plain)

            HStack(spacing: 0) {
                ForEach(["★ Rate", "Review", "Shelve ▾"], id: \.self) { seg in
                    Text(seg)
                        .font(.grStarCaption)
                        .foregroundStyle(seg == "★ Rate" ? Color.grAmber : .grTextSecondary)
                        .frame(maxWidth: .infinity, minHeight: 38)
                    if seg != "Shelve ▾" { Divider() }
                }
            }
            .overlay(RoundedRectangle(cornerRadius: 4).strokeBorder(Color.grDivider, lineWidth: 1))
        }
        .padding(.horizontal, 16)
    }
}
```

### Shelf Status Pill

```swift
struct ShelfPill: View {
    enum Shelf { case read, current, want }
    let shelf: Shelf

    var body: some View {
        Text(label)
            .font(.grLink)
            .padding(.horizontal, 14).padding(.vertical, 7)
            .background(bg)
            .foregroundStyle(fg)
            .overlay(border)
            .clipShape(Capsule())
    }
    private var label: String { switch shelf { case .read: "Read"; case .current: "Currently Reading"; case .want: "Want to Read" } }
    private var bg: Color { switch shelf {
        case .read: .grBrown
        case .current: Color.grShelfGreen.opacity(0.18)
        case .want: .grSurfaceSubtle } }
    private var fg: Color { switch shelf {
        case .read: .grTan
        case .current: .grShelfGreen
        case .want: .grTextSecondary } }
    @ViewBuilder private var border: some View {
        switch shelf {
        case .current: Capsule().strokeBorder(Color.grShelfGreen.opacity(0.4), lineWidth: 1)
        case .want:    Capsule().strokeBorder(Color.grDivider, lineWidth: 1)
        case .read:    EmptyView()
        }
    }
}
```

### Community Review Card

```swift
struct ReviewCard: View {
    let initials: String
    let name: String
    let meta: String          // "1,204 reviews · 38 followers"
    let stars: Int
    let body_: String

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            HStack(spacing: 10) {
                Text(initials)
                    .font(.grLabel).foregroundStyle(Color.grTan)
                    .frame(width: 36, height: 36)
                    .background(LinearGradient(colors: [.grBrownDeep, .grBrown],
                                               startPoint: .topLeading, endPoint: .bottomTrailing))
                    .clipShape(Circle())
                VStack(alignment: .leading, spacing: 1) {
                    Text(name).font(.grLabel).foregroundStyle(Color.grTextPrimary)
                    Text(meta).font(.grCaption).foregroundStyle(Color.grTextTertiary)
                }
            }
            StarRating(value: Double(stars), size: 13)
            Text(body_).font(.grReview).foregroundStyle(Color.grTextPrimary).lineSpacing(13 * 0.55)
        }
        .padding(16)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(Color.grCardWhite)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct GoodreadsTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            MyBooksView().tabItem { Label("My Books", systemImage: "books.vertical.fill") }
            BrowseView().tabItem { Label("Browse", systemImage: "magnifyingglass") }
            UpdatesView().tabItem { Label("Updates", systemImage: "bell.fill") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.grBrown)   // light; use .grBrownOnDark in dark mode
    }
}
```

## 5. Motion

```swift
// Shelve action — button spring + success haptic
withAnimation(.spring(response: 0.18, dampingFraction: 0.6)) { shelf = "Want to Read" }
UINotificationFeedbackGenerator().notificationOccurred(.success)

// Star commit — staggered fill + bounce + medium haptic
withAnimation(.spring(response: 0.2, dampingFraction: 0.55)) { rating = n }
UIImpactFeedbackGenerator(style: .medium).impactOccurred()

// "…more" expander
withAnimation(.easeOut(duration: 0.22)) { expanded.toggle() }

// Reading Challenge ring sweep on appear
.onAppear { withAnimation(.easeOut(duration: 0.7)) { progress = current } }

// Cover → detail: matchedGeometryEffect on the cover for a shared-element push
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 23pt |
| My Books (tab) | `books.vertical` / `.fill` | 23pt |
| Browse (tab) | `magnifyingglass` | 23pt |
| Updates (tab) | `bell` / `bell.fill` | 23pt |
| Profile (tab) | `person.crop.circle` | 23pt |
| Star (rating) | `star.fill` | 13–28pt |
| Half star | `star.leadinghalf.filled` | 15pt |
| Back | `chevron.left` | 16pt |
| Share | `square.and.arrow.up` | 18pt |
| Add to network | `person.2` | 18pt |
| Shelf chevron | `chevron.down` | 12pt |
| Want to Read check | `checkmark` | 16pt |
| Search | `magnifyingglass` | 16pt |
| Progress update | `arrow.triangle.2.circlepath` | 16pt |
| Like (review) | `heart` / `heart.fill` | 14pt |
| Comment | `text.bubble` | 14pt |

## 7. Dark Mode

```swift
struct GoodreadsTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.grDarkCanvas : Color.grCanvas)
            .foregroundStyle(scheme == .dark ? Color.grDarkTextPrimary : Color.grTextPrimary)
            .tint(scheme == .dark ? Color.grBrownOnDark : Color.grBrown)
    }
}

extension View { func goodreadsTheme() -> some View { modifier(GoodreadsTheme()) } }
```

Dark mode inverts the tan paper to warm ink-brown `#161313` → `#161310`, never true black. Amber `#E9A100` is held identical (it is the load-bearing rating signal). Brown shifts to tan-gold `#C9883D` for active tabs; link teal lightens to `#6FB3AD`. Cover shadows deepen to 0.55 opacity so jackets still detach from the dark canvas. Book covers are never desaturated.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator` shown above)
- Bundle Merriweather (Regular/Bold/Black) and Lato (Regular/Bold/Black) TTFs via `Info.plist` — both SIL OFL, free to distribute
- Dynamic Type: scale book titles, section headers, body, review prose; pin tab labels, overlines, star captions, shelf pills at fixed sizes
- VoiceOver: announce ratings as "Rated 4 out of 5 stars, 3,284,991 ratings"; the interactive rate strip exposes an `.accessibilityValue` and an adjustable trait so users can swipe up/down to set rating
- Book covers: provide `.accessibilityLabel("\(title) by \(author)")`; never leave cover images unlabeled
- Color contrast: `#382110` on `#F4F1EA` passes WCAG AA; amber `#E9A100` is used as a graphical rating, paired with a text average so it never carries meaning by color alone
- Reduce Motion: disable the star scale-bounce and ring sweep; substitute an instant fill / crossfade — keep the fill state (it conveys the rating)
- Dark mode: invert via `grDark*`; keep amber constant; add no borders to covers
- Reading comfort: respect the user's preferred text size on review prose; never cap synopsis/review text below the system Dynamic Type setting
