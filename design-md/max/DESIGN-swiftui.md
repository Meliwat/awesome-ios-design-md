# Max (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Max's visual language into paste-ready SwiftUI code: `Color` extensions, the brand `LinearGradient`, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let maxCanvas    = Color(red: 0.071, green: 0.020, blue: 0.227) // #12053A
    static let maxDeepBlack = Color.black                                  // #000000
    static let maxSurface1  = Color(red: 0.106, green: 0.043, blue: 0.302) // #1B0B4D
    static let maxSurface2  = Color(red: 0.145, green: 0.059, blue: 0.369) // #250F5E
    static let maxSurface3  = Color(red: 0.212, green: 0.125, blue: 0.431) // #36206E
    static let maxDivider   = Color(red: 0.212, green: 0.125, blue: 0.431) // #36206E

    // MARK: - Text
    static let maxTextPrimary   = Color.white                                // #FFFFFF
    static let maxTextSecondary = Color(red: 0.690, green: 0.659, blue: 0.816) // #B0A8D0
    static let maxTextTertiary  = Color(red: 0.494, green: 0.463, blue: 0.651) // #7E76A6

    // MARK: - Brand
    static let maxGradientStart  = Color(red: 0.000, green: 0.275, blue: 1.000) // #0046FF
    static let maxGradientEnd    = Color(red: 0.482, green: 0.184, blue: 0.969) // #7B2FF7
    static let maxGradientSolid  = Color(red: 0.353, green: 0.169, blue: 0.878) // #5A2BE0
    static let maxGradientPressed = Color(red: 0.290, green: 0.133, blue: 0.741) // #4A22BD
    static let maxLiveGold       = Color(red: 0.949, green: 0.788, blue: 0.298) // #F2C94C
    static let maxErrorRed       = Color(red: 1.000, green: 0.361, blue: 0.478) // #FF5C7A
}

extension LinearGradient {
    /// The Max brand gradient (135°, blue → violet)
    static let maxBrand = LinearGradient(
        colors: [.maxGradientStart, .maxGradientEnd],
        startPoint: .topLeading,
        endPoint: .bottomTrailing
    )
}
```

## 2. Typography

Inter stands in for Max's clean grotesque brand face. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime; fall back to `.system` (SF Pro).

```swift
extension Font {
    static let maxBillboardTitle = Font.custom("Inter-ExtraBold",  size: 32).weight(.heavy)
    static let maxTitleLarge     = Font.custom("Inter-ExtraBold",  size: 28).weight(.heavy)
    static let maxRowHeader      = Font.custom("Inter-ExtraBold",  size: 22).weight(.heavy)
    static let maxSectionHeader  = Font.custom("Inter-ExtraBold",  size: 20).weight(.heavy)
    static let maxEpisodeTitle   = Font.custom("Inter-SemiBold",   size: 16).weight(.semibold)
    static let maxTileTitle      = Font.custom("Inter-SemiBold",   size: 15).weight(.semibold)
    static let maxBody           = Font.custom("Inter-Regular",    size: 15).weight(.regular)
    static let maxMeta           = Font.custom("Inter-Regular",    size: 13).weight(.regular)
    static let maxTileSubtitle   = Font.custom("Inter-Regular",    size: 12).weight(.regular)
    static let maxLabelUpper     = Font.custom("Inter-ExtraBold",  size: 11).weight(.heavy)
    static let maxButton         = Font.custom("Inter-ExtraBold",  size: 16).weight(.heavy)
    static let maxButtonSecondary = Font.custom("Inter-SemiBold",  size: 15).weight(.semibold)
    static let maxTab            = Font.custom("Inter-SemiBold",   size: 10).weight(.semibold)
    static let maxBadge          = Font.custom("Inter-ExtraBold",  size: 11).weight(.heavy)
}

extension Font {
    static func max(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Primary Gradient Play Button (with shimmer)

```swift
struct MaxPlayButton: View {
    var title: String = "Play"
    let action: () -> Void
    @State private var shimmer = false

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill").font(.system(size: 18, weight: .heavy))
                Text(title).font(.maxButton).tracking(0.2)
            }
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .frame(height: 52)
            .background(LinearGradient.maxBrand, in: RoundedRectangle(cornerRadius: 8))
            .overlay(
                RoundedRectangle(cornerRadius: 8)
                    .fill(LinearGradient(
                        colors: [.clear, .white.opacity(0.18), .clear],
                        startPoint: .leading, endPoint: .trailing))
                    .offset(x: shimmer ? 220 : -220)
                    .mask(RoundedRectangle(cornerRadius: 8))
            )
            .shadow(color: Color.maxGradientSolid.opacity(0.35), radius: 24, y: 8)
        }
        .sensoryFeedback(.impact(weight: .light), trigger: title)
        .buttonStyle(MaxPressableStyle(pressedScale: 0.97))
        .onAppear {
            withAnimation(.linear(duration: 4).repeatForever(autoreverses: false)) { shimmer = true }
        }
    }
}

struct MaxPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .overlay(configuration.isPressed
                ? Color.maxGradientPressed.opacity(0.55).clipShape(RoundedRectangle(cornerRadius: 8))
                : nil)
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Secondary Button (More Info)

```swift
struct MaxSecondaryButton: View {
    let title: String
    var systemImage: String? = nil
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                if let systemImage { Image(systemName: systemImage).font(.system(size: 16)) }
                Text(title).font(.maxButtonSecondary)
            }
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .frame(height: 52)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.white.opacity(0.14)))
        }
        .buttonStyle(MaxPressableStyle())
    }
}
```

### Max Originals Badge

```swift
struct MaxOriginalsBadge: View {
    var body: some View {
        Text("Max Originals")
            .font(.maxBadge)
            .tracking(0.4)
            .foregroundStyle(.white)
            .padding(.horizontal, 8)
            .padding(.vertical, 4)
            .background(
                Capsule().fill(Color.maxCanvas.opacity(0.55))
            )
            .overlay(
                Capsule().strokeBorder(LinearGradient.maxBrand, lineWidth: 1.5)
            )
    }
}
```

### Content Tile (with Originals badge)

```swift
struct MaxContentTile: View {
    let title: String
    let subtitle: String
    let artwork: Image
    var isOriginal: Bool = false
    let width: CGFloat
    var aspect: CGFloat = 16/9

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            ZStack(alignment: .topLeading) {
                artwork
                    .resizable()
                    .aspectRatio(aspect, contentMode: .fill)
                    .frame(width: width, height: width / aspect)
                    .clipShape(RoundedRectangle(cornerRadius: 6))
                if isOriginal {
                    MaxOriginalsBadge().padding(8)
                }
            }
            Text(title).font(.maxTileTitle).foregroundStyle(.white).lineLimit(1)
            Text(subtitle).font(.maxTileSubtitle).foregroundStyle(.maxTextSecondary).lineLimit(1)
        }
        .frame(width: width)
    }
}
```

### Billboard (auto-trailer hero)

```swift
struct MaxBillboard: View {
    let title: String
    let metadata: String
    let trailerPoster: Image    // muted trailer frame stand-in
    let onPlay: () -> Void

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            trailerPoster
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(height: 460)
                .clipped()
            LinearGradient(colors: [.clear, .maxCanvas], startPoint: .center, endPoint: .bottom)

            VStack(alignment: .leading, spacing: 12) {
                MaxOriginalsBadge()
                Text(title).font(.maxBillboardTitle).foregroundStyle(.white)
                Text(metadata).font(.maxMeta).foregroundStyle(.maxTextSecondary)
                HStack(spacing: 12) {
                    MaxPlayButton(action: onPlay).frame(width: 140)
                    MaxSecondaryButton(title: "More Info", systemImage: "info.circle") {}
                        .frame(width: 150)
                }
            }
            .padding(16)
        }
        .frame(height: 460)
        .background(Color.maxCanvas)
    }
}
```

## 4. Trailer Crossfade

```swift
struct MaxBillboardCarousel: View {
    let titles: [BillboardItem]
    @State private var index = 0

    var body: some View {
        ZStack {
            ForEach(Array(titles.enumerated()), id: \.offset) { i, item in
                MaxBillboard(title: item.title, metadata: item.meta,
                             trailerPoster: item.poster, onPlay: item.play)
                    .opacity(i == index ? 1 : 0)
                    .animation(.easeInOut(duration: 1.2), value: index)
            }
        }
        .task {
            while !Task.isCancelled {
                try? await Task.sleep(for: .seconds(20))
                index = (index + 1) % titles.count
            }
        }
    }
}
```

## 5. Tab Bar

Use `TabView` with a custom `UITabBarAppearance` for the 94%-opaque purple canvas + `.regularMaterial` blur. Active tint is white; a 3pt solid `#5A2BE0` underline is the indicator (the gradient can't render at this size).

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor(Color.maxCanvas).withAlphaComponent(0.94)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem   { Label("Home",    systemImage: "house.fill") }
            SearchView().tabItem { Label("Search",  systemImage: "magnifyingglass") }
            MyListView().tabItem { Label("My List", systemImage: "checkmark.rectangle.stack.fill") }
        }
        .tint(.white) // active = white; underline accent applied per-tab via overlay
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

// Add to My List
.sensoryFeedback(.success, trigger: isInMyList)

// Billboard crossfade — 1.2s opacity dissolve every ~20s (see §4)
// CTA shimmer — 4s linear repeating offset masked to the button (see §3)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play | `play.fill` | 18-24pt |
| Pause | `pause.fill` | 24pt |
| Skip Forward 10 | `goforward.10` | 28pt |
| Skip Back 10 | `gobackward.10` | 28pt |
| Add to My List | `plus` / `checkmark` | 22pt |
| Download | `arrow.down.to.line` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| More Info | `info.circle` | 16-22pt |
| Captions | `captions.bubble` | 22pt |
| AirPlay | `airplayvideo` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Search (tab) | `magnifyingglass` | 24pt |
| My List (tab) | `checkmark.rectangle.stack.fill` | 24pt |
| See All | `chevron.right` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on show titles, episode titles, synopsis — pin tab labels, scrubber times, and tile subtitles (layout-sensitive)
- VoiceOver: label tiles "Play Dune: Prophecy, Max Originals, 2024, TV-MA"; merge tile title+subtitle and expose the Originals badge as part of the label
- Contrast: `#B0A8D0` lavender-gray on `#12053A` canvas meets WCAG AA at 13pt+; validate 11pt badge labels and bump toward `#C4BDDD` for strict compliance
- White text on the blue→violet gradient passes AA across the sweep; if you tune the gradient lighter, re-verify the violet end against white
- Reduce Motion: disable the CTA shimmer and the billboard crossfade (snap-cut instead), and replace the tile scale-up with a static gradient border
