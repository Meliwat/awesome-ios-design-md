# Tubi (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Tubi's visual language into paste-ready SwiftUI code: `Color` extensions, gradient helpers, `Font` extensions, `ViewModifier`s, and example views.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-only)
    static let tubiCanvas    = Color(red: 0.039, green: 0.039, blue: 0.165) // #0A0A2A
    static let tubiSurface1  = Color(red: 0.082, green: 0.082, blue: 0.239) // #15153D
    static let tubiSurface2  = Color(red: 0.118, green: 0.118, blue: 0.322) // #1E1E52
    static let tubiSurface3  = Color(red: 0.149, green: 0.149, blue: 0.400) // #262666
    static let tubiDivider   = Color(red: 0.165, green: 0.165, blue: 0.361) // #2A2A5C

    // MARK: - Brand Gradient Stops
    static let tubiPurple    = Color(red: 0.455, green: 0.031, blue: 1.000) // #7408FF
    static let tubiMagenta   = Color(red: 1.000, green: 0.000, blue: 1.000) // #FF00FF
    static let tubiViolet    = Color(red: 0.631, green: 0.169, blue: 1.000) // #A12BFF
    static let tubiPink      = Color(red: 1.000, green: 0.310, blue: 0.847) // #FF4FD8

    // MARK: - CTA / Accent
    static let tubiPlayWhite     = Color.white                                  // #FFFFFF
    static let tubiPlayPressed   = Color(red: 0.902, green: 0.902, blue: 0.949) // #E6E6F2
    static let tubiFreeYellow    = Color(red: 1.000, green: 0.831, blue: 0.000) // #FFD400
    static let tubiOnWhite       = Color(red: 0.039, green: 0.039, blue: 0.165) // #0A0A2A
    static let tubiFreeText      = Color(red: 0.102, green: 0.039, blue: 0.165) // #1A0A2A

    // MARK: - Text
    static let tubiTextPrimary   = Color.white                                  // #FFFFFF
    static let tubiTextSecondary = Color(red: 0.725, green: 0.725, blue: 0.839) // #B9B9D6
    static let tubiTextTertiary  = Color(red: 0.478, green: 0.478, blue: 0.627) // #7A7AA0

    // MARK: - Semantic
    static let tubiSuccess = Color(red: 0.122, green: 0.820, blue: 0.482) // #1FD17B
    static let tubiError   = Color(red: 1.000, green: 0.290, blue: 0.431) // #FF4A6E
    static let tubiLive    = Color(red: 1.000, green: 0.231, blue: 0.361) // #FF3B5C
    static let tubiRating  = Color(red: 1.000, green: 0.690, blue: 0.125) // #FFB020
}

enum TubiGradient {
    /// Brand gradient — wordmark, secondary CTA, active tab, "Tubi Original"
    static let brand = LinearGradient(
        colors: [.tubiPurple, .tubiMagenta],
        startPoint: .topLeading, endPoint: .bottomTrailing)

    /// Continue-watching progress fill
    static let progress = LinearGradient(
        colors: [.tubiPurple, .tubiMagenta],
        startPoint: .leading, endPoint: .trailing)

    /// Bottom scrim over hero / detail backdrops
    static let heroScrim = LinearGradient(
        stops: [
            .init(color: .clear, location: 0.40),
            .init(color: Color.tubiCanvas.opacity(0.95), location: 1.0),
        ],
        startPoint: .top, endPoint: .bottom)
}
```

## 2. Typography

Tubi's brand face is a tight geometric grotesque; **Inter** at heavy weights is the closest free analog. Bundle Inter (SIL OFL) via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let tubiHeroTitle  = Font.custom("Inter-Black",      size: 32) // 900
    static let tubiDetailTitle = Font.custom("Inter-ExtraBold", size: 28) // 800
    static let tubiRowHeader  = Font.custom("Inter-ExtraBold",  size: 22) // 800
    static let tubiSection    = Font.custom("Inter-Bold",       size: 18) // 700
    static let tubiBody       = Font.custom("Inter-Regular",    size: 16) // 400
    static let tubiCardTitle  = Font.custom("Inter-SemiBold",   size: 15) // 600
    static let tubiButton     = Font.custom("Inter-ExtraBold",  size: 15) // 800
    static let tubiMeta       = Font.custom("Inter-Medium",     size: 13) // 500
    static let tubiCaption    = Font.custom("Inter-Medium",     size: 12) // 500
    static let tubiBadge      = Font.custom("Inter-Bold",       size: 11) // 700 caps
    static let tubiTab        = Font.custom("Inter-SemiBold",   size: 10) // 600
}

// Hero title modifier — heavy + tight tracking
extension View {
    func tubiHero() -> some View {
        self.font(.tubiHeroTitle)
            .tracking(-0.6)
            .foregroundStyle(Color.tubiTextPrimary)
            .lineSpacing(0)
    }
}
```

## 3. Signature Components

### Featured Hero

```swift
struct TubiHero: View {
    let backdropURL: String
    let badge: String          // "TUBI ORIGINAL"
    let title: String
    let meta: String           // "2024 · Thriller · 1h 52m · TV-MA"
    let onPlay: () -> Void
    let onAdd: () -> Void

    var body: some View {
        ZStack(alignment: .bottomLeading) {
            AsyncImage(url: URL(string: backdropURL)) { img in
                img.resizable().aspectRatio(contentMode: .fill)
            } placeholder: {
                LinearGradient(colors: [Color.tubiSurface2, Color.tubiCanvas],
                               startPoint: .top, endPoint: .bottom)
            }
            .frame(height: 230)
            .clipped()

            TubiGradient.heroScrim

            VStack(alignment: .leading, spacing: 0) {
                Spacer()
                HStack {
                    Text(badge.uppercased())
                        .font(.system(size: 9, weight: .heavy))
                        .tracking(0.6)
                        .foregroundStyle(.white)
                        .padding(.horizontal, 8).padding(.vertical, 4)
                        .background(Capsule().fill(TubiGradient.brand))
                    Spacer()
                }
                Spacer()
                Text(title).tubiHero()
                Text(meta)
                    .font(.tubiCaption)
                    .foregroundStyle(Color.tubiTextSecondary)
                    .padding(.top, 7)
                HStack(spacing: 8) {
                    Button(action: onPlay) {
                        Label("Play Free", systemImage: "play.fill")
                            .font(.tubiButton)
                            .foregroundStyle(Color.tubiOnWhite)
                            .frame(maxWidth: .infinity, minHeight: 48)
                            .background(RoundedRectangle(cornerRadius: 8).fill(Color.tubiPlayWhite))
                    }
                    Button(action: onAdd) {
                        Image(systemName: "plus")
                            .font(.system(size: 18, weight: .bold))
                            .foregroundStyle(.white)
                            .frame(width: 44, height: 48)
                            .background(RoundedRectangle(cornerRadius: 8).fill(Color.white.opacity(0.16)))
                    }
                }
                .padding(.top, 12)
            }
            .padding(16)
        }
        .frame(height: 230)
        .clipShape(RoundedRectangle(cornerRadius: 18))
        .padding(.horizontal, 14)
    }
}
```

### Poster Card

```swift
struct TubiPoster: View {
    let imageURL: String
    let title: String
    var progress: Double? = nil   // 0...1 for continue-watching
    @State private var focused = false

    var body: some View {
        ZStack(alignment: .bottom) {
            AsyncImage(url: URL(string: imageURL)) { img in
                img.resizable().aspectRatio(contentMode: .fill)
            } placeholder: { Color.tubiSurface2 }
            .frame(width: 100, height: 150)
            .clipped()

            // FREE tag
            VStack {
                HStack {
                    Text("FREE")
                        .font(.system(size: 8, weight: .black))
                        .foregroundStyle(Color.tubiFreeText)
                        .padding(.horizontal, 5).padding(.vertical, 2)
                        .background(RoundedRectangle(cornerRadius: 3).fill(Color.tubiFreeYellow))
                    Spacer()
                }
                Spacer()
            }
            .padding(6)

            Text(title)
                .font(.system(size: 11, weight: .bold))
                .foregroundStyle(.white)
                .shadow(color: .black.opacity(0.8), radius: 4, y: 1)
                .frame(maxWidth: .infinity, alignment: .leading)
                .padding(6)

            if let progress {
                VStack {
                    Spacer()
                    GeometryReader { geo in
                        ZStack(alignment: .leading) {
                            Rectangle().fill(Color.white.opacity(0.16))
                            Rectangle().fill(TubiGradient.progress)
                                .frame(width: geo.size.width * progress)
                        }
                    }
                    .frame(height: 4)
                }
            }
        }
        .frame(width: 100, height: 150)
        .clipShape(RoundedRectangle(cornerRadius: 10))
        .shadow(color: .black.opacity(0.45), radius: 8, y: 6)
        .scaleEffect(focused ? 1.04 : 1.0)
        .overlay(
            RoundedRectangle(cornerRadius: 10)
                .strokeBorder(TubiGradient.brand, lineWidth: focused ? 2 : 0)
        )
        .animation(.easeOut(duration: 0.15), value: focused)
    }
}
```

### Content Row

```swift
struct TubiRow<Item: Identifiable, Card: View>: View {
    let title: String
    let items: [Item]
    let showSeeAll: Bool
    @ViewBuilder let card: (Item) -> Card

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            HStack {
                Text(title)
                    .font(.tubiRowHeader)
                    .tracking(-0.3)
                    .foregroundStyle(Color.tubiTextPrimary)
                Spacer()
                if showSeeAll {
                    Text("See All ›")
                        .font(.system(size: 13, weight: .bold))
                        .foregroundStyle(Color.tubiPink)
                }
            }
            .padding(.horizontal, 16)

            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 10) {
                    ForEach(items) { card($0) }
                }
                .padding(.horizontal, 16)
            }
        }
        .padding(.top, 16)
    }
}
```

### Live TV EPG Row

```swift
struct EPGProgram: Identifiable {
    let id = UUID()
    let title: String
    let timeRange: String   // "8:00 – 8:30 PM"
    let isOnNow: Bool
}

struct TubiEPGRow: View {
    let logoURL: String
    let channelName: String
    let programs: [EPGProgram]

    var body: some View {
        HStack(alignment: .top, spacing: 12) {
            AsyncImage(url: URL(string: logoURL)) { $0.resizable() }
                placeholder: { Color.tubiSurface1 }
                .frame(width: 56, height: 56)
                .clipShape(RoundedRectangle(cornerRadius: 10))

            VStack(alignment: .leading, spacing: 6) {
                Text(channelName)
                    .font(.tubiCardTitle)
                    .foregroundStyle(Color.tubiTextPrimary)
                ScrollView(.horizontal, showsIndicators: false) {
                    HStack(spacing: 8) {
                        ForEach(programs) { p in
                            VStack(alignment: .leading, spacing: 2) {
                                if p.isOnNow {
                                    HStack(spacing: 4) {
                                        Circle().fill(Color.tubiLive).frame(width: 6, height: 6)
                                        Text("LIVE").font(.system(size: 9, weight: .heavy))
                                            .foregroundStyle(Color.tubiLive)
                                    }
                                }
                                Text(p.title)
                                    .font(.system(size: 13, weight: .semibold))
                                    .foregroundStyle(Color.tubiTextPrimary)
                                Text(p.timeRange)
                                    .font(.system(size: 11, weight: .medium))
                                    .foregroundStyle(Color.tubiTextSecondary)
                            }
                            .padding(8)
                            .frame(width: 150, alignment: .leading)
                            .background(
                                RoundedRectangle(cornerRadius: 10).fill(Color.tubiSurface2)
                            )
                            .overlay(alignment: .leading) {
                                if p.isOnNow {
                                    Rectangle().fill(TubiGradient.brand).frame(width: 3)
                                }
                            }
                        }
                    }
                }
            }
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 8)
    }
}
```

### Buttons

```swift
struct TubiPrimaryButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Label(title, systemImage: "play.fill")
                .font(.tubiButton)
                .foregroundStyle(Color.tubiOnWhite)
                .frame(maxWidth: .infinity, minHeight: 48)
                .background(RoundedRectangle(cornerRadius: 8).fill(Color.tubiPlayWhite))
        }
        .buttonStyle(.plain)
    }
}

struct TubiGradientButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.tubiButton)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity, minHeight: 48)
                .background(RoundedRectangle(cornerRadius: 8).fill(TubiGradient.brand))
        }
        .buttonStyle(.plain)
    }
}
```

## 4. Bottom Tab Bar

```swift
struct TubiTabView: View {
    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            LiveTVView().tabItem { Label("Live TV", systemImage: "tv") }
            MyListView().tabItem { Label("My List", systemImage: "bookmark.fill") }
            AccountView().tabItem { Label("Account", systemImage: "person.crop.circle") }
        }
        .tint(.tubiMagenta) // active = magenta, no pill
        .onAppear {
            let a = UITabBarAppearance()
            a.configureWithOpaqueBackground()
            a.backgroundColor = UIColor(Color.tubiCanvas.opacity(0.96))
            a.shadowColor = UIColor(Color.tubiDivider)
            UITabBar.appearance().standardAppearance = a
            UITabBar.appearance().scrollEdgeAppearance = a
        }
    }
}
```

## 5. Motion

```swift
// Poster focus / press
.scaleEffect(focused ? 1.04 : 1.0)
.animation(.easeOut(duration: 0.15), value: focused)

// Hero cross-dissolve between featured titles (~6s dwell)
.transition(.opacity)
.animation(.easeInOut(duration: 0.4), value: featuredIndex)

// Continue-watching progress fill on appear
withAnimation(.easeOut(duration: 0.5)) { progressShown = true }

// Add to My List: + → ✓
withAnimation(.spring(response: 0.25, dampingFraction: 0.6)) { added.toggle() }
.sensoryFeedback(.impact(flexibility: .soft), trigger: added)

// Play transition: scale + cross-fade into player over 350ms
withAnimation(.easeInOut(duration: 0.35)) { showPlayer = true }

// Wordmark shimmer on Home appear (once) — animate a moving mask highlight over 800ms
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| Live TV (tab) | `tv` / `tv.fill` | 22pt |
| My List (tab) | `bookmark` / `bookmark.fill` | 22pt |
| Account (tab) | `person.crop.circle` | 22pt |
| Play (button) | `play.fill` | 13pt |
| Add to list | `plus` / `checkmark` | 18pt |
| Download | `arrow.down.to.line` | 24pt |
| Share | `square.and.arrow.up` | 22pt |
| Back | `chevron.left` | 24pt |
| More like this | `rectangle.stack` | 16pt |
| Cast & crew | `person.2` | 16pt |
| Live dot | `circle.fill` | 6pt |
| Restart episode | `gobackward` | 18pt |
| Settings | `gearshape` | 22pt |

## 7. Dark Mode

Tubi is **dark-only** — there is no light scheme. Lock the app to dark and never derive a light palette.

```swift
struct TubiTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.tubiCanvas)
            .foregroundStyle(Color.tubiTextPrimary)
            .preferredColorScheme(.dark)   // force dark
    }
}

extension View {
    func tubiTheme() -> some View { modifier(TubiTheme()) }
}
```

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`.sensoryFeedback` requires iOS 17; fall back to `UIImpactFeedbackGenerator`)
- Bundle Inter TTFs (Regular/Medium/SemiBold/Bold/ExtraBold/Black) via `Info.plist` — SIL OFL, free to ship
- Dynamic Type: scale hero/detail titles, row headers, body, synopsis; keep badge text, tab labels, progress labels, EPG time-header FIXED (layout-sensitive)
- VoiceOver: poster cards labeled "{title}, free to watch" (the FREE tag must be announced — it's core info); continue-watching adds ", {n} minutes left"; EPG "On Now" block labeled "{program}, live now on {channel}"
- The yellow FREE tag is decorative-looking but semantically important — include it in the accessibility label, never `.accessibilityHidden`
- Color contrast: white text on `#0A0A2A` and `#1A0A2A`-on-`#FFD400` both pass WCAG AA; `#B9B9D6` metadata on canvas passes AA at 13pt+
- Reduce Motion: disable poster scale-on-focus and hero ken-burns drift; keep the cross-dissolve as a plain crossfade; progress bar appears at final width without the 0→% animation
- Reduce Transparency: replace the tab bar's `backdrop blur` with solid `#0A0A2A`
- The brand gradient must never be the only signal for an interactive state — focus also scales (1.04) so it's perceivable without color
