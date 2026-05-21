# Overcast (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Overcast's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views including the orange-ring play button, the Now Playing player, the Smart Speed / Voice Boost rows, and playlist rows.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Brand (single accent)
    static let ocOrange        = Color(red: 0.988, green: 0.494, blue: 0.059) // #FC7E0F
    static let ocOrangePressed = Color(red: 0.878, green: 0.416, blue: 0.000) // #E06A00
    static let ocLinkBlue      = Color(red: 0.176, green: 0.498, blue: 0.976) // #2D7FF9

    // MARK: - Canvas & Surfaces (Light — paper)
    static let ocPaper        = Color(red: 0.984, green: 0.980, blue: 0.965) // #FBFAF6
    static let ocCream        = Color(red: 0.969, green: 0.961, blue: 0.937) // #F7F5EF
    static let ocPressedLight = Color(red: 0.929, green: 0.922, blue: 0.890) // #EDEBE3
    static let ocDividerLight = Color(red: 0.894, green: 0.882, blue: 0.847) // #E4E1D8

    // MARK: - Canvas & Surfaces (Dark)
    static let ocCanvas   = Color(red: 0.071, green: 0.071, blue: 0.071) // #121212
    static let ocSurface1 = Color(red: 0.110, green: 0.110, blue: 0.118) // #1C1C1E
    static let ocSurface2 = Color(red: 0.165, green: 0.165, blue: 0.173) // #2A2A2C
    static let ocDividerDk = Color(red: 0.188, green: 0.188, blue: 0.196) // #303032

    // MARK: - Text
    static let ocTextPrimaryLt   = Color(red: 0.102, green: 0.102, blue: 0.102) // #1A1A1A
    static let ocTextSecondaryLt = Color(red: 0.431, green: 0.431, blue: 0.431) // #6E6E6E
    static let ocTextPrimaryDk   = Color(red: 0.949, green: 0.949, blue: 0.949) // #F2F2F2
    static let ocTextSecondaryDk = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A
    static let ocTextTertiary    = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A

    // MARK: - Semantic
    static let ocSuccess = Color(red: 0.204, green: 0.780, blue: 0.349) // #34C759
    static let ocError   = Color(red: 1.000, green: 0.231, blue: 0.188) // #FF3B30
    static let ocWarn    = Color(red: 1.000, green: 0.624, blue: 0.039) // #FF9F0A

    // MARK: - Playlist icon tiles
    static let ocPlAll      = Color.ocOrange                                 // #FC7E0F
    static let ocPlProgress = Color(red: 0.176, green: 0.498, blue: 0.976)   // #2D7FF9
    static let ocPlStarred  = Color(red: 0.204, green: 0.780, blue: 0.349)   // #34C759
    static let ocPlDownload = Color(red: 0.345, green: 0.337, blue: 0.839)   // #5856D6
}
```

## 2. Typography

Overcast uses the iOS system face (SF Pro) for native Dynamic Type. Timecodes and the time-saved stat use monospaced digits.

```swift
extension Font {
    static let ocScreenTitle = Font.system(size: 32, weight: .heavy,    design: .default)
    static let ocNowPlaying  = Font.system(size: 22, weight: .bold,     design: .default)
    static let ocSection     = Font.system(size: 22, weight: .bold,     design: .default)
    static let ocEpisode     = Font.system(size: 18, weight: .bold,     design: .default)
    static let ocRowTitle    = Font.system(size: 15, weight: .semibold, design: .default)
    static let ocBody        = Font.system(size: 16, weight: .regular,  design: .default)
    static let ocShowName    = Font.system(size: 14, weight: .medium,   design: .default)
    static let ocMeta        = Font.system(size: 14, weight: .regular,  design: .default)
    static let ocPlDesc      = Font.system(size: 12, weight: .regular,  design: .default)
    static let ocButton      = Font.system(size: 16, weight: .semibold, design: .default)
    static let ocToggle      = Font.system(size: 15, weight: .semibold, design: .default)
    static let ocChip        = Font.system(size: 10, weight: .semibold, design: .default)
    static let ocTab         = Font.system(size: 10, weight: .semibold, design: .default)
    static let ocCount       = Font.system(size: 14, weight: .semibold, design: .default)
    static let ocSkipLabel   = Font.system(size: 9,  weight: .bold,     design: .default)

    static let ocTimecode    = Font.system(size: 12, weight: .medium, design: .default)
        .monospacedDigit()
    /// "SAVED 1 HR 12 MIN WITH SMART SPEED" — apply .tracking(0.4) + .textCase(.uppercase)
    static let ocStatCaption = Font.system(size: 12, weight: .semibold, design: .default)
}
```

## 3. Signature Components

### Orange-Ring Play Button (the signature control)

```swift
struct OCPlayRing: View {
    @Binding var isPlaying: Bool
    var diameter: CGFloat = 66

    var body: some View {
        Button { isPlaying.toggle() } label: {
            ZStack {
                Circle()
                    .strokeBorder(Color.ocOrange, lineWidth: 2.5)
                    .frame(width: diameter, height: diameter)
                Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                    .font(.system(size: diameter * 0.36))
                    .foregroundStyle(Color.ocOrange)
            }
        }
        .buttonStyle(OCScaleStyle(0.96))
    }
}

struct OCScaleStyle: ButtonStyle {
    let scale: CGFloat
    init(_ scale: CGFloat = 0.98) { self.scale = scale }
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? scale : 1)
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}
```

### Skip Button (chevron + interval label)

```swift
struct OCSkipButton: View {
    let forward: Bool
    let seconds: Int
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            VStack(spacing: 2) {
                Image(systemName: forward ? "chevron.right.2" : "chevron.left.2")
                    .font(.system(size: 24, weight: .semibold))
                    .foregroundStyle(Color.ocTextPrimaryDk) // primary in current scheme
                Text("\(seconds)")
                    .font(.ocSkipLabel)
                    .foregroundStyle(Color.ocTextSecondaryDk)
            }
        }
        .buttonStyle(OCScaleStyle())
    }
}
```

### Now Playing Player

```swift
struct OCNowPlaying: View {
    @Environment(\.colorScheme) private var scheme
    let episodeTitle: String
    let showName: String
    @State private var progress: Double = 0.38
    @State private var isPlaying = true
    @State private var scrubbing = false

    private var canvas: Color { scheme == .dark ? .ocCanvas : .ocPaper }
    private var primary: Color { scheme == .dark ? .ocTextPrimaryDk : .ocTextPrimaryLt }
    private var secondary: Color { scheme == .dark ? .ocTextSecondaryDk : .ocTextSecondaryLt }
    private var track: Color { scheme == .dark ? .ocSurface2 : .ocDividerLight }

    var body: some View {
        VStack(spacing: 0) {
            HStack {
                Image(systemName: "chevron.left").font(.system(size: 20, weight: .semibold))
                    .foregroundStyle(Color.ocOrange)
                Spacer()
                Text("Now Playing").font(.system(size: 13, weight: .bold)).foregroundStyle(primary)
                Spacer()
                Image(systemName: "list.bullet").font(.system(size: 20, weight: .semibold))
                    .foregroundStyle(primary)
            }
            .padding(.horizontal, 18).padding(.vertical, 8)

            // Cover with orange bottom rule + soft shadow
            ZStack(alignment: .bottom) {
                RoundedRectangle(cornerRadius: 10)
                    .fill(LinearGradient(colors: [Color(red: 0.17, green: 0.23, blue: 0.33),
                                                  Color(red: 0.07, green: 0.10, blue: 0.18)],
                                         startPoint: .topLeading, endPoint: .bottomTrailing))
                    .aspectRatio(1, contentMode: .fit)
                Rectangle().fill(Color.ocOrange).frame(height: 5)
            }
            .clipShape(RoundedRectangle(cornerRadius: 10))
            .shadow(color: .black.opacity(scheme == .dark ? 0.7 : 0.18), radius: 40, x: 0, y: 18)
            .padding(.horizontal, 34).padding(.top, 6).padding(.bottom, 20)

            VStack(alignment: .leading, spacing: 4) {
                Text(episodeTitle).font(.ocNowPlaying).foregroundStyle(primary)
                Text(showName).font(.ocShowName).foregroundStyle(secondary)
            }
            .frame(maxWidth: .infinity, alignment: .leading)
            .padding(.horizontal, 34)

            // Scrubber
            VStack(spacing: 9) {
                GeometryReader { geo in
                    ZStack(alignment: .leading) {
                        Capsule().fill(track).frame(height: 3)
                        Capsule().fill(Color.ocOrange).frame(width: geo.size.width * progress, height: 3)
                        Circle().fill(Color.ocOrange)
                            .frame(width: scrubbing ? 18 : 14, height: scrubbing ? 18 : 14)
                            .shadow(color: .black.opacity(0.5), radius: 4, y: 1)
                            .offset(x: geo.size.width * progress - 7)
                    }
                    .frame(height: 18)
                    .contentShape(Rectangle())
                    .gesture(DragGesture()
                        .onChanged { v in scrubbing = true; progress = min(max(0, v.location.x / geo.size.width), 1) }
                        .onEnded { _ in scrubbing = false })
                }
                .frame(height: 18)
                HStack {
                    Text("48:12").font(.ocTimecode).foregroundStyle(secondary)
                    Spacer()
                    Text("-1:22:40").font(.ocTimecode).foregroundStyle(secondary)
                }
            }
            .padding(.horizontal, 34).padding(.top, 4)

            // Transport
            HStack {
                OCSkipButton(forward: false, seconds: 30) {}
                Spacer()
                OCPlayRing(isPlaying: $isPlaying)
                Spacer()
                OCSkipButton(forward: true, seconds: 30) {}
            }
            .padding(.horizontal, 50).padding(.vertical, 14)

            // Effect row
            HStack {
                effect("speedometer", "1.3×", active: true)
                Spacer()
                effect("waveform.path", "Smart Speed", active: true)
                Spacer()
                effect("speaker.wave.2.fill", "Voice Boost", active: true)
                Spacer()
                effect("star", "Star", active: false)
            }
            .padding(.horizontal, 26).padding(.bottom, 14)
            Spacer()
        }
        .background(canvas.ignoresSafeArea())
    }

    private func effect(_ icon: String, _ label: String, active: Bool) -> some View {
        VStack(spacing: 5) {
            Image(systemName: icon).font(.system(size: 19))
            Text(label).font(.ocChip)
        }
        .foregroundStyle(active ? Color.ocOrange : (scheme == .dark ? Color.ocTextSecondaryDk : Color.ocTextSecondaryLt))
    }
}
```

### Smart Speed / Voice Boost Toggle Row

```swift
struct OCEffectToggleRow: View {
    @Environment(\.colorScheme) private var scheme
    let title: String
    let subtitle: String
    @Binding var isOn: Bool

    var body: some View {
        HStack {
            VStack(alignment: .leading, spacing: 3) {
                Text(title).font(.ocToggle)
                    .foregroundStyle(scheme == .dark ? Color.ocTextPrimaryDk : Color.ocTextPrimaryLt)
                Text(subtitle).font(.ocPlDesc)
                    .foregroundStyle(scheme == .dark ? Color.ocTextSecondaryDk : Color.ocTextSecondaryLt)
            }
            Spacer()
            Toggle("", isOn: $isOn).labelsHidden().tint(Color.ocOrange)
        }
        .padding(.vertical, 15).padding(.horizontal, 16)
        .overlay(Divider().background(scheme == .dark ? Color.ocDividerDk : Color.ocDividerLight),
                 alignment: .bottom)
    }
}
```

### Playlist Row

```swift
struct OCPlaylistRow: View {
    @Environment(\.colorScheme) private var scheme
    let icon: String
    let tile: Color
    let name: String
    let desc: String
    let count: Int

    var body: some View {
        HStack(spacing: 14) {
            RoundedRectangle(cornerRadius: 9).fill(tile)
                .frame(width: 38, height: 38)
                .overlay(Image(systemName: icon).font(.system(size: 18, weight: .semibold)).foregroundStyle(.white))
            VStack(alignment: .leading, spacing: 2) {
                Text(name).font(.ocRowTitle)
                    .foregroundStyle(scheme == .dark ? Color.ocTextPrimaryDk : Color.ocTextPrimaryLt)
                Text(desc).font(.ocPlDesc)
                    .foregroundStyle(scheme == .dark ? Color.ocTextSecondaryDk : Color.ocTextSecondaryLt)
                    .lineLimit(1)
            }
            Spacer()
            Text("\(count)").font(.ocCount)
                .foregroundStyle(scheme == .dark ? Color.ocTextSecondaryDk : Color.ocTextSecondaryLt)
        }
        .padding(.vertical, 14).padding(.horizontal, 16)
        .overlay(Divider().background(scheme == .dark ? Color.ocDividerDk : Color.ocDividerLight),
                 alignment: .bottom)
    }
}
```

### Buttons

```swift
struct OCPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.ocButton).foregroundStyle(.white)
                .padding(.vertical, 14).padding(.horizontal, 26)
                .background(RoundedRectangle(cornerRadius: 10).fill(Color.ocOrange))
        }
        .buttonStyle(OCScaleStyle())
    }
}

struct OCOutlineButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title).font(.system(size: 15, weight: .semibold))
                .foregroundStyle(Color.ocOrange)
                .padding(.vertical, 12).padding(.horizontal, 22)
                .overlay(RoundedRectangle(cornerRadius: 10).strokeBorder(Color.ocOrange, lineWidth: 1.5))
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct OCTabView: View {
    var body: some View {
        TabView {
            PlaylistsView().tabItem { Label("Playlists", systemImage: "list.bullet") }
            PodcastsView().tabItem { Label("Podcasts", systemImage: "square.grid.2x2") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            SettingsView().tabItem { Label("Settings", systemImage: "gearshape") }
        }
        .tint(.ocOrange) // single accent; no tint pill (UITabBar default)
    }
}
```

## 5. Motion

```swift
// Play/pause + ring press
.buttonStyle(OCScaleStyle(0.96)) // scale 0.96, 120ms ease-out

// Scrubber knob grow
.frame(width: scrubbing ? 18 : 14, height: scrubbing ? 18 : 14)
.animation(.easeOut(duration: 0.12), value: scrubbing)

// Toggle flip — Toggle(...).tint(.ocOrange) animates track cross-fade implicitly

// Mini-player -> Full player
.transition(.move(edge: .bottom)) // present with .easeOut(duration: 0.3)

// Theme switch — cross-fade via .animation(.easeInOut(duration: 0.25), value: colorScheme)

// Haptics
let soft = UIImpactFeedbackGenerator(style: .soft)
soft.impactOccurred() // play, toggle flip, scrubber boundary, recommend
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Playlists (tab) | `list.bullet` | 22pt |
| Podcasts (tab) | `square.grid.2x2` / `.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Settings (tab) | `gearshape` / `.fill` | 22pt |
| Play | `play.fill` | 24pt |
| Pause | `pause.fill` | 24pt |
| Skip back | `chevron.left.2` | 24pt |
| Skip forward | `chevron.right.2` | 24pt |
| Smart Speed | `waveform.path` | 19pt |
| Voice Boost | `speaker.wave.2.fill` | 19pt |
| Playback speed | `speedometer` | 19pt |
| Star | `star` / `star.fill` | 19pt |
| In Progress (playlist) | `clock.arrow.circlepath` | 18pt |
| Downloaded | `arrow.down.circle.fill` | 18pt |
| Collapse player | `chevron.left` | 20pt |
| Queue / list | `list.bullet` | 20pt |
| Recommend | `hand.thumbsup` / `.fill` | 18pt |
| More | `ellipsis` | 18pt |
| Add podcast | `plus` | 18pt |

## 7. Dark Mode

```swift
struct OCTheme: ViewModifier {
    @Environment(\.colorScheme) var scheme
    func body(content: Content) -> some View {
        content
            .background(scheme == .dark ? Color.ocCanvas : Color.ocPaper)
            .foregroundStyle(scheme == .dark ? Color.ocTextPrimaryDk : Color.ocTextPrimaryLt)
    }
}
extension View { func ocTheme() -> some View { modifier(OCTheme()) } }
```

Overcast ships a warm paper-cream light theme (`#FBFAF6` canvas, `#F7F5EF` surfaces — never stark white) and a true dark theme (`#121212` canvas, `#1C1C1E` surfaces). Overcast Orange `#FC7E0F` is the single accent and is identical across themes. The play button is an outlined orange ring in both. On the paper theme, shadows are gentle (0.18 opacity) so they don't muddy the warm canvas; on dark they deepen to 0.7.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.monospacedDigit()` Font modifier iOS 15+; `Toggle.tint` iOS 15+; `chevron.left.2`/`chevron.right.2` symbols iOS 16+ — fall back to `chevron.left`/`chevron.right` doubled)
- System font (SF Pro) — no bundled fonts required; `Inter` is only a web/spec fallback
- Dynamic Type: support on screen titles, episode titles, body, metadata; keep tab labels, effect chips, timecodes, playlist counts, skip-interval labels at FIXED sizes
- Use `.monospacedDigit()` on every timecode/duration and the Smart Speed time-saved stat
- VoiceOver: label the play ring "Play" / "Pause, {episode}"; the scrubber as an `.accessibilityValue` percentage with `.accessibilityAdjustableAction` for the skip intervals; playlist rows as "Playlist: {name}, {count} episodes, {description}"
- The orange ring must keep ≥3:1 contrast against both `#FBFAF6` and `#121212` (it does at `#FC7E0F`)
- Touch targets: play ring 66pt, skip buttons 44pt hit, playlist rows full-row ≥56pt, tab icons 44pt hit
- Reduce Motion: disable the scrubber-knob grow, the play/pause morph, and the theme cross-fade; keep state changes
- Reduce Transparency: replace blurred tab bars with solid `#FBFAF6` / `#121212`
- Color: never rely on orange alone for active effects — pair the Smart Speed / Voice Boost ON state with the labeled toggle and the time-saved stat for color-blind users
