# YouTube Music (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates YouTube Music's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark — the only mode)
    static let ytmCanvas      = Color(red: 0.012, green: 0.012, blue: 0.012) // #030303
    static let ytmSurface1    = Color(red: 0.122, green: 0.122, blue: 0.122) // #1F1F1F
    static let ytmSurface2    = Color(red: 0.153, green: 0.153, blue: 0.153) // #272727
    static let ytmMiniSurface = Color(red: 0.157, green: 0.157, blue: 0.157) // #282828
    static let ytmChipBg      = Color(red: 0.165, green: 0.165, blue: 0.165) // #2A2A2A
    static let ytmDivider     = Color(red: 0.188, green: 0.188, blue: 0.188) // #303030

    // MARK: - Brand
    static let ytmRed         = Color(red: 1.0,   green: 0.0,   blue: 0.0)   // #FF0000
    static let ytmRedPressed  = Color(red: 0.8,   green: 0.0,   blue: 0.0)   // #CC0000
    static let ytmActionWhite = Color.white                                    // #FFFFFF

    // MARK: - Text
    static let ytmTextPrimary   = Color.white                                  // #FFFFFF
    static let ytmTextSecondary = Color(red: 0.667, green: 0.667, blue: 0.667) // #AAAAAA
    static let ytmTextTertiary  = Color(red: 0.443, green: 0.443, blue: 0.443) // #717171

    // MARK: - Semantic
    static let ytmSuccess     = Color(red: 0.169, green: 0.651, blue: 0.251) // #2BA640
    static let ytmError       = Color(red: 1.0,   green: 0.306, blue: 0.271) // #FF4E45
    static let ytmTabBar      = Color(red: 0.039, green: 0.039, blue: 0.039) // #0A0A0A
}
```

## 2. Typography

YouTube Music uses **Roboto** across every YouTube product — never substitute. Bundle Roboto TTFs via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static func ytm(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .black:    return "Roboto-Black"
            case .bold:     return "Roboto-Bold"
            case .medium:   return "Roboto-Medium"
            default:        return "Roboto-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let ytmScreenTitle = Font.custom("Roboto-Black",   size: 32) // 900
    static let ytmNowPlaying  = Font.custom("Roboto-Bold",    size: 26)
    static let ytmSection     = Font.custom("Roboto-Bold",    size: 22)
    static let ytmCardHeader  = Font.custom("Roboto-Bold",    size: 18)
    static let ytmBody        = Font.custom("Roboto-Regular", size: 16)
    static let ytmRowTitle    = Font.custom("Roboto-Medium",  size: 15)
    static let ytmSubtitle    = Font.custom("Roboto-Regular", size: 14)
    static let ytmToggle      = Font.custom("Roboto-Bold",    size: 12)
    static let ytmChip        = Font.custom("Roboto-Medium",  size: 13)
    static let ytmTimestamp   = Font.custom("Roboto-Regular", size: 11)
    static let ytmEyebrow     = Font.custom("Roboto-Bold",    size: 10)
    static let ytmTab         = Font.custom("Roboto-Medium",  size: 10)
    static let ytmButton      = Font.custom("Roboto-Bold",    size: 15)
}
```

## 3. Signature Components

### Immersive Now Playing (art-glow backdrop + art + meta)

```swift
struct NowPlayingView: View {
    let artworkURL: String
    let track: String
    let artist: String
    @State private var progress: Double = 0.42
    @State private var scrubbing = false
    @State private var mode: PlayMode = .song

    enum PlayMode { case song, video }

    var body: some View {
        ZStack {
            // Art-derived blurred backdrop glow
            AsyncImage(url: URL(string: artworkURL)) { img in
                img.resizable().scaledToFill()
            } placeholder: { Color.ytmCanvas }
            .scaleEffect(1.4)
            .blur(radius: 40)
            .opacity(0.85)
            .overlay(
                LinearGradient(colors: [.clear, .ytmCanvas],
                               startPoint: .center, endPoint: .bottom)
            )
            .ignoresSafeArea()

            VStack(spacing: 0) {
                topBar
                AsyncImage(url: URL(string: artworkURL)) { img in
                    img.resizable().scaledToFill()
                } placeholder: { Color.ytmSurface1 }
                .frame(width: 232, height: 232)
                .clipShape(RoundedRectangle(cornerRadius: 8))
                .shadow(color: .black.opacity(0.7), radius: 30, y: 24)
                .padding(.top, 26)

                SongVideoToggle(mode: $mode).padding(.top, 22)

                VStack(alignment: .leading, spacing: 4) {
                    Text(track).font(.ytmNowPlaying).foregroundStyle(Color.ytmTextPrimary)
                    Text(artist).font(.ytmSubtitle).foregroundStyle(Color.ytmTextSecondary)
                }
                .frame(maxWidth: .infinity, alignment: .leading)
                .padding(.horizontal, 24).padding(.top, 22)

                Scrubber(progress: $progress, scrubbing: $scrubbing)
                    .padding(.horizontal, 24).padding(.top, 20)

                TransportControls().padding(.top, 14)
                Spacer()
            }
        }
    }

    private var topBar: some View {
        HStack {
            Image(systemName: "chevron.down").font(.system(size: 22))
            Spacer()
            Text("FROM YOUR LIBRARY")
                .font(.system(size: 11, weight: .medium))
                .tracking(0.6)
                .foregroundStyle(Color.ytmTextSecondary)
            Spacer()
            Image(systemName: "ellipsis").font(.system(size: 22))
        }
        .foregroundStyle(Color.ytmTextPrimary)
        .padding(.horizontal, 20).padding(.top, 6)
    }
}
```

### Song / Video Toggle (Signature)

```swift
struct SongVideoToggle: View {
    @Binding var mode: NowPlayingView.PlayMode

    var body: some View {
        HStack(spacing: 6) {
            segment("Song", active: mode == .song) { mode = .song }
            segment("Video", active: mode == .video) { mode = .video }
        }
        .padding(4)
        .background(Color.white.opacity(0.08))
        .clipShape(Capsule())
    }

    private func segment(_ label: String, active: Bool, _ tap: @escaping () -> Void) -> some View {
        Text(label)
            .font(.ytmToggle)
            .tracking(0.2)
            .foregroundStyle(active ? Color.ytmCanvas : Color.ytmTextSecondary)
            .padding(.vertical, 7).padding(.horizontal, 18)
            .background(active ? Color.ytmActionWhite : Color.clear)
            .clipShape(Capsule())
            .contentShape(Rectangle())
            .onTapGesture {
                withAnimation(.easeOut(duration: 0.2)) { tap() }
            }
            .sensoryFeedback(.impact(weight: .light), trigger: active)
    }
}
```

### Scrubber (white at rest, red while dragging)

```swift
struct Scrubber: View {
    @Binding var progress: Double
    @Binding var scrubbing: Bool

    var body: some View {
        VStack(spacing: 8) {
            GeometryReader { geo in
                let w = geo.size.width
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.white.opacity(0.22)).frame(height: 3)
                    Capsule()
                        .fill(scrubbing ? Color.ytmRed : Color.ytmActionWhite)
                        .frame(width: max(0, w * progress), height: 3)
                    Circle()
                        .fill(scrubbing ? Color.ytmRed : Color.ytmActionWhite)
                        .frame(width: scrubbing ? 16 : 12, height: scrubbing ? 16 : 12)
                        .background(
                            Circle()
                                .fill(Color.ytmRed.opacity(scrubbing ? 0.18 : 0))
                                .frame(width: 28, height: 28)
                        )
                        .offset(x: w * progress - (scrubbing ? 8 : 6))
                }
                .frame(height: 16)
                .contentShape(Rectangle())
                .gesture(
                    DragGesture(minimumDistance: 0)
                        .onChanged { v in
                            scrubbing = true
                            progress = min(1, max(0, v.location.x / w))
                        }
                        .onEnded { _ in
                            withAnimation(.easeOut(duration: 0.15)) { scrubbing = false }
                        }
                )
                .sensoryFeedback(.impact(weight: .light), trigger: scrubbing)
            }
            .frame(height: 16)

            HStack {
                Text("1:48").font(.ytmTimestamp).monospacedDigit()
                Spacer()
                Text("-2:34").font(.ytmTimestamp).monospacedDigit()
            }
            .foregroundStyle(Color.ytmTextSecondary)
        }
    }
}
```

### Transport Controls (white play button)

```swift
struct TransportControls: View {
    @State private var playing = true

    var body: some View {
        HStack {
            Image(systemName: "backward.end.fill").font(.system(size: 22))
            Spacer()
            Image(systemName: "backward.fill").font(.system(size: 26))
            Spacer()
            Button {
                playing.toggle()
            } label: {
                ZStack {
                    Circle().fill(Color.ytmActionWhite).frame(width: 64, height: 64)
                    Image(systemName: playing ? "pause.fill" : "play.fill")
                        .font(.system(size: 26))
                        .foregroundStyle(Color.ytmCanvas) // black glyph on white
                }
            }
            .buttonStyle(.plain)
            Spacer()
            Image(systemName: "forward.fill").font(.system(size: 26))
            Spacer()
            Image(systemName: "forward.end.fill").font(.system(size: 22))
        }
        .foregroundStyle(Color.ytmTextPrimary)
        .padding(.horizontal, 32)
    }
}
```

### Up-Next Queue Shelf

```swift
struct UpNextShelf: View {
    struct QueueItem: Identifiable { let id = UUID(); let title: String; let artist: String }
    let items: [QueueItem]

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack {
                Text("UP NEXT")
                    .font(.ytmEyebrow).tracking(0.6)
                    .foregroundStyle(Color.ytmTextSecondary)
                Spacer()
                Image(systemName: "list.bullet")
                    .font(.system(size: 20))
                    .foregroundStyle(Color.ytmTextSecondary)
            }
            .padding(16)

            ForEach(items) { item in
                HStack(spacing: 10) {
                    RoundedRectangle(cornerRadius: 4)
                        .fill(LinearGradient(colors: [.orange, .pink], startPoint: .topLeading, endPoint: .bottomTrailing))
                        .frame(width: 40, height: 40)
                    VStack(alignment: .leading, spacing: 2) {
                        Text(item.title).font(.ytmRowTitle).foregroundStyle(Color.ytmTextPrimary)
                        Text(item.artist).font(.ytmSubtitle).foregroundStyle(Color.ytmTextSecondary)
                    }
                    Spacer()
                    Image(systemName: "line.3.horizontal")
                        .font(.system(size: 18))
                        .foregroundStyle(Color.ytmTextTertiary)
                }
                .padding(.horizontal, 16).padding(.vertical, 8)
            }
        }
        .background(Color.ytmCanvas)
        .overlay(Rectangle().fill(Color.white.opacity(0.12)).frame(height: 0.5), alignment: .top)
    }
}
```

### Mini-Player Bar

```swift
struct MiniPlayer: View {
    var body: some View {
        VStack(spacing: 0) {
            HStack(spacing: 12) {
                RoundedRectangle(cornerRadius: 4)
                    .fill(LinearGradient(colors: [.purple, .blue], startPoint: .topLeading, endPoint: .bottomTrailing))
                    .frame(width: 40, height: 40)
                VStack(alignment: .leading, spacing: 2) {
                    Text("Midnight City").font(.system(size: 13, weight: .medium)).foregroundStyle(Color.ytmTextPrimary)
                    Text("M83").font(.system(size: 11)).foregroundStyle(Color.ytmTextSecondary)
                }
                Spacer()
                Image(systemName: "pause.fill").font(.system(size: 22)).foregroundStyle(Color.ytmTextPrimary)
                Image(systemName: "forward.fill").font(.system(size: 22)).foregroundStyle(Color.ytmTextPrimary)
            }
            .padding(.horizontal, 12).padding(.vertical, 8)
            Rectangle().fill(Color.ytmActionWhite).frame(height: 2)
        }
        .background(Color.ytmMiniSurface)
        .clipShape(RoundedRectangle(cornerRadius: 8))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct YTMTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            SamplesView().tabItem { Label("Samples", systemImage: "rectangle.stack.fill") }
            ExploreView().tabItem { Label("Explore", systemImage: "magnifyingglass") }
            LibraryView().tabItem { Label("Library", systemImage: "books.vertical.fill") }
        }
        .tint(.ytmActionWhite) // active is pure white, no red, no pill
        .toolbarBackground(Color.ytmTabBar, for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Song ↔ Video toggle: thumb slide + body morph
withAnimation(.easeOut(duration: 0.2)) { mode = .video }
// pair with a matchedGeometryEffect or .transition on the player body (250ms)

// Now Playing present/dismiss (mini-bar expands upward)
.transition(.move(edge: .bottom))
// 320ms ease-out

// Up-next shelf
.transition(.move(edge: .bottom).combined(with: .opacity))
// 280ms ease-out + scrim fade

// Scrubber drag — thumb grows + recolors instantly
.animation(.easeOut(duration: 0.15), value: scrubbing)

// Art backdrop crossfade on track change
.animation(.easeInOut(duration: 0.4), value: artworkURL)

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: toggleFlipped)
.sensoryFeedback(.impact(weight: .light), trigger: scrubbing)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 24pt |
| Samples (tab) | `rectangle.stack` / `rectangle.stack.fill` | 24pt |
| Explore (tab) | `magnifyingglass` | 24pt |
| Library (tab) | `books.vertical` / `books.vertical.fill` | 24pt |
| Dismiss player | `chevron.down` | 22pt |
| Overflow | `ellipsis` | 22pt |
| Play | `play.fill` | 26pt |
| Pause | `pause.fill` | 26pt |
| Next track | `forward.fill` | 26pt |
| Previous track | `backward.fill` | 26pt |
| Skip to end | `forward.end.fill` | 22pt |
| Skip to start | `backward.end.fill` | 22pt |
| Queue / up-next | `list.bullet` | 20pt |
| Drag handle | `line.3.horizontal` | 18pt |
| Shuffle | `shuffle` | 22pt |
| Repeat | `repeat` / `repeat.1` | 22pt |
| Like | `hand.thumbsup` / `hand.thumbsup.fill` | 22pt |
| Cast | `airplayaudio` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Download | `arrow.down.circle` / `checkmark.circle.fill` | 20pt |

## 7. Dark Mode

```swift
struct YTMTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.ytmCanvas)
            .foregroundStyle(Color.ytmTextPrimary)
            .preferredColorScheme(.dark) // YT Music is dark-only on iOS
    }
}

extension View {
    func ytmTheme() -> some View { modifier(YTMTheme()) }
}
```

YouTube Music ships **dark-only** on iOS — there is no light mode. Always pin `.preferredColorScheme(.dark)`. The canvas is near-black `#030303` (darker than Spotify's `#121212`) so the blurred album-art backdrop reads as a glow. Never lighten the canvas to "soften" it — the contrast against the art is the point.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator(style: .light)`)
- Bundle Roboto TTFs (Apache 2.0 — free to redistribute); never substitute SF Pro or a serif
- Dynamic Type: scale screen title, section header, body, row title; keep the Song/Video toggle label, tab labels, timestamps, and "UP NEXT" eyebrow at FIXED sizes (layout-sensitive)
- VoiceOver: label the play button "Play" / "Pause"; the Song/Video toggle as "Song, selected" / "Video, button"; scrubber as an adjustable element ("Playback position, 42 percent") with `.accessibilityValue` and increment/decrement actions
- The art-glow backdrop must be `.accessibilityHidden(true)` — it is decorative; the album art carries the label
- Color contrast: `#FFFFFF` and `#AAAAAA` on `#030303` pass WCAG AA; `#717171` is reserved for non-essential metadata only
- The white play button's `#030303` glyph passes contrast trivially — never tint the glyph red
- Reduce Motion: disable the art-backdrop crossfade and the toggle slide (use a crossfade); keep the scrubber thumb size change (it conveys state)
- Reduce Transparency: replace the `rgba(255,255,255,0.08)` toggle background with solid `#272727`
- Tabular figures: apply `.monospacedDigit()` to all timestamps so the scrubber time doesn't jitter
