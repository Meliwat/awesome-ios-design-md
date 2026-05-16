# TIDAL (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates TIDAL's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the signature quality badge, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let tidalCanvas   = Color.black                                  // #000000
    static let tidalSurface1 = Color(red: 0.039, green: 0.039, blue: 0.039) // #0A0A0A
    static let tidalSurface2 = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let tidalDivider  = Color(red: 0.149, green: 0.149, blue: 0.149) // #262626

    // MARK: - Text
    static let tidalTextPrimary   = Color.white                              // #FFFFFF
    static let tidalTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A
    static let tidalTextTertiary  = Color(red: 0.361, green: 0.361, blue: 0.361) // #5C5C5C

    // MARK: - Brand
    static let tidalWhite        = Color.white                              // #FFFFFF (action)
    static let tidalCyan         = Color(red: 0.0, green: 1.0, blue: 1.0)   // #00FFFF (quality only)
    static let tidalCyanPressed  = Color(red: 0.0, green: 0.8, blue: 0.8)   // #00CCCC
    static let tidalErrorRed     = Color(red: 1.0, green: 0.271, blue: 0.227) // #FF453A
}
```

## 2. Typography

TIDAL's brand sans is proprietary. Bundle it via `Info.plist` (`UIAppFonts`) or use Space Grotesk as the closest free geometric grotesque; final fallback is `.system(design: .default)`.

```swift
extension Font {
    static let tidalTitleLarge  = Font.custom("SpaceGrotesk-Bold", size: 28).weight(.bold)
    static let tidalNowPlaying  = Font.custom("SpaceGrotesk-Bold", size: 24).weight(.bold)
    static let tidalSection     = Font.custom("SpaceGrotesk-Bold", size: 22).weight(.bold)
    static let tidalAlbumTitle  = Font.custom("SpaceGrotesk-Bold", size: 20).weight(.bold)

    static let tidalTrackTitle  = Font.custom("SpaceGrotesk-SemiBold", size: 16).weight(.semibold)
    static let tidalCardTitle   = Font.custom("SpaceGrotesk-SemiBold", size: 15).weight(.semibold)
    static let tidalSubtitle    = Font.custom("SpaceGrotesk-Regular",  size: 14).weight(.regular)
    static let tidalBody        = Font.custom("SpaceGrotesk-Regular",  size: 15).weight(.regular)
    static let tidalMeta        = Font.custom("SpaceGrotesk-Regular",  size: 12).weight(.regular)
    static let tidalBadge       = Font.custom("SpaceGrotesk-Bold",     size: 10).weight(.bold)
    static let tidalLabelUpper  = Font.custom("SpaceGrotesk-Bold",     size: 11).weight(.bold)
    static let tidalButton      = Font.custom("SpaceGrotesk-Bold",     size: 15).weight(.bold)
    static let tidalTab         = Font.custom("SpaceGrotesk-SemiBold", size: 10).weight(.semibold)
    static let tidalTimestamp   = Font.custom("SpaceGrotesk-SemiBold", size: 11).weight(.semibold)
}

extension Font {
    static func tidal(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

Apply `.monospacedDigit()` to durations and track numbers.

## 3. Signature Components

### Quality-Tier Badge

```swift
enum TidalQuality: String {
    case master = "MASTER"
    case hifi   = "HIFI"
    case max    = "MAX"
    case atmos  = "DOLBY ATMOS"
}

struct QualityBadge: View {
    let quality: TidalQuality

    var body: some View {
        Text(quality.rawValue)
            .font(.tidalBadge)
            .tracking(1.0)
            .foregroundStyle(Color.tidalCyan)
            .padding(.vertical, 4)
            .padding(.horizontal, 8)
            .overlay(
                RoundedRectangle(cornerRadius: 4)
                    .strokeBorder(Color.tidalCyan, lineWidth: 1)
            )
            .accessibilityLabel("\(quality.rawValue) quality")
    }
}
```

### Primary Play Button (flat — no shadow)

```swift
struct TidalPlayButton: View {
    let isPlaying: Bool
    var size: CGFloat = 64
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                .font(.system(size: size * 0.4, weight: .bold))
                .foregroundStyle(.black) // black on white
                .frame(width: size, height: size)
                .background(Circle().fill(Color.tidalWhite))
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: isPlaying)
        .buttonStyle(TidalPressable(pressedScale: 0.93))
    }
}

struct TidalPressable: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.75), value: configuration.isPressed)
    }
}
```

### Primary / Outline Pill

```swift
struct TidalPill: View {
    let title: String
    var style: Style = .filled
    let action: () -> Void
    enum Style { case filled, outline }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(style == .filled ? .tidalButton : .tidalSubtitle.weight(.semibold))
                .foregroundStyle(style == .filled ? .black : .white)
                .padding(.vertical, 11)
                .padding(.horizontal, style == .filled ? 32 : 24)
                .background(Capsule().fill(style == .filled ? Color.tidalWhite : .clear))
                .overlay(Capsule().strokeBorder(style == .outline ? Color.tidalTextSecondary : .clear, lineWidth: 1))
        }
        .buttonStyle(TidalPressable())
    }
}
```

### Track Row (with quality badge + equalizer)

```swift
struct TidalTrackRow: View {
    let title: String
    let artist: String
    let artwork: Image
    let duration: String
    let quality: TidalQuality?
    let isPlaying: Bool

    var body: some View {
        HStack(spacing: 12) {
            ZStack(alignment: .leading) {
                artwork
                    .resizable().aspectRatio(1, contentMode: .fill)
                    .frame(width: 44, height: 44)
                    .clipShape(Rectangle()) // square — never rounded
                if isPlaying { Equalizer().frame(width: 16, height: 16).padding(.leading, 4) }
            }

            VStack(alignment: .leading, spacing: 2) {
                Text(title).font(.tidalTrackTitle).foregroundStyle(.white).lineLimit(1)
                Text(artist).font(.tidalSubtitle).foregroundStyle(.tidalTextSecondary).lineLimit(1)
            }

            Spacer(minLength: 8)

            if let q = quality { QualityBadge(quality: q) }
            Text(duration).font(.tidalTimestamp).foregroundStyle(.tidalTextSecondary).monospacedDigit()
            Image(systemName: "ellipsis").font(.system(size: 20)).foregroundStyle(.tidalTextSecondary)
        }
        .padding(.horizontal, 16)
        .frame(height: 60)
        .background(Color.tidalCanvas)
        .contentShape(Rectangle())
    }
}

struct Equalizer: View {
    @State private var phase: CGFloat = 0
    var body: some View {
        TimelineView(.animation) { ctx in
            HStack(spacing: 2) {
                ForEach(0..<3) { i in
                    let t = ctx.date.timeIntervalSinceReferenceDate
                    let h = 0.3 + 0.7 * abs(sin(t * 4 + Double(i)))
                    Capsule().fill(Color.white).frame(width: 3, height: 16 * h)
                }
            }
        }
    }
}
```

### Full-Bleed Now Playing

```swift
struct TidalNowPlaying: View {
    let title: String
    let artist: String
    let artwork: Image
    let quality: TidalQuality?
    @State private var isPlaying = true

    var body: some View {
        ZStack {
            // Darkened blurred backdrop — monochrome, no color extraction
            artwork
                .resizable().aspectRatio(contentMode: .fill)
                .blur(radius: 40)
                .overlay(Color.black.opacity(0.6))
                .ignoresSafeArea()

            VStack(spacing: 24) {
                Spacer()
                artwork
                    .resizable().aspectRatio(1, contentMode: .fit)
                    .frame(maxWidth: 320)
                    .clipShape(Rectangle()) // square, no shadow — flat

                VStack(spacing: 6) {
                    HStack(spacing: 10) {
                        Text(title).font(.tidalNowPlaying).foregroundStyle(.white)
                        if let q = quality { QualityBadge(quality: q) }
                    }
                    Text(artist).font(.tidalSubtitle).foregroundStyle(.tidalTextSecondary)
                }

                // Scrubber + transport (omitted for brevity)
                HStack(spacing: 28) {
                    Image(systemName: "shuffle").foregroundStyle(.tidalTextSecondary)
                    Image(systemName: "backward.end.fill").foregroundStyle(.white)
                    TidalPlayButton(isPlaying: isPlaying) { isPlaying.toggle() }
                    Image(systemName: "forward.end.fill").foregroundStyle(.white)
                    Image(systemName: "repeat").foregroundStyle(.tidalTextSecondary)
                }
                .font(.system(size: 22))
                Spacer()
            }
            .padding(.horizontal, 24)
        }
    }
}
```

## 4. Quality Tier Mapping

Map the streamed audio format to a badge so fidelity is always visible:

```swift
func badge(for format: AudioFormat) -> TidalQuality? {
    switch format {
    case .flac24:           return .master   // MQA / hi-res
    case .flac16:           return .hifi     // lossless CD
    case .flac24max:        return .max      // FLAC 24-bit up to 192kHz
    case .atmos:            return .atmos
    case .aac:              return nil       // no badge for lossy
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        a.backgroundColor = UIColor.black.withAlphaComponent(0.96)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            VideosView().tabItem { Label("Videos", systemImage: "play.rectangle.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            CollectionView().tabItem { Label("My Collection", systemImage: "square.stack.fill") }
        }
        .tint(.white) // active = white; cyan is fidelity-only
    }
}
```

## 6. Motion

```swift
// Screen / now-playing transitions: opacity crossfade only — no slide, no spring
.transition(.opacity)
.animation(.easeInOut(duration: 0.25), value: route)

// Play/pause tap
.sensoryFeedback(.impact(weight: .medium), trigger: isPlaying)

// Scrubber: track height grows 1pt → 3pt while dragging; light selection haptic tick
UISelectionFeedbackGenerator().selectionChanged()

// Favorite tap — fill + subtle bounce 1.0 → 1.12 → 1.0 over 260ms
.sensoryFeedback(.impact(weight: .light), trigger: isFavorited)

// Mini-bar → Full player: matchedGeometryEffect on the artwork, animated with .opacity (crossfade, not spring)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play | `play.fill` | 26pt |
| Pause | `pause.fill` | 26pt |
| Next / Previous | `forward.end.fill` / `backward.end.fill` | 22pt |
| Shuffle | `shuffle` | 22pt |
| Repeat | `repeat` | 22pt |
| Favorite | `heart` / `heart.fill` | 22pt |
| Add to collection | `plus.circle` | 22pt |
| Credits | `info.circle` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| More | `ellipsis` | 20pt |
| Search | `magnifyingglass` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Videos (tab) | `play.rectangle.fill` | 24pt |
| Collection (tab) | `square.stack.fill` | 24pt |
| Cast | `airplayaudio` | 22pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on track titles, artist names, body, headers — pin quality badges (10pt), timestamps (11pt), tab labels (10pt)
- VoiceOver: announce the quality badge as part of the row ("Black Skinhead, Kanye West, Master quality, 3 minutes 8 seconds")
- Quality badge contrast: `#00FFFF` on `#000000` is extremely high-contrast (AAA) — safe at 10pt
- Secondary text `#9A9A9A` on `#000000` passes WCAG AA at 14pt+; validate at 11pt and lighten toward `#A8A8A8` if targeting strict compliance
- Force dark: TIDAL is dark-only — set `.preferredColorScheme(.dark)` at the app root and never expose a light variant
