# Spotify (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Spotify's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let spotifyCanvas       = Color(red: 0.07, green: 0.07, blue: 0.07)   // #121212
    static let spotifyDeepBlack    = Color.black                                 // #000000
    static let spotifySurface1     = Color(red: 0.094, green: 0.094, blue: 0.094) // #181818
    static let spotifySurface2     = Color(red: 0.157, green: 0.157, blue: 0.157) // #282828
    static let spotifySurface3     = Color(red: 0.243, green: 0.243, blue: 0.243) // #3E3E3E
    static let spotifyDivider      = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A

    // MARK: - Text
    static let spotifyTextPrimary   = Color.white                                // #FFFFFF
    static let spotifyTextSecondary = Color(red: 0.702, green: 0.702, blue: 0.702) // #B3B3B3
    static let spotifyTextTertiary  = Color(red: 0.416, green: 0.416, blue: 0.416) // #6A6A6A

    // MARK: - Brand
    static let spotifyGreen        = Color(red: 0.114, green: 0.725, blue: 0.329) // #1DB954
    static let spotifyGreenPressed = Color(red: 0.086, green: 0.612, blue: 0.275) // #169C46
    static let spotifyLogoGreen    = Color(red: 0.118, green: 0.843, blue: 0.376) // #1ED760
    static let spotifyErrorRed     = Color(red: 0.945, green: 0.369, blue: 0.424) // #F15E6C
}
```

## 2. Typography

Spotify Mix is proprietary. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to `.system(design: .default)` since SF Pro's geometric quality is the closest free substitute.

```swift
extension Font {
    // Spotify Mix Title (use at 18pt+)
    static let spotifyTitleLarge  = Font.custom("SpotifyMix-Bold", size: 28).weight(.bold)
    static let spotifyTitle       = Font.custom("SpotifyMix-Bold", size: 22).weight(.bold)
    static let spotifyPlaylistHero = Font.custom("SpotifyMix-Bold", size: 24).weight(.bold)

    // Spotify Mix UI (use below 18pt)
    static let spotifyTrackTitle  = Font.custom("SpotifyMix-Semibold", size: 16).weight(.semibold)
    static let spotifyCardTitle   = Font.custom("SpotifyMix-Semibold", size: 15).weight(.semibold)
    static let spotifySubtitle    = Font.custom("SpotifyMix-Regular", size: 14).weight(.regular)
    static let spotifyBody        = Font.custom("SpotifyMix-Regular", size: 15).weight(.regular)
    static let spotifyMeta        = Font.custom("SpotifyMix-Regular", size: 12).weight(.regular)
    static let spotifyLabelUpper  = Font.custom("SpotifyMix-Bold", size: 11).weight(.bold)
    static let spotifyButton      = Font.custom("SpotifyMix-Bold", size: 16).weight(.bold)
    static let spotifyTab         = Font.custom("SpotifyMix-Bold", size: 11).weight(.bold)
}

// If Spotify Mix is unavailable, register a system fallback once:
extension Font {
    static func spotify(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Primary Green Play Button

```swift
struct SpotifyPlayButton: View {
    let isPlaying: Bool
    let size: CGFloat
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                .font(.system(size: size * 0.45, weight: .bold))
                .foregroundStyle(.black) // intentional: black on green
                .frame(width: size, height: size)
                .background(Circle().fill(Color.spotifyGreen))
        }
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isPlaying)
        .buttonStyle(SpotifyPressableStyle(pressedScale: 0.92))
    }
}

struct SpotifyPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Primary Pill CTA (Follow / Premium)

```swift
struct SpotifyPillButton: View {
    let title: String
    var style: Style = .filled
    let action: () -> Void

    enum Style { case filled, outline }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.spotifyButton)
                .foregroundStyle(style == .filled ? .black : .white)
                .padding(.vertical, 10)
                .padding(.horizontal, 32)
                .background(
                    Capsule().fill(style == .filled ? Color.spotifyGreen : .clear)
                )
                .overlay(
                    Capsule().strokeBorder(style == .outline ? Color.spotifyTextSecondary : .clear, lineWidth: 1)
                )
        }
        .buttonStyle(SpotifyPressableStyle())
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

    var body: some View {
        HStack(spacing: 12) {
            artwork
                .resizable()
                .aspectRatio(1, contentMode: .fill)
                .frame(width: 44, height: 44)
                .clipShape(RoundedRectangle(cornerRadius: 4))

            VStack(alignment: .leading, spacing: 2) {
                Text(title)
                    .font(.spotifyTrackTitle)
                    .foregroundStyle(isPlaying ? Color.spotifyGreen : .white)
                    .lineLimit(1)
                Text(artist)
                    .font(.spotifySubtitle)
                    .foregroundStyle(isPlaying ? Color.spotifyGreen : .spotifyTextSecondary)
                    .lineLimit(1)
            }

            Spacer()

            Button { /* menu */ } label: {
                Image(systemName: "ellipsis")
                    .font(.system(size: 20))
                    .foregroundStyle(.spotifyTextSecondary)
            }
        }
        .padding(.horizontal, 16)
        .frame(height: 56)
        .contentShape(Rectangle())
    }
}
```

### Now Playing Hero (with dynamic gradient)

```swift
struct NowPlayingScreen: View {
    let trackTitle: String
    let artist: String
    let artwork: Image
    let dominantColor: Color  // extracted upstream via Core Image

    var body: some View {
        ZStack {
            LinearGradient(
                colors: [dominantColor, .spotifyCanvas],
                startPoint: .top,
                endPoint: .bottom
            )
            .ignoresSafeArea()

            VStack(spacing: 32) {
                Spacer()
                artwork
                    .resizable()
                    .aspectRatio(1, contentMode: .fit)
                    .frame(maxWidth: 340)
                    .clipShape(RoundedRectangle(cornerRadius: 8))
                    .shadow(color: .black.opacity(0.5), radius: 32, y: 12)

                VStack(spacing: 6) {
                    Text(trackTitle)
                        .font(.spotifyTitle)
                        .foregroundStyle(.white)
                    Text(artist)
                        .font(.spotifySubtitle)
                        .foregroundStyle(.spotifyTextSecondary)
                }

                // Scrubber + controls (omitted for brevity)
                Spacer()
            }
            .padding(.horizontal, 24)
        }
    }
}
```

## 4. Dynamic Album Color Extraction

```swift
import UIKit
import CoreImage

enum AlbumColorExtractor {
    static func dominantColor(from image: UIImage) -> Color {
        guard let ciImage = CIImage(image: image) else { return .spotifyCanvas }
        let extentVector = CIVector(
            x: ciImage.extent.origin.x, y: ciImage.extent.origin.y,
            z: ciImage.extent.size.width, w: ciImage.extent.size.height
        )
        let filter = CIFilter(name: "CIAreaAverage", parameters: [
            kCIInputImageKey: ciImage,
            kCIInputExtentKey: extentVector,
        ])!
        guard let output = filter.outputImage else { return .spotifyCanvas }
        var bitmap = [UInt8](repeating: 0, count: 4)
        let context = CIContext(options: [.workingColorSpace: kCFNull as Any])
        context.render(output, toBitmap: &bitmap, rowBytes: 4,
                       bounds: CGRect(x: 0, y: 0, width: 1, height: 1),
                       format: .RGBA8, colorSpace: nil)
        return Color(.sRGB,
                     red: Double(bitmap[0]) / 255,
                     green: Double(bitmap[1]) / 255,
                     blue: Double(bitmap[2]) / 255,
                     opacity: 1)
    }
}
```

## 5. Tab Bar

Use `TabView` with a custom `UITabBarAppearance` to match Spotify's 92%-opaque canvas with `.regularMaterial` blur.

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor(Color.spotifyCanvas).withAlphaComponent(0.92)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            LibraryView().tabItem { Label("Your Library", systemImage: "books.vertical.fill") }
            PremiumView().tabItem { Label("Premium", systemImage: "sparkles") }
        }
        .tint(.white) // active = white, not green
    }
}
```

## 6. Motion

Use `SwiftUI` spring defaults. Haptics via the modern `.sensoryFeedback` API (iOS 17+).

```swift
// Play/pause tap
.sensoryFeedback(.impact(flexibility: .soft), trigger: isPlaying)

// Heart like tap
.sensoryFeedback(.success, trigger: isLiked)

// Currently playing equalizer
// Use three rectangles with animated scaleEffect y-axis values tied to a .timer publisher

// Mini-bar → Full player
// Use matchedGeometryEffect on the album artwork between the two views
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play | `play.fill` | 24-32pt |
| Pause | `pause.fill` | 24-32pt |
| Next | `forward.end.fill` | 20pt |
| Previous | `backward.end.fill` | 20pt |
| Shuffle | `shuffle` | 22pt |
| Repeat | `repeat` | 22pt |
| Heart | `heart` / `heart.fill` | 24pt |
| Download | `arrow.down.circle` / `arrow.down.circle.fill` | 24pt |
| Share | `square.and.arrow.up` | 22pt |
| More | `ellipsis` | 20pt |
| Search | `magnifyingglass` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Library (tab) | `books.vertical.fill` | 24pt |
| Device | `hifispeaker` | 22pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on track titles, artist names, section headers — fix Play button size, scrubber timestamps, tab labels
- VoiceOver: use `.accessibilityLabel` for the Play button (e.g., "Play Blinding Lights by The Weeknd")
- Contrast: Spotify's `#B3B3B3` secondary text on `#121212` canvas meets WCAG AA for 14pt+ — validate at 11pt sizes and bump contrast if needed
