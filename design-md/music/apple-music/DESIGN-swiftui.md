# Apple Music (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Apple Music's visual language into paste-ready SwiftUI code: `Color` extensions, type styles, the red Play button, album tiles, mini-player, Now Playing, and time-synced lyrics.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let amRed         = Color(red: 0.980, green: 0.176, blue: 0.282) // #FA2D48
    static let amCoral       = Color(red: 0.988, green: 0.235, blue: 0.267) // #FC3C44
    static let amRedPressed  = Color(red: 0.831, green: 0.129, blue: 0.231) // #D4213B

    // MARK: - Canvas
    static let amCanvasLight   = Color.white                                  // #FFFFFF
    static let amCanvasDark    = Color.black                                  // #000000
    static let amSurface1Light = Color(red: 0.949, green: 0.949, blue: 0.969) // #F2F2F7
    static let amSurface1Dark  = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let amSurface2Dark  = Color(red: 0.173, green: 0.173, blue: 0.180) // #2C2C2E
    static let amSurface3Dark  = Color(red: 0.227, green: 0.227, blue: 0.235) // #3A3A3C
    static let amDividerLight  = Color(red: 0.776, green: 0.776, blue: 0.784) // #C6C6C8
    static let amDividerDark   = Color(red: 0.220, green: 0.220, blue: 0.227) // #38383A

    // MARK: - Badges
    static let amAtmosGold     = Color(red: 0.831, green: 0.659, blue: 0.341) // #D4A857
    static let amLosslessSilver = Color(red: 0.557, green: 0.557, blue: 0.576) // #8E8E93

    // MARK: - iOS system colors (prefer native when possible)
    static let amSystemBlue    = Color(red: 0.0,   green: 0.478, blue: 1.0)   // #007AFF
    static let amSystemGreen   = Color(red: 0.204, green: 0.780, blue: 0.349) // #34C759
    static let amSystemRed     = Color(red: 1.0,   green: 0.231, blue: 0.188) // #FF3B30
}
```

**Prefer iOS semantic colors when available.** For primary / secondary / tertiary labels and backgrounds, use `Color.primary`, `Color.secondary`, `Color(uiColor: .secondaryLabel)`, `Color(uiColor: .systemBackground)` — they auto-adapt to light / dark and Dynamic Type high-contrast settings.

## 2. Typography

Apple Music is pure SF Pro. Prefer `.system` fonts to get automatic optical variant switching and Dynamic Type for free.

```swift
extension Font {
    // Display (SF Pro Display — 20pt+ automatic)
    static let amLargeTitle    = Font.system(size: 34, weight: .bold, design: .default)
    static let amTitle1        = Font.system(size: 28, weight: .bold, design: .default)
    static let amHeroListen    = Font.system(size: 24, weight: .bold, design: .rounded) // SF Pro Rounded for editorial cards
    static let amTitle2        = Font.system(size: 22, weight: .bold, design: .default)
    static let amTitle3        = Font.system(size: 20, weight: .semibold, design: .default)
    static let amNowPlaying    = Font.system(size: 18, weight: .semibold, design: .default)

    // Text (SF Pro Text)
    static let amHeadline      = Font.headline
    static let amBody          = Font.body
    static let amCallout       = Font.callout
    static let amSubheadline   = Font.subheadline
    static let amFootnote      = Font.footnote
    static let amCaption1      = Font.caption
    static let amCaption2      = Font.caption2

    static let amButton        = Font.system(size: 17, weight: .semibold, design: .default)
    static let amTabLabel      = Font.system(size: 10, weight: .medium, design: .default)

    static let amLyricsCurrent = Font.system(size: 28, weight: .bold, design: .default)
    static let amLyricsOther   = Font.system(size: 22, weight: .semibold, design: .default)
}
```

## 3. Signature Components

### Primary Red Play Button

```swift
struct ApplePlayButton: View {
    let isPlaying: Bool
    var size: CGFloat = 64
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                .font(.system(size: size * 0.44, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: size, height: size)
                .background(Circle().fill(Color.amRed))
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isPlaying)
        .buttonStyle(AMPressableStyle(pressedScale: 0.92))
    }
}

struct AMPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.75), value: configuration.isPressed)
    }
}
```

### Pill Button (Play / Shuffle / Join Apple Music)

```swift
struct AMPillButton: View {
    let title: String
    let systemIcon: String
    var tint: Color = .amRed
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: systemIcon)
                    .font(.system(size: 15, weight: .bold))
                Text(title)
                    .font(.amButton)
            }
            .foregroundStyle(.white)
            .padding(.vertical, 10)
            .padding(.horizontal, 24)
            .background(RoundedRectangle(cornerRadius: 8).fill(tint))
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: title)
        .buttonStyle(AMPressableStyle())
    }
}
```

### Album Tile

```swift
struct AlbumTile: View {
    let artworkURL: URL
    let title: String
    let subtitle: String   // artist or description
    var width: CGFloat = 160
    let onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            VStack(alignment: .leading, spacing: 6) {
                AsyncImage(url: artworkURL) { img in
                    img.resizable().scaledToFill()
                } placeholder: {
                    Rectangle().fill(Color.amSurface1Light)
                }
                .frame(width: width, height: width)
                .clipShape(RoundedRectangle(cornerRadius: 12))
                .shadow(color: .black.opacity(0.08), radius: 16, y: 4)

                Text(title)
                    .font(.system(size: 15, weight: .semibold))
                    .foregroundStyle(Color.primary)
                    .lineLimit(2)
                    .frame(width: width, alignment: .leading)

                Text(subtitle)
                    .font(.system(size: 13, weight: .regular))
                    .foregroundStyle(Color.secondary)
                    .lineLimit(1)
            }
        }
        .buttonStyle(.plain)
        .sensoryFeedback(.selection, trigger: title)
    }
}
```

### Track Row

```swift
struct TrackRow: View {
    let title: String
    let artist: String
    let artwork: Image
    let isPlaying: Bool
    let hasAtmos: Bool
    let explicit: Bool

    var body: some View {
        HStack(spacing: 12) {
            ZStack {
                artwork
                    .resizable()
                    .aspectRatio(1, contentMode: .fill)
                    .frame(width: 44, height: 44)
                    .clipShape(RoundedRectangle(cornerRadius: 8))
                if isPlaying {
                    EqualizerBars(color: Color.amRed)
                        .frame(width: 18, height: 18)
                        .background(Color.black.opacity(0.5))
                        .clipShape(RoundedRectangle(cornerRadius: 4))
                }
            }

            VStack(alignment: .leading, spacing: 2) {
                HStack(spacing: 4) {
                    if explicit {
                        Text("E")
                            .font(.system(size: 11, weight: .bold))
                            .foregroundStyle(.white)
                            .padding(.horizontal, 4)
                            .background(RoundedRectangle(cornerRadius: 3).fill(Color.gray))
                    }
                    Text(title)
                        .font(.system(size: 17))
                        .foregroundStyle(Color.primary)
                        .lineLimit(1)
                    if hasAtmos {
                        AtmosBadge()
                    }
                }
                Text(artist)
                    .font(.system(size: 15))
                    .foregroundStyle(Color.secondary)
                    .lineLimit(1)
            }

            Spacer()

            Button {} label: {
                Image(systemName: "ellipsis")
                    .font(.system(size: 20))
                    .foregroundStyle(Color.secondary)
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 56)
        .contentShape(Rectangle())
    }
}

struct AtmosBadge: View {
    var body: some View {
        Text("Dolby Atmos")
            .font(.system(size: 11, weight: .semibold))
            .foregroundStyle(.white)
            .padding(.vertical, 4)
            .padding(.horizontal, 8)
            .background(
                Capsule().fill(
                    LinearGradient(colors: [Color.amAtmosGold, Color.amAtmosGold.opacity(0.85)],
                                   startPoint: .top, endPoint: .bottom)
                )
            )
    }
}

struct EqualizerBars: View {
    let color: Color
    @State private var phase: CGFloat = 0
    let bars: [CGFloat] = [0.3, 0.6, 0.4, 0.8]

    var body: some View {
        HStack(spacing: 2) {
            ForEach(0..<3, id: \.self) { i in
                Capsule()
                    .fill(color)
                    .frame(width: 3)
                    .frame(height: 14 * (0.4 + 0.6 * abs(sin(phase + CGFloat(i) * 0.7))))
            }
        }
        .onAppear {
            withAnimation(.linear(duration: 0.6).repeatForever(autoreverses: true)) {
                phase = .pi
            }
        }
    }
}
```

### Now Playing Hero (with Extracted Gradient)

```swift
struct NowPlayingScreen: View {
    let trackTitle: String
    let artist: String
    let artwork: Image
    let dominantColor: Color
    let complementaryColor: Color
    @Binding var isPlaying: Bool
    @Binding var progress: Double   // 0.0–1.0

    var body: some View {
        ZStack {
            LinearGradient(
                colors: [dominantColor, complementaryColor],
                startPoint: .top,
                endPoint: .bottom
            )
            .ignoresSafeArea()

            VStack(spacing: 24) {
                Spacer()
                artwork
                    .resizable()
                    .aspectRatio(1, contentMode: .fit)
                    .frame(maxWidth: 340)
                    .clipShape(RoundedRectangle(cornerRadius: 16))
                    .shadow(color: .black.opacity(0.3), radius: 40, y: 20)

                VStack(spacing: 4) {
                    Text(trackTitle)
                        .font(.amNowPlaying)
                        .foregroundStyle(.white)
                        .lineLimit(1)
                    Text(artist)
                        .font(.system(size: 15))
                        .foregroundStyle(.white.opacity(0.7))
                        .lineLimit(1)
                }

                AMScrubber(progress: $progress)

                HStack(spacing: 32) {
                    Image(systemName: "backward.fill").font(.system(size: 32)).foregroundStyle(.white)
                    ApplePlayButton(isPlaying: isPlaying, size: 64) { isPlaying.toggle() }
                    Image(systemName: "forward.fill").font(.system(size: 32)).foregroundStyle(.white)
                }

                Spacer()
            }
            .padding(.horizontal, 24)
        }
    }
}

struct AMScrubber: View {
    @Binding var progress: Double

    var body: some View {
        GeometryReader { geo in
            ZStack(alignment: .leading) {
                Capsule().fill(Color.white.opacity(0.2)).frame(height: 4)
                Capsule().fill(Color.amRed).frame(width: geo.size.width * progress, height: 4)
                Circle()
                    .fill(.white)
                    .shadow(color: .black.opacity(0.2), radius: 2, y: 1)
                    .frame(width: 16, height: 16)
                    .offset(x: geo.size.width * progress - 8)
            }
        }
        .frame(height: 16)
    }
}
```

### Mini-Player

```swift
struct MiniPlayer: View {
    let trackTitle: String
    let artist: String
    let artwork: Image
    @Binding var isPlaying: Bool
    let onExpand: () -> Void

    var body: some View {
        HStack(spacing: 12) {
            artwork
                .resizable()
                .aspectRatio(1, contentMode: .fill)
                .frame(width: 44, height: 44)
                .clipShape(RoundedRectangle(cornerRadius: 8))

            VStack(alignment: .leading, spacing: 2) {
                Text(trackTitle)
                    .font(.system(size: 15, weight: .medium))
                    .foregroundStyle(Color.primary)
                    .lineLimit(1)
                Text(artist)
                    .font(.system(size: 13))
                    .foregroundStyle(Color.secondary)
                    .lineLimit(1)
            }

            Spacer()

            Button { isPlaying.toggle() } label: {
                Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                    .font(.system(size: 22))
                    .foregroundStyle(Color.primary)
            }

            Button {} label: {
                Image(systemName: "forward.fill")
                    .font(.system(size: 22))
                    .foregroundStyle(Color.primary)
            }
            .padding(.trailing, 12)
        }
        .padding(.leading, 12)
        .frame(height: 64)
        .background(.regularMaterial)
        .onTapGesture { onExpand() }
    }
}
```

### Time-Synced Lyrics View

```swift
struct LyricsView: View {
    struct Line: Identifiable {
        let id = UUID()
        let start: TimeInterval
        let end: TimeInterval
        let text: String
    }

    let lines: [Line]
    let currentTime: TimeInterval

    var body: some View {
        ScrollViewReader { proxy in
            ScrollView {
                LazyVStack(alignment: .leading, spacing: 24) {
                    ForEach(lines) { line in
                        Text(line.text)
                            .font(isCurrent(line) ? .amLyricsCurrent : .amLyricsOther)
                            .foregroundStyle(.white.opacity(opacity(for: line)))
                            .id(line.id)
                            .animation(.spring(response: 0.3, dampingFraction: 0.85), value: isCurrent(line))
                    }
                }
                .padding(.horizontal, 24)
                .padding(.vertical, 80)
            }
            .onChange(of: currentTime) { _ in
                if let current = lines.first(where: { isCurrent($0) }) {
                    withAnimation(.spring(response: 0.4, dampingFraction: 0.85)) {
                        proxy.scrollTo(current.id, anchor: .center)
                    }
                }
            }
        }
    }

    private func isCurrent(_ line: Line) -> Bool {
        currentTime >= line.start && currentTime < line.end
    }

    private func opacity(for line: Line) -> Double {
        if isCurrent(line) { return 1 }
        if currentTime < line.start { return 0.6 }   // upcoming
        return 0.3                                    // past
    }
}
```

## 4. Dynamic Album Color Extraction

```swift
import UIKit
import CoreImage

enum AMColorExtractor {
    /// Returns (dominant, complementary) colors derived from an album image
    static func extractColors(from image: UIImage) -> (Color, Color) {
        guard let ciImage = CIImage(image: image) else { return (.black, .black) }
        let filter = CIFilter(name: "CIAreaAverage")!
        filter.setValue(ciImage, forKey: kCIInputImageKey)
        filter.setValue(CIVector(x: 0, y: 0, z: ciImage.extent.width, w: ciImage.extent.height),
                        forKey: kCIInputExtentKey)
        guard let output = filter.outputImage else { return (.black, .black) }

        var bitmap = [UInt8](repeating: 0, count: 4)
        let ctx = CIContext(options: [.workingColorSpace: kCFNull as Any])
        ctx.render(output, toBitmap: &bitmap, rowBytes: 4,
                   bounds: CGRect(x: 0, y: 0, width: 1, height: 1),
                   format: .RGBA8, colorSpace: nil)

        let r = Double(bitmap[0]) / 255
        let g = Double(bitmap[1]) / 255
        let b = Double(bitmap[2]) / 255

        // Dominant = average; complementary = desaturated + darker variant
        let dominant = Color(.sRGB, red: r, green: g, blue: b)
        let complementary = Color(.sRGB, red: r * 0.4, green: g * 0.4, blue: b * 0.4)
        return (dominant, complementary)
    }
}
```

## 5. Tab Bar

```swift
struct AppleMusicTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithDefaultBackground() // Uses .regularMaterial
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            ListenNowView()
                .tabItem { Label("Home", systemImage: "house.fill") }
            BrowseView()
                .tabItem { Label("New", systemImage: "music.note.list") }
            RadioView()
                .tabItem { Label("Radio", systemImage: "dot.radiowaves.left.and.right") }
            LibraryView()
                .tabItem { Label("Library", systemImage: "square.stack.fill") }
            SearchView()
                .tabItem { Label("Search", systemImage: "magnifyingglass") }
        }
        .tint(.amRed)
    }
}
```

## 6. Motion

```swift
// Play button — handled by .sensoryFeedback(.impact(.soft))

// Heart like
@State private var liked = false
Button { liked.toggle() } label: {
    Image(systemName: liked ? "heart.fill" : "heart")
        .foregroundStyle(liked ? Color.amRed : Color.secondary)
}
.sensoryFeedback(.success, trigger: liked)
.scaleEffect(liked ? 1.0 : 1.0)
.animation(.spring(response: 0.25, dampingFraction: 0.6), value: liked)

// Shuffle toggle
Image(systemName: "shuffle")
    .foregroundStyle(shuffleOn ? Color.amRed : Color.secondary)
    .sensoryFeedback(.selection, trigger: shuffleOn)

// Mini-player → Now Playing shared-element
@Namespace private var playerNS
// Apply .matchedGeometryEffect(id: "artwork", in: playerNS) to both the mini artwork and the NowPlayingScreen artwork
// Wrap presentation change in withAnimation(.spring(response: 0.35, dampingFraction: 0.8))
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play | `play.fill` | 22pt (mini) / 28pt (inline) / 28pt inside 64pt button |
| Pause | `pause.fill` | same |
| Forward / Previous | `forward.fill` / `backward.fill` | 22pt (mini) / 32pt (Now Playing) |
| Shuffle | `shuffle` | 22pt |
| Repeat | `repeat` / `repeat.1` | 22pt |
| Heart | `heart` / `heart.fill` | 22pt |
| Download | `arrow.down.circle` / `arrow.down.circle.fill` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| More | `ellipsis` | 20pt |
| Search | `magnifyingglass` | 17pt |
| Home (Listen Now) | `house` / `house.fill` | 25pt |
| New (Browse) | `music.note.list` | 25pt |
| Radio | `dot.radiowaves.left.and.right` | 25pt |
| Library | `square.stack` / `square.stack.fill` | 25pt |
| Back | `chevron.left` | 22pt |
| Chevron | `chevron.right` | 15pt |
| Lyrics | `quote.bubble` | 22pt |
| AirPlay | `airplayaudio` | 22pt |
| Queue | `list.bullet` | 22pt |
| Volume low / high | `speaker.wave.1` / `speaker.wave.3` | 18pt |
| Explicit | custom 'E' in rounded rect | — |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (prefer iOS 17 for `.sensoryFeedback`, otherwise use `UIImpactFeedbackGenerator` / `UINotificationFeedbackGenerator`)
- **Dynamic Type: full support from XS to AX5** — Apple Music is a flagship Dynamic Type app. Use iOS text styles where possible (`.headline`, `.body`, `.subheadline`, `.footnote`, `.caption`) so scaling happens automatically.
- VoiceOver: every button needs a descriptive label; the Play button reads "Play Blinding Lights by The Weeknd"; heart reads "Add to Favorites" / "Remove from Favorites"
- Contrast: Apple Music relies heavily on `.label`, `.secondaryLabel`, etc. — these auto-adjust for Increase Contrast accessibility setting
- Motion: respect `reduceMotion` — skip the mini-player spring expansion, fall back to cross-fade; skip the lyric scale animation and show the current line plainly
- SF Symbols: use their native rendering mode (`palette` for multi-color badges, `hierarchical` for gradient tinting)
- Materials: `.regularMaterial` respects system appearance; no need to swap backgrounds between light and dark
- Audio: use `AVAudioSession.setCategory(.playback)` to continue during screen lock; show lock-screen Now Playing info via `MPNowPlayingInfoCenter`
- Album-art color extraction: cache per-track to avoid re-computing on scrub; consider `MPMediaItemArtwork.image(at:)` and Core Image
