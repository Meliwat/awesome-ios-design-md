# Prime Video (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Prime Video's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let primeCanvas    = Color(red: 0.059, green: 0.090, blue: 0.118) // #0F171E
    static let primeDeepBlack = Color.black                                  // #000000
    static let primeSurface1  = Color(red: 0.102, green: 0.141, blue: 0.184) // #1A242F
    static let primeSurface2  = Color(red: 0.137, green: 0.184, blue: 0.243) // #232F3E
    static let primeSurface3  = Color(red: 0.180, green: 0.231, blue: 0.278) // #2E3B47
    static let primeDivider   = Color(red: 0.180, green: 0.231, blue: 0.278) // #2E3B47

    // MARK: - Text
    static let primeTextPrimary   = Color.white                                // #FFFFFF
    static let primeTextSecondary = Color(red: 0.667, green: 0.718, blue: 0.769) // #AAB7C4
    static let primeTextTertiary  = Color(red: 0.431, green: 0.482, blue: 0.537) // #6E7B89

    // MARK: - Brand
    static let primeBlue        = Color(red: 0.000, green: 0.659, blue: 0.882) // #00A8E1
    static let primeBluePressed = Color(red: 0.000, green: 0.549, blue: 0.741) // #008CBD
    static let primeImdbYellow  = Color(red: 0.961, green: 0.773, blue: 0.094) // #F5C518
    static let primeLiveRed     = Color(red: 0.898, green: 0.035, blue: 0.078) // #E50914
}
```

## 2. Typography

Amazon Ember is a licensed brand typeface. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to Inter (or `.system`) — an open humanist grotesque is the closest free substitute.

```swift
extension Font {
    static let primeDetailsTitle = Font.custom("AmazonEmber-Bold",     size: 30).weight(.bold)
    static let primeTitleLarge   = Font.custom("AmazonEmber-Bold",     size: 28).weight(.bold)
    static let primeRowHeader    = Font.custom("AmazonEmber-Bold",     size: 22).weight(.bold)
    static let primeSectionHeader = Font.custom("AmazonEmber-Bold",    size: 20).weight(.bold)
    static let primeEpisodeTitle = Font.custom("AmazonEmber-Medium",   size: 16).weight(.semibold)
    static let primeTileTitle    = Font.custom("AmazonEmber-Medium",   size: 15).weight(.semibold)
    static let primeBody         = Font.custom("AmazonEmber-Regular",  size: 15).weight(.regular)
    static let primeMeta         = Font.custom("AmazonEmber-Regular",  size: 13).weight(.regular)
    static let primeTileSubtitle = Font.custom("AmazonEmber-Regular",  size: 12).weight(.regular)
    static let primeLabelUpper   = Font.custom("AmazonEmber-Bold",     size: 11).weight(.bold)
    static let primeButton       = Font.custom("AmazonEmber-Bold",     size: 16).weight(.bold)
    static let primeButtonSecondary = Font.custom("AmazonEmber-Medium", size: 15).weight(.semibold)
    static let primeTab          = Font.custom("AmazonEmber-Medium",   size: 10).weight(.semibold)
    static let primeBadge        = Font.custom("AmazonEmber-Bold",     size: 11).weight(.bold)
}

extension Font {
    static func prime(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Primary Blue Play Button

```swift
struct PrimePlayButton: View {
    var title: String = "Play"
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill").font(.system(size: 18, weight: .bold))
                Text(title).font(.primeButton).tracking(0.2)
            }
            .foregroundStyle(Color.primeCanvas) // intentional: dark-navy on bright blue
            .frame(maxWidth: .infinity)
            .frame(height: 52)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.primeBlue))
            .shadow(color: Color.primeBlue.opacity(0.32), radius: 24, y: 8)
        }
        .sensoryFeedback(.impact(weight: .light), trigger: title)
        .buttonStyle(PrimePressableStyle(pressedScale: 0.97))
    }
}

struct PrimePressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Watchlist Toggle

```swift
struct PrimeWatchlistButton: View {
    @Binding var added: Bool
    @State private var bump = false

    var body: some View {
        Button {
            added.toggle()
            bump = true
        } label: {
            HStack(spacing: 8) {
                Image(systemName: added ? "checkmark" : "plus")
                    .font(.system(size: 16, weight: .bold))
                Text(added ? "Watchlisted" : "Watchlist").font(.primeButtonSecondary)
            }
            .foregroundStyle(added ? Color.primeBlue : .white)
            .frame(maxWidth: .infinity)
            .frame(height: 52)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.white.opacity(0.14)))
            .scaleEffect(bump ? 1.15 : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.6), value: bump)
        }
        .sensoryFeedback(.success, trigger: added)
        .onChange(of: bump) { _, v in if v { DispatchQueue.main.asyncAfter(deadline: .now() + 0.25) { bump = false } } }
        .buttonStyle(PrimePressableStyle())
    }
}
```

### Content Tile (2:3 poster with Prime ribbon)

```swift
struct PrimeContentTile: View {
    let title: String
    let artwork: Image
    var includedWithPrime: Bool = false
    var progress: Double? = nil
    let width: CGFloat
    var aspect: CGFloat = 2/3

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            ZStack(alignment: .bottom) {
                artwork
                    .resizable()
                    .aspectRatio(aspect, contentMode: .fill)
                    .frame(width: width, height: width / aspect)
                    .clipShape(RoundedRectangle(cornerRadius: 6))
                if let progress {
                    GeometryReader { geo in
                        ZStack(alignment: .leading) {
                            Rectangle().fill(Color.white.opacity(0.25))
                            Rectangle().fill(Color.primeBlue).frame(width: geo.size.width * progress)
                        }
                    }
                    .frame(height: 3)
                }
            }
            Text(title).font(.primeTileTitle).foregroundStyle(.white).lineLimit(1)
            if includedWithPrime {
                Text("Included with Prime")
                    .font(.primeTileSubtitle)
                    .foregroundStyle(Color.primeBlue)
            }
        }
        .frame(width: width)
    }
}
```

### Hero Billboard

```swift
struct PrimeBillboard: View {
    let title: String
    let metaLeading: String   // "2024 · 16+ · 1 Season · "
    let imdb: String          // "★ 8.4 IMDb"
    let still: Image
    let onPlay: () -> Void
    @Binding var watchlisted: Bool

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            still
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(height: 440)
                .clipped()
            LinearGradient(colors: [.clear, .primeCanvas], startPoint: .center, endPoint: .bottom)

            VStack(alignment: .leading, spacing: 12) {
                Text(title).font(.primeDetailsTitle).foregroundStyle(.white)
                (Text(metaLeading).foregroundColor(.primeTextSecondary)
                 + Text(imdb).foregroundColor(.primeImdbYellow))
                .font(.primeMeta)
                HStack(spacing: 12) {
                    PrimePlayButton(action: onPlay).frame(width: 130)
                    PrimeWatchlistButton(added: $watchlisted).frame(width: 150)
                }
            }
            .padding(16)
        }
        .frame(height: 440)
        .background(Color.primeCanvas)
    }
}
```

## 4. X-Ray Cast Overlay

```swift
struct PrimeXRayOverlay: View {
    let cast: [CastMember]   // headshot + name + role
    let nowPlaying: String?
    @Binding var shown: Bool

    var body: some View {
        VStack {
            Spacer()
            if shown {
                VStack(alignment: .leading, spacing: 12) {
                    HStack {
                        Text("IN THIS SCENE").font(.primeLabelUpper)
                            .foregroundStyle(.primeTextSecondary).tracking(0.6)
                        Spacer()
                        Button { withAnimation(.easeOut(duration: 0.28)) { shown = false } } label: {
                            Image(systemName: "chevron.down").foregroundStyle(.primeTextSecondary)
                        }
                    }
                    ScrollView(.horizontal, showsIndicators: false) {
                        HStack(spacing: 16) {
                            ForEach(cast) { m in
                                VStack(spacing: 6) {
                                    m.headshot.resizable().frame(width: 56, height: 56).clipShape(Circle())
                                    Text(m.name).font(.primeTileSubtitle).foregroundStyle(.white)
                                    Text(m.role).font(.primeTileSubtitle).foregroundStyle(.primeTextSecondary)
                                }
                                .frame(width: 80)
                            }
                        }
                    }
                    if let nowPlaying {
                        HStack(spacing: 8) {
                            Image(systemName: "music.note").foregroundStyle(.primeBlue)
                            Text("Now playing: \(nowPlaying)").font(.primeMeta).foregroundStyle(.white)
                        }
                    }
                }
                .padding(20)
                .frame(maxWidth: .infinity, alignment: .leading)
                .background(.ultraThinMaterial)
                .background(Color.primeSurface2.opacity(0.96))
                .clipShape(.rect(topLeadingRadius: 16, topTrailingRadius: 16))
                .transition(.move(edge: .bottom))
            }
        }
    }
}
```

## 5. Tab Bar

Use `TabView` with a custom `UITabBarAppearance` for the 94%-opaque navy canvas + `.regularMaterial` blur. **Active tint is Prime Blue** — blue is the indicator.

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor(Color.primeCanvas).withAlphaComponent(0.94)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem      { Label("Home",      systemImage: "house.fill") }
            StoreView().tabItem     { Label("Store",     systemImage: "bag.fill") }
            LiveView().tabItem      { Label("Live",      systemImage: "dot.radiowaves.left.and.right") }
            FindView().tabItem      { Label("Find",      systemImage: "magnifyingglass") }
            DownloadsView().tabItem { Label("Downloads", systemImage: "arrow.down.circle.fill") }
        }
        .tint(.primeBlue) // active = blue, blue is the indicator
    }
}
```

## 6. Motion

Use `SwiftUI` spring defaults. Haptics via the modern `.sensoryFeedback` API (iOS 17+).

```swift
// Tile press scale-up
.scaleEffect(isPressed ? 1.04 : 1.0)
.animation(.easeOut(duration: 0.18), value: isPressed)

// Play CTA tap
.sensoryFeedback(.impact(weight: .light), trigger: didTapPlay)

// Watchlist add — bump 1.0 → 1.15 → 1.0, .sensoryFeedback(.success, trigger: added) (see §3)

// X-Ray slide-up — withAnimation(.easeOut(duration: 0.28)) { shown.toggle() }, .move(edge: .bottom)

// Row snap — use a ScrollView with .scrollTargetBehavior(.viewAligned) (iOS 17+) on a LazyHStack of tiles
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play / Resume | `play.fill` | 18-24pt |
| Pause | `pause.fill` | 24pt |
| Skip Forward 10 | `goforward.10` | 28pt |
| Skip Back 10 | `gobackward.10` | 28pt |
| Watchlist add | `plus` / `checkmark` | 16pt |
| X-Ray | `info.circle` | 16pt |
| Download | `arrow.down.to.line` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Audio / subtitles | `captions.bubble` | 22pt |
| AirPlay | `airplayvideo` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Store (tab) | `bag.fill` | 24pt |
| Live (tab) | `dot.radiowaves.left.and.right` | 24pt |
| Downloads (tab) | `arrow.down.circle.fill` | 24pt |
| See more | `chevron.right` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; row snap via `.scrollTargetBehavior` is iOS 17 — fall back to a paged `TabView` style or manual snapping on iOS 16)
- Support Dynamic Type on titles, episode titles, synopsis — pin tab labels, scrubber times, and tile subtitles (layout-sensitive in rows)
- VoiceOver: label tiles "Play The Citadel Files, 2024, 16+, included with Prime"; in the X-Ray panel, group each cast member as "Actor Name as Role"
- Contrast: `#AAB7C4` secondary on `#0F171E` passes WCAG AA at 13pt+; the bright blue (`#00A8E1`) with dark-navy (`#0F171E`) text exceeds AAA — keep that pairing; never white-on-blue
- IMDb yellow (`#F5C518`) on navy passes AA at 13pt+; it's a badge color, not a general accent
- Reduce Motion: disable the tile scale-up (static blue border instead), cross-fade the X-Ray instead of sliding, and disable row snap deceleration
