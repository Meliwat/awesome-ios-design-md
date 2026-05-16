# SoundCloud (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates SoundCloud's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the signature waveform scrubber, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Light)
    static let scCanvas      = Color(red: 1.00, green: 1.00, blue: 1.00) // #FFFFFF
    static let scSurface     = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
    static let scDivider     = Color(red: 0.898, green: 0.898, blue: 0.898) // #E5E5E5

    // MARK: - Canvas & Surfaces (Dark)
    static let scCanvasDark  = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let scSurfaceDark = Color(red: 0.149, green: 0.149, blue: 0.149) // #262626
    static let scDividerDark = Color(red: 0.20, green: 0.20, blue: 0.20)    // #333333

    // MARK: - Text
    static let scTextPrimary   = Color(red: 0.20, green: 0.20, blue: 0.20) // #333333
    static let scTextSecondary = Color(red: 0.60, green: 0.60, blue: 0.60) // #999999
    static let scTextTertiary  = Color(red: 0.749, green: 0.749, blue: 0.749) // #BFBFBF

    // MARK: - Brand
    static let scOrange        = Color(red: 1.00, green: 0.333, blue: 0.00) // #FF5500
    static let scOrangeLight   = Color(red: 1.00, green: 0.467, blue: 0.00) // #FF7700
    static let scOrangePressed = Color(red: 0.902, green: 0.290, blue: 0.00) // #E64A00
    static let scErrorRed      = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
}
```

Drive these through the environment color scheme so canvas/surface/text flip automatically:

```swift
extension Color {
    static func scCanvasAdaptive(_ scheme: ColorScheme) -> Color { scheme == .dark ? scCanvasDark : scCanvas }
    static func scSurfaceAdaptive(_ scheme: ColorScheme) -> Color { scheme == .dark ? scSurfaceDark : scSurface }
    static func scTextAdaptive(_ scheme: ColorScheme) -> Color { scheme == .dark ? .white : scTextPrimary }
}
```

## 2. Typography

Interstate is licensed (Font Bureau). Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to Inter, then `.system(design: .default)`.

```swift
extension Font {
    static let scTitleLarge  = Font.custom("Interstate-Bold", size: 28).weight(.bold)
    static let scNowPlaying  = Font.custom("Interstate-Bold", size: 22).weight(.bold)
    static let scSection     = Font.custom("Interstate-Bold", size: 22).weight(.bold)
    static let scProfileName = Font.custom("Interstate-Bold", size: 20).weight(.bold)

    static let scTrackTitle  = Font.custom("Interstate-Medium",  size: 16).weight(.medium)
    static let scCardTitle   = Font.custom("Interstate-Medium",  size: 15).weight(.medium)
    static let scSubtitle    = Font.custom("Interstate-Regular", size: 14).weight(.regular)
    static let scBody        = Font.custom("Interstate-Regular", size: 15).weight(.regular)
    static let scComment     = Font.custom("Interstate-Regular", size: 14).weight(.regular)
    static let scMeta        = Font.custom("Interstate-Regular", size: 12).weight(.regular)
    static let scLabelUpper  = Font.custom("Interstate-Bold",    size: 11).weight(.bold)
    static let scButton      = Font.custom("Interstate-Bold",    size: 15).weight(.bold)
    static let scTab         = Font.custom("Interstate-Medium",  size: 10).weight(.medium)
    static let scTimestamp   = Font.custom("Interstate-Medium",  size: 11).weight(.medium)
}

// System fallback if Interstate / Inter are unavailable
extension Font {
    static func sc(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

Apply `.monospacedDigit()` to any `Text` that renders a timestamp or play count.

## 3. Signature Components

### Commentable Waveform Scrubber

```swift
struct WaveformComment: Identifiable {
    let id = UUID()
    let position: Double      // 0...1 along the track
    let avatar: Image
    let text: String
}

struct WaveformScrubber: View {
    let samples: [CGFloat]        // 0...1 normalized amplitudes
    let progress: Double          // 0...1 current playback position
    let comments: [WaveformComment]
    let onSeek: (Double) -> Void

    @State private var revealed: WaveformComment?

    var body: some View {
        GeometryReader { geo in
            let w = geo.size.width
            let barW: CGFloat = 1.5
            let gap: CGFloat = 1.0
            let count = max(1, Int(w / (barW + gap)))

            ZStack(alignment: .topLeading) {
                // Revealed inline comment
                if let c = revealed {
                    Text(c.text)
                        .font(.scComment)
                        .foregroundStyle(.scTextPrimary)
                        .padding(.horizontal, 10).padding(.vertical, 6)
                        .background(Capsule().fill(Color.scSurface))
                        .offset(x: min(max(0, CGFloat(c.position) * w - 60), w - 120), y: -34)
                        .transition(.move(edge: .bottom).combined(with: .opacity))
                }

                // Bars
                HStack(alignment: .center, spacing: gap) {
                    ForEach(0..<count, id: \.self) { i in
                        let amp = samples.isEmpty ? 0.3 : samples[i * samples.count / count]
                        let played = Double(i) / Double(count) <= progress
                        Capsule()
                            .fill(played ? Color.scOrange : Color.scOrangeLight.opacity(0.45))
                            .frame(width: barW, height: max(3, amp * 56))
                    }
                }
                .frame(height: 64, alignment: .center)

                // Playhead
                Rectangle()
                    .fill(Color.scTextPrimary)
                    .frame(width: 1, height: 64)
                    .offset(x: CGFloat(progress) * w)

                // Inline comment avatars on the baseline
                ForEach(comments) { c in
                    c.avatar
                        .resizable()
                        .frame(width: 20, height: 20)
                        .clipShape(Circle())
                        .overlay(Circle().strokeBorder(Color.scCanvas, lineWidth: 1.5))
                        .offset(x: CGFloat(c.position) * w - 10, y: 52)
                        .onAppear {
                            // reveal as the playhead sweeps past
                        }
                }
            }
            .contentShape(Rectangle())
            .gesture(
                DragGesture(minimumDistance: 0)
                    .onChanged { v in
                        let p = min(1, max(0, v.location.x / w))
                        onSeek(p)
                        UISelectionFeedbackGenerator().selectionChanged()
                        if let hit = comments.min(by: { abs($0.position - p) < abs($1.position - p) }),
                           abs(hit.position - p) < 0.03 {
                            withAnimation(.easeOut(duration: 0.2)) { revealed = hit }
                        }
                    }
            )
        }
        .frame(height: 64)
    }
}
```

### Primary Play FAB

```swift
struct SCPlayButton: View {
    let isPlaying: Bool
    var size: CGFloat = 64
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                .font(.system(size: size * 0.4, weight: .bold))
                .foregroundStyle(.white)
                .frame(width: size, height: size)
                .background(Circle().fill(Color.scOrange))
                .shadow(color: Color.scOrange.opacity(0.32), radius: 20, y: 6)
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: isPlaying)
        .buttonStyle(SCPressable(pressedScale: 0.93))
    }
}

struct SCPressable: ButtonStyle {
    var pressedScale: CGFloat = 0.97
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Primary / Outline Pill (Follow / Following)

```swift
struct SCPill: View {
    let title: String
    var style: Style = .filled
    let action: () -> Void
    enum Style { case filled, outline }

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(style == .filled ? .scButton : .scSubtitle)
                .foregroundStyle(style == .filled ? .white : .scTextPrimary)
                .padding(.vertical, 9)
                .padding(.horizontal, style == .filled ? 24 : 20)
                .background(RoundedRectangle(cornerRadius: 4).fill(style == .filled ? Color.scOrange : .clear))
                .overlay(RoundedRectangle(cornerRadius: 4).strokeBorder(style == .outline ? Color.scTextSecondary : .clear, lineWidth: 1))
        }
        .buttonStyle(SCPressable())
    }
}
```

### Track Row (with mini-waveform)

```swift
struct SCTrackRow: View {
    let title: String
    let uploader: String
    let artwork: Image
    let samples: [CGFloat]
    let progress: Double
    let isPlaying: Bool

    var body: some View {
        HStack(spacing: 12) {
            artwork
                .resizable().aspectRatio(1, contentMode: .fill)
                .frame(width: 56, height: 56)
                .clipShape(RoundedRectangle(cornerRadius: 4))

            VStack(alignment: .leading, spacing: 2) {
                Text(uploader).font(.scSubtitle).foregroundStyle(.scTextSecondary).lineLimit(1)
                Text(title)
                    .font(.scTrackTitle)
                    .foregroundStyle(isPlaying ? Color.scOrange : .scTextPrimary)
                    .lineLimit(1)
            }

            Spacer(minLength: 8)

            WaveformScrubber(samples: samples, progress: progress, comments: [], onSeek: { _ in })
                .frame(width: 110, height: 28)
                .allowsHitTesting(false)

            Image(systemName: "ellipsis")
                .font(.system(size: 20))
                .foregroundStyle(.scTextSecondary)
        }
        .padding(.horizontal, 16)
        .frame(height: 72)
        .contentShape(Rectangle())
    }
}
```

### Now Playing Hero

```swift
struct SCNowPlaying: View {
    let title: String
    let uploader: String
    let artwork: Image
    let samples: [CGFloat]
    @Binding var progress: Double
    let comments: [WaveformComment]
    @State private var isPlaying = true

    var body: some View {
        VStack(spacing: 24) {
            Spacer()
            artwork
                .resizable().aspectRatio(1, contentMode: .fit)
                .frame(maxWidth: 300)
                .clipShape(RoundedRectangle(cornerRadius: 6))
                .shadow(color: .black.opacity(0.18), radius: 32, y: 12)

            VStack(spacing: 4) {
                Text(title).font(.scNowPlaying).foregroundStyle(.scTextPrimary)
                Text(uploader).font(.scSubtitle).foregroundStyle(.scTextSecondary)
            }

            WaveformScrubber(samples: samples, progress: progress, comments: comments) { progress = $0 }
                .padding(.horizontal, 16)

            HStack(spacing: 28) {
                Image(systemName: "shuffle").foregroundStyle(.scTextSecondary)
                Image(systemName: "backward.end.fill").foregroundStyle(.scTextPrimary)
                SCPlayButton(isPlaying: isPlaying) { isPlaying.toggle() }
                Image(systemName: "forward.end.fill").foregroundStyle(.scTextPrimary)
                Image(systemName: "repeat").foregroundStyle(.scTextSecondary)
            }
            .font(.system(size: 22))
            Spacer()
        }
        .padding(.horizontal, 24)
        .background(Color.scCanvas.ignoresSafeArea())
    }
}
```

## 4. Inline Comment Reveal

Drive comment reveal from the playback position so a comment surfaces exactly as the orange sweep reaches its avatar:

```swift
@State private var shown: Set<UUID> = []

func tick(_ progress: Double, comments: [WaveformComment]) {
    for c in comments where !shown.contains(c.id) && abs(c.position - progress) < 0.004 {
        shown.insert(c.id)
        withAnimation(.easeOut(duration: 0.2)) { /* set revealed = c */ }
        DispatchQueue.main.asyncAfter(deadline: .now() + 3) {
            withAnimation { /* clear revealed */ }
        }
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    @Environment(\.colorScheme) private var scheme
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterial)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            LibraryView().tabItem { Label("Library", systemImage: "play.square.stack") }
            UploadView().tabItem { Label("Upload", systemImage: "arrow.up.circle.fill") }
            ProfileView().tabItem { Label("You", systemImage: "person.crop.circle") }
        }
        .tint(.scOrange) // active = orange
    }
}
```

## 6. Motion

```swift
// Waveform progress sweep — advance progress linearly with the player clock (no easing):
// progress = currentTime / duration, updated on a CADisplayLink or .timer publisher.

// Play/pause tap
.sensoryFeedback(.impact(weight: .medium), trigger: isPlaying)

// Like tap — fill + bounce
.sensoryFeedback(.impact(weight: .light), trigger: isLiked)
// scaleEffect keyframes 1.0 → 1.2 → 1.0 over 280ms

// Scrub — selection haptic every ~5%
UISelectionFeedbackGenerator().selectionChanged()

// Mini-bar → Full player: matchedGeometryEffect on the artwork between the two views
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play | `play.fill` | 26pt |
| Pause | `pause.fill` | 26pt |
| Next / Previous | `forward.end.fill` / `backward.end.fill` | 22pt |
| Shuffle | `shuffle` | 22pt |
| Repeat | `repeat` | 22pt |
| Like | `heart` / `heart.fill` | 20pt |
| Repost | `arrow.2.squarepath` | 20pt |
| Comment | `bubble.right` | 20pt |
| Share | `square.and.arrow.up` | 20pt |
| More | `ellipsis` | 20pt |
| Search | `magnifyingglass` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Library (tab) | `play.square.stack` | 24pt |
| Upload (tab) | `arrow.up.circle.fill` | 28pt |
| Profile (tab) | `person.crop.circle` | 24pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UIImpactFeedbackGenerator` / `UISelectionFeedbackGenerator`)
- Support Dynamic Type on track titles, uploader names, body, and comments — pin the waveform timestamps (11pt) and tab labels (10pt)
- VoiceOver: label the waveform as an adjustable element — `accessibilityValue` = "1:24 of 3:32"; expose seek via `accessibilityAdjustableAction`
- Announce inline comments as they reveal with `.accessibilityElement` + `AccessibilityNotification.announcement`
- Contrast: `#999999` secondary on `#FFFFFF` passes WCAG AA at 14pt+; validate the 11pt timestamps and darken toward `#808080` if targeting strict compliance
- Respect `colorScheme` for canvas/surface/text — SoundCloud Orange stays `#FF5500` in both modes
