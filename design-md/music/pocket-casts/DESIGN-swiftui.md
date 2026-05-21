# Pocket Casts (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Pocket Casts' visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views including the per-podcast theme tint, the Now Playing player, and the episode list.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand
    static let pcRed         = Color(red: 0.957, green: 0.243, blue: 0.216) // #F43E37
    static let pcRedPressed  = Color(red: 0.827, green: 0.169, blue: 0.145) // #D32B25

    // MARK: - Canvas & Surfaces (Dark — canonical)
    static let pcCanvas      = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let pcSurface1    = Color(red: 0.137, green: 0.137, blue: 0.137) // #232323
    static let pcSurface2    = Color(red: 0.180, green: 0.180, blue: 0.180) // #2E2E2E
    static let pcDivider     = Color(red: 0.220, green: 0.220, blue: 0.220) // #383838

    // MARK: - Canvas & Surfaces (Light)
    static let pcCanvasLight   = Color.white                                  // #FFFFFF
    static let pcSurfaceLight  = Color(red: 0.949, green: 0.949, blue: 0.957) // #F2F2F4
    static let pcDividerLight  = Color(red: 0.886, green: 0.886, blue: 0.894) // #E2E2E4

    // MARK: - Text
    static let pcTextPrimary    = Color.white                                  // #FFFFFF
    static let pcTextSecondary  = Color(red: 0.722, green: 0.722, blue: 0.722) // #B8B8B8
    static let pcTextTertiary   = Color(red: 0.459, green: 0.459, blue: 0.459) // #757575
    static let pcTextPrimaryLt  = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A

    // MARK: - Accent & Semantic
    static let pcInfoBlue    = Color(red: 0.012, green: 0.663, blue: 0.957) // #03A9F4
    static let pcPlayedGreen = Color(red: 0.471, green: 0.835, blue: 0.286) // #78D549
    static let pcStarGold    = Color(red: 0.984, green: 0.753, blue: 0.176) // #FBC02D
    static let pcWarnAmber   = Color(red: 1.000, green: 0.702, blue: 0.000) // #FFB300

    // MARK: - Theme Tint examples (per-podcast, sampled from cover art)
    static let pcTintWarm    = Color(red: 0.878, green: 0.325, blue: 0.235) // #E0533C
    static let pcTintCool    = Color(red: 0.243, green: 0.482, blue: 0.761) // #3E7BC2
    static let pcTintGreen   = Color(red: 0.247, green: 0.651, blue: 0.416) // #3FA66A
    static let pcTintPurple  = Color(red: 0.494, green: 0.341, blue: 0.761) // #7E57C2
    static let pcTintPink    = Color(red: 0.839, green: 0.271, blue: 0.494) // #D6457E
}

/// The per-podcast accent, injected via the environment so every player
/// sub-view re-tints when a different show opens. Defaults to brand Red.
private struct PCThemeTintKey: EnvironmentKey {
    static let defaultValue: Color = .pcRed
}
extension EnvironmentValues {
    var pcThemeTint: Color {
        get { self[PCThemeTintKey.self] }
        set { self[PCThemeTintKey.self] = newValue }
    }
}
```

## 2. Typography

Pocket Casts uses the iOS system face (SF Pro) for native Dynamic Type. Timecodes use a monospaced-digit variant so they don't jitter while scrubbing.

```swift
extension Font {
    static let pcScreenTitle = Font.system(size: 32, weight: .heavy,    design: .default)
    static let pcNowPlaying  = Font.system(size: 22, weight: .bold,     design: .default)
    static let pcSection     = Font.system(size: 22, weight: .bold,     design: .default)
    static let pcEpisode     = Font.system(size: 18, weight: .bold,     design: .default)
    static let pcRowTitle    = Font.system(size: 15, weight: .semibold, design: .default)
    static let pcBody        = Font.system(size: 16, weight: .regular,  design: .default)
    static let pcShowName    = Font.system(size: 14, weight: .medium,   design: .default)
    static let pcMeta        = Font.system(size: 14, weight: .regular,  design: .default)
    static let pcButton      = Font.system(size: 16, weight: .bold,     design: .default)
    static let pcChip        = Font.system(size: 10, weight: .semibold, design: .default)
    static let pcTab         = Font.system(size: 10, weight: .semibold, design: .default)
    static let pcCaption     = Font.system(size: 12, weight: .regular,  design: .default)

    /// Tabular figures for scrubber timecodes / durations.
    static let pcTimecode    = Font.system(size: 12, weight: .medium,   design: .default)
        .monospacedDigit()
    /// Uppercase eyebrows ("NOW PLAYING", "SUN · OCT 13") — apply
    /// .tracking(0.6...1.0) and .textCase(.uppercase) at the call site.
    static let pcEyebrow     = Font.system(size: 12, weight: .bold,     design: .default)
}
```

## 3. Signature Components

### Per-Podcast Theme Tint (cross-fade on show change)

```swift
struct PCThemed<Content: View>: View {
    let tint: Color
    @ViewBuilder let content: () -> Content

    var body: some View {
        content()
            .environment(\.pcThemeTint, tint)
            .animation(.easeOut(duration: 0.25), value: tint) // cross-fade, never hard cut
    }
}
// Usage: PCThemed(tint: .pcTintWarm) { NowPlayingView(...) }
```

### Now Playing Player

```swift
struct NowPlayingView: View {
    @Environment(\.pcThemeTint) private var tint
    let episodeTitle: String
    let showName: String
    @State private var progress: Double = 0.42
    @State private var isPlaying = true
    @State private var scrubbing = false

    var body: some View {
        VStack(spacing: 0) {
            // Top bar
            HStack {
                Image(systemName: "chevron.down").font(.system(size: 18, weight: .semibold))
                Spacer()
                Text("NOW PLAYING")
                    .font(.pcEyebrow).tracking(1).textCase(.uppercase)
                    .foregroundStyle(Color.pcTextSecondary)
                Spacer()
                Image(systemName: "ellipsis").font(.system(size: 18, weight: .semibold))
            }
            .foregroundStyle(Color.pcTextPrimary)
            .padding(.horizontal, 20).padding(.vertical, 8)

            // Cover art with theme-tint glow
            RoundedRectangle(cornerRadius: 8)
                .fill(LinearGradient(colors: [tint, tint.opacity(0.55)],
                                     startPoint: .topLeading, endPoint: .bottomTrailing))
                .aspectRatio(1, contentMode: .fit)
                .overlay(
                    Circle().strokeBorder(.white.opacity(0.92), lineWidth: 7)
                        .frame(width: 130, height: 130)
                )
                .shadow(color: tint.opacity(0.55), radius: 24, x: 0, y: 8)
                .padding(.horizontal, 36).padding(.top, 8).padding(.bottom, 22)

            // Track meta
            VStack(alignment: .leading, spacing: 4) {
                Text(episodeTitle).font(.pcNowPlaying).foregroundStyle(Color.pcTextPrimary)
                Text(showName).font(.pcShowName).foregroundStyle(tint)
            }
            .frame(maxWidth: .infinity, alignment: .leading)
            .padding(.horizontal, 36)

            // Scrubber
            VStack(spacing: 10) {
                GeometryReader { geo in
                    ZStack(alignment: .leading) {
                        Capsule().fill(Color.pcSurface2).frame(height: 4)
                        Capsule().fill(tint).frame(width: geo.size.width * progress, height: 4)
                        Circle().fill(.white)
                            .frame(width: scrubbing ? 18 : 13, height: scrubbing ? 18 : 13)
                            .shadow(color: .black.opacity(0.5), radius: 4, y: 1)
                            .offset(x: geo.size.width * progress - 6.5)
                    }
                    .frame(height: 18)
                    .contentShape(Rectangle())
                    .gesture(
                        DragGesture()
                            .onChanged { v in
                                scrubbing = true
                                progress = min(max(0, v.location.x / geo.size.width), 1)
                            }
                            .onEnded { _ in scrubbing = false }
                    )
                }
                .frame(height: 18)
                HStack {
                    Text("14:22").font(.pcTimecode).foregroundStyle(Color.pcTextSecondary)
                    Spacer()
                    Text("-19:48").font(.pcTimecode).foregroundStyle(Color.pcTextSecondary)
                }
            }
            .padding(.horizontal, 36).padding(.top, 6)

            // Transport
            HStack {
                transportIcon("gobackward.15", size: 24)
                Spacer()
                transportIcon("backward.fill", size: 28)
                Spacer()
                Button { isPlaying.toggle() } label: {
                    ZStack {
                        Circle().fill(tint).frame(width: 68, height: 68)
                            .shadow(color: tint.opacity(0.55), radius: 24, x: 0, y: 8)
                        Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                            .font(.system(size: 26)).foregroundStyle(.white)
                    }
                }
                .buttonStyle(PCScaleStyle())
                Spacer()
                transportIcon("forward.fill", size: 28)
                Spacer()
                transportIcon("goforward.30", size: 24)
            }
            .padding(.horizontal, 40).padding(.vertical, 18)

            // Action chips
            HStack {
                chip("list.bullet", "Up Next", active: true)
                Spacer()
                chip("speedometer", "1.2×", active: false)
                Spacer()
                chip("waveform", "Trim", active: true)
                Spacer()
                chip("star", "Star", active: false)
            }
            .padding(.horizontal, 28).padding(.bottom, 14)

            Spacer()
        }
        .background(Color.pcCanvas.ignoresSafeArea())
    }

    private func transportIcon(_ name: String, size: CGFloat) -> some View {
        Image(systemName: name).font(.system(size: size))
            .foregroundStyle(Color.pcTextPrimary)
    }

    private func chip(_ icon: String, _ label: String, active: Bool) -> some View {
        VStack(spacing: 5) {
            Image(systemName: icon).font(.system(size: 19))
            Text(label).font(.pcChip)
        }
        .foregroundStyle(active ? tint : Color.pcTextSecondary)
    }
}

struct PCScaleStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.94 : 1)
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}
```

### Episode List Row

```swift
struct EpisodeRow: View {
    @Environment(\.pcThemeTint) private var tint
    let dateLabel: String
    let title: String
    let meta: String
    let artwork: LinearGradient

    var body: some View {
        HStack(spacing: 14) {
            RoundedRectangle(cornerRadius: 6).fill(artwork)
                .frame(width: 52, height: 52)
            VStack(alignment: .leading, spacing: 3) {
                Text(dateLabel)
                    .font(.pcEyebrow).tracking(0.6).textCase(.uppercase)
                    .foregroundStyle(Color.pcTextTertiary)
                Text(title).font(.pcRowTitle).foregroundStyle(Color.pcTextPrimary)
                    .lineLimit(1)
                Text(meta).font(.pcMeta).foregroundStyle(Color.pcTextSecondary)
            }
            Spacer()
            Button {} label: {
                Image(systemName: "play.fill")
                    .font(.system(size: 12)).foregroundStyle(Color.pcTextPrimary)
                    .frame(width: 30, height: 30)
                    .overlay(Circle().strokeBorder(Color.pcTextSecondary, lineWidth: 1.5))
            }
        }
        .padding(.vertical, 14).padding(.horizontal, 16)
        .overlay(Divider().background(Color.pcDivider), alignment: .bottom)
        .swipeActions(edge: .leading) {
            Button { } label: { Label("Up Next", systemImage: "text.append") }.tint(tint)
        }
    }
}
```

### Pill Buttons

```swift
struct PCPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.pcButton).foregroundStyle(.white)
                .padding(.vertical, 14).padding(.horizontal, 28)
                .background(Capsule().fill(Color.pcRed))
        }
        .buttonStyle(PCScaleStyle())
    }
}

struct PCTintButton: View {
    @Environment(\.pcThemeTint) private var tint
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.pcButton).foregroundStyle(.white)
                .padding(.vertical, 14).padding(.horizontal, 28)
                .background(Capsule().fill(tint))
        }
        .buttonStyle(PCScaleStyle())
    }
}

struct PCOutlineButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.system(size: 14, weight: .semibold))
                .foregroundStyle(Color.pcTextPrimary)
                .padding(.vertical, 12).padding(.horizontal, 22)
                .overlay(Capsule().strokeBorder(Color.pcTextSecondary, lineWidth: 1.5))
        }
    }
}
```

### Trim Silence Toggle Row

```swift
struct PCEffectRow: View {
    let title: String
    let subtitle: String
    @Binding var isOn: Bool

    var body: some View {
        HStack {
            VStack(alignment: .leading, spacing: 2) {
                Text(title).font(.pcRowTitle).foregroundStyle(Color.pcTextPrimary)
                Text(subtitle).font(.pcCaption).foregroundStyle(Color.pcTextSecondary)
            }
            Spacer()
            Toggle("", isOn: $isOn)
                .labelsHidden()
                .tint(Color.pcRed)
        }
        .padding(.vertical, 14).padding(.horizontal, 16)
        .overlay(Divider().background(Color.pcDivider), alignment: .bottom)
    }
}
```

### Mini-Player

```swift
struct PCMiniPlayer: View {
    @Environment(\.pcThemeTint) private var tint
    let title: String
    let show: String
    @Binding var isPlaying: Bool

    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 6).fill(tint).frame(width: 44, height: 44)
            VStack(alignment: .leading, spacing: 2) {
                Text(title).font(.system(size: 14, weight: .semibold))
                    .foregroundStyle(Color.pcTextPrimary).lineLimit(1)
                Text(show).font(.pcCaption).foregroundStyle(Color.pcTextSecondary).lineLimit(1)
            }
            Spacer()
            Button { isPlaying.toggle() } label: {
                Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                    .font(.system(size: 22)).foregroundStyle(Color.pcTextPrimary)
            }
        }
        .padding(.horizontal, 14).frame(height: 64)
        .background(Color.pcSurface1)
        .overlay(Rectangle().fill(tint).frame(height: 2), alignment: .top)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct PCTabView: View {
    var body: some View {
        TabView {
            PodcastsView().tabItem { Label("Podcasts", systemImage: "square.grid.2x2.fill") }
            FiltersView().tabItem { Label("Filters", systemImage: "line.3.horizontal.decrease") }
            DiscoverView().tabItem { Label("Discover", systemImage: "safari") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.pcRed) // active tab = brand Red; no tint pill (UITabBar default)
    }
}
```

## 5. Motion

```swift
// Play/pause icon morph + button press
.buttonStyle(PCScaleStyle()) // scale 0.94, 120ms ease-out
// icon swap play.fill <-> pause.fill animates implicitly via .animation on isPlaying

// Scrubber knob grow on drag
.frame(width: scrubbing ? 18 : 13, height: scrubbing ? 18 : 13)
.animation(.easeOut(duration: 0.12), value: scrubbing)

// Theme-tint cross-fade on show change
.animation(.easeOut(duration: 0.25), value: tint)

// Mini-player -> Full player (sheet slide)
.transition(.move(edge: .bottom))
// present full player with .easeOut(duration: 0.3)

// Up Next reorder — .onMove on a List; neighbors animate with default List move
// Download complete tick
withAnimation(.spring(response: 0.15, dampingFraction: 0.6)) { showTick = true }

// Haptics
let soft = UIImpactFeedbackGenerator(style: .soft)
soft.impactOccurred() // play, scrubber boundary, reorder pickup/drop, toggle flip
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Podcasts (tab) | `square.grid.2x2` / `square.grid.2x2.fill` | 22pt |
| Filters (tab) | `line.3.horizontal.decrease` | 22pt |
| Discover (tab) | `safari` / `safari.fill` | 22pt |
| Profile (tab) | `person.crop.circle` | 22pt |
| Play | `play.fill` | 26pt |
| Pause | `pause.fill` | 26pt |
| Previous / Next | `backward.fill` / `forward.fill` | 28pt |
| Skip back 15 | `gobackward.15` | 24pt |
| Skip forward 30 | `goforward.30` | 24pt |
| Up Next | `list.bullet` / `text.append` | 19pt |
| Playback speed | `speedometer` | 19pt |
| Trim Silence | `waveform` | 19pt |
| Volume Boost | `speaker.wave.3.fill` | 19pt |
| Star | `star` / `star.fill` | 19pt |
| Mark played | `checkmark.circle.fill` | 19pt |
| Download | `arrow.down.circle` | 18pt |
| Downloaded | `arrow.down.circle.fill` | 18pt |
| Collapse player | `chevron.down` | 18pt |
| More menu | `ellipsis` | 18pt |
| Search | `magnifyingglass` | 16pt |
| Reorder handle | `line.3.horizontal` | 24pt |
| AirPlay | `airplayaudio` | 19pt |

## 7. Dark Mode

```swift
struct PCTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.pcCanvas : Color.pcCanvasLight)
            .foregroundStyle(scheme == .dark ? Color.pcTextPrimary : Color.pcTextPrimaryLt)
    }
}
extension View { func pcTheme() -> some View { modifier(PCTheme()) } }
```

Pocket Casts is dark-first: `#1A1A1A` canvas, `#232323` cards, never pure black. Brand Red `#F43E37` is identical across themes. The per-podcast theme tint is sampled from cover art at runtime and injected via `\.pcThemeTint`; it cross-fades over 250ms on show change. On dark, sheet shadows are weak, so sheets adopt a lighter `#232323` top surface to read as elevated.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.monospacedDigit()` Font modifier and `swipeActions` are iOS 15+; `Toggle.tint` iOS 15+)
- System font (SF Pro) — no bundled fonts required; `Inter` is only a web/spec fallback
- Dynamic Type: support on screen titles, episode titles, body, metadata; keep tab labels, chip labels, timecodes, date eyebrows at FIXED sizes for layout stability
- Use `.monospacedDigit()` on every timecode/duration so digits don't shift while scrubbing
- VoiceOver: label the play button "Play" / "Pause, {episode}"; the scrubber as an `.accessibilityValue` percentage with `.accessibilityAdjustableAction` for skip; episode rows as "Episode: {title}, {meta}, {date}"
- Theme tint must keep ≥3:1 contrast against `#1A1A1A`; if a sampled color fails, fall back to brand Red `#F43E37`
- Touch targets: circular play 68pt, transport 44pt hit, episode-row play 44pt hit, tab icons 44pt hit
- Reduce Motion: disable the theme-tint cross-fade (instant swap), the scrubber-knob grow, and the play/pause morph; keep state changes
- Reduce Transparency: replace the `rgba(26,26,26,0.94)` blurred tab bar with solid `#1A1A1A`
- Color: never rely on the unplayed Red dot alone — pair with a count label for color-blind users
