# Amazon Music (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Amazon Music's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark — the primary mode)
    static let amCanvas      = Color(red: 0.047, green: 0.106, blue: 0.133) // #0C1B22
    static let amSurface1    = Color(red: 0.071, green: 0.165, blue: 0.200) // #122A33
    static let amSurface2    = Color(red: 0.102, green: 0.216, blue: 0.259) // #1A3742
    static let amDivider     = Color(red: 0.137, green: 0.275, blue: 0.325) // #234653
    static let amGradientTop = Color(red: 0.086, green: 0.251, blue: 0.298) // #16404C
    static let amTabBar      = Color(red: 0.031, green: 0.071, blue: 0.094) // #081218

    // MARK: - Brand
    static let amCyan        = Color(red: 0.0,   green: 0.659, blue: 0.882) // #00A8E1
    static let amCyanBright  = Color(red: 0.145, green: 0.820, blue: 0.855) // #25D1DA
    static let amCyanPressed = Color(red: 0.0,   green: 0.525, blue: 0.702) // #0086B3

    // MARK: - Text
    static let amTextPrimary   = Color.white                                  // #FFFFFF
    static let amTextSecondary = Color(red: 0.624, green: 0.714, blue: 0.749) // #9FB6BF
    static let amTextTertiary  = Color(red: 0.420, green: 0.525, blue: 0.576) // #6B8693
    static let amOnBright      = Color(red: 0.016, green: 0.153, blue: 0.188) // #042730

    // MARK: - Semantic
    static let amSuccess     = Color(red: 0.180, green: 0.769, blue: 0.714) // #2EC4B6
    static let amError       = Color(red: 1.0,   green: 0.420, blue: 0.420) // #FF6B6B

    // MARK: - X-Ray panel
    static let amXRayFill    = Color(red: 0.0, green: 0.659, blue: 0.882).opacity(0.07)
    static let amXRayBorder  = Color(red: 0.145, green: 0.820, blue: 0.855).opacity(0.22)
}
```

## 2. Typography

Amazon Music uses **Amazon Ember** (Amazon's corporate face). Bundle the Ember TTFs via `Info.plist` / `UIAppFonts`; the documented fallback is Inter (its metrics track Ember closely).

```swift
extension Font {
    static func amazon(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .heavy:    return "AmazonEmber-Heavy"
            case .bold:     return "AmazonEmber-Bold"
            case .semibold: return "AmazonEmber-Medium"
            case .medium:   return "AmazonEmber-Medium"
            default:        return "AmazonEmber-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let amGreeting    = Font.custom("AmazonEmber-Heavy",   size: 32) // 800
    static let amPlayerTitle = Font.custom("AmazonEmber-Bold",    size: 26)
    static let amSection     = Font.custom("AmazonEmber-Bold",    size: 22)
    static let amCardTitle   = Font.custom("AmazonEmber-Bold",    size: 18)
    static let amBody        = Font.custom("AmazonEmber-Regular", size: 16)
    static let amRowTitle    = Font.custom("AmazonEmber-Medium",  size: 15)
    static let amLyricLine   = Font.custom("AmazonEmber-Medium",  size: 15) // 600 visual
    static let amSubtitle    = Font.custom("AmazonEmber-Regular", size: 14)
    static let amXRayLabel   = Font.custom("AmazonEmber-Bold",    size: 10)
    static let amXRayBadge   = Font.custom("AmazonEmber-Heavy",   size: 9)
    static let amChip        = Font.custom("AmazonEmber-Medium",  size: 13)
    static let amTimestamp   = Font.custom("AmazonEmber-Regular", size: 11)
    static let amTab         = Font.custom("AmazonEmber-Medium",  size: 10)
    static let amButton      = Font.custom("AmazonEmber-Bold",    size: 15)
}
```

## 3. Signature Components

### Full-Screen Player (gradient + art + meta + X-Ray)

```swift
struct PlayerView: View {
    let artworkURL: String
    let track: String
    let artist: String

    var body: some View {
        ZStack {
            LinearGradient(
                colors: [
                    Color(red: 0.110, green: 0.290, blue: 0.341), // #1C4A57
                    Color(red: 0.086, green: 0.227, blue: 0.271), // #163A45
                    Color(red: 0.059, green: 0.153, blue: 0.188), // #0F2730
                    .amCanvas,
                ],
                startPoint: .top, endPoint: .bottom
            )
            .ignoresSafeArea()

            VStack(spacing: 0) {
                topBar
                AsyncImage(url: URL(string: artworkURL)) { img in
                    img.resizable().scaledToFill()
                } placeholder: { Color.amSurface1 }
                .frame(width: 196, height: 196)
                .clipShape(RoundedRectangle(cornerRadius: 6))
                .shadow(color: .black.opacity(0.65), radius: 22, y: 18)
                .padding(.top, 18)

                HStack(alignment: .bottom) {
                    VStack(alignment: .leading, spacing: 3) {
                        Text(track).font(.amPlayerTitle).foregroundStyle(Color.amTextPrimary)
                        Text(artist).font(.amSubtitle).foregroundStyle(Color.amTextSecondary)
                    }
                    Spacer()
                    Image(systemName: "plus")
                        .font(.system(size: 22, weight: .semibold))
                        .foregroundStyle(Color.amCyanBright)
                }
                .padding(.horizontal, 22).padding(.top, 16)

                XRayLyricsPanel(
                    lines: ["Daylight, I wake up feeling like", "But stay woke", "Niggas creepin'", "They gon' find you"],
                    currentIndex: 1
                )
                .padding(.horizontal, 18).padding(.top, 16)

                Scrubber().padding(.horizontal, 22).padding(.top, 16)
                TransportControls().padding(.top, 12)
                Spacer()
            }
        }
    }

    private var topBar: some View {
        HStack {
            Image(systemName: "chevron.down").font(.system(size: 22))
            Spacer()
            Text("PLAYING FROM PLAYLIST")
                .font(.system(size: 10, weight: .bold)).tracking(0.7)
                .foregroundStyle(Color.amTextSecondary)
            Spacer()
            Image(systemName: "ellipsis").font(.system(size: 22))
        }
        .foregroundStyle(Color.amTextPrimary)
        .padding(.horizontal, 20).padding(.top, 6)
    }
}
```

### X-Ray Lyrics Panel (Signature)

```swift
struct XRayLyricsPanel: View {
    let lines: [String]
    let currentIndex: Int

    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            HStack(spacing: 7) {
                Text("X-RAY")
                    .font(.amXRayBadge).tracking(0.5)
                    .foregroundStyle(Color.amOnBright)
                    .padding(.horizontal, 7).padding(.vertical, 3)
                    .background(RoundedRectangle(cornerRadius: 4).fill(Color.amCyanBright))
                Text("LYRICS")
                    .font(.amXRayLabel).tracking(0.6)
                    .foregroundStyle(Color.amTextSecondary)
            }
            .padding(.bottom, 10)

            ForEach(Array(lines.enumerated()), id: \.offset) { i, line in
                Text(line)
                    .font(.amLyricLine)
                    .foregroundStyle(color(for: i))
                    .padding(.vertical, 3)
                    .animation(.easeOut(duration: 0.18), value: currentIndex)
                    .contentShape(Rectangle())
                    .onTapGesture { seek(toLine: i) }
            }
        }
        .padding(.horizontal, 16).padding(.vertical, 14)
        .frame(maxWidth: .infinity, alignment: .leading)
        .background(RoundedRectangle(cornerRadius: 10).fill(Color.amXRayFill))
        .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.amXRayBorder, lineWidth: 1))
    }

    private func color(for i: Int) -> Color {
        if i == currentIndex { return .amCyanBright }
        if i == currentIndex + 1 { return .amTextSecondary }
        return .amTextTertiary
    }

    private func seek(toLine i: Int) { /* scrub player to lines[i] timestamp */ }
}
```

### Scrubber

```swift
struct Scrubber: View {
    @State private var progress: Double = 0.38
    @State private var dragging = false

    var body: some View {
        VStack(spacing: 7) {
            GeometryReader { geo in
                let w = geo.size.width
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.white.opacity(0.16)).frame(height: 4)
                    Capsule().fill(Color.amCyan).frame(width: max(0, w * progress), height: 4)
                    Circle()
                        .fill(Color.amCyan)
                        .frame(width: 13, height: 13)
                        .background(
                            Circle().fill(Color.amCyan.opacity(0.2))
                                .frame(width: dragging ? 25 : 21, height: dragging ? 25 : 21)
                        )
                        .offset(x: w * progress - 6.5)
                }
                .frame(height: 16)
                .contentShape(Rectangle())
                .gesture(
                    DragGesture(minimumDistance: 0)
                        .onChanged { v in dragging = true; progress = min(1, max(0, v.location.x / w)) }
                        .onEnded { _ in withAnimation(.easeOut(duration: 0.15)) { dragging = false } }
                )
            }
            .frame(height: 16)
            HStack {
                Text("1:54").font(.amTimestamp).monospacedDigit()
                Spacer()
                Text("-3:33").font(.amTimestamp).monospacedDigit()
            }
            .foregroundStyle(Color.amTextSecondary)
        }
    }
}
```

### Transport Controls (cyan play button)

```swift
struct TransportControls: View {
    @State private var playing = true

    var body: some View {
        HStack {
            Image(systemName: "repeat").font(.system(size: 22)).foregroundStyle(Color.amTextSecondary)
            Spacer()
            Image(systemName: "backward.fill").font(.system(size: 24)).foregroundStyle(Color.amTextPrimary)
            Spacer()
            Button { playing.toggle() } label: {
                ZStack {
                    Circle().fill(Color.amCyan)
                        .frame(width: 60, height: 60)
                        .shadow(color: Color.amCyan.opacity(0.55), radius: 11, y: 8)
                    Image(systemName: playing ? "pause.fill" : "play.fill")
                        .font(.system(size: 24))
                        .foregroundStyle(.white)
                }
            }
            .buttonStyle(.plain)
            Spacer()
            Image(systemName: "forward.fill").font(.system(size: 24)).foregroundStyle(Color.amTextPrimary)
            Spacer()
            Image(systemName: "shuffle").font(.system(size: 22)).foregroundStyle(Color.amTextSecondary)
        }
        .padding(.horizontal, 30)
    }
}
```

### Home Shelf (time-of-day greeting + carousels)

```swift
struct HomeShelf: View {
    let greeting: String
    let cards: [(title: String, subtitle: String)]

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 24) {
                Text(greeting)
                    .font(.amGreeting)
                    .foregroundStyle(Color.amTextPrimary)
                    .padding(.horizontal, 18)

                VStack(alignment: .leading, spacing: 12) {
                    Text("Recently played").font(.amSection).foregroundStyle(Color.amTextPrimary).padding(.horizontal, 18)
                    ScrollView(.horizontal, showsIndicators: false) {
                        HStack(spacing: 8) {
                            ForEach(cards, id: \.title) { card in
                                VStack(alignment: .leading, spacing: 8) {
                                    RoundedRectangle(cornerRadius: 6)
                                        .fill(LinearGradient(colors: [.amCyan, .amSurface1], startPoint: .topLeading, endPoint: .bottomTrailing))
                                        .frame(width: 150, height: 150)
                                    Text(card.title).font(.amCardTitle).foregroundStyle(Color.amTextPrimary)
                                    Text(card.subtitle).font(.amSubtitle).foregroundStyle(Color.amTextSecondary)
                                }
                                .frame(width: 150)
                            }
                        }
                        .padding(.horizontal, 18)
                    }
                }
            }
            .padding(.vertical, 16)
        }
        .background(Color.amCanvas)
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
                    .fill(LinearGradient(colors: [.amCyan, .amSurface1], startPoint: .topLeading, endPoint: .bottomTrailing))
                    .frame(width: 40, height: 40)
                VStack(alignment: .leading, spacing: 2) {
                    Text("Redbone").font(.system(size: 13, weight: .semibold)).foregroundStyle(Color.amTextPrimary)
                    Text("Childish Gambino").font(.system(size: 11)).foregroundStyle(Color.amTextSecondary)
                }
                Spacer()
                Image(systemName: "pause.fill").font(.system(size: 22)).foregroundStyle(Color.amCyan)
                Image(systemName: "forward.fill").font(.system(size: 22)).foregroundStyle(Color.amCyan)
            }
            .padding(.horizontal, 12).padding(.vertical, 8)
            Rectangle().fill(Color.amCyan).frame(height: 2)
        }
        .background(Color.amSurface2)
        .clipShape(RoundedRectangle(cornerRadius: 8))
    }
}
```

## 4. Bottom Tab Bar

```swift
struct AmazonMusicTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            FindView().tabItem { Label("Find", systemImage: "magnifyingglass") }
            PodcastsView().tabItem { Label("Podcasts", systemImage: "mic.fill") }
            LibraryView().tabItem { Label("Library", systemImage: "books.vertical.fill") }
        }
        .tint(.amCyanBright) // active is BRIGHT cyan, no tint pill
        .toolbarBackground(Color.amTabBar, for: .tabBar)
        .toolbarBackground(.visible, for: .tabBar)
    }
}
```

## 5. Motion

```swift
// Player present/dismiss (mini-bar expands upward)
.transition(.move(edge: .bottom))   // 300ms ease-out

// X-Ray lyric recolor as playback advances
.animation(.easeOut(duration: 0.18), value: currentIndex)
// the panel ScrollViewReader.scrollTo(currentIndex, anchor: .center) keeps it centered

// X-Ray seek tap — tapped line pulses bright cyan
withAnimation(.easeOut(duration: 0.2)) { /* set tappedPulse */ }

// Scrubber drag — halo grows on touch-down
.animation(.easeOut(duration: 0.15), value: dragging)

// Gradient + art crossfade on track change
.animation(.easeInOut(duration: 0.35), value: artworkURL)

// Haptics
.sensoryFeedback(.impact(weight: .light), trigger: playing)
.sensoryFeedback(.impact(weight: .light), trigger: lyricTapped)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Find (tab) | `magnifyingglass` | 22pt |
| Podcasts (tab) | `mic` / `mic.fill` | 22pt |
| Library (tab) | `books.vertical` / `books.vertical.fill` | 22pt |
| Dismiss player | `chevron.down` | 22pt |
| Overflow | `ellipsis` | 22pt |
| Play / Pause | `play.fill` / `pause.fill` | 24pt |
| Next / Previous | `forward.fill` / `backward.fill` | 24pt |
| Repeat | `repeat` / `repeat.1` | 22pt |
| Shuffle | `shuffle` | 22pt |
| Add to library | `plus` / `checkmark` | 22–26pt |
| Queue | `list.bullet` | 20pt |
| Lyrics toggle | `quote.bubble` | 20pt |
| Equalizer / Atmos | `slider.horizontal.3` | 20pt |
| Cast / devices | `airplayaudio` | 20pt |
| Search | `magnifyingglass` | 18pt |
| Download | `arrow.down.circle` / `checkmark.circle.fill` | 20pt |
| Like | `heart` / `heart.fill` | 22pt |

## 7. Dark Mode

```swift
struct AmazonMusicTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.amCanvas)
            .foregroundStyle(Color.amTextPrimary)
            .preferredColorScheme(.dark) // Amazon Music is dark-first on iOS
    }
}

extension View {
    func amazonMusicTheme() -> some View { modifier(AmazonMusicTheme()) }
}
```

Amazon Music is **dark-first** on iOS. The canvas is the deep teal-navy `#0C1B22` — a *tinted* dark, never neutral grey or pure black. The whole UI (surfaces, dividers, secondary text) is teal-tinted so it reads as one continuous blue field; the cyan accent is drawn from that same hue axis. Do not "neutralize" any surface toward grey.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator(style: .light)`)
- Bundle Amazon Ember TTFs; the only acceptable substitute is Inter (its x-height and proportions track Ember so layouts transfer faithfully)
- Dynamic Type: scale greeting, section header, body, row title, and lyric line; keep the "X-RAY" badge, X-Ray label, tab labels, and timestamps FIXED (layout-sensitive). The lyric panel must grow taller as lines scale and keep the current line centered
- VoiceOver: label the play button "Play"/"Pause"; the X-Ray panel as "Lyrics"; each lyric line as "Lyric line: {text}" with a custom action "Play from here"; the scrubber as adjustable with `.accessibilityValue`
- The "X-RAY" badge text `#042730` on `#25D1DA` is dark-on-bright for AA contrast — never invert it to a low-contrast pairing
- Color contrast: `#FFFFFF` and `#9FB6BF` on `#0C1B22` pass WCAG AA; `#6B8693` (dimmed lyric lines) is decorative context only — the *current* line is always high-contrast bright cyan
- The cyan play button glyph is white on `#00A8E1` — passes AA; keep the cyan glow shadow (it is the focal cue, not decoration)
- Reduce Motion: disable the gradient/art crossfade and the lyric-scroll animation (snap instead); keep the current-line recolor (it conveys playback position)
- Reduce Transparency: replace the `rgba(0,168,225,0.07)` X-Ray fill with solid `#122A33` and keep the cyan border
- Tabular figures: apply `.monospacedDigit()` to all timestamps so the scrubber time doesn't jitter
