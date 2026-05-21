# Deezer (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Deezer's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the living-gradient Flow artwork, the embedded equalizer, the circular gradient play button, the gradient scrubber, and the now-playing-aware song row.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (Dark — the only real theme)
    static let dzCanvas      = Color(red: 0.059, green: 0.051, blue: 0.075) // #0F0D13
    static let dzSurface1    = Color(red: 0.098, green: 0.086, blue: 0.122) // #19161F
    static let dzSurface2    = Color(red: 0.133, green: 0.118, blue: 0.169) // #221E2B
    static let dzDivider     = Color(red: 0.165, green: 0.149, blue: 0.200) // #2A2633

    // MARK: - Text
    static let dzTextPrimary   = Color.white                                  // #FFFFFF
    static let dzTextSecondary = Color(red: 0.635, green: 0.612, blue: 0.690) // #A29CB0
    static let dzTextTertiary  = Color(red: 0.431, green: 0.408, blue: 0.502) // #6E6880

    // MARK: - Brand (the gradient + its endpoints)
    static let dzPurple     = Color(red: 0.635, green: 0.220, blue: 1.000) // #A238FF
    static let dzPink       = Color(red: 1.000, green: 0.000, blue: 0.573) // #FF0092
    static let dzPurpleDeep = Color(red: 0.486, green: 0.157, blue: 0.769) // #7C28C4
    static let dzPinkPress  = Color(red: 0.839, green: 0.000, blue: 0.475) // #D60079
    static let dzArtMagenta = Color(red: 0.780, green: 0.122, blue: 0.557) // #C71F8E

    // MARK: - Semantic
    static let dzSuccess = Color(red: 0.118, green: 0.843, blue: 0.376) // #1ED760
    static let dzError   = Color(red: 1.000, green: 0.302, blue: 0.369) // #FF4D5E
    static let dzWarning = Color(red: 1.000, green: 0.690, blue: 0.180) // #FFB02E
}

// The Deezer signature gradient — used ONLY on alive elements
extension LinearGradient {
    static let dzFlow = LinearGradient(
        colors: [.dzPurple, .dzPink],
        startPoint: .leading, endPoint: .trailing
    )
    static let dzPlayButton = LinearGradient(
        colors: [.dzPurple, .dzPink],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
    static let dzArtwork = LinearGradient(
        stops: [
            .init(color: .dzPurple,     location: 0.0),
            .init(color: .dzArtMagenta, location: 0.45),
            .init(color: .dzPink,       location: 1.0),
        ],
        startPoint: .topLeading, endPoint: .bottomTrailing
    )
}
```

## 2. Typography

Deezer ships **Deezer Sans**; use **Inter** as the faithful fallback. Bundle both via `Info.plist` / `UIAppFonts`. Body/metadata uses Inter at 400/500; titles use the brand face at 700–800.

```swift
enum DZFont {
    // Swap "DeezerSans" → "Inter" if the brand face isn't bundled.
    static let brand = "DeezerSans"
    static let read  = "Inter"
}

extension Font {
    static func dzBrand(_ size: CGFloat, _ weight: Font.Weight = .bold) -> Font {
        Font.custom(DZFont.brand, size: size).weight(weight)
    }
    static func dzRead(_ size: CGFloat, _ weight: Font.Weight = .regular) -> Font {
        Font.custom(DZFont.read, size: size).weight(weight)
    }

    static let dzScreenTitle    = Font.custom(DZFont.brand, size: 32).weight(.heavy)   // 800
    static let dzNowPlaying     = Font.custom(DZFont.brand, size: 26).weight(.heavy)   // 800
    static let dzSection        = Font.custom(DZFont.brand, size: 22).weight(.bold)    // 700
    static let dzSubhead        = Font.custom(DZFont.brand, size: 18).weight(.bold)    // 700
    static let dzBody           = Font.custom(DZFont.read,  size: 16).weight(.regular) // 400
    static let dzRowTitle       = Font.custom(DZFont.brand, size: 15).weight(.semibold)// 600
    static let dzNowArtist      = Font.custom(DZFont.read,  size: 15).weight(.medium)  // 500
    static let dzMeta           = Font.custom(DZFont.read,  size: 14).weight(.regular) // 400
    static let dzOverline       = Font.custom(DZFont.brand, size: 12).weight(.bold)    // 700, UPPERCASE
    static let dzScrubTime      = Font.custom(DZFont.read,  size: 11).weight(.medium)  // 500, tabular
    static let dzTabLabel       = Font.custom(DZFont.brand, size: 10).weight(.semibold)// 600
    static let dzButton         = Font.custom(DZFont.brand, size: 16).weight(.bold)    // 700
    static let dzChip           = Font.custom(DZFont.brand, size: 13).weight(.bold)    // 700
}

// Tabular numerals for all timestamps
extension View {
    func dzTabularNumbers() -> some View { self.monospacedDigit() }
}
```

## 3. Signature Components

### Living-Gradient Flow Artwork (with FLOW badge + embedded equalizer)

```swift
struct FlowArtwork: View {
    var isPlaying: Bool = true
    @State private var drift = false

    // static design heights; replace with live FFT data when playing
    private let bars: [CGFloat] = [0.38,0.62,0.88,0.54,0.76,0.42,0.68,0.92,0.50,0.72,0.34,0.60]

    var body: some View {
        GeometryReader { geo in
            ZStack(alignment: .topLeading) {
                LinearGradient.dzArtwork
                    .overlay(
                        RadialGradient(colors: [.white.opacity(0.22), .clear],
                                       center: .init(x: 0.78, y: 0.22),
                                       startRadius: 0, endRadius: geo.size.width * 0.6)
                    )
                    .overlay(
                        RadialGradient(colors: [Color.dzPurpleDeep.opacity(0.55), .clear],
                                       center: .init(x: 0.20, y: 0.82),
                                       startRadius: 0, endRadius: geo.size.width * 0.6)
                    )
                    .hueRotation(.degrees(drift ? 8 : 0))

                // FLOW badge
                HStack(spacing: 7) {
                    Image(systemName: "bolt.fill").font(.system(size: 12)).foregroundStyle(.white)
                    Text("FLOW").font(.dzOverline).tracking(0.4).foregroundStyle(.white)
                }
                .padding(.vertical, 7).padding(.leading, 11).padding(.trailing, 13)
                .background(Color.dzCanvas.opacity(0.55))
                .clipShape(Capsule())
                .background(.ultraThinMaterial, in: Capsule())
                .padding(16)

                // Embedded equalizer
                HStack(alignment: .bottom, spacing: 4) {
                    ForEach(bars.indices, id: \.self) { i in
                        RoundedRectangle(cornerRadius: 3)
                            .fill(Color.white.opacity(0.62))
                            .frame(height: geo.size.height * 0.46 * bars[i])
                    }
                }
                .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .bottom)
                .padding(.horizontal, 18).padding(.bottom, 22)
            }
            .clipShape(RoundedRectangle(cornerRadius: 16))
            .shadow(color: Color.dzPurple.opacity(0.55), radius: 24, x: 0, y: 24)
        }
        .aspectRatio(1, contentMode: .fit)
        .onAppear {
            guard isPlaying else { return }
            withAnimation(.easeInOut(duration: 12).repeatForever(autoreverses: true)) { drift = true }
        }
    }
}
```

### Circular Gradient Play Button

```swift
struct PlayButton: View {
    @Binding var isPlaying: Bool

    var body: some View {
        Button {
            isPlaying.toggle()
        } label: {
            ZStack {
                Circle().fill(LinearGradient.dzPlayButton)
                Image(systemName: isPlaying ? "pause.fill" : "play.fill")
                    .font(.system(size: 26, weight: .bold))
                    .foregroundStyle(.white)
                    .offset(x: isPlaying ? 0 : 2)   // optical centering for ▶
            }
            .frame(width: 68, height: 68)
            .shadow(color: Color.dzPink.opacity(0.6), radius: 14, x: 0, y: 12)
        }
        .buttonStyle(PressScale())
        .sensoryFeedback(.impact(flexibility: .soft), trigger: isPlaying)
    }
}

struct PressScale: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.94 : 1)
            .animation(.easeOut(duration: 0.12), value: configuration.isPressed)
    }
}
```

### Gradient Scrubber

```swift
struct GradientScrubber: View {
    @Binding var progress: Double      // 0...1
    let elapsed: String                // "1:48"
    let remaining: String              // "-2:31"
    @State private var dragging = false

    var body: some View {
        VStack(spacing: 9) {
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.dzSurface2).frame(height: 4)
                    Capsule().fill(LinearGradient.dzFlow)
                        .frame(width: geo.size.width * progress, height: 4)
                    Circle().fill(.white)
                        .frame(width: dragging ? 17 : 13, height: dragging ? 17 : 13)
                        .shadow(color: .black.opacity(0.5), radius: 3, y: 2)
                        .offset(x: geo.size.width * progress - (dragging ? 8.5 : 6.5))
                }
                .frame(height: 17)
                .contentShape(Rectangle())
                .gesture(
                    DragGesture(minimumDistance: 0)
                        .onChanged { v in
                            dragging = true
                            progress = min(max(0, v.location.x / geo.size.width), 1)
                        }
                        .onEnded { _ in dragging = false }
                )
            }
            .frame(height: 17)

            HStack {
                Text(elapsed).font(.dzScrubTime).dzTabularNumbers()
                Spacer()
                Text(remaining).font(.dzScrubTime).dzTabularNumbers()
            }
            .foregroundStyle(Color.dzTextSecondary)
        }
    }
}
```

### Now-Playing-Aware Song Row

```swift
struct SongRow: View {
    let title: String
    let artist: String
    let isPlaying: Bool
    var artworkGradient: LinearGradient = .dzArtwork

    var body: some View {
        HStack(spacing: 12) {
            RoundedRectangle(cornerRadius: 6).fill(artworkGradient)
                .frame(width: 44, height: 44)

            VStack(alignment: .leading, spacing: 2) {
                Text(title)
                    .font(.dzRowTitle)
                    .foregroundStyle(isPlaying ? Color.dzPink : Color.dzTextPrimary)
                    .animation(.easeOut(duration: 0.18), value: isPlaying)
                Text(artist).font(.dzMeta).foregroundStyle(Color.dzTextSecondary)
            }
            Spacer()

            if isPlaying {
                EqualizerMark()
            } else {
                Image(systemName: "ellipsis").font(.system(size: 18))
                    .foregroundStyle(Color.dzTextSecondary)
            }
        }
        .frame(height: 64)
        .contentShape(Rectangle())
    }
}

struct EqualizerMark: View {
    @State private var animate = false
    private let base: [CGFloat] = [7, 14, 5, 11]
    var body: some View {
        HStack(alignment: .bottom, spacing: 2) {
            ForEach(base.indices, id: \.self) { i in
                RoundedRectangle(cornerRadius: 1)
                    .fill(Color.dzPink)
                    .frame(width: 3, height: animate ? base[i] : base[(i + 1) % base.count])
            }
        }
        .frame(height: 16)
        .onAppear {
            withAnimation(.easeInOut(duration: 0.5).repeatForever(autoreverses: true)) { animate = true }
        }
    }
}
```

### Now Playing Top Bar + Primary Button

```swift
struct NowPlayingTopBar: View {
    var body: some View {
        HStack {
            Image(systemName: "chevron.down").font(.system(size: 22, weight: .bold))
                .foregroundStyle(Color.dzTextPrimary)
            Spacer()
            VStack(spacing: 2) {
                Text("FLOW · YOUR MIX").font(.dzOverline).tracking(0.4)
                    .foregroundStyle(Color.dzTextSecondary)
                Text("Made for you").font(.dzRead(13, .bold))
                    .foregroundStyle(Color.dzTextPrimary)
            }
            Spacer()
            Image(systemName: "ellipsis").font(.system(size: 22, weight: .bold))
                .foregroundStyle(Color.dzTextPrimary)
        }
    }
}

struct PrimaryButton: View {
    let title: String
    var action: () -> Void = {}
    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill").font(.system(size: 14, weight: .bold))
                Text(title).font(.dzButton)
            }
            .foregroundStyle(.white)
            .padding(.vertical, 14).padding(.horizontal, 30)
            .background(LinearGradient.dzFlow, in: Capsule())
        }
        .buttonStyle(PressScale())
    }
}
```

## 4. Bottom Tab Bar

```swift
struct DZTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem    { Label("Home",    systemImage: "house.fill") }
            SearchView().tabItem  { Label("Search",  systemImage: "magnifyingglass") }
            MusicView().tabItem   { Label("Music",   systemImage: "music.note.list") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.fill") }
        }
        .tint(.dzPurple)   // active = Deezer purple; inactive defaults to secondary
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.dzCanvas).withAlphaComponent(0.94)
            a.shadowColor = UIColor(Color.dzDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
            UITabBar.appearance().unselectedItemTintColor = UIColor(Color.dzTextTertiary)
        }
    }
}
```

## 5. Motion

```swift
// Play/pause — glyph crossfade + scale, soft haptic
.scaleEffect(isPressed ? 0.94 : 1)
.animation(.easeOut(duration: 0.12), value: isPressed)
.sensoryFeedback(.impact(flexibility: .soft), trigger: isPlaying)

// Player expand/collapse (mini → full)
withAnimation(.spring(response: 0.32, dampingFraction: 0.82)) { expanded.toggle() }

// Living gradient drift (only while Flow plays)
withAnimation(.easeInOut(duration: 12).repeatForever(autoreverses: true)) { drift = true }

// Equalizer bars — continuous while playing; STOP (no animation) on pause
// Reduce Motion: gate the repeatForever behind !reduceMotion

// Song-row activation
.foregroundStyle(isPlaying ? Color.dzPink : Color.dzTextPrimary)
.animation(.easeOut(duration: 0.18), value: isPlaying)

// Skip / next — artwork slide + metadata crossfade
.transition(.asymmetric(insertion: .move(edge: .trailing), removal: .opacity))
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Collapse player | `chevron.down` | 22pt |
| Overflow menu | `ellipsis` | 22pt |
| Play | `play.fill` | 26pt |
| Pause | `pause.fill` | 26pt |
| Previous | `backward.fill` | 26pt |
| Next | `forward.fill` | 26pt |
| Shuffle | `shuffle` | 22pt |
| Repeat | `repeat` | 22pt |
| Favorite (filled) | `heart.fill` | 24pt |
| FLOW badge | `bolt.fill` | 12pt |
| Home (tab) | `house` / `house.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Music (tab) | `music.note.list` | 22pt |
| Profile (tab) | `person` / `person.fill` | 22pt |
| Search field | `magnifyingglass` | 18pt |
| Voice search | `mic.fill` | 18pt |
| Download | `arrow.down.circle` | 20pt |
| Add to favorites | `plus` | 18pt |

## 7. Dark Mode

Deezer is dark-native — there is no real light theme. Pin the dark palette regardless of system setting; only honor a forced light environment with the minimal fallback in DESIGN.md §2.

```swift
struct DZTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.dzCanvas)
            .foregroundStyle(Color.dzTextPrimary)
            .preferredColorScheme(.dark)   // force dark — Deezer's identity
    }
}
extension View { func dzTheme() -> some View { modifier(DZTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator(style: .soft)`)
- Bundle Deezer Sans if licensed; otherwise ship **Inter** (SIL OFL, free) as the faithful fallback — never silently degrade to system-only for titles
- Dynamic Type: scale screen/now-playing titles, section headers, body, row text; keep tab labels, scrubber time, overlines, FLOW badge, chip text FIXED (layout-sensitive)
- VoiceOver: label the play button "Play" / "Pause"; the scrubber as an `.accessibilityElement` with `.adjustableAction` (swipe up/down = seek ±5s), value announced as elapsed time; the FLOW artwork as "Flow, your personalized mix, now playing {track}"
- The equalizer is decorative — mark it `.accessibilityHidden(true)`; the now-playing state must ALSO be conveyed non-visually (VoiceOver "Now playing" trait on the active row), not by pink color alone
- Color contrast: `#FFFFFF` on `#0F0D13` and white on the gradient both pass WCAG AA; `#A29CB0` secondary on `#0F0D13` passes AA for ≥14pt text
- Reduce Motion: disable the living-gradient drift, the equalizer animation (show static bars), and the artwork slide on skip — keep play/pause crossfade (state-critical)
- Reduce Transparency: replace the tab-bar / FLOW-badge blur with solid `#0F0D13` / `rgba(15,13,19,0.85)`
- Never animate the equalizer while paused — it must visually freeze when playback stops (both an accuracy and a Reduce-Motion concern)
