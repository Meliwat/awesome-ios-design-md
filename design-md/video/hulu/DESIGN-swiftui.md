# Hulu (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Hulu's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces
    static let huluCanvas    = Color(red: 0.043, green: 0.047, blue: 0.059) // #0B0C0F
    static let huluDeepBlack = Color.black                                  // #000000
    static let huluSurface1  = Color(red: 0.082, green: 0.086, blue: 0.098) // #151619
    static let huluSurface2  = Color(red: 0.118, green: 0.125, blue: 0.141) // #1E2024
    static let huluSurface3  = Color(red: 0.165, green: 0.176, blue: 0.200) // #2A2D33
    static let huluDivider   = Color(red: 0.165, green: 0.176, blue: 0.200) // #2A2D33

    // MARK: - Text
    static let huluTextPrimary   = Color.white                                // #FFFFFF
    static let huluTextSecondary = Color(red: 0.627, green: 0.643, blue: 0.671) // #A0A4AB
    static let huluTextTertiary  = Color(red: 0.420, green: 0.435, blue: 0.467) // #6B6F77

    // MARK: - Brand
    static let huluGreen        = Color(red: 0.110, green: 0.906, blue: 0.514) // #1CE783
    static let huluGreenPressed = Color(red: 0.082, green: 0.722, blue: 0.412) // #15B869
    static let huluLiveRed      = Color(red: 0.941, green: 0.278, blue: 0.416) // #F0476A
}
```

## 2. Typography

Graphik is a licensed brand typeface. Bundle the TTFs via `Info.plist` (`UIAppFonts`) or register at runtime. Fall back to Inter (or `.system`) — a clean grotesque is the closest free substitute.

```swift
extension Font {
    static let huluHeroTitle    = Font.custom("Graphik-Bold",     size: 32).weight(.heavy)
    static let huluTitleLarge   = Font.custom("Graphik-Bold",     size: 28).weight(.heavy)
    static let huluRailHeader   = Font.custom("Graphik-Bold",     size: 22).weight(.heavy)
    static let huluSectionHeader = Font.custom("Graphik-Bold",    size: 20).weight(.heavy)
    static let huluEpisodeTitle = Font.custom("Graphik-Semibold", size: 16).weight(.semibold)
    static let huluTileTitle    = Font.custom("Graphik-Semibold", size: 15).weight(.semibold)
    static let huluBody         = Font.custom("Graphik-Regular",  size: 15).weight(.regular)
    static let huluMeta         = Font.custom("Graphik-Regular",  size: 13).weight(.regular)
    static let huluTileSubtitle = Font.custom("Graphik-Regular",  size: 12).weight(.regular)
    static let huluLabelUpper   = Font.custom("Graphik-Bold",     size: 11).weight(.heavy)
    static let huluButton       = Font.custom("Graphik-Bold",     size: 16).weight(.heavy)
    static let huluButtonSecondary = Font.custom("Graphik-Semibold", size: 15).weight(.semibold)
    static let huluTab          = Font.custom("Graphik-Semibold", size: 10).weight(.semibold)
    static let huluBadge        = Font.custom("Graphik-Bold",     size: 11).weight(.heavy)
}

// If Graphik is unavailable, register a system fallback once:
extension Font {
    static func hulu(_ size: CGFloat, weight: Font.Weight = .regular) -> Font {
        .system(size: size, weight: weight, design: .default)
    }
}
```

## 3. Signature Components

### Primary Green Watch Button

```swift
struct HuluWatchButton: View {
    var title: String = "Watch"
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                Image(systemName: "play.fill")
                    .font(.system(size: 18, weight: .heavy))
                Text(title)
                    .font(.huluButton)
                    .tracking(0.2)
            }
            .foregroundStyle(Color.huluCanvas) // intentional: near-black on bright green
            .frame(maxWidth: .infinity)
            .frame(height: 52)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.huluGreen))
            .shadow(color: Color.huluGreen.opacity(0.30), radius: 24, y: 8)
        }
        .sensoryFeedback(.impact(weight: .light), trigger: title)
        .buttonStyle(HuluPressableStyle(pressedScale: 0.97))
    }
}

struct HuluPressableStyle: ButtonStyle {
    var pressedScale: CGFloat = 0.98
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .scaleEffect(configuration.isPressed ? pressedScale : 1)
            .animation(.spring(response: 0.25, dampingFraction: 0.7), value: configuration.isPressed)
    }
}
```

### Secondary Button (Details / Trailer)

```swift
struct HuluSecondaryButton: View {
    let title: String
    var systemImage: String? = nil
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                if let systemImage { Image(systemName: systemImage).font(.system(size: 16)) }
                Text(title).font(.huluButtonSecondary)
            }
            .foregroundStyle(.white)
            .frame(maxWidth: .infinity)
            .frame(height: 52)
            .background(RoundedRectangle(cornerRadius: 8).fill(Color.white.opacity(0.12)))
        }
        .buttonStyle(HuluPressableStyle())
    }
}
```

### 16:9 Content Tile (with progress bar)

```swift
struct HuluContentTile: View {
    let title: String
    let subtitle: String
    let artwork: Image
    var progress: Double? = nil   // 0...1, nil if not started
    let width: CGFloat

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            ZStack(alignment: .bottom) {
                artwork
                    .resizable()
                    .aspectRatio(16/9, contentMode: .fill)
                    .frame(width: width, height: width * 9/16)
                    .clipShape(RoundedRectangle(cornerRadius: 6))

                if let progress {
                    GeometryReader { geo in
                        ZStack(alignment: .leading) {
                            Rectangle().fill(Color.white.opacity(0.25))
                            Rectangle().fill(Color.huluGreen)
                                .frame(width: geo.size.width * progress)
                        }
                    }
                    .frame(height: 4)
                    .clipShape(RoundedRectangle(cornerRadius: 2))
                    .padding(.horizontal, 2)
                    .padding(.bottom, 2)
                }
            }
            Text(title)
                .font(.huluTileTitle)
                .foregroundStyle(.white)
                .lineLimit(1)
            Text(subtitle)
                .font(.huluTileSubtitle)
                .foregroundStyle(.huluTextSecondary)
                .lineLimit(1)
        }
        .frame(width: width)
    }
}
```

### Details Hero (darkened backdrop + green CTA)

```swift
struct HuluDetailsHero: View {
    let title: String
    let metadata: String
    let backdrop: Image
    let onWatch: () -> Void

    var body: some View {
        VStack(spacing: 0) {
            ZStack(alignment: .bottomLeading) {
                backdrop
                    .resizable()
                    .aspectRatio(16/9, contentMode: .fill)
                    .frame(height: 280)
                    .clipped()
                LinearGradient(
                    colors: [.clear, .huluCanvas],
                    startPoint: .center, endPoint: .bottom
                )
                VStack(alignment: .leading, spacing: 6) {
                    Text(title)
                        .font(.huluHeroTitle)
                        .foregroundStyle(.white)
                    Text(metadata)
                        .font(.huluMeta)
                        .foregroundStyle(.huluTextSecondary)
                }
                .padding(16)
            }

            VStack(spacing: 16) {
                HuluWatchButton(action: onWatch)
                HStack(spacing: 32) {
                    HuluIconAction(symbol: "plus", label: "My Stuff")
                    HuluIconAction(symbol: "arrow.down.to.line", label: "Download")
                    HuluIconAction(symbol: "square.and.arrow.up", label: "Share")
                }
            }
            .padding(16)
        }
        .background(Color.huluCanvas)
    }
}

struct HuluIconAction: View {
    let symbol: String
    let label: String
    var active: Bool = false
    var body: some View {
        VStack(spacing: 6) {
            Image(systemName: symbol)
                .font(.system(size: 22))
            Text(label).font(.huluTileSubtitle)
        }
        .foregroundStyle(active ? Color.huluGreen : .white)
        .frame(minWidth: 44, minHeight: 44)
    }
}
```

### Episode Row

```swift
struct HuluEpisodeRow: View {
    let title: String
    let meta: String
    let summary: String
    let thumbnail: Image
    var progress: Double? = nil

    var body: some View {
        HStack(spacing: 12) {
            ZStack(alignment: .bottom) {
                thumbnail
                    .resizable()
                    .aspectRatio(16/9, contentMode: .fill)
                    .frame(width: 140, height: 79)
                    .clipShape(RoundedRectangle(cornerRadius: 6))
                if let progress {
                    GeometryReader { geo in
                        ZStack(alignment: .leading) {
                            Rectangle().fill(Color.white.opacity(0.25))
                            Rectangle().fill(Color.huluGreen).frame(width: geo.size.width * progress)
                        }
                    }
                    .frame(height: 4)
                }
            }
            VStack(alignment: .leading, spacing: 4) {
                Text(title).font(.huluEpisodeTitle).foregroundStyle(.white).lineLimit(1)
                Text(meta).font(.huluMeta).foregroundStyle(.huluTextSecondary)
                Text(summary).font(.huluMeta).foregroundStyle(.huluTextSecondary).lineLimit(2)
            }
            Spacer()
            Image(systemName: "arrow.down.to.line")
                .font(.system(size: 20))
                .foregroundStyle(.huluTextSecondary)
        }
        .padding(.horizontal, 16)
        .frame(minHeight: 96)
        .contentShape(Rectangle())
    }
}
```

## 4. Live Badge

```swift
struct HuluLiveBadge: View {
    @State private var pulse = false
    var body: some View {
        HStack(spacing: 5) {
            Circle()
                .fill(.white)
                .frame(width: 6, height: 6)
                .opacity(pulse ? 0.4 : 1)
                .animation(.easeInOut(duration: 1.4).repeatForever(autoreverses: true), value: pulse)
            Text("LIVE").font(.huluBadge).tracking(0.4)
        }
        .foregroundStyle(.white)
        .padding(.horizontal, 8)
        .padding(.vertical, 4)
        .background(Capsule().fill(Color.huluLiveRed))
        .onAppear { pulse = true }
    }
}
```

## 5. Tab Bar

Use `TabView` with a custom `UITabBarAppearance` to match Hulu's 94%-opaque canvas with `.regularMaterial` blur. **Active tint is Hulu Green** — green is the indicator here.

```swift
struct RootTabView: View {
    init() {
        let appearance = UITabBarAppearance()
        appearance.configureWithTransparentBackground()
        appearance.backgroundEffect = UIBlurEffect(style: .systemMaterialDark)
        appearance.backgroundColor = UIColor(Color.huluCanvas).withAlphaComponent(0.94)
        UITabBar.appearance().standardAppearance = appearance
        UITabBar.appearance().scrollEdgeAppearance = appearance
    }
    var body: some View {
        TabView {
            HomeView().tabItem    { Label("Home",     systemImage: "house.fill") }
            HubsView().tabItem    { Label("Hubs",     systemImage: "square.grid.2x2.fill") }
            MyStuffView().tabItem { Label("My Stuff", systemImage: "bookmark.fill") }
            SearchView().tabItem  { Label("Search",   systemImage: "magnifyingglass") }
            AccountView().tabItem { Label("Account",  systemImage: "person.crop.circle.fill") }
        }
        .tint(.huluGreen) // active = green, green IS the indicator
    }
}
```

## 6. Motion

Use `SwiftUI` spring defaults. Haptics via the modern `.sensoryFeedback` API (iOS 17+).

```swift
// Tile press scale-up
.scaleEffect(isPressed ? 1.04 : 1.0)
.animation(.easeOut(duration: 0.18), value: isPressed)

// Watch CTA tap
.sensoryFeedback(.impact(weight: .light), trigger: didTapWatch)

// Add to My Stuff
.sensoryFeedback(.success, trigger: isInMyStuff)

// LIVE dot pulse
// .opacity oscillation 1.0 → 0.4 → 1.0 over 1.4s, .repeatForever(autoreverses: true)
```

## 7. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Play / Watch | `play.fill` | 18-24pt |
| Pause | `pause.fill` | 24pt |
| Skip Forward 10 | `goforward.10` | 28pt |
| Skip Back 10 | `gobackward.10` | 28pt |
| Add to My Stuff | `plus` / `checkmark` | 22pt |
| Download | `arrow.down.to.line` | 22pt |
| Share | `square.and.arrow.up` | 22pt |
| Captions | `captions.bubble` | 22pt |
| AirPlay | `airplayvideo` | 22pt |
| Search | `magnifyingglass` | 18pt |
| Home (tab) | `house.fill` | 24pt |
| Hubs (tab) | `square.grid.2x2.fill` | 24pt |
| My Stuff (tab) | `bookmark.fill` | 24pt |
| Account (tab) | `person.crop.circle.fill` | 24pt |
| See All | `chevron.right` | 14pt |

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (for `.sensoryFeedback` you need iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Support Dynamic Type on show titles, episode titles, synopsis — pin tab labels, scrubber times, and tile subtitles (layout-sensitive in dense rails)
- VoiceOver: label tiles as "Watch The Bear, Season 3 Episode 4, 24 minutes left"; merge tile title+subtitle and expose the progress as an `accessibilityValue`
- Contrast: `#A0A4AB` secondary text on `#0B0C0F` canvas meets WCAG AA at 13pt+; validate the 11pt badge labels and bump toward `#B6BAC0` if targeting strict compliance
- The bright green (`#1CE783`) with near-black (`#0B0C0F`) text exceeds AAA — keep that pairing; never white-on-green
- Reduce Motion: replace the tile scale-up with a 1pt green border highlight and disable the LIVE pulse
