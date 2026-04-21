# Netflix (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Netflix's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the red Play button, poster rows, Top 10 numeral overlay, and the profile picker.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let netflixRed        = Color(red: 0.898, green: 0.035, blue: 0.078)  // #E50914
    static let netflixRedPressed = Color(red: 0.718, green: 0.027, blue: 0.059)  // #B7070F
    static let netflixRedDimmed  = Color(red: 0.514, green: 0.063, blue: 0.063)  // #831010

    // MARK: - Canvas & Surfaces
    static let netflixCanvas    = Color(red: 0.078, green: 0.078, blue: 0.078) // #141414
    static let netflixDeepBlack = Color.black                                   // #000000
    static let netflixSurface1  = Color(red: 0.122, green: 0.122, blue: 0.122) // #1F1F1F
    static let netflixSurface2  = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let netflixSurface3  = Color(red: 0.227, green: 0.227, blue: 0.227) // #3A3A3A
    static let netflixDivider   = Color(red: 0.169, green: 0.169, blue: 0.169) // #2B2B2B
    static let netflixInput     = Color(red: 0.2,   green: 0.2,   blue: 0.2)   // #333333

    // MARK: - Text
    static let netflixTextPrimary   = Color.white                                // #FFFFFF
    static let netflixTextSecondary = Color(red: 0.667, green: 0.667, blue: 0.667) // #AAAAAA
    static let netflixTextTertiary  = Color(red: 0.467, green: 0.467, blue: 0.467) // #777777

    // MARK: - Profile Accent Rotation
    static let netflixProfileRed    = Color(red: 0.898, green: 0.035, blue: 0.078) // #E50914
    static let netflixProfileBlue   = Color(red: 0.243, green: 0.243, blue: 0.569) // #3E3E91
    static let netflixProfileYellow = Color(red: 0.961, green: 0.847, blue: 0.361) // #F5D85C
    static let netflixProfileGreen  = Color(red: 0.294, green: 0.541, blue: 0.243) // #4B8A3E
    static let netflixKidsOrange    = Color(red: 0.973, green: 0.596, blue: 0.114) // #F8981D

    // MARK: - Semantic
    static let netflixInfo = Color(red: 0.329, green: 0.725, blue: 0.773) // #54B9C5
}
```

## 2. Typography

Netflix Sans is proprietary. Bundle via `UIAppFonts`. Fall back to SF Pro Display.

```swift
extension Font {
    // Headlines
    static let netflixTitleHero    = Font.custom("NetflixSans-Bold",   size: 32).weight(.bold)
    static let netflixScreenTitle  = Font.custom("NetflixSans-Bold",   size: 20).weight(.bold)
    static let netflixRowHeader    = Font.custom("NetflixSans-Bold",   size: 17).weight(.bold)

    // Content
    static let netflixEpisodeTitle = Font.custom("NetflixSans-Medium", size: 17).weight(.medium)
    static let netflixBody         = Font.custom("NetflixSans-Regular",size: 14).weight(.regular)
    static let netflixMetadata     = Font.custom("NetflixSans-Regular",size: 13).weight(.regular)
    static let netflixMetadataSm   = Font.custom("NetflixSans-Regular",size: 12).weight(.regular)

    // Buttons & labels
    static let netflixButtonPlay   = Font.custom("NetflixSans-Bold",   size: 17).weight(.bold)
    static let netflixButtonSecondary = Font.custom("NetflixSans-Medium", size: 15).weight(.medium)
    static let netflixBadge        = Font.custom("NetflixSans-Bold",   size: 11).weight(.bold)
    static let netflixTabLabel     = Font.custom("NetflixSans-Medium", size: 10).weight(.medium)
    static let netflixCert         = Font.custom("NetflixSans-Bold",   size: 12).weight(.bold)

    static func netflix(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Primary Play Button

```swift
struct NetflixPlayButton: View {
    let title: String   // "Play" or "Resume"
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill")
                    .font(.system(size: 20, weight: .bold))
                    .foregroundStyle(.white)
                Text(title)
                    .font(.netflixButtonPlay)
                    .foregroundStyle(.white)
            }
            .frame(maxWidth: .infinity)
            .padding(.vertical, 14)
            .background(RoundedRectangle(cornerRadius: 4).fill(Color.netflixRed))
        }
        .sensoryFeedback(.impact(flexibility: .rigid), trigger: title)
        .buttonStyle(NetflixPressableStyle())
    }
}

struct NetflixPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .brightness(configuration.isPressed ? -0.1 : 0)
            .animation(.spring(response: 0.2, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### Secondary Button (My List / Info / Trailer)

```swift
struct NetflixSecondaryButton: View {
    let systemIcon: String
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: systemIcon)
                    .font(.system(size: 16))
                    .foregroundStyle(.white)
                Text(title)
                    .font(.netflixButtonSecondary)
                    .foregroundStyle(.white)
            }
            .frame(maxWidth: .infinity)
            .padding(.vertical, 12)
            .background(
                RoundedRectangle(cornerRadius: 4)
                    .fill(Color.white.opacity(0.15))
            )
        }
        .buttonStyle(NetflixPressableStyle())
    }
}
```

### Poster Tile (2:3 portrait)

```swift
struct PosterTile: View {
    let imageURL: URL
    let width: CGFloat
    var progress: Double? = nil   // 0.0–1.0 — pass for Continue Watching
    let onTap: () -> Void

    var body: some View {
        Button(action: onTap) {
            ZStack(alignment: .bottom) {
                AsyncImage(url: imageURL) { img in
                    img.resizable().scaledToFill()
                } placeholder: {
                    Rectangle().fill(Color.netflixSurface1)
                }
                .frame(width: width, height: width * 1.5)
                .clipShape(RoundedRectangle(cornerRadius: 4))

                if let progress {
                    GeometryReader { geo in
                        ZStack(alignment: .leading) {
                            Rectangle().fill(Color.white.opacity(0.3)).frame(height: 2)
                            Rectangle().fill(Color.netflixRed)
                                .frame(width: geo.size.width * progress, height: 2)
                        }
                    }
                    .frame(height: 2)
                    .padding(.bottom, 4)
                    .padding(.horizontal, 4)
                }
            }
        }
        .buttonStyle(.plain)
    }
}
```

### Horizontal Row

```swift
struct PosterRow: View {
    let title: String
    let posters: [URL]
    let tileWidth: CGFloat = 130

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(title)
                .font(.netflixRowHeader)
                .foregroundStyle(.white)
                .padding(.horizontal, 16)

            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 8) {
                    ForEach(posters, id: \.self) { url in
                        PosterTile(imageURL: url, width: tileWidth) {}
                    }
                }
                .padding(.horizontal, 16)
            }
        }
    }
}
```

### Top 10 Row (with Giant Numerals)

```swift
struct Top10Row: View {
    let posters: [URL]   // exactly 10

    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("Top 10 TV Shows in the U.S. Today")
                .font(.netflixRowHeader)
                .foregroundStyle(.white)
                .padding(.horizontal, 16)

            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 0) {
                    ForEach(Array(posters.prefix(10).enumerated()), id: \.offset) { idx, url in
                        Top10Item(index: idx + 1, posterURL: url)
                    }
                }
                .padding(.horizontal, 16)
            }
        }
    }
}

struct Top10Item: View {
    let index: Int
    let posterURL: URL
    let tileWidth: CGFloat = 120

    var body: some View {
        HStack(alignment: .bottom, spacing: -24) {
            // Giant outlined numeral on the left, partially covered by poster
            Text("\(index)")
                .font(.custom("NetflixSans-Black", size: 160).weight(.black))
                .foregroundStyle(Color.netflixCanvas)
                .overlay(
                    Text("\(index)")
                        .font(.custom("NetflixSans-Black", size: 160).weight(.black))
                        .foregroundStyle(
                            LinearGradient(
                                colors: [Color.white.opacity(0.1), Color.clear],
                                startPoint: .top, endPoint: .bottom
                            )
                        )
                )
                .shadow(color: .white.opacity(0.2), radius: 0, x: 0, y: 0) // subtle inset-like stroke

            PosterTile(imageURL: posterURL, width: tileWidth) {}
        }
        .padding(.trailing, 8)
    }
}
```

**Note**: True "outlined" type requires stroking — easiest path is to use a font weight with built-in contrast (Netflix Sans Black) colored `#141414` and rely on the poster lifting in front. For a true outline, use TextKit or Core Text with `.stroke` attribute.

### Hero Card (Trailer Auto-Play)

```swift
import AVKit

struct HeroCard: View {
    let keyArtURL: URL
    let trailerURL: URL?
    let titleLogoURL: URL
    let genres: [String]
    let onPlay: () -> Void
    let onMyList: () -> Void
    let onInfo: () -> Void

    @State private var showTrailer = false
    @State private var player: AVPlayer?

    var body: some View {
        ZStack(alignment: .bottom) {
            Color.netflixCanvas

            if showTrailer, let player {
                VideoPlayer(player: player)
                    .disabled(true)
                    .aspectRatio(16/9, contentMode: .fill)
                    .clipped()
            } else {
                AsyncImage(url: keyArtURL) { img in
                    img.resizable().scaledToFill()
                } placeholder: {
                    Color.netflixDeepBlack
                }
                .aspectRatio(16/9, contentMode: .fill)
                .clipped()
            }

            LinearGradient(
                colors: [Color.clear, Color.netflixCanvas],
                startPoint: .top, endPoint: .bottom
            )
            .frame(height: 200)

            VStack(spacing: 12) {
                AsyncImage(url: titleLogoURL) { img in
                    img.resizable().scaledToFit()
                } placeholder: { Color.clear }
                    .frame(maxWidth: .infinity)
                    .frame(height: 80)

                HStack(spacing: 8) {
                    ForEach(Array(genres.enumerated()), id: \.offset) { idx, g in
                        Text(g)
                            .font(.system(size: 13, weight: .medium))
                            .foregroundStyle(.white)
                        if idx < genres.count - 1 {
                            Text("•")
                                .font(.system(size: 13, weight: .medium))
                                .foregroundStyle(Color.netflixTextSecondary)
                        }
                    }
                }

                HStack(spacing: 12) {
                    NetflixSecondaryButton(systemIcon: "plus", title: "My List", action: onMyList)
                    NetflixPlayButton(title: "Play", action: onPlay)
                    NetflixSecondaryButton(systemIcon: "info.circle", title: "Info", action: onInfo)
                }
                .padding(.horizontal, 16)
            }
            .padding(.bottom, 16)
        }
        .frame(height: 440)
        .onAppear {
            // 2s delay before auto-playing trailer
            if let url = trailerURL {
                player = AVPlayer(url: url)
                player?.isMuted = true
                DispatchQueue.main.asyncAfter(deadline: .now() + 2) {
                    withAnimation(.easeInOut(duration: 0.6)) { showTrailer = true }
                    player?.play()
                }
            }
        }
    }
}
```

### Profile Picker

```swift
struct ProfilePicker: View {
    let profiles: [Profile]
    let onSelect: (Profile) -> Void

    struct Profile: Identifiable {
        let id = UUID()
        let name: String
        let avatarColor: Color
        let isKids: Bool
    }

    private let columns = Array(repeating: GridItem(.flexible(), spacing: 24), count: 2)

    var body: some View {
        ZStack {
            Color.netflixCanvas.ignoresSafeArea()
            VStack(spacing: 48) {
                Text("Who's watching?")
                    .font(.netflixTitleHero)
                    .foregroundStyle(.white)

                LazyVGrid(columns: columns, spacing: 24) {
                    ForEach(profiles) { profile in
                        Button {
                            withAnimation(.spring(response: 0.35, dampingFraction: 0.7)) {
                                onSelect(profile)
                            }
                        } label: {
                            VStack(spacing: 12) {
                                Circle()
                                    .fill(profile.avatarColor)
                                    .frame(width: 80, height: 80)
                                    .overlay(
                                        Image(systemName: "person.fill")
                                            .font(.system(size: 40))
                                            .foregroundStyle(.white)
                                    )
                                Text(profile.name)
                                    .font(.system(size: 14, weight: .medium))
                                    .foregroundStyle(.white)
                            }
                        }
                        .buttonStyle(.plain)
                    }
                }

                Button("Manage Profiles") {}
                    .font(.system(size: 15, weight: .medium))
                    .foregroundStyle(Color.netflixTextSecondary)
            }
        }
    }
}
```

## 4. NETFLIX Wordmark Header

```swift
struct NetflixHeader: View {
    let categoryChips: [String]   // e.g. ["TV Shows", "Movies", "My List"]
    @State private var selectedChip: String? = nil

    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            // The red NETFLIX wordmark image (your vector asset)
            Image("NetflixWordmark")
                .resizable()
                .scaledToFit()
                .frame(height: 22)
                .padding(.horizontal, 16)

            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 8) {
                    ForEach(categoryChips, id: \.self) { chip in
                        Button {
                            selectedChip = selectedChip == chip ? nil : chip
                        } label: {
                            Text(chip)
                                .font(.system(size: 14, weight: .medium))
                                .foregroundStyle(.white)
                                .padding(.horizontal, 16)
                                .padding(.vertical, 6)
                                .overlay(
                                    Capsule().stroke(Color.white.opacity(0.4), lineWidth: 1)
                                )
                        }
                    }
                }
                .padding(.horizontal, 16)
            }
        }
        .padding(.vertical, 12)
    }
}
```

## 5. Tab Bar

```swift
struct NetflixTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor(Color.netflixCanvas).withAlphaComponent(0.92)
        appearance.shadowColor = UIColor(Color.netflixDivider)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }

    var body: some View {
        TabView {
            HomeView()
                .tabItem { Label("Home", systemImage: "house.fill") }
            NewHotView()
                .tabItem { Label("New & Hot", systemImage: "play.rectangle.on.rectangle.fill") }
            MyNetflixView()
                .tabItem { Label("My Netflix", systemImage: "person.crop.circle.fill") }
            DownloadsView()
                .tabItem { Label("Downloads", systemImage: "arrow.down.circle.fill") }
        }
        .tint(.white)
    }
}
```

## 6. Motion

```swift
// Poster → Title detail shared element
@Namespace private var posterNS

// In grid:
PosterTile(imageURL: url, width: 130) { /* select */ }
    .matchedGeometryEffect(id: url, in: posterNS)

// In detail:
AsyncImage(url: heroImageURL) { ... }
    .matchedGeometryEffect(id: url, in: posterNS)
// Wrap in withAnimation(.spring(response: 0.35, dampingFraction: 0.85))

// My List add/remove
@State private var added = false
Image(systemName: added ? "checkmark" : "plus")
    .rotationEffect(.degrees(added ? 90 : 0))
    .animation(.spring(response: 0.2, dampingFraction: 0.7), value: added)
    .sensoryFeedback(.success, trigger: added)

// Rating double-thumbs "Love this!"
// Use a ZStack overlay with a large red heart-thumb icon, scale 0 → 1.2 → 1.0 + fade, heavy haptic
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play | `play.fill` | 20pt (button) / 28pt (player) |
| Pause | `pause.fill` | 28pt |
| Plus (My List) | `plus` | 16pt (secondary) / 24pt (icon-only) |
| Checkmark (Added) | `checkmark` | 24pt |
| Info | `info.circle` | 16pt |
| Trailer | `play.rectangle` | 16pt |
| Download | `arrow.down.to.line` | 24pt |
| Downloaded | `checkmark.circle.fill` | 24pt |
| Share | `square.and.arrow.up` | 22pt |
| Thumb up | `hand.thumbsup.fill` | 24pt |
| Thumb down | `hand.thumbsdown.fill` | 24pt |
| Double-thumb (Love) | `heart.fill` (custom composite) | 32pt |
| Search | `magnifyingglass` | 18pt |
| Home | `house` / `house.fill` | 24pt |
| New & Hot | `play.rectangle.on.rectangle` / `.fill` | 24pt |
| My Netflix | `person.crop.circle` / `.fill` | 24pt |
| Downloads | `arrow.down.circle` / `.fill` | 24pt |
| Back | `chevron.left` | 24pt |
| More | `ellipsis` | 20pt |
| Close (modal) | `xmark` | 24pt |
| Speaker (audio toggle) | `speaker.slash.fill` / `speaker.wave.2.fill` | 20pt |
| Fullscreen | `arrow.up.left.and.arrow.down.right` | 20pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Dynamic Type: enabled on synopsis, episode titles, settings; fixed on row headers, Play button label, tab labels, badges, and poster row tile titles
- VoiceOver: group poster into one element, label with title + "Play {title}" as primary action, "Add to My List" / "Info" as secondary actions
- Color contrast: `#AAAAAA` on `#141414` meets WCAG AA for 13pt+; validate at 11pt badge sizes
- Reduce Motion: disable the 2s-delay trailer auto-play, disable the hero fade-in, substitute shared-element transitions with cross-fade
- Auto-play: always start muted; provide a persistent speaker toggle in the upper-right of the hero
- Downloads: expose the download state clearly to assistive tech ("Downloaded", "Downloading 45%")
- Kids profile: switch the color palette to brighter saturated tones (kids-orange `#F8981D`) and larger touch targets (+4pt on all buttons)
