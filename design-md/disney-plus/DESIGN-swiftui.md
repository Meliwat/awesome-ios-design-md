# Disney+ (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Disney+'s visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, the starfield billboard, the signature brand-portal tiles, the tile glow+scale focus language, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let dpCanvas   = Color(red: 0.039, green: 0.055, blue: 0.165) // #0A0E2A
    static let dpSurface1 = Color(red: 0.071, green: 0.082, blue: 0.180) // #12152E
    static let dpSurface2 = Color(red: 0.102, green: 0.122, blue: 0.239) // #1A1F3D
    static let dpDivider  = Color(red: 0.165, green: 0.188, blue: 0.314) // #2A3050

    // MARK: - Text
    static let dpTextPrimary   = Color.white                                 // #FFFFFF
    static let dpTextSecondary = Color(red: 0.627, green: 0.651, blue: 0.753) // #A0A6C0
    static let dpTextTertiary  = Color(red: 0.353, green: 0.376, blue: 0.502) // #5A6080

    // MARK: - Brand
    static let dpBlue        = Color(red: 0.0,   green: 0.388, blue: 0.898) // #0063E5
    static let dpGlowBlue    = Color(red: 0.102, green: 0.459, blue: 1.0)   // #1A75FF
    static let dpBluePressed = Color(red: 0.0,   green: 0.322, blue: 0.741) // #0052BD
    static let dpLiveRed     = Color(red: 0.898, green: 0.282, blue: 0.302) // #E5484D
}

extension ShapeStyle where Self == Color {
    static var dpFocusGlow: Color { Color(red: 0.102, green: 0.459, blue: 1.0).opacity(0.30) }
}
```

## 2. Typography

Disney's product sans is an Avenir-family humanist. Use `Avenir Next` (bundled on iOS) or bundle the brand TTFs; Inter is the closest free fallback, `.system(design: .default)` is the final.

```swift
extension Font {
    static let dpBillboard  = Font.custom("AvenirNext-Bold",     size: 30).weight(.bold)
    static let dpDetailTitle = Font.custom("AvenirNext-Bold",    size: 26).weight(.bold)
    static let dpSection    = Font.custom("AvenirNext-Bold",     size: 20).weight(.bold)
    static let dpRowHeader  = Font.custom("AvenirNext-Bold",     size: 18).weight(.bold)
    static let dpCardTitle  = Font.custom("AvenirNext-DemiBold", size: 14).weight(.semibold)
    static let dpSynopsis   = Font.custom("AvenirNext-Regular",  size: 15).weight(.regular)
    static let dpMetaStrip  = Font.custom("AvenirNext-DemiBold", size: 13).weight(.semibold)
    static let dpSubtitle   = Font.custom("AvenirNext-Regular",  size: 14).weight(.regular)
    static let dpMeta       = Font.custom("AvenirNext-Regular",  size: 12).weight(.regular)
    static let dpBadge      = Font.custom("AvenirNext-Bold",     size: 10).weight(.bold)
    static let dpButton     = Font.custom("AvenirNext-Bold",     size: 16).weight(.bold)
    static let dpButtonSec  = Font.custom("AvenirNext-DemiBold", size: 14).weight(.semibold)
    static let dpTab        = Font.custom("AvenirNext-DemiBold", size: 10).weight(.semibold)
}

extension Font {
    static func dp(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

Apply `.monospacedDigit()` to runtime and episode counts.

## 3. Signature Components

### Starfield + Billboard with Auto-Trailer

```swift
struct Starfield: View {
    let count = 90
    @State private var stars: [(CGPoint, CGFloat, Double)] = []

    var body: some View {
        GeometryReader { geo in
            Canvas { ctx, size in
                for (p, r, a) in stars {
                    let rect = CGRect(x: p.x * size.width, y: p.y * size.height, width: r, height: r)
                    ctx.fill(Path(ellipseIn: rect), with: .color(.white.opacity(a)))
                }
            }
            .onAppear {
                stars = (0..<count).map { _ in
                    (CGPoint(x: .random(in: 0...1), y: .random(in: 0...1)),
                     CGFloat.random(in: 1...2),
                     Double.random(in: 0...0.5))
                }
            }
        }
        .allowsHitTesting(false)
    }
}

struct HeroBillboard: View {
    let keyArt: Image
    let logoArt: Image
    let metadata: String           // "Action · PG-13 · 2024 · 2h 11m"
    @State private var showTrailer = false

    var body: some View {
        ZStack(alignment: .bottom) {
            keyArt
                .resizable().aspectRatio(contentMode: .fill)
                .frame(height: UIScreen.main.bounds.height * 0.62)
                .clipped()
                .overlay(Starfield().opacity(0.7))
                .overlay(
                    LinearGradient(colors: [.clear, .clear, .dpCanvas],
                                   startPoint: .top, endPoint: .bottom)
                )

            VStack(spacing: 14) {
                logoArt
                    .resizable().scaledToFit()
                    .frame(maxHeight: 110)

                Text(metadata)
                    .font(.dpMetaStrip).tracking(0.3)
                    .foregroundStyle(.dpTextSecondary)

                HStack(spacing: 12) {
                    DPPlayButton(label: "Play") { }
                        .shadow(color: .dpFocusGlow, radius: 22, y: 6)
                    DPSecondaryButton(icon: "plus", label: "Watchlist") { }
                }
                .padding(.horizontal, 16)
            }
            .padding(.bottom, 24)
        }
        .frame(maxWidth: .infinity)
        .background(Color.dpCanvas)
        .task {
            try? await Task.sleep(for: .seconds(3))
            withAnimation(.easeInOut(duration: 0.6)) { showTrailer = true }
            // swap keyArt for a muted AVPlayer layer when showTrailer is true
        }
    }
}
```

### Primary / Secondary Buttons

```swift
struct DPPlayButton: View {
    let label: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill")
                Text(label).font(.dpButton).tracking(0.3)
            }
            .foregroundStyle(Color.dpCanvas) // dark glyph on white
            .frame(maxWidth: .infinity)
            .frame(height: 48)
            .background(RoundedRectangle(cornerRadius: 8).fill(.white))
        }
        .sensoryFeedback(.impact(weight: .medium), trigger: label)
        .buttonStyle(DPPressable())
    }
}

struct DPSecondaryButton: View {
    let icon: String
    let label: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: icon)
                Text(label).font(.dpButtonSec)
            }
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .frame(height: 48)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.white.opacity(0.12)))
            .overlay(RoundedRectangle(cornerRadius: 8).strokeBorder(Color.white.opacity(0.24), lineWidth: 1))
        }
        .buttonStyle(DPPressable())
    }
}

struct DPPressable: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? 0.98 : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.8), value: configuration.isPressed)
    }
}
```

### Brand-Portal Tile (Signature) + the Focus Glow Language

```swift
struct BrandPortalTile: View {
    let logo: Image
    let gradient: [Color]          // brand-tinted
    @State private var focused = false

    var body: some View {
        logo
            .resizable().scaledToFit()
            .padding(20)
            .frame(width: 160, height: 96)
            .background(
                LinearGradient(colors: gradient, startPoint: .topLeading, endPoint: .bottomTrailing)
            )
            .clipShape(RoundedRectangle(cornerRadius: 10))
            .overlay(
                RoundedRectangle(cornerRadius: 10)
                    .strokeBorder(focused ? Color.dpGlowBlue : Color.dpDivider,
                                  lineWidth: focused ? 2 : 1)
            )
            .scaleEffect(focused ? 1.04 : 1)
            .shadow(color: focused ? Color.dpGlowBlue.opacity(0.35) : .clear, radius: 24)
            .animation(.easeOut(duration: 0.18), value: focused)
            .onTapGesture { /* open universe hub */ }
            .onLongPressGesture(minimumDuration: 0, pressing: { focused = $0 }, perform: {})
    }
}

// Reusable focus modifier — the unified Disney+ selection language
struct DPFocusable: ViewModifier {
    @State private var focused = false
    func body(content: Content) -> some View {
        content
            .scaleEffect(focused ? 1.04 : 1)
            .overlay(
                RoundedRectangle(cornerRadius: 6)
                    .strokeBorder(focused ? Color.dpGlowBlue : .clear, lineWidth: 2)
            )
            .shadow(color: focused ? Color.dpFocusGlow : .clear, radius: 24)
            .animation(.easeOut(duration: 0.18), value: focused)
            .onLongPressGesture(minimumDuration: 0, pressing: { focused = $0 }, perform: {})
    }
}
extension View { func dpFocusable() -> some View { modifier(DPFocusable()) } }
```

### 16:9 Content Card

```swift
struct ContentCard16x9: View {
    let keyArt: Image
    var progress: Double? = nil    // continue-watching

    var body: some View {
        ZStack(alignment: .bottom) {
            keyArt
                .resizable().aspectRatio(16/9, contentMode: .fill)
                .frame(width: 220, height: 124)
                .clipShape(RoundedRectangle(cornerRadius: 6))

            if let p = progress {
                VStack {
                    Spacer()
                    GeometryReader { g in
                        ZStack(alignment: .leading) {
                            Rectangle().fill(Color.white.opacity(0.25))
                            Rectangle().fill(Color.dpBlue).frame(width: g.size.width * p)
                        }
                    }
                    .frame(height: 3)
                }
                Image(systemName: "play.circle.fill")
                    .font(.system(size: 34))
                    .foregroundStyle(.white.opacity(0.9))
            }
        }
        .frame(width: 220, height: 124)
        .dpFocusable()
    }
}
```

### Episode Row

```swift
struct EpisodeRow: View {
    let thumb: Image
    let title: String              // "E4 · The Siege"
    let synopsis: String
    let runtime: String
    var progress: Double? = nil

    var body: some View {
        HStack(spacing: 12) {
            ZStack(alignment: .bottom) {
                thumb.resizable().aspectRatio(16/9, contentMode: .fill)
                    .frame(width: 160, height: 90).clipShape(RoundedRectangle(cornerRadius: 6))
                Image(systemName: "play.circle.fill").font(.system(size: 28)).foregroundStyle(.white.opacity(0.9))
                if let p = progress {
                    VStack { Spacer()
                        GeometryReader { g in
                            ZStack(alignment: .leading) {
                                Rectangle().fill(Color.white.opacity(0.25))
                                Rectangle().fill(Color.dpBlue).frame(width: g.size.width * p)
                            }
                        }.frame(height: 3)
                    }
                }
            }
            VStack(alignment: .leading, spacing: 4) {
                Text(title).font(.dpCardTitle).foregroundStyle(.white)
                Text(synopsis).font(.custom("AvenirNext-Regular", size: 13)).foregroundStyle(.dpTextSecondary).lineLimit(2)
                Text(runtime).font(.dpMeta).foregroundStyle(.dpTextSecondary).monospacedDigit()
            }
            Spacer(minLength: 4)
            Image(systemName: "arrow.down.circle").font(.system(size: 22)).foregroundStyle(.dpTextSecondary)
        }
        .padding(12)
        .background(RoundedRectangle(cornerRadius: 8).fill(Color.dpSurface1))
    }
}
```

## 4. Hero Auto-Trailer

Cross-fade the still to a muted, looping `AVPlayer` after the dwell:

```swift
import AVKit

struct AutoTrailerLayer: View {
    let url: URL
    @State private var player: AVPlayer?
    var body: some View {
        VideoPlayer(player: player)
            .disabled(true)
            .onAppear {
                let p = AVPlayer(url: url); p.isMuted = true
                p.actionAtItemEnd = .none
                NotificationCenter.default.addObserver(forName: .AVPlayerItemDidPlayToEndTime, object: p.currentItem, queue: .main) { _ in
                    p.seek(to: .zero); p.play()
                }
                player = p; p.play()
            }
    }
}
```

## 5. Tab Bar

```swift
struct RootTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithTransparentBackground()
        a.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        a.backgroundColor = UIColor(Color.dpCanvas).withAlphaComponent(0.96)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            WatchlistView().tabItem { Label("Watchlist", systemImage: "plus.rectangle.on.rectangle") }
            DownloadsView().tabItem { Label("Downloads", systemImage: "arrow.down.circle") }
            ProfileView().tabItem { Label("Profile", systemImage: "person.crop.circle") }
        }
        .tint(.white) // active = white; #0063E5 used as a dot indicator overlay
        .preferredColorScheme(.dark)
    }
}
```

## 6. Motion

```swift
// Tile glow + scale (signature): focus → scale 1.0→1.04 + #1A75FF border + dpFocusGlow,
// .animation(.easeOut(duration: 0.18))

// Hero auto-trailer: still → muted video .easeInOut(duration: 0.6) after a 3s .task sleep

// Play tap: DPPressable scale 0.98 spring(damping 0.8) + .sensoryFeedback(.impact(weight: .medium))

// Brand-portal open: matchedGeometryEffect tile → hub, .spring(response: 0.35)

// Detail open: matchedGeometryEffect key art rise; scrim .opacity over 300ms

// Starfield: a very slow TimelineView parallax drift (sub-pixel) — ambient only
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play | `play.fill` | 16-20pt |
| Play overlay (card) | `play.circle.fill` | 28-34pt |
| Watchlist | `plus` / `checkmark` | 22pt |
| Download | `arrow.down.circle` / `arrow.down.circle.fill` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Rate | `hand.thumbsup` | 22pt |
| Mute toggle | `speaker.slash.fill` / `speaker.wave.2.fill` | 20pt |
| Back | `chevron.left` | 20pt |
| Cast / AirPlay | `airplayvideo` | 22pt |
| More | `ellipsis` | 20pt |
| Search | `magnifyingglass` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Watchlist (tab) | `plus.rectangle.on.rectangle` | 24pt |
| Downloads (tab) | `arrow.down.circle` | 24pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on detail title, synopsis, metadata strip, episode titles — pin badges (10pt) and tab labels (10pt)
- VoiceOver: the billboard's `accessibilityLabel` = "<Series>, <metadata>"; the Play button = "Play <title>"; brand-portal tiles = "Disney universe, button" etc.
- The auto-trailer must respect `accessibilityReduceMotion` (skip the video swap, keep the still) and "Auto-Play Previews" preferences
- Continue-watching progress: mirror with `.accessibilityValue("42 percent watched")`
- Contrast: `#A0A6C0` secondary on `#0A0E2A` passes WCAG AA at 14pt+; validate the 12-13pt metadata and lighten toward `#B4BAD0` if targeting strict compliance
- Force dark: Disney+ is dark-only — set `.preferredColorScheme(.dark)` at the root; never expose a light variant
- Starfield is decorative — mark it `.accessibilityHidden(true)`
