# Bandcamp (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Bandcamp's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the big square album art, the buy/support card, the inline teal-waveform player, the tracklist, and the fan collection card.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light — Bandcamp's true identity)
    static let bcPaper        = Color.white                                   // #FFFFFF
    static let bcPaperGray    = Color(red: 0.957, green: 0.957, blue: 0.957) // #F4F4F4
    static let bcHoverGray    = Color(red: 0.929, green: 0.929, blue: 0.929) // #EDEDED
    static let bcDivider      = Color(red: 0.886, green: 0.886, blue: 0.886) // #E2E2E2
    static let bcTrackDivider = Color(red: 0.941, green: 0.941, blue: 0.941) // #F0F0F0

    // MARK: - Canvas & Surfaces (Dark — true inversion, NOT default)
    static let bcDarkCanvas   = Color(red: 0.063, green: 0.078, blue: 0.090) // #101417
    static let bcDarkSurface1 = Color(red: 0.102, green: 0.125, blue: 0.141) // #1A2024
    static let bcDarkSurface2 = Color(red: 0.137, green: 0.169, blue: 0.188) // #232B30
    static let bcDarkDivider  = Color(red: 0.173, green: 0.208, blue: 0.231) // #2C353B

    // MARK: - Text
    static let bcInk           = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let bcTextSecondary = Color(red: 0.463, green: 0.463, blue: 0.463) // #767676
    static let bcTextTertiary  = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A
    static let bcDarkInk       = Color(red: 0.929, green: 0.945, blue: 0.953) // #EDF1F3

    // MARK: - Brand
    static let bcTeal       = Color(red: 0.114, green: 0.627, blue: 0.765) // #1DA0C3
    static let bcTealDeep   = Color(red: 0.384, green: 0.604, blue: 0.663) // #629AA9
    static let bcTealPress  = Color(red: 0.090, green: 0.506, blue: 0.620) // #17819E
    static let bcDarkTeal   = Color(red: 0.239, green: 0.710, blue: 0.839) // #3DB5D6

    // MARK: - Semantic
    static let bcSuccess = Color(red: 0.298, green: 0.686, blue: 0.314) // #4CAF50
    static let bcError   = Color(red: 0.894, green: 0.345, blue: 0.345) // #E45858
    static let bcWarning = Color(red: 0.878, green: 0.627, blue: 0.188) // #E0A030
}
```

## 2. Typography

Bandcamp uses a clean humanist sans; use **DM Sans** as the faithful fallback (SIL OFL, free to bundle). Bundle via `Info.plist` / `UIAppFonts`. Editorial, record-sleeve rhythm — titles at 700, artist/price at 600–700, body at 400.

```swift
enum BCFont {
    static let face = "DMSans"   // swap to your licensed Bandcamp face if available
}

extension Font {
    static func bc(_ size: CGFloat, _ weight: Font.Weight = .regular) -> Font {
        Font.custom(BCFont.face, size: size).weight(weight)
    }

    static let bcScreenTitle  = Font.custom(BCFont.face, size: 32).weight(.bold)      // 700
    static let bcAlbumTitle   = Font.custom(BCFont.face, size: 22).weight(.bold)      // 700
    static let bcSection      = Font.custom(BCFont.face, size: 22).weight(.bold)      // 700
    static let bcArtistLink   = Font.custom(BCFont.face, size: 18).weight(.semibold)  // 600 (teal)
    static let bcSubhead      = Font.custom(BCFont.face, size: 18).weight(.semibold)  // 600
    static let bcBody         = Font.custom(BCFont.face, size: 16).weight(.regular)   // 400
    static let bcPrice        = Font.custom(BCFont.face, size: 22).weight(.bold)      // 700
    static let bcPriceUnit    = Font.custom(BCFont.face, size: 13).weight(.semibold)  // 600
    static let bcRowTitle     = Font.custom(BCFont.face, size: 15).weight(.semibold)  // 600
    static let bcMeta         = Font.custom(BCFont.face, size: 14).weight(.regular)   // 400
    static let bcTrackName    = Font.custom(BCFont.face, size: 14).weight(.medium)    // 500
    static let bcTrackNum     = Font.custom(BCFont.face, size: 12).weight(.semibold)  // 600, tabular
    static let bcTabLabel     = Font.custom(BCFont.face, size: 10).weight(.semibold)  // 600
    static let bcButton       = Font.custom(BCFont.face, size: 15).weight(.bold)      // 700
}

extension View { func bcTabular() -> some View { self.monospacedDigit() } }
```

## 3. Signature Components

### Big Square Album Art

```swift
struct AlbumArt: View {
    let url: String?
    var fullBleed: Bool = true   // true on album page; false (4pt radius) in grids

    var body: some View {
        Group {
            if let url, let u = URL(string: url) {
                AsyncImage(url: u) { img in
                    img.resizable().aspectRatio(contentMode: .fill)
                } placeholder: {
                    Rectangle().fill(Color.bcPaperGray)   // flat, no shimmer
                }
            } else {
                Rectangle().fill(Color.bcPaperGray)
            }
        }
        .aspectRatio(1, contentMode: .fit)               // strictly square
        .clipShape(RoundedRectangle(cornerRadius: fullBleed ? 0 : 4))
        // NEVER a circle, never tinted, never shadowed — content is sovereign
    }
}
```

### Buy / Support Card (signature)

```swift
struct BuySupportCard: View {
    let price: String          // "9"
    let currency: String       // "USD"
    let nameYourPrice: Bool
    var onBuy: () -> Void = {}
    var onWishlist: () -> Void = {}

    var body: some View {
        VStack(spacing: 0) {
            HStack(alignment: .firstTextBaseline) {
                Text("$\(price)").font(.bcPrice).foregroundStyle(Color.bcInk).bcTabular()
                Text(currency).font(.bcPriceUnit).foregroundStyle(Color.bcTextSecondary)
                Spacer()
                Text(nameYourPrice ? "or more" : "")
                    .font(.bcPriceUnit).foregroundStyle(Color.bcTextSecondary)
            }
            if nameYourPrice {
                Text("Name your price · Pay what you want")
                    .font(.bc(12, .regular)).foregroundStyle(Color.bcTextSecondary)
                    .frame(maxWidth: .infinity, alignment: .leading)
                    .padding(.top, 4)
            }
            Button(action: onBuy) {
                Text("Buy Digital Album")
                    .font(.bcButton).foregroundStyle(.white)
                    .frame(maxWidth: .infinity).padding(.vertical, 13)
                    .background(Color.bcTeal, in: RoundedRectangle(cornerRadius: 4))
            }
            .buttonStyle(BCPress())
            .padding(.top, 14)

            Button(action: onWishlist) {
                Text("Add to wishlist")
                    .font(.bc(13, .semibold)).foregroundStyle(Color.bcTeal)
            }
            .padding(.top, 10)
        }
        .padding(16)
        .background(Color.bcPaperGray)
        .overlay(RoundedRectangle(cornerRadius: 6).strokeBorder(Color.bcDivider, lineWidth: 1))
        .clipShape(RoundedRectangle(cornerRadius: 6))
    }
}

struct BCPress: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.99 : 1)
            .brightness(configuration.isPressed ? -0.05 : 0)
            .animation(.easeOut(duration: 0.1), value: configuration.isPressed)
    }
}
```

### Inline Teal-Waveform Player

```swift
struct InlinePlayer: View {
    @Binding var isPlaying: Bool
    let track: String          // "2. Glider"
    let elapsed: String        // "1:42"
    let total: String          // "5:08"
    let progress: Double       // 0...1
    private let bars: [CGFloat] = [6,13,9,16,7,11,8,14,10,12]

    var body: some View {
        HStack(spacing: 12) {
            Button { isPlaying.toggle() } label: {
                ZStack {
                    Circle().fill(Color.bcInk).frame(width: 36, height: 36)
                    Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                        .font(.system(size: 14, weight: .bold))
                        .foregroundStyle(.white).offset(x: isPlaying ? 0 : 2)
                }
            }.buttonStyle(BCPress())

            VStack(alignment: .leading, spacing: 2) {
                Text(track).font(.bc(13, .semibold)).foregroundStyle(Color.bcInk)
                Text("\(elapsed) / \(total)")
                    .font(.bc(11)).foregroundStyle(Color.bcTextSecondary).bcTabular()
            }
            Spacer()

            HStack(alignment: .center, spacing: 2) {
                ForEach(bars.indices, id: \.self) { i in
                    Capsule()
                        .fill(Color.bcTeal.opacity(Double(i) / Double(bars.count) < progress ? 1 : 0.35))
                        .frame(width: 2.5, height: bars[i])
                }
            }
            .frame(height: 18)
        }
        .padding(.horizontal, 20).padding(.vertical, 12)
        .overlay(alignment: .top)    { Divider().background(Color.bcDivider) }
        .overlay(alignment: .bottom) { Divider().background(Color.bcDivider) }
    }
}
```

### Tracklist

```swift
struct TrackRow: View {
    let number: Int
    let name: String
    let duration: String
    let isPlaying: Bool

    var body: some View {
        VStack(spacing: 0) {
            HStack(spacing: 12) {
                Text("\(number)")
                    .font(.bcTrackNum).foregroundStyle(Color.bcTextTertiary)
                    .frame(width: 18, alignment: .leading).bcTabular()
                Text(name)
                    .font(isPlaying ? .bc(14, .bold) : .bcTrackName)
                    .foregroundStyle(isPlaying ? Color.bcTeal : Color.bcInk)
                Spacer()
                Text(duration)
                    .font(.bc(12)).foregroundStyle(Color.bcTextSecondary).bcTabular()
            }
            .padding(.vertical, 9)
            Divider().background(Color.bcTrackDivider)
        }
        .padding(.horizontal, 20)
        .contentShape(Rectangle())
    }
}
```

### Fan Collection Card

```swift
struct CollectionCard: View {
    let artUrl: String?
    let title: String
    let artist: String

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            AlbumArt(url: artUrl, fullBleed: false)   // 4pt radius square
            Text(title).font(.bc(13, .semibold)).foregroundStyle(Color.bcInk)
                .lineLimit(1).padding(.top, 8)
            Text(artist).font(.bc(12)).foregroundStyle(Color.bcTextSecondary)
                .lineLimit(1).padding(.top, 2)
        }
    }
}

// Feed wrapper: "{Fan} bought this" + optional note above the card
struct FeedItem: View {
    let fan: String
    let note: String?
    let card: CollectionCard
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("\(fan) bought this")
                .font(.bc(13, .semibold)).foregroundStyle(Color.bcInk)
            if let note { Text(note).font(.bcBody).foregroundStyle(Color.bcTextSecondary) }
            card
        }
        .padding(.horizontal, 20)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct BCTabView: View {
    var body: some View {
        TabView {
            FeedView().tabItem       { Label("Feed",       systemImage: "house.fill") }
            DiscoverView().tabItem   { Label("Discover",   systemImage: "magnifyingglass") }
            CollectionView().tabItem { Label("Collection", systemImage: "square.grid.2x2.fill") }
            ProfileView().tabItem    { Label("Profile",    systemImage: "person.fill") }
        }
        .tint(.bcTeal)   // active = Bandcamp teal; inactive defaults to gray
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.bcPaper)
            a.shadowColor = UIColor(Color.bcDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
            UITabBar.appearance().unselectedItemTintColor = UIColor(Color.bcTextTertiary)
        }
    }
}
```

## 5. Motion

```swift
// Buy button press — scale 0.99 + darken, soft haptic
.scaleEffect(isPressed ? 0.99 : 1)
.brightness(isPressed ? -0.05 : 0)
.animation(.easeOut(duration: 0.1), value: isPressed)
.sensoryFeedback(.impact(flexibility: .soft), trigger: didBuy)

// Add to collection / wishlist — quick teal fill + soft haptic
withAnimation(.easeOut(duration: 0.15)) { inCollection = true }
.sensoryFeedback(.impact(flexibility: .soft), trigger: inCollection)

// Page navigation — native push (300ms); album art shared element if available
.navigationTransition(.zoom(sourceID: albumID, in: namespace))   // iOS 18+

// Player progress — linear, no spring; waveform bars fill teal as time advances
// progress drives bar opacity (played = 1.0, unplayed = 0.35)

// Tracklist row tap — highlight #EDEDED 120ms, then track name turns teal
// Bottom sheet (checkout/share) — slide up 300ms ease-out + dim fade-in
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Back | `chevron.left` | 22pt |
| Overflow menu | `ellipsis` | 22pt |
| Play | `play.fill` | 14pt |
| Pause | `pause.fill` | 14pt |
| Next track | `forward.fill` | 16pt |
| Previous track | `backward.fill` | 16pt |
| Wishlist (add) | `heart` | 18pt |
| Wishlist (added) | `heart.fill` | 18pt |
| In collection | `checkmark.circle.fill` | 18pt |
| Gift this | `gift` | 20pt |
| Share | `square.and.arrow.up` | 22pt |
| Feed (tab) | `house` / `house.fill` | 22pt |
| Discover (tab) | `magnifyingglass` | 22pt |
| Collection (tab) | `square.grid.2x2` / `.fill` | 22pt |
| Profile (tab) | `person` / `person.fill` | 22pt |
| Search field | `magnifyingglass` | 16pt |
| Download | `arrow.down.circle` | 20pt |
| Buy / cart | `bag` | 20pt |
| Artist / merch | `tshirt` | 20pt |

## 7. Dark Mode

Bandcamp is light-first — the white editorial page IS the identity. Support a true dark inversion (cool charcoal `#101417`), but do NOT make it the default; follow the system setting.

```swift
struct BCTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.bcDarkCanvas : Color.bcPaper)
            .foregroundStyle(scheme == .dark ? Color.bcDarkInk : Color.bcInk)
            .tint(scheme == .dark ? Color.bcDarkTeal : Color.bcTeal)
    }
}
extension View { func bcTheme() -> some View { modifier(BCTheme()) } }
```

In dark mode: canvas → `#101417`, grouped surface → `#1A2024`, divider → `#2C353B`, ink → `#EDF1F3`, teal shifts to `#3DB5D6`. **Album art is never tinted or dimmed** — only chrome inverts; content stays sovereign.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator(style: .soft)`; `.navigationTransition(.zoom)` requires iOS 18 — gate it)
- Bundle the Bandcamp face if licensed; otherwise ship **DM Sans** (SIL OFL, free) as the faithful fallback — never silently degrade to system-only for titles
- Dynamic Type: scale screen/album titles, artist link, body, prices, track names; keep tab labels, track numbers, captions, price unit FIXED (layout-sensitive)
- VoiceOver: label album art as "{Album} by {Artist}, album artwork"; the buy card as one element announcing "{price} {currency} or more, Buy Digital Album"; tracklist rows as "Track {n}, {name}, {duration}{, now playing}" — convey the playing state in the label, not teal color alone
- The price is critical content: never hide it from VoiceOver; it must be reachable and announced before the Buy action
- Color contrast: `#1A1A1A` on `#FFFFFF` passes WCAG AAA; `#1DA0C3` on `#FFFFFF` passes AA for ≥14pt and for the Buy button (white on teal passes AA); `#767676` on white passes AA for ≥14pt
- Reduce Motion: disable the album-art zoom transition (use a plain push) and the teal fill animation (instant state) — keep nothing essential behind motion
- Reduce Transparency: bottom sheets use a solid `#FFFFFF` / `#101417` background instead of any material
- Dark mode is a true inversion to `#101417` (NOT the default); never dim or overlay album art for contrast — the artwork the artist uploaded must always render full-color and untouched
