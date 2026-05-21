# Raya (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Raya's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (permanently dark — the only theme)
    static let rayaCanvas   = Color.black                                     // #000000 (pure)
    static let rayaSurface1 = Color(red: 0.051, green: 0.051, blue: 0.051)   // #0D0D0D
    static let rayaSurface2 = Color(red: 0.086, green: 0.086, blue: 0.086)   // #161616
    static let rayaDivider  = Color(red: 0.141, green: 0.141, blue: 0.141)   // #242424
    static let rayaHairline = Color(red: 0.180, green: 0.180, blue: 0.180)   // #2E2E2E

    // MARK: - Text
    static let rayaTextPrimary   = Color.white                                // #FFFFFF
    static let rayaTextSecondary = Color(red: 0.604, green: 0.604, blue: 0.604) // #9A9A9A
    static let rayaTextTertiary  = Color(red: 0.369, green: 0.369, blue: 0.369) // #5E5E5E

    // MARK: - Accent (there is, by design, only one)
    static let rayaAccent        = Color.white                                // #FFFFFF
    static let rayaAccentPressed = Color(red: 0.847, green: 0.847, blue: 0.847) // #D8D8D8

    // MARK: - Semantic (used reluctantly)
    static let rayaError = Color(red: 0.898, green: 0.282, blue: 0.302)       // #E5484D
}

// On-photo text + scrim
extension Color {
    static let rayaOnPhoto    = Color.white.opacity(0.92)
    static let rayaOnPhotoDim = Color.white.opacity(0.6)
}
extension LinearGradient {
    static let rayaScrim = LinearGradient(
        stops: [
            .init(color: .clear, location: 0.0),
            .init(color: .black.opacity(0.55), location: 0.55),
            .init(color: .black.opacity(0.92), location: 1.0),
        ],
        startPoint: .top, endPoint: .bottom)
}
```

## 2. Typography

Raya uses a clean low-contrast neo-grotesque (Inter/Söhne family). Bundle `Inter` (SIL OFL) via `Info.plist` / `UIAppFonts` as the closest free substitute. One family does everything — the typographic mirror of the one-color rule.

```swift
extension Font {
    static func raya(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        let face: String = {
            switch weight {
            case .semibold, .bold: return "Inter-SemiBold"
            case .medium:          return "Inter-Medium"
            case .light:           return "Inter-Light"
            default:               return "Inter-Regular"
            }
        }()
        return Font.custom(face, size: size)
    }

    static let rayaWordmark    = Font.custom("Inter-SemiBold", size: 30)
    static let rayaProfileName = Font.custom("Inter-SemiBold", size: 28)
    static let rayaSection     = Font.custom("Inter-SemiBold", size: 20)
    static let rayaSubtitle    = Font.custom("Inter-Medium",   size: 16)
    static let rayaBody        = Font.custom("Inter-Regular",  size: 15)
    static let rayaRole        = Font.custom("Inter-Medium",   size: 13)
    static let rayaMusic       = Font.custom("Inter-Medium",   size: 11)
    static let rayaMeta        = Font.custom("Inter-Regular",  size: 13)
    static let rayaEyebrow     = Font.custom("Inter-SemiBold", size: 11)
    static let rayaButton      = Font.custom("Inter-SemiBold", size: 14)
    static let rayaCaption     = Font.custom("Inter-Regular",  size: 12)
    static let rayaTab         = Font.custom("Inter-Medium",   size: 10)
}

extension View {
    func rayaUppercase(_ tracking: CGFloat) -> some View {
        self.textCase(.uppercase).tracking(tracking)
    }
}
```

## 3. Signature Components

### Slideshow Profile

```swift
struct SlideshowProfile: View {
    let slides: [String]          // image/video asset names
    let activeIndex: Int
    let progress: Double          // 0...1 within the active slide
    let name: String
    let role: String              // "Director · Film & Music"
    let bio: String
    let location: String
    let artist: String
    let track: String

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            Image(slides[activeIndex])
                .resizable().aspectRatio(contentMode: .fill)
                .ignoresSafeArea()

            LinearGradient.rayaScrim.ignoresSafeArea()

            VStack(alignment: .leading, spacing: 12) {
                // Story bars
                HStack(spacing: 5) {
                    ForEach(slides.indices, id: \.self) { i in
                        GeometryReader { geo in
                            ZStack(alignment: .leading) {
                                Capsule().fill(Color.white.opacity(0.3))
                                Capsule().fill(Color.white)
                                    .frame(width: i < activeIndex ? geo.size.width
                                                 : i == activeIndex ? geo.size.width * progress : 0)
                            }
                        }
                        .frame(height: 2.5)
                    }
                }
                // Music ticker
                HStack(spacing: 8) {
                    Image(systemName: "music.note")
                        .font(.system(size: 13)).foregroundStyle(.white)
                    Text("\(artist) — \(track)")
                        .font(.rayaMusic).foregroundStyle(.white.opacity(0.85))
                    Spacer()
                    Equalizer()
                }
                Spacer()
            }
            .padding(.horizontal, 16)
            .padding(.top, 6)

            // Caption
            VStack(alignment: .leading, spacing: 8) {
                Text(name).font(.rayaProfileName).foregroundStyle(.white)
                Text(role).font(.rayaRole).foregroundStyle(.rayaOnPhoto).rayaUppercase(0.4)
                Text(bio).font(.rayaBody).foregroundStyle(.white.opacity(0.72))
                HStack(spacing: 6) {
                    Image(systemName: "mappin.and.ellipse")
                        .font(.system(size: 12)).foregroundStyle(.rayaOnPhotoDim)
                    Text(location).font(.rayaMeta).foregroundStyle(.rayaOnPhotoDim)
                }
            }
            .padding(.horizontal, 20)
            .padding(.bottom, 96)
        }
        .background(Color.rayaCanvas)
    }
}

struct Equalizer: View {
    @State private var phase = false
    let heights: [CGFloat] = [0.4, 0.9, 0.6, 1.0]
    var body: some View {
        HStack(alignment: .bottom, spacing: 2) {
            ForEach(0..<4, id: \.self) { i in
                Capsule().fill(Color.white)
                    .frame(width: 2, height: 11 * (phase ? heights[i] : heights[3 - i]))
            }
        }
        .frame(height: 11)
        .onAppear {
            withAnimation(.easeInOut(duration: 0.6).repeatForever(autoreverses: true)) { phase = true }
        }
    }
}
```

### Action Row

```swift
struct SlideshowActions: View {
    var onSkip: () -> Void = {}
    var onLike: () -> Void = {}
    var onNote: () -> Void = {}
    @State private var heartFilled = false
    @State private var heartScale: CGFloat = 1

    var body: some View {
        HStack(spacing: 56) {
            Button(action: onSkip) {
                Image(systemName: "xmark")
                    .font(.system(size: 26, weight: .light))
                    .foregroundStyle(.white.opacity(0.9))
            }
            Button {
                withAnimation(.easeOut(duration: 0.2)) { heartFilled = true; heartScale = 1.1 }
                UIImpactFeedbackGenerator(style: .soft).impactOccurred()
                onLike()
            } label: {
                Image(systemName: heartFilled ? "heart.fill" : "heart")
                    .font(.system(size: 24, weight: .light))
                    .foregroundStyle(.white)
                    .frame(width: 60, height: 60)
                    .overlay(Circle().strokeBorder(Color.white, lineWidth: 1))
                    .scaleEffect(heartScale)
            }
            Button(action: onNote) {
                Image(systemName: "bubble.left")
                    .font(.system(size: 24, weight: .light))
                    .foregroundStyle(.white.opacity(0.9))
            }
        }
        .padding(.bottom, 18)
    }
}
```

### Primary Button

```swift
struct RayaPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.rayaButton).tracking(0.3)
                .foregroundStyle(.black)
                .frame(maxWidth: .infinity).padding(.vertical, 15)
                .background(Color.white)
                .clipShape(Capsule())
        }
        .buttonStyle(.plain)
    }
}

struct RayaOutlineButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.rayaButton).tracking(0.3)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity).padding(.vertical, 14)
                .overlay(Capsule().strokeBorder(Color.white, lineWidth: 1))
        }
        .buttonStyle(.plain)
    }
}
```

### Member Chip

```swift
struct MemberChip: View {
    let label: String
    let selected: Bool
    var body: some View {
        Text(label)
            .font(.raya(12, weight: .medium))
            .foregroundStyle(selected ? .black : .white)
            .padding(.horizontal, 16).padding(.vertical, 8)
            .background(selected ? Color.white : Color.clear, in: Capsule())
            .overlay(Capsule().strokeBorder(Color.rayaHairline, lineWidth: selected ? 0 : 1))
    }
}
```

### Music Ticker (standalone)

```swift
struct MusicTicker: View {
    let artist: String
    let track: String
    var body: some View {
        HStack(spacing: 8) {
            Image(systemName: "opticaldisc").font(.system(size: 13)).foregroundStyle(.white)
            Text("\(artist) — \(track)")
                .font(.rayaMusic).foregroundStyle(.white.opacity(0.85))
            Equalizer()
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
// Shown only on Messages / Profile — Discover is chromeless slideshow
struct RayaTabView: View {
    var body: some View {
        TabView {
            DiscoverView().tabItem { Label("Discover", systemImage: "square.stack") }
            MessagesView().tabItem { Label("Messages", systemImage: "bubble.left") }
            ProfileView().tabItem { Label("Profile", systemImage: "person") }
        }
        .tint(.white) // active = white; no pill indicator
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = .black // true black — no blur
            a.shadowColor = UIColor(red: 0.180, green: 0.180, blue: 0.180, alpha: 1) // 1px hairline
            let inactive = UIColor(red: 0.369, green: 0.369, blue: 0.369, alpha: 1)
            a.stackedLayoutAppearance.normal.iconColor = inactive
            a.stackedLayoutAppearance.normal.titleTextAttributes = [.foregroundColor: inactive]
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Slideshow auto-advance — active bar fills, then cross-fade
// Drive `progress` 0→1 with a TimelineView/Timer over the slide duration
withAnimation(.linear(duration: slideDuration)) { progress = 1 }
// on complete: advance index, reset progress, .transition(.opacity) cross-fade (250ms)

// Story-bar scrub — instant cut on tap
.onTapGesture { location in
    if location.x > screenWidth * 0.66 { nextSlide() }       // right third
    else if location.x < screenWidth * 0.33 { prevSlide() }  // left third
}
.onLongPressGesture(minimumDuration: 0.2,
    pressing: { isPressing in isPaused = isPressing }, perform: {})

// Music equalizer — looping height animation (see Equalizer)
// Track change: text .transition(.opacity), 180ms

// Caption entrance — fade + rise 8pt when a member's first slide loads
.transition(.opacity.combined(with: .move(edge: .bottom)))
withAnimation(.easeOut(duration: 0.3)) { captionVisible = true }

// Like — outline → fill, gentle pulse, then next member cross-fades (no color burst)
withAnimation(.easeOut(duration: 0.2)) { heartFilled = true; heartScale = 1.1 }
UIImpactFeedbackGenerator(style: .soft).impactOccurred()

// Wordmark cold-open — fade in, hold, fade out
// opacity 0→1 (400ms) → hold 1.1s → 1→0 (400ms) → reveal slideshow

// Tab switch — instant white tint cross-fade (no pill slide)

// Haptics — restrained, like the palette
UIImpactFeedbackGenerator(style: .soft).impactOccurred()  // like, scrub
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Discover (tab) | `square.stack` / `square.stack.fill` | 22pt |
| Messages (tab) | `bubble.left` / `bubble.left.fill` | 22pt |
| Profile (tab) | `person` / `person.fill` | 22pt |
| Like (action) | `heart` → `heart.fill` | 24pt |
| Skip (action) | `xmark` | 26pt |
| Note (action) | `bubble.left` | 24pt |
| Music ticker | `music.note` / `opticaldisc` | 13pt |
| Location pin | `mappin.and.ellipse` | 12pt |
| Profile actions | `ellipsis` | 20pt |
| Send (message) | `arrow.up` (or text "SEND") | 20pt |
| Settings | `gearshape` | 22pt |
| Back | `chevron.left` | 20pt |
| Pause indicator | `pause.fill` | 14pt |

## 7. Dark Mode

```swift
// Raya is permanently dark — there is no light variant, by design.
struct RayaTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.rayaCanvas)       // pure #000000
            .foregroundStyle(Color.rayaTextPrimary)
            .tint(Color.rayaAccent)             // the only accent — white
            .environment(\.colorScheme, .dark)  // never render light
    }
}
extension View { func rayaTheme() -> some View { modifier(RayaTheme()) } }
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` is iOS 17 — examples use `UIImpactFeedbackGenerator` for iOS 16; `TimelineView` for slide progress is iOS 15+)
- Bundle Inter TTFs via `Info.plist` `UIAppFonts` — SIL OFL, free to redistribute (substitute for Raya's custom neo-grotesque)
- Raya is permanently dark: force `.environment(\.colorScheme, .dark)`; never author a light variant — there is no light mode by design
- Dynamic Type: scale profile name, bio, section titles, message body; keep story-bar text, the music ticker, the role line, eyebrows, and tab labels at FIXED sizes (layout-sensitive over media)
- VoiceOver: the slideshow is one element announcing "{name}, {role}, {bio}, {location}, slide {i} of {n}, playing {artist} — {track}"; expose next/previous slide and pause as custom/adjustable actions; action buttons get explicit labels ("Skip", "Like", "Send a note"); the equalizer is decorative — mark `.accessibilityHidden(true)`
- Reduced motion sensitivity: the auto-advancing slideshow should respect a "tap to advance" preference; never force motion on users who pause
- Color contrast: `#FFFFFF` on `#000000` is maximum contrast; on-photo caption text uses `rgba(255,255,255,0.92)` on a `→ rgba(0,0,0,0.92)` scrim — verify the scrim is dark enough under bright member photos (deepen the gradient if a slide is high-key)
- The 1px `#2E2E2E` hairline is the only depth cue — ensure it is never the sole carrier of meaning for screen-reader users (pair structural separation with labels)
- Reduce Motion: replace slide cross-fades with instant cuts, freeze the equalizer at a static mid-state, skip the wordmark cold-open animation, and disable the like pulse — keep the soft haptic
- Hit targets: Heart 60pt; Skip / Note 44pt tap area (even though the glyph is borderless); story-bar scrub spans full left/right thirds; music ticker ≥ 44pt
- No shadows / no color: never add `.shadow(...)`, never introduce a tint — separation is the faint surface lift + hairline; the only color in the app is the member's media, which must never be filtered or colored-framed
