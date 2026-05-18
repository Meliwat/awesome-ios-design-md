# Peacock (iOS) — SwiftUI Implementation Guide

Companion to [DESIGN.md](DESIGN.md) — the framework-neutral spec. This file translates Peacock's visual language into paste-ready SwiftUI code: `Color` extensions, `Font` extensions, `ViewModifier`s, and example views. Peacock is dark-only; there is no light scheme.

## 1. Color Tokens

```swift
import SwiftUI

extension Color {
    // MARK: - Canvas & Surfaces (dark-only product)
    static let pcCanvas      = Color(red: 0.039, green: 0.039, blue: 0.078) // #0A0A14
    static let pcCanvasWarm  = Color(red: 0.078, green: 0.055, blue: 0.149) // #140E26
    static let pcSurface1    = Color(red: 0.102, green: 0.082, blue: 0.188) // #1A1530
    static let pcSurface2    = Color(red: 0.141, green: 0.110, blue: 0.239) // #241C3D
    static let pcDivider     = Color(red: 0.173, green: 0.145, blue: 0.271) // #2C2545

    // MARK: - Text
    static let pcTextPrimary   = Color.white                                  // #FFFFFF
    static let pcTextSecondary = Color(red: 0.710, green: 0.698, blue: 0.761) // #B5B2C2
    static let pcTextTertiary  = Color(red: 0.478, green: 0.463, blue: 0.537) // #7A7689

    // MARK: - Primary CTA (always white)
    static let pcCTA        = Color.white                                     // #FFFFFF
    static let pcCTAPressed = Color(red: 0.902, green: 0.902, blue: 0.918)    // #E6E6EA
    static let pcCTALabel   = Color(red: 0.039, green: 0.039, blue: 0.078)    // #0A0A14

    // MARK: - Feather accent (identity / upsell / selection only)
    static let pcFeatherYellow = Color(red: 0.980, green: 0.800, blue: 0.082) // #FACC15
    static let pcFeatherOrange = Color(red: 0.976, green: 0.451, blue: 0.086) // #F97316
    static let pcFeatherPink   = Color(red: 0.925, green: 0.282, blue: 0.600) // #EC4899
    static let pcFeatherPurple = Color(red: 0.545, green: 0.361, blue: 0.965) // #8B5CF6
    static let pcFeatherBlue   = Color(red: 0.149, green: 0.388, blue: 0.922) // #2563EB

    // MARK: - Semantic
    static let pcLive     = Color(red: 0.898, green: 0.078, blue: 0.169) // #E5142B
    static let pcSuccess  = Color(red: 0.114, green: 0.725, blue: 0.329) // #1DB954
    static let pcError    = Color(red: 1.000, green: 0.271, blue: 0.227) // #FF453A
}

extension LinearGradient {
    /// The 5-stop Peacock feather gradient — logomark, premium upsell, selection only.
    static let pcFeather = LinearGradient(
        colors: [.pcFeatherYellow, .pcFeatherOrange, .pcFeatherPink, .pcFeatherPurple, .pcFeatherBlue],
        startPoint: .leading, endPoint: .trailing
    )
    /// Orange→pink subset used on smaller premium buttons & progress fills.
    static let pcPremium = LinearGradient(
        colors: [.pcFeatherOrange, .pcFeatherPink],
        startPoint: .leading, endPoint: .trailing
    )
    /// Hero bottom scrim.
    static let pcHeroScrim = LinearGradient(
        stops: [
            .init(color: .clear,                       location: 0.38),
            .init(color: Color.pcCanvas.opacity(0.70), location: 0.70),
            .init(color: .pcCanvas,                    location: 1.0),
        ],
        startPoint: .top, endPoint: .bottom
    )
}
```

## 2. Typography

Peacock Sans is proprietary; bundle **Poppins** (SIL OFL) as the closest substitute via `Info.plist` / `UIAppFonts`.

```swift
extension Font {
    static let pcScreenTitle  = Font.custom("Poppins-ExtraBold", size: 32)
    static let pcHeroTitle    = Font.custom("Poppins-ExtraBold", size: 28)
    static let pcRowHeader    = Font.custom("Poppins-Bold",      size: 22)
    static let pcCardTitle    = Font.custom("Poppins-Bold",      size: 18)
    static let pcBody         = Font.custom("Poppins-SemiBold",  size: 16)
    static let pcBodyRegular  = Font.custom("Poppins-Regular",   size: 16)
    static let pcCaption      = Font.custom("Poppins-Medium",    size: 15)
    static let pcMeta         = Font.custom("Poppins-Regular",   size: 13)
    static let pcPosterCap    = Font.custom("Poppins-Medium",    size: 11)
    static let pcTab          = Font.custom("Poppins-SemiBold",  size: 12)
    static let pcEyebrow      = Font.custom("Poppins-Bold",      size: 11)
    static let pcButton       = Font.custom("Poppins-Bold",      size: 15)
    static let pcRank         = Font.custom("Poppins-ExtraBold", size: 13)
    static let pcBadge        = Font.custom("Poppins-ExtraBold", size: 10)
}

// Eyebrow modifier — 11pt uppercase tracked, feather-yellow
struct PCEyebrow: ViewModifier {
    func body(content: Content) -> some View {
        content
            .font(.pcEyebrow)
            .tracking(1.4)
            .textCase(.uppercase)
            .foregroundStyle(Color.pcFeatherYellow)
    }
}
extension View { func pcEyebrow() -> some View { modifier(PCEyebrow()) } }
```

## 3. Signature Components

### Hero Billboard

```swift
struct HeroBillboard: View {
    let artURL: URL?
    let eyebrow: String
    let title: String
    let meta: String          // "2023 · Action · TV-MA"
    let onPlay: () -> Void
    let onAdd: () -> Void

    var body: some View {
        ZStack(alignment: .bottom) {
            AsyncImage(url: artURL) { img in
                img.resizable().aspectRatio(contentMode: .fill)
            } placeholder: {
                Rectangle().fill(Color.pcCanvasWarm)
            }
            .frame(height: 360)
            .clipped()

            LinearGradient.pcHeroScrim.frame(height: 360)

            VStack(spacing: 8) {
                Text(eyebrow).pcEyebrow()
                Text(title)
                    .font(.pcHeroTitle)
                    .foregroundStyle(Color.pcTextPrimary)
                    .multilineTextAlignment(.center)
                Text(meta)
                    .font(.pcMeta)
                    .foregroundStyle(Color.pcTextSecondary)
                HStack(spacing: 10) {
                    Button(action: onPlay) {
                        Label("Play", systemImage: "play.fill")
                            .font(.pcButton)
                            .foregroundStyle(Color.pcCTALabel)
                            .padding(.vertical, 13).padding(.horizontal, 26)
                            .background(Color.pcCTA, in: RoundedRectangle(cornerRadius: 6))
                    }
                    Button(action: onAdd) {
                        Label("My Stuff", systemImage: "plus")
                            .font(.pcBody)
                            .foregroundStyle(Color.pcTextPrimary)
                            .padding(.vertical, 12).padding(.horizontal, 18)
                            .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 6))
                    }
                }
                .padding(.top, 6)
            }
            .padding(.horizontal, 20)
            .padding(.bottom, 14)
        }
    }
}
```

### Channels Rail

```swift
struct ChannelTile: Identifiable {
    let id = UUID()
    let name: String
    let gradient: [Color]
    let isLive: Bool
}

struct ChannelsRail: View {
    let channels: [ChannelTile]

    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            HStack {
                Text("Channels").font(.pcRowHeader).foregroundStyle(Color.pcTextPrimary)
                Spacer()
                Text("See All").font(.pcCaption).foregroundStyle(Color.pcTextSecondary)
            }
            .padding(.horizontal, 18)

            ScrollView(.horizontal, showsIndicators: false) {
                HStack(spacing: 10) {
                    ForEach(channels) { ch in
                        ZStack(alignment: .topTrailing) {
                            LinearGradient(colors: ch.gradient, startPoint: .topLeading, endPoint: .bottomTrailing)
                                .frame(width: 60, height: 60)
                                .clipShape(RoundedRectangle(cornerRadius: 14))
                                .overlay(
                                    Text(ch.name)
                                        .font(.pcBadge)
                                        .foregroundStyle(.white)
                                )
                            if ch.isLive {
                                Text("LIVE")
                                    .font(.system(size: 8, weight: .heavy))
                                    .foregroundStyle(.white)
                                    .padding(.horizontal, 4).padding(.vertical, 2)
                                    .background(Color.pcLive, in: RoundedRectangle(cornerRadius: 3))
                                    .offset(x: -4, y: 4)
                            }
                        }
                    }
                }
                .padding(.horizontal, 18)
            }
        }
        .padding(.vertical, 6)
    }
}
```

### Poster Card (with rank + Top 10 badge)

```swift
struct PosterCard: View {
    let artURL: URL?
    let title: String
    var rank: Int? = nil
    var isTop10: Bool = false
    @State private var pressed = false

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            ZStack(alignment: .bottomLeading) {
                AsyncImage(url: artURL) { $0.resizable().aspectRatio(contentMode: .fill) }
                    placeholder: { Rectangle().fill(Color.pcSurface1) }
                    .frame(width: 112, height: 168)
                    .clipShape(RoundedRectangle(cornerRadius: 8))

                if isTop10 {
                    Text("TOP 10")
                        .font(.pcBadge).tracking(0.4)
                        .foregroundStyle(.white)
                        .padding(.horizontal, 6).padding(.vertical, 3)
                        .background(LinearGradient.pcPremium, in: RoundedRectangle(cornerRadius: 4))
                        .padding(6)
                        .frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                }
                if let rank {
                    Text("\(rank)")
                        .font(.pcRank)
                        .foregroundStyle(.white)
                        .padding(.horizontal, 7).padding(.vertical, 2)
                        .background(Color.black.opacity(0.45), in: RoundedRectangle(cornerRadius: 6))
                        .padding(6)
                }
            }
            Text(title)
                .font(.pcPosterCap)
                .foregroundStyle(Color.pcTextSecondary)
                .lineLimit(1)
        }
        .frame(width: 112)
        .scaleEffect(pressed ? 0.97 : 1)
        .animation(.easeOut(duration: 0.12), value: pressed)
        ._onButtonGesture { pressed = $0 } perform: {}
    }
}
```

### Live Badge

```swift
struct LiveBadge: View {
    @State private var dim = false
    var body: some View {
        HStack(spacing: 6) {
            Circle()
                .fill(.white)
                .frame(width: 7, height: 7)
                .opacity(dim ? 0.4 : 1)
                .animation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true), value: dim)
            Text("LIVE").font(.pcBadge).tracking(0.6).foregroundStyle(.white)
        }
        .padding(.horizontal, 10).padding(.vertical, 5)
        .background(Color.pcLive, in: RoundedRectangle(cornerRadius: 4))
        .onAppear { dim = true }
    }
}
```

### Continue Watching Progress

```swift
struct WatchProgress: View {
    let fraction: Double      // 0...1
    let label: String         // "S2 E6 · 24 min left"
    @State private var animated: Double = 0

    var body: some View {
        VStack(alignment: .leading, spacing: 6) {
            GeometryReader { geo in
                ZStack(alignment: .leading) {
                    Capsule().fill(Color.pcSurface2).frame(height: 4)
                    Capsule().fill(LinearGradient.pcPremium)
                        .frame(width: geo.size.width * animated, height: 4)
                }
            }
            .frame(height: 4)
            Text(label).font(.pcPosterCap).foregroundStyle(Color.pcTextSecondary)
        }
        .onAppear { withAnimation(.easeOut(duration: 0.4)) { animated = fraction } }
    }
}
```

### Premium Upsell Button

```swift
struct PremiumButton: View {
    let title: String
    let action: () -> Void
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.pcButton)
                .foregroundStyle(.white)
                .padding(.vertical, 12).padding(.horizontal, 22)
                .background(LinearGradient.pcFeather, in: RoundedRectangle(cornerRadius: 6))
        }
    }
}
```

## 4. Bottom Tab Bar

```swift
struct PeacockTabView: View {
    init() {
        let a = UITabBarAppearance()
        a.configureWithOpaqueBackground()
        a.backgroundColor = UIColor(Color.pcCanvas.opacity(0.96))
        a.shadowColor = UIColor(Color.pcDivider)
        UITabBar.appearance().standardAppearance = a
        UITabBar.appearance().scrollEdgeAppearance = a
    }

    var body: some View {
        TabView {
            HomeView().tabItem { Label("Home", systemImage: "house.fill") }
            ChannelsView().tabItem { Label("Channels", systemImage: "tv") }
            SearchView().tabItem { Label("Search", systemImage: "magnifyingglass") }
            MyStuffView().tabItem { Label("My Stuff", systemImage: "bookmark.fill") }
        }
        .tint(.pcTextPrimary)   // active = white icon fill; no tint pill
    }
}
```

## 5. Motion

```swift
// Poster press
.scaleEffect(pressed ? 0.97 : 1)
.animation(.easeOut(duration: 0.12), value: pressed)

// Live dot pulse
.opacity(dim ? 0.4 : 1)
.animation(.easeInOut(duration: 1.2).repeatForever(autoreverses: true), value: dim)

// Hero cross-fade between featured titles (~8s)
.transition(.opacity)
.animation(.easeInOut(duration: 0.4), value: featuredIndex)

// Detail open — shared element style
.matchedGeometryEffect(id: title.id, in: namespace)
// withAnimation(.easeOut(duration: 0.3))

// Sheet present
.sheet(isPresented: $showSheet) { /* slides up 300ms ease-out, dim fades */ }

// Haptics
let g = UIImpactFeedbackGenerator(style: .light); g.impactOccurred() // poster select
```

## 6. SF Symbols Used

| Component | Symbol | Size |
|-----------|--------|------|
| Home (tab) | `house` / `house.fill` | 22pt |
| Channels (tab) | `tv` / `tv.fill` | 22pt |
| Search (tab) | `magnifyingglass` | 22pt |
| My Stuff (tab) | `bookmark` / `bookmark.fill` | 22pt |
| Play (CTA) | `play.fill` | 14pt |
| Add to My Stuff | `plus` / `checkmark` | 14pt |
| Back | `chevron.left` | 18pt |
| More info | `info.circle` | 18pt |
| Download | `arrow.down.circle` / `arrow.down.circle.fill` | 20pt |
| Cast | `airplayvideo` | 20pt |
| Profile | `person.crop.circle` | 21pt |
| Live indicator | `dot.radiowaves.left.and.right` | 14pt |
| Trailer | `play.rectangle` | 16pt |
| Settings | `gearshape` | 21pt |
| Share | `square.and.arrow.up` | 20pt |

## 7. Dark Mode

```swift
struct PeacockTheme: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background(Color.pcCanvas)
            .foregroundStyle(Color.pcTextPrimary)
            .preferredColorScheme(.dark)   // Peacock is dark-only — lock it
    }
}
extension View { func peacockTheme() -> some View { modifier(PeacockTheme()) } }
```

Peacock ships **no light theme**. Lock `.preferredColorScheme(.dark)` at the root. The only "elevation" cues on the near-black indigo canvas are the warm `#140E26` glow under hero art and a 1pt `#2C2545` border on floating sheets/menus — soft shadows are nearly invisible at this luminance.

## 8. Minimum iOS & Accessibility Notes

- Minimum target: iOS 16 (`AsyncImage`, `.ultraThinMaterial`, `Label` are comfortable; `.sensoryFeedback` needs iOS 17 — fall back to `UIImpactFeedbackGenerator`)
- Bundle Poppins (Regular / Medium / SemiBold / Bold / ExtraBold) TTFs via `Info.plist` `UIAppFonts` — SIL OFL, free to ship; Peacock Sans is proprietary and not redistributable
- Dynamic Type: scale hero title, row headers, body, captions; keep tab labels, eyebrows, badges, rank numerals, "LIVE" at FIXED sizes (composited over art / layout-sensitive)
- VoiceOver: label poster cards "{title}, {genre}, rank {n}"; the white CTA as "Play {title}"; live cards announce "Live now"; group hero eyebrow+title+meta as one element
- The pulsing live dot is decorative — mark `.accessibilityHidden(true)`; convey "Live" via the badge label text
- Color contrast: white `#FFFFFF` and `#B5B2C2` on `#0A0A14` pass WCAG AA; the white CTA with `#0A0A14` label passes AAA; never place body text on the feather gradient
- Reduce Motion: disable hero cross-fade and the live-dot pulse (show a static dot); keep press scale subtle or remove
- Dark mode: there is no alternate scheme — the indigo `#0A0A14` / violet `#140E26` system holds in all conditions; do not invert
