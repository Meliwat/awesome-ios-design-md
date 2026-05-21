# Apple TV (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates the Apple TV app's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. The browse experience is true-black; only embedded Store/account sheets follow system appearance.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (true-black product)
    static let atvCanvas    = Color.black                                     // #000000
    static let atvSurface1  = Color(red: 0.110, green: 0.110, blue: 0.118)   // #1C1C1E
    static let atvSurface2  = Color(red: 0.173, green: 0.173, blue: 0.180)   // #2C2C2E
    static let atvDivider   = Color(red: 0.220, green: 0.220, blue: 0.227)   // #38383A

    // MARK: - Text (iOS dark label ramp)
    static let atvTextPrimary   = Color.white                                 // #FFFFFF
    static let atvTextSecondary = Color(red: 0.596, green: 0.596, blue: 0.624) // #98989F
    static let atvTextTertiary  = Color(red: 0.388, green: 0.388, blue: 0.400) // #636366

    // MARK: - Primary CTA (always white)
    static let atvCTA        = Color.white                                    // #FFFFFF
    static let atvCTAPressed = Color(red: 0.898, green: 0.898, blue: 0.918)   // #E5E5EA
    static let atvCTALabel   = Color.black                                    // #000000

    // MARK: - Accent (the only one — iOS dark systemBlue)
    static let atvBlue        = Color(red: 0.039, green: 0.518, blue: 1.000)  // #0A84FF
    static let atvBluePressed = Color(red: 0.000, green: 0.376, blue: 0.875)  // #0060DF

    // MARK: - Semantic
    static let atvMLS     = Color(red: 0.929, green: 0.102, blue: 0.435)      // #ED1A6F (MLS only)
    static let atvLive    = Color(red: 1.000, green: 0.271, blue: 0.227)      // #FF453A
    static let atvSuccess = Color(red: 0.188, green: 0.820, blue: 0.345)      // #30D158
    static let atvGold    = Color(red: 1.000, green: 0.839, blue: 0.039)      // #FFD60A
}

extension Color {
    /// iOS translucent control fill — secondary buttons, search field base.
    static let atvGlassFill = Color(red: 0.471, green: 0.471, blue: 0.502).opacity(0.36)
}

extension LinearGradient {
    /// Hero card bottom scrim.
    static let atvHeroScrim = LinearGradient(
        stops: [
            .init(color: .clear,                location: 0.40),
            .init(color: .black.opacity(0.65),  location: 0.72),
            .init(color: .black.opacity(0.92),  location: 1.0),
        ],
        startPoint: .top, endPoint: .bottom
    )
}
```

## 2. Typography

The brand face is **SF Pro** — prefer `-apple-system` on iOS (it resolves to SF Pro and respects optical sizing + Dynamic Type automatically). For a non-Apple build, bundle **Inter** (SIL OFL) as the substitute.

```swift
extension Font {
    // Prefer system styles on iOS — they ARE SF Pro with optical sizing + Dynamic Type
    static let atvLargeTitle = Font.system(size: 34, weight: .heavy,    design: .default)
    static let atvHeroTitle  = Font.system(size: 28, weight: .heavy,    design: .default)
    static let atvRowHeader  = Font.system(size: 22, weight: .bold,     design: .default)
    static let atvTitle3     = Font.system(size: 20, weight: .semibold, design: .default)
    static let atvBody       = Font.system(size: 17, weight: .regular,  design: .default)
    static let atvHeadline   = Font.system(size: 15, weight: .semibold, design: .default)
    static let atvSubhead    = Font.system(size: 13, weight: .regular,  design: .default)
    static let atvCaption    = Font.system(size: 12, weight: .medium,   design: .default)
    static let atvEyebrow    = Font.system(size: 11, weight: .bold,     design: .default)
    static let atvButton     = Font.system(size: 15, weight: .semibold, design: .default)
    static let atvChannelTag = Font.system(size:  9, weight: .bold,     design: .default)

    // Non-Apple build (Inter substitute):
    // static let atvLargeTitle = Font.custom("Inter-ExtraBold", size: 34)
}

// Eyebrow modifier — 11pt uppercase tracked, secondary
struct ATVEyebrow: ViewModifier {
    func body(content: Content) -> some View {
        content
            .font(.atvEyebrow)
            .tracking(1.2)
            .textCase(.uppercase)
            .foregroundStyle(Color.atvTextSecondary)
    }
}
extension View { func atvEyebrow() -> some View { modifier(ATVEyebrow()) } }
```

## 3. Signature Components

### Inset Rounded Hero Card

```swift
struct HeroCard: View {
    let artURL: URL?
    let eyebrow: String        // "Apple TV+ · New Episode"
    let title: String
    let meta: String
    let onPlay: () -> Void
    let onAdd: () -> Void

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            AsyncImage(url: artURL) { img in
                img.resizable().aspectRatio(contentMode: .fill)
            } placeholder: {
                Rectangle().fill(Color.atvSurface1)
            }
            .frame(height: 380)
            .clipped()

            LinearGradient.atvHeroScrim

            VStack(alignment: .leading, spacing: 7) {
                Text(eyebrow).atvEyebrow()
                Text(title).font(.atvHeroTitle).foregroundStyle(Color.atvTextPrimary)
                Text(meta).font(.atvCaption).foregroundStyle(Color.atvTextSecondary)
                HStack(spacing: 10) {
                    Button(action: onPlay) {
                        Label("Play", systemImage: "play.fill")
                            .font(.atvButton)
                            .foregroundStyle(Color.atvCTALabel)
                            .padding(.vertical, 13).padding(.horizontal, 30)
                            .background(Color.atvCTA, in: RoundedRectangle(cornerRadius: 12))
                    }
                    Button(action: onAdd) {
                        Image(systemName: "plus")
                            .font(.system(size: 18, weight: .semibold))
                            .foregroundStyle(.white)
                            .frame(width: 44, height: 44)
                            .background(.ultraThinMaterial, in: Circle())
                    }
                }
                .padding(.top, 7)
            }
            .padding(18)
        }
        .frame(height: 380)
        .clipShape(RoundedRectangle(cornerRadius: 16))   // inset + rounded = floating gallery
        .padding(.horizontal, 14)
    }
}
```

### Up Next Thumbnail (16:9 + resume bar)

```swift
struct UpNextThumb: View {
    let artURL: URL?
    let title: String
    let subhead: String       // "S3 E8 · 24 min left"
    var progress: Double = 0  // 0...1; 0 hides the bar
    var channelTag: String? = "Apple TV+"
    @State private var pressed = false

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            ZStack(alignment: .topLeading) {
                AsyncImage(url: artURL) { $0.resizable().aspectRatio(contentMode: .fill) }
                    placeholder: { Rectangle().fill(Color.atvSurface1) }
                    .frame(width: 196, height: 110)
                    .clipShape(RoundedRectangle(cornerRadius: 10))

                if let channelTag {
                    Text(channelTag)
                        .font(.atvChannelTag).tracking(0.4)
                        .foregroundStyle(.white)
                        .padding(.horizontal, 6).padding(.vertical, 3)
                        .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 5))
                        .padding(8)
                }
                if progress > 0 {
                    GeometryReader { geo in
                        ZStack(alignment: .leading) {
                            Rectangle().fill(Color.white.opacity(0.28))
                            Rectangle().fill(Color.white).frame(width: geo.size.width * progress)
                        }
                        .frame(height: 4)
                        .frame(maxHeight: .infinity, alignment: .bottom)
                    }
                    .frame(width: 196, height: 110)
                }
            }
            Text(title).font(.atvHeadline).foregroundStyle(Color.atvTextPrimary).lineLimit(1)
            Text(subhead).font(.atvSubhead).foregroundStyle(Color.atvTextSecondary).lineLimit(1)
        }
        .frame(width: 196)
        .scaleEffect(pressed ? 0.97 : 1)
        .animation(.easeOut(duration: 0.12), value: pressed)
        ._onButtonGesture { pressed = $0 } perform: {}
    }
}
```

### Live Badge & MLS Chip

```swift
struct LiveBadge: View {
    @State private var dim = false
    var body: some View {
        HStack(spacing: 6) {
            Circle().fill(.white).frame(width: 7, height: 7)
                .opacity(dim ? 0.4 : 1)
                .animation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true), value: dim)
            Text("LIVE").font(.atvEyebrow).tracking(0.6).foregroundStyle(.white)
        }
        .padding(.horizontal, 10).padding(.vertical, 5)
        .background(Color.atvLive, in: RoundedRectangle(cornerRadius: 6))
        .onAppear { dim = true }
    }
}

struct MLSChip: View {
    let title: String   // "MLS Season Pass"
    var body: some View {
        Text(title)
            .font(.system(size: 12, weight: .bold))
            .foregroundStyle(.white)
            .padding(.horizontal, 14).padding(.vertical, 7)
            .background(Color.atvMLS, in: RoundedRectangle(cornerRadius: 10)) // the ONLY non-system color
    }
}
```

### Shelf Header

```swift
struct ShelfHeader: View {
    let title: String
    var accessory: AnyView? = nil
    var body: some View {
        HStack(spacing: 8) {
            Text(title).font(.atvRowHeader).foregroundStyle(Color.atvTextPrimary)
            if let accessory { accessory }
            Spacer()
            Image(systemName: "chevron.right")
                .font(.system(size: 16, weight: .semibold))
                .foregroundStyle(Color.atvTextTertiary)
        }
        .padding(.horizontal, 18)
        .padding(.bottom, 10)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct AppleTVTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithDefaultBackground()             // translucent blur material
        a.backgroundColor = UIColor.black.withAlphaComponent(0.92)
        a.shadowColor = UIColor(Color.atvDivider)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }

    var body: some View {
        TabView {
            WatchNowView().tabItem { Label("Watch Now", systemImage: "play.tv") }
            TVPlusView().tabItem { Label("TV+", systemImage: "play.rectangle.on.rectangle") }
            StoreView().tabItem { Label("Store", systemImage: "bag") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
        }
        .tint(.atvBlue)   // active = system blue; standard iOS color+fill swap, no pill
    }
}
```

## 5. Motion

```swift
// Thumbnail press
.scaleEffect(pressed ? 0.97 : 1)
.animation(.easeOut(duration: 0.12), value: pressed)

// Live dot pulse
.opacity(dim ? 0.4 : 1)
.animation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true), value: dim)

// Hero cross-fade between featured (~10s, if multiple)
.transition(.opacity)
.animation(.easeInOut(duration: 0.4), value: featuredIndex)

// Large title collapse — use a NavigationStack with .navigationTitle + .navigationBarTitleDisplayMode(.large)
// (system handles the large→inline transition tied to scroll)

// Detail open — standard iOS push or .sheet (system 350ms transition; no custom shared element)
.sheet(item: $selected) { DetailView(item: $0) }

// Resume bar appears at value (no fill animation — it's state, not feedback)

// Haptics — sparing, system only
let g = UISelectionFeedbackGenerator(); g.selectionChanged() // thumbnail tap
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Watch Now (tab) | `play.tv` / `play.tv.fill` | 24pt |
| TV+ (tab) | `play.rectangle.on.rectangle` / `.fill` | 24pt |
| Store (tab) | `bag` / `bag.fill` | 24pt |
| Search (tab) | `magnifyingglass` | 24pt |
| MLS (tab, when subscribed) | `sportscourt` / `soccerball` | 24pt |
| Play (CTA) | `play.fill` | 14pt |
| Add to Watchlist | `plus` / `checkmark` | 18pt |
| Back | `chevron.left` | system |
| More / disclosure | `chevron.right` | 16pt |
| Download | `arrow.down.circle` / `.fill` | 20pt |
| Cast / AirPlay | `airplayvideo` | 22pt |
| Profile | (avatar image) / `person.crop.circle` | 30pt |
| Live indicator | `dot.radiowaves.left.and.right` | 14pt |
| Trailer | `play.rectangle` | 16pt |
| Info | `info.circle` | 18pt |
| Settings | `gearshape` | 22pt |

## 7. Dark Mode

```swift
struct AppleTVTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.atvCanvas)            // pure #000000
            .foregroundStyle(Color.atvTextPrimary)
            .preferredColorScheme(.dark)            // browse is always true-black
    }
}
extension View { func appleTVTheme() -> some View { modifier(AppleTVTheme()) } }
```

The browse experience is **always true-black** — lock `.preferredColorScheme(.dark)` on the tab root. Only embedded Store / account `.sheet`s should drop the override and follow the system appearance (they are standard iOS surfaces). Depth comes from the inset rounded hero floating on `#000` plus iOS `.ultraThinMaterial` chrome — there are no drop shadows in browse.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`AsyncImage`, `.ultraThinMaterial`, `Label`, `NavigationStack` are comfortable; `.sensoryFeedback` needs iOS 17 — fall back to `UISelectionFeedbackGenerator`)
- **Prefer the system font**: use `Font.system(...)` / `-apple-system` on iOS — it IS SF Pro with optical sizing and Dynamic Type for free; only bundle Inter (SIL OFL) for non-Apple targets (SF Pro itself is not redistributable off-platform)
- Dynamic Type: rely on `.font(.largeTitle)` / `.body` text styles so everything scales per HIG; keep tab labels, channel tags, eyebrows, "LIVE" at FIXED sizes (layout-sensitive over art)
- VoiceOver: label Up Next thumbnails "{title}, {subhead}, {progress}% watched"; the white CTA "Play {title}"; live cards "Live now"; group hero eyebrow+title+meta as one element
- The pulsing live dot is decorative — mark `.accessibilityHidden(true)`; convey "Live" via the badge label
- Color contrast: white `#FFFFFF` and `#98989F` on `#000000` pass WCAG AA; the white CTA with black label passes AAA; `#0A84FF` is the system link color (AA on black); the MLS pink `#ED1A6F` with white passes AA
- Reduce Motion: disable the hero cross-fade and the live-dot pulse (static dot); keep the press scale subtle or remove; rely on system reduce-motion for transitions
- Reduce Transparency: when enabled, swap `.ultraThinMaterial` chrome for an opaque `Color.atvCanvas` tab bar (system does this automatically for UITabBar)
- Dark mode: browse is always true-black; do not invert. Only embedded Store/account sheets follow system light/dark
