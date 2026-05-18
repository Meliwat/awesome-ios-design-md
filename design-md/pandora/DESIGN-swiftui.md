# Pandora (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Pandora's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and the signature thumb up/down, album-art gradient Now Playing, and station-list row.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let pandBlue        = Color(red: 0.133, green: 0.251, blue: 0.600) // #224099  heritage
    static let pandBright      = Color(red: 0.212, green: 0.408, blue: 1.0)   // #3668FF  THE accent
    static let pandBrightPressed = Color(red: 0.165, green: 0.333, blue: 0.847) // #2A55D8

    // MARK: - Canvas & Surfaces (Dark — primary)
    static let pandCanvas    = Color(red: 0.043, green: 0.059, blue: 0.110) // #0B0F1C  navy
    static let pandSurface1  = Color(red: 0.078, green: 0.102, blue: 0.169) // #141A2B
    static let pandSurface2  = Color(red: 0.118, green: 0.145, blue: 0.220) // #1E2538
    static let pandDivider   = Color(red: 0.157, green: 0.188, blue: 0.290) // #28304A
    static let pandGradientMid = Color(red: 0.102, green: 0.133, blue: 0.251) // #1A2240

    // MARK: - Canvas & Surfaces (Light)
    static let pandCanvasLight   = Color.white                                   // #FFFFFF
    static let pandSurface1Light = Color(red: 0.957, green: 0.965, blue: 0.984) // #F4F6FB
    static let pandDividerLight  = Color(red: 0.894, green: 0.910, blue: 0.949) // #E4E8F2

    // MARK: - Text
    static let pandTextPrimary    = Color(red: 0.929, green: 0.941, blue: 0.969) // #EDF0F7
    static let pandTextSecondary  = Color(red: 0.604, green: 0.639, blue: 0.741) // #9AA3BD
    static let pandTextTertiary   = Color(red: 0.392, green: 0.431, blue: 0.549) // #646E8C
    static let pandTextPrimaryLight   = Color(red: 0.067, green: 0.086, blue: 0.165) // #11162A
    static let pandTextSecondaryLight = Color(red: 0.361, green: 0.400, blue: 0.502) // #5C6680
    static let pandOnBright = Color.white                                          // #FFFFFF

    // MARK: - Semantic
    static let pandSuccess = Color(red: 0.169, green: 0.769, blue: 0.541) // #2BC48A
    static let pandWarning = Color(red: 0.949, green: 0.663, blue: 0.227) // #F2A93A
    static let pandError   = Color(red: 0.941, green: 0.322, blue: 0.420) // #F0526B
}
```

## 2. Typography

Pandora's brand face is a clean grotesque. Bundle it if licensed; otherwise ship **Inter** (SIL OFL). Timecodes and counts are tabular.

```swift
extension Font {
    // Brand face name — swap "Inter" for the licensed brand face if available
    private static let brand = "Inter"

    static let pandDisplay     = Font.custom("\(brand)-ExtraBold", size: 32).weight(.heavy)
    static let pandScreenTitle = Font.custom("\(brand)-ExtraBold", size: 26).weight(.heavy)
    static let pandTrackTitle  = Font.custom("\(brand)-ExtraBold", size: 21).weight(.heavy)
    static let pandSection     = Font.custom("\(brand)-Bold",      size: 17).weight(.bold)
    static let pandBody        = Font.custom("\(brand)-Regular",   size: 16).weight(.regular)
    static let pandStationName = Font.custom("\(brand)-SemiBold",  size: 15).weight(.semibold)
    static let pandMeta        = Font.custom("\(brand)-Regular",   size: 14).weight(.regular)
    static let pandAlbumLine   = Font.custom("\(brand)-Medium",    size: 13).weight(.medium)
    static let pandEyebrow     = Font.custom("\(brand)-Bold",      size: 12).weight(.bold)
    static let pandTime        = Font.custom("\(brand)-Medium",    size: 11).weight(.medium)
    static let pandButton      = Font.custom("\(brand)-Bold",      size: 16).weight(.bold)
    static let pandTab         = Font.custom("\(brand)-SemiBold",  size: 10).weight(.semibold)
    static let pandThumbCount  = Font.custom("\(brand)-SemiBold",  size: 12).weight(.semibold)
}

extension View {
    func pandTabularNumbers() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Thumb Up / Down (the signature atom)

```swift
enum ThumbState { case none, up, down }

struct ThumbControls: View {
    @Binding var state: ThumbState
    let onThumbUp: () -> Void
    let onThumbDown: () -> Void   // also skips the track

    var body: some View {
        HStack {
            ThumbButton(
                systemName: "hand.thumbsdown.fill",
                // Thumb-down is NEVER solid — outline only, even when active
                isFilled: false,
                stroke: state == .down ? Color.pandTextSecondary : Color.pandTextTertiary
            ) {
                state = .down; onThumbDown()
            }

            Spacer()

            ThumbButton(
                systemName: "hand.thumbsup.fill",
                // Thumb-up fills solid Bright Blue when active — a lasting genome vote
                isFilled: state == .up,
                stroke: state == .up ? Color.pandBright : Color.pandTextTertiary
            ) {
                state = (state == .up ? .none : .up); onThumbUp()
            }
        }
    }
}

struct ThumbButton: View {
    let systemName: String
    let isFilled: Bool
    let stroke: Color
    let action: () -> Void
    @State private var pop = false

    var body: some View {
        Button {
            pop = true
            action()
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.24) { pop = false }
        } label: {
            Image(systemName: systemName)
                .font(.system(size: 26, weight: .regular))
                .foregroundStyle(isFilled ? Color.pandBright : .clear)
                .overlay(
                    Image(systemName: systemName)
                        .font(.system(size: 26, weight: .regular))
                        .foregroundStyle(stroke)
                        .opacity(isFilled ? 0 : 1)   // outline appearance when not filled
                )
                .frame(width: 44, height: 44)
                .scaleEffect(pop ? 1.2 : 1.0)
                .animation(.easeOut(duration: 0.24), value: pop)
        }
        .sensoryFeedback(.impact(weight: .light), trigger: pop)
    }
}
```

> Note: SF Symbols `hand.thumbsup` (outline) / `hand.thumbsup.fill` (solid) are the cleaner mapping; the snippet above keeps one symbol and toggles fill so the up/down asymmetry stays explicit. The hard rule: **up can be solid `#3668FF`; down is always an outline.**

### Now Playing (album-art gradient hero)

```swift
struct NowPlayingView: View {
    let artwork: URL?
    let title: String, artist: String, albumLine: String
    let stationName: String
    @Binding var thumb: ThumbState
    @Binding var isPlaying: Bool
    var progress: Double
    let elapsed: String, remaining: String
    let artTopColor: Color   // sampled from the artwork

    var body: some View {
        ZStack {
            LinearGradient(colors: [artTopColor, .pandGradientMid, .pandCanvas],
                           startPoint: .top, endPoint: .bottom)
                .ignoresSafeArea()

            VStack(spacing: 0) {
                // Top bar — floats over the gradient, NO card frame
                HStack {
                    Image(systemName: "chevron.down").font(.system(size: 20, weight: .semibold))
                    Spacer()
                    VStack(spacing: 2) {
                        Text("STATION").font(.pandEyebrow).tracking(1.0)
                            .foregroundStyle(Color.pandTextSecondary)
                        Text(stationName).font(.system(size: 13, weight: .bold))
                            .foregroundStyle(Color.pandTextPrimary)
                    }
                    Spacer()
                    Image(systemName: "ellipsis").font(.system(size: 20, weight: .semibold))
                }
                .foregroundStyle(Color.pandTextPrimary)
                .padding(.horizontal, 20).padding(.top, 8)

                AsyncImage(url: artwork) { $0.resizable().aspectRatio(1, contentMode: .fit) }
                    placeholder: { RoundedRectangle(cornerRadius: 10).fill(Color.pandSurface2) }
                    .clipShape(RoundedRectangle(cornerRadius: 10))
                    .shadow(color: .black.opacity(0.7), radius: 25, x: 0, y: 24)
                    .padding(.horizontal, 32).padding(.top, 26)

                VStack(alignment: .leading, spacing: 4) {
                    Text(title).font(.pandTrackTitle).foregroundStyle(Color.pandTextPrimary)
                    Text(artist).font(.pandStationName.weight(.medium)).foregroundStyle(Color.pandTextSecondary)
                    Text(albumLine).font(.pandAlbumLine).foregroundStyle(Color.pandTextTertiary)
                }
                .frame(maxWidth: .infinity, alignment: .leading)
                .padding(.horizontal, 32).padding(.top, 22)

                // Scrubber
                VStack(spacing: 8) {
                    GeometryReader { geo in
                        ZStack(alignment: .leading) {
                            Capsule().fill(Color.pandSurface2).frame(height: 4)
                            Capsule().fill(Color.pandBright)
                                .frame(width: geo.size.width * progress, height: 4)
                            Circle().fill(.white).frame(width: 12, height: 12)
                                .offset(x: geo.size.width * progress - 6)
                        }
                    }.frame(height: 12)
                    HStack {
                        Text(elapsed).pandTabularNumbers()
                        Spacer()
                        Text(remaining).pandTabularNumbers()
                    }
                    .font(.pandTime).foregroundStyle(Color.pandTextTertiary)
                }
                .padding(.horizontal, 32).padding(.top, 20)

                // Transport: thumb-down · skip-back · play · skip-forward · thumb-up
                HStack {
                    ThumbButton(systemName: "hand.thumbsdown.fill",
                                isFilled: false,
                                stroke: thumb == .down ? .pandTextSecondary : .pandTextTertiary) {
                        thumb = .down
                    }
                    Spacer()
                    Button {} label: { Image(systemName: "backward.fill").font(.system(size: 26)) }
                        .foregroundStyle(Color.pandTextPrimary)
                    Spacer()
                    Button { isPlaying.toggle() } label: {
                        Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                            .font(.system(size: 26)).foregroundStyle(.white)
                            .frame(width: 66, height: 66)
                            .background(Circle().fill(Color.pandBright))
                            .shadow(color: Color.pandBright.opacity(0.7), radius: 13, x: 0, y: 10)
                    }
                    Spacer()
                    Button {} label: { Image(systemName: "forward.fill").font(.system(size: 26)) }
                        .foregroundStyle(Color.pandTextPrimary)
                    Spacer()
                    ThumbButton(systemName: "hand.thumbsup.fill",
                                isFilled: thumb == .up,
                                stroke: thumb == .up ? .pandBright : .pandTextTertiary) {
                        thumb = (thumb == .up ? .none : .up)
                    }
                }
                .padding(.horizontal, 36).padding(.top, 22)

                Spacer()
            }
        }
    }
}
```

### Station List Row

```swift
struct StationRow: View {
    let artwork: URL?
    let name: String, subtitle: String
    let isPlaying: Bool

    var body: some View {
        HStack(spacing: 14) {
            AsyncImage(url: artwork) { $0.resizable().aspectRatio(contentMode: .fill) }
                placeholder: { RoundedRectangle(cornerRadius: 8).fill(Color.pandSurface2) }
                .frame(width: 52, height: 52)
                .clipShape(RoundedRectangle(cornerRadius: 8))

            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.pandStationName.weight(.bold)).foregroundStyle(Color.pandTextPrimary)
                Text(subtitle).font(.pandThumbCount).foregroundStyle(Color.pandTextSecondary)
            }
            Spacer()
            if isPlaying { EqualizerBars() }
        }
        .padding(10)
        .background(isPlaying ? Color.pandSurface2 : .clear, in: RoundedRectangle(cornerRadius: 12))
    }
}

struct EqualizerBars: View {
    @State private var phase = false
    private let heights: [CGFloat] = [0.6, 1.0, 0.4, 0.8]
    var body: some View {
        HStack(alignment: .bottom, spacing: 2) {
            ForEach(0..<4) { i in
                Capsule().fill(Color.pandBright)
                    .frame(width: 3, height: 16 * (phase ? heights[i] : heights[(i + 2) % 4]))
            }
        }
        .frame(height: 16)
        .onAppear {
            withAnimation(.easeInOut(duration: 0.5).repeatForever(autoreverses: true)) { phase = true }
        }
    }
}
```

### Primary Button

```swift
struct PandPrimaryButton: View {
    let title: String
    let action: () -> Void
    @State private var pressed = false

    var body: some View {
        Button(action: action) {
            Text(title).font(.pandButton).foregroundStyle(Color.pandOnBright)
                .padding(.horizontal, 28).frame(height: 50)
                .frame(maxWidth: .infinity)
                .background(pressed ? Color.pandBrightPressed : Color.pandBright, in: Capsule())
                .scaleEffect(pressed ? 0.98 : 1)
        }
        .buttonStyle(.plain)
        .simultaneousGesture(DragGesture(minimumDistance: 0)
            .onChanged { _ in pressed = true }.onEnded { _ in pressed = false })
        .animation(.easeOut(duration: 0.12), value: pressed)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct PandTabView: View {
    var body: some View {
        TabView {
            ForYouView().tabItem { Label("For You", systemImage: "house.fill") }
            BrowseView().tabItem { Label("Browse", systemImage: "magnifyingglass") }
            CollectionView().tabItem { Label("My Collection", systemImage: "music.note.list") }
            RecentsView().tabItem { Label("Recents", systemImage: "clock.fill") }
        }
        .tint(.pandTextPrimary)   // active = text-primary; no pill — matches Pandora
    }
}
```

## 5. Motion

```swift
// Thumb tap — scale pop + (up only) fill cross-fade
withAnimation(.easeOut(duration: 0.24)) { pop = true }   // 1.0 → 1.2 → 1.0
.sensoryFeedback(.impact(weight: .light), trigger: thumbChanged)
// Thumb-down additionally triggers the skip transition

// Track change — art cross-fade + scale 0.96 → 1.0 (320ms); gradient re-derives (400ms)
withAnimation(.easeOut(duration: 0.32)) { artScale = 1.0 }
withAnimation(.easeInOut(duration: 0.4)) { artTopColor = sampled(newArtwork) }
// title/artist: .transition(.opacity) 200ms

// Skip — art slides horizontally then settles 260ms ease-out
// Play/pause — icon morph 150ms; button scale 1.0 → 0.96 → 1.0 on press
// Scrubber drag — knob 1:1; timecodes update live (tabular, no layout shift)

// Mini → Now Playing — shared element art expands (320ms ease-out) + gradient fade-in
.matchedGeometryEffect(id: "artwork", in: namespace)

// Station start — equalizer pulses in, push Now Playing (300ms)
// Sheet present — .presentationDetents([.medium, .large]) 320ms + scrim
.sensoryFeedback(.success, trigger: stationCreated)
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| For You (tab) | `house.fill` | 22pt |
| Browse (tab) | `magnifyingglass` | 22pt |
| My Collection (tab) | `music.note.list` | 22pt |
| Recents (tab) | `clock.fill` | 22pt |
| Thumb up | `hand.thumbsup` / `hand.thumbsup.fill` | 26pt |
| Thumb down | `hand.thumbsdown` (outline only) | 26pt |
| Play | `play.fill` | 26pt |
| Pause | `pause.fill` | 26pt |
| Skip forward | `forward.fill` | 26pt |
| Skip back | `backward.fill` | 26pt |
| Collapse Now Playing | `chevron.down` | 20pt |
| Overflow | `ellipsis` | 20pt |
| Create station | `plus.circle.fill` | 20pt |
| Shuffle | `shuffle` | 20pt |
| Add variety | `slider.horizontal.3` | 18pt |
| Why this track | `info.circle` | 18pt |
| Share | `square.and.arrow.up` | 20pt |
| Search | `magnifyingglass` | 18pt |

## 7. Dark Mode

```swift
struct PandTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme

    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.pandCanvas : Color.pandCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.pandTextPrimary : Color.pandTextPrimaryLight)
            .tint(.pandBright)  // accent / play / active thumb-up identical across themes
    }
}

extension View { func pandTheme() -> some View { modifier(PandTheme()) } }
```

Pandora is dark-first: the canvas is a deep blue-navy `#0B0F1C` derived from Pandora Blue `#224099`, never a neutral gray. Bright Blue `#3668FF` is constant across themes — the active thumb-up, play button, scrubber fill, and primary CTA. On Now Playing the background is a per-track gradient sampled from the artwork into the navy floor; album art is never tinted. Light mode (settings/account) inverts text to `#11162A` and surfaces to white but keeps `#3668FF` as the accent.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UIImpactFeedbackGenerator`); `matchedGeometryEffect` works from iOS 14
- Bundle Pandora's brand face via `Info.plist` `UIAppFonts` if licensed; otherwise ship Inter (SIL OFL) and keep the named ramp identical
- Tabular numerals: apply `.monospacedDigit()` to timecodes, durations, and thumb counts so they never jitter
- Album-art color sampling: derive `artTopColor` with `UIImage` average/dominant color (or a `CIAreaAverage` filter) clamped for contrast against `#EDF0F7` text; cache per track
- Dynamic Type: scale display, screen title, track title, body, station name; keep tab labels, eyebrow, timecodes, thumb counts FIXED (layout-critical); album art never scales
- VoiceOver: the thumb-up announces "Thumb up, {selected/not selected}, shapes this station"; thumb-down "Thumb down, skips and tunes the station away"; the play button "Play / Pause"; the station row "{name}, {subtitle}, double-tap to play"
- The thumb asymmetry must be conveyed non-visually too: distinct VoiceOver hints make clear up commits and down corrects — never rely on fill alone
- Contrast: `#EDF0F7` on `#0B0F1C` and white on `#3668FF` pass WCAG AA; verify the sampled gradient top color keeps title contrast ≥ 4.5:1 (darken the sample if needed)
- Reduce Motion: disable the art cross-fade scale, the thumb pop, and the equalizer animation (use a static 3-bar glyph); keep the gradient (it conveys the track)
- Reduce Transparency: replace the mini-player / tab-bar blur with a solid `#0B0F1C` fill
- Hit targets: thumbs 44pt each and clearly separated from skip/play; play 66pt; skip 44pt+; station row full-row ≥ 64pt; tab icons 22pt in ≥48pt
