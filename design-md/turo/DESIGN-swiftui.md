# Turo (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Turo's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and the signature photo hero, host card, and sticky Book bar.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let turoPurple        = Color(red: 0.349, green: 0.231, blue: 0.984) // #593BFB  booking action
    static let turoPurpleBright  = Color(red: 0.486, green: 0.361, blue: 1.0)   // #7C5CFF  link / gradient hi
    static let turoPurplePressed = Color(red: 0.290, green: 0.184, blue: 0.839) // #4A2FD6
    static let turoTeal          = Color(red: 0.361, green: 0.878, blue: 0.722) // #5CE0B8  trust / value
    static let turoTealDeep      = Color(red: 0.122, green: 0.722, blue: 0.565) // #1FB890

    // MARK: - Canvas & Surfaces (Light)
    static let turoCanvas    = Color.white                                   // #FFFFFF
    static let turoSurface1  = Color(red: 0.965, green: 0.961, blue: 0.980) // #F6F5FA
    static let turoSurface2  = Color.white                                   // #FFFFFF (card)
    static let turoPressed   = Color(red: 0.925, green: 0.918, blue: 0.957) // #ECEAF4
    static let turoDivider   = Color(red: 0.902, green: 0.894, blue: 0.937) // #E6E4EF

    // MARK: - Canvas & Surfaces (Dark)
    static let turoDarkCanvas   = Color(red: 0.059, green: 0.059, blue: 0.071) // #0F0F12
    static let turoDarkSurface1 = Color(red: 0.102, green: 0.102, blue: 0.122) // #1A1A1F
    static let turoDarkSurface2 = Color(red: 0.145, green: 0.145, blue: 0.173) // #25252C
    static let turoDarkDivider  = Color(red: 0.176, green: 0.176, blue: 0.208) // #2D2D35

    // MARK: - Text
    static let turoTextPrimary    = Color(red: 0.082, green: 0.075, blue: 0.122) // #15131F
    static let turoTextSecondary  = Color(red: 0.420, green: 0.408, blue: 0.471) // #6B6878
    static let turoTextTertiary   = Color(red: 0.612, green: 0.600, blue: 0.671) // #9C99AB
    static let turoDarkTextPrimary   = Color(red: 0.949, green: 0.945, blue: 0.965) // #F2F1F6
    static let turoDarkTextSecondary = Color(red: 0.643, green: 0.635, blue: 0.702) // #A4A2B3
    static let turoDarkTextTertiary  = Color(red: 0.435, green: 0.427, blue: 0.494) // #6F6D7E
    static let turoOnPurple = Color.white                                          // #FFFFFF
    static let turoOnTeal   = Color(red: 0.024, green: 0.137, blue: 0.106)         // #06231B

    // MARK: - Semantic
    static let turoSuccess     = Color(red: 0.122, green: 0.722, blue: 0.565) // #1FB890 light
    static let turoSuccessDark = Color(red: 0.361, green: 0.878, blue: 0.722) // #5CE0B8 dark
    static let turoError       = Color(red: 0.961, green: 0.325, blue: 0.420) // #F5536B dark
    static let turoStar        = Color(red: 1.0,   green: 0.706, blue: 0.0)   // #FFB400
}
```

## 2. Typography

Turo's brand face is a geometric humanist sans. Bundle it if licensed; otherwise ship **Manrope** (SIL OFL). Prices and counts are tabular everywhere they appear.

```swift
extension Font {
    // Brand face name — swap "Manrope" for the licensed brand face if available
    private static let brand = "Manrope"

    static let turoDisplay     = Font.custom("\(brand)-ExtraBold", size: 32).weight(.heavy)
    static let turoPriceHero   = Font.custom("\(brand)-ExtraBold", size: 26).weight(.heavy)
    static let turoCarTitle    = Font.custom("\(brand)-ExtraBold", size: 23).weight(.heavy)
    static let turoSection     = Font.custom("\(brand)-Bold",      size: 18).weight(.bold)
    static let turoBody        = Font.custom("\(brand)-Regular",   size: 16).weight(.regular)
    static let turoBodyEmph    = Font.custom("\(brand)-SemiBold",  size: 16).weight(.semibold)
    static let turoCardTitle   = Font.custom("\(brand)-SemiBold",  size: 15).weight(.semibold)
    static let turoCardPrice   = Font.custom("\(brand)-ExtraBold", size: 14).weight(.heavy)
    static let turoMeta        = Font.custom("\(brand)-Regular",   size: 14).weight(.regular)
    static let turoEyebrow     = Font.custom("\(brand)-Bold",      size: 13).weight(.bold)
    static let turoCaption     = Font.custom("\(brand)-Medium",    size: 12).weight(.medium)
    static let turoButton      = Font.custom("\(brand)-ExtraBold", size: 16).weight(.heavy)
    static let turoTab         = Font.custom("\(brand)-SemiBold",  size: 10).weight(.semibold)
    static let turoRatingNum   = Font.custom("\(brand)-Bold",      size: 13).weight(.bold)
}

extension View {
    func turoTabularNumbers() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Photo Hero (full-bleed carousel)

```swift
struct CarPhotoHero: View {
    let photos: [URL]
    @State private var page = 0
    @Binding var isFavorite: Bool
    let onBack: () -> Void
    let onShare: () -> Void

    var body: some View {
        ZStack(alignment: .bottom) {
            TabView(selection: $page) {
                ForEach(Array(photos.enumerated()), id: \.offset) { i, url in
                    AsyncImage(url: url) { $0.resizable().aspectRatio(contentMode: .fill) }
                        placeholder: { Rectangle().fill(Color.turoDarkSurface2) }
                        .tag(i)
                }
            }
            .tabViewStyle(.page(indexDisplayMode: .never))
            .frame(height: 260)
            .clipped()                              // full-bleed, NO card frame

            // Page dots — active is an 18pt pill
            HStack(spacing: 5) {
                ForEach(photos.indices, id: \.self) { i in
                    Capsule()
                        .fill(i == page ? Color.white : Color.white.opacity(0.45))
                        .frame(width: i == page ? 18 : 6, height: 6)
                        .animation(.easeOut(duration: 0.2), value: page)
                }
            }
            .padding(.bottom, 14)
        }
        .frame(height: 260)
        .overlay(alignment: .top) {
            HStack {
                heroButton("chevron.left", action: onBack)
                Spacer()
                HStack(spacing: 10) {
                    heroButton("square.and.arrow.up", action: onShare)
                    heroButton(isFavorite ? "heart.fill" : "heart",
                               tint: isFavorite ? .turoTeal : .white) { isFavorite.toggle() }
                }
            }
            .padding(.horizontal, 16).padding(.top, 56)
        }
    }

    private func heroButton(_ symbol: String, tint: Color = .white, action: @escaping () -> Void) -> some View {
        Button(action: action) {
            Image(systemName: symbol)
                .font(.system(size: 18, weight: .semibold))
                .foregroundStyle(tint)
                .frame(width: 38, height: 38)
                .background(.ultraThinMaterial, in: Circle())
                .background(Color.black.opacity(0.35), in: Circle())
        }
    }
}
```

### Specs Strip

```swift
struct SpecsStrip: View {
    struct Spec: Identifiable { let id = UUID(); let icon: String; let value: String; let label: String }
    let specs: [Spec]

    var body: some View {
        HStack(spacing: 10) {
            ForEach(specs) { s in
                VStack(spacing: 6) {
                    Image(systemName: s.icon)
                        .font(.system(size: 18, weight: .semibold))
                        .foregroundStyle(Color.turoTeal)
                    Text(s.value).font(.system(size: 13, weight: .bold))
                        .foregroundStyle(Color.turoDarkTextPrimary)
                    Text(s.label).font(.system(size: 10))
                        .foregroundStyle(Color.turoDarkTextSecondary)
                }
                .frame(maxWidth: .infinity)
                .padding(.vertical, 12)
                .background(Color.turoDarkSurface1, in: RoundedRectangle(cornerRadius: 12))
                .overlay(RoundedRectangle(cornerRadius: 12).strokeBorder(Color.turoDarkDivider, lineWidth: 1))
            }
        }
    }
}
```

### Host Card

```swift
struct HostCard: View {
    let initials: String, name: String
    let allStar: Bool
    let subline: String     // "214 trips · Joined 2019 · Responds in ~10 min"
    let onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            HStack(spacing: 12) {
                ZStack {
                    LinearGradient(colors: [.turoPurpleBright, .turoTeal],
                                   startPoint: .topLeading, endPoint: .bottomTrailing)
                    Text(initials).font(.system(size: 17, weight: .heavy)).foregroundStyle(.white)
                }
                .frame(width: 46, height: 46).clipShape(Circle())

                VStack(alignment: .leading, spacing: 3) {
                    HStack(spacing: 6) {
                        Text(name).font(.turoCardTitle).foregroundStyle(Color.turoDarkTextPrimary)
                        if allStar {
                            HStack(spacing: 4) {
                                Image(systemName: "star.fill").font(.system(size: 9))
                                Text("ALL-STAR HOST").font(.system(size: 10, weight: .heavy))
                            }
                            .foregroundStyle(Color.turoTeal)
                            .padding(.horizontal, 8).padding(.vertical, 3)
                            .background(Color.turoTeal.opacity(0.16), in: Capsule())
                        }
                    }
                    Text(subline).font(.turoCaption).foregroundStyle(Color.turoDarkTextSecondary)
                }
                Spacer()
                Image(systemName: "chevron.right").font(.system(size: 14))
                    .foregroundStyle(Color.turoDarkTextTertiary)
            }
            .padding(14)
            .background(Color.turoDarkSurface1, in: RoundedRectangle(cornerRadius: 14))
            .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(Color.turoDarkDivider, lineWidth: 1))
        }
        .buttonStyle(.plain)
    }
}
```

### Sticky Price + Book Bar

```swift
struct BookBar: View {
    let dailyRate: String       // "$83"
    let estTotal: String        // "$249 est. total · 3 days"
    let onTotalTap: () -> Void
    let onContinue: () -> Void

    var body: some View {
        HStack(alignment: .center) {
            VStack(alignment: .leading, spacing: 2) {
                HStack(alignment: .firstTextBaseline, spacing: 4) {
                    Text(dailyRate).font(.system(size: 20, weight: .heavy))
                        .foregroundStyle(Color.turoDarkTextPrimary).turoTabularNumbers()
                    Text("/ day").font(.system(size: 13, weight: .medium))
                        .foregroundStyle(Color.turoDarkTextSecondary)
                }
                Button(action: onTotalTap) {
                    Text(estTotal).font(.system(size: 11, weight: .semibold))
                        .foregroundStyle(Color.turoTeal).underline()
                }
            }
            Spacer()
            Button(action: onContinue) {
                Text("Continue").font(.turoButton).foregroundStyle(Color.turoOnPurple)
                    .padding(.horizontal, 30).frame(height: 44)
                    .background(Color.turoPurple, in: RoundedRectangle(cornerRadius: 10))
            }
        }
        .padding(.horizontal, 20).padding(.top, 14).padding(.bottom, 18)
        .background(.ultraThinMaterial)
        .overlay(Rectangle().frame(height: 0.5).foregroundStyle(Color.turoDarkDivider), alignment: .top)
    }
}
```

### Search / Listing Card

```swift
struct CarCard: View {
    let photo: URL?, title: String, ratingLine: String, price: String
    @Binding var saved: Bool

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            ZStack(alignment: .topTrailing) {
                AsyncImage(url: photo) { $0.resizable().aspectRatio(contentMode: .fill) }
                    placeholder: { Rectangle().fill(Color.turoDarkSurface2) }
                    .frame(height: 110).clipped()
                Button { saved.toggle() } label: {
                    Image(systemName: saved ? "heart.fill" : "heart")
                        .font(.system(size: 14, weight: .semibold))
                        .foregroundStyle(saved ? Color.turoTeal : .white)
                        .frame(width: 26, height: 26)
                        .background(Color.black.opacity(0.4), in: Circle())
                }.padding(8)
            }
            VStack(alignment: .leading, spacing: 3) {
                Text(title).font(.system(size: 13, weight: .bold))
                    .foregroundStyle(Color.turoDarkTextPrimary)
                HStack(spacing: 4) {
                    Image(systemName: "star.fill").font(.system(size: 11)).foregroundStyle(Color.turoStar)
                    Text(ratingLine).font(.system(size: 11)).foregroundStyle(Color.turoDarkTextSecondary)
                }
                Text(price).font(.turoCardPrice).foregroundStyle(Color.turoDarkTextPrimary)
                    .padding(.top, 6).turoTabularNumbers()
            }
            .padding(.horizontal, 12).padding(.vertical, 10)
        }
        .background(Color.turoDarkSurface2)
        .clipShape(RoundedRectangle(cornerRadius: 14))
        .overlay(RoundedRectangle(cornerRadius: 14).strokeBorder(Color.turoDarkDivider, lineWidth: 1))
    }
}
```

### Map Price Pin

```swift
enum PinState { case `default`, visited, selected }

struct MapPricePin: View {
    let price: String
    let state: PinState

    var body: some View {
        Text(price)
            .font(.system(size: 13, weight: .heavy))
            .foregroundStyle(fg)
            .padding(.horizontal, 12).padding(.vertical, 7)
            .background(bg, in: Capsule())
            .overlay(state == .visited ? Capsule().strokeBorder(Color.turoDarkDivider, lineWidth: 1) : nil)
            .shadow(color: .black.opacity(0.5), radius: 8, y: 3)
            .turoTabularNumbers()
    }
    private var bg: Color {
        switch state { case .default: .turoPurple; case .visited: .turoDarkSurface2; case .selected: .white }
    }
    private var fg: Color {
        switch state { case .default: .white; case .visited: .turoDarkTextSecondary; case .selected: .turoPurple }
    }
}
```

### Primary Button

```swift
struct TuroPrimaryButton: View {
    let title: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(title).font(.turoButton).foregroundStyle(Color.turoOnPurple)
                .frame(maxWidth: .infinity).frame(height: 52)
                .background(pressed ? Color.turoPurplePressed : Color.turoPurple,
                            in: RoundedRectangle(cornerRadius: 10))
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .onChanged { _ in pressed = true }.onEnded { _ in pressed = false })
        .animation(.easeOut(duration: 0.12), value: pressed)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct TuroTabView: View {
    var body: some View {
        TabView {
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            TripsView().tabItem { Label("Trips", systemImage: "car.fill") }
            FavoritesView().tabItem { Label("Favorites", systemImage: "heart") }
            InboxView().tabItem { Label("Inbox", systemImage: "bubble.left") }
            MoreView().tabItem { Label("More", systemImage: "line.3.horizontal") }
        }
        .tint(.turoPurple)   // active = Turo Purple; iOS draws no pill — matches Turo
    }
}
```

## 5. Motion

```swift
// Photo carousel — page dot morph (active grows to 18pt pill)
.animation(.easeOut(duration: 0.2), value: page)
.sensoryFeedback(.selection, trigger: page)   // soft paging tick

// Favorite tap — heart pop + teal fill
withAnimation(.spring(response: 0.28, dampingFraction: 0.5)) { isFavorite.toggle() }
// scaleEffect keyframes: 1.0 → 1.25 → 1.0
.sensoryFeedback(.impact(weight: .light), trigger: isFavorite)

// Listing open — shared element-style: card photo expands into the 260pt hero (320ms)
.matchedGeometryEffect(id: car.id, in: namespace)
withAnimation(.easeOut(duration: 0.32)) { showDetail = true }

// Map pin select — scale 1.0 → 1.12 + recolor to white selected (150ms)
withAnimation(.easeOut(duration: 0.15)) { selectedPin = id }

// Filter chip toggle — fill cross-fades to purple (150ms)
withAnimation(.easeOut(duration: 0.15)) { filterActive.toggle() }

// Book bar CTA press: scale 1.0 → 0.98 (120ms) + Purple → Pressed Purple
// Sheet present: .presentationDetents([.medium, .large]) — 320ms + scrim
// Booking confirmed: success haptic
.sensoryFeedback(.success, trigger: bookingConfirmed)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Search (tab) | `magnifyingglass` | 22pt |
| Trips (tab) | `car.fill` | 22pt |
| Favorites (tab) | `heart` / `heart.fill` | 22pt |
| Inbox (tab) | `bubble.left` | 22pt |
| More (tab) | `line.3.horizontal` | 22pt |
| Back | `chevron.left` | 18pt |
| Share | `square.and.arrow.up` | 18pt |
| Favorite | `heart` / `heart.fill` | 18pt |
| Star rating | `star.fill` | 13pt |
| All-Star badge | `star.fill` | 9pt |
| Instant book | `bolt.fill` | 14pt |
| Fuel (spec) | `fuelpump.fill` / `bolt.car.fill` | 18pt |
| Seats (spec) | `person.2.fill` | 18pt |
| Transmission (spec) | `gearshape.fill` | 18pt |
| Doors (spec) | `car.side` | 18pt |
| Location | `mappin.and.ellipse` | 16pt |
| Trip dates | `calendar` | 16pt |
| Chevron (row) | `chevron.right` | 14pt |
| Filters | `slider.horizontal.3` | 18pt |

## 7. Dark Mode

```swift
struct TuroTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.turoDarkCanvas : Color.turoCanvas)
            .foregroundStyle(scheme == .dark ? Color.turoDarkTextPrimary : Color.turoTextPrimary)
            .tint(.turoPurple)  // booking-action + active-tab color is identical across themes
    }
}

extension View { func turoTheme() -> some View { modifier(TuroTheme()) } }
```

The dark canvas is near-black `#0F0F12` so full-bleed car photography pops — never a tinted gray. Turo Purple `#593BFB` and Turo Teal `#5CE0B8` are identical across light and dark; on dark, inline links brighten to `#7C5CFF` for WCAG AA. Cards use a 1pt `#2D2D35` border on dark since shadows read poorly on near-black. Photos are never dimmed — only chrome darkens.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UIImpactFeedbackGenerator`); `matchedGeometryEffect` works from iOS 14
- Bundle Turo's brand face via `Info.plist` `UIAppFonts` if licensed; otherwise ship Manrope (SIL OFL) and keep the named ramp identical
- Tabular numerals: apply `.monospacedDigit()` to prices, est. totals, trip counts, and ratings so layout never shifts
- Dynamic Type: scale display, car title, sections, body, card titles, prices; keep tab labels, badge eyebrows, and the rating number FIXED (layout-critical) — derive outside the scaled metric; the photo hero never scales
- VoiceOver: announce the photo hero as "Photo 1 of 8, Tesla Model 3"; the host card as "Host Marcus J., All-Star Host, 214 trips, responds in about 10 minutes, double-tap to view profile"; the Book bar CTA as a button with the daily rate and est. total in its label
- Color is never the only signal: pair the All-Star badge with its star + text, trip status with an icon + label
- Contrast: white on `#593BFB` and `#06231B` on `#5CE0B8` pass WCAG AA; on the near-black canvas use `#F2F1F6` text and the brightened `#7C5CFF` link
- Reduce Motion: disable the card→hero expand and the heart pop (use crossfades); keep the success haptic on booking
- Reduce Transparency: replace the Book-bar `.ultraThinMaterial` and floating-control blur with solid `#0F0F12` / `rgba(15,15,18,0.9)` fills
- Hit targets: primary/Book CTA 52pt (44pt compact); tab icons 22pt in ≥48pt; floating photo controls 38pt visible / 44pt hit; cards full-card tappable ≥64pt
