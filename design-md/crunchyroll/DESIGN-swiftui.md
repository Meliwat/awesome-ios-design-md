# Crunchyroll (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Crunchyroll's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Crunchyroll is **dark-only** — there is no light scheme.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-only)
    static let crCanvas    = Color.black                                       // #000000 — true black OLED
    static let crSurface1  = Color(red: 0.086, green: 0.090, blue: 0.102)      // #16171A
    static let crSurface2  = Color(red: 0.137, green: 0.145, blue: 0.169)      // #23252B
    static let crSurface3  = Color(red: 0.180, green: 0.188, blue: 0.208)      // #2E3035
    static let crDivider   = Color(red: 0.165, green: 0.173, blue: 0.192)      // #2A2C31

    // MARK: - Text
    static let crTextPrimary   = Color.white                                   // #FFFFFF
    static let crTextSecondary = Color(red: 0.627, green: 0.627, blue: 0.627)  // #A0A0A0
    static let crTextTertiary  = Color(red: 0.416, green: 0.424, blue: 0.439)  // #6A6C70
    static let crTextOnScrim   = Color(red: 0.847, green: 0.847, blue: 0.847)  // #D8D8D8

    // MARK: - Brand & Status
    static let crOrange        = Color(red: 0.957, green: 0.459, blue: 0.129)  // #F47521 — the only accent
    static let crOrangePressed = Color(red: 0.847, green: 0.380, blue: 0.059)  // #D8610F
    static let crOrangeTint    = Color(red: 1.000, green: 0.549, blue: 0.259)  // #FF8C42
    static let crPremiumGold   = Color(red: 1.000, green: 0.757, blue: 0.027)  // #FFC107
    static let crPremiumInk    = Color(red: 0.102, green: 0.075, blue: 0.016)  // #1A1304
    static let crSimulcast     = Color(red: 0.169, green: 0.714, blue: 0.451)  // #2BB673
    static let crNewBlue       = Color(red: 0.165, green: 0.616, blue: 0.957)  // #2A9DF4
    static let crError         = Color(red: 0.878, green: 0.243, blue: 0.243)  // #E03E3E
}

// Hero-to-black scrim gradient (the signature depth cue)
extension LinearGradient {
    static let crHeroScrim = LinearGradient(
        stops: [
            .init(color: .clear,                    location: 0.0),
            .init(color: .black.opacity(0.55),      location: 0.55),
            .init(color: .black,                    location: 1.0),
        ],
        startPoint: .top, endPoint: .bottom
    )
}
```

## 2. Typography

Crunchyroll's product face is **Lato**, leaning hard on the 900 (Black) weight for titles, CTAs, and badges. Bundle Lato TTFs via `Info.plist` / `UIAppFonts`. Map Lato Black → SF Pro `.black` when not bundled.

```swift
extension Font {
    static func crLato(_ size: CGFloat, _ weight: Weight = .regular) -> Font {
        let face: String
        switch weight {
        case .black, .heavy: face = "Lato-Black"
        case .bold:          face = "Lato-Bold"
        default:             face = "Lato-Regular"
        }
        return .custom(face, size: size)
    }

    static let crScreenTitle   = Font.custom("Lato-Black",   size: 32)  // weight 900
    static let crHeroTitle     = Font.custom("Lato-Black",   size: 28)
    static let crSectionHeader = Font.custom("Lato-Black",   size: 22)
    static let crRowHeader     = Font.custom("Lato-Bold",    size: 18)
    static let crBody          = Font.custom("Lato-Regular", size: 16)
    static let crEpisodeTitle  = Font.custom("Lato-Bold",    size: 15)
    static let crSynopsis      = Font.custom("Lato-Regular", size: 13)
    static let crMeta          = Font.custom("Lato-Regular", size: 14)
    static let crButtonLabel   = Font.custom("Lato-Black",   size: 16)
    static let crSegmented     = Font.custom("Lato-Black",   size: 14)
    static let crBadge         = Font.custom("Lato-Black",   size: 11)
    static let crTab           = Font.custom("Lato-Bold",    size: 10)
    static let crCaption       = Font.custom("Lato-Regular", size: 12)
}
```

## 3. Signature Components

### Anime Detail Hero (full-bleed key-art + scrim)

```swift
struct AnimeHero: View {
    let keyArtUrl: String
    let title: String
    let meta: String           // "★ 4.9 · 2024 · TV-14 · Action, Fantasy"
    let isSimulcast: Bool
    let isPremium: Bool

    var body: some View {
        GeometryReader { geo in
            ZStack(alignment: .bottomLeading) {
                AsyncImage(url: URL(string: keyArtUrl)) { img in
                    img.resizable().aspectRatio(contentMode: .fill)
                } placeholder: {
                    Rectangle().fill(Color.crSurface1)
                }
                .frame(width: geo.size.width, height: geo.size.height)
                .clipped()

                LinearGradient.crHeroScrim

                VStack(alignment: .leading, spacing: 8) {
                    HStack(spacing: 8) {
                        if isSimulcast { ContentBadge(.simulcast) }
                        if isPremium   { ContentBadge(.premium) }
                    }
                    Text(title)
                        .font(.crHeroTitle)
                        .foregroundStyle(Color.crTextPrimary)
                        .lineLimit(2)
                    Text(meta)
                        .font(.crMeta)
                        .foregroundStyle(Color.crTextOnScrim)
                }
                .padding(.horizontal, 20)
                .padding(.bottom, 18)
            }
        }
        .frame(height: UIScreen.main.bounds.height * 0.60)
    }
}
```

### Primary CTA ("Start Watching" / "Resume")

```swift
struct WatchButton: View {
    let label: String          // "Start Watching E1" or "Resume E7"
    let action: () -> Void
    @GestureState private var pressed = false

    var body: some View {
        Button(action: action) {
            HStack(spacing: 10) {
                Image(systemName: "play.fill").font(.system(size: 18))
                Text(label).font(.crButtonLabel).tracking(0.3)
            }
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .frame(height: 52)
            .background(pressed ? Color.crOrangePressed : Color.crOrange)
            .clipShape(RoundedRectangle(cornerRadius: 8))
            .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .simultaneousGesture(
            DragGesture(minimumDistance: 0)
                .updating($pressed) { _, s, _ in s = true }
        )
    }
}
```

### Episode Row (resume-aware)

```swift
struct EpisodeRow: View {
    let number: String         // "E1"
    let stillUrl: String
    let title: String
    let synopsis: String
    let progress: Double       // 0.0 ... 1.0

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            ZStack(alignment: .topLeading) {
                AsyncImage(url: URL(string: stillUrl)) { img in
                    img.resizable().aspectRatio(contentMode: .fill)
                } placeholder: { Rectangle().fill(Color.crSurface2) }
                .frame(width: 112, height: 64)
                .clipShape(RoundedRectangle(cornerRadius: 6))

                Text(number)
                    .font(.crBadge)
                    .foregroundStyle(.white)
                    .padding(.vertical, 2).padding(.horizontal, 6)
                    .background(Color.black.opacity(0.7))
                    .clipShape(RoundedRectangle(cornerRadius: 3))
                    .padding(4)

                if progress > 0 {
                    VStack {
                        Spacer()
                        GeometryReader { g in
                            ZStack(alignment: .leading) {
                                Rectangle().fill(Color.white.opacity(0.25))
                                Rectangle().fill(Color.crOrange)
                                    .frame(width: g.size.width * progress)
                            }
                        }
                        .frame(height: 3)
                    }
                    .frame(width: 112, height: 64)
                }
            }

            VStack(alignment: .leading, spacing: 3) {
                Text(title)
                    .font(.crEpisodeTitle)
                    .foregroundStyle(Color.crTextPrimary)
                Text(synopsis)
                    .font(.crSynopsis)
                    .foregroundStyle(Color.crTextSecondary)
                    .lineLimit(2)
            }
            Spacer(minLength: 0)
        }
        .padding(.vertical, 12)
        .overlay(alignment: .bottom) {
            Rectangle().fill(Color.crDivider).frame(height: 1)
        }
    }
}
```

### Content Badge (Simulcast / Premium / New Episode)

```swift
struct ContentBadge: View {
    enum Kind { case simulcast, premium, newEpisode }
    let kind: Kind
    init(_ kind: Kind) { self.kind = kind }

    private var text: String {
        switch kind {
        case .simulcast:  return "SIMULCAST"
        case .premium:    return "PREMIUM"
        case .newEpisode: return "NEW EPISODE"
        }
    }
    private var bg: Color {
        switch kind {
        case .simulcast:  return .crSimulcast
        case .premium:    return .crPremiumGold
        case .newEpisode: return .crNewBlue
        }
    }
    private var fg: Color { kind == .premium ? .crPremiumInk : .white }

    var body: some View {
        Text(text)
            .font(.crBadge)
            .tracking(0.4)
            .foregroundStyle(fg)
            .padding(.vertical, 4).padding(.horizontal, 8)
            .background(bg)
            .clipShape(RoundedRectangle(cornerRadius: 4))
    }
}
```

### Segmented Control (sliding orange underline)

```swift
struct CRSegmented: View {
    let items: [String]        // ["Episodes", "Details", "More Like This"]
    @Binding var selection: Int
    @Namespace private var ns

    var body: some View {
        VStack(spacing: 0) {
            HStack(spacing: 24) {
                ForEach(items.indices, id: \.self) { i in
                    VStack(spacing: 10) {
                        Text(items[i])
                            .font(.crSegmented)
                            .foregroundStyle(i == selection ? Color.crTextPrimary : Color.crTextSecondary)
                        ZStack {
                            if i == selection {
                                RoundedRectangle(cornerRadius: 2)
                                    .fill(Color.crOrange)
                                    .frame(height: 3)
                                    .matchedGeometryEffect(id: "underline", in: ns)
                            } else {
                                Color.clear.frame(height: 3)
                            }
                        }
                    }
                    .onTapGesture {
                        withAnimation(.easeOut(duration: 0.22)) { selection = i }
                    }
                }
                Spacer()
            }
            Rectangle().fill(Color.crDivider).frame(height: 1)
        }
    }
}
```

### Sub | Dub Control

```swift
struct SubDubControl: View {
    @Binding var isDub: Bool

    var body: some View {
        HStack(spacing: 0) {
            ForEach(["SUB", "DUB"], id: \.self) { label in
                let active = (label == "DUB") == isDub
                Text(label)
                    .font(.crLato(12, .black))
                    .foregroundStyle(.white)
                    .padding(.vertical, 6).padding(.horizontal, 14)
                    .background(active ? Color.crOrange : Color.clear)
                    .overlay(
                        RoundedRectangle(cornerRadius: 6)
                            .strokeBorder(active ? .clear : Color.white.opacity(0.5), lineWidth: 1)
                    )
                    .clipShape(RoundedRectangle(cornerRadius: 6))
                    .onTapGesture { isDub = (label == "DUB") }
            }
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct CRTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem      { Label("Home",      systemImage: "house.fill") }
            BrowseView().tabItem    { Label("Browse",    systemImage: "square.grid.2x2.fill") }
            WatchlistView().tabItem { Label("Watchlist", systemImage: "plus") }
            MangaView().tabItem     { Label("Manga",     systemImage: "book.fill") }
            ProfileView().tabItem   { Label("Profile",   systemImage: "person.crop.circle") }
        }
        .tint(.crOrange)                         // active = Crunchyroll Orange, no tint pill
        .toolbarBackground(Color.black.opacity(0.95), for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
        .preferredColorScheme(.dark)             // dark-only
    }
}
```

## 5. Motion

```swift
// Segmented underline slide
withAnimation(.easeOut(duration: 0.22)) { selection = i }
// .matchedGeometryEffect(id: "underline", in: ns) on the 3pt orange bar

// Card press
.scaleEffect(pressed ? 0.97 : 1)
.animation(.easeOut(duration: 0.15), value: pressed)

// "Lights down" — fade detail to black before the player appears
withAnimation(.easeInOut(duration: 0.25)) { detailOpacity = 0 }   // then present player

// Progress fill on return to a partially-watched title
.onAppear { withAnimation(.easeOut(duration: 0.40)) { fill = watched } }

// Hero parallax (detail scroll)
// Track scrollOffset; key-art .offset(y: scrollOffset * 0.5)

// Haptics
let soft = UIImpactFeedbackGenerator(style: .light)   // watchlist toggle, segment change
let med  = UIImpactFeedbackGenerator(style: .medium)  // Start Watching
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Browse (tab) | `square.grid.2x2` / `.fill` | 22pt |
| Watchlist (tab) | `plus` | 22pt |
| Manga (tab) | `book` / `book.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Play (CTA) | `play.fill` | 18pt |
| Add to Watchlist | `plus` / `checkmark` (saved) | 22pt |
| Rate | `hand.thumbsup` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Comments | `bubble.left` | 22pt |
| Cast | `airplayvideo` | 22pt |
| Back | `chevron.left` | 22pt |
| Search | `magnifyingglass` | 22pt |
| Notifications | `bell` | 22pt |
| Premium lock | `lock.fill` | 14pt |
| Download | `arrow.down.circle` | 22pt |

## 7. Dark Mode

Crunchyroll is **dark-only** — there is no light scheme. Lock the app to dark and the true-black canvas.

```swift
struct CRTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.crCanvas)
            .foregroundStyle(Color.crTextPrimary)
            .preferredColorScheme(.dark)         // never light
            .tint(.crOrange)
    }
}

extension View {
    func crTheme() -> some View { modifier(CRTheme()) }
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`matchedGeometryEffect`, `AsyncImage`, `.toolbarBackground` all available; `.sensoryFeedback` needs iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Bundle Lato (Regular / Bold / Black) TTFs via `Info.plist` `UIAppFonts` — Lato is SIL OFL, free to redistribute; map Lato-Black → SF Pro `.black` if unbundled
- Lock `preferredColorScheme(.dark)` app-wide — Crunchyroll has no light mode; a light canvas breaks the OLED key-art floor
- Dynamic Type: scale hero title, section headers, body, synopsis, meta; keep badges, tab labels, segmented items, episode-number chip, and progress-bar geometry at FIXED sizes
- VoiceOver: label the hero as "{title}, {meta}, {Simulcast/Premium}"; label episode rows as "Episode {n}, {title}, {percent}% watched"; the progress bar should expose its value via `.accessibilityValue`
- Contrast: `#FFFFFF` on `#000000` is maximal; `#A0A0A0` meta on the scrim passes AA at 14pt; PREMIUM badge uses `#1A1304` on `#FFC107` (dark-on-gold) to pass AA — never white-on-gold
- The single-accent rule is an accessibility asset: orange is the *only* "actionable / resume" signal — don't dilute it with a second accent
- Reduce Motion: disable hero parallax and the "lights down" fade (cut straight to the player); keep the segmented underline as a crossfade instead of a slide
- Respect the safe area / Dynamic Island: detail-screen top controls (back, share, cast) float over the key-art and must sit below the island
